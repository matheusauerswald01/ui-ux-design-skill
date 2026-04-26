<div align="center">

# 🎨 UI/UX Design Skill for Claude

**Uma skill profissional de nível Awwwards para Claude — motion design, animações, 3D, scrolltelling e interfaces que impressionam.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Claude Skill](https://img.shields.io/badge/Claude-Skill-orange.svg)](https://claude.ai)
[![Version](https://img.shields.io/badge/version-1.0.0-green.svg)](CHANGELOG.md)

[Instalação](#-instalação) · [O que faz](#-o-que-faz) · [Exemplos](#-exemplos-de-uso) · [Referências](#-referências)

</div>

---

## ✨ O que é isso?

Uma **skill** é um arquivo de instrução que você instala no Claude para torná-lo especialista em uma área específica. Esta skill transforma o Claude em um designer/engenheiro de interfaces de alto nível, capaz de criar experiências visuais no padrão de estúdios premiados como Active Theory, Locomotive e Resn.

### O que ela cobre:

| Área | Tecnologias |
|------|-------------|
| **Motion Design** | GSAP, Framer Motion, CSS Animations |
| **3D na Web** | Three.js, WebGL, Shaders |
| **Vídeo/Motion** | Remotion, Lottie |
| **Design System** | Tipografia fluida, Paletas, Tokens CSS |
| **UX Avançado** | Scrolltelling, Storytelling, Responsividade |
| **Componentes** | Magnetic buttons, Text reveals, Cursors, Parallax |

---

## 📦 Instalação

### Método 1 — Arquivo `.skill` (recomendado)

1. Baixe o arquivo [`ui-ux-design.skill`](releases/latest)
2. Acesse [claude.ai](https://claude.ai) → **Settings** → **Skills**
3. Clique em **"Install Skill"** e selecione o arquivo
4. Pronto! O Claude agora é seu designer sênior 🎉

### Método 2 — Manual (clonar o repositório)

```bash
git clone https://github.com/SEU_USUARIO/ui-ux-design-skill.git
```

Depois copie a pasta `ui-ux-design-skill/` para o diretório de skills do Claude no seu ambiente.

---

## 🚀 Exemplos de Uso

Depois de instalar, basta conversar naturalmente com o Claude. Exemplos de prompts:

### Hero Section com Animação
```
Crie uma hero section para uma agência de branding. 
Quero animação de entrada nos títulos com GSAP SplitText, 
fundo com noise texture e um CTA magnético.
```

### Scrolltelling de Produto
```
Faça uma landing page com scrolltelling para um app de meditação. 
Cada seção deve revelar o conteúdo conforme o usuário rola a página. 
Dark mode, paleta calmante, fontes humanistas.
```

### Componente 3D
```
Crie um card de produto com efeito 3D tilt no hover usando Three.js. 
O card deve ter reflexo de luz e sombra realista.
```

### Sistema de Design Completo
```
Preciso de um design system dark para um SaaS de analytics. 
Defina tokens de cor, escala tipográfica com clamp(), 
componentes base e um dashboard de exemplo.
```

### Página Responsiva
```
Tenho uma section de depoimentos onde no mobile os cards 
ficam com espaçamento quebrado. Corrija e melhore o design.
```

---

## 🧠 Como a Skill Pensa

A skill foi projetada com **raciocínio de layout preventivo** — antes de gerar qualquer código, ela verifica mentalmente:

- ✅ Texto nunca sobrepõe outro texto em nenhum breakpoint
- ✅ Cards nunca ficam isolados ou com espaçamento quebrado  
- ✅ Animações usam apenas `transform` e `opacity` (nunca `top/left`)
- ✅ `prefers-reduced-motion` sempre respeitado
- ✅ Contraste WCAG AA verificado automaticamente
- ✅ Performance: `will-change` aplicado e removido corretamente

---

## 📁 Estrutura do Repositório

```
ui-ux-design-skill/
├── SKILL.md                          # Instrução principal da skill
├── references/
│   ├── advanced-techniques.md        # Parallax, page transitions, shaders...
│   └── color-systems.md              # Paletas prontas e teoria de cor
├── examples/
│   ├── hero-section/                 # Exemplo completo de hero animada
│   ├── scrolltelling/                # Exemplo de scrolltelling com GSAP
│   └── design-system/                # Exemplo de design system dark
├── docs/
│   ├── GUIA-COMPLETO.md              # Documentação detalhada
│   ├── PROMPTS.md                    # Prompts prontos para usar
│   └── FAQ.md                        # Perguntas frequentes
├── CHANGELOG.md
├── CONTRIBUTING.md
└── LICENSE
```

---

## 📖 Documentação

| Arquivo | Conteúdo |
|---------|----------|
| [Guia Completo](docs/GUIA-COMPLETO.md) | Todos os recursos da skill detalhados |
| [Prompts Prontos](docs/PROMPTS.md) | Biblioteca de prompts para resultados premium |
| [FAQ](docs/FAQ.md) | Respostas para dúvidas comuns |
| [Técnicas Avançadas](references/advanced-techniques.md) | Receitas de código avançadas |
| [Sistemas de Cores](references/color-systems.md) | Paletas e teoria de cor |

---

## 🎯 Padrão de Qualidade

Esta skill foi calibrada para atingir o nível de estúdios como:

- **[Active Theory](https://activetheory.net)** — narrative + 3D + interatividade
- **[Locomotive](https://locomotive.ca)** — scroll experience + tipografia
- **[Resn](https://resn.co.nz)** — experimentação + UX inovador
- **[Linear](https://linear.app)** — produto com identidade forte
- **[Stripe](https://stripe.com)** — animações sutis e precisas
- **[Vercel](https://vercel.com)** — dark design system de qualidade

---

## 🤝 Contribuindo

Contribuições são muito bem-vindas! Veja [CONTRIBUTING.md](CONTRIBUTING.md) para detalhes.

Ideias para contribuir:
- Adicionar novos exemplos de componentes
- Criar paletas de cores para nichos específicos
- Traduzir documentação
- Reportar bugs ou comportamentos inesperados

---

## 📄 Licença

MIT © 2025 — Veja [LICENSE](LICENSE) para detalhes.

---

<div align="center">
  <sub>Feito com 🖤 para designers e devs que não aceitam o genérico.</sub>
</div>
