# Referência: Física Avançada, WASM, Motion UI

---

## SOFT BODY PHYSICS

### Position-Based Dynamics (PBD) com Rapier
```js
import { World, RigidBodyDesc, ColliderDesc, ActiveEvents } from "@dimforge/rapier3d";
import * as THREE from "three";

class SoftBodyMesh {
  constructor(scene, physics) {
    this.scene = scene;
    this.physics = physics;
    this.mesh = null;
    this.particles = [];
    this.constraints = [];
    this.restLengths = new Map();
  }

  // Criar soft body a partir de geometria THREE.js
  fromGeometry(geometry, stiffness = 0.3) {
    const positions = geometry.attributes.position;
    const indices = geometry.index?.array || [];

    // Criar partícula para cada vértice
    for (let i = 0; i < positions.count; i++) {
      const pos = new THREE.Vector3().fromBufferAttribute(positions, i);

      const rigidBodyDesc = RigidBodyDesc.dynamic()
        .setTranslation(pos.x, pos.y, pos.z)
        .setLinearDamping(0.8)
        .setAngularDamping(0.8);

      const rigidBody = this.physics.createRigidBody(rigidBodyDesc);
      this.particles.push(rigidBody);
    }

    // Constraints de distância entre partículas conectadas
    for (let i = 0; i < indices.length; i += 3) {
      const pairs = [
        [indices[i], indices[i+1]],
        [indices[i+1], indices[i+2]],
        [indices[i+2], indices[i]],
      ];

      pairs.forEach(([a, b]) => {
        const key = `${Math.min(a,b)}-${Math.max(a,b)}`;
        if (this.restLengths.has(key)) return;

        const posA = new THREE.Vector3().fromBufferAttribute(positions, a);
        const posB = new THREE.Vector3().fromBufferAttribute(positions, b);
        this.restLengths.set(key, posA.distanceTo(posB));
        this.constraints.push({ a, b, key });
      });
    }

    this.mesh = new THREE.Mesh(geometry.clone(), new THREE.MeshStandardMaterial());
    this.scene.add(this.mesh);
    return this;
  }

  // Pin vértices específicos (prender ao espaço)
  pin(vertexIndices) {
    vertexIndices.forEach(i => {
      this.particles[i]?.setBodyType(1, true); // 1 = kinematic
    });
    return this;
  }

  // Atualizar mesh com posições das partículas
  update() {
    if (!this.mesh) return;
    const positions = this.mesh.geometry.attributes.position;

    this.particles.forEach((particle, i) => {
      const t = particle.translation();
      positions.setXYZ(i, t.x, t.y, t.z);
    });

    positions.needsUpdate = true;
    this.mesh.geometry.computeVertexNormals();
  }
}
```

