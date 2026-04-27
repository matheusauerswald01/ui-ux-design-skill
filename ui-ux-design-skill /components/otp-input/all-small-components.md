# Componentes: OTP Input, Color Picker, Signature, Panels, Notifications

---

## 1. OTP / PIN INPUT

```jsx
function OTPInput({ length = 6, onComplete, type = "number", disabled }) {
  const [values, setValues] = useState(Array(length).fill(""));
  const refs = useRef([]);

  const handleChange = (idx, val) => {
    const digit = val.replace(/\D/g, "").slice(-1);
    const next = [...values];
    next[idx] = digit;
    setValues(next);

    if (digit && idx < length - 1) refs.current[idx + 1]?.focus();
    if (next.every(v => v !== "")) onComplete?.(next.join(""));
  };

  const handleKeyDown = (idx, e) => {
    if (e.key === "Backspace" && !values[idx] && idx > 0) {
      refs.current[idx - 1]?.focus();
      const next = [...values]; next[idx - 1] = ""; setValues(next);
    }
    if (e.key === "ArrowLeft" && idx > 0) refs.current[idx - 1]?.focus();
    if (e.key === "ArrowRight" && idx < length - 1) refs.current[idx + 1]?.focus();
  };

  const handlePaste = (e) => {
    e.preventDefault();
    const pasted = e.clipboardData.getData("text").replace(/\D/g, "").slice(0, length);
    const next = [...values];
    pasted.split("").forEach((c, i) => { next[i] = c; });
    setValues(next);
    refs.current[Math.min(pasted.length, length - 1)]?.focus();
    if (pasted.length === length) onComplete?.(pasted);
  };

  return (
    <div
      className="otp-wrapper"
      role="group"
      aria-label={`Código de ${length} dígitos`}
      onPaste={handlePaste}
    >
      {values.map((val, i) => (
        <input
          key={i}
          ref={el => refs.current[i] = el}
          type={type === "password" ? "password" : "text"}
          inputMode={type === "number" ? "numeric" : "text"}
          maxLength={1}
          value={val}
          onChange={e => handleChange(i, e.target.value)}
          onKeyDown={e => handleKeyDown(i, e)}
          className={`otp-input ${val ? "otp-input--filled" : ""}`}
          disabled={disabled}
          aria-label={`Dígito ${i + 1} de ${length}`}
          autoComplete={i === 0 ? "one-time-code" : "off"}
        />
      ))}
    </div>
  );
}
```

```css
.otp-wrapper { display: flex; gap: 0.5rem; }
.otp-input {
  width: 52px; height: 56px; text-align: center; font-size: 1.5rem; font-weight: 600;
  background: var(--color-surface); border: 1.5px solid var(--color-border);
  border-radius: var(--radius-md); color: var(--color-text-primary);
  transition: border-color var(--motion-fast), box-shadow var(--motion-fast);
  caret-color: var(--color-primary); outline: none;
  font-variant-numeric: tabular-nums;
}
.otp-input:focus { border-color: var(--color-primary); box-shadow: 0 0 0 3px rgba(99,102,241,0.15); }
.otp-input--filled { border-color: var(--color-primary); }
.otp-input:disabled { opacity: 0.5; cursor: not-allowed; }

/* Shake animation em erro */
.otp-wrapper.is-error { animation: shake 0.4s ease; }
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-6px); }
  75% { transform: translateX(6px); }
}
@media (max-width: 400px) { .otp-input { width: 44px; height: 48px; font-size: 1.25rem; } }
```

---

## 2. COLOR PICKER

