# Referência: CSS de Vanguarda + Prompts v4

---

## CSS CUTTING EDGE — Completo

### Scroll-Driven Animations — casos avançados
```css
/* Revelar texto por palavra com scroll */
@keyframes word-reveal {
  from { opacity: 0.15; filter: blur(2px); }
  to   { opacity: 1;    filter: blur(0); }
}

.word-reveal-text span {
  display: inline-block;
  animation: word-reveal linear;
  animation-timeline: view();
  animation-range: entry-crossing 0% entry-crossing 50%;
}

/* Progress de leitura */
@keyframes reading-progress { to { transform: scaleX(1); } }
.reading-bar {
  position: fixed; top: 0; left: 0; right: 0; height: 3px;
  background: var(--color-primary);
  transform-origin: left;
  transform: scaleX(0);
  animation: reading-progress linear;
  animation-timeline: scroll(root block);
}

/* Fade in sections sequencialmente */
.section {
  animation: fade-up linear both;
  animation-timeline: view();
  animation-range: entry 0% entry 30%;
}
@keyframes fade-up {
  from { opacity: 0; transform: translateY(40px); }
}

/* Parallax puro CSS */
.parallax-layer {
  animation: parallax linear;
  animation-timeline: scroll(root block);
}
@keyframes parallax {
  from { transform: translateY(-30px); }
  to   { transform: translateY(30px); }
}

/* Rotação de ícone com scroll */
.rotating-icon {
  animation: spin-on-scroll linear;
  animation-timeline: scroll(root block);
}
@keyframes spin-on-scroll { to { transform: rotate(360deg); } }
```

### :has() — Casos avançados
```css
/* Floating label em inputs */
.field { position: relative; }
.field-input { padding-block: 1.25rem 0.5rem; }
.field-label {
  position: absolute; top: 0.875rem; left: 0.875rem;
  font-size: 1rem; color: var(--color-text-muted);
  transition: all var(--motion-fast) var(--ease-smooth);
  pointer-events: none;
}
/* Mover label quando tem valor ou está focado — sem JS */
.field:has(input:focus) .field-label,
.field:has(input:not(:placeholder-shown)) .field-label {
  transform: translateY(-60%) scale(0.75);
  color: var(--color-primary);
  background: var(--color-surface);
  padding-inline: 4px;
}

/* Desabilitar botão quando form inválido */
form:has(:invalid) .form-submit { opacity: 0.5; pointer-events: none; cursor: not-allowed; }
form:has(:valid) .form-submit { opacity: 1; pointer-events: auto; }

/* Grid de card diferente se tem imagem */
.card:has(img) { display: grid; grid-template-rows: auto 1fr; }
.card:not(:has(img)) { padding-block-start: 1.5rem; }

/* Sidebar colapsada com filho ativo */
.nav-item:has(.nav-submenu .active) > .nav-link {
  color: var(--color-primary);
  font-weight: 600;
}

/* Contador de itens selecionados */
.list:has(input:checked) .bulk-actions { display: flex; }
.list:not(:has(input:checked)) .bulk-actions { display: none; }
```

### @layer — Design System production
```css
/* Definição explícita de layers */
@layer reset, theme, base, layout, components, patterns, utilities, overrides;

@layer reset {
  *, *::before, *::after { box-sizing: border-box; }
  * { margin: 0; }
  img, picture, video, canvas, svg { display: block; max-width: 100%; }
  input, button, textarea, select { font: inherit; }
  p, h1, h2, h3, h4, h5, h6 { overflow-wrap: break-word; }
}

@layer theme {
  :root {
    /* Tokens em layer de tema */
    --color-primary: oklch(58% 0.20 260);
    --space-unit: 0.25rem;
  }
  [data-theme="dark"] { /* dark overrides */ }
}

@layer components {
  .btn { /* sem !important — @layer cuida da especificidade */ }
  .btn-primary { background: var(--color-primary); }
}

@layer utilities {
  /* Utilities sempre ganham por serem em layer maior */
  .sr-only { position: absolute; width: 1px; height: 1px; /* ... */ }
  .d-none { display: none !important; } /* !important aqui está OK */
}

/* Importar com layer */
@import url("reset.css") layer(reset);
@import url("components.css") layer(components);
```

### CSS @scope — Estilos escopados
```css
/* Estilos que só se aplicam dentro do componente */
@scope (.card) {
  /* Estes estilos NÃO afetam elementos fora de .card */
  h3 { font-size: 1.125rem; font-weight: 600; }
  p  { color: var(--color-text-secondary); line-height: 1.6; }
  a  { color: var(--color-primary); }
}

/* Escopo com exclusão */
@scope (.card) to (.card-footer) {
  /* Afeta elementos entre .card e .card-footer */
  * { color: inherit; }
}

/* Útil para temas por componente */
@scope (.dark-card) {
  --color-bg: #1a1a1a;
  --color-text: white;
  * { color: var(--color-text); background: var(--color-bg); }
}
```

### Anchor Positioning — tooltips sem JS
```css
/* Definir anchor */
.trigger {
  anchor-name: --tooltip-anchor;
  position: relative;
}

/* Posicionar tooltip relativo ao anchor */
.tooltip {
  position-anchor: --tooltip-anchor;
  position: fixed;
  position-try-fallbacks: flip-block, flip-inline, flip-block flip-inline;
  /* Top por padrão */
  bottom: anchor(top);
  left: anchor(center);
  translate: -50% -8px;
  /* Bottom fallback */
  @position-try flip-block {
    top: anchor(bottom);
    bottom: auto;
    translate: -50% 8px;
  }
}

/* Popover API nativo */
.popover {
  popover: auto; /* ou "manual" */
  position-anchor: --trigger;
  top: anchor(bottom);
  left: anchor(left);
  margin-top: 8px;
}
button[popovertarget="my-popover"] { anchor-name: --trigger; }
```

