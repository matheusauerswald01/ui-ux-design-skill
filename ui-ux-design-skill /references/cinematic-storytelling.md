# Referência: Design Cinematográfico — Storytelling Completo

## PRINCÍPIO FUNDADOR

> "Web design não é design gráfico com animação. É cinema interativo."
> O usuário é o espectador. O scroll é a linha do tempo. A seção é o frame. O produto é o herói.

---

## THREE-ACT STRUCTURE PARA LANDING PAGES

```
ATO 1 — SETUP (0-30% do scroll)
Objetivo: Estabelecer contexto, criar empatia, definir o problema
Elementos: Hero section + establishing shot + problema nomeado
Tom: Reconhecimento ("isso sou eu")
Cor: Fria/neutra — ainda não há esperança

  → Establishing shot: Hero com proposta em 3s
  → Nomear a dor do usuário com precisão cirúrgica
  → Apresentar o mundo atual (problemático)

ATO 2 — CONFRONTO (30-70% do scroll)
Objetivo: Apresentar a solução, construir prova, criar desejo
Elementos: Features, depoimentos, stats, demonstrações
Tom: Descoberta ("isso pode funcionar para mim")
Cor: Transição warm/cool para warm

  → A solução revelada progressivamente
  → Provas de cada claim (datos, depoimentos, cases)
  → O momento de revelation — o aha moment

ATO 3 — RESOLUÇÃO (70-100% do scroll)
Objetivo: Fechar a narrativa, criar urgência ética, convidar à ação
Elementos: Climax emocional + CTA + proof final
Tom: Transformação ("quero isso")
Cor: Warm/vibrante — o futuro está chegando

  → Transformação do usuário (antes → depois)
  → Climax emocional antes do CTA
  → CTA como convite, não comando
  → Post-credit scene (surpresa após conversão)
```

### Template de Color Script
```
SEÇÃO 1 (Hero):        oklch(20% 0.05 240)  → fundo azul-escuro frio, problema
SEÇÃO 2 (Problema):    oklch(15% 0.03 250)  → mais fundo, tensão máxima
SEÇÃO 3 (Revelação):   oklch(25% 0.10 260)  → levemente mais claro, esperança
SEÇÃO 4 (Features):    oklch(30% 0.08 230)  → neutro técnico, construir confiança
SEÇÃO 5 (Social proof):oklch(25% 0.12 200)  → teal, humanidade, comunidade
SEÇÃO 6 (Climax):      oklch(35% 0.15 60)   → warm amber, emoção máxima
SEÇÃO 7 (CTA):         oklch(40% 0.18 80)   → dourado/verde, resolução positiva
SEÇÃO 8 (Footer):      oklch(15% 0.04 240)  → retorna ao escuro, calmaria pós-clímax

/* Implementar com CSS variables mudando via ScrollTrigger */
```

### Implementação JavaScript
```js
function initColorScript() {
  const sections = [
    { el: ".hero",       bg: "oklch(20% 0.05 240)", text: "oklch(90% 0.02 240)" },
    { el: ".problem",    bg: "oklch(15% 0.03 250)", text: "oklch(88% 0.02 250)" },
    { el: ".reveal",     bg: "oklch(25% 0.10 260)", text: "oklch(92% 0.02 260)" },
    { el: ".features",   bg: "oklch(30% 0.08 230)", text: "oklch(90% 0.02 230)" },
    { el: ".proof",      bg: "oklch(25% 0.12 200)", text: "oklch(93% 0.03 200)" },
    { el: ".climax",     bg: "oklch(35% 0.15 60)",  text: "oklch(15% 0.08 60)"  },
    { el: ".cta-section",bg: "oklch(40% 0.18 80)",  text: "oklch(15% 0.10 80)"  },
    { el: ".footer",     bg: "oklch(12% 0.04 240)", text: "oklch(70% 0.02 240)" },
  ];

  sections.forEach(({ el, bg, text }) => {
    ScrollTrigger.create({
      trigger: el,
      start: "top 60%",
      end: "bottom 40%",
      onEnter: () => {
        gsap.to("body", {
          "--bg-color": bg,
          "--text-color": text,
          duration: 1.2,
          ease: "power2.inOut",
        });
      },
      onEnterBack: () => {
        gsap.to("body", {
          "--bg-color": bg,
          "--text-color": text,
          duration: 1.2,
          ease: "power2.inOut",
        });
      },
    });
  });
}
```

