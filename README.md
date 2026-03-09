<div align="center">

# ⚡ HyperPath
### *Unified Intelligence Platform*

**Two Minds. One Platform.**

HyperPath reads your browser history, YouTube data, and digital footprint to build a hyper-personalised roadmap — powered by real data and live Claude AI analysis.

[![Live Demo](https://img.shields.io/badge/🚀_Live_Demo-hyper--oad.netlify.app-e85d0a?style=for-the-badge)](https://hyper-oad.netlify.app/)
[![HTML5](https://img.shields.io/badge/HTML5-Vanilla_JS-E34F26?style=for-the-badge&logo=html5&logoColor=white)](/)
[![Claude API](https://img.shields.io/badge/Claude_API-Anthropic-8b5cf6?style=for-the-badge)](https://anthropic.com)
[![Zero Dependencies](https://img.shields.io/badge/Dependencies-Zero-2dd982?style=for-the-badge)](/)
[![Netlify](https://img.shields.io/badge/Hosted_on-Netlify-00C7B7?style=for-the-badge&logo=netlify&logoColor=white)](https://netlify.com)

</div>

---

## 🧠 What is HyperPath?

HyperPath is a **browser-history intelligence engine** — a pure HTML5 + Vanilla JS single-page app that cross-references your real digital data (Brave/Chrome history, YouTube watch history, competition registrations, AI session logs) to generate a zero-redundancy, evidence-backed learning or career roadmap.

Unlike generic "What should I learn?" tools, HyperPath **knows what you've already done** and skips it. It surfaces hidden strengths, maps real gaps, and uses the **Anthropic Claude API** called directly from the browser to generate hyper-personalised AI analysis.

> *"The roadmap is built from what you've actually done — not what you think you've done."*

---

## 📁 File Structure

```
hyperpath/
│
├── index.html        # Landing page — dual profile selector
├── vaibhav.html      # Business Intelligence dashboard (Vaibhav B.)
├── gaurav.html       # Technical Learning dashboard (Gaurav Jain)
└── README.md
```

> **Zero setup.** No npm. No build step. No node_modules. Open `index.html` in any browser and it works instantly.

---

## 🗺️ User Journey — Screen Flow

Both `vaibhav.html` and `gaurav.html` are **multi-screen single-page apps** with no router — just CSS `display` toggling between screens.

```
Landing → Data Sources → Cognitive Calibration → Analyzing (animated logs)
       → DNA Profile ← AI fires here → Roadmap → Progress → Dashboard → Intel
```

Screen navigation is handled by a single `go()` / `goTo()` function. Here is the real code from each file:

```javascript
// ── vaibhav.html ──────────────────────────────────────────────────────
// Screen order is mapped to step-dot positions in the top nav
const SMAP = {
  's-land':      0,
  's-connect':   1,
  's-test':      2,
  's-analyze':   3,
  's-profile':   4,   // ← Claude API is triggered when this screen loads
  's-roadmap':   5,
  's-progress':  6,
  's-dashboard': 7
};

function go(id) {
  // Hide all screens
  document.querySelectorAll('.screen').forEach(s => {
    s.style.display = 'none';
    s.classList.remove('active');
  });

  // Show the target screen
  const el = document.getElementById(id);
  if (!el) return;
  el.style.display = 'flex';
  el.classList.add('active');
  window.scrollTo({ top: 0, behavior: 'smooth' });

  // Update top-nav step indicator dots
  const step = SMAP[id] || 0;
  document.getElementById('tnav').style.display = step > 0 ? 'flex' : 'none';
  document.querySelectorAll('.ns').forEach((n, i) => {
    n.className = 'ns';
    if (i < step - 1)    n.classList.add('done');
    else if (i === step - 1) n.classList.add('cur');
  });

  // Lazy-render screens only on first visit
  if (id === 's-roadmap')   renderRoadmap();
  if (id === 's-yt-intel')  renderYTIntel();
  if (id === 's-comps')     renderComps();
  if (id === 's-progress')  renderProgress();
  if (id === 's-dashboard') renderDash();
  if (id === 's-profile')   fetchAIProfile();  // ← fires the Claude API call
}
```

```javascript
// ── gaurav.html ───────────────────────────────────────────────────────
function goTo(id) {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  const target = document.getElementById(id);
  if (target) { target.classList.add('active'); window.scrollTo(0, 0); }

  const screenOrder = [
    's-goal', 's-connect', 's-test', 's-analyzing', 's-profile',
    's-roadmap', 's-progress', 's-dashboard', 's-brave-intel', 's-yt-intel'
  ];
  const nav = document.getElementById('topnav');
  if (id === 's-landing') { nav.style.display = 'none'; }
  else { nav.style.display = 'flex'; updateNavSteps(screenOrder.indexOf(id)); }

  if (id === 's-roadmap')     buildRoadmapUI();
  if (id === 's-brave-intel') buildBraveIntel();
  if (id === 's-dashboard')   loadNudge();
  // Note: gaurav.html AI is triggered on-demand via buttons, not on screen load
}
```

---

## 🤖 Claude API — Full Working Implementation

This is the engine of HyperPath. **Both HTML files call the Anthropic Claude API directly from the browser using the native `fetch()` API** — no backend, no server, no Node.js, no proxy. 100% Vanilla JS.

---

### `gaurav.html` — `callAI()` — Multi-purpose AI function

This single function powers all AI interactions across every screen in `gaurav.html`.

```javascript
async function callAI(prompt, targetEl, systemContext) {

  // 1. Find the DOM element where the AI response will be rendered
  const el = document.getElementById(targetEl);
  if (!el) return;

  // 2. Show a loading / thinking state immediately
  el.innerHTML = `
    <div class="ai-generating">
      <span>Analyzing your profile</span>
      <span class="ai-cursor"></span>
    </div>`;

  // 3. Build the system prompt — hardcoded with Gaurav's real browser data
  //    (systemContext param lets individual callers override this with custom context)
  const systemPrompt = systemContext || `You are HyperPath's AI learning advisor.
You have access to Gaurav Jain's complete digital learning profile:

BRAVE BROWSER DATA (6,120 URLs · 15,321 visits · 959 searches):
- Top site: Anime_One web app (1538 visits on localhost:3000)
- Kaggle: 203 visits (7th most visited site overall)
- YouTube: 154 visits
- Flask debugger localhost:5678: 27 visits (actively building Flask apps)
- Jupyter Notebook localhost:8888: 39 visits (running ML code)
- LeetCode: 47 visits (Missing Number problem)
- Claude.ai: 28 visits
- Search categories: DSA 82 queries · Tools/Setup 72 · Web Dev 69 · Python 55 · ML 40
- Specific searches: 'binary search', 'striver dsa sheet', 'pandas encoding error',
  'tensors in python', 'deploy python netlify', 'flask vs django', 'anime api', 'jikan api'
- Downloads: 145 files (12 PDFs = DSA books · CampusX Day 34 CSV files)

YOUTUBE DATA (361 videos):
- DSA/Algorithms: 29% — 106 videos
- Machine Learning: 23% — 83 videos
- CampusX: 85 videos (Hindi ML) — Day 34 just completed
- take U forward: 55 videos (Striver A2Z DSA course)
- CodeHelp by Babbar: 20 videos (Lecture 13 watched March 7)
- Stanford CS229 Lecture 1 watched March 6

KEY CROSS-SOURCE INSIGHTS:
- He BUILDS real projects — Anime_One app on localhost has 1538 visits = months of work
- He runs Jupyter AND uses Kaggle = hands-on ML, not just theory consumption
- He has an active Flask bug to fix (UnboundLocalError: firstName scope issue)
- He searches the same things twice = concepts not sticking (needs different approach)
- He uses Claude extensively for project building (28 visits, 3 chat files downloaded)

Respond in 2–4 concise paragraphs. Be specific — reference actual visit counts,
search terms, or video titles. Do NOT be generic. Do NOT give generic career advice.`;

  // 4. Make the API call
  try {
    const response = await fetch("https://api.anthropic.com/v1/messages", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
        // Add API key here — see Setup section below
      },
      body: JSON.stringify({
        model: "claude-sonnet-4-20250514",
        max_tokens: 1000,
        system: systemPrompt,
        messages: [{ role: "user", content: prompt }]
      })
    });

    // 5. Parse the response JSON
    const data = await response.json();

    // content is an array of blocks — map over all blocks and join text
    const text = data.content?.map(b => b.text || '').join('')
               || 'Unable to generate response.';

    // 6. Render into the DOM
    //    - Convert double newlines into HTML paragraphs
    //    - Convert **bold markdown** into <strong> HTML tags
    el.innerHTML = text
      .replace(/\n\n/g, '</p><p style="margin-top:10px;">')
      .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');

  } catch (err) {
    el.textContent = 'AI analysis unavailable. Please check your connection.';
    console.error('AI error:', err);
  }
}
```

**Four wrapper functions call `callAI()` from different places in the UI:**

```javascript
// Called from DNA Profile screen question buttons
function askAI(question) {
  callAI(question, 'ai-response');
}

// Called from Roadmap screen — injects current goal into context
function askRoadmapAI(question) {
  const context = `Current roadmap: ${_selectedGoal}. ${question}`;
  callAI(context, 'ai-roadmap-response');
}

// Called from phase modal — asks about a specific roadmap step
function askPhaseAI(phaseTitle, evidence) {
  const q = `Tell me specifically about the phase "${phaseTitle}" for Gaurav.
Evidence: ${evidence}.
Why is this the right next step? What should he do in the first 30 minutes?`;
  callAI(q, 'ai-roadmap-response');
}

// Called from bottom-sheet modals — inline AI inside a resource card
function askModalAI(question) {
  callAI(question, 'modal-ai-text');
}
```

---

### `vaibhav.html` — `fetchAIProfile()` — Auto-firing profile synthesis

Unlike `gaurav.html`, Vaibhav's AI fires **automatically the moment the DNA Profile screen is navigated to** — no button press needed.

```javascript
// Triggered by go('s-profile') in the navigation function
async function fetchAIProfile() {

  const box = document.getElementById('ai-profile-text');

  // Guard: don't fire twice if user navigates back to this screen
  if (!box || box.dataset.loaded) return;
  box.dataset.loaded = 'true';

  // 1. Build the prompt — injects real data + live calibration quiz answers (ANS object)
  const prompt = `You are the AI core of HyperPath, a business learning intelligence engine.
Generate a deep, hyperpersonalised profile synthesis for this user.

USER DATA:
- 7,497 YouTube videos parsed · 366 business/strategy videos identified
- caseinterview.com binge: 5 consecutive videos in one sitting
- Gemini AI projects: Cipla Health (Nicotex STP strategy) · MedCare (vertical moat +
  closed-loop revenue analysis) · PrepBaba (90-day 10K-user plan with CAC metrics) ·
  VAANIK Startup · Seven Seers consulting project
- Unstop: 403 visits · 30+ competitions registered — Case in Point'26 · ThinCovation MDI ·
  StrategiQ · Strategy Storm · ValQuest IIM Lucknow · Consultathon IIT BHU · CaseCrux IIT ·
  Pitchers IIM Rohtak · and 20+ more
- Genspark AI: 526 sessions (more than ChatGPT at 178 sessions)
- Figma: 158 visits · Full MedCare product prototype designed and built
- Chrome searches: "Bain BrAINWARS 2026" · "McKinsey case studies PDF" ·
  "180 degrees consulting SRCC case comp" · "mock AI interview for consulting" ·
  "vaibhav bansal linkedin iim nagpur finalist"
- 180 Degrees Consulting SRCC: 12+ visits to case library and competition guides
- Claude.ai: 98 visits

Calibration answers (from quiz the user just completed):
  Goal     = ${ANS.goal}
  Timeline = ${ANS.tl}
  Gap      = ${ANS.gap}
  Bain     = ${ANS.bain}
  IIM Nagpur = ${ANS.iim}

Write a 3–4 sentence strategic diagnosis that is:
1. HYPER-SPECIFIC — reference actual data points, actual project names, actual searches
2. INSIGHTFUL — tell them something they haven't consciously articulated themselves
3. ACTIONABLE — end with exactly one razor-sharp next move

Do NOT use bullet points. Write in flowing prose. Bold 2–3 key phrases using **text**.`;

  try {
    // 2. Direct browser fetch to Anthropic API
    const resp = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
        // Add API key here — see Setup section below
      },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 1000,
        messages: [{ role: 'user', content: prompt }]
      })
    });

    // 3. Parse the response
    const data = await resp.json();
    let text = data.content?.[0]?.text || '';

    // 4. Convert **markdown bold** → <strong>HTML bold</strong>
    text = text.replace(/\*\*([^*]+)\*\*/g, '<strong>$1</strong>');
    box.innerHTML = text;

  } catch (e) {
    // 5. Hardcoded fallback insight if API fails or key is missing
    box.innerHTML = `<strong>Your data tells a clear story:</strong> You've assembled
the theory (9 framework binge), the AI workflow (Genspark-first, 526 sessions), the
domain depth (Cipla STP + MedCare vertical moat in Figma), and the competition hunger
(30+ Unstop registrations including Bain BrAINWARS 2026). The gap isn't knowledge —
it's <strong>converting registered competitions into actual wins.</strong> Your razor-sharp
next move: pick the single upcoming competition in your Unstop history closest to deadline,
apply the Analyst Academy slide format to your Cipla Gemini work, and submit it as your
first competition deck.`;
  }
}
```

---

## 📦 API Response — What Claude Returns

The Anthropic API returns this JSON structure. Here's exactly how both files handle it:

```json
{
  "id": "msg_01XFDUDYJgAACzvnptvVoYEL",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "Your AI-generated analysis appears here..."
    }
  ],
  "model": "claude-sonnet-4-20250514",
  "stop_reason": "end_turn",
  "usage": {
    "input_tokens": 850,
    "output_tokens": 320
  }
}
```

```javascript
// gaurav.html — maps over ALL content blocks (safe for multi-block responses)
const text = data.content?.map(b => b.text || '').join('') || 'Unable to generate response.';

// vaibhav.html — reads the first content block directly
let text = data.content?.[0]?.text || '';
```

---

## 🎛️ State Management

Zero framework. All state is plain JavaScript variables in memory.

```javascript
// ── gaurav.html ──
let _selectedGoal  = "Machine Learning";  // which roadmap is active
let _currentScreen = "s-landing";         // current visible screen

// ── vaibhav.html ──
// Stores the user's live calibration quiz answers
// These are injected directly into the Claude API prompt at call time
const ANS = {
  goal: 'mbb',       // user's primary target
  tl:   '3m',        // timeline pressure
  gap:  'delivery',  // self-identified weakest link
  bain: 'applying',  // Bain BrAINWARS status
  iim:  'yes',       // IIM Nagpur finalist confirmation
  next: 'case'       // next competition priority
};

// ANS is updated live as the user clicks options in the calibration quiz:
function pick(el, q) {
  el.closest('.q-blk').querySelectorAll('.opt').forEach(o => o.classList.remove('sel'));
  el.classList.add('sel');
  if (el.dataset.k) ANS[el.dataset.k] = el.dataset.v;  // ← live state update
  document.getElementById('pf').style.width = Math.min(100, (q / 6 * 100)) + '%';
}
```

---

## 🎨 UI Rendering — Pure DOM, No Framework

```javascript
// Loading state while Claude is thinking (gaurav.html)
el.innerHTML = `<div class="ai-generating">
  <span>Analyzing your profile</span>
  <span class="ai-cursor"></span>
</div>`;

// Render Claude's response — markdown to HTML conversion
el.innerHTML = text
  .replace(/\n\n/g, '</p><p style="margin-top:10px;">')  // line breaks → paragraphs
  .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');      // **bold** → <strong>

// Rotating daily nudge (gaurav.html dashboard)
function loadNudge() {
  const nudges = [
    "🦁 Brave data: Kaggle 203 visits but no notebook submitted. Write your first cell today.",
    "⚡ You watched Babbar's Binary Search Lecture 13 on Mar 7. Problem set is next.",
    "🤖 CampusX Day 35 (Outlier Detection) is literally your next video. Jupyter at localhost:8888.",
    "🏗️ Anime_One has 1538 visits but is still local. You searched 'deploy python netlify' twice."
  ];
  const el = document.getElementById('nudge-text');
  if (el) el.textContent = nudges[Math.floor(Math.random() * nudges.length)];
}
```

---

## 🎨 Design System

```css
:root {
  --bg:     #080706;                /* near-black main background */
  --bg2:    #0e0d0b;                /* slightly lighter surface */
  --card:   #141310;                /* card / panel background */
  --card2:  #1a1916;                /* card hover / active state */
  --border: rgba(255,248,235,.07);  /* very subtle border */
  --cream:  #fff8eb;                /* primary text colour */
  --muted:  rgba(255,248,235,.4);   /* secondary / dimmed text */
  --muted2: rgba(255,248,235,.65);  /* mid-weight text */
  --fire:   #e85d0a;                /* primary accent — orange-red */
  --fire2:  #ff7a33;                /* accent hover state */
  --fire3:  rgba(232,93,10,.12);    /* accent background tint */
  --green:  #2dd982;                /* success / completed states */
  --blue:   #5badff;                /* secondary accent */
  --gold:   #f5c842;                /* highlights / scores */
  --purple: #b07ef8;                /* AI-related elements */
  --red:    #ff4f6b;                /* warnings / errors */
  --r:      10px;                   /* standard border radius */
  --r2:     16px;                   /* large border radius */
}
```

**Google Fonts loaded:**
```html
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;500;600;700;800
  &family=Bricolage+Grotesque:wght@300;400;500;600;700
  &family=DM+Mono:ital,wght@0,300;0,400;1,300&display=swap"
  rel="stylesheet">
```

| Font | Role |
|------|------|
| `Syne 800` | All headings, stat numbers, nav labels, buttons |
| `Bricolage Grotesque` | All body copy and descriptions |
| `DM Mono` | Eyebrow labels, data tags, monospace elements |

---

## 🚀 Running Locally

```bash
# Clone the repo
git clone https://github.com/yourusername/hyperpath.git
cd hyperpath

# Open directly (no server needed for non-AI features)
open index.html           # macOS
start index.html          # Windows

# Or use any static file server
python -m http.server 8000
npx serve .
```

### Enabling the Claude AI Features

The AI calls go directly from the browser to `https://api.anthropic.com/v1/messages`. To make them work locally, add your Anthropic API key to the request headers in both `callAI()` (gaurav.html) and `fetchAIProfile()` (vaibhav.html):

```javascript
// In callAI() — gaurav.html, line ~2090
// In fetchAIProfile() — vaibhav.html, line ~991

headers: {
  "Content-Type": "application/json",
  "x-api-key": "sk-ant-api03-YOUR_KEY_HERE",
  "anthropic-version": "2023-06-01",
  "anthropic-dangerous-direct-browser-access": "true"
}
```

Get your key at [console.anthropic.com](https://console.anthropic.com/).

> ⚠️ **Security note:** Never commit your API key to Git. For a shared/production deployment, proxy the API call through a Netlify Function, Vercel Edge Function, or Cloudflare Worker so the key stays server-side.

---

## 🧩 How to Add Your Own Profile

**Step 1 — Copy a base file**
```bash
cp gaurav.html yourname.html    # tech / learning profile
cp vaibhav.html yourname.html   # business / consulting profile
```

**Step 2 — Replace the data objects at the top of the script block**
```javascript
const BRAVE_DATA = {
  meta: {
    totalUniqueURLs: 5000,
    totalVisitEvents: 12000,
    totalSearchTerms: 800,
    totalDownloads: 100,
  },
  searchCategories: {
    "Your Topic A": { count: 70, pct: 25 },
    "Your Topic B": { count: 50, pct: 18 },
  },
  top20Sites: [
    { url: "yourdomain.com", title: "Your Project", visits: 500, category: "Project" },
  ],
};
```

**Step 3 — Rewrite the AI system prompt with your data**
```javascript
const systemPrompt = `You are HyperPath's AI advisor for [YOUR NAME].

BROWSER DATA:
- [Your top visited sites and visit counts]
- [Your search categories and query counts]
- [Your notable downloads and projects]

YOUTUBE DATA:
- [Your channel breakdown and video counts]

KEY INSIGHTS:
- [3–5 things your data reveals about you specifically]

Be specific, reference real numbers. Never be generic.`;
```

**Step 4 — Add your card to index.html**
```html
<div class="profile-card" onclick="window.location.href='yourname.html'">
  <div class="card-header">
    <div class="card-avatar">YN</div>
    <div class="card-name">Your Name</div>
  </div>
  <button class="card-cta">Explore Your Path →</button>
</div>
```

**Exporting your data:**
- **Chrome / Brave** → Install the [History Export](https://chromewebstore.google.com/detail/history-export/lpmoaclacdaofhlijklgcidenicelmke) extension → Export JSON
- **YouTube** → [Google Takeout](https://takeout.google.com/) → YouTube only → `watch-history.json`

---

## 👤 Current Profiles

| | **Vaibhav Bansal** | **Gaurav Jain** |
|---|---|---|
| **Path** | 🔥 Business & Consulting | 💻 Technical & Coding |
| **Archetype** | Competition-First Strategy Builder | Full-Stack ML/DSA Hybrid Builder |
| **Browser** | Chrome | Brave |
| **URLs** | 7,905 Chrome entries | 6,120 Brave URLs · 15,321 visits |
| **YouTube** | 7,497 videos · 366 business | 361 videos · 8 channels tracked |
| **Standout Data** | 30+ Unstop comps · 526 Genspark sessions | 1,538 Anime_One localhost visits |
| **AI Trigger** | Auto on DNA Profile screen load | On-demand via question buttons |
| **AI Call Location** | `fetchAIProfile()` in vaibhav.html | `callAI()` in gaurav.html |
| **Target** | MBB — McKinsey / Bain / BCG | ML Engineering + Full-Stack Dev |

---

## 📊 Platform Stats

| Metric | Value |
|--------|-------|
| Total URLs Analysed | 14,025 |
| Total Videos Tracked | 7,858 |
| User Profiles | 2 |
| Claude API Model | `claude-sonnet-4-20250514` |
| Max Tokens per API Call | 1,000 |
| External JS Libraries | 0 |
| Build Step Required | None |
| Total File Size | ~260 KB |

---

## 🛠️ Full Tech Stack

| Layer | What's Used |
|-------|------------|
| Markup | HTML5 — one self-contained file per profile |
| Styling | CSS3 — custom properties, grid, flexbox, `@keyframes` animations |
| Logic | Vanilla JavaScript — `async/await`, `fetch()`, direct DOM manipulation |
| AI | Anthropic Claude API (`POST /v1/messages`) — called directly from browser |
| Model | `claude-sonnet-4-20250514` · `max_tokens: 1000` |
| Fonts | Google Fonts (Syne · Bricolage Grotesque · DM Mono) |
| Hosting | Netlify — static deploy, no server |
| State | In-memory JS variables — no localStorage, no cookies, no DB |
| Routing | CSS `display` toggle — no SPA framework, no History API |

---

## 🤝 Built By

| Person | Contribution |
|--------|-------------|
| **Gaurav Jain** | Technical profile · Brave Browser data · DSA/ML roadmap |
| **Vaibhav Bansal** | Business profile · Chrome data · MBB consulting roadmap |

---

## 📄 License

MIT — use freely. If you build your own profile on top of this engine, tag us.

---

<div align="center">

**HyperPath** — *Your Data. Your Edge.*

[🚀 Live Demo](https://hyper-oad.netlify.app/) &nbsp;·&nbsp; [💻 Gaurav's Path](https://hyper-oad.netlify.app/gaurav.html) &nbsp;·&nbsp; [🔥 Vaibhav's Path](https://hyper-oad.netlify.app/vaibhav.html)

</div>
