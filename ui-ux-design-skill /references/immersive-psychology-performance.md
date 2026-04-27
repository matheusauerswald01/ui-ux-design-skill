# Referências: Web Imersiva + Psicologia UX + Performance Avançada

---

## WEB IMERSIVA — Técnicas completas

### Cursor premium com estados e física
```jsx
// Hook de cursor com todos os estados
function usePremiumCursor() {
  const dotX = useMotionValue(-100);
  const dotY = useMotionValue(-100);
  const ringX = useSpring(useMotionValue(-100), { stiffness: 400, damping: 40 });
  const ringY = useSpring(useMotionValue(-100), { stiffness: 400, damping: 40 });
  const [state, setState] = useState("default");

  useEffect(() => {
    const move = (e) => {
      dotX.set(e.clientX);
      dotY.set(e.clientY);
      ringX.set(e.clientX);
      ringY.set(e.clientY);
    };

    const handlers = {
      "a, button, [role=button]": { enter: "link", leave: "default" },
      "video, iframe": { enter: "media", leave: "default" },
      "input, textarea": { enter: "text", leave: "default" },
      "[data-cursor-expand]": { enter: "expand", leave: "default" },
    };

    Object.entries(handlers).forEach(([sel, { enter, leave }]) => {
      document.querySelectorAll(sel).forEach(el => {
        el.addEventListener("mouseenter", () => setState(enter));
        el.addEventListener("mouseleave", () => setState(leave));
      });
    });

    window.addEventListener("mousemove", move, { passive: true });
    return () => window.removeEventListener("mousemove", move);
  }, []);

  return { dotX, dotY, ringX, ringY, state };
}

// Estilos por estado
const cursorStates = {
  default: { scale: 1, background: "rgba(255,255,255,0.9)", mixBlendMode: "normal" },
  link:    { scale: 2.5, background: "rgba(255,255,255,0.9)", mixBlendMode: "difference" },
  media:   { scale: 3, background: "rgba(0,0,0,0.6)", content: "▶" },
  text:    { scale: 0.3, background: "rgba(255,255,255,0.9)" },
  expand:  { scale: 4, background: "transparent", border: "1px solid white" },
};
```

### Aurora / Gradient mesh animado
```css
/* Aurora CSS pura — sem JS */
.aurora {
  position: fixed;
  inset: 0;
  pointer-events: none;
  overflow: hidden;
  z-index: -1;
}
.aurora-blob {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  opacity: 0.5;
  animation: aurora-float linear infinite;
}
.aurora-blob:nth-child(1) {
  width: 800px; height: 600px;
  background: radial-gradient(circle, oklch(65% 0.25 260) 0%, transparent 70%);
  top: -200px; left: -200px;
  animation-duration: 20s;
}
.aurora-blob:nth-child(2) {
  width: 600px; height: 700px;
  background: radial-gradient(circle, oklch(65% 0.20 290) 0%, transparent 70%);
  bottom: -100px; right: -100px;
  animation-duration: 17s;
  animation-direction: reverse;
}
.aurora-blob:nth-child(3) {
  width: 500px; height: 500px;
  background: radial-gradient(circle, oklch(70% 0.22 210) 0%, transparent 70%);
  top: 30%; left: 40%;
  animation-duration: 25s;
}

@keyframes aurora-float {
  0%, 100% { transform: translate(0, 0) scale(1) rotate(0deg); }
  25%       { transform: translate(80px, -60px) scale(1.1) rotate(90deg); }
  50%       { transform: translate(-60px, 80px) scale(0.9) rotate(180deg); }
  75%       { transform: translate(60px, 60px) scale(1.05) rotate(270deg); }
}

@media (prefers-reduced-motion: reduce) {
  .aurora-blob { animation: none; }
}
```

### Glitch effect
```css
/* Glitch com clip-path — texto ou imagem */
.glitch {
  position: relative;
}
.glitch::before,
.glitch::after {
  content: attr(data-text);
  position: absolute; inset: 0;
  background: var(--color-bg);
}
.glitch::before {
  animation: glitch-1 2s infinite steps(2);
  clip-path: polygon(0 0, 100% 0, 100% 35%, 0 35%);
  color: #ff0040;
}
.glitch::after {
  animation: glitch-2 2s infinite steps(2) 0.1s;
  clip-path: polygon(0 65%, 100% 65%, 100% 100%, 0 100%);
  color: #00ffff;
}

@keyframes glitch-1 {
  0%   { transform: none; }
  20%  { transform: translate(-3px, 1px); }
  40%  { transform: translate(3px, -1px); }
  60%  { transform: translate(-2px, 2px); }
  80%  { transform: none; }
}
@keyframes glitch-2 {
  0%   { transform: none; }
  15%  { transform: translate(3px, -1px); }
  35%  { transform: translate(-3px, 1px); }
  55%  { transform: translate(2px, 1px); }
  75%  { transform: none; }
}

@media (prefers-reduced-motion: reduce) {
  .glitch::before, .glitch::after { animation: none; }
}
```