---

## HERO'S JOURNEY — USUÁRIO COMO HERÓI

```
MAPA NARRATIVO:

Mundo comum:       A vida do usuário ANTES (com o problema)
                   → Mostrar com empatia, não com julgamento

Chamado à aventura: A possibilidade de mudança
                   → Headline que nomeia o futuro possível

Recusa do chamado:  As objeções internas do usuário
                   → Tratar com microcopy e FAQs estratégicos

Mentor:            As provas e testemunhos que guiam
                   → Depoimentos, logos, stats — o mentor fala, não o herói

Travessia do limiar: Experimentar a solução (trial, demo)
                   → CTA de baixo comprometimento ("ver como funciona")

Testes e aliados:  O onboarding e primeiras vitórias
                   → Celebrar quick wins, progressão visível

Abordagem:         Aprofundamento no produto
                   → Features avançadas, integração total

Ordeal:            O momento de maior dúvida/frustração
                   → Onde o suporte e UX são críticos

Recompensa:        O primeiro resultado real
                   → Momento de peak-end, celebração

Caminho de volta:  Normalização do novo estado
                   → O produto virou parte da vida

Ressurreição:      Prova final de transformação
                   → Case study, review, depoimento do usuário

Elixir:            O usuário agora ajuda outros
                   → Referral, community, ambassador
```

---

## PACING — RITMO DE EDIÇÃO APLICADO AO SCROLL

```
CORTE RÁPIDO (animações <300ms, seções curtas):
→ Criar energia, urgência, excitação
→ Usar para: produtos de consumo, gaming, startups energéticas
→ Perigo: fadiga cognitiva se mantido muito tempo

CORTE MÉDIO (animações 400-600ms, seções normais):
→ Padrão confortável para maioria dos produtos
→ Profissional sem ser entediante

CORTE LENTO (animações 800ms+, seções longas pinadas):
→ Peso, importância, luxo, contemplação
→ Usar para: luxury, produtos complexos, storytelling emocional
→ Perigo: perder atenção de usuários impacientes

RITMO VARIADO (o segredo dos grandes filmes):
→ Rápido → Lento = Drama e peso no momento lento
→ Lento → Rápido = Energia e liberação na aceleração
→ Nunca manter o mesmo ritmo por mais de 3 seções seguidas

IMPLEMENTAÇÃO COM GSAP:
```
```js
// Sistema de pacing por segmento
const pacing = {
  energy:        { duration: 0.3, stagger: 0.04, ease: "power4.out" },
  professional:  { duration: 0.5, stagger: 0.08, ease: "power3.out" },
  luxury:        { duration: 1.0, stagger: 0.15, ease: "power2.inOut" },
  cinematic:     { duration: 1.4, stagger: 0.20, ease: "expo.out" },
  contemplative: { duration: 2.0, stagger: 0.30, ease: "power1.inOut" },
};

// Usar na timeline:
// gsap.from(el, pacing.luxury)
```

---

## LEITMOTIF VISUAL — ELEMENTO RECORRENTE

