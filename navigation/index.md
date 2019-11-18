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

- can use similar structure as above for 1 and 2 level hierarchies
- strongly suggest using nested lists for hierarchies more than 2 levels deep
- indicate that an element can be collapsed, and it's current state, using `aria-expanded`
   + attribute *must* be present in order for screen reader users to know the item is collapsible
   + value of "true" for expanded, "false" if collapsed
- adding keyboard behavior may be challenging if both landing pages and collapse controls needed for each item
	+ give each item two controls, one to control visibility, and one to jump to landing page
		+ example: http://www.bookshare.org/
	+ show static behavior to screen readers, while sighted mouse and keyboard users see expand/collapse on focus
		+ CSS `opacity`
		+ CSS `position` (moving off-screen)
		+ CSS `clip`

### HTML

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

### Javascript

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

### See full working example

#### Nested Lists

- runnable: https://RichCaloggero.github.io/code-examples/navigation/nested-lists.html
- source: https://raw.githubusercontent.com/RichCaloggero/code-examples/blob/master/navigation/nested-lists.html


#### No Nested Lists


- runnable: https://RichCaloggero.github.io/code-examples/navigation/no-sublists.html
- source: https://raw.githubusercontent.com/RichCaloggero/code-examples/master/navigation/no-sublists.html


## Representing Navigation as a Tree

IMO, this is the correct way to implement deeply nested navigation.

https://www.w3.org/TR/wai-aria-practices/examples/treeview/treeview-2/treeview-2a.html

## Navigation Menus

https://www.w3.org/TR/wai-aria-practices/examples/menubar/menubar-1/menubar-1.html

## Wordpress Plugin "MaxMegaMenu"

This plugin produces menus which attempt to get around the expand on focus issues described above by expanding only when a forward tab is detected. This is somewhat better, but is still unexpected behavior. Here is a small script which can mitagate this behavior. To install it, do the following:

1. open your wordpress plugins and install the plugin called "Header and Footer Scripts". There may be many whose titles contain these words, but the one I used is simply called "Header and Footer Scripts".
2. Activate the plugin
3. Using the plugin, add the following code into the footer and click "save" (note the `script` tag is required here)
4. open the megamenu plugin to the general settings page and:
	- uncheck "unbind javascript events"
	- check "Prefix Menu Item Classes"
	- click "save changes"
5. Refresh main site preview to see changes
6. Publish

```
<script>
{ // module (does not create globals)
const menus = document.querySelectorAll(".mega-menu-wrap");

menus.forEach(menu => {
menu.addEventListener("keydown", inhibitHandlers, true);
menu.addEventListener("keyup", inhibitHandlers, true);
menu.addEventListener("keypress", inhibitHandlers, true);

menu.querySelectorAll(".mega-menu > .mega-menu-item").forEach(menuItem => addHeading(menuItem));
});

function addHeading (menuItem) {
const link = menuItem.querySelector(".mega-menu-link");
const h2 = document.createElement("h2");
h2.textContent = link.textContent;
link.textContent = "";
link.appendChild(h2);
//menuItem.replaceChild(h2, link);
} // addHeading

function inhibitHandlers (e) {
e.stopImmediatePropagation();
e.stopPropagation();
//e.preventDefault();
} // inhibitHandlers
} // end
</script>
```

How to trigger update!