### Noise & SVG Filters avançados
```html
<!-- Filtros SVG em elementos HTML -->
<svg style="display:none" aria-hidden="true">
  <defs>
    <!-- Noise distortion -->
    <filter id="noise">
      <feTurbulence type="fractalNoise" baseFrequency="0.65" numOctaves="3" stitchTiles="stitch"/>
      <feColorMatrix type="saturate" values="0"/>
      <feBlend in="SourceGraphic" mode="overlay"/>
    </filter>

    <!-- Liquid distortion -->
    <filter id="liquid">
      <feTurbulence type="turbulence" baseFrequency="0.015" numOctaves="4" seed="2">
        <animate attributeName="baseFrequency" dur="8s" values="0.01;0.02;0.01" repeatCount="indefinite"/>
      </feTurbulence>
      <feDisplacementMap in="SourceGraphic" scale="30" xChannelSelector="R" yChannelSelector="G"/>
    </filter>

    <!-- Glow -->
    <filter id="glow">
      <feGaussianBlur stdDeviation="3" result="blur"/>
      <feColorMatrix in="blur" type="matrix" values="1 0 0 0 0 0 1 0 0 0 0 0 1 0 0 0 0 0 18 -7" result="glow"/>
      <feBlend in="SourceGraphic" in2="glow"/>
    </filter>
  </defs>
</svg>
```
```css
/* Aplicar em elementos HTML */
.noisy   { filter: url(#noise); }
.liquid  { filter: url(#liquid); }
.glowing { filter: url(#glow); }
```

### Page transitions cinematográficas
```js
// Curtain reveal
function CurtainTransition({ children, color = "#0a0a0a" }) {
  const curtainRef = useRef(null);

  useEffect(() => {
    const ctx = gsap.context(() => {
      gsap.timeline()
        .set(curtainRef.current, { scaleY: 1, transformOrigin: "bottom" })
        .to(curtainRef.current, {
          scaleY: 0,
          duration: 0.7,
          ease: "power4.inOut",
          transformOrigin: "top",
        });
    });
    return () => ctx.revert();
  }, []);

  return (
    <>
      {children}
      <div ref={curtainRef} style={{
        position: "fixed", inset: 0,
        background: color, zIndex: 9999,
        transformOrigin: "bottom",
      }} aria-hidden="true" />
    </>
  );
}

// Circle reveal (iris)
async function irisTransition(targetEl, onNavigate) {
  const rect = targetEl.getBoundingClientRect();
  const cx = rect.left + rect.width / 2;
  const cy = rect.top + rect.height / 2;
  const maxR = Math.sqrt(Math.pow(Math.max(cx, window.innerWidth - cx), 2)
                       + Math.pow(Math.max(cy, window.innerHeight - cy), 2));

  const overlay = document.createElement("div");
  overlay.style.cssText = `position:fixed;inset:0;z-index:9999;pointer-events:none;
    background:radial-gradient(circle at ${cx}px ${cy}px, transparent 0px, #0a0a0a 0px)`;
  document.body.appendChild(overlay);

  await gsap.to(overlay, {
    "--r": `${maxR}px`,
    duration: 0.5,
    ease: "power2.in",
  });

  await onNavigate();

  await gsap.to(overlay, {
    "--r": "0px",
    duration: 0.5,
    ease: "power2.out",
  });

  overlay.remove();
}
```

### Easter eggs & Momentos de deleite
```js
// Konami Code
function initEasterEgg(callback) {
  const sequence = ["ArrowUp","ArrowUp","ArrowDown","ArrowDown","ArrowLeft","ArrowRight","ArrowLeft","ArrowRight","b","a"];
  let pos = 0;

  document.addEventListener("keydown", (e) => {
    if (e.key === sequence[pos]) {
      pos++;
      if (pos === sequence.length) {
        callback();
        pos = 0;
      }
    } else {
      pos = 0;
    }
  });
}