```jsx
function ColorPicker({ value = "#6366f1", onChange }) {
  const [hex, setHex] = useState(value);
  const [mode, setMode] = useState("hex"); // hex | rgb | hsl
  const satBrightRef = useRef(null);
  const hueRef = useRef(null);

  const parseHex = (h) => {
    const r = parseInt(h.slice(1,3), 16);
    const g = parseInt(h.slice(3,5), 16);
    const b = parseInt(h.slice(5,7), 16);
    return { r, g, b };
  };

  const hexToHSL = (h) => {
    let { r, g, b } = parseHex(h);
    r /= 255; g /= 255; b /= 255;
    const max = Math.max(r, g, b), min = Math.min(r, g, b);
    let h2, s, l = (max + min) / 2;
    if (max === min) { h2 = s = 0; }
    else {
      const d = max - min;
      s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
      switch(max) {
        case r: h2 = ((g - b) / d + (g < b ? 6 : 0)) / 6; break;
        case g: h2 = ((b - r) / d + 2) / 6; break;
        case b: h2 = ((r - g) / d + 4) / 6; break;
      }
    }
    return { h: Math.round(h2 * 360), s: Math.round(s * 100), l: Math.round(l * 100) };
  };

  const contrast = (hex) => {
    const { r, g, b } = parseHex(hex || "#000000");
    const lum = (c) => { c /= 255; return c <= 0.03928 ? c/12.92 : Math.pow((c+0.055)/1.055, 2.4); };
    const rel = 0.2126*lum(r) + 0.7152*lum(g) + 0.0722*lum(b);
    const white = (1 + 0.05) / (rel + 0.05);
    const black = (rel + 0.05) / (0 + 0.05);
    return { vsWhite: white.toFixed(1), vsBlack: black.toFixed(1) };
  };

  const hsl = hexToHSL(hex || "#6366f1");
  const contrastRatios = contrast(hex);

  const handleHexInput = (e) => {
    const v = e.target.value;
    setHex(v);
    if (/^#[0-9A-Fa-f]{6}$/.test(v)) onChange?.(v);
  };

  // EyeDropper API
  const pickFromScreen = async () => {
    if (!("EyeDropper" in window)) return alert("EyeDropper não suportado neste browser");
    try {
      const dropper = new window.EyeDropper();
      const result = await dropper.open();
      setHex(result.sRGBHex);
      onChange?.(result.sRGBHex);
    } catch {}
  };

  return (
    <div className="cp-wrapper" aria-label="Seletor de cor">
      {/* Saturation/Brightness 2D */}
      <div
        ref={satBrightRef}
        className="cp-sb"
        style={{ background: `hsl(${hsl.h}, 100%, 50%)` }}
        role="slider"
        aria-label="Saturação e brilho"
        tabIndex={0}
      >
        <div className="cp-sb-white" />
        <div className="cp-sb-black" />
        <div className="cp-sb-cursor" style={{ left: `${hsl.s}%`, top: `${100 - hsl.l}%` }} />
      </div>

      {/* Hue slider */}
      <input
        ref={hueRef}
        type="range" min={0} max={360}
        value={hsl.h}
        className="cp-hue"
        aria-label={`Matiz: ${hsl.h}°`}
      />

      {/* Alpha slider */}
      <input type="range" min={0} max={100} defaultValue={100} className="cp-alpha"
             aria-label="Opacidade" />

      {/* Inputs */}
      <div className="cp-inputs">
        <div className="cp-mode-tabs" role="tablist">
          {["hex", "rgb", "hsl"].map(m => (
            <button key={m} role="tab" aria-selected={mode === m}
                    className={`cp-mode-tab ${mode === m ? "active" : ""}`}
                    onClick={() => setMode(m)}>
              {m.toUpperCase()}
            </button>
          ))}
        </div>
        {mode === "hex" && (
          <input type="text" value={hex} onChange={handleHexInput}
                 className="cp-hex-input" aria-label="Valor hexadecimal da cor"
                 placeholder="#000000" maxLength={7} />
        )}
        {mode === "rgb" && (
          <div className="cp-rgb-inputs">
            {["R", "G", "B"].map(c => (
              <div key={c} className="cp-channel">
                <input type="number" min={0} max={255} className="cp-channel-input"
                       aria-label={`Canal ${c}`} />
                <label>{c}</label>
              </div>
            ))}
          </div>
        )}
      </div>

      {/* Contrast ratio */}
      <div className="cp-contrast" aria-live="polite">
        <div className={`cp-contrast-row ${parseFloat(contrastRatios.vsWhite) >= 4.5 ? "pass" : "fail"}`}>
          <span>Texto branco</span>
          <span>{contrastRatios.vsWhite}:1 {parseFloat(contrastRatios.vsWhite) >= 4.5 ? "✓ AA" : "✗"}</span>
        </div>
        <div className={`cp-contrast-row ${parseFloat(contrastRatios.vsBlack) >= 4.5 ? "pass" : "fail"}`}>
          <span>Texto preto</span>
          <span>{contrastRatios.vsBlack}:1 {parseFloat(contrastRatios.vsBlack) >= 4.5 ? "✓ AA" : "✗"}</span>
        </div>
      </div>

      {/* EyeDropper */}
      {"EyeDropper" in window && (
        <button className="cp-eyedropper" onClick={pickFromScreen}
                title="Selecionar cor da tela" aria-label="Usar conta-gotas para selecionar cor da tela">
          🔍 Conta-gotas
        </button>
      )}
    </div>
  );
}
```

