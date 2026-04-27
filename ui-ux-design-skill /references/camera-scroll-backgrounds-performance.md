# Referência: Câmera + Scroll, Backgrounds Premium, Performance Total

---

## CÂMERA 3D COM SCROLL — SISTEMA COMPLETO

### Camera path com CatmullRomCurve3
```jsx
import { useRef, useMemo } from "react";
import { useFrame, useThree } from "@react-three/fiber";
import * as THREE from "three";

function CameraScrollPath({ scrollProgress }) {
  const { camera } = useThree();

  // Definir pontos da trajetória da câmera
  const cameraPath = useMemo(() => new THREE.CatmullRomCurve3([
    new THREE.Vector3(0, 2, 10),     // início
    new THREE.Vector3(-3, 4, 6),     // curva esquerda
    new THREE.Vector3(0, 1, 2),      // aproximação do objeto
    new THREE.Vector3(3, 3, -2),     // gira ao redor
    new THREE.Vector3(0, 5, -8),     // atrás do objeto
  ], false, "catmullrom", 0.5), []);

  // Definir pontos de "olhar para" (LookAt path)
  const targetPath = useMemo(() => new THREE.CatmullRomCurve3([
    new THREE.Vector3(0, 0, 0),
    new THREE.Vector3(0, 0, 0),
    new THREE.Vector3(0, 1, 0),
    new THREE.Vector3(0, 0, 0),
    new THREE.Vector3(0, 0, 0),
  ], false, "catmullrom", 0.5), []);

  const currentTarget = useMemo(() => new THREE.Vector3(), []);
  const currentPos    = useMemo(() => new THREE.Vector3(), []);

  useFrame(() => {
    const t = Math.max(0, Math.min(1, scrollProgress));

    // Posição da câmera
    cameraPath.getPoint(t, currentPos);
    camera.position.lerp(currentPos, 0.1); // lerp para suavidade

    // Ponto de foco
    targetPath.getPoint(t, currentTarget);
    camera.lookAt(currentTarget);
  });

  return null;
}

// Hook para conectar Lenis/scroll ao progresso
function useScrollProgress() {
  const [progress, setProgress] = useState(0);

  useEffect(() => {
    const updateProgress = () => {
      const scrollTop = window.scrollY;
      const docHeight = document.documentElement.scrollHeight - window.innerHeight;
      setProgress(scrollTop / docHeight);
    };
    window.addEventListener("scroll", updateProgress, { passive: true });
    return () => window.removeEventListener("scroll", updateProgress);
  }, []);

  return progress;
}

// Usar com ScrollTrigger para seções específicas
function useSectionScrollProgress(sectionRef) {
  const [progress, setProgress] = useState(0);

  useEffect(() => {
    const trigger = ScrollTrigger.create({
      trigger: sectionRef.current,
      start: "top top",
      end: "bottom bottom",
      onUpdate: (self) => setProgress(self.progress),
    });
    return () => trigger.kill();
  }, []);

  return progress;
}
```

### Dolly Zoom (Efeito Vertigo)
```jsx
function DollyZoom({ targetFOV = 20, initialFOV = 75, progress }) {
  const { camera } = useThree();
  const initialDist = useRef(5);

  useFrame(() => {
    // FOV diminui enquanto câmera avança
    const fov = THREE.MathUtils.lerp(initialFOV, targetFOV, progress);
    camera.fov = fov;
    camera.updateProjectionMatrix();

    // Compensar com posição para manter objeto mesmo tamanho
    const newDist = initialDist.current * Math.tan((initialFOV / 2) * Math.PI / 180)
                  / Math.tan((fov / 2) * Math.PI / 180);
    camera.position.z = newDist;
  });

  return null;
}
```

### Camera shake
```jsx
function CameraShake({ intensity = 0.1, active = false }) {
  const { camera } = useThree();
  const origin = useMemo(() => camera.position.clone(), []);

  useFrame(({ clock }) => {
    if (!active) {
      camera.position.lerp(origin, 0.1);
      return;
    }
    const t = clock.elapsedTime * 15;
    camera.position.x = origin.x + (Math.sin(t * 1.1) + Math.sin(t * 0.7)) * intensity * 0.5;
    camera.position.y = origin.y + (Math.cos(t * 0.9) + Math.cos(t * 1.3)) * intensity * 0.5;
  });

  return null;
}
```