```
Conceito: Uma forma, cor ou padrão que aparece em momentos-chave,
criando coesão e reconhecimento sem o usuário perceber conscientemente.

Exemplos de leitmotif visual:

1. FORMA CIRCULAR
  → Hero: grande círculo de gradiente no background
  → Features: ícones dentro de círculos
  → Depoimentos: avatares circulares
  → CTA: botão com border-radius: 50% ou pill
  → Footer: logo circular
  Resultado: Unidade subliminar de todo o layout

2. LINHA DIAGONAL
  → Separadores de seção em diagonal (clip-path)
  → Backgrounds com linhas diagonais sutis
  → CTAs com ícone de seta diagonal
  → Imagens cortadas diagonalmente
  Cria: Dinamismo e movimento direcional

3. PADRÃO DE PONTOS
  → Background de dot grid no hero
  → Separador feito de pontos
  → Loading indicator de pontos pulsantes
  → Textura sutil em cards
  Cria: Consistência e marca recognizable

4. COR ACCENT ÚNICA
  → Uma única cor de acento usada APENAS em elementos narrativamente importantes
  → Não pode aparecer em elementos decorativos
  → Quando aparece, o usuário sabe que é importante
```

```css
/* Implementação com CSS */
:root {
  --leitmotif-color: oklch(65% 0.25 260); /* só para elementos narrativos */
  --leitmotif-shape: circle; /* forma que se repete */
  --leitmotif-size-unit: 8px; /* tudo é múltiplo deste valor */
}

/* Leitmotif de forma */
.hero-bg-circle { border-radius: 50%; }
.icon-wrapper   { border-radius: 50%; }
.avatar         { border-radius: 50%; }
.cta-btn        { border-radius: 9999px; }  /* pill é o extremo */

/* Leitmotif de cor — só em elementos de destaque */
.section-number { color: var(--leitmotif-color); }
.key-stat       { color: var(--leitmotif-color); }
.active-nav     { border-color: var(--leitmotif-color); }
.cta-primary    { background: var(--leitmotif-color); }
```

---

## ESTABLISHING SHOT — HERO SECTION PERFEITA

```
REGRA DAS 3 QUESTÕES (devem ser respondidas em 3 segundos):
1. Onde estou? → Categoria do produto/serviço
2. O que é isso? → Proposta de valor específica
3. Por que me importa? → Benefício para MIM

FÓRMULA DE HEADLINE QUE FUNCIONA:
[Resultado desejado] + [Sem/Para] + [Objeção comum]
"Entregue projetos no prazo, sem reuniões desnecessárias"
"Escreva melhor, mesmo sem ser escritor"
"Venda mais, sem precisar fazer cold call"

ESTRUTURA VISUAL DO ESTABLISHING SHOT:
- Fundo que define o mundo/atmosfera
- Uma única imagem/visual hero que mostra o "depois"
- Headline em menos de 8 palavras
- Subheadline em menos de 20 palavras
- 1-2 CTAs máximo (primário e secundário)
- Prova social imediata (logo de clientes, star rating, usuário count)
- Scroll indicator (sutil, não insistente)
```

---

## MATCH CUT — CONTINUIDADE VISUAL ENTRE SEÇÕES

```js
// Elemento que se transforma entre seções
function initMatchCut() {
  // Seção 1: circle que representa o problema
  // Seção 2: o mesmo circle vira o logo da solução
  const fromEl = document.querySelector(".problem-circle");
  const toEl   = document.querySelector(".solution-logo");

  const state = Flip.getState(fromEl);

  // Mover o elemento para a nova seção
  toEl.appendChild(fromEl);

  Flip.from(state, {
    duration: 1.2,
    ease: "power3.inOut",
    scale: true,
    onComplete: () => {
      // Transformar a forma também (circle → square)
      gsap.to(fromEl, { borderRadius: "0%", duration: 0.4 });
    }
  });
}

// MorphSVG match cut
gsap.to("#problem-icon path", {
  morphSVG: "#solution-icon path",
  duration: 1.5,
  ease: "power3.inOut",
  scrollTrigger: {
    trigger: ".transition-section",
    start: "top 50%",
  }
});
```

---

## ENVIRONMENTAL STORYTELLING

