<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Wiegand · KI Schadensdokumentation</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;0,9..40,600;0,9..40,700;1,9..40,400&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
:root {
  --bg: #0d0f14;
  --bg2: #13161e;
  --bg3: #1a1e2a;
  --bg4: #222636;
  --border: rgba(255,255,255,0.07);
  --border2: rgba(255,255,255,0.12);
  --text: #e8eaf0;
  --text2: #9098b0;
  --text3: #5a6380;
  --accent: #4f7cff;
  --accent2: #3d63e0;
  --accent-glow: rgba(79,124,255,0.2);
  --green: #34d07f;
  --green-bg: rgba(52,208,127,0.1);
  --red: #ff5a5a;
  --red-bg: rgba(255,90,90,0.1);
  --amber: #f5a623;
  --amber-bg: rgba(245,166,35,0.1);
  --purple: #9b6dff;
  --purple-bg: rgba(155,109,255,0.12);
  --r: 12px;
  --rs: 8px;
  --font: 'DM Sans', sans-serif;
  --mono: 'DM Mono', monospace;
  --shadow: 0 2px 8px rgba(0,0,0,0.4);
  --shadow-lg: 0 8px 32px rgba(0,0,0,0.5);
}
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { scroll-behavior: smooth; }
body {
  font-family: var(--font);
  background: var(--bg);
  color: var(--text);
  font-size: 14px;
  line-height: 1.6;
  min-height: 100vh;
}

/* ── Scrollbar ── */
::-webkit-scrollbar { width: 6px; }
::-webkit-scrollbar-track { background: var(--bg); }
::-webkit-scrollbar-thumb { background: var(--bg4); border-radius: 3px; }

/* ── Layout ── */
#loginScreen {
  position: fixed; inset: 0; z-index: 9999;
  background: var(--bg);
  display: flex; flex-direction: column; align-items: center; justify-content: center;
  padding: 24px;
}
.login-glow {
  position: absolute; top: -200px; left: 50%; transform: translateX(-50%);
  width: 600px; height: 600px;
  background: radial-gradient(circle, rgba(79,124,255,0.12) 0%, transparent 70%);
  pointer-events: none;
}
.login-brand { text-align: center; margin-bottom: 48px; position: relative; }
.login-icon {
  width: 64px; height: 64px; border-radius: 18px;
  background: linear-gradient(135deg, var(--accent), #7b5fff);
  display: flex; align-items: center; justify-content: center;
  font-size: 28px; margin: 0 auto 16px;
  box-shadow: 0 0 40px rgba(79,124,255,0.35);
}
.login-brand h1 { font-size: 22px; font-weight: 700; color: var(--text); letter-spacing: -0.3px; }
.login-brand p { font-size: 13px; color: var(--text3); margin-top: 4px; }
.login-label {
  font-size: 11px; font-weight: 600; color: var(--text3);
  text-transform: uppercase; letter-spacing: .1em; margin-bottom: 16px;
  text-align: center;
}
.employee-grid {
  display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px;
  max-width: 640px; width: 100%;
}
.emp-card {
  background: var(--bg2); border: 1px solid var(--border);
  border-radius: var(--r); padding: 16px 12px; text-align: center;
  cursor: pointer; transition: all .2s;
}
.emp-card:hover {
  background: var(--bg3); border-color: var(--accent);
  transform: translateY(-2px); box-shadow: 0 0 20px var(--accent-glow);
}
.emp-avatar { font-size: 1.8rem; display: block; margin-bottom: 8px; }
.emp-name { font-size: 12px; font-weight: 600; color: var(--text); display: block; margin-bottom: 2px; }
.emp-role { font-size: 10px; color: var(--text3); display: block; line-height: 1.4; }

/* ── App Shell ── */
#appScreen { display: none; }
.topbar {
  position: sticky; top: 0; z-index: 100;
  background: rgba(13,15,20,0.92); backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--border);
  min-height: 56px; height: auto; display: flex; align-items: center; padding: 0 16px; gap: 10px; flex-wrap: wrap;
}
.topbar-brand {
  display: flex; align-items: center; gap: 10px;
  font-size: 14px; font-weight: 700; color: var(--text); white-space: nowrap;
}
.topbar-brand .ico {
  width: 32px; height: 32px; border-radius: 9px;
  background: linear-gradient(135deg, var(--accent), #7b5fff);
  display: flex; align-items: center; justify-content: center; font-size: 14px;
}
.topbar-nav { display: flex; gap: 2px; overflow-x: auto; flex-shrink: 1; min-width: 0; scrollbar-width: none; }
.topbar-nav::-webkit-scrollbar { display: none; }
.nav-btn {
  background: transparent; border: none; color: var(--text3);
  font-family: var(--font); font-size: 13px; font-weight: 500;
  padding: 6px 14px; border-radius: 8px; cursor: pointer; transition: all .15s;
}
.nav-btn:hover { background: var(--bg3); color: var(--text); }
.nav-btn.active { background: var(--bg3); color: var(--accent); }
.topbar-spacer { flex: 1; }
.key-wrap {
  display: flex; align-items: center; gap: 8px;
}
.key-label { font-size: 10px; font-weight: 600; color: var(--text3); white-space: nowrap; }
.key-input {
  background: var(--bg3); border: 1px solid var(--border2);
  border-radius: var(--rs); padding: 7px 11px;
  font-family: var(--mono); font-size: 11px; color: var(--text);
  outline: none; width: 200px; transition: border-color .15s;
}
.key-input:focus { border-color: var(--accent); }
.emp-pill {
  display: flex; align-items: center; gap: 7px;
  background: var(--bg3); border: 1px solid var(--border2);
  border-radius: 20px; padding: 5px 12px; cursor: pointer;
  font-size: 12px; font-weight: 500; color: var(--text2); transition: all .15s;
  white-space: nowrap;
}
.emp-pill:hover { border-color: var(--accent); color: var(--text); }

/* ── Views ── */
.view { display: none; max-width: 1060px; margin: 0 auto; padding: 28px 24px; }
.view.active { display: block; }

/* ── Section ── */
.section-title {
  font-size: 11px; font-weight: 600; color: var(--text3);
  text-transform: uppercase; letter-spacing: .1em;
  margin-bottom: 12px; display: flex; align-items: center; gap: 8px;
}
.section-title span { color: var(--accent); }

/* ── Card ── */
.card {
  background: var(--bg2); border: 1px solid var(--border);
  border-radius: var(--r); padding: 20px;
  margin-bottom: 16px; box-shadow: var(--shadow);
}
.card.highlighted { border-color: var(--accent); box-shadow: 0 0 0 1px var(--accent), var(--shadow); }

/* ── Grid ── */
.g2 { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.g3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 12px; }

/* ── Fields ── */
.field label {
  display: block; font-size: 11px; font-weight: 600; color: var(--text3);
  text-transform: uppercase; letter-spacing: .06em; margin-bottom: 5px;
}
.field input, .field select, .field textarea {
  width: 100%;
  background: var(--bg3); border: 1px solid var(--border2);
  border-radius: var(--rs); padding: 9px 12px;
  font-family: var(--font); font-size: 13px; color: var(--text);
  outline: none; transition: border-color .15s;
}
.field input:focus, .field select:focus, .field textarea:focus {
  border-color: var(--accent); box-shadow: 0 0 0 3px var(--accent-glow);
}
.field select option { background: var(--bg3); }
.field textarea { min-height: 72px; resize: vertical; line-height: 1.6; }

/* ── Buttons ── */
.btn {
  display: inline-flex; align-items: center; gap: 7px;
  border: none; border-radius: var(--rs); padding: 9px 16px;
  font-family: var(--font); font-size: 13px; font-weight: 600;
  cursor: pointer; transition: all .15s; white-space: nowrap;
}
.btn-primary { background: var(--accent); color: #fff; }
.btn-primary:hover { background: var(--accent2); box-shadow: 0 0 20px var(--accent-glow); }
.btn-primary:disabled { opacity: .4; cursor: not-allowed; }
.btn-ghost {
  background: var(--bg3); color: var(--text2);
  border: 1px solid var(--border2);
}
.btn-ghost:hover { background: var(--bg4); color: var(--text); border-color: var(--border2); }
.btn-green { background: var(--green); color: #0d1a0f; }
.btn-green:hover { opacity: .88; }
.btn-red { background: var(--red-bg); color: var(--red); border: 1px solid rgba(255,90,90,0.2); }
.btn-full { width: 100%; justify-content: center; padding: 12px; }
.btn-sm { padding: 6px 12px; font-size: 12px; }

/* ── Upload ── */
.upload-zone {
  border: 2px dashed var(--border2); border-radius: var(--r);
  padding: 24px; text-align: center; cursor: pointer;
  transition: all .2s; position: relative; background: var(--bg3);
}
.upload-zone:hover { border-color: var(--accent); background: rgba(79,124,255,0.04); }
.upload-zone input { position: absolute; inset: 0; opacity: 0; cursor: pointer; width: 100%; height: 100%; }
.upload-icon { font-size: 2rem; margin-bottom: 8px; }
.upload-text { font-size: 13px; color: var(--text2); }
.upload-text span { color: var(--accent); font-weight: 600; }
.upload-sub { font-size: 11px; color: var(--text3); margin-top: 4px; }

/* ── Photo grid ── */
.photos-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(100px, 1fr)); gap: 8px; margin-top: 14px; }
.photo-thumb {
  position: relative; border-radius: var(--rs); overflow: hidden;
  aspect-ratio: 1; border: 1px solid var(--border2);
  background: var(--bg3);
}
.photo-thumb img { width: 100%; height: 100%; object-fit: cover; display: block; }
.photo-thumb .del {
  position: absolute; top: 4px; right: 4px;
  background: rgba(255,90,90,0.9); color: #fff;
  border: none; border-radius: 50%; width: 20px; height: 20px;
  font-size: 11px; cursor: pointer; display: flex; align-items: center; justify-content: center;
}
.photo-thumb .main-badge {
  position: absolute; bottom: 4px; left: 4px;
  background: var(--accent); color: #fff; font-size: 9px;
  font-weight: 700; padding: 2px 5px; border-radius: 4px; text-transform: uppercase;
}
.photos-count { font-size: 11px; color: var(--green); font-weight: 500; margin-top: 8px; }

/* ── Video upload ── */
.video-zone {
  border: 2px dashed rgba(155,109,255,0.3); border-radius: var(--r);
  padding: 18px; text-align: center; cursor: pointer;
  transition: all .2s; position: relative; background: rgba(155,109,255,0.04);
  margin-top: 14px;
}
.video-zone:hover { border-color: var(--purple); }
.video-zone input { position: absolute; inset: 0; opacity: 0; cursor: pointer; width: 100%; height: 100%; }
.video-frames-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(90px, 1fr)); gap: 8px; margin-top: 12px; }
.vframe { position: relative; border-radius: var(--rs); overflow: hidden; aspect-ratio: 16/9; border: 1px solid rgba(155,109,255,0.3); }
.vframe img { width: 100%; height: 100%; object-fit: cover; }
.vframe-badge { position: absolute; bottom: 3px; left: 3px; background: var(--purple); color: #fff; font-size: 8px; font-weight: 700; padding: 1px 5px; border-radius: 3px; }
.video-changes { background: var(--bg3); border: 1px solid rgba(155,109,255,0.25); border-radius: var(--r); padding: 16px; margin-top: 12px; }
.video-changes h4 { font-size: 12px; font-weight: 600; color: var(--purple); margin-bottom: 10px; }
.vchange-item { display: flex; gap: 10px; align-items: flex-start; padding: 10px; background: var(--bg2); border-radius: var(--rs); margin-bottom: 8px; border: 1px solid var(--border); }
.vchange-item img { width: 70px; height: 46px; object-fit: cover; border-radius: 6px; flex-shrink: 0; border: 1px solid rgba(155,109,255,0.3); }
.vchange-time { font-size: 10px; font-weight: 600; color: var(--purple); margin-bottom: 3px; }
.vchange-text { font-size: 12px; color: var(--text2); line-height: 1.5; }

/* ── Wunschbad Board ── */
.board-chips { display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 12px; }
.chip {
  padding: 5px 13px; border-radius: 20px;
  border: 1px solid var(--border2); background: var(--bg3);
  font-size: 11px; font-weight: 500; color: var(--text2);
  cursor: pointer; transition: all .15s;
}
.chip:hover { border-color: var(--accent); color: var(--accent); }
.chip.active { background: var(--accent-glow); border-color: var(--accent); color: var(--accent); }
.board-slots { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-bottom: 12px; }
.board-slot {
  border: 2px dashed var(--border2); border-radius: var(--rs);
  aspect-ratio: 1; display: flex; flex-direction: column;
  align-items: center; justify-content: center; cursor: pointer;
  transition: all .15s; background: var(--bg3); position: relative; overflow: hidden;
}
.board-slot:hover { border-color: var(--accent); background: var(--accent-glow); }
.board-slot.has-img { border-style: solid; border-color: var(--accent); }
.board-slot .slot-icon { font-size: 1.3rem; }
.board-slot .slot-lbl { font-size: 9px; font-weight: 600; color: var(--text3); margin-top: 4px; text-align: center; }
.board-slot img { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: cover; }
.board-slot .slot-del {
  position: absolute; top: 3px; right: 3px; z-index: 1;
  background: var(--red); color: #fff; border: none;
  border-radius: 50%; width: 18px; height: 18px; font-size: 10px;
  cursor: pointer; display: flex; align-items: center; justify-content: center;
}

/* ── Audio rec ── */
.rec-card { display: flex; align-items: flex-start; gap: 18px; }
.rec-area { text-align: center; flex-shrink: 0; }
.rec-btn {
  width: 60px; height: 60px; border-radius: 50%;
  border: 2px solid var(--accent); background: var(--accent-glow);
  color: var(--accent); font-size: 1.5rem;
  cursor: pointer; transition: all .2s;
  display: flex; align-items: center; justify-content: center;
  margin: 0 auto 6px;
}
.rec-btn:hover { background: var(--accent); color: #fff; }
.rec-btn.recording { border-color: var(--red); background: var(--red-bg); color: var(--red); animation: pulse .9s infinite; }
@keyframes pulse { 0%,100% { transform: scale(1); } 50% { transform: scale(1.07); } }
.rec-status { font-size: 10px; font-weight: 600; color: var(--text3); }
.rec-status.active { color: var(--red); }
.rec-timer { font-size: 16px; font-weight: 700; color: var(--text2); margin-top: 3px; font-family: var(--mono); display: none; }
.rec-info h3 { font-size: 13px; font-weight: 600; color: var(--text); margin-bottom: 5px; }
.rec-info p { font-size: 12px; color: var(--text3); line-height: 1.7; }
.transcript-box {
  display: none; background: rgba(79,124,255,0.08); border: 1px solid rgba(79,124,255,0.2);
  border-radius: var(--rs); padding: 10px 12px; margin-top: 10px;
  font-size: 12px; color: var(--text2); line-height: 1.7;
}
.transcript-box.on { display: block; }
.transcript-lbl { font-size: 10px; font-weight: 700; color: var(--accent); margin-bottom: 3px; text-transform: uppercase; letter-spacing: .06em; }
.progress-bar { height: 2px; background: var(--bg4); border-radius: 1px; margin-top: 10px; overflow: hidden; }
.progress-fill { height: 100%; background: var(--accent); border-radius: 1px; transition: width .3s; width: 0%; }
.audio-playback { margin-top: 10px; display: none; }
.audio-playback audio { width: 100%; border-radius: var(--rs); }

/* ── Modes ── */
.modes-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-bottom: 16px; }
.mode-chip {
  border: 1px solid var(--border2); border-radius: var(--rs);
  padding: 12px 8px; text-align: center; cursor: pointer;
  transition: all .15s; background: var(--bg3);
  font-size: 11px; font-weight: 600; color: var(--text3);
}
.mode-chip:hover { border-color: var(--accent); color: var(--accent); }
.mode-chip.on { border-color: var(--accent); background: var(--accent-glow); color: var(--accent); }
.mode-chip .mi { font-size: 1.3rem; display: block; margin-bottom: 4px; }

/* ── Loading ── */
.loading { display: none; text-align: center; padding: 36px 20px; }
.loading.on { display: block; }
.spinner {
  width: 36px; height: 36px;
  border: 2.5px solid var(--bg4); border-top-color: var(--accent);
  border-radius: 50%; animation: spin .75s linear infinite;
  margin: 0 auto 14px;
}
@keyframes spin { to { transform: rotate(360deg); } }
.loading p { color: var(--text3); font-size: 13px; }
.loading p span { color: var(--accent); font-weight: 600; }
.loading-steps { margin-top: 12px; }
.step { font-size: 12px; color: var(--text3); padding: 3px 0; }
.step.active { color: var(--accent); font-weight: 600; }
.step.done { color: var(--green); }

/* ── Result ── */
.result-box { display: none; }
.result-box.on { display: block; }
.result-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 18px; padding-bottom: 14px; border-bottom: 1px solid var(--border); }
.result-title { font-size: 14px; font-weight: 700; color: var(--green); display: flex; align-items: center; gap: 7px; }
.result-actions { display: flex; gap: 8px; }
.result-content { font-size: 13px; line-height: 1.9; color: var(--text2); }
.result-content h3 { font-size: 10px; font-weight: 700; color: var(--accent); text-transform: uppercase; letter-spacing: .08em; margin: 20px 0 6px; padding-bottom: 4px; border-bottom: 1px solid var(--border); }
.result-content h3:first-child { margin-top: 0; }
.result-content strong { color: var(--text); font-weight: 600; }

/* ── Viz section ── */
.viz-wrap { background: var(--bg3); border: 1px solid var(--border); border-radius: var(--r); padding: 18px; margin-top: 16px; }
.viz-header { display: flex; align-items: flex-start; justify-content: space-between; margin-bottom: 16px; gap: 12px; }
.viz-header-text h3 { font-size: 13px; font-weight: 700; color: var(--text); margin-bottom: 3px; }
.viz-header-text p { font-size: 11px; color: var(--text3); }
.viz-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 12px; margin-top: 12px; }
.viz-item img { width: 100%; border-radius: var(--rs); border: 1px solid var(--border2); display: block; }
.viz-item p { font-size: 10px; color: var(--text3); margin-top: 5px; text-transform: uppercase; letter-spacing: .05em; }
.viz-steps { margin-top: 10px; }
.viz-step { font-size: 12px; padding: 3px 0; color: var(--text3); }
.viz-step.active { color: var(--accent); font-weight: 600; }
.viz-step.done { color: var(--green); }

.reno-wishes { background: var(--bg2); border: 1px solid var(--border2); border-radius: var(--rs); padding: 12px 14px; margin-bottom: 14px; }
.reno-wishes-label { font-size: 11px; font-weight: 600; color: var(--text3); text-transform: uppercase; letter-spacing: .06em; margin-bottom: 8px; }
.reno-row { display: flex; gap: 8px; align-items: flex-start; }
.reno-row textarea { flex: 1; background: var(--bg3); border: 1px solid var(--border2); border-radius: var(--rs); padding: 8px 10px; font-size: 12px; font-family: var(--font); color: var(--text); outline: none; resize: vertical; min-height: 60px; }
.reno-row textarea:focus { border-color: var(--accent); }
.reno-row textarea::placeholder { color: var(--text3); }
.mini-rec-btn { width: 36px; height: 36px; border-radius: 50%; border: 1px solid var(--border2); background: var(--bg3); color: var(--text3); font-size: 1rem; cursor: pointer; flex-shrink: 0; display: flex; align-items: center; justify-content: center; transition: all .15s; }
.mini-rec-btn:hover { border-color: var(--accent); color: var(--accent); }
.mini-rec-btn.recording { border-color: var(--red); background: var(--red-bg); color: var(--red); animation: pulse .9s infinite; }

/* ── Wizard ── */
.wizard-card { border: 1px solid var(--accent) !important; }
.wizard-progress { display: flex; align-items: center; gap: 10px; margin-bottom: 14px; font-size: 12px; font-weight: 600; color: var(--accent); }
.wizard-dots { display: flex; gap: 5px; }
.wdot { width: 8px; height: 8px; border-radius: 50%; background: var(--bg4); transition: all .3s; }
.wdot.done { background: var(--green); }
.wdot.active { background: var(--accent); transform: scale(1.3); }
.wizard-question { font-size: 13px; color: var(--text2); background: rgba(79,124,255,0.06); border: 1px solid rgba(79,124,255,0.15); border-left: 3px solid var(--accent); border-radius: var(--rs); padding: 12px 14px; margin: 14px 0 10px; line-height: 1.8; }
.wizard-desc { font-size: 12px; color: var(--text2); background: var(--green-bg); border: 1px solid rgba(52,208,127,0.2); border-radius: var(--rs); padding: 10px 12px; margin: 10px 0; line-height: 1.7; }
.wizard-feedback { display: flex; gap: 8px; align-items: flex-start; }
.wizard-feedback textarea { flex: 1; min-height: 54px; background: var(--bg3); border: 1px solid var(--border2); border-radius: var(--rs); padding: 8px 11px; font-size: 13px; font-family: var(--font); color: var(--text); outline: none; resize: vertical; }
.wizard-feedback textarea:focus { border-color: var(--accent); }
.wizard-actions { display: flex; gap: 8px; flex-wrap: wrap; margin-top: 12px; }
.wizard-summary-title { font-size: 14px; font-weight: 700; color: var(--green); margin-bottom: 14px; display: flex; align-items: center; gap: 7px; }

/* ── Invoice ── */
.invoice-wrap { background: var(--green-bg); border: 1px solid rgba(52,208,127,0.2); border-radius: var(--r); padding: 18px; margin-top: 16px; }
.invoice-wrap h3 { font-size: 13px; font-weight: 700; color: var(--green); margin-bottom: 4px; }
.invoice-wrap > p { font-size: 12px; color: var(--text3); margin-bottom: 14px; }
.invoice-preview { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--rs); padding: 14px; margin-bottom: 12px; display: none; font-size: 12px; line-height: 1.8; color: var(--text2); }
.invoice-preview.on { display: block; }
.invoice-preview h4 { font-size: 13px; font-weight: 700; color: var(--text); margin-bottom: 8px; }

/* ── Error / Toast ── */
.err-bar { display: none; background: var(--red-bg); border: 1px solid rgba(255,90,90,0.25); color: var(--red); padding: 9px 14px; border-radius: var(--rs); font-size: 12px; margin-bottom: 14px; }
.err-bar.on { display: block; }
#toast {
  position: fixed; bottom: 28px; left: 50%; transform: translateX(-50%);
  background: var(--bg4); color: var(--text); font-size: 12px; font-weight: 500;
  padding: 10px 22px; border-radius: 22px; z-index: 9999;
  opacity: 0; transition: opacity .25s; pointer-events: none;
  box-shadow: var(--shadow-lg); white-space: nowrap; border: 1px solid var(--border2);
}
#toast.on { opacity: 1; }

/* ── Dashboard ── */
.dash-stats { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; margin-bottom: 20px; }
.stat-card { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--r); padding: 16px; }
.stat-value { font-size: 28px; font-weight: 700; color: var(--text); line-height: 1; }
.stat-label { font-size: 11px; color: var(--text3); margin-top: 5px; }
.stat-delta { font-size: 11px; color: var(--green); margin-top: 4px; }
.projects-list { display: flex; flex-direction: column; gap: 10px; }
.project-row {
  background: var(--bg2); border: 1px solid var(--border);
  border-radius: var(--r); padding: 14px 16px;
  display: flex; align-items: center; gap: 14px;
  transition: border-color .15s; cursor: pointer;
}
.project-row:hover { border-color: var(--accent); }
.project-thumb { width: 52px; height: 52px; border-radius: var(--rs); background: var(--bg3); overflow: hidden; flex-shrink: 0; }
.project-thumb img { width: 100%; height: 100%; object-fit: cover; }
.project-info { flex: 1; min-width: 0; }
.project-name { font-size: 13px; font-weight: 600; color: var(--text); }
.project-meta { font-size: 11px; color: var(--text3); margin-top: 2px; }
.project-status { font-size: 10px; font-weight: 600; padding: 3px 9px; border-radius: 10px; }
.status-done { background: var(--green-bg); color: var(--green); }
.status-new { background: var(--amber-bg); color: var(--amber); }
.empty-state { text-align: center; padding: 60px 20px; color: var(--text3); }
.empty-state .emo { font-size: 3rem; margin-bottom: 14px; }
.empty-state p { font-size: 13px; }

/* ── Style chips for board ── */
.board-active-badge { display: none; font-size: 11px; color: var(--accent); font-weight: 600; padding: 6px 10px; background: var(--accent-glow); border-radius: var(--rs); margin-top: 8px; }

