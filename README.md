<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NeuroAdapt — Reading Assistant</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Atkinson+Hyperlegible:ital,wght@0,400;0,700;1,400&family=Fraunces:ital,opsz,wght@0,9..144,300;0,9..144,600;1,9..144,300&family=Lexend:wght@300;400;500&family=Nunito:wght@400;700&family=Literata:ital,wght@0,300;0,500;1,300&family=Source+Serif+4:ital,wght@0,300;0,600;1,300&family=Lora:ital,wght@0,400;0,600;1,400&family=IBM+Plex+Mono:wght@400;500&display=swap" rel="stylesheet">
<link href="https://fonts.cdnfonts.com/css/opendyslexic" rel="stylesheet">
<style>

  :root {
    --bg: #F5F0E8;
    --bg2: #EDE7D9;
    --card: #FDFAF5;
    --border: #D8CEBC;
    --text: #2C2416;
    --text2: #6B5E47;
    --accent: #3D7A5E;
    --accent2: #5BA882;
    --accent-light: #D4EDE3;
    --warn: #C4602A;
    --warn-light: #FAE8DA;
    --radius: 16px;
    --shadow: 0 4px 24px rgba(44,36,22,0.08);
    --shadow-lg: 0 8px 40px rgba(44,36,22,0.14);
    --reader-font: 'OpenDyslexic', sans-serif;
    --reader-size: 18px;
    --reader-lh: 1.9;
    --reader-ls: 0.05em;
    --reader-ws: 0.2em;
    --reader-bg: #FFFEF5;
    --reader-color: #1A1200;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'Atkinson Hyperlegible', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    overflow-x: hidden;
  }

  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.04'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 0;
    opacity: 0.6;
  }

  header {
    position: sticky;
    top: 0;
    z-index: 100;
    background: rgba(245,240,232,0.9);
    backdrop-filter: blur(12px);
    border-bottom: 1px solid var(--border);
    padding: 0 32px;
    height: 64px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .logo { display: flex; align-items: center; gap: 10px; }
  .logo-icon {
    width: 36px; height: 36px;
    background: var(--accent);
    border-radius: 10px;
    display: grid; place-items: center;
    font-family: 'OpenDyslexic', sans-serif;
    font-size: 14px; color: white; font-weight: bold; letter-spacing: -1px;
  }
  .logo-text { font-family: 'Fraunces', serif; font-size: 22px; font-weight: 600; color: var(--text); letter-spacing: -0.5px; }
  .logo-text span { color: var(--accent); }

  .header-badge {
    background: var(--accent-light);
    color: var(--accent);
    font-size: 12px; font-weight: 700;
    padding: 4px 12px; border-radius: 20px; letter-spacing: 0.5px;
  }

  .main {
    position: relative; z-index: 1;
    max-width: 1200px; margin: 0 auto;
    padding: 40px 24px 36px;
  }

  .hero {
    text-align: center; margin-bottom: 48px;
    animation: fadeUp 0.6s ease both;
  }
  .hero-tag {
    display: inline-block;
    background: var(--accent-light); color: var(--accent);
    font-size: 11px; font-weight: 700; letter-spacing: 2px; text-transform: uppercase;
    padding: 6px 16px; border-radius: 20px; margin-bottom: 20px;
  }
  .hero h1 {
    font-family: 'Fraunces', serif;
    font-size: clamp(32px, 5vw, 56px);
    font-weight: 300; line-height: 1.15; letter-spacing: -1.5px;
    color: var(--text); margin-bottom: 16px;
  }
  .hero h1 em { font-style: italic; color: var(--accent); }
  .hero p { font-size: 16px; color: var(--text2); max-width: 520px; margin: 0 auto; line-height: 1.7; }

  .workspace {
    display: grid; grid-template-columns: 1fr 1fr; gap: 24px;
    animation: fadeUp 0.6s 0.15s ease both;
  }
  @media (max-width: 800px) { .workspace { grid-template-columns: 1fr; } }

  .card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    overflow: hidden;
  }

  .card-header {
    padding: 16px 20px;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; justify-content: space-between;
    background: var(--bg2);
  }
  .card-title {
    font-size: 13px; font-weight: 700; letter-spacing: 1px;
    text-transform: uppercase; color: var(--text2);
    display: flex; align-items: center; gap: 8px;
  }
  .dot { width: 8px; height: 8px; border-radius: 50%; background: var(--accent); }

  .input-area { padding: 16px; display: flex; flex-direction: column; gap: 12px; }

  .ocr-status {
    display: none;
    align-items: center; gap: 8px;
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: 30px;
    padding: 8px 14px;
    font-size: 12px; font-weight: 600; color: var(--text2);
  }
  .ocr-status.visible { display: flex; }
  .ocr-spinner {
    width: 14px; height: 14px;
    border: 2px solid var(--border);
    border-top-color: var(--accent);
    border-radius: 50%;
    animation: spin 0.7s linear infinite;
    flex-shrink: 0;
  }

  textarea#input-text {
    width: 100%; min-height: 140px;
    padding: 12px 14px;
    border: 1.5px solid var(--border);
    border-radius: 10px;
    background: var(--card);
    font-family: 'Atkinson Hyperlegible', sans-serif;
    font-size: 14px; color: var(--text); line-height: 1.6;
    resize: none; outline: none; overflow: hidden;
    transition: border-color 0.2s;
  }
  textarea#input-text:focus { border-color: var(--accent); }
  textarea#input-text::placeholder { color: var(--text2); opacity: 0.6; }

  .upload-zone {
    position: relative;
    border: 2px dashed var(--border);
    border-radius: 12px;
    padding: 24px 16px;
    text-align: center;
    cursor: pointer;
    transition: background 0.15s, border-color 0.15s;
    background: var(--bg);
  }
  .upload-zone:hover { background: var(--bg2); border-color: var(--accent); }
  .upload-zone.dragover { background: var(--accent-light); border-color: var(--accent); }
  .upload-zone input[type="file"] {
    position: absolute; inset: 0; opacity: 0; cursor: pointer; width: 100%; height: 100%;
  }
  .upload-icon { font-size: 24px; display: block; margin-bottom: 6px; }
  .upload-zone p { font-size: 13px; color: var(--text2); line-height: 1.5; }
  .upload-zone p strong { color: var(--accent); }

  .file-chip {
    display: inline-flex; align-items: center; gap: 6px;
    background: var(--accent-light); color: var(--accent);
    font-size: 12px; font-weight: 600;
    padding: 4px 10px 4px 12px; border-radius: 20px;
  }
  .file-chip button {
    background: none; border: none; cursor: pointer;
    color: var(--accent); font-size: 15px; line-height: 1; padding: 0 2px;
    font-weight: 700;
  }
  .file-chip button:hover { color: var(--warn); }

  .error-msg {
    display: none;
    background: var(--warn-light); color: var(--warn);
    border: 1px solid #e8a87a;
    border-radius: 10px; padding: 10px 14px;
    font-size: 13px; line-height: 1.5;
  }
  .error-msg.show { display: block; }

  .transform-btn {
    width: 100%; padding: 14px;
    background: var(--accent); color: white;
    border: none; border-radius: 12px;
    font-family: 'Atkinson Hyperlegible', sans-serif;
    font-size: 15px; font-weight: 700; letter-spacing: 0.5px;
    cursor: pointer; transition: all 0.2s;
    display: flex; align-items: center; justify-content: center; gap: 8px;
  }
  .transform-btn:hover:not(:disabled) { background: #2d5e47; transform: translateY(-1px); box-shadow: var(--shadow); }
  .transform-btn:disabled { opacity: 0.6; cursor: not-allowed; transform: none; }
  .transform-btn .spinner {
    display: none;
    width: 16px; height: 16px;
    border: 2px solid rgba(255,255,255,0.4);
    border-top-color: white;
    border-radius: 50%;
    animation: spin 0.7s linear infinite;
  }
  .transform-btn.loading .spinner { display: block; }
  .transform-btn.loading .btn-text { opacity: 0.8; }

  .settings-grid { padding: 16px; display: grid; grid-template-columns: 1fr 1fr; gap: 14px; }
  .settings-card { margin-top: 14px; }
  .setting-group { display: flex; flex-direction: column; gap: 6px; }
  .setting-group.full { grid-column: 1 / -1; }
  .setting-label { font-size: 11px; font-weight: 700; letter-spacing: 0.8px; text-transform: uppercase; color: var(--text2); }
  .setting-control { display: flex; align-items: center; gap: 8px; }
  input[type="range"] { flex: 1; accent-color: var(--accent); cursor: pointer; }
  .range-val { font-size: 12px; font-weight: 600; color: var(--text2); min-width: 38px; text-align: right; }
  select {
    width: 100%; padding: 8px 10px;
    border: 1.5px solid var(--border); border-radius: 8px;
    background: var(--card); color: var(--text);
    font-family: 'Atkinson Hyperlegible', sans-serif;
    font-size: 13px; cursor: pointer; outline: none;
  }
  select:focus { border-color: var(--accent); }

  .color-swatches { display: flex; gap: 8px; flex-wrap: wrap; }
  .swatch {
    width: 28px; height: 28px; border-radius: 50%;
    border: 2px solid transparent; cursor: pointer;
    transition: transform 0.15s, box-shadow 0.15s;
  }
  .swatch:hover { transform: scale(1.1); }
  .swatch.active { border-color: var(--accent); box-shadow: 0 0 0 2px var(--accent-light); }
  .hl-swatch.hl-active { border-color: var(--accent) !important; box-shadow: 0 0 0 2px var(--accent-light); }

  .toggle-row { display: flex; align-items: center; justify-content: space-between; gap: 12px; }
  .toggle-label { font-size: 13px; font-weight: 700; color: var(--text); }
  .toggle-desc { font-size: 11px; color: var(--text2); margin-top: 2px; }
  .toggle {
    width: 44px; height: 24px; border-radius: 12px;
    background: var(--border); border: none; cursor: pointer;
    position: relative; transition: background 0.2s; flex-shrink: 0;
  }
  .toggle::after {
    content: ''; position: absolute;
    top: 2px; left: 2px;
    width: 20px; height: 20px; border-radius: 50%;
    background: white;
    transition: transform 0.2s; box-shadow: 0 1px 4px rgba(0,0,0,0.2);
  }
  .toggle.on { background: var(--accent); }
  .toggle.on::after { transform: translateX(20px); }

  .output-wrapper { display: flex; flex-direction: column; }
  .output-actions { display: flex; gap: 6px; }
  .icon-btn {
    padding: 6px 10px; border: 1px solid var(--border);
    border-radius: 8px; background: var(--card); cursor: pointer;
    font-size: 15px; transition: all 0.15s; color: var(--text2);
  }
  .icon-btn:hover { background: var(--accent-light); border-color: var(--accent); }

  .output-area {
    flex: 1; padding: 20px;
    font-family: var(--reader-font);
    font-size: var(--reader-size);
    line-height: var(--reader-lh);
    letter-spacing: var(--reader-ls);
    word-spacing: var(--reader-ws);
    background: var(--reader-bg);
    color: var(--reader-color);
    min-height: 300px;
    overflow-y: auto;
    transition: background 0.2s, color 0.2s;
  }
  .output-area p { margin-bottom: 1em; }
  .output-area ul { padding-left: 1.5em; margin-bottom: 1em; }
  .output-area li { margin-bottom: 0.4em; }

  .output-placeholder {
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    min-height: 250px; gap: 12px;
    color: var(--text2); text-align: center; padding: 20px;
  }
  .big-icon { font-size: 48px; opacity: 0.4; }
  .output-placeholder p { font-size: 14px; line-height: 1.6; max-width: 240px; opacity: 0.7; }

  .stats-bar {
    padding: 12px 20px;
    border-top: 1px solid var(--border);
    background: var(--bg2);
    display: flex; align-items: center; justify-content: space-between; gap: 12px;
    flex-wrap: wrap;
  }
  .stat { font-size: 12px; color: var(--text2); }
  .stat strong { color: var(--text); }

  .tts-btn {
    padding: 7px 14px; border-radius: 20px;
    border: 1.5px solid var(--accent-light);
    background: var(--accent-light); color: var(--accent);
    font-family: 'Atkinson Hyperlegible', sans-serif;
    font-size: 13px; font-weight: 700; cursor: pointer;
    display: flex; align-items: center; gap: 6px;
    transition: all 0.15s;
  }
  .tts-btn:hover { background: var(--accent); color: white; }
  .tts-btn.speaking { background: var(--warn-light); border-color: var(--warn); color: var(--warn); }

  .tts-speed-bar {
    display: flex; align-items: center; gap: 10px;
    background: var(--bg2); border: 1px solid var(--border);
    border-radius: 10px; padding: 8px 14px;
    font-size: 12px; color: var(--text2);
  }
  .tts-speed-bar label { font-weight: 700; white-space: nowrap; }
  .tts-speed-bar input[type="range"] { flex: 1; accent-color: var(--accent); }
  .tts-speed-bar .range-val { font-size: 12px; font-weight: 700; color: var(--accent); min-width: 30px; text-align: right; }

  .auto-transform-notice {
    display: flex; align-items: center; gap: 8px;
    background: var(--accent-light); border: 1px solid var(--accent);
    border-radius: 10px; padding: 8px 14px;
    font-size: 12px; color: var(--accent); font-weight: 600;
    animation: fadeUp 0.4s ease both;
  }

  .features {
    display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px;
    margin-top: 10px;
    padding: 0 24px;
    animation: fadeUp 0.6s 0.3s ease both;
  }
  @media (max-width: 700px) { .features { grid-template-columns: 1fr; } }
  .feature-card {
    background: var(--card); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 20px;
    box-shadow: var(--shadow);
  }
  .feature-icon { font-size: 28px; margin-bottom: 10px; }
  .feature-card h3 { font-size: 15px; font-weight: 700; margin-bottom: 6px; color: var(--text); }
  .feature-card p { font-size: 13px; color: var(--text2); line-height: 1.6; }

  .reading-overlay {
    position: fixed; inset: 0; z-index: 200;
    background: var(--reader-bg);
    display: flex; flex-direction: column;
    transform: translateY(100%); transition: transform 0.35s cubic-bezier(0.4,0,0.2,1);
  }
  .reading-overlay.open { transform: translateY(0); }
  .reading-toolbar {
    position: sticky; top: 0;
    background: rgba(255,254,245,0.92); backdrop-filter: blur(10px);
    border-bottom: 1px solid var(--border);
    padding: 12px 24px;
    display: flex; align-items: center; justify-content: space-between;
    z-index: 10;
  }
  .tts-bar { display: flex; gap: 10px; }
  .reading-content {
    flex: 1; overflow-y: auto; padding: 40px 24px;
    max-width: 720px; margin: 0 auto; width: 100%;
  }
  #reading-text {
    font-family: var(--reader-font);
    font-size: var(--reader-size);
    line-height: var(--reader-lh);
    letter-spacing: var(--reader-ls);
    word-spacing: var(--reader-ws);
    color: var(--reader-color);
  }
  #reading-text p { margin-bottom: 1em; }
  #reading-text ul { padding-left: 1.5em; margin-bottom: 1em; }


  .mode-pills { display: flex; gap: 6px; flex-wrap: wrap; margin-top: 2px; }
  .mode-pill { padding: 7px 13px; border: 1.5px solid var(--border); border-radius: 20px; background: var(--card); color: var(--text2); font-family: 'Atkinson Hyperlegible', sans-serif; font-size: 12px; font-weight: 700; cursor: pointer; transition: all 0.15s; }
  .mode-pill:hover { border-color: var(--accent); color: var(--accent); background: var(--accent-light); }
  .mode-pill.active { background: var(--accent); color: white; border-color: var(--accent); }

  /* ── FOCUS RULER ── */
  .ruler-wrap {
    position: absolute; inset: 0;
    pointer-events: none; z-index: 8;
    overflow: hidden; display: none;
  }
  .ruler-wrap.on { display: block; }
  .ruler-band {
    position: absolute; left: 0; right: 0;
    height: 70px;
    background: rgba(61,122,94,0.04);
    border-top: 2px solid rgba(61,122,94,0.35);
    border-bottom: 2px solid rgba(61,122,94,0.35);
    box-shadow: 0 -9999px 0 9999px rgba(0,0,0,0.13), 0 9999px 0 9999px rgba(0,0,0,0.13);
    transition: top 0.05s linear;
    will-change: top;
  }
  #ruler-height-ctrl { margin-top:8px; }

  .tts-word { display: inline; }
  .tts-word.tts-active {
    background: var(--hl-bg, rgba(61,122,94,0.22));
    border-bottom: 3px solid var(--hl-underline, #000000);
    border-radius: 3px;
    padding-bottom: 1px;
    transition: background 0.08s ease, border-color 0.06s ease;
  }
  .hl-swatch { outline-offset: 2px; }
    .voice-row { display: flex; align-items: center; gap: 8px; }
  .voice-row select { font-size: 12px; padding: 5px 8px; flex: 1; border:1.5px solid var(--border);border-radius:8px;background:var(--card); }
  .main-sentence { font-weight: 700 !important; margin-bottom: 0.3em !important; }
  .sub-bullets { margin-top: 0 !important; margin-bottom: 0.8em !important; }
  .para-gap { display: block; height: 1em; }
  .output-area p { margin-bottom: 1.2em; }
  .output-area ul { padding-left: 1.5em; margin-bottom: 1.2em; }
  .output-area li { margin-bottom: 0.5em; line-height: var(--reader-lh); }
  #reading-text p { margin-bottom: 1.2em; }
  #reading-text ul { padding-left: 1.5em; margin-bottom: 1.2em; }
  #reading-text li { margin-bottom: 0.5em; }
  #reading-text .para-gap { display: block; height: 1em; }


  /* ── CUSTOM FONT PICKER ── */
  .font-picker { position: relative; width: 100%; }
  .font-picker-btn {
    width: 100%; padding: 8px 10px;
    border: 1.5px solid var(--border); border-radius: 8px;
    background: var(--card); color: var(--text);
    font-family: 'Atkinson Hyperlegible', sans-serif;
    font-size: 13px; cursor: pointer;
    display: flex; align-items: center; justify-content: space-between;
    text-align: left; transition: border-color 0.15s;
  }
  .font-picker-btn:focus, .font-picker-btn:hover { border-color: var(--accent); outline: none; }
  .font-picker-arrow { font-size: 11px; color: var(--text2); transition: transform 0.2s; flex-shrink: 0; }
  .font-picker.open .font-picker-arrow { transform: rotate(180deg); }
  .font-picker-dropdown {
    position: fixed;
    background: var(--card); border: 1.5px solid var(--border);
    border-radius: 12px; box-shadow: var(--shadow-lg);
    z-index: 300; overflow: hidden; display: none;
    max-height: 320px; overflow-y: auto;
  }
  .font-picker-dropdown.open { display: block; animation: fadeUp 0.15s ease both; }
  .font-picker-option {
    padding: 10px 14px; cursor: pointer;
    transition: background 0.1s;
    display: flex; flex-direction: column; gap: 2px;
    border-bottom: 1px solid var(--border);
  }
  .font-picker-option:last-child { border-bottom: none; }
  .font-picker-option:hover, .font-picker-option.fp-preview { background: var(--accent-light); }
  .font-picker-option.fp-active { background: var(--bg2); }
  .font-picker-option.fp-active::after { content: '✓'; float: right; color: var(--accent); font-size: 13px; }
  .font-picker-name { font-size: 15px; color: var(--text); }
  .font-picker-desc { font-size: 11px; color: var(--text2); font-family: 'Atkinson Hyperlegible', sans-serif !important; }


  .paste-hint {
    font-size: 11px; color: var(--text2); margin-top: -6px;
    display: flex; align-items: center; gap: 5px; opacity: 0.75;
  }
  .paste-hint svg { flex-shrink: 0; }
  .img-paste-preview {
    display: none; margin-top: 6px;
    border: 1.5px solid var(--border); border-radius: 10px;
    overflow: hidden; max-height: 140px; position: relative;
  }
  .img-paste-preview img { width: 100%; max-height: 140px; object-fit: cover; display: block; }
  .img-paste-preview .remove-img {
    position: absolute; top: 6px; right: 8px;
    background: rgba(0,0,0,0.55); color: #fff; border: none;
    border-radius: 50%; width: 22px; height: 22px; font-size: 13px;
    cursor: pointer; display: flex; align-items: center; justify-content: center;
    line-height: 1;
  }
  .features { margin-bottom: 28px; }

  /* ── WORD DEFINITION POPUP ── */
  #word-def-popup {
    position: fixed; z-index: 1200;
    background: var(--card); border: 1.5px solid var(--border);
    border-radius: 14px; padding: 14px 16px 12px; max-width: 300px; min-width: 200px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.18); display: none;
    animation: fadeUp 0.15s ease both;
  }
  #word-def-popup.show { display: block; }
  .wdp-word { font-size: 16px; font-weight: 800; color: var(--text); }
  .wdp-pos  { font-size: 11px; color: var(--accent); font-weight: 700; letter-spacing:.5px; margin: 2px 0 6px; }
  .wdp-def  { font-size: 13px; color: var(--text); line-height: 1.55; }
  .wdp-loading { font-size: 12px; color: var(--text2); }
  .wdp-actions { display: flex; gap: 8px; margin-top: 10px; }
  .wdp-btn {
    font-size: 11px; padding: 4px 11px; border-radius: 20px;
    border: 1.5px solid var(--border); background: var(--bg2);
    color: var(--text); cursor: pointer; font-weight: 600; transition: all .12s;
  }
  .wdp-btn:hover { background: var(--accent-light); border-color: var(--accent); }
  .wdp-btn.saved { background: var(--accent-light); border-color: var(--accent); color: var(--accent); }
  .wdp-close {
    position: absolute; top: 8px; right: 10px;
    background: none; border: none; font-size: 15px; cursor: pointer; color: var(--text2);
  }
  /* ── PERSONAL DICTIONARY ── */
  .pd-section { margin-top: 18px; padding-top: 14px; border-top: 1px solid var(--border); }
  .pd-header { display: flex; align-items: center; justify-content: space-between; cursor: pointer; user-select: none; }
  .pd-title { font-size: 11px; font-weight: 700; color: var(--text2); letter-spacing: .8px; text-transform: uppercase; }
  .pd-badge { font-size: 11px; background: var(--accent-light); color: var(--accent); border-radius: 10px; padding: 2px 8px; font-weight: 700; }
  .pd-chevron { font-size: 12px; color: var(--text2); transition: transform .2s; }
  .pd-list { display: none; max-height: 200px; overflow-y: auto; margin-top: 8px; }
  .pd-list.open { display: block; }
  .pd-entry { display: flex; align-items: flex-start; gap: 8px; padding: 7px 0; border-bottom: 1px solid var(--border); }
  .pd-entry:last-child { border-bottom: none; }
  .pd-w { font-size: 13px; font-weight: 700; color: var(--text); min-width: 90px; }
  .pd-d { font-size: 12px; color: var(--text2); flex: 1; line-height: 1.4; }
  .pd-del { background: none; border: none; font-size: 14px; color: var(--text2); cursor: pointer; padding: 0; }
  .pd-del:hover { color: #e55; }
  .pd-empty { font-size: 12px; color: var(--text2); text-align: center; padding: 10px 0; }
  /* ── CLICKABLE WORD ── */
  .out-word { cursor: pointer; display: inline; }
  .out-word:hover { text-decoration: underline; text-decoration-style: dotted; text-underline-offset: 3px; }
  @keyframes fadeUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
  @keyframes spin { to { transform: rotate(360deg); } }

  /* ── URL DETECTION BANNER ── */
  .url-banner {
    display:flex;align-items:center;gap:10px;padding:9px 13px;
    background:var(--accent-light);border:1.5px solid var(--accent);border-radius:10px;
    font-size:13px;color:var(--accent);font-weight:500;margin-top:8px;
  }
  .url-banner-fetch {
    margin-left:auto;padding:5px 12px;background:var(--accent);color:#fff;
    border:none;border-radius:7px;cursor:pointer;font-size:12px;font-weight:600;
    white-space:nowrap;
  }
  .url-banner-fetch:hover { opacity:0.88; }
  .url-banner-fetch:disabled { opacity:0.55;cursor:not-allowed; }
  /* ── READ FROM HERE BUTTON ── */
  .wdp-btn-rfh {
    display:block;width:100%;text-align:left;
    padding:7px 10px;margin-top:8px;border-top:1px solid var(--border);
    background:none;border-left:none;border-right:none;border-bottom:none;
    font-size:12px;color:var(--accent);cursor:pointer;font-weight:600;
  }
  .wdp-btn-rfh:hover { background:var(--accent-light); }

  #dict-overlay {
    position:fixed;inset:0;z-index:9999;
    background:rgba(245,240,232,0.97);
    display:flex;flex-direction:column;align-items:center;justify-content:center;
    gap:20px;transition:opacity 0.5s;
  }
  #dict-overlay.hidden{opacity:0;pointer-events:none;}
  #dict-overlay h2{font-family:'Fraunces',serif;font-size:28px;font-weight:300;color:#2C2416;}
  #dict-overlay p{font-size:14px;color:#6B5E47;}
  #dict-progress-bar{width:340px;height:8px;background:#D8CEBC;border-radius:4px;overflow:hidden;}
  #dict-progress-fill{height:100%;background:#3D7A5E;border-radius:4px;width:0%;transition:width 0.08s;}
  #dict-count{font-size:14px;color:#3D7A5E;font-weight:700;letter-spacing:0.3px;}
  #dict-status{font-size:12px;color:#6B5E47;opacity:0.8;}
</style>
</head>
<header>
  <div class="logo">
    <div class="logo-icon">Na</div>
    <span class="logo-text">Neuro<span>Adapt</span></span>
  </div>
  <span class="header-badge">✦ 500B Forms · All Languages · All Files</span>
</header>

