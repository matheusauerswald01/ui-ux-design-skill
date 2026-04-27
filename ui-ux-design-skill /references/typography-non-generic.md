# Referência: Tipografia Não-Genérica — Sistema Completo

---

## PARES DE FONTES POR SEGMENTO

### Regra fundamental: nunca Inter + Inter
```
Display + Body = contraste de personalidade
Serif + Sans = clássico e moderno
Variable + Fixed = flexibilidade + estabilidade
```

### Fintech / SaaS B2B
```css
/* Opção 1 — Autoridade geométrica */
--font-display: "Neue Haas Grotesk Display", "Helvetica Neue", sans-serif;
--font-body:    "Neue Haas Grotesk Text", "Helvetica Neue", sans-serif;
/* Pairing: mesma família mas pesos e tamanhos muito distintos */

/* Opção 2 — Modern precision */
--font-display: "DM Sans", sans-serif;       /* títulos: weight 700 */
--font-body:    "DM Sans", sans-serif;       /* body: weight 400 */
--font-mono:    "DM Mono", monospace;        /* dados: weight 400 */

/* Opção 3 — Premium B2B */
--font-display: "Mona Sans", sans-serif;     /* variable — Githubs font */
--font-body:    "Hubot Sans", sans-serif;    /* companion font */

/* Alternativa Google Fonts gratuita: */
--font-display: "Space Grotesk", sans-serif;
--font-body:    "Inter", sans-serif;
--font-mono:    "JetBrains Mono", monospace;
```

### Agência / Criativo
```css
/* Opção 1 — Experimental bold */
--font-display: "Clash Display", sans-serif;   /* só display sizes */
--font-body:    "Cabinet Grotesk", sans-serif;

/* Opção 2 — Editorial dramático */
--font-display: "Satoshi", sans-serif;         /* pesos extremos: 900 */
--font-body:    "Satoshi", sans-serif;         /* pesos leves: 300 */

/* Opção 3 — Expressivo */
--font-display: "Syne", sans-serif;            /* variable, eixo width */
--font-body:    "General Sans", sans-serif;

/* Alternativa Google Fonts: */
--font-display: "Bebas Neue", sans-serif;      /* all-caps display */
--font-body:    "DM Sans", sans-serif;
```

### Editorial / Publicação / Blog
```css
/* Opção 1 — Luxo editorial */
--font-display: "Cormorant Garamond", serif;   /* italic para hero */
--font-body:    "Source Serif 4", serif;       /* long-form comfort */
--font-sans:    "Karla", sans-serif;           /* UI elements */

/* Opção 2 — Contemporâneo */
--font-display: "Fraunces", serif;             /* optical sizing */
--font-body:    "Lora", serif;

/* Opção 3 — Jornalístico */
--font-display: "Playfair Display", serif;     /* clássico */
--font-body:    "Libre Baskerville", serif;

/* Todos disponíveis no Google Fonts */
```

### Healthtech / Wellness
```css
/* Opção 1 — Acolhedor e claro */
--font-display: "Nunito", sans-serif;          /* rounded, friendly */
--font-body:    "Nunito Sans", sans-serif;

/* Opção 2 — Humanista suave */
--font-display: "Plus Jakarta Sans", sans-serif;
--font-body:    "Plus Jakarta Sans", sans-serif;

/* Opção 3 — Médico sério mas humano */
--font-display: "Instrument Sans", sans-serif;
--font-body:    "Instrument Sans", sans-serif;
```

### Luxury / Fashion
```css
/* Opção 1 — Alta costura */
--font-display: "Cormorant Garamond", serif;   /* ultra light italic */
--font-body:    "EB Garamond", serif;

/* Opção 2 — Contemporâneo luxo */
--font-display: "Editorial New", serif;        /* premium pago */
--font-body:    "Neue Haas Grotesk", sans-serif;

/* Alternativa: */
--font-display: "Libre Caslon Display", serif;
--font-body:    "Libre Caslon Text", serif;
```

### Edtech / Gamificação
```css
/* Opção 1 — Energético */
--font-display: "Poppins", sans-serif;         /* rounded, energetic */
--font-body:    "Nunito", sans-serif;

/* Opção 2 — Moderno amigável */
--font-display: "Outfit", sans-serif;
--font-body:    "Outfit", sans-serif;
```

---

## TIPOGRAFIA EDITORIAL — TÉCNICAS PREMIUM

### Título que ocupa o viewport
```css
/* Hero text que preenche 100% da largura */
.viewport-title {
  font-size: clamp(4rem, 15vw, 12rem);
  font-weight: 900;
  line-height: 0.9;
  letter-spacing: -0.04em;
  text-transform: uppercase;

  /* Opcional: texto que toca as bordas */
  width: 100%;
  text-align: center;
}

/* Técnica: font-size baseado na viewport para fill exato */
.fill-width-title {
  font-size: 14.5vw; /* ajustar por fonte */
  white-space: nowrap;
  overflow: hidden;
}
```

