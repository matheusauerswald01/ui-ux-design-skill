# Referência: WebGL & React Three Fiber Avançado

## R3F — Patterns de produção

### Setup base premium
```jsx
import { Canvas, useFrame, useThree, useLoader } from "@react-three/fiber";
import { Suspense } from "react";
import * as THREE from "three";

function PremiumCanvas({ children }) {
  return (
    <Canvas
      camera={{ position: [0, 0, 5], fov: 45, near: 0.1, far: 1000 }}
      dpr={[1, Math.min(devicePixelRatio, 2)]}
      gl={{
        antialias: true,
        alpha: true,
        powerPreference: "high-performance",
        outputColorSpace: THREE.SRGBColorSpace,
        toneMapping: THREE.ACESFilmicToneMapping,
        toneMappingExposure: 1.0,
      }}
      shadows="soft"
      performance={{ min: 0.5 }} // reduzir DPR se FPS cair
    >
      <Suspense fallback={<CanvasLoader />}>
        {children}
      </Suspense>
    </Canvas>
  );
}
```

### useFrame patterns
```jsx
// RAF com clock e delta time
function AnimatedObject() {
  const meshRef = useRef();
  const clockRef = useRef({ elapsed: 0 });

  useFrame(({ clock }, delta) => {
    // delta = tempo desde último frame (independente de FPS)
    meshRef.current.rotation.y += delta * 0.5;

    // Usar clock.elapsedTime para animações absolutas
    meshRef.current.position.y = Math.sin(clock.elapsedTime) * 0.5;

    // Lerp para suavidade
    meshRef.current.rotation.x = THREE.MathUtils.lerp(
      meshRef.current.rotation.x,
      targetX,
      delta * 5
    );
  });

  return <mesh ref={meshRef}><boxGeometry /><meshStandardMaterial /></mesh>;
}
```

### Shader com uniforms animados
```jsx
import { shaderMaterial } from "@react-three/drei";
import { extend } from "@react-three/fiber";

const WaveMaterial = shaderMaterial(
  { uTime: 0, uColor: new THREE.Color("#6366f1"), uMouse: new THREE.Vector2() },
  // vertex
  `
  varying vec2 vUv;
  void main() {
    vUv = uv;
    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
  }
  `,
  // fragment
  `
  uniform float uTime;
  uniform vec3 uColor;
  uniform vec2 uMouse;
  varying vec2 vUv;

  float noise(vec2 p) {
    return fract(sin(dot(p, vec2(127.1, 311.7))) * 43758.5453123);
  }

  void main() {
    vec2 uv = vUv;
    float wave = sin(uv.x * 10.0 + uTime) * 0.02;
    uv.y += wave;

    float dist = distance(uv, uMouse);
    float ripple = sin(dist * 20.0 - uTime * 3.0) * 0.02 / (dist + 0.1);
    uv += ripple;

    vec3 color = mix(uColor, vec3(1.0), uv.y * 0.5);
    gl_FragColor = vec4(color, 1.0);
  }
  `
);

extend({ WaveMaterial });

function WaveMesh() {
  const ref = useRef();
  useFrame(({ clock, mouse }) => {
    ref.current.uTime = clock.elapsedTime;
    ref.current.uMouse.set(mouse.x * 0.5 + 0.5, mouse.y * 0.5 + 0.5);
  });
  return (
    <mesh>
      <planeGeometry args={[5, 5, 64, 64]} />
      <waveMaterial ref={ref} />
    </mesh>
  );
}
```

### Física com Rapier
```jsx
import { Physics, RigidBody, CuboidCollider, BallCollider } from "@react-three/rapier";

function PhysicsScene() {
  return (
    <Physics gravity={[0, -9.81, 0]} debug={process.env.NODE_ENV === "development"}>
      {/* Chão */}
      <RigidBody type="fixed">
        <CuboidCollider args={[10, 0.1, 10]} position={[0, -2, 0]} />
      </RigidBody>

      {/* Esfera com física */}
      <RigidBody position={[0, 5, 0]} colliders="ball" restitution={0.7} friction={0.5}>
        <mesh castShadow>
          <sphereGeometry args={[0.5, 32, 32]} />
          <meshStandardMaterial color="#6366f1" metalness={0.5} roughness={0.2} />
        </mesh>
      </RigidBody>

      {/* Caixa cinemática (controlada por código) */}
      <RigidBody type="kinematicPosition" position={[-2, 0, 0]}>
        <mesh castShadow>
          <boxGeometry />
          <meshStandardMaterial color="#22c55e" />
        </mesh>
      </RigidBody>
    </Physics>
  );
}
```

### Post-processing cinematográfico
```jsx
import { EffectComposer, Bloom, ChromaticAberration, DepthOfField,
         Noise, Vignette, SSAO, ToneMapping } from "@react-three/postprocessing";
import { BlendFunction } from "postprocessing";

function PostProcessing({ intense = false }) {
  return (
    <EffectComposer multisampling={8}>
      <Bloom
        luminanceThreshold={0.8}
        luminanceSmoothing={0.9}
        intensity={intense ? 2 : 0.5}
        blendFunction={BlendFunction.ADD}
      />
      <ChromaticAberration
        offset={[0.0005, 0.0005]}
        radialModulation={true}
        modulationOffset={0.5}
      />
      <DepthOfField focusDistance={0.01} focalLength={0.02} bokehScale={3} />
      <Noise opacity={0.025} blendFunction={BlendFunction.OVERLAY} />
      <Vignette eskil={false} offset={0.15} darkness={0.6} />
      <ToneMapping />
    </EffectComposer>
  );
}
```