```css
.cp-wrapper { width: 240px; background: var(--color-surface); border: 1px solid var(--color-border); border-radius: var(--radius-lg); padding: 0.75rem; display: flex; flex-direction: column; gap: 0.625rem; }
.cp-sb { height: 150px; border-radius: var(--radius-md); position: relative; cursor: crosshair; }
.cp-sb-white { position: absolute; inset: 0; background: linear-gradient(to right, white, transparent); border-radius: inherit; }
.cp-sb-black { position: absolute; inset: 0; background: linear-gradient(to bottom, transparent, black); border-radius: inherit; }
.cp-sb-cursor { position: absolute; width: 14px; height: 14px; border-radius: 50%; border: 2px solid white; box-shadow: 0 1px 4px rgba(0,0,0,0.4); transform: translate(-50%, -50%); pointer-events: none; }
.cp-hue { width: 100%; -webkit-appearance: none; height: 10px; border-radius: 5px; background: linear-gradient(to right, hsl(0,100%,50%), hsl(60,100%,50%), hsl(120,100%,50%), hsl(180,100%,50%), hsl(240,100%,50%), hsl(300,100%,50%), hsl(360,100%,50%)); cursor: pointer; }
.cp-hue::-webkit-slider-thumb { -webkit-appearance: none; width: 16px; height: 16px; border-radius: 50%; background: white; border: 2px solid rgba(0,0,0,0.2); box-shadow: 0 1px 4px rgba(0,0,0,0.3); }
.cp-alpha { width: 100%; height: 10px; border-radius: 5px; }
.cp-inputs { display: flex; flex-direction: column; gap: 0.375rem; }
.cp-mode-tabs { display: flex; gap: 2px; background: var(--color-surface-raised); padding: 2px; border-radius: var(--radius-sm); }
.cp-mode-tab { flex: 1; padding: 3px; border: none; background: none; cursor: pointer; font-size: 10px; font-weight: 600; color: var(--color-text-muted); border-radius: 4px; }
.cp-mode-tab.active { background: var(--color-surface); color: var(--color-text-primary); }
.cp-hex-input { width: 100%; padding: 0.375rem 0.5rem; background: var(--color-surface-raised); border: 1px solid var(--color-border); border-radius: var(--radius-sm); color: var(--color-text-primary); font-family: var(--font-mono); font-size: 0.875rem; outline: none; }
.cp-hex-input:focus { border-color: var(--color-primary); }
.cp-rgb-inputs { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 4px; }
.cp-channel { display: flex; flex-direction: column; gap: 2px; }
.cp-channel-input { width: 100%; padding: 4px; text-align: center; background: var(--color-surface-raised); border: 1px solid var(--color-border); border-radius: var(--radius-sm); font-size: 0.8rem; outline: none; color: var(--color-text-primary); }
.cp-channel label { text-align: center; font-size: 10px; color: var(--color-text-muted); }
.cp-contrast { display: flex; flex-direction: column; gap: 4px; }
.cp-contrast-row { display: flex; justify-content: space-between; font-size: 11px; padding: 4px 6px; border-radius: var(--radius-sm); }
.cp-contrast-row.pass { background: rgba(34,197,94,0.1); color: var(--color-success); }
.cp-contrast-row.fail { background: rgba(239,68,68,0.1); color: var(--color-error); }
.cp-eyedropper { width: 100%; padding: 0.375rem; background: var(--color-surface-raised); border: 1px solid var(--color-border); border-radius: var(--radius-sm); font-size: 0.8rem; cursor: pointer; color: var(--color-text-secondary); }
.cp-eyedropper:hover { background: var(--color-border); }
```

---

## 3. SIGNATURE CAPTURE

