# Componente: Microinterações — Biblioteca Completa

## 1. Toggle Switch animado

```jsx
function Toggle({ checked, onChange, label, disabled }) {
  return (
    <label className={`toggle ${disabled ? 'toggle--disabled' : ''}`}>
      <span className="toggle-label">{label}</span>
      <span className="toggle-track" aria-hidden="true">
        <input
          type="checkbox"
          checked={checked}
          onChange={e => onChange(e.target.checked)}
          disabled={disabled}
          role="switch"
          aria-checked={checked}
          aria-label={label}
          style={{ position:'absolute', opacity:0, width:'100%', height:'100%', cursor:'pointer', margin:0 }}
        />
        <span className="toggle-thumb" />
      </span>
    </label>
  );
}
```

```css
.toggle {
  display: inline-flex; align-items: center; gap: 0.75rem;
  cursor: pointer; user-select: none;
}
.toggle--disabled { opacity: 0.5; pointer-events: none; }

.toggle-track {
  position: relative; width: 44px; height: 24px;
  background: var(--color-border-strong); border-radius: 12px;
  transition: background var(--motion-fast) var(--ease-smooth);
}
.toggle-track:has(input:checked) { background: var(--color-primary); }
.toggle-track:has(input:focus-visible) {
  outline: 2px solid var(--color-primary); outline-offset: 2px;
}

.toggle-thumb {
  position: absolute; top: 2px; left: 2px;
  width: 20px; height: 20px; border-radius: 50%;
  background: white;
  box-shadow: 0 1px 4px rgba(0,0,0,0.2);
  transition: transform var(--motion-fast) var(--ease-smooth);
  pointer-events: none;
}
.toggle-track:has(input:checked) .toggle-thumb { transform: translateX(20px); }
.toggle-track:has(input:active) .toggle-thumb { transform: scaleX(1.2); }
.toggle-track:has(input:checked):has(input:active) .toggle-thumb {
  transform: translateX(18px) scaleX(1.2);
}
@media (prefers-reduced-motion: reduce) {
  .toggle-track, .toggle-thumb { transition: none; }
}
```

## 2. Checkbox animado

```jsx
function Checkbox({ checked, onChange, label, indeterminate }) {
  const ref = useRef(null);
  useEffect(() => { if (ref.current) ref.current.indeterminate = indeterminate; }, [indeterminate]);

  return (
    <label className="checkbox">
      <span className="checkbox-control" aria-hidden="true">
        <input
          ref={ref}
          type="checkbox"
          checked={checked}
          onChange={e => onChange(e.target.checked)}
          style={{ position:'absolute', opacity:0, width:'100%', height:'100%', margin:0, cursor:'pointer' }}
        />
        <svg viewBox="0 0 16 16" className="checkbox-icon">
          {indeterminate ? (
            <line x1="4" y1="8" x2="12" y2="8" stroke="white" strokeWidth="2" strokeLinecap="round"/>
          ) : (
            <polyline points="3,8 6.5,11.5 13,4.5" fill="none" stroke="white"
                      strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"
                      strokeDasharray="16" strokeDashoffset={checked ? 0 : 16}
                      style={{ transition: 'stroke-dashoffset 0.2s ease' }}/>
          )}
        </svg>
      </span>
      <span className="checkbox-label">{label}</span>
    </label>
  );
}
```

```css
.checkbox { display: inline-flex; align-items: center; gap: 0.625rem; cursor: pointer; }
.checkbox-control {
  position: relative; width: 18px; height: 18px;
  border: 1.5px solid var(--color-border-strong); border-radius: 4px;
  background: var(--color-surface); flex-shrink: 0;
  transition: background var(--motion-fast), border-color var(--motion-fast),
              transform var(--motion-fast);
}
.checkbox-control:has(input:checked),
.checkbox-control:has(input:indeterminate) {
  background: var(--color-primary); border-color: var(--color-primary);
}
.checkbox-control:has(input:focus-visible) {
  outline: 2px solid var(--color-primary); outline-offset: 2px;
}
.checkbox-control:has(input:active) { transform: scale(0.9); }
.checkbox-icon { width: 100%; height: 100%; }
.checkbox-label { font-size: var(--text-sm); line-height: 1.5; }
```