/* ── 3D Overlay ── */
#room3dOverlay {
  display: none; position: fixed; inset: 0; z-index: 9999;
  background: #0a0806; flex-direction: column; font-family: var(--font);
}
.r3d-topbar {
  height: 52px; background: rgba(10,8,6,0.96); border-bottom: 1px solid rgba(255,255,255,0.07);
  display: flex; align-items: center; padding: 0 20px; gap: 12px; flex-shrink: 0;
}
.r3d-title { font-size: 13px; font-weight: 700; color: #f0ece2; display: flex; align-items: center; gap: 8px; }
.r3d-dot { width: 7px; height: 7px; border-radius: 50%; background: var(--purple); box-shadow: 0 0 8px #a78bfa; }
.r3d-mode-toggle { display: flex; background: rgba(255,255,255,0.06); border-radius: 20px; padding: 3px; gap: 2px; }
.r3d-mode-btn {
  border: none; border-radius: 17px; padding: 5px 14px; cursor: pointer;
  font-family: var(--font); font-size: 11px; font-weight: 600;
  background: transparent; color: rgba(255,255,255,0.4); transition: all .18s;
}
.r3d-mode-btn.active { background: rgba(255,255,255,0.13); color: #fff; }
#room3dCanvas { width: 100%; height: 100%; display: block; outline: none; }
#r3dXhair { display: none; position: absolute; top: 50%; left: 50%; transform: translate(-50%,-50%); pointer-events: none; }
#r3dXhair::before { content: ''; position: absolute; width: 20px; height: 1.5px; top: 0; left: -10px; background: rgba(255,255,255,0.75); border-radius: 1px; }
#r3dXhair::after { content: ''; position: absolute; width: 1.5px; height: 20px; left: 0; top: -10px; background: rgba(255,255,255,0.75); border-radius: 1px; }
.r3d-hint {
  position: absolute; bottom: 20px; left: 50%; transform: translateX(-50%);
  background: rgba(0,0,0,0.45); backdrop-filter: blur(12px);
  border: 1px solid rgba(255,255,255,0.1); border-radius: 24px;
  padding: 8px 20px; display: flex; gap: 18px; align-items: center;
  font-size: 11px; color: rgba(255,255,255,0.4); white-space: nowrap; pointer-events: none;
}
.r3d-kbd { background: rgba(255,255,255,0.1); padding: 2px 7px; border-radius: 4px; font-size: 10px; color: rgba(255,255,255,0.65); font-family: var(--mono); }
#r3dBadge { position: absolute; top: 12px; right: 16px; background: rgba(124,58,237,.2); border: 1px solid rgba(124,58,237,.4); border-radius: 20px; padding: 5px 13px; font-size: 11px; font-weight: 600; color: #c4b5fd; display: none; pointer-events: none; }
#r3dLoading { position: absolute; top: 52px; left: 0; right: 0; bottom: 0; background: rgba(10,8,6,0.97); display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 16px; }
.r3d-spinner { width: 40px; height: 40px; border: 2.5px solid rgba(124,58,237,0.15); border-top-color: #7c3aed; border-radius: 50%; animation: spin .85s linear infinite; }
#r3dStatusTxt { font-size: 13px; font-weight: 600; color: #e0dcd4; margin-bottom: 10px; }
#r3dStatusBar-wrap { width: 260px; height: 3px; background: rgba(255,255,255,0.07); border-radius: 2px; overflow: hidden; }
#r3dStatusBar { height: 100%; background: var(--purple); border-radius: 2px; width: 0%; transition: width .4s ease; }
.r3d-steps { display: flex; flex-direction: column; gap: 4px; margin-top: 12px; }
.r3d-step { font-size: 11px; color: rgba(255,255,255,.22); display: flex; align-items: center; gap: 6px; }
.r3d-step-active { color: #c4b5fd; font-weight: 600; }
.r3d-step-done { color: #6ee7b7; }

/* ── Mobile keys panel ── */
.mobile-keys {
  display: none;
  background: var(--bg2); border-bottom: 1px solid var(--border);
  padding: 10px 16px; flex-wrap: wrap; gap: 8px; align-items: center;
}
.mobile-keys .key-label { font-size: 10px; font-weight: 600; color: var(--text3); }
.mobile-keys .key-input { width: 100%; max-width: 200px; }
/* ── Responsive ── */
@media(max-width: 768px) {
  .mobile-keys { display: flex; }
  .g2, .g3, .modes-grid, .employee-grid, .dash-stats { grid-template-columns: 1fr; }
  .topbar { flex-wrap: wrap; height: auto; padding: 10px 16px; gap: 8px; }
  .key-wrap { display: none; }
  .board-slots { grid-template-columns: repeat(2, 1fr); }
}

/* ── Divider ── */
.divider { height: 1px; background: var(--border); margin: 16px 0; }
.section-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 14px; }
</style>
</head>
<body>

<!-- ═══════════════════════════════ LOGIN ═══════════════════════════════ -->
<div id="loginScreen">
  <div class="login-glow"></div>
  <div class="login-brand">
    <div class="login-icon">🏠</div>
    <h1>Gebäudetrocknung Wiegand</h1>
    <p>Eschwege · Meisterbetrieb · 30+ Jahre · 24/7: 01520 88 112 88</p>
  </div>
  <div class="login-label">👋 Wer bist du?</div>
  <div class="employee-grid">
    <div class="emp-card" onclick="selectEmployee('Werner Wiegand','Geschäftsführer')">
      <span class="emp-avatar">👔</span>
      <span class="emp-name">Werner Wiegand</span>
      <span class="emp-role">Geschäftsführer</span>
    </div>
    <div class="emp-card" onclick="selectEmployee('Rayk Wiegand','Geschäftsführer')">
      <span class="emp-avatar">👔</span>
      <span class="emp-name">Rayk Wiegand</span>
      <span class="emp-role">Geschäftsführer</span>
    </div>
    <div class="emp-card" onclick="selectEmployee('E. Rost','Projektleiter Sanierung')">
      <span class="emp-avatar">🔧</span>
      <span class="emp-name">E. Rost</span>
      <span class="emp-role">Projektleiter Sanierung</span>
    </div>
    <div class="emp-card" onclick="selectEmployee('Marius Weihseller','Trocknungstechniker')">
      <span class="emp-avatar">💧</span>
      <span class="emp-name">Marius Weihseller</span>
      <span class="emp-role">Trocknungstechniker</span>
    </div>
    <div class="emp-card" onclick="selectEmployee('Ralf Kern','Schreiner & Elektriker')">
      <span class="emp-avatar">🪚</span>
      <span class="emp-name">Ralf Kern</span>
      <span class="emp-role">Schreiner & Elektriker</span>
    </div>
    <div class="emp-card" onclick="selectEmployee('Marcel Hartmann','Fliesen- & Mosaiklegger')">
      <span class="emp-avatar">🔲</span>
      <span class="emp-name">Marcel Hartmann</span>
      <span class="emp-role">Fliesen- & Mosaiklegger</span>
    </div>
    <div class="emp-card" onclick="selectEmployee('Rico Baumann','Trocknungstechniker')">
      <span class="emp-avatar">💧</span>
      <span class="emp-name">Rico Baumann</span>
      <span class="emp-role">Trocknungstechniker</span>
    </div>
    <div class="emp-card" onclick="selectEmployee('Viktor Wist','Maler')">
      <span class="emp-avatar">🎨</span>
      <span class="emp-name">Viktor Wist</span>
      <span class="emp-role">Maler</span>
    </div>
    <div class="emp-card" onclick="selectEmployee('Doro Zaruba','Backoffice')">
      <span class="emp-avatar">📋</span>
      <span class="emp-name">Doro Zaruba</span>
      <span class="emp-role">Backoffice</span>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════ APP ═══════════════════════════════ -->
<div id="appScreen">

  <div class="topbar">
    <div class="topbar-brand">
      <div class="ico">🏠</div>
      Wiegand KI
    </div>
    <div class="topbar-nav">
      <button class="nav-btn active" id="nav-neu" onclick="showView('neu')">+ Neuer Auftrag</button>
      <button class="nav-btn" id="nav-dashboard" onclick="showView('dashboard')">📊 Dashboard</button>
      <button class="nav-btn" id="nav-projekte" onclick="showView('projekte')">📁 Projekte</button>
      <button class="nav-btn" id="nav-analyse" onclick="showView('analyse')">🔍 KI-Analyse</button>
      <button class="nav-btn" id="nav-grundrisse" onclick="showView('grundrisse')">📐 Grundrisse</button>
      <button class="nav-btn" id="nav-kontakte" onclick="showView('kontakte')">👥 Kontakte</button>
      <button class="nav-btn" id="nav-aufgaben" onclick="showView('aufgaben')">✅ Aufgaben</button>
      <button class="nav-btn" id="nav-kalender" onclick="showView('kalender')">📅 Kalender</button>
      <button class="nav-btn" id="nav-chat" onclick="showView('chat')">💬 Team-Chat</button>
      <button class="nav-btn" id="nav-sevdesk" onclick="showView('sevdesk')">💶 SevDesk</button>
      <button class="nav-btn" id="nav-saas" onclick="showView('saas')">⚙️ SaaS Admin</button>
    </div>
    <div class="topbar-spacer"></div>
    <div class="key-wrap">
      <span class="key-label">🔑 OpenAI</span>
      <input class="key-input" type="password" id="openaiKey" placeholder="sk-..." style="width:170px" value="sk-proj-sOtbqfIyQMrAm9SurpbsOhI3uAd11lZaNkY5h7mBttlUWPjgMM6hAbAKWI3Q1n2dX6gBUIlslAT3BlbkFJUr1bLQfUVy9gcnb6v8ykYhO0IynJBuumASzXbEukKUEFl1-XsUdG9zam_tA-wYYfIRRsSCOSI">
      <span class="key-label" style="color:var(--purple)">🪄 Meshy</span>
      <input class="key-input" type="password" id="meshyKey" placeholder="msy_..." style="width:130px;border-color:rgba(155,109,255,0.3);" />
    <span class="key-label" style="color:#38bdf8">🌐 World Labs</span>
      <input class="key-input" type="password" id="wlKey" placeholder="wlt_..." style="width:150px;border-color:rgba(56,189,248,0.3);" value="gudpd2yW8bJJDwP6nQHCB5BlqjTassnb">
    </div>
    <div class="emp-pill" id="empPill" onclick="switchEmployee()">
      <span id="empPillText">👤 Mitarbeiter</span>
      <span style="font-size:10px;opacity:.6">↩</span>
    </div>
  </div>
  <!-- Mobile API Keys -->
  <div class="mobile-keys" id="mobileKeys">
    <span class="key-label">🔑 OpenAI Key</span>
    <input class="key-input" type="password" id="openaiKeyMobile" placeholder="sk-..." oninput="document.getElementById('openaiKey').value=this.value" />
    <span class="key-label" style="color:var(--purple)">🪄 Meshy</span>
    <input class="key-input" type="password" id="meshyKeyMobile" placeholder="msy_..." style="max-width:130px" oninput="document.getElementById('meshyKey').value=this.value" />
    <span class="key-label" style="color:#38bdf8">🌐 World Labs</span>
    <input class="key-input" type="password" id="wlKeyMobile" placeholder="wlt_..." style="max-width:140px" oninput="document.getElementById('wlKey').value=this.value" />
  </div>

  <!-- ─── DASHBOARD ─── -->
  <div class="view" id="view-dashboard">
    <div class="section-header">
      <div class="section-title"><span>📁</span> Alle Projekte</div>
      <button class="btn btn-primary btn-sm" onclick="showView('neu')">+ Neuer Auftrag</button>
    </div>
    <div class="dash-stats">
      <div class="stat-card">
        <div class="stat-value" id="statTotal">0</div>
        <div class="stat-label">Projekte gesamt</div>
      </div>
      <div class="stat-card">
        <div class="stat-value" id="statMonth">0</div>
        <div class="stat-label">Diesen Monat</div>
      </div>
      <div class="stat-card">
        <div class="stat-value" id="statViz">0</div>
        <div class="stat-label">Mit Visualisierung</div>
      </div>
      <div class="stat-card">
        <div class="stat-value" id="statEmployee">–</div>
        <div class="stat-label">Aktiver Techniker</div>
      </div>
    </div>
    <div class="projects-list" id="projectsList"></div>
  </div>

  <!-- ─── NEUER AUFTRAG ─── -->
  <div class="view active" id="view-neu">
    <div class="err-bar" id="errBar"></div>

    <!-- Objektdaten -->
    <div class="card">
      <div class="section-title"><span>📍</span> Objektdaten</div>
      <div class="g3" style="margin-bottom:12px">
        <div class="field"><label>Objekt / Adresse</label><input type="text" id="objekt" placeholder="z.B. EFH, Musterstr. 5, Eschwege" /></div>
        <div class="field"><label>Auftraggeber</label><input type="text" id="auftraggeber" placeholder="z.B. Familie Müller" /></div>
        <div class="field">
          <label>Art des Schadens</label>
          <select id="schadensart">
            <option value="">Bitte wählen...</option>
            <option>Wasserschaden (Rohrbruch)</option>
            <option>Wasserschaden (Überschwemmung)</option>
            <option>Leckage (unbekannte Ursache)</option>
            <option>Schimmelbefall</option>
            <option>Feuchtigkeit in Wänden/Böden</option>
            <option>Badezimmer-Sanierung</option>
            <option>Keller-Sanierung</option>
            <option>Sonstiges</option>
          </select>
        </div>
      </div>
      <div class="g3">
        <div class="field"><label>Fläche (m²)</label><input type="number" id="flaeche" placeholder="z.B. 12" /></div>
        <div class="field">
          <label>Versicherungsfall</label>
          <select id="versicherung">
            <option>Nein</option>
            <option>Ja – Wohngebäude</option>
            <option>Ja – Hausrat</option>
            <option>Ja – Haftpflicht</option>
            <option>Unklar</option>
          </select>
        </div>
        <div class="field"><label>Kostenziel (€)</label><input type="number" id="budget" placeholder="z.B. 5000" /></div>
      </div>
    </div>

    <!-- Fotos -->
    <div class="card">
      <div class="section-title"><span>📸</span> Fotos des Schadens <span style="font-size:10px;font-weight:400;color:var(--text3);text-transform:none;letter-spacing:0">&nbsp;— erstes Foto = Hauptbild für Visualisierung</span></div>
      <div class="upload-zone" id="uploadZone">
        <input type="file" id="fileInput" accept="image/*" multiple onchange="handleFiles(this)" />
        <div class="upload-icon">📁</div>
        <div class="upload-text">Fotos ablegen oder <span>auswählen</span></div>
        <div class="upload-sub">Mehrere gleichzeitig · JPG, PNG, WEBP</div>
      </div>
      <div class="photos-grid" id="photosGrid"></div>
      <div class="photos-count" id="photosCount"></div>

      <!-- Video -->
      <div class="divider"></div>
      <div class="section-title" style="margin-bottom:8px"><span style="color:var(--purple)">📹</span> Video <span style="font-size:10px;font-weight:400;color:var(--text3);text-transform:none;letter-spacing:0">&nbsp;— KI extrahiert 6 Frames · Codewort „Änderung" markiert Wunschstellen</span></div>
      <div class="video-zone" id="videoZone">
        <input type="file" id="videoInput" accept="video/*" onchange="handleVideo(this)" />
        <div style="font-size:1.6rem;margin-bottom:6px">📹</div>
        <div class="upload-text" style="color:var(--text3)">Video ablegen oder <span style="color:var(--purple)">auswählen</span></div>
        <div class="upload-sub">MP4, MOV, AVI</div>
      </div>
      <div class="video-frames-grid" id="videoFramesGrid"></div>
      <div style="font-size:11px;color:var(--purple);font-weight:500;margin-top:6px" id="videoStatus"></div>
      <div id="videoTranscriptWrap" style="display:none;margin-top:10px;padding:10px;background:rgba(155,109,255,0.07);border:1px solid rgba(155,109,255,0.2);border-radius:var(--rs);font-size:11px;color:var(--text2);line-height:1.7">
        <div style="font-size:10px;font-weight:700;color:var(--purple);margin-bottom:3px;text-transform:uppercase;letter-spacing:.05em">🎙️ Transkript</div>
        <div id="videoTranscriptText"></div>
      </div>
      <div id="videoChangesWrap"></div>

      <!-- Wunschbad-Board -->
      <div class="divider"></div>
      <div class="section-title" style="margin-bottom:10px"><span style="color:#38bdf8">🎯</span> Wunschbad-Board <span style="font-size:10px;font-weight:400;color:var(--text3);text-transform:none;letter-spacing:0">&nbsp;— Zeige der KI deinen Wunschstil</span></div>
      <div class="board-chips" id="boardChips">
        <button class="chip" onclick="toggleChip(this,'Modern Minimalist clean white')">✦ Modern</button>
        <button class="chip" onclick="toggleChip(this,'Skandinavisch hell natürlich Holz')">🌿 Skandinavisch</button>
        <button class="chip" onclick="toggleChip(this,'Industrial dark concrete exposed brick')">🏭 Industrial</button>
        <button class="chip" onclick="toggleChip(this,'Luxus Spa dunkel elegant marble')">💎 Luxus Spa</button>
        <button class="chip" onclick="toggleChip(this,'Mediterran Naturstein warm terracotta')">🌊 Mediterran</button>
        <button class="chip" onclick="toggleChip(this,'Japanisch Zen Holz Stein ruhig')">🍃 Zen</button>
      </div>
      <div class="board-slots" id="boardSlots">
        <div class="board-slot" id="bslot0" onclick="document.getElementById('bsi0').click()">
          <input type="file" id="bsi0" accept="image/*" style="display:none" onchange="handleBoardFile(this,0)">
          <div class="slot-icon">🛁</div><div class="slot-lbl">Traumbad</div>
        </div>
        <div class="board-slot" id="bslot1" onclick="document.getElementById('bsi1').click()">
          <input type="file" id="bsi1" accept="image/*" style="display:none" onchange="handleBoardFile(this,1)">
          <div class="slot-icon">🔲</div><div class="slot-lbl">Fliesen</div>
        </div>
        <div class="board-slot" id="bslot2" onclick="document.getElementById('bsi2').click()">
          <input type="file" id="bsi2" accept="image/*" style="display:none" onchange="handleBoardFile(this,2)">
          <div class="slot-icon">🪣</div><div class="slot-lbl">Waschbecken</div>
        </div>
        <div class="board-slot" id="bslot3" onclick="document.getElementById('bsi3').click()">
          <input type="file" id="bsi3" accept="image/*" style="display:none" onchange="handleBoardFile(this,3)">
          <div class="slot-icon">🚿</div><div class="slot-lbl">Armaturen</div>
        </div>
      </div>
      <textarea id="boardText" rows="2" placeholder="Beschreibe dein Traumbad: z.B. helle Holzelemente, anthrazit Fliesen, freistehende Badewanne, warme Beleuchtung..."
        style="width:100%;background:var(--bg3);border:1px solid var(--border2);border-radius:var(--rs);padding:8px 10px;font-size:12px;font-family:var(--font);color:var(--text);outline:none;resize:vertical;margin-bottom:8px"
        oninput="boardStyleCache=null;updateBoardBadge()"></textarea>
      <div style="display:flex;align-items:center;gap:8px">
        <button class="mini-rec-btn" id="boardRecBtn" onclick="toggleBoardRec()">🎙️</button>
        <span id="boardRecStatus" style="font-size:11px;color:var(--text3)">Wunsch einsprechen (optional)</span>
        <span id="boardRecTimer" style="display:none;font-size:11px;color:var(--accent);font-weight:600;font-family:var(--mono)">0:00</span>
      </div>
      <div id="boardTranscript" style="display:none;font-size:11px;padding:6px 10px;background:rgba(79,124,255,0.07);border:1px solid rgba(79,124,255,0.2);border-radius:var(--rs);margin-top:6px;color:var(--text2);line-height:1.6"></div>
      <div id="boardActiveBadge" class="board-active-badge">✅ Wunschbad-Board aktiv – KI orientiert sich an deinen Vorgaben</div>

      <button class="btn btn-primary btn-full" id="startWizardBtn" onclick="startWizard()" style="display:none;margin-top:16px">✨ Verbesserungsvorschlag für alle Fotos erstellen</button>
    </div>

    <!-- Wizard -->
    <div class="card wizard-card" id="wizardCard" style="display:none">
      <div class="section-title"><span>✨</span> Verbesserungsvorschlag</div>
      <div class="wizard-progress">
        <span id="wizProgressText">Foto 1 von 1</span>
        <div class="wizard-dots" id="wizDots"></div>
      </div>
      <div class="loading" id="wizLoading" style="padding:20px">
        <div class="spinner"></div>
        <p>Erstelle Vorschlag... <span>~45s</span></p>
        <div class="loading-steps">
          <div class="viz-step" id="wstep1">⏳ Schritt 1: Raum & Elemente analysieren</div>
          <div class="viz-step" id="wstep2">⏳ Schritt 2: Foto für KI vorbereiten</div>
          <div class="viz-step" id="wstep3">⏳ Schritt 3: Verbesserungsbild generieren</div>
        </div>
      </div>
      <div id="wizResult" style="display:none">
        <div class="viz-grid" id="wizGrid"></div>
        <div class="wizard-desc" id="wizDesc"></div>
        <div class="wizard-question">💬 Gefällt dir dieser Vorschlag? Beschreibe was du ändern möchtest – ich generiere sofort ein neues Bild.</div>
        <div class="wizard-feedback">
          <textarea id="wizFeedback" placeholder="z.B. Hellere Fliesen, Holzboden statt Stein, Anthrazit-Schränke..."></textarea>
        </div>
        <div class="wizard-actions">
          <button class="btn btn-ghost" onclick="regenWiz()">🔄 Neu generieren</button>
          <button class="btn btn-ghost" id="wizNextBtn" style="display:none" onclick="nextWiz()">→ Nächstes Foto</button>
          <button class="btn btn-green" id="wizDoneBtn" style="display:none" onclick="finishWiz()">✅ Alle fertig</button>
          <button class="btn btn-sm" id="wizStep3DBtn" style="display:none;background:var(--purple);color:#fff" onclick="open3DRoomWithExtras()">🌐 3D-Raum</button>
        </div>
      </div>
      <div id="wizSummary" style="display:none">
        <div class="wizard-summary-title">✅ Alle Verbesserungsvorschläge fertig</div>
        <div class="viz-grid" id="wizSummaryGrid"></div>
        <div style="display:flex;justify-content:center;margin-top:16px">
          <button class="btn btn-sm" id="wizSummary3DBtn" style="display:none;background:var(--purple);color:#fff;font-size:13px;padding:10px 22px" onclick="open3DRoomWithExtras()">🌐 World Labs 3D-Raum aus Vorschlag erstellen</button>
        </div>
      </div>
    </div>

    <!-- Sprache -->
    <div class="card">
      <div class="section-title"><span>🎙️</span> Sprachbeschreibung <span style="font-size:10px;font-weight:400;color:var(--text3);text-transform:none;letter-spacing:0">&nbsp;— unbegrenzte Länge</span></div>
      <div class="rec-card">
        <div class="rec-area">
          <button class="rec-btn" id="recBtn" onclick="toggleRec()">🎙️</button>
          <div class="rec-status" id="recStatus">Drücken</div>
          <div class="rec-timer" id="recTimer">0:00</div>
        </div>
        <div class="rec-info" style="flex:1">
          <h3>Schaden mündlich beschreiben</h3>
          <p>• Wände, Boden, Decke beschreiben<br>
             • Gerüche erwähnen (Schimmel, Moder)<br>
             • Genaue Lage des Schadens<br>
             • Ursache wenn bekannt</p>
          <div class="progress-bar"><div class="progress-fill" id="progressFill"></div></div>
          <div class="audio-playback" id="audioPrev">
            <audio id="audioPlayer" controls></audio>
            <p style="color:var(--green);font-size:11px;margin-top:4px;font-weight:500">✓ Aufnahme gespeichert</p>
          </div>
          <div class="transcript-box" id="transcriptBox">
            <div class="transcript-lbl">🗒️ Transkript (Whisper KI)</div>
            <div id="transcriptText"></div>
          </div>
          <div style="margin-top:12px">
            <div class="field">
              <label>✏️ Oder schriftlich beschreiben</label>
              <textarea id="manualDesc" placeholder="z.B. Feuchte Wände im Keller, Schimmelflecken an der Decke..."></textarea>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Module -->
    <div class="card">
      <div class="section-title"><span>⚙️</span> Was soll erstellt werden?</div>
      <div class="modes-grid">
        <div class="mode-chip on" id="m-schaden" onclick="toggleMode('schaden')"><span class="mi">🔍</span>Schadensbeurteilung</div>
        <div class="mode-chip on" id="m-konzept" onclick="toggleMode('konzept')"><span class="mi">📋</span>Sanierungskonzept</div>
        <div class="mode-chip on" id="m-kosten" onclick="toggleMode('kosten')"><span class="mi">💰</span>Kostenschätzung</div>
        <div class="mode-chip on" id="m-foerder" onclick="toggleMode('foerder')"><span class="mi">🏛️</span>Förderung & Versicherung</div>
      </div>
      <button class="btn btn-primary btn-full" id="analyzeBtn" onclick="runAnalyse()">🔍 KI-Analyse starten</button>
    </div>

    <!-- Loading -->
    <div class="loading" id="mainLoading">
      <div class="spinner"></div>
      <p>Analysiere... <span>Bitte warten</span></p>
      <div class="loading-steps">
        <div class="step" id="step1">⏳ Sprache transkribieren</div>
        <div class="step" id="step2">⏳ Fotos analysieren</div>
        <div class="step" id="step3">⏳ Sanierungskonzept erstellen</div>
        <div class="step" id="step4">⏳ Kostenschätzung berechnen</div>
      </div>
    </div>

    <!-- Result -->
    <div class="card result-box" id="resultCard">
      <div class="result-header">
        <div class="result-title">✅ Analyse abgeschlossen</div>
        <div class="result-actions">
          <button class="btn btn-ghost btn-sm" onclick="copyResult()">📋 Kopieren</button>
          <button class="btn btn-ghost btn-sm" onclick="exportPDF()">📄 PDF</button>
          <button class="btn btn-ghost btn-sm" onclick="saveProject()">💾 Speichern</button>
        </div>
      </div>
      <div class="result-content" id="resultContent"></div>

      <!-- Visualisierung -->
      <div class="viz-wrap">
        <div class="viz-header">
          <div class="viz-header-text">
            <h3>✨ KI-Visualisierung: Sanierter Raum</h3>
            <p>Originalfoto → gpt-image-1 bearbeitet das Foto direkt – Kamerawinkel & Layout bleiben erhalten</p>
          </div>
          <button class="btn btn-primary btn-sm" id="vizBtn" onclick="generateViz()">✨ Erstellen</button>
        </div>
        <div class="reno-wishes">
          <div class="reno-wishes-label">💬 Ihre Wünsche <span style="font-weight:400;text-transform:none">(optional)</span></div>
          <div class="reno-row">
            <textarea id="renoWishes" placeholder="z.B. Weiße Fliesen · Anthrazit-Schränke · Regendusche · Holzboden..."></textarea>
            <button class="mini-rec-btn" id="renoRecBtn" onclick="toggleRenoRec()" title="Wünsche einsprechen">🎙️</button>
          </div>
          <div id="renoTranscript" style="display:none;font-size:11px;padding:5px 9px;background:var(--accent-glow);border-radius:var(--rs);margin-top:6px;color:var(--text2)"></div>
        </div>
        <div class="loading" id="vizLoading" style="padding:20px">
          <div class="spinner"></div>
          <p>Analysiere Raum... <span>~45s</span></p>
          <div class="viz-steps">
            <div class="viz-step" id="vstep1">⏳ Schritt 1: Raumtyp & Perspektive analysieren</div>
            <div class="viz-step" id="vstep2">⏳ Schritt 2: Foto vorbereiten</div>
            <div class="viz-step" id="vstep3">⏳ Schritt 3: Sanierungsbild generieren</div>
          </div>
        </div>
        <div id="vizResult" style="display:none">
          <div class="viz-grid" id="vizGrid"></div>
          <div style="display:flex;align-items:center;justify-content:center;gap:14px;margin-top:14px;flex-wrap:wrap">
            <p style="font-size:11px;color:var(--text3)">🎯 Gleiche Perspektive – modernisiert</p>
            <button class="btn btn-ghost btn-sm" id="vizRegenBtn" style="display:none" onclick="generateViz(true)">🔄 Neuen Vorschlag</button>
            <div style="display:none;align-items:center;gap:8px" id="modelSelectWrap">
              <span style="font-size:11px;color:var(--text3)">Modell:</span>
              <div style="display:flex;background:var(--bg4);border-radius:20px;padding:2px;gap:2px">
                <button id="mainBtnMini" onclick="setWLModelMain('mini')"
                  style="border:none;border-radius:18px;padding:4px 12px;cursor:pointer;font-family:var(--font);font-size:11px;font-weight:600;background:var(--accent);color:#fff;transition:.15s">⚡ Mini</button>
                <button id="mainBtnPlus" onclick="setWLModelMain('plus')"
                  style="border:none;border-radius:18px;padding:4px 12px;cursor:pointer;font-family:var(--font);font-size:11px;font-weight:600;background:transparent;color:var(--text3);transition:.15s">💎 Plus</button>
              </div>
            </div>
            <button class="btn btn-sm" id="btn3dRoom" style="display:none;background:var(--purple);color:#fff" onclick="open3DRoomWithExtras()">🌐 World Labs 3D-Raum erstellen</button>
          </div>

          <!-- Optionale weitere Perspektiven für 3D -->
          <div id="extra3dWrap" style="display:none;margin-top:14px;padding:14px;background:var(--bg3);border-radius:var(--rs);border:1px solid rgba(155,109,255,0.3)">
            <div style="display:flex;align-items:center;gap:8px;margin-bottom:8px">
              <span style="font-size:13px;font-weight:700;color:var(--purple)">📸 Optional: weitere Perspektiven hochladen</span>
            </div>
            <div style="font-size:11px;color:var(--text3);margin-bottom:12px;line-height:1.6">
              Mit <strong style="color:var(--text2)">1 Bild</strong> → KI generiert die anderen 3 Winkel automatisch (DALL-E) ✨<br>
              Mit <strong style="color:var(--text2)">4 Bildern</strong> → Exakter, präziserer 3D-Raum aus allen Richtungen
            </div>
            <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:8px;margin-bottom:10px">
              <div id="extra3dSlot0" onclick="document.getElementById('extra3dFile0').click()" style="aspect-ratio:1;border:2px dashed rgba(155,109,255,0.35);border-radius:var(--rs);display:flex;flex-direction:column;align-items:center;justify-content:center;cursor:pointer;background:var(--bg4);position:relative;overflow:hidden;transition:all .15s">
                <input type="file" id="extra3dFile0" accept="image/*" style="display:none" onchange="loadExtra3D(0,this)">
                <div style="font-size:1.4rem">+</div>
                <div style="font-size:9px;font-weight:700;color:rgba(155,109,255,0.8);margin-top:4px">90° RECHTS</div>
                <div style="font-size:9px;color:var(--text3);margin-top:2px">optional</div>
              </div>
              <div id="extra3dSlot1" onclick="document.getElementById('extra3dFile1').click()" style="aspect-ratio:1;border:2px dashed rgba(155,109,255,0.35);border-radius:var(--rs);display:flex;flex-direction:column;align-items:center;justify-content:center;cursor:pointer;background:var(--bg4);position:relative;overflow:hidden;transition:all .15s">
                <input type="file" id="extra3dFile1" accept="image/*" style="display:none" onchange="loadExtra3D(1,this)">
                <div style="font-size:1.4rem">+</div>
                <div style="font-size:9px;font-weight:700;color:rgba(155,109,255,0.8);margin-top:4px">180° HINTEN</div>
                <div style="font-size:9px;color:var(--text3);margin-top:2px">optional</div>
              </div>
              <div id="extra3dSlot2" onclick="document.getElementById('extra3dFile2').click()" style="aspect-ratio:1;border:2px dashed rgba(155,109,255,0.35);border-radius:var(--rs);display:flex;flex-direction:column;align-items:center;justify-content:center;cursor:pointer;background:var(--bg4);position:relative;overflow:hidden;transition:all .15s">
                <input type="file" id="extra3dFile2" accept="image/*" style="display:none" onchange="loadExtra3D(2,this)">
                <div style="font-size:1.4rem">+</div>
                <div style="font-size:9px;font-weight:700;color:rgba(155,109,255,0.8);margin-top:4px">270° LINKS</div>
                <div style="font-size:9px;color:var(--text3);margin-top:2px">optional</div>
              </div>
            </div>
            <div id="extra3dStatus" style="font-size:11px;color:var(--text3)">Keine eigenen Bilder — DALL-E ergänzt die anderen Winkel automatisch ✨</div>
          </div>

      <!-- Rechnung -->
      <div class="invoice-wrap">
        <h3>📄 Rechnung erstellen</h3>
        <p>Automatisch aus der Analyse befüllt – nur E-Mail eingeben und senden</p>
        <div class="g3" style="margin-bottom:12px">
          <div class="field"><label>Kunden-E-Mail</label><input type="email" id="inv-email" placeholder="kunde@email.de" /></div>
          <div class="field"><label>Rechnungsnummer</label><input type="text" id="inv-nr" placeholder="RE-2024-001" /></div>
          <div class="field"><label>Gesamtbetrag (€ brutto)</label><input type="number" id="inv-betrag" placeholder="3500" /></div>
        </div>
        <div class="invoice-preview" id="invoicePreview"></div>
        <div style="display:flex;gap:10px;flex-wrap:wrap">
          <button class="btn btn-ghost btn-sm" onclick="previewInvoice()">🖨️ Vorschau</button>
          <button class="btn btn-green btn-sm" onclick="sendInvoice()">📨 Senden</button>
        </div>
        <div id="sendStatus" style="margin-top:10px;font-size:12px"></div>
      </div>
    </div>
  </div><!-- /view-neu -->


  <!-- ══ PROJEKTE ══ -->
  <div class="view" id="view-projekte">
    <div class="section-header"><div class="section-title"><span>📁</span> Projekte</div><button class="btn btn-primary btn-sm" onclick="showView('neu')">+ Neuer Auftrag</button></div>
    <div class="dash-stats">
      <div class="stat-card"><div class="stat-value" id="pStatTotal">0</div><div class="stat-label">Gesamt</div></div>
      <div class="stat-card"><div class="stat-value" id="pStatMonth">0</div><div class="stat-label">Diesen Monat</div></div>
      <div class="stat-card"><div class="stat-value" id="pStatViz">0</div><div class="stat-label">Mit Visualisierung</div></div>
      <div class="stat-card"><div class="stat-value" id="pStatEmp">–</div><div class="stat-label">Aktiver Techniker</div></div>
    </div>
    <div class="projects-list" id="projekteList"></div>
  </div>

  <!-- ══ KI-ANALYSE ══ -->
  <div class="view" id="view-analyse">
    <div class="section-header">
      <div class="section-title"><span>🔍</span> KI-Analyse</div>
      <div style="font-size:12px;color:var(--text3)">Bausanierungskonzept nach IDW S 6 Standard</div>
    </div>

    <!-- Upload + Auftragsdaten -->
    <div class="card" style="margin-top:14px">
      <div class="section-title" style="margin-bottom:14px"><span>📸</span> Fotos & Auftrag</div>
      <div class="upload-zone" id="analyseUploadZone" style="margin-bottom:14px">
        <input type="file" accept="image/*" multiple onchange="analyseHandleFiles(this)">
        <div class="upload-icon" id="analyseUploadIcon">📸</div>
        <div class="upload-text">Fotos hochladen oder <span>auswählen</span></div>
        <div class="upload-sub">JPG, PNG, WEBP — mehrere gleichzeitig möglich</div>
      </div>
      <div id="analysePhotoGrid" class="photos-grid" style="margin-bottom:14px"></div>

      <div class="g2" style="margin-bottom:10px">
        <div class="field"><label>Objekt / Adresse</label><input type="text" id="anlObjekt" placeholder="z.B. EFH Musterstr. 5, Eschwege"></div>
        <div class="field"><label>Auftraggeber</label><input type="text" id="anlKunde" placeholder="z.B. Familie Müller"></div>
      </div>
      <div class="g2" style="margin-bottom:10px">
        <div class="field"><label>Art des Schadens</label>
          <select id="anlSchaden" style="width:100%">
            <option value="">Bitte wählen...</option>
            <option>Wasserschaden</option><option>Schimmelbefall</option>
            <option>Brandschaden</option><option>Sturmschaden</option>
            <option>Badsanierung</option><option>Feuchtigkeitsschaden</option>
            <option>Sonstiger Schaden</option>
          </select>
        </div>
        <div class="field"><label>Fläche (m²)</label><input type="number" id="anlFlaeche" placeholder="z.B. 25" min="1"></div>
      </div>
      <div class="g2" style="margin-bottom:14px">
        <div class="field"><label>Versicherungsfall</label>
          <select id="anlVers" style="width:100%">
            <option value="Nein">Nein</option><option value="Ja">Ja</option><option value="Prüfung läuft">Prüfung läuft</option>
          </select>
        </div>
        <div class="field"><label>Kostenziel (€)</label><input type="number" id="anlBudget" placeholder="z.B. 5000"></div>
      </div>

      <div class="field" style="margin-bottom:14px">
        <label>Zusätzliche Beschreibung (optional)</label>
        <textarea id="anlBeschreibung" placeholder="z.B. Rohrbruch im Badezimmer, Wasser stand ca. 3cm hoch, Parkett aufgequollen..." style="min-height:70px;resize:vertical"></textarea>
      </div>

      <button class="btn btn-primary btn-full" onclick="runAnalyseTab()" id="anlBtn" style="padding:13px;font-size:14px;font-weight:700">
        🔍 IDW S 6 Sanierungskonzept erstellen
      </button>
    </div>

    <!-- Ladebalken -->
    <div id="anlLoading" style="display:none;margin-top:16px">
      <div class="card" style="text-align:center;padding:28px">
        <div class="spinner" style="margin:0 auto 14px"></div>
        <div style="font-weight:600;font-size:14px;color:var(--text)" id="anlLoadingText">Analyse läuft...</div>
        <div style="font-size:12px;color:var(--text3);margin-top:6px">GPT-4o erstellt 2-seitiges Sanierungskonzept nach IDW S 6</div>
        <div style="display:flex;justify-content:center;gap:8px;margin-top:16px;flex-wrap:wrap" id="anlSteps">
          <span id="anlStep1" style="font-size:11px;color:var(--text3);padding:4px 10px;background:var(--bg3);border-radius:10px">⏳ Fotos analysieren</span>
          <span id="anlStep2" style="font-size:11px;color:var(--text3);padding:4px 10px;background:var(--bg3);border-radius:10px">⏳ Schaden bewerten</span>
          <span id="anlStep3" style="font-size:11px;color:var(--text3);padding:4px 10px;background:var(--bg3);border-radius:10px">⏳ IDW S 6 Konzept erstellen</span>
          <span id="anlStep4" style="font-size:11px;color:var(--text3);padding:4px 10px;background:var(--bg3);border-radius:10px">⏳ Kostenschätzung</span>
        </div>
      </div>
    </div>

    <!-- Ergebnis -->
    <div id="anlResult" style="display:none;margin-top:16px">
      <div class="card">
        <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:16px;flex-wrap:wrap;gap:10px">
          <div class="section-title" style="margin:0"><span>📋</span> Sanierungskonzept (IDW S 6)</div>
          <div style="display:flex;gap:8px;flex-wrap:wrap">
            <button class="btn btn-ghost btn-sm" onclick="anlPrint()">🖨️ Drucken / PDF</button>
            <button class="btn btn-ghost btn-sm" onclick="anlCopy()">📋 Kopieren</button>
            <button class="btn btn-primary btn-sm" onclick="showView('neu')">+ Neuer Auftrag →</button>
          </div>
        </div>
        <!-- IDW S6 Header -->
        <div style="border:1px solid var(--border2);border-radius:var(--rs);padding:14px;margin-bottom:16px;background:var(--bg3)">
          <div style="display:flex;justify-content:space-between;flex-wrap:wrap;gap:8px;font-size:12px;color:var(--text2)">
            <div><strong style="color:var(--text)">Gebäudetrocknung Wiegand GbR</strong><br>Eschwege · Meisterbetrieb · 30+ Jahre</div>
            <div style="text-align:right"><span id="anlHeaderObjekt"></span><br><span id="anlHeaderDatum"></span></div>
          </div>
          <div style="margin-top:8px;font-size:11px;color:var(--text3)">Erstellt nach IDW Standard: Anforderungen an Sanierungskonzepte (IDW S 6, Fassung 2023)</div>
        </div>
        <div id="anlResultContent" style="font-size:13px;line-height:1.7;color:var(--text2)"></div>
      </div>

      <!-- Letzte Analysen -->
      <div class="card" style="margin-top:12px">
        <div class="section-title" style="margin-bottom:12px"><span>🕐</span> Letzte Analysen</div>
        <div id="anlHistory"></div>
      </div>
    </div>
  </div>

  <!-- ══ GRUNDRISSE ══ -->
  <div class="view" id="view-grundrisse">
    <div class="section-title"><span>📐</span> Grundrisse</div>
    <div class="g2" style="margin-top:16px">
      <div class="card">
        <div class="section-title" style="margin-bottom:14px"><span>📤</span> Grundriss hochladen</div>
        <div class="upload-zone"><input type="file" accept="image/*,.pdf" onchange="grundrissUpload(this)"><div class="upload-icon">📐</div><div class="upload-text">PDF oder Bild hochladen</div><div class="upload-sub">JPG, PNG, PDF</div></div>
        <div id="grundrissPreview" style="margin-top:12px"></div>
      </div>
      <div class="card"><div class="section-title" style="margin-bottom:14px"><span>🗂️</span> Gespeicherte Grundrisse</div><div id="grundrissList"></div></div>
    </div>
    <div class="card" style="margin-top:12px">
      <div class="section-header" style="margin-bottom:12px"><div class="section-title"><span>🚪</span> Räume</div><button class="btn btn-ghost btn-sm" onclick="addGrundrissRoom()">+ Raum</button></div>
      <div id="grundrissRooms" style="display:grid;grid-template-columns:repeat(auto-fill,minmax(150px,1fr));gap:10px"></div>
    </div>

    <!-- KI-Grundriss Generator -->
    <div class="card" style="margin-top:12px">
      <div class="section-title" style="margin-bottom:6px"><span>🤖</span> KI-Grundriss Generator <span style="color:var(--text3);font-size:11px;font-weight:400;margin-left:8px">— Fotos oder Scan → maßstabgerechter Grundriss</span></div>
      <p style="font-size:12px;color:var(--text3);margin-bottom:14px">Lade 1–4 Fotos hoch — die KI erstellt daraus einen maßstabgerechten Grundriss mit Maßen.</p>

      <!-- Scan-Status -->
      <div id="grScanStatus" style="background:var(--bg3);border-radius:var(--r);padding:20px;text-align:center;margin-bottom:14px;border:2px dashed var(--border2)">
        <div style="font-size:2.5rem;margin-bottom:10px">📡</div>
        <div style="font-weight:700;font-size:14px;color:var(--text);margin-bottom:6px">Raum noch nicht gescannt</div>
        <div style="font-size:12px;color:var(--text3)">Scanne den Raum — die KI analysiert den gesamten Scan und erstellt den Grundriss</div>
      </div>
      <div id="grScanDone" style="display:none;background:rgba(52,208,127,0.08);border:1px solid rgba(52,208,127,0.3);border-radius:var(--r);padding:14px;margin-bottom:14px;display:none;align-items:center;gap:12px">
        <div style="font-size:2rem">✅</div>
        <div>
          <div style="font-weight:700;font-size:13px;color:var(--green)" id="grScanInfo">Scan abgeschlossen</div>
          <div style="font-size:11px;color:var(--text3);margin-top:2px">Klicke auf "Grundriss generieren"</div>
        </div>
      </div>
      <div style="display:flex;gap:8px;margin-bottom:14px;flex-wrap:wrap;align-items:center">
        <button class="btn btn-primary" onclick="generateGrundriss()" id="grGenerateBtn" disabled style="opacity:0.5">🤖 Grundriss generieren</button>
      </div>

      <!-- Ergebnis -->
      <div id="grResult" style="display:none">
        <div style="border:1px solid var(--border2);border-radius:var(--r);overflow:hidden;margin-bottom:12px">
          <canvas id="grCanvas" style="width:100%;display:block;background:#fff"></canvas>
        </div>
        <div id="grMeasurements" style="background:var(--bg3);border-radius:var(--rs);padding:12px;font-size:12px;color:var(--text2);line-height:1.8;margin-bottom:10px"></div>
        <div style="display:flex;gap:8px;flex-wrap:wrap">
          <button class="btn btn-ghost btn-sm" onclick="grDownload()">⬇ PNG speichern</button>
          <button class="btn btn-ghost btn-sm" onclick="grSaveToList()">💾 In Grundrisse speichern</button>
          <button class="btn btn-primary btn-sm" onclick="grUseFor3D()">🏠 Für 3D-Raum verwenden →</button>
        </div>
      </div>
      <div id="grLoading" style="display:none;text-align:center;padding:30px">
        <div class="spinner"></div>
        <p style="color:var(--text3);margin-top:12px" id="grLoadingText">KI analysiert Fotos...</p>
      </div>
    </div>

    <!-- Manueller Zeichner -->
    <div class="card" style="margin-top:12px">
      <div class="section-title" style="margin-bottom:14px"><span>✏️</span> Manuell zeichnen <span style="color:var(--text3);font-size:11px;font-weight:400;margin-left:8px">— eigene Skizze</span></div>
      <div style="display:flex;gap:8px;margin-bottom:10px;flex-wrap:wrap">
        <button class="btn btn-ghost btn-sm" onclick="v3dDrawTool='wall';grundrissHighlightTool('wall')" id="v3dToolWall" style="border-color:var(--accent);color:var(--accent)">🧱 Wand</button>
        <button class="btn btn-ghost btn-sm" onclick="v3dDrawTool='door';grundrissHighlightTool('door')" id="v3dToolDoor">🚪 Tür</button>
        <button class="btn btn-ghost btn-sm" onclick="v3dDrawTool='window';grundrissHighlightTool('window')" id="v3dToolWindow">🪟 Fenster</button>
        <button class="btn btn-ghost btn-sm" onclick="v3dClearCanvas()">🗑 Löschen</button>
      </div>
      <canvas id="v3dCanvas" width="700" height="400" style="width:100%;border:1px solid var(--border2);border-radius:var(--rs);cursor:crosshair;background:#0d0f14;display:block"></canvas>
      <div style="font-size:11px;color:var(--text3);margin-top:6px">🖱 Klicken + Ziehen = Linie zeichnen</div>
    </div>
  </div>

  <!-- ══ 3D-VIEWER ══ -->
  <!-- ══ KONTAKTE ══ -->
  <div class="view" id="view-kontakte">
    <div class="section-header"><div class="section-title"><span>👥</span> Kontakte</div><button class="btn btn-primary btn-sm" onclick="addKontakt()">+ Kontakt</button></div>
    <div style="display:flex;gap:8px;margin:14px 0">
      <input type="text" id="kontaktSearch" placeholder="Suchen..." oninput="renderKontakte()" style="flex:1;background:var(--bg3);border:1px solid var(--border2);border-radius:var(--rs);padding:8px 12px;color:var(--text);font-family:var(--font);font-size:13px;outline:none">
      <select id="kontaktFilter" onchange="renderKontakte()" style="background:var(--bg3);border:1px solid var(--border2);border-radius:var(--rs);padding:8px 12px;color:var(--text);font-family:var(--font);font-size:13px;outline:none"><option value="">Alle</option><option value="kunde">Kunden</option><option value="versicherung">Versicherung</option><option value="lieferant">Lieferant</option><option value="partner">Partner</option></select>
    </div>
    <div id="kontakteGrid" style="display:grid;grid-template-columns:repeat(auto-fill,minmax(280px,1fr));gap:12px"></div>
  </div>

  <!-- ══ AUFGABEN ══ -->
  <div class="view" id="view-aufgaben">
    <div class="section-header"><div class="section-title"><span>✅</span> Aufgaben</div><button class="btn btn-primary btn-sm" onclick="addAufgabe()">+ Aufgabe</button></div>
    <div style="display:flex;gap:6px;margin:14px 0;flex-wrap:wrap">
      <button class="chip active" id="aufgabeTab-alle" onclick="setAufgabeTab('alle')">Alle</button>
      <button class="chip" id="aufgabeTab-offen" onclick="setAufgabeTab('offen')">Offen</button>
      <button class="chip" id="aufgabeTab-inarbeit" onclick="setAufgabeTab('inarbeit')">In Arbeit</button>
      <button class="chip" id="aufgabeTab-erledigt" onclick="setAufgabeTab('erledigt')">Erledigt</button>
    </div>
    <div id="aufgabenList"></div>
  </div>

  <!-- ══ KALENDER ══ -->
  <div class="view" id="view-kalender">
    <div class="section-header"><div class="section-title"><span>📅</span> Kalender</div><button class="btn btn-primary btn-sm" onclick="addTermin()">+ Termin</button></div>
    <div class="card" style="margin-top:16px">
      <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:14px">
        <button class="btn btn-ghost btn-sm" onclick="kalPrev()">← Zurück</button>
        <div id="kalMonthLabel" style="font-weight:700;font-size:15px"></div>
        <button class="btn btn-ghost btn-sm" onclick="kalNext()">Weiter →</button>
      </div>
      <div id="kalGrid" style="display:grid;grid-template-columns:repeat(7,1fr);gap:3px"></div>
    </div>
    <div class="card" style="margin-top:12px"><div class="section-title" style="margin-bottom:12px"><span>📋</span> Termine</div><div id="terminList"></div></div>
  </div>

  <!-- ══ TEAM-CHAT ══ -->
  <div class="view" id="view-chat">
    <div class="section-title"><span>💬</span> Team-Chat</div>
    <div style="display:grid;grid-template-columns:200px 1fr;gap:12px;margin-top:16px;height:calc(100vh - 180px);min-height:400px">
      <div class="card" style="overflow-y:auto;padding:14px">
        <div style="font-size:10px;font-weight:700;color:var(--text3);letter-spacing:.08em;margin-bottom:8px">KANÄLE</div>
        <div id="chatChannels"></div>
        <div style="font-size:10px;font-weight:700;color:var(--text3);letter-spacing:.08em;margin:14px 0 8px">DIREKT</div>
        <div id="chatDMs"></div>
      </div>
      <div class="card" style="display:flex;flex-direction:column;padding:0;overflow:hidden">
        <div style="padding:12px 16px;border-bottom:1px solid var(--border);font-weight:600;font-size:13px" id="chatTitle"># allgemein</div>
        <div id="chatMessages" style="flex:1;overflow-y:auto;padding:16px;display:flex;flex-direction:column;gap:10px"></div>
        <div style="padding:10px 14px;border-top:1px solid var(--border);display:flex;gap:8px">
          <input type="text" id="chatInput" placeholder="Nachricht..." onkeydown="if(event.key==='Enter')sendChatMsg()" style="flex:1;background:var(--bg3);border:1px solid var(--border2);border-radius:var(--rs);padding:8px 12px;color:var(--text);font-family:var(--font);font-size:13px;outline:none">
          <button class="btn btn-primary btn-sm" onclick="sendChatMsg()">Senden</button>
        </div>
      </div>
    </div>
  </div>

  <!-- ══ SEVDESK ══ -->
  <div class="view" id="view-sevdesk">
    <div class="section-title"><span>💶</span> SevDesk – Buchhaltung</div>
    <div class="g2" style="margin-top:16px">
      <div class="card">
        <div class="section-title" style="margin-bottom:14px"><span>📄</span> Neue Rechnung</div>
        <div class="field" style="margin-bottom:10px"><label>Kunde</label><input type="text" id="sevKunde" placeholder="Name oder Firma"></div>
        <div class="field" style="margin-bottom:10px"><label>Leistung</label><input type="text" id="sevLeistung" placeholder="Gebäudetrocknung..."></div>
        <div class="g2" style="margin-bottom:10px">
          <div class="field"><label>Betrag netto (€)</label><input type="number" id="sevBetrag" placeholder="0.00" oninput="calcSevMwst()"></div>
          <div class="field"><label>MwSt</label><select id="sevMwst" onchange="calcSevMwst()" style="width:100%;background:var(--bg3);border:1px solid var(--border2);border-radius:var(--rs);padding:9px 12px;color:var(--text);font-size:13px;outline:none"><option value="19">19%</option><option value="7">7%</option><option value="0">0%</option></select></div>
        </div>
        <div id="sevCalc" style="background:var(--bg3);border-radius:var(--rs);padding:11px;margin-bottom:12px;font-size:13px;color:var(--text2)">Betrag eingeben…</div>
        <div style="display:flex;gap:8px;flex-wrap:wrap">
          <button class="btn btn-primary" onclick="createSevRechnung()">📄 Erstellen</button>
          <button class="btn btn-ghost" onclick="exportSevCSV()">⬇ CSV</button>
        </div>
      </div>
      <div class="card">
        <div class="section-title" style="margin-bottom:14px"><span>📊</span> Rechnungen</div>
        <div id="sevStats" style="color:var(--text3);font-size:13px;margin-bottom:12px"></div>
        <div id="sevRechnungen"></div>
      </div>
    </div>
  </div>

  <!-- ══ SAAS ADMIN ══ -->
  <div class="view" id="view-saas">
    <div class="section-title"><span>⚙️</span> SaaS Admin</div>
    <div class="g2" style="margin-top:16px">
      <div class="card">
        <div class="section-title" style="margin-bottom:14px"><span>🔑</span> API-Schlüssel</div>
        <div class="field" style="margin-bottom:10px"><label>OpenAI API Key</label><input type="password" id="saasOpenAI" placeholder="sk-proj-..." style="width:100%;background:var(--bg3);border:1px solid var(--border2);border-radius:var(--rs);padding:9px 12px;color:var(--text);font-size:13px;outline:none"></div>
        <div class="field" style="margin-bottom:14px"><label>World Labs Key</label><input type="password" id="saasWL" placeholder="wlt_..." style="width:100%;background:var(--bg3);border:1px solid var(--border2);border-radius:var(--rs);padding:9px 12px;color:var(--text);font-size:13px;outline:none"></div>
        <button class="btn btn-primary btn-sm" onclick="saveApiKeys()">💾 Keys speichern</button>
        <div id="saasKeySaved" style="display:none;margin-top:8px;font-size:12px;color:var(--green)">✓ Gespeichert</div>
      </div>
      <div class="card">
        <div class="section-title" style="margin-bottom:14px"><span>👥</span> Benutzer</div>
        <div id="saasUsers"></div>
        <button class="btn btn-ghost btn-sm" style="margin-top:10px" onclick="addSaasUser()">+ Benutzer</button>
      </div>
    </div>
    <div class="g2" style="margin-top:12px">
      <div class="card">
        <div class="section-title" style="margin-bottom:14px"><span>📊</span> Nutzung</div>
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px">
          <div class="stat-card"><div class="stat-value" id="saasStatA">0</div><div class="stat-label">Analysen</div></div>
          <div class="stat-card"><div class="stat-value" id="saasStatV">0</div><div class="stat-label">Visualisierungen</div></div>
          <div class="stat-card"><div class="stat-value" id="saasStatP">0</div><div class="stat-label">Projekte</div></div>
          <div class="stat-card"><div class="stat-value" id="saasStatS">–</div><div class="stat-label">Speicher</div></div>
        </div>
      </div>
      <div class="card">
        <div class="section-title" style="margin-bottom:14px"><span>🔔</span> System-Log</div>
        <div id="saasLog" style="font-family:var(--mono);font-size:11px;color:var(--text3);line-height:1.9;max-height:180px;overflow-y:auto"></div>
      </div>
    </div>
  </div>

</div><!-- /appScreen -->

<!-- ═══════════════════════ WORLD LABS 3D OVERLAY ═══════════════════════ -->
<div id="room3dOverlay">
  <div class="r3d-topbar">
    <div class="r3d-title"><div class="r3d-dot"></div> 3D Sanierungsraum · World Labs Marble</div>
    <div style="flex:1"></div>
    <!-- Quality toggle -->
    <div class="r3d-mode-toggle">
      <button class="r3d-mode-btn active" id="r3dBtnMini" onclick="setWLModel('mini')">⚡ Mini (~45s)</button>
      <button class="r3d-mode-btn" id="r3dBtnPlus" onclick="setWLModel('plus')">💎 Plus (~5min)</button>
    </div>
    <button class="btn btn-red btn-sm" onclick="close3DRoom()" style="margin-left:12px">✕ Schließen</button>
  </div>

  <div style="flex:1;position:relative;overflow:hidden;display:flex;flex-direction:column">
    <!-- Loading screen -->
    <div id="r3dLoading" style="position:absolute;inset:0;background:rgba(10,8,6,0.97);display:flex;flex-direction:column;align-items:center;justify-content:center;gap:16px;z-index:10">
      <div class="r3d-spinner"></div>
      <div style="text-align:center">
        <div id="r3dStatusTxt" style="font-size:14px;font-weight:600;color:#e0dcd4;margin-bottom:10px">World Labs generiert 3D-Raum...</div>
        <div id="r3dStatusBar-wrap"><div id="r3dStatusBar"></div></div>
        <div id="r3dStatusSub" style="font-size:11px;color:rgba(255,255,255,.3);margin-top:8px">Marble wandelt dein Sanierungsbild in einen begehbaren 3D-Raum um</div>
      </div>
      <div class="r3d-steps">
        <div class="r3d-step" id="r3dst1">○ Bild zu World Labs hochladen</div>
        <div class="r3d-step" id="r3dst2">○ Marble 3D-Welt generieren</div>
        <div class="r3d-step" id="r3dst3">○ Auf Fertigstellung warten</div>
        <div class="r3d-step" id="r3dst4">○ Gaussian Splat laden &amp; rendern</div>
      </div>
    </div>

    <!-- GaussianSplats3D viewer container — direkt in der App -->
    <div id="splat3dContainer" style="width:100%;height:100%;flex:1;display:none;position:relative;overflow:hidden;background:#0a0806;min-height:0"></div>

    <!-- Babylon.js Fallback canvas -->
    <canvas id="room3dCanvas" tabindex="0" style="display:none;width:100%;height:100%;flex:1;outline:none"></canvas>

    <!-- World URL + Open button (nach Generierung) -->
    <div id="r3dReadyBar" style="display:none;background:rgba(10,8,6,0.88);border-top:1px solid rgba(255,255,255,0.07);padding:10px 20px;align-items:center;gap:14px;flex-shrink:0">
      <span style="font-size:11px;color:rgba(255,255,255,.4)">🖱 Ziehen = drehen &nbsp;·&nbsp; Scroll = zoom &nbsp;·&nbsp; Rechtsklick = verschieben</span>
      <div style="flex:1"></div>
      <a id="r3dOpenBtn" href="#" target="_blank" class="btn btn-sm" style="background:rgba(155,109,255,0.15);color:#c4b5fd;border:1px solid rgba(155,109,255,0.3);text-decoration:none;font-size:11px">🌐 Auch in Marble öffnen</a>
    </div>

    <div id="r3dBadge"></div>
    <!-- Quality hint -->
    <div id="r3dQualityHint" style="position:absolute;top:64px;right:12px;background:rgba(0,0,0,0.55);backdrop-filter:blur(8px);border:1px solid rgba(255,255,255,0.12);border-radius:10px;padding:8px 12px;font-size:11px;color:rgba(255,255,255,0.6);max-width:220px;line-height:1.6;pointer-events:none;display:none">
      💡 <strong style="color:#c4b5fd">Gaussian Splatting</strong><br>
      Leicht verschwommen beim Stillstand — beim Bewegen schärfer. Normal für diese 3D-Technologie.<br>
      Für maximale Schärfe: <strong style="color:#c4b5fd">Plus-Modell</strong> wählen.
    </div>
  </div>
</div>

<div id="toast"></div>

<script>
// ═══════════════════════════════════════════════════════
//  STATE
// ═══════════════════════════════════════════════════════
let currentEmployee = null;
let photos = [];
let audBlob = null, isRec = false, mRec = null, aChunks = [], timerInterval = null, recSeconds = 0;
let modes = new Set(['schaden','konzept','kosten','foerder']);
let lastAnalysis = '';
let transcript = '';
let vizHistory = [], cachedEditPrompt = '', cachedRoomType = 'Raum', cachedRoomAnalysis = null;
let renoAudBlob = null, renoIsRec = false, renoMRec = null, renoAChunks = [];
let wizIndex = 0, wizCachedAnalysis = [], wizCachedTypes = [], wizResults = [];
let globalWizStyle = null, wizFirstResultSrc = null, wizPrefilledFeedback = [];
let boardSlots = [null,null,null,null], boardChipsSet = new Set();
let boardAudBlob = null, boardIsRec = false, boardMRec = null, boardAChunks = [];
let boardTimerInterval = null, boardRecSeconds = 0, boardStyleCache = null;
let videoChanges = [];
let _3dNachherSrc = null;
let _3d = {engine:null,scene:null,fpsCam:null,arcCam:null,mode:'walk',drag:false,lx:0,ly:0};

// ═══════════════════════════════════════════════════════
//  NAVIGATION / LOGIN
// ═══════════════════════════════════════════════════════
function selectEmployee(name, role) {
  currentEmployee = {name, role};
  document.getElementById('loginScreen').style.display = 'none';
  document.getElementById('appScreen').style.display = 'block';
  document.getElementById('empPillText').textContent = '👤 ' + name;
  document.getElementById('statEmployee').textContent = name.split(' ')[0];
  showView('neu');
}
function switchEmployee() {
  document.getElementById('loginScreen').style.display = 'flex';
  document.getElementById('appScreen').style.display = 'none';
}
function showView(v) {
  document.querySelectorAll('.view').forEach(el => el.classList.remove('active'));
  document.querySelectorAll('.nav-btn').forEach(el => el.classList.remove('active'));
  var ve=document.getElementById('view-'+v), ne=document.getElementById('nav-'+v);
  if(ve) ve.classList.add('active');
  if(ne) ne.classList.add('active');
  if(v==='dashboard')  refreshDashboard();
  if(v==='projekte')   refreshProjekte();
  if(v==='kontakte')   renderKontakte();
  if(v==='aufgaben')   renderAufgaben();
  if(v==='kalender')   renderKalender();
  if(v==='chat')       initChat();
  if(v==='sevdesk')    renderSevDesk();
  if(v==='saas')       renderSaas();
  if(v==='grundrisse') renderGrundrisse();
  if(v==='grundrisse') { renderGrundrisse(); setTimeout(v3dInitCanvas, 200); }
  if(v==='analyse')    { renderAnalyseHistory(); }
}

// ═══════════════════════════════════════════════════════
//  DASHBOARD
// ═══════════════════════════════════════════════════════
function refreshDashboard() {
  const projects = getProjects();
  const now = new Date();
  const thisMonth = projects.filter(p => {
    const d = new Date(p.date);
    return d.getMonth() === now.getMonth() && d.getFullYear() === now.getFullYear();
  });
  const withViz = projects.filter(p => p.vizImages && p.vizImages.length > 0);
  document.getElementById('statTotal').textContent = projects.length;
  document.getElementById('statMonth').textContent = thisMonth.length;
  document.getElementById('statViz').textContent = withViz.length;
  const list = document.getElementById('projectsList');
  if (!projects.length) {
    list.innerHTML = `<div class="empty-state"><div class="emo">📂</div><p>Noch keine Projekte gespeichert.<br>Erstelle dein erstes Projekt!</p></div>`;
    return;
  }
  list.innerHTML = projects.map((p,i) => `
    <div class="project-row" onclick="loadProject(${i})">
      <div class="project-thumb">
        ${p.vizImages && p.vizImages[0] ? `<img src="${p.vizImages[0]}" alt="" />` : `<div style="width:100%;height:100%;display:flex;align-items:center;justify-content:center;font-size:1.5rem">🏠</div>`}
      </div>
      <div class="project-info">
        <div class="project-name">${p.objekt || 'Unbekanntes Objekt'}</div>
        <div class="project-meta">${p.kunde || '–'} · ${p.schadensart || '–'} · ${new Date(p.date).toLocaleDateString('de-DE')} · ${p.employee?.name || '–'}</div>
      </div>
      <div class="project-status ${p.vizImages?.length ? 'status-done' : 'status-new'}">${p.vizImages?.length ? '✓ Visualisiert' : 'Neu'}</div>
    </div>`).join('');
}
function getProjects() {
  try { return JSON.parse(localStorage.getItem('wiegand_projects') || '[]'); } catch { return []; }
}
function loadProject(i) {
  const p = getProjects()[i];
  if (!p) return;
  document.getElementById('objekt').value = p.objekt || '';
  document.getElementById('auftraggeber').value = p.kunde || '';
  if (p.analysis) {
    lastAnalysis = p.analysis;
    document.getElementById('resultContent').innerHTML = formatMD(lastAnalysis);
    document.getElementById('resultCard').classList.add('on');
  }
  showView('neu');
  toast('📂 Projekt geladen: ' + (p.objekt || 'Unbekannt'));
}

// ═══════════════════════════════════════════════════════
//  HELPERS
// ═══════════════════════════════════════════════════════
function showErr(msg) {
  const el = document.getElementById('errBar');
  el.textContent = '❌ ' + msg; el.className = 'err-bar on';
  setTimeout(() => el.className = 'err-bar', 7000);
}
function hideErr() { document.getElementById('errBar').className = 'err-bar'; }
function toast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg; t.classList.add('on');
  setTimeout(() => t.classList.remove('on'), 3200);
}
function formatMD(text) {
  return text
    .replace(/### (.+)/g,'<h3>$1</h3>')
    .replace(/## (.+)/g,'<h3>$1</h3>')
    .replace(/\*\*(.+?)\*\*/g,'<strong>$1</strong>')
    .replace(/\*(.+?)\*/g,'<em>$1</em>')
    .replace(/^- (.+)/gm,'• $1')
    .replace(/\n/g,'<br>');
}
function setMainStep(n) {
  const labels = ['Sprache transkribieren','Fotos analysieren','Sanierungskonzept erstellen','Kostenschätzung berechnen'];
  for (let i=1;i<=4;i++) {
    const el = document.getElementById('step'+i);
    if (!el) continue;
    if (i<n) { el.className='step done'; el.textContent='✅ '+labels[i-1]; }
    else if (i===n) { el.className='step active'; el.textContent='🔄 '+labels[i-1]; }
    else { el.className='step'; el.textContent='⏳ '+labels[i-1]; }
  }
}
function setVizStep(n) {
  const labels = ['Raumtyp & Perspektive analysieren','Foto vorbereiten','Sanierungsbild generieren'];
  for (let i=1;i<=3;i++) {
    const el = document.getElementById('vstep'+i);
    if (!el) continue;
    const lbl = 'Schritt '+i+': '+labels[i-1];
    if (i<n) { el.className='viz-step done'; el.textContent='✅ '+lbl; }
    else if (i===n) { el.className='viz-step active'; el.textContent='🔄 '+lbl; }
    else { el.className='viz-step'; el.textContent='⏳ '+lbl; }
  }
}
function setWizStep(n) {
  const labels = ['Raum & Elemente analysieren','Foto für KI vorbereiten','Verbesserungsbild generieren'];
  for (let i=1;i<=3;i++) {
    const el = document.getElementById('wstep'+i);
    if (!el) continue;
    const lbl = 'Schritt '+i+': '+labels[i-1];
    if (i<n) { el.className='viz-step done'; el.textContent='✅ '+lbl; }
    else if (i===n) { el.className='viz-step active'; el.textContent='🔄 '+lbl; }
    else { el.className='viz-step'; el.textContent='⏳ '+lbl; }
  }
}
function setR3DStep(n) {
  for (let i=1;i<=6;i++) {
    const el = document.getElementById('r3dst'+i);
    if (!el) continue;
    const text = el.textContent.replace(/^[○◉●] /,'');
    if (i<n) { el.className='r3d-step r3d-step-done'; el.textContent='● '+text; }
    else if (i===n) { el.className='r3d-step r3d-step-active'; el.textContent='◉ '+text; }
    else { el.className='r3d-step'; el.textContent='○ '+text; }
  }
}

// ═══════════════════════════════════════════════════════
//  PHOTOS
// ═══════════════════════════════════════════════════════
function handleFiles(input) {
  const files = Array.from(input.files);
  if (!files.length) return;
  let loaded = 0;
  files.forEach(file => {
    const reader = new FileReader();
    reader.onload = e => {
      photos.push({base64: e.target.result.split(',')[1], full: e.target.result, name: file.name});
      if (++loaded === files.length) renderPhotos();
    };
    reader.readAsDataURL(file);
  });
}
function renderPhotos() {
  vizHistory=[]; cachedEditPrompt=''; cachedRoomType='Raum'; cachedRoomAnalysis=null; var _ex=document.getElementById('extra3dWrap'); if(_ex) _ex.style.display='none'; var _b3=document.getElementById('btn3dRoom'); if(_b3) _b3.style.display='none';
  wizResults=[]; wizCachedAnalysis=[]; wizCachedTypes=[]; globalWizStyle=null; wizPrefilledFeedback=[];
  document.getElementById('vizResult').style.display='none';
  document.getElementById('wizardCard').style.display='none';
  const regen = document.getElementById('vizRegenBtn');
  if (regen) regen.style.display='none';
  document.getElementById('startWizardBtn').style.display = photos.length ? 'flex':'none';
  // Zeige 3D-Button sobald Fotos vorhanden (kein Viz nötig)
  const b3d = document.getElementById('btn3dRoom');
  const msw = document.getElementById('modelSelectWrap');
  if (b3d && photos.length) { b3d.style.display='inline-flex'; var ex3d=document.getElementById('extra3dWrap'); if(ex3d) ex3d.style.display='block'; }
  if (msw && photos.length) { msw.style.display='flex'; }
  const grid = document.getElementById('photosGrid');
  grid.innerHTML = photos.map((p,i) => `
    <div class="photo-thumb">
      <img src="${p.full}" alt="${p.name}"/>
      <button class="del" onclick="removePhoto(${i})">×</button>
      ${i===0?'<span class="main-badge">Haupt</span>':''}
    </div>`).join('');
  document.getElementById('photosCount').textContent = photos.length ? `✓ ${photos.length} Foto${photos.length>1?'s':''} geladen`:'';
  document.getElementById('fileInput').value='';
}
function removePhoto(i) { photos.splice(i,1); renderPhotos(); }

// ═══════════════════════════════════════════════════════
//  AUDIO – HAUPTAUFNAHME
// ═══════════════════════════════════════════════════════
async function toggleRec() {
  if (!isRec) {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({audio:true});
      const mime = MediaRecorder.isTypeSupported('audio/webm;codecs=opus')?'audio/webm;codecs=opus':'audio/webm';
      mRec = new MediaRecorder(stream, {mimeType:mime});
      aChunks = [];
      mRec.ondataavailable = e => { if(e.data.size>0) aChunks.push(e.data); };
      mRec.onstop = () => {
        audBlob = new Blob(aChunks, {type:mime});
        document.getElementById('audioPlayer').src = URL.createObjectURL(audBlob);
        document.getElementById('audioPrev').style.display = 'block';
        stream.getTracks().forEach(t=>t.stop());
      };
      mRec.start(500); isRec=true; recSeconds=0;
      document.getElementById('recBtn').textContent='⏹';
      document.getElementById('recBtn').classList.add('recording');
      document.getElementById('recStatus').textContent='● Aufnahme';
      document.getElementById('recStatus').classList.add('active');
      document.getElementById('recTimer').style.display='block';
      timerInterval = setInterval(()=>{
        recSeconds++;
        const m=Math.floor(recSeconds/60),s=recSeconds%60;
        document.getElementById('recTimer').textContent=`${m}:${String(s).padStart(2,'0')}`;
        document.getElementById('progressFill').style.width=Math.min((recSeconds/120)*100,100)+'%';
      },1000);
    } catch(e) { showErr('Mikrofon-Zugriff verweigert. Bitte Berechtigung im Browser erteilen.'); }
  } else {
    mRec.stop(); isRec=false; clearInterval(timerInterval);
    document.getElementById('recBtn').textContent='🎙️';
    document.getElementById('recBtn').classList.remove('recording');
    document.getElementById('recStatus').textContent='✓ Gespeichert';
    document.getElementById('recStatus').classList.remove('active');
  }
}

// ═══════════════════════════════════════════════════════
//  MODES
// ═══════════════════════════════════════════════════════
function toggleMode(m) {
  const el = document.getElementById('m-'+m);
  if (modes.has(m)) { modes.delete(m); el.classList.remove('on'); }
  else { modes.add(m); el.classList.add('on'); }
}

// ═══════════════════════════════════════════════════════
//  HAUPTANALYSE
// ═══════════════════════════════════════════════════════
const SYSTEM_PROMPT = `Du bist ein erfahrener Bausachverständiger und Sanierungsspezialist bei Gebäudetrocknung Wiegand GbR in Eschwege (Werra-Meißner-Kreis). Meisterbetrieb, 30+ Jahre Erfahrung, Kooperationspartner von Versicherungen und Behörden.

AKTUELLE MARKTPREISE (Stand 2025, Deutschland):

GEBÄUDETROCKNUNG & WASSERSCHADEN:
– Technische Trocknung (einfach): 3–8 €/m²
– Technische Trocknung (aufwändig, Hohlräume): 8–12 €/m²
– Putz entfernen & entsorgen: 10–30 €/m²
– Schimmelsanierung Oberfläche: 200–500 €/m²
– Schimmelsanierung tiefgehend: 500–1.200 €/m²
– Estrichtrocknung: 15–40 €/m²
– Leckortung & Diagnose: 80–250 €/Einsatz
– Gerätemiete Kondensationstrockner: 40–80 €/Tag
– Gesamtkosten klein (10–20 m²): 400–800 €
– Gesamtkosten mittel (bis 50 m²): 1.000–2.500 €
– Gesamtkosten groß/komplex: 3.000–15.000 €
– Handwerkerstunde (Sanierung): 55–75 €/h zzgl. 19% MwSt

BADSANIERUNG:
– Standard: 1.000–1.500 €/m²
– Gehoben: 1.800–2.500 €/m²
– Luxus: 3.000–4.500 €/m²
– Bad 8 m² Standard gesamt: 8.000–12.000 €
– Bad 8 m² gehoben gesamt: 14.000–20.000 €

FÖRDERUNGEN:
– KfW 455-B: Barrierefreier Umbau bis 6.250 € Zuschuss
– Pflegekasse (ab Pflegegrad 1): bis 4.000 € einmalig
– Steuerbonus: 20% der Handwerkerkosten, max. 1.200 €/Jahr (§ 35a EStG)

Antworte direkt, handlungsorientiert, mit €-Werten. Formatierung: ### für Abschnitte, **fett** für kritische Punkte, Listen mit –. Antworte ausschließlich auf Deutsch.`;

const IDW_S6_KONZEPT_PROMPT = `Erstelle ein vollständiges 2-seitiges Bausanierungskonzept nach IDW S 6 Standard für dieses Objekt.

Das Konzept muss ALLE folgenden Pflichtbestandteile nach IDW S 6 enthalten:

**I. AUFTRAGSANGABEN & AUSGANGSLAGE**
- Auftraggeber, Objekt, Datum, Bearbeiter
- Rechtliche und bauliche Ausgangssituation
- Kurze Beschreibung des Ist-Zustands

**II. SCHADENSANALYSE & KRISENURSACHEN**
- Sichtbare und vermutete Schadensursachen
- Schadensausmaß (Fläche, Tiefe, Schweregrad)
- Krisenstadium: akut / fortschreitend / stabilisiert
- Gefährdungsbeurteilung (Gesundheit, Statik, Folgeschäden)

**III. STRATEGISCHES SANIERUNGSLEITBILD**
- Zielzustand nach Abschluss der Sanierung
- Nachhaltigkeitsziele (Schimmelprävention, Feuchteregulierung)
- Wettbewerbsfähigkeit / Werterhalt der Immobilie

**IV. SANIERUNGSMASSNAHMEN (Schritt-für-Schritt)**
- Sofortmaßnahmen (binnen 24–48h)
- Kurzfristige Maßnahmen (Woche 1–2)
- Mittelfristige Maßnahmen (Woche 3–8)
- Geräte, Materialien, Normen (DIN 68800, WTA-Merkblätter)
- Verantwortliche Gewerke

**V. INTEGRIERTE SANIERUNGS-PLANUNG**
- Zeitplan mit konkreten Meilensteinen
- Ressourcenplanung (Personal, Geräte)
- Qualitätssicherungsmaßnahmen & Kontrollpunkte
- Dokumentationspflichten (Feuchteprotokolle, Fotodokumentation)

**VI. KOSTENSCHÄTZUNG & FINANZIERUNG**
- Aufschlüsselung nach Gewerken (Netto + 19% MwSt = Brutto)
- Gesamtkosten mit Unter- und Obergrenze
- Versicherungsleistung / Eigenanteil
- Fördermöglichkeiten (KfW, Pflegekasse, §35a EStG)

**VII. RISIKOBEURTEILUNG & HANDLUNGSEMPFEHLUNGEN**
- Risiken bei Nicht-Sanierung oder verzögerter Sanierung
- Empfehlung zur Sanierungsfähigkeit (positiv/bedingt/negativ)
- Abschließende Beurteilung durch Sachverständigen

**VIII. SCHLUSSBEMERKUNG (IDW S 6 Pflicht)**
„Dieses Sanierungskonzept wurde nach den Grundsätzen des IDW Standards für Sanierungskonzepte (IDW S 6, Fassung 2023) erstellt. Die Ausführungen basieren auf dem Grundsatz der Wesentlichkeit und der zum Zeitpunkt der Erstellung vorliegenden Informationen."

Formatiere das Konzept professionell mit ### Überschriften. Schreibe konkret, handlungsorientiert, mit echten €-Werten. Umfang: ca. 600–800 Wörter (entspricht 2 DIN-A4-Seiten).`;

async function runAnalyse() {
  const key = document.getElementById('openaiKey').value.trim();
  if (!key) { showErr('Bitte OpenAI API Key eingeben.'); return; }
  if (!photos.length && !audBlob && !document.getElementById('manualDesc').value.trim()) {
    showErr('Bitte Foto, Sprachaufnahme oder Textbeschreibung bereitstellen.'); return;
  }
  hideErr();
  document.getElementById('analyzeBtn').disabled = true;
  document.getElementById('mainLoading').classList.add('on');
  document.getElementById('resultCard').classList.remove('on');
  transcript = '';

  setMainStep(1);
  if (audBlob) {
    try {
      const fd = new FormData();
      fd.append('file', audBlob, 'aufnahme.webm');
      fd.append('model','whisper-1'); fd.append('language','de');
      const r = await fetch('https://api.openai.com/v1/audio/transcriptions',{method:'POST',headers:{'Authorization':'Bearer '+key},body:fd});
      if (r.ok) { const d=await r.json(); if(d.text){transcript=d.text;document.getElementById('transcriptText').textContent=transcript;document.getElementById('transcriptBox').classList.add('on');}}
    } catch(e) { console.warn('Transkription fehlgeschlagen:',e); }
  }

  setMainStep(2);
  const obj      = document.getElementById('objekt').value||'Unbekannt';
  const kunde    = document.getElementById('auftraggeber').value||'Unbekannt';
  const schaden  = document.getElementById('schadensart').value||'Unbekannt';
  const flaeche  = document.getElementById('flaeche').value||'unbekannt';
  const vers     = document.getElementById('versicherung').value||'';
  const budget   = document.getElementById('budget').value||'';
  const manualDesc = document.getElementById('manualDesc').value.trim();
  const tech     = currentEmployee ? currentEmployee.name+' ('+currentEmployee.role+')' : 'Wiegand Team';
  const datum    = new Date().toLocaleDateString('de-DE');

  let userText = `Analysiere diesen Gebäudeschaden.\n\n**Auftragsdaten:**\n- Objekt: ${obj}\n- Auftraggeber: ${kunde}\n- Datum: ${datum}\n- Schadensart: ${schaden}\n- Fläche: ${flaeche} m²\n- Versicherungsfall: ${vers}\n- Bearbeiter: ${tech}\n${budget?'- Kostenziel: '+budget+' €\n':''}${transcript?'\n**Sprachbeschreibung:**\n'+transcript+'\n':''}${manualDesc?'\n**Schriftliche Beschreibung:**\n'+manualDesc+'\n':''}\nErstelle:`;
  if (modes.has('schaden')) userText += `\n\n### Schadensbeurteilung\n- Sichtbare Schäden, Ursache, Ausmaß, Sofortmaßnahmen`;
  if (modes.has('konzept')) userText += `\n\n${IDW_S6_KONZEPT_PROMPT}`;
  if (modes.has('kosten'))  userText += `\n\n### Kostenschätzung\n- Aufschlüsselung in €, Netto + MwSt = Brutto`;
  if (modes.has('foerder')) userText += `\n\n### Förderung & Versicherung\n- Relevante Hinweise und Empfehlungen`;

  const parts = [{type:'text',text:userText}];
  for (let i=0;i<Math.min(photos.length,4);i++) {
    parts.push({type:'image_url',image_url:{url:'data:image/jpeg;base64,'+photos[i].base64,detail:'high'}});
  }

  setMainStep(3);
  try {
    const resp = await fetch('https://api.openai.com/v1/chat/completions',{
      method:'POST',
      headers:{'Content-Type':'application/json','Authorization':'Bearer '+key},
      body: JSON.stringify({model:'gpt-4o',max_tokens:4000,messages:[{role:'system',content:SYSTEM_PROMPT},{role:'user',content:parts}]})
    });
    const data = await resp.json();
    if (!resp.ok) throw new Error(data.error?.message||'API Fehler '+resp.status);
    setMainStep(4);
    await new Promise(r=>setTimeout(r,400));
    lastAnalysis = data.choices[0].message.content;
    document.getElementById('resultContent').innerHTML = formatMD(lastAnalysis);
    document.getElementById('resultCard').classList.add('on');
    document.getElementById('resultCard').scrollIntoView({behavior:'smooth'});
    toast('✅ Analyse abgeschlossen');
  } catch(e) { showErr('Analyse-Fehler: '+e.message); }
  document.getElementById('mainLoading').classList.remove('on');
  document.getElementById('analyzeBtn').disabled = false;
}

// ═══════════════════════════════════════════════════════
//  RENO REC
// ═══════════════════════════════════════════════════════
async function toggleRenoRec() {
  if (!renoIsRec) {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({audio:true});
      const mime = MediaRecorder.isTypeSupported('audio/webm;codecs=opus')?'audio/webm;codecs=opus':'audio/webm';
      renoMRec = new MediaRecorder(stream,{mimeType:mime});
      renoAChunks=[];
      renoMRec.ondataavailable=e=>{if(e.data.size>0)renoAChunks.push(e.data);};
      renoMRec.onstop=()=>{renoAudBlob=new Blob(renoAChunks,{type:mime});stream.getTracks().forEach(t=>t.stop());document.getElementById('renoRecBtn').textContent='✓';};
      renoMRec.start(500); renoIsRec=true;
      document.getElementById('renoRecBtn').textContent='⏹';
      document.getElementById('renoRecBtn').classList.add('recording');
    } catch(e){showErr('Mikrofon-Zugriff verweigert.');}
  } else {
    renoMRec.stop(); renoIsRec=false;
    document.getElementById('renoRecBtn').classList.remove('recording');
  }
}

// ═══════════════════════════════════════════════════════
//  ROOM ANALYSIS PROMPT
// ═══════════════════════════════════════════════════════
const ROOM_ANALYSIS_PROMPT = `You are an expert interior architect. Analyze this room photo precisely.
Return ONLY valid JSON, no markdown:
{"roomType":"German noun e.g. Badezimmer","cameraAngle":"height from floor in cm, direction, tilt","fixedElements":"all fixtures with exact wall placement","fixtureSizes":"each element as % of wall area","surfaceCondition":"current damage/condition","renovationMaterials":"specific modern renovation materials"}`;

// ═══════════════════════════════════════════════════════
//  VIZ
// ═══════════════════════════════════════════════════════
function buildEditPrompt(a, materials, inspStyle=null, globalStyle=null) {
  const parts = [
    `RENOVATION VISUALIZATION: Transform this exact photo into a photorealistic modern renovation.`,
    `CAMERA LOCK (absolute): ${a.cameraAngle||'same viewpoint as source'}.`,
    `POSITION LOCK (absolute): ${a.fixedElements||'all elements at original positions'}.`,
    `SIZE LOCK (absolute): ${a.fixtureSizes||'all elements at original scale'}.`,
    `APPLY ONLY: Replace surface finishes. ${materials}`
  ];
  if (globalStyle) parts.push(`COLOR CONSISTENCY: Same bathroom series. Use EXACTLY: ${globalStyle}. No different colors.`);
  if (inspStyle) parts.push(`STYLE INSPIRATION: ${inspStyle}`);
  parts.push(`FORBIDDEN: Moving, resizing, adding or removing elements. No geometry changes.`);
  parts.push(`OUTPUT: Photorealistic interior. Professional lighting. No damage, no stains.`);
  return parts.join(' ');
}

async function getBoardDescription(key) {
  if (boardStyleCache) return boardStyleCache;
  const chips = [...boardChipsSet].join(', ');
  const text = document.getElementById('boardText')?.value?.trim()||'';
  let audioText = '';
  const tEl = document.getElementById('boardTranscript');
  if (tEl&&tEl.style.display!=='none') audioText = tEl.textContent.replace('🎙️ Transkript:','').trim();
  const userText = [chips,text,audioText].filter(Boolean).join('. ');
  const images = boardSlots.filter(s=>s!==null);
  if (!images.length&&!userText) return null;
  if (!images.length) { boardStyleCache=userText; return boardStyleCache; }
  try {
    const mc=[];
    const slotLabels=['bathroom reference','tile pattern','washbasin','fixtures'];
    images.forEach((img,idx)=>{
      const origIdx=boardSlots.indexOf(img);
      mc.push({type:'image_url',image_url:{url:'data:image/jpeg;base64,'+img.base64,detail:'low'}});
      mc.push({type:'text',text:`Image shows: ${slotLabels[origIdx]||'style reference'}`});
    });
    const hint = userText?`User style: "${userText}". `:'';
    mc.push({type:'text',text:`${hint}Describe the combined interior design style in 2-3 sentences for an AI image prompt. Include: exact colors, tile formats, fixture finishes, material names. Be specific. English only. Max 150 words.`});
    const r=await fetch('https://api.openai.com/v1/chat/completions',{method:'POST',headers:{'Content-Type':'application/json','Authorization':'Bearer '+key},body:JSON.stringify({model:'gpt-4o',temperature:0,max_tokens:200,messages:[{role:'user',content:mc}]})});
    const d=await r.json();
    if(r.ok&&d.choices?.[0]){const desc=d.choices[0].message.content.trim();boardStyleCache=userText?desc+' '+userText:desc;return boardStyleCache;}
  } catch(e){console.warn('Board-Stilanalyse:',e);}
  boardStyleCache=userText||null;return boardStyleCache;
}

function resizeImagePNG(dataUrl, maxSize) {
  return new Promise((resolve,reject)=>{
    const img=new Image();
    img.onload=()=>{
      const scale=Math.min(maxSize/img.width,maxSize/img.height,1);
      const w=Math.round(img.width*scale),h=Math.round(img.height*scale);
      const c=document.createElement('canvas');c.width=w;c.height=h;
      c.getContext('2d').drawImage(img,0,0,w,h);
      c.toBlob(blob=>blob?resolve(blob):reject(new Error('PNG-Export fehlgeschlagen')),'image/png');
    };
    img.onerror=()=>reject(new Error('Bild konnte nicht geladen werden'));
    img.src=dataUrl;
  });
}

async function generateViz(skipAnalysis=false) {
  const key=document.getElementById('openaiKey').value.trim();
  if(!key){showErr('Bitte OpenAI Key eingeben.');return;}
  if(!photos.length){showErr('Bitte zuerst ein Foto hochladen.');return;}
  document.getElementById('vizBtn').disabled=true;
  const regenBtn=document.getElementById('vizRegenBtn');
  if(regenBtn)regenBtn.disabled=true;
  document.getElementById('vizLoading').classList.add('on');
  if(!vizHistory.length) document.getElementById('vizResult').style.display='none';

  let userWishes=document.getElementById('renoWishes').value.trim();
  if(renoAudBlob&&!skipAnalysis){
    try{
      const wfd=new FormData();wfd.append('file',renoAudBlob,'wuensche.webm');wfd.append('model','whisper-1');wfd.append('language','de');
      const wr=await fetch('https://api.openai.com/v1/audio/transcriptions',{method:'POST',headers:{'Authorization':'Bearer '+key},body:wfd});
      if(wr.ok){const wd=await wr.json();if(wd.text){const wt=wd.text.trim();userWishes=userWishes?userWishes+' · '+wt:wt;const el=document.getElementById('renoTranscript');el.textContent='🎙️ '+wt;el.style.display='block';}}
    }catch(e){console.warn('Wünsche-Transkription:',e);}
  }

  if(!skipAnalysis||!cachedRoomAnalysis){
    setVizStep(1);
    try{
      const r1=await fetch('https://api.openai.com/v1/chat/completions',{method:'POST',headers:{'Content-Type':'application/json','Authorization':'Bearer '+key},body:JSON.stringify({model:'gpt-4o',temperature:0,max_tokens:700,messages:[{role:'user',content:[{type:'image_url',image_url:{url:'data:image/jpeg;base64,'+photos[0].base64,detail:'high'}},{type:'text',text:ROOM_ANALYSIS_PROMPT}]}]})});
      const d1=await r1.json();if(!r1.ok)throw new Error(d1.error?.message);
      const raw=d1.choices[0].message.content.trim();const m=raw.match(/\{[\s\S]*\}/);
      if(m){const p=JSON.parse(m[0]);cachedRoomAnalysis=p;if(p.roomType){cachedRoomType=p.roomType;}}
    }catch(e){console.warn('Raumanalyse:',e);}
  }

  const inspStyle=await getBoardDescription(key);
  const a=cachedRoomAnalysis;
  let editPrompt;
  if(a){
    const mats=userWishes?`Apply ONLY: "${userWishes}". Keep rest: ${a.renovationMaterials||'modern materials'}.`:`Use: ${a.renovationMaterials||'modern high-end materials, bright and clean'}.`;
    editPrompt=buildEditPrompt(a,mats,inspStyle||null);
  } else {
    editPrompt=cachedEditPrompt||`RENOVATION VISUALIZATION. POSITION LOCK. CAMERA LOCK. Replace damaged surfaces with modern materials. ${userWishes?'User wishes: '+userWishes+'.':''} ${inspStyle?'STYLE: '+inspStyle:''} Photorealistic, bright.`;
  }
  cachedEditPrompt=editPrompt;

  setVizStep(2);
  let imageBlob;
  try{imageBlob=await resizeImagePNG(photos[0].full,1024);}
  catch(e){showErr('Bildvorbereitung: '+e.message);document.getElementById('vizLoading').classList.remove('on');document.getElementById('vizBtn').disabled=false;if(regenBtn)regenBtn.disabled=false;return;}

  setVizStep(3);
  try{
    const fd=new FormData();
    fd.append('model','gpt-image-1');fd.append('image[]',imageBlob,'room.png');
    fd.append('prompt',editPrompt);fd.append('n','1');fd.append('size','auto');fd.append('quality','high');
    const resp=await fetch('https://api.openai.com/v1/images/edits',{method:'POST',headers:{'Authorization':'Bearer '+key},body:fd});
    const data=await resp.json();
    if(!resp.ok){const e=data.error?.message||'Unbekannter Fehler';throw new Error(e+(e.includes('billing')?' – Guthaben prüfen':e.includes('key')?' – Key ungültig':''));}
    const item=data.data[0];
    const imgSrc=item.b64_json?'data:image/png;base64,'+item.b64_json:item.url;
    vizHistory.push(imgSrc);
    renderVizHistory();
    toast('✨ Visualisierung erstellt');
  }catch(e){showErr('Visualisierung: '+e.message);}
  document.getElementById('vizLoading').classList.remove('on');
  document.getElementById('vizBtn').disabled=false;
  if(regenBtn)regenBtn.disabled=false;
}

function renderVizHistory() {
  document.getElementById('vizGrid').innerHTML=`
    <div class="viz-item"><img src="${photos[0].full}" alt="Vorher"/><p>📷 IST-Zustand</p></div>
    ${vizHistory.map((s,i)=>`<div class="viz-item"><img src="${s}" alt="Vorschlag ${i+1}"/><p>✨ Vorschlag ${i+1} – ${cachedRoomType}</p></div>`).join('')}`;
  // Make sure resultCard is visible (even if analyse hasn't run yet)
  document.getElementById('resultCard').classList.add('on');
  document.getElementById('vizResult').style.display='block';
  document.getElementById('vizRegenBtn').style.display='inline-flex';
  _3dNachherSrc=vizHistory[vizHistory.length-1];
  const b3d=document.getElementById('btn3dRoom');
  if(b3d){ b3d.style.display='inline-flex'; var ex3d=document.getElementById('extra3dWrap'); if(ex3d) ex3d.style.display='block'; }
  const msw=document.getElementById('modelSelectWrap');
  if(msw){msw.style.display='flex';}
  document.getElementById('resultCard').classList.add('on');
  document.getElementById('vizResult').scrollIntoView({behavior:'smooth'});
}

// ═══════════════════════════════════════════════════════
//  WIZARD
// ═══════════════════════════════════════════════════════
function updateWizProgress() {
  const total=photos.length;
  document.getElementById('wizProgressText').textContent=`Foto ${wizIndex+1} von ${total}`;
  document.getElementById('wizDots').innerHTML=photos.map((_,i)=>`<div class="wdot ${i<wizIndex?'done':i===wizIndex?'active':''}"></div>`).join('');
  const isLast=wizIndex===photos.length-1;
  document.getElementById('wizNextBtn').style.display=isLast?'none':'inline-flex';
  document.getElementById('wizDoneBtn').style.display=isLast?'inline-flex':'none';
}

async function startWizard() {
  const key=document.getElementById('openaiKey').value.trim();
  if(!key){showErr('Bitte OpenAI Key eingeben.');return;}
  if(!photos.length){showErr('Bitte Fotos hochladen.');return;}
  wizIndex=0;
  wizCachedAnalysis=new Array(photos.length).fill(null);
  wizCachedTypes=new Array(photos.length).fill('Raum');
  wizPrefilledFeedback=new Array(photos.length).fill('');
  wizResults=[]; globalWizStyle=null; wizFirstResultSrc=null;
  document.getElementById('wizardCard').style.display='block';
  document.getElementById('wizSummary').style.display='none';
  document.getElementById('wizResult').style.display='none';
  document.getElementById('wizardCard').scrollIntoView({behavior:'smooth'});
  await generateWizPhoto(0);
}

async function generateWizPhoto(index, feedback='') {
  const key=document.getElementById('openaiKey').value.trim();
  if(!key){showErr('Bitte OpenAI Key eingeben.');return;}
  wizIndex=index;
  const ef=feedback||wizPrefilledFeedback[index]||'';
  updateWizProgress();
  document.getElementById('wizLoading').classList.add('on');
  document.getElementById('wizResult').style.display='none';
  document.getElementById('wizFeedback').value=ef;

  if(!wizCachedAnalysis[index]){
    setWizStep(1);
    try{
      const r1=await fetch('https://api.openai.com/v1/chat/completions',{method:'POST',headers:{'Content-Type':'application/json','Authorization':'Bearer '+key},body:JSON.stringify({model:'gpt-4o',temperature:0,max_tokens:700,messages:[{role:'user',content:[{type:'image_url',image_url:{url:'data:image/jpeg;base64,'+photos[index].base64,detail:'high'}},{type:'text',text:ROOM_ANALYSIS_PROMPT}]}]})});
      const d1=await r1.json();if(!r1.ok)throw new Error(d1.error?.message);
      const raw=d1.choices[0].message.content.trim();const m=raw.match(/\{[\s\S]*\}/);
      if(m){const p=JSON.parse(m[0]);wizCachedAnalysis[index]=p;if(p.roomType)wizCachedTypes[index]=p.roomType;if(index===0&&p.renovationMaterials)globalWizStyle=p.renovationMaterials;}
    }catch(e){console.warn('Wiz-Analyse:',e);}
  }

  const gStyle=index>0?globalWizStyle:null;
  const a=wizCachedAnalysis[index];
  const inspStyle=await getBoardDescription(key);
  let editPrompt;
  if(a){
    const mats=ef?`Apply ONLY: "${ef}". Keep rest: ${a.renovationMaterials||'modern materials'}.`:`Use: ${a.renovationMaterials||'modern high-end materials, bright and clean'}.`;
    editPrompt=buildEditPrompt(a,mats,inspStyle||null,gStyle);
  } else {
    editPrompt=ef?`POSITION LOCK. Apply ONLY: "${ef}". Photorealistic.`:`POSITION LOCK. Modern renovation, same camera angle. Photorealistic.`;
  }

  setWizStep(2);
  let imageBlob;
  try{imageBlob=await resizeImagePNG(photos[index].full,1024);}
  catch(e){showErr('Bild-Vorbereitung: '+e.message);document.getElementById('wizLoading').classList.remove('on');return;}

  setWizStep(3);
  try{
    const fd=new FormData();
    fd.append('model','gpt-image-1');fd.append('image[]',imageBlob,'room.png');
    if(index>0&&wizFirstResultSrc){
      try{const rb=await resizeImagePNG(wizFirstResultSrc,1024);fd.append('image[]',rb,'style_ref.png');}catch(e){console.warn('Ref-Bild:',e);}
    }
    const sbPrimary=boardSlots.find(s=>s!==null);
    if(sbPrimary){try{const sb=await resizeImagePNG(sbPrimary.full,1024);fd.append('image[]',sb,'wunschbad.png');}catch(e){console.warn('Board-Bild:',e);}}
    fd.append('prompt',editPrompt);fd.append('n','1');fd.append('size','auto');fd.append('quality','high');
    const resp=await fetch('https://api.openai.com/v1/images/edits',{method:'POST',headers:{'Authorization':'Bearer '+key},body:fd});
    const data=await resp.json();
    if(!resp.ok)throw new Error(data.error?.message||'Fehler');
    const item=data.data[0];
    const imgSrc=item.b64_json?'data:image/png;base64,'+item.b64_json:item.url;
    wizResults[index]={original:photos[index].full,result:imgSrc,roomType:wizCachedTypes[index]||'Raum'};
    if(index===0)wizFirstResultSrc=imgSrc;
    const roomType=wizCachedTypes[index]||'Raum';
    document.getElementById('wizGrid').innerHTML=`
      <div class="viz-item"><img src="${photos[index].full}" alt="Vorher"/><p>📷 IST-Zustand – Foto ${index+1}</p></div>
      <div class="viz-item"><img src="${imgSrc}" alt="Vorschlag"/><p>✨ Verbesserungsvorschlag – ${roomType}</p></div>`;
    const descParts=[];
    if(a?.surfaceCondition)descParts.push(`<strong>Ausgangszustand:</strong> ${a.surfaceCondition}`);
    if(a?.renovationMaterials)descParts.push(`<strong>Materialien:</strong> ${a.renovationMaterials}`);
    if(ef)descParts.push(`<strong>Umgesetzte Änderungen:</strong> „${ef}"`);
    document.getElementById('wizDesc').innerHTML=descParts.join('<br>') || `Renovierungsvorschlag für ${roomType} generiert.`;
    // Set 3D source so user can open 3D immediately from this result
    _3dNachherSrc = imgSrc;
    // Show 3D button in wizard step
    const ws3d = document.getElementById('wizStep3DBtn');
    if(ws3d) ws3d.style.display='inline-flex';
    document.getElementById('wizResult').style.display='block';
    document.getElementById('wizResult').scrollIntoView({behavior:'smooth'});
  }catch(e){showErr('Wizard-Fehler: '+e.message);}
  document.getElementById('wizLoading').classList.remove('on');
}

async function regenWiz() { await generateWizPhoto(wizIndex, document.getElementById('wizFeedback').value.trim()); }
async function nextWiz() { if(wizIndex<photos.length-1) await generateWizPhoto(wizIndex+1); }
function finishWiz() {
  document.getElementById('wizSummaryGrid').innerHTML=wizResults.map((r,i)=>`
    <div class="viz-item"><img src="${r.original}" alt="Vorher"/><p>📷 Vorher – Foto ${i+1}</p></div>
    <div class="viz-item"><img src="${r.result}" alt="Nachher"/><p>✨ Nachher – ${r.roomType}</p></div>`).join('');
  document.getElementById('wizResult').style.display='none';
  document.getElementById('wizLoading').classList.remove('on');
  document.getElementById('wizSummary').style.display='block';
  // Set 3D source to last wizard result and show 3D button
  if(wizResults.length) {
    _3dNachherSrc = wizResults[wizResults.length-1].result;
    const b3d = document.getElementById('btn3dRoom');
    if(b3d){ b3d.style.display='inline-flex'; var ex3d2=document.getElementById('extra3dWrap'); if(ex3d2) ex3d2.style.display='block'; }
    const rc = document.getElementById('resultCard');
    if(rc) rc.classList.add('on');
    // Show 3D button in wizard summary too
    document.getElementById('wizSummary3DBtn').style.display='inline-flex';
  }
  document.getElementById('wizSummary').scrollIntoView({behavior:'smooth'});
}

// ═══════════════════════════════════════════════════════
//  BOARD
// ═══════════════════════════════════════════════════════
function handleBoardFile(input, i) {
  const file=input.files[0];if(!file)return;
  const reader=new FileReader();
  reader.onload=e=>{
    boardSlots[i]={base64:e.target.result.split(',')[1],full:e.target.result,name:file.name};
    renderBoardSlot(i);boardStyleCache=null;updateBoardBadge();
  };
  reader.readAsDataURL(file);input.value='';
}
function renderBoardSlot(i) {
  const el=document.getElementById('bslot'+i);if(!el)return;
  const labels=['Traumbad 🛁','Fliesen 🔲','Waschbecken 🪣','Armaturen 🚿'];
  const icons=['🛁','🔲','🪣','🚿'];
  if(!boardSlots[i]){
    el.classList.remove('has-img');
    el.innerHTML=`<input type="file" id="bsi${i}" accept="image/*" style="display:none" onchange="handleBoardFile(this,${i})"><div class="slot-icon">${icons[i]}</div><div class="slot-lbl">${labels[i]}</div>`;
    el.onclick=()=>document.getElementById('bsi'+i).click();
  }else{
    el.classList.add('has-img');
    el.innerHTML=`<img src="${boardSlots[i].full}" alt="Slot ${i}"/><button class="slot-del" onclick="event.stopPropagation();removeBoardSlot(${i})">✕</button>`;
    el.onclick=null;
  }
}
function removeBoardSlot(i){boardSlots[i]=null;renderBoardSlot(i);boardStyleCache=null;updateBoardBadge();}
function toggleChip(el,value){
  if(boardChipsSet.has(value)){boardChipsSet.delete(value);el.classList.remove('active');}
  else{boardChipsSet.add(value);el.classList.add('active');}
  boardStyleCache=null;updateBoardBadge();
}
function updateBoardBadge(){
  const text=document.getElementById('boardText')?.value?.trim()||'';
  const hasAny=boardSlots.some(s=>s)||boardChipsSet.size>0||text||boardAudBlob;
  const badge=document.getElementById('boardActiveBadge');
  if(badge)badge.style.display=hasAny?'block':'none';
}
async function toggleBoardRec(){
  const btn=document.getElementById('boardRecBtn'),status=document.getElementById('boardRecStatus'),timer=document.getElementById('boardRecTimer');
  if(!boardIsRec){
    navigator.mediaDevices.getUserMedia({audio:true}).then(stream=>{
      boardMRec=new MediaRecorder(stream);boardAChunks=[];
      boardMRec.ondataavailable=e=>{if(e.data.size>0)boardAChunks.push(e.data);};
      boardMRec.onstop=async()=>{
        boardAudBlob=new Blob(boardAChunks,{type:'audio/webm'});
        const key=document.getElementById('openaiKey').value.trim();
        if(key){
          try{
            const fd=new FormData();fd.append('file',boardAudBlob,'wunsch.webm');fd.append('model','whisper-1');fd.append('language','de');
            const r=await fetch('https://api.openai.com/v1/audio/transcriptions',{method:'POST',headers:{'Authorization':'Bearer '+key},body:fd});
            const d=await r.json();
            if(r.ok&&d.text){const t=document.getElementById('boardTranscript');t.style.display='block';t.innerHTML='🎙️ Transkript: '+d.text;}
          }catch(e){console.warn('Board-Transcription:',e);}
        }
        updateBoardBadge();status.textContent='✓ Gespeichert';btn.textContent='🎙️';btn.classList.remove('recording');
      };
      boardMRec.start();boardIsRec=true;boardRecSeconds=0;
      timer.style.display='inline';btn.textContent='⏹';btn.classList.add('recording');status.textContent='● Aufnahme...';
      boardTimerInterval=setInterval(()=>{boardRecSeconds++;const m=Math.floor(boardRecSeconds/60),s=boardRecSeconds%60;timer.textContent=m+':'+(s<10?'0':'')+s;},1000);
    }).catch(()=>showErr('Mikrofon-Zugriff verweigert'));
  }else{
    boardMRec.stop();boardMRec.stream?.getTracks().forEach(t=>t.stop());
    boardIsRec=false;clearInterval(boardTimerInterval);timer.style.display='none';
  }
}

// ═══════════════════════════════════════════════════════
//  VIDEO
// ═══════════════════════════════════════════════════════
function extractFrames(file,count=6){
  return new Promise((resolve,reject)=>{
    const video=document.createElement('video'),url=URL.createObjectURL(file);
    video.src=url;video.muted=true;video.preload='auto';
    video.addEventListener('loadedmetadata',()=>{
      const dur=video.duration;
      if(!dur||dur===Infinity){URL.revokeObjectURL(url);return reject(new Error('Video-Dauer nicht ermittelbar.'));}
      const frames=[],canvas=document.createElement('canvas'),ctx=canvas.getContext('2d');
      let cap=0;
      video.addEventListener('seeked',function onSeeked(){
        canvas.width=video.videoWidth;canvas.height=video.videoHeight;ctx.drawImage(video,0,0);
        const dl=canvas.toDataURL('image/jpeg',0.85);
        frames.push({base64:dl.split(',')[1],full:dl,name:`frame_${cap+1}.jpg`});
        cap++;
        if(cap<count){video.currentTime=Math.min((dur/(count-1))*cap,dur-0.1);}
        else{video.removeEventListener('seeked',onSeeked);URL.revokeObjectURL(url);resolve(frames);}
      });
      video.currentTime=0;
    });
    video.addEventListener('error',()=>{URL.revokeObjectURL(url);reject(new Error('Video konnte nicht geladen werden.'));});
  });
}
function extractFramesAtTimes(file,timestamps){
  return new Promise(resolve=>{
    if(!timestamps||!timestamps.length){resolve([]);return;}
    const video=document.createElement('video'),url=URL.createObjectURL(file);
    video.src=url;video.muted=true;video.preload='auto';
    video.addEventListener('loadedmetadata',()=>{
      const dur=video.duration,frames=[],canvas=document.createElement('canvas'),ctx=canvas.getContext('2d');
      let cap=0;
      video.addEventListener('seeked',function onSeeked(){
        canvas.width=video.videoWidth;canvas.height=video.videoHeight;ctx.drawImage(video,0,0);
        const dl=canvas.toDataURL('image/jpeg',0.85);
        frames.push({base64:dl.split(',')[1],full:dl,name:`aenderung_frame_${cap+1}.jpg`});
        cap++;
        if(cap<timestamps.length){video.currentTime=Math.min(timestamps[cap],dur-0.1);}
        else{video.removeEventListener('seeked',onSeeked);URL.revokeObjectURL(url);resolve(frames);}
      });
      video.currentTime=Math.min(timestamps[0],dur-0.1);
    });
    video.addEventListener('error',()=>{URL.revokeObjectURL(url);resolve([]);});
  });
}
function fmtTime(sec){const m=Math.floor(sec/60),s=Math.floor(sec%60);return`${m}:${String(s).padStart(2,'0')}`;}

async function handleVideo(input){
  const file=input.files[0];if(!file)return;
  const key=document.getElementById('openaiKey').value.trim();
  document.getElementById('videoStatus').textContent='';
  document.getElementById('videoFramesGrid').innerHTML='';
  document.getElementById('videoTranscriptWrap').style.display='none';
  document.getElementById('videoChangesWrap').innerHTML='';
  videoChanges=[];input.value='';

  if(!key){
    document.getElementById('videoStatus').textContent='⚠️ Kein API-Key – nur Frames extrahiert';
    try{
      const frames=await extractFrames(file,6);
      frames.forEach(f=>photos.push(f));renderPhotos();
      document.getElementById('videoFramesGrid').innerHTML=frames.map((f,i)=>`<div class="vframe"><img src="${f.full}"/><span class="vframe-badge">Frame ${i+1}</span></div>`).join('');
      document.getElementById('videoStatus').textContent=`✅ ${frames.length} Frames extrahiert`;
    }catch(e){document.getElementById('videoStatus').textContent='❌ '+e.message;}
    return;
  }

  try{
    document.getElementById('videoStatus').textContent='🔄 Audio wird transkribiert...';
    let segments=[],fullTranscript='';
    try{
      const fd=new FormData();fd.append('file',file,file.name||'video.mp4');fd.append('model','whisper-1');fd.append('language','de');fd.append('response_format','verbose_json');fd.append('timestamp_granularities[]','segment');
      const r=await fetch('https://api.openai.com/v1/audio/transcriptions',{method:'POST',headers:{'Authorization':'Bearer '+key},body:fd});
      if(r.ok){const d=await r.json();fullTranscript=d.text||'';segments=d.segments||[];}
    }catch(e){console.warn('Whisper:',e);}

    if(fullTranscript){document.getElementById('videoTranscriptText').textContent=fullTranscript;document.getElementById('videoTranscriptWrap').style.display='block';}

    const keyword='änderung';
    const aSegs=[];
    for(let i=0;i<segments.length;i++){
      const text=(segments[i].text||'').toLowerCase();
      if(text.includes(keyword)){
        const instrText=[segments[i].text||'',segments[i+1]?(segments[i+1].text||''):'',segments[i+2]?(segments[i+2].text||''):''].join(' ').trim();
        aSegs.push({time:segments[i].start||0,text:instrText});i+=2;
      }
    }

    let changeFrames=[];
    if(aSegs.length>0){try{changeFrames=await extractFramesAtTimes(file,aSegs.map(s=>s.time));}catch(e){console.warn('Änderungs-Frames:',e);}}
    videoChanges=aSegs.map((seg,i)=>({time:seg.time,text:seg.text,frame:changeFrames[i]||null}));

    const allFrames=await extractFrames(file,6);
    allFrames.forEach(f=>photos.push(f));renderPhotos();
    document.getElementById('videoFramesGrid').innerHTML=allFrames.map((f,i)=>`<div class="vframe"><img src="${f.full}"/><span class="vframe-badge">Frame ${i+1}</span></div>`).join('');

    const parts=[`✅ ${allFrames.length} Frames extrahiert`];
    if(fullTranscript)parts.push('Audio transkribiert');
    if(aSegs.length>0)parts.push(`${aSegs.length} Änderung${aSegs.length>1?'en':''} erkannt`);
    document.getElementById('videoStatus').textContent=parts.join(' · ');

    if(videoChanges.length>0){
      document.getElementById('videoChangesWrap').innerHTML=`
        <div class="video-changes">
          <h4>🎯 ${videoChanges.length} Änderungswunsch${videoChanges.length>1?'wünsche':''} erkannt</h4>
          ${videoChanges.map(ch=>`
            <div class="vchange-item">
              ${ch.frame?`<img src="${ch.frame.full}" alt="Frame"/>`:`<div style="width:70px;height:46px;background:var(--bg3);border-radius:6px;display:flex;align-items:center;justify-content:center;font-size:1.3rem">📹</div>`}
              <div><div class="vchange-time">⏱ ${fmtTime(ch.time)} – Codewort „Änderung"</div><div class="vchange-text">${ch.text}</div></div>
            </div>`).join('')}
          <button class="btn btn-primary btn-full" style="margin-top:12px" onclick="startWizardFromVideo()">✨ Wizard für alle Änderungswünsche starten</button>
        </div>`;
    }
  }catch(e){document.getElementById('videoStatus').textContent='❌ '+e.message;showErr('Video: '+e.message);}
}

async function startWizardFromVideo(){
  const key=document.getElementById('openaiKey').value.trim();
  if(!key){showErr('Bitte OpenAI Key eingeben.');return;}
  const usable=videoChanges.filter(ch=>ch.frame);
  if(!usable.length){showErr('Keine Frames verfügbar.');return;}
  photos=usable.map(ch=>({base64:ch.frame.base64,full:ch.frame.full,name:ch.frame.name}));
  wizPrefilledFeedback=usable.map(ch=>ch.text);
  renderPhotos();
  wizIndex=0;wizCachedAnalysis=new Array(photos.length).fill(null);wizCachedTypes=new Array(photos.length).fill('Raum');wizResults=[];globalWizStyle=null;
  document.getElementById('wizardCard').style.display='block';
  document.getElementById('wizSummary').style.display='none';
  document.getElementById('wizResult').style.display='none';
  document.getElementById('wizardCard').scrollIntoView({behavior:'smooth'});
  await generateWizPhoto(0);
}

// ═══════════════════════════════════════════════════════
//  RECHNUNG / PDF / SPEICHERN
// ═══════════════════════════════════════════════════════
function previewInvoice(){
  const nr=document.getElementById('inv-nr').value||'RE-2024-001';
  const betrag=parseFloat(document.getElementById('inv-betrag').value||'0');
  const email=document.getElementById('inv-email').value||'–';
  const obj=document.getElementById('objekt').value||'Objekt';
  document.getElementById('invoicePreview').innerHTML=`
    <h4>Rechnung ${nr}</h4>
    <p><strong>Empfänger:</strong> ${email}</p>
    <p><strong>Leistung:</strong> Gebäudetrocknung & Sanierung – ${obj}</p>
    <p><strong>Betrag (brutto):</strong> ${betrag.toLocaleString('de-DE',{minimumFractionDigits:2})} €</p>
    <p><strong>Netto:</strong> ${(betrag/1.19).toLocaleString('de-DE',{minimumFractionDigits:2})} € · <strong>MwSt 19%:</strong> ${(betrag-betrag/1.19).toLocaleString('de-DE',{minimumFractionDigits:2})} €</p>`;
  document.getElementById('invoicePreview').classList.add('on');
}
function sendInvoice(){
  const email=document.getElementById('inv-email').value;
  if(!email){showErr('E-Mail fehlt.');return;}
  const nr=document.getElementById('inv-nr').value||'RE-2024-001';
  const betrag=document.getElementById('inv-betrag').value||'0';
  const obj=document.getElementById('objekt').value||'Objekt';
  const subject=encodeURIComponent(`Rechnung ${nr} – Gebäudetrocknung Wiegand`);
  const body=encodeURIComponent(`Sehr geehrte Damen und Herren,\n\nbitte überweisen Sie den Rechnungsbetrag von ${betrag} € für die Sanierungsarbeiten am Objekt „${obj}".\n\nRechnungsnummer: ${nr}\n\nMit freundlichen Grüßen\nGebäudetrocknung Wiegand\nTel: 01520 88 112 88`);
  window.location.href=`mailto:${email}?subject=${subject}&body=${body}`;
}
function copyResult(){
  navigator.clipboard.writeText(document.getElementById('resultContent').innerText)
    .then(()=>toast('📋 In Zwischenablage kopiert'))
    .catch(()=>showErr('Kopieren fehlgeschlagen'));
}
function exportPDF(){
  if(!lastAnalysis){showErr('Bitte zuerst KI-Analyse starten.');return;}
  const obj=document.getElementById('objekt').value||'Objekt';
  const kunde=document.getElementById('auftraggeber').value||'Kunde';
  const date=new Date().toLocaleDateString('de-DE');
  const vizImg=vizHistory[vizHistory.length-1]||(wizResults.length&&wizResults[wizResults.length-1]?.result)||'';
  const win=window.open('','_blank');
  const body=lastAnalysis.replace(/### (.+)/g,'<h3>$1</h3>').replace(/\*\*(.+?)\*\*/g,'<strong>$1</strong>').replace(/^- (.+)/gm,'• $1').replace(/\n/g,'<br>');
  win.document.write(`<!DOCTYPE html><html><head><meta charset=UTF-8><title>Bericht ${obj}</title><style>body{font-family:system-ui,sans-serif;max-width:800px;margin:40px auto;color:#1f2937;font-size:13px;line-height:1.7}h1{font-size:20px;color:#1a56db;border-bottom:2px solid #1a56db;padding-bottom:8px;margin-bottom:16px}h3{font-size:11px;text-transform:uppercase;letter-spacing:.06em;color:#6b7280;border-bottom:1px solid #e5e7eb;padding-bottom:3px;margin:18px 0 6px}.meta{display:grid;grid-template-columns:1fr 1fr;gap:8px 24px;background:#f9fafb;padding:14px;border-radius:8px;margin-bottom:20px;font-size:12px}.viz{text-align:center;margin:20px 0}.viz img{max-width:100%;border-radius:10px;box-shadow:0 4px 16px rgba(0,0,0,.1)}.footer{margin-top:40px;padding-top:14px;border-top:1px solid #e5e7eb;font-size:11px;color:#9ca3af;text-align:center}</style></head><body>
    <h1>Schadensgutachten · ${obj}</h1>
    <div class=meta><span><b>Objekt:</b> ${obj}</span><span><b>Auftraggeber:</b> ${kunde}</span><span><b>Datum:</b> ${date}</span><span><b>Techniker:</b> ${currentEmployee?currentEmployee.name:'–'}</span></div>
    ${vizImg?`<div class=viz><img src="${vizImg}"><p style="font-size:11px;color:#6b7280;margin-top:8px">KI-Sanierungsvorschlag</p></div>`:''}
    <div>${body}</div>
    <div class=footer>Gebäudetrocknung Wiegand GbR · Eschwege · 01520 88 112 88 · www.gebaeudetrocknung-wiegand.de</div>
    <script>window.onload=()=>window.print()<\/script></body></html>`);
  win.document.close();
}
function saveProject(){
  try{
    const proj={
      date:new Date().toISOString(),employee:currentEmployee,
      objekt:document.getElementById('objekt').value,
      kunde:document.getElementById('auftraggeber').value,
      schadensart:document.getElementById('schadensart').value,
      analysis:lastAnalysis,
      vizImages:vizHistory.slice(-1),
      wizImages:wizResults.map(r=>r.result).slice(-1)
    };
    const arr=getProjects();arr.unshift(proj);if(arr.length>50)arr.splice(50);
    localStorage.setItem('wiegand_projects',JSON.stringify(arr));
    toast('💾 Projekt gespeichert');
  }catch(e){toast('⚠ Speichern fehlgeschlagen');}
}

// ═══════════════════════════════════════════════════════
//  WORLD LABS 3D — Marble API
// ═══════════════════════════════════════════════════════
let _wlModel = 'Marble 0.1-mini'; // default: fast & günstig

function setWLModelMain(m) {
  _wlModel = m === 'plus' ? 'Marble 0.1-plus' : 'Marble 0.1-mini';
  try { localStorage.setItem('wl_model', m); } catch(e) {}
  const mini = document.getElementById('mainBtnMini');
  const plus = document.getElementById('mainBtnPlus');
  if (mini) { mini.style.background = m==='mini' ? 'var(--accent)' : 'transparent'; mini.style.color = m==='mini' ? '#fff' : 'var(--text3)'; }
  if (plus) { plus.style.background = m==='plus' ? 'var(--purple)' : 'transparent'; plus.style.color = m==='plus' ? '#fff' : 'var(--text3)'; }
  // sync overlay buttons too
  const bm = document.getElementById('r3dBtnMini'); if(bm) bm.classList.toggle('active', m==='mini');
  const bp = document.getElementById('r3dBtnPlus'); if(bp) bp.classList.toggle('active', m==='plus');
}

function setWLModel(m) {
  _wlModel = m === 'plus' ? 'Marble 0.1-plus' : 'Marble 0.1-mini';
  try { localStorage.setItem('wl_model', m); } catch(e) {}
  document.getElementById('r3dBtnMini').classList.toggle('active', m === 'mini');
  document.getElementById('r3dBtnPlus').classList.toggle('active', m === 'plus');
  toast(m === 'plus' ? '💎 Marble Plus ausgewählt (~5 Min, höchste Qualität)' : '⚡ Marble Mini ausgewählt (~45s, günstig)');
}

function initWLModel() {
  try {
    const saved = localStorage.getItem('wl_model') || 'mini';
    _wlModel = saved === 'plus' ? 'Marble 0.1-plus' : 'Marble 0.1-mini';
    document.getElementById('r3dBtnMini').classList.toggle('active', saved === 'mini');
    document.getElementById('r3dBtnPlus').classList.toggle('active', saved === 'plus');
  } catch(e) {}
}

function show3DStatus(msg, pct, sub) {
  document.getElementById('r3dLoading').style.display = 'flex';
  document.getElementById('r3dStatusBar').style.width = pct + '%';
  document.getElementById('r3dStatusTxt').textContent = msg;
  if (sub) document.getElementById('r3dStatusSub').textContent = sub;
}
function hide3DStatus() { document.getElementById('r3dLoading').style.display = 'none'; }
function r3dBadge(msg) {
  const b = document.getElementById('r3dBadge');
  if (b) { b.textContent = msg; b.style.display = 'block'; setTimeout(() => b.style.display = 'none', 5000); }
}
function setR3DStep(n) {
  for (let i = 1; i <= 4; i++) {
    const el = document.getElementById('r3dst' + i);
    if (!el) continue;
    const text = el.textContent.replace(/^[○◉●] /, '');
    if (i < n) { el.className = 'r3d-step r3d-step-done'; el.textContent = '● ' + text; }
    else if (i === n) { el.className = 'r3d-step r3d-step-active'; el.textContent = '◉ ' + text; }
    else { el.className = 'r3d-step'; el.textContent = '○ ' + text; }
  }
}


// ── Extra 3D Fotos ──
var _extra3dPhotos = [null, null, null];
function loadExtra3D(idx, input) {
  var file = input.files[0]; if (!file) return;
  var r = new FileReader();
  r.onload = function(e) {
    _extra3dPhotos[idx] = { full: e.target.result, base64: e.target.result.split(',')[1] };
    var slot = document.getElementById('extra3dSlot' + idx);
    if (slot) {
      slot.style.border = '2px solid rgba(155,109,255,0.7)';
      var img = document.createElement('img');
      img.src = e.target.result;
      img.style.cssText = 'position:absolute;inset:0;width:100%;height:100%;object-fit:cover;opacity:0.85';
      var existing = slot.querySelector('img'); if (existing) existing.remove();
      slot.appendChild(img);
    }
    var count = _extra3dPhotos.filter(Boolean).length;
    var st = document.getElementById('extra3dStatus');
    if (st) st.textContent = count + ' eigene' + (count>1?'':'s') + ' Bild' + (count>1?'er':'') + ' · ' + (3-count) + ' per DALL-E ergänzt';
  };
  r.readAsDataURL(file);
}

async function open3DRoomWithExtras() {
  // Eigene Extra-Fotos in photos-Array einfügen (nur wenn vorhanden)
  var origPhotosCount = photos.length;
  _extra3dPhotos.forEach(function(p) {
    if (p) photos.push({ full: p.full, base64: p.base64, name: 'extra.jpg' });
  });
  await open3DRoom();
  // Photos-Array wieder auf Original zurücksetzen
  photos.splice(origPhotosCount);
}

async function open3DRoom() {
  // Nutze Nachher-Bild wenn vorhanden, sonst Originalfoto
  const src = _3dNachherSrc
    || (vizHistory.length ? vizHistory[vizHistory.length - 1] : null)
    || (wizResults.length ? wizResults[wizResults.length - 1]?.result : null)
    || (photos.length ? photos[0].full : null);
  if (!src) { showErr('Bitte zuerst ein Foto hochladen.'); return; }

  const wlKey = document.getElementById('wlKey').value.trim() || 'gudpd2yW8bJJDwP6nQHCB5BlqjTassnb';
  if (!wlKey) { showErr('Bitte World Labs API Key eingeben (oben in der Leiste).'); return; }

  document.getElementById('room3dOverlay').style.display = 'flex';
  document.body.style.overflow = 'hidden';
  initWLModel(); // Restore model selection
  const sc = document.getElementById('splat3dContainer');
  if(sc){ sc.style.display='none'; sc.innerHTML=''; }
  if(_gsViewer){ try{_gsViewer.dispose();}catch(e){} _gsViewer=null; }
  if(_spzBlobUrl){ URL.revokeObjectURL(_spzBlobUrl); _spzBlobUrl=null; }
  document.getElementById('r3dReadyBar').style.display = 'none';
  show3DStatus('Bild wird für World Labs vorbereitet...', 5, 'Marble wandelt dein Sanierungsbild in einen begehbaren 3D-Raum um');

  try {
    // ── Schritt 1: Bild hochladen ──
    setR3DStep(1);
    show3DStatus('Bild zu World Labs hochladen...', 15, 'Signed Upload URL anfordern');

    // 1a. PNG Blob aus DataURL
    let imgBlob;
    try { imgBlob = await resizeImagePNG(src, 1024); }
    catch (e) { throw new Error('Bildvorbereitung fehlgeschlagen: ' + e.message); }

    // 1b. Prepare upload → signed URL
    const prepResp = await fetch('https://api.worldlabs.ai/marble/v1/media-assets:prepare_upload', {
      method: 'POST',
      headers: { 'WLT-Api-Key': wlKey, 'Content-Type': 'application/json' },
      body: JSON.stringify({ file_name: 'sanierungsbild.png', kind: 'image', extension: 'png' })
    });
    const prepData = await prepResp.json();
    if (!prepResp.ok) {
      let prepErr = prepData.message || '';
      if (prepData.detail) {
        prepErr = Array.isArray(prepData.detail)
          ? prepData.detail.map(d => `${d.loc?.join('.')}: ${d.msg}`).join(', ')
          : JSON.stringify(prepData.detail);
      }
      throw new Error(`World Labs Upload-Vorbereitung ${prepResp.status}: ${prepErr || 'Fehler'}`);
    }

    const mediaAssetId = prepData.media_asset?.media_asset_id || prepData.media_asset?.id;
    const uploadUrl = prepData.upload_info?.upload_url;
    const uploadHeaders = prepData.upload_info?.required_headers || {};
    if (!uploadUrl) throw new Error('Kein Upload-URL von World Labs erhalten.');

    // 1c. Upload to signed URL
    show3DStatus('Bild hochladen...', 25, 'Upload läuft...');
    const uploadResp = await fetch(uploadUrl, {
      method: 'PUT',
      headers: { 'Content-Type': 'image/png', ...uploadHeaders },
      body: imgBlob
    });
    if (!uploadResp.ok) throw new Error('Bild-Upload fehlgeschlagen: ' + uploadResp.status);

    // ── Schritt 2: Welt generieren ──
    setR3DStep(2);
    const modelLabel = _wlModel === 'Marble 0.1-plus' ? 'Marble 0.1-plus – beste Qualität (~5 Min)' : 'Marble 0.1-mini – schnell (~45s)';
    show3DStatus('Marble generiert 3D-Welt...', 35, `Modell: ${modelLabel}`);

    // ── GPT-4o analysiert das EXAKTE Nachher-Bild für präzisen 3D-Prompt ──
    let worldPrompt = '';
    if (openaiKey) {
      show3DStatus('GPT-4o analysiert das Nachher-Bild...', 18, 'Exakte Raumbeschreibung für 3D-Rekonstruktion');
      try {
        const analyzeResp = await fetch('https://api.openai.com/v1/chat/completions', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json', 'Authorization': 'Bearer ' + openaiKey },
          body: JSON.stringify({
            model: 'gpt-4o',
            max_tokens: 600,
            temperature: 0,
            messages: [{
              role: 'user',
              content: [
                { type: 'image_url', image_url: { url: src.startsWith('data:') ? src : 'data:image/jpeg;base64,' + src, detail: 'high' } },
                { type: 'text', text: 'Describe this room image in extreme detail for 3D reconstruction. Include: exact room type, all wall colors and materials, floor material and color, ceiling height and color, every fixture (bathtub/shower/toilet/sink/radiator) with exact position (which wall, left/center/right), window count and positions, door position, lighting, tile patterns if any. Be specific and precise. Write 4-6 sentences as a scene description for a 3D artist.' }
              ]
            }]
          })
        });
        const analyzeData = await analyzeResp.json();
        if (analyzeResp.ok) {
          worldPrompt = analyzeData.choices[0].message.content.trim();
          worldPrompt += ' CRITICAL: Single fully enclosed room. The door behind the viewer is completely closed and sealed. No hallways, no corridors, no other rooms visible through any opening. All four walls are solid with no passageways. Viewer is trapped inside one room only.';
        }
      } catch(e) { console.warn('GPT-4o Analyse fehlgeschlagen:', e.message); }
    }

    // Fallback falls GPT-4o nicht verfügbar
    if (!worldPrompt) {
      const roomType = cachedRoomType || 'Innenraum';
      const a = cachedRoomAnalysis;
      worldPrompt = [
        `Photorealistic interior room: ${roomType}.`,
        a?.renovationMaterials ? `Materials: ${a.renovationMaterials}.` : 'Modern high-quality renovation.',
        a?.fixedElements ? `Layout: ${a.fixedElements}.` : '',
        'CRITICAL: Single fully enclosed room only. The entrance door behind the viewer is completely closed. No hallways, no corridors, no other rooms visible. All walls are solid. No open doorways or passages anywhere.',
        'Exact faithful 3D reconstruction. Sharp details, accurate proportions.'
      ].filter(Boolean).join(' ');
    }

    // ── DALL-E generiert 3 weitere Winkel (90°, 180°, 270°) ──
    const openaiKey = document.getElementById('openaiKey').value.trim();
    let extraAssetIds = [];

    if (openaiKey) {
      show3DStatus('DALL-E generiert 360°-Ansichten...', 28, '3 weitere Perspektiven werden automatisch erzeugt');

      const angles = [
        { deg: 90,  desc: 'the same room viewed from the right side wall, rotated 90 degrees' },
        { deg: 180, desc: 'the same room viewed from the opposite wall, rotated 180 degrees' },
        { deg: 270, desc: 'the same room viewed from the left side wall, rotated 270 degrees' }
      ];

      for (const angle of angles) {
        try {
          show3DStatus('DALL-E generiert 360°-Ansichten...', 28,
            `Perspektive ${angle.deg}° wird generiert...`);

          // DALL-E Variation des Raums aus anderem Winkel
          const dalleResp = await fetch('https://api.openai.com/v1/images/generations', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json', 'Authorization': 'Bearer ' + openaiKey },
            body: JSON.stringify({
              model: 'dall-e-3',
              prompt: `${worldPrompt} View from ${angle.desc}. Identical room, identical materials, colors and fixtures as described. No people. Professional architectural interior photography.`,
              n: 1,
              size: '1024x1024',
              quality: 'standard'
            })
          });
          const dalleData = await dalleResp.json();
          if (!dalleResp.ok) { console.warn('DALL-E:', dalleData.error?.message); continue; }

          const dalleUrl = dalleData.data?.[0]?.url;
          if (!dalleUrl) continue;

          // Bild von DALL-E URL herunterladen und zu World Labs hochladen
          const imgResp = await fetch(dalleUrl);
          const imgBlob = await imgResp.blob();
          const imgArrBuf = await imgBlob.arrayBuffer();
          const imgPng = new Blob([imgArrBuf], { type: 'image/png' });

          const epPrep = await fetch('https://api.worldlabs.ai/marble/v1/media-assets:prepare_upload', {
            method: 'POST',
            headers: { 'WLT-Api-Key': wlKey, 'Content-Type': 'application/json' },
            body: JSON.stringify({ file_name: `view_${angle.deg}.png`, kind: 'image', extension: 'png' })
          });
          const epData = await epPrep.json();
          if (!epPrep.ok) continue;
          const epId  = epData.media_asset?.media_asset_id;
          const epUrl = epData.upload_info?.upload_url;
          const epHdr = epData.upload_info?.required_headers || {};
          await fetch(epUrl, { method: 'PUT', headers: { 'Content-Type': 'image/png', ...epHdr }, body: imgPng });
          if (epId) extraAssetIds.push(epId);
        } catch(e) { console.warn('DALL-E Winkel fehlgeschlagen:', e.message); }
      }
    }

    const viewCount = extraAssetIds.length + 1;
    show3DStatus('Marble generiert 3D-Welt...', 38,
      `Modell: ${modelLabel} · ${viewCount} Perspektive${viewCount > 1 ? 'n' : ''} (${viewCount > 1 ? '360° Rekonstruktion' : 'Einzelbild'})`);

    // Multi-image wenn DALL-E Bilder vorhanden
    let worldPromptBody;
    if (extraAssetIds.length > 0) {
      const azimuths = [0, 90, 180, 270];
      const allIds = [mediaAssetId, ...extraAssetIds];
      worldPromptBody = {
        type: 'multi-image',
        multi_image_prompt: allIds.map((id, i) => ({
          azimuth: azimuths[i] || (i * 90),
          content: { source: 'media_asset', media_asset_id: id }
        })),
        text_prompt: worldPrompt
      };
    } else {
      worldPromptBody = {
        type: 'image',
        image_prompt: { source: 'media_asset', media_asset_id: mediaAssetId },
        text_prompt: worldPrompt
      };
    }

    const genBody = {
      display_name: `Sanierung – ${document.getElementById('objekt')?.value || 'Wiegand'}`,
      model: _wlModel,
      permission: { public: true },
      world_prompt: worldPromptBody
    };

    const genResp = await fetch('https://api.worldlabs.ai/marble/v1/worlds:generate', {
      method: 'POST',
      headers: { 'WLT-Api-Key': wlKey, 'Content-Type': 'application/json' },
      body: JSON.stringify(genBody)
    });
    const genData = await genResp.json();
    if (!genResp.ok) {
      // 422 = validation error — extract field details
      let errMsg = genData.message || genData.error?.message || '';
      if (genData.detail) {
        const details = Array.isArray(genData.detail)
          ? genData.detail.map(d => `${d.loc?.join('.')}: ${d.msg}`).join(', ')
          : JSON.stringify(genData.detail);
        errMsg = details || errMsg;
      }
      throw new Error(`World Labs ${genResp.status}: ${errMsg || 'Unbekannter Fehler'}`);
    }

    const operationId = genData.operation_id;
    if (!operationId) throw new Error('Keine Operation-ID von World Labs erhalten.');

    // ── Schritt 3: Polling ──
    setR3DStep(3);
    // Plus braucht bis zu 8 Min, Mini bis zu 2 Min — großzügig bemessen
    const maxWait = _wlModel === 'Marble 0.1-plus' ? 540 : 150;
    let worldId = null;
    let finalPollData = null; // gespeichert für Schritt 4
    let elapsed = 0;
    let pct = 35;
    let lastStatus = '';

    while (elapsed < maxWait) {
      await new Promise(r => setTimeout(r, 5000));
      elapsed += 5;
      pct = Math.min(88, 35 + (elapsed / maxWait) * 53);

      let pollData;
      try {
        const pollResp = await fetch(
          `https://api.worldlabs.ai/marble/v1/operations/${operationId}`,
          { headers: { 'WLT-Api-Key': wlKey } }
        );
        if (!pollResp.ok) {
          // Netzwerkfehler — kurz warten und weiter versuchen
          console.warn('Poll HTTP error:', pollResp.status);
          continue;
        }
        pollData = await pollResp.json();
      } catch(networkErr) {
        console.warn('Poll network error:', networkErr);
        continue; // Netzwerkfehler überspringen, nicht abbrechen
      }

      // Fortschritt aus metadata extrahieren
      const status = pollData.metadata?.progress?.status || '';
      const desc   = pollData.metadata?.progress?.description || '';
      if (status !== lastStatus) {
        lastStatus = status;
        console.log('World Labs Status:', status, desc);
      }

      // worldId aus metadata vormerken (kommt früher als response)
      if (pollData.metadata?.world_id) {
        worldId = pollData.metadata.world_id;
      }

      // Fertig?
      if (pollData.done) {
        if (pollData.error) {
          const errMsg = pollData.error?.message || pollData.error?.code || JSON.stringify(pollData.error);
          throw new Error('World Labs Generierung fehlgeschlagen: ' + errMsg);
        }
        if (pollData.response) {
          finalPollData = pollData;
          // worldId aus response (zuverlässigste Quelle)
          worldId = pollData.response.world_id
            || pollData.metadata?.world_id
            || worldId;
          // Fallback: aus marble URL extrahieren
          if (!worldId && pollData.response.world_marble_url) {
            const m = pollData.response.world_marble_url.match(/world\/([a-zA-Z0-9-]+)/);
            if (m) worldId = m[1];
          }
          break; // Erfolgreich fertig
        }
        // done:true aber noch kein response — nochmal warten
        continue;
      }

      // Noch in Arbeit — Status anzeigen
      const remaining = maxWait - elapsed;
      const statusText = desc || (status === 'IN_PROGRESS' ? 'Wird verarbeitet...' : 'Warte auf Ergebnis...');
      show3DStatus(
        `Marble: ${statusText} (${Math.round(elapsed)}s)`,
        pct,
        `Modell: ${_wlModel} · Noch ~${Math.max(0, Math.round(remaining))}s`
      );
    }

    if (!worldId) {
      // Timeout — aber Generation läuft evtl. noch bei World Labs
      // Zeige hilfreiche Meldung mit operationId damit der Nutzer die Welt später findet
      throw new Error(
        `Timeout nach ${maxWait}s. Die Generation läuft möglicherweise noch bei World Labs. ` +
        `Operation-ID: ${operationId} — prüfe platform.worldlabs.ai`
      );
    }

    // ── Schritt 4: Frische URLs holen + rendern ──
    setR3DStep(4);
    const marbleUrl = `https://marble.worldlabs.ai/world/${worldId}`;
    document.getElementById('r3dOpenBtn').href = marbleUrl;

    show3DStatus('Asset-URLs werden abgerufen...', 90, 'Frische Download-URLs von World Labs holen');

    let spzUrl = null;
    let panoUrl = null;
    let thumbUrl = null;
    try {
      const worldResp = await fetch(`https://api.worldlabs.ai/marble/v1/worlds/${worldId}`, {
        headers: { 'WLT-Api-Key': wlKey }
      });
      if (worldResp.ok) {
        const worldData = await worldResp.json();
        const assets = worldData.world?.assets || worldData.assets || {};
        const spzAssets = assets.splats?.spz_urls || {};
        spzUrl = _wlModel === 'Marble 0.1-plus'
          ? (spzAssets['full_res'] || spzAssets['500k'] || spzAssets['100k'])
          : (spzAssets['500k'] || spzAssets['full_res'] || spzAssets['100k'])
          || null;
        panoUrl = assets.imagery?.pano_url || null;
        thumbUrl = assets.thumbnail_url || null;
        console.log('Fresh SPZ URL:', spzUrl ? spzUrl.split('?')[0] : 'none');
        console.log('Pano URL:', panoUrl ? panoUrl.split('?')[0] : 'none');
      }
    } catch(e) {
      console.warn('World fetch fehlgeschlagen:', e);
    }

    // Fallback: SPZ aus Poll-Response
    if (!spzUrl) {
      const spzAssets = finalPollData?.response?.assets?.splats?.spz_urls || {};
      spzUrl = spzAssets['500k'] || spzAssets['full_res'] || spzAssets['100k'] || null;
    }

    if (spzUrl) {
      show3DStatus('GaussianSplats3D laden...', 93, 'Renderer initialisieren');
      if (typeof GaussianSplats3D === 'undefined') await loadGSLibrary();

      if (typeof GaussianSplats3D !== 'undefined') {
        // Try SPZ — if fetch fails (file://), fall back to pano viewer
        const success = await loadGaussianSplatSafe(spzUrl, panoUrl, marbleUrl);
        if (!success && panoUrl) {
          await loadPanoViewer(panoUrl, marbleUrl);
        } else if (!success) {
          window.open(marbleUrl, '_blank');
        }
      } else if (panoUrl) {
        await loadPanoViewer(panoUrl, marbleUrl);
      } else {
        hide3DStatus();
        document.getElementById('r3dReadyBar').style.display = 'flex';
        window.open(marbleUrl, '_blank');
      }
    } else if (panoUrl) {
      // Kein SPZ aber Pano vorhanden — zeige 360° Panorama
      show3DStatus('360° Panorama wird geladen...', 93, 'Interaktives Panorama');
      await loadPanoViewer(panoUrl, marbleUrl);
    } else {
      hide3DStatus();
      document.getElementById('r3dReadyBar').style.display = 'flex';
      r3dBadge('⚠ Keine Assets – Marble wird geöffnet');
      window.open(marbleUrl, '_blank');
    }

  } catch (e) {
    hide3DStatus();
    showErr('World Labs: ' + e.message);
    // Offer Meshy fallback
    const meshyKey = document.getElementById('meshyKey').value.trim();
    if (meshyKey) {
      r3dBadge('⚠ World Labs fehlgeschlagen – Meshy Fallback aktiv');
      toast('⚠ World Labs fehlgeschlagen – Meshy-Fallback wird gestartet');
      setTimeout(() => open3DRoomMeshyFallback(src, meshyKey), 1200);
    } else {
      close3DRoom();
    }
  }
}

// ── Meshy Fallback (Babylon.js) ──────────────────────

async function open3DRoomMeshyFallback(src, meshyKey) {
  document.getElementById('room3dOverlay').style.display = 'flex';
  document.body.style.overflow = 'hidden';
  show3DStatus('Meshy Fallback – Babylon.js 3D-Szene...', 10, 'World Labs nicht verfügbar');

  // Dynamic load Babylon if not loaded
  if (typeof BABYLON === 'undefined') {
    await new Promise((resolve, reject) => {
      const s = document.createElement('script');
      s.src = 'https://cdn.babylonjs.com/v7.34.1/babylon.js';
      s.onload = resolve; s.onerror = reject;
      document.head.appendChild(s);
    });
    await new Promise((resolve, reject) => {
      const s = document.createElement('script');
      s.src = 'https://cdn.babylonjs.com/v7.34.1/babylonjs.loaders.min.js';
      s.onload = resolve; s.onerror = reject;
      document.head.appendChild(s);
    });
  }

  show3DStatus('Szene aufbauen...', 40, 'Babylon.js Renderer');
  const fallbackRoom = {
    roomType: cachedRoomType || 'Raum',
    roomDimensions: { width: 5, depth: 5, height: 2.8 },
    wallColors: { front: '#e8e0d0', back: '#e0d8c8', left: '#dfd7c7', right: '#dfd7c7' },
    floorColor: '#c8bfa0', ceilingColor: '#f4f0e8', lighting: 'warm',
    furniture: [
      { name: 'bathtub', meshyPrompt: 'freestanding oval white ceramic bathtub modern', color: '#ffffff', posX: -1.5, posZ: -1.2, width: 1.7, depth: 0.8, height: 0.6, rotY: 0 },
      { name: 'vanity', meshyPrompt: 'floating bathroom vanity white vessel sink chrome', color: '#f5f5f5', posX: 1.6, posZ: -0.5, width: 1.2, depth: 0.5, height: 0.9, rotY: 0 },
      { name: 'toilet', meshyPrompt: 'wall hung rimless white toilet modern', color: '#ffffff', posX: 0.4, posZ: -1.9, width: 0.4, depth: 0.7, height: 0.5, rotY: 0 }
    ]
  };

  try {
    const canvas = document.getElementById('room3dCanvas');
    canvas.style.display = 'block';
    const sc = document.getElementById('splat3dContainer');
  if(sc){ sc.style.display='none'; sc.innerHTML=''; }
  if(_gsViewer){ try{_gsViewer.dispose();}catch(e){} _gsViewer=null; }
  if(_spzBlobUrl){ URL.revokeObjectURL(_spzBlobUrl); _spzBlobUrl=null; }
    await initBabylonScene(fallbackRoom, src);
    hide3DStatus();
    if (meshyKey) {
      r3dBadge('🪄 Meshy lädt 3D-Möbel...');
      loadMeshyFurniture(meshyKey, fallbackRoom).then(() => r3dBadge('✓ Möbel platziert')).catch(() => {});
    }
  } catch(e) {
    hide3DStatus();
    close3DRoom();
    showErr('Fallback 3D-Szene fehlgeschlagen: ' + e.message);
  }
}

async function initBabylonScene(roomData, nachherSrc) {
  const canvas = document.getElementById('room3dCanvas');
  if (_3d.engine) { _3d.engine.dispose(); _3d.engine = null; }
  _3d.engine = new BABYLON.Engine(canvas, true, { antialias: true });
  const sc = new BABYLON.Scene(_3d.engine); _3d.scene = sc;
  sc.clearColor = new BABYLON.Color4(0.05, 0.04, 0.03, 1);
  const W = roomData.roomDimensions?.width||5, H = roomData.roomDimensions?.height||2.8, D = roomData.roomDimensions?.depth||5;
  const fps = new BABYLON.UniversalCamera('fps', new BABYLON.Vector3(0, 1.65, D/2-0.8), sc);
  fps.setTarget(new BABYLON.Vector3(0, 1.65, 0)); fps.minZ=0.08; fps.speed=0.10; fps.angularSensibility=560;
  fps.keysUp=[87,38]; fps.keysDown=[83,40]; fps.keysLeft=[65,37]; fps.keysRight=[68,39]; _3d.fpsCam=fps;
  const arc = new BABYLON.ArcRotateCamera('arc', -Math.PI/2, 1.1, Math.max(W,D)*1.5, new BABYLON.Vector3(0,H*0.4,0), sc);
  arc.lowerRadiusLimit=2; arc.upperRadiusLimit=Math.max(W,D)*3; arc.upperBetaLimit=Math.PI/2.05; _3d.arcCam=arc;
  sc.activeCamera=fps;
  const hemi = new BABYLON.HemisphericLight('h', new BABYLON.Vector3(0,1,0), sc);
  hemi.intensity=1.0; hemi.diffuse=new BABYLON.Color3(1,0.97,0.88); hemi.groundColor=new BABYLON.Color3(0.35,0.30,0.22);
  function hexToC3(hex){hex=(hex||'#d0c8b8').replace('#','');return new BABYLON.Color3(parseInt(hex.slice(0,2),16)/255,parseInt(hex.slice(2,4),16)/255,parseInt(hex.slice(4,6),16)/255);}
  function imgMat(tw,th,em){
    const mat=new BABYLON.StandardMaterial('',sc);
    const dt=new BABYLON.DynamicTexture('',{width:tw,height:th},sc,false);
    const img=new Image(); img.crossOrigin='anonymous';
    img.onload=()=>{const ctx=dt.getContext();const s=Math.max(tw/img.width,th/img.height);ctx.drawImage(img,(tw-img.width*s)/2,(th-img.height*s)/2,img.width*s,img.height*s);dt.update();};
    img.src=nachherSrc; mat.diffuseTexture=dt; mat.emissiveTexture=dt; mat.emissiveColor=new BABYLON.Color3(em,em*0.97,em*0.92); return mat;
  }
  function addBox(w,h,d,x,y,z,mat){const m=BABYLON.MeshBuilder.CreateBox('',{width:w,height:h,depth:d,sideOrientation:BABYLON.Mesh.DOUBLESIDE},sc);m.position.set(x,y,z);m.material=mat;return m;}
  addBox(W,H,0.05,0,H/2,-D/2,imgMat(2048,1024,0.92));
  addBox(W,H,0.05,0,H/2,D/2,imgMat(2048,1024,0.72));
  addBox(0.05,H,D,-W/2,H/2,0,imgMat(1024,1024,0.78));
  addBox(0.05,H,D,W/2,H/2,0,imgMat(1024,1024,0.78));
  const floor=BABYLON.MeshBuilder.CreateGround('',{width:W,height:D},sc); floor.material=imgMat(1024,1024,0.60);
  const ceil=BABYLON.MeshBuilder.CreateGround('',{width:W,height:D},sc); ceil.position.y=H; ceil.rotation.x=Math.PI;
  const cm=new BABYLON.PBRMaterial('',sc); cm.albedoColor=hexToC3(roomData.ceilingColor||'#f2eee6'); cm.roughness=0.9; cm.metallic=0; ceil.material=cm;
  (roomData.furniture||[]).forEach(item=>{
    const iw=Math.max(0.15,item.width||1), id=Math.max(0.15,item.depth||0.6), ih=Math.max(0.1,item.height||0.8);
    const px=Math.max(-(W/2-0.3),Math.min(W/2-0.3,item.posX||0));
    const pz=Math.max(-(D/2-0.3),Math.min(D/2-0.3,item.posZ||0));
    const mat=new BABYLON.PBRMaterial('',sc); mat.albedoColor=hexToC3(item.color||'#e8e4e0'); mat.roughness=0.2; mat.metallic=0.02;
    const mesh=BABYLON.MeshBuilder.CreateBox('ph_'+(item.name||'').toLowerCase(),{width:iw,height:ih,depth:id},sc);
    mesh.position.set(px,ih/2,pz); mesh.material=mat; if(item.rotY)mesh.rotation.y=item.rotY;
  });
  const LX=W/2-0.5, LZ=D/2-0.5;
  sc.registerBeforeRender(()=>{ if(_3d.mode!=='walk')return; fps.position.x=Math.max(-LX,Math.min(LX,fps.position.x)); fps.position.z=Math.max(-LZ,Math.min(LZ,fps.position.z)); fps.position.y=1.65; });
  canvas.addEventListener('click',()=>{ if(_3d.mode==='walk'&&document.pointerLockElement!==canvas) canvas.requestPointerLock?.(); });
  canvas.addEventListener('mousedown',e=>{ _3d.drag=true; _3d.lx=e.clientX; _3d.ly=e.clientY; });
  canvas.addEventListener('mouseup',()=>_3d.drag=false);
  canvas.addEventListener('mousemove',e=>{ if(!_3d.drag||_3d.mode!=='walk'||document.pointerLockElement===canvas)return; fps.rotation.y+=(e.clientX-_3d.lx)*0.003; fps.rotation.x=Math.max(-1.2,Math.min(1.2,fps.rotation.x+(e.clientY-_3d.ly)*0.003)); _3d.lx=e.clientX; _3d.ly=e.clientY; });
  _3d.engine.runRenderLoop(()=>sc.render());
  window.addEventListener('resize',()=>{ if(_3d.engine)_3d.engine.resize(); });
  set3DMode('walk');
}

async function loadMeshyFurniture(meshyKey, roomData) {
  const furniture = roomData.furniture || [];
  for (let fi = 0; fi < furniture.length; fi++) {
    const item = furniture[fi];
    if (!_3d.scene) break;
    try {
      r3dBadge(`🪄 Möbel ${fi+1}/${furniture.length}: ${item.name}...`);
      const ph = _3d.scene.getMeshByName('ph_' + (item.name||'').toLowerCase());
      if (ph) ph.dispose();
      const cr = await fetch('https://api.meshy.ai/openapi/v2/text-to-3d', { method:'POST', headers:{'Authorization':'Bearer '+meshyKey,'Content-Type':'application/json'}, body:JSON.stringify({mode:'preview',prompt:item.meshyPrompt||item.name,ai_model:'meshy-4',topology:'triangle',target_polycount:10000}) });
      const cd = await cr.json(); if (!cr.ok) continue;
      const taskId = cd.result; let glbUrl = null;
      for (let i = 0; i < 150; i++) {
        await new Promise(r=>setTimeout(r,5000)); if (!_3d.scene) return;
        const pr = await fetch('https://api.meshy.ai/openapi/v2/text-to-3d/'+taskId,{headers:{'Authorization':'Bearer '+meshyKey}});
        const pd = await pr.json();
        if (pd.status==='SUCCEEDED') { glbUrl=pd.model_urls?.glb; break; }
        if (pd.status==='FAILED') break;
      }
      if (!glbUrl || !_3d.scene) continue;
      const res = await BABYLON.SceneLoader.ImportMeshAsync('','',glbUrl,_3d.scene,null,'.glb');
      const root = res.meshes[0]; if (!root) continue;
      const bb = root.getHierarchyBoundingVectors(); const size = bb.max.subtract(bb.min);
      const sf = (item.width||1) / Math.max(size.x||1, 0.01);
      root.scaling = new BABYLON.Vector3(sf,sf,sf);
      root.position = new BABYLON.Vector3(item.posX||0, 0, item.posZ||0);
      if (item.rotY) root.rotation.y = item.rotY;
    } catch(e) { console.warn('Meshy item:', item.name, e); }
  }
}

// ── Safe SPZ loader — returns true if success, false if fail ──
async function loadGaussianSplatSafe(spzUrl, panoUrl, marbleUrl) {
  try {
    await loadGaussianSplat(spzUrl);
    return true;
  } catch(e) {
    console.warn('SPZ load failed, trying pano fallback:', e.message);
    return false;
  }
}

// ── 360° Panorama Viewer — works on file:// via <img> tag (no CORS) ──
async function loadPanoViewer(panoUrl, marbleUrl) {
  var container = document.getElementById('splat3dContainer');
  container.style.cssText = 'width:100%;height:100%;display:flex;flex-direction:column;position:relative;overflow:hidden;background:#0a0806;flex:1';
  container.innerHTML = '';
  show3DStatus('3D-Raum wird aufgebaut...', 95, 'First-Person Modus');

  // Load Three.js
  if (!window.THREE) {
    await new Promise(function(resolve, reject) {
      var s = document.createElement('script');
      s.src = 'https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js';
      s.onload = resolve; s.onerror = reject;
      document.head.appendChild(s);
    });
  }

  // Canvas
  var canvas = document.createElement('canvas');
  canvas.style.cssText = 'width:100%;height:100%;display:block;outline:none;touch-action:none;flex:1';
  canvas.setAttribute('tabindex', '0');
  container.appendChild(canvas);
  setTimeout(function() { canvas.focus(); }, 100);

  // Hint bar
  var hintBar = document.createElement('div');
  hintBar.style.cssText = 'position:absolute;bottom:160px;left:50%;transform:translateX(-50%);background:rgba(0,0,0,0.55);backdrop-filter:blur(8px);border:1px solid rgba(255,255,255,0.1);border-radius:20px;padding:6px 16px;font-size:11px;color:rgba(255,255,255,0.5);pointer-events:none;white-space:nowrap;z-index:10';
  hintBar.textContent = 'BEWEGEN = linker Joystick · UMSEHEN = rechter Joystick';
  container.appendChild(hintBar);

  // Three.js scene
  var W = container.clientWidth || window.innerWidth;
  var H = container.clientHeight || window.innerHeight;

  var renderer = new THREE.WebGLRenderer({ canvas: canvas, antialias: true });
  renderer.setSize(W, H);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

  var scene = new THREE.Scene();
  scene.background = new THREE.Color(0x111111);

  // Camera = Kopf der Person
  var camera = new THREE.PerspectiveCamera(75, W/H, 0.05, 100);
  camera.position.set(0, 1.65, 0); // Augenhöhe 1.65m

  // Load panorama as texture
  show3DStatus('Panorama wird geladen...', 97, '');
  var texture;
  try {
    texture = await new Promise(function(resolve, reject) {
      var loader = new THREE.TextureLoader();
      loader.crossOrigin = 'anonymous';
      loader.load(panoUrl, resolve, undefined, reject);
    });
  } catch(e) {
    container.innerHTML = '<div style="color:#9098b0;text-align:center;padding:60px;font-family:sans-serif"><p style="font-size:2rem">🌐</p><p style="margin-top:12px">Panorama nicht verfügbar</p><p style="margin-top:12px"><a href="' + marbleUrl + '" target="_blank" style="color:#9b6dff">In Marble öffnen →</a></p></div>';
    hide3DStatus();
    document.getElementById('r3dReadyBar').style.display = 'flex';
    return;
  }

  // Raum als Box — Person steht innen
  // Wände, Boden, Decke bekommen alle das Panorama als Textur
  var roomSize = 8; // 8m x 8m Raum
  var roomH = 3;    // 3m hoch

  // Erstelle 6 Wände als einzelne Planes mit korrekten Panorama-Ausschnitten
  function makeWall(w, h, tx, ty, tz, rx, ry) {
    var geo = new THREE.PlaneGeometry(w, h);
    var mat = new THREE.MeshBasicMaterial({ map: texture, side: THREE.FrontSide });
    var mesh = new THREE.Mesh(geo, mat);
    mesh.position.set(tx, ty, tz);
    mesh.rotation.set(rx, ry, 0);
    scene.add(mesh);
    return mesh;
  }

  // Sphere innen — Hauptansicht
  var sphereGeo = new THREE.SphereGeometry(50, 64, 32);
  sphereGeo.scale(-1, 1, 1); // innen
  var sphereMat = new THREE.MeshBasicMaterial({ map: texture });
  var sphere = new THREE.Mesh(sphereGeo, sphereMat);
  scene.add(sphere);

  // Boden — damit man weiß wo man steht
  var floorGeo = new THREE.PlaneGeometry(40, 40);
  var floorMat = new THREE.MeshBasicMaterial({ color: 0x1a1a1a, transparent: true, opacity: 0.3 });
  var floor = new THREE.Mesh(floorGeo, floorMat);
  floor.rotation.x = -Math.PI / 2;
  floor.position.y = 0;
  scene.add(floor);

  // Licht
  scene.add(new THREE.AmbientLight(0xffffff, 1));

  // Person state
  var px = 0, py = 1.65, pz = 0; // position
  var yaw = 0, pitch = 0;         // blickrichtung
  var moveSpeed = 0.04;

  // Keyboard
  var keys = {};
  function onKD(e) {
    keys[e.key.toLowerCase()] = true;
    if (['w','a','s','d','arrowup','arrowdown','arrowleft','arrowright','q','e',' '].indexOf(e.key.toLowerCase()) > -1) {
      e.preventDefault();
      e.stopPropagation();
    }
  }
  function onKU(e) { keys[e.key.toLowerCase()] = false; }
  document.addEventListener('keydown', onKD, true); // capture phase
  document.addEventListener('keyup', onKU, true);
  canvas.addEventListener('keydown', onKD);
  canvas.addEventListener('keyup', onKU);

  // Pointer lock (Maus)
  canvas.addEventListener('click', function() { canvas.requestPointerLock && canvas.requestPointerLock(); });
  document.addEventListener('pointerlockchange', function() {
    hintBar.textContent = document.pointerLockElement === canvas
      ? 'W/A/S/D = Laufen · Maus = Umsehen · ESC = Freigeben'
      : 'BEWEGEN = linker Joystick · UMSEHEN = rechter Joystick · Klick = Maus sperren';
  });
  document.addEventListener('mousemove', function(e) {
    if (document.pointerLockElement !== canvas) return;
    yaw   -= e.movementX * 0.002;
    pitch  = Math.max(-1.2, Math.min(1.2, pitch - e.movementY * 0.002));
  });

  // Mouse drag fallback
  var mDown = false, mLastX = 0, mLastY = 0;
  canvas.addEventListener('mousedown', function(e) { mDown=true; mLastX=e.clientX; mLastY=e.clientY; canvas.style.cursor='grabbing'; });
  window.addEventListener('mouseup',   function() { mDown=false; canvas.style.cursor='default'; });
  window.addEventListener('mousemove', function(e) {
    if (!mDown || document.pointerLockElement===canvas) return;
    yaw   -= (e.clientX-mLastX) * 0.004;
    pitch  = Math.max(-1.2, Math.min(1.2, pitch-(e.clientY-mLastY)*0.004));
    mLastX=e.clientX; mLastY=e.clientY;
  });

  // Scroll zoom
  canvas.addEventListener('wheel', function(e) {
    e.preventDefault();
    camera.fov = Math.max(40, Math.min(90, camera.fov + e.deltaY * 0.03));
    camera.updateProjectionMatrix();
  }, {passive:false});

  // Store for cleanup
  _panoGL = { renderer: renderer, animId: null, onResize: null, onKD: onKD, onKU: onKU };

  function onResize() {
    var w = container.clientWidth||window.innerWidth;
    var h = container.clientHeight||window.innerHeight;
    renderer.setSize(w, h);
    camera.aspect = w/h;
    camera.updateProjectionMatrix();
  }
  window.addEventListener('resize', onResize);
  _panoGL.onResize = onResize;

  // Render loop
  // Position display
  var posDisplay = document.createElement('div');
  posDisplay.style.cssText = 'position:absolute;top:10px;left:10px;background:rgba(0,0,0,0.6);color:#9b6dff;font-family:monospace;font-size:11px;padding:6px 10px;border-radius:8px;z-index:20;pointer-events:none';
  container.appendChild(posDisplay);

  function render() {
    _panoGL.animId = requestAnimationFrame(render);

    // WASD keyboard
    var sinY = Math.sin(yaw), cosY = Math.cos(yaw);
    var moved = false;
    if (keys['w']||keys['arrowup'])    { px += sinY*moveSpeed; pz += cosY*moveSpeed; moved=true; }
    if (keys['s']||keys['arrowdown'])  { px -= sinY*moveSpeed; pz -= cosY*moveSpeed; moved=true; }
    if (keys['a']||keys['arrowleft'])  { px -= cosY*moveSpeed; pz += sinY*moveSpeed; moved=true; }
    if (keys['d']||keys['arrowright']) { px += cosY*moveSpeed; pz -= sinY*moveSpeed; moved=true; }
    if (keys['q']||keys['pageup'])     { py = Math.min(4, py+moveSpeed); moved=true; }
    if (keys['e']||keys['pagedown'])   { py = Math.max(0.5, py-moveSpeed); moved=true; }
    posDisplay.textContent = 'X:'+px.toFixed(1)+' Y:'+py.toFixed(1)+' Z:'+pz.toFixed(1) + (moved?' 🚶':'');

    // Joystick movement (global vars set by makeJoystick)
    var jLx=window._jLx||0, jLy=window._jLy||0;
    var jRx=window._jRx||0, jRy=window._jRy||0;
    if (jLx||jLy) {
      px += (sinY*(-jLy) + cosY*jLx) * moveSpeed * 2.5;
      pz += (cosY*(-jLy) - sinY*jLx) * moveSpeed * 2.5;
    }
    if (jRx||jRy) {
      yaw   -= jRx * 0.04;
      pitch  = Math.max(-1.2, Math.min(1.2, pitch - jRy * 0.03));
    }

    // Begrenzung — bleibt im Raum
    px = Math.max(-30, Math.min(30, px));
    pz = Math.max(-30, Math.min(30, pz));
    py = Math.max(0.5, Math.min(4, py));

    // Kamera = Kopf der Person
    camera.position.set(px, py, pz);

    // Sphere bleibt fix — Kamera bewegt sich durch sie hindurch
    // sphere.position bleibt (0,0,0)

    // Blickrichtung
    var lookX = px + Math.sin(yaw)*Math.cos(pitch)*10;
    var lookY = py + Math.sin(pitch)*10;
    var lookZ = pz + Math.cos(yaw)*Math.cos(pitch)*10;
    camera.lookAt(lookX, lookY, lookZ);

    renderer.render(scene, camera);
  }
  render();

  // Joysticks
  window._jLx=0;window._jLy=0;window._jRx=0;window._jRy=0;

  function makeJoystick(side) {
    var wrap = document.createElement('div');
    wrap.style.cssText = 'position:absolute;bottom:24px;' + (side==='left'?'left:24px':'right:24px') + ';width:110px;height:110px;z-index:30;touch-action:none';

    var ring = document.createElement('div');
    ring.style.cssText = 'position:absolute;inset:0;border-radius:50%;background:rgba(0,0,0,0.4);border:2.5px solid rgba(255,255,255,0.35)';
    wrap.appendChild(ring);

    var lbl = document.createElement('div');
    lbl.style.cssText = 'position:absolute;bottom:-20px;width:100%;text-align:center;font-size:10px;color:rgba(255,255,255,0.4);pointer-events:none';
    lbl.textContent = side==='left'?'BEWEGEN':'UMSEHEN';
    wrap.appendChild(lbl);

    var dot = document.createElement('div');
    dot.style.cssText = 'position:absolute;width:44px;height:44px;border-radius:50%;background:rgba(255,255,255,0.65);top:33px;left:33px;box-shadow:0 2px 8px rgba(0,0,0,0.5);pointer-events:none';
    wrap.appendChild(dot);

    var active=false, tid=-1, cx=0, cy=0, maxR=30;

    function getCenter() { var r=wrap.getBoundingClientRect(); cx=r.left+55; cy=r.top+55; }
    function moveDot(px,py) {
      var dx=px-cx,dy=py-cy,d=Math.sqrt(dx*dx+dy*dy);
      if(d>maxR){dx=dx/d*maxR;dy=dy/d*maxR;}
      dot.style.left=(33+dx)+'px'; dot.style.top=(33+dy)+'px';
      if(side==='left'){window._jLx=dx/maxR;window._jLy=dy/maxR;}
      else{window._jRx=dx/maxR;window._jRy=dy/maxR;}
    }
    function reset(){
      dot.style.left='33px';dot.style.top='33px';
      if(side==='left'){window._jLx=0;window._jLy=0;}
      else{window._jRx=0;window._jRy=0;}
      active=false;tid=-1;
    }

    wrap.addEventListener('touchstart',function(e){
      e.preventDefault();e.stopPropagation();
      if(active)return;
      var t=e.changedTouches[0];tid=t.identifier;active=true;
      getCenter();moveDot(t.clientX,t.clientY);
    },{passive:false});

    document.addEventListener('touchmove',function(e){
      if(!active)return;
      for(var i=0;i<e.touches.length;i++){
        if(e.touches[i].identifier===tid){moveDot(e.touches[i].clientX,e.touches[i].clientY);return;}
      }
    },{passive:true});

    document.addEventListener('touchend',function(e){
      if(!active)return;
      for(var i=0;i<e.changedTouches.length;i++){
        if(e.changedTouches[i].identifier===tid){reset();return;}
      }
    },{passive:true});

    return wrap;
  }

  container.appendChild(makeJoystick('left'));
  container.appendChild(makeJoystick('right'));

  hide3DStatus();
  document.getElementById('r3dReadyBar').style.display = 'flex';
  r3dBadge('✅ 3D-Raum bereit – Joystick zum Laufen!');
  toast('🎮 Linker Joystick = Laufen · Rechter = Umsehen');
}



function set3DMode(m) {
  _3d.mode = m;
  const sc = _3d.scene; if (!sc) return;
  sc.activeCamera = m === 'walk' ? _3d.fpsCam : _3d.arcCam;
}

// ── GaussianSplats3D Renderer ────────────────────────
let _gsViewer = null;
let _panoGL = null; // WebGL pano renderer

function loadGSLibrary() {
  return new Promise((resolve) => {
    if (typeof GaussianSplats3D !== 'undefined') { resolve(); return; }
    const s = document.createElement('script');
    s.src = 'https://cdn.jsdelivr.net/npm/@mkkellogg/gaussian-splats-3d@0.4.7/build/gaussian-splats-3d.umd.min.js';
    s.onload = resolve;
    s.onerror = resolve; // resolve anyway, check later
    document.head.appendChild(s);
  });
}

let _spzBlobUrl = null; // gespeicherte blob:// URL für cleanup

async function loadGaussianSplat(spzUrl) {
  const container = document.getElementById('splat3dContainer');
  // Container braucht explizite Dimensionen für GaussianSplats3D
  container.style.cssText = 'width:100%;height:100%;display:block;position:relative;overflow:hidden;background:#0a0806';
  container.innerHTML = '';

  show3DStatus('SPZ wird heruntergeladen...', 93, 'Datei wird geladen...');

  try {
    // ── Schritt 1: SPZ herunterladen ──
    let localBlobUrl;
    let fileSizeMB = '?';

    try {
      show3DStatus('SPZ herunterladen...', 93, `${spzUrl.split('?')[0].split('/').pop().substring(0,30)}...`);
      // Signed GCS URL — no auth header needed, but need credentials:omit for CORS
      const spzResp = await fetch(spzUrl, {
        method: 'GET',
        mode: 'cors',
        credentials: 'omit',
      });
      if (!spzResp.ok) throw new Error('HTTP ' + spzResp.status);
      const spzBuffer = await spzResp.arrayBuffer();
      fileSizeMB = (spzBuffer.byteLength / 1024 / 1024).toFixed(1);

      // Blob mit .spz MIME type erstellen
      const spzBlob = new Blob([spzBuffer], { type: 'application/octet-stream' });
      if (_spzBlobUrl) URL.revokeObjectURL(_spzBlobUrl);
      _spzBlobUrl = URL.createObjectURL(spzBlob);
      // GaussianSplats3D erkennt Format über Dateiname — wir nutzen path Parameter
      localBlobUrl = _spzBlobUrl;

      show3DStatus('SPZ geladen ✓', 95, `${fileSizeMB} MB · Renderer wird gestartet...`);
    } catch(fetchErr) {
      console.warn('Blob-Download fehlgeschlagen:', fetchErr.message);
      // Fallback: URL direkt übergeben
      localBlobUrl = spzUrl;
      show3DStatus('Direkt-URL wird verwendet...', 94, fetchErr.message);
    }

    // ── Schritt 2: GaussianSplats3D Viewer ──
    if (_gsViewer) { try { _gsViewer.dispose(); } catch(e) {} _gsViewer = null; }

    show3DStatus('3D-Renderer starten...', 96, 'GaussianSplats3D v0.4.7');

    const viewer = new GaussianSplats3D.Viewer({
      rootElement: container,
      selfDrivenMode: true,
      useWorkers: false,
      cameraUp: [0, 1, 0],
      initialCameraPosition: [0, 1.6, 3.5],
      initialCameraLookAt: [0, 1.0, 0],
      gpuAcceleratedSort: false,
      dynamicScene: false,
      logLevel: GaussianSplats3D.LogLevel ? GaussianSplats3D.LogLevel.None : 0,
    });

    _gsViewer = viewer;

    // SPZ Format explizit über path-Option übergeben
    const sceneOpts = {
      splatAlphaRemovalThreshold: 3,
      showLoadingUI: false,
      progressiveLoad: false,
      path: localBlobUrl,
    };

    // Format-Erkennung: GaussianSplats3D v0.4.7 erkennt SPZ über SceneFormat
    if (GaussianSplats3D.SceneFormat) {
      const fmts = GaussianSplats3D.SceneFormat;
      // Suche SPZ-Format-Wert
      const spzFmt = fmts.SPZ ?? fmts.Spz ?? Object.values(fmts).find(v => String(v).toLowerCase().includes('spz'));
      if (spzFmt !== undefined) sceneOpts.format = spzFmt;
    }

    await viewer.addSplatScene(localBlobUrl, sceneOpts);

    viewer.start();

    // Canvas vom Viewer explizit auf volle Größe setzen
    const viewerCanvas = container.querySelector('canvas');
    if (viewerCanvas) {
      viewerCanvas.style.width = '100%';
      viewerCanvas.style.height = '100%';
    }

    hide3DStatus();
    document.getElementById('r3dReadyBar').style.display = 'flex';
    r3dBadge(`✅ 3D-Raum geladen · ${fileSizeMB} MB`);
    toast('✅ 3D-Welt direkt geladen!');

  } catch(err) {
    console.error('GaussianSplats3D Fehler:', err);
    // Zeige Fehler sichtbar
    show3DStatus('Fehler: ' + err.message, 0, 'Öffne Marble als Fallback...');
    await new Promise(r => setTimeout(r, 2000));
    hide3DStatus();
    container.style.display = 'none';
    document.getElementById('r3dReadyBar').style.display = 'flex';
    const marbleUrl = document.getElementById('r3dOpenBtn').href;
    r3dBadge('⚠ Öffne in Marble...');
    window.open(marbleUrl, '_blank');
  }
}

function close3DRoom() {
  document.getElementById('room3dOverlay').style.display = 'none';
  document.body.style.overflow = '';
  // Cleanup GaussianSplats3D viewer + blob URL
  if (_gsViewer) {
    try { _gsViewer.dispose(); } catch(e) {}
    _gsViewer = null;
  }
  if (_panoGL) {
    if (_panoGL.animId) cancelAnimationFrame(_panoGL.animId);
    if (_panoGL.onResize) window.removeEventListener('resize', _panoGL.onResize);
    if (_panoGL.onKD) document.removeEventListener('keydown', _panoGL.onKD);
    if (_panoGL.onKU) document.removeEventListener('keyup',   _panoGL.onKU);
    try { _panoGL.renderer && _panoGL.renderer.dispose(); } catch(e) {}
    _panoGL = null;
  }
  if (_spzBlobUrl) { URL.revokeObjectURL(_spzBlobUrl); _spzBlobUrl = null; }
  const container = document.getElementById('splat3dContainer');
  if (container) { container.style.display = 'none'; container.innerHTML = ''; }
  const canvas = document.getElementById('room3dCanvas');
  if (canvas) canvas.style.display = 'none';
  if (_3d.engine) { _3d.engine.dispose(); _3d.engine = null; _3d.scene = null; }
  document.getElementById('r3dReadyBar').style.display = 'none';
}

// ══════════════════════════════════════════════════════
//  KI-ANALYSE TAB — IDW S 6 Sanierungskonzept
// ══════════════════════════════════════════════════════
var _anlPhotos = [];

function analyseHandleFiles(input) {
  Array.from(input.files).forEach(function(file) {
    var r = new FileReader();
    r.onload = function(e) {
      _anlPhotos.push({ full: e.target.result, base64: e.target.result.split(',')[1], name: file.name });
      renderAnalysePhotos();
    };
    r.readAsDataURL(file);
  });
}

function renderAnalysePhotos() {
  var grid = document.getElementById('analysePhotoGrid');
  if (!grid) return;
  grid.innerHTML = _anlPhotos.map(function(p, i) {
    return '<div class="photo-thumb" style="position:relative"><img src="' + p.full + '" style="width:100%;height:100%;object-fit:cover;border-radius:6px">' +
      '<button onclick="anlRemovePhoto(' + i + ')" style="position:absolute;top:4px;right:4px;background:rgba(0,0,0,0.6);border:none;color:#fff;border-radius:50%;width:20px;height:20px;cursor:pointer;font-size:11px;display:flex;align-items:center;justify-content:center">✕</button></div>';
  }).join('');
}

function anlRemovePhoto(idx) {
  _anlPhotos.splice(idx, 1);
  renderAnalysePhotos();
}

function anlSetStep(n) {
  for (var i = 1; i <= 4; i++) {
    var el = document.getElementById('anlStep' + i);
    if (!el) continue;
    var labels = ['Fotos analysieren','Schaden bewerten','IDW S 6 Konzept erstellen','Kostenschätzung'];
    if (i < n)      { el.style.background='rgba(52,208,127,0.15)'; el.style.color='var(--green)'; el.textContent='✅ ' + labels[i-1]; }
    else if (i === n){ el.style.background='rgba(79,124,255,0.15)'; el.style.color='var(--accent)'; el.textContent='⏳ ' + labels[i-1]; }
    else             { el.style.background='var(--bg3)'; el.style.color='var(--text3)'; el.textContent='⏳ ' + labels[i-1]; }
  }
}

async function runAnalyseTab() {
  var key = document.getElementById('openaiKey').value.trim();
  if (!key) { showErr('Bitte OpenAI Key eingeben.'); return; }
  if (!_anlPhotos.length && !document.getElementById('anlBeschreibung').value.trim()) {
    showErr('Bitte mindestens ein Foto oder eine Beschreibung eingeben.'); return;
  }

  var obj      = document.getElementById('anlObjekt').value || 'Unbekannt';
  var kunde    = document.getElementById('anlKunde').value || 'Unbekannt';
  var schaden  = document.getElementById('anlSchaden').value || 'Unbekannt';
  var flaeche  = document.getElementById('anlFlaeche').value || 'unbekannt';
  var vers     = document.getElementById('anlVers').value || 'Nein';
  var budget   = document.getElementById('anlBudget').value || '';
  var desc     = document.getElementById('anlBeschreibung').value.trim();
  var tech     = (typeof currentEmployee !== 'undefined' && currentEmployee) ? currentEmployee.name + ' (' + currentEmployee.role + ')' : 'Wiegand Team';
  var datum    = new Date().toLocaleDateString('de-DE');

  // Update header
  var ho = document.getElementById('anlHeaderObjekt');
  var hd = document.getElementById('anlHeaderDatum');
  if (ho) ho.textContent = obj + ' · ' + kunde;
  if (hd) hd.textContent = datum + ' · ' + tech;

  document.getElementById('anlBtn').disabled = true;
  document.getElementById('anlLoading').style.display = 'block';
  document.getElementById('anlResult').style.display = 'none';
  anlSetStep(1);

  var loadingText = document.getElementById('anlLoadingText');

  try {
    // Fotos analysieren
    if (loadingText) loadingText.textContent = 'Fotos werden analysiert...';
    anlSetStep(1);
    await new Promise(function(r){ setTimeout(r, 300); });

    anlSetStep(2);
    if (loadingText) loadingText.textContent = 'Schaden wird bewertet...';

    anlSetStep(3);
    if (loadingText) loadingText.textContent = 'IDW S 6 Sanierungskonzept wird erstellt...';

    // Nachrichten aufbauen
    var userPrompt = 'Auftragsdaten:\n' +
      '- Objekt: ' + obj + '\n' +
      '- Auftraggeber: ' + kunde + '\n' +
      '- Datum: ' + datum + '\n' +
      '- Schadensart: ' + schaden + '\n' +
      '- Fläche: ' + flaeche + ' m²\n' +
      '- Versicherungsfall: ' + vers + '\n' +
      '- Bearbeiter: ' + tech + '\n' +
      (budget ? '- Kostenziel: ' + budget + ' €\n' : '') +
      (desc ? '\nZusätzliche Beschreibung:\n' + desc + '\n' : '') +
      '\n' + IDW_S6_KONZEPT_PROMPT;

    var content = [{ type: 'text', text: userPrompt }];
    _anlPhotos.slice(0, 4).forEach(function(p) {
      content.push({ type: 'image_url', image_url: { url: 'data:image/jpeg;base64,' + p.base64, detail: 'high' } });
    });

    var resp = await fetch('https://api.openai.com/v1/chat/completions', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', 'Authorization': 'Bearer ' + key },
      body: JSON.stringify({
        model: 'gpt-4o',
        max_tokens: 4000,
        messages: [
          { role: 'system', content: SYSTEM_PROMPT },
          { role: 'user', content: content }
        ]
      })
    });

    var data = await resp.json();
    if (!resp.ok) throw new Error(data.error?.message || 'API Fehler ' + resp.status);

    anlSetStep(4);
    if (loadingText) loadingText.textContent = 'Kostenschätzung wird ergänzt...';
    await new Promise(function(r){ setTimeout(r, 400); });

    var text = data.choices[0].message.content;

    // Ergebnis anzeigen
    var rc = document.getElementById('anlResultContent');
    if (rc) rc.innerHTML = formatMD(text);

    // In History speichern
    var hist = JSON.parse(localStorage.getItem('wiegand_analysen') || '[]');
    hist.unshift({
      datum: datum, obj: obj, kunde: kunde, schaden: schaden,
      text: text, thumbnail: _anlPhotos[0]?.full || null, id: Date.now()
    });
    if (hist.length > 10) hist.pop();
    localStorage.setItem('wiegand_analysen', JSON.stringify(hist));
    renderAnalyseHistory();

    document.getElementById('anlLoading').style.display = 'none';
    document.getElementById('anlResult').style.display = 'block';
    document.getElementById('anlResult').scrollIntoView({ behavior: 'smooth' });
    toast('✅ IDW S 6 Sanierungskonzept erstellt');

  } catch(e) {
    document.getElementById('anlLoading').style.display = 'none';
    showErr('Analyse-Fehler: ' + e.message);
  }

  document.getElementById('anlBtn').disabled = false;
}

function renderAnalyseHistory() {
  var hist = JSON.parse(localStorage.getItem('wiegand_analysen') || '[]');
  var el = document.getElementById('anlHistory');
  if (!el) return;
  if (!hist.length) { el.innerHTML = '<div style="color:var(--text3);font-size:13px">Noch keine Analysen.</div>'; return; }
  el.innerHTML = hist.map(function(h) {
    return '<div style="display:flex;align-items:center;gap:12px;padding:10px;background:var(--bg3);border-radius:var(--rs);margin-bottom:7px;cursor:pointer" onclick="anlShowHistory(' + h.id + ')">' +
      (h.thumbnail ? '<img src="' + h.thumbnail + '" style="width:48px;height:36px;object-fit:cover;border-radius:6px;flex-shrink:0">' : '<div style="width:48px;height:36px;background:var(--bg4);border-radius:6px;display:flex;align-items:center;justify-content:center;font-size:1.2rem">📋</div>') +
      '<div style="flex:1;min-width:0"><div style="font-weight:600;font-size:12px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">' + (h.obj || 'Unbekannt') + ' · ' + (h.schaden || '') + '</div>' +
      '<div style="font-size:11px;color:var(--text3)">' + h.datum + ' · ' + (h.kunde || '') + '</div></div></div>';
  }).join('');
}

function anlShowHistory(id) {
  var hist = JSON.parse(localStorage.getItem('wiegand_analysen') || '[]');
  var entry = hist.find(function(h){ return h.id === id; });
  if (!entry) return;
  var rc = document.getElementById('anlResultContent');
  if (rc) rc.innerHTML = formatMD(entry.text);
  var ho = document.getElementById('anlHeaderObjekt');
  var hd = document.getElementById('anlHeaderDatum');
  if (ho) ho.textContent = (entry.obj || '') + ' · ' + (entry.kunde || '');
  if (hd) hd.textContent = entry.datum;
  document.getElementById('anlResult').style.display = 'block';
  document.getElementById('anlResult').scrollIntoView({ behavior: 'smooth' });
}

function anlPrint() {
  var content = document.getElementById('anlResultContent')?.innerHTML || '';
  var win = window.open('', '_blank');
  win.document.write('<html><head><title>IDW S 6 Sanierungskonzept</title><style>body{font-family:Arial,sans-serif;font-size:13px;line-height:1.7;max-width:800px;margin:40px auto;color:#1a1a1a}h3{color:#1a1a1a;border-bottom:1px solid #ddd;padding-bottom:4px}strong{color:#000}@media print{body{margin:20px}}</style></head><body>');
  win.document.write('<div style="text-align:center;margin-bottom:24px;border-bottom:2px solid #1a1a1a;padding-bottom:16px"><h2>Gebäudetrocknung Wiegand GbR</h2><p>Sanierungskonzept nach IDW S 6 · ' + new Date().toLocaleDateString('de-DE') + '</p></div>');
  win.document.write(content);
  win.document.write('</body></html>');
  win.document.close();
  win.focus();
  setTimeout(function(){ win.print(); }, 500);
}

function anlCopy() {
  var content = document.getElementById('anlResultContent')?.innerText || '';
  navigator.clipboard.writeText(content).then(function(){ toast('📋 In Zwischenablage kopiert'); });
}


function refreshProjekte() {
  const p=getProjects(), m=new Date().getMonth();
  document.getElementById('pStatTotal').textContent=p.length;
  document.getElementById('pStatMonth').textContent=p.filter(x=>new Date(x.date).getMonth()===m).length;
  document.getElementById('pStatViz').textContent=p.filter(x=>x.vizImages?.length||x.wizImages?.length).length;
  document.getElementById('pStatEmp').textContent=currentEmployee?.name?.split(' ')[0]||'–';
  const el=document.getElementById('projekteList'); if(!el) return;
  if(!p.length){el.innerHTML='<div style="color:var(--text3);text-align:center;padding:40px;font-size:13px">Noch keine Projekte.</div>';return;}
  el.innerHTML=p.map((x,i)=>`<div class="card" style="display:flex;align-items:center;gap:12px;padding:14px;cursor:pointer;margin-bottom:8px" onclick="loadProject(${i});showView('neu')">
    <div style="width:44px;height:44px;border-radius:8px;background:var(--bg3);display:flex;align-items:center;justify-content:center;font-size:1.3rem;flex-shrink:0">🏠</div>
    <div style="flex:1;min-width:0"><div style="font-weight:600;font-size:13px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${x.objekt||'Unbekannt'}</div>
    <div style="font-size:11px;color:var(--text3);margin-top:2px">${x.kunde||'–'} · ${new Date(x.date).toLocaleDateString('de-DE')}</div></div>
    <div style="font-size:11px;color:var(--accent);white-space:nowrap">${x.schadensart||'–'}</div></div>`).join('');
}

// ══════════════════════════════════════════════════════
//  KI-ANALYSE
// ══════════════════════════════════════════════════════
function analyseLoadFiles(input) {
  Array.from(input.files).forEach(file=>{
    const r=new FileReader(); r.onload=e=>{
      const g=document.getElementById('analyseGrid');
      if(g) g.innerHTML+=`<div class="photo-thumb"><img src="${e.target.result}" style="width:100%;height:100%;object-fit:cover"></div>`;
    }; r.readAsDataURL(file);
  });
  const h=document.getElementById('analyseHistoryList'), p=getProjects().filter(x=>x.analysis);
  if(h) h.innerHTML=p.length?p.slice(0,5).map(x=>`<div style="padding:9px;background:var(--bg3);border-radius:var(--rs);margin-bottom:5px;cursor:pointer" onclick="loadProject(getProjects().indexOf(x));showView('neu')"><div style="font-weight:600;font-size:12px">${x.objekt||'Unbekannt'}</div><div style="font-size:11px;color:var(--text3)">${new Date(x.date).toLocaleDateString('de-DE')}</div></div>`).join(''):'<div style="color:var(--text3);font-size:13px">Noch keine Analysen.</div>';
}

// ══════════════════════════════════════════════════════
//  GRUNDRISSE
// ══════════════════════════════════════════════════════
function grundrissUpload(input) {
  const file=input.files[0]; if(!file) return;
  const r=new FileReader(); r.onload=e=>{
    const prev=document.getElementById('grundrissPreview');
    if(prev) prev.innerHTML=`<img src="${e.target.result}" style="max-width:100%;border-radius:var(--rs)">`;
    const list=JSON.parse(localStorage.getItem('wiegand_grundrisse')||'[]');
    list.unshift({name:file.name,data:e.target.result,date:new Date().toLocaleDateString('de-DE')});
    if(list.length>10) list.pop();
    localStorage.setItem('wiegand_grundrisse',JSON.stringify(list));
    renderGrundrisse(); toast('📐 Grundriss gespeichert');
  }; r.readAsDataURL(file);
}
function renderGrundrisse() {
  const list=JSON.parse(localStorage.getItem('wiegand_grundrisse')||'[]');
  const el=document.getElementById('grundrissList'); if(!el) return;
  el.innerHTML=list.length?list.map((g,i)=>`<div style="display:flex;align-items:center;gap:8px;padding:9px;background:var(--bg3);border-radius:var(--rs);margin-bottom:6px;cursor:pointer" onclick="showGrundriss(${i})"><span style="font-size:1.2rem">📐</span><div style="flex:1;min-width:0"><div style="font-size:12px;font-weight:600;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${g.name}</div><div style="font-size:11px;color:var(--text3)">${g.date}</div></div></div>`).join(''):'<div style="color:var(--text3);font-size:13px">Keine Grundrisse.</div>';
  renderGrundrissRooms();
}
function showGrundriss(i) {
  const list=JSON.parse(localStorage.getItem('wiegand_grundrisse')||'[]');
  if(!list[i]) return;
  const prev=document.getElementById('grundrissPreview');
  if(prev) prev.innerHTML=`<img src="${list[i].data}" style="max-width:100%;border-radius:var(--rs)">`;
}
function addGrundrissRoom() {
  const name=prompt('Raumname:'); if(!name) return;
  const rooms=JSON.parse(localStorage.getItem('wiegand_rooms')||'[]');
  rooms.push({id:Date.now(),name,flaeche:''});
  localStorage.setItem('wiegand_rooms',JSON.stringify(rooms)); renderGrundrissRooms();
}
function renderGrundrissRooms() {
  const rooms=JSON.parse(localStorage.getItem('wiegand_rooms')||'[]');
  const el=document.getElementById('grundrissRooms'); if(!el) return;
  el.innerHTML=rooms.map(r=>`<div class="card" style="background:var(--bg3);padding:12px"><div style="font-weight:600;font-size:12px;margin-bottom:6px">🚪 ${r.name}</div><input placeholder="Fläche m²" value="${r.flaeche}" style="width:100%;background:var(--bg4);border:1px solid var(--border2);border-radius:6px;padding:5px 8px;color:var(--text);font-size:11px;outline:none;margin-bottom:5px"><button onclick="deleteRoom(${r.id})" style="font-size:10px;color:var(--red);background:none;border:none;cursor:pointer">Löschen</button></div>`).join('')
  +`<div style="background:var(--bg3);border:2px dashed var(--border2);border-radius:var(--r);padding:16px;text-align:center;cursor:pointer" onclick="addGrundrissRoom()"><div style="font-size:1.5rem">+</div><div style="font-size:11px;color:var(--text3);margin-top:4px">Raum</div></div>`;
}
function deleteRoom(id) {
  localStorage.setItem('wiegand_rooms',JSON.stringify(JSON.parse(localStorage.getItem('wiegand_rooms')||'[]').filter(r=>r.id!==id)));
  renderGrundrissRooms();
}

// ══════════════════════════════════════════════════════
//  3D-VIEWER TAB — Vollständiger Raum-Scanner
// ══════════════════════════════════════════════════════
var _v3dPhotos = [null, null, null, null]; // Nord, Ost, Süd, West
var _v3dModel  = 'Marble 0.1-mini';
var v3dDrawTool = 'wall';
var v3dIsDrawing = false;
var v3dStartPt   = null;
var v3dLines     = []; // {x1,y1,x2,y2,type}

function setV3DModel(m) {
  _v3dModel = m==='plus' ? 'Marble 0.1-plus' : 'Marble 0.1-mini';
  document.getElementById('v3dBtnMini')?.classList.toggle('active', m==='mini');
  document.getElementById('v3dBtnPlus')?.classList.toggle('active', m==='plus');
}

function v3dLoadPhoto(idx, input) {
  const file = input.files[0]; if (!file) return;
  const r = new FileReader();
  r.onload = e => {
    _v3dPhotos[idx] = { full: e.target.result, base64: e.target.result.split(',')[1], name: file.name };
    // Zeige Vorschau im Slot
    const slot = document.getElementById('v3dSlot' + idx);
    if (slot) {
      slot.style.border = '2px solid var(--accent)';
      slot.style.background = 'var(--bg4)';
      const existImg = slot.querySelector('img');
      if (existImg) existImg.remove();
      const img = document.createElement('img');
      img.src = e.target.result;
      img.style.cssText = 'position:absolute;inset:0;width:100%;height:100%;object-fit:cover;opacity:0.7';
      slot.appendChild(img);
      // Zeige RichtungsLabel
      const labels = ['NORD','OST','SÜD','WEST'];
      slot.querySelector('div:nth-child(2)').textContent = '✅ ' + labels[idx];
    }
    v3dUpdateStatus();
  };
  r.readAsDataURL(file);
}

function v3dUpdateStatus() {
  const count = _v3dPhotos.filter(Boolean).length;
  const statusEl = document.getElementById('v3dPhotoStatus');
  if (statusEl) statusEl.textContent = `${count} von 4 Fotos hochgeladen${count===0?' — mindestens 1 Foto erforderlich':count===1?' — gut, mehr = besser':count===2?' — schon besser!':count===3?' — fast perfekt!':' — optimal! ✅'}`;
  statusEl.style.color = count >= 4 ? 'var(--green)' : count >= 2 ? 'var(--amber)' : 'var(--text3)';

  // Start-Button aktivieren
  const btn = document.getElementById('v3dStartBtn');
  if (btn) btn.disabled = count === 0;

  // Qualitätsscore
  v3dUpdateQuality();

  // Maß-Vorschau
  v3dUpdateMassVorschau();
}

function v3dUpdateMassVorschau() {
  const l = parseFloat(document.getElementById('v3dLaenge')?.value||0);
  const b = parseFloat(document.getElementById('v3dBreite')?.value||0);
  const h = parseFloat(document.getElementById('v3dHoehe')?.value||0);
  const el = document.getElementById('v3dMassVorschau');
  if (!el) return;
  if (l && b && h) {
    const flaeche = (l * b).toFixed(1);
    const volumen = (l * b * h).toFixed(1);
    el.style.display = 'block';
    el.innerHTML = `📐 <strong>${l}m × ${b}m × ${h}m</strong> — Fläche: <strong>${flaeche} m²</strong> — Volumen: <strong>${volumen} m³</strong>`;
  } else {
    el.style.display = 'none';
  }
}

function v3dUpdateQuality() {
  const el = document.getElementById('v3dQualityInfo'); if (!el) return;
  const photos = _v3dPhotos.filter(Boolean).length;
  const l = parseFloat(document.getElementById('v3dLaenge')?.value||0);
  const b = parseFloat(document.getElementById('v3dBreite')?.value||0);
  const h = parseFloat(document.getElementById('v3dHoehe')?.value||0);
  const hasLines = v3dLines.length > 0;

  let score = 0;
  const tips = [];
  if (photos >= 1) { score += 30; } else { tips.push('📷 Mindestens 1 Foto hochladen'); }
  if (photos >= 2) { score += 15; }
  if (photos >= 4) { score += 20; tips.push = tips.push || (()=>{}); }
  if (l && b && h) { score += 25; } else { tips.push('📏 Raummaße eingeben (+25 Pkt)'); }
  if (hasLines)   { score += 10; } else { tips.push('✏️ Grundriss skizzieren (+10 Pkt)'); }

  const color = score >= 80 ? 'var(--green)' : score >= 50 ? 'var(--amber)' : 'var(--red)';
  const label = score >= 80 ? 'Exzellent' : score >= 50 ? 'Gut' : score >= 30 ? 'Ausreichend' : 'Minimal';
  el.innerHTML = `📊 <strong>Qualitätsscore: <span style="color:${color}">${score}/100 — ${label}</span></strong>${tips.length ? '<br><span style="font-size:11px">' + tips.join(' · ') + '</span>' : '<br><span style="font-size:11px;color:var(--green)">✅ Optimale Konfiguration für präzisen 3D-Raum</span>'}`;
}

// Maße-Input Listener
['v3dLaenge','v3dBreite','v3dHoehe','v3dFenster'].forEach(id => {
  document.addEventListener('input', e => { if (e.target.id === id) { v3dUpdateMassVorschau(); v3dUpdateQuality(); } });
});

// Grundriss Canvas
function v3dInitCanvas() {
  const canvas = document.getElementById('v3dCanvas'); if (!canvas) return;
  const ctx = canvas.getContext('2d');
  const colors = { wall: '#4f7cff', door: '#34d07f', window: '#f5a623' };

  function getPos(e) {
    const r = canvas.getBoundingClientRect();
    const scaleX = canvas.width  / r.width;
    const scaleY = canvas.height / r.height;
    const cl = e.touches ? e.touches[0] : e;
    return { x: (cl.clientX - r.left) * scaleX, y: (cl.clientY - r.top) * scaleY };
  }

  function redraw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    // Grid
    ctx.strokeStyle = 'rgba(255,255,255,0.05)';
    ctx.lineWidth = 1;
    for (let x = 0; x < canvas.width; x += 40) { ctx.beginPath(); ctx.moveTo(x,0); ctx.lineTo(x,canvas.height); ctx.stroke(); }
    for (let y = 0; y < canvas.height; y += 40) { ctx.beginPath(); ctx.moveTo(0,y); ctx.lineTo(canvas.width,y); ctx.stroke(); }
    // Alle Linien
    v3dLines.forEach(l => {
      ctx.strokeStyle = colors[l.type] || '#fff';
      ctx.lineWidth   = l.type === 'wall' ? 4 : 2;
      ctx.setLineDash(l.type === 'door' ? [8,4] : l.type === 'window' ? [4,4] : []);
      ctx.beginPath(); ctx.moveTo(l.x1,l.y1); ctx.lineTo(l.x2,l.y2); ctx.stroke();
    });
    ctx.setLineDash([]);
  }

  canvas.addEventListener('mousedown', e => { v3dIsDrawing=true; v3dStartPt=getPos(e); });
  canvas.addEventListener('mousemove', e => {
    if (!v3dIsDrawing || !v3dStartPt) return;
    redraw();
    const p = getPos(e);
    ctx.strokeStyle = colors[v3dDrawTool] || '#fff';
    ctx.lineWidth   = v3dDrawTool==='wall' ? 4 : 2;
    ctx.setLineDash(v3dDrawTool==='door' ? [8,4] : v3dDrawTool==='window' ? [4,4] : []);
    ctx.beginPath(); ctx.moveTo(v3dStartPt.x,v3dStartPt.y); ctx.lineTo(p.x,p.y); ctx.stroke();
    ctx.setLineDash([]);
  });
  canvas.addEventListener('mouseup', e => {
    if (!v3dIsDrawing || !v3dStartPt) return;
    const p = getPos(e);
    if (Math.abs(p.x-v3dStartPt.x) > 5 || Math.abs(p.y-v3dStartPt.y) > 5) {
      v3dLines.push({ x1:v3dStartPt.x, y1:v3dStartPt.y, x2:p.x, y2:p.y, type:v3dDrawTool });
    }
    v3dIsDrawing=false; v3dStartPt=null;
    redraw(); v3dUpdateQuality();

    // Tool-Buttons highlighten
    ['wall','door','window'].forEach(t => {
      const btn = document.getElementById('v3dTool'+t.charAt(0).toUpperCase()+t.slice(1));
      if (btn) { btn.style.borderColor = t===v3dDrawTool ? 'var(--accent)' : ''; btn.style.color = t===v3dDrawTool ? 'var(--accent)' : ''; }
    });
  });
  canvas.addEventListener('mouseleave', () => { if(v3dIsDrawing){v3dIsDrawing=false;v3dStartPt=null;} });

  // Tool-Button Highlighting
  document.getElementById('v3dToolWall')?.addEventListener('click', () => {
    v3dDrawTool='wall';
    ['Wall','Door','Window'].forEach(t => { const b=document.getElementById('v3dTool'+t); if(b){b.style.borderColor='';b.style.color='';} });
    document.getElementById('v3dToolWall').style.borderColor='var(--accent)'; document.getElementById('v3dToolWall').style.color='var(--accent)';
  });
  document.getElementById('v3dToolDoor')?.addEventListener('click', () => {
    v3dDrawTool='door';
    ['Wall','Door','Window'].forEach(t => { const b=document.getElementById('v3dTool'+t); if(b){b.style.borderColor='';b.style.color='';} });
    document.getElementById('v3dToolDoor').style.borderColor='var(--green)'; document.getElementById('v3dToolDoor').style.color='var(--green)';
  });
  document.getElementById('v3dToolWindow')?.addEventListener('click', () => {
    v3dDrawTool='window';
    ['Wall','Door','Window'].forEach(t => { const b=document.getElementById('v3dTool'+t); if(b){b.style.borderColor='';b.style.color='';} });
    document.getElementById('v3dToolWindow').style.borderColor='var(--amber)'; document.getElementById('v3dToolWindow').style.color='var(--amber)';
  });

  redraw();
}

// ══════════════════════════════════════════════════════
//  KI-GRUNDRISS GENERATOR
// ══════════════════════════════════════════════════════
var _grPhotos = [null, null, null, null];
var _grResult = null;

function grLoadPhoto(idx, input) {
  const file = input.files[0]; if (!file) return;
  const r = new FileReader();
  r.onload = e => {
    _grPhotos[idx] = { full: e.target.result, base64: e.target.result.split(',')[1] };
    const slot = document.getElementById('grSlot' + idx);
    if (slot) {
      slot.style.border = '2px solid var(--accent)';
      const img = document.createElement('img');
      img.src = e.target.result;
      img.style.cssText = 'position:absolute;inset:0;width:100%;height:100%;object-fit:cover;opacity:0.8';
      const existing = slot.querySelector('img');
      if (existing) existing.remove();
      slot.appendChild(img);
    }
    const count = _grPhotos.filter(Boolean).length;
    const statusEl = document.getElementById('grPhotoStatus');
    if (statusEl) statusEl.textContent = count + ' Foto' + (count !== 1 ? 's' : '') + ' hochgeladen' + (count >= 4 ? ' — optimal ✅' : count >= 2 ? ' — gut' : ' — mehr = besser');
    const btn = document.getElementById('grGenerateBtn');
    if (btn) btn.disabled = count === 0;
  };
  r.readAsDataURL(file);
}

async function generateGrundriss() {
  // Nutze alle Scan-Frames wenn vorhanden, sonst _grPhotos
  var grPhotos;
  if (typeof _scanFrames !== 'undefined' && _scanFrames.length > 0) {
    // Wähle 6 gleichmäßig verteilte Frames aus dem gesamten Scan
    var total = _scanFrames.length;
    var picks = [0, 0.15, 0.3, 0.5, 0.7, 0.9].map(function(p) {
      return _scanFrames[Math.min(Math.floor(p*total), total-1)];
    });
    grPhotos = picks.map(function(f) { return { base64: f.split(',')[1] }; });
  } else {
    grPhotos = _grPhotos.filter(Boolean);
  }
  if (!grPhotos.length) { showErr('Bitte zuerst den Raum scannen.'); return; }
  const key = document.getElementById('openaiKey').value.trim();
  if (!key) { showErr('Bitte OpenAI Key eingeben.'); return; }

  document.getElementById('grLoading').style.display = 'block';
  document.getElementById('grResult').style.display = 'none';
  document.getElementById('grGenerateBtn').disabled = true;
  document.getElementById('grLoadingText').textContent = 'KI analysiert ' + grPhotos.length + ' Foto(s)...';

  try {
    const content = [];
    const dirs = ['Vorne', 'Rechts', 'Hinten', 'Links'];
    grPhotos.forEach(function(p, i) {
      content.push({ type: 'image_url', image_url: { url: 'data:image/jpeg;base64,' + p.base64, detail: 'low' } });
    });
    content.push({
      type: 'text',
      text: `Analyze this room and return ONLY a JSON object. No explanation, no markdown, just the JSON.

Use furniture for scale: door=80cm wide, 200cm tall; sofa=210cm; TV=120cm; bed=140cm; window=100-150cm.

Return exactly this structure:
{"laenge":5.2,"breite":4.1,"hoehe":2.6,"raumtyp":"Wohnzimmer","flaeche":21.3,"waende":[[0,0,520,0],[520,0,520,410],[520,410,0,410],[0,410,0,0]],"tueren":[[100,0,180,0]],"fenster":[[300,0,430,0]],"begruendung":"sofa approx 210cm used as reference"}`
    });

    document.getElementById('grLoadingText').textContent = 'GPT-4o erstellt Grundriss...';

    const resp = await fetch('https://api.openai.com/v1/chat/completions', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', 'Authorization': 'Bearer ' + key },
      body: JSON.stringify({
        model: 'gpt-4o',
        max_tokens: 800,
        temperature: 0,
        messages: [{ role: 'user', content }]
      })
    });
    const data = await resp.json();
    if (!resp.ok) throw new Error(data.error?.message || 'API Fehler ' + resp.status);

    let raw = data.choices[0].message.content.trim();
    raw = raw.replace(/```json/g,'').replace(/```/g,'').trim();
    const s = raw.indexOf('{'), e = raw.lastIndexOf('}');
    if (s === -1 || e === -1) throw new Error('Keine JSON-Daten erhalten. Antwort: ' + raw.slice(0,100));
    const result = JSON.parse(raw.slice(s, e+1));

    // Konvertiere altes Format falls nötig
    if (!result.einheiten && result.waende) {
      result.einheiten = [];
      (result.waende||[]).forEach(w => result.einheiten.push({typ:'wand', von:[w[0],w[1]], bis:[w[2],w[3]]}));
      (result.tueren||[]).forEach(t => result.einheiten.push({typ:'tuer', von:[t[0],t[1]], bis:[t[2],t[3]]}));
      (result.fenster||[]).forEach(f => result.einheiten.push({typ:'fenster', von:[f[0],f[1]], bis:[f[2],f[3]]}));
    }

    // Beschriftungen ergänzen
    result.einheiten.forEach(e => {
      if (!e.beschriftung) {
        const dx = Math.abs(e.bis[0]-e.von[0]), dy = Math.abs(e.bis[1]-e.von[1]);
        const len = Math.round(Math.sqrt(dx*dx+dy*dy));
        if (len > 0) e.beschriftung = (len/100).toFixed(2)+'m';
      }
    });

    _grResult = result;
    document.getElementById('grLoadingText').textContent = 'Grundriss wird gezeichnet...';
    grDrawFloorplan(result);

    const mEl = document.getElementById('grMeasurements');
    if (mEl) mEl.innerHTML = `<strong>📐 ${result.raumtyp||'Raum'}</strong> &nbsp;·&nbsp; ${result.laenge}m × ${result.breite}m × ${result.hoehe}m &nbsp;·&nbsp; <strong>${result.flaeche||(result.laenge*result.breite).toFixed(1)} m²</strong><br><span style="color:var(--text3);font-size:11px">💡 ${result.begruendung||''}</span>`;

    document.getElementById('grResult').style.display = 'block';
    toast('✅ Grundriss erstellt!');

  } catch(e) {
    showErr('Grundriss: ' + e.message);
    console.error('Grundriss Fehler:', e);
  } finally {
    document.getElementById('grLoading').style.display = 'none';
    document.getElementById('grGenerateBtn').disabled = false;
  }
}


