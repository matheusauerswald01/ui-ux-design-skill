# Referência: Jornada Emocional & Gêneros de Design

---

## MAPA DE JORNADA EMOCIONAL COMPLETO

### Sequência emocional em landing pages que convertem
```
ETAPA 1 — RECONHECIMENTO (Emoção: alívio)
"Eles me entendem"
Visual: Fundo neutro, tipografia humanista, linguagem em 1ª pessoa
Cor: Warm gray, sem saturação
Motion: Estático ou muito sutil

  COPY PATTERN:
  "Você já [problema específico]?"
  "[X] horas por semana fazendo [tarefa tediosa]?"
  "Cansado de [frustração comum]?"

ETAPA 2 — CURIOSIDADE (Emoção: interesse)
"Isso pode ser diferente"
Visual: Primeira revelação do produto, visualmente intrigante
Cor: Ligeiro aumento de saturação, primeira cor de destaque
Motion: Uma animação de revelação — suave, não agressiva

  COPY PATTERN:
  "E se você pudesse [resultado desejado]?"
  "Imagine [estado futuro positivo]"
  "[Produto] muda como você [atividade]"

ETAPA 3 — DESEJO (Emoção: ânsia)
"Eu quero isso"
Visual: Produto em uso com resultado visível
Cor: Paleta mais quente, elementos de destaque aparecem
Motion: Animações de produto demonstrando o valor

  COPY PATTERN:
  "[Stat impressionante] de [usuários] que [resultado]"
  "[Resultado específico] em [tempo específico]"
  "De [estado ruim] para [estado bom] em [tempo]"

ETAPA 4 — DÚVIDA (Emoção: hesitação)
"Mas será que funciona para mim?"
Visual: Depoimentos com rostos reais, casos concretos
Cor: Neutra, confiável — não tentar "vender" visualmente aqui
Motion: Sutil, deixar o conteúdo falar

  COPY PATTERN:
  "[Nome, cargo, empresa] reduziu [métrica] em [X]%"
  "Como [perfil similar] conseguiu [resultado]"
  "[Número] empresas como a sua já usam"

ETAPA 5 — CONFIANÇA (Emoção: segurança)
"Isso é legítimo"
Visual: Logos de clientes, certificações, mídia, prêmios
Cor: Azul de confiança, verde de validação
Motion: Aparecimento progressivo de logos/provas

  ELEMENTOS DE CONFIANÇA (hierarquia):
  Expert endorsements > Customer logos > User reviews > Awards

ETAPA 6 — EXCITAÇÃO (Emoção: antecipação)
"Eu preciso disso agora"
Visual: Climax visual — a seção mais impactante
Cor: Paleta mais saturada e quente da página
Motion: Animação de maior impacto, possível momento de delight

  COPY PATTERN:
  "Junte-se a [número] que já [transformação]"
  "Seus competidores já estão usando"
  "[Resultado final] em [tempo específico]"

ETAPA 7 — AÇÃO (Emoção: comprometimento)
"Vou fazer isso"
Visual: CTA limpo, sem distrações, espaço em branco
Cor: A cor de ação mais saturada e energética
Motion: Hover do botão com micro-feedback imediato

  CTA PATTERN:
  "[Verbo ativo] + [benefício imediato]"
  "Começar grátis" > "Criar conta" > "Acessar agora"
  Microcopy abaixo: remover última objeção
```

---

## CURIOSITY GAP — TÉCNICA DE ENGAJAMENTO

### Princípio
```
Revelar informação suficiente para criar curiosidade,
não suficiente para satisfazer. O gap cria tensão que
o usuário resolve fazendo scroll.
```

### Implementações
```jsx
// 1. Headline incompleta
"O segredo que 94% das equipes de produto..."
// Parar — o usuário rola para completar

// 2. Imagem cortada
function CroppedReveal({ imageSrc }) {
  return (
    <div style={{
      height: "60vh",
      overflow: "hidden",
      maskImage: "linear-gradient(to bottom, black 60%, transparent 100%)",
    }}>
      <img src={imageSrc} alt="" style={{ width: "100%", height: "auto" }} />
      <div style={{ textAlign: "center", marginTop: "-2rem" }}>
        <span style={{ color: "var(--color-text-muted)", fontSize: "0.875rem" }}>
          ↓ Continue para ver o resultado
        </span>
      </div>
    </div>
  );
}

// 3. Dado parcial
"Nossos clientes economizam em média R$ [rolou para ver]"

// 4. Número que cresce mas não termina sem scroll
function PartialCounter({ finalValue }) {
  const [displayed, setDisplayed] = useState(0);
  const ref = useRef(null);

  useEffect(() => {
    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) {
        // Conta até 70% do valor e para — curiosidade!
        animateTo(finalValue * 0.7);
      }
    });
    observer.observe(ref.current);
    return () => observer.disconnect();
  }, []);

  return (
    <div ref={ref}>
      <span>{displayed.toLocaleString()}</span>
      <span style={{ opacity: 0.4 }}>+</span> {/* indica que há mais */}
    </div>
  );
}
```

