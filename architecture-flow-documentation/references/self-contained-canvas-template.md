# Self-Contained Canvas Architecture Template

Use this skeleton as the starting shape for generated HTML. Replace the data in `diagram` first, then adjust colors and layout.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Architecture Flow</title>
  <style>
    :root {
      color-scheme: dark;
      --bg: #0f172a;
      --panel: #1e293b;
      --panel-2: #0f172a;
      --line: #334155;
      --text: #f1f5f9;
      --muted: #94a3b8;
      --accent: #3b82f6;
    }
    * { box-sizing: border-box; }
    body {
      margin: 0;
      min-height: 100vh;
      background: var(--bg);
      color: var(--text);
      font-family: "Segoe UI", system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
    }
    #main-container { display: flex; height: 100vh; overflow: hidden; }
    #sidebar { width: 340px; background: var(--panel); border-right: 1px solid var(--line); overflow-y: auto; }
    .sidebar-header { position: sticky; top: 0; z-index: 2; padding: 24px; background: var(--panel-2); border-bottom: 1px solid var(--line); }
    .sidebar-header h2 { margin: 0; font-size: 13px; letter-spacing: .08em; text-transform: uppercase; color: #cbd5e1; }
    .sidebar-header p { margin: 6px 0 0; font-size: 12px; color: #64748b; }
    #step-list { list-style: none; margin: 0; padding: 0; }
    .phase-divider { padding: 12px 24px; background: var(--panel-2); border-bottom: 1px solid var(--line); color: #93c5fd; font-size: 11px; font-weight: 800; letter-spacing: .08em; text-transform: uppercase; }
    .step-item { padding: 15px 24px; border-bottom: 1px solid var(--line); cursor: pointer; transition: background .18s ease, border-color .18s ease; }
    .step-item:hover { background: #334155; }
    .step-item.active { background: rgba(59,130,246,.18); border-left: 4px solid var(--accent); }
    .step-number { margin-bottom: 4px; font-size: 11px; font-weight: 800; text-transform: uppercase; color: #60a5fa; }
    .step-title { margin-bottom: 4px; font-size: 14px; font-weight: 750; }
    .step-desc { font-size: 12px; line-height: 1.45; color: var(--muted); }
    #content-area { display: flex; flex: 1; min-width: 0; flex-direction: column; }
    #view-container { position: relative; flex: 1; min-height: 0; overflow: hidden; }
    .view { position: absolute; inset: 0; transition: opacity .25s ease; }
    .hidden-view { opacity: 0; pointer-events: none; }
    canvas { display: block; width: 100%; height: 100%; }
    #technical-view { overflow: auto; background: #f8fafc; color: #0f172a; padding: 32px; }
    #technical-view pre { max-width: 1100px; margin: 0 auto; padding: 24px; border: 1px solid #cbd5e1; border-radius: 8px; background: white; white-space: pre-wrap; }
    #controls { height: 82px; display: flex; align-items: center; justify-content: space-between; gap: 20px; padding: 0 24px; background: var(--panel); border-top: 1px solid var(--line); }
    .left-controls, .seq-controls { display: flex; align-items: center; gap: 10px; }
    button { border: 0; border-radius: 6px; background: #334155; color: #cbd5e1; padding: 9px 12px; font-weight: 750; cursor: pointer; }
    button:hover { background: #475569; color: white; }
    button:disabled { opacity: .35; cursor: default; }
    button.active { background: var(--accent); color: white; }
    #btnPlay { width: 42px; height: 42px; border-radius: 999px; background: var(--accent); color: white; }
    #step-description { min-width: 320px; max-width: 560px; padding: 12px 14px; border: 1px solid #1e293b; border-radius: 6px; background: rgba(15,23,42,.82); color: var(--muted); font-size: 12px; line-height: 1.35; }
    @media (max-width: 900px) {
      #main-container { flex-direction: column; }
      #sidebar { width: 100%; max-height: 34vh; border-right: 0; border-bottom: 1px solid var(--line); }
      #controls { height: auto; min-height: 96px; flex-wrap: wrap; align-items: stretch; padding: 12px; }
      #step-description { max-width: none; width: 100%; }
    }
  </style>
</head>
<body>
  <div id="main-container">
    <aside id="sidebar">
      <div class="sidebar-header">
        <h2 id="sidebar-title">Architecture Flow</h2>
        <p id="sidebar-subtitle">Interactive system walkthrough</p>
      </div>
      <ul id="step-list"></ul>
    </aside>
    <main id="content-area">
      <div id="view-container">
        <section id="canvas-view" class="view"><canvas id="archCanvas"></canvas></section>
        <section id="technical-view" class="view hidden-view"><pre id="technical-source"></pre></section>
      </div>
      <footer id="controls">
        <div class="left-controls">
          <button id="tabCanvas" class="active">Beautiful Flow</button>
          <button id="tabTechnical">Technical Spec</button>
          <strong id="page-heading">Architecture</strong>
        </div>
        <div class="seq-controls">
          <button id="btnArch">Static View</button>
          <button id="btnPrev" disabled>Prev</button>
          <button id="btnPlay">Play</button>
          <button id="btnNext">Next</button>
        </div>
        <div id="step-description">Mode: Static Architecture Overview</div>
      </footer>
    </main>
  </div>

  <script>
    const diagram = {
      title: "Architecture",
      subtitle: "Interactive system walkthrough",
      accent: "#3b82f6",
      logicalWidth: 1500,
      logicalHeight: 850,
      zones: [],
      nodes: {},
      staticConnections: [],
      sequenceSteps: [],
      technicalSource: "sequenceDiagram\\n    autonumber"
    };

    const canvas = document.getElementById("archCanvas");
    const ctx = canvas.getContext("2d");
    let mode = "arch";
    let currentStep = -1;
    let isPlaying = false;
    let progress = 0;
    let animationStart = 0;
    let scale = 1;
    let offsetX = 0;
    let offsetY = 0;

    document.documentElement.style.setProperty("--accent", diagram.accent || "#3b82f6");
    document.getElementById("sidebar-title").textContent = diagram.title;
    document.getElementById("sidebar-subtitle").textContent = diagram.subtitle || "";
    document.getElementById("page-heading").textContent = diagram.title;
    document.getElementById("technical-source").textContent = diagram.technicalSource || "";

    function populateSidebar() {
      const list = document.getElementById("step-list");
      let lastPhase = "";
      diagram.sequenceSteps.forEach((step, index) => {
        if (step.phase && step.phase !== lastPhase) {
          const divider = document.createElement("li");
          divider.className = "phase-divider";
          divider.textContent = step.phase;
          list.appendChild(divider);
          lastPhase = step.phase;
        }
        const item = document.createElement("li");
        item.className = "step-item";
        item.id = `step-item-${index}`;
        item.innerHTML = `<div class="step-number">Step ${index}</div><div class="step-title">${escapeHtml(step.label)}</div><div class="step-desc">${escapeHtml(step.desc)}</div>`;
        item.addEventListener("click", () => jumpToStep(index));
        list.appendChild(item);
      });
    }

    function escapeHtml(value) {
      return String(value).replace(/[&<>"']/g, (char) => ({ "&": "&amp;", "<": "&lt;", ">": "&gt;", '"': "&quot;", "'": "&#39;" }[char]));
    }

    function resize() {
      const rect = canvas.parentElement.getBoundingClientRect();
      const dpr = window.devicePixelRatio || 1;
      canvas.width = Math.max(1, Math.floor(rect.width * dpr));
      canvas.height = Math.max(1, Math.floor(rect.height * dpr));
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
      scale = Math.min(rect.width / diagram.logicalWidth, rect.height / diagram.logicalHeight) * 0.9;
      offsetX = (rect.width - diagram.logicalWidth * scale) / 2;
      offsetY = (rect.height - diagram.logicalHeight * scale) / 2;
      draw();
    }

    function roundedRect(x, y, w, h, r, fill, stroke, dash = []) {
      ctx.beginPath();
      ctx.moveTo(x + r, y);
      ctx.lineTo(x + w - r, y);
      ctx.quadraticCurveTo(x + w, y, x + w, y + r);
      ctx.lineTo(x + w, y + h - r);
      ctx.quadraticCurveTo(x + w, y + h, x + w - r, y + h);
      ctx.lineTo(x + r, y + h);
      ctx.quadraticCurveTo(x, y + h, x, y + h - r);
      ctx.lineTo(x, y + r);
      ctx.quadraticCurveTo(x, y, x + r, y);
      ctx.closePath();
      ctx.setLineDash(dash);
      if (fill) { ctx.fillStyle = fill; ctx.fill(); }
      if (stroke) { ctx.strokeStyle = stroke; ctx.lineWidth = 2; ctx.stroke(); }
      ctx.setLineDash([]);
    }

    function connectionPoints(from, to) {
      const a = diagram.nodes[from];
      const b = diagram.nodes[to];
      let p1 = { x: a.x + a.w / 2, y: a.y + a.h / 2 };
      let p2 = { x: b.x + b.w / 2, y: b.y + b.h / 2 };
      if (a.x + a.w < b.x) { p1.x = a.x + a.w; p2.x = b.x; }
      else if (b.x + b.w < a.x) { p1.x = a.x; p2.x = b.x + b.w; }
      else if (a.y + a.h < b.y) { p1.y = a.y + a.h; p2.y = b.y; }
      else if (b.y + b.h < a.y) { p1.y = a.y; p2.y = b.y + b.h; }
      return [p1, p2];
    }

    function drawLine(x1, y1, x2, y2, color, label, glow) {
      const midX = (x1 + x2) / 2;
      ctx.beginPath();
      ctx.moveTo(x1, y1);
      ctx.bezierCurveTo(midX, y1, midX, y2, x2, y2);
      ctx.strokeStyle = color;
      ctx.lineWidth = glow ? 4 : 1;
      ctx.shadowBlur = glow ? 12 : 0;
      ctx.shadowColor = color;
      ctx.stroke();
      ctx.shadowBlur = 0;
      const angle = Math.atan2(y2 - y1, x2 - x1);
      ctx.beginPath();
      ctx.moveTo(x2, y2);
      ctx.lineTo(x2 - 8 * Math.cos(angle - Math.PI / 6), y2 - 8 * Math.sin(angle - Math.PI / 6));
      ctx.lineTo(x2 - 8 * Math.cos(angle + Math.PI / 6), y2 - 8 * Math.sin(angle + Math.PI / 6));
      ctx.closePath();
      ctx.fillStyle = color;
      ctx.fill();
      if (label) {
        ctx.fillStyle = "#94a3b8";
        ctx.font = "500 11px sans-serif";
        ctx.textAlign = "center";
        ctx.fillText(label, midX, (y1 + y2) / 2 - 12);
      }
    }

    function draw() {
      const rect = canvas.parentElement.getBoundingClientRect();
      ctx.clearRect(0, 0, rect.width, rect.height);
      ctx.save();
      ctx.translate(offsetX, offsetY);
      ctx.scale(scale, scale);

      diagram.zones.forEach((zone) => {
        roundedRect(zone.x, zone.y, zone.w, zone.h, 20, zone.color, zone.border, [15, 8]);
        ctx.fillStyle = zone.border;
        ctx.font = "bold 16px sans-serif";
        ctx.textAlign = "left";
        ctx.fillText(zone.title, zone.x + 20, zone.y + 28);
      });

      const connections = mode === "arch"
        ? diagram.staticConnections
        : diagram.staticConnections.map((item) => [item[0], item[1], ""]);
      connections.forEach(([from, to, label]) => {
        const [p1, p2] = connectionPoints(from, to);
        drawLine(p1.x, p1.y, p2.x, p2.y, mode === "arch" ? "#334155" : "#1e293b", label, false);
      });

      Object.entries(diagram.nodes).forEach(([id, node]) => {
        const focused = mode === "seq" && currentStep >= 0 && [diagram.sequenceSteps[currentStep].from, diagram.sequenceSteps[currentStep].to].includes(id);
        ctx.save();
        ctx.globalAlpha = mode === "seq" ? (focused ? 1 : 0.28) : 1;
        ctx.shadowColor = focused ? node.border : "rgba(0,0,0,.45)";
        ctx.shadowBlur = focused ? 20 : 10;
        roundedRect(node.x, node.y, node.w, node.h, 12, node.color || "#1e293b", node.border || "#475569");
        ctx.shadowBlur = 0;
        ctx.fillStyle = node.textColor || "#f8fafc";
        ctx.font = "bold 14px sans-serif";
        ctx.textAlign = "center";
        ctx.fillText(node.title, node.x + node.w / 2, node.y + node.h / 2 - 5);
        ctx.fillStyle = "#94a3b8";
        ctx.font = "500 11px sans-serif";
        ctx.fillText(node.subtitle || "", node.x + node.w / 2, node.y + node.h / 2 + 14);
        ctx.restore();
      });

      if (mode === "seq" && currentStep >= 0) {
        const step = diagram.sequenceSteps[currentStep];
        const [p1, p2] = connectionPoints(step.from, step.to);
        drawLine(p1.x, p1.y, p2.x, p2.y, step.color || diagram.accent, "", true);
        const x = p1.x + (p2.x - p1.x) * progress;
        const y = p1.y + (p2.y - p1.y) * progress;
        ctx.beginPath();
        ctx.arc(x, y, 6, 0, Math.PI * 2);
        ctx.fillStyle = step.color || diagram.accent;
        ctx.shadowBlur = 16;
        ctx.shadowColor = step.color || diagram.accent;
        ctx.fill();
        ctx.shadowBlur = 0;
      }
      ctx.restore();
    }

    function animate(timestamp) {
      if (!animationStart) animationStart = timestamp;
      progress = Math.min(1, (timestamp - animationStart) / 1500);
      draw();
      if (progress < 1 && mode === "seq") {
        requestAnimationFrame(animate);
      } else if (isPlaying) {
        currentStep = currentStep + 1 >= diagram.sequenceSteps.length ? 0 : currentStep + 1;
        animationStart = 0;
        updateUI();
        requestAnimationFrame(animate);
      }
    }

    function jumpToStep(index) {
      mode = "seq";
      isPlaying = false;
      currentStep = index;
      progress = 0;
      animationStart = 0;
      updateUI();
      requestAnimationFrame(animate);
    }

    function updateUI() {
      document.querySelectorAll(".step-item").forEach((el) => el.classList.remove("active"));
      document.getElementById("btnPrev").disabled = mode === "arch" || currentStep <= 0;
      document.getElementById("btnNext").disabled = mode === "arch" || currentStep >= diagram.sequenceSteps.length - 1;
      document.getElementById("btnPlay").textContent = isPlaying ? "Pause" : "Play";
      const description = document.getElementById("step-description");
      if (mode === "arch") {
        description.textContent = "Mode: Static Architecture Overview";
      } else {
        const step = diagram.sequenceSteps[currentStep];
        description.textContent = `Step ${currentStep}/${diagram.sequenceSteps.length - 1}: ${step.desc}`;
        const active = document.getElementById(`step-item-${currentStep}`);
        if (active) {
          active.classList.add("active");
          active.scrollIntoView({ behavior: "smooth", block: "nearest" });
        }
      }
      draw();
    }

    document.getElementById("tabCanvas").addEventListener("click", () => {
      document.getElementById("canvas-view").classList.remove("hidden-view");
      document.getElementById("technical-view").classList.add("hidden-view");
      document.getElementById("tabCanvas").classList.add("active");
      document.getElementById("tabTechnical").classList.remove("active");
      resize();
    });
    document.getElementById("tabTechnical").addEventListener("click", () => {
      document.getElementById("technical-view").classList.remove("hidden-view");
      document.getElementById("canvas-view").classList.add("hidden-view");
      document.getElementById("tabTechnical").classList.add("active");
      document.getElementById("tabCanvas").classList.remove("active");
    });
    document.getElementById("btnArch").addEventListener("click", () => {
      mode = "arch"; isPlaying = false; currentStep = -1; progress = 0; updateUI();
    });
    document.getElementById("btnPlay").addEventListener("click", () => {
      if (mode === "arch") { mode = "seq"; currentStep = 0; progress = 0; }
      isPlaying = !isPlaying;
      animationStart = 0;
      updateUI();
      if (isPlaying) requestAnimationFrame(animate);
    });
    document.getElementById("btnPrev").addEventListener("click", () => jumpToStep(Math.max(0, currentStep - 1)));
    document.getElementById("btnNext").addEventListener("click", () => jumpToStep(Math.min(diagram.sequenceSteps.length - 1, currentStep + 1)));
    window.addEventListener("resize", resize);
    populateSidebar();
    resize();
  </script>
</body>
</html>
```