function grDrawFloorplan(data) {
  const canvas = document.getElementById('grCanvas');
  if (!canvas) return;

  // Finde Bounding Box
  const allX = [], allY = [];
  (data.einheiten || []).forEach(e => { allX.push(e.von[0], e.bis[0]); allY.push(e.von[1], e.bis[1]); });
  if (!allX.length) { allX.push(0, (data.laenge||5)*100); allY.push(0, (data.breite||4)*100); }

  const minX = Math.min(...allX), maxX = Math.max(...allX);
  const minY = Math.min(...allY), maxY = Math.max(...allY);
  const roomW = maxX - minX || 500;
  const roomH = maxY - minY || 400;

  const PAD = 60;
  const canvasW = 800, canvasH = Math.round(canvasW * (roomH / roomW)) + PAD * 2;
  canvas.width  = canvasW;
  canvas.height = Math.max(canvasH, 400);

  const scale = (canvasW - PAD * 2) / roomW;
  const ctx = canvas.getContext('2d');

  // Hintergrund
  ctx.fillStyle = '#ffffff';
  ctx.fillRect(0, 0, canvas.width, canvas.height);

  // Raster
  ctx.strokeStyle = '#f0f0f0'; ctx.lineWidth = 1;
  const gridSize = 50 * scale;
  for (let x = PAD; x < canvasW; x += gridSize) { ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke(); }
  for (let y = PAD; y < canvas.height; y += gridSize) { ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvasW, y); ctx.stroke(); }

  function tx(x) { return PAD + (x - minX) * scale; }
  function ty(y) { return PAD + (y - minY) * scale; }

  // Möbel zuerst (darunter)
  ctx.font = '11px sans-serif';
  (data.moebel || []).forEach(m => {
    const mx = tx(m.x), my = ty(m.y), mw = m.breite * scale, mh = m.tiefe * scale;
    ctx.fillStyle = 'rgba(200,220,255,0.5)';
    ctx.strokeStyle = '#8ab4ff'; ctx.lineWidth = 1;
    ctx.fillRect(mx, my, mw, mh);
    ctx.strokeRect(mx, my, mw, mh);
    ctx.fillStyle = '#555';
    ctx.textAlign = 'center';
    ctx.fillText(m.name, mx + mw/2, my + mh/2 + 4);
  });

  // Wände, Türen, Fenster
  (data.einheiten || []).forEach(e => {
    const x1 = tx(e.von[0]), y1 = ty(e.von[1]), x2 = tx(e.bis[0]), y2 = ty(e.bis[1]);
    ctx.lineCap = 'round';

    if (e.typ === 'wand') {
      ctx.strokeStyle = '#1a1a1a'; ctx.lineWidth = 5;
      ctx.setLineDash([]);
    } else if (e.typ === 'tuer') {
      ctx.strokeStyle = '#34d07f'; ctx.lineWidth = 3;
      ctx.setLineDash([6, 3]);
    } else if (e.typ === 'fenster') {
      ctx.strokeStyle = '#4f7cff'; ctx.lineWidth = 4;
      ctx.setLineDash([]);
    }
    ctx.beginPath(); ctx.moveTo(x1, y1); ctx.lineTo(x2, y2); ctx.stroke();
    ctx.setLineDash([]);

    // Beschriftung
    if (e.beschriftung) {
      const mx = (x1 + x2) / 2, my = (y1 + y2) / 2;
      const isHoriz = Math.abs(y2 - y1) < Math.abs(x2 - x1);
      ctx.save();
      ctx.translate(mx, my);
      if (!isHoriz) ctx.rotate(-Math.PI / 2);
      ctx.font = 'bold 11px sans-serif';
      ctx.fillStyle = e.typ === 'fenster' ? '#4f7cff' : e.typ === 'tuer' ? '#34d07f' : '#333';
      ctx.textAlign = 'center';
      ctx.fillStyle = 'rgba(255,255,255,0.85)';
      const tw = ctx.measureText(e.beschriftung).width;
      ctx.fillRect(-tw/2 - 3, -8, tw + 6, 14);
      ctx.fillStyle = e.typ === 'fenster' ? '#4f7cff' : e.typ === 'tuer' ? '#1a9e5c' : '#333';
      ctx.fillText(e.beschriftung, 0, 3);
      ctx.restore();
    }
  });

  // Kompassrose
  ctx.font = 'bold 13px sans-serif';
  ctx.fillStyle = '#666';
  ctx.textAlign = 'center';
  ctx.fillText('N', PAD + roomW*scale/2, 20);

  // Legende
  const legX = canvasW - 130, legY = 20;
  ctx.font = '11px sans-serif'; ctx.textAlign = 'left';
  [['#1a1a1a', 5, [], 'Wand'], ['#34d07f', 3, [6,3], 'Tür'], ['#4f7cff', 4, [], 'Fenster']].forEach(([col, lw, dash, lbl], i) => {
    const y = legY + i * 18;
    ctx.strokeStyle = col; ctx.lineWidth = lw; ctx.setLineDash(dash);
    ctx.beginPath(); ctx.moveTo(legX, y+5); ctx.lineTo(legX+25, y+5); ctx.stroke();
    ctx.setLineDash([]);
    ctx.fillStyle = '#333';
    ctx.fillText(lbl, legX + 30, y + 9);
  });

  // Maßstab-Balken
  const scaleBarCm = 100; // 1m
  const scaleBarPx = scaleBarCm * scale;
  const sbX = PAD, sbY = canvas.height - 25;
  ctx.strokeStyle = '#333'; ctx.lineWidth = 2;
  ctx.beginPath(); ctx.moveTo(sbX, sbY); ctx.lineTo(sbX + scaleBarPx, sbY); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(sbX, sbY-5); ctx.lineTo(sbX, sbY+5); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(sbX+scaleBarPx, sbY-5); ctx.lineTo(sbX+scaleBarPx, sbY+5); ctx.stroke();
  ctx.font = '11px sans-serif'; ctx.fillStyle = '#333'; ctx.textAlign = 'center';
  ctx.fillText('1m', sbX + scaleBarPx/2, sbY + 14);
}