```
Detalhes que revelam personalidade sem dizer explicitamente:

CURSOR CUSTOMIZADO:
→ Agência criativa: cursor que deixa rastro de cor
→ Tech startup: cursor hexagonal com coordenadas
→ Luxury: cursor circular minimal com dot no centro
→ Playful: cursor que muda de emoji por contexto

LOADING SCREEN:
→ Não genérico: loading screen que já é conteúdo
→ Piada interna, dado curioso, visual surpreendente
→ Progresso que vai de 0 a 100 com personalidade
→ Humor que define o tom da marca antes de carregar

404 PAGE:
→ Oportunidade de mostrar personalidade
→ Humor, easter egg, jogo interativo, redirect criativo
→ Airbnb: imagem personalizada por localização
→ Mailchimp: fred o macaco em situações cômicas

FOOTER:
→ Não lixo: footer como último momento de storytelling
→ Frase que resume a missão de forma inesperada
→ Easter egg interativo, mensagem aos devs, curiosidade
→ Social links que refletem a voz da marca

ERROR MESSAGES:
→ "Algo deu errado" → genérico e frio
→ "Ops! Nossa equipe já foi notificada e está correndo para consertar" → humano
→ Erro com humor quando appropriado: "Esse formulário está com vergonha de ser enviado"

HOVER STATES:
→ Micro-animações que revelam personalidade
→ Cursor muda por tipo de conteúdo
→ Elementos que respondem com "emoção"

MICROCOPY EM PLACES INESPERADOS:
→ Placeholder de input: "Seu email mais bonito aqui"
→ Botão de submit: não "Enviar" mas "Contar minha história"
→ Loading: "Preparando algo especial para você"
→ Success: "Uhuuu! Deu certo! (fizemos uma dancinha aqui)"
```

---

## STORYBOARD PARA WEB — PROTOCOLO PRÉ-PRODUÇÃO

```
ANTES DE QUALQUER LINHA DE CÓDIGO:

FASE 1 — ROTEIRO (30min)
□ Definir o protagonista (persona detalhada)
□ Definir o problema que ele tem
□ Definir a transformação que o produto promove
□ Escrever a "sinopse" da landing page em 3 frases

FASE 2 — STORY BEATS (45min)
□ Listar 8-12 "cenas" principais (seções)
□ Para cada cena: emoção objetivo, mensagem principal, prova
□ Definir o pacing de cada cena (rápido/médio/lento)
□ Mapear onde está o climax

FASE 3 — COLOR SCRIPT (20min)
□ Escolher paleta inicial (fria/quente/neutra)
□ Mapear como a paleta evolui com o arc emocional
□ Definir o leitmotif visual

FASE 4 — STORYBOARD (60min)
□ Sketch rápido de cada seção (wireframe + movimento)
□ Anotar: o que entra? Como? O que sai? Velocidade?
□ Marcar onde estão as "cenas de respiro"
□ Identificar o shot de abertura e o de fechamento

FASE 5 — MOTION SCRIPT (30min)
□ Definir a linguagem de motion por segmento
□ Escolher ease curves e durations globais
□ Planejar 3 animações de assinatura (que definem a marca)
□ Listar easter eggs e momentos de deleite

TOTAL: ~3 horas antes de abrir qualquer editor
```

---

## SOUND DESIGN PARA WEB

