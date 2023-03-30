---
layout: post
title: Why do the firefox devs hate their users?
categories: firefox rant

---
* table of contents
{:toc}

## Bookmarks
Bookmarks are a very basic feature of internet browsing. Firefox doesn't care about organizing them though.
#### Sorting is a two step process
When you create a bookmark with ctrl+D or by clicking the bookmark star button it produces a small menu: bookmark-menu1.jpg
So if you have more than a dozen folders, or any hierarchy at all, the absurdly small window will be quite cumbersome to use. A creative user is likely to save all bookmarks to "Other Bookmarks" and use the BO (Bookmarks Organizer) to sort. Which brings me to the next issue:
#### Bookmarks organizer lacks *show in folder*
This is sort of the inverse of the above problem. When you are organizing your older bookmarks, you might search for some keyword. Organizer has the search feature, but no way to tell the user where the bookmark resides in the library. There should be an command somewhere to show a searched bookmark in it's parent folder, like file managers "Show In Folder" option. 

## Pocket
Firefox ought to be as FLOSS as possible. Pocket was a feeble attempt to match the competitions spammy UX. It worked. There's no way to disable pocket either, so it's permanently cluttering the UI, even for users that do not like the new functionality. 

## Look and Feel
#### Compact Density
Removed compact density(https://bugzilla.mozilla.org/show_bug.cgi?id=1693028#c64)
#### Default layout
Default layout intentionally wastes pixels with "Flexible Space". Why? Just make a new GUI element called `domain indicator` or something that doesn't display the whole url and make that the default layout. That way, users that wish to see the URL can replace the domain indicator with the URL bar and firefox doesn't waste it's users screen space and attention. 
