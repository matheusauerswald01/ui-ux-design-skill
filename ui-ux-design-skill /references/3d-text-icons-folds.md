# Referência: 3D Texto, Ícones, Dobras e Transições

---

## TEXTO 3D — SISTEMA COMPLETO

### TextGeometry extrudado com bevel
```jsx
import { Text3D, Center, Float, MeshTransmissionMaterial } from "@react-three/drei";
import { useRef } from "react";
import { useFrame } from "@react-three/fiber";

function Text3DPremium({ text, color = "#6366f1" }) {
  const ref = useRef();
  useFrame(({ clock }) => {
    ref.current.rotation.y = Math.sin(clock.elapsedTime * 0.3) * 0.1;
  });

  return (
    <Center>
      <Float speed={2} rotationIntensity={0.2} floatIntensity={0.5}>
        <Text3D
          ref={ref}
          font="/fonts/Inter_Bold.json" // converter com facetype.js
          size={1.2}
          height={0.25}           // extrusão
          curveSegments={32}
          bevelEnabled
          bevelThickness={0.04}
          bevelSize={0.02}
          bevelOffset={0}
          bevelSegments={8}
        >
          {text}
          {/* Material transmissivo — vidro premium */}
          <MeshTransmissionMaterial
            backside
            samples={16}
            resolution={512}
            transmission={0.9}
            roughness={0.05}
            thickness={0.3}
            ior={1.5}
            chromaticAberration={0.02}
            color={color}
            envMapIntensity={2}
          />
        </Text3D>
      </Float>
    </Center>
  );
}

// Texto em curva 3D
function CurvedText3D({ text, radius = 3 }) {
  const curve = new THREE.CatmullRomCurve3(
    Array.from({ length: 20 }, (_, i) => {
      const angle = (i / 20) * Math.PI * 2;
      return new THREE.Vector3(
        Math.cos(angle) * radius,
        0,
        Math.sin(angle) * radius
      );
    }),
    true // closed
  );

  // Posicionar cada char ao longo da curva
  return (
    <group>
      {text.split("").map((char, i) => {
        const t = i / text.length;
        const pt = curve.getPoint(t);
        const tangent = curve.getTangent(t);
        return (
          <Text3D
            key={i}
            font="/fonts/Inter_Bold.json"
            size={0.3}
            height={0.05}
            position={pt.toArray()}
            rotation={[0, Math.atan2(tangent.x, tangent.z), 0]}
          >
            {char}
            <meshStandardMaterial color="#ffffff" />
          </Text3D>
        );
      })}
    </group>
  );
}

// Texto neon com emissive
function NeonText3D({ text, neonColor = "#ff0080" }) {
  return (
    <Text3D font="/fonts/Inter_Bold.json" size={1} height={0.05}>
      {text}
      <meshStandardMaterial
        color={neonColor}
        emissive={neonColor}
        emissiveIntensity={2}
        toneMapped={false}
      />
    </Text3D>
  );
}

// Texto que se dissolve em partículas
function DissolveText({ text, dissolveProgress }) {
  const particlesRef = useRef();
  const particleCount = 5000;

  const positions = useMemo(() => {
    // Amostrar posições do texto renderizado
    const pos = new Float32Array(particleCount * 3);
    for (let i = 0; i < particleCount; i++) {
      pos[i * 3]     = (Math.random() - 0.5) * 6;
      pos[i * 3 + 1] = (Math.random() - 0.5) * 2;
      pos[i * 3 + 2] = (Math.random() - 0.5) * 0.5;
    }
    return pos;
  }, []);

  useFrame(() => {
    // Animar dissolve baseado em dissolveProgress (0→1)
    if (particlesRef.current) {
      particlesRef.current.material.uniforms.uDissolve.value = dissolveProgress;
    }
  });

  return (
    <points ref={particlesRef}>
      <bufferGeometry>
        <bufferAttribute attach="attributes-position" args={[positions, 3]} />
      </bufferGeometry>
      <shaderMaterial
        transparent
        uniforms={{ uDissolve: { value: 0 }, uColor: { value: new THREE.Color("#6366f1") } }}
        vertexShader={`
          attribute vec3 position;
          uniform float uDissolve;
          varying float vAlpha;
          void main() {
            vec3 pos = position;
            float rand = fract(sin(dot(position.xy, vec2(127.1, 311.7))) * 43758.5453);
            pos.x += (rand - 0.5) * uDissolve * 3.0;
            pos.y += rand * uDissolve * 2.0;
            pos.z += (rand - 0.5) * uDissolve * 2.0;
            vAlpha = 1.0 - uDissolve * rand;
            gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);
            gl_PointSize = 2.0;
          }
        `}
        fragmentShader={`
          uniform vec3 uColor;
          varying float vAlpha;
          void main() {
            gl_FragColor = vec4(uColor, vAlpha);
          }
        `}
      />
    </points>
  );
}
```

