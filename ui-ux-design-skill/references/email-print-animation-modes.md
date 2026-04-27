# Referências: Email, Print, Data Animation, Segmentos, Modes

---

## EMAIL DESIGN (HTML Email)

```html
<!-- Estrutura base compatível com Outlook, Gmail, Apple Mail -->
<!DOCTYPE html>
<html lang="pt-BR" xmlns:o="urn:schemas-microsoft-com:office:office"
      xmlns:v="urn:schemas-microsoft-com:vml">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<!--[if mso]>
<noscript><xml><o:OfficeDocumentSettings><o:PixelsPerInch>96</o:PixelsPerInch>
</o:OfficeDocumentSettings></xml></noscript>
<![endif]-->
<style>
  /* Reset */
  * { margin:0; padding:0; box-sizing:border-box; }
  body { width:100%; background:#f4f4f5; font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif; }
  table { border-spacing:0; border-collapse:collapse; }
  td { padding:0; }
  img { border:0; display:block; }
  a { color:#6366f1; text-decoration:none; }

  /* Dark mode */
  @media (prefers-color-scheme: dark) {
    .email-body { background:#1a1a1a !important; }
    .email-card { background:#2d2d2d !important; border-color:#3f3f3f !important; }
    .text-primary { color:#f0f0f0 !important; }
    .text-secondary { color:#a0a0a0 !important; }
  }

  /* Responsivo */
  @media only screen and (max-width:600px) {
    .email-card { padding:24px 20px !important; }
    .email-btn { width:100% !important; text-align:center !important; }
    .hide-mobile { display:none !important; }
  }
</style>
</head>
<body>

<!-- Wrapper -->
<table role="presentation" width="100%" class="email-body" style="background:#f4f4f5">
<tr><td align="center" style="padding:40px 16px">

<!-- Card principal — max 600px -->
<table role="presentation" width="600" class="email-card"
       style="max-width:600px;width:100%;background:#fff;border:1px solid #e5e7eb;
              border-radius:12px;overflow:hidden">

  <!-- Header com logo -->
  <tr>
    <td style="padding:32px 40px 24px;border-bottom:1px solid #f3f4f6">
      <img src="https://exemplo.com/logo.png" alt="Logo" width="120" height="32"
           style="width:120px;height:auto">
    </td>
  </tr>

  <!-- Corpo -->
  <tr>
    <td class="text-primary" style="padding:40px;color:#111827">
      <h1 style="font-size:24px;font-weight:700;line-height:1.3;margin-bottom:16px">
        Título do email
      </h1>
      <p class="text-secondary" style="font-size:16px;line-height:1.6;color:#6b7280;margin-bottom:24px">
        Corpo do email com conteúdo principal. Máximo 3-4 parágrafos.
        Manter linguagem direta e objetivo claro.
      </p>

      <!-- CTA Button — usar table para compatibilidade Outlook -->
      <table role="presentation" class="email-btn">
        <tr>
          <td style="border-radius:8px;background:#6366f1">
            <a href="https://exemplo.com/action"
               style="display:block;padding:14px 28px;color:#fff;font-weight:600;
                      font-size:16px;text-decoration:none;white-space:nowrap">
              Texto do botão
            </a>
          </td>
        </tr>
      </table>

      <!-- Microcopy abaixo do CTA -->
      <p style="font-size:13px;color:#9ca3af;margin-top:12px;text-align:center">
        Se o botão não funcionar, <a href="https://exemplo.com">clique aqui</a>.
      </p>
    </td>
  </tr>

  <!-- Footer do email -->
  <tr>
    <td style="padding:24px 40px;background:#f9fafb;border-top:1px solid #f3f4f6">
      <p style="font-size:12px;color:#9ca3af;line-height:1.5;text-align:center">
        Você recebeu este email porque está inscrito em nossa lista.<br>
        <a href="{{unsubscribe_url}}" style="color:#6b7280">Descadastrar</a>
        · <a href="{{preferences_url}}" style="color:#6b7280">Preferências</a>
        · <a href="{{privacy_url}}" style="color:#6b7280">Privacidade</a><br><br>
        Empresa Ltda · Rua Exemplo, 123 · São Paulo, SP
      </p>
    </td>
  </tr>

</table>
</td></tr>
</table>
</body>
</html>
```