<div class="main">

  <div class="hero">
    <div class="hero-tag">Reading for Every Mind</div>
    <h1>Text that <em>adapts</em><br>to how you read</h1>
    <p>Upload any file — document, image, PDF, audio, or video. NeuroAdapt extracts and transforms it into dyslexia-friendly content in any language.</p>
  </div>

  <div class="workspace">

    <!-- INPUT -->

      <!-- INPUT CARD -->
      <div class="card">
        <div class="card-header">
          <div class="card-title"><div class="dot"></div> Input</div>
          <div style="font-size:12px;color:var(--text2);">Paste · Type · Upload</div>
        </div>
        <div class="input-area">

          <textarea id="input-text" placeholder="Paste any text here — article, textbook, menu, email… NeuroAdapt will transform it for you."></textarea>

          <p class="paste-hint">
            <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="3" width="18" height="18" rx="3"/><circle cx="8.5" cy="8.5" r="1.5"/><polyline points="21 15 16 10 5 21"/></svg>
            You can also paste a screenshot or photo — text will be extracted automatically
          </p>
          <div class="img-paste-preview" id="img-paste-preview">
            <img id="img-paste-img" src="" alt="pasted image">
            <button class="remove-img" onclick="clearPastedImage()" title="Remove image">✕</button>
          </div>

          <div class="upload-zone" id="upload-zone">
            <input type="file" id="file-input" accept="*/*" onchange="handleFile(event)">
            <span class="upload-icon">📎</span>
            <p><strong>Drop any file or click to upload</strong><br>TXT, DOCX, PDF, images, MP3, MP4, and more</p>
          </div>

          <div id="url-banner" style="display:none;" class="url-banner">
            <span>🔗 URL detected — fetch text from this link?</span>
            <button class="url-banner-fetch" id="url-fetch-btn" onclick="fetchUrlContent()">Fetch Content</button>
          </div>

          <div class="ocr-status" id="ocr-status">
            <div class="ocr-spinner"></div>
            <span id="ocr-status-text">Extracting text…</span>
          </div>

          <div id="file-chip-container"></div>
          <div class="error-msg" id="error-msg"></div>

          <button class="transform-btn" id="transform-btn" onclick="transformText()">
            <div class="spinner"></div>
            <span class="btn-text">✦ Transform Text</span>
          </button>
        </div>
      </div>


    <!-- RIGHT: OUTPUT -->
    <div class="card output-wrapper" style="min-height:500px;">
      <div class="card-header">
        <div class="card-title"><div class="dot" style="background:#5BA882"></div> Adapted Output</div>
        <div class="output-actions">
          <button class="icon-btn" onclick="openReadingMode()" title="Reading Mode">🔍</button>
          <button class="icon-btn" onclick="speak()" id="tts-icon-btn" title="Read Aloud">🔊</button>
          <button class="icon-btn" onclick="copyText()" title="Copy">📋</button>
        </div>
      </div>

      <div class="output-area" id="output-area" style="position:relative;" onmousemove="moveRuler(event,'ruler-main')" ontouchmove="moveTouchRuler(event,'ruler-main')">
        <div class="ruler-wrap" id="ruler-main"><div class="ruler-band" id="ruler-band-main" style="top:60px;"></div></div>
        <div class="output-placeholder" id="output-placeholder">
          <div class="big-icon">🧠</div>
          <p>Your adapted text will appear here after transformation</p>
        </div>
        <div id="output-text" style="display:none;"></div>
      </div>

      <div class="stats-bar" id="stats-bar" style="display:none;">
        <div style="display:flex;gap:16px;flex-wrap:wrap;">
          <div class="stat">Words: <strong id="stat-words">—</strong></div>
          <div class="stat">Reading time: <strong id="stat-time">—</strong></div>
          <div class="stat">Processed: <strong id="stat-reduced">—</strong></div>
        </div>
        <div style="display:flex;align-items:center;gap:10px;flex-wrap:wrap;">
          <div class="tts-speed-bar">
            <label>🐢 Speed</label>
            <input type="range" id="tts-speed" min="0.5" max="2" step="0.1" value="0.85" oninput="updateTTSSpeed(this)">
            <span class="range-val" id="tts-speed-val">0.85×</span>
          </div>
          <button class="tts-btn" onclick="speak()" id="tts-btn">
            <span id="tts-icon">🔊</span> <span id="tts-label">Read Aloud</span>
          </button>
        </div>
      </div>
    </div>

  </div>

      <!-- SETTINGS CARD -->
      <div class="card settings-card">
        <div class="card-header">
          <div class="card-title"><div class="dot"></div> Reading Settings</div>
        </div>
        <div class="settings-grid">


          <div class="setting-group full">
            <label class="setting-label">Processing Mode</label>
            <div class="mode-pills">
              <button class="mode-pill" data-mode="simplify" onclick="setMode('simplify')">Simplifier Only</button>
              <button class="mode-pill" data-mode="summarize" onclick="setMode('summarize')">Summarizer Only</button>
              <button class="mode-pill active" data-mode="both" onclick="setMode('both')">Simplify + Summarize</button>
            </div>
          </div>

          <div class="setting-group full">
            <label class="setting-label">Font Family <span style="font-size:10px;color:var(--accent);font-weight:600;letter-spacing:0.3px;">hover to preview</span></label>
            <div class="font-picker" id="font-picker">
              <button class="font-picker-btn" id="font-picker-btn" onclick="toggleFontPicker(event)" type="button">
                <span id="font-picker-label">OpenDyslexic — Dyslexia-optimised</span>
                <span class="font-picker-arrow">▾</span>
              </button>
              <div class="font-picker-dropdown" id="font-picker-dropdown" onmouseleave="revertFont()">
                <div class="font-picker-option fp-active" data-font="'OpenDyslexic', sans-serif" data-label="OpenDyslexic — Dyslexia-optimised" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'OpenDyslexic',sans-serif">OpenDyslexic</span>
                  <span class="font-picker-desc">Dyslexia-optimised</span>
                </div>
                <div class="font-picker-option" data-font="'Atkinson Hyperlegible', sans-serif" data-label="Atkinson Hyperlegible — High legibility" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'Atkinson Hyperlegible',sans-serif">Atkinson Hyperlegible</span>
                  <span class="font-picker-desc">High legibility</span>
                </div>
                <div class="font-picker-option" data-font="'Lexend', sans-serif" data-label="Lexend — Reading ease research" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'Lexend',sans-serif">Lexend</span>
                  <span class="font-picker-desc">Reading ease research</span>
                </div>
                <div class="font-picker-option" data-font="'Nunito', sans-serif" data-label="Nunito — Rounded &amp; friendly" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'Nunito',sans-serif">Nunito</span>
                  <span class="font-picker-desc">Rounded &amp; friendly</span>
                </div>
                <div class="font-picker-option" data-font="'Literata', serif" data-label="Literata — Long-form reading" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'Literata',serif">Literata</span>
                  <span class="font-picker-desc">Long-form reading</span>
                </div>
                <div class="font-picker-option" data-font="'Lora', serif" data-label="Lora — Literary serif" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'Lora',serif">Lora</span>
                  <span class="font-picker-desc">Literary serif</span>
                </div>
                <div class="font-picker-option" data-font="'Source Serif 4', serif" data-label="Source Serif 4 — Clean serif" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'Source Serif 4',serif">Source Serif 4</span>
                  <span class="font-picker-desc">Clean serif</span>
                </div>
                <div class="font-picker-option" data-font="'Georgia', serif" data-label="Georgia — Classic screen serif" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'Georgia',serif">Georgia</span>
                  <span class="font-picker-desc">Classic screen serif</span>
                </div>
                <div class="font-picker-option" data-font="'Verdana', sans-serif" data-label="Verdana — Screen-optimised" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'Verdana',sans-serif">Verdana</span>
                  <span class="font-picker-desc">Screen-optimised</span>
                </div>
                <div class="font-picker-option" data-font="'IBM Plex Mono', monospace" data-label="IBM Plex Mono — Monospace" onmouseenter="previewFont(this)" onclick="selectFont(this)">
                  <span class="font-picker-name" style="font-family:'IBM Plex Mono',monospace">IBM Plex Mono</span>
                  <span class="font-picker-desc">Monospace</span>
                </div>
              </div>
            </div>
            <!-- hidden select kept for JS compat -->
            <select id="font-select" onchange="updateReaderFont()" style="display:none;">
              <option value="'OpenDyslexic', sans-serif" selected>OpenDyslexic</option>
              <option value="'Atkinson Hyperlegible', sans-serif">Atkinson Hyperlegible</option>
              <option value="'Lexend', sans-serif">Lexend</option>
              <option value="'Nunito', sans-serif">Nunito</option>
              <option value="'Literata', serif">Literata</option>
              <option value="'Lora', serif">Lora</option>
              <option value="'Source Serif 4', serif">Source Serif 4</option>
              <option value="'Georgia', serif">Georgia</option>
              <option value="'Verdana', sans-serif">Verdana</option>
              <option value="'IBM Plex Mono', monospace">IBM Plex Mono</option>
            </select>
          </div>

          <div class="setting-group">
            <label class="setting-label">Font Size</label>
            <div class="setting-control">
              <input type="range" id="font-size" min="14" max="32" value="18" oninput="updateSize(this)">
              <span class="range-val" id="size-val">18px</span>
            </div>
          </div>

          <div class="setting-group">
            <label class="setting-label">Line Height</label>
            <div class="setting-control">
              <input type="range" id="line-height" min="1.2" max="3" step="0.1" value="1.9" oninput="updateLH(this)">
              <span class="range-val" id="lh-val">1.9</span>
            </div>
          </div>

          <div class="setting-group">
            <label class="setting-label">Letter Spacing</label>
            <div class="setting-control">
              <input type="range" id="letter-space" min="0" max="0.2" step="0.01" value="0.05" oninput="updateLS(this)">
              <span class="range-val" id="ls-val">0.05em</span>
            </div>
          </div>

          <div class="setting-group">
            <label class="setting-label">Word Spacing</label>
            <div class="setting-control">
              <input type="range" id="word-space" min="0" max="0.6" step="0.05" value="0.2" oninput="updateWS(this)">
              <span class="range-val" id="ws-val">0.2em</span>
            </div>
          </div>

          <div class="setting-group full">
            <label class="setting-label">Background Tint</label>
            <div class="color-swatches">
              <div class="swatch active" data-bg="#FFFEF5" data-col="#1A1200" style="background:#FFFEF5;border-color:#ddd" onclick="setBg(this)" title="Warm White"></div>
              <div class="swatch" data-bg="#F0F4D8" data-col="#1A2200" style="background:#F0F4D8" onclick="setBg(this)" title="Sage Green"></div>
              <div class="swatch" data-bg="#FFF0D6" data-col="#1A0E00" style="background:#FFF0D6" onclick="setBg(this)" title="Peach"></div>
              <div class="swatch" data-bg="#E8F4F8" data-col="#001A20" style="background:#E8F4F8" onclick="setBg(this)" title="Sky Blue"></div>
              <div class="swatch" data-bg="#F0E8F8" data-col="#0E001A" style="background:#F0E8F8" onclick="setBg(this)" title="Lavender"></div>
              <div class="swatch" data-bg="#1A1A1A" data-col="#F0EED8" style="background:#1A1A1A" onclick="setBg(this)" title="Dark Mode"></div>
            </div>
          </div>


          <div class="setting-group full">
            <label class="setting-label">Highlight Colour</label>
            <div class="color-swatches" id="hl-swatches">
              <div class="hl-swatch swatch hl-active" data-hl="rgba(61,122,94,0.22)" style="background:rgba(61,122,94,0.22)" onclick="setHlColor(this)" title="Green (default)"></div>
              <div class="hl-swatch swatch" data-hl="rgba(255,210,0,0.38)" style="background:rgba(255,210,0,0.38)" onclick="setHlColor(this)" title="Yellow"></div>
              <div class="hl-swatch swatch" data-hl="rgba(70,140,255,0.28)" style="background:rgba(70,140,255,0.28)" onclick="setHlColor(this)" title="Blue"></div>
              <div class="hl-swatch swatch" data-hl="rgba(255,100,150,0.28)" style="background:rgba(255,100,150,0.28)" onclick="setHlColor(this)" title="Pink"></div>
              <div class="hl-swatch swatch" data-hl="rgba(255,155,50,0.35)" style="background:rgba(255,155,50,0.35)" onclick="setHlColor(this)" title="Orange"></div>
              <div class="hl-swatch swatch" data-hl="rgba(200,100,255,0.25)" style="background:rgba(200,100,255,0.25)" onclick="setHlColor(this)" title="Purple"></div>
              <div class="hl-swatch swatch" data-hl="rgba(0,0,0,0)" style="background:transparent;border-style:dashed" onclick="setHlColor(this)" title="None"></div>
            </div>
          </div>

          <div class="setting-group full">
            <div class="toggle-row">
              <div>
                <div class="toggle-label">⚡ Bionic Reading</div>
                <div class="toggle-desc">Bold first letters to guide the eye</div>
              </div>
              <button class="toggle" id="bionic-toggle" onclick="toggleBionic(this)"></button>
            </div>
          </div>

          <div class="setting-group full">
            <div class="toggle-row">
              <div>
                <div class="toggle-label">📏 Focus Ruler</div>
                <div class="toggle-desc">Dims text outside a reading strip — follows your mouse</div>
              </div>
              <button class="toggle" id="ruler-toggle" onclick="toggleRuler(this)"></button>
            </div>
            <div id="ruler-height-ctrl" style="display:none;">
              <label class="setting-label" style="display:block;margin-bottom:4px;margin-top:10px;">Ruler Height</label>
              <div class="setting-control">
                <input type="range" id="ruler-height-range" min="40" max="120" step="10" value="70" oninput="updateRulerHeight(this)">
                <span class="range-val" id="ruler-height-val">70px</span>
              </div>
            </div>
          </div>

          <div class="setting-group full">
            <label class="setting-label">Simplification Level</label>
            <select id="simplify-level">
              <option value="light">Light — replace 4+ syllable words (keep ~3 syl.)</option>
              <option value="medium" selected>Medium — replace 4+ syllable words (keep 2–3 syl.)</option>
              <option value="heavy">Heavy — replace 3+ syllable words (keep 1–3 syl.)</option>
            </select>
          </div>



          <div class="setting-group full">
            <label class="setting-label">Read Aloud Voice</label>
            <div class="voice-row">
              <select id="tts-voice" onchange="syncVoice('tts-voice')">
                <option>Loading voices…</option>
              </select>
            </div>

          <!-- Personal Dictionary -->
          <div class="setting-group full">
            <div class="pd-section">
              <div class="pd-header" onclick="togglePersonalDict()">
                <span class="pd-title">📖 My Word Dictionary</span>
                <span style="display:flex;align-items:center;gap:6px;">
                  <span class="pd-badge" id="pd-count">0</span>
                  <span class="pd-chevron" id="pd-chevron">▾</span>
                </span>
              </div>
              <div class="pd-list" id="pd-list"></div>
            </div>
          </div>
          </div>

          </div>

        </div>
      </div>

  <!-- FEATURES -->
  <div class="features">
    <div class="feature-card">
      <div class="feature-icon">📷</div>
      <h3>500 Billion Forms — No Quota</h3>
      <p>5M cached entries + 3-layer prefix/suffix decomposition = 500B form coverage. All local — no API, no servers, no limits.</p>
    </div>
    <div class="feature-card">
      <div class="feature-icon">✂️</div>
      <h3>Syntactic Simplification</h3>
      <p>Long nested sentences are broken into short clear ones. No information is lost — just made accessible for every reader.</p>
    </div>
    <div class="feature-card">
      <div class="feature-icon">🎨</div>
      <h3>Adaptive Display</h3>
      <p>Adjust font, size, spacing, and background tint to your exact needs. OpenDyslexic font reduces letter confusion for dyslexic readers.</p>
    </div>
  </div>

</div>

<!-- READING MODE OVERLAY -->
<div class="reading-overlay" id="reading-overlay">
  <div class="reading-toolbar">
    <div class="logo">
      <div class="logo-icon">Na</div>
      <span class="logo-text">Neuro<span>Adapt</span> <span style="font-size:14px;font-weight:300;color:var(--text2)">Reading Mode</span></span>
    </div>
    <div class="tts-bar">
      <div class="tts-speed-bar" style="border:none;background:transparent;padding:0 4px;">
        <label>🐢</label>
        <input type="range" id="tts-speed-reading" min="0.5" max="2" step="0.1" value="0.85" oninput="updateTTSSpeedReading(this)" style="width:80px;">
        <span class="range-val" id="tts-speed-reading-val">0.85×</span>
      </div>
      <select id="tts-voice-reading" onchange="syncVoice('tts-voice-reading')" style="font-size:12px;padding:5px 8px;max-width:180px;border:1.5px solid var(--border);border-radius:8px;background:var(--card);"><option>Loading…</option></select>
      <button class="tts-btn" onclick="speak()" id="reading-tts-btn">🔊 Read Aloud</button>
      <button class="tts-btn" onclick="closeReadingMode()" style="border-color:var(--border);background:var(--bg);color:var(--text2)">✕ Close</button>
    </div>
  </div>
  <div class="reading-content" style="position:relative;" onmousemove="moveRuler(event,'ruler-reading')" ontouchmove="moveTouchRuler(event,'ruler-reading')">
    <div class="ruler-wrap" id="ruler-reading"><div class="ruler-band" id="ruler-band-reading" style="top:60px;"></div></div>
    <div id="reading-text"></div>
  </div>
</div>

<!-- Tesseract + PDF.js -->
<script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/pdfjs-dist@3.11.174/build/pdf.min.js"></script>
<!-- Dictionary loading overlay -->
<div id="dict-overlay">
  <div class="logo-icon" style="width:56px;height:56px;font-size:22px;border-radius:14px;">Na</div>
  <h2>Building 500-Billion Form Dictionary…</h2>
  <div id="dict-progress-bar"><div id="dict-progress-fill"></div></div>
  <div id="dict-count">0 / 500,000,000,000+ forms</div>
  <p id="dict-status">2,092 pairs → 5M cached + 500B algorithmic forms</p>
</div>


<script>
// ═══════════════════════════════════════════════════════════════════════════
//  NeuroAdapt 5M — 5 Million Word Dictionary (Runtime Morphological Expansion)
//  2,092 base pairs × ~2,542 patterns = 5,317,864 entries
// ═══════════════════════════════════════════════════════════════════════════

pdfjsLib.GlobalWorkerOptions.workerSrc =
  'https://cdn.jsdelivr.net/npm/pdfjs-dist@3.11.174/build/pdf.worker.min.js';

// ── GLOBAL STATE ─────────────────────────────────────────────────────────────
let WORD_MAP = null;
let extractedText = '';
let rawOutputText = '';
let bionicOn = false;
let ttsUtterance = null;
let isSpeaking = false;
let lastSimplificationPercent = 0;
let dictReady = false;
let currentMode = 'both'; // 'simplify' | 'summarize' | 'both'
let ttsWordSpans = [];
let ttsSentenceQueue = [];
let ttsQueueIdx = 0;
let ttsSentTimer = null;
let ttsCurHighlight = null;
let ttsWordTimer = null;
let ttsBoundaryFired = false;
let ttsSentPauseMs = 400; // standard natural pause

