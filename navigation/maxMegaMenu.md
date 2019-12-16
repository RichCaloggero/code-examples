# Wordpress Plugin "MaxMegaMenu"

This plugin produces menus which attempt to mitigate some of the issues around the expand on focus behavior by expanding only when a forward tab is detected. This is somewhat better, but is still unexpected behavior. Here is a small script which can mitagate this behavior. When this script is installed, menus will open via keyboard only when clicked.

The remaining issue here is that if menus require click to expand, then a menu link cannot both operate a menu and load a new page.  We thus create another element associated with each menu item which acts as a standard link to the landing page. So effectively, each menu item which contains a submenu now has two items within it: the first follows the link, the second toggles the submenu.

See the second version here:
[https://atica11y.wpcomstaging.com/](https://atica11y.wpcomstaging.com/)

## Installation

The MaxMegaMenu plugin has  a mode in which first click opens menu, second click follows link, but this is unexpected, nonstandard behavior which will end up confusing users, especially those using screen readers. So when installing this script, we advise turning this mode off in the plugin configuration settings.  We also advise adding a focus indicator on the menu toggler link via the MaxMegaMenu CSS editor. To target the togglers, use a rule like:
- .mega-menu-item > .toggler:focus {/* styles to apply when the link gains keyboard focus go here */}



To install the script, do the following:

1. open your wordpress plugins and install the plugin called "Header and Footer Scripts". There may be many whose titles contain these words, but the one I used is simply called "Header and Footer Scripts".
	- [Header and Footer Scripts](https://wordpress.org/plugins/header-and-footer-scripts/)
2. Activate the plugin
3. Using the plugin, add the following code into the footer and click "save" (note the `script` tag is required here)
4. open the megamenu plugin to the general settings page and:
- set the option "Click Event Behaviour" to "First click will open a sub menu, second click will close the sub menu."
	- uncheck "unbind javascript events"
	- check "Prefix Menu Item Classes"
	- click "save changes"
5. from the MaxMegaMenu plugin page:
	- click "Menu Themes"
	- once the theme page opens, click "custom styling"
	- add CSS rule as described above
	- click save settings
6. Refresh main site preview to see changes

## Javascript 

Use the "header and footer scripts" plugin (or one with comparable funcionality) to add the following as a footer script as described above. It must be added as a "footer script" so that all other DOM transformations are applied before it executes.

```
<script>
{ // wrap in block to prevent polution of global environment

// find all maxMegaMenu generated menus on this page
const menus = document.querySelectorAll(".mega-menu-wrap");

menus.forEach(menu => {
// All three of these keyboard events must be intercepted, else the plugin default behavior persists
menu.addEventListener("keydown", inhibitHandlers, true);
menu.addEventListener("keyup", inhibitHandlers, true);
menu.addEventListener("keypress", inhibitHandlers, true);

// add h2 tags to top level menu toggles
menu.querySelectorAll(".mega-menu > .mega-menu-item")
.forEach(menuItem => addHeading(menuItem));
});

function inhibitHandlers (e) {
e.stopImmediatePropagation();
e.stopPropagation();
} // inhibitHandlers

function addHeading (menuItem) {
const link = menuItem.querySelector(".mega-menu-link");
const h2 = document.createElement("h2");
h2.textContent = link.textContent;
link.textContent = "";
link.appendChild(h2);
} // addHeading


//alert("footer script added");
} // end
</script>
```

### Separate toggler and "landing page" links

```
<script>
{ // wrap in block to prevent polution of global environment

// find all maxMegaMenu generated menus on this page
const menus = document.querySelectorAll(".mega-menu-wrap");

menus.forEach(menu => {
// All three of these keyboard events must be intercepted, else the plugin default behavior persists
menu.addEventListener("keydown", inhibitHandlers, true);
menu.addEventListener("keyup", inhibitHandlers, true);
menu.addEventListener("keypress", inhibitHandlers, true);

menu.querySelectorAll(".mega-sub-menu")
.forEach(submenu => addToggler(submenu));

// add h2 tags to top level menu toggles
menu.querySelectorAll(".mega-menu > .mega-menu-item")
.forEach(menuItem => addHeading(menuItem));
});

function inhibitHandlers (e) {
e.stopImmediatePropagation();
e.stopPropagation();
} // inhibitHandlers

function addToggler (submenu) {
// existing link already has event, so we'll use that as our toggler
const toggler = submenu.parentElement.children[0];
const link = document.createElement("a");
link.innerHTML = toggler.innerHTML;
link.setAttribute("href", toggler.getAttribute("href"));

toggler.setAttribute("role", "button");
toggler.setAttribute("href", "#");
toggler.classList.add("toggler");
toggler.innerHTML = `<i aria-hidden="true">&DownArrow;</i>`;
toggler.setAttribute("aria-label", link.textContent);
toggler.style.float = "right";
toggler.style.paddingLeft = "5px";

submenu.parentElement.insertBefore(link, toggler);
 } // addToggler

function addHeading (menuItem) {
const target = menuItem.firstElementChild;
target.innerHTML = `<h2>${target.innerHTML}</h2>`;
} // addHeading


//alert("footer script added");
} // end
</script>
```