// Confetti ao atingir meta
function launchConfetti() {
  const colors = ["#6366f1", "#22c55e", "#f59e0b", "#ec4899", "#14b8a6"];
  const count = 150;

  for (let i = 0; i < count; i++) {
    const el = document.createElement("div");
    const color = colors[Math.floor(Math.random() * colors.length)];
    const size = Math.random() * 8 + 4;

    Object.assign(el.style, {
      position: "fixed",
      left: Math.random() * 100 + "vw",
      top: "-20px",
      width: size + "px",
      height: size + "px",
      background: color,
      borderRadius: Math.random() > 0.5 ? "50%" : "2px",
      zIndex: 9999,
      pointerEvents: "none",
      transform: `rotate(${Math.random() * 360}deg)`,
    });
    document.body.appendChild(el);

    gsap.to(el, {
      y: "110vh",
      x: (Math.random() - 0.5) * 300,
      rotation: Math.random() * 720 - 360,
      opacity: 0,
      duration: Math.random() * 2 + 1,
      delay: Math.random() * 0.5,
      ease: "power1.in",
      onComplete: () => el.remove(),
    });
  }
}

// Haptic feedback
function haptic(pattern = "light") {
  if (!navigator.vibrate) return;
  const patterns = {
    light: [10],
    medium: [20],
    heavy: [30],
    success: [10, 50, 20],
    error: [30, 30, 30],
    warning: [20, 20, 20],
  };
  navigator.vibrate(patterns[pattern] || [10]);
}
```

---

## PSICOLOGIA UX — Sistema completo

### Reciprocidade em onboarding
```jsx
// Dar antes de pedir — aumenta conversão
function ReciprocalOnboarding() {
  const [step, setStep] = useState("gift"); // gift → value → ask

  return (
    <div>
      {step === "gift" && (
        // Dar algo de valor PRIMEIRO — sem pedir nada
        <div>
          <h2>Aqui está seu relatório gratuito</h2>
          <p>Sem cadastro necessário</p>
          <ReportPreview />
          <button onClick={() => setStep("value")}>
            Ver relatório completo →
          </button>
        </div>
      )}
      {step === "value" && (
        // Mostrar mais valor — ainda sem pedir
        <div>
          <FullReport />
          <button onClick={() => setStep("ask")}>
            Salvar meu relatório →
          </button>
        </div>
      )}
      {step === "ask" && (
        // Agora pedir — usuário já recebeu valor
        <SignupForm
          headline="Crie sua conta gratuita para salvar"
          subtext="Sem cartão de crédito necessário"
        />
      )}
    </div>
  );
}
```

### Choice Architecture — Pricing
```jsx
function PricingWithArchitecture({ plans }) {
  // Ordem importa: anchor alto primeiro
  const ordered = [...plans].sort((a, b) => b.price - a.price);

  return (
    <div className="pricing-grid">
      {ordered.map((plan, i) => (
        <PricingCard
          key={plan.id}
          plan={plan}
          featured={plan.id === "pro"} // destaque no produto target
          // Decoy: plan "básico" faz o "pro" parecer barato
          isDecoy={plan.id === "basic"}
        />
      ))}
    </div>
  );
}
```

### Social proof hierarchy
```jsx
// Tipos de prova social em ordem de persuasão:
const socialProofTypes = {
  expert: { weight: 10, component: ExpertEndorsement },
  celebrity: { weight: 8, component: CelebrityQuote },
  userPhoto: { weight: 7, component: UserReviewWithPhoto }, // foto = 40% mais confiável
  userText: { weight: 5, component: UserReviewText },
  wisdom: { weight: 3, component: UserCount }, // "1M+ usuários"
  certification: { weight: 6, component: TrustBadges },
};

// Combinar tipos para máximo impacto
function SocialProofSection() {
  return (
    <>
      <TrustBadges /> {/* certificações, parceiros */}
      <ExpertQuote person="CEO da Empresa X" />
      <ReviewsWithPhotos count={3} />
      <LiveUserCount /> {/* "2.847 empresas usando agora" */}
    </>
  );
}
```

---

## PERFORMANCE AVANÇADA — completa

### content-visibility + contain
```css
/* Renderização seletiva — até 50% mais rápido */
.page-section {
  content-visibility: auto;
  /* Reservar espaço estimado para evitar CLS */
  contain-intrinsic-size: 0 600px;
}

