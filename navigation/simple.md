# Simple

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