```jsx
function SignatureCapture({ onSave, width = 400, height = 160 }) {
  const canvasRef = useRef(null);
  const [isDrawing, setIsDrawing] = useState(false);
  const [isEmpty, setIsEmpty] = useState(true);
  const points = useRef([]);

  const getPoint = (e) => {
    const rect = canvasRef.current.getBoundingClientRect();
    const touch = e.touches?.[0];
    return {
      x: (touch?.clientX ?? e.clientX) - rect.left,
      y: (touch?.clientY ?? e.clientY) - rect.top,
      pressure: e.pressure ?? (touch?.force ?? 0.5),
    };
  };

  const startDraw = (e) => {
    e.preventDefault();
    setIsDrawing(true);
    setIsEmpty(false);
    const pt = getPoint(e);
    points.current = [pt];
    const ctx = canvasRef.current.getContext("2d");
    ctx.beginPath();
    ctx.moveTo(pt.x, pt.y);
  };

  const draw = (e) => {
    if (!isDrawing) return;
    e.preventDefault();
    const pt = getPoint(e);
    points.current.push(pt);
    const ctx = canvasRef.current.getContext("2d");
    const prev = points.current[points.current.length - 2];

    // Bézier curve para suavidade
    const mx = (prev.x + pt.x) / 2;
    const my = (prev.y + pt.y) / 2;

    ctx.lineWidth = Math.max(0.5, (pt.pressure || 0.5) * 3);
    ctx.lineCap = "round";
    ctx.lineJoin = "round";
    ctx.strokeStyle = "var(--color-text-primary, #111)";
    ctx.quadraticCurveTo(prev.x, prev.y, mx, my);
    ctx.stroke();
    ctx.beginPath();
    ctx.moveTo(mx, my);
  };

  const stopDraw = () => setIsDrawing(false);

  const clear = () => {
    const ctx = canvasRef.current.getContext("2d");
    ctx.clearRect(0, 0, width, height);
    setIsEmpty(true);
    points.current = [];
  };

  const save = () => {
    if (isEmpty) return;
    const dataURL = canvasRef.current.toDataURL("image/png");
    const svgData = canvasToSVG(points.current, width, height);
    onSave?.({ png: dataURL, svg: svgData });
  };

  return (
    <div className="sig-wrapper">
      <div className="sig-canvas-wrap">
        <canvas
          ref={canvasRef}
          width={width}
          height={height}
          className="sig-canvas"
          onMouseDown={startDraw}
          onMouseMove={draw}
          onMouseUp={stopDraw}
          onMouseLeave={stopDraw}
          onTouchStart={startDraw}
          onTouchMove={draw}
          onTouchEnd={stopDraw}
          role="img"
          aria-label="Área de assinatura — desenhe sua assinatura"
          style={{ touchAction: "none" }}
        />
        {isEmpty && (
          <div className="sig-placeholder" aria-hidden="true">
            Assine aqui
          </div>
        )}
      </div>
      <div className="sig-controls">
        <button className="btn btn-ghost btn-sm" onClick={clear} disabled={isEmpty}
                aria-label="Limpar assinatura">
          Limpar
        </button>
        <button className="btn btn-primary btn-sm" onClick={save} disabled={isEmpty}
                aria-label="Salvar assinatura">
          Salvar
        </button>
      </div>
    </div>
  );
}

function canvasToSVG(pts, w, h) {
  if (!pts.length) return "";
  const d = pts.reduce((acc, p, i) => i === 0 ? `M${p.x},${p.y}` : `L${p.x},${p.y}`, "");
  return `<svg width="${w}" height="${h}" xmlns="http://www.w3.org/2000/svg"><path d="${d}" fill="none" stroke="#111" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg>`;
}
```

```css
.sig-wrapper { display: flex; flex-direction: column; gap: 0.5rem; width: fit-content; }
.sig-canvas-wrap { position: relative; border: 1.5px solid var(--color-border); border-radius: var(--radius-md); background: var(--color-surface); overflow: hidden; }
.sig-canvas { display: block; cursor: crosshair; }
.sig-placeholder { position: absolute; inset: 0; display: flex; align-items: center; justify-content: center; color: var(--color-text-muted); font-size: 0.875rem; pointer-events: none; border-bottom: 1px dashed var(--color-border); margin: 1rem; }
.sig-controls { display: flex; justify-content: flex-end; gap: 0.5rem; }
```

---

## 4. RESIZABLE PANELS

