---
layout: post
title: Use mutt as IMAP client
categories: cli

---
* table of contents
{:toc}

# Intro
Mutt is a terminal mode email client that is compatible with IMAP. I'm setting it up to work with my lame email provider, but similar settings would probably work on gmail and other providers with a few tweaks. Between the knowledge available in the [mutt manual](http://www.mutt.org/doc/manual/) and [Debian's wiki](https://wiki.debian.org/Mutt#IMAP_server) I have a few helpful macros, some configuration tips, and a general workflow to suggest. My muttrc is included for reference.

# Configuration

## IMAP
I felt like a modern experience would be purely remote, so I configure Mutt to do everything remotely.
Setting `spoolfile` and `folder` will stop default behaviors for local Maildir. `ssl_starttls` and `ssl_force_tls` are set for connection encryption. `mailboxes` directs mutt to poll and report new mail. 

## Authentication
I found a neat trick to keep my IMAP authentication slightly protected. It involves simply encrypting a section of config and sourcing the decryption command. I don't fully grok the syntax for sourcing stdout, but it is nice that it works. Barely worth noting is that `--quiet` isn't at all necessary because it only silences what's sent to stderr.

    source "gpg --quiet -d ~/.secrets/mutt_secrets.gpg |"

## Macros
Add key-binds for <kbd class="kbc-button no-container">S</kbd>pam and <kbd class="kbc-button no-container">A</kbd>rchive functions:

    macro index,pager S "<save-message>+spam<enter><enter>" "Move to Spam"
    macro index,pager A "<save-message>+Archive<enter><enter>" "Move to Archives"

The capital `S` effectively means <kbd class="kbc-button no-container">shift</kbd>+<kbd class="kbc-button no-container">s</kbd>.

The next macro does the bulk of mail sorting and searching. Lowercase <kbd class="kbc-button">l</kbd> prompts for a "view limit" which is similar to a modern mail "search" input. It simply filters (or limits) the displayed messages to those that match the pattern. The special value "all" does as it implies. This macro just keeps me from having to type "all" every time I want to clear the limit.

    macro index L "<limit>all<enter>" "Un-set view limit"

## Views

Mutt reads `~/.mailcap` for instructions to open different types of email objects. Use `auto_view` to automatically convert to text/plain. A mailcap entry using elinks might look like:

    text/html; elinks -dump-charset %{charset} -dump 1 -dump-color-mode 3 %s; copiousoutput;

# Usage

1. Fire up `mutt`

1. Prompted with a list of mail, probably reverse the sorting with <kbd class="kbc-button">O</kbd>, <kbd class="kbc-button">D</kbd>

1. Read - <kbd class="kbc-button">enter</kbd>, Delete - <kbd class="kbc-button">D</kbd>, or Archive - <kbd class="kbc-button">A</kbd>. When in the index, the cursor automatically moves to the next message. When in "pager" mode, the next message is automatically loaded.

## muttrc

{% highlight conf %}
# decrypt and set password
## not sure how the |" magic works, but it directs stdout to be sourced by mutt
source "gpg --quiet -d ~/.config/secrets/mutt_secrets.gpg |"

## now that we have the IMAP values from the secrets file, copy them to SMTP variables
set smtp_pass = $imap_pass
set smtp_url=smtps://$imap_user@mail.example.com

# spoolfile is where mutt looks to report new mail and sets the default view
set spoolfile="imaps://$imap_user@mail.example.com"
# folder sets the '+' global and is the default path for other operations
set folder ="imaps://$imap_user@mail.example.com/INBOX"
## mutt seems to work with either of these set, but doesn't behave well
## unless they are both set
# configure default locations
set record="+Sent"
set postponed="+Drafts"
set trash="+Trash"

# The mailboxes specifies folders which can receive mail and which will be 
# checked for new messages.  When changing folders, pressing space will cycle
# through folders with new mail.  The unmail-boxes command is used to remove
# a file name from the list of folders which can receive mail.  If "*" is 
# specified as the file name, the list is emptied.
mailboxes +INBOX

# Set this to a directory and mutt will cache copies of messages from your
# IMAP and POP servers here. You are free to remove entries at any time.
# When setting this variable to a directory, mutt needs to fetch every
# remote message only once and can perform regular expression searches
# as fast as for local folders.
set message_cachedir=~/.cache/mutt/messages

# oh, UTF-8? I use that on everything!
set charset="utf-8"

# moving a message to the spam folder to "mark" spam for SpamAssassin 
macro index,pager S "<save-message>+spam<enter><enter>" "Move to Spam"
# TODO: update the year for archives macro dynamically
macro index,pager A "<save-message>+Archives.2021<enter><enter>" "Move to Archives"
# This one may be redundant, The idea is to delete it immediately, not move to trash
macro index,pager P "<purge-message>" "Purge Message"
# l is limit so bind L to clear the limit expression with keyword "all"
macro index L "<limit>all<enter>" "Un-set view limit"

# activate TLS if available on the server
set ssl_starttls=yes
# always use SSL when connecting to a server. Needed to protect against downgrade attacks
set ssl_force_tls=yes

# When set, mutt will not open new IMAP connections to check 
# for new mail.  Mutt will only check for new mail over existing
# IMAP connections. This is useful if you don't want to be prompted
# for user/password pairs on mutt invocation, or if opening the connection is slow.
unset imap_passive

# Automatically poll subscribed mailboxes for new mail (new in 1.5.11)
set imap_check_subscribed

# Reduce polling frequency to a sane level for networked application
set mail_check=60

# keep a cache of headers for faster loading (1.5.9+?)
set header_cache= ~/.cache/mutt/headers

# automatically convert text/html using ~/.mailcap. for example:
# text/html; elinks -dump-charset %{charset} -dump 1 -dump-color-mode 3 %s; copiousoutput;
auto_view text/html
# prefer to view text/plain
## I don't use this because many text/plain emails are polluted with 200-char URLs
## Better to convert from text/html for cleaner viewing.
# alternative_order text/plain text/enriched text/html

# This variable specifies the maximum amount of time in seconds that mutt will
# wait before polling open IMAP connections, to prevent the server from closing
# them before mutt has finished with them.
# Doesn't seem to do much, my connection still seems to get closed
set imap_keepalive=55

#Disable the "From:" field on outgoing mail unless it is explicitly set.
set use_from=no
{% endhighlight %}

# Issues

## TLS timeout
mutt disconnects from the IMAP server periodically
> tls_socket_read (The TLS connection was non-properly terminated.)

## Sending
I haven't bothered to tune the settings for sending mail because I don't send very many emails. 

## Encryption
My configuration doesn't have any provisions for reading encrypted mail.

## Batch Processing
My keybinds only work on one message at a time. Mutt has functionality for tagging and dealing with groups of messages, but I haven't learned a workflow for it.

## Attachments
I don't have any processing of attachments other than simply saving them. Trying different ways, including Sixel, of displaying some attachments might be a good idea. It's also somewhat annoying when in pager mode because mutt automatically downloads every part of the message, including attachemnts, when it is loaded in pager.

# Mutt is great
The simplicity and customizability is superior to any other client I know of. Though the documentation is burdened by 20 years of linguistic and technological baggage, a modern experience was not hard to achieve.

<link rel="stylesheet" href="https://unpkg.com/keyboard-css@1.2.4/dist/css/main.min.css" />
