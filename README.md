<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Interactive Lebanon District Map</title>
<style>
  :root{
    --bg:#f6f8fb;
    --fill:#d9e2ef;
    --stroke:#2b3a4a;
    --hover:#2b90f0;
    --accent:#ff7a59;
    --tooltip-bg: rgba(17,17,17,0.95);
  }
  *{box-sizing:border-box}
  html,body{height:100%;margin:0;font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;background:var(--bg);color:#111}
  .wrap{max-width:1100px;margin:18px auto;padding:18px}
  header{display:flex;align-items:center;gap:16px;margin-bottom:10px}
  h1{font-size:20px;margin:0}
  p.lead{margin:0;color:#334155}
  #map-frame{background:white;border-radius:10px;padding:12px;box-shadow:0 6px 18px rgba(25,40,60,0.08);position:relative}
  #svg-container{width:100%;height:820px;display:flex;align-items:center;justify-content:center;overflow:hidden}
  /* svg styles applied after injection via CSS class .interactive-map */
  .interactive-map svg{width:100%;height:auto;max-height:780px}
  .interactive-map [data-qname]{
    fill:var(--fill);
    stroke:var(--stroke);
    stroke-width:0.8;
    transition:all .18s ease;
    cursor:pointer;
  }
  .interactive-map [data-qname]:hover{
    fill:var(--hover);
    transform:scale(1.004);
    filter:drop-shadow(0 6px 14px rgba(43,144,240,0.18));
  }
  .interactive-map .selected{fill:var(--accent) !important; color:#fff}

  /* Tooltip */
  #tooltip{
    position: absolute;
    pointer-events: none;
    display:none;
    min-width:160px;
    max-width:360px;
    background:var(--tooltip-bg);
    color:#fff;
    padding:10px 12px;
    border-radius:8px;
    font-size:14px;
    line-height:1.2;
    z-index:60;
    box-shadow:0 6px 18px rgba(2,6,23,0.35);
  }
  #tooltip b{display:block;font-weight:700;margin-bottom:6px}
  /* Legend / Controls */
  .controls{display:flex;gap:10px;align-items:center;margin-top:10px;flex-wrap:wrap}
  .legend-chip{display:inline-flex;align-items:center;gap:8px;padding:8px 10px;border-radius:999px;background:#fff;border:1px solid rgba(15,23,42,0.04)}
  .legend-swatch{width:14px;height:14px;border-radius:3px}
  /* Responsive */
  @media (max-width:720px){
    .wrap{padding:10px}
    #svg-container{height:560px}
  }
</style>
</head>
<body>
  <div class="wrap">
    <header>
      <h1>Interactive map — Lebanon districts</h1>
      <p class="lead">Hover a district to see candidate count. Click to open the district page. (Replace dataset below with your real counts/links.)</p>
    </header>

    <div id="map-frame" role="region" aria-label="Lebanon district map">
      <div class="controls" style="margin-bottom:8px">
        <div class="legend-chip"><span class="legend-swatch" style="background:var(--fill);border:1px solid var(--stroke)"></span> District</div>
        <div class="legend-chip"><span class="legend-swatch" style="background:var(--hover)"></span> Hover</div>
        <div class="legend-chip"><span class="legend-swatch" style="background:var(--accent)"></span> Selected / Clicked</div>
      </div>

      <div id="svg-container" class="interactive-map">
        <!-- SVG will be injected here -->
        <div style="text-align:center;color:#64748b">Loading map…</div>
      </div>

      <div id="tooltip" role="tooltip" aria-hidden="true"></div>
    </div>
  </div>

<script>
/*
  HOW IT WORKS
  1) This script fetches a district-level SVG of Lebanon from Wikimedia Commons.
  2) It injects the SVG into #svg-container, finds shape elements (paths/polygons) and
     attaches hover and click handlers.
  3) districtData is where you put candidate counts and the target link for each district.
     Key names should match district names used below (Wikipedia list). Edit as needed.
  4) On click, the script opens the link in the same window. You can change to open in a new tab.
*/

/* ============== CONFIG: Replace values here ============== */
/* District list + example candidate counts + page links.
   Keys are the district names as listed on Wikipedia (26 districts). Replace counts & links. */
const districtData = {
  "Akkar":            { candidates: 8,   link: "districts/akkar.html" },
  "Baalbek":          { candidates: 10,  link: "districts/baalbek.html" },
  "Hermel":           { candidates: 2,   link: "districts/hermel.html" },
  "Beirut":           { candidates: 12,  link: "districts/beirut.html" },
  "Rashaya":          { candidates: 3,   link: "districts/rashaya.html" },
  "Western Beqaa":    { candidates: 6,   link: "districts/western-beqaa.html" },
  "Zahlé":            { candidates: 9,   link: "districts/zahlé.html" },
  "Byblos":           { candidates: 5,   link: "districts/byblos.html" },
  "Keserwan":         { candidates: 7,   link: "districts/keserwan.html" },
  "Aley":             { candidates: 4,   link: "districts/aley.html" },
  "Baabda":           { candidates: 11,  link: "districts/baabda.html" },
  "Chouf":            { candidates: 6,   link: "districts/chouf.html" },
  "Matn":             { candidates: 13,  link: "districts/matn.html" },
  "Bint Jbeil":       { candidates: 4,   link: "districts/bint-jbeil.html" },
  "Hasbaya":          { candidates: 1,   link: "districts/hasbaya.html" },
  "Marjeyoun":        { candidates: 2,   link: "districts/marjeyoun.html" },
  "Nabatieh":         { candidates: 3,   link: "districts/nabatieh.html" },
  "Batroun":          { candidates: 3,   link: "districts/batroun.html" },
  "Bsharri":          { candidates: 2,   link: "districts/bsharri.html" },
  "Koura":            { candidates: 4,   link: "districts/koura.html" },
  "Miniyeh-Danniyeh": { candidates: 5,   link: "districts/miniyeh-danniyeh.html" },
  "Tripoli":          { candidates: 14,  link: "districts/tripoli.html" },
  "Zgharta":          { candidates: 3,   link: "districts/zgharta.html" },
  "Sidon":            { candidates: 7,   link: "districts/sidon.html" },
  "Jezzine":          { candidates: 2,   link: "districts/jezzine.html" },
  "Tyre":             { candidates: 5,   link: "districts/tyre.html" }
};
/* ============== End CONFIG ============== */

/* -------------- SVG source (Wikimedia Commons) --------------
   Source file: "GADM LBN Administrative Divisions.svg" on Wikimedia Commons.
   Using the hosted raw SVG file makes it easy to fetch and inject.
   License: see file page (Commons). Source used here as permitted for embedding.
   (If you prefer, download the SVG and host it on your own server, then update svgUrl.)
-----------------------------------------------------------------*/
const svgUrl = "https://upload.wikimedia.org/wikipedia/commons/9/98/GADM_LBN_Administrative_Divisions.svg";

/* Utility: sanitize text (simple) */
function escapeHtml(s){ return String(s).replace(/[&<>"']/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c])); }

/* Fetch and inject SVG */
async function loadAndPrepareMap() {
  const container = document.getElementById("svg-container");
  try {
    const resp = await fetch(svgUrl);
    if (!resp.ok) throw new Error("Map fetch failed: " + resp.status);
    const svgText = await resp.text();

    // Inject SVG markup
    container.innerHTML = svgText;

    // The Wikimedia SVG usually wraps shapes inside <g> elements with <path> or <polygon>.
    // We'll target all <path>, <polygon>, <circle>, <rect> — anything that looks like a region.
    const svgRoot = container.querySelector("svg");
    if (!svgRoot) throw new Error("No <svg> found in fetched file.");

    // Some SVGs include <title> nodes for regions; we will use them when available.
    // Normalize shapes: give each region a data-qname attribute (our canonical name)
    const candidateShapes = Array.from(svgRoot.querySelectorAll("path, polygon, rect, circle, g"))
      .filter(el => {
        // ignore very small shapes (coastline decorations) by bounding box area if possible
        // keep those that have an id or a title or class that looks like a region
        return el.id || el.querySelector?.("title") || el.getAttribute("class") || el.getAttribute("data-name");
      });

    // Map region names we know to SVG elements. We'll look for title text or the id.
    candidateShapes.forEach(el => {
      // get a friendly name in order of preference:
      let name = null;
      const title = el.querySelector ? el.querySelector("title") : null;
      if (title && title.textContent.trim()) name = title.textContent.trim();
      if (!name) name = el.getAttribute("data-name") || el.getAttribute("id") || el.getAttribute("inkscape:label") || el.getAttribute("name") || "";
      name = name.trim();

      // Normalize some common differences (example: "Zgharta" vs "Zgharta" — adjust as needed)
      // Set data-qname attribute for easy selection
      if (name) {
        el.setAttribute("data-qname", name);
        el.style.transition = "transform .15s ease";
      } else {
        // if no name, still assign a generated id so we can inspect it in devtools
        const auto = "region-" + Math.random().toString(36).slice(2,8);
        el.setAttribute("data-qname", auto);
      }
      // make pointer cursor
      el.style.cursor = "pointer";
    });

    // Attach events to interactive shapes (those with data-qname)
    attachInteractivity(svgRoot);

    // Improve appearance: set viewBox to fit container (if not already)
    svgRoot.setAttribute("preserveAspectRatio", "xMidYMid meet");

    // Accessibility
    svgRoot.setAttribute("role","img");
    svgRoot.setAttribute("aria-label","Lebanon administrative divisions map");
  } catch (err) {
    container.innerHTML = `<div style="color:#b91c1c;padding:18px">Failed to load map: ${escapeHtml(err.message)}</div>`;
    console.error(err);
  }
}

/* Attach hover + click handlers to shapes that have data-qname */
function attachInteractivity(svgRoot) {
  const tooltip = document.getElementById("tooltip");
  let lastHovered = null;

  const shapes = Array.from(svgRoot.querySelectorAll("[data-qname]"));
  shapes.forEach(shape => {
    const qname = shape.getAttribute("data-qname");

    // determine displayName (clean up certain ids if necessary)
    const displayName = qname.replace(/_/g," ").replace(/\b(qada|qadaa)\b/ig,"").trim() || qname;

    // Lookup district data (case-insensitive match)
    const matchKey = findDistrictKey(displayName);
    const data = matchKey ? districtData[matchKey] : null;

    // Add small title for non-visual readers (also appears on hover by default)
    shape.setAttribute("tabindex","0");
    shape.setAttribute("aria-label", displayName);

    // Hover / pointer events
    const showTooltip = (evt) => {
      lastHovered = shape;
      const rect = evt.currentTarget.getBoundingClientRect ? evt.currentTarget.getBoundingClientRect() : {left: evt.clientX, top: evt.clientY};
      const x = (evt.clientX || (rect.left + rect.width/2));
      const y = (evt.clientY || (rect.top + rect.height/2));

      // build tooltip HTML
      const count = data ? data.candidates : "—";
      const link = data ? data.link : null;
      const linkText = link ? `<div style="margin-top:8px"><a href="${escapeHtml(link)}" style="color:#93c5fd;text-decoration:underline" target="_blank" rel="noopener">Open page</a></div>` : "";
      tooltip.innerHTML = `<b>${escapeHtml(displayName)}</b>Candidates: <strong>${escapeHtml(String(count))}</strong>${linkText}`;

      tooltip.style.display = "block";
      tooltip.style.left = (x + 14) + "px";
      tooltip.style.top = (y + 14) + "px";
      tooltip.setAttribute("aria-hidden","false");
    };

    const moveTooltip = (evt) => {
      if (!lastHovered) return;
      const x = (evt.clientX || evt.touches?.[0]?.clientX || 0);
      const y = (evt.clientY || evt.touches?.[0]?.clientY || 0);
      tooltip.style.left = (x + 14) + "px";
      tooltip.style.top = (y + 14) + "px";
    };

    const hideTooltip = () => {
      lastHovered = null;
      tooltip.style.display = "none";
      tooltip.setAttribute("aria-hidden","true");
    };

    shape.addEventListener("mouseenter", showTooltip, {passive:true});
    shape.addEventListener("mousemove", moveTooltip, {passive:true});
    shape.addEventListener("mouseleave", hideTooltip, {passive:true});
    shape.addEventListener("focus", (e)=>showTooltip(e), {passive:true});
    shape.addEventListener("blur", hideTooltip, {passive:true});

    // Click: go to link if provided
    shape.addEventListener("click", (e) => {
      e.preventDefault();
      // mark selected visual
      shapes.forEach(s => s.classList.remove("selected"));
      shape.classList.add("selected");

      if (data && data.link) {
        // open same tab — change to window.open(data.link, "_blank") to open new tab
        window.location.href = data.link;
      } else {
        // no link: show a quick alert (you can remove)
        console.warn("No link set for", displayName);
        // Optionally, open a modal or do something else.
      }
    });

    // keyboard enter/space should trigger click
    shape.addEventListener("keydown", (ev) => {
      if (ev.key === "Enter" || ev.key === " ") {
        ev.preventDefault();
        shape.click();
      }
    });
  });
}

/* Try to find a districtData key that matches this region name (case-insensitive, fuzzy) */
function findDistrictKey(name){
  const keys = Object.keys(districtData);
  // direct case-insensitive match
  const direct = keys.find(k => k.toLowerCase() === name.toLowerCase());
  if (direct) return direct;
  // some svg names include governorate names or numbers — try partial match
  const partial = keys.find(k => name.toLowerCase().includes(k.toLowerCase()) || k.toLowerCase().includes(name.toLowerCase()));
  if (partial) return partial;
  // try splitting and matching important tokens
  const tokenMatch = keys.find(k => {
    const kn = k.toLowerCase().split(/[\s-]+/).filter(Boolean);
    return kn.some(t => name.toLowerCase().includes(t));
  });
  if (tokenMatch) return tokenMatch;
  // no match found
  return null;
}

/* Start */
loadAndPrepareMap();
</script>
</body>
</html>
