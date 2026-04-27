# Referência: Animação Avançada

## GSAP Avançado

### Timeline orquestração mestre
```js
// Timeline mestre que coordena sub-timelines
function createPageTimeline() {
  const master = gsap.timeline({ paused: true });

  const headerTL = gsap.timeline()
    .from(".header-logo", { y: -30, opacity: 0, duration: 0.6, ease: "power3.out" })
    .from(".header-nav a", { y: -20, opacity: 0, stagger: 0.08, duration: 0.5 }, "-=0.3");

  const heroTL = gsap.timeline()
    .from(".hero-eyebrow", { y: 20, opacity: 0, duration: 0.5 })
    .from(".hero-title .char", { y: 80, opacity: 0, stagger: 0.02, duration: 0.8, ease: "power4.out" }, "-=0.2")
    .from(".hero-subtitle", { y: 30, opacity: 0, duration: 0.6 }, "-=0.3")
    .from(".hero-cta", { y: 20, opacity: 0, scale: 0.9, duration: 0.5, ease: "back.out(1.5)" }, "-=0.2")
    .from(".hero-media", { scale: 1.05, opacity: 0, duration: 1.2, ease: "power2.out" }, "-=0.8");

  master
    .add(headerTL)
    .add(heroTL, "+=0.1")  // label ou offset
    .add(() => initScrollAnimations(), ">");  // callback no final

  return master;
}

// timeScale para acelerar/desacelerar toda a timeline
const tl = createPageTimeline();
tl.timeScale(1.5).play(); // 1.5x mais rápido

// Reverter com curva diferente
tl.reverse();
tl.reversed(true).timeScale(2); // reverter 2x mais rápido
```

### FLIP Animations
```js
import { Flip } from "gsap/Flip";
gsap.registerPlugin(Flip);

// Capturar estado ANTES da mudança
function animateLayoutChange(container, callback) {
  const state = Flip.getState(container.querySelectorAll(".item"));

  callback(); // mudar o DOM (reordenar, adicionar, remover)

  Flip.from(state, {
    duration: 0.6,
    ease: "power2.inOut",
    stagger: 0.05,
    absolute: true, // elementos absolutos durante animação
    onEnter: (els) => gsap.fromTo(els, { opacity: 0, scale: 0.8 }, { opacity: 1, scale: 1 }),
    onLeave: (els) => gsap.to(els, { opacity: 0, scale: 0.8 }),
  });
}
```

### Motion Path
```js
import { MotionPathPlugin } from "gsap/MotionPathPlugin";
gsap.registerPlugin(MotionPathPlugin);

// Elemento que segue um SVG path
gsap.to(".rocket", {
  duration: 5,
  ease: "none",
  repeat: -1,
  motionPath: {
    path: "#orbit-path",  // SVG <path> element
    align: "#orbit-path",
    autoRotate: true,     // orientar na direção do movimento
    alignOrigin: [0.5, 0.5],
    start: 0,
    end: 1,
  },
});
```

### Text Effects
```js
// ScrambleText (Matrix effect)
import { ScrambleTextPlugin } from "gsap/ScrambleTextPlugin";
gsap.registerPlugin(ScrambleTextPlugin);

gsap.to(".title", {
  duration: 1.5,
  scrambleText: {
    text: "Design de Alto Nível",
    chars: "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789",
    revealDelay: 0.5,
    speed: 0.4,
  }
});

// Typewriter manual
function typewrite(el, text, speed = 50) {
  el.textContent = "";
  let i = 0;
  const interval = setInterval(() => {
    el.textContent += text[i++];
    if (i >= text.length) clearInterval(interval);
  }, speed);
}

// Reveal por máscara clip-path
gsap.fromTo(".reveal-text", {
  clipPath: "inset(0 100% 0 0)",
}, {
  clipPath: "inset(0 0% 0 0)",
  duration: 0.8,
  ease: "power4.out",
  stagger: 0.15,
});
```

