<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>TaskBoard — Your Personal Command Center</title>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;500;600;700;800&family=DM+Mono:wght@400;500&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #080809;
    --bg2: #0f0f12;
    --bg3: #16161b;
    --bg4: #1d1d24;
    --border: #222228;
    --border2: #2e2e38;
    --text: #e8e5de;
    --text2: #888895;
    --text3: #44444f;
    --accent: #f0ece2;
    --c-work: #4facfe;
    --c-personal: #c084fc;
    --c-urgent: #fb7185;
    --c-health: #34d399;
    --c-learning: #fbbf24;
    --c-other: #9ca3af;
    --radius: 14px;
    --radius-sm: 8px;
  }

  html { scroll-behavior: smooth; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='1'/%3E%3C/svg%3E");
    opacity: 0.025;
    pointer-events: none;
    z-index: 0;
  }

  .sidebar {
    position: fixed;
    top: 0; left: 0; bottom: 0;
    width: 240px;
    background: var(--bg2);
    border-right: 1px solid var(--border);
    display: flex;
    flex-direction: column;
    padding: 0;
    z-index: 100;
  }

  .sidebar-logo {
    padding: 28px 24px 24px;
    border-bottom: 1px solid var(--border);
  }

  .logo-mark {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 20px;
    color: var(--accent);
    letter-spacing: -0.5px;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .logo-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: linear-gradient(135deg, #fb7185, #fbbf24, #34d399, #4facfe, #c084fc);
    flex-shrink: 0;
    animation: spin-hue 4s linear infinite;
  }

  @keyframes spin-hue {
    0% { filter: hue-rotate(0deg); }
    100% { filter: hue-rotate(360deg); }
  }

  .logo-sub {
    font-size: 11px;
    color: var(--text3);
    font-family: 'DM Mono', monospace;
    letter-spacing: 1px;
    margin-top: 4px;
    text-transform: uppercase;
  }

  .sidebar-nav {
    padding: 20px 12px;
    flex: 1;
    overflow-y: auto;
  }

  .nav-section-label {
    font-size: 10px;
    font-family: 'DM Mono', monospace;
    color: var(--text3);
    letter-spacing: 1.5px;
    text-transform: uppercase;
    padding: 0 12px;
    margin-bottom: 8px;
    margin-top: 16px;
  }

  .nav-section-label:first-child { margin-top: 0; }

  .nav-btn {
    display: flex;
    align-items: center;
    gap: 10px;
    width: 100%;
    padding: 9px 12px;
    border-radius: var(--radius-sm);
    border: none;
    background: none;
    color: var(--text2);
    font-family: 'DM Sans', sans-serif;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.15s;
    text-align: left;
  }

  .nav-btn:hover { background: var(--bg3); color: var(--text); }
  .nav-btn.active { background: var(--bg4); color: var(--accent); }

  .nav-icon {
    width: 18px; height: 18px;
    flex-shrink: 0;
    opacity: 0.7;
  }

  .nav-btn.active .nav-icon { opacity: 1; }

  .nav-count {
    margin-left: auto;
    font-size: 11px;
    font-family: 'DM Mono', monospace;
    background: var(--bg);
    padding: 1px 7px;
    border-radius: 99px;
    color: var(--text3);
    border: 1px solid var(--border);
  }

  .nav-btn.active .nav-count { color: var(--text2); }

  .cat-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  .sidebar-footer {
    padding: 16px 20px;
    border-top: 1px solid var(--border);
    font-size: 12px;
    color: var(--text3);
    font-family: 'DM Mono', monospace;
  }

  .main {
    margin-left: 240px;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    position: relative;
    z-index: 1;
  }

  .topbar {
    position: sticky; top: 0;
    background: rgba(8,8,9,0.85);
    backdrop-filter: blur(16px);
    -webkit-backdrop-filter: blur(16px);
    border-bottom: 1px solid var(--border);
    padding: 0 32px;
    height: 60px;
    display: flex;
    align-items: center;
    gap: 16px;
    z-index: 50;
  }

  .search-wrap {
    flex: 1;
    max-width: 400px;
    position: relative;
  }

  .search-icon {
    position: absolute;
    left: 12px; top: 50%;
    transform: translateY(-50%);
    width: 15px; height: 15px;
    color: var(--text3);
    pointer-events: none;
  }

  .search-input {
    width: 100%;
    background: var(--bg3);
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 8px 12px 8px 36px;
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    font-size: 14px;
    outline: none;
    transition: border-color 0.15s;
  }

  .search-input:focus { border-color: var(--border2); }
  .search-input::placeholder { color: var(--text3); }

  .topbar-right {
    margin-left: auto;
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .topbar-date {
    font-size: 13px;
    color: var(--text2);
    font-family: 'DM Mono', monospace;
  }

  .clear-done-btn {
    background: none;
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 6px 12px;
    color: var(--text2);
    font-family: 'DM Sans', sans-serif;
    font-size: 13px;
    cursor: pointer;
    transition: all 0.15s;
  }

  .clear-done-btn:hover { border-color: #fb7185; color: #fb7185; }

  .content {
    padding: 36px 36px 80px;
    flex: 1;
  }

  .page-header {
    margin-bottom: 32px;
    animation: fadeUp 0.4s ease both;
  }

  .page-title {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 36px;
    letter-spacing: -1px;
    color: var(--accent);
    line-height: 1;
    margin-bottom: 6px;
  }

  .page-sub {
    font-size: 14px;
    color: var(--text2);
  }

  .progress-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 22px 24px;
    margin-bottom: 24px;
    animation: fadeUp 0.4s 0.05s ease both;
  }

  .progress-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    margin-bottom: 14px;
  }

  .progress-title {
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 14px;
    color: var(--text);
  }

  .progress-pct {
    font-family: 'DM Mono', monospace;
    font-size: 28px;
    font-weight: 500;
    color: var(--accent);
    line-height: 1;
    transition: all 0.3s;
  }

  .bar-track {
    height: 12px;
    background: var(--bg4);
    border-radius: 99px;
    overflow: hidden;
    position: relative;
  }

  .bar-fill {
    height: 100%;
    border-radius: 99px;
    background: linear-gradient(90deg,
      #fb7185 0%, #f97316 14%, #fbbf24 28%, #34d399 42%,
      #4facfe 57%, #818cf8 71%, #c084fc 85%, #fb7185 100%
    );
    background-size: 200% 100%;
    animation: shimmer 3s linear infinite;
    transition: width 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
    min-width: 0;
    position: relative;
  }

  .bar-fill::after {
    content: '';
    position: absolute;
    right: 0; top: 0; bottom: 0;
    width: 8px;
    background: rgba(255,255,255,0.4);
    border-radius: 99px;
  }

  @keyframes shimmer {
    0% { background-position: 200% 0; }
    100% { background-position: -200% 0; }
  }

  .bar-milestones {
    display: flex;
    justify-content: space-between;
    margin-top: 8px;
  }

  .milestone {
    font-size: 10px;
    font-family: 'DM Mono', monospace;
    color: var(--text3);
  }

  .stats-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
    gap: 12px;
    margin-bottom: 24px;
    animation: fadeUp 0.4s 0.1s ease both;
  }

  .stat-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 18px 20px;
    position: relative;
    overflow: hidden;
    transition: border-color 0.2s;
  }

  .stat-card:hover { border-color: var(--border2); }

  .stat-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
  }

  .stat-card.s-total::before  { background: linear-gradient(90deg, #4facfe, #c084fc); }
  .stat-card.s-done::before   { background: linear-gradient(90deg, #34d399, #4facfe); }
  .stat-card.s-left::before   { background: linear-gradient(90deg, #fbbf24, #fb7185); }
  .stat-card.s-urgent::before { background: #fb7185; }
  .stat-card.s-streak::before { background: linear-gradient(90deg, #fbbf24, #f97316); }

  .stat-num {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 32px;
    color: var(--accent);
    line-height: 1;
    margin-bottom: 6px;
  }

  .stat-label {
    font-size: 12px;
    color: var(--text2);
    letter-spacing: 0.3px;
  }

  .add-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px 22px;
    margin-bottom: 24px;
    animation: fadeUp 0.4s 0.15s ease both;
  }

  .add-row1 {
    display: flex;
    gap: 10px;
    margin-bottom: 12px;
  }

  .add-input {
    flex: 1;
    background: var(--bg3);
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 11px 16px;
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    font-size: 15px;
    outline: none;
    transition: border-color 0.15s;
  }

  .add-input:focus { border-color: var(--border2); }
  .add-input::placeholder { color: var(--text3); }

  .add-btn {
    background: var(--accent);
    color: var(--bg);
    border: none;
    border-radius: var(--radius-sm);
    padding: 11px 22px;
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 14px;
    cursor: pointer;
    transition: opacity 0.15s, transform 0.1s;
    white-space: nowrap;
  }

  .add-btn:hover { opacity: 0.88; }
  .add-btn:active { transform: scale(0.97); }

  .add-row2 {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
    align-items: center;
  }

  .add-row2 label {
    font-size: 12px;
    color: var(--text2);
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .add-row2 select,
  .add-row2 input[type="date"] {
    background: var(--bg3);
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 6px 10px;
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    font-size: 13px;
    outline: none;
    cursor: pointer;
  }

  .add-row2 input[type="date"]::-webkit-calendar-picker-indicator {
    filter: invert(0.6);
    cursor: pointer;
  }

  .priority-wrap {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .priority-btn {
    padding: 5px 10px;
    border-radius: 99px;
    border: 1px solid var(--border);
    background: none;
    font-size: 12px;
    cursor: pointer;
    color: var(--text2);
    font-family: 'DM Sans', sans-serif;
    transition: all 0.15s;
  }

  .priority-btn.sel-low  { border-color: #34d399; color: #34d399; background: rgba(52,211,153,0.08); }
  .priority-btn.sel-med  { border-color: #fbbf24; color: #fbbf24; background: rgba(251,191,36,0.08); }
  .priority-btn.sel-high { border-color: #fb7185; color: #fb7185; background: rgba(251,113,133,0.08); }

  .tasks-section { animation: fadeUp 0.4s 0.2s ease both; }

  .section-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 12px;
  }

  .section-title {
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 13px;
    color: var(--text2);
    letter-spacing: 0.5px;
    text-transform: uppercase;
  }

  .sort-select {
    background: none;
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 4px 10px;
    color: var(--text2);
    font-family: 'DM Mono', monospace;
    font-size: 12px;
    outline: none;
    cursor: pointer;
  }

  .tasks-list {
    display: flex;
    flex-direction: column;
    gap: 6px;
  }

  .task-item {
    display: flex;
    align-items: center;
    gap: 12px;
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 13px 16px;
    transition: border-color 0.2s, opacity 0.2s, transform 0.15s;
    position: relative;
    overflow: hidden;
    animation: slideIn 0.25s ease;
  }

  @keyframes slideIn {
    from { opacity: 0; transform: translateX(-8px); }
    to   { opacity: 1; transform: translateX(0); }
  }

  .task-item::before {
    content: '';
    position: absolute;
    left: 0; top: 0; bottom: 0;
    width: 3px;
  }

  .task-item.cat-work::before     { background: var(--c-work); }
  .task-item.cat-personal::before { background: var(--c-personal); }
  .task-item.cat-urgent::before   { background: var(--c-urgent); }
  .task-item.cat-health::before   { background: var(--c-health); }
  .task-item.cat-learning::before { background: var(--c-learning); }
  .task-item.cat-other::before    { background: var(--c-other); }

  .task-item:hover { border-color: var(--border2); transform: translateX(2px); }
  .task-item.done  { opacity: 0.4; }
  .task-item.done .task-text { text-decoration: line-through; color: var(--text3); }
  .task-item.overdue:not(.done) { border-color: rgba(251,113,133,0.25); }

  .check-btn {
    width: 22px; height: 22px;
    border-radius: 50%;
    border: 2px solid var(--border2);
    background: none;
    cursor: pointer;
    flex-shrink: 0;
    display: flex; align-items: center; justify-content: center;
    transition: all 0.2s;
  }

  .check-btn:hover { border-color: #34d399; }
  .check-btn.checked { background: #34d399; border-color: #34d399; }
  .check-btn.checked::after {
    content: '';
    width: 5px; height: 9px;
    border: 2px solid #080809;
    border-left: none; border-top: none;
    transform: rotate(45deg) translateY(-1px);
    display: block;
  }

  .task-body { flex: 1; min-width: 0; }

  .task-text {
    font-size: 15px;
    color: var(--text);
    line-height: 1.4;
    word-break: break-word;
    transition: color 0.2s;
  }

  .task-meta {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-top: 5px;
    flex-wrap: wrap;
  }

  .task-badge {
    font-size: 11px;
    font-family: 'DM Mono', monospace;
    padding: 2px 8px;
    border-radius: 99px;
  }

  .badge-work     { background: rgba(79,172,254,0.12);  color: #4facfe; }
  .badge-personal { background: rgba(192,132,252,0.12); color: #c084fc; }
  .badge-urgent   { background: rgba(251,113,133,0.12); color: #fb7185; }
  .badge-health   { background: rgba(52,211,153,0.12);  color: #34d399; }
  .badge-learning { background: rgba(251,191,36,0.12);  color: #fbbf24; }
  .badge-other    { background: rgba(156,163,175,0.1);  color: #9ca3af; }

  .priority-pip {
    font-size: 10px;
    padding: 2px 7px;
    border-radius: 99px;
    font-family: 'DM Mono', monospace;
  }

  .pip-low  { background: rgba(52,211,153,0.1);  color: #34d399; }
  .pip-med  { background: rgba(251,191,36,0.1);   color: #fbbf24; }
  .pip-high { background: rgba(251,113,133,0.1);  color: #fb7185; }

  .task-due { font-size: 11px; color: var(--text3); font-family: 'DM Mono', monospace; }
  .task-due.overdue-label { color: #fb7185; }

  .task-actions {
    display: flex;
    align-items: center;
    gap: 4px;
    opacity: 0;
    transition: opacity 0.15s;
  }

  .task-item:hover .task-actions { opacity: 1; }

  .action-btn {
    background: none;
    border: none;
    cursor: pointer;
    padding: 4px;
    border-radius: 6px;
    color: var(--text3);
    font-size: 14px;
    line-height: 1;
    transition: all 0.15s;
    display: flex; align-items: center; justify-content: center;
  }

  .action-btn:hover.del  { color: #fb7185; background: rgba(251,113,133,0.1); }
  .action-btn:hover.edit { color: #4facfe; background: rgba(79,172,254,0.1); }

  .empty-state {
    text-align: center;
    padding: 64px 24px;
    color: var(--text3);
  }

  .empty-state .big { font-size: 48px; margin-bottom: 12px; }
  .empty-state p { font-size: 14px; line-height: 1.6; }

  .breakdown-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 22px 24px;
    margin-top: 24px;
    animation: fadeUp 0.4s 0.25s ease both;
  }

  .breakdown-title {
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 14px;
    color: var(--text);
    margin-bottom: 18px;
  }

  .breakdown-rows { display: flex; flex-direction: column; gap: 12px; }

  .breakdown-row { display: flex; align-items: center; gap: 12px; }

  .bd-label { font-size: 13px; color: var(--text2); width: 80px; flex-shrink: 0; }

  .bd-track {
    flex: 1;
    height: 6px;
    background: var(--bg4);
    border-radius: 99px;
    overflow: hidden;
  }

  .bd-fill { height: 100%; border-radius: 99px; transition: width 0.5s ease; }

  .bd-count {
    font-size: 12px;
    font-family: 'DM Mono', monospace;
    color: var(--text3);
    width: 24px;
    text-align: right;
    flex-shrink: 0;
  }

  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(12px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  .modal-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.7);
    backdrop-filter: blur(6px);
    -webkit-backdrop-filter: blur(6px);
    z-index: 200;
    display: flex;
    align-items: center;
    justify-content: center;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.2s;
  }

  .modal-overlay.open { opacity: 1; pointer-events: all; }

  .modal {
    background: var(--bg2);
    border: 1px solid var(--border2);
    border-radius: var(--radius);
    padding: 28px;
    width: 480px;
    max-width: 90vw;
    transform: translateY(12px);
    transition: transform 0.2s;
  }

  .modal-overlay.open .modal { transform: translateY(0); }

  .modal-title {
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 18px;
    color: var(--accent);
    margin-bottom: 20px;
  }

  .modal-field { margin-bottom: 14px; }

  .modal-label {
    font-size: 12px;
    color: var(--text2);
    margin-bottom: 6px;
    display: block;
    font-family: 'DM Mono', monospace;
    letter-spacing: 0.5px;
    text-transform: uppercase;
  }

  .modal-input, .modal-select {
    width: 100%;
    background: var(--bg3);
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 10px 14px;
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    font-size: 15px;
    outline: none;
    transition: border-color 0.15s;
  }

  .modal-input:focus, .modal-select:focus { border-color: var(--border2); }

  .modal-actions {
    display: flex;
    gap: 10px;
    margin-top: 22px;
    justify-content: flex-end;
  }

  .modal-cancel {
    background: none;
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 9px 18px;
    color: var(--text2);
    font-family: 'DM Sans', sans-serif;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.15s;
  }

  .modal-cancel:hover { border-color: var(--border2); color: var(--text); }

  .modal-save {
    background: var(--accent);
    color: var(--bg);
    border: none;
    border-radius: var(--radius-sm);
    padding: 9px 20px;
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 14px;
    cursor: pointer;
    transition: opacity 0.15s;
  }

  .modal-save:hover { opacity: 0.85; }

  .hamburger {
    display: none;
    position: fixed;
    top: 14px; left: 14px;
    z-index: 300;
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 6px 10px;
    cursor: pointer;
    font-size: 18px;
    color: var(--text);
  }

  @media (max-width: 768px) {
    .sidebar { transform: translateX(-100%); transition: transform 0.3s ease; }
    .sidebar.open { transform: translateX(0); }
    .main { margin-left: 0; }
    .hamburger { display: flex; align-items: center; }
    .topbar { padding: 0 16px 0 56px; }
    .content { padding: 24px 16px 60px; }
    .page-title { font-size: 26px; }
    .topbar-date { display: none; }
  }
</style>
</head>
<body>

<button class="hamburger" onclick="toggleSidebar()" aria-label="Menu">☰</button>

<aside class="sidebar" id="sidebar">
  <div class="sidebar-logo">
    <div class="logo-mark">
      <div class="logo-dot"></div>
      TaskBoard
    </div>
    <div class="logo-sub">Personal Command Center</div>
  </div>
  <nav class="sidebar-nav">
    <div class="nav-section-label">Views</div>
    <button class="nav-btn active" onclick="setView('all', this)">
      <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="3" width="7" height="7"/><rect x="14" y="3" width="7" height="7"/><rect x="14" y="14" width="7" height="7"/><rect x="3" y="14" width="7" height="7"/></svg>
      All Tasks
      <span class="nav-count" id="cnt-all">0</span>
    </button>
    <button class="nav-btn" onclick="setView('active', this)">
      <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/></svg>
      Active
      <span class="nav-count" id="cnt-active">0</span>
    </button>
    <button class="nav-btn" onclick="setView('done', this)">
      <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"/></svg>
      Completed
      <span class="nav-count" id="cnt-done">0</span>
    </button>
    <button class="nav-btn" onclick="setView('overdue', this)">
      <svg class="nav-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg>
      Overdue
      <span class="nav-count" id="cnt-overdue">0</span>
    </button>
    <div class="nav-section-label">Categories</div>
    <button class="nav-btn" onclick="setView('work', this)">
      <div class="cat-dot" style="background: var(--c-work)"></div>
      Work <span class="nav-count" id="cnt-work">0</span>
    </button>
    <button class="nav-btn" onclick="setView('personal', this)">
      <div class="cat-dot" style="background: var(--c-personal)"></div>
      Personal <span class="nav-count" id="cnt-personal">0</span>
    </button>
    <button class="nav-btn" onclick="setView('urgent', this)">
      <div class="cat-dot" style="background: var(--c-urgent)"></div>
      Urgent <span class="nav-count" id="cnt-urgent">0</span>
    </button>
    <button class="nav-btn" onclick="setView('health', this)">
      <div class="cat-dot" style="background: var(--c-health)"></div>
      Health <span class="nav-count" id="cnt-health">0</span>
    </button>
    <button class="nav-btn" onclick="setView('learning', this)">
      <div class="cat-dot" style="background: var(--c-learning)"></div>
      Learning <span class="nav-count" id="cnt-learning">0</span>
    </button>
    <button class="nav-btn" onclick="setView('other', this)">
      <div class="cat-dot" style="background: var(--c-other)"></div>
      Other <span class="nav-count" id="cnt-other">0</span>
    </button>
  </nav>
  <div class="sidebar-footer" id="sidebar-footer">● 0 tasks total</div>
</aside>

<main class="main">
  <header class="topbar">
    <div class="search-wrap">
      <svg class="search-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>
      <input type="text" class="search-input" id="search-input" placeholder="Search tasks..." oninput="render()">
    </div>
    <div class="topbar-right">
      <span class="topbar-date" id="topbar-date"></span>
      <button class="clear-done-btn" onclick="clearDone()">Clear done</button>
    </div>
  </header>

  <div class="content">
    <div class="page-header">
      <h1 class="page-title" id="page-title">All Tasks</h1>
      <p class="page-sub" id="page-sub">Everything on your plate</p>
    </div>

    <div class="progress-card">
      <div class="progress-header">
        <span class="progress-title">Today's Progress</span>
        <span class="progress-pct" id="pct-num">0%</span>
      </div>
      <div class="bar-track">
        <div class="bar-fill" id="bar-fill" style="width: 0%"></div>
      </div>
      <div class="bar-milestones">
        <span class="milestone">0%</span>
        <span class="milestone">25%</span>
        <span class="milestone">50%</span>
        <span class="milestone">75%</span>
        <span class="milestone">100%</span>
      </div>
    </div>

    <div class="stats-grid">
      <div class="stat-card s-total"><div class="stat-num" id="st-total">0</div><div class="stat-label">Total Tasks</div></div>
      <div class="stat-card s-done"><div class="stat-num" id="st-done">0</div><div class="stat-label">Completed</div></div>
      <div class="stat-card s-left"><div class="stat-num" id="st-left">0</div><div class="stat-label">Remaining</div></div>
      <div class="stat-card s-urgent"><div class="stat-num" id="st-urgent">0</div><div class="stat-label">Urgent</div></div>
      <div class="stat-card s-streak"><div class="stat-num" id="st-overdue">0</div><div class="stat-label">Overdue</div></div>
    </div>

    <div class="add-card">
      <div class="add-row1">
        <input type="text" class="add-input" id="add-input" placeholder="What needs to be done?" maxlength="150">
        <button class="add-btn" onclick="addTask()">+ Add Task</button>
      </div>
      <div class="add-row2">
        <label>Category
          <select id="add-cat">
            <option value="work">💼 Work</option>
            <option value="personal">💜 Personal</option>
            <option value="urgent">🔥 Urgent</option>
            <option value="health">🌿 Health</option>
            <option value="learning">📚 Learning</option>
            <option value="other">· Other</option>
          </select>
        </label>
        <label>Due date <input type="date" id="add-due"></label>
        <label>Priority</label>
        <div class="priority-wrap" id="priority-wrap">
          <button class="priority-btn sel-low" onclick="setPriority('low', this)">Low</button>
          <button class="priority-btn" onclick="setPriority('med', this)">Med</button>
          <button class="priority-btn" onclick="setPriority('high', this)">High</button>
        </div>
      </div>
    </div>

    <div class="tasks-section">
      <div class="section-header">
        <span class="section-title" id="section-label">Tasks</span>
        <select class="sort-select" id="sort-select" onchange="render()">
          <option value="newest">Newest first</option>
          <option value="oldest">Oldest first</option>
          <option value="priority">By priority</option>
          <option value="due">By due date</option>
          <option value="alpha">A → Z</option>
        </select>
      </div>
      <div class="tasks-list" id="tasks-list"></div>
      <div class="empty-state" id="empty-state" style="display:none">
        <div class="big">✦</div>
        <p>Nothing here.<br>Add a task above to get started.</p>
      </div>
    </div>

    <div class="breakdown-card">
      <div class="breakdown-title">Category Breakdown</div>
      <div class="breakdown-rows" id="breakdown-rows"></div>
    </div>
  </div>
</main>

<div class="modal-overlay" id="modal">
  <div class="modal">
    <div class="modal-title">Edit Task</div>
    <div class="modal-field">
      <label class="modal-label">Task</label>
      <input type="text" class="modal-input" id="edit-text" maxlength="150">
    </div>
    <div class="modal-field">
      <label class="modal-label">Category</label>
      <select class="modal-select" id="edit-cat">
        <option value="work">💼 Work</option>
        <option value="personal">💜 Personal</option>
        <option value="urgent">🔥 Urgent</option>
        <option value="health">🌿 Health</option>
        <option value="learning">📚 Learning</option>
        <option value="other">· Other</option>
      </select>
    </div>
    <div class="modal-field">
      <label class="modal-label">Priority</label>
      <select class="modal-select" id="edit-priority">
        <option value="low">Low</option>
        <option value="med">Medium</option>
        <option value="high">High</option>
      </select>
    </div>
    <div class="modal-field">
      <label class="modal-label">Due Date</label>
      <input type="date" class="modal-input" id="edit-due">
    </div>
    <div class="modal-actions">
      <button class="modal-cancel" onclick="closeModal()">Cancel</button>
      <button class="modal-save" onclick="saveEdit()">Save Changes</button>
    </div>
  </div>
</div>

<script>
  let tasks = JSON.parse(localStorage.getItem('tb2_tasks') || '[]');
  let view = 'all';
  let selectedPriority = 'low';
  let editId = null;

  const CAT_COLORS = {
    work: '#4facfe', personal: '#c084fc', urgent: '#fb7185',
    health: '#34d399', learning: '#fbbf24', other: '#9ca3af'
  };

  const CAT_LABELS = {
    work: 'Work', personal: 'Personal', urgent: 'Urgent',
    health: 'Health', learning: 'Learning', other: 'Other'
  };

  const VIEW_META = {
    all:      { title: 'All Tasks',    sub: 'Everything on your plate' },
    active:   { title: 'Active Tasks', sub: 'Things still in progress' },
    done:     { title: 'Completed',    sub: "Tasks you've finished" },
    overdue:  { title: 'Overdue',      sub: 'Tasks past their due date' },
    work:     { title: 'Work',         sub: 'Professional tasks and projects' },
    personal: { title: 'Personal',     sub: 'Life outside of work' },
    urgent:   { title: 'Urgent',       sub: 'Needs immediate attention' },
    health:   { title: 'Health',       sub: 'Fitness, wellness, and self-care' },
    learning: { title: 'Learning',     sub: 'Study and skill-building' },
    other:    { title: 'Other',        sub: 'Everything else' },
  };

  function save() { localStorage.setItem('tb2_tasks', JSON.stringify(tasks)); }
  function uid()  { return Date.now().toString(36) + Math.random().toString(36).slice(2, 6); }

  function isOverdue(task) {
    if (!task.due || task.done) return false;
    return new Date(task.due + 'T23:59:59') < new Date();
  }

  function getFiltered() {
    const q = document.getElementById('search-input').value.trim().toLowerCase();
    let result = tasks.filter(t => {
      if (q && !t.text.toLowerCase().includes(q)) return false;
      if (view === 'active')  return !t.done;
      if (view === 'done')    return t.done;
      if (view === 'overdue') return isOverdue(t);
      if (['work','personal','urgent','health','learning','other'].includes(view)) return t.cat === view;
      return true;
    });
    const sort = document.getElementById('sort-select').value;
    if (sort === 'oldest')   result.sort((a,b) => a.ts - b.ts);
    else if (sort === 'newest')   result.sort((a,b) => b.ts - a.ts);
    else if (sort === 'priority') { const p={high:0,med:1,low:2}; result.sort((a,b) => (p[a.priority]||2)-(p[b.priority]||2)); }
    else if (sort === 'due') result.sort((a,b) => { if (!a.due&&!b.due) return 0; if (!a.due) return 1; if (!b.due) return -1; return a.due.localeCompare(b.due); });
    else if (sort === 'alpha') result.sort((a,b) => a.text.localeCompare(b.text));
    return result;
  }

  function updateCounts() {
    document.getElementById('cnt-all').textContent      = tasks.length;
    document.getElementById('cnt-active').textContent   = tasks.filter(t => !t.done).length;
    document.getElementById('cnt-done').textContent     = tasks.filter(t => t.done).length;
    document.getElementById('cnt-overdue').textContent  = tasks.filter(isOverdue).length;
    document.getElementById('cnt-work').textContent     = tasks.filter(t => t.cat==='work').length;
    document.getElementById('cnt-personal').textContent = tasks.filter(t => t.cat==='personal').length;
    document.getElementById('cnt-urgent').textContent   = tasks.filter(t => t.cat==='urgent').length;
    document.getElementById('cnt-health').textContent   = tasks.filter(t => t.cat==='health').length;
    document.getElementById('cnt-learning').textContent = tasks.filter(t => t.cat==='learning').length;
    document.getElementById('cnt-other').textContent    = tasks.filter(t => t.cat==='other').length;
    document.getElementById('sidebar-footer').textContent = '● ' + tasks.length + ' task' + (tasks.length===1?'':'s') + ' total';
  }

  function updateStats() {
    const total = tasks.length, done = tasks.filter(t => t.done).length;
    const pct = total === 0 ? 0 : Math.round((done/total)*100);
    document.getElementById('bar-fill').style.width = pct + '%';
    document.getElementById('pct-num').textContent  = pct + '%';
    document.getElementById('st-total').textContent  = total;
    document.getElementById('st-done').textContent   = done;
    document.getElementById('st-left').textContent   = total - done;
    document.getElementById('st-urgent').textContent = tasks.filter(t => t.cat==='urgent'&&!t.done).length;
    document.getElementById('st-overdue').textContent= tasks.filter(isOverdue).length;
  }

  function updateBreakdown() {
    const cats = ['work','personal','urgent','health','learning','other'];
    const total = tasks.length || 1;
    document.getElementById('breakdown-rows').innerHTML = cats.map(c => {
      const count = tasks.filter(t => t.cat===c).length;
      const pct = Math.round((count/total)*100);
      return `<div class="breakdown-row">
        <span class="bd-label">${CAT_LABELS[c]}</span>
        <div class="bd-track"><div class="bd-fill" style="width:${pct}%;background:${CAT_COLORS[c]}"></div></div>
        <span class="bd-count">${count}</span>
      </div>`;
    }).join('');
  }

  function escHtml(s) {
    return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
  }

  function formatDue(due) {
    if (!due) return '';
    const d = new Date(due + 'T12:00:00');
    const today = new Date(); today.setHours(0,0,0,0);
    const diff = Math.round((d - today) / 86400000);
    if (diff === 0)  return 'Due today';
    if (diff === 1)  return 'Due tomorrow';
    if (diff === -1) return 'Due yesterday';
    if (diff < 0)    return Math.abs(diff) + 'd overdue';
    return d.toLocaleDateString('en-US', { month:'short', day:'numeric' });
  }

  function render() {
    updateStats(); updateCounts(); updateBreakdown();
    const meta = VIEW_META[view] || VIEW_META.all;
    document.getElementById('page-title').textContent  = meta.title;
    document.getElementById('page-sub').textContent    = meta.sub;
    document.getElementById('section-label').textContent = meta.title;
    const filtered = getFiltered();
    const list  = document.getElementById('tasks-list');
    const empty = document.getElementById('empty-state');
    if (filtered.length === 0) {
      list.innerHTML = ''; empty.style.display = 'block';
    } else {
      empty.style.display = 'none';
      list.innerHTML = filtered.map(t => {
        const over = isOverdue(t);
        const dueStr = formatDue(t.due);
        const dueClass = over && !t.done ? 'overdue-label' : '';
        return `<div class="task-item cat-${t.cat}${t.done?' done':''}${over&&!t.done?' overdue':''}">
          <button class="check-btn${t.done?' checked':''}" onclick="toggle('${t.id}')" aria-label="Toggle done"></button>
          <div class="task-body">
            <div class="task-text">${escHtml(t.text)}</div>
            <div class="task-meta">
              <span class="task-badge badge-${t.cat}">${CAT_LABELS[t.cat]}</span>
              ${t.priority&&t.priority!=='low'?`<span class="priority-pip pip-${t.priority}">${t.priority==='med'?'Medium':'High'}</span>`:''}
              ${t.due?`<span class="task-due ${dueClass}">${dueStr}</span>`:''}
            </div>
          </div>
          <div class="task-actions">
            <button class="action-btn edit" onclick="openEdit('${t.id}')" title="Edit">
              <svg width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/></svg>
            </button>
            <button class="action-btn del" onclick="del('${t.id}')" title="Delete">
              <svg width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"/><path d="M19 6l-1 14a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2L5 6"/><path d="M10 11v6"/><path d="M14 11v6"/><path d="M9 6V4a1 1 0 0 1 1-1h4a1 1 0 0 1 1 1v2"/></svg>
            </button>
          </div>
        </div>`;
      }).join('');
    }
  }

  function addTask() {
    const inp = document.getElementById('add-input');
    const text = inp.value.trim();
    if (!text) { inp.focus(); return; }
    tasks.unshift({
      id: uid(), text,
      cat: document.getElementById('add-cat').value,
      done: false,
      priority: selectedPriority,
      due: document.getElementById('add-due').value || null,
      ts: Date.now()
    });
    save(); render();
    inp.value = ''; inp.focus();
  }

  function toggle(id) {
    const t = tasks.find(t => t.id===id);
    if (t) { t.done = !t.done; save(); render(); }
  }

  function del(id) {
    tasks = tasks.filter(t => t.id!==id);
    save(); render();
  }

  function clearDone() {
    if (!tasks.some(t => t.done)) return;
    if (confirm('Remove all completed tasks?')) { tasks = tasks.filter(t => !t.done); save(); render(); }
  }

  function setView(v, btn) {
    view = v;
    document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    render();
    if (window.innerWidth <= 768) document.getElementById('sidebar').classList.remove('open');
  }

  function setPriority(p, btn) {
    selectedPriority = p;
    document.querySelectorAll('.priority-btn').forEach(b => b.className = 'priority-btn');
    btn.classList.add('sel-' + p);
  }

  function openEdit(id) {
    const t = tasks.find(t => t.id===id);
    if (!t) return;
    editId = id;
    document.getElementById('edit-text').value     = t.text;
    document.getElementById('edit-cat').value      = t.cat;
    document.getElementById('edit-priority').value = t.priority || 'low';
    document.getElementById('edit-due').value      = t.due || '';
    document.getElementById('modal').classList.add('open');
  }

  function closeModal() {
    document.getElementById('modal').classList.remove('open');
    editId = null;
  }

  function saveEdit() {
    const t = tasks.find(t => t.id===editId);
    if (!t) return;
    const text = document.getElementById('edit-text').value.trim();
    if (!text) return;
    t.text     = text;
    t.cat      = document.getElementById('edit-cat').value;
    t.priority = document.getElementById('edit-priority').value;
    t.due      = document.getElementById('edit-due').value || null;
    save(); render(); closeModal();
  }

  function toggleSidebar() {
    document.getElementById('sidebar').classList.toggle('open');
  }

  document.addEventListener('keydown', e => {
    if (e.key === 'Escape') closeModal();
    if (e.key === 'Enter' && document.activeElement === document.getElementById('add-input')) addTask();
    if (e.key === 'n' && !['INPUT','TEXTAREA','SELECT'].includes(document.activeElement.tagName)) {
      document.getElementById('add-input').focus();
    }
  });

  document.getElementById('modal').addEventListener('click', e => {
    if (e.target === document.getElementById('modal')) closeModal();
  });

  document.getElementById('topbar-date').textContent = new Date().toLocaleDateString('en-US', {
    weekday: 'short', month: 'short', day: 'numeric'
  });

  render();
</script>
</body>
</html>
