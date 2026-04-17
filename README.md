<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Training Needs — Teacher Dashboard</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif;background:#0f0f1c;color:#e2e0f0;min-height:100vh}

/* ── TOP BAR ── */
.top-bar{background:#110808;border-bottom:1px solid rgba(200,30,30,0.35);padding:14px 28px;display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:10px}
.top-bar-left h1{font-size:16px;font-weight:700;color:#fff}
.top-bar-left h1 span{color:#e03030}
.top-bar-left p{font-size:11px;color:#666;margin-top:3px}
.pill{background:rgba(220,40,40,0.15);border:1px solid rgba(220,40,40,0.4);color:#ff7070;border-radius:20px;padding:4px 14px;font-size:13px;font-weight:600}

/* ── TAB NAV ── */
.tab-nav{display:flex;background:#0d0d18;border-bottom:1px solid rgba(255,255,255,0.08);padding:0 28px}
.tab-btn{padding:13px 22px;font-size:13px;font-weight:600;color:#666;cursor:pointer;border:none;background:none;border-bottom:2px solid transparent;transition:all 0.2s;letter-spacing:0.3px}
.tab-btn:hover{color:#aaa}
.tab-btn.active{color:#ff6060;border-bottom-color:#e03030}
.tab-indicator{font-size:10px;background:rgba(220,40,40,0.2);color:#ff7070;border-radius:10px;padding:1px 7px;margin-left:6px;font-weight:700}

/* ── SHARED FILTERS BAR ── */
.filters-bar{background:#131320;border-bottom:1px solid rgba(255,255,255,0.07);padding:12px 28px;display:flex;flex-wrap:wrap;gap:10px;align-items:flex-end}
.fg{display:flex;flex-direction:column;gap:4px}
.fg label{font-size:10px;color:#555;text-transform:uppercase;letter-spacing:0.8px;font-weight:600}
.fg select,.fg input{background:#1c1c2e;border:1px solid rgba(255,255,255,0.1);color:#ccc;border-radius:7px;padding:6px 11px;font-size:12px;outline:none;min-width:140px;cursor:pointer;transition:border 0.2s}
.fg select:focus,.fg input:focus{border-color:rgba(220,40,40,0.55)}
.fg select:hover{border-color:rgba(255,255,255,0.2)}
.search-wrap{flex:1;min-width:180px}
.search-wrap input{width:100%}
.search-wrap input::placeholder{color:#444}
.reset-btn{background:rgba(220,40,40,0.12);border:1px solid rgba(220,40,40,0.35);color:#ff7070;border-radius:7px;padding:6px 15px;font-size:12px;font-weight:600;cursor:pointer;white-space:nowrap;transition:background 0.2s}
.reset-btn:hover{background:rgba(220,40,40,0.22)}

/* ── PAGE WRAPPERS ── */
.page{display:none}
.page.active{display:block}

/* ════════════════════════
   PAGE 1 — ANALYTICS
════════════════════════ */
.analytics-layout{padding:22px 28px;display:flex;flex-direction:column;gap:20px}

/* stat row */
.stat-row{display:grid;grid-template-columns:repeat(5,1fr);gap:12px}
.stat-card{background:rgba(255,255,255,0.04);border:1px solid rgba(255,255,255,0.08);border-radius:10px;padding:16px;text-align:center}
.stat-card .sn{font-size:28px;font-weight:700;color:#fff;line-height:1}
.stat-card .sn small{font-size:15px;color:#e03030}
.stat-card .sl{font-size:10px;color:#666;margin-top:5px;text-transform:uppercase;letter-spacing:0.5px}

/* chart grid */
.chart-grid-top{display:grid;grid-template-columns:repeat(4,1fr);gap:14px}
.chart-grid-bottom{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.chart-box{background:rgba(255,255,255,0.03);border:1px solid rgba(255,255,255,0.07);border-radius:11px;padding:18px}
.chart-box h4{font-size:10px;color:#666;text-transform:uppercase;letter-spacing:0.8px;margin-bottom:12px;font-weight:600}
.chart-wrap{position:relative}
.legend-list{margin-top:10px;display:flex;flex-direction:column;gap:5px;font-size:11px;color:#888}
.legend-list.row{flex-direction:row;flex-wrap:wrap;gap:8px}
.li{display:flex;align-items:center;gap:6px}
.ld{width:8px;height:8px;border-radius:2px;flex-shrink:0}

/* motivation & problem bars */
.bar-list{display:flex;flex-direction:column;gap:10px;margin-top:4px}
.bar-item label{font-size:12px;color:#bbb;display:flex;justify-content:space-between;margin-bottom:4px}
.bar-item label span{color:#777;font-size:11px}
.bar-bg{background:rgba(255,255,255,0.07);border-radius:4px;height:8px;overflow:hidden}
.bar-fill{height:100%;border-radius:4px;transition:width 0.6s ease}

/* ════════════════════════
   PAGE 2 — DATA TABLE
════════════════════════ */
.table-layout{padding:18px 28px}
.table-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px}
.table-header h3{font-size:13px;font-weight:600;color:#aaa}
.export-btn{background:rgba(255,255,255,0.05);border:1px solid rgba(255,255,255,0.1);color:#999;border-radius:6px;padding:6px 14px;font-size:12px;cursor:pointer}
.export-btn:hover{background:rgba(255,255,255,0.09)}

.data-table{width:100%;border-collapse:collapse;font-size:12px}
.data-table th{background:#111120;color:#777;font-weight:600;text-transform:uppercase;letter-spacing:0.5px;font-size:10px;padding:10px 12px;text-align:left;position:sticky;top:0;z-index:2;border-bottom:1px solid rgba(255,255,255,0.09);cursor:pointer;white-space:nowrap}
.data-table th:hover{color:#ccc}
.data-table th.sort-asc::after{content:' ↑'}
.data-table th.sort-desc::after{content:' ↓'}
.data-table td{padding:10px 12px;border-bottom:1px solid rgba(255,255,255,0.05);color:#ccc;vertical-align:top;line-height:1.5;max-width:220px}
.data-table tr:hover td{background:rgba(255,255,255,0.025)}

.fam-tag{display:inline-block;border-radius:4px;padding:2px 8px;font-size:10px;font-weight:700}
.fam-1{background:rgba(224,48,48,0.2);color:#ff7070;border:1px solid rgba(224,48,48,0.3)}
.fam-2{background:rgba(240,128,42,0.2);color:#ffaa55;border:1px solid rgba(240,128,42,0.3)}
.fam-3{background:rgba(232,192,48,0.2);color:#f5d060;border:1px solid rgba(232,192,48,0.3)}
.fam-4{background:rgba(91,199,122,0.2);color:#6ed48a;border:1px solid rgba(91,199,122,0.3)}
.fam-5{background:rgba(74,144,217,0.2);color:#70aeff;border:1px solid rgba(74,144,217,0.3)}

.grade-tag{background:rgba(130,80,200,0.18);color:#c090f0;border:1px solid rgba(130,80,200,0.28);display:inline-block;border-radius:4px;padding:2px 7px;font-size:10px;font-weight:600;white-space:nowrap}

.usage-r{color:#6ed48a;font-weight:600}
.usage-f{color:#70aeff}
.usage-h{color:#ffaa55}
.usage-n{color:#ff7070}

.prob-tag{display:inline-block;background:rgba(255,255,255,0.06);border:1px solid rgba(255,255,255,0.1);border-radius:4px;padding:1px 6px;font-size:10px;color:#aaa;margin:1px}

.comfort-bar-wrap{display:flex;align-items:center;gap:5px}
.comfort-bar-bg{background:rgba(255,255,255,0.08);border-radius:3px;height:5px;width:44px;overflow:hidden}
.comfort-bar-fill{height:100%;border-radius:3px}

.cell-text{overflow:hidden;text-overflow:ellipsis;display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;line-height:1.5;font-size:11px;color:#999}
.no-data{text-align:center;color:#555;padding:60px;font-size:14px}

/* profile card for hover expand — keep static */
.row-expand{display:none}
</style>
</head>
<body>

<!-- TOP BAR -->
<div class="top-bar">
  <div class="top-bar-left">
    <h1>Careers Adda &nbsp;·&nbsp; <span>AI Training Needs Assessment</span></h1>
    <p>88 Teacher Responses &nbsp;·&nbsp; April 17–18, 2026 &nbsp;·&nbsp; School AI Workshop</p>
  </div>
  <div class="pill" id="counterPill">88 of 88 teachers</div>
</div>

<!-- TAB NAV -->
<div class="tab-nav">
  <button class="tab-btn active" onclick="switchTab('analytics',this)">
    Analytics Dashboard <span class="tab-indicator" id="tab1count">88</span>
  </button>
  <button class="tab-btn" onclick="switchTab('table',this)">
    Teacher Data Table <span class="tab-indicator" id="tab2count">88</span>
  </button>
</div>

<!-- FILTERS BAR (shared) -->
<div class="filters-bar">
  <div class="fg search-wrap">
    <label>Search Name / Keyword</label>
    <input type="text" id="searchInput" placeholder="Search name, problem, reason…">
  </div>
  <div class="fg">
    <label>AI Familiarity</label>
    <select id="famFilter">
      <option value="">All Levels</option>
      <option value="1">Level 1 — None</option>
      <option value="2">Level 2 — Beginner</option>
      <option value="3">Level 3 — Some</option>
      <option value="4">Level 4 — Good</option>
      <option value="5">Level 5 — Expert</option>
    </select>
  </div>
  <div class="fg">
    <label>Tool Usage</label>
    <select id="usageFilter">
      <option value="">All</option>
      <option value="regularly">Uses Regularly</option>
      <option value="few">Used Few Times</option>
      <option value="heard">Heard, Never Used</option>
      <option value="never">Not At All</option>
    </select>
  </div>
  <div class="fg">
    <label>Tech Comfort</label>
    <select id="comfortFilter">
      <option value="">All</option>
      <option value="1">1 — Very Low</option>
      <option value="2">2 — Low</option>
      <option value="3">3 — Medium</option>
      <option value="4">4 — High</option>
      <option value="5">5 — Very High</option>
    </select>
  </div>
  <div class="fg">
    <label>Grade Level</label>
    <select id="gradeFilter">
      <option value="">All Grades</option>
      <option value="preprimary">Pre-Primary / KG</option>
      <option value="primary">Primary (1–5)</option>
      <option value="middle">Middle (6–8)</option>
      <option value="senior">Senior (9–12)</option>
    </select>
  </div>
  <div class="fg">
    <label>Problem Area</label>
    <select id="problemFilter">
      <option value="">All Problems</option>
      <option value="lesson">Lesson Planning</option>
      <option value="admin">Admin / Paperwork</option>
      <option value="engagement">Student Engagement</option>
      <option value="time">Time Management</option>
      <option value="personal">Personalized Learning</option>
    </select>
  </div>
  <button class="reset-btn" onclick="resetFilters()">↺ Reset</button>
</div>

<!-- ══════════════════════════════
     PAGE 1: ANALYTICS
══════════════════════════════ -->
<div class="page active" id="page-analytics">
  <div class="analytics-layout">

    <!-- STAT CARDS -->
    <div class="stat-row">
      <div class="stat-card"><div class="sn" id="s-total">88</div><div class="sl">Total Teachers</div></div>
      <div class="stat-card"><div class="sn" id="s-beginners">41<small>%</small></div><div class="sl">Beginners (L1–L2)</div></div>
      <div class="stat-card"><div class="sn" id="s-used">81<small>%</small></div><div class="sl">Used AI Tools</div></div>
      <div class="stat-card"><div class="sn" id="s-avg">2.8</div><div class="sl">Avg Familiarity</div></div>
      <div class="stat-card"><div class="sn" id="s-comfort">3.1</div><div class="sl">Avg Tech Comfort</div></div>
    </div>

    <!-- TOP CHART ROW: 4 donut/bar charts -->
    <div class="chart-grid-top">

      <div class="chart-box">
        <h4>AI Familiarity (1–5)</h4>
        <div class="chart-wrap" style="height:160px"><canvas id="famChart" role="img" aria-label="AI familiarity bar chart">Familiarity L1:16 L2:20 L3:33 L4:17 L5:2</canvas></div>
        <div class="legend-list row" style="margin-top:8px">
          <div class="li"><div class="ld" style="background:#e03030"></div><span id="leg-f1">L1: 16</span></div>
          <div class="li"><div class="ld" style="background:#f0802a"></div><span id="leg-f2">L2: 20</span></div>
          <div class="li"><div class="ld" style="background:#e8c030"></div><span id="leg-f3">L3: 33</span></div>
          <div class="li"><div class="ld" style="background:#5bc77a"></div><span id="leg-f4">L4: 17</span></div>
          <div class="li"><div class="ld" style="background:#4a90d9"></div><span id="leg-f5">L5: 2</span></div>
        </div>
      </div>

      <div class="chart-box">
        <h4>Tool Usage Status</h4>
        <div class="chart-wrap" style="height:160px"><canvas id="usageChart" role="img" aria-label="Tool usage doughnut chart">Usage breakdown</canvas></div>
        <div class="legend-list" id="usageLeg" style="margin-top:8px"></div>
      </div>

      <div class="chart-box">
        <h4>Tech Comfort Level</h4>
        <div class="chart-wrap" style="height:160px"><canvas id="comfortChart" role="img" aria-label="Tech comfort bar chart">Comfort levels</canvas></div>
        <div class="legend-list row" style="margin-top:8px">
          <div class="li"><div class="ld" style="background:#e03030"></div><span id="leg-c1">L1: 6</span></div>
          <div class="li"><div class="ld" style="background:#f0802a"></div><span id="leg-c2">L2: 18</span></div>
          <div class="li"><div class="ld" style="background:#e8c030"></div><span id="leg-c3">L3: 33</span></div>
          <div class="li"><div class="ld" style="background:#5bc77a"></div><span id="leg-c4">L4: 15</span></div>
          <div class="li"><div class="ld" style="background:#4a90d9"></div><span id="leg-c5">L5: 16</span></div>
        </div>
      </div>

      <div class="chart-box">
        <h4>Grade Level Split</h4>
        <div class="chart-wrap" style="height:160px"><canvas id="gradeChart" role="img" aria-label="Grade level doughnut chart">Grade levels</canvas></div>
        <div class="legend-list" id="gradeLeg" style="margin-top:8px"></div>
      </div>

    </div>

    <!-- BOTTOM CHART ROW: Motivations + Problems -->
    <div class="chart-grid-bottom">

      <div class="chart-box">
        <h4>Why Teachers Want to Learn AI</h4>
        <div class="bar-list" id="motivBars"></div>
      </div>

      <div class="chart-box">
        <h4>Problems They Want AI to Solve</h4>
        <div class="bar-list" id="problemBars"></div>
      </div>

    </div>

  </div>
</div>

<!-- ══════════════════════════════
     PAGE 2: DATA TABLE
══════════════════════════════ -->
<div class="page" id="page-table">
  <div class="table-layout">
    <div class="table-header">
      <h3 id="tableTitle">All 88 Teachers</h3>
      <button class="export-btn" onclick="exportCSV()">Export CSV ↓</button>
    </div>
    <div style="overflow-x:auto">
      <table class="data-table" id="mainTable">
        <thead>
          <tr>
            <th style="width:30px">#</th>
            <th onclick="sortTable('name')" id="th-name">Name</th>
            <th onclick="sortTable('grade')" id="th-grade">Grade</th>
            <th onclick="sortTable('fam')" id="th-fam">Familiarity</th>
            <th onclick="sortTable('usage')" id="th-usage">Tool Usage</th>
            <th onclick="sortTable('comfort')" id="th-comfort">Comfort</th>
            <th style="width:200px">Why Learn AI</th>
            <th style="width:200px">Problem to Solve</th>
            <th style="width:180px">Expectation</th>
          </tr>
        </thead>
        <tbody id="tableBody"></tbody>
      </table>
    </div>
  </div>
</div>

<script>
/* ─────────────────────────────────────────
   DATA
───────────────────────────────────────── */
const RAW=[
  {name:"Roshan C",grade:"7,8,9",fam:4,usage:"regularly",comfort:5,why:"To make tasks simpler / Save time",problem:"",expect:"Learn AI better"},
  {name:"Anjali Tanwar",grade:"Class 6–7",fam:1,usage:"few",comfort:2,why:"Yes",problem:"Activity different from NCERT, Realistic examples",expect:"Concept clearance about AI"},
  {name:"Ritu Sikka",grade:"KG",fam:4,usage:"few",comfort:5,why:"To organise and maintain results",problem:"Maintaining Excel sheet",expect:"Hands on experience"},
  {name:"Tejpreet Kaur",grade:"Classes 5,6,7",fam:3,usage:"few",comfort:5,why:"To keep up with the times",problem:"Children copy answers from ChatGPT. How to identify original work?",expect:"Usage of AI to stay ahead"},
  {name:"Geeta Pandit",grade:"KG",fam:1,usage:"heard",comfort:3,why:"Make teaching effective and engaging",problem:"Generate ideas, worksheets, teaching materials efficiently",expect:"Learn practical ways to use AI daily"},
  {name:"Tuhina Malhotra",grade:"6–8",fam:5,usage:"regularly",comfort:4,why:"For convenience",problem:"Creating worksheets and extra questions",expect:"Enhance my knowledge"},
  {name:"Divya Shekhar",grade:"10,11,12",fam:2,usage:"few",comfort:5,why:"Important skill to survive in today's world",problem:"Creating maps, mind maps, comprehensive notes",expect:"Better and effective use of AI"},
  {name:"Anuradha Bhatnagar",grade:"Senior",fam:1,usage:"few",comfort:1,why:"Very useful these days",problem:"Not really any problem",expect:"To find my error"},
  {name:"Vandana Awasthi",grade:"8,9,10",fam:3,usage:"few",comfort:3,why:"Yes",problem:"No idea yet",expect:"How to use AI in maths"},
  {name:"Meenakshi",grade:"IX and X",fam:3,usage:"few",comfort:5,why:"It is need of the time",problem:"Nothing specific",expect:"Good content"},
  {name:"Nidhi Tokas",grade:"11 and 12",fam:3,usage:"few",comfort:3,why:"Classroom management, attendance, lesson planning, marksheets",problem:"Lesson planning and preparing interactive PPTs",expect:"Knowledge about various AI platforms and free apps"},
  {name:"Abhay Kumar",grade:"11, 12",fam:2,usage:"few",comfort:1,why:"Need of time",problem:"",expect:"Implementation of tools as per NCGSE"},
  {name:"Shivangi Rana",grade:"9,8,7",fam:3,usage:"regularly",comfort:3,why:"Stay relevant in a future shaped by AI",problem:"Personalizing learning, automating routine tasks, giving instant feedback",expect:"Practical hands-on classroom strategies"},
  {name:"Deepika",grade:"9 and 10",fam:3,usage:"few",comfort:3,why:"Easy",problem:"New syllabus understanding",expect:"Learn better usage"},
  {name:"Vartika Gandas",grade:"4,5,6",fam:4,usage:"few",comfort:3,why:"How AI can improve education and personalise learning",problem:"Providing personalized support, bridging learning gaps",expect:"Understand how AI can be used in teaching"},
  {name:"Alisha Zuberi",grade:"8th and 9th",fam:2,usage:"few",comfort:3,why:"Create better lesson plans, simplify concepts, save time on assessments",problem:"Managing different learning levels, making lessons engaging for all students",expect:"Understand how to use AI effectively in teaching"},
  {name:"Tanshu Chawla",grade:"Nursery",fam:3,usage:"few",comfort:3,why:"Future proofing career and boosting efficiency",problem:"Minimise administrative workload",expect:"Comprehensive hands on learning"},
  {name:"Seema Kathuria",grade:"Class 6–7",fam:1,usage:"heard",comfort:1,why:"Keep myself aware of new technology",problem:"Cannot say till I know the benefits",expect:"Get familiar with AI tools"},
  {name:"Pooja Bhardwaj",grade:"12th",fam:3,usage:"few",comfort:2,why:"To make videos",problem:"Don't know where to learn from",expect:"Be able to use AI in teaching"},
  {name:"Sandeepa Kaur",grade:"1,2,5",fam:4,usage:"few",comfort:5,why:"Stay relevant, solve real-world problems",problem:"Different student levels, need personalized materials",expect:"Learn practical AI tools and techniques"},
  {name:"Ayushi Ghai",grade:"5 and 6",fam:3,usage:"few",comfort:4,why:"Be part of the change",problem:"Making subjects student-friendly for weak learners",expect:"Learn how to use AI in teaching"},
  {name:"Tisha Tokas",grade:"4,5",fam:4,usage:"regularly",comfort:5,why:"Make lesson plans more efficient and appealing",problem:"None as such",expect:"Better options for material generation"},
  {name:"Parul Mathur",grade:"1–6 (Library)",fam:1,usage:"heard",comfort:2,why:"Increase efficiency, learn latest technology",problem:"Manual stock ticking of library books",expect:"More awareness about AI"},
  {name:"Sneha",grade:"6,7,8",fam:1,usage:"regularly",comfort:1,why:"Make daily tasks easier",problem:"Providing different methods to attract student attention",expect:"Learn more about advanced technology"},
  {name:"Bindu Kaushik",grade:"8,9",fam:1,usage:"heard",comfort:3,why:"Important to upgrade myself",problem:"How to use this technology in class",expect:"To give useful class teaching"},
  {name:"Gagandeep Kaur",grade:"Nursery",fam:5,usage:"few",comfort:5,why:"Saves time and energy",problem:"Daily lesson plans and tracking each child's progress",expect:"Smart classroom strategies, child-centred learning"},
  {name:"Gauri Malan",grade:"Primary",fam:3,usage:"few",comfort:4,why:"AI is crucial for high-demand career opportunities",problem:"Time scarcity, workload, excessive paperwork",expect:"Identify, evaluate and implement AI tools"},
  {name:"Aartii Yajnik",grade:"4,5,6",fam:1,usage:"few",comfort:2,why:"Required everywhere nowadays",problem:"I don't think there's any problem",expect:"Better understanding to use AI"},
  {name:"Sumedha Bhateja",grade:"8,9,10",fam:4,usage:"regularly",comfort:4,why:"Fit into today's generation, make work easy",problem:"Managing work efficiently at same place",expect:"Learn something new"},
  {name:"Kohina Goyal",grade:"7–11",fam:3,usage:"few",comfort:3,why:"Drive innovation, automate tedious tasks",problem:"Hours spent on admin — drafting lesson plans and grading",expect:"Practical time-saving tools"},
  {name:"Mamta Varma",grade:"5,6,7",fam:1,usage:"heard",comfort:2,why:"Yes",problem:"Class Management",expect:"Update myself with AI"},
  {name:"Inderpal",grade:"4 and 5",fam:4,usage:"few",comfort:3,why:"Enhance knowledge",problem:"Making some assignments",expect:"Learn to use AI more frequently"},
  {name:"Raminder Kaur",grade:"Class 5",fam:2,usage:"few",comfort:4,why:"Makes tasks easier and quick",problem:"Developing teaching material",expect:"More organised and less time-taking work"},
  {name:"Sonal Ghildiyal",grade:"VII,VIII,IX",fam:2,usage:"regularly",comfort:2,why:"Improve teaching, learn new methods for students",problem:"Stay updated about subject advancements",expect:"Use AI to improve teaching"},
  {name:"Amita Mehta",grade:"Class 4",fam:2,usage:"few",comfort:2,why:"It is the future and changing the world",problem:"Create teaching resources, fast admin work",expect:"Understand how to use AI tools in teaching"},
  {name:"Dr. Rakesh Katoch",grade:"XI and XII",fam:2,usage:"few",comfort:4,why:"Upgrade and equip with new skills",problem:"Unknown areas in Sports field",expect:"Upgrade knowledge"},
  {name:"Ruchita Singh",grade:"Class 10",fam:3,usage:"few",comfort:4,why:"Keep up with the trend",problem:"Creative activities for lessons",expect:"Understand AI much better"},
  {name:"Shubha Singh",grade:"Nursery–2",fam:2,usage:"few",comfort:2,why:"Update myself",problem:"Not sure about specific problem",expect:"What all can be done in Art subject for small kids"},
  {name:"Subhash Singh",grade:"5,6,7,8,9,10",fam:1,usage:"never",comfort:3,why:"Upgrade",problem:"No problem as such",expect:"To learn"},
  {name:"Aparajitha Kalaga",grade:"9 & 10",fam:3,usage:"heard",comfort:3,why:"Keep up with the latest trend",problem:"Creating PPT for mathematical concepts",expect:"Learn basic methods"},
  {name:"Konika Sanyal",grade:"9th and 10th",fam:4,usage:"few",comfort:5,why:"Facilitate and enhance teaching-learning",problem:"Creating visual aids",expect:"Learn to use new AI tools"},
  {name:"Silky Arora",grade:"Nursery",fam:2,usage:"few",comfort:3,why:"Save time and make teaching more interesting",problem:"Videos according to lesson plan",expect:"Learn all options to make teaching interesting"},
  {name:"Medha Sancheti",grade:"Class 1",fam:3,usage:"few",comfort:4,why:"Keep myself updated",problem:"Hindi worksheet creation",expect:"Know more about AI tools"},
  {name:"Anshu Takkar",grade:"Class II",fam:3,usage:"few",comfort:5,why:"Make my teaching more effective",problem:"Time consuming lesson planning",expect:"Gain practical knowledge on using AI"},
  {name:"Himani Goindi",grade:"9 and 10",fam:4,usage:"never",comfort:3,why:"Better communication and understanding",problem:"Quick information and in-depth knowledge",expect:"Want to learn AI document technology"},
  {name:"Suman Ali",grade:"Nursery–2",fam:2,usage:"few",comfort:3,why:"Yes",problem:"Better and more creative ideas for teachers",expect:"Be more updated with AI"},
  {name:"Ranjusha Keshri",grade:"KG",fam:2,usage:"few",comfort:3,why:"Update myself",problem:"Improve classroom teachings",expect:"Include AI in daily teaching methods"},
  {name:"Aarti Taneja",grade:"Nursery",fam:2,usage:"few",comfort:5,why:"Great career opportunities and personal growth",problem:"Translate content and explain in simpler language",expect:"Increase my knowledge"},
  {name:"Ritu Ahuja",grade:"Class 1",fam:4,usage:"few",comfort:4,why:"Learn more",problem:"Nothing",expect:"More learning"},
  {name:"Baikunthi Chauhan",grade:"Class 1B",fam:3,usage:"few",comfort:3,why:"Work done easily and fast",problem:"Learning through animations",expect:"Smart learning and teaching"},
  {name:"Shikha Sharma",grade:"Class 6–7",fam:2,usage:"never",comfort:3,why:"Increase productivity, save time, accurate results",problem:"No current problem but may be useful in future",expect:"Beginners friendly course"},
  {name:"Divya (KG)",grade:"KG",fam:3,usage:"never",comfort:2,why:"Make teaching effective and creative",problem:"Making worksheets and teaching aids",expect:"Understand how to use AI tools daily"},
  {name:"Minakshi Puri",grade:"Class 1",fam:3,usage:"regularly",comfort:3,why:"It's an upcoming tool",problem:"",expect:"Learn how AI can help in innovative teaching"},
  {name:"Nidhi Mittal",grade:"Class 1",fam:3,usage:"few",comfort:4,why:"Upgrade myself with technology",problem:"Nothing",expect:"Use AI tools most effectively"},
  {name:"Shashank",grade:"9 and 10",fam:2,usage:"few",comfort:3,why:"Today's demand",problem:"Automated grading, data analysis, needs of CWSN students",expect:"Learn something helpful in teaching"},
  {name:"Tanya Sharma",grade:"KG",fam:1,usage:"few",comfort:2,why:"Enhance my skills",problem:"Pictures and videos for teaching",expect:"Learn more about AI"},
  {name:"Kuljit Singh",grade:"11 & 12 (PE)",fam:1,usage:"heard",comfort:2,why:"Keep up with advancement in today's world",problem:"Usually don't use AI",expect:"Know how it can help in my work"},
  {name:"Bhawna Rawat",grade:"1–5",fam:4,usage:"few",comfort:2,why:"Experimentation, new ways to solve and create content",problem:"Sometimes AI won't understand our command",expect:"Learn something new"},
  {name:"Neha Singh",grade:"Pre-Primary",fam:4,usage:"few",comfort:3,why:"More knowledge",problem:"Time management, creating engaging lesson plans, handling different learning levels",expect:"AI tools to make teaching easier, create better study material, save time"},
  {name:"Joyeeta Mukherjee",grade:"4 & 5",fam:2,usage:"heard",comfort:2,why:"Yes",problem:"Students focus on learning",expect:"New and exciting ways to grab learner attention"},
  {name:"Ritu Gandas",grade:"X,XI,XII",fam:3,usage:"few",comfort:5,why:"Updated with tech, boost efficiency, unlock creative potential",problem:"Create digital learning tools for students",expect:"Learn AI for kids learning"},
  {name:"Akshita",grade:"Preschool",fam:4,usage:"few",comfort:5,why:"Be updated in today's world",problem:"Smarter way to tackle children",expect:"Be more confident in handling students"},
  {name:"Neha Dagar",grade:"Nursery",fam:4,usage:"regularly",comfort:5,why:"It's the future",problem:"Teaching methods",expect:"New ideas"},
  {name:"Renuka Sharma",grade:"10,11,12",fam:3,usage:"few",comfort:4,why:"Need of the hour, updated with technology",problem:"Nothing as such",expect:"Help in study material and PPT"},
  {name:"Tanuja Adhikari",grade:"7–12 (Librarian)",fam:3,usage:"few",comfort:5,why:"Work smarter and stay ahead",problem:"Maintaining student interest in reading in digital age",expect:"Learn AI tools for library use"},
  {name:"Alka Dixit",grade:"Nursery",fam:2,usage:"few",comfort:2,why:"Yes",problem:"Yes",expect:"Yes"},
  {name:"Pallavi Bajaj",grade:"Classes 4–5",fam:3,usage:"few",comfort:3,why:"Creative activities, different ways of explaining concepts to kids",problem:"Keep students engaged, explain difficult topics simply",expect:"AI tools beyond ChatGPT and responsible AI use"},
  {name:"Ranjana Rawat",grade:"Nursery",fam:4,usage:"few",comfort:5,why:"Make work easier and increase efficiency",problem:"Planning activities",expect:"More AI tools"},
  {name:"Neeru",grade:"Class 3",fam:3,usage:"heard",comfort:2,why:"Upgrade my skills",problem:"Responding to parent queries after school hours",expect:"Actual uses of AI for teaching children"},
  {name:"Seema Budhiraja",grade:"11 and 12",fam:3,usage:"few",comfort:3,why:"Improve my knowledge",problem:"No problem in teaching",expect:"Enhance my knowledge"},
  {name:"Suman Dhankhar",grade:"Class 4–5",fam:1,usage:"never",comfort:3,why:"Learn and update myself",problem:"Making assignments",expect:"Be more informative"},
  {name:"Krishna Pandey",grade:"Pre-Primary",fam:1,usage:"heard",comfort:4,why:"Upgrade my skills",problem:"No",expect:"Understand what AI is and how it works"},
  {name:"Unnati Bhardwaj",grade:"Nursery–4 (Dance)",fam:3,usage:"regularly",comfort:4,why:"Complete administrative work easily",problem:"Create list of new songs for dance class",expect:"Improve interaction with AI for dance classes"},
  {name:"Sapna Mawar",grade:"9 or 10",fam:2,usage:"few",comfort:4,why:"Understand some field",problem:"Not exactly know AI",expect:"Understand and use in teaching"},
  {name:"Sangeeta Tyagi",grade:"10,11,12",fam:1,usage:"regularly",comfort:1,why:"Make my life easy",problem:"Making complicated structures in chemistry",expect:"Learn to draw chemical structures"},
  {name:"Roshni Sahdev",grade:"7 & 8",fam:4,usage:"heard",comfort:4,why:"Towards advanced learning",problem:"Not very tech savvy",expect:"Learn new tools and implement them"},
  {name:"Prabhu Bhardwaj",grade:"4–7 (PE)",fam:4,usage:"few",comfort:3,why:"Improve teaching methods, make PE more engaging",problem:"Different fitness levels, tracking progress, keeping all students engaged",expect:"Basics of AI, track student progress, plan activities"},
  {name:"Neeru Gupta",grade:"Class 5",fam:1,usage:"heard",comfort:3,why:"Prepare children for AI future",problem:"Attendance tracking, grading assignments, generating lesson plans",expect:"Know how to use AI in daily life"},
  {name:"Neelu Kapoor",grade:"8,9,10",fam:3,usage:"few",comfort:3,why:"Keep up to date with technology",problem:"Visual depiction of topics and concepts",expect:"Become aware of suitable tools for teaching"},
  {name:"Kusum Farishta",grade:"Class 2",fam:3,usage:"few",comfort:2,why:"Understand AI and improve efficiency",problem:"Teaching aids to make concepts understandable and interesting",expect:"Find solution of every problem"},
  {name:"Chander Shekhar",grade:"Class 6–7",fam:2,usage:"few",comfort:1,why:"Stay relevant, automate tedious tasks for efficiency",problem:"Time-consuming administrative tasks",expect:"Utilising tools"},
  {name:"Rashmi Sharma",grade:"VI,VII,VIII",fam:3,usage:"few",comfort:3,why:"Match myself with today's technology",problem:"Performing activities in the classroom",expect:"Learn something interesting and new"},
  {name:"Priya Chahar",grade:"Nursery",fam:3,usage:"few",comfort:3,why:"Leverage work and do tasks within TAT",problem:"Draft reports, adapt worksheets, generate lesson plans, quizzes",expect:"Accuracy, safety and saving time with AI"},
  {name:"Anjana Sharma",grade:"XI & XII",fam:2,usage:"few",comfort:3,why:"Updated about latest technology",problem:"Updating current events for students",expect:"Better knowledge to use AI for effective teaching"},
  {name:"Sampurna K. Jha",grade:"8,9,10",fam:3,usage:"few",comfort:3,why:"Stay up to date with AI tools in teaching",problem:"Tracking progress, creating lesson plans, interactive lessons",expect:"Learn genAI prompts for lesson plans and assignments"},
  {name:"Sampurna Jha",grade:"8,9,10",fam:3,usage:"few",comfort:3,why:"Automate repeated tasks in teaching",problem:"Lesson plans, attendance, scoring, creating assignments",expect:"Learn prompt engineering to automate repeatable tasks"},
  {name:"Shivi Mehrotra",grade:"IX and X",fam:2,usage:"few",comfort:2,why:"More career opportunity",problem:"Use of technology in school",expect:"How to convert syllabus using AI tools"},
  {name:"Lakshmi Kumari",grade:"3,4,5",fam:3,usage:"regularly",comfort:2,why:"Become a better teacher with AI tools",problem:"Different learning speeds, giving equal attention to all students",expect:"Practical knowledge of AI tools for engaging teaching"}
];

/* ─────────────────────────────────────────
   HELPERS
───────────────────────────────────────── */
function getGradeGroup(g){
  const gl=g.toLowerCase();
  if(/nursery|kg|pre|preschool|preprimary|kinder|1b/.test(gl))return 'preprimary';
  if(/\b(ix|xi|xii|x\b|9|10|11|12|senior)/.test(gl))return 'senior';
  if(/\b(6|7|8|vi|vii|viii)\b/.test(gl))return 'middle';
  return 'primary';
}
function getProblemTags(p){
  const pl=p.toLowerCase();
  const t=[];
  if(/lesson|plan|worksheet|content|material|ppt|quiz|syllabus|note/.test(pl))t.push('lesson');
  if(/admin|paper|mark|attendance|record|grading|report|assign/.test(pl))t.push('admin');
  if(/engag|attention|interest|activity|creative|fun|interactive/.test(pl))t.push('engagement');
  if(/time|quick|fast|save|tat|workload/.test(pl))t.push('time');
  if(/personal|different level|individual|diverse|ability|cwsn|level/.test(pl))t.push('personal');
  return t;
}

const allData=RAW.map(r=>({...r,gradeGroup:getGradeGroup(r.grade),problemTags:getProblemTags(r.problem)}));
let filtered=[...allData];
let sortKey='',sortAsc=true;

/* ─────────────────────────────────────────
   CHARTS
───────────────────────────────────────── */
const FCOLORS=['#e03030','#f0802a','#e8c030','#5bc77a','#4a90d9'];
const GCOLORS={preprimary:'#9b59b6',primary:'#4a90d9',middle:'#f0a030',senior:'#e03030'};
const GLABELS={preprimary:'Pre-Primary/KG',primary:'Primary (1–5)',middle:'Middle (6–8)',senior:'Senior (9–12)'};
const UCOLORS={regularly:'#5bc77a',few:'#4a90d9',heard:'#f0802a',never:'#e03030'};
const ULABELS={regularly:'Uses Regularly',few:'Used Few Times',heard:'Heard, Never Used',never:'Not At All'};

const sharedOpts={responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false},tooltip:{callbacks:{label:c=>' '+c.raw+' teachers'}}}};

const famCI=new Chart(document.getElementById('famChart'),{
  type:'bar',
  data:{labels:['L1','L2','L3','L4','L5'],datasets:[{data:[0,0,0,0,0],backgroundColor:FCOLORS,borderRadius:5,borderSkipped:false}]},
  options:{...sharedOpts,scales:{x:{ticks:{color:'#666',font:{size:10}},grid:{color:'rgba(255,255,255,0.04)'}},y:{ticks:{color:'#666',font:{size:10}},grid:{color:'rgba(255,255,255,0.04)'},beginAtZero:true}}}
});
const usageCI=new Chart(document.getElementById('usageChart'),{
  type:'doughnut',
  data:{labels:['Regularly','Few Times','Heard Only','Not At All'],datasets:[{data:[0,0,0,0],backgroundColor:[UCOLORS.regularly,UCOLORS.few,UCOLORS.heard,UCOLORS.never],borderWidth:0,hoverOffset:6}]},
  options:{...sharedOpts,cutout:'62%'}
});
const comfortCI=new Chart(document.getElementById('comfortChart'),{
  type:'bar',
  data:{labels:['L1','L2','L3','L4','L5'],datasets:[{data:[0,0,0,0,0],backgroundColor:FCOLORS,borderRadius:5,borderSkipped:false}]},
  options:{...sharedOpts,scales:{x:{ticks:{color:'#666',font:{size:10}},grid:{color:'rgba(255,255,255,0.04)'}},y:{ticks:{color:'#666',font:{size:10}},grid:{color:'rgba(255,255,255,0.04)'},beginAtZero:true}}}
});
const gradeCI=new Chart(document.getElementById('gradeChart'),{
  type:'doughnut',
  data:{labels:['Pre-Primary','Primary','Middle','Senior'],datasets:[{data:[0,0,0,0],backgroundColor:[GCOLORS.preprimary,GCOLORS.primary,GCOLORS.middle,GCOLORS.senior],borderWidth:0,hoverOffset:6}]},
  options:{...sharedOpts,cutout:'62%'}
});

/* ─────────────────────────────────────────
   UPDATE ALL
───────────────────────────────────────── */
function updateAll(){
  const n=filtered.length;
  if(n===0){
    document.getElementById('s-total').textContent='0';
    document.getElementById('s-beginners').innerHTML='—';
    document.getElementById('s-used').innerHTML='—';
    document.getElementById('s-avg').textContent='—';
    document.getElementById('s-comfort').textContent='—';
    famCI.data.datasets[0].data=[0,0,0,0,0];famCI.update();
    usageCI.data.datasets[0].data=[0,0,0,0];usageCI.update();
    comfortCI.data.datasets[0].data=[0,0,0,0,0];comfortCI.update();
    gradeCI.data.datasets[0].data=[0,0,0,0];gradeCI.update();
    renderTable();return;
  }

  const fc=[0,0,0,0,0],cc=[0,0,0,0,0];
  const uc={regularly:0,few:0,heard:0,never:0};
  const gc={preprimary:0,primary:0,middle:0,senior:0};
  const pc={lesson:0,admin:0,engagement:0,time:0,personal:0};
  const motiv={rel:0,learn:0,time:0,teach:0,career:0};

  filtered.forEach(r=>{
    fc[r.fam-1]++;
    cc[r.comfort-1]++;
    uc[r.usage]++;
    if(gc[r.gradeGroup]!==undefined)gc[r.gradeGroup]++;
    r.problemTags.forEach(t=>{if(pc[t]!==undefined)pc[t]++;});
    const w=r.why.toLowerCase();
    if(/relev|keep up|trend|future|updat|upgrad/.test(w))motiv.rel++;
    if(/learn|know|aware|curious/.test(w))motiv.learn++;
    if(/time|easy|efficien|fast|save/.test(w))motiv.time++;
    if(/teach|student|class|engage|lesson/.test(w))motiv.teach++;
    if(/career|opportun|skill|growth/.test(w))motiv.career++;
  });

  /* stat cards */
  document.getElementById('s-total').textContent=n;
  document.getElementById('s-beginners').innerHTML=Math.round(filtered.filter(r=>r.fam<=2).length/n*100)+'<small>%</small>';
  document.getElementById('s-used').innerHTML=Math.round(filtered.filter(r=>r.usage==='few'||r.usage==='regularly').length/n*100)+'<small>%</small>';
  document.getElementById('s-avg').textContent=(filtered.reduce((s,r)=>s+r.fam,0)/n).toFixed(1);
  document.getElementById('s-comfort').textContent=(filtered.reduce((s,r)=>s+r.comfort,0)/n).toFixed(1);

  /* counter */
  document.getElementById('counterPill').textContent=n+' of '+RAW.length+' teachers';
  document.getElementById('tab1count').textContent=n;
  document.getElementById('tab2count').textContent=n;
  document.getElementById('tableTitle').textContent=n+' Teacher'+(n===1?'':'s')+(n<RAW.length?' (filtered)':'');

  /* familiarity */
  famCI.data.datasets[0].data=fc;famCI.update();
  ['f1','f2','f3','f4','f5'].forEach((k,i)=>document.getElementById('leg-'+k).textContent='L'+(i+1)+': '+fc[i]);
  ['c1','c2','c3','c4','c5'].forEach((k,i)=>document.getElementById('leg-'+k).textContent='L'+(i+1)+': '+cc[i]);

  /* comfort */
  comfortCI.data.datasets[0].data=cc;comfortCI.update();

  /* usage */
  usageCI.data.datasets[0].data=[uc.regularly,uc.few,uc.heard,uc.never];usageCI.update();
  document.getElementById('usageLeg').innerHTML=
    ['regularly','few','heard','never'].map(k=>`<div class="li"><div class="ld" style="background:${UCOLORS[k]}"></div>${ULABELS[k]}: <b style="color:#ccc">${uc[k]}</b></div>`).join('');

  /* grade */
  gradeCI.data.datasets[0].data=[gc.preprimary,gc.primary,gc.middle,gc.senior];gradeCI.update();
  document.getElementById('gradeLeg').innerHTML=
    ['preprimary','primary','middle','senior'].map(k=>`<div class="li"><div class="ld" style="background:${GCOLORS[k]}"></div>${GLABELS[k]}: <b style="color:#ccc">${gc[k]}</b></div>`).join('');

  /* motivation bars */
  const motRows=[
    {label:'Stay Relevant / Keep Up',val:motiv.rel,color:'#4a90d9'},
    {label:'Curiosity / Learn More',val:motiv.learn,color:'#9b59b6'},
    {label:'Save Time / Efficiency',val:motiv.time,color:'#f0802a'},
    {label:'Effective Teaching',val:motiv.teach,color:'#5bc77a'},
    {label:'Career Growth',val:motiv.career,color:'#e8c030'}
  ];
  const mx1=Math.max(...motRows.map(r=>r.val))||1;
  document.getElementById('motivBars').innerHTML=motRows.map(r=>`
    <div class="bar-item">
      <label>${r.label}<span>${r.val} teachers</span></label>
      <div class="bar-bg"><div class="bar-fill" style="width:${Math.round(r.val/mx1*100)}%;background:${r.color}"></div></div>
    </div>`).join('');

  /* problem bars */
  const probRows=[
    {label:'Lesson Planning / Content',val:pc.lesson,color:'#e03030'},
    {label:'Student Engagement',val:pc.engagement,color:'#f0802a'},
    {label:'Admin / Paperwork',val:pc.admin,color:'#4a90d9'},
    {label:'Time Management',val:pc.time,color:'#e8c030'},
    {label:'Personalized Learning',val:pc.personal,color:'#5bc77a'}
  ];
  const mx2=Math.max(...probRows.map(r=>r.val))||1;
  document.getElementById('problemBars').innerHTML=probRows.map(r=>`
    <div class="bar-item">
      <label>${r.label}<span>${r.val} teachers</span></label>
      <div class="bar-bg"><div class="bar-fill" style="width:${Math.round(r.val/mx2*100)}%;background:${r.color}"></div></div>
    </div>`).join('');

  renderTable();
}

/* ─────────────────────────────────────────
   TABLE
───────────────────────────────────────── */
const PROB_NAMES={lesson:'Lesson Plan',admin:'Admin',engagement:'Engagement',time:'Time Mgmt',personal:'Personalized'};
const PROB_COLORS={lesson:'#e03030',admin:'#4a90d9',engagement:'#f0802a',time:'#e8c030',personal:'#5bc77a'};

function renderTable(){
  const tbody=document.getElementById('tableBody');
  if(!filtered.length){
    tbody.innerHTML='<tr><td colspan="9" class="no-data">No teachers match the current filters.</td></tr>';return;
  }
  const comfortColors=['','#e03030','#f0802a','#e8c030','#5bc77a','#4a90d9'];
  tbody.innerHTML=filtered.map((r,i)=>{
    const uLabel={regularly:'Regularly',few:'Few Times',heard:'Heard Only',never:'Not At All'}[r.usage];
    const uCls={regularly:'usage-r',few:'usage-f',heard:'usage-h',never:'usage-n'}[r.usage];
    const probTagsHTML=r.problemTags.map(t=>`<span class="prob-tag" style="border-color:${PROB_COLORS[t]};color:${PROB_COLORS[t]}">${PROB_NAMES[t]}</span>`).join('');
    return `<tr>
      <td style="color:#444;font-size:10px">${i+1}</td>
      <td style="font-weight:600;color:#e0ddf0;white-space:nowrap">${r.name}</td>
      <td><span class="grade-tag">${r.grade.length>14?r.grade.slice(0,14)+'…':r.grade}</span></td>
      <td><span class="fam-tag fam-${r.fam}">L${r.fam}</span></td>
      <td class="${uCls}">${uLabel}</td>
      <td>
        <div class="comfort-bar-wrap">
          <div class="comfort-bar-bg"><div class="comfort-bar-fill" style="width:${r.comfort*20}%;background:${comfortColors[r.comfort]}"></div></div>
          <span style="font-size:11px;color:${comfortColors[r.comfort]}">${r.comfort}</span>
        </div>
      </td>
      <td><div class="cell-text" title="${r.why}">${r.why||'—'}</div></td>
      <td>
        <div style="margin-bottom:4px">${probTagsHTML||''}</div>
        <div class="cell-text" style="font-size:10px;color:#777" title="${r.problem}">${r.problem||'—'}</div>
      </td>
      <td><div class="cell-text" title="${r.expect}">${r.expect||'—'}</div></td>
    </tr>`;
  }).join('');
}

/* ─────────────────────────────────────────
   FILTER + SORT
───────────────────────────────────────── */
function applyFilters(){
  const search=document.getElementById('searchInput').value.toLowerCase();
  const fam=document.getElementById('famFilter').value;
  const usage=document.getElementById('usageFilter').value;
  const comfort=document.getElementById('comfortFilter').value;
  const grade=document.getElementById('gradeFilter').value;
  const problem=document.getElementById('problemFilter').value;

  filtered=allData.filter(r=>{
    if(search&&!`${r.name} ${r.why} ${r.problem} ${r.expect} ${r.grade}`.toLowerCase().includes(search))return false;
    if(fam&&r.fam!=fam)return false;
    if(usage&&r.usage!==usage)return false;
    if(comfort&&r.comfort!=comfort)return false;
    if(grade&&r.gradeGroup!==grade)return false;
    if(problem&&!r.problemTags.includes(problem))return false;
    return true;
  });

  if(sortKey){
    filtered.sort((a,b)=>{
      let va=a[sortKey],vb=b[sortKey];
      if(typeof va==='string'){va=va.toLowerCase();vb=vb.toLowerCase();}
      return sortAsc?(va>vb?1:-1):(va<vb?1:-1);
    });
  }
  updateAll();
}

function sortTable(key){
  document.querySelectorAll('.data-table th').forEach(th=>{th.classList.remove('sort-asc','sort-desc')});
  if(sortKey===key)sortAsc=!sortAsc;else{sortKey=key;sortAsc=true;}
  const thMap={name:'th-name',grade:'th-grade',fam:'th-fam',usage:'th-usage',comfort:'th-comfort'};
  if(thMap[key]){document.getElementById(thMap[key]).classList.add(sortAsc?'sort-asc':'sort-desc');}
  applyFilters();
}

function resetFilters(){
  ['searchInput','famFilter','usageFilter','comfortFilter','gradeFilter','problemFilter'].forEach(id=>{
    const el=document.getElementById(id);
    el.tagName==='SELECT'?el.value='':el.value='';
  });
  sortKey='';sortAsc=true;
  document.querySelectorAll('.data-table th').forEach(th=>{th.classList.remove('sort-asc','sort-desc')});
  filtered=[...allData];
  updateAll();
}

function exportCSV(){
  const headers=['Name','Grade','Familiarity','Usage','Tech Comfort','Why Learn AI','Problem','Expectation'];
  const rows=filtered.map(r=>[
    r.name,r.grade,r.fam,r.usage,r.comfort,
    '"'+r.why.replace(/"/g,"'")+'"',
    '"'+r.problem.replace(/"/g,"'")+'"',
    '"'+r.expect.replace(/"/g,"'")+'"'
  ]);
  const csv=[headers,...rows].map(r=>r.join(',')).join('\n');
  const a=document.createElement('a');
  a.href='data:text/csv;charset=utf-8,'+encodeURIComponent(csv);
  a.download='Teacher_AI_Assessment_Filtered.csv';
  a.click();
}

/* ─────────────────────────────────────────
   TAB SWITCH
───────────────────────────────────────── */
function switchTab(name,btn){
  document.querySelectorAll('.tab-btn').forEach(b=>b.classList.remove('active'));
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  btn.classList.add('active');
  document.getElementById('page-'+name).classList.add('active');
}

/* ─────────────────────────────────────────
   EVENTS + INIT
───────────────────────────────────────── */
['searchInput','famFilter','usageFilter','comfortFilter','gradeFilter','problemFilter'].forEach(id=>{
  document.getElementById(id).addEventListener('input',applyFilters);
  document.getElementById(id).addEventListener('change',applyFilters);
});

updateAll();
</script>
</body>
</html>
