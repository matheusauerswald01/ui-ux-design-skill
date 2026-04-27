# Guia Completo — UI/UX Design Skill

## Índice

1. [Instalação Detalhada](#1-instalação-detalhada)
2. [Como a Skill Funciona](#2-como-a-skill-funciona)
3. [Módulos Principais](#3-módulos-principais)
4. [GSAP — Referência Rápida](#4-gsap--referência-rápida)
5. [Framer Motion — Referência Rápida](#5-framer-motion--referência-rápida)
6. [Three.js — Referência Rápida](#6-threejs--referência-rápida)
7. [Sistema de Design](#7-sistema-de-design)
8. [Responsividade](#8-responsividade)
9. [Performance](#9-performance)
10. [Acessibilidade](#10-acessibilidade)

---

## 1. Instalação Detalhada

### Pré-requisitos
- Conta no [claude.ai](https://claude.ai) (gratuita ou Pro)
- Acesso às configurações de Skills (disponível em planos Pro+)

### Passo a passo

**Via arquivo .skill:**
1. Vá em [Releases](../releases/latest) e baixe `ui-ux-design.skill`
2. Abra o Claude → clique no seu avatar → **Settings**
3. Na aba **Skills**, clique em **+ Install from file**
4. Selecione o arquivo `.skill` baixado
5. Confirme a instalação

**Verificando a instalação:**
Digite no Claude: *"Quais skills você tem instaladas?"*
O Claude deve listar `ui-ux-design` entre elas.

---

## 2. Como a Skill Funciona

A skill é ativada **automaticamente** quando você menciona qualquer coisa relacionada a design visual. Você não precisa "ativar" ela — o Claude reconhece o contexto e aplica as instruções.

**Palavras que ativam a skill:**
- design, layout, interface, UI, UX
- animação, motion, GSAP, Framer Motion
- Three.js, 3D, WebGL
- responsivo, mobile, breakpoint
- hero, landing page, dashboard
- paleta de cores, tipografia, fontes
- scrolltelling, parallax, scroll animation
- e muito mais...

**O que muda com a skill:**
Sem skill → Claude gera código genérico com Bootstrap/Tailwind padrão, cores aleatórias e sem estratégia visual.

Com skill → Claude raciocina sobre identidade, escolhe tecnologias adequadas, previne problemas de layout antes de gerar, aplica padrões de motion design profissional.

---

## 3. Módulos Principais

### 3.1 Raciocínio Preventivo de Layout

A skill inclui um checklist mental que o Claude executa antes de gerar qualquer código:

```
ANTES de implementar uma seção:
□ Texto nunca sobrepõe outro em nenhum breakpoint
□ Cards nunca ficam isolados no mobile
□ Imagens nunca cortam conteúdo importante
□ Botões têm área de toque mínima de 44x44px
□ Nenhum elemento ultrapassa o viewport horizontalmente
```

Isso significa que problemas comuns como "texto sendo tampado" ou "card flutuando sozinho no mobile" são prevenidos antes mesmo de acontecer.

### 3.2 Filosofia Anti-Genérico

A skill rejeita ativamente padrões genéricos:
- ❌ Gradientes roxo→azul sem propósito
- ❌ Cards com `border-radius: 8px` e `box-shadow` padrão
- ❌ Tipografia Inter com peso 400 sem hierarquia
- ❌ Layouts que poderiam ser de qualquer site

Em vez disso, extrai a **essência** do projeto e amplifica a identidade.

### 3.3 Estrutura Narrativa

Toda página é tratada como uma história em 3 atos:
```
ACT 1 — HOOK: Proposta de valor em <7 palavras + visual impactante
ACT 2 — DESENVOLVIMENTO: Problema→Solução + provas progressivas  
ACT 3 — CONVERSÃO: CTA com fricção zero + social proof final
```

---

## 4. GSAP — Referência Rápida

### Setup Padrão
```js
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { SplitText } from "gsap/SplitText";

gsap.registerPlugin(ScrollTrigger, SplitText);
```

### Animação de Hero Premium
Peça ao Claude: *"Crie uma animação de hero com SplitText para os títulos e entrada em stagger"*

### ScrollTrigger com Pin
Peça ao Claude: *"Quero uma seção pinada enquanto o usuário rola, revelando conteúdo progressivamente"*

### Cleanup em React (obrigatório)
```js
useEffect(() => {
  const ctx = gsap.context(() => {
    // suas animações aqui
  }, containerRef);
  return () => ctx.revert(); // limpa tudo ao desmontar
}, []);
```

---

## 5. Framer Motion — Referência Rápida

### Padrão de Entrada no Viewport
```jsx
<motion.div
  initial={{ opacity: 0, y: 40 }}
  whileInView={{ opacity: 1, y: 0 }}
  viewport={{ once: true, margin: "-100px" }}
  transition={{ duration: 0.7, ease: [0.25, 0.1, 0.25, 1] }}
>
  conteúdo
</motion.div>
```

### Stagger em Listas
Peça ao Claude: *"Crie um grid de cards com entrada em stagger usando Framer Motion"*

### Cursor Customizado
Peça ao Claude: *"Cursor customizado com Framer Motion que segue o mouse com spring"*

---

## 6. Three.js — Referência Rápida

### Quando Usar Three.js
- Background 3D interativo (partículas, geometrias)
- Produto 3D com visualização 360°
- Efeitos de shader (distorção, ruído, líquido)
- Elementos decorativos que reagem ao scroll ou mouse

### Regras de Performance
A skill garante automaticamente:
- `renderer.setPixelRatio(Math.min(devicePixelRatio, 2))` — evita sobrecarga em Retina
- `InstancedMesh` para múltiplos objetos
- Dispose correto de geometrias e materiais
- `ACESFilmicToneMapping` para cores realistas

---

## 7. Sistema de Design

### Tokens CSS — Estrutura Base
A skill sempre propõe um sistema de tokens antes dos componentes:

```css
:root {
  /* Espaçamento */
  --space-1: 0.25rem;  /* 4px */
  --space-2: 0.5rem;   /* 8px */
  --space-4: 1rem;     /* 16px */
  --space-8: 2rem;     /* 32px */
  --space-16: 4rem;    /* 64px */

  /* Raios */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 16px;
  --radius-full: 9999px;

  /* Transições */
  --transition-fast:   150ms cubic-bezier(0.4, 0, 0.2, 1);
  --transition-base:   300ms cubic-bezier(0.4, 0, 0.2, 1);
  --transition-slow:   500ms cubic-bezier(0.4, 0, 0.2, 1);
}
```

### Paletas Disponíveis
Ver [references/color-systems.md](../references/color-systems.md) para paletas completas de:
- Dark Luxury
- Tech Dark
- Organic Light
- Bold Agency
- Midnight Blue

---

## 8. Responsividade

### Filosofia: Mobile-First + Fluido

A skill usa `clamp()` para eliminar breakpoints desnecessários:

```css
/* Em vez de breakpoints: */
@media (max-width: 768px) { font-size: 2rem; }
@media (min-width: 768px) { font-size: 4rem; }

/* A skill usa: */
font-size: clamp(2rem, 6vw, 4rem);
```

### Container Fluido
```css
.container {
  width: min(100% - 2.5rem, 1280px);
  margin-inline: auto;
}
```

### Grid Adaptável (sem breakpoints!)
```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(100%, 320px), 1fr));
  gap: clamp(1rem, 3vw, 2rem);
}
```

---

## 9. Performance

### Regra de Ouro
**Nunca anime `top`, `left`, `width`, `height` ou `margin`.**

Anime apenas:
- `transform: translateX/Y/Z/rotate/scale`
- `opacity`
- `filter` (com cuidado — promove ao compositor)

### will-change — Uso Correto
```css
/* Adicionar ANTES da animação começar */
.about-to-animate { will-change: transform, opacity; }

/* Remover DEPOIS da animação terminar */
element.addEventListener('transitionend', () => {
  element.style.willChange = 'auto';
});
```

### Checklist de Performance
- [ ] Imagens com `loading="lazy"` (exceto above the fold)
- [ ] Fontes com `font-display: swap`
- [ ] Vídeos com `preload="metadata"` + `poster`
- [ ] Three.js com `pixelRatio` limitado a 2
- [ ] ScrollTrigger com `once: true` quando possível

---

## 10. Acessibilidade

### Obrigatório em Todo Projeto
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Em GSAP
```js
const prefersReduced = window.matchMedia("(prefers-reduced-motion: reduce)").matches;

if (!prefersReduced) {
  // animações completas
} else {
  // versão sem movimento (apenas opacity)
}
```

### Checklist de Acessibilidade
- [ ] Contraste mínimo 4.5:1 para texto normal
- [ ] Todos os elementos interativos acessíveis por teclado
- [ ] `aria-label` em ícones sem texto
- [ ] Foco visível estilizado (não remover `outline`)
- [ ] Hierarquia de headings lógica (h1→h2→h3)
- [ ] Textos alternativos em imagens informativas