### Sistema completo com Tone.js
```js
import * as Tone from "tone";

class WebSoundDesign {
  constructor() {
    this.initialized = false;
    this.muted = localStorage.getItem("sound") === "off";
  }

  // Inicializar apenas após interação do usuário (autoplay policy)
  async init() {
    if (this.initialized) return;
    await Tone.start();
    this.initialized = true;
    this.setupLeitmotif();
    this.setupAmbient();
  }

  // Leitmotif da marca — 3 notas que identificam a marca
  setupLeitmotif() {
    this.synth = new Tone.PolySynth(Tone.Synth, {
      oscillator: { type: "triangle" },
      envelope: { attack: 0.02, decay: 0.1, sustain: 0.3, release: 1.5 },
      volume: -20,
    }).toDestination();

    // Tema de 3 notas (ajustar por marca)
    this.leitmotif = ["C4", "E4", "G4"]; // acorde maior = positivo
    // this.leitmotif = ["A3", "C4", "E4"]; // acorde menor = emocional
    // this.leitmotif = ["C4", "F4", "G4"]; // suspenso = tech
  }

  // Áudio ambiente por seção
  setupAmbient() {
    this.reverb = new Tone.Reverb({ decay: 3, wet: 0.4 }).toDestination();
    this.filter = new Tone.Filter(800, "lowpass").connect(this.reverb);

    this.ambientNoise = new Tone.Noise("pink").connect(this.filter);
    this.ambientNoise.volume.value = -40;
  }

  // Earcon de ação
  playConfirm() {
    if (this.muted || !this.initialized) return;
    this.synth.triggerAttackRelease(this.leitmotif, "8n");
  }

  playError() {
    if (this.muted || !this.initialized) return;
    const errorSynth = new Tone.Synth({
      oscillator: { type: "sawtooth" },
      envelope: { attack: 0.005, decay: 0.3, sustain: 0, release: 0.1 },
      volume: -25,
    }).toDestination();
    errorSynth.triggerAttackRelease("A2", "16n");
    setTimeout(() => errorSynth.triggerAttackRelease("G2", "16n"), 150);
  }

  playSuccess() {
    if (this.muted || !this.initialized) return;
    const notes = ["C4", "E4", "G4", "C5"];
    notes.forEach((note, i) => {
      setTimeout(() => this.synth.triggerAttackRelease(note, "8n"), i * 100);
    });
  }

  // Scroll-linked audio — frequência do filtro muda com scroll
  updateWithScroll(progress) {
    if (this.muted || !this.initialized) return;
    const freq = 200 + progress * 2000; // 200Hz a 2200Hz
    this.filter.frequency.rampTo(freq, 0.3);
    const volume = -45 + progress * 15; // mais audível no final
    this.ambientNoise.volume.rampTo(volume, 0.5);
  }

  // Foley digital — sons que imitam ações físicas
  playHover() {
    if (this.muted || !this.initialized) return;
    const tine = new Tone.MetalSynth({ volume: -35 }).toDestination();
    tine.triggerAttackRelease("32n");
  }

  playSwipe(direction = "right") {
    if (this.muted || !this.initialized) return;
    const freq = direction === "right" ? [400, 600] : [600, 400];
    const sweep = new Tone.Synth({ volume: -30 }).toDestination();
    sweep.frequency.value = freq[0];
    sweep.triggerAttack(Tone.now());
    sweep.frequency.rampTo(freq[1], 0.15);
    sweep.triggerRelease(Tone.now() + 0.15);
  }

  toggle() {
    this.muted = !this.muted;
    localStorage.setItem("sound", this.muted ? "off" : "on");
    if (!this.muted) this.ambientNoise.start();
    else this.ambientNoise.stop();
  }
}

// Uso global
const sound = new WebSoundDesign();
document.addEventListener("click", () => sound.init(), { once: true });

// Integrar com eventos
document.querySelectorAll(".btn-primary").forEach(btn => {
  btn.addEventListener("click", () => sound.playConfirm());
  btn.addEventListener("mouseenter", () => sound.playHover());
});

// Scroll link
lenis.on("scroll", ({ progress }) => sound.updateWithScroll(progress));
```

### Controle de Som na UI
```jsx
function SoundToggle() {
  const [muted, setMuted] = useState(localStorage.getItem("sound") === "off");

  const toggle = () => {
    const newMuted = !muted;
    setMuted(newMuted);
    sound.toggle();
  };

  return (
    <button
      className="sound-toggle"
      onClick={toggle}
      aria-label={muted ? "Ativar sons" : "Desativar sons"}
      aria-pressed={!muted}
    >
      {muted ? <VolumeOff size={16} /> : <Volume2 size={16} />}
    </button>
  );
}
```

