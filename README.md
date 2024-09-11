1. GIT & file listing/navigation-friendly (tree depth & width, auto-sorting)

Inspired a little bit by https://doc.rust-lang.org/cargo/reference/registry-index.html.

Eliminate/prevent duplicate bookmarks for the same URL.

Easy navigation & fast load of listings on GitHub.
View all links as clickable on GitHub.

Bookmark Title matters: Yawas metadata. We do __not__ want any other way of exchange - that would
complicate Yawas. We can't exchange in any other way, because WebExtensions are isolated. And we
want only one extension (Floccus) to be in charge of synchronization (to avoid race
conditions/conflicts).

Share (URL, title) pairs

URL -> title (incl. highlights)
/top-leveldomain-in-full/first-letter/second-letter/third-letter/full-domain-in-lowercase/path/to/file
- to_lowercase -> '_' < 'a' 
- funny names in URI/path/query/fragment!!

/top-leveldomain-in-full/first-letter/second-letter/third-letter/full-domain-in-lowercase.md
- if in one file per domain -> "centralized" sync/export/exchange + auto-sorted --- less files at
leaf level = shorter listings --- shallower & limited depth = easier human navigation ---
auto-highlight on GitHub & in editors ---- url-escape
https://meta.stackexchange.com/questions/13501/links-to-urls-containing-parentheses/284559#284559 ->
%... ------ sorted by URL => use HTML links, URL-encode URL -> %, HTM-escape text -> &#xxxx;, and
make into a Markdown list: - <a href="...">...</a>

Floccus: This format would disable the filename field in the config.
------------

2. Bookmark location in the folder tree

If the user has a URL already bookmarked, honor its folder.

Suggest an initial folder per imported & local new URLs (and separators) sorted/auto-sorting/manual
order?

If a user has multiple such mappings configured, keep their order and use only the first one that
applies.
---

TODO Folder names can contain '/'!!
---

Suggest an initial folder per URL prefix. Again, keep the order of such mappings, and use only the
first one that applies. If the browser's current location is not bookmarked yet, and if there is a
mapping that applies to the current URL, indicate that with a toolbar button, and toolbar-click-menu
or right-click/context menu -> bookmark it there - in Floccus!!!!

---

A user may choose to sync out #1 but __not__ #2. Other users syncing in such bookmarks need to
configure a default/overflow location/folder.
---

3. Aliases

Optional: Mark "extra" bookmark entries with #__#BOOKMARK_ALIAS in their title.
- OR: bookmark-share.github.io OR yawa-share OR yawashare OR bookmarkalias that redirects --- But,
then do we sync it out? If so, we need to sync out the title - but that's contrary to the top-level
#1 above that there is ONLY ONE sync entry per URL.

-> Floccus: -> BSP2: when about to delete such an entry, check that there is one (or at least one)
entry, otherwise show a confirmation dialog. -> BSP2 in general: When adding a new bookmark, alert
if its title contains #__# or << or >>. -> Yawas: Ignore #__#NOT-FOR-YAWAS entries. -> Have a
separate add-on that shows a red light for such bookmarks.

OR: redirector-url?encoded-target-url&sync-store

- javascript:'window.location="https://github.com"'
-  not in BSP2, not in Firefox native Bookmarks
- escape('<html><head><meta charset="utf-8"/></head><body><a
  href="https://github.com">https://github.com</a></body>')
-  ->
   data:text/html,%3Chtml%3E%3Chead%3E%3Cmeta%20charset%3D%22utf-8%22/%3E%3C/head%3E%3Cbody%3E%3Ca%20href%3D%22https%3A//github.com%22%3Ehttps%3A//github.com%3C/a%3E%3C/body%3E
-  yes in Firefox native Bookmarks
- escape('<html><head><meta charset="utf-8"/></head><body><script
  type="text/javascript">window.location="https://github.com";</script></body>')
-  ->
   data:text/html,%3Chtml%3E%3Chead%3E%3Cmeta%20charset%3D%22utf-8%22/%3E%3C/head%3E%3Cbody%3E%3Cscript%20type%3D%22text/javascript%22%3Ewindow.location%3D%22https%3A//github.com%22%3B%3C/script%3E%3C/body%3E
-  yes in Firefox Native Bookmarks

https://bugzilla.mozilla.org/show_bug.cgi?id=1269456 -> "data:-URLs (which are allowed in
window.open())" ( https://discourse.mozilla.org/t/controls-not-working-on-any-about-pages/125298/5 )

https://developer.mozilla.org/en-US/docs/Web/URI/Schemes/data#length_limitations
----
If using XBEL/XML:
https://stackoverflow.com/questions/449627/are-line-breaks-in-xml-attribute-values-allowed/8188290#8188290
---

