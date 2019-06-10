# Navigation


Since the menus are not deeply nested, the screen reader user does not need to see "menu behavior" here; we can simply show the list structure as-is (see below).

Non-screen reader users (both mouse and keyboard users) can see menus open / close on focus.

This will work fine if the method of hiding the menus leaves them in the dom and available to screen reader users no matter their visibility. In this case, the accessibility tree will not need to be recalculated each time the state of a menu changes, so no erroneous announcement or behavior is seen.

Hiding with CSS display:none or with the HTML "hidden" attribute *will not work*, and will exhibit bad behavior with at least some screen readers.  This is because the accessibility tree needs to be recalculated each time a menu's visibility changes, because hiding this way *does* effect the screen reader's view of the DOM.


Three possible ways of hiding then would be:

- via css opacity property;
- via css clip property
- moving off-screen via css positioning




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