```jsx
import { Panel, PanelGroup, PanelResizeHandle } from "react-resizable-panels";

function ResizableLayout({ children }) {
  return (
    <PanelGroup direction="horizontal" className="panel-group">
      {/* Sidebar */}
      <Panel defaultSize={20} minSize={10} maxSize={40} id="sidebar" order={1}>
        <div className="panel-content">
          {children[0]}
        </div>
      </Panel>

      <PanelResizeHandle className="panel-handle" aria-label="Redimensionar painel">
        <div className="panel-handle-bar" aria-hidden="true" />
      </PanelResizeHandle>

      {/* Main */}
      <Panel id="main" order={2}>
        <PanelGroup direction="vertical">
          <Panel defaultSize={70} minSize={30} id="editor" order={1}>
            <div className="panel-content">{children[1]}</div>
          </Panel>

          <PanelResizeHandle className="panel-handle panel-handle--horizontal"
                             aria-label="Redimensionar painel inferior">
            <div className="panel-handle-bar panel-handle-bar--horizontal" aria-hidden="true" />
          </PanelResizeHandle>

          <Panel defaultSize={30} minSize={10} id="terminal" order={2}>
            <div className="panel-content">{children[2]}</div>
          </Panel>
        </PanelGroup>
      </Panel>
    </PanelGroup>
  );
}
```

```css
.panel-group { display: flex; height: 100%; width: 100%; }
.panel-content { height: 100%; overflow: auto; background: var(--color-surface); }
.panel-handle { width: 4px; background: var(--color-border); cursor: col-resize; display: flex; align-items: center; justify-content: center; transition: background var(--motion-fast); position: relative; }
.panel-handle:hover, .panel-handle[data-resize-handle-state="drag"] { background: var(--color-primary); }
.panel-handle--horizontal { width: 100%; height: 4px; cursor: row-resize; }
.panel-handle-bar { width: 2px; height: 32px; background: rgba(255,255,255,0.2); border-radius: 1px; }
.panel-handle-bar--horizontal { width: 32px; height: 2px; }
.panel-handle:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 1px; }
```

---

## 5. NOTIFICATION CENTER

```jsx
function NotificationCenter({ notifications, onMarkRead, onClearAll }) {
  const [isOpen, setIsOpen] = useState(false);
  const unread = notifications.filter(n => !n.read).length;

  const grouped = groupByDate(notifications);

  return (
    <div className="nc-wrapper" style={{ position: "relative" }}>
      <button
        className="nc-trigger"
        onClick={() => setIsOpen(o => !o)}
        aria-label={`Notificações${unread ? ` — ${unread} não lidas` : ""}`}
        aria-haspopup="true"
        aria-expanded={isOpen}
      >
        🔔
        {unread > 0 && (
          <motion.span
            className="nc-badge"
            initial={{ scale: 0 }}
            animate={{ scale: 1 }}
            key={unread}
            aria-hidden="true"
          >
            {unread > 99 ? "99+" : unread}
          </motion.span>
        )}
      </button>

      <AnimatePresence>
        {isOpen && (
          <motion.div
            className="nc-panel"
            role="dialog"
            aria-label="Central de notificações"
            initial={{ opacity: 0, y: 8, scale: 0.97 }}
            animate={{ opacity: 1, y: 0, scale: 1 }}
            exit={{ opacity: 0, y: 8, scale: 0.97 }}
            transition={{ duration: 0.15 }}
          >
            <div className="nc-header">
              <h3 className="nc-title">Notificações</h3>
              {unread > 0 && (
                <button className="nc-mark-all" onClick={() => onMarkRead?.("all")}>
                  Marcar todas como lidas
                </button>
              )}
            </div>

            <div className="nc-list" role="list">
              {notifications.length === 0 ? (
                <div className="nc-empty" role="status">
                  <span className="nc-empty-icon" aria-hidden="true">🔔</span>
                  <p>Tudo em dia!</p>
                  <span>Sem notificações no momento.</span>
                </div>
              ) : (
                Object.entries(grouped).map(([date, items]) => (
                  <div key={date}>
                    <div className="nc-date-label">{date}</div>
                    {items.map(n => (
                      <div
                        key={n.id}
                        className={`nc-item ${!n.read ? "nc-item--unread" : ""}`}
                        role="listitem"
                        onClick={() => onMarkRead?.(n.id)}
                      >
                        <div className="nc-item-icon" aria-hidden="true">{n.icon || "📌"}</div>
                        <div className="nc-item-content">
                          <p className="nc-item-title">{n.title}</p>
                          {n.body && <p className="nc-item-body">{n.body}</p>}
                          <time className="nc-item-time" dateTime={n.createdAt}>
                            {formatTimeAgo(n.createdAt)}
                          </time>
                        </div>
                        {!n.read && <span className="nc-unread-dot" aria-label="Não lida" />}
                      </div>
                    ))}
                  </div>
                ))
              )}
            </div>
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  );
}

function groupByDate(items) {
  return items.reduce((acc, item) => {
    const d = new Date(item.createdAt);
    const today = new Date();
    const yesterday = new Date(today); yesterday.setDate(today.getDate() - 1);
    let label = d.toDateString() === today.toDateString() ? "Hoje"
              : d.toDateString() === yesterday.toDateString() ? "Ontem"
              : d.toLocaleDateString("pt-BR", { day:"2-digit", month:"long" });
    if (!acc[label]) acc[label] = [];
    acc[label].push(item);
    return acc;
  }, {});
}

function formatTimeAgo(isoDate) {
  const diff = Date.now() - new Date(isoDate).getTime();
  const m = Math.floor(diff / 60000);
  if (m < 1) return "agora";
  if (m < 60) return `${m}min atrás`;
  if (m < 1440) return `${Math.floor(m/60)}h atrás`;
  return `${Math.floor(m/1440)}d atrás`;
}
```

