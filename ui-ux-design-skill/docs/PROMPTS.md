# 📋 Biblioteca de Prompts

Prompts prontos para extrair o máximo da skill. Copie, adapte e use.

---

## 🎬 Animações de Entrada

### Hero com SplitText
```
Crie uma hero section com:
- Título animado com GSAP SplitText (chars entrando em stagger)
- Subtítulo com fade up suave após o título
- CTA com animação de border reveal
- Fundo com ruído (noise texture CSS)
- Totalmente responsivo, dark mode
```

### Cards em Stagger
```
Crie um grid de 3 cards de serviço com Framer Motion.
Cada card deve entrar no viewport em sequência (stagger 0.12s).
Cards com efeito hover de elevação e border glow.
```

### Texto Revelado por Máscara
```
Implemente um componente de text reveal onde cada linha
de texto sobe de trás de uma máscara de overflow ao
entrar no viewport. Usar GSAP ou CSS puro.
```

---

## 🌀 Scroll & Parallax

### Scrolltelling em 3 Capítulos
```
Crie uma seção de scrolltelling com 3 momentos narrativos:
1. Apresentação do problema
2. A solução
3. O resultado

Cada capítulo deve ser pinado e revelar enquanto o usuário rola.
Usar GSAP ScrollTrigger com scrub.
```

### Parallax Multi-Camadas
```
Crie uma seção de parallax com:
- Camada de fundo (imagem) se movendo lentamente
- Camada de texto se movendo na velocidade normal
- Camada decorativa (formas SVG) se movendo rápido
Todas sincronizadas com o scroll.
```

### Horizontal Scroll
```
Crie uma galeria de projetos com scroll horizontal
ativado pelo scroll vertical do usuário.
GSAP ScrollTrigger + pinning.
Indicador de progresso visual.
```

---

## 🎨 Identidade Visual

### Design System Dark
```
Crie um design system completo dark para um SaaS B2B de analytics:
- Tokens CSS (cores, tipografia, espaçamento, raios, sombras)
- Paleta principal: azul escuro profissional
- Componentes: Button (3 variantes), Input, Card, Badge, Avatar
- Mostrar uso em um dashboard de exemplo
```

### Landing Page de Startup
```
Crie uma landing page completa para uma startup de [DESCREVA O PRODUTO].
- Hero com animação de entrada
- Seção de features com ícones animados
- Depoimentos com scroll horizontal
- Pricing com toggle mensal/anual
- CTA final
Dark mode, identidade moderna, responsiva.
```

### Rebranding de Seção
```
Tenho esta seção de [COLE O CÓDIGO]. 
O problema: no mobile os textos ficam sobrepostos e os
cards ficam com espaçamento quebrado.
Corrija todos os problemas de responsividade e eleve
o nível visual para um design mais premium.
```

---

## 🌐 Three.js & 3D

### Background de Partículas
```
Crie um background interativo com Three.js:
- Campo de partículas que reage ao movimento do mouse
- As partículas se afastam do cursor (repulsão suave)
- Cores: gradiente de [COR1] para [COR2]
- Performance otimizada (máx 2000 partículas)
```

### Card com Tilt 3D
```
Crie um card de produto premium com:
- Efeito de tilt 3D que segue o mouse (Three.js ou CSS perspective)
- Reflexo de luz que move com o cursor
- Hover revela badge e botão de ação
- Animação de entrada com GSAP ao entrar no viewport
```

### Texto 3D no Hero
```
Hero section com o título principal renderizado em 3D com Three.js.
- Texto com material PBR (metálico ou matte)
- Iluminação dramática com 2 point lights
- Leve rotação animada em idle
- Interação: texto reage levemente ao mouse
```

---

## 🖱️ Microinterações

### Cursor Customizado
```
Crie um cursor customizado com Framer Motion:
- Círculo pequeno que segue o mouse com spring
- Ao hover em links: expande e muda cor
- Ao clicar: pulsa
- Modo de texto: vira underline
- Esconde o cursor nativo
```

### Botão Magnético
```
Implemente um botão magnético onde ao aproximar o mouse,
o botão se move em direção ao cursor.
Ao sair, volta com efeito elástico.
Usar Framer Motion com useMotionValue e useSpring.
```

### Número Contador Animado
```
Crie um componente de estatísticas onde os números
sobem progressivamente (0 → valor final) ao entrar no viewport.
3 métricas: [X clientes], [Y projetos], [Z países]
```

---

## 📱 Mobile & Responsivo

### Navigation Mobile
```
Crie um menu mobile premium:
- Botão hamburger com animação de transform (3 linhas → X)
- Menu full-screen com overlay
- Links entram em stagger ao abrir
- Fechamento com swipe down
- Backdrop blur no fundo
```

### Carrossel Touch
```
Crie um carrossel de cards responsivo:
- Desktop: 3 cards visíveis
- Tablet: 2 cards
- Mobile: 1.2 cards (mostra parte do próximo)
- Swipe nativo no mobile
- Indicadores de progresso
- Auto-play pausado no hover/touch
```

---

## 🎭 Transições de Página

### Page Transition com Overlay
```
Implemente transições de página com Next.js App Router:
- Overlay preto que sobe e desce entre páginas
- Duração total: 800ms (400ms saída + 400ms entrada)
- Ease: power4.inOut
- Loader minimal durante transição
```

### Morphing de Layout
```
Crie uma transição entre lista de projetos e detalhe do projeto
usando Framer Motion Layout Animations (layoutId).
O card da lista deve se expandir para ocupar a tela toda.
```

---

## 🎪 Efeitos Especiais

### Glassmorphism Premium
```
Crie um painel glassmorphism premium:
- Backdrop blur real (não fake)
- Border com gradiente sutil
- Sombra interna para profundidade
- Conteúdo: [DESCREVA]
- Funciona tanto em fundo escuro quanto claro
```

### Gradiente Animado
```
Crie um fundo com gradiente mesh animado (blob/aurora effect):
- 3-4 blobs de cor que se movem lentamente
- Cores: [DESCREVA a paleta]
- Performance: usar CSS animation, não JS
- Sem travar em mobile
```

### Infinite Marquee
```
Crie um marquee de logos de clientes:
- Loop infinito suave
- Pausa no hover
- Gradient fade nas bordas (esquerda e direita)
- Velocidade ajustável
- Duplica os itens automaticamente para seamless loop
```
