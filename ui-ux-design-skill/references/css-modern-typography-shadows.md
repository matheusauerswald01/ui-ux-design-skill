# Referências: CSS Moderno, Sombras, Tipografia Avançada

---

## CSS MODERNO COMPLETO

### Container Queries — Guia Completo
```css
/* Declarar container */
.sidebar          { container-type: inline-size; container-name: sidebar; }
.card-wrapper     { container-type: inline-size; }
.layout           { container-type: size; } /* width E height */

/* Query por nome */
@container sidebar (max-width: 220px) {
  .nav-text { display: none; }
  .nav-item { justify-content: center; }
}

/* Query por tamanho */
@container (min-width: 600px) {
  .product-card { flex-direction: row; }
  .product-image { width: 40%; }
}

/* Container query units */
.hero-text {
  font-size: clamp(1.5rem, 5cqi, 3rem); /* cqi = container query inline */
  padding: 3cqb; /* cqb = container query block */
}

/* Style queries (experimental — verificar suporte) */
@container style(--variant: featured) {
  .card { border: 2px solid var(--color-primary); }
}
```

### CSS Grid Avançado
```css
/* Subgrid — alinhamento entre cards */
.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  grid-template-rows: auto;
  gap: 1.5rem;
}

.product-card {
  display: grid;
  grid-row: span 4; /* ocupa 4 rows do pai */
  grid-template-rows: subgrid; /* cada card herda rows do pai */
  /* resultado: todas as imagens na mesma altura,
     todos os títulos alinhados, etc. */
}

/* Grid editorial assimétrico */
.editorial {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  grid-template-rows: repeat(6, minmax(80px, auto));
  gap: 1rem;
}

.editorial-hero    { grid-column: 1 / 8;  grid-row: 1 / 4; }
.editorial-side-1  { grid-column: 8 / 13; grid-row: 1 / 3; }
.editorial-side-2  { grid-column: 8 / 13; grid-row: 3 / 4; }
.editorial-bottom  { grid-column: 1 / 13; grid-row: 4 / 7; }

/* Masonry com grid (Chrome 115+) */
.masonry {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  grid-template-rows: masonry;
  gap: 1rem;
}

/* Named grid areas */
.layout {
  display: grid;
  grid-template-areas:
    "header header header"
    "sidebar main    main"
    "footer  footer  footer";
  grid-template-columns: 240px 1fr 1fr;
  grid-template-rows: 64px 1fr 80px;
  min-height: 100vh;
}
.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; }
.footer  { grid-area: footer; }
```

### Scroll Snap — Casos avançados
```css
/* Carrossel com peek (mostra parte do próximo) */
.peek-carousel {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  scroll-behavior: smooth;
  padding: 0 1.5rem; /* peek lateral */
  gap: 1rem;
  scrollbar-width: none;
  -webkit-overflow-scrolling: touch;
}
.peek-item {
  scroll-snap-align: start;
  flex-shrink: 0;
  width: calc(100% - 3rem); /* não ocupa 100% — mostra o próximo */
}
@media (min-width: 768px) { .peek-item { width: calc(50% - 1rem); } }
@media (min-width: 1024px) { .peek-item { width: calc(33.333% - 0.75rem); } }

/* Scroll snap com stop obrigatório */
.slider { scroll-snap-type: x mandatory; } /* sempre termina num item */
/* vs: */
.scroll { scroll-snap-type: x proximity; } /* snaps apenas próximo a um item */

/* Scroll padding para headers fixos */
html {
  scroll-padding-top: 80px; /* altura do header */
}
/* Por seção: */
.section { scroll-margin-top: 80px; }
```