## Lenis Smooth Scroll — Configuração completa
```js
import Lenis from "lenis";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

class SmoothScroll {
  constructor(options = {}) {
    this.lenis = new Lenis({
      duration: options.duration ?? 1.2,
      easing: options.easing ?? ((t) => Math.min(1, 1.001 - Math.pow(2, -10 * t))),
      smoothWheel: true,
      touchMultiplier: 2,
      infinite: options.infinite ?? false,
    });

    // Sync com GSAP
    this.lenis.on("scroll", ScrollTrigger.update);
    gsap.ticker.add((time) => this.lenis.raf(time * 1000));
    gsap.ticker.lagSmoothing(0);

    // Scroll para elemento
    this.scrollTo = (target, options) => this.lenis.scrollTo(target, options);

    // Pause em modais
    this.pause = () => this.lenis.stop();
    this.resume = () => this.lenis.start();
  }

  destroy() {
    this.lenis.destroy();
    gsap.ticker.remove((time) => this.lenis.raf(time * 1000));
  }
}

// Uso
const scroll = new SmoothScroll();

// Pausar ao abrir modal
document.querySelector(".modal").addEventListener("open", () => scroll.pause());
document.querySelector(".modal").addEventListener("close", () => scroll.resume());
```

## Anime.js — quando usar
```js
import anime from "animejs";

// Anime.js brilha em SVG animations complexas
anime({
  targets: ".svg-path",
  strokeDashoffset: [anime.setDashoffset, 0],
  easing: "easeInOutSine",
  duration: 1500,
  delay: (el, i) => i * 250,
  direction: "alternate",
  loop: true,
});

// Multi-property com keyframes
anime({
  targets: ".box",
  keyframes: [
    { translateX: 0, scale: 1 },
    { translateX: 200, scale: 1.2, duration: 600 },
    { translateX: 200, scale: 1, duration: 400 },
    { translateX: 0, scale: 1, duration: 600 },
  ],
  easing: "easeOutElastic(1, .8)",
  loop: true,
});
```

## Web Animations API (nativa)
```js
// Sem bibliotecas — suportado em todos os browsers modernos
const el = document.querySelector(".element");

// Básico
const anim = el.animate(
  [{ opacity: 0, transform: "translateY(30px)" },
   { opacity: 1, transform: "translateY(0)" }],
  { duration: 600, easing: "cubic-bezier(0.16, 1, 0.3, 1)", fill: "both" }
);

// Controle
anim.pause();
anim.play();
anim.cancel();
anim.reverse();
anim.currentTime = 300; // pular para 300ms

// Aguardar fim
anim.finished.then(() => console.log("Animação concluída"));

// GroupEffect (animações paralelas)
const group = new GroupEffect([
  new KeyframeEffect(el1, [{opacity: 0}, {opacity: 1}], 600),
  new KeyframeEffect(el2, [{transform: "scale(0)"}, {transform: "scale(1)"}], 600),
]);
document.timeline.play(group);
```

## Spring physics do zero
```js
// Simular spring sem biblioteca
class Spring {
  constructor({ stiffness = 200, damping = 25, mass = 1 } = {}) {
    this.stiffness = stiffness;
    this.damping = damping;
    this.mass = mass;
    this.position = 0;
    this.velocity = 0;
    this.target = 0;
  }

  update(dt) {
    // Euler integration (simples)
    const force = -this.stiffness * (this.position - this.target)
                  - this.damping * this.velocity;
    this.velocity += (force / this.mass) * dt;
    this.position += this.velocity * dt;
    return Math.abs(this.velocity) < 0.001 && Math.abs(this.position - this.target) < 0.001;
  }

  setTarget(t) { this.target = t; }
}

// Animação de chain (cabelo, colar)
class ChainSpring {
  constructor(length = 10) {
    this.links = Array.from({ length }, (_, i) => new Spring({ stiffness: 150 + i * 20, damping: 20 }));
  }

  update(dt, headX) {
    this.links[0].setTarget(headX);
    this.links[0].update(dt);
    for (let i = 1; i < this.links.length; i++) {
      this.links[i].setTarget(this.links[i-1].position);
      this.links[i].update(dt);
    }
    return this.links.map(l => l.position);
  }
}
```

