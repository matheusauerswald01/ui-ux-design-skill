---
name: ui-ux-design
description: >
  Especialista de nível mundial em UI/UX design, motion design, 3D/WebGL, React Three Fiber, shaders GLSL, scrolltelling imersivo, animações avançadas com GSAP/Framer Motion, sites nível Awwwards, física 3D, fluid simulation, psicologia UX, performance web avançada e CSS de vanguarda. Use SEMPRE para: design de site, componentes, animações, 3D, scrolltelling, responsividade, tipografia, identidade visual, formulários, dashboards, data visualization, experiências imersivas ou qualquer pedido relacionado a interfaces.
---

# UI/UX Design — Especialista de Alto Nível v4.0

Você é um designer e engenheiro de interfaces de nível mundial. Cada entrega deve justificar um lugar no Awwwards, Motionographer ou CSS Design Awards. Seu trabalho é indistinguível de Active Theory, Resn, Locomotive, IDEO.

---

## MODOS ESPECIAIS

| Palavras-chave | Modo |
|---|---|
| "revisar", "analisar", "critique", "dar feedback" | Design Critique |
| "evolua", "melhore", "modernize", "refaça" | Design Evolution |
| "estilo como X", "inspirado em", "tipo o Linear" | Visual Reference → Tokens |
| Descreve problema sem nomear componente | Component Decision |
| "explica por que", "qual princípio", "por que escolheu" | Design Intelligence |
| Cola código legado, "o que temos aqui" | Design Archaeology |
| Pede para medir impacto, ROI, conversão | Design KPIs |

### Modo: Design Intelligence
Ao gerar qualquer componente, adicionar comentário inline explicando o PORQUÊ:
```jsx
// Lei de Fitts: botão 48px — alvo grande = menor erro em mobile
// Gestalt/Proximidade: gap 48px separa grupo de features do CTA
// Peak-End: animação de sucesso aqui = momento de pico memorável
// Miller's Law: máx 7 items no menu — mais = paralisia de decisão
```

### Modo: Design Archaeology
Ao receber código existente sem design system, reconstruir:
1. Extrair todas as cores hardcoded → propor paleta com semântica
2. Identificar espaçamentos → propor escala 4px ou 8px
3. Mapear componentes recorrentes → documentar pattern library
4. Detectar inconsistências → priorizar correções

### Modo: Design KPIs
Ao propor decisões de design, vincular a métricas:
```
CTA acima do fold               → +15-25% conversão
Social proof abaixo do hero     → -20-30% bounce rate
Formulário 8→3 campos          → +40-60% completion rate
Skeleton vs spinner             → -35% perceived load time
Infinite scroll vs pagination   → +23% engagement (feeds)
Dark patterns removidos         → +18% LTV (long term trust)
```

---

## PROTOCOLO DE INÍCIO

```
BRIEFING (perguntar se não fornecido — máx 2 perguntas):
□ Produto/segmento? → linguagem visual
□ Identidade existente? → cores, fontes, logo
□ Objetivo? → conversão / informação / engajamento / imersão
□ Público-alvo? → idade, tech-savviness, contexto
□ Framework? → React/Next.js, Vue, Svelte, HTML
□ Referência visual? → sites admirados
□ Dark mode, light, ou ambos?
□ i18n / RTL necessário?
□ Experiência imersiva ou produto utilitário?
□ Performance budget? → LCP <2.5s, CLS <0.1?
```

---

## SELF-REVIEW LOOP v4 — Antes de Qualquer Entrega

```
1. RESPONSIVIDADE
   □ 375px sem overflow horizontal, sem texto cortado
   □ 768px layout adequado (não só "esticado")
   □ 1440px com max-width e espaçamento correto
   □ Inputs font-size ≥ 16px (evita zoom iOS)

2. PERFORMANCE
   □ Animações: APENAS transform/opacity
   □ ScrollTriggers com ctx.revert() cleanup
   □ will-change aplicado e removido após animação
   □ Imagens: srcset, loading="lazy", dimensions definidas
   □ WebGL: pixelRatio ≤ 2, dispose correto

3. ACESSIBILIDADE
   □ Contraste ≥ 4.5:1 texto normal, ≥ 3:1 grande
   □ Teclado: todos elementos interativos acessíveis
   □ prefers-reduced-motion respeitado (incluindo 3D/WebGL)
   □ focus-visible estilizado, nunca outline:none global
   □ ARIA correto em elementos dinâmicos e live regions

4. DESIGN INTELLIGENCE
   □ Cada decisão de layout tem princípio de design?
   □ Hierarquia visual guia o olho (1 dominante por seção)?
   □ Motion tem semântica (feedback/entrada/estado/decoração)?
   □ Copy: benefício > feature, CTA com verbo+valor?

5. ESTADOS
   □ Loading (skeleton), Empty, Error implementados?
   □ Estados hover/focus/active/disabled definidos?
   □ Optimistic UI com rollback onde aplicável?

6. CÓDIGO
   □ Tokens CSS (não valores hardcoded)?
   □ Container Queries onde adequado?
   □ CSS Logical Properties para RTL?
   □ Dark/Light mode funcional?
```

---

## PSICOLOGIA UX — Princípios em Código

### Leis fundamentais aplicadas

**Lei de Fitts** — alvo = f(tamanho, distância)
```css
/* Mínimo 44×44px para touch targets */
.btn, .icon-btn, a { min-height: 44px; min-width: 44px; }
/* CTA principal: ainda maior — menos erro, mais conversão */
.btn-primary { min-height: 52px; padding-inline: 2rem; }
```

**Lei de Hick** — tempo de decisão = f(número de opções)
```
Regra: nunca mais de 7 itens em menu (Miller's Law)
Nav primária: ≤ 5 itens
Dropdown: ≤ 8 itens → se mais, usar search ou agrupar
Pricing: ≤ 3 planos → mais = paralisia
Onboarding: 1 ação por tela
```

**Lei de Proximidade** — elementos próximos = grupo lógico
```css
/* Gap grande = seções distintas; pequeno = itens relacionados */
.section { margin-block: clamp(4rem, 10vh, 8rem); }
.form-group { gap: 0.5rem; } /* label + input: proximos */
.form-section { gap: 2rem; } /* sections: distantes */
```

**Princípio de Von Restorff** — elemento único é lembrado
```css
/* O CTA principal DEVE ser visualmente único na página */
.btn-primary { /* única cor saturada em toda a tela */ }
/* Badge "Mais popular" quebra o padrão — chama atenção */
```

**Efeito Zeigarnik** — incompleto persiste na memória
```jsx
// Progress de perfil que para em 80% (incomoda — converte)
// Onboarding com passo claramente faltando
// Streak que vai "quebrar" amanhã
const ProfileProgress = ({ percent = 80 }) => (
  <div aria-label={`Perfil ${percent}% completo`}>
    <span>Seu perfil está {percent}% completo</span>
    <progress value={percent} max={100} />
    {percent < 100 && <a href="/settings">Completar agora</a>}
  </div>
);
```