### CSS Lógico Completo (RTL ready)
```css
/* Equivalências: físico → lógico */
/* margin-left/right → margin-inline-start/end */
/* margin-top/bottom  → margin-block-start/end */
/* padding-left/right → padding-inline-start/end */
/* border-left/right  → border-inline-start/end */
/* width               → inline-size */
/* height              → block-size */
/* left/right (pos)   → inset-inline-start/end */
/* top/bottom (pos)   → inset-block-start/end */
/* text-align: left   → text-align: start */

/* Exemplos práticos */
.card {
  padding-inline: 1.5rem;     /* padding left+right */
  padding-block:  1rem;       /* padding top+bottom */
  margin-block-end: 1.5rem;   /* margin-bottom */
  border-inline-start: 3px solid var(--color-primary); /* border-left */
}

.icon { margin-inline-end: 0.5rem; } /* margin-right em LTR, margin-left em RTL */

/* Shorthand */
.box {
  margin-inline: auto;     /* margin: 0 auto equivalente */
  padding-block: 2rem 1rem; /* padding-top: 2rem; padding-bottom: 1rem */
  inset: 0;                 /* top: 0; right: 0; bottom: 0; left: 0 */
  inset-inline: 1rem;       /* left: 1rem; right: 1rem */
}
```

---

## SISTEMA DE SOMBRAS AVANÇADO

```css
:root {
  /* Sistema de sombras com luz consistente (vem de cima-esquerda) */
  --shadow-xs:  0 1px 2px rgba(0,0,0,0.05);
  --shadow-sm:  0 1px 3px rgba(0,0,0,0.08), 0 1px 2px rgba(0,0,0,0.04);
  --shadow-md:  0 4px 6px rgba(0,0,0,0.06),  0 2px 4px rgba(0,0,0,0.04);
  --shadow-lg:  0 10px 15px rgba(0,0,0,0.07), 0 4px 6px rgba(0,0,0,0.04);
  --shadow-xl:  0 20px 25px rgba(0,0,0,0.08), 0 8px 10px rgba(0,0,0,0.04);
  --shadow-2xl: 0 25px 50px rgba(0,0,0,0.12);

  /* Sombras coloridas (marca) */
  --shadow-primary-sm: 0 4px 14px rgba(99,102,241,0.25);
  --shadow-primary-lg: 0 12px 30px rgba(99,102,241,0.35);

  /* Sombras semânticas */
  --shadow-success: 0 4px 14px rgba(34,197,94,0.25);
  --shadow-warning: 0 4px 14px rgba(245,158,11,0.25);
  --shadow-error:   0 4px 14px rgba(239,68,68,0.25);

  /* Inner shadows */
  --shadow-inner:       inset 0 2px 4px rgba(0,0,0,0.06);
  --shadow-inner-focus: inset 0 0 0 2px var(--color-primary);

  /* Dark mode — sombras mais escuras */
  /* (redefinir em [data-theme="dark"]) */
}

[data-theme="dark"] {
  --shadow-sm: 0 1px 3px rgba(0,0,0,0.4),  0 1px 2px rgba(0,0,0,0.2);
  --shadow-md: 0 4px 6px rgba(0,0,0,0.4),  0 2px 4px rgba(0,0,0,0.2);
  --shadow-lg: 0 10px 15px rgba(0,0,0,0.5), 0 4px 6px rgba(0,0,0,0.3);
  /* No dark mode, complementar sombras com borders */
}

/* Sombra que muda com hover (sistema) */
.card {
  box-shadow: var(--shadow-sm);
  transition: box-shadow var(--motion-base) var(--ease-smooth),
              transform var(--motion-base) var(--ease-smooth);
}
.card:hover {
  box-shadow: var(--shadow-lg);
  transform: translateY(-2px);
}

/* Sombra de texto (apenas quando necessário) */
.text-on-image {
  text-shadow: 0 1px 4px rgba(0,0,0,0.4);
}

/* Drop shadow em elementos SVG/PNG com transparência */
.logo-image {
  filter: drop-shadow(0 2px 8px rgba(0,0,0,0.12));
}
```

---

## TIPOGRAFIA AVANÇADA