function grDownload() {
  const canvas = document.getElementById('grCanvas'); if (!canvas) return;
  const a = document.createElement('a');
  a.href = canvas.toDataURL('image/png');
  a.download = 'grundriss-' + new Date().toLocaleDateString('de-DE').replace(/\./g,'-') + '.png';
  a.click();
  toast('📥 Grundriss gespeichert');
}

function grSaveToList() {
  const canvas = document.getElementById('grCanvas'); if (!canvas) return;
  const list = JSON.parse(localStorage.getItem('wiegand_grundrisse') || '[]');
  const name = (_grResult?.raumtyp || 'Raum') + ' ' + new Date().toLocaleDateString('de-DE');
  list.unshift({ name, data: canvas.toDataURL('image/png'), date: new Date().toLocaleDateString('de-DE') });
  if (list.length > 20) list.pop();
  localStorage.setItem('wiegand_grundrisse', JSON.stringify(list));
  renderGrundrisse();
  toast('💾 Grundriss in Liste gespeichert');
}

function grUseFor3D() {
  const canvas = document.getElementById('grCanvas'); if (!canvas) return;
  const dataUrl = canvas.toDataURL('image/png');
  // In 3D-Viewer Fotos übernehmen
  _v3dPhotos[0] = { full: dataUrl, base64: dataUrl.split(',')[1], name: 'grundriss.png' };
  photos = [{ full: dataUrl, base64: dataUrl.split(',')[1], name: 'grundriss.png' }];
  if (_grResult) {
    cachedRoomType = _grResult.raumtyp || 'Innenraum';
    cachedRoomAnalysis = Object.assign(cachedRoomAnalysis || {}, {
      fixedElements: `Room: ${_grResult.laenge}m x ${_grResult.breite}m x ${_grResult.hoehe}m`
    });
  }
  renderPhotos();
  showView('neu');
  toast('🏠 Grundriss übertragen — 3D-Raum generieren im Neuer Auftrag');
}

