# Referência: 3D & Gráficos — Tecnologias de Fronteira

---

## GAUSSIAN SPLATTING — WEB

Técnica 2023+ para renderizar cenas 3D foto-realistas capturadas de vídeo real.

```html
<!-- gsplat.js — viewer web de 3D Gaussian Splatting -->
<script type="module">
import { Viewer } from "https://cdn.jsdelivr.net/npm/gsplat/dist/index.module.js";

const viewer = new Viewer({
  canvas: document.getElementById("canvas"),
  camera: { position: [0, 1, 5], near: 0.1, far: 100 },
});

// Carregar cena .splat
await viewer.loadScene("https://example.com/scene.splat", {
  onProgress: (loaded, total) => {
    document.querySelector(".progress").style.width = `${(loaded/total)*100}%`;
  }
});

// Controles orbitais automáticos
viewer.startOrbitControls();

// Exportado com: nerfstudio ou gaussian-splatting original
// Pipeline: vídeos 360° → colmap → train 3DGS → export .splat
</script>
```

### Casos de uso reais
```
Imobiliário: tour virtual de apartamentos capturados com iPhone
E-commerce: produto físico visualizável em 3D sem modelagem
Museus: preservação digital de artefatos
Turismo: pontos turísticos exploráveis
Arquitetura: maquetes físicas digitalizadas
```

### Pipeline de captura
```bash
# 1. Capturar: 200-300 frames do objeto (iPhone LiDAR recomendado)
# 2. COLMAP para reconstrução de câmeras
colmap automatic_reconstructor --workspace_path ./data --image_path ./images

# 3. Treinar 3DGS
python train.py -s ./data/sparse

# 4. Exportar para web
# Output: point_cloud.ply → converter para .splat com gsplat-converter
python convert_splat.py --input point_cloud.ply --output scene.splat
```

---

## RAYMARCHING — EFEITOS IMPOSSÍVEIS