### Cloth Physics (Mass-Spring System)
```js
// Cloth simulation com verlet integration
class ClothSimulation {
  constructor(width, height, segments = 20) {
    this.width = width;
    this.height = height;
    this.segments = segments;

    const cols = segments + 1;
    const rows = segments + 1;

    this.particles = [];
    this.constraints = [];

    // Criar partículas
    for (let row = 0; row < rows; row++) {
      for (let col = 0; col < cols; col++) {
        this.particles.push({
          x: (col / segments) * width - width / 2,
          y: 0,
          z: -(row / segments) * height,
          prevX: (col / segments) * width - width / 2,
          prevY: 0,
          prevZ: -(row / segments) * height,
          pinned: row === 0, // prender linha do topo
        });
      }
    }

    const restDist = width / segments;

    // Constraints estruturais
    for (let row = 0; row < rows; row++) {
      for (let col = 0; col < cols; col++) {
        const i = row * cols + col;

        if (col < cols - 1) {
          this.constraints.push({ a: i, b: i + 1, rest: restDist });
        }
        if (row < rows - 1) {
          this.constraints.push({ a: i, b: i + cols, rest: restDist });
        }

        // Shear constraints (diagonais)
        if (col < cols - 1 && row < rows - 1) {
          this.constraints.push({ a: i, b: i + cols + 1, rest: restDist * Math.SQRT2 });
          this.constraints.push({ a: i + 1, b: i + cols, rest: restDist * Math.SQRT2 });
        }

        // Bend constraints (pular 2)
        if (col < cols - 2) {
          this.constraints.push({ a: i, b: i + 2, rest: restDist * 2 });
        }
      }
    }
  }

  applyForces(gravity = -9.81, wind = { x: 0, y: 0, z: 0 }) {
    this.particles.forEach(p => {
      if (p.pinned) return;

      // Gravidade
      const vy = p.y - p.prevY + gravity * 0.016 * 0.016;

      // Vento (com noise)
      const windX = wind.x + (Math.random() - 0.5) * 0.5;
      const windZ = wind.z + (Math.random() - 0.5) * 0.5;

      p.prevX = p.x; p.prevY = p.y; p.prevZ = p.z;

      p.x += (p.x - p.prevX) * 0.98 + windX * 0.01;
      p.y += vy;
      p.z += (p.z - p.prevZ) * 0.98 + windZ * 0.01;
    });
  }

  satisfyConstraints(iterations = 5) {
    for (let iter = 0; iter < iterations; iter++) {
      this.constraints.forEach(({ a, b, rest }) => {
        const pa = this.particles[a];
        const pb = this.particles[b];

        const dx = pb.x - pa.x;
        const dy = pb.y - pa.y;
        const dz = pb.z - pa.z;
        const dist = Math.sqrt(dx*dx + dy*dy + dz*dz);
        const diff = (dist - rest) / dist;

        const offsetX = dx * diff * 0.5;
        const offsetY = dy * diff * 0.5;
        const offsetZ = dz * diff * 0.5;

        if (!pa.pinned) { pa.x += offsetX; pa.y += offsetY; pa.z += offsetZ; }
        if (!pb.pinned) { pb.x -= offsetX; pb.y -= offsetY; pb.z -= offsetZ; }
      });
    }
  }

  updateGeometry(geometry) {
    const positions = geometry.attributes.position;
    this.particles.forEach((p, i) => {
      positions.setXYZ(i, p.x, p.y, p.z);
    });
    positions.needsUpdate = true;
    geometry.computeVertexNormals();
  }

  step() {
    this.applyForces(-20, { x: Math.sin(Date.now() * 0.001) * 5, y: 0, z: 0 });
    this.satisfyConstraints();
  }
}
```

---

## CANVAS-ONLY DOM-LESS UI

