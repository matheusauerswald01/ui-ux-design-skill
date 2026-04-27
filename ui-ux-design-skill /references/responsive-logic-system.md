# Referência: Lógica de Responsividade — Sistema Completo

## PRINCÍPIO FUNDAMENTAL: Não adaptar — reimaginar

```
Mobile não é desktop encolhido.
Cada breakpoint tem sua própria lógica visual.
O mesmo conteúdo, experiências completamente diferentes.
```

---

## QUANDO MUDAR DE PARADIGMA

```
Desktop (>1024px):  Layout completo, hover states, cursor, scroll elegante
Tablet (768-1024px): Layout intermediário, touch + mouse, densidade reduzida  
Mobile (<768px):    Touch-first, hierarquia simples, gestos nativos, sem hover
```

### Decisões de paradigma por componente

```
NAVBAR:
  Desktop → horizontal, links visíveis, mega menu no hover
  Tablet  → links visíveis mas sem mega menu, hamburger opcional
  Mobile  → hamburger SEMPRE, full-screen drawer, bottom sheet opcional

HERO:
  Desktop → título grande horizontal, imagem ao lado
  Tablet  → título menor, imagem embaixo ou atrás
  Mobile  → título em 2-3 linhas, imagem como fundo com overlay

CARDS:
  Desktop → 3-4 colunas, hover com elevação e conteúdo extra
  Tablet  → 2 colunas, hover simples
  Mobile  → 1 coluna, sem hover (usar tap states), swipeable

PRICING:
  Desktop → 3 colunas lado a lado, todos visíveis
  Tablet  → 2 colunas + scroll horizontal para 3º
  Mobile  → 1 por vez com tabs ou swipe entre planos

FORM:
  Desktop → 2 colunas para campos relacionados
  Tablet  → 1 coluna, mas botões e grupos inline
  Mobile  → 1 coluna absoluta, inputs grandes (min 44px height)

DATA TABLE:
  Desktop → todas as colunas visíveis
  Tablet  → colunas prioritárias, resto em accordion
  Mobile  → cards em lista, não tabela
```

---

## SISTEMA DE BREAKPOINTS AVANÇADO

```css
/* Não usar px fixos — usar faixas semânticas */
:root {
  --bp-phone-sm:  380px;  /* iPhone SE e similares */
  --bp-phone:     480px;  /* phones modernos */
  --bp-tablet:    768px;  /* tablets e landscape */
  --bp-laptop:    1024px; /* laptops */
  --bp-desktop:   1280px; /* desktops */
  --bp-wide:      1536px; /* telas largas */
  --bp-ultrawide: 1920px; /* ultra wide */
}

/* Usar em CSS com custom media (PostCSS ou futuro nativo) */
/* ou em JS: */
const breakpoints = {
  phoneSm:  "(max-width: 379px)",
  phone:    "(max-width: 479px)",
  tablet:   "(max-width: 767px)",
  laptop:   "(max-width: 1023px)",
  desktop:  "(min-width: 1024px)",
};
```

---

## TIPOGRAFIA RESPONSIVA — PARADIGMAS POR BREAKPOINT

```css
/* Não só reduzir — mudar características */
h1 {
  /* Desktop: apertado, bold, impactante */
  font-size: clamp(3rem, 8vw, 7rem);
  font-weight: 800;
  letter-spacing: -0.04em;
  line-height: 0.9;
  text-transform: uppercase; /* apenas desktop */
}

@media (max-width: 767px) {
  h1 {
    /* Mobile: mais legível, menos teatral */
    font-size: clamp(2rem, 8vw, 3rem);
    font-weight: 700;
    letter-spacing: -0.02em;
    line-height: 1.1;
    text-transform: none; /* uppercase pode ficar difícil de ler */
  }
}

/* Tracking muda com tamanho — sempre */
.hero-title {
  letter-spacing: clamp(-0.04em, -0.02em + 0.5vw, 0em);
  /* em displays grandes: muito apertado */
  /* em mobile: quase normal */
}

/* Line-height muda com densidade */
.body-text {
  line-height: clamp(1.5, 1.5 + 0.5vw, 1.8);
  /* desktop: pode ser mais apertado */
  /* mobile: precisa de mais espaço entre linhas */
}
```

---

## VIEWPORT UNITS CORRETOS

```css
/* PROBLEMA: 100vh inclui barra do browser mobile */
.hero-wrong { height: 100vh; } /* ❌ no iOS, parte fica embaixo da barra */

/* SOLUÇÃO: usar svh (small viewport height) */
.hero-correct { min-height: 100svh; } /* ✅ exclui UI do browser */

/* Quando usar cada um: */
/* svh (small) — hero sections, elementos que precisam ficar na tela */
.hero { min-height: 100svh; }

/* dvh (dynamic) — elementos que respondem ao teclado virtual */
.modal { max-height: 100dvh; }

/* lvh (large) — quando você QUER incluir a área da barra */
.decorative { height: 100lvh; }

/* Fallback para browsers antigos */
.hero {
  min-height: 100vh; /* fallback */
  min-height: 100svh;
}

/* Safe area insets — para notch e home indicator */
.fixed-bottom {
  padding-bottom: max(1rem, env(safe-area-inset-bottom));
}
.fixed-top {
  padding-top: max(0px, env(safe-area-inset-top));
}

/* Viewport width sem scrollbar */
.full-width {
  width: 100vw; /* pode causar scroll horizontal */
  width: 100dvw; /* melhor — sem overflow */
}
```

