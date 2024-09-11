<!-- In VS Code: Do NOT apply ReWrap to the whole file! Only to sections. -->

## TODO
- Chrome: bookmarks -> `data:` with `<script>` or `javascript:` URLs - both in native Chrome
  bookmarks and in Chrome API. Otherwise we need our own representation & handling in all
  extensions!
- Yawas validation
  - unbookmarked -> title doesn't contain `#__#`. Otherwise user changes manually.
  - Yawas must filter out any `#__#` when updating the title later! OR: We secape any `#__#` by
    doubling it -> `#__##__#`
  - adding highlights (and highlighted text for a comment): the highlight must not contain `<<` or
    `>>`. Alert the user, and refuse. The user can workaround by splitting the highlights.
  - adding a comment: The comment must not contain any `<<` or `>>`, and the comment must not end
    with `<` or `>` if the highlighted text starts with the same character (`<` or `>`,
    respectively).
  - add this to Yawas, and/or a separate extension, and provide this as a JS library.

<details>
<summary>Details - top level</summary>

Content- top level

<details>
<summary>Details - inner level</summary>
Content - inner level
</details>

</details>


### Protocol Handlers
We could store alias bookmarks with a custom URL schema/protocol (`ext+your-protocol-name:`), and redirect them with an
extension. But (as of September 2024) [MDN > WebExtensions > Protocol
Handlers](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/protocol_handlers#browser_compatibility)
are supported __only__ on Firefox, and __not__ on any other browsers.

Also, (as of September 2024) Firefox asks for a confirmation everytime you attempt to open a URL
with a custom protocol `ext+your-protocol-name:` using native Firefox Bookmarks (sidebar). It
doesn't show a checkbox to remember the decision. (It does show such a checkbox (to remember your
decision) - but only when opening a URL with a custom protocol from an extension.)

-->
- Store aliases as `data:html` URLs containing `<script>` that redirects.
- in BSP2 and similar extensions, match such URLs, ignore the (optional) folder path, and redirect to the
  target URL.