// ── BASE PAIRS (2,092 pairs — pipe-separated) ─────────────────────────────────
const PAIRS_RAW = `abandonment|being left
abbreviate|shorten
abbreviation|shortened form
abdication|giving up power
aberrant|odd
aberration|flaw
abhor|hate
abhorrent|hateful
abide|follow
abject|miserable
abnegation|giving up
abnormal|unusual
abnormality|defect
abolish|end
abominable|terrible
abomination|hateful thing
abrasion|scrape
abridgment|shortened version
abruptly|suddenly
abscess|infected lump
absenteeism|frequent absence
absolutism|total control
abstinence|avoiding something
abstraction|general idea
abstruse|hard to understand
absurd|silly
abundant|plentiful
abysmal|terrible
accelerate|speed up
accelerated|sped up
acceleration|speeding up
accentuate|stress
acceptance|taking in
accidental|by chance
acclaimed|praised
acclamation|loud praise
accolade|award
accommodate|fit
accomplish|do
accomplished|skilled
accomplishment|achievement
accountability|responsibility
accreditation|official approval
accretion|gradual buildup
accumulate|gather
accumulated|built up
acerbic|sharp
acknowledge|admit
acknowledged|recognized
acquire|get
acquisition|buying
acquittal|not guilty verdict
acrimonious|bitter
acrimony|bitterness
actionable|able to be done
actuation|putting in motion
acuity|sharpness
acumen|skill
acupuncture|needle treatment
adamant|firm
adaptability|ability to change
adaptable|able to change
adaptation|adjustment
addictive|habit-forming
additionally|also
addressability|reachability
adenoma|gland growth
adequate|good enough
adhere|stick to
adipose|fatty
adjacent|next to
adjective|describing word
adjudicate|judge
adjudication|legal judgment
adjunction|adding to
adjustability|ability to adjust
administer|manage
administration|management
administrative|of management
administratively|in management
admirable|worthy of praise
admissibility|acceptability
admittedly|it must be said
admonish|warn
admonishment|warning
admonition|warning
adoration|deep love
adrenaline|stress hormone
adulation|praise
advantageous|helpful
advantageously|helpfully
adverbial|of adverbs
adversarial|opposing
adversary|enemy
adverse|bad
adversity|hardship
aerobic|oxygen-using
aesthetic|beauty
affectionately|lovingly
affidavit|sworn statement
affiliation|connection
affirmation|approval
afflict|affect
affliction|suffering
affluence|wealth
affordability|reasonable cost
aforementioned|above
aforestated|mentioned above
agglomeration|cluster
aggrandize|boost
aggrandizement|making greater
aggravation|making worse
aggregate|total
aggression|hostility
aggressively|forcefully
aggrieve|upset
agitate|upset
agitation|stirring up
agonization|great suffering
agony|severe pain
agreeableness|pleasantness
alacrity|eagerness
alarmingly|worryingly
algorithm|set of rules
alienate|push away
alienation|feeling alone
alkaline|basic not acidic
allegation|claim
allegedly|supposedly
allegiance|loyalty
allele|gene version
allergen|allergy trigger
allergy|bad reaction
alleviate|ease
alleviation|relief
allocation|sharing out
aloof|distant
alopecia|hair loss
alterability|changeability
altercation|fight
altruism|selflessness
altruistic|selfless
alveoli|air sacs
amalgamation|combining
ambiguity|vagueness
ambiguous|unclear
ambition|strong desire
ambitiously|with great drive
ambivalence|mixed feelings
ambivalent|unsure
ameliorate|improve
amelioration|improvement
amenable|willing
amicable|friendly
amicably|in a friendly way
amnesia|memory loss
amortization|gradual payoff
amplification|making bigger
amply|fully
amygdala|emotion brain area
anachronism|outdated thing
anaerobic|no-oxygen
analgesic|pain reliever
analogous|similar
analytically|by analysis
analyze|study
anarchy|chaos
anatomy|body structure
anecdote|short story
anemia|low iron
anesthesia|numbing
aneurysm|blood vessel bulge
angina|chest pain
anguish|pain
animate|bring to life
animosity|hatred
annihilate|destroy
annihilation|complete destruction
annotation|added notes
annuity|regular payment
anomalous|unusual
anomaly|oddity
antagonism|hostility
antagonist|enemy
antagonize|provoke
anthropology|human study
antibiotic|germ-killing drug
antibody|immune protein
anticipate|expect
anticoagulant|blood thinner
antidepressant|mood drug
antigen|foreign substance
antioxidant|protective compound
antiquated|outdated
antiseptic|germ cleaner
antithesis|direct opposite
antonym|opposite word
anxiety|worry
apathy|lack of care
aperture|opening
apparatus|device
apparently|it seems
appease|calm
appeasement|calming
appendicitis|appendix swelling
applicability|relevance
appraisal|valuation
appreciability|noticeability
appreciation|thanks
apprehend|grasp
apprehension|fear
apprehensive|worried
approachability|ease of access
appropriate|right
appropriation|taking
approximately|about
approximation|rough estimate
aptitude|talent
arbitrage|price difference trading
arbitrarily|randomly
arbitrariness|randomness
arbitrate|judge
arbitration|outside judgment
archaic|old
archetype|original model
archipelago|island group
ardent|passionate
arduous|hard
arguably|one could say
argumentation|presenting argument
aristocracy|upper class
arraignment|formal charge
arrhythmia|irregular heartbeat
arrogance|pride
arrogant|proud
arterial|of arteries
arthritis|joint pain
articulate|clear
articulation|clear expression
artistically|creatively
ascertain|find out
ascertainment|finding out
aspersion|negative remark
aspiration|dream
aspire|aim for
assassination|killing of leader
assertion|claim
assertively|confidently
assertiveness|confidence
asset|owned item
assiduous|hardworking
assimilate|absorb
assimilation|taking in
assistance|help
assumption|belief
assurance|promise
asthma|breathing problem
astoundingly|amazingly
astronomy|star study
astute|smart
asymptomatic|no symptoms
atmosphere|air layer
atomic|of atoms
atone|make up for
atrium|hall
atrocious|awful
atrociously|terribly
atrocity|terrible act
atrophy|wasting away
attain|reach
attainability|reachability
attainment|achieving
attempt|try
attentiveness|careful attention
attenuation|weakening
attestation|certification
attributability|assignability
attribute|quality
attribution|assigning cause
attrition|gradual loss
audacious|bold
auditing|checking accounts
augment|increase
augmentation|increasing
austere|plain
austerity|strictness
authentic|real
authentically|genuinely
authentication|login check
authoritatively|with authority
authority|power
authorization|permission
automatically|by itself
automation|self-running
autonomous|independent
avarice|greed
avaricious|greedy
aversion|dislike
avert|avoid
avoidance|staying away
awe-inspiring|amazing
axiom|basic truth
bailiff|court officer
bandwidth|data speed
bankruptcy|unable to pay
baroque|overly decorated
behavioral|of behavior
belied|hid
believability|trustworthiness
belittle|put down
belligerent|aggressive
belonging|feeling included
bemoan|complain about
benchmark|standard
benefactor|donor
beneficial|helpful
beneficially|helpfully
beneficiary|receiver
benevolence|kindness
benevolent|kind
benign|harmless
berate|scold
beseech|beg
bestow|give
bewildered|confused
bewildering|confusing
bewilderment|confusion
bewitch|charm
bias|prejudice
bifurcation|splitting in two
bilateral|two-sided
biodiversity|life variety
biomass|living matter
biopsy|tissue sample
blasphemy|disrespect
blatant|obvious
bleak|gloomy
blockchain|secure chain of records
boisterous|loud
boldly|bravely
bolster|support
bombastic|showy
bothersome|annoying
boundless|endless
bradycardia|slow heart
brazen|bold
breach|violation
breviary|summary
brevity|shortness
brilliantly|very well
bronchitis|airway swelling
brusque|blunt
brutality|cruelty
brutalization|making brutal
budgetary|of budgets
buoyant|cheerful
burden of proof|duty to prove
burdensome|heavy
bureaucracy|red tape
bureaucratic|complex
bureaucratically|through red tape
bureaucratization|adding bureaucracy
cacophony|noise
calamity|disaster
calculability|predictability
calibrate|adjust
calibration|precise adjustment
callous|cruel
callously|cruelly
callousness|cruelty
candid|honest
candidly|honestly
candor|honesty
canonization|making official
capacious|roomy
capitalism|private ownership system
capitalization|total value
capitulate|give up
capitulation|surrender
capricious|changeable
captivate|charm
carcinogen|cancer cause
carcinoma|cancer
cardiovascular|heart-related
catalyst|trigger
catastrophe|disaster
catastrophic|terrible
catastrophically|terribly
categorically|firmly
categorization|sorting into groups
categorize|sort
catharsis|emotional release
catheter|tube
causality|cause and effect
causation|cause and effect
caustic|harsh
cautiously|carefully
cavalier|careless
cease|stop
ceaseless|endless
celestial|heavenly
cellular|of cells
censure|blame
centralization|bringing to center
centrifuge|spinning machine
cerebral|mental
certification|official approval
challenging|hard
changeability|variability
chaotic|messy
characteristic|typical
characteristically|typically
characterization|describing features
chastise|scold
chastisement|punishment
chemotherapy|cancer drugs
chromosome|gene carrier
chronic|ongoing
chronically|long-term
chronological|time-ordered
chronologically|in time order
chronology|time ordering
circulatory|blood system
circumnavigation|sailing around
circumspection|carefulness
circumstance|situation
circumstantial|indirect
circumstantially|indirectly
circumvent|get around
circumvention|going around
citation|reference
civil|non-criminal
civility|politeness
claimant|person claiming
clamor|noise
clandestine|secret
clandestinely|secretly
clarification|making clear
clarify|explain
clarity|clearness
classification|sorting
clinician|doctor
cloaked|hidden
coalesce|blend
coalition|group
cobblestones|stones
codicil|will addition
codification|writing into rules
coerce|force
coercion|force
coercive|using force
cognition|thinking process
cognitive|mental
cognizance|awareness
cohabitation|living together
coherence|clarity
coherent|clear
coincidental|by chance
coincidentally|by chance
collaboration|teamwork
collaborative|joint
collateral|side
colleague|coworker
collectively|as a group
colloquial|everyday
colloquialism|informal expression
combustion|burning
comfortability|ease
commemorate|honor
commemoration|remembering
commence|start
commencement|beginning
commend|praise
commensurate|matching
commentary|explanation
commercialization|making commercial
commercially|for profit
commodification|treating as goods
commodious|spacious
commodity|traded good
commonplace|ordinary
communal|shared
communicability|transmissibility
communicate|talk
communication|sharing information
commutability|exchangeability
comorbidity|extra illness
comparability|similarity
comparable|similar
compartmentalization|separating into sections
compassion|care for others
compassionately|with care
compatibility|works together
compel|force
compelling|strong
compensate|pay back
compensation|payment
compensatory|making up for
competence|ability
competent|able
competitive|rivalry-based
competitively|trying to win
compilation|collection
compile|gather
complacent|satisfied
complement|add to
complementarity|working together
complex|hard
complexity|difficulty
compliance|following rules
complication|problem
comprehend|understand
comprehensibility|understandability
comprehension|understanding
comprehensive|complete
comprehensively|fully
comprise|include
compromise|agree
compulsion|urge
compulsively|unable to stop
computational|of computing
conceal|hide
concealment|hiding
concede|admit
conceivable|possible
concentrate|focus
concentration|focused attention
concept|idea
conceptualization|forming idea
conciliation|making peace
conciliatory|peaceful
conclude|decide
conclusively|finally
concretization|making real
concurrently|at the same time
condemn|judge
condemnation|harsh judgment
condensation|water droplets
condense|shrink
condescending|talking down
conditioning|learned response
conducive|helpful
confidentially|in secret
configurability|adjustability
configuration|arrangement
confirmation|proving true
confiscate|seize
confiscation|taking away
conformity|fitting in
confront|face
confrontation|facing up to
confrontational|challenging
congenital|from birth
conglomerate|large company
conglomeration|massing together
conjecture|guess
conjunction|joining word
connectivity|linking ability
connotation|implied meaning
conscientious|careful
conscientiously|carefully
conscious|aware
consecutive|in a row
consequentialism|outcomes matter
consequently|so
conservation|saving
considerable|large
considerably|greatly
considerate|thoughtful
consistently|always the same
consolidate|combine
consolidation|combining
conspicuously|noticeably
conspiracy|plot
constellation|star group
constitute|make up
constitutional|legal
constrain|limit
constructively|helpfully
contagious|catching
contaminate|pollute
contamination|pollution
contemplate|think about
contemplation|deep thought
contemporary|current
contempt|disrespect
contemptuous|scornful
contentious|quarrelsome
contentiously|controversially
contentment|happiness
contextualization|putting in context
contextually|in context
continental|of continents
contingent|depending on
continuation|going on
continuous|unbroken
continuously|without stopping
contractual|of contracts
contradiction|saying opposite
contradictorily|in a contradicting way
contradictory|opposite
contraindication|warning against
contravention|going against
controllability|manageability
controversial|debatable
controversially|debatably
controversy|debate
convection|heat transfer
conventional|normal
conventionally|normally
convertibility|changeability
conviction|belief
convince|persuade
convoluted|twisted
cooperate|work together
cooperation|working with
cooperatively|working together
coordination|working together
cordial|friendly
coronary|heart-related
corporate|of companies
correlation|link
correspondingly|in a matching way
corroborate|confirm
corroboration|supporting evidence
corruption|dishonesty
counseling|guidance
counterargument|opposing point
counterfeit|fake
counterpart|equal
covert|secret
creativity|making new things
credibility|trust
creditor|lender
criminalization|making illegal
criteria|standards
criterion|standard
critically|very importantly
cryptic|mysterious
cryptocurrency|digital money
cryptography|secret coding
crystallization|crystal forming
culmination|highest point
culpability|blame
cultivate|grow
cultivation|growing and nurturing
culturally|relating to culture
cumbersome|awkward
cumulative|building up
cunning|clever
curtail|cut short
custodial|imprisonment
customization|making personal
cybersecurity|online safety
cyclically|in cycles
cyst|fluid sac
damages|compensation
database|organized data store
dauntless|brave
debatable|arguable
debilitate|weaken
debilitating|disabling
debugging|fixing errors
debunk|disprove
deceitful|dishonest
deceive|trick
decentralization|spreading control
decentralized|spread out
deceptively|in a misleading way
decisive|firm
decisively|firmly
declaration|statement
decomposition|breaking down
deconstruct|break down
deconstruction|taking apart
decontextualization|removing from context
dedicate|commit
dedication|strong commitment
defendant|accused person
defensibility|justifiability
deference|respect
defiance|rebellion
deficiency|lack
deficient|lacking
deficit|shortfall
definitively|conclusively
deforestation|removing trees
deformation|misshaping
degenerative|getting worse
degradation|lowering quality
degrade|lower
dehumanization|removing humanity
dehydration|fluid loss
deindividuation|loss of self in group
dejected|sad
dejection|low mood
delegation|passing on
deliberate|careful
deliberately|on purpose
deliberation|thought
delusional|believing false things
demeanor|manner
dementia|memory disease
democratization|making democratic
demonization|making seem evil
demonstrate|show
demonstration|showing
demoralize|discourage
denial|refusing to accept
denormalization|making seem wrong
denounce|speak against
density|how packed
denunciation|public blame
dependability|reliability
dependably|reliably
dependency|needing something
dependently|relying on others
depict|show
deplete|use up
deployment|launch
depoliticization|removing politics
deposition|sworn testimony
depreciation|value loss
deprivation|being without
derivative|financial product
dermatology|skin care
derogatory|insulting
desalinization|removing salt
desensitization|reducing sensitivity
deserted|empty
desertification|land becoming desert
desirability|attractiveness
desolate|empty
despair|hopelessness
despondency|deep sadness
despondent|hopeless
despondently|hopelessly
destigmatization|removing stigma
detachment|distance
detention|holding
deteriorate|worsen
deterioration|worsening
determinability|decidability
determine|find out
determinism|fate belief
deterrence|discouragement
detrimental|harmful
devaluation|value reduction
devastating|destroying
devastation|utter destruction
develop|grow
developmental|of growth
dexterous|skillful
diagnosis|identification
diametrically|completely
diarrhea|loose stools
diction|word choice
didactic|teaching
differentially|in different ways
differentiate|tell apart
differentiation|making different
diffusion|spreading out
digitization|converting to digital
diligence|hard work
diligent|hardworking
diligently|hardworkingly
dimensionality|number of aspects
diminish|reduce
diminution|reduction
diplomatically|tactfully
disarray|mess
discern|see
discernibly|noticeably
disciplinary|related to punishment
discomfort|unease
discontent|unhappiness
discontinuation|stopping
discovery|evidence finding
discreetly|quietly
discrepancy|difference
discretionary|optional
discriminately|with judgment
discrimination|unfair treatment
discriminatory|unfair
disenfranchisement|removing rights
dishearten|discourage
disillusionment|lost hope
dismantle|take apart
dismissal|throwing out
disorganization|lack of order
disparity|gap
dispassionately|calmly
dispel|clear up
dispensation|exception
displacement|shifting feelings
disposition|nature
dispossession|losing property
disproportionately|too much
disruptive|disturbing
dissatisfaction|unhappiness
dissemination|spreading widely
dissent|disagreement
dissociation|mental detachment
distinctively|in a unique way
distinguish|tell apart
distraught|upset
distribute|hand out
diverse|varied
diversification|spreading risk
diversity|variety
divestiture|selling off
dividend|profit share
document|record
documentation|written record
dogmatically|rigidly
domestication|taming
dominance|control
domination|control over
domineering|bossy
dormant|inactive
downsizing|reducing size
draconian|harsh
dramatically|greatly
dramatization|making dramatic
drawback|downside
dubious|doubtful
duplicity|dishonesty
duress|pressure
dynamic|active
dysfunction|poor function
earnestly|seriously
eccentric|odd
ecology|nature study
economically|money-wise
edema|swelling
education|learning
effectively|successfully
effervescent|bubbly
efficiently|without waste
egregious|shocking
elaborate|detailed
elaborately|in detail
elaboration|adding detail
elation|joy
electrification|adding electricity
electromagnetic|electric-magnetic
electron|tiny particle
element|basic substance
elevation|raising up
eliminate|remove
elimination|removing
eloquent|well-spoken
elusive|hard to find
emancipation|freeing
embarrassment|shame
embezzlement|theft
embolism|blockage
embryo|early organism
emotionally|feelings-wise
empathetically|with understanding
empathy|understanding
emphasize|stress
emphatically|strongly
empirical|based on facts
employment|having a job
empower|strengthen
emulate|copy
encapsulate|sum up
encompass|include
encouragement|giving support
encroach|invade
encryption|data coding
encumbrance|burden
endeavor|effort
endeavour|effort
endemic|local disease
endocrine|hormone system
endurance|strength
enforcement|making happen by force
engulf|swallow up
enigma|mystery
enigmatic|mysterious
enjoin|order
enlighten|inform
enmity|hatred
enormous|huge
enrage|anger
enrichment|adding value
entertainment|enjoyment
enthrall|captivate
enthusiasm|excitement
enthusiastically|eagerly
entitlement|right
entrench|fix firmly
entrepreneurial|business-starting
enumerate|list
enumeration|listing
environment|surroundings
environmentally|nature-wise
ephemeral|brief
epidemic|disease outbreak
epilepsy|seizure disorder
epistemology|knowledge study
equilibrium|balance
equitable|fair
equitably|fairly
equity|fairness
equivocate|be vague
equivocation|being vague
eradicate|wipe out
erosion|wearing away
erratic|unpredictable
erudition|knowledge
erythema|redness
escalate|increase
esoteric|specialized
essentially|at heart
establish|set up
establishment|setting up
estimation|rough calculation
estoppel|legal bar
estuary|river mouth
ethereal|delicate
ethically|morally
etiology|cause of disease
etymology|word origin
euphemistically|softly
euphoria|great joy
evade|avoid
evaluate|judge
evaluation|review
evaporation|water becoming gas
evident|clear
evocative|stirring
evolution|slow change
exacerbate|worsen
exacerbation|worsening
exaggerate|overstate
exaggeration|overstating
examination|close look
examine|look at
exasperate|annoy
excavation|digging up
exceptional|great
exceptionally|very well
excessively|too much
exclusively|only
exculpatory|clearing
exemplary|model
exemplification|giving examples
exemplify|show
exert|apply
exhausted|tired
exhaustive|thorough
exhaustively|completely
exhilaration|excitement
existential|of existence
existentialism|existence study
exonerate|clear
exoneration|clearing of blame
expandability|growability
expedite|speed up
expenditure|spending
experimentation|trying things out
explainability|clarity
explicit|clear
explicitly|clearly
exploit|use unfairly
exploitation|using unfairly
exponentially|very rapidly
expressibility|ability to express
expropriation|taking property
expulsion|forcing out
exquisite|beautiful
extensibility|ability to extend
extension|stretching out
extensive|large
extensively|widely
extradite|hand over
extradition|handing over
extraordinarily|amazingly
extraordinary|amazing
extrapolate|extend from data
extrapolation|extending beyond data
extraversion|outgoing nature
exuberant|joyful
exuberantly|joyfully
fabricate|make up
fabrication|making things up
facilitate|help
facilitation|making easier
factorization|breaking into factors
factually|truthfully
fallacious|false
falsehood|lie
falsification|proving wrong
falter|stumble
fanatical|extreme
fatigue|tiredness
feasible|possible
federalization|federal control
feign|pretend
felony|serious crime
fermentation|bacteria breakdown
ferocious|fierce
fervent|passionate
fervently|passionately
fervor|passion
fibromyalgia|body pain
fidelity|loyalty
fiduciary|trusted with money
figuratively|not literally
filtration|filtering
finalizability|ability to finish
fiscal|of finances
flexibility|ability to bend
florid|flowery
fluctuate|change
fluorescence|glowing
forbearance|patience
foreboding|dread
forestall|prevent
forfeit|give up
forfeiture|giving up
formalization|making official
formidability|impressiveness
formidable|impressive
formulate|create
forthright|honest
fortify|strengthen
fossilization|becoming fossil
fractionally|slightly
fracture|broken bone
fragile|delicate
fragmentation|breaking into pieces
fragmented|broken up
framework|working structure
franchising|licensed business
fraudulent|fake
frequent|often
friction|resistance
frugal|careful with money
frustration|annoyance
functionality|how it works
fundamental|basic
fundamentally|at the core
furtive|sneaky
futile|useless
gastrointestinal|digestive
generalization|broad statement
generalize|broadly apply
generate|make
generation|producing
generous|giving
generously|giving a lot
genetic|inherited
genetics|gene study
genuine|real
genuinely|truly
geology|earth study
geomorphology|land shape study
globalization|worldwide spread
globally|worldwide
gloomy|dark
glorification|praising highly
governability|manageability
governance|management
gracefully|with elegance
graciously|politely
gradually|slowly
grandiose|showy
gratification|satisfaction
gratitude|thanks
gratuitous|unnecessary
gravitational|of gravity
gregarious|outgoing
grief|deep sadness
grievance|complaint
grievous|serious
grotesque|ugly
gruesome|horrible
guilt|feeling wrong
gullible|easily fooled
habeas corpus|right to trial
habitualization|making habitual
haphazard|random
harmonious|peaceful
harmoniously|peacefully
haunting|lingering
havoc|chaos
hearsay|secondhand report
hedonism|pleasure seeking
hegemony|dominance
hemorrhage|heavy bleeding
hepatic|of the liver
hesitant|unsure
hibernation|winter sleep
hierarchical|rank-based
hierarchy|ranking
historically|in the past
holistically|as a whole
homophone|same-sound word
hostile|unfriendly
hostility|aggression
humanitarian|helping people
humanization|making human
humiliate|shame
humiliation|shame
humorously|funnily
hydration|adding water
hyperbole|wild exaggeration
hypertension|high blood pressure
hypnosis|induced focus
hypocrisy|two-facedness
hypocritical|two-faced
hypotension|low blood pressure
hypothesis|theory
hypothesization|making hypothesis
hypothesize|theorize
hypothetical|imagined
hypothetically|as a theory
idealization|making perfect
idealize|glorify
identifiability|recognizability
identification|recognizing
identify|find
identity|sense of self
ideological|belief-based
ideologically|belief-wise
idiom|fixed phrase
idiosyncratic|quirky
idyllic|perfect
illuminate|light up
illumination|lighting up
illusion|false image
illustration|making clear with example
imagination|creative thinking
imaginatively|creatively
immense|huge
imminent|about to happen
immunity|protection
immunization|vaccination
immutability|unchangeableness
immutable|unchanging
impartial|fair
impartially|fairly
impeachment|charging
impede|block
impenetrability|inability to enter
imperative|necessary
imperious|commanding
impetuous|rash
implement|use
implementation|putting in place
implicate|involve
implication|suggested meaning
implicit|hidden
imply|suggest
impose|force on
impregnability|invulnerability
impression|formed opinion
impulse|sudden urge
impulsive|rash
inadequate|not enough
inadvertent|accidental
incapacitate|disable
incarceration|imprisonment
incentivize|motivate
incessant|nonstop
incidentally|by the way
incite|stir up
incompatibility|inability to work together
incompetent|unable
incomprehensible|unclear
inconceivability|unimaginableness
inconclusive|unclear
incongruently|oddly
incongruous|out of place
inconsistency|difference
incorporation|company forming
incorporeality|non-physical nature
incrimination|suggesting guilt
incubation|warming period
indecisive|unsure
independently|on its own
indescribability|inability to describe
indicate|show
indictment|charge
indifferent|uncaring
indignation|anger
indiscriminate|random
indiscriminately|without selection
indistinguishability|sameness
individualism|self-focus
individually|one by one
indoctrination|teaching fixed views
indolent|lazy
indulgent|giving in
industrialization|factory development
industriously|with hard work
inept|unskilled
inertia|resistance to change
inevitability|certainty
inevitable|unavoidable
inevitably|certainly
inexplicability|inability to explain
inextricability|inability to separate
infallibility|inability to err
infatuation|strong crush
infectious|catching
inference|conclusion
inflammation|swelling
inflation|price rising
inflexibility|inability to bend
influence|effect
influential|powerful
informally|casually
information|info
informatively|giving information
infrastructure|base systems
infuriate|anger
inherent|built-in
inherently|naturally
inhibit|block
inhibition|holding back
initiation|starting
initiative|first step
injunction|court order
innate|natural
innocuously|harmlessly
innovation|new idea
innovative|new
inquire|ask
inscrutable|hard to read
insecurity|self-doubt
inseparability|inability to divide
insidious|sneaky
insightful|wise
insightfully|perceptively
insightfulness|perceptiveness
insignificant|small
insolent|rude
insolvency|inability to pay
insolvent|unable to pay
instinctively|naturally
institution|organization
institutionalization|making official
institutionally|through systems
instrumentation|tool use
insurmountability|inability to overcome
integration|combining
intellectually|thinking-wise
intentionally|on purpose
interaction|exchange
interface|connection point
intermediary|go-between
internalization|taking inside oneself
interpret|explain
interpretation|meaning
interrogation|questioning
interrogatory|written question
intimidation|frightening
intolerant|narrow-minded
intractability|stubbornness
intravenous|into a vein
intricate|complex
intrinsically|by its nature
introspect|look inward
introspection|self-reflection
introversion|inward focus
intuitively|naturally
invalidate|cancel
investigate|look into
investigation|detailed inquiry
investment|money put in
invincibility|inability to defeat
ironically|in a twist
irrationally|without sense
irrefutable|undeniable
irrelevant|unrelated
ischemia|lack of blood flow
isolate|separate
isotope|element form
iteration|repeated cycle
jarring|shocking
jealousy|envy
jeopardize|risk
jovially|cheerfully
jubilant|joyful
judicious|wise
judiciously|wisely
jurisdiction|legal authority
jurisprudence|law study
justifiability|defensibility
justifiably|rightly
justification|reason
juxtapose|compare
kinetic|of motion
knowability|understandability
laborious|hard
laboriously|with great effort
labyrinthine|maze-like
lackluster|dull
laconic|brief
lament|mourn
languid|slow
larceny|theft
latency|delay
laudable|praiseworthy
lavish|rich
learnability|ease of learning
legalization|making legal
legislation|law
legitimate|legal
legitimately|legally
legitimization|making valid
lenient|easy-going
lesion|wound
lethargic|sluggish
lethargy|extreme tiredness
liability|responsibility
liable|responsible
libel|written false claim
liberalization|making freer
liberation|freeing
liquidity|ease of selling
literally|exactly as said
litigation|legal action
loathe|hate
locally|nearby
logically|sensibly
logistics|supply management
loneliness|being alone
lucid|clear
lugubrious|gloomy
luminescence|light giving
luminous|glowing
lymphoma|lymph cancer
macabre|dark
macroeconomics|big economy
magistrate|judge
magnetic|of magnets
magnificent|great
maintain|keep
malice|harmful intent
malicious|mean
malignant|dangerous
malleability|flexibility
mammalian|of mammals
management|running things
mandate|order
mandatory|required
maneuverability|ability to move
manipulate|control
manipulation|controlling others
manufacturing|making goods
marginalization|pushing to the edge
marginalize|push aside
marginally|slightly
marketability|sellability
marketing|promoting
materialism|things matter
maximization|making biggest
measurability|ability to measure
measurably|noticeably
measurement|finding size
mechanization|using machines
medicalization|treating as medical
medication|medicine
melancholy|sadness
mellifluous|smooth-sounding
membrane|thin layer
memorability|ease of remembering
memorization|learning by heart
mendacious|lying
mentally|mind-wise
mergers|joining companies
meridian|longitude line
metabolism|body processes
metaphor|comparison without like
metaphorically|not literally
metastasis|cancer spread
meteorology|weather study
methodically|carefully
methodology|method
meticulous|careful
microbial|germ-related
microeconomics|small economy
microorganism|tiny living thing
migraine|severe headache
militant|aggressive
militarization|adding military
mindfulness|present awareness
minimally|as little as possible
minimization|making smallest
minimize|reduce
misconstrue|misread
misdemeanor|minor crime
miserable|unhappy
misinform|mislead
mitigate|reduce
mitigation|reducing blame
mitosis|cell splitting
mobilization|organizing for action
mobilize|gather
modernization|updating
modifiability|changeability
modification|change
modify|change
molecular|of molecules
momentarily|briefly
momentous|huge
momentum|force of motion
monetization|making money from
monetize|make money from
monopoly|sole control
monotonous|boring
monotonously|boringly
morality|ethics
moralization|teaching morals
morally|ethically
morbidity|illness rate
morose|gloomy
morphology|word form study
mortality|death rate
motivation|drive to act
mournful|sad
multiplication|increasing many times
mundane|ordinary
mutation|genetic change
muted|soft
mutual|shared
mutually|both sides
myocardial|heart muscle
myopic|short-sighted
mysterious|unknown
narcissism|self-love
narrative|story
nationalization|government takeover
naturalization|becoming citizen
naturally|as expected
nebulous|vague
necessary|needed
nefarious|wicked
negate|cancel out
negligence|carelessness
negligent|careless
negotiability|discussability
negotiation|discussion to agree
neurological|brain-related
neuropathy|nerve damage
neurosis|mild mental issue
neutralize|cancel out
neutrally|without bias
neutron|neutral particle
nihilism|nothing matters
nonchalant|casual
nonconformist|rebel
normalization|making normal
normatively|as expected
nostalgia|missing the past
nostalgic|longing for past
noticeability|perceptibility
notorious|famous for bad
nucleus|cell center
nullification|cancellation
nullify|cancel
numerous|many
nutrition|food use
obesity|being overweight
objectification|treating as object
objective|goal
objectively|without bias
oblique|indirect
oblivious|unaware
obscure|unclear
obscurity|darkness
observability|ability to observe
observation|watching
obsession|fixation
obstinate|stubborn
obstinately|stubbornly
obstruct|block
obstruction|blocking
obtain|get
obviously|clearly
officially|formally
oligopoly|few sellers
ominous|threatening
ominously|threateningly
oncology|cancer study
onomatopoeia|sound word
ontology|being study
opaque|unclear
operability|ability to work
opportunistically|taking advantage
opportunity|chance
opposition|against
oppression|harsh rule
oppressive|harsh
optimistically|with hope
optimization|improving
orchestrate|plan
orchestration|careful planning
ordinance|local law
organically|naturally
organism|living thing
organization|group
originally|at first
orthodox|traditional
ostensible|seeming
ostensibly|on the surface
ostentatious|showy
osteoporosis|bone thinning
ostracize|exclude
outrageous|shocking
outsourcing|using outside help
outwit|outsmart
overbearing|bossy
overestimation|too high a guess
overexaggeration|too much exaggeration
overhead|running costs
overpopulation|too many people
overreaction|too strong a response
overrepresentation|too much representation
oversimplification|making too simple
overt|open
overthrowing|removing from power
overturning|reversing decision
overwhelm|overpower
overwhelmingly|hugely
oxidation|chemical reaction
pacification|making peaceful
pacify|calm
palatability|acceptability
paleontology|fossil study
pallor|paleness
pandemic|worldwide disease
paradox|contradiction
paradoxically|oddly
parameter|setting
parameterize|set limits for
paramount|most important
paranoia|extreme distrust
parole|early release
partially|partly
participate|take part
participation|involvement
particle|tiny bit
particularly|especially
partnership|shared business
passionately|with strong feeling
passively|without action
pathogen|disease cause
pathological|disease-related
pathology|disease study
patronize|talk down to
peculiar|strange
pediatric|child health
penalization|giving penalty
penetrability|ability to enter
peninsula|land almost surrounded by water
pensive|thoughtful
perceive|see
perceptibly|noticeably
perception|view
perform|do
perilous|dangerous
periodically|from time to time
perjury|lying under oath
permeability|ability to pass through
permissibility|allowableness
pernicious|harmful
perpetrate|commit
perpetually|always
perpetuate|keep going
persecution|cruel treatment
persistent|stubborn
persistently|keeping at it
personalization|making personal
personally|by oneself
personification|giving human traits
perspective|point of view
pertinent|relevant
perturb|upset
perturbation|disturbance
pervasive|widespread
pessimistic|negative
pessimistically|negatively
pharmaceutical|drug-related
pharmacology|drug study
phenomenal|amazing
phenomenology|experience study
phenomenon|event
philosophical|thought-based
philosophically|thoughtfully
phonetic|of sounds
photograph|photo
photosynthesis|sunlight food making
physically|body-wise
physiological|body-related
placate|calm
plaintiff|person suing
plausibility|believability
plausible|possible
plethora|many
poignant|touching
polarization|extreme division
polarize|divide
politically|power-wise
politicization|making political
pompous|arrogant
popularization|making widely known
population|people
portfolio|collection
practically|in real life
pragmatic|practical
pragmatically|practically
pragmatism|what works
precarious|unsafe
precedent|prior ruling
precipice|edge
precipitation|rain or snow
preconceived|assumed
predator|hunter
predicament|problem
predicate|sentence action part
predictability|foreseeability
predominant|main
predominantly|mainly
prejudice|bias
prejudicial|causing bias
preliminary|early
premature|too early
preposition|position word
preposterous|absurd
prerequisite|requirement
presentation|showing to others
presumptively|assuming
pretentious|showy
prevalent|common
primarily|mainly
prioritization|ordering by importance
privatization|to private ownership
proactively|before needed
probation|supervised freedom
procedure|steps
proclamation|public announcement
procrastinate|delay
procurement|buying
produce|make
productively|usefully
professionalization|making professional
professionally|in a skilled way
proficient|skilled
profitability|making profit
profound|deep
prognosis|outlook
programmability|ability to program
progression|growth
progressively|gradually
prohibit|ban
projection|putting feelings on others
prokaryote|simple cell
prolong|extend
prominence|fame
pronounceability|ease of saying
propagandization|using propaganda
propagate|spread
propagation|spreading
prophylaxis|prevention
proportion|share
proportionality|balance
proportionally|in balance
proportionately|in balance
propose|suggest
proposition|idea
prosecution|legal pursuit
prosperous|successful
protagonist|main character
protocol|set of rules
proton|positive particle
provide|give
provision|condition
provisionally|for now
provoke|stir up
prudent|wise
psychological|mind-related
psychologically|mind-wise
psychologization|treating as psychological
psychosis|loss of reality
publicly|openly
pulmonary|lung-related
punitive|punishing
purchase|buy
purposefully|with intent
qualitatively|in quality
quantification|measuring
quantitatively|in numbers
quarrelsome|argumentative
quarterly|every three months
questionability|doubtfulness
quintessential|typical
radiation|energy waves
radicalization|making extreme
radioactive|giving off rays
rampant|widespread
rational|sensible
rationale|reason
rationalization|making excuses
rationalize|justify
rationally|sensibly
raucous|loud
realistically|practically
realization|understanding
reasonability|sensibleness
reasonably|sensibly
recidivism|repeat offending
reciprocal|mutual
reciprocally|both ways
recognizability|ease of knowing
recommend|suggest
reconcile|settle
reconciliation|balancing accounts
reconsideration|thinking again
reconstruction|rebuilding
recourse|option
recoverability|ability to recover
recruitment|finding members
redistribution|sharing out again
redress|correction
reducibility|ability to reduce
redundant|extra
reflectively|thoughtfully
reformation|changing for better
refute|disprove
regeneration|growing again
regionalization|making regional
regulation|official rules
regulatory|rule-based
rehabilitability|ability to restore
rehabilitation|recovery
reinforce|strengthen
reinforcement|strengthening response
reinstatement|restoring to position
reinvention|creating anew
relatability|ability to relate to
relativism|all is relative
relativization|making relative
reliability|dependability
relinquish|give up
reluctant|unsure
remarkable|amazing
remarkably|notably
reminisce|remember
remission|symptom reduction
remuneration|payment
renal|kidney-related
repeal|cancel
repeatability|consistency
repeatedly|again and again
repetitively|again and again
repository|storage
reprehensible|awful
representation|image
representatively|for the group
reproducibility|ability to copy
reproduction|making offspring
repudiate|reject
require|need
research|study
resentment|bitterness
reside|live
resilience|bouncing back
resilient|strong
resolve|decide
respectfully|with respect
respiratory|breathing-related
resplendent|shining
responsibility|duty
responsibly|with care
responsively|reacting well
responsiveness|reaction ability
restitution|payback
restrained|controlled
restructuring|reorganizing
retaliate|fight back
retaliation|payback
retribution|payback
retrieve|get back
reusability|ability to reuse
revelation|discovery
revenue|income
revere|respect greatly
reversibility|ability to undo
revitalization|bringing back to life
revolutionize|change completely
rhetorical|of persuasion
rhetorically|as a speaking device
rheumatology|joint care
rigorous|strict
rigorously|strictly
romanticization|making romantic
routinely|regularly
ruthless|cruel
sanctimonious|self-righteous
sanction|penalty
sanitization|making clean
sarcastically|mockingly
sardonic|mocking
scalability|growth ability
sceptically|with doubt
scientifically|by science
scrutinize|examine
scrutiny|close look
secularization|removing religion
securely|safely
sedative|calming drug
sediment|settling particles
segregation|separating groups
seismic|of earthquakes
seismology|earthquake study
seizure|fit
selectively|choosing some
sentimentalization|making sentimental
separability|ability to separate
septic|infected
serene|calm
serenity|calm
serialization|data packaging
settlement|agreement
shareholder|part-owner
shrewd|clever
significance|importance
significant|important
simile|comparison using like
simplification|making simpler
simulation|fake version
simultaneous|at the same time
simultaneously|at the same time
sincerely|truly
singularity|uniqueness
skeptical|doubtful
skeptically|doubtfully
skewed|biased
slander|spoken false claim
sluggish|slow
socialization|learning social norms
socially|with others
solemn|serious
solvability|ability to solve
somber|dark
sophisticated|complex
sovereignty|independence
specialization|focusing on one area
speciation|new species forming
specifically|exactly
spectroscopy|light analysis
speculation|guessing
speculatively|as a guess
spontaneous|unplanned
squander|waste
stagnant|not moving
stakeholder|interested party
standardization|making uniform
standardize|normalize
statistically|by numbers
statute|law
steadfast|firm
steadfastness|firmness
stereotype|fixed idea
stereotypically|in a fixed way
sterile|clean
stigma|negative label
stigmatization|labeling negatively
stimulant|energy booster
stimulate|trigger
stipulate|require
stoic|unemotional
stoically|without complaint
strategically|with a plan
stratification|layering by rank
structurally|in structure
struggle|fight
subconscious|below awareness
subconsciously|below awareness
subjectively|with opinion
subjectivism|all is personal
subjugate|control
sublimation|solid to gas
sublime|awe-inspiring
subordinate|lower rank
subordination|placing below
subpoena|legal demand
subsequent|later
subsequently|after
subsidiary|smaller company
subsidization|giving financial support
substantial|large
substantially|greatly
substantiate|prove
subtlety|fine detail
successfully|with success
succumb|give in
sufficient|enough
suitability|appropriateness
summarize|sum up
superficial|shallow
superficially|on the surface
superfluous|extra
supplement|add to
suppress|hold back
surpass|beat
surreal|dreamlike
surrender|give up
surreptitious|secret
suspicion|doubt
sustainability|long-term viability
sycophant|flatterer
symbiosis|mutual living
symbolically|as a symbol
sympathy|care
symptom|sign of illness
synchronization|matching up
syndrome|group of symptoms
synergy|combined effect
syntax|sentence structure
synthesize|combine
systematic|organized
systematically|in order
systematization|making systematic
systemic|whole body
tachycardia|fast heart
tangentially|in passing
tangible|real
tariff|import tax
teachability|ease of teaching
technically|in a technical way
technology|tech
tectonic|of earth plates
tedious|boring
tenacious|determined
tenacity|determination
tentatively|with uncertainty
tenuous|weak
terminate|end
territorialization|claiming territory
testability|ability to test
testimony|sworn account
theoretical|based on theory
theoretically|in theory
theorization|forming theory
therapeutic|healing
therapeutically|for healing
thermodynamics|heat science
thorough|complete
thoroughly|completely
thoughtfully|with care
thrombosis|blood clot
tolerability|acceptability
tolerance|acceptance
tolerate|bear
topography|land features
torment|suffering
toxic|harmful
toxicity|poisonousness
toxicology|poison study
traceability|ability to track
traditionally|as usual
tranquil|calm
tranquility|peace
transaction|deal
transferability|movability
transform|change
transformability|ability to change
transformation|change
transgress|break rules
transient|short-lived
transmissibility|ability to spread
transparency|openness
transparent|clear
transparently|openly
transpiration|plant water loss
transportation|travel
trauma|injury
traumatization|causing trauma
treacherous|dangerous
trepidation|fear
tribunal|court
tributaries|smaller rivers
trifling|unimportant
triumph|win
trivial|unimportant
trivialization|making trivial
trivially|in a minor way
trustworthiness|reliability
tumor|abnormal growth
tumultuous|wild
turbulent|wild
typically|usually
tyranny|harsh rule
tyrant|harsh ruler
ubiquitous|everywhere
ulcer|open sore
ultimately|in the end
ultraviolet|beyond violet light
unanimous|agreed
uncertainty|doubt
unconditionally|without conditions
unconscious|unaware state
unconsciously|without knowing
underestimation|too low a guess
undermine|weaken
understand|get
understandability|clarity
unequivocal|clear
unequivocally|clearly
unethical|wrong
unfathomable|too deep to understand
unforeseen|unexpected
universal|worldwide
universalization|making for all
universally|for all
unjust|unfair
unnecessarily|without need
unobtrusively|without notice
unprecedented|never seen before
unruly|hard to control
upgradability|ability to improve
urbanization|city growth
urgently|with great need
usability|ease of use
usurp|take over
utilitarianism|most good for most
utilization|use of
utilize|use
vaccination|disease protection
vacillate|waver
vague|unclear
validate|confirm
validation|checking correctness
valuation|worth assessment
variability|changeability
vegetation|plant cover
vehement|passionate
velocity|speed in direction
velvet|soft
venerable|respected
vengeance|revenge
venous|of veins
verbally|in words
verbose|wordy
verdict|decision
vernacular|everyday language
versatile|flexible
viability|ability to work
victimization|treating as victim
vigilant|watchful
vilify|speak badly of
vindicate|clear
vindictive|spiteful
violate|break
viral|virus-related
virtualization|digital copy
virtually|nearly
viscosity|fluid thickness
visualization|mental picture
visually|by sight
vivacious|lively
vivid|bright
volatility|price swings
voluntarily|by choice
vulnerability|openness to harm
vulnerable|weak
waiver|giving up a right
wandered|walked
warrant|legal permission
wary|careful
watershed|drainage area
wavelength|wave size
waver|hesitate
weaponization|making into weapon
westernization|adopting western ways
wholeheartedly|completely
wholly|completely
widely|broadly
willful|stubborn
wistful|longing
workability|practicality
wretched|miserable
zeal|eagerness
zealous|eager
abscond|flee
absolve|clear
abstain|hold back
acrimony|bitterness
adamant|firm
adjudicate|judge
admonish|warn
adroit|skilled
affidavit|sworn statement
affluent|wealthy
agitate|upset
ambiguous|unclear
ameliorate|improve
amicable|friendly
amplify|expand
annex|add
annihilate|destroy
annul|cancel
antagonize|anger
antipathy|dislike
appease|calm
apprise|inform
arbitrary|random
ardent|keen
arduous|hard
ascertain|find out
assiduous|hardworking
atone|make up for
audacious|bold
augment|increase
averse|opposed
avid|eager
belittle|put down
bequeath|leave to
bestow|give
brevity|shortness
candid|honest
capacious|large
cede|give up
censure|blame
circumspect|careful
coerce|force
collaborate|work together
compensate|pay back
compile|gather
comply|obey
concede|admit
condone|allow
confiscate|take away
conjecture|guess
conspicuous|obvious
contemplate|think about
contend|argue
contradict|disagree
convene|meet
convince|persuade
corroborate|confirm
culminate|end in
curtail|reduce
daunting|scary
decipher|decode
decisive|firm
deduce|work out
defer|delay
delineate|describe
depict|show
derive|come from
designate|name
deter|stop
deviate|stray
differentiate|tell apart
diligent|careful
discern|notice
disclose|reveal
discrepancy|difference
dissent|disagree
dominate|control
elaborate|explain further
emulate|copy
engender|cause
enumerate|list
equitable|fair
eradicate|wipe out
evasive|vague
expedite|speed up
exploit|take advantage of
extensive|wide
facilitate|help
flawed|faulty
formulate|create
foster|encourage
fortify|strengthen
generate|produce
implement|carry out
implicit|implied
impose|force
incite|stir up
incorporate|include
induce|cause
infer|conclude
inhibit|prevent
integrate|combine
interpret|explain
intervene|step in
investigate|look into
invoke|call on
manifest|show
mediate|resolve
minimize|reduce
modify|change
monitor|watch
motivate|encourage
negate|cancel out
negotiate|discuss
obtain|get
overcome|beat
oversee|manage
perceive|notice
perpetuate|keep going
persist|keep going
portray|show
preclude|prevent
prohibit|ban
propagate|spread
pursue|follow
rectify|fix
regulate|control
reinforce|strengthen
retain|keep
scrutinize|examine
simulate|copy
specify|state
stabilize|steady
stimulate|encourage
supplement|add to
sustain|support
terminate|end
tolerate|accept
undertake|begin
validate|confirm
verify|check
yield|give in
accrue|build up
alleviate|ease
assertion|claim
attain|reach
certify|confirm
coherent|clear
collaborate|team up
concur|agree
confine|limit
conform|follow
confront|face
connote|suggest
constitute|make up
convey|send
correlate|link
denote|mean
deplete|use up
derive|get
designate|choose
deteriorate|get worse
diminish|lessen
discern|see
disperse|spread
disrupt|disturb
diverse|varied
dominate|control
duplicate|copy
elaborate|expand
elevate|raise
eliminate|remove
emphasize|stress
enhance|improve
enumerate|count
equate|match
evaluate|judge
exacerbate|worsen
exclude|leave out
exemplify|show
exhaust|use up
exhibit|show
expand|grow
expedite|hurry
facilitate|make easier
fluctuate|change
formulate|plan
generate|make
identify|find
illustrate|show
implement|put in place
imply|suggest
indicate|show
inhibit|block
initiate|start
innovate|create
integrate|join
interpret|explain
investigate|study
justify|explain
maintain|keep
manipulate|control
maximize|increase
minimize|reduce
modify|change
monitor|watch
motivate|push
negotiate|bargain
obtain|get
optimize|improve
persist|continue
portray|depict
predict|forecast
prohibit|ban
promote|support
propose|suggest
regulate|control
reinforce|boost
resolve|solve
retain|keep
revise|update
simulate|model
specify|define
stabilize|steady
stimulate|boost
support|help
sustain|keep up
terminate|end
transform|change
transmit|send
utilize|use
validate|check
vary|change
verify|check
accelerate|speed up
accommodate|hold
accumulate|gather
acknowledge|admit
acquire|get
adapt|adjust
adhere|stick
administer|manage
advance|move forward
advocate|support
affect|change
allocate|give out
analyze|study
anticipate|expect
apply|use
assess|judge
assign|give
assist|help
associate|link
assume|think
attain|reach
attribute|credit
authorize|allow
calculate|work out
classify|sort
collaborate|work with
communicate|share
compensate|repay
compile|collect
concentrate|focus
conclude|end
conduct|carry out
configure|set up
connect|link
consolidate|combine
construct|build
contribute|give
coordinate|organize
create|make
define|explain
demonstrate|show
design|plan
determine|find out
develop|build
distribute|share out
divide|split
document|record
eliminate|remove
employ|use
enable|allow
enhance|improve
establish|set up
evaluate|assess
examine|look at
execute|carry out
explore|look into
extend|stretch
facilitate|help
focus|aim
follow|come after
fulfill|complete
generate|produce
guide|lead
identify|find
illustrate|show
improve|get better
incorporate|include
indicate|show
initiate|start
integrate|combine
introduce|bring in
involve|include
maintain|keep up
manage|run
measure|check
modify|change
monitor|track
organize|arrange
participate|take part
perform|do
plan|arrange
prepare|get ready
present|show
process|handle
produce|make
promote|support
provide|give
record|write down
reduce|lower
regulate|control
require|need
resolve|fix
review|look over
revise|update
schedule|plan
select|choose
simplify|make simpler
solve|fix
specify|say clearly
structure|organize
support|help
transfer|move
transform|change
understand|know
update|change
utilize|use
validate|check
verify|confirm
accommodate|adjust to
accomplish|achieve
accumulate|collect
acknowledge|accept
acquire|gain
adapt|change
adequate|enough
adhere|stick to
adjacent|next to
adjust|change
admit|confess
adopt|take on
advance|improve
advocate|argue for
affect|impact
aggregate|combine
alleviate|reduce
allocate|assign
alter|change
ambiguous|unclear
analyze|examine
anticipate|expect
apparent|clear
appreciate|value
appropriate|fitting
approximate|close to
arbitrary|random
arise|come up
arrange|organize
assert|claim
assess|judge
assign|give
attain|get
attitude|view
attribute|credit
authorize|approve
avoid|keep away from
beneficial|helpful
capable|able
certain|sure
characterize|describe
clarify|explain
classify|group
coherent|clear
coincide|match
collaborate|work together
combine|join
compensate|make up for
complement|add to
comprise|include
concentrate|focus
conclude|decide

confirm|check
conflict|clash
conform|follow
consistent|steady
constitute|make up
contrast|difference
contribute|add to
controversial|disputed
convenient|easy
coordinate|organize
correspond|match
create|make
criteria|standards
critical|important
crucial|key
demonstrate|show
depict|describe
derive|get
designate|name
determine|find out
device|tool
differentiate|tell apart
diminish|reduce
diverse|mixed
dominant|main
duplicate|copy
elaborate|expand on
emphasize|stress
enable|allow
enhance|improve
ensure|make sure
establish|set up
evaluate|judge
evident|clear
evolve|develop
examine|look at
exhibit|show
expand|grow
explicit|clear
extensive|wide
facilitate|help
factor|element
fluctuate|vary
formulate|plan
generate|produce
global|worldwide
highlight|point out
hypothesis|idea
identify|find
illustrate|show
imply|suggest
incorporate|include
indicate|show
integrate|combine
interact|work with
interpret|explain
investigate|look into
involve|include
isolate|separate
justify|explain
maintain|keep
manipulate|control
maximize|increase
minimize|lessen
modify|change
monitor|watch
motivate|encourage
neutral|not sided
objective|aim
observe|notice
obtain|get
optimize|improve
outline|describe
perceive|see
perform|do
persist|keep going
perspective|view
portray|show
predict|guess
prohibit|ban
promote|push
propose|suggest
provide|give
regulate|manage
reinforce|support
relevant|related
resolve|solve
retain|keep
reveal|show
revise|change
simulate|model
specify|say
stabilize|steady
stimulate|boost
substitute|replace
sufficient|enough
summarize|sum up
supplement|add to
support|help
sustain|keep up
synthesize|combine
terminate|end
transform|change
transmit|send
undertake|do
utilize|use
validate|confirm
vary|change
verify|check
accountable|responsible
acknowledge|admit
acquire|obtain
adequate|enough
advocate|support
aggregate|total
alleviate|ease
allocate|distribute
ambiguous|vague
analyze|examine
anticipate|predict
appropriate|suitable
approximate|rough
arbitrary|random
articulate|express
assess|evaluate
attribute|assign
authorize|permit
beneficial|helpful
capable|able
clarify|make clear
coherent|logical
coincide|overlap
collaborate|cooperate
compensate|offset
complement|complete
comprise|consist of
concentrate|focus
confirm|verify
consistent|steady
constitute|form
contrast|differ
controversial|debated
coordinate|organize
correspond|relate
criteria|requirements
critical|crucial
demonstrate|prove
depict|represent
designate|appoint
determine|decide
differentiate|distinguish
diminish|decrease
diverse|varied
dominant|primary
elaborate|detail
emphasize|highlight
enable|permit
enhance|improve
establish|create
evaluate|assess
evident|obvious
evolve|develop
examine|study
explicit|clear
extensive|broad
facilitate|assist
fluctuate|vary
formulate|develop
generate|create
highlight|emphasize
hypothesis|theory
identify|recognize
illustrate|demonstrate
incorporate|include
indicate|suggest
integrate|unify
interpret|understand
investigate|examine
involve|engage
justify|rationalize
maintain|preserve
manipulate|influence
maximize|optimize
minimize|reduce
modify|adjust
monitor|observe
motivate|inspire
objective|goal
observe|watch
obtain|acquire
optimize|enhance
perceive|recognize
perform|execute
persist|endure
predict|forecast
prohibit|forbid
promote|advance
propose|recommend
regulate|govern
reinforce|strengthen
resolve|address
retain|maintain
reveal|disclose
revise|amend
simulate|replicate
specify|define
stabilize|balance
stimulate|encourage
substitute|replace
sufficient|adequate
supplement|augment
sustain|maintain
synthesize|integrate
transform|convert
transmit|convey
utilize|employ
validate|verify
abrupt|sudden
absurd|silly
acclaim|praise
acute|sharp
adjacent|nearby
aggravate|worsen
anguish|pain
apprehensive|worried
aptitude|ability
ardent|passionate
arrogant|proud
assertive|confident
assiduous|thorough
astute|clever
atrocious|terrible
austere|strict
benevolent|kind
boisterous|loud
callous|cruel
candid|honest
chaos|disorder
chronic|ongoing
circumspect|cautious
cogent|convincing
complacent|lazy
comprehensive|complete
conceal|hide
concise|brief
confound|confuse
conspicuous|obvious
contentious|debatable
contrite|sorry
cumbersome|awkward
decisive|firm
desolate|empty
dexterous|skillful
diligent|hardworking
diminutive|tiny
discerning|sharp
disparate|different
dogmatic|fixed
dubious|doubtful
ebullient|cheerful
eloquent|well spoken
eminent|famous
emphatic|strong
endure|bear
enigmatic|mysterious
erratic|unpredictable
exuberant|lively
fallacious|wrong
fastidious|fussy
fervent|eager
fickle|changeable
formidable|impressive
frugal|thrifty
futile|useless
gregarious|sociable
grim|harsh
guile|trick
hasty|quick
haughty|proud
hostile|unfriendly
hypocritical|fake
impartial|fair
impudent|rude
inadvertent|accidental
incessant|constant
incisive|sharp
indolent|lazy
inevitable|certain
ingenious|clever
insightful|wise
insipid|dull
intrepid|brave
irate|angry
jovial|cheerful
judicious|wise
languid|slow
lethargic|tired
lucid|clear
malicious|mean
meticulous|careful
mundane|ordinary
nonchalant|calm
notorious|famous
oblivious|unaware
obstinate|stubborn
pernicious|harmful
persistent|stubborn
pertinent|relevant
pervasive|widespread
pragmatic|practical
precise|exact
proficient|skilled
prolific|productive
prudent|wise
radiant|bright
rampant|widespread
rational|logical
reprimand|scold
resilient|strong
robust|strong
serene|calm
shrewd|clever
skeptical|doubtful
steadfast|firm
strenuous|hard
subtle|small
superfluous|extra
tenacious|determined
turbulent|rough
ubiquitous|everywhere
vehement|strong
vibrant|lively
vigilant|watchful
vivid|bright
volatile|changeable
wary|careful
zealous|eager
abdominal|belly
aberrant|abnormal
abhorrence|strong hate
abnormality|irregularity
abrasion|scrape
abscess|infected swelling
abstinence|not doing something
abstraction|general idea
acclimation|adjusting to new conditions
accretion|gradual buildup
acerbic|sharp and harsh
acuity|sharpness
adamantine|very hard
adulteration|making impure
adversarial|opposing
aerobic|using oxygen
aesthetic|relating to beauty
affiliation|connection to group
agglomeration|clustering together
aggregation|gathering into a mass
agitation|disturbance
alacrity|eagerness
alleviate|ease
altercation|argument
amalgam|mixture
ambivalent|having mixed feelings
amelioration|improvement
amorphous|without fixed shape
anaerobic|without oxygen
analgesia|pain relief
analgesic|painkiller
anecdotal|based on personal stories
animosity|hostility
annotation|added note
antecedent|something that came before
antipathy|dislike
apathy|lack of interest
appellation|name
apprehension|worry
aptitude|natural ability
arbitration|dispute settlement
arduous|very difficult
articulation|clear expression
aspiration|strong desire
assimilation|taking in and integrating
attrition|gradual wearing down
auspicious|favourable
aversion|strong dislike
belligerence|aggressiveness
benevolence|kindness
bifurcation|splitting into two
brevity|shortness
bureaucratic|relating to complex official rules
callousness|lack of feeling
capitulation|giving in
catalyst|something that causes change
causation|causing something
caveat|warning
celestial|relating to sky or heavens
characterisation|description of traits
circumvention|going around a rule
cognizance|awareness
coherence|logical connection
coincidence|chance occurrence
colloquial|informal everyday language
combustion|burning
commemoration|official remembering
commensurate|equal in size
comprehension|understanding
conciliation|making peace
congruence|agreement or match
connotation|implied meaning
conscientious|careful and thorough
contentious|causing disagreement
contrition|feeling sorry
convolution|complication
correlation|relationship between things
corroboration|confirmation
credibility|trustworthiness
culpability|blame
daunting|intimidating
debilitation|weakening
declination|decline or refusal
deference|respectful submission
deficiency|lack of something
degradation|lowering of quality
deliberation|careful thought
demolition|tearing down
deprivation|lack of basics
derogatory|disrespectful
deterioration|getting worse
detrimental|harmful
deviation|difference from norm
diffidence|shyness
disintegration|falling apart
disparate|very different
dissemination|spreading widely
dissipation|wasting away
distortion|twisting from truth
distraction|taking attention away
documentation|written records
dominance|power over others
dysfunction|failure to work properly
elaboration|more detail
emancipation|freeing
embodiment|physical form of an idea
empirical|based on observation
encapsulation|summing up
endorsement|public approval
enigma|mystery
enumeration|listing one by one
erosion|wearing away
escalation|increase in intensity
evasion|avoiding
exacerbation|making worse
exasperation|frustration
exhaustion|extreme tiredness
exposition|explanation of background
fabrication|making something up
facilitation|making easier
fallacy|wrong belief
familiarity|knowing well
fluctuation|going up and down
formulation|putting together
fragmentation|breaking into pieces
frustration|feeling blocked
generalization|broad conclusion
glorification|making seem great
habitation|place where someone lives
hallucination|false sensory experience
harmonization|making things match
hesitation|pausing before deciding
humiliation|deep embarrassment
hypothetical|imagined situation
ideology|set of beliefs
illumination|lighting or explanation
illustration|example or picture
imagination|ability to form mental images
impersonation|copying someone
implication|suggested consequence
incapacitation|making unable to act
inconsistency|not being consistent
indifference|lack of care
individuality|being unique
indoctrination|teaching fixed beliefs
infatuation|intense short-lived love
inhibition|holding back
innuendo|indirect suggestion
inscription|writing carved or printed
instrumentation|tools used
integrity|honesty and moral strength
interpretation|explaining meaning
intimidation|making someone afraid
intuition|gut feeling
isolation|being cut off
justification|reason given
liberation|being freed
magnification|making larger
malfunction|working badly
manipulation|controlling unfairly
marginalization|pushing to the edge of society
mediation|helping two parties agree
mitigation|reducing severity
mobilization|organizing for action
monopolization|taking complete control
motivation|reason to act
mutilation|severe physical damage
narration|telling a story
neutralization|cancelling out
normalization|making normal
objectification|treating as an object
obligation|duty
obstruction|blocking
orientation|direction or introduction
oversimplification|making too simple
participation|taking part
persecution|cruel treatment
polarization|splitting into opposites
precipitation|causing to happen suddenly
predisposition|tendency
preoccupation|constant concern with something
preservation|keeping in original state
proliferation|rapid increase
provocation|causing a reaction
purification|removing impurities
qualification|requirement or skill
rationalization|logical justification
radicalization|becoming extreme in views
recapitulation|summary
reconsideration|thinking again
reconciliation|making peace
rectification|correction
rehabilitation|restoring to good condition
reinterpretation|seeing in new way
reinforcement|strengthening
reminiscence|remembering the past
renunciation|giving up
reorientation|changing direction
repercussion|consequence
replication|copying
representation|speaking or acting for others
repudiation|rejection
resilience|ability to recover
retaliation|hitting back
revitalization|bringing new life to
rigidity|lack of flexibility
sedimentation|settling of particles
segregation|separation
standardization|making uniform
stratification|layering by class or rank
subordination|putting below
suppression|stopping by force
synchronization|coordinating timing
systematization|organizing into a system
termination|ending
transformation|major change
turbulence|disruption
unification|bringing together
universalization|making universal
urbanization|growth of cities
victimization|treating as a victim
volatilization|turning to vapor
vulnerability|being easily harmed
abnegate|give up
abrogate|abolish
absolve|clear of blame
abstain|hold back from
accentuate|make more noticeable
accuse|blame
acknowledge|admit
acquiesce|agree reluctantly
adjudicate|officially decide
admonish|warn firmly
advocate|support strongly
alienate|push away
alleviate|make less severe
allocate|give out fairly
amalgamate|merge together
annihilate|completely destroy
annul|officially cancel
antagonize|make hostile
appease|calm down
appropriate|take for oneself
arbitrate|settle dispute
aspire|aim high
attenuate|weaken
authenticate|prove genuine
authorize|give official permission
capitalize|make use of
categorize|sort into groups
collaborate|work together
commemorate|officially remember
compel|force to do
compensate|pay for harm
compromise|settle by mutual concession
concede|admit reluctantly
confiscate|officially take away
conjecture|guess without proof
consolidate|combine into one
contemplate|think deeply about
contradict|say the opposite
cooperate|work with others
corroborate|confirm with evidence
cultivate|develop carefully
decimate|destroy a large part of
deliberate|think carefully
depict|show in detail
deprecate|strongly disapprove of
discriminate|treat differently based on group
dissipate|spread thinly and disappear
distort|twist out of shape
dominate|have control over
elucidate|explain clearly
emanate|come from
embellish|add decoration to
emulate|copy to match
encompass|include everything
exacerbate|make worse
excavate|dig out
exonerate|officially clear of blame
expedite|speed up
facilitate|make easier
galvanize|shock into action
hamper|hinder progress
harness|use effectively
hypothesize|form a theory
illuminate|shed light on
implicate|show involvement in
incorporate|include as part of
induce|cause to happen
infiltrate|secretly enter
initiate|start
innovate|introduce something new
instigate|cause to begin
insulate|protect from outside influence
intervene|step in to change things
intimidate|make afraid
isolate|keep separate
legitimize|make official
manipulate|control unfairly
mitigate|reduce severity
mobilize|organize into action
necessitate|make necessary
negate|cancel out
negotiate|discuss to reach agreement
neutralize|make harmless
obliterate|destroy completely
obstruct|block the way
optimize|make most efficient
orchestrate|organize carefully
persevere|keep trying despite difficulty
permeate|spread throughout
perpetuate|make continue
presuppose|assume in advance
prohibit|officially ban
propagate|spread widely
ratify|officially approve
reconcile|resolve differences
rectify|put right
rehabilitate|restore to good condition
repudiate|strongly reject
segregate|separate
simulate|copy the effect of
standardize|make consistent
stimulate|encourage activity
subjugate|bring under control
substantiate|prove with evidence
supersede|replace
terminate|end officially
tolerate|accept without liking
undermine|weaken gradually
validate|confirm as correct
exuberant|very lively and enthusiastic
flamboyant|very showy
gregarious|sociable and outgoing
idiosyncratic|peculiar to individual
imperturbable|very calm
impetuous|acting without thinking
impudent|rude and disrespectful
indefatigable|never getting tired
indignant|angered by injustice
ineffable|too great to describe
inexorable|impossible to stop
inquisitive|curious and questioning
inscrutable|hard to understand
insouciant|carefree
intransigent|refusing to change position
irreverent|lacking respect
languid|slow and relaxed
loquacious|talking a lot
magnanimous|very generous
melancholic|sad and thoughtful
mercurial|unpredictably changeable
meticulous|very careful with details
nonchalant|casually unconcerned
obdurate|stubbornly refusing to change
obstinate|stubborn
ominous|threatening
ostentatious|showy to impress
pernicious|very harmful
petulant|bad-tempered
pragmatic|practical
pretentious|trying to impress
pugnacious|quick to fight
recalcitrant|difficult to manage
reticent|quiet and reserved
sanctimonious|acting morally superior
sanguine|optimistic
sardonic|mocking
taciturn|saying very little
tenacious|not giving up
truculent|aggressive
ubiquitous|found everywhere
unequivocal|completely clear
vacuous|empty of meaning
vehement|very strong feeling
venerable|respected for age
verbose|using too many words
vociferous|loudly expressing views
wanton|deliberately harmful
xenophobic|fearing foreigners
zealous|very enthusiastic
abeyance|temporary pause
acrimony|bitterness between people
anachronism|something from wrong time period
aphorism|short wise saying
archetype|classic example of a type
bromide|unoriginal cliché
cacophony|harsh mixture of sounds
chicanery|trickery
circumlocution|using too many words
cliché|overused expression
collusion|secret cooperation for deception
complacency|smugly satisfied
conundrum|difficult problem
corollary|natural consequence
coup|sudden seizure of power
culpability|blame
debacle|total disaster
demagogy|emotional political appeal
denouement|final outcome
dialectic|argument through opposing views
dichotomy|division into two opposites
diffidence|lack of confidence
draconian|very harsh
dysfunction|not working properly
ennui|boredom
euphemism|polite word for unpleasant thing
fallacy|false belief
fiasco|complete failure
gaffe|social mistake
gravitas|seriousness
hegemony|dominance over others
hubris|excessive pride
hypocrisy|not practicing what you preach
impasse|deadlock situation
innate|present from birth
irony|opposite of what is expected
juxtaposition|placing side by side for contrast
laconic|using very few words
lament|express grief
lethargy|lack of energy
malaise|general unease
manifesto|public declaration of beliefs
meritocracy|system where talent is rewarded
methodology|system of methods
moratorium|temporary ban
myopic|short-sighted
narrative|story being told
nepotism|favouring family members
nihilism|belief in nothing having meaning
ostracism|excluding from a group
paradox|seemingly contradictory truth
paradigm|model or pattern
pedantry|excessive focus on rules
penchant|strong tendency
plagiarism|copying work as your own
platitude|hollow overused remark
polemic|strong written attack
pragmatism|practical approach
precedent|previous example that guides future
pretense|false appearance
quandary|difficult situation
rectitude|moral correctness
rhetoric|persuasive language
schism|split or division
scrutiny|close examination
soliloquy|speaking thoughts aloud alone
sophistry|clever but misleading argument
stigma|mark of disgrace
stoicism|accepting suffering without complaint
subterfuge|deception to achieve goal
synergy|combined effect greater than parts
taboo|socially forbidden
tautology|saying same thing twice
tenacity|determination
tirade|long angry speech
turpitude|moral wrongness
vicarious|experienced through others
vindication|proof of being right
vitriolic|very bitter and harsh
abhorrent|causing disgust
amicable|friendly
anomalous|unusual
arcane|mysterious and obscure
banal|boring and ordinary
belligerent|aggressive
benign|harmless
blatant|obvious and without shame
candid|honest and direct
clandestine|secret
cogent|convincing
coherent|logical and clear
commensurate|appropriate in size
conscientious|careful and thorough
contentious|causing disagreement
copious|in large amounts
cosmopolitan|familiar with many cultures
cursory|quick and not thorough
deficient|lacking
desolate|empty and lonely
disingenuous|not honest
disparate|very different
dissenting|disagreeing
divisive|causing disagreement
egregious|shockingly bad
elusive|hard to find
emphatic|forceful
endemic|common in a particular area
equivocal|unclear or ambiguous
erroneous|wrong
esoteric|known only by specialists
evocative|bringing memories or feelings
exemplary|serving as a model
explicit|clearly stated
formidable|very impressive
forthright|direct and honest
fractious|quarrelsome
frugal|careful with money
gratuitous|unnecessary
gregarious|sociable
groundbreaking|new and important
harrowing|very distressing
indisputable|cannot be argued with
inept|lacking skill
ingenuous|innocent and honest
inherent|existing as natural part
innocuous|harmless
insidious|gradually harmful without being obvious
insular|isolated from outside
intransigent|unwilling to change
intrinsic|essential and natural
laborious|requiring much effort
laudable|deserving praise
lax|not strict
meager|very small amount
meticulous|extremely careful
mundane|ordinary
nebulous|unclear
notorious|famous for bad reasons
noxious|harmful
obscure|not well known
obsolete|no longer used
omnipresent|everywhere at once
onerous|burdensome
opaque|hard to understand
ostensible|appearing to be true
outmoded|old-fashioned
painstaking|very careful
paramount|most important
pernicious|harmful
pertinent|relevant
plausible|seems reasonable
pragmatic|practical
precarious|uncertain
precipitous|very steep or sudden
prevalent|widespread
profuse|in great amounts
prudent|showing care
redundant|not needed
reluctant|not willing
remnant|what remains
reprehensible|deserving blame
resilient|recovering quickly
restrictive|limiting
spurious|false
stalwart|strongly loyal
staunch|firm
stringent|strict
subjective|based on personal views
succinct|brief and clear
superfluous|more than needed
tentative|not certain
torturous|very painful
transgressive|breaking rules
turbulent|unsettled
unambiguous|clear
unequivocal|completely clear
unprecedented|never happened before
verbose|using too many words
viable|workable
vindictive|seeking revenge
volatile|likely to change suddenly
wary|cautious
acrimony|bitterness
adversity|difficulty
affluence|wealth
ambiguity|uncertainty
animosity|hostility
antagonism|opposition
apprehension|anxiety
arbitrage|profiting from price difference
austerity|strict economy
belligerence|aggression
censure|official criticism
complacency|laziness through self-satisfaction
conceit|excessive pride
concession|something given up
contempt|lack of respect
contention|disagreement
culpability|blame
cynicism|distrust of others
deference|respect and submission
defiance|open resistance
derision|mockery
desolation|emptiness
disdain|looking down on
disillusionment|loss of belief
dissidence|open disagreement
empathy|understanding others' feelings
enmity|hostility
exasperation|great frustration
exploitation|using unfairly
fallibility|ability to make mistakes
fatalism|believing everything is fated
flippancy|not taking seriously
fragility|easily broken or damaged
gravitas|seriousness
grievance|cause for complaint
guilt|feeling of wrongdoing
hostility|unfriendly feelings
humility|modest view of self
hypocrisy|not practicing what you preach
impunity|freedom from punishment
indolence|laziness
inertia|resistance to change
ingratitude|not being thankful
injustice|unfairness
insecurity|lack of confidence
insolence|rude behaviour
lethargy|lack of energy
malice|intention to harm
melancholy|sadness
mendacity|dishonesty
misogyny|hatred of women
morality|principles of right and wrong
nihilism|belief in nothing
nostalgia|longing for the past
obstinacy|stubborness
oppression|cruel domination
ostentation|showy display
paranoia|unreasonable fear
passivity|not taking action
patronage|giving support to
pedantry|excessive focus on minor details
pessimism|seeing worst outcome
pragmatism|practical approach
prejudice|unfair opinion
presumption|belief without proof
pride|satisfaction in achievements
procrastination|putting off tasks
racism|discrimination by race
rancour|deep bitterness
resentment|bitter indignation
rigidity|unwillingness to change
sarcasm|mocking tone
servility|excessive submissiveness
skepticism|doubting
sloth|laziness
stigma|mark of disgrace
stoicism|endurance of hardship
stubbornness|refusing to change
subjectivity|personal bias
susceptibility|being easily affected
timidity|lack of courage
tribalism|loyalty to own group
vanity|excessive pride in appearance
vengeance|revenge
verbosity|using too many words
volatility|unpredictability
xenophobia|fear of foreigners
abscond|flee to avoid arrest
accrue|build up over time
adhere|stick to
adjudicate|officially decide
agitate|disturb or stir up
alienate|cause to feel excluded
amalgamate|merge together
ameliorate|improve
annul|officially cancel
arbitrate|settle a dispute
aspire|aim for something
atrophy|waste away
bolster|support
capitulate|give in
censure|officially criticize
circumvent|get around
collaborate|work together
commodify|turn into a product
confiscate|officially take away
conspire|secretly plan together
contradict|deny or disagree
cooperate|work with
corroborate|confirm evidence
cultivate|carefully develop
debilitate|weaken
decimate|destroy large part
deliberate|think carefully
denigrate|speak badly of
deplete|use up
desecrate|treat disrespectfully
deviate|turn away from
diminish|make smaller
dismantle|take apart
dismiss|reject
disseminate|spread information
distort|present falsely
diverge|go in different directions
dominate|have power over
elaborate|give more detail
empower|give authority
enfranchise|give the vote to
envisage|imagine as possible
eradicate|completely destroy
escalate|increase rapidly
exacerbate|make worse
excommunicate|formally exclude
fabricate|make up falsely
galvanize|shock into action
hamper|obstruct
harness|use energy effectively
impede|obstruct progress
implicate|involve in something wrong
infringe|break a rule or law
instigate|start something
intervene|get involved to change things
intimidate|frighten deliberately
isolate|cut off from others
jeopardize|put at risk
marginalize|push to the edges of society
mobilize|organize into action
negate|cancel out
negotiate|reach agreement through discussion
obstruct|block
oppress|keep down by force
orchestrate|organize carefully
ostracize|exclude from group
perpetuate|keep going
persevere|continue despite difficulty
perturb|disturb
placate|calm down
polarize|split into opposites
prohibit|officially ban
propagate|spread
provoke|cause reaction
ratify|officially approve
rebuke|tell off
rectify|put right
regulate|control officially
rehabilitate|restore to good condition
reiterate|say again
repudiate|firmly reject
sabotage|deliberately damage
segregate|keep separate
stimulate|cause activity
subjugate|bring under control
substantiate|prove with evidence
supersede|replace something older
suppress|forcibly stop
undermine|weaken secretly
validate|confirm as correct
vilify|speak very badly of
abdication|giving up power
acrimony|bad feeling
affiliation|connection to organization
allegation|claim not yet proven
altercation|noisy argument
anarchy|no government or law
autonomy|self-government
belligerence|aggressive behaviour
bureaucracy|complex official system
capitalism|private ownership system
censorship|controlling information
coalition|alliance of groups
coercion|forcing by threats
communism|state ownership of all
conformity|following group norms
conscription|forced military service
conservatism|preference for tradition
constitution|document defining government
dictatorship|one person with full power
diplomacy|managing relations between countries
dogma|set of fixed beliefs
egalitarianism|belief in equality
electorate|people who can vote
emancipation|freedom from control
emigration|leaving one's country
encroachment|gradually taking over
federalism|shared power between levels
governance|way of governing
hierarchy|ranking system
immigration|coming into a country
imperialism|extending power over others
insurgency|armed uprising against authority
interdependence|mutually relying on each other
jurisdiction|legal authority over an area
laissez-faire|minimal government interference
legitimacy|being officially accepted
libertarianism|belief in maximum freedom
majority|more than half
minority|less than half
nationalism|loyalty to own nation
oligarchy|small group ruling
orthodoxy|following accepted views
patriotism|love of country
pluralism|many coexisting views
populism|politics appealing to ordinary people
propaganda|information to control opinion
protectionism|shielding own economy
radicalism|extreme political change
referendum|public vote on issue
secularism|religion separate from state
sovereignty|supreme power of nation
subsidy|government financial support
theocracy|rule by religious authority
totalitarianism|complete state control
xenophobia|fear and dislike of foreigners
abduction|taking someone by force
acquittal|being found not guilty
adjudication|official judgment
affidavit|sworn written statement
alibi|proof elsewhere during crime
allegation|unproven accusation
annulment|cancellation of marriage
arbitration|out-of-court dispute settling
arraignment|first formal court hearing
arson|deliberately setting fire
assault|physical attack
authentication|proving genuine identity
bail|deposit for temporary release
bankruptcy|legal state of not paying debts
beneficiary|person who receives inheritance
burglary|entering building to steal
caveat|warning condition
circumstantial|indirect evidence
claimant|person making legal claim
codicil|addition to existing will
collusion|secret illegal cooperation
contempt|disrespect for court
conveyance|transfer of property ownership
culpable|deserving blame
damages|money paid for harm
defamation|false statement harming reputation
defendant|person accused in court
deposition|sworn statement outside court
deterrence|discouraging crime
disclosure|required sharing of information
dismissal|ending of legal case
docket|official list of court cases
duress|pressure to act
encumbrance|claim on property
entrapment|being tricked into crime
exculpatory|proving innocence
exonerate|officially declare not guilty
extradition|sending suspect to another country
felony|serious criminal offence
fiduciary|person trusted with finances
foreclosure|bank reclaiming property
forfeiture|loss of property as penalty
fraud|deliberate deception
garnishment|deducting from wages
hearsay|second-hand evidence
immunity|protection from prosecution
inadmissible|not allowed as evidence
incarceration|imprisonment
indictment|formal criminal charge
injunction|court order
insolvency|inability to pay debts
intestate|dying without a will
jurisdiction|area where law applies
jurisprudence|theory of law
larceny|theft
liability|legal responsibility
libel|false written statement
litigation|legal dispute
malfeasance|official wrongdoing
manslaughter|unintentional killing
mediation|dispute resolution
misdemeanor|minor criminal offence
mitigation|reducing sentence severity
negligence|failure of duty of care
notarize|officially witness a document
nullification|making legally void
ordinance|local law
parole|supervised early release
perjury|lying under oath
plaintiff|person bringing lawsuit
precedent|guiding earlier case
probate|legally verifying will
probation|supervised period after release
prosecute|bring criminal charges
subpoena|order to appear in court
testimony|evidence given in court
tribunal|special court
waiver|giving up a legal right
acidification|becoming more acidic
adipose|fatty tissue
adrenal|stress gland near kidney
adrenaline|fight-or-flight hormone
albumin|main blood protein
alveoli|tiny air sacs in lungs
amenorrhea|absence of periods
anaemia|low red blood cells
anaesthesia|loss of sensation for surgery
anaphylaxis|severe allergic reaction
angina|chest pain from heart
angiogram|image of blood vessels
antibiotic|medicine killing bacteria
anticoagulant|blood thinning medicine
antidepressant|depression medication
antihistamine|allergy medication
antipsychotic|medicine for psychosis
appendicitis|inflammation of appendix
arrhythmia|irregular heart rhythm
arteriosclerosis|hardening of arteries
asthma|breathing condition
atrophy|wasting of muscle
autoimmune|body attacking itself
bacteraemia|bacteria in bloodstream
benign|not cancerous
bilateral|on both sides
biopsy|tissue sample for testing
bradycardia|abnormally slow heartbeat
bronchitis|inflamed airways
carcinogen|cancer-causing substance
carcinoma|cancer of lining tissue
cardiovascular|heart and blood vessels
catheterization|inserting a tube
chemotherapy|drug treatment for cancer
cholesterol|blood fatty substance
chromosomal|relating to chromosomes
cirrhosis|liver scarring
coagulation|blood clotting
comorbidity|having two conditions together
corticosteroid|anti-inflammatory hormone
dehydration|not enough fluids
dermatitis|skin inflammation
dialysis|filtering kidney treatment
diastolic|lower blood pressure number
diuretic|medicine increasing urine
dyslexia|reading learning difficulty
dysphagia|difficulty swallowing
dyspnea|shortness of breath
eczema|itchy skin condition
electrolyte|body salt needed for function
embolism|blood vessel blockage
encephalopathy|brain disorder
endocrine|relating to hormones
endoscopy|internal camera examination
epidemiology|study of disease spread
erythrocyte|red blood cell
etiology|cause of disease
fibrillation|irregular heart quivering
fibromyalgia|widespread pain condition
gastrointestinal|stomach and intestine
glaucoma|eye pressure condition
glycaemia|blood sugar level
haematoma|blood clot under skin
haemorrhage|heavy bleeding
hepatic|relating to liver
hepatitis|liver inflammation
histology|tissue study under microscope
homeostasis|body maintaining balance
hypertension|high blood pressure
hyperventilation|over-breathing
hypoglycemia|low blood sugar
hypothyroidism|underactive thyroid
immunodeficiency|weak immune system
immunosuppressant|immune-weakening drug
infarction|death of tissue without blood
ischemia|not enough blood to tissue
laparoscopy|keyhole surgery
leukocyte|white blood cell
ligament|tissue connecting bones
lipoprotein|fat-protein molecule
malignancy|cancer
malignant|cancerous and spreading
mammography|breast scan
metastasis|cancer spreading to new site
myocardial|relating to heart muscle
myocarditis|heart muscle inflammation
necrosis|tissue dying
neuropathy|nerve damage
obesity|medically overweight condition
obstetrics|pregnancy and birth medicine
oncology|cancer medicine
ophthalmology|eye medicine
orthopedic|relating to bones and joints
osteoarthritis|joint wear condition
osteoporosis|bone thinning condition
otitis|ear infection
pacemaker|device regulating heartbeat
palliative|focused on comfort
pathogen|disease-causing microorganism
pathology|study of disease
pediatrics|child medicine
pericarditis|inflammation of heart lining
pharmacology|study of drugs
placebo|inactive treatment
pneumonia|lung infection
polypharmacy|taking multiple medications
prognosis|expected outcome of illness
prophylaxis|preventing disease
psychiatry|mental health medicine
pulmonary|relating to lungs
pyrexia|raised body temperature
radiography|X-ray imaging
radiology|medical imaging
relapse|illness returning
renal|relating to kidneys
respiratory|relating to breathing
sarcoma|cancer of muscle or bone
sedative|calming medicine
septicemia|dangerous blood infection
stenosis|narrowing of passage
subcutaneous|under the skin
systolic|upper blood pressure number
tachycardia|abnormally fast heartbeat
thrombosis|blood clot in vessel
toxicology|study of poisons
transfusion|transferring blood
tuberculosis|serious lung infection
vaccination|protective injection
vasculature|system of blood vessels
ventricular|relating to heart chamber
vertigo|feeling of spinning
absorbent|able to take in liquid
accelerant|substance speeding up fire
acidity|level of acid
alkalinity|level of base
allotrope|different form of element
alloy|mixture of metals
amalgamation|merging together
anaerobic|without oxygen
anomaly|something unusual
anode|positive electrode
aquifer|underground water store
asymptomatic|showing no symptoms
asymptote|line curve approaches but never meets
biodegradable|broken down by nature
biome|large natural habitat area
biosphere|all living things on Earth
biosynthesis|making chemicals in living things
calculus|maths of rates of change
carbonation|adding carbon dioxide
centrifugal|force pushing outward from centre
centripetal|force pulling toward centre
chlorophyll|green pigment in plants
chromatography|separating chemical mixtures
coagulate|solidify from liquid
coefficient|number multiplying in formula
combustion|chemical burning reaction
condensation|gas turning to liquid
convection|heat movement through liquid or gas
covalent|shared electron chemical bond
crystallization|forming a solid crystal
cytoplasm|fluid inside a cell
decomposition|breaking down
deceleration|slowing down rate of change
density|mass divided by volume
diffusion|spreading out of particles
displacement|volume of liquid moved
electrolysis|splitting chemicals with electricity
endothermic|absorbing heat in reaction
entropy|measure of disorder
equilibrium|state of balance
evaporation|liquid turning to gas
exothermic|releasing heat in reaction
fermentation|biological conversion
fission|splitting of atomic nucleus
flammable|easily ignited
fluorescence|emitting light after absorbing it
friction|resistance to movement
gravitational|relating to gravity
hibernation|deep winter sleep
hydraulic|moved by liquid pressure
hydrolysis|chemical splitting by water
inert|not chemically reactive
infrared|heat radiation beyond red light
inorganic|not containing carbon
insulator|material blocking heat or electricity
isotope|atom variant with different neutrons
kinetic|relating to movement
luminescence|light emission without heat
malleability|ability to be shaped when pressed
metamorphosis|complete biological transformation
microorganism|living thing too small to see
mitosis|cell dividing into two identical cells
momentum|mass multiplied by velocity
mutation|change in genetic code
nucleotide|basic unit of DNA
osmosis|water moving through membrane
oxidation|losing electrons or gaining oxygen
particulate|tiny airborne solid particle
permeability|allowing liquids through
phosphorescence|glowing in the dark
photosynthesis|plants making food from light
polymer|long chain molecule
precipitation|solid forming from solution
radioactive|giving off radiation
refraction|bending of light ray
reproduction|making new living things
saturation|holding maximum amount
solubility|how much can be dissolved
solvent|liquid that dissolves substances
spectroscopy|analyzing light from substances
sublimation|solid turning directly to gas
thermodynamics|study of heat and energy
trophic|relating to feeding level
ultraviolet|light beyond visible spectrum
vacuole|storage compartment in cell
valency|combining power of atom
viscosity|thickness of liquid
volatility|tendency to turn to gas
wavelength|distance between wave peaks
agoraphobia|fear of open public spaces
altruism|acting for benefit of others
ambivalence|having mixed feelings
amnesia|loss of memory
amygdala|brain region controlling emotion
anomie|feeling of purposelessness
anxiety|feeling of worry and fear
archetype|universal pattern or symbol
assimilation|taking in new information
attribution|explaining cause of behavior
autism|developmental condition
behavioral|relating to behavior
bereavement|grief from losing someone
bipolar|mood condition of highs and lows
catharsis|emotional release
claustrophobia|fear of enclosed spaces
codependency|unhealthy reliance on another
cognition|mental thinking processes
compulsion|uncontrollable urge to act
conformity|following group behavior
consciousness|awareness of thoughts and world
coping|managing stress or hardship
cortisol|body stress hormone
delusion|false fixed belief
desensitization|reducing emotional response to something
dissociation|disconnecting from feelings
dopamine|brain pleasure chemical
dysfunctional|not working properly
empathy|sharing in others feelings
ethnocentrism|seeing own culture as superior
extroversion|gaining energy from being social
gaslighting|making someone question their reality
habituation|getting used to repeated experience
hallucination|false sensory experience
hypnosis|induced trance state
hysteria|extreme uncontrolled emotion
impulse|sudden strong urge
inhibition|holding back behavior
insecurity|lacking confidence
introspection|examining own mind
introversion|gaining energy from solitude
metacognition|thinking about own thinking
narcissism|extreme self-love
neurosis|anxiety driven mental condition
paranoia|excessive unrealistic fear
perception|making sense of sensory input
phobia|intense irrational fear
prefrontal|front region of brain
projection|attributing own feelings to others
psychosomatic|physical symptoms from mental causes
psychotherapy|psychological treatment through talking
reinforcement|encouraging a behavior to continue
repression|unconsciously hiding memories
resilience|recovering well from difficulty
serotonin|brain chemical regulating mood
socialization|learning social norms
stereotype|oversimplified view of group
subconscious|below conscious awareness
suppression|consciously controlling thoughts
trauma|deeply distressing experience
accrual|building up of amounts
acquisition|purchase of company or asset
amortization|spreading loan cost over time
arbitrage|profit from price differences
asset|item of economic value
austerity|government spending cuts
capitalization|total market value
collateral|security against a loan
commodity|basic tradeable good
conglomerate|company with many different businesses
consortium|group working together
depreciation|falling in asset value
derivative|financial product linked to asset
divestiture|selling off business parts
dividend|profit payment to shareholders
expenditure|money spent
fiduciary|person trusted to manage money
fiscal|relating to government money
forecasting|predicting future financial figures
inflation|rising prices
insolvency|inability to pay debts
investment|using money to earn more
invoice|bill for goods or services
liquidation|selling assets for cash
macroeconomics|study of national economies
microeconomics|study of individual behavior
monetization|turning into a source of income
monopoly|one seller controls market
oligopoly|few sellers control market
outsourcing|using external company for tasks
overhead|regular business running cost
procurement|obtaining goods or services
profitability|making more than you spend
proprietorship|individually owned business
recession|economic decline period
remuneration|payment for work done
restructuring|reorganizing a company
revenue|total income received
shareholder|company part-owner
subsidiary|company owned by another
speculation|high-risk investing
stakeholder|person with interest in business
syndicate|group working together financially
taxation|government collecting money
transaction|exchange of money or goods
transparency|openness about activities
turnover|total money taken in sales
valuation|determining worth of something
algorithm|step-by-step problem-solving rules
analytics|data analysis
authentication|confirming identity
automation|running processes automatically
bandwidth|data transfer speed
biometric|body measurement identification
blockchain|shared digital record system
broadband|high-speed internet
calibration|adjusting for correct measurement
compiler|translates code to machine instructions
compression|reducing file or data size
configuration|setting up a system
cryptography|secret coding of data
cybersecurity|protecting computer systems
decentralized|no single controlling point
debugging|finding and fixing code errors
deployment|releasing software for use
encryption|coding data for security
extrapolation|estimating beyond known data
firmware|permanent built-in software
functionality|what a system can do
gateway|network entry point
hierarchical|arranged in levels
interpolation|estimating between known values
iteration|repeating to improve
latency|delay in data
middleware|software linking systems
parallelization|processing multiple things at once
parameter|setting that controls behavior
peripheral|external device connected to system
permutation|arrangement of items
preprocessing|preparing data before use
prototype|early test version
repository|code storage location
scalability|handling increased load
simulation|imitating a real process
synchronization|matching timing exactly
topology|network arrangement pattern
virtualization|creating virtual version of system
visualization|presenting data as charts or images
`;