```glsl
/* Raymarching básico — renderiza SDFs no fragment shader */
/* Sem geometria Three.js necessária — puro matemática no shader */

uniform vec2 uResolution;
uniform float uTime;
uniform vec3 uCameraPos;
varying vec2 vUv;

/* Signed Distance Functions (SDFs) — primitivas */
float sdSphere(vec3 p, float r) {
  return length(p) - r;
}

float sdBox(vec3 p, vec3 b) {
  vec3 q = abs(p) - b;
  return length(max(q, 0.0)) + min(max(q.x, max(q.y, q.z)), 0.0);
}

float sdTorus(vec3 p, vec2 t) {
  vec2 q = vec2(length(p.xz) - t.x, p.y);
  return length(q) - t.y;
}

float sdMandelbulb(vec3 pos) {
  vec3 z = pos;
  float dr = 1.0;
  float r = 0.0;
  int power = 8;

  for (int i = 0; i < 15; i++) {
    r = length(z);
    if (r > 2.0) break;

    float theta = acos(z.z / r);
    float phi = atan(z.y, z.x);
    dr = pow(r, float(power - 1)) * float(power) * dr + 1.0;

    float zr = pow(r, float(power));
    theta *= float(power);
    phi *= float(power);

    z = zr * vec3(sin(theta) * cos(phi), sin(phi) * sin(theta), cos(theta));
    z += pos;
  }

  return 0.5 * log(r) * r / dr;
}

/* Operações booleanas em SDFs */
float opUnion(float d1, float d2)        { return min(d1, d2); }
float opSubtraction(float d1, float d2)  { return max(-d1, d2); }
float opIntersection(float d1, float d2) { return max(d1, d2); }

/* Smooth union (blend entre formas) */
float opSmoothUnion(float d1, float d2, float k) {
  float h = max(k - abs(d1 - d2), 0.0) / k;
  return min(d1, d2) - h * h * k * (1.0 / 4.0);
}

/* Scene — combinar SDFs */
float scene(vec3 p) {
  float sphere = sdSphere(p - vec3(sin(uTime) * 0.5, 0.0, 0.0), 0.8);
  float box    = sdBox(p - vec3(0.0, 0.5, 0.0), vec3(0.4, 0.4, 0.4));
  float torus  = sdTorus(p, vec2(1.2, 0.3));

  return opSmoothUnion(sphere, opUnion(box, torus), 0.3);
}

/* Normal calculation (derivada numérica) */
vec3 calcNormal(vec3 p) {
  float eps = 0.001;
  return normalize(vec3(
    scene(p + vec3(eps, 0, 0)) - scene(p - vec3(eps, 0, 0)),
    scene(p + vec3(0, eps, 0)) - scene(p - vec3(0, eps, 0)),
    scene(p + vec3(0, 0, eps)) - scene(p - vec3(0, 0, eps))
  ));
}

/* Ambient Occlusion */
float calcAO(vec3 pos, vec3 nor) {
  float occ = 0.0;
  float sca = 1.0;
  for (int i = 0; i < 5; i++) {
    float h = 0.01 + 0.12 * float(i) / 4.0;
    float d = scene(pos + h * nor);
    occ += (h - d) * sca;
    sca *= 0.95;
  }
  return clamp(1.0 - 1.5 * occ, 0.0, 1.0);
}

/* Main raymarcher */
void main() {
  vec2 uv = (vUv - 0.5) * 2.0;
  uv.x *= uResolution.x / uResolution.y;

  vec3 ro = uCameraPos; /* ray origin */
  vec3 rd = normalize(vec3(uv, -1.5)); /* ray direction */

  /* March */
  float t = 0.0;
  float hit = -1.0;
  for (int i = 0; i < 128; i++) {
    vec3 pos = ro + t * rd;
    float d = scene(pos);
    if (d < 0.001) { hit = t; break; }
    if (t > 100.0) break;
    t += d;
  }

  vec3 color = vec3(0.0);
  if (hit > 0.0) {
    vec3 pos = ro + hit * rd;
    vec3 nor = calcNormal(pos);
    float ao = calcAO(pos, nor);

    /* Iluminação Phong */
    vec3 lightDir = normalize(vec3(1.0, 2.0, 1.0));
    float diff = max(dot(nor, lightDir), 0.0);
    float spec = pow(max(dot(reflect(-lightDir, nor), -rd), 0.0), 32.0);

    color = vec3(0.3 + 0.7 * diff) * ao + spec * 0.5;
  }

  gl_FragColor = vec4(color, 1.0);
}
```

### Three.js + Raymarching
```jsx
function RaymarchedScene() {
  const meshRef = useRef();

  useFrame(({ clock, camera }) => {
    if (meshRef.current?.material?.uniforms) {
      meshRef.current.material.uniforms.uTime.value = clock.elapsedTime;
      meshRef.current.material.uniforms.uCameraPos.value.copy(camera.position);
    }
  });

  return (
    <mesh ref={meshRef}>
      <planeGeometry args={[2, 2]} />
      <shaderMaterial
        vertexShader={`varying vec2 vUv; void main() { vUv = uv; gl_Position = vec4(position, 1.0); }`}
        fragmentShader={raymarcherFrag}
        uniforms={{
          uTime:      { value: 0 },
          uResolution:{ value: new THREE.Vector2(window.innerWidth, window.innerHeight) },
          uCameraPos: { value: new THREE.Vector3(0, 0, 3) },
        }}
      />
    </mesh>
  );
}
```

---

## RENDERIZAÇÃO VOLUMÉTRICA