---

## FEAR → RESOLUTION ARC

### Framework completo
```
NOMEAR O MEDO ANTES DE RESOLVER:

Fintech:     "Você está perdendo dinheiro por não otimizar X"
Healthtech:  "Ignorar esse sintoma pode custar caro"
SaaS B2B:    "Sua equipe está perdendo 8h/semana em tarefas manuais"
Edtech:      "Você pode estar deixando seu filho para trás"
E-commerce:  "Seus competidores já estão usando IA para reduzir custos"

PADRÃO VISUAL DO FEAR:
- Fundo escuro/neutro (sem esperança ainda)
- Tipografia pesada, sem cor positiva
- Dado de perda (não de ganho)
- Imagem que evoca a dor (não genérica)

PADRÃO VISUAL DA RESOLUTION:
- Transição de cor (escuro → claro) ou (frio → quente)
- Tipografia que "respira"
- Dado de ganho
- Imagem que evoca o futuro positivo
- Motion de alívio (expansão, abertura, breathing)
```

---

## DESIGN PARA MEMÓRIA

### O que fica depois que a aba fecha
```
PRINCÍPIO PEAK-END:
O usuário lembra do PICO e do FINAL da experiência.
Tudo no meio importa menos.

PICO — projetar intencionalmente:
□ Qual é o momento mais surpreendente da página?
□ Qual é a animação mais inesperada?
□ Qual stat mais impressionante?
□ Qual microcopy mais memorável?
→ Amplificar esse momento, não abafá-lo

FINAL — projetar o último sentimento:
□ O que o usuário sente AO FECHAR a página?
□ Footer com personalidade, não lista de links
□ Última linha de copy: filosófica, engraçada, ou inspiradora
□ Opcional: animação de despedida ao sair

RESÍDUO VERBAL (frase que fica):
Cada marca precisa de uma frase que fica:
Apple: "Think Different"
Nike: "Just Do It"
Airbnb: "Belong Anywhere"
Sua landing page: qual é a frase que o usuário vai contar?
```

### Implementação do Post-Credit Scene
```jsx
function PostCreditScene() {
  const [triggered, setTriggered] = useState(false);
  const footerRef = useRef(null);

  useEffect(() => {
    ScrollTrigger.create({
      trigger: footerRef.current,
      start: "top 80%",
      onEnter: () => {
        if (!triggered) {
          setTriggered(true);
          // Lançar a cena pós-crédito
          setTimeout(() => launchPostCredit(), 800);
        }
      }
    });
  }, []);

  const launchPostCredit = () => {
    // Confetti, som de sucesso, mensagem especial
    launchConfetti();
    sound.playSuccess();
    showSpecialMessage("Você chegou até aqui! Isso merece um desconto extra 🎉");
  };

  return <footer ref={footerRef} className="footer">{/* ... */}</footer>;
}
```

---

## WORLD-BUILDING DIGITAL