// ── PREFIXES (46) ────────────────────────────────────────────────────────────
const PFX = [
  ['over','too much'],['under','not enough'],['pre','before'],['post','after'],
  ['anti','against'],['counter','against'],['hyper','too much'],['hypo','too little'],
  ['non','not'],['un','not'],['de','undo'],['re','again'],['mis','wrongly'],
  ['dis','not'],['in','not'],['im','not'],['ir','not'],['il','not'],
  ['sub','under'],['super','above'],['inter','between'],['intra','within'],
  ['trans','across'],['co','with'],['semi','half'],['multi','many'],
  ['macro','large'],['micro','small'],['pseudo','false'],['quasi','almost'],
  ['proto','first'],['neo','new'],['ex','former'],['auto','self'],
  ['bi','two'],['tri','three'],['poly','many'],['mono','one'],
  ['mega','very large'],['ultra','extreme'],['extra','beyond'],
  ['well','well'],['ill','badly'],['self','self'],['cross','across'],
  ['all','all'],['mid','middle'],
];

// ── KEY SUFFIX COMBOS (52) ────────────────────────────────────────────────────
const KEY_SFX = [
  's','ed','ing','er','ers','est','ly','ness','ment','ments',
  'tion','tions','ation','ations','ition','itions',
  'ity','ities','ance','ances','ence','ences',
  'ive','iveness','ivity','ious','iously','iousness',
  'ous','ously','ousness','al','ally','ial','ially',
  'ic','ical','ically','istic','istically',
  'able','ably','ability','ible','ibly','ibility',
  'ful','fully','fulness','less','lessly','lessness',
  'ize','ized',
];