### Regras de HTML Email
```
✅ Sempre usar tables para layout (não divs/flexbox)
✅ CSS inline em todos os elementos (Gmail remove <style>)
✅ Imagens com width/height explícitos
✅ Alt text em todas as imagens (muitos clientes bloqueiam imagens)
✅ Links completos com https://
✅ Testar em: Gmail, Outlook 2019, Apple Mail, iOS Mail, Android Gmail
✅ Max-width: 600px
✅ Fonte mínima: 14px (16px em mobile)

❌ Nunca usar: flexbox, grid, position, float, JS, formulários
❌ Nunca usar: webfonts (usar stack de sistema)
❌ Nunca confiar em CSS externo
```

---

## PRINT & PDF STYLING

```css
@media print {
  /* Reset de cores para impressão */
  * { -webkit-print-color-adjust: exact; print-color-adjust: exact; }

  /* Ocultar elementos desnecessários */
  nav, .sidebar, .footer, .no-print,
  .cookie-banner, .chat-widget { display: none !important; }

  /* Layout de impressão */
  body { font-size: 12pt; line-height: 1.5; color: #000; background: #fff; }
  .container { max-width: 100%; padding: 0; margin: 0; }

  /* Quebras de página */
  h1, h2, h3 { break-after: avoid; page-break-after: avoid; }
  table, figure { break-inside: avoid; page-break-inside: avoid; }
  tr { break-inside: avoid; }

  /* Forçar quebra antes de seções */
  .page-break-before { break-before: page; page-break-before: always; }

  /* Links — mostrar URL */
  a[href]::after { content: " (" attr(href) ")"; font-size: 0.85em; color: #666; }
  a[href^="#"]::after, a[href^="javascript"]::after { content: ""; }

  /* Tipografia impressa */
  h1 { font-size: 24pt; } h2 { font-size: 18pt; } h3 { font-size: 14pt; }
  p, li { orphans: 3; widows: 3; } /* min 3 linhas no início/fim de página */

  /* Margens da página */
  @page {
    margin: 2cm 1.5cm;
    size: A4;
  }
  @page :first { margin-top: 3cm; }

  /* Cabeçalho/rodapé de página */
  .print-header { display: block !important; }
  .print-header { position: fixed; top: 0; width: 100%; }
}

/* Esconder elementos de impressão no ecrã */
.print-only { display: none; }
@media print { .print-only { display: block; } }
```

```js
// Gerar PDF via Puppeteer (Node.js)
const puppeteer = require('puppeteer');

async function generatePDF(url, outputPath) {
  const browser = await puppeteer.launch({ headless: 'new' });
  const page = await browser.newPage();

  await page.goto(url, { waitUntil: 'networkidle0' });
  await page.emulateMediaType('print');

  await page.pdf({
    path: outputPath,
    format: 'A4',
    margin: { top:'2cm', right:'1.5cm', bottom:'2cm', left:'1.5cm' },
    printBackground: true,
    displayHeaderFooter: true,
    headerTemplate: `<div style="font-size:10px;width:100%;text-align:right;padding-right:1cm">
                       <span class="date"></span>
                     </div>`,
    footerTemplate: `<div style="font-size:10px;width:100%;text-align:center">
                       Página <span class="pageNumber"></span> de <span class="totalPages"></span>
                     </div>`,
  });

  await browser.close();
}
```

---

## DATA ANIMATION

