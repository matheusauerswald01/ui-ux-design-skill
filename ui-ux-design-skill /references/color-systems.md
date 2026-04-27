# Sistemas de Cores — Referência

## Paletas Profissionais por Categoria

### Dark Luxury (moda, joias, premium)
```css
:root {
  --bg:        #08070a;
  --surface:   #110f14;
  --border:    rgba(255,255,255,0.06);
  --primary:   #c9a96e;  /* gold */
  --text:      rgba(255,255,255,0.90);
  --text-sub:  rgba(255,255,255,0.45);
}
```

### Tech Dark (SaaS, apps, ferramentas)
```css
:root {
  --bg:        #0d0d0d;
  --surface:   #161616;
  --border:    rgba(255,255,255,0.08);
  --primary:   #6366f1;  /* indigo */
  --accent:    #22d3ee;  /* cyan */
  --text:      rgba(255,255,255,0.92);
  --text-sub:  rgba(255,255,255,0.50);
}
```

### Organic Light (bem-estar, natureza, sustentável)
```css
:root {
  --bg:        #f7f4ef;
  --surface:   #eeebe4;
  --border:    rgba(0,0,0,0.07);
  --primary:   #3d6b4f;  /* forest green */
  --accent:    #c8a45a;  /* warm gold */
  --text:      rgba(0,0,0,0.85);
  --text-sub:  rgba(0,0,0,0.45);
}
```

### Bold Agency (criativo, digital, branding)
```css
:root {
  --bg:        #f2f0ec;
  --surface:   #e8e4de;
  --border:    rgba(0,0,0,0.08);
  --primary:   #ff3c2f;  /* red */
  --text:      #1a1a1a;
  --text-sub:  rgba(26,26,26,0.50);
}
```

### Midnight Blue (financeiro, corporativo moderno)
```css
:root {
  --bg:        #020817;
  --surface:   #0f172a;
  --border:    rgba(148,163,184,0.1);
  --primary:   #3b82f6;
  --accent:    #8b5cf6;
  --text:      rgba(248,250,252,0.95);
  --text-sub:  rgba(148,163,184,0.7);
}
```

## Gradientes Premium

```css
/* Mesh gradient — muito usado em 2024/2025 */
.mesh-bg {
  background:
    radial-gradient(at 40% 20%, hsla(228,100%,74%,0.3) 0px, transparent 50%),
    radial-gradient(at 80% 0%,  hsla(189,100%,56%,0.2) 0px, transparent 50%),
    radial-gradient(at 0% 50%,  hsla(355,85%,93%,0.2) 0px, transparent 50%),
    radial-gradient(at 80% 50%, hsla(340,100%,76%,0.2) 0px, transparent 50%),
    radial-gradient(at 0% 100%, hsla(269,100%,77%,0.2) 0px, transparent 50%);
}

/* Text gradient */
.gradient-text {
  background: linear-gradient(135deg, #fff 0%, rgba(255,255,255,0.4) 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

/* Border gradient */
.gradient-border {
  position: relative;
  border-radius: 12px;
  background: var(--surface);
}
.gradient-border::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  padding: 1px;
  background: linear-gradient(135deg, rgba(255,255,255,0.2), rgba(255,255,255,0.0));
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  mask-composite: exclude;
  pointer-events: none;
}
```

## Uso Estratégico de Cor

### Regra 60-30-10
- **60%** — Cor de fundo (neutro)
- **30%** — Cor secundária (superfície, cards)
- **10%** — Cor de destaque (CTAs, ícones, hover states)

### Cor como Narrativa
- **Azul** → Confiança, tecnologia, profissionalismo
- **Verde** → Crescimento, sustentabilidade, saúde
- **Vermelho** → Urgência, energia, paixão
- **Amarelo/Ouro** → Luxo, otimismo, atenção
- **Roxo** → Criatividade, luxo acessível, inovação
- **Preto/Branco** → Elegância, clareza, sofisticação

### Modo Escuro → Claro
Nunca inverta apenas as cores. Recalibrar:
- Sombras ficam mais suaves em dark mode
- Saturação levemente reduzida em superfícies dark
- Em light mode, adicionar profundidade com sombras sutis