/* Isolamento preciso */
.isolated-card {
  contain: layout paint; /* reflow e repaint isolados */
}
.complex-animation {
  contain: layout; /* isolado, mas ainda pode pintar */
}
```

### Speculation Rules API
```html
<!-- Prerender páginas que o usuário provavelmente vai visitar -->
<script type="speculationrules">
{
  "prerender": [{
    "source": "document",
    "where": { "href_matches": "/blog/*" },
    "eagerness": "moderate"
  }],
  "prefetch": [{
    "source": "list",
    "urls": ["/about", "/pricing"],
    "eagerness": "eager"
  }]
}
</script>
```

### Service Worker — Cache strategies
```js
// sw.js — estratégias por tipo de recurso
const CACHE_NAME = "v1";

const strategies = {
  // HTML: Network first, cache fallback
  html: async (request) => {
    try {
      const response = await fetch(request);
      const cache = await caches.open(CACHE_NAME);
      cache.put(request, response.clone());
      return response;
    } catch {
      return caches.match(request);
    }
  },

  // Assets: Cache first, network fallback
  assets: async (request) => {
    const cached = await caches.match(request);
    if (cached) return cached;
    const response = await fetch(request);
    const cache = await caches.open(CACHE_NAME);
    cache.put(request, response.clone());
    return response;
  },

  // API: Stale-while-revalidate
  api: async (request) => {
    const cache = await caches.open(CACHE_NAME);
    const cached = await cache.match(request);
    const networkPromise = fetch(request).then(response => {
      cache.put(request, response.clone());
      return response;
    });
    return cached || networkPromise;
  },
};

self.addEventListener("fetch", (e) => {
  const url = new URL(e.request.url);
  if (e.request.mode === "navigate") return e.respondWith(strategies.html(e.request));
  if (url.pathname.startsWith("/api/")) return e.respondWith(strategies.api(e.request));
  return e.respondWith(strategies.assets(e.request));
});
```

### Offline UI indicator
```jsx
function OfflineIndicator() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);

  useEffect(() => {
    const on = () => setIsOnline(true);
    const off = () => setIsOnline(false);
    window.addEventListener("online", on);
    window.addEventListener("offline", off);
    return () => { window.removeEventListener("online", on); window.removeEventListener("offline", off); };
  }, []);

  return (
    <AnimatePresence>
      {!isOnline && (
        <motion.div
          className="offline-banner"
          initial={{ y: -60 }} animate={{ y: 0 }} exit={{ y: -60 }}
          role="alert" aria-live="assertive"
        >
          📶 Sem conexão — mostrando dados em cache
        </motion.div>
      )}
    </AnimatePresence>
  );
}
```

### Low-end device adaptation
```js
function useDeviceCapability() {
  const [capability, setCapability] = useState("high");

  useEffect(() => {
    const conn = navigator.connection;
    const memory = navigator.deviceMemory; // GB
    const cores = navigator.hardwareConcurrency;

    if (
      conn?.saveData ||
      conn?.effectiveType === "slow-2g" ||
      conn?.effectiveType === "2g" ||
      memory < 2 ||
      cores < 4
    ) {
      setCapability("low");
    } else if (conn?.effectiveType === "3g" || memory < 4) {
      setCapability("medium");
    }
  }, []);

  return capability;
}

// Uso
function AdaptiveHero() {
  const capability = useDeviceCapability();
  const prefersReduced = useReducedMotion();

  return (
    <div className="hero">
      {capability === "high" && !prefersReduced
        ? <WebGLBackground />      // 3D completo
        : capability === "medium"
        ? <CSSAuroraBackground />  // CSS animation
        : <StaticBackground />     // imagem estática
      }
    </div>
  );
}
```

### INP optimization patterns
```js
// Deferir work pesado após resposta visual
function handleButtonClick(e) {
  // 1. Resposta visual IMEDIATA
  e.currentTarget.classList.add("loading");

  // 2. Work pesado em idle
  if ("scheduler" in window) {
    scheduler.postTask(async () => {
      await processData();
      e.currentTarget.classList.remove("loading");
    }, { priority: "user-visible" });
  } else {
    requestIdleCallback(async () => {
      await processData();
      e.currentTarget.classList.remove("loading");
    });
  }
}

// Debounce em inputs de busca
function useDebouncedSearch(delay = 300) {
  const [query, setQuery] = useState("");
  const [debouncedQuery, setDebouncedQuery] = useState("");

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedQuery(query), delay);
    return () => clearTimeout(timer);
  }, [query, delay]);

  return { query, setQuery, debouncedQuery };
}
```