// Raum-Scanner Fotos auch in Grundriss-Slots übertragen
function grLoadFromScan(scanPhotos) {
  // Frames intern speichern — KEINE Bilder in der UI anzeigen
  _grPhotos = scanPhotos.slice(0,4);
  while (_grPhotos.length < 4) _grPhotos.push(null);
  // Scan-Status aktualisieren
  var statusEl = document.getElementById('grScanStatus');
  var doneEl   = document.getElementById('grScanDone');
  var infoEl   = document.getElementById('grScanInfo');
  var btn      = document.getElementById('grGenerateBtn');
  if (statusEl) statusEl.style.display = 'none';
  if (doneEl)   doneEl.style.display = 'flex';
  if (infoEl)   infoEl.textContent = scanPhotos.length + ' Frames aufgenommen — bereit für Grundriss';
  if (btn)      { btn.disabled = false; btn.style.opacity = '1'; }
}


function grUseFor3D() {
  const canvas = document.getElementById('grCanvas'); if (!canvas) return;
  const dataUrl = canvas.toDataURL('image/png');
  // In 3D-Viewer Fotos übernehmen
  _v3dPhotos[0] = { full: dataUrl, base64: dataUrl.split(',')[1], name: 'grundriss.png' };
  photos = [{ full: dataUrl, base64: dataUrl.split(',')[1], name: 'grundriss.png' }];
  if (_grResult) {
    cachedRoomType = _grResult.raumtyp || 'Innenraum';
    cachedRoomAnalysis = Object.assign(cachedRoomAnalysis || {}, {
      fixedElements: `Room: ${_grResult.laenge}m x ${_grResult.breite}m x ${_grResult.hoehe}m`
    });
  }
  renderPhotos();
  showView('neu');
  toast('🏠 Grundriss übertragen — 3D-Raum generieren im Neuer Auftrag');
}