---

## 3D RESPONSIVO — DEGRADAÇÃO GRACIOSA

```jsx
function Adaptive3DBackground() {
  const [capability, setCapability] = useState("high");
  const prefersReduced = useReducedMotion();

  useEffect(() => {
    // Detectar capacidade do dispositivo
    const canvas = document.createElement("canvas");
    const gl = canvas.getContext("webgl2") || canvas.getContext("webgl");

    if (!gl || prefersReduced) {
      setCapability("none");
      return;
    }

    // Verificar GPU info
    const debugInfo = gl.getExtension("WEBGL_debug_renderer_info");
    const renderer = debugInfo
      ? gl.getParameter(debugInfo.UNMASKED_RENDERER_WEBGL)
      : "";

    // Devices com GPU fraca
    const isLowEnd = /Mali-4|Adreno 3|Intel HD 400|GMA/i.test(renderer);
    const isMobile = /Mobi|Android/i.test(navigator.userAgent);
    const hasLowMemory = navigator.deviceMemory < 4;

    if (isLowEnd || hasLowMemory) {
      setCapability("css");
    } else if (isMobile) {
      setCapability("simple");
    }
  }, [prefersReduced]);

  if (capability === "none")   return <StaticBackground />;
  if (capability === "css")    return <CSSBackground />;
  if (capability === "simple") return <SimplifiedWebGL />;
  return <FullWebGL />;
}

// Configurações de qualidade para R3F
const qualityConfigs = {
  high: {
    dpr: [1, 2],
    shadows: "soft",
    postProcessing: true,
    particleCount: 5000,
  },
  medium: {
    dpr: [1, 1.5],
    shadows: "basic",
    postProcessing: false,
    particleCount: 1500,
  },
  low: {
    dpr: [1, 1],
    shadows: false,
    postProcessing: false,
    particleCount: 300,
  },
};
```

---

## ANIMATIONS RESPONSIVAS COM GSAP

```js
// gsap.matchMedia — valores diferentes por breakpoint
function initAnimations() {
  const mm = gsap.matchMedia();

  mm.add("(min-width: 1024px)", () => {
    // Desktop: animações completas
    gsap.from(".hero-title .char", {
      y: 100,
      opacity: 0,
      stagger: 0.02,
      duration: 1.2,
      ease: "power4.out",
    });

    ScrollTrigger.create({
      trigger: ".parallax",
      scrub: 2,
      onUpdate: ({ progress }) => {
        gsap.set(".parallax-layer", { y: progress * -200 });
      }
    });

    // Retornar cleanup
    return () => {
      ScrollTrigger.getAll().forEach(t => t.kill());
    };
  });

  mm.add("(max-width: 767px)", () => {
    // Mobile: animações simplificadas
    gsap.from(".hero-title", {
      y: 30,
      opacity: 0,
      duration: 0.6,
      ease: "power2.out",
      // Sem stagger de chars — pesado em mobile
    });

    // Sem parallax em mobile — causa jank
    // ScrollTrigger simples apenas
    ScrollTrigger.create({
      trigger: ".feature-section",
      start: "top 80%",
      onEnter: () => gsap.to(".feature-card", {
        opacity: 1, y: 0, stagger: 0.1, duration: 0.4
      }),
    });
  });

  return mm;
}
```

---

## TOUCH INTERACTIONS PREMIUM

```js
// Swipe com velocidade e momentum
class TouchSwipe {
  constructor(el, { onSwipe, threshold = 50, velocityThreshold = 0.3 }) {
    this.el = el;
    this.onSwipe = onSwipe;
    this.threshold = threshold;
    this.velocityThreshold = velocityThreshold;
    this.startX = 0;
    this.startY = 0;
    this.startTime = 0;

    this.init();
  }

  init() {
    this.el.addEventListener("touchstart", this.onStart, { passive: true });
    this.el.addEventListener("touchend", this.onEnd, { passive: true });
  }

  onStart = (e) => {
    const touch = e.touches[0];
    this.startX = touch.clientX;
    this.startY = touch.clientY;
    this.startTime = Date.now();
  };

  onEnd = (e) => {
    const touch = e.changedTouches[0];
    const dx = touch.clientX - this.startX;
    const dy = touch.clientY - this.startY;
    const dt = Date.now() - this.startTime;
    const velocity = Math.abs(dx) / dt;

    if (Math.abs(dx) > this.threshold || velocity > this.velocityThreshold) {
      if (Math.abs(dx) > Math.abs(dy)) { // horizontal swipe
        this.onSwipe(dx > 0 ? "right" : "left", { dx, dy, velocity });
      }
    }
  };

  destroy() {
    this.el.removeEventListener("touchstart", this.onStart);
    this.el.removeEventListener("touchend", this.onEnd);
  }
}

// Long press
function useLongPress(callback, ms = 500) {
  const timerRef = useRef(null);
  const [pressing, setPressing] = useState(false);

  const start = (e) => {
    // Prevenir context menu em mobile
    e.preventDefault();
    setPressing(true);
    timerRef.current = setTimeout(() => {
      callback(e);
      setPressing(false);
    }, ms);
  };

  const stop = () => {
    clearTimeout(timerRef.current);
    setPressing(false);
  };

  return {
    onMouseDown: start,
    onMouseUp: stop,
    onTouchStart: start,
    onTouchEnd: stop,
    pressing,
  };
}
```