---

## COLOR GRADING PARA WEB

### CSS color grading por look cinematográfico
```css
/* Teal-Orange (blockbuster) */
.grade-teal-orange img {
  filter: saturate(1.2) hue-rotate(5deg);
}
.grade-teal-orange .hero {
  background: linear-gradient(
    135deg,
    oklch(30% 0.15 200) 0%,  /* teal escuro */
    oklch(15% 0.05 240) 50%, /* azul-preto */
    oklch(40% 0.20 50) 100%  /* amber */
  );
}

/* Wes Anderson */
.grade-wes {
  --color-bg: oklch(90% 0.08 60);   /* pastel cream */
  --color-primary: oklch(55% 0.20 15); /* dusty rose */
  --color-accent: oklch(60% 0.18 160); /* sage green */
  font-feature-settings: "kern" 1, "liga" 1;
}
.grade-wes img { filter: saturate(1.3) sepia(0.15) brightness(1.05); }

/* Film Noir */
.grade-noir {
  --color-bg: oklch(8% 0.01 240);   /* quase preto */
  --color-text: oklch(95% 0 0);     /* branco puro */
  --color-accent: oklch(65% 0.15 60); /* gold */
}
.grade-noir img { filter: contrast(1.4) saturate(0.3); }

/* Blade Runner */
.grade-blade-runner {
  --color-bg: oklch(8% 0.04 250);   /* azul-preto */
  --neon-orange: oklch(70% 0.25 50);
  --neon-blue: oklch(65% 0.20 220);
}
.grade-blade-runner img {
  filter: contrast(1.2) saturate(1.5) hue-rotate(-10deg) brightness(0.8);
}

/* The Matrix */
.grade-matrix {
  filter: sepia(0.3) hue-rotate(80deg) saturate(1.5);
}

/* Amelie */
.grade-amelie img {
  filter: saturate(1.4) sepia(0.1) hue-rotate(-15deg) brightness(1.1);
}

/* Bleach Bypass (Se7en/Fincher) */
.grade-bleach-bypass img {
  filter: saturate(0.6) contrast(1.5) brightness(0.95);
}
/* Adicionar grain pesado */
.grade-bleach-bypass::after { opacity: 0.07; }
```

### WebGL Color Grade Shader
```glsl
/* Fragment shader — Lift-Gamma-Gain cinematográfico */
uniform sampler2D uTexture;
uniform vec3 uLift;    /* shadows */
uniform vec3 uGamma;   /* midtones */
uniform vec3 uGain;    /* highlights */
uniform float uSaturation;
uniform float uContrast;
uniform vec2 uVignette; /* center, intensity */
varying vec2 vUv;

vec3 liftGammaGain(vec3 col, vec3 lift, vec3 gamma, vec3 gain) {
  col = col * (gain - lift) + lift;
  col = pow(max(col, 0.0), 1.0 / max(gamma, vec3(0.001)));
  return col;
}

vec3 saturation(vec3 col, float sat) {
  float lum = dot(col, vec3(0.2126, 0.7152, 0.0722));
  return mix(vec3(lum), col, sat);
}

float vignette(vec2 uv, float power) {
  uv = uv * 2.0 - 1.0;
  return 1.0 - dot(uv * power, uv * power);
}

void main() {
  vec4 tex = texture2D(uTexture, vUv);
  vec3 col = tex.rgb;

  /* Lift-Gamma-Gain */
  col = liftGammaGain(col, uLift, uGamma, uGain);

  /* Saturação */
  col = saturation(col, uSaturation);

  /* Contraste em S-curve */
  col = 0.5 + (col - 0.5) * uContrast;

  /* Vignette */
  col *= vignette(vUv, uVignette.y);

  gl_FragColor = vec4(col, tex.a);
}
```