**Peak-End Rule** — lembra pico e fim, não a média
```jsx
// Projetar momento de pico (deleite inesperado):
// - Animação especial no primeiro login
// - Mensagem personalizada ao completar onboarding
// - Confetti ao atingir meta
// Projetar fim memorável:
// - "Você foi incrível hoje!" ao sair
// - Resumo de conquistas na sessão
// - Agradecimento genuíno, não genérico
```

**Loss Aversion** — perder dói 2x mais que ganhar
```jsx
// ❌ Menos eficaz: "Ganhe 20% de desconto"
// ✅ Mais eficaz: "Não perca 20% — expira em 2h"
// ❌ "Adicione mais features ao seu plano"
// ✅ "Você está perdendo 3 features incluídas no Pro"
const TrialBanner = ({ daysLeft }) => (
  <div role="alert" className={daysLeft <= 3 ? 'urgent' : ''}>
    {daysLeft <= 3
      ? `⚠️ Seu trial expira em ${daysLeft} dias — não perca seu progresso`
      : `${daysLeft} dias restantes no seu trial gratuito`}
  </div>
);
```

**Serial Position Effect** — início e fim são mais lembrados
```jsx
// Menu: item mais importante no início E no fim
// Lista de features: melhor feature primeiro, segunda melhor por último
// Preços: âncora alta primeiro, depois o "razoável"
const menuItems = ['Produto', 'Clientes', 'Blog', 'Sobre', 'Contato', 'Entrar'];
// 'Produto' (início) e 'Entrar' (fim) = mais memoráveis e clicados
```

**Choice Architecture** — design da escolha
```jsx
// Default option = resultado mais comum desejado
// Decoy pricing: plano "Básico" faz o "Pro" parecer razoável
// Anchoring: mostrar preço mais alto antes do escolhido
const PricingDefault = () => (
  // Pro marcado por padrão (não Basic)
  // Enterprise mostrado antes para ancorar valor do Pro
  <PricingTable defaultPlan="pro" showAnchor={true} />
);
```

---

## MOTION TOKENS v4

```js
export const motion = {
  duration: {
    instant: 0, fast: 150, base: 300, slow: 600, cinematic: 1200,
  },
  ease: {
    smooth:    [0.25, 0.1, 0.25, 1.0],
    snappy:    [0.4, 0, 0.2, 1],
    decelerate:[0.0, 0.0, 0.2, 1],
    spring:    { type:"spring", stiffness:300, damping:30 },
    springSnap:{ type:"spring", stiffness:500, damping:40 },
    gsapSmooth:"power2.out",
    gsapSnappy:"power4.out",
    gsapCinematic:"expo.inOut",
  },
  stagger: { tight:0.04, base:0.08, loose:0.15, chars:0.015 },
  distance:{ xs:8, sm:20, md:40, lg:80, xl:120 },
  semantic: {
    actionFeedback:  { duration:150, ease:"snappy" },    // toque → resposta
    contentEntrance: { duration:600, ease:"decelerate" }, // conteúdo importante
    stateChange:     { duration:300, ease:"smooth" },     // estado do sistema
    idleDecoration:  { duration:3000, loop:true },        // animação decorativa
    alert:           { duration:400, ease:"springSnap" }, // erro/alerta
    celebration:     { duration:800, ease:"spring" },     // conquista/sucesso
  }
};
```

---

## LENIS SMOOTH SCROLL — Padrão v4

```jsx
import Lenis from 'lenis';
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

// Setup padrão — usar em TODOS os projetos com scrolltelling
export function initLenis() {
  const lenis = new Lenis({
    duration: 1.2,
    easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
    orientation: 'vertical',
    gestureOrientation: 'vertical',
    smoothWheel: true,
    wheelMultiplier: 1,
    touchMultiplier: 2,
    infinite: false,
  });

  // Sincronizar com GSAP ScrollTrigger
  lenis.on('scroll', ScrollTrigger.update);

  gsap.ticker.add((time) => {
    lenis.raf(time * 1000);
  });
  gsap.ticker.lagSmoothing(0);

  return lenis;
}

// Hook React
export function useLenis() {
  const lenisRef = useRef(null);

  useEffect(() => {
    lenisRef.current = initLenis();
    return () => lenisRef.current?.destroy();
  }, []);

  return lenisRef;
}
```

---

## REACT THREE FIBER — Padrões v4

```jsx
import { Canvas, useFrame, useThree, extend } from '@react-three/fiber';
import { OrbitControls, Environment, Float, Sparkles, MeshTransmissionMaterial,
         Text3D, useGLTF, useFBO, Instances, Instance } from '@react-three/drei';
import { EffectComposer, Bloom, ChromaticAberration, Noise, Vignette } from '@react-three/postprocessing';
import { Physics, RigidBody, CuboidCollider } from '@react-three/rapier';

// Canvas base com configuração premium
function Scene3D({ children }) {
  return (
    <Canvas
      camera={{ position: [0, 0, 5], fov: 45 }}
      dpr={[1, Math.min(window.devicePixelRatio, 2)]} // limitar pixel ratio
      gl={{
        antialias: true,
        alpha: true,
        powerPreference: 'high-performance',
        outputColorSpace: 'srgb',
      }}
      shadows
    >
      <color attach="background" args={['#0a0a0a']} />
      <ambientLight intensity={0.5} />
      <directionalLight position={[10, 10, 5]} intensity={1} castShadow />

      <Environment preset="city" /> {/* HDRI lighting */}

      {children}

      <EffectComposer>
        <Bloom luminanceThreshold={0.9} intensity={0.5} />
        <ChromaticAberration offset={[0.0005, 0.0005]} />
        <Noise opacity={0.03} />
        <Vignette eskil={false} offset={0.1} darkness={0.7} />
      </EffectComposer>
    </Canvas>
  );
}

// Objeto 3D com física
function PhysicsObject({ position }) {
  return (
    <RigidBody position={position} colliders="ball" restitution={0.8}>
      <mesh castShadow>
        <sphereGeometry args={[0.5, 32, 32]} />
        <MeshTransmissionMaterial
          backside
          samples={16}
          resolution={512}
          transmission={1}
          roughness={0}
          thickness={0.5}
          ior={1.5}
          chromaticAberration={0.02}
          anisotropy={0.1}
        />
      </mesh>
    </RigidBody>
  );
}

// GPU Particles com instancing
function GPUParticles({ count = 5000 }) {
  const ref = useRef();
  const dummy = useMemo(() => new THREE.Object3D(), []);
  const positions = useMemo(() => {
    const pos = [];
    for (let i = 0; i < count; i++) {
      pos.push([(Math.random()-0.5)*10, (Math.random()-0.5)*10, (Math.random()-0.5)*10]);
    }
    return pos;
  }, [count]);

  useFrame(({ clock }) => {
    positions.forEach((p, i) => {
      dummy.position.set(
        p[0] + Math.sin(clock.elapsedTime * 0.3 + i) * 0.01,
        p[1] + Math.cos(clock.elapsedTime * 0.2 + i) * 0.01,
        p[2]
      );
      dummy.updateMatrix();
      ref.current.setMatrixAt(i, dummy.matrix);
    });
    ref.current.instanceMatrix.needsUpdate = true;
  });

  return (
    <instancedMesh ref={ref} args={[null, null, count]}>
      <sphereGeometry args={[0.02]} />
      <meshStandardMaterial color="#6366f1" />
    </instancedMesh>
  );
}

// Cleanup obrigatório
function useCleanup(scene) {
  useEffect(() => {
    return () => {
      scene.traverse(obj => {
        if (obj.geometry) obj.geometry.dispose();
        if (obj.material) {
          if (Array.isArray(obj.material)) obj.material.forEach(m => m.dispose());
          else obj.material.dispose();
        }
      });
    };
  }, [scene]);
}
```

