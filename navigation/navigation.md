# Navigation

Navigation is the cornerstone of most (if not all) websites and web applications. There are many ways to implement navigation, some more accessible and usable than others.

## Simple

- a list of links
- or a container containing a simple series of links
- single level (i.e. no nesting)
- wrap in `nav` element with `aria-label`
- subnav or section-specific nav separate container, usually inside `main`

```
<header>
<nav aria-label="global">
<a href="p1.html">section 1</a>
<a aria-current="page" href="p2.html">section 2</a>
<a href="p3.html">section 3</a>
</nav>
</header><main>
<nav aria-label="section">
<a href="p2.1.html">subsection 2.1</a>
<a aria-current="page" href="p2.2.html">subsection 2.2</a>
</nav>
<p>... main page content ...</p>
</main>
<footer>
<p>... footer content ...</p>
</footer>
```

## Shallow Nesting

A simple two-level hierarchy can be expressed with at least one level of list. The second level can be either nested lists, or a simple `div` containing a series of links.


- top level links wrapped in heading tags
- second level links in second level list or simple `div`
- if third level needed, then put at top of `main` as above

## Collapsible

Collapsible navigation presents several issues for keyboard and screen reader users.

- can use similar structure as above for 1 and 2 level hierarchies
- strongly suggest using nested lists for hierarchies more than 2 levels deep
- indicate that an element can be collapsed, and it's current state, using `aria-expanded`
   + attribute *must* be present in order for screen reader users to know the item is collapsible
   + value of "true" for expanded, "false" if collapsed
- adding keyboard behavior may be challenging if both landing pages and collapse controls needed for each item

### Keyboard control

If all collapsible controls simply control item visibility, leaving all links as leaf nodes, then use `button` elements (or links with role of button) to control visibility, and normal links to actually jump to new pages.

For example, consider a site which sells items in various categories: household, automotive, office, etc. It is likely that these category navigation elements do not need to jump the user to new pages. Thus, category names can be buttons which simply hide / show items in that category, reserving links for items which take the user to a page where they can purchase the particular item.

However, if the desired behavior is to have each actionable item be a link (for example to a landing page for a subsection), as well as controlling expansion, then either:

- give each item two controls, one to control visibility, and one to jump to landing page
   + example: http://www.bookshare.org/
- show static behavior to screen readers, while sighted mouse and keyboard users see expandion on focus

The issue is that to implement this behavior for keyboard users, one must expand / collapse on focus. Some screen readers (most notably those which use both real and virtual modes, i.e. Jaws and NVDA), may exhibit timing issues when attempting to render the DOM when visibility changes.

To avoid the issue, one must use a method of controling element visibility other than CSS `display:none`, CSS `visibility:hidden` and the HTML5 `hidden` attribute. These hiding methods hide from screen readers as well as from sighted users, which means that each time an element's visibility changes, the screen reader must reinterpret the DOM. This can cause the screen reader to present incorrect and/or garbled speech while the transition is in process.

To avoid the issue, use ways of controling visibility which are not detectable via screen reader:

- CSS `opacity`
- CSS `position` (moving off-screen)
- CSS `clip`

Mouse users will see items expand / collapse on hover, sighted keyboard users will see items expand / collapse on focus, but screen reader users will perceive all elements as being static and always visible.  Because of this fact, it is helpful to not create hierarchies more than two or three levels deep. Unfortunately, this limits the usefulness of this scheme, since presumably the reason to use collapsible nav elements is to allow for more deeply nested hierarchies.

### More Details


#### HTML


```
<nav><ul class="menu"><li>
<h2><a class="label" href="#">top 1</a></h2>
<ul class="collapseible menu"><li>
<a href="#">item 1.1</a>
</li><li>
<a href="#">item 1.2</a>
</li><li>
<a href="#">item 1.3</a>
</li></ul>

</li><li>
<h2><a class="label" href="#">top 2</a></h2>
<ul class="collapseible menu"><li>
<a href="#">item 2.1</a>
</li><li>
<a href="#">item 2.2</a>
</li><li>
<a href="#">item 2.3</a>
</li></ul>

</li><li>
<h2><a class="label" href="#">top 3</a></h2>
<ul class="collapseible menu"><li>
<a href="#">item 3.1</a>
</li><li>
<a href="#">item 3.2</a>
</li><li>
<a href="#">item 3.3</a>
</li></ul>

</li></ul>
</nav>
```

#### Javascript

```
hideAll();

// handles case where focus moves outside the nav
document.body.addEventListener ("focusin", e => {
if (!topMenu().contains(e.target)) {
hideAll();
message(`outside nav - hiding all`);
return;
} // if
}); // focus outside nav


topMenu().addEventListener("focusin", focusIn); // cfocusin

function focusIn (e) {
if (!e.target) return;

const label = e.target;
const menu = menuElement(label);
const parentMenu = closestMenu(label);

if (parentMenu === topMenu()) {
if (menu && isHidden(menu)) show(menu);

} else {
if (isHidden(parentMenu)) show(parentMenu);
} // if
} // focusIn
```

## See full working example

### Using nested lists

This example uses a typical nested list structure (see above) and will be very familiar to most users.

Nested lists can be overly verbose and sometimes cumbersome to use, especially if there are many levels of nesting. The upside, however, is that all screen readers are good at letting the user know when they are switching levels (i.e. moving from a parent list to a sublist, or vice versa)

Adding headings can help deeply nested lists become more navicable. Be sure the heading level reflects nesting depth in some way (does not need to be a one-to-one correspondence between heading level number and nesting depth), but as nesting depth increases then so to should heading level.

- runnable: https://RichCaloggero.github.io/code-examples/navigation/nested-lists.html
- source: https://raw.githubusercontent.com/RichCaloggero/code-examples/blob/master/navigation/nested-lists.html


### no nested lists

This example uses an outer list, and the collapseibles are simple divs.

This is slightly less verbose (screen reader doesn't have to announce each list as it is entered, and doesn't need to continuously announce item counts, etc). However, the downside of this implementation is that what one gains in conciseness, is IMO a loss of clarity. In the examples below, the outer list items are wrapped in headings, so what the screen reader user sees would be something like this:

>heading level 2, top 1<br>
item 1.1<br>
item 1.2<br>
...<br>
heading level 2, top 2<br>
item 2.1<br>
item 2.2<br>
...<br>

Basically, the screen reader user sees a sequence of links separated by headings, which is fairly clear, and slightly less verbose, but is a bit less clear, and will totally fail if we add another level to the tree.

We thus favor the nested list approach because although it is a bit more verbose in ways, it is clearer and will scale if we add more menu levels.

- runnable: https://RichCaloggero.github.io/code-examples/navigation/no-sublists.html
- source: https://raw.githubusercontent.com/RichCaloggero/code-examples/master/navigation/no-sublists.html