// Raum-Scanner Fotos auch in Grundriss-Slots übertragen
function grLoadFromScan(photos) {
  photos.forEach((p, i) => {
    if (!p || i > 3) return;
    _grPhotos[i] = p;
    const slot = document.getElementById('grSlot' + i);
    if (slot) {
      slot.style.border = '2px solid var(--green)';
      const img = document.createElement('img');
      img.src = p.full;
      img.style.cssText = 'position:absolute;inset:0;width:100%;height:100%;object-fit:cover;opacity:0.8';
      slot.appendChild(img);
    }
  });
  const count = _grPhotos.filter(Boolean).length;
  const s = document.getElementById('grPhotoStatus');
  if (s) s.textContent = count + ' Fotos vom Scanner übertragen ✅';
  const btn = document.getElementById('grGenerateBtn');
  if (btn) btn.disabled = false;
}

function grundrissHighlightTool(tool) {
  var colors = {wall:'var(--accent)', door:'var(--green)', window:'var(--amber)'};
  ['Wall','Door','Window'].forEach(function(t) {
    var btn = document.getElementById('v3dTool'+t);
    if (!btn) return;
    var key = t.toLowerCase();
    btn.style.borderColor = key===tool ? colors[key] : '';
    btn.style.color       = key===tool ? colors[key] : '';
  });
}