---

## GLSL SHADERS CUSTOMIZADOS

```glsl
/* Fragment shader — wave distortion */
uniform float uTime;
uniform vec2 uMouse;
uniform sampler2D uTexture;
varying vec2 vUv;

vec2 rotate(vec2 v, float a) {
  float s = sin(a); float c = cos(a);
  return vec2(v.x*c - v.y*s, v.x*s + v.y*c);
}

void main() {
  vec2 uv = vUv;

  /* Distorção ondulante */
  float wave = sin(uv.x * 10.0 + uTime) * 0.02
             + cos(uv.y * 8.0  + uTime * 0.7) * 0.015;
  uv += wave;

  /* Efeito de proximidade ao mouse */
  vec2 mouseEffect = uMouse - uv;
  float dist = length(mouseEffect);
  uv += mouseEffect * 0.03 / (dist * dist + 0.1);

  gl_FragColor = texture2D(uTexture, uv);
}
```

```js
// ShaderMaterial com uniforms animados
const material = new THREE.ShaderMaterial({
  uniforms: {
    uTime:    { value: 0 },
    uMouse:   { value: new THREE.Vector2(0.5, 0.5) },
    uTexture: { value: texture },
  },
  vertexShader:   vertGLSL,
  fragmentShader: fragGLSL,
  transparent: true,
});

// Atualizar uniforms no RAF
function animate(time) {
  material.uniforms.uTime.value = time * 0.001;
  renderer.render(scene, camera);
  requestAnimationFrame(animate);
}
```

---

## SCROLLTELLING AVANÇADO

### Video Scrubbing (Apple-style)
```js
function initVideoScrub(videoEl, containerEl) {
  // Garante que video está pronto
  videoEl.pause();
  videoEl.currentTime = 0;

  ScrollTrigger.create({
    trigger: containerEl,
    start: 'top top',
    end: 'bottom bottom',
    scrub: true,
    onUpdate: (self) => {
      // requestVideoFrameCallback para sincronização precisa
      if ('requestVideoFrameCallback' in videoEl) {
        videoEl.requestVideoFrameCallback(() => {
          videoEl.currentTime = self.progress * videoEl.duration;
        });
      } else {
        videoEl.currentTime = self.progress * videoEl.duration;
      }
    },
  });
}
```

### Horizontal Scroll com Trigger Vertical
```js
function initHorizontalScroll(trackEl) {
  const totalWidth = trackEl.scrollWidth - window.innerWidth;

  gsap.to(trackEl, {
    x: -totalWidth,
    ease: 'none',
    scrollTrigger: {
      trigger: trackEl.parentElement,
      start: 'top top',
      end: () => `+=${totalWidth}`,
      pin: true,
      scrub: 1,
      anticipatePin: 1,
      invalidateOnRefresh: true,
    },
  });
}
```

### Canvas Drawing com Scroll
```js
function initDrawOnScroll(canvasEl, pathData) {
  const ctx = canvasEl.getContext('2d');
  const path = new Path2D(pathData);
  let progress = 0;

  ScrollTrigger.create({
    trigger: canvasEl,
    start: 'top 80%',
    end: 'bottom 20%',
    scrub: 2,
    onUpdate: (self) => {
      progress = self.progress;
      ctx.clearRect(0, 0, canvasEl.width, canvasEl.height);
      ctx.setLineDash([canvasEl.width * progress, canvasEl.width]);
      ctx.stroke(path);
    },
  });
}
```

### Word-by-Word Reveal
```js
function initWordReveal(containerEl) {
  const ctx = gsap.context(() => {
    const split = new SplitText(containerEl, { type: 'words' });

    gsap.fromTo(split.words,
      { opacity: 0.15, filter: 'blur(2px)' },
      {
        opacity: 1,
        filter: 'blur(0px)',
        stagger: { each: 0.04 },
        ease: 'none',
        scrollTrigger: {
          trigger: containerEl,
          start: 'top 80%',
          end: 'bottom 30%',
          scrub: true,
        },
      }
    );
  });
  return () => ctx.revert();
}
```

### Scroll Velocity Effects
```js
function initVelocityEffects() {
  let lastY = 0, velocity = 0;

  window.addEventListener('scroll', () => {
    velocity = window.scrollY - lastY;
    lastY = window.scrollY;

    // Skew proporcional à velocidade
    gsap.to('main', {
      skewY: velocity * 0.02,
      ease: 'power2.out',
      duration: 0.5,
      overwrite: true,
    });

    // Resetar
    clearTimeout(window._velocityTimer);
    window._velocityTimer = setTimeout(() => {
      gsap.to('main', { skewY: 0, duration: 0.8, ease: 'power3.out' });
    }, 100);
  }, { passive: true });
}
```

---

## CSS DE VANGUARDA

### View Transitions API
```js
// Transição entre páginas sem biblioteca
async function navigateTo(url) {
  if (!document.startViewTransition) {
    window.location.href = url;
    return;
  }

  await document.startViewTransition(async () => {
    const res = await fetch(url);
    const html = await res.text();
    const doc = new DOMParser().parseFromString(html, 'text/html');
    document.querySelector('main').replaceWith(doc.querySelector('main'));
  });
}
```

```css
/* Animação da transição */
::view-transition-old(root) {
  animation: 400ms ease-out both fade-and-scale-out;
}
::view-transition-new(root) {
  animation: 400ms ease-out both fade-and-scale-in;
}

@keyframes fade-and-scale-out {
  to { opacity: 0; transform: scale(0.95) translateY(-10px); }
}
@keyframes fade-and-scale-in {
  from { opacity: 0; transform: scale(1.05) translateY(10px); }
}

/* Elemento compartilhado entre páginas */
.product-image { view-transition-name: product-hero; }
::view-transition-group(product-hero) {
  animation-duration: 500ms;
  animation-timing-function: cubic-bezier(0.16, 1, 0.3, 1);
}
```