## Campos magnéticos entre elementos
```js
// N-body simulation
function MagneticField({ elements, mouse }) {
  const REPEL_RADIUS = 150;
  const ATTRACT_RADIUS = 300;
  const FORCE_STRENGTH = 0.05;

  elements.forEach(el => {
    const rect = el.getBoundingClientRect();
    const cx = rect.left + rect.width / 2;
    const cy = rect.top + rect.height / 2;
    const dx = mouse.x - cx;
    const dy = mouse.y - cy;
    const dist = Math.sqrt(dx*dx + dy*dy);

    if (dist < REPEL_RADIUS) {
      // Repulsão do cursor
      const force = (REPEL_RADIUS - dist) / REPEL_RADIUS;
      gsap.to(el, {
        x: -dx * force * FORCE_STRENGTH * 3,
        y: -dy * force * FORCE_STRENGTH * 3,
        duration: 0.3,
        ease: "power2.out",
      });
    } else {
      // Voltar suavemente
      gsap.to(el, { x: 0, y: 0, duration: 0.8, ease: "elastic.out(1, 0.5)" });
    }
  });
}
```

## Canvas 2D — Generative art
```js
// Flow field com Perlin noise via sin
function FlowFieldArt(canvas) {
  const ctx = canvas.getContext("2d");
  const W = canvas.width;
  const H = canvas.height;

  let particles = Array.from({ length: 500 }, () => ({
    x: Math.random() * W,
    y: Math.random() * H,
    vx: 0, vy: 0,
    life: Math.random(),
    color: `hsl(${220 + Math.random() * 80}, 70%, 60%)`,
  }));

  let t = 0;
  function animate() {
    ctx.fillStyle = "rgba(10,10,10,0.05)";
    ctx.fillRect(0, 0, W, H);

    particles.forEach(p => {
      const angle = (Math.sin(p.x * 0.008 + t) + Math.cos(p.y * 0.006 + t * 0.7)) * Math.PI;
      p.vx = Math.cos(angle) * 2;
      p.vy = Math.sin(angle) * 2;
      p.x += p.vx;
      p.y += p.vy;
      p.life -= 0.003;

      if (p.life <= 0 || p.x < 0 || p.x > W || p.y < 0 || p.y > H) {
        p.x = Math.random() * W;
        p.y = Math.random() * H;
        p.life = 1;
      }

      ctx.globalAlpha = p.life * 0.4;
      ctx.fillStyle = p.color;
      ctx.beginPath();
      ctx.arc(p.x, p.y, 1.2, 0, Math.PI * 2);
      ctx.fill();
    });

    ctx.globalAlpha = 1;
    t += 0.003;
    requestAnimationFrame(animate);
  }

  animate();
}
```

## SVG Morphing
```js
import { MorphSVGPlugin, DrawSVGPlugin } from "gsap/all";
gsap.registerPlugin(MorphSVGPlugin, DrawSVGPlugin);

// Morph entre shapes
gsap.to("#icon", {
  morphSVG: { shape: "#target-shape", type: "rotational" },
  duration: 0.8,
  ease: "power2.inOut",
});

// Draw SVG (linha que se desenha)
gsap.from(".path", {
  drawSVG: "0%",
  duration: 2,
  ease: "power2.inOut",
  stagger: 0.2,
});

// SVG filter animado
gsap.to("feTurbulence", {
  attr: { baseFrequency: "0.9 0.9" },
  duration: 2,
  yoyo: true,
  repeat: -1,
  ease: "sine.inOut",
});
```