function v3dClearCanvas() {
  v3dLines = [];
  const canvas = document.getElementById('v3dCanvas'); if (!canvas) return;
  const ctx = canvas.getContext('2d');
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  // Redraw grid
  ctx.strokeStyle = 'rgba(255,255,255,0.05)'; ctx.lineWidth = 1;
  for (let x=0;x<canvas.width;x+=40){ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x,canvas.height);ctx.stroke();}
  for (let y=0;y<canvas.height;y+=40){ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(canvas.width,y);ctx.stroke();}
  v3dUpdateQuality();
}

function v3dBuildTextPrompt() {
  const l = parseFloat(document.getElementById('v3dLaenge')?.value||0);
  const b = parseFloat(document.getElementById('v3dBreite')?.value||0);
  const h = parseFloat(document.getElementById('v3dHoehe')?.value||0);
  const typ = document.getElementById('v3dRaumtyp')?.value || 'Innenraum';
  const fenster = document.getElementById('v3dFenster')?.value || '';
  const bes = document.getElementById('v3dBesonderheiten')?.value?.trim() || '';

  const parts = [
    `Photorealistic interior: ${typ}.`,
    l && b && h ? `Exact room dimensions: ${l}m wide, ${b}m deep, ${h}m high. Floor area: ${(l*b).toFixed(1)} square meters.` : '',
    fenster ? `${fenster} window${fenster>1?'s':''} in the room.` : '',
    bes ? `Special features: ${bes}.` : '',
    'Full 360-degree spatial reconstruction matching exact proportions.',
    'Photorealistic rendering, accurate scale, no distortion.',
    'Sharp details, professional architectural quality.'
  ];
  return parts.filter(Boolean).join(' ');
}

function v3dGenerateFromCanvas() {
  if (v3dLines.length === 0) { showErr('Bitte zuerst einen Grundriss zeichnen.'); return; }
  const canvas = document.getElementById('v3dCanvas');
  if (!canvas) return;
  // Canvas als Bild verwenden
  const dataUrl = canvas.toDataURL('image/png');
  _v3dPhotos[0] = { full: dataUrl, base64: dataUrl.split(',')[1], name: 'grundriss.png' };
  toast('✏️ Grundriss als Hauptbild gesetzt — jetzt generieren!');
  v3dUpdateStatus();
}

async function v3dAutoDetectMeasurements() {
  const photo = _v3dPhotos.find(Boolean);
  if (!photo) { showErr('Bitte zuerst mindestens 1 Foto hochladen.'); return; }

  const key = document.getElementById('openaiKey').value.trim();
  if (!key) { showErr('Bitte OpenAI Key eingeben.'); return; }

  const btn = document.getElementById('v3dAutoBtn');
  const status = document.getElementById('v3dAutoStatus');
  btn.disabled = true;
  btn.textContent = '⏳ KI analysiert...';
  status.style.display = 'block';
  status.innerHTML = '🤖 GPT-4o analysiert das Foto und schätzt die Raummaße...';

  try {
    const resp = await fetch('https://api.openai.com/v1/chat/completions', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', 'Authorization': 'Bearer ' + key },
      body: JSON.stringify({
        model: 'gpt-4o',
        max_tokens: 400,
        messages: [{
          role: 'user',
          content: [
            {
              type: 'image_url',
              image_url: { url: 'data:image/jpeg;base64,' + photo.base64, detail: 'high' }
            },
            {
              type: 'text',
              text: `You are an expert interior architect. Analyze this room photo and estimate the dimensions.

You MUST respond with ONLY a valid JSON object. No explanation before or after. No markdown. Just the raw JSON.

Example response:
{"laenge":4.5,"breite":3.2,"hoehe":2.6,"raumtyp":"Wohnzimmer","fenster":2,"besonderheiten":"TV-Wand gegenüber Couch","begruendung":"Sofa ca 2.2m, Raum ca 2x Sofa breit"}

Use furniture as scale reference:
- Standard door: 200cm tall, 80cm wide
- Sofa: 200-230cm long
- TV: 120-150cm wide  
- Dining chair: 45cm wide
- Bed: 140-180cm wide
- Kitchen counter: 60cm deep

Room types: Badezimmer, Wohnzimmer, Schlafzimmer, Küche, Keller, Flur, Büro, Sonstiges

Respond with ONLY the JSON, nothing else.`
            }
          ]
        }]
      })
    });

    const data = await resp.json();
    if (!resp.ok) throw new Error(data.error?.message || 'API Fehler');

    let raw = data.choices[0].message.content.trim();
    // Remove markdown code blocks if present
    raw = raw.replace(/```json/g, '').replace(/```/g, '').trim();
    // Find JSON object
    const matchStart = raw.indexOf('{');
    const matchEnd = raw.lastIndexOf('}');
    if (matchStart === -1 || matchEnd === -1) throw new Error('Keine JSON-Daten in Antwort');
    let result;
    try {
      result = JSON.parse(raw.slice(matchStart, matchEnd + 1));
    } catch(parseErr) {
      throw new Error('JSON konnte nicht gelesen werden: ' + parseErr.message);
    }

    // Felder befüllen
    if (result.laenge) document.getElementById('v3dLaenge').value = result.laenge;
    if (result.breite) document.getElementById('v3dBreite').value = result.breite;
    if (result.hoehe)  document.getElementById('v3dHoehe').value  = result.hoehe;
    if (result.fenster) document.getElementById('v3dFenster').value = result.fenster;
    if (result.raumtyp) {
      const sel = document.getElementById('v3dRaumtyp');
      if (sel) {
        const opt = Array.from(sel.options).find(o => o.value === result.raumtyp);
        if (opt) sel.value = result.raumtyp;
      }
    }
    if (result.besonderheiten) document.getElementById('v3dBesonderheiten').value = result.besonderheiten;

    status.innerHTML = `✅ <strong>KI-Schätzung fertig!</strong><br>
      📐 ${result.laenge}m × ${result.breite}m × ${result.hoehe}m — ${result.raumtyp}<br>
      <span style="color:var(--text3);font-size:11px">💡 ${result.begruendung}</span><br>
      <span style="color:var(--amber);font-size:11px">⚠️ Schätzung — bitte mit Maßband prüfen für maximale Genauigkeit</span>`;

    v3dUpdateMassVorschau();
    v3dUpdateQuality();
    toast('✅ Maße erkannt — bitte prüfen und ggf. korrigieren');

  } catch(e) {
    status.innerHTML = '❌ Fehler: ' + e.message;
  } finally {
    btn.disabled = false;
    btn.textContent = '🤖 KI erkennt Maße automatisch';
  }
}


async function startViewer3D() {
  const firstPhoto = _v3dPhotos.find(Boolean);
  if (!firstPhoto) { showErr('Bitte mindestens 1 Foto hochladen.'); return; }

  // Fotos ins Haupt-Foto-Array übernehmen (für open3DRoom Kompatibilität)
  photos = _v3dPhotos.filter(Boolean).map((p, i) => ({
    base64: p.base64, full: p.full, name: p.name || `foto_${i}.jpg`
  }));

  // Text-Prompt mit Maßen bauen und in cachedRoomAnalysis injizieren
  const prompt = v3dBuildTextPrompt();
  cachedRoomType = document.getElementById('v3dRaumtyp')?.value || 'Innenraum';

  // Inject dimensions into the room analysis cache so open3DRoom uses them
  const l = parseFloat(document.getElementById('v3dLaenge')?.value||0);
  const b = parseFloat(document.getElementById('v3dBreite')?.value||0);
  const h = parseFloat(document.getElementById('v3dHoehe')?.value||0);
  if (l && b && h) {
    cachedRoomAnalysis = Object.assign(cachedRoomAnalysis || {}, {
      fixedElements: `Room is ${l}m x ${b}m x ${h}m`,
      renovationMaterials: cachedRoomAnalysis?.renovationMaterials || 'modern high-quality finishes'
    });
  }

  // Modell setzen
  var saved = _wlModel;
  _wlModel = _v3dModel;
  await open3DRoom();
  _wlModel = saved;
}

// Canvas beim Anzeigen des Views initialisieren
document.addEventListener('DOMContentLoaded', function() {
  // v3dInitCanvas wird beim ersten showView('viewer3d') aufgerufen
});

// ══════════════════════════════════════════════════════
//  KONTAKTE
// ══════════════════════════════════════════════════════
function getKontakte(){try{return JSON.parse(localStorage.getItem('wiegand_kontakte')||'[]');}catch{return[];}}
function saveKontakte(k){try{localStorage.setItem('wiegand_kontakte',JSON.stringify(k));}catch{}}
function addKontakt() {
  const name=prompt('Name:'); if(!name) return;
  const firma=prompt('Firma (optional):')||'';
  const tel=prompt('Telefon:')||'';
  const email=prompt('E-Mail:')||'';
  const typ=prompt('Typ (kunde/versicherung/lieferant/partner):','kunde')||'kunde';
  saveKontakte([{name,firma,tel,email,typ,id:Date.now()},...getKontakte()]);
  renderKontakte(); toast('👤 Kontakt gespeichert');
}
function renderKontakte() {
  const q=(document.getElementById('kontaktSearch')?.value||'').toLowerCase();
  const f=document.getElementById('kontaktFilter')?.value||'';
  const all=getKontakte().filter(k=>(!f||k.typ===f)&&(!q||k.name.toLowerCase().includes(q)||(k.firma||'').toLowerCase().includes(q)));
  const el=document.getElementById('kontakteGrid'); if(!el) return;
  const tc={kunde:'var(--accent)',versicherung:'var(--green)',lieferant:'var(--amber)',partner:'var(--purple)'};
  el.innerHTML=all.length?all.map(k=>`<div class="card" style="padding:14px">
    <div style="display:flex;align-items:center;gap:10px;margin-bottom:10px">
      <div style="width:38px;height:38px;border-radius:50%;background:var(--bg3);display:flex;align-items:center;justify-content:center;font-size:1.1rem;flex-shrink:0">👤</div>
      <div style="flex:1;min-width:0"><div style="font-weight:600;font-size:13px">${k.name}</div><div style="font-size:11px;color:var(--text3)">${k.firma||''}</div></div>
      <span style="font-size:10px;font-weight:700;color:${tc[k.typ]||'var(--text3)'};background:rgba(255,255,255,0.07);padding:2px 8px;border-radius:10px">${k.typ}</span>
    </div>
    ${k.tel?`<div style="font-size:12px;color:var(--text2);margin-bottom:3px">📞 ${k.tel}</div>`:''}
    ${k.email?`<div style="font-size:12px;color:var(--text2);margin-bottom:8px">✉️ ${k.email}</div>`:''}
    <div style="display:flex;gap:6px;flex-wrap:wrap">
      ${k.tel?`<a href="tel:${k.tel}" class="btn btn-ghost btn-sm" style="font-size:11px">Anrufen</a>`:''}
      ${k.email?`<a href="mailto:${k.email}" class="btn btn-ghost btn-sm" style="font-size:11px">E-Mail</a>`:''}
      <button onclick="deleteKontakt(${k.id})" class="btn btn-red btn-sm" style="font-size:11px;margin-left:auto">✕</button>
    </div></div>`).join(''):'<div style="color:var(--text3);font-size:13px;padding:20px">Keine Kontakte.</div>';
}
function deleteKontakt(id){if(!confirm('Löschen?'))return;saveKontakte(getKontakte().filter(k=>k.id!==id));renderKontakte();}