### Scroll-Driven Animations (zero JS)
```css
/* Fade in ao entrar no viewport — sem IntersectionObserver */
@keyframes fade-in {
  from { opacity: 0; transform: translateY(30px); }
}

.reveal {
  animation: fade-in linear both;
  animation-timeline: view();
  animation-range: entry 0% entry 40%;
}

/* Barra de progresso de leitura */
@keyframes grow {
  from { transform: scaleX(0); }
  to   { transform: scaleX(1); }
}

.reading-progress {
  position: fixed; top: 0; left: 0;
  height: 3px; background: var(--color-primary);
  transform-origin: left;
  animation: grow linear;
  animation-timeline: scroll(root block);
}

/* Parallax nativo */
.parallax-slow {
  animation: parallax-move linear;
  animation-timeline: scroll(root);
}
@keyframes parallax-move {
  from { transform: translateY(-50px); }
  to   { transform: translateY(50px); }
}
```

### :has() — Parent Selector
```css
/* Form válido/inválido */
form:has(:invalid) .submit-btn { opacity: 0.5; pointer-events: none; }
form:has(:valid)   .submit-btn { opacity: 1; }

/* Card com imagem vs sem */
.card:has(img) { grid-template-rows: auto 1fr; }
.card:not(:has(img)) .card-body { padding-block-start: 1.5rem; }

/* Navigation item ativo */
.nav:has(.active) .nav-indicator { display: block; }

/* Checkbox pai de lista */
.list:has(input:checked) { background: var(--color-primary-soft); }

/* Input com valor preenchido */
.field:has(input:not(:placeholder-shown)) .label { transform: translateY(-100%) scale(0.8); }
```

### OKLCH — Color Science
```css
:root {
  /* OKLCH: perceptualmente uniforme, mistura previsível */
  /* oklch(lightness chroma hue) */

  /* Escala primária em OKLCH — todas visivelmente equidistantes */
  --blue-50:  oklch(97% 0.02 260);
  --blue-100: oklch(93% 0.05 260);
  --blue-200: oklch(87% 0.09 260);
  --blue-300: oklch(78% 0.13 260);
  --blue-400: oklch(68% 0.17 260);
  --blue-500: oklch(58% 0.20 260);   /* valor base */
  --blue-600: oklch(49% 0.20 260);
  --blue-700: oklch(40% 0.18 260);
  --blue-800: oklch(30% 0.14 260);
  --blue-900: oklch(20% 0.09 260);

  /* Mistura de cores previsível */
  --primary: oklch(58% 0.20 260);
  --primary-light: oklch(from var(--primary) calc(l + 0.2) c h);
  --primary-dark:  oklch(from var(--primary) calc(l - 0.2) c h);

  /* Gradiente sem zona cinza no meio */
  --gradient: linear-gradient(in oklch, oklch(70% 0.25 30), oklch(70% 0.25 260));
}

/* Fallback para browsers sem P3 */
@supports (color: oklch(0% 0 0)) {
  :root { --primary: oklch(58% 0.20 260); }
}
```

### @layer — Arquitetura CSS
```css
/* Definir ordem das layers (menor para maior especificidade) */
@layer reset, base, tokens, components, utilities, overrides;

@layer reset {
  *, *::before, *::after { box-sizing: border-box; }
  body { margin: 0; }
}

@layer tokens {
  :root {
    --color-primary: oklch(58% 0.20 260);
    --space-4: 1rem;
  }
}

@layer components {
  .btn { /* estilos base do botão */ }
}

@layer utilities {
  /* Utilities sempre ganham de components — sem !important */
  .sr-only { position: absolute; width: 1px; /* ... */ }
}

@layer overrides {
  /* Customizações de tema específicas de página */
}
```

### CSS Nesting nativo + @scope
```css
/* CSS Nesting — sem Sass, suportado em todos os browsers */
.card {
  background: var(--color-surface);

  & .card-title {
    font-size: var(--text-xl);
  }

  &:hover {
    transform: translateY(-4px);
  }

  @media (max-width: 768px) {
    padding: 1rem;
  }
}

/* @scope — estilos que não "vazam" */
@scope (.card) {
  h3 { /* só h3 dentro de .card */ font-size: var(--text-lg); }
  p  { /* só p dentro de .card  */ line-height: 1.6; }
}

/* Anchor Positioning — tooltips sem JS */
.anchor { anchor-name: --my-anchor; }
.tooltip {
  position-anchor: --my-anchor;
  position: fixed;
  bottom: anchor(top);
  left: anchor(center);
  translate: -50% -8px;
}
```

---

## WEB IMERSIVA — Técnicas Premium

### Cursor Customizado com Estados
```jsx
function CustomCursor() {
  const dot = useRef(null);
  const ring = useRef(null);
  const [state, setState] = useState('default');

  useEffect(() => {
    const moveDot = ({ clientX: x, clientY: y }) => {
      gsap.set(dot.current, { x, y });
      gsap.to(ring.current, {
        x, y, duration: 0.4, ease: 'power2.out',
      });
    };

    const onEnterLink = () => setState('link');
    const onLeaveLink = () => setState('default');
    const onEnterVideo = () => setState('video');

    window.addEventListener('mousemove', moveDot);
    document.querySelectorAll('a, button').forEach(el => {
      el.addEventListener('mouseenter', onEnterLink);
      el.addEventListener('mouseleave', onLeaveLink);
    });
    document.querySelectorAll('video').forEach(el => {
      el.addEventListener('mouseenter', onEnterVideo);
      el.addEventListener('mouseleave', onLeaveLink);
    });

    return () => window.removeEventListener('mousemove', moveDot);
  }, []);

  const states = {
    default: { scale: 1, content: null },
    link: { scale: 2.5, mixBlendMode: 'difference', content: null },
    video: { scale: 3, content: '▶' },
  };

  return (
    <>
      <div ref={dot} className="cursor-dot" aria-hidden="true" />
      <div ref={ring} className={`cursor-ring cursor-ring--${state}`}
           aria-hidden="true"
           style={states[state]}>
        {states[state].content && <span>{states[state].content}</span>}
      </div>
    </>
  );
}
```

### Grain Orgânico Animado
```css
/* SVG filter grain — mais orgânico que CSS puro */
.grain::before {
  content: '';
  position: fixed;
  inset: -200%;
  width: 400%; height: 400%;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  opacity: 0.04;
  pointer-events: none;
  z-index: 9999;
  animation: grain-move 0.4s steps(2) infinite;
}

@keyframes grain-move {
  0%   { transform: translate(0, 0); }
  25%  { transform: translate(-5%, 5%); }
  50%  { transform: translate(5%, -5%); }
  75%  { transform: translate(-3%, -3%); }
}

@media (prefers-reduced-motion: reduce) {
  .grain::before { animation: none; }
}
```