### Framework para criar um universo visual coerente
```
CAMADA 1 — FÍSICA DO UNIVERSO:
Quais são as "leis da física" deste universo de design?
- O que PODE se mover? (todos os elementos? apenas alguns?)
- Qual é a "gravidade"? (elementos caem? flutuam? vibram?)
- Qual é o "material" do universo? (glass, metal, paper, neon?)
- Como a luz se comporta? (dura e dramática? difusa e suave?)

CAMADA 2 — FLORA E FAUNA:
Que elementos visuais "habitam" este universo?
- Que formas são recorrentes? (círculos, triângulos, ondas?)
- Que texturas existem?
- Que criaturas/mascotes existem?
- Que fontes "falam" esta língua?

CAMADA 3 — HISTÓRIA E CULTURA:
Qual é o passado deste universo?
- De onde vêm os valores da marca?
- Que referências históricas ou culturais moldam a estética?
- Que "rituais" a marca tem? (certezas que sempre fazem)

CAMADA 4 — LINGUAGEM:
Como as entidades deste universo se comunicam?
- Qual é o tom de voz? (formal? íntimo? irônico? épico?)
- Qual é o vocabulário? (palavras que usam vs evitam)
- Como erros são tratados? (com humor? com gravidade?)

EXEMPLO — UNIVERSO LINEAR:
Física: Movimento preciso, sem ruído, quase cirúrgico
Flora:  Linhas finas, ícones minimalistas, grids exatos
Fauna:  Sem mascotes, os dados são o protagonista
História: Fundada para resolver a frustração do projeto caótico
Linguagem: Direto, sem floreados, valoriza a eficiência

EXEMPLO — UNIVERSO DUOLINGO:
Física: Tudo pode saltar e explodir de felicidade
Flora:  Formas arredondadas, cores saturadas, ilustrações
Fauna:  Duo o coruja — o embaixador carismático do universo
História: Aprender deve ser divertido, não torturante
Linguagem: Irreverente, usa memes, celebra os erros
```

---

## GÊNEROS CINEMATOGRÁFICOS — TEMPLATES COMPLETOS

### Noir — Dark Luxury
```css
/* Implementação completa */
[data-genre="noir"] {
  --color-bg:      oklch(8% 0.01 240);
  --color-surface: oklch(12% 0.02 240);
  --color-text:    oklch(92% 0 0);
  --color-accent:  oklch(65% 0.18 55);   /* gold */
  --font-display:  "Cormorant Garamond", serif;
  --font-body:     "EB Garamond", serif;
  --shadow-drop:   0 20px 60px rgba(0,0,0,0.8);
  --animation-dur: 1.2s;  /* tudo lento */
  --grain-opacity: 0.06;  /* grain pesado */
}

[data-genre="noir"] img {
  filter: contrast(1.3) saturate(0.2) brightness(0.85);
}

[data-genre="noir"] .hero {
  background:
    radial-gradient(ellipse at 20% 50%, oklch(25% 0.08 55 / 0.3) 0%, transparent 60%),
    oklch(8% 0.01 240);
}
```

### Sci-Fi — Tech Futurista
```css
[data-genre="scifi"] {
  --color-bg:      oklch(8% 0.04 250);
  --color-surface: oklch(12% 0.06 240);
  --color-text:    oklch(88% 0.04 200);
  --color-accent:  oklch(70% 0.25 200);  /* cyan */
  --color-accent2: oklch(65% 0.20 50);   /* orange */
  --font-display:  "Syne", monospace;
  --font-body:     "JetBrains Mono", monospace;
}

/* HUD scanlines */
[data-genre="scifi"] .hud-element::after {
  content: "";
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent 0px,
    transparent 2px,
    rgba(0, 255, 200, 0.02) 2px,
    rgba(0, 255, 200, 0.02) 4px
  );
  pointer-events: none;
}

/* Data streams decoration */
[data-genre="scifi"] .data-decoration {
  font-family: "JetBrains Mono", monospace;
  font-size: 0.6rem;
  color: oklch(65% 0.20 200 / 0.3);
  animation: data-flow 10s linear infinite;
}
@keyframes data-flow {
  from { transform: translateY(-100%); }
  to   { transform: translateY(100%); }
}
```

### Art Film — Minimalismo Extremo
```css
[data-genre="art-film"] {
  --color-bg:      oklch(97% 0 0);
  --color-text:    oklch(10% 0 0);
  --color-accent:  oklch(10% 0 0);   /* preto como acento */
  --font-display:  "Cormorant Garamond", serif;
  --font-body:     "EB Garamond", serif;
  --animation-dur: 2.5s;  /* tudo muito lento */
  --space-unit:    4rem;  /* muito espaço */
}

[data-genre="art-film"] section {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20vh 15vw;  /* enorme espaço em branco */
}

[data-genre="art-film"] h1 {
  font-size: clamp(1.5rem, 5vw, 4rem);
  font-weight: 300;
  font-style: italic;
  line-height: 1.3;
  max-width: 20ch;
}
```