```js
// UI renderizada 100% em Canvas — padrão Figma, Notion
class CanvasUI {
  constructor(canvas) {
    this.canvas = canvas;
    this.ctx = canvas.getContext("2d");
    this.elements = [];
    this.hoveredEl = null;
    this.clickedEl = null;

    this.bindEvents();
    this.startRenderLoop();
  }

  bindEvents() {
    this.canvas.addEventListener("mousemove", (e) => {
      const { x, y } = this.getCanvasPos(e);
      this.hoveredEl = this.hitTest(x, y);
      this.canvas.style.cursor = this.hoveredEl?.cursor || "default";
      this.render();
    });

    this.canvas.addEventListener("click", (e) => {
      const { x, y } = this.getCanvasPos(e);
      const el = this.hitTest(x, y);
      el?.onClick?.();
    });
  }

  getCanvasPos(e) {
    const rect = this.canvas.getBoundingClientRect();
    const dpr = window.devicePixelRatio;
    return {
      x: (e.clientX - rect.left) * dpr,
      y: (e.clientY - rect.top) * dpr,
    };
  }

  hitTest(x, y) {
    // Testar do topo ao fundo (z-order)
    for (let i = this.elements.length - 1; i >= 0; i--) {
      const el = this.elements[i];
      if (el.hitTest && el.hitTest(x, y)) return el;
    }
    return null;
  }

  addElement(el) {
    this.elements.push(el);
    return el;
  }

  startRenderLoop() {
    const render = () => {
      this.render();
      requestAnimationFrame(render);
    };
    requestAnimationFrame(render);
  }

  render() {
    const ctx = this.ctx;
    const dpr = window.devicePixelRatio;

    ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
    ctx.scale(dpr, dpr); // HiDPI

    this.elements.forEach(el => el.render(ctx, el === this.hoveredEl));

    ctx.resetTransform();
  }
}

// Elementos Canvas UI
class CanvasButton {
  constructor(x, y, width, height, label, onClick) {
    this.x = x; this.y = y; this.width = width; this.height = height;
    this.label = label; this.onClick = onClick;
    this.cursor = "pointer";
    this.hoverProgress = 0;
  }

  hitTest(mx, my) {
    return mx >= this.x && mx <= this.x + this.width &&
           my >= this.y && my <= this.y + this.height;
  }

  render(ctx, hovered) {
    // Animate hover
    this.hoverProgress += (hovered ? 1 : -1) * 0.1;
    this.hoverProgress = Math.max(0, Math.min(1, this.hoverProgress));

    const bg = `rgba(99, 102, 241, ${0.8 + this.hoverProgress * 0.2})`;

    // Background
    ctx.fillStyle = bg;
    ctx.beginPath();
    ctx.roundRect(this.x, this.y, this.width, this.height, 8);
    ctx.fill();

    // Shadow on hover
    if (this.hoverProgress > 0) {
      ctx.shadowColor = "rgba(99, 102, 241, 0.4)";
      ctx.shadowBlur = this.hoverProgress * 20;
    }

    // Label
    ctx.fillStyle = "white";
    ctx.font = `500 14px Inter, sans-serif`;
    ctx.textAlign = "center";
    ctx.textBaseline = "middle";
    ctx.fillText(this.label, this.x + this.width / 2, this.y + this.height / 2);

    ctx.shadowColor = "transparent";
    ctx.shadowBlur = 0;
  }
}

// Lista virtualizada em Canvas — 1 MILHÃO de items sem lag
class VirtualList {
  constructor(x, y, width, height, itemHeight, items) {
    this.x = x; this.y = y; this.width = width; this.height = height;
    this.itemHeight = itemHeight;
    this.items = items;
    this.scrollY = 0;
  }

  render(ctx) {
    const visibleStart = Math.floor(this.scrollY / this.itemHeight);
    const visibleEnd = Math.ceil((this.scrollY + this.height) / this.itemHeight);

    ctx.save();
    ctx.beginPath();
    ctx.rect(this.x, this.y, this.width, this.height);
    ctx.clip();

    for (let i = visibleStart; i <= Math.min(visibleEnd, this.items.length - 1); i++) {
      const item = this.items[i];
      const itemY = this.y + i * this.itemHeight - this.scrollY;

      // Renderizar item
      ctx.fillStyle = i % 2 === 0 ? "#fafafa" : "white";
      ctx.fillRect(this.x, itemY, this.width, this.itemHeight);

      ctx.fillStyle = "#111";
      ctx.font = "14px Inter";
      ctx.textBaseline = "middle";
      ctx.fillText(item.label, this.x + 16, itemY + this.itemHeight / 2);
    }

    ctx.restore();
  }
}
```

---

## WEBASSEMBLY — QUANDO E COMO USAR

```
QUANDO USAR WASM:
✅ Processamento de imagem (filtros, compressão, encoding)
✅ Algoritmos de física complexos (já compilados em C++)
✅ Decoders de formatos (AVIF, JXL, HEIC)
✅ Criptografia e hashing
✅ Processar áudio (DSP)
✅ Machine learning (ONNX Runtime)

QUANDO NÃO USAR:
❌ Lógica de UI simples (JS é suficiente)
❌ Acesso ao DOM (WASM não tem acesso direto)
❌ Quando o bottleneck é I/O, não CPU
```