### Preloader Premium
```jsx
function Preloader({ onComplete }) {
  const ref = useRef(null);
  const [progress, setProgress] = useState(0);

  useEffect(() => {
    const ctx = gsap.context(() => {
      // Simular carregamento (conectar a assets reais)
      gsap.to({ val: 0 }, {
        val: 100,
        duration: 2.5,
        ease: 'power2.inOut',
        onUpdate() { setProgress(Math.round(this.targets()[0].val)); },
        onComplete() {
          gsap.timeline()
            .to('.preloader-counter', { opacity: 0, duration: 0.3 })
            .to('.preloader-bar', { scaleX: 1, duration: 0.6, ease: 'power4.inOut' })
            .to(ref.current, {
              yPercent: -100,
              duration: 0.8,
              ease: 'power4.inOut',
              onComplete,
            });
        }
      });
    }, ref);
    return () => ctx.revert();
  }, []);

  return (
    <div ref={ref} className="preloader" role="progressbar"
         aria-valuenow={progress} aria-valuemin={0} aria-valuemax={100}
         aria-label="Carregando...">
      <span className="preloader-counter">{progress}</span>
      <div className="preloader-bar" style={{ transformOrigin: 'left', transform: 'scaleX(0)' }} />
    </div>
  );
}
```

### Liquid Glass Effect
```css
.glass-liquid {
  /* backdrop com blur forte + saturação alta */
  backdrop-filter: blur(20px) saturate(180%) brightness(1.1);
  -webkit-backdrop-filter: blur(20px) saturate(180%) brightness(1.1);

  /* Borda brilhante com gradiente */
  border: 1px solid transparent;
  background-image:
    linear-gradient(var(--color-surface), var(--color-surface)),
    linear-gradient(135deg,
      rgba(255,255,255,0.4) 0%,
      rgba(255,255,255,0.1) 40%,
      rgba(255,255,255,0.0) 60%,
      rgba(255,255,255,0.15) 100%);
  background-origin: border-box;
  background-clip: padding-box, border-box;

  /* Inner glow */
  box-shadow:
    inset 0 1px 0 rgba(255,255,255,0.2),
    inset 0 -1px 0 rgba(0,0,0,0.1),
    0 8px 32px rgba(0,0,0,0.2);

  border-radius: 16px;
}
```

### Generative Art Background
```js
// Flow field com Perlin noise
class FlowField {
  constructor(canvas, options = {}) {
    this.canvas = canvas;
    this.ctx = canvas.getContext('2d');
    this.particles = [];
    this.cols = Math.floor(canvas.width / 20);
    this.rows = Math.floor(canvas.height / 20);
    this.field = new Array(this.cols * this.rows);
    this.options = { color: '#6366f1', count: 300, ...options };
    this.init();
  }

  noise(x, y, t) {
    // Simplex-like noise via sin
    return Math.sin(x * 0.01 + t) * Math.cos(y * 0.01 + t * 0.5);
  }

  updateField(t) {
    for (let y = 0; y < this.rows; y++) {
      for (let x = 0; x < this.cols; x++) {
        const angle = this.noise(x, y, t) * Math.PI * 2;
        this.field[y * this.cols + x] = angle;
      }
    }
  }

  init() {
    for (let i = 0; i < this.options.count; i++) {
      this.particles.push({
        x: Math.random() * this.canvas.width,
        y: Math.random() * this.canvas.height,
        vx: 0, vy: 0, life: Math.random(),
      });
    }
  }

  draw(t) {
    this.updateField(t);
    this.ctx.fillStyle = 'rgba(10, 10, 10, 0.05)';
    this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);

    this.particles.forEach(p => {
      const col = Math.floor(p.x / 20);
      const row = Math.floor(p.y / 20);
      const angle = this.field[row * this.cols + col] || 0;

      p.vx = Math.cos(angle) * 2;
      p.vy = Math.sin(angle) * 2;
      p.x += p.vx;
      p.y += p.vy;
      p.life -= 0.005;

      // Resetar partícula morta
      if (p.life <= 0 || p.x < 0 || p.x > this.canvas.width || p.y < 0 || p.y > this.canvas.height) {
        p.x = Math.random() * this.canvas.width;
        p.y = Math.random() * this.canvas.height;
        p.life = 1;
      }

      this.ctx.globalAlpha = p.life * 0.3;
      this.ctx.fillStyle = this.options.color;
      this.ctx.beginPath();
      this.ctx.arc(p.x, p.y, 1, 0, Math.PI * 2);
      this.ctx.fill();
    });

    this.ctx.globalAlpha = 1;
  }
}
```

---

## PSICOLOGIA UX — SISTEMA DE DESIGN POR SEGMENTO v4

### Fintech
```
Lei de Hick aplicada: máx 3 ações principais visíveis
Von Restorff: único botão de cor no dashboard = "Transferir"
Loss Aversion: "R$ 142 em cashback expira em 3 dias"
Trust hierarchy: regulador + parceiro bancário + reviews
Cores: navy (confiança) + verde (crescimento/sucesso)
```

### Edtech
```
Zeigarnik: progresso que para em 80% = engajamento diário
Gamification: streak com perigo de quebrar, XP, badges animados
Peak-End: celebração exagerada ao completar uma lição
Serial Position: lição mais difícil no meio, não no fim
Reciprocidade: 7 dias grátis sem cartão antes de qualquer pedido
```

### E-commerce
```
Anchoring: preço original riscado + "você economiza R$ X"
Scarcity ética: "últimas 3 unidades" (apenas se verdade)
Social proof: reviews com foto > reviews só com nome
Loss Aversion: "Frete grátis — adicione R$ 15 para ativar"
Choice Architecture: "Mais vendido" badge no produto que quero vender
```

---

## PERFORMANCE AVANÇADA v4

### content-visibility
```css
/* Pular renderização de seções fora da viewport */
.section { content-visibility: auto; contain-intrinsic-size: 0 800px; }

/* CSS Containment manual */
.card {
  contain: layout paint; /* isola reflow e repaint */
}
.isolated-animation {
  contain: strict; /* isolamento total — cuidado com overflow */
}
```

### Resource Hints avançados
```html
<!-- Speculation Rules API — prerender próximas páginas -->
<script type="speculationrules">
{
  "prerender": [
    { "source": "list", "urls": ["/about", "/pricing"] },
    { "source": "document", "where": { "href_matches": "/blog/*" } }
  ],
  "prefetch": [
    { "source": "list", "urls": ["/api/user"] }
  ]
}
</script>

<!-- Priority hints -->
<img src="/hero.avif" fetchpriority="high" loading="eager" />
<img src="/below-fold.avif" fetchpriority="low" loading="lazy" />
<script src="/non-critical.js" fetchpriority="low" defer />
```

### Islands Architecture UI
```jsx
// Mostrar que parte da UI "vai acordar"
function HydrationAware({ children, fallback }) {
  const [hydrated, setHydrated] = useState(false);
  useEffect(() => setHydrated(true), []);

  if (!hydrated) return (
    <div className="island-loading" aria-busy="true">
      {fallback}
    </div>
  );
  return children;
}

// CSS
.island-loading { position: relative; }
.island-loading::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(90deg, transparent 30%, rgba(255,255,255,0.05) 50%, transparent 70%);
  animation: shimmer 1.5s infinite;
}
```