// ══════════════════════════════════════════════════════
//  AUFGABEN
// ══════════════════════════════════════════════════════
function getAufgaben(){try{return JSON.parse(localStorage.getItem('wiegand_aufgaben')||'[]');}catch{return[];}}
function saveAufgaben(a){try{localStorage.setItem('wiegand_aufgaben',JSON.stringify(a));}catch{}}
var _aufgabeTab='alle';
function setAufgabeTab(t) {
  _aufgabeTab=t;
  ['alle','offen','inarbeit','erledigt'].forEach(x=>document.getElementById('aufgabeTab-'+x)?.classList.toggle('active',x===t));
  renderAufgaben();
}
function addAufgabe() {
  const titel=prompt('Aufgabe:'); if(!titel) return;
  const prio=prompt('Priorität (hoch/mittel/niedrig):','mittel')||'mittel';
  const proj=prompt('Projekt (optional):')||'';
  const faellig=prompt('Fällig (TT.MM.JJJJ, optional):')||'';
  saveAufgaben([{id:Date.now(),titel,prio,proj,faellig,status:'offen'},...getAufgaben()]);
  renderAufgaben(); toast('✅ Aufgabe hinzugefügt');
}
function renderAufgaben() {
  let a=getAufgaben(); if(_aufgabeTab!=='alle') a=a.filter(x=>x.status===_aufgabeTab);
  const el=document.getElementById('aufgabenList'); if(!el) return;
  const pc={hoch:'var(--red)',mittel:'var(--amber)',niedrig:'var(--green)'};
  const si={offen:'○',inarbeit:'◉',erledigt:'✓'};
  el.innerHTML=a.length?a.map(x=>`<div class="card" style="display:flex;align-items:center;gap:10px;padding:13px;margin-bottom:7px;opacity:${x.status==='erledigt'?'0.5':'1'}">
    <button onclick="cycleAufgabe(${x.id})" style="background:none;border:none;font-size:1.3rem;color:${x.status==='erledigt'?'var(--green)':'var(--text3)'};cursor:pointer;flex-shrink:0">${si[x.status]||'○'}</button>
    <div style="flex:1;min-width:0"><div style="font-weight:600;font-size:13px;${x.status==='erledigt'?'text-decoration:line-through;color:var(--text3)':''}">${x.titel}</div>
    <div style="font-size:11px;color:var(--text3);margin-top:2px">${x.proj?'📁 '+x.proj+' · ':''}${x.faellig?'📅 '+x.faellig:''}</div></div>
    <span style="font-size:10px;font-weight:700;color:${pc[x.prio]||'var(--text3)'};background:rgba(255,255,255,0.07);padding:2px 8px;border-radius:10px">${x.prio}</span>
    <button onclick="deleteAufgabe(${x.id})" style="background:none;border:none;color:var(--text3);cursor:pointer;font-size:16px">✕</button></div>`).join('')
  :'<div style="color:var(--text3);text-align:center;padding:40px;font-size:13px">Keine Aufgaben.</div>';
}
function cycleAufgabe(id){const s=['offen','inarbeit','erledigt'],a=getAufgaben(),i=a.findIndex(x=>x.id===id);if(i<0)return;a[i].status=s[(s.indexOf(a[i].status)+1)%3];saveAufgaben(a);renderAufgaben();}
function deleteAufgabe(id){saveAufgaben(getAufgaben().filter(a=>a.id!==id));renderAufgaben();}

// ══════════════════════════════════════════════════════
//  KALENDER
// ══════════════════════════════════════════════════════
var _kalDate=new Date();
function getTermine(){try{return JSON.parse(localStorage.getItem('wiegand_termine')||'[]');}catch{return[];}}
function saveTermine(t){try{localStorage.setItem('wiegand_termine',JSON.stringify(t));}catch{}}
function kalPrev(){_kalDate.setMonth(_kalDate.getMonth()-1);renderKalender();}
function kalNext(){_kalDate.setMonth(_kalDate.getMonth()+1);renderKalender();}
function addTermin() {
  const titel=prompt('Termin:'); if(!titel) return;
  const datum=prompt('Datum (JJJJ-MM-TT):',new Date().toISOString().slice(0,10))||'';
  const zeit=prompt('Uhrzeit (HH:MM, optional):')||'';
  saveTermine([...getTermine(),{id:Date.now(),titel,datum,zeit}]);
  renderKalender(); toast('📅 Termin gespeichert');
}
function renderKalender() {
  const y=_kalDate.getFullYear(),m=_kalDate.getMonth();
  const lbl=document.getElementById('kalMonthLabel');
  if(lbl) lbl.textContent=_kalDate.toLocaleDateString('de-DE',{month:'long',year:'numeric'});
  const grid=document.getElementById('kalGrid'); if(!grid) return;
  const termine=getTermine(), offset=(new Date(y,m,1).getDay()+6)%7, days=new Date(y,m+1,0).getDate();
  const today=new Date().toISOString().slice(0,10);
  let cells=['Mo','Di','Mi','Do','Fr','Sa','So'].map(d=>`<div style="text-align:center;font-size:10px;font-weight:700;color:var(--text3);padding:4px">${d}</div>`).join('');
  for(let i=0;i<offset;i++) cells+='<div></div>';
  for(let d=1;d<=days;d++){
    const ds=`${y}-${String(m+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
    const has=termine.some(t=>t.datum===ds),isT=today===ds;
    cells+=`<div onclick="kalClick('${ds}')" style="text-align:center;padding:5px 2px;border-radius:6px;cursor:pointer;font-size:12px;font-weight:${isT?700:400};color:${isT?'#fff':'var(--text2)'};background:${isT?'var(--accent)':has?'var(--bg3)':'transparent'};border:1px solid ${has&&!isT?'var(--accent)':'transparent'};position:relative">${d}${has?'<div style="position:absolute;bottom:1px;left:50%;transform:translateX(-50%);width:4px;height:4px;border-radius:50%;background:var(--green)"></div>':''}</div>`;
  }
  grid.innerHTML=cells;
  const tlist=document.getElementById('terminList');
  if(tlist){const up=termine.sort((a,b)=>a.datum.localeCompare(b.datum)).slice(0,8);tlist.innerHTML=up.length?up.map(t=>`<div style="display:flex;align-items:center;gap:8px;padding:9px;background:var(--bg3);border-radius:var(--rs);margin-bottom:5px"><span>📅</span><div style="flex:1"><div style="font-weight:600;font-size:12px">${t.titel}</div><div style="font-size:11px;color:var(--text3)">${t.datum}${t.zeit?' · '+t.zeit:''}</div></div><button onclick="deleteTermin(${t.id})" style="background:none;border:none;color:var(--text3);cursor:pointer">✕</button></div>`).join(''):'<div style="color:var(--text3);font-size:13px">Keine Termine.</div>';}
}
function kalClick(ds){const t=getTermine().filter(x=>x.datum===ds);if(t.length){alert(t.map(x=>x.titel+(x.zeit?' ('+x.zeit+')':'')).join('\n'));return;}const titel=prompt('Termin am '+ds+':');if(!titel)return;saveTermine([...getTermine(),{id:Date.now(),titel,datum:ds,zeit:''}]);renderKalender();toast('📅 Termin gespeichert');}
function deleteTermin(id){saveTermine(getTermine().filter(t=>t.id!==id));renderKalender();}

// ══════════════════════════════════════════════════════
//  TEAM-CHAT
// ══════════════════════════════════════════════════════
var _chatCh='allgemein';
function getChatData(){try{return JSON.parse(localStorage.getItem('wiegand_chat')||'{}');}catch{return{};}}
function saveChatData(d){try{localStorage.setItem('wiegand_chat',JSON.stringify(d));}catch{}}
function initChat() {
  const channels=['allgemein','aufträge','technik','büro'];
  const chEl=document.getElementById('chatChannels'); if(!chEl) return;
  chEl.innerHTML=channels.map(c=>`<div onclick="switchChatCh('${c}')" style="padding:6px 10px;border-radius:6px;cursor:pointer;font-size:13px;color:${c===_chatCh?'var(--accent)':'var(--text2)'};background:${c===_chatCh?'var(--bg3)':'transparent'};margin-bottom:2px"># ${c}</div>`).join('');
  const emps=['Werner Wiegand','Rayk Wiegand','E. Rost','Marius Weihseller','Ralf Kern','Marcel Hartmann'];
  const dmEl=document.getElementById('chatDMs');
  if(dmEl) dmEl.innerHTML=emps.map(e=>`<div onclick="switchChatCh('dm-${e}')" style="padding:5px 10px;border-radius:6px;cursor:pointer;font-size:12px;color:var(--text2);display:flex;align-items:center;gap:6px;margin-bottom:2px"><span style="width:6px;height:6px;border-radius:50%;background:var(--green);display:inline-block"></span>${e.split(' ')[0]}</div>`).join('');
  renderChatMsgs();
}
function switchChatCh(ch){_chatCh=ch;const t=document.getElementById('chatTitle');if(t)t.textContent=ch.startsWith('dm-')?'👤 '+ch.replace('dm-',''):'# '+ch;initChat();}
function sendChatMsg(){const inp=document.getElementById('chatInput');const txt=inp?.value?.trim();if(!txt)return;const d=getChatData();if(!d[_chatCh])d[_chatCh]=[];d[_chatCh].push({txt,user:currentEmployee?.name||'Unbekannt',time:new Date().toLocaleTimeString('de-DE',{hour:'2-digit',minute:'2-digit'}),id:Date.now()});saveChatData(d);inp.value='';renderChatMsgs();}
function renderChatMsgs(){const msgs=(getChatData()[_chatCh]||[]);const el=document.getElementById('chatMessages');if(!el)return;const me=currentEmployee?.name||'';el.innerHTML=msgs.length?msgs.map(m=>{const isMe=m.user===me;return`<div style="display:flex;flex-direction:column;align-items:${isMe?'flex-end':'flex-start'};gap:2px"><div style="font-size:10px;color:var(--text3)">${m.user} · ${m.time}</div><div style="max-width:72%;padding:8px 13px;border-radius:12px;background:${isMe?'var(--accent)':'var(--bg3)'};color:${isMe?'#fff':'var(--text)'};font-size:13px;line-height:1.5">${m.txt}</div></div>`;}).join(''):'<div style="color:var(--text3);text-align:center;font-size:13px;margin:auto">Noch keine Nachrichten.</div>';el.scrollTop=el.scrollHeight;}

// ══════════════════════════════════════════════════════
//  SEVDESK
// ══════════════════════════════════════════════════════
function getSevR(){try{return JSON.parse(localStorage.getItem('wiegand_rechnungen')||'[]');}catch{return[];}}
function saveSevR(r){try{localStorage.setItem('wiegand_rechnungen',JSON.stringify(r));}catch{}}
function calcSevMwst(){const n=parseFloat(document.getElementById('sevBetrag')?.value||0),p=parseInt(document.getElementById('sevMwst')?.value||19),s=n*p/100,b=n+s;const el=document.getElementById('sevCalc');if(el)el.innerHTML=`Netto: <strong>${n.toFixed(2)} €</strong> + MwSt ${p}%: <strong>${s.toFixed(2)} €</strong> = Brutto: <strong style="color:var(--green)">${b.toFixed(2)} €</strong>`;}
function createSevRechnung(){const k=document.getElementById('sevKunde')?.value?.trim();if(!k){showErr('Bitte Kunde eingeben.');return;}const l=document.getElementById('sevLeistung')?.value?.trim()||'Sanierungsleistung';const n=parseFloat(document.getElementById('sevBetrag')?.value||0);const p=parseInt(document.getElementById('sevMwst')?.value||19);const b=n*(1+p/100);const nr='RE-'+new Date().getFullYear()+'-'+String(getSevR().length+1).padStart(3,'0');saveSevR([{id:Date.now(),nr,kunde:k,leistung:l,netto:n,mwst:p,brutto:b,datum:new Date().toLocaleDateString('de-DE'),status:'offen'},...getSevR()]);renderSevDesk();toast('📄 Rechnung '+nr+' erstellt');}
function exportSevCSV(){const r=getSevR();if(!r.length){showErr('Keine Rechnungen.');return;}const csv=['Nr,Kunde,Leistung,Netto,MwSt,Brutto,Datum,Status',...r.map(x=>`${x.nr},"${x.kunde}","${x.leistung}",${x.netto.toFixed(2)},${x.mwst}%,${x.brutto.toFixed(2)},${x.datum},${x.status}`)].join('\n');const a=document.createElement('a');a.href='data:text/csv;charset=utf-8,'+encodeURIComponent(csv);a.download='wiegand-rechnungen.csv';a.click();}
function renderSevDesk(){const r=getSevR();const total=r.reduce((s,x)=>s+x.brutto,0);const st=document.getElementById('sevStats');if(st)st.innerHTML=r.length?`<strong>${r.length}</strong> Rechnungen · Gesamt: <strong style="color:var(--green)">${total.toFixed(2)} €</strong>`:'Noch keine Rechnungen.';const el=document.getElementById('sevRechnungen');if(!el)return;el.innerHTML=r.slice(0,15).map(x=>`<div style="display:flex;align-items:center;gap:10px;padding:9px;background:var(--bg3);border-radius:var(--rs);margin-bottom:5px"><div style="flex:1;min-width:0"><div style="font-weight:600;font-size:12px">${x.nr} · ${x.kunde}</div><div style="font-size:11px;color:var(--text3)">${x.leistung} · ${x.datum}</div></div><div style="font-weight:700;font-size:13px;color:var(--green);white-space:nowrap">${x.brutto.toFixed(2)} €</div><span onclick="toggleSevStatus(${x.id})" style="font-size:10px;cursor:pointer;color:${x.status==='bezahlt'?'var(--green)':'var(--amber)'};background:rgba(255,255,255,0.07);padding:2px 8px;border-radius:10px">${x.status}</span></div>`).join('');}
function toggleSevStatus(id){const r=getSevR(),i=r.findIndex(x=>x.id===id);if(i<0)return;r[i].status=r[i].status==='offen'?'bezahlt':'offen';saveSevR(r);renderSevDesk();}

// ══════════════════════════════════════════════════════
//  SAAS ADMIN
// ══════════════════════════════════════════════════════
function saveApiKeys(){const ok=document.getElementById('saasOpenAI')?.value?.trim();const wl=document.getElementById('saasWL')?.value?.trim();if(ok){['openaiKey','openaiKeyMobile'].forEach(id=>{const e=document.getElementById(id);if(e)e.value=ok;});}if(wl){['wlKey','wlKeyMobile'].forEach(id=>{const e=document.getElementById(id);if(e)e.value=wl;});}const s=document.getElementById('saasKeySaved');if(s){s.style.display='block';setTimeout(()=>s.style.display='none',2000);}toast('🔑 Keys gespeichert');}
function addSaasUser(){const name=prompt('Name:');if(!name)return;const rolle=prompt('Rolle:','Techniker')||'Techniker';const u=JSON.parse(localStorage.getItem('wiegand_users')||'[]');u.push({name,rolle,id:Date.now()});localStorage.setItem('wiegand_users',JSON.stringify(u));renderSaas();}
function renderSaas(){const p=getProjects();[['saasStatA',p.filter(x=>x.analysis).length],['saasStatV',p.filter(x=>x.vizImages?.length).length],['saasStatP',p.length],['saasStatS',(JSON.stringify(localStorage).length/1024).toFixed(0)+' KB']].forEach(([id,v])=>{const e=document.getElementById(id);if(e)e.textContent=v;});const ok=document.getElementById('saasOpenAI'),wl=document.getElementById('saasWL');if(ok&&!ok.value)ok.value=document.getElementById('openaiKey')?.value||'';if(wl&&!wl.value)wl.value=document.getElementById('wlKey')?.value||'';const u=JSON.parse(localStorage.getItem('wiegand_users')||'[]');const uel=document.getElementById('saasUsers');if(uel)uel.innerHTML=u.length?u.map(x=>`<div style="display:flex;align-items:center;gap:8px;padding:9px;background:var(--bg3);border-radius:var(--rs);margin-bottom:5px"><span>👤</span><div style="flex:1"><div style="font-weight:600;font-size:12px">${x.name}</div><div style="font-size:11px;color:var(--text3)">${x.rolle}</div></div><button onclick="deleteSaasUser(${x.id})" style="background:none;border:none;color:var(--text3);cursor:pointer">✕</button></div>`).join(''):'<div style="color:var(--text3);font-size:13px">Keine Benutzer.</div>';const log=document.getElementById('saasLog');if(log)log.innerHTML=['[System] Gestartet: '+new Date().toLocaleTimeString('de-DE'),'[System] '+p.length+' Projekte','[LocalStorage] '+(JSON.stringify(localStorage).length/1024).toFixed(1)+' KB'].join('<br>');}
function deleteSaasUser(id){localStorage.setItem('wiegand_users',JSON.stringify(JSON.parse(localStorage.getItem('wiegand_users')||'[]').filter(u=>u.id!==id)));renderSaas();}
</script>
<script src="https://cdn.jsdelivr.net/npm/@mkkellogg/gaussian-splats-3d@0.4.7/build/gaussian-splats-3d.umd.min.js"></script>
<script>
(function(){function s(id,v){var e=document.getElementById(id);if(e)e.value=v;}function a(){s('openaiKey','sk-proj-sOtbqfIyQMrAm9SurpbsOhI3uAd11lZaNkY5h7mBttlUWPjgMM6hAbAKWI3Q1n2dX6gBUIlslAT3BlbkFJUr1bLQfUVy9gcnb6v8ykYhO0IynJBuumASzXbEukKUEFl1-XsUdG9zam_tA-wYYfIRRsSCOSI');s('openaiKeyMobile','sk-proj-sOtbqfIyQMrAm9SurpbsOhI3uAd11lZaNkY5h7mBttlUWPjgMM6hAbAKWI3Q1n2dX6gBUIlslAT3BlbkFJUr1bLQfUVy9gcnb6v8ykYhO0IynJBuumASzXbEukKUEFl1-XsUdG9zam_tA-wYYfIRRsSCOSI');s('wlKey','gudpd2yW8bJJDwP6nQHCB5BlqjTassnb');s('wlKeyMobile','gudpd2yW8bJJDwP6nQHCB5BlqjTassnb');}a();document.addEventListener('DOMContentLoaded',a);window.addEventListener('load',a);setTimeout(a,300);})();
</script>

<!-- ═══════════════════ RAUM-SCANNER ═══════════════════ -->
<div id="roomScannerOverlay" style="display:none;position:fixed;inset:0;z-index:99998;background:#000">

  <video id="scannerVideo" autoplay playsinline muted style="width:100%;height:100%;object-fit:cover;position:absolute;inset:0"></video>

  <!-- Grüner Abschluss-Screen -->
  <div id="scanDoneScreen" style="display:none;position:absolute;inset:0;background:rgba(5,15,5,0.97);flex-direction:column;align-items:center;justify-content:center;z-index:40">
    <div style="font-size:5rem;margin-bottom:16px">✅</div>
    <div style="color:#34d07f;font-size:26px;font-weight:800;margin-bottom:8px">Scan abgeschlossen!</div>
    <div id="scanDoneFrames" style="color:rgba(255,255,255,0.7);font-size:14px;margin-bottom:28px">KI erstellt Grundriss...</div>
    <div class="spinner" style="border-color:rgba(52,208,127,0.25);border-top-color:#34d07f;width:40px;height:40px"></div>
  </div>

  <!-- Header -->
  <div style="position:absolute;top:0;left:0;right:0;padding:14px 16px;display:flex;align-items:center;justify-content:space-between;background:linear-gradient(to bottom,rgba(0,0,0,0.85),transparent);z-index:20">
    <div style="display:flex;align-items:center;gap:10px">
      <div id="scanRecDot" style="width:11px;height:11px;border-radius:50%;background:#666;transition:background .3s"></div>
      <span style="color:#fff;font-weight:700;font-size:15px">📡 Raum-Scanner</span>
    </div>
    <button onclick="closeRoomScanner()" style="background:rgba(255,255,255,0.18);border:none;color:#fff;padding:7px 16px;border-radius:20px;cursor:pointer;font-size:13px;font-weight:600">✕</button>
  </div>

  <!-- Checkliste links -->
  <div style="position:absolute;top:68px;left:14px;z-index:20;display:flex;flex-direction:column;gap:6px">
    <div style="font-size:10px;font-weight:700;color:rgba(255,255,255,0.45);letter-spacing:.08em;margin-bottom:2px">KI ERKENNT</div>
    <div id="ckWaende"  class="scan-check">◻ Wände</div>
    <div id="ckTueren"  class="scan-check">◻ Türen</div>
    <div id="ckFenster" class="scan-check">◻ Fenster</div>
    <div id="ckMasse"   class="scan-check">◻ Maße</div>
    <div id="ckRaum"    class="scan-check">◻ Raumtyp</div>
  </div>

  <!-- Vollständigkeit rechts -->
  <div style="position:absolute;top:68px;right:14px;z-index:20;text-align:right">
    <div style="font-size:10px;font-weight:700;color:rgba(255,255,255,0.45);letter-spacing:.08em;margin-bottom:6px">VOLLSTÄNDIG</div>
    <div id="scanPct" style="font-size:38px;font-weight:800;color:#888;line-height:1;transition:color .5s">0%</div>
    <div id="scanPctLabel" style="font-size:11px;color:rgba(255,255,255,0.5);margin-top:3px">Warte auf Bewegung</div>
  </div>

  <!-- Mitte: Hinweis -->
  <div style="position:absolute;top:50%;left:50%;transform:translate(-50%,-55%);text-align:center;pointer-events:none;z-index:20">
    <div id="scanIcon" style="font-size:3rem;margin-bottom:10px;transition:all .3s">📱</div>
    <div id="scanHint" style="color:#fff;font-size:17px;font-weight:700;text-shadow:0 2px 12px rgba(0,0,0,1)">Bewege das Gerät langsam durch den Raum</div>
    <div id="scanSub"  style="color:rgba(255,255,255,0.7);font-size:12px;margin-top:6px;text-shadow:0 1px 6px rgba(0,0,0,0.9)">Kamera erkennt neue Perspektiven automatisch</div>
  </div>

  <!-- Fortschrittsring -->
  <div style="position:absolute;bottom:155px;left:50%;transform:translateX(-50%);z-index:20">
    <svg width="116" height="116" viewBox="0 0 116 116">
      <circle cx="58" cy="58" r="50" fill="none" stroke="rgba(255,255,255,0.12)" stroke-width="7"/>
      <circle id="scanRing" cx="58" cy="58" r="50" fill="none" stroke="#34d07f" stroke-width="7"
        stroke-dasharray="314" stroke-dashoffset="314" stroke-linecap="round"
        transform="rotate(-90 58 58)" style="transition:stroke-dashoffset .6s ease,stroke .5s"/>
      <text x="58" y="53" text-anchor="middle" fill="white" font-size="18" font-weight="bold" id="scanFrameCount">0</text>
      <text x="58" y="70" text-anchor="middle" fill="rgba(255,255,255,0.6)" font-size="10">Frames</text>
    </svg>
  </div>

  <!-- Stopp-Button -->
  <div style="position:absolute;bottom:38px;left:0;right:0;display:flex;flex-direction:column;align-items:center;gap:10px;z-index:20">
    <button id="scanStopBtn" onclick="scanStop()" style="width:78px;height:78px;border-radius:50%;background:rgba(255,90,90,0.9);border:5px solid rgba(255,255,255,0.25);cursor:pointer;font-size:1.9rem;box-shadow:0 4px 24px rgba(255,90,90,0.4)">⏹</button>
    <div style="color:rgba(255,255,255,0.55);font-size:11px">Tippe zum Beenden</div>
  </div>
</div>

<style>
.scan-check{background:rgba(0,0,0,0.45);backdrop-filter:blur(8px);border:1px solid rgba(255,255,255,0.12);border-radius:20px;padding:5px 12px;font-size:12px;font-weight:600;color:rgba(255,255,255,0.45);transition:all .4s;white-space:nowrap}
.scan-check.done{background:rgba(52,208,127,0.18);border-color:rgba(52,208,127,0.45);color:#34d07f}
</style>

<script>
// ══════════════════════════════════════════════════════
//  ADOBE-ARTIGER BEWEGUNGS-SCANNER
// ══════════════════════════════════════════════════════
var _scanStream    = null;
var _scanRunning   = false;
var _scanFrames    = [];
var _scanKiData    = null;
var _scanAnalyzing = false;
var _scanMotion    = { lastAlpha:0, lastBeta:0, lastGamma:0, hasPermission:false };
var _scanMotionInterval = null;
var _scanKiInterval     = null;
var _scanLastCaptureTime = 0;
var _scanMinInterval     = 1200; // min ms zwischen Frames

var CHECKS = {
  waende: {id:'ckWaende', label:'Wände'},
  tueren: {id:'ckTueren', label:'Türen'},
  fenster:{id:'ckFenster',label:'Fenster'},
  masse:  {id:'ckMasse',  label:'Maße'},
  raum:   {id:'ckRaum',   label:'Raumtyp'}
};

async function openRoomScanner() {
  // Reset
  _scanFrames=[]; _scanKiData=null; _scanAnalyzing=false; _scanRunning=false;
  _scanMotion={lastAlpha:0,lastBeta:0,lastGamma:0,hasPermission:false};
  _scanLastCaptureTime=0;

  // UI Reset
  Object.keys(CHECKS).forEach(function(k){
    var el=document.getElementById(CHECKS[k].id);
    if(el){el.className='scan-check';el.textContent='◻ '+CHECKS[k].label;}
  });
  document.getElementById('scanPct').textContent='0%';
  document.getElementById('scanPct').style.color='#888';
  document.getElementById('scanPctLabel').textContent='Warte auf Bewegung';
  document.getElementById('scanRing').style.strokeDashoffset='314';
  document.getElementById('scanRing').style.stroke='#34d07f';
  document.getElementById('scanFrameCount').textContent='0';
  document.getElementById('scanDoneScreen').style.display='none';
  document.getElementById('scanRecDot').style.background='#666';
  document.getElementById('scanIcon').textContent='📱';
  document.getElementById('scanHint').textContent='Bewege das Gerät langsam durch den Raum';

  document.getElementById('roomScannerOverlay').style.display='block';
  document.body.style.overflow='hidden';

  // Kamera starten
  try {
    _scanStream = await navigator.mediaDevices.getUserMedia({
      video:{facingMode:{ideal:'environment'},width:{ideal:1920},height:{ideal:1080}},audio:false
    });
  } catch(e) {
    try { _scanStream = await navigator.mediaDevices.getUserMedia({video:true,audio:false}); }
    catch(e2) { alert('Kamera nicht verfügbar: '+e2.message); closeRoomScanner(); return; }
  }
  document.getElementById('scannerVideo').srcObject=_scanStream;
  _scanRunning=true;
  document.getElementById('scanRecDot').style.background='#ff5a5a';

  // DeviceMotion / Gyro für Bewegungserkennung
  if(typeof DeviceMotionEvent !== 'undefined') {
    if(typeof DeviceMotionEvent.requestPermission === 'function') {
      // iOS 13+ braucht Permission
      try {
        var perm = await DeviceMotionEvent.requestPermission();
        if(perm==='granted') startMotionDetection();
        else startFallbackScan(); // Kein Gyro → zeitbasiert
      } catch(e) { startFallbackScan(); }
    } else {
      startMotionDetection();
    }
  } else {
    startFallbackScan();
  }

  // KI-Analyse alle 10s
  _scanKiInterval = setInterval(function(){
    if(_scanFrames.length>=3 && !_scanAnalyzing) runScanAI();
  }, 10000);
}

function startMotionDetection() {
  _scanMotion.hasPermission=true;
  document.getElementById('scanHint').textContent='Drehe dich langsam durch den Raum';
  document.getElementById('scanSub').textContent='Gerät erkennt automatisch neue Perspektiven';
  document.getElementById('scanIcon').textContent='🔄';

  window.addEventListener('deviceorientation', onScanOrientation);

  // Erste Frame nach 1s aufnehmen
  setTimeout(captureIfNew, 1000);
}

function startFallbackScan() {
  // Kein Gyro: alle 2.5s ein Frame aufnehmen
  document.getElementById('scanHint').textContent='Drehe dich langsam durch den Raum';
  document.getElementById('scanSub').textContent='Bewege dich — alle 2.5s wird automatisch ein Foto aufgenommen';
  document.getElementById('scanIcon').textContent='🔄';
  _scanMotionInterval = setInterval(captureIfNew, 2500);
}

function onScanOrientation(e) {
  if(!_scanRunning) return;
  var alpha=e.alpha||0, beta=e.beta||0, gamma=e.gamma||0;
  var da=Math.abs(alpha-_scanMotion.lastAlpha);
  var db=Math.abs(beta -_scanMotion.lastBeta);
  var dg=Math.abs(gamma-_scanMotion.lastGamma);
  // Wrap-around für alpha (0-360)
  if(da>180) da=360-da;
  var totalMove = da + db + dg;

  if(totalMove > 12) { // Signifikante Bewegung → neuer Frame
    var now=Date.now();
    if(now-_scanLastCaptureTime > _scanMinInterval) {
      _scanMotion.lastAlpha=alpha;
      _scanMotion.lastBeta=beta;
      _scanMotion.lastGamma=gamma;
      captureIfNew();
    }
  }
}

function captureIfNew() {
  if(!_scanRunning || !_scanStream) return;
  var now=Date.now();
  if(now-_scanLastCaptureTime < _scanMinInterval) return;
  _scanLastCaptureTime=now;

  var video=document.getElementById('scannerVideo');
  var c=document.createElement('canvas');
  c.width=video.videoWidth||1280;
  c.height=video.videoHeight||720;
  c.getContext('2d').drawImage(video,0,0);
  _scanFrames.push(c.toDataURL('image/jpeg',0.82));

  // UI aktualisieren
  document.getElementById('scanFrameCount').textContent=_scanFrames.length;

  // Fortschrittsring: max bei ~15 Frames = 100%
  var pct=Math.min(_scanFrames.length/15, 1);
  document.getElementById('scanRing').style.strokeDashoffset=314*(1-pct);

  // Ab 5 Frames: erste KI-Analyse starten
  if(_scanFrames.length===5 && !_scanAnalyzing) runScanAI();

  // Hints
  var n=_scanFrames.length;
  if(n===1) { document.getElementById('scanHint').textContent='Gut! Weiter drehen...'; }
  else if(n===3) { document.getElementById('scanSub').textContent='KI analysiert gleich...'; }
  else if(n>=15) { document.getElementById('scanHint').textContent='Super! Drücke Stopp wenn fertig'; }
}

async function runScanAI() {
  if(_scanAnalyzing || _scanFrames.length<3) return;
  _scanAnalyzing=true;
  var key=document.getElementById('openaiKey').value.trim();
  if(!key){ _scanAnalyzing=false; return; }

  // 4 gleichmäßige Frames für die Analyse
  var total=_scanFrames.length;
  var picks=[0,0.33,0.66,0.99].map(function(p){
    return _scanFrames[Math.min(Math.floor(p*total),total-1)];
  });

  try {
    var content=[];
    picks.forEach(function(f,i){
      content.push({type:'image_url',image_url:{url:'data:image/jpeg;base64,'+f.split(',')[1],detail:'low'}});
    });
    content.push({type:'text',text:'Analyze these room frames. Return ONLY JSON: {"waende":true,"tueren":true,"fenster":false,"masse":true,"raumtyp":"Wohnzimmer","vollstaendigkeit":70,"hinweis":"Zeige noch die linke Wand","laenge":5.2,"breite":4.1,"hoehe":2.6}'});

    var resp=await fetch('https://api.openai.com/v1/chat/completions',{
      method:'POST',
      headers:{'Content-Type':'application/json','Authorization':'Bearer '+key},
      body:JSON.stringify({model:'gpt-4o',max_tokens:250,temperature:0,messages:[{role:'user',content:content}]})
    });
    var data=await resp.json();
    if(!resp.ok){_scanAnalyzing=false;return;}

    var raw=data.choices[0].message.content.trim().replace(/```json/g,'').replace(/```/g,'').trim();
    var s=raw.indexOf('{'),e=raw.lastIndexOf('}');
    if(s===-1){_scanAnalyzing=false;return;}
    var result=JSON.parse(raw.slice(s,e+1));
    _scanKiData=result;
    updateScanChecks(result);
  } catch(ex){ console.warn('ScanAI:',ex.message); }
  _scanAnalyzing=false;
}

function updateScanChecks(r) {
  var vals={waende:r.waende,tueren:r.tueren,fenster:r.fenster,masse:r.masse,raum:!!r.raumtyp};
  var doneCount=0;
  Object.keys(CHECKS).forEach(function(k){
    var el=document.getElementById(CHECKS[k].id);
    if(!el) return;
    if(vals[k]){
      el.className='scan-check done';
      el.textContent='✅ '+CHECKS[k].label;
      doneCount++;
    } else {
      el.className='scan-check';
      el.textContent='◻ '+CHECKS[k].label;
    }
  });

  var pct=r.vollstaendigkeit||Math.round((doneCount/5)*100);
  var color=pct>=80?'#34d07f':pct>=50?'#f5a623':'#ff7070';
  document.getElementById('scanPct').textContent=pct+'%';
  document.getElementById('scanPct').style.color=color;
  document.getElementById('scanRing').style.stroke=color;
  document.getElementById('scanPctLabel').textContent=pct>=80?'Bereit!':pct>=50?'Fast fertig...':'Weiter scannen...';

  if(r.hinweis) {
    document.getElementById('scanHint').textContent=r.hinweis;
    document.getElementById('scanSub').textContent='Vollständigkeit: '+pct+'%';
    document.getElementById('scanIcon').textContent=pct>=80?'✅':'🤖';
  }

  // Bei >=80%: automatisch fertig nach 2s
  if(pct>=80 && _scanRunning) {
    document.getElementById('scanPctLabel').textContent='Perfekt! Scan wird beendet...';
    setTimeout(function(){ if(_scanRunning) scanStop(); }, 2000);
  }
}

function scanStop() {
  if(!_scanRunning) return;
  _scanRunning=false;
  clearInterval(_scanMotionInterval);
  clearInterval(_scanKiInterval);
  window.removeEventListener('deviceorientation', onScanOrientation);
  captureIfNew(); // Letzter Frame

  document.getElementById('scanDoneScreen').style.display='flex';
  document.getElementById('scanDoneFrames').textContent=_scanFrames.length+' Frames aufgenommen — KI erstellt Grundriss...';
  if(_scanStream){ _scanStream.getTracks().forEach(function(t){t.stop();}); _scanStream=null; }

  setTimeout(processScanFrames, 900);
}

function processScanFrames() {
  if(!_scanFrames.length){ closeRoomScanner(); showErr('Keine Frames aufgenommen.'); return; }

  // Status in Grundriss-UI aktualisieren — KEINE Bilder anzeigen
  var statusEl=document.getElementById('grScanStatus');
  var doneEl=document.getElementById('grScanDone');
  var infoEl=document.getElementById('grScanInfo');
  var btn=document.getElementById('grGenerateBtn');
  if(statusEl) statusEl.style.display='none';
  if(doneEl)   doneEl.style.display='flex';
  if(infoEl)   infoEl.textContent=_scanFrames.length+' Frames aufgenommen · KI erstellt präzisen Grundriss';
  if(btn)      { btn.disabled=false; btn.style.opacity='1'; }

  // KI-Maße übertragen
  if(_scanKiData) {
    ['v3dLaenge','v3dBreite','v3dHoehe'].forEach(function(id,i){
      var el=document.getElementById(id);
      var val=[_scanKiData.laenge,_scanKiData.breite,_scanKiData.hoehe][i];
      if(el && val) el.value=val;
    });
  }

  closeRoomScanner();
  showView('grundrisse');
  setTimeout(function(){ generateGrundriss(); }, 500);
}

function closeRoomScanner() {
  clearInterval(_scanMotionInterval);
  clearInterval(_scanKiInterval);
  window.removeEventListener('deviceorientation', onScanOrientation);
  _scanRunning=false;
  if(_scanStream){ _scanStream.getTracks().forEach(function(t){t.stop();}); _scanStream=null; }
  document.getElementById('roomScannerOverlay').style.display='none';
  document.body.style.overflow='';
}
</script>


</body>
</html>
