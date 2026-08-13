<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Shelf Atlas</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Newsreader:ital,wght@0,400;0,500;0,600;1,400&family=Inter:wght@400;500&display=swap');

:root{
  --bg: #fafaf8;
  --ink: #1a1a18;
  --mid: #6b6b64;
  --line: #e2e0d9;
}
*{ box-sizing: border-box; }
html,body{ margin:0; }
body{
  background: var(--bg);
  color: var(--ink);
  font-family: 'Inter', sans-serif;
  padding: 48px 24px 80px;
}
.wrap{ max-width: 680px; margin: 0 auto; }

.top{ display:flex; align-items:flex-start; justify-content:space-between; gap:16px; margin-bottom: 4px; }
h1{ font-family: 'Newsreader', serif; font-weight: 500; font-size: 26px; margin: 0 0 4px; }
.sub{ font-size: 13px; color: var(--mid); margin: 0 0 20px; line-height: 1.5; max-width: 52ch; }

.tabs{ display:flex; gap: 18px; border-bottom: 1px solid var(--line); margin-bottom: 26px; }
.tab-btn{
  border: none; background: none; cursor:pointer;
  font-family: 'Inter', sans-serif; font-size: 13px; color: var(--mid);
  padding: 8px 2px; margin-bottom: -1px; border-bottom: 2px solid transparent;
}
.tab-btn.active{ color: var(--ink); border-bottom-color: var(--ink); }
.tab-panel{ display:none; }
.tab-panel.active{ display:block; }

