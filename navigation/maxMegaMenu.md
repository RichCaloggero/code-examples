## Wordpress Plugin "MaxMegaMenu"

This plugin produces menus which attempt to get around the [expand on focus issues described here](.) by expanding only when a forward tab is detected. This is somewhat better, but is still unexpected behavior. Here is a small script which can mitagate this behavior. To install it, do the following:

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