---

## MISE EN SCÈNE PARA WEB

### Regra dos Terços em CSS Grid
```css
/* Grid baseado em regra dos terços */
.thirds-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;  /* 3 colunas iguais */
  grid-template-rows: 1fr 1fr 1fr;      /* 3 linhas iguais */
}

/* Elemento no "sweetspot" do terço inferior esquerdo */
.cta-sweetspot {
  grid-column: 1 / 2;
  grid-row: 3 / 4;
}

/* Phi (proporção áurea) em padding */
:root {
  --phi: 1.618;
  --space-phi: clamp(1rem, calc(1rem * var(--phi)), 3rem);
}

.phi-container {
  padding-block: var(--space-phi);
  /* o espaço acima é phi vezes o abaixo */
  padding-top: calc(var(--space-phi) * var(--phi));
  padding-bottom: var(--space-phi);
}
```

### Leading Lines com SVG
```jsx
function LeadingLines({ color = "var(--color-primary)", opacity = 0.15 }) {
  return (
    <svg
      className="leading-lines"
      style={{ position: "absolute", inset: 0, width: "100%", height: "100%", pointerEvents: "none" }}
      aria-hidden="true"
    >
      {/* Linhas que convergem para o centro-direita (onde está o CTA) */}
      <line x1="0%" y1="20%" x2="75%" y2="50%" stroke={color} strokeWidth="0.5" opacity={opacity} />
      <line x1="0%" y1="80%" x2="75%" y2="50%" stroke={color} strokeWidth="0.5" opacity={opacity} />
      <line x1="10%" y1="0%" x2="75%" y2="50%" stroke={color} strokeWidth="0.3" opacity={opacity * 0.7} />
      <line x1="10%" y1="100%" x2="75%" y2="50%" stroke={color} strokeWidth="0.3" opacity={opacity * 0.7} />

      {/* Ponto focal */}
      <circle cx="75%" cy="50%" r="4" fill={color} opacity={opacity * 2} />
    </svg>
  );
}
```

### Eyeline Match — Força do Olhar
```jsx
// Imagem com pessoa olhando para o CTA
function EyelineLayout({ imageSrc, ctaText, ctaHref }) {
  return (
    <div className="eyeline-section">
      {/* Pessoa olhando para a DIREITA (onde está o CTA) */}
      <div className="eyeline-image">
        <img
          src={imageSrc}
          alt="Pessoa satisfeita com o produto"
          // IMPORTANTE: a pessoa na imagem olha para a direita
          // Usuário segue instintivamente o olhar → CTA
        />
      </div>
      {/* CTA no lado para onde a pessoa olha */}
      <div className="eyeline-cta">
        <a href={ctaHref} className="btn btn-primary">{ctaText}</a>
      </div>
    </div>
  );
}
```

---

## GÉNEROS CINEMATOGRÁFICOS → TEMPLATES DE DESIGN

### Checklist por gênero
```
NOIR — LUXURY/DARK:
□ Background: oklch(8% 0.01 240) ou mais escuro
□ Fonte display: serifada condensed (Cormorant, Playfair)
□ Imagens: P&B ou desaturadas com alto contraste
□ Animações: lentas, pesadas, nenhuma "alegria"
□ Grain: pesado, sempre presente
□ Acentos: gold ou vermelho apenas

SCI-FI — TECH FUTURISTA:
□ Background: azul-preto profundo
□ Elementos HUD: borders com scanlines, retículos
□ Fonte: monospace + display geométrica
□ Cores: ciano, verde-elétrico, azul-cobalto
□ Animações: precisas, mecânicas, responsivas
□ Partículas: fluxo de dados, grid de pontos

ROMANCE — WELLNESS/LIFESTYLE:
□ Paleta: cream, rose, sage, gold muito suave
□ Fontes: serifada cursiva + humanista
□ Imagens: quentes, bokeh, luz natural
□ Animações: suaves, spring, orgânicas
□ Texturas: papel, tecido, natureza
□ Espaçamento: generoso, sem pressa

THRILLER — URGÊNCIA/CONVERSÃO:
□ Cores: preto + vermelho + branco (zero outros)
□ Tipografia: ultra-bold condensed, caps
□ Animações: rápidas, cortantes, sem suavidade
□ Countdown timers, escassez visual
□ Contraste máximo em tudo

DOCUMENTÁRIO — CREDIBILIDADE:
□ Fotografia real, não ilustração
□ Dados em destaque, sem decoração
□ Fonte: sans-serif neutra (não Inter)
□ Cores: dessaturadas com acento informacional
□ Vídeo: b-roll como background com overlay
□ Citações: tipografia editorial com foto real
```

