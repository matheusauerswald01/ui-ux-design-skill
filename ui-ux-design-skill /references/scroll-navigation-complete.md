# Referência: Scroll & Navegação — Sistema Completo

---

## SCROLL-TRIGGERED vs SCROLL-BOUND — DIFERENÇA CRÍTICA

```
SCROLL-TRIGGERED (toggle):
  → Animação DISPARA quando elemento entra no viewport
  → Acontece uma vez (ou a cada entrada)
  → NÃO é mapeada ao progresso do scroll
  → Ideal para: reveals de conteúdo, entradas de seções, contadores

SCROLL-BOUND / SCRUBBER (contínua):
  → Animação é MAPEADA ao progresso do scroll
  → scrub: true (ou número para inércia)
  → O scroll CONTROLA a posição da animação
  → Ideal para: parallax, video scrubbing, câmera 3D, storytelling

NUNCA confundir:
```

```js
// ❌ ERRADO — usar scrub em elemento que deveria ser triggered
ScrollTrigger.create({
  trigger: ".card",
  scrub: true, // card fica "meio revelado" se não rolar o suficiente
  animation: gsap.from(".card", { opacity: 0, y: 50 }),
});

// ✅ CERTO — triggered para reveals
ScrollTrigger.create({
  trigger: ".card",
  start: "top 85%",
  animation: gsap.from(".card", { opacity: 0, y: 50, duration: 0.6 }),
  once: true, // nunca repete
});

// ✅ CERTO — scrubber para parallax
gsap.to(".parallax-layer", {
  y: -200,
  ease: "none",
  scrollTrigger: {
    trigger: ".section",
    start: "top bottom",
    end: "bottom top",
    scrub: 1.5, // número = inércia em segundos
  }
});
```

---

## LENIS v2 — API COMPLETA

```js
import Lenis from "lenis";
import gsap from "gsap";
import ScrollTrigger from "gsap/ScrollTrigger";

// Setup v2 (2024)
const lenis = new Lenis({
  // Básico
  duration: 1.2,
  easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
  orientation: "vertical",
  gestureOrientation: "vertical",

  // Novo em v2
  smoothWheel: true,
  syncTouch: false,           // não interceptar touch nativo
  syncTouchLerp: 0.075,       // inércia no touch
  touchInertiaMultiplier: 35, // peso do inércia touch

  // Wrapper e conteúdo (para scroll em elemento específico)
  wrapper: window,
  content: document.documentElement,

  // Infinite scroll
  infinite: false,
});

// Sync com GSAP — OBRIGATÓRIO
lenis.on("scroll", ScrollTrigger.update);
gsap.ticker.add((time) => lenis.raf(time * 1000));
gsap.ticker.lagSmoothing(0);

// Eventos avançados
lenis.on("scroll", ({ scroll, limit, velocity, direction, progress }) => {
  // scroll: posição atual
  // limit: máximo scrollável
  // velocity: velocidade atual
  // direction: 1 (baixo) ou -1 (cima)
  // progress: 0-1
  console.log({ scroll, velocity, direction, progress });
});

// ScrollTo programático
lenis.scrollTo("#section", {
  offset: -80,          // compensar header fixo
  duration: 1.5,
  easing: (t) => t < 0.5 ? 4 * t * t * t : (t - 1) * (2 * t - 2) * (2 * t - 2) + 1,
  immediate: false,     // true para sem animação
  lock: false,          // bloquear scroll do usuário durante
  onComplete: () => console.log("arrived"),
});

// Stop/Start (para modais, etc.)
lenis.stop();   // pausar
lenis.start();  // retomar

// Destroy completo
lenis.destroy();

// Next.js App Router integration
// app/providers.tsx
"use client";
export function SmoothScrollProvider({ children }) {
  useEffect(() => {
    const lenis = new Lenis();
    lenis.on("scroll", ScrollTrigger.update);
    gsap.ticker.add((time) => lenis.raf(time * 1000));
    gsap.ticker.lagSmoothing(0);
    return () => { lenis.destroy(); };
  }, []);
  return children;
}
```

---

## LENIS + SCROLLTRIGGER — PROXY (CRÍTICO)