### Variable Fonts — Sistema Completo
```css
/* Carregar font variável */
@font-face {
  font-family: 'Inter Variable';
  src: url('/fonts/InterVariable.woff2') format('woff2 supports variations'),
       url('/fonts/InterVariable.woff2') format('woff2');
  font-weight: 100 900;
  font-style: normal;
  font-display: swap;
  font-named-instance: 'Regular';
}

/* Usar axes disponíveis */
.heading { font-variation-settings: 'wght' 700, 'wdth' 100; }
.compact { font-variation-settings: 'wght' 500, 'wdth' 75; }
.display { font-variation-settings: 'wght' 800, 'GRAD' 150; }

/* Animar font weight com variable fonts */
@keyframes weight-pulse {
  0%, 100% { font-variation-settings: 'wght' 400; }
  50%       { font-variation-settings: 'wght' 700; }
}
.loading-text { animation: weight-pulse 1.5s ease-in-out infinite; }
```

### OpenType Features
```css
/* Números em tabelas — mesmo width */
.data-cell   { font-variant-numeric: tabular-nums; font-feature-settings: 'tnum' 1; }

/* Frações */
.fraction    { font-variant-numeric: diagonal-fractions; font-feature-settings: 'frac' 1; }

/* Ordinals (1ª, 2º) */
.ordinal     { font-variant-numeric: ordinal; font-feature-settings: 'ordn' 1; }

/* Small caps */
.label       { font-variant-caps: small-caps; font-feature-settings: 'smcp' 1; }

/* Ligatures */
.body-text   { font-variant-ligatures: common-ligatures; font-feature-settings: 'liga' 1, 'calt' 1; }

/* Kerning */
*            { font-kerning: auto; } /* default — manter */

/* Combinação ideal para texto de produto */
.product-ui {
  font-feature-settings: 'kern' 1, 'liga' 1, 'calt' 1, 'tnum' 0;
  font-kerning: auto;
  text-rendering: optimizeLegibility;
}

/* Números em interfaces */
.stat-number {
  font-variant-numeric: tabular-nums lining-nums;
  font-feature-settings: 'tnum' 1, 'lnum' 1;
}
```

### Tracking por Tamanho
```css
/* Tracking (letter-spacing) deve variar inversamente ao tamanho */
.text-hero  { letter-spacing: -0.04em; } /* ≥80px: muito comprimido */
.text-3xl   { letter-spacing: -0.03em; } /* 48-64px */
.text-2xl   { letter-spacing: -0.02em; } /* 32-48px */
.text-xl    { letter-spacing: -0.01em; } /* 24-32px */
.text-base  { letter-spacing: 0; }       /* 16-20px: neutro */
.text-sm    { letter-spacing: 0.01em; }  /* 14px: levemente espaçado */
.text-xs    { letter-spacing: 0.02em; }  /* 12px: mais espaçado */
.text-caps  { letter-spacing: 0.08em; }  /* CAIXA ALTA: sempre com tracking */

/* Line height adaptado ao uso */
.heading    { line-height: 1.1; }  /* títulos: comprimido */
.subheading { line-height: 1.3; }
.body       { line-height: 1.65; } /* corpo: confortável */
.ui-label   { line-height: 1.4; }  /* labels de UI */
.data       { line-height: 1.2; }  /* tabelas/dados */
.longform   { line-height: 1.75; } /* artigos longos */
```

### Measure (largura de leitura)
```css
/* Largura ideal de leitura */
.article  { max-width: 65ch; } /* 55-75ch é o ideal */
.ui-text  { max-width: 45ch; } /* interfaces: mais curto */
.caption  { max-width: 35ch; }

/* Evitar orfãos e viúvas */
.body-text {
  orphans: 3;  /* mín 3 linhas no início de coluna/página */
  widows: 3;   /* mín 3 linhas no fim de coluna/página */
}

/* Balance de linha (Chrome 114+) */
.headline { text-wrap: balance; }    /* distribui linhas uniformemente */
.caption  { text-wrap: pretty; }    /* evita orfãos automaticamente */
```
