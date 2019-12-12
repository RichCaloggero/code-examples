# Collapsible
A simple two-level hierarchy can be expressed with at least one level of list. The second level can be either nested lists, or a simple `div` containing a series of links.

- strongly suggest using nested lists for hierarchies more than 2 levels deep
- indicate that an element can be collapsed, and it's current state, using `aria-expanded`
   + `aria-expanded` on the trigger, not the menu container
   + attribute *must* be present in order for screen reader users to know the item is collapsible
   + value of "true" for expanded, "false" if collapsed
- adding keyboard behavior may be challenging if both landing pages and collapse controls needed for each item

## Supporting both menu behavior and "landing page" links via keyboard

When using a mouse, it is fairly straightforward to create a navigation system which allows one to have controls which both reveal a submenu and link to another page:

- click follows link
- hover displays the menu

Keyboard and touch devices do not readily support this behavior. It is possible to cause menus to expand on keyboard focus, but this has negative implications for screen reader users. Touch devices cannot do this at all: there is no notion of touch focus; when an item is touched it is both focused and clicked.

For the screen reader user, when collapsible sections suddenly open on focus, this creates a situation where the document may be in two different states depending on exactly how it is navigated. Further, it may show different behavior depending on exactly which browser / screen reader combination is used to navigate.

We thus _strongly suggest not creating navigation menus which expand / collapse on focus!_ Instead, consider these two possible solutions.

1. give each item two controls, one to control visibility, and one to jump to landing page
	- example: http://www.bookshare.org/
2. give each item a single control, and place link to "landing page" as first item of submen
		+ example: [https://libraries.mit.edu/](https://libraries.mit.edu/)
3. show static behavior to screen readers, while sighted mouse and keyboard users see expand/collapse on focus
	- CSS `opacity`
	- CSS `position` (moving off-screen)
	- CSS `clip`
	- example: [https://sfs.mit.edu/](https://sfs.mit.edu/)