```js
// Sem isso, ScrollTrigger usa window.scrollY e não funciona com Lenis
// Resultado: triggers disparam no momento errado

ScrollTrigger.scrollerProxy(document.documentElement, {
  scrollTop(value) {
    if (arguments.length) {
      lenis.scrollTo(value, { immediate: true });
    }
    return lenis.scroll;
  },
  getBoundingClientRect() {
    return { top: 0, left: 0, width: window.innerWidth, height: window.innerHeight };
  },
  pinType: document.documentElement.style.transform ? "transform" : "fixed"
});

// Refresh após setup
lenis.on("scroll", ScrollTrigger.update);
ScrollTrigger.addEventListener("refresh", () => lenis.resize());
ScrollTrigger.refresh();
```

---

## GSAP SCROLLTRIGGER — AVANÇADO

### batch() — múltiplos elementos com uma configuração
```js
// ❌ ERRADO — loop com um ScrollTrigger por elemento (performance ruim)
gsap.utils.toArray(".card").forEach(card => {
  ScrollTrigger.create({ trigger: card, animation: gsap.from(card, { y: 30 }) });
});

// ✅ CERTO — batch processa vários de uma vez
ScrollTrigger.batch(".card", {
  interval: 0.1,        // tempo entre batches (stagger)
  batchMax: 5,          // máx elementos por batch
  start: "top 85%",
  onEnter: (batch) => gsap.from(batch, {
    y: 40,
    opacity: 0,
    stagger: 0.08,
    duration: 0.6,
    ease: "power3.out",
  }),
  onLeave: (batch) => gsap.to(batch, { opacity: 0 }),
  onEnterBack: (batch) => gsap.to(batch, { opacity: 1 }),
  once: false,
});
```

### containerAnimation — scroll dentro de scroll
```js
// Para seções com scroll horizontal que contêm animações verticais
const horizontalTween = gsap.to(".panels", {
  x: () => -(document.querySelector(".panels").scrollWidth - window.innerWidth),
  ease: "none",
  scrollTrigger: {
    trigger: ".panels-container",
    pin: true,
    scrub: 1,
    end: () => "+=" + document.querySelector(".panels").offsetWidth,
  }
});

// Animação DENTRO do scroll horizontal
gsap.from(".panel-content", {
  opacity: 0,
  y: 20,
  scrollTrigger: {
    trigger: ".panel-content",
    containerAnimation: horizontalTween, // referência ao scroll horizontal
    start: "left 70%",
    toggleActions: "play none none reset",
  }
});
```

### invalidateOnRefresh — para valores dinâmicos
```js
gsap.to(".hero", {
  y: () => -window.innerHeight * 0.3, // valor calculado
  ease: "none",
  scrollTrigger: {
    trigger: ".hero",
    scrub: true,
    invalidateOnRefresh: true, // recalcula ao redimensionar
  }
});
```

### scrollerProxy — para scrollers customizados
```js
// Para qualquer smooth scroller (não só Lenis)
ScrollTrigger.scrollerProxy(".custom-scroller", {
  scrollTop(value) {
    if (arguments.length) {
      this._scrollTop = value;
    }
    return this._scrollTop || 0;
  },
  getBoundingClientRect() {
    return { top: 0, left: 0, width: window.innerWidth, height: window.innerHeight };
  }
});
```

---

## SCROLLYTELLING — SCENE MANAGER

