---
layout: post
title: What's the deal with firefox?
categories: firefox rant

---
* table of contents
{:toc}

## Look and Feel
#### Compact Density
Removed compact density for [no reason](https://bugzilla.mozilla.org/show_bug.cgi?id=1693028#c64)
#### Default layout
Default layout intentionally wastes pixels with 'Flexible Space'. Instead: make a new GUI element called `domain indicator` or something that doesn't display the whole url and use it in the default layout. That way, users that wish to see the entire URL can replace the new domain indicator with the traditional URL bar while new users can have a simplified experience.

## Bookmarks
Bookmarks are a basic and ancient feature of web browsing. Firefox doesn't want you to use them though.
#### Mobile collections
Collections do not have feature improvements over bookmarks. 
#### Sorting bookmarks is a two step process
When you create a bookmark with ctrl+D or by clicking the bookmark star button it produces a small menu: bookmark-menu1.jpg
So if you have more than a dozen folders, or any hierarchy at all, the absurdly small window will be quite cumbersome to use. I have taken to saving all bookmarks to "Other Bookmarks" and using the BO (Bookmarks Organizer) to sort.
#### ~~Bookmarks organizer lacks *show in folder*~~
~~This is the inverse of the above problem. When you are organizing your older bookmarks, you might search for some keyword. Organizer has the search feature, but no way to tell the user where the bookmark resides in the library. There should be an command somewhere to show a searched bookmark in it's parent folder, like file managers "Show In Folder" option.~~

*Update 2025: firefox organizer [now has](https://phabricator.services.mozilla.com/D121040) "show in folder" yipee*

## Pocket
Pocket was a feeble attempt to match competitor's spammy UX. It worked. There's no normal route to disable pocket either, so it's just cluttering the UI for users that do not want it.

## Development
### Changelog
There is no consolidated changelog that FLOSS projects typically have. Instead one is forced to browse https://www.mozilla.org/en-US/firefox/releases/

### Mercurial
Mozilla uses mercurial instead of git to manage the project code. This is sub-optimal for encouraging community contribution.