### Comedy/Playful
```css
[data-genre="playful"] {
  --color-bg:     oklch(98% 0.02 85);
  --color-primary: oklch(65% 0.30 30);  /* coral vibrante */
  --color-accent:  oklch(75% 0.28 270); /* purple */
  --color-accent2: oklch(80% 0.25 150); /* lime */
  --font-display:  "Nunito", sans-serif;
  --font-body:     "Nunito Sans", sans-serif;
  --animation-dur: 0.3s;
  --bounce-ease:   cubic-bezier(0.34, 1.56, 0.64, 1); /* overshoot */
}

[data-genre="playful"] .btn {
  border-radius: 9999px;
  font-weight: 700;
  transform-origin: center;
  transition: transform var(--animation-dur) var(--bounce-ease);
}
[data-genre="playful"] .btn:hover {
  transform: scale(1.05) rotate(-2deg);
}
[data-genre="playful"] .btn:active {
  transform: scale(0.95) rotate(2deg);
}

/* Elementos flutuantes */
[data-genre="playful"] .decorative {
  animation: float-playful 3s ease-in-out infinite;
}
@keyframes float-playful {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  33%       { transform: translateY(-10px) rotate(3deg); }
  66%       { transform: translateY(-5px) rotate(-2deg); }
}
```

---

## TIMING MUSICAL EM ANIMAÇÕES

### BPM como base de timing
```js
// Sistema de timing baseado em música
class MusicalTiming {
  constructor(bpm = 120) {
    this.bpm = bpm;
    this.beat = 60000 / bpm;        // ms por beat (500ms em 120bpm)
  }

  get whole()       { return this.beat * 4; }     // 2000ms
  get half()        { return this.beat * 2; }     // 1000ms
  get quarter()     { return this.beat; }          // 500ms
  get eighth()      { return this.beat / 2; }     // 250ms
  get sixteenth()   { return this.beat / 4; }     // 125ms
  get thirtySecond(){ return this.beat / 8; }     // 62.5ms
  get triplet()     { return this.beat / 3; }     // ~166ms

  // Duração em beats
  beats(n) { return this.beat * n; }

  // Stagger em subdivisões musicais
  staggerEighths(count) {
    return Array.from({ length: count }, (_, i) => i * this.eighth / 1000);
  }
}

// Para interfaces energéticas (150 BPM)
const energyTiming = new MusicalTiming(150);

// Para luxo contemplativo (60 BPM)
const luxuryTiming = new MusicalTiming(60);

// Para tech preciso (120 BPM — padrão)
const techTiming = new MusicalTiming(120);

// Usar em GSAP
gsap.from(".hero-chars", {
  y: 80,
  opacity: 0,
  stagger: techTiming.sixteenth / 1000, // 0.125s entre chars
  duration: techTiming.quarter / 1000,   // 0.5s cada
  ease: "power4.out",
});
```

---

## PROMPTS V6 — CINEMATOGRÁFICOS

```
STORYTELLING COMPLETO:
"Crie uma landing page de 8 seções com three-act structure para [produto].
Color script que vai de frio (problema) para quente (solução).
Três momentos de curiosity gap, um climax antes do CTA, e uma cena pós-crédito no footer."

COLOR GRADING:
"Aplique um look cinematográfico estilo Blade Runner 2049 ao hero section:
neon azul e laranja sobre fundo quase preto, grain sutil, vignette nas bordas.
Implementar com WebGL fragment shader ou CSS filter+SVG."

WORLD-BUILDING:
"Crie o universo visual da [marca]. Defina: a física do universo (como os elementos se movem),
o leitmotif visual (forma recorrente), a assinatura de motion (ease curve própria),
e 3 easter eggs que revelam personalidade."

GÊNERO:
"Redesenhe esta seção no estilo film noir — alto contraste, sombras dramáticas,
tipografia serifada, paleta P&B com acento dourado, grain pesado, animações lentas e pesadas."

JORNADA EMOCIONAL:
"Mapeie a jornada emocional do hero section ao CTA: Reconhecimento → Curiosidade →
Desejo → Dúvida → Confiança → Ação. Cada seção com seu tratamento visual e copy pattern."

TIMING MUSICAL:
"Crie as animações de entrada desta seção usando timing musical em 90 BPM.
Stagger baseado em colcheias (333ms), duração de cada elemento = 1 beat (666ms),
ease que simula attack e release de instrumento percussivo."

MISE EN SCÈNE:
"Componha esta hero section como um diretor de fotografia:
regra dos terços, leading lines apontando ao CTA, eyeline match na foto com o produto,
profundidade de campo simulada com blur de camadas."
```