```js
class ScrollytellingManager {
  constructor(container) {
    this.container = container;
    this.scenes = [];
    this.currentScene = null;
    this.lenis = new Lenis();
    this.setupLenis();
  }

  setupLenis() {
    this.lenis.on("scroll", ScrollTrigger.update);
    gsap.ticker.add((time) => this.lenis.raf(time * 1000));
    gsap.ticker.lagSmoothing(0);
  }

  addScene(config) {
    const scene = {
      id: config.id,
      trigger: config.trigger,
      duration: config.duration || "100%",
      onEnter: config.onEnter || (() => {}),
      onLeave: config.onLeave || (() => {}),
      onUpdate: config.onUpdate || null,
      animation: config.animation || null,
      chapter: config.chapter || null,
    };

    ScrollTrigger.create({
      trigger: scene.trigger,
      start: "top top",
      end: scene.duration,
      pin: config.pin !== false,
      anticipatePin: 1,
      scrub: scene.onUpdate ? 1 : false,
      onEnter: () => {
        this.currentScene = scene;
        scene.onEnter();
        this.updateChapterIndicator(scene.chapter);
      },
      onLeave: () => scene.onLeave(),
      onUpdate: scene.onUpdate ? (self) => scene.onUpdate(self.progress) : undefined,
    });

    this.scenes.push(scene);
    return this;
  }

  updateChapterIndicator(chapter) {
    if (!chapter) return;
    document.querySelectorAll(".chapter-dot").forEach((dot, i) => {
      dot.classList.toggle("active", i === chapter - 1);
    });
    document.querySelector(".chapter-label").textContent = `${chapter} / ${this.scenes.filter(s => s.chapter).length}`;
  }

  // Modo acessível: sem scroll, teclado/botões
  enableA11yMode() {
    ScrollTrigger.getAll().forEach(t => t.kill());
    this.lenis.stop();

    let currentIndex = 0;
    const scenes = this.scenes;

    const go = (index) => {
      if (index < 0 || index >= scenes.length) return;
      scenes[currentIndex]?.onLeave();
      currentIndex = index;
      scenes[currentIndex].onEnter();
      scenes[currentIndex].onUpdate?.(1);
    };

    document.querySelector("[data-next]")?.addEventListener("click", () => go(currentIndex + 1));
    document.querySelector("[data-prev]")?.addEventListener("click", () => go(currentIndex - 1));
    document.addEventListener("keydown", (e) => {
      if (e.key === "ArrowRight" || e.key === "ArrowDown") go(currentIndex + 1);
      if (e.key === "ArrowLeft" || e.key === "ArrowUp") go(currentIndex - 1);
    });

    go(0);
  }

  destroy() {
    this.lenis.destroy();
    ScrollTrigger.getAll().forEach(t => t.kill());
  }
}

// Uso
const scrolly = new ScrollytellingManager(".story-container");

scrolly
  .addScene({
    id: "intro",
    trigger: ".scene-intro",
    chapter: 1,
    pin: true,
    duration: "200%",
    onEnter: () => gsap.to(".title", { opacity: 1, y: 0, duration: 0.8 }),
    onLeave: () => gsap.to(".title", { opacity: 0, duration: 0.4 }),
    onUpdate: (progress) => {
      gsap.set(".hero-3d", { rotateY: progress * 180 });
    },
  })
  .addScene({
    id: "product",
    trigger: ".scene-product",
    chapter: 2,
    pin: true,
    duration: "300%",
    onUpdate: (progress) => {
      gsap.set(".product-camera", {
        x: progress * -400,
        z: progress * 200,
      });
    },
  });
```

---

## CINEMATIC WEB — ARQUITETURA COMPLETA

```js
/**
 * CinematicCompositor — orquestra WebGL + DOM + Áudio
 * Padrão usado por: Active Theory, Lusion, Resn
 */
class CinematicCompositor {
  constructor() {
    // Layers de renderização
    this.layers = {
      webgl:   new WebGLLayer(),    // Three.js / R3F
      dom:     new DOMLayer(),      // HTML/CSS overlay
      audio:   new AudioLayer(),    // Web Audio API
      cursor:  new CursorLayer(),   // Custom cursor
    };

    this.scheduler = new SceneScheduler(this.layers);
    this.lenis = null;
    this.stats = null;
  }

  init(canvas, container) {
    this.layers.webgl.init(canvas);
    this.layers.dom.init(container);
    this.layers.audio.init();
    this.layers.cursor.init();

    // Lenis como controlador central de scroll
    this.lenis = new Lenis();
    this.lenis.on("scroll", ({ scroll, velocity, direction, progress }) => {
      ScrollTrigger.update();
      this.scheduler.onScroll({ scroll, velocity, direction, progress });
      this.layers.webgl.onScroll(scroll, velocity);
      this.layers.audio.onScroll(progress);
    });

    // RAF loop central
    gsap.ticker.add((time) => {
      this.lenis.raf(time * 1000);
      this.layers.webgl.render(time);
      this.layers.cursor.update(time);
    });
    gsap.ticker.lagSmoothing(0);
  }

  addScene(config) {
    return this.scheduler.addScene(config);
  }

  destroy() {
    this.lenis.destroy();
    gsap.ticker.remove();
    Object.values(this.layers).forEach(l => l.destroy?.());
  }
}

// Layer types
class WebGLLayer {
  init(canvas) {
    this.renderer = new THREE.WebGLRenderer({ canvas, alpha: true });
    this.renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
    this.scene = new THREE.Scene();
    this.camera = new THREE.PerspectiveCamera(45, innerWidth / innerHeight, 0.1, 1000);
  }

  onScroll(scroll, velocity) {
    // Atualizar shaders com velocidade de scroll
    this.scene.traverse(obj => {
      if (obj.material?.uniforms?.uScrollVelocity) {
        obj.material.uniforms.uScrollVelocity.value = velocity;
      }
    });
  }

  render(time) {
    this.renderer.render(this.scene, this.camera);
  }
}
```

