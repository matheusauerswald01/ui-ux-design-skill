# MODES — Guia de Modos Especiais v3.0

## Como ativar cada modo

| Palavras-chave | Modo ativado |
|---|---|
| "revisar", "analisar", "dar feedback", "critique" | Design Critique |
| "evolua", "melhore", "modernize", "refaça" | Design Evolution |
| "estilo como X", "inspirado em", "quero algo tipo" | Visual Reference → Tokens |
| "não sei qual componente usar", descreve problema sem nome | Component Decision |

---

## Modo Design Critique — Template Completo

```
DESIGN CRITIQUE — [Nome]
Data: [data]
Avaliador: Claude UI/UX Design Specialist v3.0

════════════════════════════════════

ANÁLISE INICIAL
Tipo: [Landing Page / Dashboard / Componente / App Mobile / ...]
Segmento: [Fintech / SaaS / E-commerce / ...]
Estado: [MVP / Produção / Redesign / ...]

════════════════════════════════════

1. IDENTIDADE & ESTRATÉGIA (0-10)
Score: __/10

□ Personalidade visual clara? [Sim/Não + justificativa]
□ Coerência com o segmento? [Sim/Não + justificativa]
□ Hierarquia guia o olho? [Sim/Não + justificativa]
□ Copy integrada ao design? [Sim/Não + justificativa]

════════════════════════════════════

2. PROBLEMAS CRÍTICOS ⚠️
(Quebram a experiência — corrigir imediatamente)

[PROBLEMA 1]
→ Descrição: ...
→ Impacto no usuário: ...
→ Solução exata: [código ou instrução específica]
→ Prioridade: 🔴 CRÍTICO

════════════════════════════════════

3. PROBLEMAS DE QUALIDADE 🟡
(Reduzem o nível — corrigir em breve)

[PROBLEMA 1]
→ Descrição: ...
→ Por que importa: ...
→ Solução: ...

════════════════════════════════════

4. OPORTUNIDADES ✨
(Elevariam significativamente o resultado)

[OPORTUNIDADE 1]
→ O que adicionar/mudar: ...
→ Impacto esperado: ...
→ Complexidade: Baixa/Média/Alta

════════════════════════════════════

5. CÓDIGO REFATORADO
[Código completo com comentários inline explicando cada mudança]

════════════════════════════════════

SCORES FINAIS
┌─────────────────────┬──────────┬────────────┐
│ Dimensão            │ Atual    │ Potencial  │
├─────────────────────┼──────────┼────────────┤
│ Identidade          │   /10    │    /10     │
│ Responsividade      │   /10    │    /10     │
│ Acessibilidade      │   /10    │    /10     │
│ Performance         │   /10    │    /10     │
│ Copy & UX           │   /10    │    /10     │
│ Motion & Polimento  │   /10    │    /10     │
├─────────────────────┼──────────┼────────────┤
│ TOTAL               │   /60    │    /60     │
└─────────────────────┴──────────┴────────────┘
```

---

## Modo Design Evolution — 3 Direções

Quando ativado, apresentar sempre as 3 direções:

### Direção 1: Refinamento
"Mesmo caminho, melhor execução"
- Mantém layout, paleta e tipografia atuais
- Corrige inconsistências e imperfeições
- Aplica sistema de tokens
- Polimento de estados e transições
- Tempo estimado: Baixo

### Direção 2: Elevação
"Mesma identidade, nível premium"
- Mantém identidade visual central
- Upgrade de tipografia (variable fonts, hierarquia mais forte)
- Sistema de design mais elaborado
- Animações mais refinadas
- Dark mode/light mode system
- Tempo estimado: Médio

### Direção 3: Transformação
"Nova direção, mesma essência"
- Reinterpreta a identidade com nova linguagem visual
- Novo sistema de cores e tipografia
- Layout e estrutura repensados
- Motion design protagonista
- Experiência repensada do zero com a mesma intenção
- Tempo estimado: Alto

---