```js
// Exemplo: usar WASM para processamento pesado
// Rapier.js é Rust→WASM — já é WASM!

// ImageMagick WASM para processamento de imagem
import { ImageMagick, initializeImageMagick } from "@imagemagick/magick-wasm";

await initializeImageMagick();

async function processImage(file) {
  const bytes = await file.arrayBuffer();
  const data = new Uint8Array(bytes);

  return new Promise((resolve) => {
    ImageMagick.read(data, (image) => {
      // Operações pesadas em WASM — muito mais rápido que JS
      image.resize(800, 600);
      image.blur(0, 3);
      image.quality = 85;

      image.write(MagickFormat.Webp, (output) => {
        resolve(new Blob([output], { type: "image/webp" }));
      });
    });
  });
}

// ONNX Runtime WASM — inferência de ML
import * as ort from "onnxruntime-web";

const session = await ort.InferenceSession.create("/model.onnx", {
  executionProviders: ["webgpu", "wasm"], // tentar WebGPU, fallback WASM
});

async function runInference(imageData) {
  const tensor = new ort.Tensor("float32", imageData, [1, 3, 224, 224]);
  const { output } = await session.run({ input: tensor });
  return output.data;
}
```

---

## HOVER REVEALS — BIBLIOTECA COMPLETA

```jsx
// 1. Clip-path reveal
function ClipReveal({ children, revealContent }) {
  const [hovered, setHovered] = useState(false);
  return (
    <div
      style={{ position: "relative", overflow: "hidden" }}
      onMouseEnter={() => setHovered(true)}
      onMouseLeave={() => setHovered(false)}
    >
      {children}
      <motion.div
        style={{ position: "absolute", inset: 0, background: "var(--color-primary)" }}
        animate={{ clipPath: hovered
          ? "inset(0% 0% 0% 0%)"
          : "inset(0% 100% 0% 0%)" }}
        transition={{ duration: 0.4, ease: [0.16, 1, 0.3, 1] }}
      >
        {revealContent}
      </motion.div>
    </div>
  );
}

// 2. Image cursor follower
function ImageFollower({ images }) {
  const followerRef = useRef(null);
  const [activeImage, setActiveImage] = useState(null);
  const x = useMotionValue(-200);
  const y = useMotionValue(-200);
  const springX = useSpring(x, { stiffness: 300, damping: 30 });
  const springY = useSpring(y, { stiffness: 300, damping: 30 });

  return (
    <>
      {/* Imagem que segue o cursor */}
      <motion.div
        ref={followerRef}
        style={{
          position: "fixed", top: 0, left: 0,
          x: springX, y: springY,
          translateX: "-50%", translateY: "-50%",
          pointerEvents: "none", zIndex: 9999,
          width: 200, height: 250,
        }}
        animate={{ opacity: activeImage ? 1 : 0, scale: activeImage ? 1 : 0.8 }}
      >
        {activeImage && <img src={activeImage} alt="" style={{ width: "100%", height: "100%", objectFit: "cover", borderRadius: 8 }} />}
      </motion.div>

      {/* Links com imagem de hover */}
      {images.map((item, i) => (
        <div
          key={i}
          onMouseMove={(e) => { x.set(e.clientX); y.set(e.clientY); }}
          onMouseEnter={() => setActiveImage(item.image)}
          onMouseLeave={() => setActiveImage(null)}
        >
          {item.label}
        </div>
      ))}
    </>
  );
}

// 3. Text scramble no hover
function ScrambleText({ text }) {
  const ref = useRef(null);
  const chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!@#$";
  let interval = null;
  let iteration = 0;

  const scramble = () => {
    clearInterval(interval);
    iteration = 0;

    interval = setInterval(() => {
      ref.current.innerText = text
        .split("")
        .map((char, i) => {
          if (i < iteration) return text[i];
          return char === " " ? " " : chars[Math.floor(Math.random() * chars.length)];
        })
        .join("");

      if (iteration >= text.length) clearInterval(interval);
      iteration += 1 / 3;
    }, 30);
  };

  return (
    <span
      ref={ref}
      onMouseEnter={scramble}
      style={{ fontFamily: "monospace", cursor: "pointer" }}
    >
      {text}
    </span>
  );
}

// 4. 3D tilt card
function TiltCard({ children }) {
  const ref = useRef(null);
  const [tilted, setTilted] = useState({ x: 0, y: 0 });

  const handleMouseMove = (e) => {
    const rect = ref.current.getBoundingClientRect();
    const x = (e.clientY - rect.top - rect.height / 2) / (rect.height / 2);
    const y = (e.clientX - rect.left - rect.width / 2) / (rect.width / 2);
    setTilted({ x: -x * 15, y: y * 15 });
  };

  return (
    <motion.div
      ref={ref}
      onMouseMove={handleMouseMove}
      onMouseLeave={() => setTilted({ x: 0, y: 0 })}
      animate={{ rotateX: tilted.x, rotateY: tilted.y }}
      transition={{ type: "spring", stiffness: 300, damping: 30 }}
      style={{ transformStyle: "preserve-3d", perspective: 1000 }}
    >
      {children}
    </motion.div>
  );
}

// 5. Neighbor element reaction
function NeighborReaction({ items }) {
  const [hoveredIndex, setHoveredIndex] = useState(-1);

  const getScale = (i) => {
    if (hoveredIndex === -1) return 1;
    const dist = Math.abs(i - hoveredIndex);
    if (dist === 0) return 1.3;
    if (dist === 1) return 1.15;
    if (dist === 2) return 1.05;
    return 1;
  };

  return (
    <div style={{ display: "flex", gap: "0.5rem" }}>
      {items.map((item, i) => (
        <motion.div
          key={i}
          animate={{ scale: getScale(i) }}
          transition={{ type: "spring", stiffness: 400, damping: 30 }}
          onMouseEnter={() => setHoveredIndex(i)}
          onMouseLeave={() => setHoveredIndex(-1)}
          style={{ transformOrigin: "bottom center" }}
        >
          {item}
        </motion.div>
      ))}
    </div>
  );
}
```