### Cinematic flythrough completo
```jsx
function CinematicScene() {
  const sectionRef = useRef();
  const progress = useSectionScrollProgress(sectionRef);

  return (
    <section ref={sectionRef} style={{ height: "400vh" }}>
      {/* Canvas fixo durante scroll */}
      <div style={{ position: "sticky", top: 0, height: "100vh" }}>
        <Canvas>
          <CameraScrollPath scrollProgress={progress} />
          <DollyZoom progress={progress * 2} />
          <CameraShake intensity={0.02} active={progress > 0.8} />

          {/* Cena */}
          <Environment preset="night" />
          <ProductModel />
          <ParticleField />

          <EffectComposer>
            <DepthOfField focusDistance={progress * 0.1} focalLength={0.05} bokehScale={3} />
            <Bloom intensity={0.3 + progress * 0.7} />
          </EffectComposer>
        </Canvas>

        {/* Texto overlay sincronizado */}
        <ChapterText progress={progress} chapters={[
          { from: 0, to: 0.25, text: "Apresentando o produto" },
          { from: 0.25, to: 0.6, text: "Detalhes que importam" },
          { from: 0.6, to: 1,   text: "A experiência completa" },
        ]} />
      </div>
    </section>
  );
}
```

---

## BACKGROUNDS PREMIUM — SISTEMA DE DECISÃO

### Quando usar cada tipo
```
Luxury / Fashion:   Noise orgânico pesado + gradiente escuro
Tech / SaaS:        Grid de pontos animado + gradiente sutil
Criativo / Agência: Fluid WebGL + partículas
Editorial:          Fundo liso + grain sutil (sem distração)
Wellness:           Gradiente suave + breathing mesh
Gaming:             Partículas neon + efeitos de energia
Startup:            Gradient mesh OKLCH animado
Govtech:            Fundo branco puro, zero distração
Fintech:            Grid de linhas + gradiente azul profundo
```

### Grid de pontos interativo (premium para tech)
```js
class DotGrid {
  constructor(canvas) {
    this.canvas = canvas;
    this.ctx = canvas.getContext("2d");
    this.dots = [];
    this.mouse = { x: -1000, y: -1000 };
    this.spacing = 32;
    this.init();
  }

  init() {
    const cols = Math.ceil(this.canvas.width / this.spacing) + 1;
    const rows = Math.ceil(this.canvas.height / this.spacing) + 1;

    for (let x = 0; x < cols; x++) {
      for (let y = 0; y < rows; y++) {
        this.dots.push({
          baseX: x * this.spacing,
          baseY: y * this.spacing,
          x: x * this.spacing,
          y: y * this.spacing,
          vx: 0, vy: 0,
        });
      }
    }
  }

  update() {
    this.dots.forEach(dot => {
      const dx = dot.x - this.mouse.x;
      const dy = dot.y - this.mouse.y;
      const dist = Math.sqrt(dx * dx + dy * dy);
      const maxDist = 120;

      if (dist < maxDist) {
        const force = (maxDist - dist) / maxDist;
        dot.vx += dx / dist * force * 3;
        dot.vy += dy / dist * force * 3;
      }

      // Spring back to origin
      dot.vx += (dot.baseX - dot.x) * 0.08;
      dot.vy += (dot.baseY - dot.y) * 0.08;

      // Damping
      dot.vx *= 0.85;
      dot.vy *= 0.85;

      dot.x += dot.vx;
      dot.y += dot.vy;
    });
  }

  draw(theme = "dark") {
    this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);

    this.dots.forEach(dot => {
      const dx = dot.x - this.mouse.x;
      const dy = dot.y - this.mouse.y;
      const dist = Math.sqrt(dx * dx + dy * dy);
      const maxDist = 120;
      const alpha = dist < maxDist
        ? 0.15 + (1 - dist / maxDist) * 0.6
        : 0.15;

      this.ctx.beginPath();
      this.ctx.arc(dot.x, dot.y, 1.5, 0, Math.PI * 2);
      this.ctx.fillStyle = theme === "dark"
        ? `rgba(255,255,255,${alpha})`
        : `rgba(0,0,0,${alpha})`;
      this.ctx.fill();
    });
  }

  animate() {
    this.update();
    this.draw();
    requestAnimationFrame(() => this.animate());
  }
}
```