---

## HORIZONTAL SCROLL — VARIANTES COMPLETAS

```js
// 1. Gallery horizontal simples
function initHorizontalGallery() {
  const track = document.querySelector(".gallery-track");
  const totalWidth = track.scrollWidth - window.innerWidth;

  gsap.to(track, {
    x: -totalWidth,
    ease: "none",
    scrollTrigger: {
      trigger: ".gallery-container",
      pin: true,
      scrub: 1,
      end: () => "+=" + totalWidth,
      invalidateOnRefresh: true,
    }
  });
}

// 2. Multi-speed horizontal layers
function initMultiSpeedHorizontal() {
  const layers = [
    { el: ".layer-bg", speed: 0.3 },
    { el: ".layer-mid", speed: 0.6 },
    { el: ".layer-fg", speed: 1.0 },
  ];

  layers.forEach(({ el, speed }) => {
    const element = document.querySelector(el);
    const totalWidth = element.scrollWidth - window.innerWidth;

    gsap.to(element, {
      x: -totalWidth * speed,
      ease: "none",
      scrollTrigger: {
        trigger: ".horizontal-section",
        pin: layers[0].el === el, // só o primeiro pina
        scrub: 1,
        end: () => "+=" + (totalWidth * 1.0),
      }
    });
  });
}

// 3. Horizontal com snap
function initHorizontalSnap() {
  const panels = gsap.utils.toArray(".panel");
  const totalWidth = panels.reduce((acc, p) => acc + p.offsetWidth, 0);

  gsap.to(".panels-container", {
    x: -(totalWidth - window.innerWidth),
    ease: "none",
    scrollTrigger: {
      trigger: ".panels-wrapper",
      pin: true,
      scrub: 1,
      end: () => "+=" + (totalWidth - window.innerWidth),
      snap: {
        snapTo: 1 / (panels.length - 1),
        duration: { min: 0.2, max: 0.5 },
        ease: "power2.inOut",
      }
    }
  });
}

// 4. CSS Scroll Snap (sem JS para casos simples)
const css = `
.snap-container {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  scroll-behavior: smooth;
  gap: 1rem;
  padding: 0 max(1rem, (100vw - 1200px) / 2);
  scrollbar-width: none;
}
.snap-item {
  scroll-snap-align: start;
  flex-shrink: 0;
  width: min(80vw, 400px);
}
`;
```

---

## STICKY SECTIONS — ANTI-FLICKERING

```js
// Receita anti-flicker para pinning
function initStickySectionSafe(trigger, animation) {
  // 1. Garantir que as dimensões estão corretas ANTES de criar ScrollTrigger
  const triggerEl = document.querySelector(trigger);
  triggerEl.style.overflow = "hidden"; // evitar height colapso

  // 2. Usar will-change APENAS no elemento pinado
  triggerEl.style.willChange = "transform";

  const st = ScrollTrigger.create({
    trigger: triggerEl,
    start: "top top",
    end: "+=200%", // duração em pixels
    pin: true,
    anticipatePin: 1,        // prepara o pin antes de chegar
    pinSpacing: true,        // adiciona espaço após o pin
    fastScrollEnd: true,     // melhor performance em scroll rápido
    preventOverlaps: true,   // evita conflito com outros triggers
    refreshPriority: -1,     // prioridade de refresh
    animation: animation,
    scrub: 1,
    onRefresh: () => {
      // Recalcular após resize
      animation.progress(0);
    },
  });

  // 3. Cleanup de will-change após
  st.vars.onKill = () => {
    triggerEl.style.willChange = "auto";
  };

  // 4. ResizeObserver para refresh automático
  new ResizeObserver(() => {
    st.refresh();
  }).observe(triggerEl);

  return st;
}

// Multi-pin orchestration
function initMultiPin(sections) {
  sections.forEach((section, i) => {
    ScrollTrigger.create({
      trigger: section.el,
      start: "top top",
      end: `+=${section.duration}`,
      pin: true,
      pinSpacing: i < sections.length - 1, // só o último sem spacing
      anticipatePin: 1,
    });
  });
}
```