---

## FRAMER MOTION AVANÇADO

```jsx
// AnimationControls para controle imperativo
function ControlledAnimation() {
  const controls = useAnimation();

  const playSequence = async () => {
    await controls.start({ opacity: 1, y: 0 });
    await controls.start({ scale: 1.05 });
    await controls.start({ scale: 1 });
  };

  return (
    <motion.div animate={controls} initial={{ opacity: 0, y: 20 }}>
      <button onClick={playSequence}>Play</button>
    </motion.div>
  );
}

// useTransform — pipeline de valores
function TransformChain({ scrollY }) {
  // Compor transformações
  const opacity = useTransform(scrollY, [0, 300], [1, 0]);
  const scale   = useTransform(scrollY, [0, 300], [1, 0.8]);
  const blur    = useTransform(scrollY, [0, 300], [0, 10]);

  // Calcular filter como string
  const filter  = useTransform(blur, v => `blur(${v}px)`);

  return (
    <motion.div style={{ opacity, scale, filter }}>
      Conteúdo que desvanece com scroll
    </motion.div>
  );
}

// useVelocity — velocidade como valor animável
function VelocityEffect({ scrollY }) {
  const scrollVelocity = useVelocity(scrollY);
  const skewX = useTransform(scrollVelocity, [-500, 500], [-10, 10]);
  const smoothSkew = useSpring(skewX, { stiffness: 100, damping: 30 });

  return <motion.div style={{ skewX: smoothSkew }}>Texto com skew de velocidade</motion.div>;
}

// Drag + Layout animations (arrastar e reordenar)
function DraggableList({ items }) {
  const [order, setOrder] = useState(items.map((_, i) => i));

  return (
    <Reorder.Group axis="y" values={order} onReorder={setOrder}>
      {order.map(i => (
        <Reorder.Item
          key={i}
          value={i}
          layout
          whileDrag={{ scale: 1.05, boxShadow: "0 10px 30px rgba(0,0,0,0.2)" }}
          transition={{ type: "spring", stiffness: 500, damping: 40 }}
        >
          {items[i]}
        </Reorder.Item>
      ))}
    </Reorder.Group>
  );
}

// SSR com Framer Motion
// Em Next.js, usar LazyMotion para reduzir bundle em SSR
import { LazyMotion, domAnimation, m } from "framer-motion";

function SSRSafeComponent() {
  return (
    <LazyMotion features={domAnimation}>
      <m.div initial={{ opacity: 0 }} animate={{ opacity: 1 }}>
        Funciona no SSR
      </m.div>
    </LazyMotion>
  );
}
```
