# Integração com Figma Tokens

## Formatos Suportados

### 1. Token Studio (JSON)
```json
{
  "global": {
    "color": {
      "brand": {
        "primary": { "value": "#6366f1", "type": "color" },
        "secondary": { "value": "#8b5cf6", "type": "color" }
      },
      "neutral": {
        "100": { "value": "#f5f5f5", "type": "color" },
        "900": { "value": "#171717", "type": "color" }
      }
    },
    "spacing": {
      "4":  { "value": "16px", "type": "dimension" },
      "8":  { "value": "32px", "type": "dimension" },
      "16": { "value": "64px", "type": "dimension" }
    },
    "typography": {
      "heading": {
        "fontFamily": { "value": "Inter", "type": "fontFamily" },
        "fontWeight": { "value": "700", "type": "fontWeight" }
      }
    },
    "borderRadius": {
      "sm":   { "value": "4px",     "type": "dimension" },
      "md":   { "value": "8px",     "type": "dimension" },
      "lg":   { "value": "16px",    "type": "dimension" },
      "full": { "value": "9999px",  "type": "dimension" }
    }
  }
}
```

### 2. W3C Design Tokens Format
```json
{
  "color": {
    "primary": { "$value": "#6366f1", "$type": "color" }
  }
}
```

---

## Protocolo de Importação

Ao receber tokens do Figma, seguir estas etapas:

### Passo 1 — Identificar grupos
```
□ Cores (brand, neutral, semantic, gradient)
□ Tipografia (families, sizes, weights, line-heights)
□ Espaçamento (scale ou named)
□ Raios de borda
□ Sombras
□ Motion (durations, easings)
□ Z-index
□ Breakpoints
```

### Passo 2 — Converter para CSS Custom Properties
```css
/* Raw tokens — valores primitivos */
:root {
  /* Cores primitivas */
  --_color-indigo-500: #6366f1;
  --_color-indigo-600: #4f46e5;
  --_color-violet-500: #8b5cf6;
  --_color-neutral-50: #fafafa;
  --_color-neutral-900: #171717;

  /* Tipografia primitiva */
  --_font-sans: "Inter", system-ui, sans-serif;
  --_font-serif: "Fraunces", Georgia, serif;

  /* Espaçamento primitivo */
  --_space-1: 4px;
  --_space-2: 8px;
  --_space-4: 16px;
  --_space-8: 32px;
  --_space-16: 64px;
}

/* Tokens semânticos — significado de uso */
:root {
  /* Cores semânticas */
  --color-bg:             var(--_color-neutral-900);
  --color-surface:        #1a1a1a;
  --color-border:         rgba(255,255,255,0.08);
  --color-primary:        var(--_color-indigo-500);
  --color-primary-hover:  var(--_color-indigo-600);
  --color-secondary:      var(--_color-violet-500);

  --color-text:           rgba(255,255,255,0.95);
  --color-text-muted:     rgba(255,255,255,0.50);

  --color-success: #22c55e;
  --color-warning: #f59e0b;
  --color-error:   #ef4444;

  /* Tipografia semântica */
  --font-heading: var(--_font-sans);
  --font-body:    var(--_font-sans);
  --font-mono:    "JetBrains Mono", monospace;

  /* Espaçamento semântico */
  --space-xs:  var(--_space-1);
  --space-sm:  var(--_space-2);
  --space-md:  var(--_space-4);
  --space-lg:  var(--_space-8);
  --space-xl:  var(--_space-16);

  /* Raios */
  --radius-sm:   4px;
  --radius-md:   8px;
  --radius-lg:   16px;
  --radius-full: 9999px;
}
```

### Passo 3 — Aliases de componente
```css
/* Tokens específicos de componente */
:root {
  /* Button */
  --btn-primary-bg:     var(--color-primary);
  --btn-primary-text:   #ffffff;
  --btn-primary-hover:  var(--color-primary-hover);
  --btn-radius:         var(--radius-md);
  --btn-padding:        var(--_space-2) var(--_space-4);

  /* Card */
  --card-bg:       var(--color-surface);
  --card-border:   var(--color-border);
  --card-radius:   var(--radius-lg);
  --card-padding:  var(--_space-4);
  --card-shadow:   0 4px 24px rgba(0,0,0,0.3);

  /* Input */
  --input-bg:        rgba(255,255,255,0.05);
  --input-border:    var(--color-border);
  --input-focus:     var(--color-primary);
  --input-radius:    var(--radius-md);
  --input-padding:   var(--_space-2) var(--_space-4);
}
```

### Passo 4 — Validar contraste
```js
// Validação automática de contraste (WCAG)
function getRelativeLuminance(hex) {
  const rgb = hex.match(/\w\w/g).map(h => parseInt(h, 16) / 255);
  return rgb.map(c => c <= 0.03928 ? c / 12.92 : Math.pow((c + 0.055) / 1.055, 2.4))
    .reduce((acc, c, i) => acc + c * [0.2126, 0.7152, 0.0722][i], 0);
}

function getContrastRatio(hex1, hex2) {
  const l1 = getRelativeLuminance(hex1);
  const l2 = getRelativeLuminance(hex2);
  const lighter = Math.max(l1, l2);
  const darker  = Math.min(l1, l2);
  return (lighter + 0.05) / (darker + 0.05);
}

// Uso:
// getContrastRatio("#6366f1", "#ffffff") → 4.6 ✅ (WCAG AA)
// getContrastRatio("#6366f1", "#0a0a0a") → 3.2 ❌ (falha AA para texto normal)
```

### Passo 5 — Documentar
```css
/**
 * SISTEMA DE TOKENS — [Nome do Projeto]
 * Fonte: Figma → [nome do arquivo]
 * Última sync: [data]
 *
 * Hierarquia:
 * --_*         Primitivos (não usar diretamente)
 * --color-*    Semânticos de cor
 * --font-*     Semânticos de tipografia
 * --space-*    Semânticos de espaçamento
 * --[comp]-*   Tokens de componente
 *
 * Para sincronizar com Figma: npx token-transformer tokens.json output.css
 */
```

---

## Ferramentas de Sync Figma → Código

```bash
# Token Studio (plugin Figma + CLI)
npm install -g token-transformer
token-transformer tokens.json output.json global,light --expandTypography

# Style Dictionary (Amazon)
npm install -g style-dictionary
style-dictionary build --config sd.config.json

# Theo (Salesforce)
npm install -g theo
theo tokens.yml --transform web --format custom-properties
```

---

## Exemplo Completo — Design System a partir de Tokens

```jsx
// design-system/tokens.ts
export const tokens = {
  color: {
    primary:    "var(--color-primary)",
    secondary:  "var(--color-secondary)",
    bg:         "var(--color-bg)",
    surface:    "var(--color-surface)",
    border:     "var(--color-border)",
    text:       "var(--color-text)",
    textMuted:  "var(--color-text-muted)",
    success:    "var(--color-success)",
    warning:    "var(--color-warning)",
    error:      "var(--color-error)",
  },
  space: {
    xs: "var(--space-xs)",
    sm: "var(--space-sm)",
    md: "var(--space-md)",
    lg: "var(--space-lg)",
    xl: "var(--space-xl)",
  },
  radius: {
    sm:   "var(--radius-sm)",
    md:   "var(--radius-md)",
    lg:   "var(--radius-lg)",
    full: "var(--radius-full)",
  },
} as const;

// Uso nos componentes
const Button = styled.button`
  background:    ${tokens.color.primary};
  padding:       ${tokens.space.sm} ${tokens.space.md};
  border-radius: ${tokens.radius.md};
`;
```