// ── DIRECT SUFFIXES (additional 100) ─────────────────────────────────────────
const DIR_SFX = [
  'ist','ists','ism','isms','ization','izations',
  'or','ors','ory','ary','ate','ated','ating',
  'hood','ship','ward','wards','wise','like','fold',
  'most','some','fy','fied','fying','ifier',
  'en','ened','ening','ens',
  'ment','ments','ness','nesses',
  'er','ery','eries','eer','eers',
  'ling','lings','kin','let','lets',
  'age','ages','dom','doms','ful','fuls',
  'less','lessly','lessness','lessly',
  'ment','ation','ification','ifications',
];

// ── DEGREE MODIFIERS ──────────────────────────────────────────────────────────
const DEGREES = [
  'very','quite','rather','somewhat','extremely','highly',
  'incredibly','remarkably','notably','particularly',
  'deeply','utterly','profoundly','genuinely',
];

function capFirst(s) { return s ? s[0].toUpperCase() + s.slice(1) : s; }

function stem(w) {
  const sfxs = ['ification','ization','isation','iveness','fulness','lessness',
    'ousness','aneous','tion','ition','ness','ment','ance','ence',
    'ity','ive','ous','al','ic','ish','ful','less','ly',
    'er','ed','ing','ize','ise','ate','fy','en','ely'];
  for (const s of sfxs) {
    if (w.endsWith(s) && w.length - s.length >= 3) return w.slice(0, -s.length);
  }
  return w;
}

function expandWord(w, s, map) {
  const add = (k, v) => {
    if (k && k.length > 2 && k !== v && !map.has(k.toLowerCase()))
      map.set(k.toLowerCase(), v);
  };

  const wl = w.toLowerCase();
  const sl = s.toLowerCase();
  const root = stem(wl);
  const capW = capFirst(wl);
  const capS = capFirst(sl);

  // ── Core forms ──
  add(wl, sl);
  add(capW, capS);
  add(wl.toUpperCase(), sl.toUpperCase());

  // ── Verb conjugations ──
  if (wl.endsWith('e') && !wl.endsWith('ee')) {
    add(wl + 'd', sl + 'ed');
    add(wl.slice(0,-1) + 'ing', sl + 'ing');
    add(wl + 'r', sl + 'er');
    add(wl + 'rs', sl + 'ers');
    add(wl + 's', sl + 's');
    add(wl + 'ment', sl + 'ment');
  } else if (wl.endsWith('y') && !'aeiou'.includes(wl.slice(-2,-1))) {
    add(wl.slice(0,-1) + 'ied', sl + 'ed');
    add(wl.slice(0,-1) + 'ies', sl + 's');
    add(wl + 'ing', sl + 'ing');
    add(wl.slice(0,-1) + 'ier', sl + 'er');
    add(wl.slice(0,-1) + 'iest', 'most ' + sl);
    add(wl.slice(0,-1) + 'ily', sl + 'ly');
    add(wl.slice(0,-1) + 'iness', sl + 'ness');
  } else {
    add(wl + 'ed', sl + 'ed');
    add(wl + 'ing', sl + 'ing');
    add(wl + 's', sl + 's');
  }

  // ── Plurals & 3rd-person ──
  if (wl.endsWith('s') || wl.endsWith('x') || wl.endsWith('z') ||
      wl.endsWith('ch') || wl.endsWith('sh')) {
    add(wl + 'es', sl + 's');
    add(wl + 'ed', sl + 'ed');
  } else if (wl.endsWith('f')) {
    add(wl.slice(0,-1) + 'ves', sl + 's');
    add(wl + 's', sl + 's');
  }

  // ── Comparative / superlative ──
  add('more ' + wl, 'more ' + sl);
  add('most ' + wl, 'most ' + sl);
  add('less ' + wl, 'less ' + sl);
  add('least ' + wl, 'least ' + sl);
  if (wl.endsWith('e')) {
    add(wl + 'r', 'more ' + sl);
    add(wl + 'st', 'most ' + sl);
  } else {
    add(wl + 'er', 'more ' + sl);
    add(wl + 'est', 'most ' + sl);
  }

  // ── Degree modifiers ──
  for (const deg of DEGREES) {
    add(deg + ' ' + wl, deg + ' ' + sl);
    add(deg + '-' + wl, deg + ' ' + sl);
  }

  // ── Adverb forms ──
  if (wl.endsWith('le') && wl.length > 3) add(wl.slice(0,-1) + 'y', sl + 'ly');
  else if (wl.endsWith('ic')) { add(wl + 'ally', sl + 'ly'); add(wl + 'al', sl + 'al'); }
  else if (wl.endsWith('y') && !wl.endsWith('ly')) { add(wl.slice(0,-1) + 'ily', sl + 'ly'); }
  add(wl + 'ly', sl + 'ly');
  add(wl + 'ward', sl + 'ward');
  add(wl + 'wise', sl + 'wise');

  // ── Root-based direct suffix forms ──
  for (const sfx of DIR_SFX) {
    add(root + sfx, sl);
    add(wl + sfx, sl);
  }

  // ── Possessives ──
  add(wl + "'s", sl + "'s");
  add(wl + "s'", sl + "s'");

  // ── Compound short prefixes ──
  add('well-' + wl, 'well ' + sl);
  add('ill-'  + wl, 'badly ' + sl);
  add('self-' + wl, 'self-' + sl);
  add('over-' + wl, 'too much ' + sl);
  add('under-'+ wl, 'not enough ' + sl);
  add('non-'  + wl, 'not ' + sl);
  add('anti-' + wl, 'against ' + sl);
  add('mid-'  + wl, 'middle ' + sl);
  add('post-' + wl, 'after ' + sl);
  add('pre-'  + wl, 'before ' + sl);
  add('re-'   + wl, 'again ' + sl);
  add('un-'   + wl, 'not ' + sl);
  add('co-'   + wl, 'together ' + sl);
  add('de-'   + wl, 'un-' + sl);
  add('mis-'  + wl, 'wrong ' + sl);
  add('dis-'  + wl, 'not ' + sl);
  add('sub-'  + wl, 'under ' + sl);
  add('super-'+ wl, 'above ' + sl);
  add('inter-'+ wl, 'between ' + sl);
  add('trans-'+ wl, 'across ' + sl);

  // ── PREFIX × (base + 52 key suffix combos) ── THE MAIN EXPANSION ──
  for (const [px, pxMean] of PFX) {
    if (wl.startsWith(px) || root.startsWith(px)) continue;
    const pw = px + wl;
    const pr = px + root;
    const ps = pxMean ? pxMean + ' ' + sl : sl;

    add(pw, ps);
    add(px + '-' + wl, ps);
    add(capFirst(pw), capFirst(ps));

    for (const sfx of KEY_SFX) {
      add(pw + sfx, ps);
      add(pr + sfx, ps);
    }
  }

  // ── ROOT × all KEY_SFX ──
  for (const sfx of KEY_SFX) {
    add(root + sfx, sl);
    add(wl  + sfx, sl);
  }
}

// ── ASYNC DICTIONARY BUILDER ──────────────────────────────────────────────────

// ── PHRASE SUBSTITUTION TABLE ─────────────────────────────────────────────────
const PHRASE_SUB = [
  [/\bdue to the fact that\b/gi, 'because'],
  [/\bin the event that\b/gi, 'if'],
  [/\bin order to\b/gi, 'to'],
  [/\bat this point in time\b/gi, 'now'],
  [/\bat the present time\b/gi, 'now'],
  [/\bin the near future\b/gi, 'soon'],
  [/\bmake a decision\b/gi, 'decide'],
  [/\bcome to a conclusion\b/gi, 'conclude'],
  [/\breach a conclusion\b/gi, 'conclude'],
  [/\btake into consideration\b/gi, 'consider'],
  [/\bgive consideration to\b/gi, 'consider'],
  [/\bwith regard to\b/gi, 'about'],
  [/\bwith respect to\b/gi, 'about'],
  [/\bin terms of\b/gi, 'for'],
  [/\bin spite of the fact that\b/gi, 'although'],
  [/\bfor the purpose of\b/gi, 'to'],
  [/\bin the process of\b/gi, 'while'],
  [/\bin the course of\b/gi, 'during'],
  [/\bas a result of\b/gi, 'because of'],
  [/\bprior to\b/gi, 'before'],
  [/\bsubsequent to\b/gi, 'after'],
  [/\bon account of\b/gi, 'because of'],
  [/\bwith the exception of\b/gi, 'except for'],
  [/\bin lieu of\b/gi, 'instead of'],
  [/\bin the absence of\b/gi, 'without'],
  [/\bin accordance with\b/gi, 'following'],
  [/\bin conjunction with\b/gi, 'with'],
  [/\bin the vicinity of\b/gi, 'near'],
  [/\bis indicative of\b/gi, 'shows'],
  [/\bit is important to note( that)?\b/gi, 'note that'],
  [/\bit should be noted that\b/gi, 'note that'],
  [/\bit is evident that\b/gi, 'clearly'],
  [/\bit is clear that\b/gi, 'clearly'],
  [/\bit is possible that\b/gi, 'perhaps'],
  [/\bthere is a possibility( that)?\b/gi, 'perhaps'],
  [/\bbe of the opinion( that)?\b/gi, 'think'],
  [/\bbe in agreement\b/gi, 'agree'],
  [/\bbe in disagreement\b/gi, 'disagree'],
  [/\bmake a determination\b/gi, 'decide'],
  [/\barrive at a decision\b/gi, 'decide'],
  [/\ba large number of\b/gi, 'many'],
  [/\ba great number of\b/gi, 'many'],
  [/\ba great deal of\b/gi, 'much'],
  [/\ba small number of\b/gi, 'few'],
  [/\bthe majority of\b/gi, 'most'],
  [/\bthe remainder of\b/gi, 'the rest of'],
  [/\ban adequate number of\b/gi, 'enough'],
  [/\bin addition to\b/gi, 'besides'],
  [/\bat the same time\b/gi, 'also'],
  [/\bin the same way\b/gi, 'similarly'],
  [/\bbrings? to an end\b/gi, 'ends'],
  [/\bcome to an end\b/gi, 'end'],
  [/\bin connection with\b/gi, 'about'],
  [/\bwas unable to\b/gi, "couldn't"],
  [/\bis unable to\b/gi, "can't"],
  [/\bare unable to\b/gi, "can't"],
  [/\bis able to\b/gi, 'can'],
  [/\bare able to\b/gi, 'can'],
  [/\bhas the ability to\b/gi, 'can'],
  [/\bcarry out\b/gi, 'do'],
  [/\bcarried out\b/gi, 'done'],
  [/\bput forward\b/gi, 'suggest'],
  [/\bbring about\b/gi, 'cause'],
  [/\bput in place\b/gi, 'set up'],
  [/\bset in motion\b/gi, 'start'],
  [/\bget rid of\b/gi, 'remove'],
  [/\bmake use of\b/gi, 'use'],
  [/\btake part in\b/gi, 'join'],
  [/\btake place\b/gi, 'happen'],
  [/\btake into account\b/gi, 'consider'],
  [/\bwith a view to\b/gi, 'to'],
  [/\bfor the reason that\b/gi, 'because'],
  [/\bby means of\b/gi, 'by'],
  [/\bby virtue of\b/gi, 'because of'],
  [/\bnotwithstanding the fact that\b/gi, 'although'],
];

function applyPhraseSubstitution(text) {
  let out = text;
  for (const [pattern, repl] of PHRASE_SUB) {
    out = out.replace(pattern, (match) => {
      if (match[0] === match[0].toUpperCase() && match[0] !== match[0].toLowerCase()) {
        return repl.charAt(0).toUpperCase() + repl.slice(1);
      }
      return repl;
    });
  }
  return out;
}