```glsl
/* Volume rendering — nuvens/fumaça */
uniform sampler3D uVolume;    /* 3D texture com densidade */
uniform float uTime;
varying vec2 vUv;

float fbm(vec3 p) {
  float value = 0.0;
  float amplitude = 0.5;
  float frequency = 1.0;

  for (int i = 0; i < 6; i++) {
    value += amplitude * snoise(p * frequency + uTime * 0.1);
    amplitude *= 0.5;
    frequency *= 2.0;
  }
  return value;
}

float cloudDensity(vec3 p) {
  float base = max(0.0, fbm(p * 0.5) - 0.3);
  float detail = fbm(p * 2.0) * 0.3;
  return smoothstep(0.0, 1.0, base + detail);
}

void main() {
  vec2 uv = vUv * 2.0 - 1.0;
  vec3 ro = vec3(0.0, 0.0, 2.0); /* ray origin */
  vec3 rd = normalize(vec3(uv, -1.0)); /* ray direction */

  /* March through volume */
  vec4 color = vec4(0.0);
  float t = 0.0;

  for (int i = 0; i < 64; i++) {
    vec3 p = ro + t * rd;

    float density = cloudDensity(p);
    if (density > 0.01) {
      /* Cor baseada em altura (mais claro no topo) */
      vec3 cloudColor = mix(vec3(0.8, 0.8, 0.9), vec3(1.0, 1.0, 1.0), p.y * 0.5 + 0.5);

      /* Alpha compositing */
      float alpha = density * 0.1;
      color.rgb += (1.0 - color.a) * alpha * cloudColor;
      color.a   += (1.0 - color.a) * alpha;

      if (color.a > 0.99) break;
    }

    t += 0.05;
    if (t > 4.0) break;
  }

  gl_FragColor = color;
}
```

---

## GPGPU — GPU PARA COMPUTAÇÃO

```js
// Three.js GPUComputationRenderer — simulação de 500k partículas
import { GPUComputationRenderer } from "three/examples/jsm/misc/GPUComputationRenderer";

function initGPGPU(renderer, particleCount = 512) {
  // particleCount x particleCount = total de partículas
  // 512 x 512 = 262144 partículas!

  const gpuCompute = new GPUComputationRenderer(particleCount, particleCount, renderer);

  // Textura de posição
  const dtPosition = gpuCompute.createTexture();
  const dtVelocity = gpuCompute.createTexture();

  // Inicializar posições aleatórias
  const posData = dtPosition.image.data;
  for (let i = 0; i < posData.length; i += 4) {
    posData[i]     = (Math.random() - 0.5) * 10; // x
    posData[i + 1] = (Math.random() - 0.5) * 10; // y
    posData[i + 2] = (Math.random() - 0.5) * 10; // z
    posData[i + 3] = 1.0;                          // w (vida)
  }

  // Shader de posição (atualizado por frame na GPU)
  const posShader = gpuCompute.addVariable("texturePosition",
    `
    uniform float uTime;
    uniform float uDelta;
    uniform vec3 uMouse;

    void main() {
      vec2 uv = gl_FragCoord.xy / resolution.xy;
      vec4 pos = texture2D(texturePosition, uv);
      vec4 vel = texture2D(textureVelocity, uv);

      /* Atualizar posição com velocidade */
      pos.xyz += vel.xyz * uDelta;

      /* Repulsão do mouse */
      vec3 toMouse = pos.xyz - uMouse;
      float dist = length(toMouse);
      if (dist < 2.0) {
        vel.xyz += normalize(toMouse) * (2.0 - dist) * 0.01;
      }

      /* Voltar ao centro */
      vel.xyz += -pos.xyz * 0.001;
      vel.xyz *= 0.99; /* damping */

      /* Wrap around */
      if (length(pos.xyz) > 8.0) {
        pos.xyz = -pos.xyz * 0.1;
        vel.xyz *= 0.1;
      }

      gl_FragColor = pos;
    }
    `,
    dtPosition
  );

  // Adicionar dependência entre shaders
  gpuCompute.setVariableDependencies(posShader, [posShader, velShader]);

  // Uniforms
  posShader.material.uniforms.uTime  = { value: 0 };
  posShader.material.uniforms.uDelta = { value: 0 };
  posShader.material.uniforms.uMouse = { value: new THREE.Vector3() };

  gpuCompute.init();

  // Render loop
  function compute(delta, time, mousePos) {
    posShader.material.uniforms.uTime.value  = time;
    posShader.material.uniforms.uDelta.value = delta;
    posShader.material.uniforms.uMouse.value.copy(mousePos);

    gpuCompute.compute();

    // Retornar texture para usar nos shaders de render
    return gpuCompute.getCurrentRenderTarget(posShader).texture;
  }

  return { compute, posShader };
}
```

---

## WEBGPU — NOVA GERAÇÃO