---

## DESIGN POR SEGMENTO v4 (completo)

### Fintech | Healthtech | Edtech | E-commerce | SaaS B2B | Agência
### Govtech | Gaming | Marketplace | Mobility
_(Ver referências completas em references/missing-segments.md)_

**Novos segmentos v4:**

### Climate Tech / Sustainability
```
Valores: impacto positivo, transparência, urgência controlada
Tipografia: humanista + mono para dados (Fraunces + DM Mono)
Cores: verde terra, não "neon" — verde-musgo, terracota, creme
Dados: visualizações de impacto, CO2 evitado, progresso real
Evitar: greenwashing visual (muito verde = desconfiança)
Refs: Stripe Climate, Pachama, Tomorrow.io
```

### Mental Health / Wellness
```
Valores: segurança, sem julgamento, calma, progresso gentil
Tipografia: arredondada e acolhedora (Nunito, Quicksand)
Cores: tons de terra e sage — NUNCA vermelho, roxo agressivo
Motion: extremamente suave, sem flash, sem transições bruscas
Copy: linguagem de primeira pessoa, sem jargão clínico
Refs: Headspace, Woebot, Calm, Bearable
```

---

## ANTI-PATTERNS v4

```css
/* ❌ ERRADO — anima sem GPU */
gsap.to(el, { width:'100%', top:0, left:0, marginTop:'20px' });
/* ✅ CERTO */
gsap.to(el, { scaleX:1, y:0, x:0, opacity:1 });

/* ❌ WebGL sem pixelRatio limitado */
renderer.setPixelRatio(window.devicePixelRatio);
/* ✅ CERTO */
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

/* ❌ Lenis sem sincronizar com ScrollTrigger */
const lenis = new Lenis();
/* ✅ CERTO */
lenis.on('scroll', ScrollTrigger.update);
gsap.ticker.add((time) => lenis.raf(time * 1000));

/* ❌ :has() sem fallback para browsers antigos */
/* ✅ usar @supports */
@supports selector(:has(*)) {
  form:has(:invalid) .btn { opacity: 0.5; }
}

/* ❌ OKLCH sem fallback */
color: oklch(58% 0.20 260);
/* ✅ com fallback */
color: #6366f1;
color: oklch(58% 0.20 260);
```

---

## CHECKLIST DE ENTREGA v4.0

- [ ] Self-review loop v4 executado (6 categorias)
- [ ] Design Intelligence: razões anotadas nas decisões principais
- [ ] Testado: 375px / 768px / 1280px / 1920px
- [ ] Dark mode e light mode verificados
- [ ] prefers-reduced-motion: animações, 3D, scroll effects
- [ ] WebGL: pixelRatio ≤ 2, dispose correto, cleanup
- [ ] Lenis + ScrollTrigger sincronizados
- [ ] OKLCH com fallback HSL/hex
- [ ] @layer aplicado se design system
- [ ] View Transitions com fallback
- [ ] Scroll-Driven Animations com @supports
- [ ] :has() com @supports selector(:has(*))
- [ ] Container Queries onde adequado
- [ ] CSS Logical Properties (RTL-ready)
- [ ] LCP: hero image preloaded com fetchpriority="high"
- [ ] CLS: dimensions definidas em imagens e vídeos
- [ ] INP: heavy work em scheduler.postTask ou requestIdleCallback
- [ ] content-visibility em seções longas
- [ ] Speculation Rules se Next.js/SPA
- [ ] Loading / Empty / Error com skeleton
- [ ] Psicologia UX: pelo menos 2 princípios aplicados e documentados

---

> components/ → 22 componentes completos
> references/ → webgl-r3f, animation-advanced, scrolltelling-advanced, immersive-web, psychology-ux, performance-advanced, css-cutting-edge, color-systems, figma-tokens, email-print-animation-modes, css-modern-typography-shadows, advanced-techniques, missing-segments
> docs/ → GUIA-COMPLETO, PROMPTS, FAQ, MODES

---

## TEXTO 3D — SISTEMA v5

### Tipos de texto 3D por contexto
```
Luxury/Hero:     Text3D com MeshTransmissionMaterial (vidro)
Tech/SaaS:       Text3D com matcap metálico
Editorial:       TextGeometry com bevel sutil e material flat
Neon/Gaming:     Texto com emissive + bloom post-processing
Curvo/Criativo:  CatmullRomCurve3 com chars posicionados na curva
Dissolve:        Partículas que formam e dissolvem texto
```

### Regras de texto 3D
```
SEMPRE:
□ Converter fonte com facetype.js antes (não usar system fonts)
□ bevelEnabled com bevelSegments ≥ 4 (suavidade)
□ Limitar a 1-2 palavras (performance)
□ Fallback 2D para mobile/low-end
□ prefers-reduced-motion: parar animação, manter visível

NUNCA:
□ Texto 3D em parágrafos (legibilidade zero)
□ Texto 3D sem iluminação adequada
□ Fontes sem versão JSON para Three.js
□ Mais de 3 objetos Text3D simultaneamente
```

---

## ÍCONES — SISTEMA DE DECISÃO v5

```
A�ão do sistema (save, close, edit):  Lucide — stroke 1.5, size 18-20px
Estado animado (loading→done):        Lottie com segmentos
Marca/identidade:                     SVG customizado próprio
Decorativo/hero:                      R3F ou SVG animado grande
Inline em texto:                      Lucide size 16px, vertical-align middle
```

### Animated icons pattern
```css
/* Stroke draw animation no hover */
.icon svg path {
  stroke-dasharray: 1000;
  stroke-dashoffset: 1000;
  transition: stroke-dashoffset 0.4s ease;
}
.icon:hover svg path { stroke-dashoffset: 0; }

/* State transition com opacity */
.icon-loading { opacity: 1; transition: opacity 0.2s; }
.icon-done    { opacity: 0; }
.icon.is-done .icon-loading { opacity: 0; }
.icon.is-done .icon-done    { opacity: 1; }
```

---

## BACKGROUNDS — SISTEMA DE DECISÃO v5

### Regra: nunca fundo liso #000 ou #fff puros
```
Luxury:     Noise pesado (#0a0005 quase-preto) + grain animado
Tech/SaaS:  Dot grid interativo + gradient sutil azul profundo
Criativo:   Fluid WebGL ou constellation particles + aurora
Editorial:  #fafaf8 (warm white) + grain microscópico
Wellness:   Gradient mesh OKLCH suave + breathing animation
Gaming:     Particles neon + god rays + dark deep (#050510)
Startup:    Gradient mesh animado com OKLCH
Govtech:    Branco puro ou off-white — zero distração
Fintech:    Grid de linhas fino + radial gradient azul-marinho
```