---

## TÉCNICAS CINEMATOGRÁFICAS → WEB

### Slow Motion com GSAP
```js
// Slow motion em momento de impacto
function dramaticReveal(element) {
  const tl = gsap.timeline();
  tl
    .to(element, { scale: 1.02, duration: 0.8, ease: "power1.out" })
    .to(element, { scale: 1, duration: 3.0, ease: "power1.inOut" }, "+=0") // slow mo
    .to(".stat-number", { textContent: targetValue, snap: { textContent: 1 }, duration: 2.5, ease: "power2.out" }, "<");

  // Efeito de slow-mo no background também
  tl.to(".bg-particles", { timeScale: 0.2 }, 0);
}
```

### Freeze Frame
```js
// Congelar animação em momento de impacto
function freezeFrame(element, duration = 500) {
  const tl = gsap.timeline();
  tl
    .to(element, { scale: 1, opacity: 1, y: 0, duration: 0.4, ease: "power4.out" })
    .to({}, { duration: duration / 1000 }) // freeze — pausa proposital
    .to(element, { /* continuar */ });
}
```

### Whip Pan Transition
```js
function whipPanTransition(fromSection, toSection) {
  const tl = gsap.timeline();
  tl
    .to(fromSection, {
      x: "-100vw",
      duration: 0.25,
      ease: "power4.in",
      filter: "blur(20px)",
    })
    .fromTo(toSection,
      { x: "100vw", filter: "blur(20px)" },
      { x: 0, filter: "blur(0px)", duration: 0.25, ease: "power4.out" }
    );
}
```

### Fourth Wall Break — UI Consciente
```jsx
function AwareUI({ userName }) {
  const [idleTime, setIdleTime] = useState(0);
  const [message, setMessage] = useState(null);

  useEffect(() => {
    const idleMessages = [
      "Ainda pensando? 🤔",
      "Oi! Ainda estou aqui se precisar de ajuda.",
      "Não há pressa... mas nossa oferta expira amanhã.",
    ];

    const timer = setInterval(() => {
      setIdleTime(t => t + 1);
      if (idleTime > 30 && !message) {
        setMessage(idleMessages[Math.floor(Math.random() * idleMessages.length)]);
      }
    }, 1000);

    return () => clearInterval(timer);
  }, [idleTime, message]);

  // Saudar pelo nome ao retornar
  useEffect(() => {
    const handleVisibility = () => {
      if (!document.hidden && userName) {
        setMessage(`Bem-vindo de volta, ${userName.split(" ")[0]}!`);
        setTimeout(() => setMessage(null), 3000);
      }
    };
    document.addEventListener("visibilitychange", handleVisibility);
    return () => document.removeEventListener("visibilitychange", handleVisibility);
  }, [userName]);

  return (
    <AnimatePresence>
      {message && (
        <motion.div
          className="aware-message"
          initial={{ opacity: 0, y: 20, scale: 0.9 }}
          animate={{ opacity: 1, y: 0, scale: 1 }}
          exit={{ opacity: 0, y: 10, scale: 0.9 }}
        >
          {message}
        </motion.div>
      )}
    </AnimatePresence>
  );
}
```
