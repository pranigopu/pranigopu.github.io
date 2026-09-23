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
| Agentic AI | [Agentic AI](https://pranigopu.github.io/agentic-ai) | A page containing my writings on agentic AI from a foundational level. |
| Observability | [OPCM](https://pranigopu.github.io/opcm) | OTel + Prometheus Centralised Monitoring, based on my journey in a professional project. |

<div id="works-explorer" hidden>
<input type="search" id="we-q" placeholder="Search works by title, topic or keyword" aria-label="Search works">
<div id="we-chips" class="we-chips" role="group" aria-label="Filter by category"></div>
<p id="we-status" class="we-status" aria-live="polite"></p>
<div id="we-results"></div>
</div>
<style>
#works-explorer{--we-line:color-mix(in srgb,currentColor 20%,transparent);--we-soft:color-mix(in srgb,currentColor 8%,transparent);margin:1.5rem 0}
#works-explorer input{width:100%;box-sizing:border-box;padding:.65rem .85rem;font:inherit;color:inherit;background:transparent;border:1px solid var(--we-line);border-radius:6px}
#works-explorer input:focus-visible,#works-explorer .we-chip:focus-visible{outline:2px solid currentColor;outline-offset:2px}
.we-chips{display:flex;flex-wrap:wrap;gap:.4rem;margin:.9rem 0 .4rem}
.we-chip{font:inherit;font-size:.85em;color:inherit;background:transparent;border:1px solid var(--we-line);border-radius:999px;padding:.25rem .75rem;cursor:pointer}
.we-chip span{opacity:.6;margin-left:.2rem}
.we-chip:hover{background:var(--we-soft)}
.we-chip[aria-pressed="true"]{background:var(--we-soft);border-color:currentColor;font-weight:600}
.we-status{font-size:.85em;opacity:.7;margin:.4rem 0 1rem}
.we-group{margin:0 0 1.75rem}
.we-h{font-weight:700;font-size:1.05em;padding-bottom:.35rem;margin-bottom:.75rem;border-bottom:1px solid var(--we-line)}
.we-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:.75rem}
.we-card{border:1px solid var(--we-line);border-radius:6px;padding:.8rem .9rem;display:flex;flex-direction:column;gap:.35rem}
.we-card:hover{background:var(--we-soft)}
.we-kind{align-self:flex-start;font-size:.72em;border:1px solid var(--we-line);border-radius:4px;padding:0 .4rem;opacity:.75}
.we-title{font-weight:600;line-height:1.3;overflow-wrap:anywhere}
.we-desc{font-size:.9em;opacity:.85;line-height:1.45}
</style>
<script>
(function () {
  var box = document.getElementById('works-explorer');
  var table = Array.from(document.querySelectorAll('table')).find(function (t) {
    return Array.from(t.querySelectorAll('th')).map(function (th) { return th.textContent.trim().toLowerCase(); }).join('|') === 'category|work|description';
  });
  if (!box || !table) return;
  var esc = function (s) { return s.replace(/[&<>"]/g, function (c) { return { '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;' }[c]; }); };
  var rows = Array.from(table.querySelectorAll('tbody tr')).map(function (tr) {
    var c = tr.children, a = c[1].querySelector('a'), href = a ? a.href : '';
    return {
      cat: c[0].textContent.trim(), work: c[1].innerHTML, desc: c[2].innerHTML, text: tr.textContent.toLowerCase(),
      kind: /github\.com/.test(href) ? 'GitHub' : /\.pdf($|\?)/i.test(href) ? 'PDF' : 'Page'
    };
  });
  var cats = [];
  rows.forEach(function (r) { if (cats.indexOf(r.cat) < 0) cats.push(r.cat); });
  var state = { cat: '', q: '' };
  var m = location.hash.match(/cat=([^&]*)/);
  if (m) { var c0 = decodeURIComponent(m[1]); if (cats.indexOf(c0) >= 0) state.cat = c0; }
  var q = box.querySelector('#we-q'), chips = box.querySelector('#we-chips'), out = box.querySelector('#we-results'), status = box.querySelector('#we-status');
  var count = function (c) { return rows.filter(function (r) { return r.cat === c; }).length; };
  chips.innerHTML = [['', 'All', rows.length]].concat(cats.map(function (c) { return [c, c, count(c)]; })).map(function (x) {
    return '<button type="button" class="we-chip" data-cat="' + esc(x[0]) + '" aria-pressed="' + (state.cat === x[0]) + '">' + esc(x[1]) + ' <span>' + x[2] + '</span></button>';
  }).join('');
  function draw() {
    var term = state.q.trim().toLowerCase(), shown = 0, html = '';
    cats.forEach(function (c) {
      if (state.cat && state.cat !== c) return;
      var items = rows.filter(function (r) { return r.cat === c && (!term || r.text.indexOf(term) >= 0); });
      if (!items.length) return;
      shown += items.length;
      html += '<section class="we-group"><div class="we-h">' + esc(c) + '</div><div class="we-grid">' + items.map(function (r) {
        return '<article class="we-card"><span class="we-kind">' + r.kind + '</span><div class="we-title">' + r.work + '</div><div class="we-desc">' + r.desc + '</div></article>';
      }).join('') + '</div></section>';
    });
    out.innerHTML = html || '<p>No works match. Try another word or pick a different category.</p>';
    status.textContent = 'Showing ' + shown + ' of ' + rows.length + ' works';
  }
  chips.addEventListener('click', function (e) {
    var b = e.target.closest('.we-chip');
    if (!b) return;
    state.cat = state.cat === b.dataset.cat ? '' : b.dataset.cat;
    chips.querySelectorAll('.we-chip').forEach(function (x) { x.setAttribute('aria-pressed', String(x.dataset.cat === state.cat)); });
    history.replaceState(null, '', state.cat ? '#cat=' + encodeURIComponent(state.cat) : location.pathname + location.search);
    draw();
  });
  q.addEventListener('input', function () { state.q = q.value; draw(); });
  table.style.display = 'none';
  box.hidden = false;
  draw();
})();
</script>

# Uncategorised Work
> May eventually be migrated to "Categorised Work".

**See**: [Untitled](./untitled/)