## 3. Button com feedback de ação

```jsx
function ActionButton({ onClick, children, loadingText = "Processando..." }) {
  const [state, setState] = useState('idle');

  const handle = async () => {
    if (state !== 'idle') return;
    setState('loading');
    try {
      await onClick();
      setState('success');
      setTimeout(() => setState('idle'), 2000);
    } catch {
      setState('error');
      setTimeout(() => setState('idle'), 3000);
    }
  };

  return (
    <motion.button
      className={`btn btn-primary action-btn action-btn--${state}`}
      onClick={handle}
      disabled={state !== 'idle'}
      whileTap={{ scale: state === 'idle' ? 0.97 : 1 }}
      aria-live="polite"
      aria-busy={state === 'loading'}
    >
      <AnimatePresence mode="wait">
        {state === 'idle' && (
          <motion.span key="idle" initial={{opacity:0}} animate={{opacity:1}} exit={{opacity:0}}>
            {children}
          </motion.span>
        )}
        {state === 'loading' && (
          <motion.span key="loading" initial={{opacity:0}} animate={{opacity:1}} exit={{opacity:0}}
                       style={{display:'flex',alignItems:'center',gap:'0.5rem'}}>
            <Spinner size={16} /> {loadingText}
          </motion.span>
        )}
        {state === 'success' && (
          <motion.span key="success" initial={{scale:0.8,opacity:0}}
                       animate={{scale:1,opacity:1}} exit={{opacity:0}}
                       style={{display:'flex',alignItems:'center',gap:'0.5rem'}}>
            ✓ Concluído
          </motion.span>
        )}
        {state === 'error' && (
          <motion.span key="error" initial={{x:-4}} animate={{x:0}} exit={{opacity:0}}
                       style={{display:'flex',alignItems:'center',gap:'0.5rem'}}>
            ✕ Erro — Tentar novamente
          </motion.span>
        )}
      </AnimatePresence>
    </motion.button>
  );
}

function Spinner({ size = 20 }) {
  return (
    <svg width={size} height={size} viewBox="0 0 20 20" aria-hidden="true">
      <circle cx="10" cy="10" r="8" fill="none" stroke="currentColor"
              strokeWidth="2.5" strokeLinecap="round"
              strokeDasharray="30 20"
              style={{ animation:'spin 0.8s linear infinite' }}/>
      <style>{`@keyframes spin { to { transform: rotate(360deg); transform-origin: center; } }`}</style>
    </svg>
  );
}
```

## 4. Progress Bar animada

```jsx
function ProgressBar({ value, max = 100, label, showValue = true, animated = true }) {
  const percent = Math.min(100, Math.max(0, (value / max) * 100));
  const color = percent === 100 ? 'var(--color-success)'
               : percent > 60  ? 'var(--color-primary)'
               : percent > 30  ? 'var(--color-warning)'
               : 'var(--color-error)';

  return (
    <div className="progress-wrapper">
      {(label || showValue) && (
        <div className="progress-header">
          {label && <span className="progress-label">{label}</span>}
          {showValue && (
            <motion.span className="progress-value"
                         key={percent}
                         initial={{ opacity: 0, y: -4 }}
                         animate={{ opacity: 1, y: 0 }}>
              {Math.round(percent)}%
            </motion.span>
          )}
        </div>
      )}
      <div
        className="progress-track"
        role="progressbar"
        aria-valuenow={value}
        aria-valuemin={0}
        aria-valuemax={max}
        aria-label={label || 'Progresso'}
      >
        <motion.div
          className="progress-fill"
          style={{ backgroundColor: color }}
          initial={{ width: 0 }}
          animate={{ width: `${percent}%` }}
          transition={{ duration: animated ? 0.8 : 0, ease: [0.4,0,0.2,1] }}
        >
          {/* Shimmer em progresso ativo */}
          {percent < 100 && animated && (
            <span className="progress-shimmer" aria-hidden="true" />
          )}
        </motion.div>
      </div>
    </div>
  );
}
```