### Outlined text
```css
/* Texto vazado — só o outline */
.outlined-text {
  color: transparent;
  -webkit-text-stroke: 1px var(--color-text-primary);
  text-stroke: 1px var(--color-text-primary);
}

/* Combinação fill + outline para contraste */
.mixed-text {
  /* palavra 1: preenchida */
}
.mixed-text .outlined {
  color: transparent;
  -webkit-text-stroke: 2px currentColor;
}

/* Texto que vai de outline para fill com hover */
.hover-fill {
  color: transparent;
  -webkit-text-stroke: 1.5px var(--color-primary);
  background-clip: text;
  -webkit-background-clip: text;
  background-image: linear-gradient(var(--color-primary), var(--color-primary));
  background-size: 0% 100%;
  background-repeat: no-repeat;
  transition: background-size 0.4s ease, color 0.4s ease;
}
.hover-fill:hover {
  background-size: 100% 100%;
  color: white;
  -webkit-text-stroke: 0px;
}
```

### Imagem/vídeo dentro do texto
```css
/* Imagem dentro das letras */
.image-in-text {
  font-size: clamp(4rem, 12vw, 8rem);
  font-weight: 900;
  background-image: url("/hero-texture.jpg");
  background-size: cover;
  background-position: center;
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  color: transparent;
}

/* Vídeo dentro do texto */
.video-text-wrapper {
  position: relative;
  display: inline-block;
}
.video-text {
  mix-blend-mode: multiply; /* ou screen dependendo do fundo */
  font-size: clamp(5rem, 15vw, 10rem);
  font-weight: 900;
  color: #000;
  position: relative;
  z-index: 2;
}
/* vídeo absoluto atrás, fica visível onde o texto está */
```

### Kinetic typography
```js
// Ticker de palavras
function WordTicker({ words }) {
  const [index, setIndex] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => setIndex(i => (i + 1) % words.length), 2000);
    return () => clearInterval(interval);
  }, [words.length]);

  return (
    <div className="ticker-wrap" aria-live="polite" aria-atomic="true">
      <AnimatePresence mode="wait">
        <motion.span
          key={index}
          initial={{ y: "100%", opacity: 0 }}
          animate={{ y: "0%", opacity: 1 }}
          exit={{ y: "-100%", opacity: 0 }}
          transition={{ duration: 0.4, ease: [0.16, 1, 0.3, 1] }}
          className="ticker-word"
          style={{ display: "block", overflow: "hidden" }}
        >
          {words[index]}
        </motion.span>
      </AnimatePresence>
    </div>
  );
}

// Palavras que se arranjam
function WordArrange({ words }) {
  return (
    <div style={{ display: "flex", flexWrap: "wrap", gap: "0.25em" }}>
      {words.map((word, i) => (
        <motion.span
          key={word}
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ delay: i * 0.06, duration: 0.4, ease: [0.16, 1, 0.3, 1] }}
        >
          {word}
        </motion.span>
      ))}
    </div>
  );
}
```

---

## VARIABLE FONTS — TÉCNICAS AVANÇADAS

```css
/* Peso que muda com scroll usando Scroll-Driven Animations */
@keyframes weight-on-scroll {
  from { font-variation-settings: "wght" 300; }
  to   { font-variation-settings: "wght" 800; }
}

.scroll-weight {
  animation: weight-on-scroll linear;
  animation-timeline: scroll(root block);
  animation-range: 0% 50%;
}

/* Hover que altera múltiplos axes */
.variable-hover {
  font-variation-settings: "wght" 400, "wdth" 100;
  transition: font-variation-settings 0.3s ease;
}
.variable-hover:hover {
  font-variation-settings: "wght" 700, "wdth" 110;
}

/* Grade axis para dark mode — menos contraste retinal */
:root {
  --font-grade: 0;
}
@media (prefers-color-scheme: dark) {
  :root { --font-grade: -25; } /* reduz densidade de tinta */
}
body {
  font-variation-settings: "GRAD" var(--font-grade);
}

/* GSAP animando axes */
gsap.to(".title", {
  fontVariationSettings: "'wght' 800, 'wdth' 125",
  duration: 1.2,
  ease: "power2.out",
  scrollTrigger: {
    trigger: ".title",
    start: "top 80%",
    scrub: 1,
  }
});
```

---

## FONT LOADING — SEM FOUT/FOIT

```css
/* Strategy 1: font-display: optional (melhor para performance) */
@font-face {
  font-family: "Inter Variable";
  src: url("/fonts/Inter.woff2") format("woff2 supports variations");
  font-weight: 100 900;
  font-display: optional; /* não mostra fallback se font carrega rápido */
}

/* Strategy 2: size-adjust para fallback perfeito */
@font-face {
  font-family: "Inter Fallback";
  src: local("Arial");
  ascent-override: 90.49%;
  descent-override: 22.48%;
  line-gap-override: 0%;
  size-adjust: 107.06%;
  font-display: swap;
}

/* Usar fallback com mesmo nome para troca imperceptível */
body {
  font-family: "Inter Variable", "Inter Fallback", sans-serif;
}
```