### Geometria morph (blob animado)
```jsx
function MorphBlob() {
  const meshRef = useRef();
  const positionsRef = useRef();

  useEffect(() => {
    const geo = meshRef.current.geometry;
    positionsRef.current = geo.attributes.position.array.slice();
  }, []);

  useFrame(({ clock }) => {
    const t = clock.elapsedTime;
    const geo = meshRef.current.geometry;
    const pos = geo.attributes.position;
    const orig = positionsRef.current;

    for (let i = 0; i < pos.count; i++) {
      const x = orig[i * 3];
      const y = orig[i * 3 + 1];
      const z = orig[i * 3 + 2];

      // Noise baseado em posição e tempo
      const noise = Math.sin(x * 3 + t) * Math.cos(y * 2 + t * 0.7) * 0.15;
      const r = 1 + noise;

      // Normalizar e escalar
      const len = Math.sqrt(x*x + y*y + z*z);
      pos.setXYZ(i, (x/len) * r, (y/len) * r, (z/len) * r);
    }
    pos.needsUpdate = true;
    geo.computeVertexNormals();
  });

  return (
    <mesh ref={meshRef}>
      <sphereGeometry args={[1, 128, 128]} />
      <MeshTransmissionMaterial
        transmission={0.9} roughness={0.1} thickness={0.5}
        chromaticAberration={0.03} ior={1.5}
      />
    </mesh>
  );
}
```

### Spline integration
```jsx
import Spline from "@splinetool/react-spline";

function SplineScene() {
  const splineRef = useRef();

  const onLoad = (spline) => {
    splineRef.current = spline;
  };

  const onMouseDown = (e) => {
    // Interagir com objetos da cena Spline
    if (e.target.name === "Button") {
      splineRef.current?.emitEvent("mouseDown", "Button");
    }
  };

  return (
    <Spline
      scene="https://prod.spline.design/SCENE_ID/scene.splinecode"
      onLoad={onLoad}
      onMouseDown={onMouseDown}
      style={{ width: "100%", height: "100%", background: "transparent" }}
    />
  );
}
```

### Performance obrigatória
```jsx
// Dispose correto — sem memory leaks
function useDispose(ref) {
  useEffect(() => {
    return () => {
      if (!ref.current) return;
      ref.current.traverse(obj => {
        obj.geometry?.dispose();
        if (obj.material) {
          const mats = Array.isArray(obj.material) ? obj.material : [obj.material];
          mats.forEach(m => {
            Object.values(m).forEach(v => {
              if (v instanceof THREE.Texture) v.dispose();
            });
            m.dispose();
          });
        }
      });
    };
  }, []);
}

// LOD (Level of Detail)
import { Detailed } from "@react-three/drei";
function LODObject() {
  return (
    <Detailed distances={[0, 15, 30]}>
      <HighDetailMesh />
      <MedDetailMesh />
      <LowDetailMesh />
    </Detailed>
  );
}

// Instancing para muitos objetos iguais
import { Instances, Instance } from "@react-three/drei";
function ManyObjects({ positions }) {
  return (
    <Instances limit={10000}>
      <sphereGeometry args={[0.1]} />
      <meshStandardMaterial />
      {positions.map((pos, i) => (
        <Instance key={i} position={pos} />
      ))}
    </Instances>
  );
}
```

## Fluid simulation WebGL
```js
// Navier-Stokes simplificado (ping-pong FBO)
class FluidSim {
  constructor(gl, width = 512, height = 512) {
    this.gl = gl;
    this.width = width;
    this.height = height;
    this.textures = [this.createFBO(), this.createFBO()];
    this.current = 0;
  }

  createFBO() {
    const gl = this.gl;
    const tex = gl.createTexture();
    gl.bindTexture(gl.TEXTURE_2D, tex);
    gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA32F, this.width, this.height, 0, gl.RGBA, gl.FLOAT, null);
    gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
    gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR);
    gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
    gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
    const fbo = gl.createFramebuffer();
    gl.bindFramebuffer(gl.FRAMEBUFFER, fbo);
    gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, tex, 0);
    return { texture: tex, fbo };
  }

  splat(x, y, dx, dy, color) {
    // Adicionar força e cor ao ponto (x, y)
    // Renderizar com shader de splat
  }

  step(dt) {
    // 1. Advect velocity
    // 2. Apply forces (splats)
    // 3. Pressure solve (Jacobi iteration)
    // 4. Subtract gradient
    // Trocar buffers
    this.current = 1 - this.current;
  }
}
```

## Áudio espacial 3D
```jsx
import { PositionalAudio } from "@react-three/drei";

function SpatialSoundObject({ url, distance = 5 }) {
  const soundRef = useRef();

  return (
    <mesh onClick={() => soundRef.current?.play()}>
      <sphereGeometry args={[0.3]} />
      <meshStandardMaterial color="gold" />
      <PositionalAudio
        ref={soundRef}
        url={url}
        distance={distance}
        loop
      />
    </mesh>
  );
}
```