### Topographic background (SVG)
```jsx
function TopographicBackground({ lineCount = 12, color = "currentColor" }) {
  const paths = useMemo(() => {
    // Gerar linhas de contorno com noise
    return Array.from({ length: lineCount }, (_, i) => {
      const y = (i / lineCount) * 100;
      const amplitude = 10 + Math.sin(i * 1.5) * 8;
      let d = `M 0 ${y}`;

      for (let x = 0; x <= 100; x += 2) {
        const noise = Math.sin(x * 0.1 + i * 0.7) * amplitude
                    + Math.sin(x * 0.05 + i * 1.2) * amplitude * 0.5;
        d += ` L ${x} ${y + noise}`;
      }
      return d;
    });
  }, [lineCount]);

  return (
    <svg
      viewBox="0 0 100 100"
      preserveAspectRatio="xMidYMid slice"
      style={{ position: "absolute", inset: 0, width: "100%", height: "100%", opacity: 0.15 }}
      aria-hidden="true"
    >
      {paths.map((d, i) => (
        <path
          key={i}
          d={d}
          fill="none"
          stroke={color}
          strokeWidth="0.3"
          opacity={0.4 + (i / lineCount) * 0.6}
        />
      ))}
    </svg>
  );
}
```

### Gradient mesh animado em OKLCH
```css
/* CSS — breathing gradient mesh */
.mesh-bg {
  position: relative;
  overflow: hidden;
}

.mesh-bg::before {
  content: "";
  position: absolute;
  inset: -50%;
  width: 200%;
  height: 200%;
  background:
    radial-gradient(ellipse 60% 50% at 20% 30%, oklch(65% 0.25 260 / 0.4) 0%, transparent 60%),
    radial-gradient(ellipse 50% 60% at 80% 70%, oklch(65% 0.22 300 / 0.35) 0%, transparent 60%),
    radial-gradient(ellipse 70% 40% at 60% 10%, oklch(70% 0.20 220 / 0.3) 0%, transparent 60%),
    radial-gradient(ellipse 40% 70% at 10% 80%, oklch(60% 0.25 240 / 0.35) 0%, transparent 60%);
  animation: mesh-breathe 15s ease-in-out infinite alternate;
}

@keyframes mesh-breathe {
  0%   { transform: translate(0%, 0%) scale(1); }
  25%  { transform: translate(3%, -4%) scale(1.05); }
  50%  { transform: translate(-4%, 3%) scale(0.98); }
  75%  { transform: translate(2%, 5%) scale(1.03); }
  100% { transform: translate(-3%, -2%) scale(1.01); }
}

@media (prefers-reduced-motion: reduce) {
  .mesh-bg::before { animation: none; }
}
```