### Converter fonte para JSON (facetype.js)
```bash
# CLI
npx facetype-js font.ttf > public/fonts/font.json

# Ou via browser: facetype.js.com
# Formatos: TTF, OTF → JSON com glyphs para Three.js
```

---

## ÍCONES 3D INTERATIVOS

### SVG → 3D extrudado
```jsx
import { SVGLoader } from "three/examples/jsm/loaders/SVGLoader";
import { useLoader } from "@react-three/fiber";

function SVGIcon3D({ svgUrl, depth = 0.1, color = "#6366f1" }) {
  const svgData = useLoader(SVGLoader, svgUrl);

  const shapes = useMemo(() => {
    return svgData.paths.flatMap(path =>
      SVGLoader.createShapes(path).map(shape => ({ shape, color: path.color }))
    );
  }, [svgData]);

  return (
    <Float speed={3} rotationIntensity={0.3} floatIntensity={0.8}>
      <group scale={[0.01, -0.01, 0.01]} position={[-12, 12, 0]}>
        {shapes.map(({ shape, color: shapeColor }, i) => (
          <mesh key={i}>
            <extrudeGeometry
              args={[shape, {
                depth: depth * 100,
                bevelEnabled: true,
                bevelThickness: 2,
                bevelSize: 1.5,
                bevelSegments: 4,
              }]}
            />
            <meshStandardMaterial
              color={shapeColor || color}
              metalness={0.3}
              roughness={0.2}
              envMapIntensity={1.5}
            />
          </mesh>
        ))}
      </group>
    </Float>
  );
}

// Ícone com hover spring
function HoverIcon3D({ children, hoverColor = "#22c55e" }) {
  const ref = useRef();
  const [hovered, setHovered] = useState(false);
  const { color, scale } = useSpring({
    color: hovered ? hoverColor : "#6366f1",
    scale: hovered ? 1.2 : 1,
    config: { tension: 300, friction: 20 },
  });

  return (
    <animated.group
      ref={ref}
      scale={scale}
      onPointerEnter={() => setHovered(true)}
      onPointerLeave={() => setHovered(false)}
    >
      {children}
    </animated.group>
  );
}
```

### Sistema de ícones 2D premium
```jsx
// Regra: quando usar cada tipo de ícone
const iconDecisionTree = {
  // Ação do sistema: Lucide (consistência, thin stroke)
  systemActions: "Lucide React — stroke-width 1.5, size 18-20px",

  // Estado animado (loading→done): Lottie
  stateTransition: "Lottie com segmentos A→B, não loop completo",

  // Branding/identidade: SVG customizado
  brand: "SVG próprio — não usar icon library para elementos de marca",

  // Decorativo: R3F ou SVG animado
  decorative: "Three.js ou SVG com CSS animation",
};

// Animated icon — stroke que se desenha
function DrawIcon({ icon: Icon, active, color }) {
  return (
    <svg
      viewBox="0 0 24 24"
      fill="none"
      stroke={color || "currentColor"}
      strokeWidth="1.5"
      strokeLinecap="round"
      strokeLinejoin="round"
      className={`icon ${active ? "icon--active" : ""}`}
    >
      <Icon />
    </svg>
  );
}

// CSS para stroke animation
// .icon path { stroke-dasharray: 100; stroke-dashoffset: 100; transition: stroke-dashoffset 0.4s ease; }
// .icon--active path { stroke-dashoffset: 0; }

// Icon scale system
const iconSizes = {
  xs: 14, // labels, chips
  sm: 16, // inline text
  md: 20, // default UI
  lg: 24, // buttons, nav
  xl: 32, // feature sections
  "2xl": 48, // hero, decorativo
};
```

---

## DOBRAS E PAGE FOLDS