```jsx
// Bar Chart Race (ranking animado)
function BarChartRace({ frames, interval = 1000 }) {
  const [currentFrame, setCurrentFrame] = useState(0);
  const [playing, setPlaying] = useState(false);

  useEffect(() => {
    if (!playing) return;
    const timer = setInterval(() => {
      setCurrentFrame(f => {
        if (f >= frames.length - 1) { setPlaying(false); return f; }
        return f + 1;
      });
    }, interval);
    return () => clearInterval(timer);
  }, [playing, frames.length, interval]);

  const data = frames[currentFrame];
  const max = Math.max(...data.map(d => d.value));

  return (
    <div className="race-wrapper">
      <div className="race-chart" aria-live="polite" aria-atomic="true"
           aria-label={`Frame ${currentFrame + 1} de ${frames.length}`}>
        {data.sort((a, b) => b.value - a.value).slice(0, 10).map((item, i) => (
          <motion.div
            key={item.id}
            className="race-bar-row"
            layout
            transition={{ duration: interval / 1000 * 0.8, ease: [0.4,0,0.2,1] }}
          >
            <span className="race-rank">{i + 1}</span>
            <span className="race-label">{item.label}</span>
            <motion.div
              className="race-bar"
              style={{ backgroundColor: item.color || chartColors.categorical[i % 8] }}
              animate={{ width: `${(item.value / max) * 100}%` }}
              transition={{ duration: interval / 1000 * 0.9 }}
            />
            <motion.span
              className="race-value"
              animate={{ opacity: 1 }}
              key={item.value}
            >
              {item.value.toLocaleString()}
            </motion.span>
          </motion.div>
        ))}
      </div>

      <div className="race-controls">
        <button className="btn btn-ghost" onClick={() => { setCurrentFrame(0); setPlaying(false); }}>
          ⏮
        </button>
        <button className="btn btn-primary" onClick={() => setPlaying(p => !p)}
                aria-label={playing ? 'Pausar' : 'Reproduzir'}>
          {playing ? '⏸' : '▶'}
        </button>
        <span className="race-year">{frames[currentFrame].label}</span>
      </div>
    </div>
  );
}

// Sparkline (mini chart inline)
function Sparkline({ data, color = 'var(--color-primary)', width = 80, height = 24 }) {
  const max = Math.max(...data);
  const min = Math.min(...data);
  const range = max - min || 1;

  const points = data.map((v, i) =>
    `${(i / (data.length - 1)) * width},${height - ((v - min) / range) * height}`
  ).join(' ');

  const isPositive = data[data.length - 1] >= data[0];

  return (
    <svg width={width} height={height} aria-hidden="true" style={{ overflow:'visible' }}>
      <polyline
        points={points}
        fill="none"
        stroke={isPositive ? 'var(--color-success)' : 'var(--color-error)'}
        strokeWidth="1.5"
        strokeLinecap="round"
        strokeLinejoin="round"
      />
    </svg>
  );
}
```

---

## MODOS AVANÇADOS — Implementação

### Design Critique — Exemplo de output

Quando ativado, Claude entrega:
```
DESIGN CRITIQUE — Hero Section

IDENTIDADE & ESTRATÉGIA
✓ Identidade coerente com o segmento tech
✗ Design genérico — poderia ser de qualquer empresa SaaS
✗ Hierarquia visual fraca — CTA compete com headline em peso visual

PROBLEMAS CRÍTICOS
→ Mobile (375px): Texto do headline truncado com overflow-x oculto
   Impacto: 60% dos usuários não leem a proposta de valor
   Solução: font-size: clamp(2rem, 8vw, 4rem) + overflow: visible

→ Contraste: CTA button (#a5b4fc sobre #fff) = 2.8:1 — falha WCAG AA
   Impacto: Usuários com baixa visão não identificam o botão
   Solução: Usar #4f46e5 como background do botão (#fff texto = 8.6:1)

PROBLEMAS DE QUALIDADE
→ Imagem hero sem srcset — carregando 2.4MB em mobile
   Solução: picture element com AVIF/WebP e srcset por breakpoint

→ Animação usa top/left (causa reflow) em vez de transform
   Solução: gsap.to(el, { y: 0, opacity: 1 }) em vez de { top: 0 }

OPORTUNIDADES
→ Adicionar badge pulsante acima do headline aumenta engajamento ~15%
→ Social proof logo abaixo do CTA reduz fricção de conversão

SCORE ATUAL: 5/10
POTENCIAL COM MELHORIAS: 8.5/10
```

### Visual Reference → Tokens — Protocolo

Quando usuário menciona "estilo como o Linear":
```css
/* Tokens extraídos da referência Linear */
:root {
  /* Identidade: dark, preciso, foco em produto */
  --color-bg:      #0f0f0f;    /* fundo quase preto */
  --color-surface: #1c1c1c;    /* superfície elevada */
  --color-border:  rgba(255,255,255,0.06); /* borda muito sutil */
  --color-primary: #5c6bc0;    /* índigo suave */
  --color-accent:  #7c8cf8;    /* roxo claro para destaques */

  /* Tipografia: geométrica, peso variável */
  --font-heading:  'Inter Variable', sans-serif;
  --font-mono:     'Geist Mono', monospace;

  /* Motion: rápido e preciso */
  --motion-fast:   120ms;      /* mais rápido que o padrão */
  --ease-primary:  cubic-bezier(0.2, 0, 0, 1); /* ease muito suave */
}
```
