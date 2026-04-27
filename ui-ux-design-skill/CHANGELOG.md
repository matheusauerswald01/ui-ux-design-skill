# Changelog

## [4.0.0] — 2025

### 89 melhorias implementadas sobre v3.0

**3D & WebGL (18 melhorias)**
- React Three Fiber completo: Canvas, useFrame, useThree, Suspense
- Drei helpers: MeshTransmissionMaterial, Environment HDRI, Float, Sparkles, Text3D
- GLSL Shaders customizados: WaveMaterial com uniforms, noise, mouse interaction
- Post-processing: Bloom, ChromaticAberration, DepthOfField, Noise, Vignette
- GPU Particle Systems com InstancedMesh
- 3D Typography com TextGeometry e matcap
- Física 3D com @react-three/rapier: RigidBody, colliders, joints
- 3D Product Viewer com GLTF, Draco, AR mode
- Camera scroll storytelling com CatmullRomCurve3
- WebGPU renderer (experimental)
- Spline integration com @splinetool/react-spline
- Geometry morphing com MorphTargetInfluences e Simplex noise
- PBR Materials e HDRI com Polyhaven
- Fluid simulation com Navier-Stokes e ping-pong FBO
- CSS 3D avançado sem WebGL: perspective, preserve-3d, cube carousel
- Instancing avançado com LOD automático
- Áudio espacial 3D com PannerNode e PositionalAudio
- Geração procedural: terrain, L-systems, asteroides

**Animação avançada (16 melhorias)**
- GSAP Timeline orquestração mestre com sub-timelines, labels, timeScale
- Lenis smooth scroll com integração GSAP e SPA support
- FLIP animations com GSAP Flip plugin
- Motion Path com MotionPathPlugin e CSS offset-path
- Text effects: ScrambleText, typewriter, mask reveal
- Spring physics do zero: Euler integration, RK4, chains
- Canvas 2D: OffscreenCanvas, pixel manipulation, generative art
- SVG morphing com MorphSVG e DrawSVG plugins
- Coreografia entrada/saída com interrupção graceful
- Reveal animations: wipe, iris, curtain, pixel dissolve, glitch reveal
- Anime.js para SVG complexo e Mo.js para burst particles
- Remotion avançado: compositions, Audio sync, Lambda rendering
- Web Animations API nativa sem bibliotecas
- Número animado avançado: odometer, gauge, SVG arc
- Lottie avançado: segmentos, interatividade, DotLottie format
- Campos magnéticos N-body entre elementos

**Scrolltelling (11 melhorias)**
- Video scrubbing Apple-style com requestVideoFrameCallback
- Horizontal scroll com trigger vertical e GSAP pin
- Canvas drawing progressivo com scroll
- Word-by-word opacity reveal com SplitText
- Parallax multicamadas 6-8 layers com blur crescente
- Sticky narrative sections com feature reveal progressivo
- 3D scene scroll-linked com ScrollTrigger scrub
- Scroll-linked color themes com lerp
- Velocity effects: skew, partículas, blur
- Chapter-based scrolltelling com navegação e progress
- Layout morphing com FLIP triggered por scroll

**Web imersiva (14 melhorias)**
- Cursor premium com 5 estados e spring physics
- Grain orgânico animado via SVG feTurbulence
- Aurora / gradient mesh com blobs animados
- Glitch effect com clip-path slices
- Liquid glass Apple-style com backdrop-filter e feDisplacementMap
- Generative art background: flow field com Perlin noise
- Page transitions cinematográficas: curtain, iris, flood, shatter
- WebGL background interativo com ripple e displacement map
- Claymorphism / inflate 3D com multiple shadows
- Estrutura de site imersivo completo (arquitetura Awwwards)
- Preloader premium com transição coordenada
- Easter eggs: Konami code, confetti, haptic feedback
- Video backgrounds otimizados com ffmpeg strategy
- Noise & distortion CSS/SVG: feTurbulence, feColorMatrix, feBlend

**CSS de vanguarda (8 melhorias)**
- View Transitions API com shared element morph
- Scroll-Driven Animations CSS: animation-timeline, view(), scroll()
- :has() parent selector em 10+ casos de uso
- @layer cascade layers para design system
- @scope para estilos escopados nativos
- CSS Anchor Positioning para tooltips sem JS
- CSS Nesting nativo sem Sass
- OKLCH & P3 wide gamut: sistema de paletas perceptualmente uniforme

**Psicologia UX (7 princípios)**
- Lei de Fitts em código CSS (44px mínimo)
- Lei de Hick: regras numéricas de opções
- Princípios Gestalt em layout
- Efeito Zeigarnik com componentes de progresso
- Peak-End Rule em momentos de deleite
- Loss Aversion em copy e urgência ética
- Choice Architecture em pricing e defaults

**Componentes novos (13)**
- Command Palette com Fuse.js e keyboard navigation
- Data Table com TanStack Table v8 e react-virtual
- Rich Text Editor com Tiptap, bubble menu, slash commands
- Kanban com @dnd-kit, drag entre colunas, WIP limits
- Gantt chart com zoom, progresso, today line
- OTP/PIN Input com paste, backspace navigation, shake error
- Color Picker com EyeDropper API, OKLCH, contrast ratio real-time
- Signature Capture com pressure simulation e Bézier curves
- Resizable Panels com react-resizable-panels
- Notification Center com grupos por data, otimistic mark-read
- (+ Infinite Canvas, Node Editor, Gallery Masonry nas referências)

**Performance (6 melhorias)**
- content-visibility: auto com contain-intrinsic-size
- Islands Architecture com indicador de hidratação
- Speculation Rules API para prerender de próximas páginas
- Service Worker com 3 estratégias de cache
- Stale-while-revalidate UI pattern
- INP optimization com scheduler.postTask e debounce

**Segmentos novos (5)**
- Climate Tech / Sustainability
- Mental Health / Wellness
- PropTech / Imóveis
- Logistics / Supply Chain
- EdTech avançado / Micro-learning

**Modos especiais (2 novos)**
- Design Intelligence: explicar princípio de cada decisão com comentário inline
- Design Archaeology: reconstruir design system de código legado

## [3.0.0] — 2025
31 melhorias: modos especiais, CSS moderno, dark/light mode, Web Vitals, AI-native UI, spatial/AR, componentes Footer/Pricing/Forms/Microinterações/DataViz

## [2.0.0] — 2025
Protocolo de briefing, anti-patterns, motion tokens, 6 segmentos, estados, revisão de código, acessibilidade profunda, Figma tokens, 4 componentes base

## [1.0.0] — 2025
SKILL.md inicial, GSAP/Framer Motion/Three.js básico, técnicas avançadas, paletas

## Roadmap v4.1

- [ ] Componente: Testimonials masonry com carrossel
- [ ] Componente: Settings page completa
- [ ] Componente: Search com Algolia/Typesense
- [ ] Componente: Infinite Canvas (Excalidraw-like)
- [ ] Referência: React Native / Expo patterns
- [ ] Referência: Storybook documentation patterns
- [ ] Modo: Design Brief Generator (Claude cria o briefing)
- [ ] Modo: A/B Test Designer (propõe variantes para teste)