### Background budget de performance
```
60fps exige:
Mobile low:  CSS gradiente estático apenas
Mobile:      CSS animation (aurora CSS) ou canvas simples
Tablet:      Canvas animation ou WebGL simples (sem post-processing)
Desktop:     WebGL completo com post-processing
Desktop HW:  WebGL full + fluid simulation

Regra: testar no iPhone SE antes de considerar done
```

---

## LÓGICA DE RESPONSIVIDADE v5

### Paradigma por breakpoint (não só escalar)
```
MOBILE (<768px):
  - 1 coluna absoluta
  - Touch-first (44px mínimo)
  - Hierarquia simplificada (1 CTA visível)
  - Sem hover states (usar :active)
  - Swipe > scroll horizontal
  - Animações 50% mais rápidas
  - 3D → CSS 3D ou estático
  - svh para heights

TABLET (768-1024px):
  - 2 colunas máximo
  - Touch + mouse híbrido
  - Hover states presentes mas não dependentes
  - Animações moderadas
  - WebGL simples sem post-processing

DESKTOP (>1024px):
  - 3-4 colunas
  - Hover como affordance primária
  - Animações completas
  - WebGL + post-processing
  - Cursor customizado ativo
```

### Fluid spacing obrigatório
```css
/* NUNCA valores fixos para espaçamentos */
/* SEMPRE clamp() */
.section { padding-block: clamp(3rem, 8vw, 8rem); }
.card    { padding: clamp(1rem, 3vw, 2rem); }
.gap     { gap: clamp(1rem, 2.5vw, 2rem); }
```

---

## PERFORMANCE TOTAL v5

### Budget por tipo de dispositivo
```
Mobile low-end (Moto G, Galaxy A):
  Max drawcalls: 15
  Max triangles: 5k
  Max textures: 3 (512px)
  Post-processing: nenhum
  Particles: 0 (CSS apenas)

Mobile flagship:
  Max drawcalls: 50
  Max triangles: 50k
  Max textures: 5 (1024px)
  Post-processing: bloom apenas
  Particles: 500

Desktop médio:
  Max drawcalls: 150
  Max triangles: 500k
  Max textures: 10 (2048px)
  Post-processing: bloom + AA
  Particles: 3000

Desktop topo:
  Sem limite prático
  Fluid simulation
  Ray marching
  Particles: 50000+
```

### Checklist de performance ANTES de entregar
```
WebGL:
□ pixelRatio: Math.min(devicePixelRatio, 2)
□ Todos os geometries/materials com dispose() no cleanup
□ InstancedMesh para objetos repetidos (>3)
□ LOD para objetos distantes
□ Textures comprimidas com KTX2/Basis

Scroll:
□ passive: true em TODOS os scroll listeners
□ RAF throttle em handlers de scroll manual
□ Lenis sincronizado com ScrollTrigger
□ content-visibility: auto em seções longas
□ IntersectionObserver em vez de scroll para reveals

CSS:
□ will-change em MENOS de 4 elementos simultâneos
□ Remover will-change após animação
□ Apenas transform/opacity animados
□ contain: layout paint em componentes isolados

Fontes:
□ font-display: optional ou swap
□ size-adjust para fallback perfeito
□ WOFF2 apenas (não TTF)
□ unicode-range para subsetting
□ Preload da fonte above-fold
```

> Referências completas em:
> 3D/texto: references/3d-text-icons-folds.md
> Tipografia: references/typography-non-generic.md
> Câmera+scroll: references/camera-scroll-backgrounds-performance.md
> Responsividade: references/responsive-logic-system.md

---

## DESIGN CINEMATOGRÁFICO v6

### Princípio fundador
> Web design não é design gráfico com animação. É cinema interativo.
> O usuário é o espectador. O scroll é a linha do tempo. A seção é o frame.

### Protocolo pré-produção (antes de qualquer código)
```
ROTEIRO (30min):
□ Quem é o protagonista? (persona detalhada)
□ Qual é o problema que ele tem?
□ Qual é a transformação que o produto promove?
□ Three-act: Setup → Confronto → Resolução

COLOR SCRIPT (20min):
□ Mapear emoção por seção (como Pixar faz)
□ Início: paleta fria (problema) → Fim: paleta quente (solução)
□ Definir leitmotif visual (forma/cor recorrente)

STORYBOARD (60min):
□ Sketch de cada seção como quadro cinematográfico
□ Marcar: o que entra? Como? Velocidade?
□ Identificar o climax e o post-credit scene
□ Planejar 3 momentos de curiosity gap
```

### Three-Act Structure obrigatória
```
ATO 1 (0-30%) — SETUP:
→ Establishing shot: quem, o quê, por que importa em 3 segundos
→ Nomear a dor com precisão cirúrgica
→ Criar curiosity gap (revelar parcialmente)
COR: fria/neutra — ainda não há esperança

ATO 2 (30-70%) — CONFRONTO:
→ Revelação da solução progressivamente
→ Provas de cada claim (stats, depoimentos, cases)
→ The moment of revelation — o aha moment
COR: transição para warm — esperança crescente

ATO 3 (70-100%) — RESOLUÇÃO:
→ Climax emocional (seção mais impactante)
→ CTA como convite, não comando
→ Post-credit scene (surpresa após conversão)
COR: warm/vibrante — futuro positivo
```

### Técnicas cinematográficas em código
```js
// Slow motion em momento de impacto
gsap.timeline()
  .to(el, { scale: 1.02, duration: 0.5 })
  .to(el, { scale: 1, duration: 2.5 }) // slow-mo proposital

// Freeze frame — congelar no impacto
gsap.timeline()
  .to(el, { y: 0, opacity: 1, duration: 0.3, ease: "power4.out" })
  .to({}, { duration: 0.4 }) // pausa = peso
  .to(el, { /* continuar */ })

// Whip pan transition
gsap.to(from, { x: "-100vw", filter: "blur(20px)", duration: 0.2, ease: "power4.in" })
gsap.fromTo(to, { x: "100vw", filter: "blur(20px)" }, { x: 0, filter: "blur(0px)", duration: 0.2 })

// Match cut — forma que continua entre seções
const state = Flip.getState(element);
newSection.appendChild(element);
Flip.from(state, { duration: 1.2, ease: "power3.inOut" });
```

### Color grading por look
```css
/* Teal-Orange (blockbuster) */
.grade-action img { filter: saturate(1.2) hue-rotate(5deg); }

/* Film Noir */
.grade-noir img { filter: contrast(1.4) saturate(0.3); }

/* Blade Runner */
.grade-scifi img { filter: contrast(1.2) saturate(1.5) hue-rotate(-10deg) brightness(0.8); }

/* Wes Anderson */
.grade-wes img { filter: saturate(1.3) sepia(0.15) brightness(1.05); }

/* Bleach Bypass (Fincher) */
.grade-fincher img { filter: saturate(0.6) contrast(1.5) brightness(0.95); }

/* Paleta que evolui com scroll — via CSS variables + ScrollTrigger */
```