---

## SCROLLJACKING ÉTICO

```
QUANDO é aceitável:
✅ Apple-style product presentations (bem-documentado que funciona)
✅ Storytelling sites não-funcionais (portfolio, campaigns)
✅ Quando há sinal claro de scroll para o usuário
✅ Quando a experiência sem scrolljacking seria claramente inferior
✅ Quando há fallback mobile funcional

QUANDO NUNCA usar:
❌ Sites de conteúdo (blog, documentação, e-commerce)
❌ Quando o usuário precisa ler para tomar decisão
❌ Quando o scroll é mais lento que o esperado
❌ Sem fallback para mobile/touch
❌ Em formulários ou qualquer processo de task completion

IMPLEMENTAÇÃO ÉTICA:
```

```js
// Scrolljacking ético com feedback claro
class EthicalScrolljack {
  constructor(container, scenes) {
    this.container = container;
    this.scenes = scenes;
    this.current = 0;
    this.isAnimating = false;
    this.isMobile = window.innerWidth < 768;

    if (this.isMobile) {
      this.initMobileFallback(); // normal scroll em mobile
    } else {
      this.initScrolljack();
    }
  }

  initScrolljack() {
    // Bloquear scroll nativo
    document.body.style.overflow = "hidden";

    // Usar wheel para detectar intenção
    this.container.addEventListener("wheel", this.onWheel.bind(this), { passive: false });

    // Indicador visual de progresso (OBRIGATÓRIO para ética)
    this.createProgressIndicator();

    // Keyboard navigation (acessibilidade)
    document.addEventListener("keydown", (e) => {
      if (e.key === "ArrowDown") this.goToScene(this.current + 1);
      if (e.key === "ArrowUp") this.goToScene(this.current - 1);
    });
  }

  onWheel(e) {
    e.preventDefault();
    if (this.isAnimating) return;

    const direction = e.deltaY > 0 ? 1 : -1;
    this.goToScene(this.current + direction);
  }

  goToScene(index) {
    if (index < 0 || index >= this.scenes.length || this.isAnimating) return;

    this.isAnimating = true;
    this.scenes[this.current]?.onLeave();
    this.current = index;
    this.scenes[this.current]?.onEnter();
    this.updateProgress();

    setTimeout(() => { this.isAnimating = false; }, 1000);
  }

  createProgressIndicator() {
    const nav = document.createElement("nav");
    nav.setAttribute("aria-label", "Navegação de seções");
    nav.className = "scrolljack-nav";

    this.scenes.forEach((_, i) => {
      const dot = document.createElement("button");
      dot.className = "scrolljack-dot";
      dot.setAttribute("aria-label", `Ir para seção ${i + 1}`);
      dot.addEventListener("click", () => this.goToScene(i));
      nav.appendChild(dot);
    });

    document.body.appendChild(nav);
    this.progressDots = nav.querySelectorAll(".scrolljack-dot");
    this.updateProgress();
  }

  updateProgress() {
    this.progressDots.forEach((dot, i) => {
      dot.classList.toggle("active", i === this.current);
      dot.setAttribute("aria-current", i === this.current ? "true" : "false");
    });
  }

  initMobileFallback() {
    // Scroll normal no mobile — nunca scrolljack em mobile
    this.scenes.forEach(scene => {
      ScrollTrigger.create({
        trigger: scene.el,
        start: "top 70%",
        onEnter: () => scene.onEnter?.(),
      });
    });
  }
}
```

---

## LOCOMOTIVE SCROLL v5

```js
import LocomotiveScroll from "locomotive-scroll";

// v5 é baseado no Lenis — API similar mas com data-attributes extras
const scroll = new LocomotiveScroll({
  el: document.querySelector("[data-scroll-container]"),
  smooth: true,
  multiplier: 1,
  class: "is-inview",             // classe adicionada ao entrar
  initPosition: { x: 0, y: 0 },
  getDirection: true,
  getSpeed: true,
});

// Data-attributes no HTML (v5)
// data-scroll                           → observar elemento
// data-scroll-speed="2"                → velocidade de parallax
// data-scroll-delay="0.2"              → atraso no movimento
// data-scroll-direction="horizontal"   → direção de parallax
// data-scroll-position="bottom"        → ponto de trigger
// data-scroll-target="#header"         → triggar outro elemento
// data-scroll-class="is-visible"       → classe ao entrar

// Integrar com GSAP
scroll.on("scroll", ({ scroll, speed }) => {
  ScrollTrigger.update();
  gsap.to(".speed-element", { y: scroll.y * -0.3 }); // parallax manual
});

// Refresh ao redimensionar
window.addEventListener("resize", () => scroll.update());

// Destroy
scroll.destroy();
```