### OKLCH — Sistema de cores completo
```css
/* Sistema em OKLCH — perceptualmente uniforme */
:root {
  /* Escala primária: lightness muda 8% por step */
  --hue: 260; /* azul */
  --chroma: 0.20;

  --blue-50:  oklch(97% calc(var(--chroma) * 0.1)  var(--hue));
  --blue-100: oklch(93% calc(var(--chroma) * 0.25) var(--hue));
  --blue-200: oklch(85% calc(var(--chroma) * 0.5)  var(--hue));
  --blue-300: oklch(76% calc(var(--chroma) * 0.7)  var(--hue));
  --blue-400: oklch(68% calc(var(--chroma) * 0.85) var(--hue));
  --blue-500: oklch(58% var(--chroma)               var(--hue));
  --blue-600: oklch(50% var(--chroma)               var(--hue));
  --blue-700: oklch(42% var(--chroma)               var(--hue));
  --blue-800: oklch(33% calc(var(--chroma) * 0.8)  var(--hue));
  --blue-900: oklch(22% calc(var(--chroma) * 0.5)  var(--hue));

  /* Fallback para browsers sem OKLCH */
  --primary: #6366f1;
  /* Override se suportado */
  @supports (color: oklch(0% 0 0)) {
    --primary: var(--blue-500);
  }

  /* Gradiente em oklch — sem "zona morta" cinza */
  --gradient-primary: linear-gradient(in oklch, var(--blue-400), var(--blue-700));

  /* Mistura relativa */
  --primary-hover: oklch(from var(--primary) calc(l - 0.08) c h);
  --primary-light: oklch(from var(--primary) calc(l + 0.15) c h);
}
```

---

## PROMPTS v4 — Biblioteca expandida

### 3D e WebGL
```
Crie uma hero section com cena React Three Fiber:
- Blob 3D animado com morphing (sphereGeometry + noise)
- Material transmissivo (vidro) com MeshTransmissionMaterial
- Post-processing: bloom suave + chromatic aberration
- Partículas de fundo com GPU instancing
- Câmera suave que segue o mouse com lerp
- Reduced-motion: fallback para gradiente CSS estático
```

```
Crie um product configurator 3D:
- Carregar modelo GLTF com Draco compression
- Trocar cor do material com seletor
- OrbitControls para rotação
- Screenshot button
- Loading state com skeleton
- Anotações em pontos do modelo
```

### Scrolltelling avançado
```
Crie uma landing page com video scrubbing estilo Apple:
- Vídeo que avança com o scroll do usuário
- Texto que aparece sobreposto em momentos específicos
- Container pinado com altura 3x a viewport
- requestVideoFrameCallback para sincronização
- Fallback de imagem para mobile (baixo bandwidth)
```

```
Crie uma seção de scrolltelling com word-by-word reveal:
- Parágrafo de 50 palavras que vai de opacity 0.15 → 1 por palavra
- Cada palavra revela conforme scroll avança
- GSAP SplitText + ScrollTrigger scrub
- Linha de progresso visual ao lado
```

### Web imersiva
```
Crie uma página de agência nível Awwwards:
- Preloader com animação de 2.5s
- Cursor customizado com 4 estados (default, link, media, drag)
- Grain orgânico em todas as seções
- Hero com WebGL background (flow field de partículas)
- Scrolltelling: câmera 3D que se move com scroll
- Footer com easter egg no logo
```

### Psicologia UX
```
Redesenhe esta tela de pricing aplicando:
- Choice Architecture: âncora alta primeiro, destaque no plano target
- Loss Aversion: "não perca X" em vez de "ganhe X"
- Social proof hierarchy: expert + reviews com foto + contagem
- Zeigarnik: progress de setup que para em 80% no plano free
- Serial Position: feature mais valiosa no início E no fim da lista
```

### CSS vanguarda
```
Implemente floating labels em todos os campos do formulário usando :has() — sem JavaScript.
Adicione validação visual com :valid/:invalid e :has() para desabilitar o submit quando inválido.
```

```
Crie um sistema de animações de scroll usando APENAS Scroll-Driven Animations CSS:
- Fade in de seções
- Progress bar de leitura
- Parallax em imagens
- Rotação de ícone decorativo
Zero JavaScript, apenas CSS @keyframes + animation-timeline: scroll()
```

### Design Critique
```
Faça um Design Critique completo neste componente [colar código].
Quero:
1. Score atual em 6 dimensões
2. Problemas críticos com código de solução
3. Problemas de qualidade com explicação do princípio violado
4. 3 oportunidades de elevação
5. Score potencial após melhorias
6. Código refatorado completo com comentários de Design Intelligence
```

### Psicologia aplicada
```
Crie um onboarding de 5 etapas para um SaaS aplicando:
- Reciprocidade: dar valor antes de pedir cadastro
- Efeito Zeigarnik: progresso visível que para em 80%
- Peak-End: animação de celebração especial na última etapa
- Law of Hick: máximo 1 ação por tela
- Loss Aversion: "não perca seu progresso" na etapa 4
```