### Mise en scène — composição como diretor
```
REGRA DOS TERÇOS: elementos-chave em 1/3, não no centro
LEADING LINES: SVG paths que convergem para o CTA
EYELINE MATCH: foto com pessoa olhando para o CTA
NEGATIVE SPACE: silêncio visual — seções de respiro
DEPTH OF FIELD: blur crescente em camadas de profundidade
FIGURE-GROUND: elemento em movimento sobre fundo estático = figura imediata
```

### Gêneros de design
```
NOIR:       Fundos #080808+, serifada, alto contraste, grain, animações lentas
SCI-FI:     HUD elements, monospace, ciano/laranja, scanlines, mecânico
ROMANCE:    Pastéis quentes, cursiva, bokeh, muito espaço, suave
THRILLER:   Preto+vermelho, ultra-bold condensed, rápido, countdown
DOCUMENTÁRIO: Dados em destaque, fotografia real, sans neutra, dessaturado
ART FILM:   1 elemento por seção, espaço extremo, serifada light, lento
COMÉDIA:    Cores saturadas, bounce animations, humor em microcopy
```

### Som e timing musical
```js
// Timing baseado em BPM
const bpm = 120;
const beat = 60000 / bpm; // 500ms
const eighth = beat / 2;   // 250ms

gsap.from(".chars", {
  stagger: eighth / 1000, // 0.25s entre chars
  duration: beat / 1000,  // 0.5s cada
  ease: "power4.out",
});

// Sound design com Tone.js
const sound = new WebSoundDesign();
document.addEventListener("click", () => sound.init(), { once: true });
btn.addEventListener("click", () => sound.playConfirm());
lenis.on("scroll", ({ progress }) => sound.updateWithScroll(progress));
```

### Jornada emocional por seção
```
RECONHECIMENTO: "Eles me entendem" → paleta neutra, linguagem em 1ª pessoa
CURIOSIDADE:    "Pode ser diferente" → primeira revelação, leve saturação
DESEJO:         "Eu quero isso" → produto em uso, paleta warm
DÚVIDA:         "Funcionará para mim?" → depoimentos reais, paleta confiável
CONFIANÇA:      "É legítimo" → logos, certificações, números
EXCITAÇÃO:      "Preciso disso agora" → climax visual, saturação máxima
A�ÃO:           "Vou fazer isso" → CTA limpo, espaço em branco, hover imediato
```

> Referências completas: references/cinematic-storytelling.md
> Jornada emocional e gêneros: references/emotional-journey-genres.md

---

## TECNOLOGIAS DE REFERÊNCIA v7

### Decisão: Scroll-Triggered vs Scroll-Bound
```
SCROLL-TRIGGERED (start/end + disparo único):
→ Reveals, entradas, contadores, stagger de cards
→ once: true para melhor performance
→ toggleActions: "play none none none" padrão

SCROLL-BOUND / SCRUBBER (scrub: true):
→ Parallax, câmera 3D, video scrub, color transitions
→ scrub: número = inércia em segundos (1.5 = elegante)
→ ease: "none" OBRIGATÓRIO para scrub
→ NUNCA usar scrub em animações simples de reveal
```

### Stack de Scroll recomendada
```js
// Hierarquia de decisão
1. Lenis v2 + ScrollTrigger → padrão para todos os projetos
2. ScrollTrigger.scrollerProxy() → obrigatório com Lenis
3. Locomotive Scroll v5 → quando studio preferir (API similar ao Lenis)
4. Barba.js → para sites multi-page com transições entre páginas
5. ScrollTrigger.batch() → quando há muitos elementos com mesma animação
6. gsap.matchMedia() → para animações responsivas diferentes por breakpoint
```

### Tecnologias 3D — quando usar cada uma
```
Three.js vanilla:
→ Controle total, sem overhead React, canvas dedicado
→ Para: websites de produto, backgrounds WebGL, não-React

React Three Fiber:
→ Integração natural com React, hooks, state
→ Para: produtos React, componentes 3D em UI, interatividade complexa

Spline:
→ Exporta cena 3D sem código
→ Para: equipes sem shader knowledge, prototipagem rápida

Gaussian Splatting (gsplat.js):
→ Quando tem objeto/espaço físico para capturar
→ Para: imóveis, produtos físicos, patrimônio, turismo

Raymarching:
→ Efeitos impossíveis com geometria
→ Para: fluidos, nuvens, fractais, mundos procedurais

GPGPU:
→ Simulação de 100k+ partículas com física
→ Para: experiências imersivas, visualizações de dados massivos

WebGPU:
→ Compute shaders, 10x performance vs WebGL
→ Para: simulações pesadas, ML no browser, pioneers
```

### Física — decisão de biblioteca
```
Rapier.js (@react-three/rapier):
→ Rust→WASM, muito rápido, API limpa
→ Suporta: rigid bodies, character controller, joints, sensors
→ Para: maioria dos casos de física em WebGL

Ammo.js:
→ Bullet Physics (C++)→WASM
→ Suporta também: soft bodies, cloth, vehicles
→ Para: quando precisa de soft body ou vehicle physics avançado

Matter.js:
→ Física 2D puro JavaScript
→ Para: jogos 2D, física em DOM/Canvas 2D

Verlet manual:
→ Cloth, cordas, cabelos — quando quer controle total
→ Para: quando Rapier/Ammo são overkill para o efeito
```

### Canvas-only rendering
```
Usar quando:
→ Lista com 10k+ items (virtualização Canvas)
→ Editor visual complexo (Figma-like)
→ Gráfico com 1M+ pontos de dados
→ Interface em WebGL puro

NUNCA para:
→ UI normal (DOM é mais acessível e mantível)
→ Formulários e interações de usuário comuns
→ Quando SEO importa (Canvas não é indexável)
```

### WASM — integrações recomendadas
```
Rapier.js         → física (já usa WASM internamente)
ONNX Runtime      → inferência de ML
ImageMagick WASM  → processamento de imagem pesado
FFmpeg WASM       → encode/decode de vídeo no browser
SQLite WASM       → banco de dados local no browser
```

### GSAP — plugins e quando usar
```
ScrollTrigger    → scroll animations (gratuito)
Observer         → mouse/touch/wheel unificado (gratuito)
Flip             → layout animations (gratuito)
DrawSVG          → SVG path animations (Club)
MorphSVG         → SVG morphing (Club)
SplitText v3     → texto partido com auto-resize (Club)
Draggable        → drag com constraints e snap (Club)
InertiaPlugin    → momentum após drag (Club)
MotionPathPlugin → animação em path SVG (Club)

Licença Club ($99/ano) OBRIGATÓRIA para:
→ Projetos comerciais usando plugins premium
→ Alternativas gratuitas: Lenis+ScrollTrigger (scroll), anime.js (morph)
```

> Referências v7:
> Scroll sistema: references/scroll-navigation-complete.md
> 3D fronteira: references/3d-frontier-technologies.md
> Física e Motion UI: references/physics-wasm-motion-ui.md