---

## BARBA.JS — PAGE TRANSITIONS

```js
import barba from "@barba/core";
import { gsap } from "gsap";

barba.init({
  // Prevenir transição em links externos e âncoras
  prevent: ({ el }) => {
    return el.classList.contains("no-barba") ||
           el.href.includes(window.location.host) === false;
  },

  // Transitions por namespace de rota
  transitions: [
    // Transição padrão (fallback)
    {
      name: "default-transition",
      async leave(data) {
        const el = data.current.container;
        await gsap.to(el, { opacity: 0, y: -30, duration: 0.4, ease: "power2.in" });
      },
      async enter(data) {
        const el = data.next.container;
        gsap.set(el, { opacity: 0, y: 30 });
        await gsap.to(el, { opacity: 1, y: 0, duration: 0.5, ease: "power2.out" });
      },
    },

    // Transição específica: Home → Projeto
    {
      name: "home-to-project",
      from: { namespace: ["home"] },
      to:   { namespace: ["project"] },

      async leave(data) {
        const clickedCard = data.trigger;
        const state = Flip.getState(clickedCard);

        // FLIP: card vira o hero do próximo projeto
        await Flip.from(state, {
          duration: 0.8,
          ease: "power3.inOut",
          absolute: true,
          onComplete: () => data.current.container.style.display = "none",
        });
      },

      async enter(data) {
        const heroImg = data.next.container.querySelector(".project-hero");
        gsap.from(heroImg, { opacity: 0, scale: 1.05, duration: 0.6 });
        await gsap.from(".project-content", { opacity: 0, y: 30, stagger: 0.1, duration: 0.6 });
      },
    },
  ],

  // Hooks globais
  views: [
    {
      namespace: "home",
      afterEnter() {
        // Reinicializar animações da home
        initHomeAnimations();
        ScrollTrigger.refresh();
      },
      beforeLeave() {
        // Limpar animações antes de sair
        ScrollTrigger.getAll().forEach(t => t.kill());
      },
    },
  ],
});

// Prefetch automático ao hover
barba.use(barbaPrefetch);
```

---

## GSAP PLUGINS PREMIUM — GUIA COMPLETO

```js
// Observer Plugin — mouse/touch/pointer unificado
import { Observer } from "gsap/Observer";
gsap.registerPlugin(Observer);

Observer.create({
  target: window,
  type: "wheel,touch,pointer",
  onDown: () => goToNextSection(),
  onUp: () => goToPrevSection(),
  tolerance: 10,          // mínimo de pixels para trigger
  preventDefault: true,
  onDragStart: (self) => { /* drag iniciado */ },
  onDrag: (self) => {
    // self.deltaX, self.deltaY — delta atual
    // self.velocityX, self.velocityY — velocidade
  },
});

// InertiaPlugin — momentum após drag
import { InertiaPlugin } from "gsap/InertiaPlugin";
gsap.registerPlugin(InertiaPlugin);

const draggable = Draggable.create(".slider", {
  type: "x",
  bounds: ".slider-container",
  inertia: true,
  snap: { x: gsap.utils.snap(300) }, // snap a cada 300px
  onDragEnd() {
    // this.x = posição final
    // this.velocityX = velocidade no final
  },
})[0];

// SplitText v3 — texto partido com resize automático
import { SplitText } from "gsap/SplitText";
gsap.registerPlugin(SplitText);

const split = new SplitText(".headline", {
  type: "chars,words,lines",
  linesClass: "split-line",    // wrapper com overflow:hidden
  wordsClass: "split-word",
  charsClass: "split-char",
  autoSplit: true,             // v3: re-split automaticamente em resize
  onSplit(elements) {
    // Chamado após cada split (incluindo resize)
    // elements.chars, elements.words, elements.lines
    gsap.from(elements.chars, {
      y: "100%",
      opacity: 0,
      stagger: 0.02,
      duration: 0.8,
      ease: "power4.out",
    });
  },
});

// Revert para remover o split e restaurar HTML original
split.revert();
```