```css
.progress-wrapper { display: flex; flex-direction: column; gap: 0.375rem; }
.progress-header { display: flex; justify-content: space-between; align-items: center; }
.progress-label { font-size: var(--text-sm); font-weight: 500; }
.progress-value { font-size: var(--text-sm); color: var(--color-text-secondary); font-variant-numeric: tabular-nums; }
.progress-track {
  height: 8px; background: var(--color-surface-raised);
  border-radius: 99px; overflow: hidden;
}
.progress-fill {
  height: 100%; border-radius: 99px;
  position: relative; overflow: hidden;
}
.progress-shimmer {
  position: absolute; inset: 0;
  background: linear-gradient(90deg, transparent 33%, rgba(255,255,255,0.3) 50%, transparent 66%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}
@media (prefers-reduced-motion: reduce) { .progress-shimmer { animation: none; } }
```

## 5. Like / Reaction button

```jsx
function LikeButton({ initialCount = 0, initialLiked = false }) {
  const [liked, setLiked] = useState(initialLiked);
  const [count, setCount] = useState(initialCount);
  const [particles, setParticles] = useState([]);

  const toggle = () => {
    const wasLiked = liked;
    setLiked(l => !l);
    setCount(c => wasLiked ? c - 1 : c + 1);

    if (!wasLiked) {
      // Partículas ao curtir
      setParticles(Array.from({ length: 6 }).map((_, i) => ({
        id: Date.now() + i,
        angle: (i / 6) * 360,
        color: ['#ef4444','#f97316','#f59e0b','#ec4899','#8b5cf6','#3b82f6'][i],
      })));
      setTimeout(() => setParticles([]), 600);
    }
  };

  return (
    <div style={{ position: 'relative', display: 'inline-flex', alignItems: 'center', gap: 6 }}>
      <motion.button
        className={`like-btn ${liked ? 'liked' : ''}`}
        onClick={toggle}
        whileTap={{ scale: 0.85 }}
        aria-pressed={liked}
        aria-label={liked ? 'Remover curtida' : 'Curtir'}
      >
        <motion.svg width="20" height="20" viewBox="0 0 20 20" aria-hidden="true">
          <motion.path
            d="M10 17.5S2.5 13 2.5 7.5a4 4 0 017.5-2 4 4 0 017.5 2C17.5 13 10 17.5 10 17.5z"
            fill={liked ? '#ef4444' : 'none'}
            stroke={liked ? '#ef4444' : 'currentColor'}
            strokeWidth="1.5"
            animate={{ scale: liked ? [1, 1.3, 1] : 1 }}
            transition={{ duration: 0.3 }}
          />
        </motion.svg>
      </motion.button>

      <motion.span
        key={count}
        className="like-count"
        initial={{ y: liked ? -8 : 8, opacity: 0 }}
        animate={{ y: 0, opacity: 1 }}
        transition={{ duration: 0.2 }}
        aria-live="polite"
      >
        {count.toLocaleString()}
      </motion.span>

      {/* Partículas */}
      {particles.map(p => (
        <motion.span
          key={p.id}
          style={{
            position:'absolute', left:'50%', top:'50%',
            width:6, height:6, borderRadius:'50%',
            background: p.color, pointerEvents:'none',
          }}
          initial={{ scale: 0, x: 0, y: 0, opacity: 1 }}
          animate={{
            scale: 1,
            x: Math.cos(p.angle * Math.PI/180) * 24,
            y: Math.sin(p.angle * Math.PI/180) * 24,
            opacity: 0,
          }}
          transition={{ duration: 0.5, ease: 'easeOut' }}
          aria-hidden="true"
        />
      ))}
    </div>
  );
}
```

```css
.like-btn {
  background: none; border: none; cursor: pointer; padding: 4px;
  border-radius: 6px; color: var(--color-text-secondary);
  transition: color var(--motion-fast), background var(--motion-fast);
}
.like-btn:hover { color: var(--color-error); background: rgba(239,68,68,0.08); }
.like-btn.liked { color: var(--color-error); }
.like-btn:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 2px; }
.like-count { font-size: var(--text-sm); font-variant-numeric: tabular-nums; color: var(--color-text-secondary); min-width: 1.5ch; }
```

## 6. Contador animado (number morphing)