## Modo Visual Reference → Tokens

Referências mapeadas e seus tokens extraídos:

### Linear.app
```css
:root {
  --color-bg:       #0f0f0f;
  --color-surface:  #1c1c1c;
  --color-border:   rgba(255,255,255,0.06);
  --color-primary:  #5c6bc0;
  --font-heading:   'Inter Variable';
  --font-mono:      'Geist Mono';
  --motion-fast:    120ms;
  --ease-primary:   cubic-bezier(0.2,0,0,1);
  --shadow-card:    0 0 0 1px rgba(255,255,255,0.06);
}
```

### Stripe
```css
:root {
  --color-bg:       #ffffff;
  --color-surface:  #f6f9fc;
  --color-border:   #e3e8f0;
  --color-primary:  #635bff;
  --font-heading:   'Sohne', 'Inter';
  --shadow-card:    0 2px 5px rgba(0,0,0,0.08), 0 0 0 1px rgba(0,0,0,0.04);
  --radius-card:    8px;
  --motion-base:    200ms;
}
```

### Vercel
```css
:root {
  --color-bg:       #000000;
  --color-surface:  #111111;
  --color-border:   rgba(255,255,255,0.10);
  --color-primary:  #ffffff;
  --color-accent:   #0070f3;
  --font-heading:   'Geist';
  --shadow-card:    0 0 0 1px rgba(255,255,255,0.08);
  --radius-sm:      6px;
  --radius-md:      12px;
}
```

### Airbnb
```css
:root {
  --color-bg:       #ffffff;
  --color-surface:  #f7f7f7;
  --color-border:   #dddddd;
  --color-primary:  #ff385c;
  --color-dark:     #222222;
  --font-heading:   'Cereal', 'Circular';
  --radius-card:    12px;
  --shadow-card:    0 6px 20px rgba(0,0,0,0.12);
}
```

### Notion
```css
:root {
  --color-bg:       #ffffff;
  --color-surface:  #f7f6f3;
  --color-border:   rgba(55,53,47,0.09);
  --color-text:     rgb(55,53,47);
  --font-body:      'Segoe UI', -apple-system, sans-serif;
  --radius-sm:      3px;
  --radius-md:      6px;
  --shadow-card:    rgba(15,15,15,0.05) 0px 0px 0px 1px;
}
```

---

## Modo Component Decision — Arvore Completa

```
PROBLEMA DO USUÁRIO → COMPONENTE IDEAL

"Preciso mostrar informação extra sobre um elemento"
├── Ao hover, curta → Tooltip
├── Ao hover, longa → Popover
├── Ao clicar, ações → Dropdown Menu
└── Ao clicar, formulário → Modal pequeno

"Preciso de um painel lateral"
├── Permanente + desktop → Sidebar fixa
├── Temporário + mobile → Drawer (slide-in)
├── Overlay + qualquer → Sheet (bottom sheet mobile)
└── Ajustes rápidos → Panel slide-over

"Preciso mostrar conteúdo oculto"
├── FAQ / detalhes → Accordion
├── Conteúdo longo + navegação → Tabs
├── Texto colapsável → Expandable text
└── Código → Code block com copy

"Preciso de feedback para o usuário"
├── Ação concluída (3-5s) → Toast
├── Ação em progresso → Progress bar inline
├── Estado global de alerta → Alert Banner
├── Confirmação de ação perigosa → Modal de confirmação
└── Erro em campo → Field error message

"Preciso mostrar dados"
├── Comparação categórica → Bar chart
├── Tendência temporal → Line/Area chart
├── Parte do todo → Donut/Pie chart
├── Distribuição → Histogram
├── Correlação → Scatter plot
├── Tabular + filtros → Data table
└── KPIs rápidos → Metric cards

"Preciso de navegação"
├── Seções da página → Scroll navigation
├── Seções distintas → Tabs
├── Hierarquia → Breadcrumbs
├── Passos lineares → Steps/Wizard
└── Múltiplas opções → Navigation menu
```