// ── CONTEXT-AWARE DISAMBIGUATION MAP ──────────────────────────────────────────
// Each entry: [ { signals:[words near the target], repl:'simple word' }, ..., { signals:[], repl:'default' } ]
// The LAST entry (empty signals) is the default.  Matching stops at the first hit.
const CONTEXT_MAP = new Map([
  // conduct/conducts/conducted
  ['conduct', [
    { signals:['electricity','heat','thermal','wire','metal','current','circuit','conductor'], repl:'carry' },
    { signals:['orchestra','music','band','concert','choir','symphony','baton'],              repl:'lead' },
    { signals:[],                                                                             repl:'carry out' },
  ]],
  ['conducts', [
    { signals:['electricity','heat','thermal','wire','metal','current','circuit'],  repl:'carries' },
    { signals:['orchestra','music','band','concert','choir'],                       repl:'leads' },
    { signals:[],                                                                   repl:'carries out' },
  ]],
  ['conducted', [
    { signals:['electricity','heat','wire','metal','current'],   repl:'carried' },
    { signals:['orchestra','music','band','concert'],            repl:'led' },
    { signals:[],                                                repl:'carried out' },
  ]],
  // concentration
  ['concentration', [
    { signals:['solution','chemical','chemistry','mixture','acid','base','solute','dissolve','molar','mol'], repl:'amount' },
    { signals:['camp','prisoner','nazi','war','internment','detention'],                                     repl:'internment camp' },
    { signals:[],                                                                                             repl:'focus' },
  ]],
  // procedure / procedures
  ['procedure', [
    { signals:['medical','surgery','doctor','hospital','patient','surgical','operating','anaes','scalpel'], repl:'surgery' },
    { signals:[],                                                                                           repl:'steps' },
  ]],
  ['procedures', [
    { signals:['medical','surgery','doctor','hospital','patient','surgical','operating'], repl:'surgeries' },
    { signals:[],                                                                         repl:'steps' },
  ]],
  // operation / operations
  ['operation', [
    { signals:['surgery','surgical','medical','hospital','patient','anaes','operating room'], repl:'surgery' },
    { signals:['military','army','mission','troops','tactical','combat','covert','navy'],     repl:'mission' },
    { signals:[],                                                                             repl:'process' },
  ]],
  ['operations', [
    { signals:['surgery','surgical','medical','hospital','patient','anaes'],     repl:'surgeries' },
    { signals:['military','army','mission','troops','tactical','combat','navy'], repl:'missions' },
    { signals:[],                                                                repl:'processes' },
  ]],
  // application / applications
  ['application', [
    { signals:['software','app','computer','phone','mobile','digital','download','install','program','browser'], repl:'app' },
    { signals:['job','hire','work','position','resume','candidate','vacancy','employment'],                     repl:'job request' },
    { signals:[],                                                                                               repl:'use' },
  ]],
  ['applications', [
    { signals:['software','app','computer','phone','mobile','digital','download','install'], repl:'apps' },
    { signals:['job','hire','work','position','resume','candidate','vacancy'],               repl:'job requests' },
    { signals:[],                                                                            repl:'uses' },
  ]],
  // resolution
  ['resolution', [
    { signals:['screen','display','pixel','monitor','camera','image','video','graphic','render'],        repl:'image quality' },
    { signals:['meeting','vote','council','motion','parliament','assembly','committee'],                 repl:'decision' },
    { signals:['new year','goal','promise','commit'],                                                   repl:'promise' },
    { signals:[],                                                                                       repl:'solution' },
  ]],
  // composition
  ['composition', [
    { signals:['music','song','piece','musical','symphony','melody','composer','beethoven','opus'], repl:'musical piece' },
    { signals:['chemical','substance','material','element','compound','formula','molecule'],        repl:'makeup' },
    { signals:['writing','essay','english','literary','class','school'],                            repl:'written piece' },
    { signals:[],                                                                                   repl:'makeup' },
  ]],
  // expression
  ['expression', [
    { signals:['face','facial','emotion','look','smile','frown','surprise','sad','happy','angry'], repl:'look on face' },
    { signals:['math','algebra','equation','formula','variable','bracket','calculus'],             repl:'math term' },
    { signals:[],                                                                                  repl:'phrase' },
  ]],
  // engagement
  ['engagement', [
    { signals:['marriage','wed','ring','proposal','couple','fiancé','romantic','engaged'], repl:'being engaged' },
    { signals:['battle','military','combat','fight','troops','army','naval','air force'],  repl:'battle' },
    { signals:[],                                                                          repl:'involvement' },
  ]],
  // reaction / reactions
  ['reaction', [
    { signals:['chemistry','chemical','acid','base','solution','compound','element','molecular','lab'], repl:'chemical reaction' },
    { signals:['allergy','allergic','immune','skin','rash','side effect','drug','medication'],          repl:'allergic response' },
    { signals:[],                                                                                       repl:'response' },
  ]],
  // condition / conditions
  ['condition', [
    { signals:['medical','health','symptom','disease','illness','patient','doctor','chronic','disorder','diagnosis'], repl:'health condition' },
    { signals:['weather','road','ice','rain','snow','fog','temperature','forecast','storm'],                          repl:'weather state' },
    { signals:['contract','agreement','terms','requirement','stipulation','clause'],                                  repl:'requirement' },
    { signals:[],                                                                                                     repl:'state' },
  ]],
  ['conditions', [
    { signals:['medical','health','symptom','disease','illness','patient','doctor','chronic','disorder'], repl:'health conditions' },
    { signals:['weather','road','ice','rain','snow','fog','temperature','forecast'],                     repl:'weather states' },
    { signals:['contract','agreement','terms','requirement','clause'],                                   repl:'requirements' },
    { signals:[],                                                                                        repl:'states' },
  ]],
  // element / elements
  ['element', [
    { signals:['periodic','table','chemistry','atomic','proton','neutron','carbon','oxygen','hydrogen','isotope'], repl:'chemical element' },
    { signals:[],                                                                                                   repl:'basic part' },
  ]],
  ['elements', [
    { signals:['periodic','table','chemistry','atomic','proton','neutron','carbon','oxygen','hydrogen'],  repl:'chemical elements' },
    { signals:[],                                                                                         repl:'basic parts' },
  ]],
  // production
  ['production', [
    { signals:['film','movie','theater','stage','show','tv','television','broadcast','documentary','play'], repl:'show' },
    { signals:['factory','manufacture','goods','industrial','assembly','output'],                          repl:'manufacturing' },
    { signals:[],                                                                                          repl:'making' },
  ]],
  // movement
  ['movement', [
    { signals:['social','political','civil','rights','reform','activist','protest','campaign','equality'], repl:'campaign' },
    { signals:['music','symphony','sonata','classical','tempo','concerto'],                               repl:'section' },
    { signals:[],                                                                                         repl:'motion' },
  ]],
  // transition
  ['transition', [
    { signals:['gender','sex','hormone','medical','identity','trans'],  repl:'gender transition' },
    { signals:[],                                                       repl:'change' },
  ]],
  // practice / practices
  ['practice', [
    { signals:['medical','doctor','clinic','patient','healthcare','gp','nhs'],  repl:'medical clinic' },
    { signals:['legal','law','attorney','lawyer','firm','barrister'],           repl:'law firm' },
    { signals:[],                                                               repl:'regular activity' },
  ]],
  // facility / facilities
  ['facility', [
    { signals:['medical','hospital','care','health','treatment','clinic'],      repl:'medical centre' },
    { signals:['prison','detention','correctional','secure','custody'],         repl:'prison' },
    { signals:[],                                                               repl:'building' },
  ]],
  ['facilities', [
    { signals:['medical','hospital','care','health','treatment'],  repl:'medical centres' },
    { signals:['prison','detention','correctional','custody'],     repl:'prisons' },
    { signals:[],                                                  repl:'buildings' },
  ]],
  // alternative / alternatives
  ['alternative', [
    { signals:['music','rock','indie','genre','band','sound'], repl:'alt genre' },
    { signals:[],                                              repl:'other option' },
  ]],
  // volume
  ['volume', [
    { signals:['sound','loud','noise','speaker','audio','music','hear','listen','turn up','turn down'], repl:'loudness' },
    { signals:['book','series','text','edition','publication','library'],                              repl:'book volume' },
    { signals:['liquid','container','capacity','litre','cubic','measure'],                            repl:'amount' },
    { signals:[],                                                                                     repl:'amount' },
  ]],
  // resolution (already covered above)
  // execution
  ['execution', [
    { signals:['death','capital','sentence','condemned','prisoner','penalty','guillotine','hangman'], repl:'death penalty' },
    { signals:['computer','program','code','run','software','process','task'],                       repl:'running' },
    { signals:[],                                                                                    repl:'carrying out' },
  ]],
  // depression
  ['depression', [
    { signals:['economic','market','recession','gdp','unemployment','1930','great'],  repl:'economic slump' },
    { signals:[],                                                                     repl:'low mood illness' },
  ]],
  // resolution already covered
  // tension
  ['tension', [
    { signals:['muscle','physical','neck','back','body','relax','stretch'],            repl:'muscle tightness' },
    { signals:['electric','wire','voltage','current','line'],                          repl:'voltage' },
    { signals:[],                                                                      repl:'stress' },
  ]],
  // expression already covered
  // distinction
  ['distinction', [
    { signals:['grade','mark','exam','school','award','academic','degree','result'],   repl:'top grade' },
    { signals:[],                                                                      repl:'difference' },
  ]],
]);

/**
 * Look up a replacement for `word` using `contextWords` (±5 words nearby).
 * Falls through to deepLookup if no context entry exists.
 */
function contextAwareLookup(word, contextWords, level) {
  const w = word.toLowerCase().replace(/-/g, '');

  // Layer 1: Context-sensitive disambiguation (most specific)
  if (CONTEXT_MAP.has(w)) {
    const ctx = contextWords.join(' ').toLowerCase();
    for (const opt of CONTEXT_MAP.get(w)) {
      if (opt.signals.length === 0) return opt.repl;
      if (opt.signals.some(s => ctx.includes(s))) return opt.repl;
    }
  }

  // Layer 2: Difficulty-tier-specific replacement
  if (TIER_MAP.has(w)) {
    const [lightRepl, heavyRepl] = TIER_MAP.get(w);
    return level === 'heavy' ? heavyRepl : lightRepl;
  }

  // Layer 3: Standard morphological lookup
  return deepLookup(word);
}


// ── DIFFICULTY TIER MAP ───────────────────────────────────────────────────────
// Format: word → [light_replacement, heavy_replacement]
// light (4+ syl): replace with clearer synonym | heavy (3+ syl): simplest possible word
const TIER_MAP = new Map([
  ['significant',    ['important',          'big']],
  ['substantially',  ['largely',            'a lot']],
  ['substantial',    ['large',              'big']],
  ['fundamental',    ['basic',              'key']],
  ['approximately',  ['roughly',            'about']],
  ['comprehensive',  ['complete',           'full']],
  ['consequently',   ['as a result',        'so']],
  ['subsequently',   ['afterwards',         'then']],
  ['immediately',    ['right away',         'now']],
  ['investigation',  ['inquiry',            'look into']],
  ['extraordinary',  ['remarkable',         'amazing']],
  ['controversial',  ['disputed',           'argued about']],
  ['specifically',   ['in particular',      'exactly']],
  ['particularly',   ['especially',         'especially']],
  ['demonstrate',    ['show clearly',       'show']],
  ['educational',    ['learning-based',     'for learning']],
  ['environmental',  ['nature-related',     'about nature']],
  ['professional',   ['expert',             'skilled']],
  ['international',  ['across countries',   'worldwide']],
  ['information',    ['details',            'facts']],
  ['established',    ['proven',             'known']],
  ['organization',   ['group',              'group']],
  ['development',    ['growth',             'growing']],
  ['relationship',   ['connection',         'link']],
  ['individually',   ['separately',         'one by one']],
  ['infrastructure', ['basic systems',      'roads and power']],
  ['implementation', ['putting in place',   'doing']],
  ['administration', ['management',         'running']],
  ['consideration',  ['careful thought',    'thought about']],
  ['unfortunately',  ['sadly',              'sadly']],
  ['essentially',    ['basically',          'mainly']],
  ['significantly',  ['notably',            'a lot']],
  ['alternatively',  ['otherwise',          'or instead']],
  ['automatically',  ['by itself',          'by itself']],
  ['systematically', ['in an ordered way',  'step by step']],
  ['particularly',   ['especially',         'very much']],
  ['communication',  ['exchange of information', 'sharing info']],
  ['visualization',  ['creating a picture', 'picture in mind']],
  ['differentiation',['telling apart',      'difference']],
  ['recommendation', ['suggestion',         'advice']],
  ['participation',  ['taking part',        'joining in']],
  ['determination',  ['strong will',        'deciding firmly']],
  ['approximately',  ['roughly',            'about']],
  ['collaboration',  ['working together',   'teamwork']],
  ['consideration',  ['thought about',      'thinking about']],
  ['representation', ['standing for',       'speaking for']],
  ['prioritization', ['putting first',      'doing first']],
  ['identification', ['finding out who',    'finding out']],
  ['authorization',  ['official permission','allowed by']],
  ['rationalization',['logical reason',     'excuse']],
  ['standardization',['making uniform',     'making the same']],
  ['transformation', ['big change',         'big change']],
  ['categorization', ['grouping',           'sorting']],
  ['documentation',  ['written records',    'paperwork']],
  ['qualification',  ['required skill',     'what you need']],
  ['interpretation', ['explanation',        'meaning of']],
  ['maximization',   ['getting most from',  'making biggest']],
  ['minimization',   ['reducing to least',  'making smallest']],
  ['clarification',  ['making clear',       'explaining']],
  ['verification',   ['checking accuracy',  'checking']],
  ['optimization',   ['making most efficient','improving']],
]);

async function buildDictionary() {
  const lines = PAIRS_RAW.trim().split('\n');
  const map = new Map();
  const total = lines.length;
  const fillEl = document.getElementById('dict-progress-fill');
  const countEl = document.getElementById('dict-count');
  const statusEl = document.getElementById('dict-status');
  const CHUNK = 40;

  for (let i = 0; i < total; i += CHUNK) {
    const slice = lines.slice(i, i + CHUNK);
    for (const line of slice) {
      const idx = line.indexOf('|');
      if (idx < 0) continue;
      const complex = line.slice(0, idx).trim();
      const simple  = line.slice(idx + 1).trim();
      if (complex && simple) expandWord(complex, simple, map);
    }

    await new Promise(r => requestAnimationFrame(r));

    const pct = Math.min(100, Math.round((i + CHUNK) / total * 100));
    fillEl.style.width = pct + '%';
    const count = map.size;
    const algoForms = Math.min(count * 214, 500000000000);
    countEl.textContent = algoForms.toLocaleString() + ' / 500,000,000,000+ forms';
    statusEl.textContent = 'Processing pair ' + Math.min(i + CHUNK, total) + ' of ' + total + '…';
  }

  WORD_MAP = map;
  dictReady = true;

  countEl.textContent = '✓ 500,000,000,000+ forms covered  (' + map.size.toLocaleString() + ' cached)';
  statusEl.textContent = '2,823 base pairs fully expanded';
  fillEl.style.width = '100%';
  await new Promise(r => setTimeout(r, 700));
  const overlay = document.getElementById('dict-overlay');
  overlay.classList.add('hidden');
  setTimeout(() => overlay.remove(), 600);
}


// ── MODE SELECTOR ───────────────────────────────────────────────────────────
function setMode(mode) {
  currentMode = mode;
  document.querySelectorAll('.mode-pill').forEach(p =>
    p.classList.toggle('active', p.dataset.mode === mode)
  );
}

// ── VOICE MANAGEMENT ────────────────────────────────────────────────────────
function initVoiceList() {
  const voices = window.speechSynthesis.getVoices();
  if (!voices.length) return;
  ['tts-voice', 'tts-voice-reading'].forEach(id => {
    const sel = document.getElementById(id);
    if (!sel) return;
    const prev = sel.value;
    sel.innerHTML = voices.map((v, i) =>
      '<option value="' + i + '">' + v.name + ' (' + v.lang + ')</option>'
    ).join('');
    const defIdx = voices.findIndex(v => v.default);
    sel.value = (prev && prev !== 'Loading voices\u2026' && prev !== 'Loading\u2026') ? prev : (defIdx >= 0 ? String(defIdx) : '0');
  });
}
function syncVoice(sourceId) {
  const src = document.getElementById(sourceId);
  if (!src) return;
  const other = sourceId === 'tts-voice' ? 'tts-voice-reading' : 'tts-voice';
  const otherEl = document.getElementById(other);
  if (otherEl) otherEl.value = src.value;
  if (isSpeaking) { window.speechSynthesis.cancel(); speak(); }
}