```css
.nc-trigger { position: relative; width: 40px; height: 40px; border-radius: 50%; border: 1px solid var(--color-border); background: var(--color-surface); color: var(--color-text-primary); cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 1.1rem; transition: background var(--motion-fast); }
.nc-trigger:hover { background: var(--color-surface-raised); }
.nc-trigger:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 2px; }
.nc-badge { position: absolute; top: -4px; right: -4px; min-width: 18px; height: 18px; background: var(--color-error); color: white; border-radius: 9px; font-size: 10px; font-weight: 700; display: flex; align-items: center; justify-content: center; padding: 0 4px; border: 2px solid var(--color-bg); }
.nc-panel { position: absolute; right: 0; top: calc(100% + 8px); width: min(380px, 100vw - 2rem); max-height: 480px; background: var(--color-surface); border: 1px solid var(--color-border); border-radius: var(--radius-lg); box-shadow: var(--shadow-xl); z-index: var(--z-overlay, 200); display: flex; flex-direction: column; overflow: hidden; }
.nc-header { display: flex; align-items: center; justify-content: space-between; padding: 1rem 1.25rem; border-bottom: 1px solid var(--color-border); }
.nc-title { font-size: 1rem; font-weight: 600; }
.nc-mark-all { font-size: 0.8rem; color: var(--color-primary); background: none; border: none; cursor: pointer; }
.nc-list { overflow-y: auto; flex: 1; }
.nc-date-label { padding: 0.5rem 1.25rem 0.25rem; font-size: 11px; font-weight: 600; color: var(--color-text-muted); text-transform: uppercase; letter-spacing: 0.05em; }
.nc-item { display: flex; align-items: flex-start; gap: 0.75rem; padding: 0.875rem 1.25rem; cursor: pointer; transition: background var(--motion-fast); position: relative; }
.nc-item:hover { background: var(--color-surface-raised); }
.nc-item--unread { background: rgba(99,102,241,0.05); }
.nc-item-icon { font-size: 1.25rem; flex-shrink: 0; margin-top: 2px; }
.nc-item-title { font-size: 0.875rem; font-weight: 500; line-height: 1.4; }
.nc-item-body { font-size: 0.8rem; color: var(--color-text-secondary); margin-top: 2px; line-height: 1.4; }
.nc-item-time { font-size: 0.75rem; color: var(--color-text-muted); margin-top: 4px; display: block; }
.nc-unread-dot { width: 8px; height: 8px; border-radius: 50%; background: var(--color-primary); flex-shrink: 0; margin-top: 6px; margin-left: auto; }
.nc-empty { display: flex; flex-direction: column; align-items: center; padding: 3rem 1.5rem; text-align: center; color: var(--color-text-muted); gap: 0.5rem; }
.nc-empty-icon { font-size: 2rem; opacity: 0.4; }
.nc-empty p { font-weight: 500; color: var(--color-text-secondary); }
```