### Constellation particles (Canvas)
```js
class ConstellationField {
  constructor(canvas, options = {}) {
    this.canvas = canvas;
    this.ctx = canvas.getContext("2d");
    this.opts = {
      count: options.count || 100,
      connectDist: options.connectDist || 150,
      mouseInfluence: options.mouseInfluence || 200,
      color: options.color || "rgba(99,102,241,",
      ...options,
    };
    this.particles = [];
    this.mouse = { x: -999, y: -999 };
    this.init();
  }

  init() {
    for (let i = 0; i < this.opts.count; i++) {
      this.particles.push({
        x: Math.random() * this.canvas.width,
        y: Math.random() * this.canvas.height,
        vx: (Math.random() - 0.5) * 0.5,
        vy: (Math.random() - 0.5) * 0.5,
        r: Math.random() * 2 + 1,
      });
    }
  }

  draw() {
    this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);

    // Mover partículas
    this.particles.forEach(p => {
      // Mouse repulsion
      const dx = p.x - this.mouse.x;
      const dy = p.y - this.mouse.y;
      const dist = Math.sqrt(dx * dx + dy * dy);
      if (dist < this.opts.mouseInfluence) {
        const force = (this.opts.mouseInfluence - dist) / this.opts.mouseInfluence;
        p.vx += dx / dist * force * 0.05;
        p.vy += dy / dist * force * 0.05;
      }

      p.vx *= 0.99; p.vy *= 0.99;
      p.x += p.vx; p.y += p.vy;

      // Bounce
      if (p.x < 0 || p.x > this.canvas.width) p.vx *= -1;
      if (p.y < 0 || p.y > this.canvas.height) p.vy *= -1;

      // Draw dot
      this.ctx.beginPath();
      this.ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
      this.ctx.fillStyle = this.opts.color + "0.7)";
      this.ctx.fill();
    });

    // Connections
    for (let i = 0; i < this.particles.length; i++) {
      for (let j = i + 1; j < this.particles.length; j++) {
        const a = this.particles[i], b = this.particles[j];
        const dx = a.x - b.x, dy = a.y - b.y;
        const dist = Math.sqrt(dx * dx + dy * dy);
        if (dist < this.opts.connectDist) {
          const alpha = 1 - dist / this.opts.connectDist;
          this.ctx.beginPath();
          this.ctx.moveTo(a.x, a.y);
          this.ctx.lineTo(b.x, b.y);
          this.ctx.strokeStyle = this.opts.color + alpha * 0.4 + ")";
          this.ctx.lineWidth = 0.5;
          this.ctx.stroke();
        }
      }
    }
  }

  animate() {
    this.draw();
    this._raf = requestAnimationFrame(() => this.animate());
  }

  destroy() { cancelAnimationFrame(this._raf); }
}
```

---

## PERFORMANCE TOTAL — SISTEMA COMPLETO

### Adaptive quality com FPS monitor
```js
class PerformanceMonitor {
  constructor(onQualityChange) {
    this.fps = 60;
    this.frames = [];
    this.quality = "high";
    this.onQualityChange = onQualityChange;
    this.lastTime = performance.now();
    this.running = false;
  }

  start() {
    this.running = true;
    this.measure();
  }

  measure() {
    if (!this.running) return;

    const now = performance.now();
    const delta = now - this.lastTime;
    this.lastTime = now;

    this.frames.push(1000 / delta);
    if (this.frames.length > 60) this.frames.shift();

    const avgFPS = this.frames.reduce((a, b) => a + b, 0) / this.frames.length;
    this.fps = avgFPS;

    // Degradar qualidade se FPS cair
    const newQuality =
      avgFPS >= 55 ? "high"   :
      avgFPS >= 40 ? "medium" : "low";

    if (newQuality !== this.quality) {
      this.quality = newQuality;
      this.onQualityChange(newQuality);
    }

    requestAnimationFrame(() => this.measure());
  }

  stop() { this.running = false; }
}

// Usar em R3F
function AdaptiveQuality() {
  const [quality, setQuality] = useState("high");
  const monitor = useRef();

  useEffect(() => {
    monitor.current = new PerformanceMonitor(setQuality);
    monitor.current.start();
    return () => monitor.current.stop();
  }, []);

  return (
    <>
      {quality === "high" && <EffectComposer><Bloom /><ChromaticAberration /></EffectComposer>}
      {quality === "medium" && <EffectComposer><Bloom intensity={0.2} /></EffectComposer>}
      {/* low: sem post-processing */}
    </>
  );
}
```

### Bundle splitting para Three.js
```js
// Carregar Three.js apenas quando necessário
const ThreeScene = React.lazy(() =>
  import("./ThreeScene").then(mod => ({
    default: mod.ThreeScene
  }))
);

// Carregar quando seção estiver próxima
function LazyThreeSection() {
  const ref = useRef();
  const [shouldLoad, setShouldLoad] = useState(false);

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setShouldLoad(true);
          observer.disconnect();
        }
      },
      { rootMargin: "500px" } // pré-carregar 500px antes de entrar
    );
    if (ref.current) observer.observe(ref.current);
    return () => observer.disconnect();
  }, []);

  return (
    <div ref={ref} style={{ minHeight: "100vh" }}>
      {shouldLoad && (
        <Suspense fallback={<ThreeSkeleton />}>
          <ThreeScene />
        </Suspense>
      )}
    </div>
  );
}
```