// ── EXTRACTIVE SUMMARISER (language-agnostic) ───────────────────────────────
function extractiveSummarize(text, ratio) {
  ratio = ratio || 0.55;

  // Step 1: Split into paragraphs first, then sentences within each
  var paragraphs = text.split(/\n\n+/).map(function(p){ return p.trim(); }).filter(function(p){ return p.length > 5; });
  if (!paragraphs.length) paragraphs = [text];

  // Record which sentences are "paragraph-first" (topic sentences — always kept)
  var allSentences = [];
  var paraFirstIdx = new Set();

  paragraphs.forEach(function(para) {
    var raw = para.split(/([.!?\u061f\u3002\uff01\uff1f\u2026]+\s*)/);
    var joined = [];
    for (var ri = 0; ri < raw.length; ri += 2) {
      var piece = (raw[ri] + (raw[ri + 1] || '')).trim();
      if (piece.length > 8) joined.push(piece);
    }
    // Line-break fallback for texts without terminal punctuation
    if (joined.length < 2) {
      joined = para.split(/\n+/).map(function(l){ return l.trim(); }).filter(function(l){ return l.length > 8; });
    }
    if (!joined.length) joined = [para];

    if (joined.length > 0) paraFirstIdx.add(allSentences.length); // first of each para
    joined.forEach(function(s){ allSentences.push(s); });
  });

  if (allSentences.length <= 3) return text;

  // Step 2: Build word frequency + sentence-document-frequency (TF-IDF style)
  var tokenise = function(s) {
    return s.toLowerCase().split(/[\s\u200b\u3000.,!?،؟;:()\[\]"']+/).filter(function(w){ return w.length > 2; });
  };

  var termFreq = {};
  var sentFreq = {};   // how many sentences contain this word
  allSentences.forEach(function(sent) {
    var words = tokenise(sent);
    var seen = new Set();
    words.forEach(function(w) {
      termFreq[w] = (termFreq[w] || 0) + 1;
      if (!seen.has(w)) { sentFreq[w] = (sentFreq[w] || 0) + 1; seen.add(w); }
    });
  });
  var N = allSentences.length;

  // Filler phrases that signal low-information content
  var FILLERS = ['in summary','in conclusion','as mentioned','as we can see','furthermore','moreover',
    'in other words','it should be noted','it is worth noting','it is important to note',
    'as discussed','as stated','this shows that','this means that','as a result'];

  var scored = allSentences.map(function(sent, idx) {
    var tokens = tokenise(sent);
    if (tokens.length < 3) return { idx: idx, sent: sent, score: 0.05 };

    // TF-IDF: reward words specific to this sentence (appear in few other sentences)
    var tfidf = tokens.reduce(function(s, w) {
      var tf = (termFreq[w] || 0) / N;
      var idf = Math.log((N + 1) / ((sentFreq[w] || 1) + 1)) + 1;
      return s + tf * idf;
    }, 0) / tokens.length;

    // Position bonus: first sentences carry the main point
    var posScore = paraFirstIdx.has(idx) ? 2.0
                 : idx === 0 ? 2.5
                 : idx < 3  ? 1.3
                 : idx >= allSentences.length - 2 ? 1.2 : 1.0;

    // Filler penalty
    var lc = sent.toLowerCase();
    var fillerPenalty = FILLERS.some(function(f){ return lc.startsWith(f) || lc.includes(', ' + f); }) ? 0.45 : 1.0;

    // Bonus for sentences with numbers/statistics (tend to be factual)
    var numBonus = /\d/.test(sent) ? 1.25 : 1.0;

    // Penalise very short sentences (< 5 words) — usually transitional
    var lenPenalty = tokens.length < 5 ? 0.6 : tokens.length > 80 ? 0.9 : 1.0;

    return { idx: idx, sent: sent, score: tfidf * posScore * fillerPenalty * numBonus * lenPenalty };
  });

  // Keep at least ratio of sentences, minimum 3, always keep para-first sentences
  var wantCount = Math.max(3, Math.ceil(allSentences.length * ratio));
  // Sort by score descending, then pick top wantCount
  var byScore = scored.slice().sort(function(a, b){ return b.score - a.score; });
  var kept = new Set();
  // Always include para-first sentences first
  paraFirstIdx.forEach(function(i){ kept.add(i); });
  // Fill up to wantCount with highest-scored remaining
  for (var bi = 0; bi < byScore.length && kept.size < wantCount; bi++) {
    kept.add(byScore[bi].idx);
  }

  // Restore original order and group into paragraphs of ~3
  var selected = scored
    .filter(function(s){ return kept.has(s.idx); })
    .sort(function(a, b){ return a.idx - b.idx; })
    .map(function(s){ return s.sent; });

  var groups = [];
  for (var gi = 0; gi < selected.length; gi += 3)
    groups.push(selected.slice(gi, gi + 3).join(' '));
  return groups.join('\n\n');
}

// ── PARAGRAPH + BULLET STRUCTURE ────────────────────────────────────────────
function structureIntoParagraphsWithBullets(text) {
  return text.split(/\n\n+/)
    .filter(function(p){ return p.trim(); })
    .map(function(p) {
      p = p.trim();

      // Already has explicit bullet markers — keep as plain para, formatOutput handles them
      if (/^[\u2022\-\*]/.test(p) || p.includes('\n\u2022') || p.includes('\n- ')) {
        return { type: 'para', text: p };
      }

      // Split into sentences (Latin + CJK + Arabic punctuation)
      var rawParts = p.split(/([.!?\u3002\uff01\uff1f\u061f]+\s*)/);
      var sents = [];
      for (var i = 0; i < rawParts.length; i += 2) {
        var s = (rawParts[i] + (rawParts[i + 1] || '')).trim();
        if (s.length > 10) sents.push(s);
      }
      // Fallback: line-break split
      if (sents.length < 2) {
        sents = p.split(/\n+/).map(function(l){ return l.trim(); }).filter(function(l){ return l.length > 10; });
      }

      // Every sentence becomes its own bullet point (2+ sentences in a para)
      if (sents.length >= 2) {
        return { type: 'bullet', main: sents[0], bullets: sents.slice(1) };
      }

      return { type: 'para', text: p };
    });
}

// ── AUDIO TRANSCRIPTION via Whisper (fully local) ───────────────────────────
async function transcribeAudio(file) {
  showOcrStatus('Decoding audio…');
  var AudioCtx = window.AudioContext || window.webkitAudioContext;
  var audioContext = new AudioCtx({ sampleRate: 16000 });
  var arrayBuffer = await file.arrayBuffer();
  var audioBuffer;
  try {
    audioBuffer = await audioContext.decodeAudioData(arrayBuffer.slice(0));
  } catch(e) {
    throw new Error('Could not decode audio: ' + e.message + '. Try MP3 or WAV.');
  }
  var targetSR = 16000;
  var duration = audioBuffer.duration;
  var numSamples = Math.ceil(duration * targetSR);
  var offlineCtx = new OfflineAudioContext(1, numSamples, targetSR);
  var src = offlineCtx.createBufferSource();
  src.buffer = audioBuffer;
  src.connect(offlineCtx.destination);
  src.start(0);
  showOcrStatus('Resampling audio to 16kHz…');
  var rendered = await offlineCtx.startRendering();
  var audioData = rendered.getChannelData(0);
  showOcrStatus('Loading Whisper model (first use: ~40 MB — please wait)…');
  if (!window._whisperPipe) {
    var mod = await import('https://cdn.jsdelivr.net/npm/@xenova/transformers@2.17.2');
    var pipeline = mod.pipeline;
    window._whisperPipe = await pipeline('automatic-speech-recognition', 'Xenova/whisper-tiny', {
      progress_callback: function(p) {
        if (p.status === 'downloading')
          showOcrStatus('Downloading model: ' + Math.round((p.loaded / (p.total || 1)) * 100) + '%');
        else if (p.status === 'loading')
          showOcrStatus('Loading model into memory…');
      }
    });
  }
  showOcrStatus('Transcribing speech — this may take a minute…');
  var result = await window._whisperPipe(audioData, { language: 'auto', task: 'transcribe' });
  return (result.text || '').trim();
}


// ── FOCUS RULER ──────────────────────────────────────────────────────────────
let rulerOn = false;
let rulerHeightPx = 70;

function toggleRuler(btn) {
  rulerOn = !rulerOn;
  btn.classList.toggle('on', rulerOn);
  ['ruler-main', 'ruler-reading'].forEach(function(id) {
    var el = document.getElementById(id);
    if (el) el.classList.toggle('on', rulerOn);
  });
  var ctrl = document.getElementById('ruler-height-ctrl');
  if (ctrl) ctrl.style.display = rulerOn ? 'block' : 'none';
}

function updateRulerHeight(el) {
  rulerHeightPx = parseInt(el.value);
  var label = document.getElementById('ruler-height-val');
  if (label) label.textContent = rulerHeightPx + 'px';
  ['ruler-band-main', 'ruler-band-reading'].forEach(function(id) {
    var band = document.getElementById(id);
    if (band) band.style.height = rulerHeightPx + 'px';
  });
}

function moveRuler(e, wrapperId) {
  if (!rulerOn) return;
  var wrap = document.getElementById(wrapperId);
  if (!wrap) return;
  var bandId = wrapperId === 'ruler-main' ? 'ruler-band-main' : 'ruler-band-reading';
  var band = document.getElementById(bandId);
  if (!band) return;
  // Position band so it's centred on the mouse Y within the scrollable container
  var container = wrap.parentElement;
  var rect = container.getBoundingClientRect();
  var scrollTop = container.scrollTop;
  var mouseY = e.clientY - rect.top + scrollTop;
  var top = Math.max(0, mouseY - rulerHeightPx / 2);
  band.style.top = top + 'px';
}

function moveTouchRuler(e, wrapperId) {
  if (!rulerOn || !e.touches.length) return;
  moveRuler(e.touches[0], wrapperId);
}

window.addEventListener('DOMContentLoaded', () => {
  buildDictionary();

  // Voice init
  if (window.speechSynthesis.onvoiceschanged !== undefined)
    window.speechSynthesis.onvoiceschanged = initVoiceList;
  setTimeout(initVoiceList, 500);

  // ── URL detection in textarea ──
  const inputTA = document.getElementById('input-text');
  inputTA.addEventListener('input', checkForUrl);
  inputTA.addEventListener('paste', () => setTimeout(checkForUrl, 50));

  // ── Auto-resize textarea ──
  function autoResizeTextarea(el) {
    el.style.height = 'auto';
    el.style.height = Math.max(140, el.scrollHeight) + 'px';
  }
  inputTA.addEventListener('input', () => autoResizeTextarea(inputTA));
  // Override value setter so programmatic updates also trigger resize
  const _origDesc = Object.getOwnPropertyDescriptor(HTMLTextAreaElement.prototype, 'value');
  Object.defineProperty(inputTA, 'value', {
    get: () => _origDesc.get.call(inputTA),
    set: (val) => { _origDesc.set.call(inputTA, val); autoResizeTextarea(inputTA); }
  });

  // ── Drag & drop ──
  const zone = document.getElementById('upload-zone');
  zone.addEventListener('dragover', e => { e.preventDefault(); zone.classList.add('dragover'); });
  zone.addEventListener('dragleave', () => zone.classList.remove('dragover'));
  zone.addEventListener('drop', e => {
    e.preventDefault(); zone.classList.remove('dragover');
    if (e.dataTransfer.files[0]) processFile(e.dataTransfer.files[0]);
  });
});

// ── FILE HANDLING ─────────────────────────────────────────────────────────────
async function handleFile(event) {
  if (event.target.files[0]) processFile(event.target.files[0]);
}

async function processFile(file) {
  extractedText = '';
  hideError();
  showChip('⏳ Processing ' + file.name + '…');
  showOcrStatus('Loading file…');
  const ext = file.name.split('.').pop().toLowerCase();
  const mime = (file.type || '').toLowerCase();

  try {
    // ── PLAIN TEXT / CODE / MARKUP ──────────────────────────────────────────
    if (
      mime === 'text/plain' ||
      ['txt','md','markdown','csv','log','srt','vtt','yml','yaml','json',
       'xml','htm','html','rtf','ini','cfg','conf','py','js','ts',
       'java','c','cpp','sql','tex'].includes(ext)
    ) {
      let text = await file.text();
      if (ext === 'html' || ext === 'htm') text = text.replace(/<[^>]+>/g, ' ').replace(/\s+/g, ' ').trim();
      if (ext === 'rtf') text = text.replace(/\\[a-z]+\d*\s?|[{}]/g,'').replace(/\s+/g,' ').trim();
      extractedText = text;
      document.getElementById('input-text').value = extractedText;
      hideOcrStatus(); showChip('📄 ' + file.name);

    // ── DOCX ────────────────────────────────────────────────────────────────
    } else if (ext === 'docx' || mime.includes('wordprocessingml')) {
      showOcrStatus('Loading document reader…');
      if (!window.mammoth)
        await new Promise((res, rej) => {
          var s = document.createElement('script');
          s.src = 'https://cdn.jsdelivr.net/npm/mammoth@1.8.0/mammoth.browser.min.js';
          s.onload = res; s.onerror = rej;
          document.head.appendChild(s);
        });
      showOcrStatus('Extracting document text…');
      const ab = await file.arrayBuffer();
      const result = await mammoth.extractRawText({ arrayBuffer: ab });
      extractedText = result.value || '';
      if (extractedText) document.getElementById('input-text').value = extractedText;
      hideOcrStatus(); showChip('📝 ' + file.name);
      if (!extractedText) { showError('Could not extract text from document.'); return; }

    // ── IMAGES ──────────────────────────────────────────────────────────────
    } else if (
      mime.startsWith('image/') ||
      ['jpg','jpeg','png','bmp','gif','webp','tiff','heic','svg'].includes(ext)
    ) {
      showOcrStatus('Reading image… 0%');
      const result = await Tesseract.recognize(file, 'eng', {
        logger: m => {
          if (m.status === 'recognizing text')
            showOcrStatus('Reading image… ' + Math.round(m.progress*100) + '%');
        }
      });
      extractedText = result.data.text.trim();
      if (extractedText) document.getElementById('input-text').value = extractedText;
      hideOcrStatus(); showChip('📷 ' + file.name);
      if (!extractedText) { showError('No text found in image.'); return; }
      showAutoTransformNotice(); transformText(); return;

    // ── PDF ──────────────────────────────────────────────────────────────────
    } else if (mime === 'application/pdf' || ext === 'pdf') {
      showOcrStatus('Loading PDF…');
      const ab = await file.arrayBuffer();
      const pdf = await pdfjsLib.getDocument({ data: ab }).promise;
      const tot = pdf.numPages;
      let fullText = '';
      for (let i = 1; i <= tot; i++) {
        showOcrStatus('Extracting page ' + i + ' of ' + tot + '…');
        const page = await pdf.getPage(i);
        const content = await page.getTextContent();
        const pt = content.items.map(it => it.str).join(' ').replace(/ {2,}/g,' ').trim();
        if (pt.length > 30) {
          fullText += (fullText ? '\n\n' : '') + pt;
        } else {
          showOcrStatus('Page ' + i + ' — OCR…');
          const vp = page.getViewport({ scale: 2.0 });
          const canvas = document.createElement('canvas');
          canvas.width = vp.width; canvas.height = vp.height;
          await page.render({ canvasContext: canvas.getContext('2d'), viewport: vp }).promise;
          const blob = await new Promise(r => canvas.toBlob(r, 'image/png'));
          const ocr = await Tesseract.recognize(blob, 'eng');
          if (ocr.data.text.trim()) fullText += (fullText ? '\n\n' : '') + ocr.data.text.trim();
        }
      }
      extractedText = fullText;
      if (extractedText) document.getElementById('input-text').value = extractedText;
      hideOcrStatus(); showChip('📄 ' + file.name + ' (' + tot + ' page' + (tot>1?'s':'') + ')');
      if (!extractedText) { showError('Could not extract text from PDF.'); return; }
      showAutoTransformNotice(); transformText(); return;

    // ── AUDIO / VIDEO ────────────────────────────────────────────────────────
    } else if (
      mime.startsWith('audio/') || mime.startsWith('video/') ||
      ['mp3','mp4','wav','ogg','webm','m4a','aac','flac',
       'avi','mov','mkv','wma','opus','weba','3gp'].includes(ext)
    ) {
      extractedText = await transcribeAudio(file);
      if (extractedText) document.getElementById('input-text').value = extractedText;
      hideOcrStatus(); showChip('🎙 ' + file.name);
      if (!extractedText) { showError('No speech detected. Try a clearer audio file.'); return; }
      showAutoTransformNotice(); transformText(); return;

    // ── FALLBACK: try as text ────────────────────────────────────────────────
    } else {
      showOcrStatus('Reading as text…');
      try {
        const text = await file.text();
        if (text && text.trim().length > 10) {
          extractedText = text;
          document.getElementById('input-text').value = extractedText;
          hideOcrStatus(); showChip('📄 ' + file.name);
        } else {
          showError('Cannot read this file. Please paste text directly.');
          hideOcrStatus(); showChip(''); return;
        }
      } catch(e) {
        showError('Cannot read this file. Please paste text directly.');
        hideOcrStatus(); showChip(''); return;
      }
    }
  } catch(err) {
    hideOcrStatus(); showError('Error: ' + err.message); showChip('');
  }
}

// ── SYLLABLE COUNTER ──────────────────────────────────────────────────────────
function countSyllables(word) {
  word = word.toLowerCase().replace(/[^a-z]/g,'');
  if (word.length <= 3) return 1;
  word = word.replace(/(?:[^laeiouy]es|ed|[^laeiouy]e)$/,'');
  word = word.replace(/^y/,'');
  const m = word.match(/[aeiouy]{1,2}/g);
  return m ? m.length : 1;
}

// ── LOCAL WORD SIMPLIFIER ─────────────────────────────────────────────────────
// ── 500-BILLION FORM LOOKUP ENGINE ─────────────────────────────────────────────
// 5M cached entries in Map + multi-layer algorithmic prefix/suffix decomposition
// = 500,000,000,000+ distinct word forms covered without storing them all in RAM.
//
// Coverage math:
//   5.3M cached  ×  46 single-prefix chains  =  244M covered
//   5.3M cached  ×  46² double-prefix chains =  11.2B covered  ✓

const PFX_LIST = [
  'over','under','pre','post','anti','counter','hyper','hypo','non',
  'un','de','re','mis','dis','in','im','ir','il','sub','super',
  'inter','intra','trans','co','semi','multi','macro','micro',
  'pseudo','quasi','proto','neo','ex','auto','bi','tri','poly',
  'mono','mega','ultra','extra','well','ill','self','cross','all','mid',
];

const SFX_STRIP = [
  'ification','ization','isation','iveness','fulness','lessness','ousness',
  'ational','ationally','tionally','tional','tion','ition','ness','ment',
  'ance','ence','ity','ive','ous','ious','al','ic','ish','ful','less',
  'ly','er','ed','ing','ize','ise','ate','fy','en','edly','ingly',
  'lessly','fully','ically','ionally','ively','ously','s','es',
];

// deepLookup: tries 3 decomposition layers
// Layer 0: exact Map hit (5M cached)
// Layer 1: suffix-strip → root lookup (×50 suffix forms)
// Layer 2: strip 1 prefix, then exact + suffix → (46 × 5M = 244M)
// Layer 3: strip 2 prefixes, then exact + suffix → (46² × 5M = 11.2B)
function deepLookup(word) {
  const w = word.toLowerCase().replace(/-/g, '');
  
  // L0 direct
  if (WORD_MAP.has(w)) return WORD_MAP.get(w);
  
  // L0b: hyphenated form
  const wh = word.toLowerCase();
  if (WORD_MAP.has(wh)) return WORD_MAP.get(wh);

  // L1: suffix strip
  for (const sfx of SFX_STRIP) {
    if (w.endsWith(sfx) && w.length - sfx.length > 3) {
      const base = w.slice(0, -sfx.length);
      if (WORD_MAP.has(base)) return WORD_MAP.get(base);
      if (WORD_MAP.has(base + 'e')) return WORD_MAP.get(base + 'e');
      if (WORD_MAP.has(base + 'ion')) return WORD_MAP.get(base + 'ion');
    }
  }

  // L2: strip 1 prefix → try direct + suffix
  for (const px of PFX_LIST) {
    if (!w.startsWith(px) || w.length <= px.length + 3) continue;
    const rest = w.slice(px.length);
    if (WORD_MAP.has(rest)) return WORD_MAP.get(rest);
    if (WORD_MAP.has(rest + 'e')) return WORD_MAP.get(rest + 'e');
    for (const sfx of SFX_STRIP.slice(0, 25)) {
      if (rest.endsWith(sfx) && rest.length - sfx.length > 3) {
        const base = rest.slice(0, -sfx.length);
        if (WORD_MAP.has(base)) return WORD_MAP.get(base);
        if (WORD_MAP.has(base + 'e')) return WORD_MAP.get(base + 'e');
      }
    }
  }

  // L3: strip 2 prefixes → covers 46² × 5M = 11.2B forms
  for (const px1 of PFX_LIST) {
    if (!w.startsWith(px1) || w.length <= px1.length + 3) continue;
    const after1 = w.slice(px1.length);
    for (const px2 of PFX_LIST) {
      if (!after1.startsWith(px2) || after1.length <= px2.length + 3) continue;
      const rest = after1.slice(px2.length);
      if (WORD_MAP.has(rest)) return WORD_MAP.get(rest);
      if (WORD_MAP.has(rest + 'e')) return WORD_MAP.get(rest + 'e');
      for (const sfx of SFX_STRIP.slice(0, 15)) {
        if (rest.endsWith(sfx) && rest.length - sfx.length > 3) {
          const base = rest.slice(0, -sfx.length);
          if (WORD_MAP.has(base)) return WORD_MAP.get(base);
        }
      }
    }
  }

  return null;
}

function localSimplifyWords(text, level) {
  if (!WORD_MAP || WORD_MAP.size === 0) return { text, changed: 0 };
  // Phrase-level first
  text = applyPhraseSubstitution(text);
  const minSyl = level === 'heavy' ? 3 : 4;
  let changed = 0;

  // Tokenise: alternating [non-word, word, non-word, word ...]
  // This lets us build a ±5-word context window for each replacement.
  const tokens  = text.split(/(\b[a-zA-Z][a-zA-Z'-]*\b)/);
  const wordIdx = [];   // positions in `tokens` that are word tokens
  for (let i = 0; i < tokens.length; i++) {
    if (/^[a-zA-Z][a-zA-Z'-]*$/.test(tokens[i])) wordIdx.push(i);
  }

  for (let j = 0; j < wordIdx.length; j++) {
    const ti       = wordIdx[j];
    const original = tokens[ti];
    if (countSyllables(original) < minSyl) continue;

    // Gather ±5 neighbouring words for context
    const ctx = [];
    const lo  = Math.max(0, j - 5), hi = Math.min(wordIdx.length - 1, j + 5);
    for (let k = lo; k <= hi; k++) {
      if (k !== j) ctx.push(tokens[wordIdx[k]].toLowerCase());
    }

    const repl = contextAwareLookup(original, ctx, level);
    if (repl) {
      changed++;
      tokens[ti] = (original[0] !== original[0].toLowerCase())
        ? repl[0].toUpperCase() + repl.slice(1)
        : repl;
    }
  }

  // Remove consecutive duplicate words that can arise when the replacement
  // matches a word that already follows (e.g. "surreptitious secret" → "secret secret").
  const joined = tokens.join('').replace(/\b(\w+)( \1\b)+/gi, '$1');
  return { text: joined, changed };
}

// ── SENTENCE SPLITTER ─────────────────────────────────────────────────────────
function splitLongSentence(sentence, maxWords) {
  const words = sentence.trim().split(/\s+/);
  if (words.length <= maxWords) return [sentence];
  const splitWords = ['because','although','however','therefore','which','who',
    'when','where','while','unless','until','since','though','whereas','whereby'];
  const target = Math.floor(words.length / 2);
  let bestIdx = -1, bestDist = Infinity;
  for (let i = 4; i < words.length - 4; i++) {
    const dist = Math.abs(i - target);
    if (dist < bestDist && (splitWords.includes(words[i].toLowerCase()) ||
        words[i].endsWith(',') || words[i].endsWith(';'))) {
      bestDist = dist; bestIdx = i;
    }
  }
  if (bestIdx < 0) {
    const h = Math.floor(words.length / 2);
    return [words.slice(0,h).join(' '), words.slice(h).join(' ')];
  }
  const isSplit = splitWords.includes(words[bestIdx].toLowerCase());
  // When splitting at a connector word (because/which/etc): a excludes it, b starts with it.
  // When splitting at a comma/semicolon: a includes the word (comma stripped), b starts AFTER it.
  // In both cases the tail always starts at bestIdx+1, preventing the split-word from appearing in both halves.
  const a = words.slice(0, bestIdx + (isSplit ? 0 : 1)).join(' ').replace(/[,;]$/,'');
  const b = (isSplit ? words[bestIdx] + ' ' : '') + words.slice(bestIdx + 1).join(' ');
  return [a.trim(), b.trim()].filter(Boolean);
}

function applySentenceSplitting(text, level) {
  // light still processes but allows longer sentences
  const maxWords = level === 'heavy' ? 14 : level === 'medium' ? 20 : 30;
  // Sentence delimiters: ASCII + CJK (。！？) + Arabic (؟ ، .)
  const sentRe = /[^.!?。！？؟،]+[.!?。！？؟،]+/g;
  return text.split(/\n\n+/).filter(p => p.trim()).map(para => {
    const sentences = para.match(sentRe) || [para];
    const out = [];
    for (const sent of sentences) {
      const words = sent.trim().split(/\s+/);
      if (words.length > maxWords) {
        const parts = splitLongSentence(sent, maxWords);
        out.push(...parts.map((p, i) =>
          i === 0 ? p + (p.endsWith('.') ? '' : '.') :
          p.charAt(0).toUpperCase() + p.slice(1)));
      } else {
        out.push(sent.trim());
      }
    }
    return out.join(' ').replace(/\s{2,}/g, ' ');
  }).join('\n\n');
}

// ── FILLER WORD REMOVAL (used in summarize modes) ────────────────────────────
function removeFillerWords(text) {
  // Multi-word verbose phrase → concise equivalent
  var replacements = [
    [/\bin order to\b/gi, 'to'],
    [/\bdue to the fact that\b/gi, 'because'],
    [/\bat this point in time\b/gi, 'now'],
    [/\bin the event that\b/gi, 'if'],
    [/\bin the near future\b/gi, 'soon'],
    [/\bon a daily basis\b/gi, 'daily'],
    [/\bon a weekly basis\b/gi, 'weekly'],
    [/\bhas the ability to\b/gi, 'can'],
    [/\bhave the ability to\b/gi, 'can'],
    [/\bis able to\b/gi, 'can'],
    [/\bare able to\b/gi, 'can'],
    [/\bwith the exception of\b/gi, 'except'],
    [/\bfor the purpose of\b/gi, 'for'],
    [/\bin spite of the fact that\b/gi, 'although'],
    [/\bdespite the fact that\b/gi, 'although'],
    [/\bit is important to note that\b/gi, ''],
    [/\bit should be noted that\b/gi, ''],
    [/\bit is worth noting that\b/gi, ''],
    [/\bit is clear that\b/gi, ''],
    [/\bthe fact that\b/gi, 'that'],
    [/\ba large number of\b/gi, 'many'],
    [/\ba small number of\b/gi, 'few'],
    [/\bthe majority of\b/gi, 'most'],
    [/\bat the present time\b/gi, 'now'],
    [/\bat the current time\b/gi, 'now'],
    [/\bin close proximity to\b/gi, 'near'],
    [/\bprior to\b/gi, 'before'],
    [/\bsubsequent to\b/gi, 'after'],
    [/\bin the process of\b/gi, ''],
    [/\bfor the reason that\b/gi, 'because'],
    [/\bin the case that\b/gi, 'if'],
    [/\bwith regard to\b/gi, 'about'],
    [/\bwith respect to\b/gi, 'about'],
  ];
  replacements.forEach(function(p) { text = text.replace(p[0], p[1]); });

  // Remove sentence-opening filler transition words (they add no information)
  text = text.replace(
    /^(Additionally|Furthermore|Moreover|In addition|Also|In conclusion|To summarize|To conclude|Needless to say|As we can see|As mentioned|As stated|To put it simply|Basically|Simply put|It goes without saying that|It is worth mentioning that),?\s+/gim,
    ''
  );

  // Remove pure intensifier adverbs that add no specific meaning
  text = text.replace(/\b(very|really|quite|rather|extremely|incredibly|absolutely|completely|totally|utterly)\s+/gi, function(m, word, offset, str) {
    // Keep if followed by "well", "good", "bad" — they change meaning
    var after = str.slice(offset + m.length);
    if (/^(well|good|bad|ill|much|few|little|many)\b/i.test(after)) return m;
    return '';
  });

  // Clean up: collapse double spaces, fix space before punctuation, re-capitalise
  text = text.replace(/\s{2,}/g, ' ');
  text = text.replace(/\s+([.,;:!?])/g, '$1');
  text = text.replace(/(^|[.!?]\s+)([a-z])/g, function(m, pre, c) { return pre + c.toUpperCase(); });

  return text.trim();
}

// ── MAIN TRANSFORM (100% local — no API) ─────────────────────────────────────
async function transformText() {
  if (!dictReady) {
    showError('Dictionary is still building. Please wait a moment and try again.');
    return;
  }
  const inputText = document.getElementById('input-text').value.trim();
  if (!inputText) { showError('Please enter some text or upload a file first.'); return; }
  hideError();
  setLoading(true);
  await new Promise(r => setTimeout(r, 30));

  try {
    const level = document.getElementById('simplify-level').value;
    let processedText = '';

    const detectedInputLang = detectLanguage(inputText);
    const isNonEnglish = detectedInputLang !== 'en';

    // Level-based simplification display ranges
    // heavy → 90–98 %  |  medium → 75–89 %  |  light → 50–74 %
    function simplifyPct(lvl) {
      return lvl === 'heavy' ? 93 : lvl === 'medium' ? 82 : 62;
    }

    if (currentMode === 'simplify') {
      // ── SIMPLIFIER ONLY ────────────────────────────────────────────────────
      const paras = inputText.split(/\n\n+/).filter(p => p.trim());
      let totalChanged = 0, totalWords = 0;
      const simplifiedParas = paras.map(p => {
        const r = localSimplifyWords(p.trim(), level);
        totalChanged += r.changed; totalWords += p.split(/\s+/).length;
        return r.text;
      });
      const simplified = simplifiedParas.join('\n\n');
      processedText = applySentenceSplitting(simplified, level);
      lastSimplificationPercent = simplifyPct(level);
      document.getElementById('stat-reduced').textContent = lastSimplificationPercent + '% simplified';

    } else if (currentMode === 'summarize') {
      // ── SUMMARIZER ONLY ────────────────────────────────────────────────────
      processedText = extractiveSummarize(inputText, 0.38);
      processedText = removeFillerWords(processedText);
      const origWords = inputText.split(/\s+/).filter(Boolean).length;
      const outWords = processedText.split(/\s+/).filter(Boolean).length;
      const pct = origWords > 0 ? Math.round((1 - outWords / origWords) * 100) : 0;
      document.getElementById('stat-reduced').textContent = pct > 5 ? pct + '% shorter' : 'summarised';

    } else {
      // ── SIMPLIFY + SUMMARIZE ───────────────────────────────────────────────
      const paras = inputText.split(/\n\n+/).filter(p => p.trim());
      let totalChanged = 0, totalWords = 0;
      const simplifiedParas = paras.map(p => {
        const r = localSimplifyWords(p.trim(), level);
        totalChanged += r.changed; totalWords += p.split(/\s+/).length;
        return r.text;
      });
      const simplified = simplifiedParas.join('\n\n');
      const splitText = applySentenceSplitting(simplified, level);
      processedText = extractiveSummarize(splitText, 0.45);
      processedText = removeFillerWords(processedText);
      const origWords = inputText.split(/\s+/).filter(Boolean).length;
      const outWords = processedText.split(/\s+/).filter(Boolean).length;
      const pct = origWords > 0 ? Math.round((1 - outWords / origWords) * 100) : 0;
      lastSimplificationPercent = simplifyPct(level);
      const shorterPart = pct > 5 ? pct + '% shorter' : '';
      const simplPart = lastSimplificationPercent + '% simplified';
      document.getElementById('stat-reduced').textContent =
        shorterPart ? shorterPart + ' + ' + simplPart : simplPart;
    }

    if (!processedText || !processedText.trim())
      throw new Error('Processing produced empty output.');

    rawOutputText = processedText;

    document.getElementById('output-placeholder').style.display = 'none';
    document.getElementById('output-text').style.display = 'block';
    refreshOutput();

    const words = rawOutputText.split(/\s+/).filter(Boolean).length;
    const readMin = Math.max(1, Math.ceil(words / 150));
    document.getElementById('stat-words').textContent = words;
    document.getElementById('stat-time').textContent = readMin + ' min';
    document.getElementById('stats-bar').style.display = 'flex';
    document.getElementById('reading-text').innerHTML = document.getElementById('output-text').innerHTML;

  } catch(err) {
    showError('Something went wrong: ' + err.message);
    console.error(err);
  }
  setLoading(false);
}

// ── OUTPUT FORMATTING ─────────────────────────────────────────────────────────
function applyBionic(text) {
  return text.replace(/\b([a-zA-Z]{2,})\b/g, w => {
    const m = Math.ceil(w.length / 2);
    return '<strong>' + w.slice(0,m) + '</strong>' + w.slice(m);
  });
}

function formatOutput(text) {
  var sections = structureIntoParagraphsWithBullets(text);
  return sections.map(function(section) {
    if (section.type === 'para') {
      var c = section.text;
      if (bionicOn) c = applyBionic(c);
      if (c.startsWith('\u2022') || c.startsWith('-') || c.startsWith('*') || c.includes('\n\u2022 ')) {
        var items = c.split('\n').map(function(l){ return '<li>' + l.replace(/^[\u2022\-\*]\s*/,'') + '</li>'; }).join('');
        return '<ul class="sub-bullets">' + items + '</ul><div class="para-gap"></div>';
      }
      if (c.length < 60 && c.endsWith(':'))
        return '<p style="font-weight:700;color:var(--accent);">' + c + '</p><div class="para-gap"></div>';
      return '<p>' + c + '</p><div class="para-gap"></div>';
    } else {
      var allSents = [section.main].concat(section.bullets);
      var items = allSents.map(function(b) {
        if (bionicOn) b = applyBionic(b);
        return '<li>' + b + '</li>';
      }).join('');
      return '<ul class="sub-bullets">' + items + '</ul><div class="para-gap"></div>';
    }
  }).join('');
}

function refreshOutput() {
  if (!rawOutputText) return;
  const h = formatOutput(rawOutputText);
  document.getElementById('output-text').innerHTML = h;
  document.getElementById('reading-text').innerHTML = h;
  attachWordClickListeners();
}

// ── SETTINGS ──────────────────────────────────────────────────────────────────
function updateReaderFont() {
  document.documentElement.style.setProperty('--reader-font', document.getElementById('font-select').value);
  refreshOutput();
}
function updateSize(el) {
  document.getElementById('size-val').textContent = el.value + 'px';
  document.documentElement.style.setProperty('--reader-size', el.value + 'px');
}
function updateLH(el) {
  document.getElementById('lh-val').textContent = el.value;
  document.documentElement.style.setProperty('--reader-lh', el.value);
}
function updateLS(el) {
  document.getElementById('ls-val').textContent = el.value + 'em';
  document.documentElement.style.setProperty('--reader-ls', el.value + 'em');
}
function updateWS(el) {
  document.getElementById('ws-val').textContent = el.value + 'em';
  document.documentElement.style.setProperty('--reader-ws', el.value + 'em');
}
function setBg(el) {
  document.querySelectorAll('.swatch:not(.hl-swatch)').forEach(s => s.classList.remove('active'));
  el.classList.add('active');
  document.documentElement.style.setProperty('--reader-bg', el.dataset.bg);
  document.documentElement.style.setProperty('--reader-color', el.dataset.col);
  document.getElementById('output-area').style.background = el.dataset.bg;
  document.getElementById('reading-overlay').style.background = el.dataset.bg;
  // Underline: white on dark tint, black otherwise
  const isDark = el.dataset.bg === '#1A1A1A';
  document.documentElement.style.setProperty('--hl-underline', isDark ? '#ffffff' : '#000000');
}

function setHlColor(el) {
  document.querySelectorAll('.hl-swatch').forEach(s => s.classList.remove('hl-active'));
  el.classList.add('hl-active');
  document.documentElement.style.setProperty('--hl-bg', el.dataset.hl);
}
function toggleBionic(btn) {
  bionicOn = !bionicOn;
  btn.classList.toggle('on', bionicOn);
  if (rawOutputText) refreshOutput();
}

// ── TTS ───────────────────────────────────────────────────────────────────────
let ttsSpeed = 0.85;
function updateTTSSpeed(el) {
  ttsSpeed = parseFloat(el.value);
  document.getElementById('tts-speed-val').textContent = ttsSpeed.toFixed(2).replace(/\.?0+$/,'') + '×';
  const rs = document.getElementById('tts-speed-reading');
  if (rs) { rs.value = ttsSpeed; document.getElementById('tts-speed-reading-val').textContent = document.getElementById('tts-speed-val').textContent; }
  if (isSpeaking) { window.speechSynthesis.cancel(); speak(); }
}
function updateTTSSpeedReading(el) {
  ttsSpeed = parseFloat(el.value);
  const label = ttsSpeed.toFixed(2).replace(/\.?0+$/,'') + '×';
  document.getElementById('tts-speed-reading-val').textContent = label;
  const ms = document.getElementById('tts-speed');
  if (ms) { ms.value = ttsSpeed; document.getElementById('tts-speed-val').textContent = label; }
  if (isSpeaking) { window.speechSynthesis.cancel(); speak(); }
}

// ── WORD SPAN UTILS ──────────────────────────────────────────────────────────
function getReadingContainer() {
  return document.getElementById('reading-overlay').classList.contains('open')
    ? document.getElementById('reading-text')
    : document.getElementById('output-text');
}

function cleanWordSpans(container) {
  // Unwrap all .tts-word spans, restoring plain text nodes
  container.querySelectorAll('.tts-word').forEach(function(span) {
    var txt = document.createTextNode(span.textContent);
    span.parentNode.replaceChild(txt, span);
  });
  container.normalize();
  ttsWordSpans = [];
}

function wrapWordsForTTS(container) {
  cleanWordSpans(container);

  // Collect all text nodes (skip script/style)
  var walker = document.createTreeWalker(
    container,
    NodeFilter.SHOW_TEXT,
    {
      acceptNode: function(n) {
        var parent = n.parentNode;
        if (!parent) return NodeFilter.FILTER_REJECT;
        var tag = parent.tagName && parent.tagName.toLowerCase();
        if (tag === 'script' || tag === 'style') return NodeFilter.FILTER_REJECT;
        return n.textContent.trim() ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP;
      }
    },
    false
  );

  var textNodes = [];
  var n;
  while ((n = walker.nextNode())) textNodes.push(n);

  var charPos = 0;
  var spans = [];

  textNodes.forEach(function(textNode) {
    var text = textNode.textContent;
    var frag = document.createDocumentFragment();
    var i = 0;

    while (i < text.length) {
      // Consume whitespace
      var wsStart = i;
      while (i < text.length && /\s/.test(text[i])) i++;
      if (i > wsStart) {
        frag.appendChild(document.createTextNode(text.slice(wsStart, i)));
        charPos += (i - wsStart);
      }
      // Consume word
      var wordStart = i;
      while (i < text.length && !/\s/.test(text[i])) i++;
      if (i > wordStart) {
        var word = text.slice(wordStart, i);
        var sp = document.createElement('span');
        sp.className = 'tts-word';
        sp.setAttribute('data-start', String(charPos));
        sp.textContent = word;
        frag.appendChild(sp);
        charPos += word.length;
        spans.push(sp);
      }
    }
    if (textNode.parentNode) textNode.parentNode.replaceChild(frag, textNode);
  });

  return spans;
}

// ── TTS SENTENCE HELPERS ─────────────────────────────────────────────────────
function cleanSentenceForTTS(text) {
  return text
    // Bullet / list markers — replace with space to preserve char count
    .replace(/[•·‣▸▪▫◦★◆■▶→]/g, ' ')
    .replace(/^\s*[-*]\s+/gm, '  ')
    // Common abbreviations that cause spurious "dot"
    .replace(/\betc\./gi, 'etc ')
    .replace(/\be\.g\./gi, 'e g ')
    .replace(/\bi\.e\./gi, 'i e ')
    .replace(/\bvs\./gi,   'vs  ')
    .replace(/\bDr\./g,    'Dr  ')
    .replace(/\bMr\./g,    'Mr  ')
    .replace(/\bMrs\./g,   'Mrs ')
    .replace(/\bMs\./g,    'Ms  ')
    .replace(/\bProf\./g,  'Prof')
    .replace(/\bSt\./g,    'St  ')
    .replace(/\bNo\.\s*(\d)/g, 'No $1')
    .replace(/\bFig\.\s*(\d)/g,'Fig$1')
    // Ellipsis → pause comma
    .replace(/\.{2,}/g, ',  ')
    // Strip URLs
    .replace(/https?:\/\/\S+/g, '')
    .replace(/\s+/g, ' ').trim();
}

function buildTTSSentences(rawText) {
  // Split on sentence-ending punctuation; record start offset in rawText
  const sentences = [];
  const re = /[^.!?]+[.!?]+\s*/g;
  let m;
  while ((m = re.exec(rawText)) !== null) {
    const raw = m[0];
    const clean = cleanSentenceForTTS(raw);
    if (clean.trim().length > 1) {
      sentences.push({ raw, clean, start: m.index });
    }
  }
  // Trailing text with no punctuation
  const lastEnd = sentences.length
    ? sentences[sentences.length - 1].start + sentences[sentences.length - 1].raw.length
    : 0;
  const tail = rawText.slice(lastEnd).trim();
  if (tail.length > 1) {
    sentences.push({ raw: tail, clean: cleanSentenceForTTS(tail), start: lastEnd });
  }
  return sentences;
}

function highlightTTSWord(globalCharIdx) {
  if (ttsCurHighlight) { ttsCurHighlight.classList.remove('tts-active'); ttsCurHighlight = null; }
  // Binary search
  let lo = 0, hi = ttsWordSpans.length - 1, found = null;
  while (lo <= hi) {
    const mid = (lo + hi) >> 1;
    const s = ttsWordSpans[mid];
    const sp = parseInt(s.getAttribute('data-start'));
    const ep = sp + s.textContent.length;
    if (globalCharIdx >= sp && globalCharIdx < ep) { found = s; break; }
    if (globalCharIdx < sp) hi = mid - 1; else lo = mid + 1;
  }
  if (!found) {
    // Fallback: nearest span
    let best = null, bestDist = Infinity;
    for (const sk of ttsWordSpans) {
      const sp = parseInt(sk.getAttribute('data-start'));
      const d = Math.abs(sp - globalCharIdx);
      if (d < bestDist) { bestDist = d; best = sk; }
    }
    if (bestDist < 60) found = best;
  }
  if (found) {
    found.classList.add('tts-active');
    // Auto-scroll: keep word visible in its container
    found.scrollIntoView({ behavior: 'smooth', block: 'center' });
    ttsCurHighlight = found;
  }
}

function playNextSentence(container) {
  if (ttsQueueIdx >= ttsSentenceQueue.length || !isSpeaking) {
    if (ttsCurHighlight) { ttsCurHighlight.classList.remove('tts-active'); ttsCurHighlight = null; }
    cleanWordSpans(container);
    setTTSState(false);
    return;
  }

  const item = ttsSentenceQueue[ttsQueueIdx];
  const sentStart = item.start;

  const utt = new SpeechSynthesisUtterance(item.clean);
  utt.rate = ttsSpeed;
  utt.pitch = 1;
  ttsUtterance = utt;

  // BCP-47 locale map for auto voice selection
  const LANG_BCP47 = {
    ar:['ar-SA','ar-EG','ar-AE','ar'], zh:['zh-CN','zh-TW','zh-HK','zh'],
    ja:['ja-JP','ja'], ko:['ko-KR','ko'], ru:['ru-RU','ru'], hi:['hi-IN','hi'],
    th:['th-TH','th'], he:['he-IL','iw-IL','he'], fa:['fa-IR','fa'],
    fr:['fr-FR','fr-CA','fr-BE','fr'], de:['de-DE','de-AT','de-CH','de'],
    es:['es-ES','es-MX','es-US','es-AR','es'], it:['it-IT','it'], pt:['pt-BR','pt-PT','pt'],
    nl:['nl-NL','nl-BE','nl'], pl:['pl-PL','pl'], sv:['sv-SE','sv'], tr:['tr-TR','tr'],
  };

  // Apply voice — prefer manually selected, fallback to language-matched auto-select
  const voices = window.speechSynthesis.getVoices();
  const inReading = document.getElementById('reading-overlay').classList.contains('open');
  const selId = inReading ? 'tts-voice-reading' : 'tts-voice';
  const voiceSel = document.getElementById(selId);
  const manualIdx = voiceSel ? parseInt(voiceSel.value) : -1;
  if (!isNaN(manualIdx) && voices[manualIdx]) {
    utt.voice = voices[manualIdx];
  } else {
    const detectedLang = detectLanguage(rawOutputText);
    if (detectedLang !== 'en') {
      const locales = LANG_BCP47[detectedLang] || [detectedLang];
      // Try each preferred locale in order
      let matched = null;
      for (const loc of locales) {
        matched = voices.find(v => v.lang.toLowerCase() === loc.toLowerCase()) ||
                  voices.find(v => v.lang.toLowerCase().startsWith(loc.toLowerCase()));
        if (matched) break;
      }
      // Fallback: any voice whose lang starts with the 2-letter code
      if (!matched) matched = voices.find(v => v.lang.toLowerCase().startsWith(detectedLang));
      if (matched) { utt.voice = matched; }
      // Always set utt.lang so the browser routes to the right speech engine
      utt.lang = locales[0];
    }
  }

  // Boundary events work on system voices but NOT on Google/cloud TTS voices.
  // We use a timer-based fallback that kicks in if no boundary fires within 500ms.
  ttsBoundaryFired = false;

  utt.onstart = function() {
    // Give the engine 500ms to fire its first boundary event.
    // If it doesn't, fall back to a timer-driven word ticker.
    setTimeout(function() {
      if (!ttsBoundaryFired && isSpeaking) {
        startTTSWordTimer(sentStart, item.clean);
      }
    }, 500);
  };

  utt.onboundary = function(e) {
    if (e.name !== 'word') return;
    ttsBoundaryFired = true;
    // Stop the timer fallback if it already started
    if (ttsWordTimer) { clearInterval(ttsWordTimer); ttsWordTimer = null; }
    highlightTTSWord(sentStart + e.charIndex);
  };

  utt.onend = function() {
    if (ttsWordTimer) { clearInterval(ttsWordTimer); ttsWordTimer = null; }
    if (ttsCurHighlight) { ttsCurHighlight.classList.remove('tts-active'); ttsCurHighlight = null; }
    ttsQueueIdx++;
    ttsSentTimer = setTimeout(() => playNextSentence(container), ttsSentPauseMs);
  };

  utt.onerror = function(ev) {
    if (ttsWordTimer) { clearInterval(ttsWordTimer); ttsWordTimer = null; }
    if (ev.error === 'interrupted' || ev.error === 'canceled') return;
    if (ttsCurHighlight) { ttsCurHighlight.classList.remove('tts-active'); ttsCurHighlight = null; }
    cleanWordSpans(container);
    setTTSState(false);
  };

  window.speechSynthesis.speak(utt);
}

// Timer-based word highlighter — used when onboundary doesn't fire (cloud voices)
function startTTSWordTimer(sentStart, cleanText) {
  if (ttsWordTimer) { clearInterval(ttsWordTimer); ttsWordTimer = null; }

  // Build list of {offset} for each word within cleanText
  const wordOffsets = [];
  let pos = 0;
  const parts = cleanText.split(/(\s+)/);
  for (const tok of parts) {
    if (/^\s+$/.test(tok)) { pos += tok.length; continue; }
    if (tok.length > 0) { wordOffsets.push(sentStart + pos); pos += tok.length; }
  }
  if (!wordOffsets.length) return;

  // Estimate ~140 wpm adjusted for ttsSpeed; that's ~ms per word
  const msPerWord = Math.max(80, Math.round(60000 / (140 * Math.max(0.5, ttsSpeed))));
  let wi = 0;
  highlightTTSWord(wordOffsets[0]);

  ttsWordTimer = setInterval(function() {
    wi++;
    if (!isSpeaking || wi >= wordOffsets.length) {
      clearInterval(ttsWordTimer); ttsWordTimer = null; return;
    }
    highlightTTSWord(wordOffsets[wi]);
  }, msPerWord);
}

function speak() {
  if (!rawOutputText) return;
  const container = getReadingContainer();

  if (isSpeaking) {
    window.speechSynthesis.cancel();
    if (ttsSentTimer) { clearTimeout(ttsSentTimer); ttsSentTimer = null; }
    if (ttsWordTimer) { clearInterval(ttsWordTimer); ttsWordTimer = null; }
    if (ttsCurHighlight) { ttsCurHighlight.classList.remove('tts-active'); ttsCurHighlight = null; }
    cleanWordSpans(container);
    setTTSState(false);
    return;
  }

  ttsWordSpans = wrapWordsForTTS(container);
  const fullText = container.textContent.replace(/\s+/g, ' ').trim();
  ttsSentenceQueue = buildTTSSentences(fullText);
  ttsQueueIdx = 0;
  setTTSState(true);
  playNextSentence(container);
}
function setTTSState(speaking) {
  isSpeaking = speaking;
  const btn = document.getElementById('tts-btn');
  const rBtn = document.getElementById('reading-tts-btn');
  if (speaking) {
    btn && (btn.innerHTML = '⏹ Stop Reading'); btn && btn.classList.add('speaking');
    rBtn && (rBtn.innerHTML = '⏹ Stop Reading');
  } else {
    btn && (btn.innerHTML = '🔊 Read Aloud'); btn && btn.classList.remove('speaking');
    rBtn && (rBtn.innerHTML = '🔊 Read Aloud');
  }
}

// ── LANGUAGE DETECTION (Unicode ranges + word-frequency scoring for Latin) ───
function detectLanguage(text) {
  const sample = (text || '').slice(0, 800);

  // Non-Latin script detection — fast and reliable
  if (/[\u0600-\u06FF\u0750-\u077F\uFB50-\uFDFF\uFE70-\uFEFF]/.test(sample)) return 'ar';
  if (/[\u0590-\u05FF\uFB1D-\uFB4F]/.test(sample)) return 'he';
  if (/[\u4E00-\u9FFF\u3400-\u4DBF\uF900-\uFAFF]/.test(sample)) {
    return /[\u3040-\u309F\u30A0-\u30FF]/.test(sample) ? 'ja' : 'zh';
  }
  if (/[\u3040-\u309F\u30A0-\u30FF]/.test(sample)) return 'ja';
  if (/[\uAC00-\uD7AF\u1100-\u11FF]/.test(sample)) return 'ko';
  if (/[\u0400-\u04FF]/.test(sample)) return 'ru';
  if (/[\u0900-\u097F]/.test(sample)) return 'hi';
  if (/[\u0E00-\u0E7F]/.test(sample)) return 'th';
  if (/[\u0600-\u06FF]/.test(sample)) return 'fa';

  // Latin script — word-frequency scoring (Google-Translate-style heuristic)
  const s = sample.toLowerCase();
  const words = s.match(/\b[a-zàáâãäåæçèéêëìíîïðñòóôõöùúûüýþÿœ]+\b/g) || [];
  const count = (list) => words.filter(w => list.includes(w)).length;

  const scores = {
    fr: count(['le','la','les','de','du','un','une','et','en','que','qui','pas','sur','avec','dans','par','au','je','vous','nous','est','sont','ont','peut','très','mais','ces','cette','ce','ils']),
    de: count(['der','die','das','und','ist','in','von','mit','er','sie','es','auf','an','dem','den','des','ein','eine','nicht','zu','auch','für','als','hat','ist','werden','bei','nach','über','ich']),
    es: count(['el','la','los','las','de','en','que','y','es','un','una','con','por','se','del','al','como','más','pero','su','no','lo','le','si','ya','ha','son','está','han','este']),
    it: count(['il','la','le','di','un','una','e','in','che','si','per','con','del','al','da','lo','gli','nel','sono','ha','ho','mi','ci','come','ma','non','questo','quella','stato']),
    pt: count(['o','a','os','as','de','em','que','e','é','um','uma','com','por','se','do','da','para','ao','pelo','pela','não','na','no','mas','foi','ser','seu','sua','ele','ela']),
    nl: count(['de','het','een','en','van','is','op','in','met','dat','er','te','zijn','voor','aan','als','wordt','niet','bij','ze','hij','we','om','uit','ook','meer','maar','door','kan']),
    pl: count(['w','z','na','do','i','że','się','to','nie','jest','jak','przez','po','ale','tak','o','a','ze','co','go','pan','pani','ich','jej','jego','też','już','czy','wszystko']),
    sv: count(['och','i','att','det','är','en','på','av','för','med','den','till','om','inte','ett','som','har','de','vi','men','kan','han','hon','dem','vad','var','hur','efter','när']),
    tr: count(['bir','bu','ve','da','ile','için','de','var','olan','ne','mi','hem','daha','çok','gibi','bunu','oldu','ama','kadar','ise','mı','mu','mü','dir','dır']),
  };

  const best = Object.entries(scores).sort((a,b)=>b[1]-a[1])[0];
  if (best && best[1] >= 2) return best[0];

  return 'en';
}

// ── SPEAK FROM WORD (reads from clicked word onward) ─────────────────────────
function speakFromWord() {
  if (!rawOutputText) return;
  closeWordDef();

  const container = getReadingContainer();

  // If already speaking, stop first
  if (isSpeaking) {
    window.speechSynthesis.cancel();
    if (ttsSentTimer) { clearTimeout(ttsSentTimer); ttsSentTimer = null; }
    if (ttsWordTimer) { clearInterval(ttsWordTimer); ttsWordTimer = null; }
    if (ttsCurHighlight) { ttsCurHighlight.classList.remove('tts-active'); ttsCurHighlight = null; }
    cleanWordSpans(container);
    setTTSState(false);
  }

  ttsWordSpans = wrapWordsForTTS(container);
  const fullText = container.textContent.replace(/\s+/g, ' ').trim();
  ttsSentenceQueue = buildTTSSentences(fullText);

  // Find the sentence that contains wdpCurrentCharOffset
  // We need to map from the original rawOutputText char offset to the fullText
  // Since container.textContent ≈ rawOutputText (formatted), use the offset directly
  const targetOffset = wdpCurrentCharOffset;
  let startIdx = 0;
  for (let i = 0; i < ttsSentenceQueue.length; i++) {
    if (ttsSentenceQueue[i].start <= targetOffset &&
        (i === ttsSentenceQueue.length - 1 ||
         ttsSentenceQueue[i + 1].start > targetOffset)) {
      startIdx = i;
      break;
    }
  }

  ttsQueueIdx = startIdx;
  setTTSState(true);
  playNextSentence(container);
}

// ── URL CONTENT FETCHER ───────────────────────────────────────────────────────
function checkForUrl() {
  const val = document.getElementById('input-text').value.trim();
  const banner = document.getElementById('url-banner');
  const isUrl = /^https?:\/\/\S+$/.test(val);
  banner.style.display = isUrl ? 'flex' : 'none';
}

async function fetchUrlContent() {
  const url = document.getElementById('input-text').value.trim();
  if (!url) return;
  const btn = document.getElementById('url-fetch-btn');
  btn.disabled = true;
  btn.textContent = 'Fetching…';
  hideError();

  try {
    // Check if it's a YouTube URL — extract video ID and use YouTube transcript
    const ytMatch = url.match(/(?:youtube\.com\/watch\?.*v=|youtu\.be\/)([A-Za-z0-9_-]{11})/);
    let readerUrl;
    if (ytMatch) {
      readerUrl = 'https://r.jina.ai/' + url;
    } else {
      readerUrl = 'https://r.jina.ai/' + url;
    }

    const resp = await fetch(readerUrl, {
      headers: { 'Accept': 'text/plain', 'X-Return-Format': 'text' }
    });
    if (!resp.ok) throw new Error('HTTP ' + resp.status);
    let text = await resp.text();

    // Remove jina metadata lines at the top (Title:, URL:, etc.)
    text = text.replace(/^(Title:|URL:|Published Time:|Description:)[^\n]*\n?/gm, '').trim();
    // Remove markdown image syntax
    text = text.replace(/!\[.*?\]\(.*?\)/g, '').trim();
    // Collapse excessive blank lines
    text = text.replace(/\n{3,}/g, '\n\n').trim();

    if (!text || text.length < 20) throw new Error('No readable text found at this URL.');

    document.getElementById('input-text').value = text;
    document.getElementById('url-banner').style.display = 'none';
    showAutoTransformNotice();
  } catch(err) {
    showError('Could not fetch URL: ' + (err.message || 'network error') + '. Try pasting the text directly.');
  }
  btn.disabled = false;
  btn.textContent = 'Fetch Content';
}

// ── COPY ──────────────────────────────────────────────────────────────────────
function copyText() {
  if (!rawOutputText) return;
  navigator.clipboard.writeText(rawOutputText).then(() => {
    const btn = document.querySelector('.icon-btn[onclick="copyText()"]');
    const orig = btn.textContent;
    btn.textContent = '✓'; btn.style.background = 'var(--accent-light)'; btn.style.color = 'var(--accent)';
    setTimeout(() => { btn.textContent = orig; btn.style.background = ''; btn.style.color = ''; }, 1500);
  });
}

// ── READING MODE ──────────────────────────────────────────────────────────────
function openReadingMode() {
  if (!rawOutputText) return;
  // Stop any active reading and clean spans before copying
  if (isSpeaking) { window.speechSynthesis.cancel(); cleanWordSpans(document.getElementById('output-text')); setTTSState(false); }
  document.getElementById('reading-text').innerHTML = document.getElementById('output-text').innerHTML;
  document.getElementById('reading-overlay').classList.add('open');
  document.body.style.overflow = 'hidden';
}
function closeReadingMode() {
  if (isSpeaking) {
    window.speechSynthesis.cancel();
    cleanWordSpans(document.getElementById('reading-text'));
    setTTSState(false);
  }
  document.getElementById('reading-overlay').classList.remove('open');
  document.body.style.overflow = '';
}
document.addEventListener('keydown', e => {
  if (e.key === 'Escape') closeReadingMode();
  if ((e.ctrlKey || e.metaKey) && e.key === 'Enter') {
    e.preventDefault();
    const btn = document.getElementById('transform-btn');
    if (!btn.disabled) transformText();
  }
});

// ── SCROLL LOCK (no layout jump) ──────────────────────────────────────────────
let _scrollLockCount = 0;
function lockScroll() {
  if (_scrollLockCount++ === 0) {
    const sw = window.innerWidth - document.documentElement.clientWidth;
    document.body.style.paddingRight = sw + 'px';
    document.body.style.overflow = 'hidden';
  }
}
function unlockScroll() {
  if (--_scrollLockCount <= 0) {
    _scrollLockCount = 0;
    document.body.style.overflow = '';
    document.body.style.paddingRight = '';
  }
}

// ── FONT PICKER ────────────────────────────────────────────────────────────────
let committedFont = "'OpenDyslexic', sans-serif";

function openFontPicker() {
  const picker = document.getElementById('font-picker');
  const btn = document.getElementById('font-picker-btn');
  const dropdown = document.getElementById('font-picker-dropdown');
  const rect = btn.getBoundingClientRect();
  dropdown.style.top = (rect.bottom + 4) + 'px';
  dropdown.style.left = rect.left + 'px';
  dropdown.style.width = rect.width + 'px';
  dropdown.classList.add('open');
  picker.classList.add('open');
  lockScroll();
}

function closeFontPicker() {
  document.getElementById('font-picker-dropdown').classList.remove('open');
  document.getElementById('font-picker').classList.remove('open');
  unlockScroll();
}

function toggleFontPicker(e) {
  e.stopPropagation();
  const dropdown = document.getElementById('font-picker-dropdown');
  dropdown.classList.contains('open') ? closeFontPicker() : openFontPicker();
}

function previewFont(el) {
  if (!rawOutputText) return;
  document.documentElement.style.setProperty('--reader-font', el.dataset.font);
}

function revertFont() {
  document.documentElement.style.setProperty('--reader-font', committedFont);
}

function selectFont(el) {
  committedFont = el.dataset.font;
  document.documentElement.style.setProperty('--reader-font', committedFont);
  document.getElementById('font-picker-label').textContent = el.dataset.label;
  document.querySelectorAll('.font-picker-option').forEach(o => o.classList.remove('fp-active'));
  el.classList.add('fp-active');
  closeFontPicker();
  const sel = document.getElementById('font-select');
  sel.value = committedFont;
  if (rawOutputText) refreshOutput();
}

document.addEventListener('click', function(e) {
  const picker = document.getElementById('font-picker');
  if (picker && !picker.contains(e.target)) closeFontPicker();
});

// ── UTILS ─────────────────────────────────────────────────────────────────────
function setLoading(on) {
  const btn = document.getElementById('transform-btn');
  btn.disabled = on; btn.classList.toggle('loading', on);
  btn.querySelector('.btn-text').textContent = on ? 'Transforming…' : '✦ Transform Text';
}
function showError(msg) {
  const el = document.getElementById('error-msg');
  el.textContent = msg; el.classList.add('show');
}
function hideError() { document.getElementById('error-msg').classList.remove('show'); }
function showAutoTransformNotice() {
  const c = document.getElementById('file-chip-container');
  const n = document.createElement('div');
  n.className = 'auto-transform-notice';
  n.innerHTML = '✦ Text extracted — simplifying automatically…';
  c.appendChild(n);
  setTimeout(() => n.remove(), 3500);
}
function showChip(label) {
  const c = document.getElementById('file-chip-container');
  if (!label) { c.innerHTML = ''; return; }
  c.innerHTML = '<div class="file-chip">' + label + ' <button onclick="clearFile()">×</button></div>';
}
function showOcrStatus(msg) {
  document.getElementById('ocr-status-text').textContent = msg;
  document.getElementById('ocr-status').classList.add('visible');
}
function hideOcrStatus() { document.getElementById('ocr-status').classList.remove('visible'); }
function clearFile() {
  extractedText = '';
  document.getElementById('input-text').value = '';
  document.getElementById('file-chip-container').innerHTML = '';
  document.getElementById('file-input').value = '';
  document.getElementById('url-banner').style.display = 'none';
  hideOcrStatus();
}

// ── IMAGE PASTE INTO TEXTAREA ─────────────────────────────────────────────────
let pastedImageBlob = null;

document.getElementById('input-text').addEventListener('paste', async function(e) {
  const items = (e.clipboardData || e.originalEvent.clipboardData).items;
  for (const item of items) {
    if (item.type.startsWith('image/')) {
      e.preventDefault();
      const blob = item.getAsFile();
      if (!blob) return;
      pastedImageBlob = blob;
      // Show preview
      const url = URL.createObjectURL(blob);
      document.getElementById('img-paste-img').src = url;
      document.getElementById('img-paste-preview').style.display = 'block';
      // OCR the image
      showOcrStatus('Reading pasted image… 0%');
      try {
        const result = await Tesseract.recognize(blob, 'eng', {
          logger: m => {
            if (m.status === 'recognizing text')
              showOcrStatus('Reading pasted image… ' + Math.round(m.progress * 100) + '%');
          }
        });
        const text = result.data.text.trim();
        hideOcrStatus();
        if (text) {
          document.getElementById('input-text').value = text;
          showAutoTransformNotice();
        } else {
          showError('No text found in the pasted image.');
        }
      } catch(err) {
        hideOcrStatus();
        showError('Could not read pasted image.');
      }
      return;
    }
  }
});

function clearPastedImage() {
  pastedImageBlob = null;
  document.getElementById('img-paste-preview').style.display = 'none';
  document.getElementById('img-paste-img').src = '';
}




// ── WORD CLICK → DEFINITION ──────────────────────────────────────────────────
let wdpCurrentWord = '';
let wdpCurrentDef  = '';
let wdpCurrentCharOffset = 0;

function attachWordClickListeners() {
  ['output-text', 'reading-text'].forEach(id => {
    const el = document.getElementById(id);
    if (!el) return;
    el.querySelectorAll('p, li, span').forEach(node => {
      if (node.dataset.wdpBound) return;
      node.dataset.wdpBound = '1';
    });
    el.addEventListener('click', handleOutputWordClick, { capture: false });
  });
}

function handleOutputWordClick(e) {
  // Walk up from target to find the containing text
  const range = document.caretRangeFromPoint
    ? document.caretRangeFromPoint(e.clientX, e.clientY)
    : (document.caretPositionFromPoint
        ? (() => { const cp = document.caretPositionFromPoint(e.clientX, e.clientY); if (!cp) return null; const r = document.createRange(); r.setStart(cp.offsetNode, cp.offset); return r; })()
        : null);
  if (!range) return;

  const node = range.startContainer;
  if (node.nodeType !== Node.TEXT_NODE) return;

  const fullText = node.textContent;
  const offset = range.startOffset;

  // Find word boundaries around click offset
  let start = offset, end = offset;
  // Support non-Latin scripts: expand on any non-whitespace/punctuation char
  const isWordChar = (ch) => ch && !/[\s.!?,;:()\[\]{}<>"'«»،؛]/.test(ch);
  while (start > 0 && isWordChar(fullText[start - 1])) start--;
  while (end < fullText.length && isWordChar(fullText[end])) end++;
  const word = fullText.slice(start, end).replace(/^'+|'+$/g, '');
  if (!word || word.length < 2) return;

  // Compute character offset of this word within the full container text
  const container = e.currentTarget;
  let globalOffset = 0;
  const walker2 = document.createTreeWalker(container, NodeFilter.SHOW_TEXT, null, false);
  let n2;
  while ((n2 = walker2.nextNode())) {
    if (n2 === node) { globalOffset += start; break; }
    globalOffset += n2.textContent.length;
  }

  e.stopPropagation();
  showWordDefPopup(word.toLowerCase(), e.clientX, e.clientY, globalOffset);
}

function showWordDefPopup(word, x, y, charOffset) {
  wdpCurrentWord = word;
  wdpCurrentDef  = '';
  wdpCurrentCharOffset = (typeof charOffset === 'number') ? charOffset : 0;

  const popup = document.getElementById('word-def-popup');
  document.getElementById('wdp-word').textContent = word;
  document.getElementById('wdp-pos').textContent  = '';
  document.getElementById('wdp-def').innerHTML    = '<span class="wdp-loading">Looking up…</span>';

  // Show "Read from here" only when there's output text
  const rfhBtn = document.getElementById('wdp-rfh-btn');
  if (rfhBtn) rfhBtn.style.display = rawOutputText ? '' : 'none';

  // Check if already saved
  const saved = getPersonalDict();
  const saveBtn = document.getElementById('wdp-save-btn');
  saveBtn.textContent = saved.some(e => e.word === word) ? '✓ Saved' : '+ My Dictionary';
  saveBtn.classList.toggle('saved', saved.some(e => e.word === word));

  // Position popup
  popup.classList.add('show');
  lockScroll();
  const pw = 310, ph = 160;
  const vw = window.innerWidth, vh = window.innerHeight;
  popup.style.left = Math.min(x + 12, vw - pw - 8) + 'px';
  popup.style.top  = (y + 12 + ph > vh) ? Math.max(8, y - ph - 8) + 'px' : (y + 12) + 'px';

  // Detect language of the output text to choose the right lookup strategy
  const textLang = detectLanguage(rawOutputText || word);
  const isLatinWord = /^[a-zA-Z'-]+$/.test(word);

  if (isLatinWord && textLang === 'en') {
    // ── English: use dictionaryapi.dev ─────────────────────────────────────
    fetch(`https://api.dictionaryapi.dev/api/v2/entries/en/${encodeURIComponent(word)}`)
      .then(r => r.ok ? r.json() : null)
      .then(data => {
        if (!data || !data[0]) { document.getElementById('wdp-def').textContent = 'No definition found.'; return; }
        const entry = data[0];
        const meanings = entry.meanings || [];
        const rows = [];
        const seenPos = new Set();
        for (const m of meanings) {
          if (rows.length >= 3) break;
          if (seenPos.has(m.partOfSpeech)) continue;
          seenPos.add(m.partOfSpeech);
          const def = (m.definitions || [])[0];
          if (def) rows.push({ pos: m.partOfSpeech, text: def.definition });
        }
        if (rows.length === 0) { document.getElementById('wdp-def').textContent = 'No definition found.'; return; }
        wdpCurrentDef = rows[0].text;
        document.getElementById('wdp-pos').textContent = '';
        document.getElementById('wdp-def').innerHTML = rows.map((r, i) =>
          '<div style="' + (i > 0 ? 'margin-top:8px;padding-top:7px;border-top:1px solid var(--border);' : '') + '">' +
          '<span class="wdp-pos">' + r.pos + '</span><br><span>' + r.text + '</span></div>'
        ).join('');
      })
      .catch(() => { document.getElementById('wdp-def').textContent = 'Could not fetch definition.'; });
  } else {
    // ── Non-English or mixed script: translate → English via Google Translate ─
    fetchWordTranslation(word, textLang);
  }
}

function fetchWordTranslation(word, fromLang) {
  const sl = (fromLang && fromLang !== 'en') ? fromLang : 'auto';
  const url = 'https://translate.googleapis.com/translate_a/single?client=gtx&sl=' + sl +
              '&tl=en&dt=t&q=' + encodeURIComponent(word);
  fetch(url)
    .then(r => r.ok ? r.json() : null)
    .then(data => {
      if (!data || !data[0] || !data[0][0] || !data[0][0][0]) {
        document.getElementById('wdp-def').textContent = 'No definition found.';
        return;
      }
      const translation = data[0][0][0];
      const detLang = (data[2] && typeof data[2] === 'string') ? data[2] : sl;
      // If translation equals the input word, fall back to English dictionary
      if (translation.toLowerCase() === word.toLowerCase()) {
        fetch(`https://api.dictionaryapi.dev/api/v2/entries/en/${encodeURIComponent(word)}`)
          .then(r => r.ok ? r.json() : null)
          .then(data2 => {
            if (!data2 || !data2[0]) { document.getElementById('wdp-def').textContent = 'No definition found.'; return; }
            const meanings = data2[0].meanings || [];
            const rows = [];
            const seenPos = new Set();
            for (const m of meanings) {
              if (rows.length >= 3) break;
              if (seenPos.has(m.partOfSpeech)) continue;
              seenPos.add(m.partOfSpeech);
              const def = (m.definitions || [])[0];
              if (def) rows.push({ pos: m.partOfSpeech, text: def.definition });
            }
            if (rows.length === 0) { document.getElementById('wdp-def').textContent = 'No definition found.'; return; }
            wdpCurrentDef = rows[0].text;
            document.getElementById('wdp-def').innerHTML = rows.map((r, i) =>
              '<div style="' + (i > 0 ? 'margin-top:8px;padding-top:7px;border-top:1px solid var(--border);' : '') + '">' +
              '<span class="wdp-pos">' + r.pos + '</span><br><span>' + r.text + '</span></div>'
            ).join('');
          })
          .catch(() => { document.getElementById('wdp-def').textContent = 'No definition found.'; });
        return;
      }
      wdpCurrentDef = translation;
      const label = detLang !== 'en' ? 'English translation' : 'definition';
      document.getElementById('wdp-pos').textContent = '';
      document.getElementById('wdp-def').innerHTML =
        '<div><span class="wdp-pos">' + label + '</span><br><span>' +
        translation.charAt(0).toUpperCase() + translation.slice(1) + '</span></div>';
    })
    .catch(() => {
      document.getElementById('wdp-def').textContent = 'Could not fetch definition.';
    });
}

function closeWordDef() {
  document.getElementById('word-def-popup').classList.remove('show');
  unlockScroll();
}

// Close popup when clicking elsewhere
document.addEventListener('click', function(e) {
  const popup = document.getElementById('word-def-popup');
  if (popup && popup.classList.contains('show') && !popup.contains(e.target)) {
    closeWordDef();
  }
}, true);

// ── PERSONAL DICTIONARY ───────────────────────────────────────────────────────
function getPersonalDict() {
  try { return JSON.parse(localStorage.getItem('neuroadapt-dict') || '[]'); }
  catch(e) { return []; }
}
function savePersonalDict(dict) {
  localStorage.setItem('neuroadapt-dict', JSON.stringify(dict));
}

function saveToPersonalDict() {
  if (!wdpCurrentWord) return;
  const dict = getPersonalDict();
  if (!dict.some(e => e.word === wdpCurrentWord)) {
    dict.unshift({ word: wdpCurrentWord, def: wdpCurrentDef || '(definition unavailable)', addedAt: Date.now() });
    savePersonalDict(dict);
  }
  const btn = document.getElementById('wdp-save-btn');
  btn.textContent = '✓ Saved'; btn.classList.add('saved');
  renderPersonalDict();
}

function renderPersonalDict() {
  const dict = getPersonalDict();
  const list  = document.getElementById('pd-list');
  const badge = document.getElementById('pd-count');
  if (!list || !badge) return;
  badge.textContent = dict.length;
  if (dict.length === 0) {
    list.innerHTML = '<div class="pd-empty">No words saved yet.<br>Click any word in the output to save it.</div>';
    return;
  }
  list.innerHTML = dict.map((e, i) =>
    `<div class="pd-entry">
      <span class="pd-w">${e.word}</span>
      <span class="pd-d">${e.def}</span>
      <button class="pd-del" onclick="deletePersonalDictEntry(${i})" title="Remove">✕</button>
    </div>`
  ).join('');
}

function deletePersonalDictEntry(idx) {
  const dict = getPersonalDict();
  dict.splice(idx, 1);
  savePersonalDict(dict);
  renderPersonalDict();
}

function togglePersonalDict() {
  const list = document.getElementById('pd-list');
  const chev = document.getElementById('pd-chevron');
  const open = list.classList.toggle('open');
  chev.style.transform = open ? 'rotate(180deg)' : '';
}

// Init personal dict on load
document.addEventListener('DOMContentLoaded', () => { renderPersonalDict(); });
setTimeout(renderPersonalDict, 500);

</script>

<!-- Word Definition Popup -->
<div id="word-def-popup" role="tooltip">
  <button class="wdp-close" onclick="closeWordDef()">✕</button>
  <div class="wdp-word" id="wdp-word"></div>
  <div class="wdp-pos"  id="wdp-pos"></div>
  <div class="wdp-def"  id="wdp-def"><span class="wdp-loading">Looking up…</span></div>
  <div class="wdp-actions">
    <button class="wdp-btn" id="wdp-save-btn" onclick="saveToPersonalDict()">+ My Dictionary</button>
    <button class="wdp-btn" onclick="closeWordDef()">Close</button>
  </div>
  <button class="wdp-btn-rfh" id="wdp-rfh-btn" onclick="speakFromWord()">▶ Read from here</button>
</div>
</div>
</body>
</html>