```jsx
function AnimatedNumber({ value, duration = 1000, format = (n) => Math.round(n).toLocaleString() }) {
  const ref = useRef(null);
  const prevValue = useRef(0);

  useEffect(() => {
    const start = prevValue.current;
    const end = value;
    const startTime = performance.now();
    const prefersReduced = matchMedia('(prefers-reduced-motion: reduce)').matches;

    if (prefersReduced) {
      ref.current.textContent = format(end);
      prevValue.current = end;
      return;
    }

    const tick = (now) => {
      const elapsed = now - startTime;
      const progress = Math.min(elapsed / duration, 1);
      // Ease out cubic
      const ease = 1 - Math.pow(1 - progress, 3);
      const current = start + (end - start) * ease;
      if (ref.current) ref.current.textContent = format(current);
      if (progress < 1) requestAnimationFrame(tick);
      else prevValue.current = end;
    };

    requestAnimationFrame(tick);
  }, [value, duration]);

  return (
    <span ref={ref} aria-live="polite" aria-atomic="true"
          style={{ fontVariantNumeric: 'tabular-nums' }}>
      {format(value)}
    </span>
  );
}

// Uso:
// <AnimatedNumber value={42891} format={n => `$${Math.round(n).toLocaleString()}`} />
```

## 7. Tooltip acessível

```jsx
function Tooltip({ content, children, side = 'top' }) {
  const [open, setOpen] = useState(false);
  const id = useId();

  return (
    <span style={{ position:'relative', display:'inline-flex' }}
          onMouseEnter={() => setOpen(true)}
          onMouseLeave={() => setOpen(false)}
          onFocus={() => setOpen(true)}
          onBlur={() => setOpen(false)}>
      {React.cloneElement(children, { 'aria-describedby': id })}
      <AnimatePresence>
        {open && (
          <motion.span
            id={id}
            role="tooltip"
            className={`tooltip tooltip--${side}`}
            initial={{ opacity: 0, scale: 0.9, y: side === 'top' ? 4 : -4 }}
            animate={{ opacity: 1, scale: 1, y: 0 }}
            exit={{ opacity: 0, scale: 0.9 }}
            transition={{ duration: 0.15 }}
          >
            {content}
          </motion.span>
        )}
      </AnimatePresence>
    </span>
  );
}
```

```css
.tooltip {
  position: absolute; z-index: var(--z-toast);
  padding: 0.375rem 0.625rem;
  background: var(--color-text-primary); color: var(--color-bg);
  border-radius: var(--radius-sm); font-size: 12px; font-weight: 500;
  white-space: nowrap; pointer-events: none; max-width: 200px;
  white-space: normal; text-align: center; line-height: 1.4;
}
.tooltip--top    { bottom: calc(100% + 8px); left: 50%; transform: translateX(-50%); }
.tooltip--bottom { top: calc(100% + 8px);    left: 50%; transform: translateX(-50%); }
.tooltip--left   { right: calc(100% + 8px);  top: 50%;  transform: translateY(-50%); }
.tooltip--right  { left: calc(100% + 8px);   top: 50%;  transform: translateY(-50%); }
```

## 8. Drag & Drop

```jsx
function useDraggable(items, onReorder) {
  const [dragging, setDragging] = useState(null);
  const [over, setOver] = useState(null);

  const handleDragStart = (e, id) => {
    setDragging(id);
    e.dataTransfer.effectAllowed = 'move';
  };

  const handleDrop = (e, targetId) => {
    e.preventDefault();
    if (dragging === targetId) return;
    const from = items.findIndex(i => i.id === dragging);
    const to   = items.findIndex(i => i.id === targetId);
    const next = [...items];
    const [moved] = next.splice(from, 1);
    next.splice(to, 0, moved);
    onReorder(next);
    setDragging(null);
    setOver(null);
  };

  const dragProps = (id) => ({
    draggable: true,
    onDragStart: (e) => handleDragStart(e, id),
    onDragOver:  (e) => { e.preventDefault(); setOver(id); },
    onDragLeave: () => setOver(null),
    onDrop:      (e) => handleDrop(e, id),
    onDragEnd:   () => { setDragging(null); setOver(null); },
    style: {
      opacity: dragging === id ? 0.4 : 1,
      outline: over === id && over !== dragging
        ? '2px solid var(--color-primary)'
        : 'none',
      cursor: 'grab',
    },
  });

  return { dragProps, dragging };
}
```
