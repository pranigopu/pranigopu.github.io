<h1>The Original Prani Gopu</h1>

---

**Contents**:

- [About](#about)
- [Links](#links)
- [Categorised Work](#categorised-work)
- [Uncategorised Work](#uncategorised-work)

---

# About
Collection of my work in:

- Academic areas
- Researched topics
- Professional endeavours
- Personal projects

# Links
- [`pranigopu` (GitHub)](https://github.com/pranigopu) (projects)
- [`@pranigopu` (YouTube)](https://www.youtube.com/@pranigopu)
- [Personal Writing Collection (WordPress)](https://pranigopu.wordpress.com/)
- [`pranav-gopalkrishna` (GitHub)](https://github.com/pranav-gopalkrishna) (personal)
- [`pranav-gopalkrishna` (LinkedIn)](https://www.linkedin.com/in/pranav-gopalkrishna)

# Categorised Work

| Category | Work | Description |
| --- | --- | --- |
| Bayesian Deep Learning | [Comparative Evaluation of Uncertainty Quantification of BNNs (PDF)](https://pranigopu.github.io/comparative-evaluation-of-uncertainty-quantification-of-bnns.pdf) | My master's thesis |
| Bayesian Deep Learning | [`pranigopu`/`mastersProject` (GitHub)](https://github.com/pranigopu/mastersProject) | The repository backing my master's thesis |
| Neural Style Transfer for Audio | [Ambience-to-Music Neural Style Transfer (AM-NST) (app.readytensor.ai)](https://app.readytensor.ai/publications/ambiencetomusic-neural-style-transfer-amnst-2CirVDc5nt0b) | A report for an academic project on NST for audio using spectrograms, taking inspiration from NST for images. |
| Neural Style Transfer for Audio | [`pranigopu`/`ambience-to-music-neuralStyleTransfer` (GitHub)](https://github.com/pranigopu/ambience-to-music-neuralStyleTransfer) | The repository backing my AM-NST project. |
| Procedural Generation | [Procedural Generation in *Unexplored* (PDF)](https://pranigopu.github.io/procedural-generation-in-unexplored.pdf) | A case-study on cyclic generation for procedural level generation in the game *Unexplored*. |
| Procedural Generation | [`pranigopu`/`diver-vs-mermaid` (GitHub)](https://github.com/pranigopu/diver-vs-mermaid) | A project to design and implement cellular automata for terrain generation and behaviour trees for agent behaviour |
| Ethics & Regulation in AI | [Transparency, Explainability and Accountability (TEA) in AI (PDF)](https://pranigopu.github.io/report-on-transparency-explainability-and-accountability-in-ai.pdf) | This report aims to address some relevant ethical ideas in AI, primarily transparency, explainability and accountability, and integrate these ideas with technical/business requirements and case studies |
| Autonomous Navigation | [Autonomous Navigation](https://pranigopu.github.io/autonomous-navigation/) | A page containing my writing and links to my work in autonomous navigation (particularly for AMRs) |
| Autonomous Navigation | [`pranigopu`/`ros2-nav2-foundations` (GitHub)](https://github.com/pranigopu/ros2-nav2-foundations) | A repository containing my learnings and basic research on ROS2's Navigation2 package. |
| Computer Science | [Can Computers Think?](https://pranigopu.github.io/can-computers-think.html) | Can they, though? Does it even matter? Read more to find out what I think. |
| Computer Science | [Gamification: *Non-Game Applications of Video Game Concepts*](https://pranigopu.github.io/gamification.html) | An essay on the value and potential of gamification beyond games. |
| Mathematics | [Applications of Number Theory](https://pranigopu.github.io/applications-of-number-theory/) | Number theory is quite an abstract field of Mathematics, but its applications can be surprisingly practical. |
| Mathematics | [Linear vs. Nonlinear Phenomena](https://pranigopu.github.io/linear-vs-nonlinear-phenomena.html) | An essay on linear vs. nonlinear systems, and the value in understanding their nature and relating them. |
| Agentic AI | [Foundations of Agentic AI](https://pranigopu.github.io/foundations-of-agentic-ai) | A page containing my writings on agentic AI from a foundational level. |
| Observability | [OPCM](https://pranigopu.github.io/opcm) | OTel + Prometheus Centralised Monitoring, based on my journey in a professional project. |

<!-- The following must come after the above table, so the above table is loaded before the script tries to search and organise it -->

<div id="works-explorer" hidden>
<!-- The container starts hidden, and the script reveals it once it has read the table, so if the script fails the plain table stays visible. -->
<input type="search" id="we-q" placeholder="Search works" aria-label="Search works">
<p id="we-chips"></p>
<p id="we-status" aria-live="polite"></p>
<div id="we-results"></div>
</div>
<style>
/* The only styling: bold the selected category chip, since without it nothing shows which one is active. */
#we-chips button[aria-pressed="true"]{font-weight:bold}
</style>
<script>
(function () {
  // Everything is wrapped in a function that runs immediately, so the variables below stay private and cannot clash with the theme's own scripts.
  var box = document.getElementById('works-explorer');
  // Find the works table by its header row (Category | Work | Description), in case the page ever has other tables.
  var table = Array.from(document.querySelectorAll('table')).find(function (t) {
    return Array.from(t.querySelectorAll('th')).map(function (th) { return th.textContent.trim().toLowerCase(); }).join('|') === 'category|work|description';
  });
  // If the container or the table is missing, stop quietly and leave the page as it is.
  if (!box || !table) return;
  // Escapes characters that have special meaning in HTML, so text like "Ethics & Regulation in AI" cannot break the markup built later.
  var esc = function (s) { return s.replace(/[&<>"]/g, function (c) { return { '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;' }[c]; }); };
  // Turn each table row into a plain object: its category, the Work cell's HTML (which includes the link), the Description cell's HTML, and all of the row's text in lowercase for searching.
  var rows = Array.from(table.querySelectorAll('tbody tr')).map(function (tr) {
    var c = tr.children;
    return { cat: c[0].textContent.trim(), work: c[1].innerHTML, desc: c[2].innerHTML, text: tr.textContent.toLowerCase() };
  });
  // Collect the distinct category names in the order they first appear in the table.
  var cats = [];
  rows.forEach(function (r) { if (cats.indexOf(r.cat) < 0) cats.push(r.cat); });
  // The current filter: the selected category ('' means all) and the current search text.
  var state = { cat: '', q: '' };
  // If the URL ends in #cat=Something, start with that category selected, but only if it is a real category.
  // NOTE: This allows you to share URLs with selected categories pre-filtered.
  var m = location.hash.match(/cat=([^&]*)/);
  // ADDITIONAL NOTE:
  // - location is a built-in browser object that exists on every web page automatically, so you can use it anywhere in JavaScript without declaring it.
  // - location holds information about the current page's address, split into parts (location.href, location.hash, etc.).
  // - It is a property of the global window object; location is shorthand for window.location (you can use either form).
  if (m) { var c0 = decodeURIComponent(m[1]); if (cats.indexOf(c0) >= 0) state.cat = c0; }
  // Grab the elements the script needs to update.
  var q = box.querySelector('#we-q'), chips = box.querySelector('#we-chips'), out = box.querySelector('#we-results'), status = box.querySelector('#we-status');
  // Counts how many rows belong to a category, for the number shown on each chip.
  var count = function (c) { return rows.filter(function (r) { return r.cat === c; }).length; };
  // Build the chips: an "All" entry followed by one entry per category, each shaped as [value, label, count], then turn each into a button and set them into the page.
  // NOTE: The category name is stored on each button in data-cat, and aria-pressed marks the selected one.
  chips.innerHTML = [['', 'All', rows.length]].concat(cats.map(function (c) { return [c, c, count(c)]; })).map(function (x) {
    return '<button type="button" data-cat="' + esc(x[0]) + '" aria-pressed="' + (state.cat === x[0]) + '">' + esc(x[1]) + ' (' + x[2] + ')</button>';
  }).join(' ');
  // Redraws the results and the status line from the current filter.
  function draw() {
    var term = state.q.trim().toLowerCase(), shown = 0, html = '';
    cats.forEach(function (c) {
      // Skip categories that do not match the selected chip.
      if (state.cat && state.cat !== c) return;
      // Keep rows in this category that match the search, where an empty search matches everything.
      var items = rows.filter(function (r) { return r.cat === c && (!term || r.text.indexOf(term) >= 0); });
      // Do not show a heading for a category with no matching rows.
      if (!items.length) return;
      shown += items.length;
      // One heading per category, followed by a bulleted list of its works.
      html += '<h3>' + esc(c) + '</h3><ul>' + items.map(function (r) { return '<li>' + r.work + ' <br> <blockquote>' + r.desc + '</blockquote></li>'; }).join('') + '</ul>';
    });
    out.innerHTML = html || '<p>No works match.</p>';
    status.textContent = 'Showing ' + shown + ' of ' + rows.length + ' works';
  }
  // One click listener on the container handles every chip, because clicks on a button bubble up to its parent.
  chips.addEventListener('click', function (e) {
    // Find the button that was clicked, and ignore clicks that were not on a button.
    var b = e.target.closest('button');
    if (!b) return;
    // Clicking the selected chip again clears the filter, and clicking any other chip selects it.
    state.cat = state.cat === b.dataset.cat ? '' : b.dataset.cat;
    // ADDITIONAL NOTE:
    // - Every HTML element has a dataset property, and the browser fills it from any attributes whose names start with data- (e.g. data-cat).
    // - The rule is that data- is dropped and the rest of the name becomes a property on dataset. So data-cat becomes dataset.cat
    // - As a side note (note relevant to this script), hyphens after the data- prefix become camelCase (e.g. data-random-text becomes dataset.randomText).
    // Update aria-pressed on every chip in place, which drives the bold style and keeps keyboard focus where it was.
    chips.querySelectorAll('button').forEach(function (x) { x.setAttribute('aria-pressed', String(x.dataset.cat === state.cat)); });
    // Put the selection in the address bar without reloading or adding a history entry, so the current view can be shared as a link.
    history.replaceState(null, '', state.cat ? '#cat=' + encodeURIComponent(state.cat) : location.pathname + location.search);
    // ADDITIONAL NOTE:
    // - history is another object the browser provides on every page, like location.
    // - It is shorthand for window.history, and it represents the list of pages the tab has visited, the one the Back and Forward buttons move through.
    // - The above line ensures that each chip click updates the URL to match the selected category without reloading the page.
    // - Because the above line replaces the current entry rather than adding one, the history list stays the same length, so pressing Back leaves your page instead of stepping through every chip you clicked.
    draw();
  });
  // Redraw on every keystroke in the search box.
  q.addEventListener('input', function () { state.q = q.value; draw(); });
  // Everything is ready, so hide the original table, show the explorer, and draw the first view.
  table.style.display = 'none';
  box.hidden = false;
  draw();
})();
</script>

# Uncategorised Work
> May eventually be migrated to "Categorised Work".

**See**: [Untitled](./untitled/)