.add{
  display: grid; grid-template-columns: 1.2fr 1.2fr 0.6fr 0.9fr auto; gap: 6px; margin-bottom: 10px;
}
@media (max-width: 620px){ .add{ grid-template-columns: 1fr 1fr; } }
.add input{
  border: none; border-bottom: 1px solid var(--line); background: transparent;
  padding: 6px 2px; font-size: 13px; font-family: 'Inter', sans-serif; color: var(--ink); outline: none;
}
.add input::placeholder{ color: #b4b2a8; }
.add input:focus{ border-bottom-color: var(--ink); }
.add button{
  border: 1px solid var(--ink); background: transparent; color: var(--ink);
  font-size: 12px; font-family: 'Inter', sans-serif; padding: 0 14px; cursor: pointer;
  transition: background 0.15s ease, color 0.15s ease;
}
.add button:hover{ background: var(--ink); color: var(--bg); }

.search{
  display: block; width: 100%; border: none; border-bottom: 1px solid var(--line); background: transparent;
  padding: 6px 2px; font-size: 13px; font-family: 'Inter', sans-serif; color: var(--ink); outline: none; margin-bottom: 24px;
}
.search::placeholder{ color: #b4b2a8; }
.search:focus{ border-bottom-color: var(--ink); }

/* floating edit/suggestion panel */
.overlay-close{ position: fixed; inset:0; z-index: 55; }
.suggestion{
  position: fixed; z-index: 60; border: 1px solid var(--line); background: #f2f1eb;
  padding: 14px 16px; width: 300px; max-width: 90vw; box-shadow: 0 14px 30px rgba(0,0,0,0.18);
}
.sugg-row{ display: grid; grid-template-columns: 1fr 1fr; gap: 6px; margin-bottom: 8px; }
.sugg-row2{ display:block; margin-bottom: 8px; }
.sugg-row input, .sugg-row2 input, .sugg-row2 textarea{
  border: none; border-bottom: 1px solid #cfccc0; background: transparent;
  padding: 5px 2px; font-size: 13px; font-family: 'Inter', sans-serif; color: var(--ink); outline: none; width: 100%;
}
.sugg-row2 textarea{ resize: vertical; min-height: 32px; font-family: 'Inter', sans-serif; }
.sugg-row input:focus, .sugg-row2 input:focus, .sugg-row2 textarea:focus{ border-bottom-color: var(--ink); }
.sugg-label{ font-size: 10px; text-transform: uppercase; letter-spacing: 0.06em; color: var(--mid); display:block; margin-bottom:2px; }
.sugg-reason{ font-size: 12px; color: var(--mid); font-style: italic; margin-bottom: 12px; line-height: 1.4; }
.sugg-warn{
  font-size: 11.5px; color: #a34a28; font-weight: 500; margin-bottom: 10px;
  border-left: 2px solid #a34a28; padding-left: 8px; line-height: 1.4;
}
.sugg-actions{ display: flex; gap: 8px; }
.sugg-actions button{
  border: 1px solid var(--ink); background: transparent; color: var(--ink);
  font-size: 12px; font-family: 'Inter', sans-serif; padding: 6px 14px; cursor: pointer;
}
#sg-confirm{ background: var(--ink); color: var(--bg); }
.sugg-actions button:hover{ opacity: 0.8; }

.cluster{ margin-bottom: 34px; }
.cluster.hidden{ display:none; }
.cluster.drag-over-top{ box-shadow: inset 0 2px 0 var(--ink); }
.cluster-head{
  display: flex; align-items: baseline; gap: 8px; border-bottom: 1px solid var(--line);
  padding-bottom: 6px; margin-bottom: 10px; cursor: grab;
}
.cluster-head .drag-handle{ color: #c8c5ba; font-size: 12px; cursor: grab; user-select:none; }
.cluster-head .name{ font-family: 'Newsreader', serif; font-style: italic; font-size: 15px; cursor: pointer; }
.cluster-head .name:hover{ text-decoration: underline; }
.cluster-head .bio-icon{ font-size: 10px; color: var(--mid); cursor:pointer; }
.cluster-head .range{ font-size: 11px; color: var(--mid); margin-left: auto; letter-spacing: 0.02em; }

.cluster-summary{
  font-size: 12.5px; color: var(--mid); line-height: 1.55; padding: 2px 0 14px; max-width: 56ch; display: none;
}
.cluster-summary.open{ display: block; }
.cluster-summary .loading{ font-style: italic; }

.book{
  display: grid; grid-template-columns: 1fr auto auto 20px; align-items:center; gap: 10px;
  padding: 7px 0; border-bottom: 1px solid transparent;
}
.book.hidden{ display:none; }
.book:hover{ border-bottom-color: var(--line); }
.book .titlewrap{ min-width:0; }
.book .title{ font-size: 13.5px; display:block; }
.book .note{ font-size: 11px; color: var(--mid); font-style: italic; display:block; margin-top:1px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
.book .author{ font-size: 12.5px; color: var(--mid); white-space:nowrap; }
.book .year{ font-size: 11.5px; color: var(--mid); font-variant-numeric: tabular-nums; min-width: 34px; text-align: right; }
.book .del{ visibility: hidden; border: none; background: none; color: var(--mid); font-size: 14px; cursor: pointer; line-height: 1; padding: 0; }
.book:hover .del{ visibility: visible; }
.del:hover{ color: var(--ink); }
.book.clickable{ cursor: pointer; }

.bridge{ padding: 6px 0 18px; }
.bridge-link{ font-size: 11px; color: var(--mid); background:none; border:none; cursor:pointer; padding:0; font-family:'Inter',sans-serif; }
.bridge-link:hover{ color: var(--ink); text-decoration: underline; }
.bridge-results{ margin-top: 8px; display:none; }
.bridge-results.open{ display:block; }
.bridge-item{ display:flex; align-items:baseline; gap:8px; padding: 5px 0; font-size: 12.5px; }
.bridge-item .bi-text{ flex:1; color: var(--mid); }
.bridge-item .bi-text b{ color: var(--ink); font-weight: 500; }
.bridge-item button{
  border: 1px solid var(--line); background: transparent; color: var(--mid); font-size: 11px;
  padding: 3px 8px; cursor:pointer; font-family:'Inter',sans-serif; flex-shrink:0;
}
.bridge-item button:hover{ border-color: var(--ink); color: var(--ink); }

.wishlist{ margin-top: 44px; padding-top: 18px; border-top: 1px solid var(--line); }
.wishlist h2{ font-family:'Newsreader', serif; font-weight:500; font-style:italic; font-size:16px; margin:0 0 10px; }
.wish-item{ display:flex; align-items:baseline; gap:10px; padding:6px 0; border-bottom:1px solid transparent; }
.wish-item:hover{ border-bottom-color: var(--line); }
.wish-item .wi-text{ flex:1; font-size:13px; }
.wish-item .wi-reason{ font-size:11px; color:var(--mid); font-style:italic; display:block; }
.wish-item button{
  border: 1px solid var(--line); background: transparent; color: var(--mid); font-size: 11px;
  padding: 4px 9px; cursor:pointer; font-family:'Inter',sans-serif;
}
.wish-item button:hover{ border-color: var(--ink); color: var(--ink); }
.wish-empty{ font-size:12px; color:var(--mid); }

/* Shelf tab: bookcase with multiple wrapping shelf rows */
.bookcase{
  border: 10px solid #7a5c3e;
  border-radius: 4px;
  background: #efe8d8;
  padding: 18px 16px 14px;
  box-shadow: inset 0 0 0 1px rgba(0,0,0,0.06), 0 6px 16px rgba(0,0,0,0.12);
}
.case-row{ margin-bottom: 16px; }
.case-strip{ display:flex; align-items: flex-end; gap: 3px; }
.viz-block{
  height: 42px; border-radius: 3px 3px 1px 1px; cursor: pointer; flex-shrink: 0;
  transition: transform 0.12s ease, box-shadow 0.12s ease;
  box-shadow: inset 0 0 0 1px rgba(0,0,0,0.06);
}
.viz-block:hover{ transform: translateY(-4px); box-shadow: 0 4px 8px rgba(0,0,0,0.18); }
.case-ledge{
  height: 6px; margin-top: -1px; border-radius: 0 0 2px 2px;
  background: linear-gradient(180deg, #cabb9c, #a9977a);
  box-shadow: 0 3px 5px rgba(0,0,0,0.15);
}
.viz-tooltip{
  position: fixed; z-index: 60; background: var(--ink); color: var(--bg); font-size: 11.5px;
  padding: 6px 9px; border-radius: 4px; pointer-events:none; max-width:220px; line-height:1.4;
}

/* Encyclopedia tab */
.enc-row{
  display: grid; grid-template-columns: 1fr auto auto; align-items:center; gap: 10px;
  padding: 9px 0; border-bottom: 1px solid var(--line); cursor: pointer;
}
.enc-row:hover .enc-title{ text-decoration: underline; }
.enc-title{ font-size: 13.5px; }
.enc-author{ font-size: 12.5px; color: var(--mid); }
.enc-year{ font-size: 11.5px; color: var(--mid); font-variant-numeric: tabular-nums; min-width: 34px; text-align: right; }
.enc-cluster{ font-size: 11px; color: var(--mid); display:block; margin-top:1px; }

.empty{ text-align: center; color: var(--mid); font-size: 13px; padding: 40px 0; border-top: 1px solid var(--line); }
</style>
</head>
<body>
<div class="wrap">
  <div class="top">
    <div>
      <h1>Shelf Atlas</h1>
      <p class="sub">Add books in Collection, browse them as a bookcase in Shelf, and edit details in Encyclopedia.</p>
    </div>
    <div class="exports">
      <button id="export-json">Export JSON</button>
      <button id="export-csv">Export CSV</button>
    </div>
  </div>

  <div class="tabs">
    <button class="tab-btn" data-tab="collection">Collection</button>
    <button class="tab-btn active" data-tab="shelf">Shelf</button>
    <button class="tab-btn" data-tab="encyclopedia">Encyclopedia</button>
  </div>

  <div id="tab-shelf" class="tab-panel active">
    <div class="bookcase">
      <div id="case-rows"></div>
    </div>
  </div>

  <div id="tab-collection" class="tab-panel">
    <div class="add">
      <input id="in-title" placeholder="Title">
      <input id="in-author" placeholder="Author">
      <input id="in-year" placeholder="Year" inputmode="numeric">
      <input id="in-cluster" list="cluster-list" placeholder="Cluster">
      <button id="add-btn">Add</button>
    </div>
    <datalist id="cluster-list"></datalist>

    <input id="search" class="search" placeholder="Search your collection…">
    <div id="clusters"></div>
    <div id="empty" class="empty" style="display:none;">Nothing shelved yet.</div>

    <div class="wishlist">
      <h2>Wishlist</h2>
      <div id="wishlist-items"></div>
    </div>
  </div>

  <div id="tab-encyclopedia" class="tab-panel">
    <input id="enc-search" class="search" placeholder="Search the encyclopedia…">
    <div id="enc-list"></div>
  </div>
</div>

<script>
// --- FIX 1: Polyfill for window.storage to use native browser localStorage ---
window.storage = {
  get: async (key) => {
    const val = localStorage.getItem(key);
    return val ? { value: val } : null;
  },
  set: async (key, val) => {
    localStorage.setItem(key, val);
  }
};

let books = [];
let clusterOrder = null;
let clusterSummaries = {};
let bridgeCache = {};
let wishlist = [];

const PALETTE = ['#7B2D3E','#2F6E6A','#B8862F','#3B4E7A','#A34A28','#5C7A5A','#6B4577','#3F6B47','#8A5A2E','#4A5A7A'];

const SEED = [
  {title:"The Great Gatsby", author:"F. Scott Fitzgerald", year:1925, cluster:"Lost Generation"},
  {title:"The Sun Also Rises", author:"Ernest Hemingway", year:1926, cluster:"Lost Generation"},
  {title:"On the Road", author:"Jack Kerouac", year:1957, cluster:"Beat Generation"},
  {title:"The Call of the Wild", author:"Jack London", year:1903, cluster:"American Naturalism"},
  {title:"Cat's Cradle", author:"Kurt Vonnegut", year:1963, cluster:"Postmodern / Black Humor"},
  {title:"Infinite Jest", author:"David Foster Wallace", year:1996, cluster:"Postmodern / Black Humor"},
  {title:"Tenth of December", author:"George Saunders", year:2013, cluster:"Postmodern / Black Humor"},
  {title:"The Metamorphosis", author:"Franz Kafka", year:1915, cluster:"High Modernism"},
  {title:"Ulysses", author:"James Joyce", year:1922, cluster:"High Modernism"},
  {title:"Mrs Dalloway", author:"Virginia Woolf", year:1925, cluster:"High Modernism"},
  {title:"As I Lay Dying", author:"William Faulkner", year:1930, cluster:"Southern Gothic"},
  {title:"Blood Meridian", author:"Cormac McCarthy", year:1985, cluster:"Southern Gothic"},
  {title:"Sula", author:"Toni Morrison", year:1973, cluster:"Black Women's Fiction, 70s\u201380s"},
  {title:"A Man's Place", author:"Annie Ernaux", year:1983, cluster:"Autofiction"},
  {title:"Septology", author:"Jon Fosse", year:2019, cluster:"Autofiction"},
  {title:"My Struggle", author:"Karl Ove Knausgaard", year:2009, cluster:"Autofiction"},
];

const MERGE_ADD = [
  {title:"Portnoy's Complaint", author:"Philip Roth", year:1969, cluster:"Postwar American Jewish Writers"},
  {title:"Herzog", author:"Saul Bellow", year:1964, cluster:"Postwar American Jewish Writers"},
  {title:"The Fixer", author:"Bernard Malamud", year:1966, cluster:"Postwar American Jewish Writers"},
  {title:"A Pale View of Hills", author:"Kazuo Ishiguro", year:1982, cluster:"1983 Granta Generation (British)"},
  {title:"Money", author:"Martin Amis", year:1984, cluster:"1983 Granta Generation (British)"},
  {title:"The Comfort of Strangers", author:"Ian McEwan", year:1981, cluster:"1983 Granta Generation (British)"},
  {title:"White Teeth", author:"Zadie Smith", year:2000, cluster:"1983 Granta Generation (British)"},
  {title:"Normal People", author:"Sally Rooney", year:2018, cluster:"Millennial Women's Fiction"},
  {title:"Eileen", author:"Ottessa Moshfegh", year:2015, cluster:"Millennial Women's Fiction"},
  {title:"A Little Life", author:"Hanya Yanagihara", year:2015, cluster:"Maximalist Trauma Novel"},
  {title:"Devil in a Blue Dress", author:"Walter Mosley", year:1990, cluster:"African American Noir"},
  {title:"A Rage in Harlem", author:"Chester Himes", year:1957, cluster:"African American Noir"},
  {title:"Blanche on the Lam", author:"Barbara Neely", year:1992, cluster:"African American Noir"},
  {title:"The Sentence", author:"Louise Erdrich", year:2021, cluster:"Native American Literary Renaissance"},
  {title:"Ceremony", author:"Leslie Marmon Silko", year:1977, cluster:"Native American Literary Renaissance"},
  {title:"House Made of Dawn", author:"N. Scott Momaday", year:1968, cluster:"Native American Literary Renaissance"},
  {title:"All the Light We Cannot See", author:"Anthony Doerr", year:2014, cluster:"Lyrical Literary Historical Fiction"},
  {title:"The Book Thief", author:"Markus Zusak", year:2005, cluster:"Lyrical Literary Historical Fiction"},
  {title:"And Their Children After Them", author:"Nicolas Mathieu", year:2018, cluster:"French Working-Class Realism"},
  {title:"Lives of Girls and Women", author:"Alice Munro", year:1971, cluster:"Short Story Masters"},
  {title:"Who Will Run the Frog Hospital?", author:"Lorrie Moore", year:1994, cluster:"Short Story Masters"},
  {title:"The Shipping News", author:"Annie Proulx", year:1993, cluster:"Landscape as Character"},
  {title:"Their Eyes Were Watching God", author:"Zora Neale Hurston", year:1937, cluster:"Harlem Renaissance"},
  {title:"James", author:"Percival Everett", year:2024, cluster:"Revisionist American Retellings"},
];

async function load(){
  try{
    const res = await window.storage.get('shelf-atlas-books');
    books = res && res.value ? JSON.parse(res.value) : null;
  }catch(e){ books = null; }
  if(!books){ books = SEED.map((b,i)=>({...b, id:'s'+i})); await save(); }

  let changed = false;
  MERGE_ADD.forEach((b,i)=>{
    const exists = books.some(x => x.title.toLowerCase() === b.title.toLowerCase());
    if(!exists){ books.push({...b, id:'m'+Date.now()+i}); changed = true; }
  });
  if(changed) await save();

  try{ const o = await window.storage.get('shelf-atlas-order'); clusterOrder = o && o.value ? JSON.parse(o.value) : null; }catch(e){ clusterOrder = null; }
  try{ const s = await window.storage.get('shelf-atlas-summaries'); clusterSummaries = s && s.value ? JSON.parse(s.value) : {}; }catch(e){ clusterSummaries = {}; }
  try{ const br = await window.storage.get('shelf-atlas-bridges'); bridgeCache = br && br.value ? JSON.parse(br.value) : {}; }catch(e){ bridgeCache = {}; }
  try{ const w = await window.storage.get('shelf-atlas-wishlist'); wishlist = w && w.value ? JSON.parse(w.value) : []; }catch(e){ wishlist = []; }

  render();
}
async function save(){ try{ await window.storage.set('shelf-atlas-books', JSON.stringify(books)); }catch(e){ console.error(e); } }
async function saveOrder(){ try{ await window.storage.set('shelf-atlas-order', JSON.stringify(clusterOrder)); }catch(e){ console.error(e); } }
async function saveSummaries(){ try{ await window.storage.set('shelf-atlas-summaries', JSON.stringify(clusterSummaries)); }catch(e){ console.error(e); } }
async function saveBridges(){ try{ await window.storage.set('shelf-atlas-bridges', JSON.stringify(bridgeCache)); }catch(e){ console.error(e); } }
async function saveWishlist(){ try{ await window.storage.set('shelf-atlas-wishlist', JSON.stringify(wishlist)); }catch(e){ console.error(e); } }

function escapeHtml(s){
  return String(s).replace(/[&<>"']/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));
}
function clusterColor(cluster){
  let h = 0;
  for(let i=0;i<cluster.length;i++) h = (h*31 + cluster.charCodeAt(i)) >>> 0;
  return PALETTE[h % PALETTE.length];
}
function shade(hex, pct){
  const num = parseInt(hex.slice(1),16);
  let r = (num>>16) + Math.round(255*pct/100);
  let g = ((num>>8)&0xff) + Math.round(255*pct/100);
  let bl = (num&0xff) + Math.round(255*pct/100);
  r=Math.max(0,Math.min(255,r)); g=Math.max(0,Math.min(255,g)); bl=Math.max(0,Math.min(255,bl));
  return `#${(1<<24 | r<<16 | g<<8 | bl).toString(16).slice(1)}`;
}

// --- FIX 2: Dynamic API Key loading, updated headers & correct Anthropic model ---
async function callClaude(system, userMsg, maxTokens){
  let apiKey = localStorage.getItem('anthropic-api-key');
  if (!apiKey) {
    apiKey = prompt("Please enter your Anthropic API Key to use the AI features (it will be saved in your browser):");
    if (apiKey) localStorage.setItem('anthropic-api-key', apiKey);
    else throw new Error("API Key required.");
  }

  const res = await fetch("https://api.anthropic.com/v1/messages", {
    method: "POST",
    headers: { 
      "Content-Type": "application/json",
      "x-api-key": apiKey,
      "anthropic-version": "2023-06-01",
      "anthropic-dangerous-direct-browser-access": "true" 
    },
    body: JSON.stringify({ 
      model: "claude-3-5-sonnet-20240620", 
      max_tokens: maxTokens||300, 
      system, 
      messages:[{role:"user", content:userMsg}] 
    })
  });
  const data = await res.json();
  if (data.error) throw new Error(data.error.message);
  return (data.content||[]).filter(b=>b.type==='text').map(b=>b.text).join('').trim();
}

async function enrichBook(title, existingClusters){
  const sys = `You identify books from a title and return ONLY a JSON object, no prose, no markdown fences. `
    + `Fields: "author", "year" (integer), "cluster" (a literary movement, scene, generational cohort, or broad tradition), "reason" (one sentence, under 20 words, on why it fits), "confidence" ("high" or "low"). `
    + `Reuse one of these existing clusters ONLY if the book is a genuine fit — same national/literary tradition, era, and mode, not just a loose era overlap: ${JSON.stringify(existingClusters)}. `
    + `Do not force a book into an existing cluster from the wrong country or tradition just because it's convenient (e.g. do not put a 19th-century Russian novel in a "French Realism" cluster). If none of the existing clusters are a genuine fit, invent an accurate new one instead, and prefer broader/larger groupings over narrow ones when inventing. `
    + `Set "confidence" to "low" if you are guessing at the title, unsure of the exact book, or unsure the cluster is right — the person will double-check anything marked low. `
    + `Respond with exactly: {"author":"...","year":1234,"cluster":"...","reason":"...","confidence":"high"}`;
  const text = await callClaude(sys, title, 300);
  
  // --- FIX 3: Resilient JSON parsing regex ---
  return JSON.parse(text.replace(/```(?:json)?\s*|\s*```/ig, '').trim());
}

async function summarizeCluster(clusterName, titles){
  const sys = `You are a literary critic. In 2-3 sentences, write a short bio of the literary movement/cluster "${clusterName}": what defines it (style, themes, era) and how its member books/authors relate historically or stylistically. Be specific, not generic. Plain prose, no markdown.`;
  return await callClaude(sys, `Books in this cluster: ${titles.join(', ')}`, 220);
}

async function suggestBridge(clusterA, clusterB){
  const sys = `You are a literary critic helping bridge two adjacent shelves in a personal library. Suggest 1-2 real, specific books that would form a natural transition between the movement/cluster "${clusterA}" and the movement/cluster "${clusterB}" — books with a genuine historical, stylistic, or influence-based connection to both, not vague vibes. Return ONLY a JSON array, no prose, no markdown fences, like: [{"title":"...","author":"...","reason":"one short sentence, under 22 words"}]`;
  const text = await callClaude(sys, `Cluster A: ${clusterA}\nCluster B: ${clusterB}`, 350);
  
  // --- FIX 3: Resilient JSON parsing regex ---
  return JSON.parse(text.replace(/```(?:json)?\s*|\s*```/ig, '').trim());
}

// ---------- floating panel (edit / add-suggestion) — Encyclopedia + Add flow only ----------
let pending = null;

function closePanel(){
  document.querySelectorAll('.overlay-close').forEach(o=>o.remove());
  const host = document.getElementById('suggestion');
  if(host){ host.remove(); }
  pending = null;
}

function openEdit(b, anchorEl){
  pending = { editId: b.id, title: b.title, author: b.author, year: b.year, cluster: b.cluster, note: b.note||'', reason: 'Editing existing entry — adjust and confirm.' };
  showPanel(anchorEl);
}

function showPanel(anchorEl){
  const old = document.getElementById('suggestion');
  if(old) old.remove();
  document.querySelectorAll('.overlay-close').forEach(o=>o.remove());
  if(!pending) return;

  const overlay = document.createElement('div');
  overlay.className = 'overlay-close';
  overlay.addEventListener('click', closePanel);
  document.body.appendChild(overlay);

  const host = document.createElement('div');
  host.id = 'suggestion';
  host.className = 'suggestion';
  host.innerHTML = `
    <div class="sugg-row">
      <div><span class="sugg-label">Title</span><input id="sg-title" value="${escapeHtml(pending.title)}"></div>
      <div><span class="sugg-label">Author</span><input id="sg-author" value="${escapeHtml(pending.author||'')}"></div>
      <div><span class="sugg-label">Year</span><input id="sg-year" value="${pending.year ?? ''}"></div>
      <div><span class="sugg-label">Cluster</span><input id="sg-cluster" value="${escapeHtml(pending.cluster||'')}"></div>
    </div>
    <div class="sugg-row2">
      <span class="sugg-label">Note</span>
      <textarea id="sg-note" rows="2">${escapeHtml(pending.note||'')}</textarea>
    </div>
    <div class="sugg-reason">${pending.reason ? escapeHtml(pending.reason) : ''}</div>
    ${pending.confidence === 'low' ? `<div class="sugg-warn">Low confidence — double-check this title, author, and cluster before adding.</div>` : ''}
    <div class="sugg-actions">
      <button id="sg-confirm">${pending.editId ? 'Save changes' : 'Add to shelf'}</button>
      <button id="sg-cancel">${pending.editId ? 'Cancel' : 'Discard'}</button>
    </div>
  `;
  document.body.appendChild(host);

  const rect = anchorEl ? anchorEl.getBoundingClientRect() : { left: window.innerWidth/2 - 150, top: 100, bottom: 100, width: 300 };
  const panelW = 300;
  let left = rect.left;
  if(left + panelW > window.innerWidth - 16) left = window.innerWidth - panelW - 16;
  if(left < 16) left = 16;
  host.style.left = left + 'px';
  const estHeight = 210;
  let top = rect.top - estHeight - 10;
  if(top < 10) top = rect.bottom + 10;
  host.style.top = top + 'px';

  document.getElementById('sg-confirm').addEventListener('click', ()=>{
    const title = document.getElementById('sg-title').value.trim();
    const author = document.getElementById('sg-author').value.trim() || 'Unknown';
    let year = parseInt(document.getElementById('sg-year').value,10); year = isNaN(year)?null:year;
    const cluster = document.getElementById('sg-cluster').value.trim() || 'Unsorted';
    const note = document.getElementById('sg-note').value.trim();
    if(!title) return;
    if(pending.editId){
      const b = books.find(x=>x.id===pending.editId);
      if(b){ b.title=title; b.author=author; b.year=year; b.cluster=cluster; b.note=note; }
    } else {
      books.push({ id:'b'+Date.now(), title, author, year, cluster, note });
    }
    closePanel(); save(); render();
  });
  document.getElementById('sg-cancel').addEventListener('click', closePanel);
}

async function addBook(){
  const titleInput = document.getElementById('in-title');
  const authorInput = document.getElementById('in-author');
  const yearInput = document.getElementById('in-year');
  const clusterInput = document.getElementById('in-cluster');
  const btn = document.getElementById('add-btn');

  const title = titleInput.value.trim();
  let author = authorInput.value.trim();
  let cluster = clusterInput.value.trim();
  let year = parseInt(yearInput.value.trim(),10); year = isNaN(year)?null:year;
  if(!title) return;

  if(!author || !cluster || year==null){
    const existingClusters = [...new Set(books.map(b=>b.cluster))];
    const original = btn.textContent;
    btn.textContent = '…'; btn.disabled = true;
    try{
      const guess = await enrichBook(title, existingClusters);
      pending = {
        title, author: author || guess.author || '',
        year: year!=null ? year : (typeof guess.year==='number' ? guess.year : null),
        cluster: cluster || guess.cluster || '', note: '', reason: guess.reason || '',
        confidence: guess.confidence || 'high'
      };
      [titleInput, authorInput, yearInput, clusterInput].forEach(el=>el.value='');
      btn.textContent = original; btn.disabled = false;
      showPanel(btn);
      return;
    }catch(e){ console.error('enrich failed', e); btn.textContent = original; btn.disabled = false; }
  }

  if(!author) author = 'Unknown';
  if(!cluster) cluster = 'Unsorted';
  books.push({ id:'b'+Date.now(), title, author, year, cluster, note:'' });
  [titleInput, authorInput, yearInput, clusterInput].forEach(el=>el.value='');
  save(); render();
}

// ---------- ordering helpers ----------
function avgYear(list){
  const y = list.filter(b=>b.year!=null);
  return y.length ? y.reduce((s,b)=>s+b.year,0)/y.length : 9999;
}
function orderedClusterNames(grouped){
  const names = Object.keys(grouped);
  if(clusterOrder){
    const known = clusterOrder.filter(n=>names.includes(n));
    const missing = names.filter(n=>!clusterOrder.includes(n)).sort((a,b)=>avgYear(grouped[a])-avgYear(grouped[b]));
    return [...known, ...missing];
  }
  return names.sort((a,b)=>avgYear(grouped[a])-avgYear(grouped[b]));
}
let dragClusterName = null;

// ---------- render: Collection tab ----------
function render(){
  const container = document.getElementById('clusters');
  const empty = document.getElementById('empty');
  container.innerHTML = '';

  const names = [...new Set(books.map(b=>b.cluster))];
  document.getElementById('cluster-list').innerHTML = names.map(c=>`<option value="${escapeHtml(c)}">`).join('');

  if(!books.length){ empty.style.display='block'; renderWishlist(); renderCase(); renderEncyclopedia(); return; }
  empty.style.display='none';

  const grouped = {};
  books.forEach(b=>{ (grouped[b.cluster]=grouped[b.cluster]||[]).push(b); });
  const ordered = orderedClusterNames(grouped);
  if(!clusterOrder){ clusterOrder = ordered.slice(); saveOrder(); }

  ordered.forEach((cluster, idx)=>{
    const list = grouped[cluster].slice().sort((a,b)=>(a.year??9999)-(b.year??9999));
    const years = list.filter(b=>b.year!=null).map(b=>b.year);
    const range = years.length ? (Math.min(...years)===Math.max(...years) ? Math.min(...years) : `${Math.min(...years)}\u2013${Math.max(...years)}`) : '';

    const sec = document.createElement('div');
    sec.className = 'cluster';
    sec.dataset.cluster = cluster.toLowerCase();
    sec.innerHTML = `
      <div class="cluster-head" draggable="true">
        <span class="drag-handle">\u2807\u2807</span>
        <span class="name">${escapeHtml(cluster)}</span>
        <span class="bio-icon" title="Read the bio">\u24d8</span>
        <span class="range">${range}</span>
      </div>
      <div class="cluster-summary"></div>
    `;

    const head = sec.querySelector('.cluster-head');
    head.addEventListener('dragstart', e=>{ dragClusterName = cluster; e.dataTransfer.setData('text/plain', cluster); });
    sec.addEventListener('dragover', e=>{ e.preventDefault(); sec.classList.add('drag-over-top'); });
    
    // --- FIX 4: Prevent bubbling flicker on dragleave ---
    sec.addEventListener('dragleave', (e)=>{ 
      if (!sec.contains(e.relatedTarget)) {
        sec.classList.remove('drag-over-top');
      }
    });

    sec.addEventListener('drop', e=>{
      e.preventDefault(); sec.classList.remove('drag-over-top');
      if(!dragClusterName || dragClusterName===cluster) return;
      const cur = orderedClusterNames(grouped).filter(n=>n!==dragClusterName);
      cur.splice(cur.indexOf(cluster), 0, dragClusterName);
      clusterOrder = cur; saveOrder(); render();
    });

    const summaryEl = sec.querySelector('.cluster-summary');
    const openBio = async ()=>{
      const isOpen = summaryEl.classList.contains('open');
      if(isOpen){ summaryEl.classList.remove('open'); return; }
      summaryEl.classList.add('open');
      if(clusterSummaries[cluster]){ summaryEl.textContent = clusterSummaries[cluster]; return; }
      summaryEl.innerHTML = '<span class="loading">Reading the shelf…</span>';
      try{
        const text = await summarizeCluster(cluster, list.map(b=>`${b.title} (${b.author})`));
        clusterSummaries[cluster] = text; saveSummaries(); summaryEl.textContent = text;
      }catch(e){ summaryEl.textContent = 'Could not generate a bio right now.'; }
    };
    sec.querySelector('.name').addEventListener('click', openBio);
    sec.querySelector('.bio-icon').addEventListener('click', openBio);

    list.forEach(b=>{
      const row = document.createElement('div');
      row.className = 'book';
      row.dataset.id = b.id;
      row.dataset.search = `${b.title} ${b.author} ${b.cluster} ${b.note||''}`.toLowerCase();
      row.innerHTML = `
        <span class="titlewrap">
          <span class="title">${escapeHtml(b.title)}</span>
          ${b.note ? `<span class="note">${escapeHtml(b.note)}</span>` : ''}
        </span>
        <span class="author">${escapeHtml(b.author)}</span>
        <span class="year">${b.year ?? ''}</span>
        <button class="del" title="Remove">\u00d7</button>
      `;
      row.querySelector('.del').addEventListener('click', (e)=>{ e.stopPropagation(); books = books.filter(x=>x.id!==b.id); save(); render(); });
      sec.appendChild(row);
    });

    container.appendChild(sec);

    if(idx < ordered.length - 1){
      const nextCluster = ordered[idx+1];
      const key = `${cluster}|${nextCluster}`;
      const bridge = document.createElement('div');
      bridge.className = 'bridge';
      bridge.innerHTML = `<button class="bridge-link">Suggest a bridge to \u201c${escapeHtml(nextCluster)}\u201d \u2192</button><div class="bridge-results"></div>`;
      const linkBtn = bridge.querySelector('.bridge-link');
      const resultsEl = bridge.querySelector('.bridge-results');
      const renderBridgeItems = (items)=>{
        resultsEl.innerHTML = items.map((it,i)=>`
          <div class="bridge-item">
            <span class="bi-text"><b>${escapeHtml(it.title)}</b> — ${escapeHtml(it.author)}. ${escapeHtml(it.reason||'')}</span>
            <button data-i="${i}">+ Wishlist</button>
          </div>
        `).join('');
        resultsEl.querySelectorAll('button[data-i]').forEach(btn=>{
          btn.addEventListener('click', ()=>{
            const it = items[parseInt(btn.dataset.i,10)];
            wishlist.push({ id:'w'+Date.now()+Math.random().toString(36).slice(2,5), title:it.title, author:it.author, reason:it.reason||'', clusterHint: cluster });
            saveWishlist(); renderWishlist();
            btn.textContent = 'Added'; btn.disabled = true;
          });
        });
      };
      linkBtn.addEventListener('click', async ()=>{
        const isOpen = resultsEl.classList.contains('open');
        if(isOpen){ resultsEl.classList.remove('open'); return; }
        resultsEl.classList.add('open');
        if(bridgeCache[key]){ renderBridgeItems(bridgeCache[key]); return; }
        resultsEl.innerHTML = '<span class="loading" style="font-size:11.5px;color:var(--mid);font-style:italic;">Looking for a bridge…</span>';
        try{
          const items = await suggestBridge(cluster, nextCluster);
          bridgeCache[key] = items; saveBridges();
          renderBridgeItems(items);
        }catch(e){ resultsEl.innerHTML = '<span style="font-size:11.5px;color:var(--mid);">Could not find a bridge right now.</span>'; }
      });
      container.appendChild(bridge);
    }
  });
  filterBooks();
  renderWishlist();
  renderCase();
  renderEncyclopedia();
}

function filterBooks(){
  const q = document.getElementById('search').value.trim().toLowerCase();
  document.querySelectorAll('.cluster').forEach(sec=>{
    let any = false;
    sec.querySelectorAll('.book').forEach(row=>{
      const m = !q || row.dataset.search.includes(q);
      row.classList.toggle('hidden', !m);
      if(m) any = true;
    });
    sec.classList.toggle('hidden', !any);
  });
}
document.getElementById('search').addEventListener('input', filterBooks);

function renderWishlist(){
  const host = document.getElementById('wishlist-items');
  if(!wishlist.length){ host.innerHTML = '<div class="wish-empty">Nothing here yet — bridge suggestions you add will show up here.</div>'; return; }
  host.innerHTML = wishlist.map(w=>`
    <div class="wish-item" data-id="${w.id}">
      <span class="wi-text">${escapeHtml(w.title)} — ${escapeHtml(w.author)}<span class="wi-reason">${escapeHtml(w.reason||'')}</span></span>
      <button class="wi-add">Add to shelf</button>
      <button class="wi-remove">Remove</button>
    </div>
  `).join('');
  host.querySelectorAll('.wish-item').forEach(el=>{
    const id = el.dataset.id;
    const w = wishlist.find(x=>x.id===id);
    el.querySelector('.wi-add').addEventListener('click', ()=>{
      books.push({ id:'b'+Date.now(), title:w.title, author:w.author, year:null, cluster:w.clusterHint||'Unsorted', note:w.reason||'' });
      wishlist = wishlist.filter(x=>x.id!==id);
      save(); saveWishlist(); render();
    });
    el.querySelector('.wi-remove').addEventListener('click', ()=>{
      wishlist = wishlist.filter(x=>x.id!==id);
      saveWishlist(); renderWishlist();
    });
  });
}

// ---------- Shelf tab: bookcase, wraps into multiple shelf rows ----------
function renderCase(){
  const host = document.getElementById('case-rows');
  host.innerHTML = '';
  if(!books.length) return;

  const grouped = {};
  books.forEach(b=>{ (grouped[b.cluster]=grouped[b.cluster]||[]).push(b); });
  const ordered = orderedClusterNames(grouped);

  const items = [];
  ordered.forEach(cluster=>{
    const base = clusterColor(cluster);
    const list = grouped[cluster].slice().sort((a,b)=>(a.year??9999)-(b.year??9999));
    list.forEach((b,i)=>{
      const shadeAmt = list.length > 1 ? (i/(list.length-1)) * 26 - 13 : 0;
      const w = Math.round(12 + Math.min(26, Math.max(0, b.title.length - 6)) * 0.9);
      items.push({ book:b, color: shade(base, shadeAmt), width: w });
    });
  });

  const containerWidth = host.clientWidth || 600;
  const gap = 3;
  let rows = [[]];
  let rowW = 0;
  items.forEach(it=>{
    if(rowW + it.width + gap > containerWidth && rows[rows.length-1].length){
      rows.push([]);
      rowW = 0;
    }
    rows[rows.length-1].push(it);
    rowW += it.width + gap;
  });

  let tooltip = null;
  rows.forEach(rowItems=>{
    const rowEl = document.createElement('div');
    rowEl.className = 'case-row';
    const strip = document.createElement('div');
    strip.className = 'case-strip';
    rowItems.forEach(it=>{
      const el = document.createElement('div');
      el.className = 'viz-block';
      el.style.width = it.width + 'px';
      el.style.background = it.color;
      el.addEventListener('mouseenter', ()=>{
        tooltip = document.createElement('div');
        tooltip.className = 'viz-tooltip';
        tooltip.textContent = `${it.book.title} — ${it.book.author}${it.book.year?` (${it.book.year})`:''}`;
        document.body.appendChild(tooltip);
        const r = el.getBoundingClientRect();
        tooltip.style.left = Math.max(8, r.left - 60) + 'px';
        tooltip.style.top = (r.top - 34) + 'px';
      });
      el.addEventListener('mouseleave', ()=>{ if(tooltip){ tooltip.remove(); tooltip = null; } });
      strip.appendChild(el);
    });
    const ledge = document.createElement('div');
    ledge.className = 'case-ledge';
    rowEl.appendChild(strip);
    rowEl.appendChild(ledge);
    host.appendChild(rowEl);
  });
}
let caseResizeTimer = null;
window.addEventListener('resize', ()=>{
  clearTimeout(caseResizeTimer);
  caseResizeTimer = setTimeout(renderCase, 150);
});

// ---------- Encyclopedia tab ----------
function renderEncyclopedia(){
  const host = document.getElementById('enc-list');
  if(!books.length){ host.innerHTML = '<div class="empty">Nothing to look up yet.</div>'; return; }
  const q = (document.getElementById('enc-search').value || '').trim().toLowerCase();
  const sorted = books.slice().sort((a,b)=>a.title.localeCompare(b.title));
  host.innerHTML = sorted.map(b=>`
    <div class="enc-row" data-id="${b.id}" data-search="${escapeHtml(`${b.title} ${b.author} ${b.cluster}`.toLowerCase())}">
      <span>
        <span class="enc-title">${escapeHtml(b.title)}</span>
        <span class="enc-cluster">${escapeHtml(b.cluster)}</span>
      </span>
      <span class="enc-author">${escapeHtml(b.author)}</span>
      <span class="enc-year">${b.year ?? ''}</span>
    </div>
  `).join('');
  host.querySelectorAll('.enc-row').forEach(row=>{
    if(q && !row.dataset.search.includes(q)){ row.style.display = 'none'; } else { row.style.display = ''; }
    row.addEventListener('click', ()=>{
      const b = books.find(x=>x.id===row.dataset.id);
      if(b) openEdit(b, row);
    });
  });
}
document.getElementById('enc-search').addEventListener('input', renderEncyclopedia);

// ---------- tabs ----------
document.querySelectorAll('.tab-btn').forEach(btn=>{
  btn.addEventListener('click', ()=>{
    document.querySelectorAll('.tab-btn').forEach(b=>b.classList.remove('active'));
    document.querySelectorAll('.tab-panel').forEach(p=>p.classList.remove('active'));
    btn.classList.add('active');
    document.getElementById('tab-'+btn.dataset.tab).classList.add('active');
    if(btn.dataset.tab === 'shelf') renderCase();
  });
});

// ---------- export ----------
function download(filename, content, mime){
  const blob = new Blob([content], {type: mime});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href=url; a.download=filename;
  document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
}
document.getElementById('export-json').addEventListener('click', ()=>{
  download('shelf-atlas.json', JSON.stringify({ books, clusterOrder, wishlist }, null, 2), 'application/json');
});
document.getElementById('export-csv').addEventListener('click', ()=>{
  const header = ['Title','Author','Year','Cluster','Note'];
  const rows = books.map(b=>[b.title,b.author,b.year??'',b.cluster,b.note||''].map(v=>{
    const s = String(v).replace(/"/g,'""'); return /[",\n]/.test(s) ? `"${s}"` : s;
  }).join(','));
  download('shelf-atlas.csv', [header.join(','), ...rows].join('\n'), 'text/csv');
});

document.getElementById('add-btn').addEventListener('click', addBook);
document.querySelectorAll('.add input').forEach(i=>i.addEventListener('keydown', e=>{ if(e.key==='Enter') addBook(); }));

load();
</script>
</body>
</html>
