# ❓ FAQ — Perguntas Frequentes

## Instalação

**Q: Preciso de conta paga no Claude para usar skills?**
Skills estão disponíveis nos planos Pro e superiores do Claude. Verifique os planos em [claude.ai/upgrade](https://claude.ai/upgrade).

**Q: A skill funciona no Claude API?**
Sim! Você pode incluir o conteúdo do `SKILL.md` como parte do seu system prompt ao usar a API.

**Q: Posso usar a skill em projetos comerciais?**
Sim, a skill é licenciada sob MIT. O código gerado pelo Claude com a skill é seu.

---

## Uso

**Q: Preciso mencionar "skill" ou "UI/UX" para ela ser ativada?**
Não. A skill é ativada automaticamente quando o Claude detecta contexto de design. Apenas descreva o que você precisa naturalmente.

**Q: A skill funciona em português e inglês?**
Sim, em qualquer idioma. As instruções internas são seguidas independente do idioma do seu prompt.

**Q: Posso pedir para a skill usar um framework específico (ex: Next.js, Vue, Svelte)?**
Sim! Especifique no seu prompt: *"usando Next.js App Router"*, *"com Vue 3 + Composition API"*, etc. A skill adapta os padrões para o framework.

**Q: Como peço para a skill NÃO usar alguma biblioteca?**
Seja explícito: *"sem GSAP, apenas CSS animations"* ou *"sem Three.js, apenas Framer Motion"*.

---

## Qualidade e Resultados

**Q: O resultado sempre vai ser nível Awwwards?**
A skill define os padrões e o raciocínio, mas o resultado final depende de:
- Quão detalhado é o seu prompt
- A complexidade do que foi pedido
- O plano/modelo do Claude (Sonnet e Opus produzem melhores resultados)

Para melhores resultados, use os prompts da [biblioteca de prompts](PROMPTS.md).

**Q: A skill previne todos os bugs de responsividade?**
A skill inclui um checklist preventivo que cobre os problemas mais comuns. Para garantia total, sempre teste o output em diferentes dispositivos.

**Q: Posso usar a skill para revisar design existente?**
Sim! Cole seu código e peça: *"Revise este design, identifique problemas de responsividade, hierarquia visual e eleve o nível para premium"*.

---

## Técnico

**Q: A skill inclui as dependências do GSAP, Three.js etc.?**
A skill contém o conhecimento para usar essas bibliotecas, mas você precisa instalar as dependências no seu projeto normalmente via npm/yarn.

```bash
# GSAP
npm install gsap

# Framer Motion
npm install framer-motion

# Three.js
npm install three

# Remotion
npm install remotion @remotion/cli
```

**Q: O GSAP SplitText e MorphSVG são premium. A skill usa?**
A skill conhece esses plugins e pode usá-los quando pedido. Para projetos comerciais, verifique o licenciamento em [gsap.com](https://gsap.com/pricing/).

**Q: Como reportar um problema com a skill?**
Abra uma [Issue](../../issues) com:
1. O prompt que você usou
2. O resultado recebido
3. O que esperava receber

---

## Contribuição

**Q: Como posso melhorar a skill?**
Veja [CONTRIBUTING.md](../CONTRIBUTING.md). As principais formas de contribuir:
- Adicionar novos padrões de componentes
- Criar exemplos de código completos
- Reportar casos onde a skill falhou
- Melhorar a documentação

**Q: Posso criar uma skill derivada (fork) para meu framework específico?**
Sim, a licença MIT permite. Considere mencionar a origem e contribuir melhorias de volta!