- in Floccus, match such URLs, extract both the URL and the (optional) folder path. If the folder
  path is out of date (that is, the bookmark has been moved elsewhere), report it to the user or
  update it or do nothing (as per user's preference).


# Licenses

| Component | License |
| --------- | ------- |
| Floccus   | [MPL-2.0](https://github.com/floccusaddon/floccus/blob/develop/LICENSE.txt) |
| BSP2      | [MPL-2.0](https://addons.mozilla.org/en-US/firefox/addon/bookmark-search-plus-2/) |

---

## 1. GIT & file listing/navigation-friendly (tree depth & width, auto-sorting)

Inspired a little bit by https://doc.rust-lang.org/cargo/reference/registry-index.html.

Eliminate/prevent duplicate bookmarks for the same URL.

Easy navigation & fast load of listings on GitHub. View all links as clickable on GitHub.

Bookmark Title matters: Yawas metadata. We do __not__ want any other way of exchange - that would
complicate Yawas. We can't exchange in any other way, because WebExtensions are isolated. And we
want only one extension (Floccus) to be in charge of synchronization (to avoid race
conditions/conflicts).

Share (URL, title) pairs

URL -> title (incl. highlights)
`/top-level-domain-in-full/second-level-domain-first-letter/second-level-domain-second-letter/third-level-domain-first-letter/full.domain.in-lowercase.NOT-REVERSED.md`
- If the second level domain is just one character (like `x.com`), then its second character is
  deemed to be an underscore `_` (which is OK, since domain names can't contain an underscore).
- Similarly, if the domain doesn't have a third level, that is also deemed to be an underscore.
- Domains names are in lowercase -> when sorted by name, `_` comes before `a`.
- This will separate bookmarks from domains that exist both with `www.` bottom level domain and
  without it.
- Would it cover most use cases of bookmarks of different domains and in the same repo that would
  still be manageable even without splitting by `second-level-domain-second-letter`?
- On the other hand, what kind of use cases could bookmark so many different domains and in the same
  repo that they could benefit from one more directory level: `/second-level-domain-third-letter/`
  or `/third-level-domain-second-letter/` (and use an underscore `_` if that level has only two
  letters)?
- If we limited the depth by domain fragments to the top level and second level only, that would put
  all `owner-or-org-name.github.io` bookmarks together (under `/io/g/i/github.io`), and similar for
  some blog/media hubs. That's why we group by the `third-level-domain-first-letter`, too.

/top-leveldomain-in-full/first-letter/second-letter/third-letter/full-domain-in-lowercase.md
- if in one file per domain -> "centralized" sync/export/exchange + auto-sorted --- less files at
leaf level = shorter listings --- shallower & limited depth = easier human navigation ---
auto-highlight on GitHub & in editors ---- url-escape
https://meta.stackexchange.com/questions/13501/links-to-urls-containing-parentheses/284559#284559 ->
%... ------ sorted by URL => use HTML links, URL-encode URL -> %, HTM-escape text -> &#xxxx;, and
make into a Markdown list: - <a href="...">...</a>

Floccus: This format would disable the filename field in the config.
------------

## 2. Bookmark location in the folder tree

Do most users NOT group/position bookmarks by URL/domain, but by content (topic, genre...)?

Do users group/position bookmarks by any other way that could be auto-inferred? Content creation
date, bookmark creation date, "due" date...?

If the user has a URL already bookmarked, honor their existing folder.

Suggest an initial folder per imported & local new URLs (and separators) sorted/auto-sorting/manual
order?

If a user has multiple such mappings configured, keep their order and use only the first one that
applies.
---

TODO Folder names can contain special characters: `/`, `>`...!! If we store/map folder name parts to
(GIT) sub-directory name parts, we need to escape them.
---

Suggest an initial folder per URL prefix. Again, keep the order of such mappings, and use only the
first one that applies. If the browser's current location is not bookmarked yet, and if there is a
mapping that applies to the current URL, indicate that with a toolbar button, and toolbar-click-menu
or right-click/context menu -> bookmark it there - in Floccus!!!!

---

A user may choose to sync out #1 but __not__ #2. Other users syncing in such bookmarks need to
configure a default/overflow location/folder.
---

## 3. Aliases

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
- Passive:
- escape('<html><head><meta charset="utf-8"/></head><body><a
  href="https://github.com">https://github.com</a></body>')
-  ->
   data:text/html,%3Chtml%3E%3Chead%3E%3Cmeta%20charset%3D%22utf-8%22/%3E%3C/head%3E%3Cbody%3E%3Ca%20href%3D%22https%3A//github.com%22%3Ehttps%3A//github.com%3C/a%3E%3C/body%3E
-  Works in Firefox native bookmarks. But tricky in Firefox extensions.
- Works in Chrome native bookmarks.
- Active:
- `escape('<html><head><meta charset="utf-8"/></head><body><script type="text/javascript">window.location="https://github.com";</script></body>')` ->
   `"data:text/html,%3Chtml%3E%3Chead%3E%3Cmeta%20charset%3D%22utf-8%22/%3E%3C/head%3E%3Cbody%3E%3Cscript%20type%3D%22text/javascript%22%3Ewindow.location%3D%22https%3A//github.com%22%3B%3C/script%3E%3C/body%3E"`
   - By parts:
   - `escape('<html><head><meta charset="utf-8"/></head><body><script type="text/javascript">window.location="')` -> `"%3Chtml%3E%3Chead%3E%3Cmeta%20charset%3D%22utf-8%22/%3E%3C/head%3E%3Cbody%3E%3Cscript%20type%3D%22text/javascript%22%3Ewindow.location%3D%22"`
   - `escape("https://github.com")` -> `"https%3A//github.com"`
   - `escape('";</script></body>')` -> `"%22%3B%3C/script%3E%3C/body%3E"`
   - Parts concatenated together:
   - `escape('<html><head><meta charset="utf-8"/></head><body><script type="text/javascript">window.location="') + escape("https://github.com") + escape('";</script></body>') == escape('<html><head><meta charset="utf-8"/></head><body><script type="text/javascript">window.location="https://github.com";</script></body>')`
   - ---> `true`
   - `"%3Chtml%3E%3Chead%3E%3Cmeta%20charset%3D%22utf-8%22/%3E%3C/head%3E%3Cbody%3E%3Cscript%20type%3D%22text/javascript%22%3Ewindow.location%3D%22" + escape("https://github.com") + "%22%3B%3C/script%3E%3C/body%3E" == escape('<html><head><meta charset="utf-8"/></head><body><script type="text/javascript">window.location="https://github.com";</script></body>')`
   - ---> `true`

With `"ext+hiboosh"`:
- `escape('ext+hiboosh:').length` -> `14` = number of chars to remove from the bookmark URL after it
  was url-encoded
- each part passed to `escape()` to be encoded:
- `escape('<html><head><meta charset="utf-8"/></head><body><script type="text/javascript">window.location="') + escape("ext+hiboosh:https://github.com") + escape('".substring(14);</script></body>') == escape('<html><head><meta charset="utf-8"/></head><body><script type="text/javascript">window.location="ext+hiboosh:https://github.com".substring(14);</script></body>')`
- ---> `true`
- fixed parts encoded as string literals:
- `"%3Chtml%3E%3Chead%3E%3Cmeta%20charset%3D%22utf-8%22/%3E%3C/head%3E%3Cbody%3E%3Cscript%20type%3D%22text/javascript%22%3Ewindow.location%3D%22" +escape("ext+hiboosh:https://github.com")+ "%22.substring%2814%29%3B%3C/script%3E%3C/body%3E" == escape('<html><head><meta charset="utf-8"/></head><body><script type="text/javascript">window.location="ext+hiboosh:https://github.com".substring(14);</script></body>')`
- ---> true
`data:` URLs DO work in Firefox Native Bookmarks.

In Firefox (`130.0` as of September 2024), extension protocol handlers can't redirect/point at `data:` URLs. Only to `http` or `https` URLs or to ones relative to th extension.

```json
  "protocol_handlers": [
  {
    "protocol": "ext+hiboosh",
    "name": "Highlights and Bookmarks Sharing",
    "uriTemplate": "data:text/html,%3Chtml%3E%3Chead%3E%3Cmeta%20charset%3D%22utf-8%22/%3E%3C/head%3E%3Cbody%3E%3Cscript%20type%3D%22text/javascript%22%3Ewindow.location%3D%22%s%22.substring%2814%29%3B%3C/script%3E%3C/body%3E"
  }
```
->
```
Extension is invalid
Reading manifest: Error processing protocol_handlers.0.uriTemplate: Value "data:text/html,%3Chtml%3E%3Chead%3E%3Cmeta%20charset%3D%22utf-8%22/%3E%3C/head%3E%3Cbody%3E%3Cscript%20type%3D%22text/javascript%22%3Ewindow.location%3D%22%s%22.substring%2814%29%3B%3C/script%3E%3C/body%3E" must either: match the format "strictRelativeUrl", or match the pattern /^https?:\/\/.*$/
```

https://bugzilla.mozilla.org/show_bug.cgi?id=1269456 -> "data:-URLs (which are allowed in
window.open())" ( https://discourse.mozilla.org/t/controls-not-working-on-any-about-pages/125298/5 )

https://bugzilla.mozilla.org/show_bug.cgi?id=1317166#c11 -> "Bundle an extension page with your
extension, and pass the content via extension messaging or the URL. If you use the URL, make sure
that there are protections..."

https://developer.mozilla.org/en-US/docs/Web/URI/Schemes/data#length_limitations
----
If using XBEL/XML:
https://stackoverflow.com/questions/449627/are-line-breaks-in-xml-attribute-values-allowed/8188290#8188290
---

BSP2 and Floccus (if they make the button add a new bookmark): if the page being visited is NOT
bookmarked, but its title already contains #__#, when the user tries to bookmark it, show a popup
warning confirmation.

And/or: A separate extension, which, if the page being visited is NOT bookmarked, but its title
already contains #__#, it either changes its toolbar icon/color, or it writes/injects a warning
across the page until the user clicks the toollbar icon or a button/link in the warning overlay.