```js
// WebGPU com Three.js WebGPURenderer
import * as THREE from "three";
import WebGPURenderer from "three/addons/renderers/common/WebGPURenderer.js";
import WebGPU from "three/addons/capabilities/WebGPU.js";

async function initWebGPU(canvas) {
  if (!await WebGPU.isAvailable()) {
    // Fallback para WebGL
    return new THREE.WebGLRenderer({ canvas });
  }

  const renderer = new WebGPURenderer({ canvas });
  await renderer.init();
  return renderer;
}

// Compute shader nativo WebGPU (WGSL)
const computeCode = `
@group(0) @binding(0) var<storage, read_write> positions: array<vec4f>;
@group(0) @binding(1) var<storage, read> velocities: array<vec4f>;
@group(0) @binding(2) var<uniform> params: Params;

struct Params {
  deltaTime: f32,
  numParticles: u32,
}

@compute @workgroup_size(64)
fn main(@builtin(global_invocation_id) id: vec3u) {
  let index = id.x;
  if (index >= params.numParticles) { return; }

  var pos = positions[index];
  let vel = velocities[index];

  // Atualizar posição
  pos += vel * params.deltaTime;

  // Boundary
  if (abs(pos.x) > 5.0) { pos.x = -pos.x * 0.5; }
  if (abs(pos.y) > 5.0) { pos.y = -pos.y * 0.5; }
  if (abs(pos.z) > 5.0) { pos.z = -pos.z * 0.5; }

  positions[index] = pos;
}
`;
```

---

## WEBNN — AI NO BROWSER

```js
// WebNN API — inferência de ML nativa
async function initWebNN() {
  if (!("ml" in navigator)) {
    console.warn("WebNN não suportado — usar TensorFlow.js como fallback");
    return null;
  }

  const context = await navigator.ml.createContext({ deviceType: "gpu" });
  const builder = new MLGraphBuilder(context);

  return { context, builder };
}

// Caso de uso: segmentação de fundo em tempo real para webcam
async function backgroundSegmentation(videoEl) {
  // Usar MediaPipe Tasks (usa WebNN internamente)
  const { ImageSegmenter, FilesetResolver } = await import(
    "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision"
  );

  const vision = await FilesetResolver.forVisionTasks(
    "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision/wasm"
  );

  const segmenter = await ImageSegmenter.createFromOptions(vision, {
    baseOptions: {
      modelAssetPath: "https://storage.googleapis.com/mediapipe-models/image_segmenter/selfie_segmenter/float16/1/selfie_segmenter.tflite",
      delegate: "GPU", // WebNN para GPU
    },
    outputCategoryMask: true,
    runningMode: "VIDEO",
  });

  // Frame loop
  function segmentFrame() {
    const startTime = performance.now();
    segmenter.segmentForVideo(videoEl, startTime, (result) => {
      const mask = result.categoryMask;
      // Aplicar máscara — trocar fundo por imagem/video/WebGL scene
      applyMask(videoEl, mask);
    });
    requestAnimationFrame(segmentFrame);
  }

  videoEl.addEventListener("play", segmentFrame);
}

// Outro caso: pose estimation para interação gestual
async function poseInteraction(videoEl, onGesture) {
  const { PoseLandmarker, FilesetResolver } = await import(
    "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision"
  );

  const vision = await FilesetResolver.forVisionTasks(
    "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision/wasm"
  );

  const poseLandmarker = await PoseLandmarker.createFromOptions(vision, {
    baseOptions: {
      modelAssetPath: "https://storage.googleapis.com/mediapipe-models/pose_landmarker/pose_landmarker_lite/float16/1/pose_landmarker_lite.task",
      delegate: "GPU",
    },
    runningMode: "VIDEO",
    numPoses: 1,
  });

  function detectPose() {
    poseLandmarker.detectForVideo(videoEl, performance.now(), (result) => {
      if (result.landmarks.length > 0) {
        const landmarks = result.landmarks[0];
        // landmarks[0] = nariz, [11] = ombro esq, [12] = ombro dir
        // landmarks[15] = pulso esq, [16] = pulso dir
        const gesture = detectGesture(landmarks);
        if (gesture) onGesture(gesture);
      }
    });
    requestAnimationFrame(detectPose);
  }

  detectPose();
}
```