### CSS 3D page fold
```css
/* Page fold — canto dobrado */
.page-fold {
  position: relative;
  background: var(--color-surface);
  border-radius: var(--radius-md);
}

.page-fold::after {
  content: "";
  position: absolute;
  bottom: 0;
  right: 0;
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 0 0 40px 40px;
  border-color: transparent transparent var(--color-bg) transparent;
  /* Sombra na dobra */
  filter: drop-shadow(-2px -2px 3px rgba(0,0,0,0.15));
  transition: border-width var(--motion-base) var(--ease-smooth);
}

.page-fold:hover::after {
  border-width: 0 0 60px 60px;
}

/* Dobra 3D com perspective */
.fold-container {
  perspective: 1000px;
  perspective-origin: center;
}

.fold-page {
  transform-style: preserve-3d;
  transform-origin: left center;
  transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1.000);
}

.fold-page.folded {
  transform: rotateY(-180deg);
}

.fold-page__front,
.fold-page__back {
  position: absolute;
  inset: 0;
  backface-visibility: hidden;
}

.fold-page__back {
  transform: rotateY(180deg);
}
```

### Book page turn com Three.js
```jsx
function BookPageTurn({ frontTexture, backTexture, progress }) {
  const meshRef = useRef();
  const segments = 20; // divisões horizontais para dobra suave

  useFrame(() => {
    if (!meshRef.current) return;
    const geo = meshRef.current.geometry;
    const pos = geo.attributes.position;
    const half = segments / 2;

    for (let i = 0; i <= segments; i++) {
      const t = i / segments;
      const angle = Math.PI * t * progress;

      // Simular dobra da página
      const x = i < half
        ? t * 2 - progress * t * 2     // lado esquerdo vira
        : 1 - (1 - t) * 2 + progress * (1 - t) * 2; // lado direito

      for (let j = 0; j <= 1; j++) {
        const idx = i * 2 + j;
        pos.setX(idx, Math.cos(angle) * (i / segments) - 0.5);
        pos.setZ(idx, Math.sin(angle) * (i / segments) * 0.5);
      }
    }
    pos.needsUpdate = true;
    geo.computeVertexNormals();
  });

  return (
    <mesh ref={meshRef}>
      <planeGeometry args={[2, 3, segments, 1]} />
      <meshStandardMaterial map={frontTexture} side={THREE.FrontSide} />
    </mesh>
  );
}
```

---

## TRANSIÇÕES DE SEÇÃO 3D

### Section flip 3D (CSS)
```jsx
function Section3DTransition({ children, nextSection }) {
  const [flipping, setFlipping] = useState(false);

  const flip = async () => {
    setFlipping(true);
    await new Promise(r => setTimeout(r, 800));
    nextSection();
    setFlipping(false);
  };

  return (
    <div className={`section-3d ${flipping ? "section-3d--flip" : ""}`}>
      <div className="section-3d__face section-3d__face--front">
        {children}
      </div>
      <div className="section-3d__face section-3d__face--back">
        {/* próxima seção visível atrás */}
      </div>
    </div>
  );
}
```

```css
.section-3d {
  perspective: 2000px;
  perspective-origin: center center;
}
.section-3d__face {
  position: absolute;
  inset: 0;
  backface-visibility: hidden;
  transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1.000);
}
.section-3d__face--front  { transform: rotateX(0deg); }
.section-3d__face--back   { transform: rotateX(180deg); }
.section-3d--flip .section-3d__face--front { transform: rotateX(-180deg); }
.section-3d--flip .section-3d__face--back  { transform: rotateX(0deg); }

@media (prefers-reduced-motion: reduce) {
  .section-3d__face { transition: none; }
}
```

### Depth transition — zoom through
```js
function initDepthTransition(fromEl, toEl) {
  const ctx = gsap.context(() => {
    const tl = gsap.timeline();
    tl
      .to(fromEl, {
        scale: 20,
        opacity: 0,
        duration: 0.8,
        ease: "power3.in",
      })
      .fromTo(toEl,
        { scale: 0.05, opacity: 0 },
        { scale: 1, opacity: 1, duration: 0.8, ease: "power3.out" },
        "-=0.1"
      );
  });
  return () => ctx.revert();
}
```