### Memory management — Checklist completo
```js
// Template de componente Three.js sem memory leaks
function ThreeComponent() {
  const sceneRef = useRef();
  const rendererRef = useRef();
  const animRef = useRef();
  const listenersRef = useRef([]);

  useEffect(() => {
    const scene = new THREE.Scene();
    sceneRef.current = scene;

    const renderer = new THREE.WebGLRenderer({
      canvas: canvasRef.current,
      antialias: true,
      powerPreference: "high-performance",
    });
    renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
    rendererRef.current = renderer;

    // Adicionar listener com tracking
    const onResize = () => { /* ... */ };
    window.addEventListener("resize", onResize, { passive: true });
    listenersRef.current.push(() => window.removeEventListener("resize", onResize));

    // RAF com ID para cleanup
    const animate = () => {
      animRef.current = requestAnimationFrame(animate);
      renderer.render(scene, camera);
    };
    animate();

    // CLEANUP TOTAL
    return () => {
      // 1. Parar RAF
      cancelAnimationFrame(animRef.current);

      // 2. Remover todos os event listeners
      listenersRef.current.forEach(cleanup => cleanup());
      listenersRef.current = [];

      // 3. Dispose de toda a cena
      scene.traverse(obj => {
        if (obj.geometry) obj.geometry.dispose();
        if (obj.material) {
          const mats = Array.isArray(obj.material) ? obj.material : [obj.material];
          mats.forEach(m => {
            // Dispose de todas as texturas
            Object.entries(m).forEach(([key, val]) => {
              if (val instanceof THREE.Texture) {
                val.dispose();
              }
            });
            m.dispose();
          });
        }
      });

      // 4. Dispose do renderer
      renderer.dispose();
      renderer.forceContextLoss();

      // 5. Limpar referências
      sceneRef.current = null;
      rendererRef.current = null;
    };
  }, []);
}
```

### Scroll performance — zero jank
```js
// NUNCA fazer isso:
window.addEventListener("scroll", () => {
  const el = document.querySelector(".hero"); // DOM query no scroll
  el.style.transform = `translateY(${window.scrollY * 0.5}px)`; // layout thrashing
});

// SEMPRE fazer isso:
const heroEl = document.querySelector(".hero"); // query uma vez
let ticking = false;
let lastScrollY = 0;

window.addEventListener("scroll", () => {
  lastScrollY = window.scrollY;
  if (!ticking) {
    requestAnimationFrame(() => {
      heroEl.style.transform = `translateY(${lastScrollY * 0.5}px)`;
      ticking = false;
    });
    ticking = true;
  }
}, { passive: true }); // passive: true SEMPRE em scroll

// Ou usar Lenis que já é otimizado:
lenis.on("scroll", ({ scroll }) => {
  gsap.set(".parallax", { y: scroll * 0.3 }); // GSAP usa RAF internamente
});
```

### KTX2 / Basis textures para 3D
```js
import { KTX2Loader } from "three/examples/jsm/loaders/KTX2Loader";
import { DRACOLoader } from "three/examples/jsm/loaders/DRACOLoader";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";

// Setup loaders otimizados
function setupLoaders(renderer) {
  const ktx2Loader = new KTX2Loader()
    .setTranscoderPath("/basis/")
    .detectSupport(renderer); // detectar qual formato o GPU suporta

  const dracoLoader = new DRACOLoader()
    .setDecoderPath("/draco/"); // Draco geometry compression

  const gltfLoader = new GLTFLoader()
    .setKTX2Loader(ktx2Loader)
    .setDRACOLoader(dracoLoader);

  return gltfLoader;
}

// Comprimir assets antes de servir:
// Texturas: npx @gltf-transform/cli optimize model.glb output.glb --texture-compress ktx2
// Geometrias: gltf-pack -i model.glb -o compressed.glb
```