---

## FLUID SPACING SYSTEM

```css
/* Todos os espaçamentos fluidos — zero breakpoints */
:root {
  /* Escala de espaçamento fluida */
  --space-1:  clamp(0.25rem, 0.5vw, 0.5rem);     /* 4-8px */
  --space-2:  clamp(0.5rem,  1vw,   1rem);        /* 8-16px */
  --space-3:  clamp(0.75rem, 1.5vw, 1.5rem);      /* 12-24px */
  --space-4:  clamp(1rem,    2vw,   2rem);         /* 16-32px */
  --space-6:  clamp(1.5rem,  3vw,   3rem);         /* 24-48px */
  --space-8:  clamp(2rem,    4vw,   4rem);         /* 32-64px */
  --space-12: clamp(3rem,    6vw,   6rem);         /* 48-96px */
  --space-16: clamp(4rem,    8vw,   8rem);         /* 64-128px */
  --space-24: clamp(6rem,    12vw,  12rem);        /* 96-192px */

  /* Border radius fluido */
  --radius-sm: clamp(4px,  0.5vw, 6px);
  --radius-md: clamp(8px,  1vw,   12px);
  --radius-lg: clamp(12px, 1.5vw, 20px);
  --radius-xl: clamp(16px, 2vw,   32px);
}

/* Container fluido com padding responsivo */
.container {
  width: min(100%, 1280px);
  margin-inline: auto;
  padding-inline: clamp(1rem, 5vw, 4rem);
}

/* Grid sem breakpoints */
.auto-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(100%, 280px), 1fr));
  gap: var(--space-4);
}

/* Stack vertical com gap fluido */
.stack {
  display: flex;
  flex-direction: column;
  gap: var(--space-6);
}
```

---

## IMAGE RESPONSIVENESS — ART DIRECTION

```html
<!-- Art direction: crop diferente por breakpoint -->
<picture>
  <!-- Mobile: crop quadrado, foco no rosto -->
  <source
    media="(max-width: 767px)"
    srcset="/img/hero-square-400.avif 400w, /img/hero-square-800.avif 800w"
    sizes="100vw"
    type="image/avif"
  >
  <!-- Tablet: crop 4:3 -->
  <source
    media="(max-width: 1023px)"
    srcset="/img/hero-4x3-800.avif 800w, /img/hero-4x3-1200.avif 1200w"
    sizes="100vw"
    type="image/avif"
  >
  <!-- Desktop: crop 16:9 wide -->
  <source
    srcset="/img/hero-16x9-1200.avif 1200w, /img/hero-16x9-2000.avif 2000w"
    sizes="100vw"
    type="image/avif"
  >
  <img
    src="/img/hero-16x9-1200.jpg"
    alt="Descrição detalhada da imagem"
    width="1200"
    height="675"
    fetchpriority="high"
    decoding="async"
  >
</picture>

<!-- object-position por ponto focal -->
<style>
.hero-img {
  width: 100%;
  height: 60vh;
  object-fit: cover;
  object-position: center 30%; /* foco no terço superior */
}
@media (max-width: 767px) {
  .hero-img {
    height: 50vh;
    object-position: center center;
  }
}
</style>
```

---

## SCROLLTELLING RESPONSIVO

```js
// Versão diferente por device
function initScrolltelling() {
  const mm = gsap.matchMedia();

  mm.add("(min-width: 1024px)", () => {
    // Desktop: efeitos completos + câmera 3D
    const ctx = gsap.context(() => {
      initVideoScrub(".hero-video", ".hero-section");
      initCameraPath(canvasRef.current);
      initWordReveal(".story-text");
      initHorizontalScroll(".gallery-track");
    });
    return () => ctx.revert();
  });

  mm.add("(max-width: 767px)", () => {
    // Mobile: versão simplificada sem travamento
    const ctx = gsap.context(() => {
      // Sem video scrub (muito pesado)
      // Sem câmera 3D
      // Fade simples em vez de word reveal
      gsap.from(".story-text", {
        opacity: 0,
        y: 30,
        duration: 0.6,
        scrollTrigger: {
          trigger: ".story-text",
          start: "top 85%",
        }
      });
      // Carrossel swipeable em vez de horizontal scroll
      initSwipeCarousel(".gallery-track");
    });
    return () => ctx.revert();
  });

  return mm;
}
```