### Ribbon 3D (TubeGeometry)
```jsx
function Ribbon3D({ color = "#6366f1" }) {
  const tubeRef = useRef();

  const curve = useMemo(() => {
    return new THREE.CatmullRomCurve3([
      new THREE.Vector3(-5, 0, 0),
      new THREE.Vector3(-2, 2, 1),
      new THREE.Vector3(0, 0, -1),
      new THREE.Vector3(2, -2, 1),
      new THREE.Vector3(5, 0, 0),
    ]);
  }, []);

  useFrame(({ clock }) => {
    // Animar os pontos da curva para ribbon flutuante
    const t = clock.elapsedTime;
    curve.points[1].y = Math.sin(t * 0.8) * 2;
    curve.points[2].z = Math.cos(t * 0.6) * 1.5;
    curve.points[3].y = Math.cos(t * 0.9) * -2;
    tubeRef.current.geometry.copy(
      new THREE.TubeGeometry(curve, 64, 0.08, 8, false)
    );
  });

  return (
    <mesh ref={tubeRef}>
      <tubeGeometry args={[curve, 64, 0.08, 8, false]} />
      <meshStandardMaterial
        color={color}
        metalness={0.8}
        roughness={0.1}
        envMapIntensity={2}
      />
    </mesh>
  );
}
```

---

## MATERIAIS HOLOGRÁFICOS

```glsl
/* Fragment shader — iridescente */
uniform float uTime;
uniform sampler2D uMatcap;
varying vec3 vNormal;
varying vec3 vViewDir;

vec3 hsl2rgb(vec3 c) {
  vec3 rgb = clamp(abs(mod(c.x*6.0+vec3(0,4,2),6.0)-3.0)-1.0, 0.0, 1.0);
  return c.z + c.y * (rgb-0.5)*(1.0-abs(2.0*c.z-1.0));
}

void main() {
  vec3 normal = normalize(vNormal);
  vec3 viewDir = normalize(vViewDir);
  float fresnel = pow(1.0 - dot(normal, viewDir), 2.0);

  /* Ângulo determina a cor — efeito iridescente */
  float hue = fresnel * 0.8 + uTime * 0.05;
  vec3 iridescentColor = hsl2rgb(vec3(hue, 0.8, 0.6));

  /* Combinar com matcap */
  vec2 matcapUv = normal.xy * 0.5 + 0.5;
  vec3 matcapColor = texture2D(uMatcap, matcapUv).rgb;

  vec3 finalColor = mix(matcapColor, iridescentColor, fresnel * 0.7);
  gl_FragColor = vec4(finalColor, 1.0);
}
```

```jsx
// Usar no R3F
function HolographicMaterial() {
  const matcap = useTexture("/textures/matcap-gold.png");
  const uniforms = useMemo(() => ({
    uTime:    { value: 0 },
    uMatcap:  { value: matcap },
  }), []);

  useFrame(({ clock }) => {
    uniforms.uTime.value = clock.elapsedTime;
  });

  return (
    <shaderMaterial
      vertexShader={holographicVert}
      fragmentShader={holographicFrag}
      uniforms={uniforms}
    />
  );
}
```

---

## LIGHT SHAFTS & NEON GLOW

```glsl
/* God rays — fragment shader */
uniform sampler2D uScene;
uniform vec2 uLightPos; /* posição da luz em screen space */
uniform float uExposure;
uniform float uDecay;
uniform float uDensity;
uniform float uWeight;

void main() {
  vec2 uv = vUv;
  vec2 delta = (uv - uLightPos) * (1.0 / 64.0) * uDensity;
  float illuminationDecay = 1.0;
  vec3 color = vec3(0.0);

  for (int i = 0; i < 64; i++) {
    uv -= delta;
    vec3 sample = texture2D(uScene, uv).rgb;
    sample *= illuminationDecay * uWeight;
    color += sample;
    illuminationDecay *= uDecay;
  }

  gl_FragColor = vec4(color * uExposure, 1.0);
}
```

```css
/* Neon glow — CSS puro */
.neon-text {
  color: #fff;
  text-shadow:
    0 0 7px #fff,
    0 0 10px #fff,
    0 0 21px #fff,
    0 0 42px var(--neon-color),
    0 0 82px var(--neon-color),
    0 0 92px var(--neon-color),
    0 0 102px var(--neon-color),
    0 0 151px var(--neon-color);
}

.neon-text { --neon-color: #bc13fe; } /* roxo */
.neon-green { --neon-color: #39ff14; }
.neon-blue  { --neon-color: #00ffff; }
.neon-pink  { --neon-color: #ff10f0; }

@media (prefers-reduced-motion: reduce) {
  .neon-text { text-shadow: none; }
}
```