---

## GLTF AVANÇADO — KHR EXTENSIONS

```js
import { useGLTF } from "@react-three/drei";

function AdvancedGLTF({ path }) {
  const { scene, animations } = useGLTF(path);
  const { actions } = useAnimations(animations, scene);

  // KHR_materials_transmission (vidro)
  // KHR_materials_volume (refracting)
  // KHR_materials_iridescence (holográfico)
  // KHR_materials_sheen (tecido)
  // KHR_materials_clearcoat (verniz)
  // Todos suportados pelo Three.js WebGLRenderer

  // Ativar animação de morph targets
  useFrame(() => {
    scene.traverse(obj => {
      if (obj.morphTargetInfluences) {
        // Animar morph target 0 (ex: expressão facial)
        obj.morphTargetInfluences[0] = Math.sin(Date.now() * 0.001) * 0.5 + 0.5;
      }
    });
  });

  return <primitive object={scene} />;
}

// gltf-transform CLI — otimização de assets
// npm install -g @gltf-transform/cli
// npx gltf-transform optimize input.glb output.glb
// npx gltf-transform inspect input.glb          # ver tamanhos
// npx gltf-transform compress input.glb out.glb --texture-compress ktx2
// npx gltf-transform meshopt input.glb out.glb  # comprimir meshes
// npx gltf-transform draco input.glb out.glb    # Draco compression
// npx gltf-transform resize input.glb out.glb --texture-size 1024 # resize textures
```

---

## SPATIAL AUDIO AVANÇADO

```js
// HRTF — Head-Related Transfer Function para áudio binaural
class SpatialAudio3D {
  constructor(scene) {
    this.listener = new THREE.AudioListener();
    this.scene = scene;

    // Configurar HRTF para binaural verdadeiro
    this.listener.context.hrtf = true; // requer Web Audio API HRTF support
  }

  addPositionalSound(object, audioUrl, options = {}) {
    const sound = new THREE.PositionalAudio(this.listener);
    const loader = new THREE.AudioLoader();

    loader.load(audioUrl, (buffer) => {
      sound.setBuffer(buffer);
      sound.setLoop(options.loop ?? true);
      sound.setVolume(options.volume ?? 0.5);

      // Modelo de distância
      sound.setDistanceModel("exponential"); // mais natural
      sound.setRefDistance(options.refDistance ?? 2);    // volume máximo aqui
      sound.setRolloffFactor(options.rolloff ?? 1.5);    // velocidade de fade
      sound.setMaxDistance(options.maxDistance ?? 100);

      // Cone de áudio direcional
      if (options.cone) {
        sound.setDirectionalCone(
          options.cone.inner ?? 360,   // ângulo inner (volume máx)
          options.cone.outer ?? 360,   // ângulo outer (volume mín)
          options.cone.outerGain ?? 0  // ganho fora do cone
        );
      }

      sound.play();
    });

    object.add(sound);
    return sound;
  }

  // Reverb por material do ambiente
  setEnvironmentReverb(material = "default") {
    const ctx = this.listener.context;
    const convolver = ctx.createConvolver();

    const reverbUrls = {
      concrete: "/audio/ir/concrete-room.wav",
      wood:     "/audio/ir/wooden-room.wav",
      outdoor:  "/audio/ir/outdoor.wav",
      cave:     "/audio/ir/cave.wav",
      default:  "/audio/ir/medium-room.wav",
    };

    fetch(reverbUrls[material] || reverbUrls.default)
      .then(r => r.arrayBuffer())
      .then(buf => ctx.decodeAudioData(buf))
      .then(decoded => {
        convolver.buffer = decoded;
        // Conectar ao grafo de áudio
        this.listener.gain.connect(convolver);
        convolver.connect(ctx.destination);
      });
  }

  // Sync listener com câmera
  updateListenerPosition(camera) {
    this.listener.position.copy(camera.position);
    this.listener.quaternion.copy(camera.quaternion);
  }
}
```