```js
// Font Loading API — controle preciso
async function loadFonts() {
  await document.fonts.ready;

  const interLoaded = await document.fonts.load("700 1rem 'Inter Variable'");
  if (interLoaded.length > 0) {
    document.documentElement.classList.add("fonts-loaded");
  }
}

// Usar classe para evitar FOIT:
// body { font-family: system-ui; } /* fallback inicial */
// .fonts-loaded body { font-family: "Inter Variable", system-ui; }
```

---

## MICRO-TIPOGRAFIA PREMIUM

```css
/* Hanging punctuation */
.body-text {
  hanging-punctuation: first last;
}

/* Optical margin alignment */
.headline {
  text-align: left;
  /* Recuar levemente "W" "V" "Y" que visualmente parecem avançados */
  padding-left: 0.02em;
}

/* Hifenização automática (não usar em títulos!) */
.longform-body {
  hyphens: auto;
  hyphenate-limit-chars: 6 3 3; /* mínimo 6 chars, 3 antes, 3 depois */
  hyphenate-limit-lines: 2;     /* máximo 2 hífens seguidos */
  overflow-wrap: break-word;
}

/* Balance de linha — evita últimas linhas com 1-2 palavras */
h1, h2, h3 {
  text-wrap: balance;   /* distribui linhas uniformemente */
}
.caption, .subtitle {
  text-wrap: pretty;    /* evita órfãs sem forçar balance */
}

/* Aspas tipográficas corretas */
:lang(pt-BR) {
  quotes: "\201C" "\201D" "\2018" "\2019";
}
q::before { content: open-quote; }
q::after  { content: close-quote; }

/* Números estilo old-style para texto corrido */
.body-numbers {
  font-variant-numeric: oldstyle-nums proportional-nums;
}
/* Tabular para tabelas e dados */
.data-numbers {
  font-variant-numeric: lining-nums tabular-nums;
}

/* Espaçamento tracking correto por tamanho */
.display-xl  { font-size: 5rem;    letter-spacing: -0.04em; }
.display-lg  { font-size: 3rem;    letter-spacing: -0.03em; }
.heading-xl  { font-size: 2rem;    letter-spacing: -0.02em; }
.heading-lg  { font-size: 1.5rem;  letter-spacing: -0.01em; }
.body        { font-size: 1rem;    letter-spacing: 0; }
.label       { font-size: 0.875rem; letter-spacing: 0.01em; }
.caps        { font-size: 0.75rem;  letter-spacing: 0.08em; text-transform: uppercase; }
```

---

## ESCALA TIPOGRÁFICA MODULAR

```css
/* Escala com razão musical perfeita (4ª): 1.333 */
:root {
  --ratio: 1.333;
  --base: 1rem;  /* 16px */

  --text-xs:    calc(var(--base) / var(--ratio) / var(--ratio));  /* ~9px */
  --text-sm:    calc(var(--base) / var(--ratio));                  /* ~12px */
  --text-base:  var(--base);                                       /* 16px */
  --text-lg:    calc(var(--base) * var(--ratio));                  /* ~21px */
  --text-xl:    calc(var(--base) * var(--ratio) * var(--ratio));   /* ~28px */
  --text-2xl:   calc(var(--base) * pow(var(--ratio), 3));          /* ~38px */
  --text-3xl:   calc(var(--base) * pow(var(--ratio), 4));          /* ~50px */
  --text-hero:  calc(var(--base) * pow(var(--ratio), 6));          /* ~89px */
}

/* Versão fluida com clamp */
:root {
  --text-sm:   clamp(0.8rem,  1.5vw + 0.5rem, 1rem);
  --text-base: clamp(1rem,    2vw + 0.5rem,    1.25rem);
  --text-lg:   clamp(1.2rem,  2.5vw + 0.5rem,  1.5rem);
  --text-xl:   clamp(1.5rem,  3.5vw + 0.5rem,  2.5rem);
  --text-2xl:  clamp(2rem,    5vw + 0.5rem,    3.5rem);
  --text-hero: clamp(3rem,    8vw + 1rem,      8rem);
}
```

---

## RITMO VERTICAL — BASELINE GRID

```css
/* Sistema baseado em 8px */
:root {
  --baseline: 8px;
  --leading: calc(var(--baseline) * 3); /* 24px para body */
}

/* Todos os espaçamentos são múltiplos de 8px */
.section   { padding-block: calc(var(--baseline) * 12); } /* 96px */
.card      { padding: calc(var(--baseline) * 3); }         /* 24px */
.btn       { padding-block: calc(var(--baseline) * 1.5); } /* 12px */
.input     { padding-block: calc(var(--baseline) * 1.5); } /* 12px */

/* Cap-height alignment óptico */
/* Alinhar texto pelo cap-height, não pelo bounding box */
.hero-text {
  /* Inter tem cap-height ≈ 0.727 */
  /* Se padding desejado é 24px e font-size é 48px: */
  /* Ajuste = (1 - 0.727) * 48 / 2 = 6.55px */
  padding-top: calc(24px - 6.55px); /* 17.45px */
}
```
