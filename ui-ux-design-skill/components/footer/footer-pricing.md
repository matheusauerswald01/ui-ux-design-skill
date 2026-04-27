# Componentes: Footer Premium + Pricing Table

---

## FOOTER PREMIUM

```jsx
function Footer({ logo, description, links, socials, legal }) {
  const currentYear = new Date().getFullYear();

  return (
    <footer className="footer" role="contentinfo">
      <div className="container">
        <div className="footer-main">
          {/* Brand */}
          <div className="footer-brand">
            <a href="/" className="footer-logo" aria-label="Início">{logo}</a>
            {description && <p className="footer-desc">{description}</p>}
            {socials && (
              <div className="footer-socials" aria-label="Redes sociais">
                {socials.map(s => (
                  <a key={s.name} href={s.href} target="_blank" rel="noopener noreferrer"
                     aria-label={s.name} className="footer-social-link">
                    {s.icon}
                  </a>
                ))}
              </div>
            )}
          </div>

          {/* Links por grupo */}
          {links.map(group => (
            <div key={group.title} className="footer-group">
              <h3 className="footer-group-title">{group.title}</h3>
              <ul className="footer-links">
                {group.items.map(item => (
                  <li key={item.label}>
                    <a href={item.href} className="footer-link"
                       target={item.external ? '_blank' : undefined}
                       rel={item.external ? 'noopener noreferrer' : undefined}>
                      {item.label}
                      {item.badge && (
                        <span className="footer-badge">{item.badge}</span>
                      )}
                    </a>
                  </li>
                ))}
              </ul>
            </div>
          ))}
        </div>

        {/* Bottom bar */}
        <div className="footer-bottom">
          <p className="footer-copyright">
            © {currentYear} {legal?.company}. {legal?.rights || 'Todos os direitos reservados.'}
          </p>
          {legal?.links && (
            <nav aria-label="Links legais" className="footer-legal-links">
              {legal.links.map(l => (
                <a key={l.label} href={l.href} className="footer-legal-link">
                  {l.label}
                </a>
              ))}
            </nav>
          )}
        </div>
      </div>
    </footer>
  );
}
```

```css
.footer {
  background: var(--color-surface);
  border-top: 1px solid var(--color-border);
  padding: 4rem 0 2rem;
}

.footer-main {
  display: grid;
  grid-template-columns: 1.5fr repeat(auto-fill, minmax(140px, 1fr));
  gap: clamp(2rem, 4vw, 4rem);
  padding-bottom: 3rem;
  border-bottom: 1px solid var(--color-border);
}
@media (max-width: 768px) {
  .footer-main { grid-template-columns: 1fr 1fr; }
  .footer-brand { grid-column: 1 / -1; }
}
@media (max-width: 480px) {
  .footer-main { grid-template-columns: 1fr; }
}

.footer-logo { display: inline-block; text-decoration: none; }
.footer-logo:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 4px; }

.footer-desc {
  font-size: var(--text-sm); color: var(--color-text-secondary);
  line-height: 1.6; margin-top: 0.75rem; max-width: 28ch;
}

.footer-socials { display: flex; gap: 0.5rem; margin-top: 1.25rem; }
.footer-social-link {
  width: 36px; height: 36px; display: flex; align-items: center; justify-content: center;
  border-radius: var(--radius-md); color: var(--color-text-secondary);
  border: 1px solid var(--color-border);
  transition: color var(--motion-fast), border-color var(--motion-fast),
              background var(--motion-fast);
}
.footer-social-link:hover {
  color: var(--color-text-primary); border-color: var(--color-border-strong);
  background: var(--color-surface-raised);
}
.footer-social-link:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 2px; }

.footer-group-title {
  font-size: var(--text-sm); font-weight: 600; color: var(--color-text-primary);
  margin-bottom: 0.875rem;
}

.footer-links { list-style: none; display: flex; flex-direction: column; gap: 0.5rem; }
.footer-link {
  font-size: var(--text-sm); color: var(--color-text-secondary); text-decoration: none;
  display: inline-flex; align-items: center; gap: 0.375rem;
  transition: color var(--motion-fast);
}
.footer-link:hover { color: var(--color-text-primary); }
.footer-link:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 2px; border-radius: 2px; }

.footer-badge {
  font-size: 10px; font-weight: 600; padding: 1px 6px;
  background: var(--color-primary); color: white;
  border-radius: 99px; text-transform: uppercase; letter-spacing: 0.05em;
}

.footer-bottom {
  display: flex; flex-wrap: wrap; align-items: center;
  justify-content: space-between; gap: 1rem; padding-top: 1.5rem;
}
.footer-copyright { font-size: var(--text-sm); color: var(--color-text-muted); }
.footer-legal-links { display: flex; flex-wrap: wrap; gap: 1.25rem; }
.footer-legal-link {
  font-size: var(--text-sm); color: var(--color-text-muted); text-decoration: none;
  transition: color var(--motion-fast);
}
.footer-legal-link:hover { color: var(--color-text-secondary); }
```

---

## PRICING TABLE

```jsx
function PricingTable({ plans, billingToggle = true }) {
  const [billing, setBilling] = useState('monthly');
  const savings = 20; // % de desconto anual

  return (
    <section className="pricing" aria-labelledby="pricing-title">
      <h2 id="pricing-title" className="sr-only">Planos e preços</h2>

      {billingToggle && (
        <div className="pricing-toggle" role="group" aria-label="Período de cobrança">
          <button
            className={`pricing-toggle-btn ${billing === 'monthly' ? 'active' : ''}`}
            onClick={() => setBilling('monthly')}
            aria-pressed={billing === 'monthly'}
          >
            Mensal
          </button>
          <button
            className={`pricing-toggle-btn ${billing === 'annual' ? 'active' : ''}`}
            onClick={() => setBilling('annual')}
            aria-pressed={billing === 'annual'}
          >
            Anual
            <span className="savings-badge">-{savings}%</span>
          </button>
        </div>
      )}

      <div className="pricing-grid">
        {plans.map(plan => (
          <PricingCard
            key={plan.id}
            plan={plan}
            billing={billing}
            savingsPercent={savings}
          />
        ))}
      </div>
    </section>
  );
}

function PricingCard({ plan, billing, savingsPercent }) {
  const price = billing === 'annual'
    ? plan.price.annual
    : plan.price.monthly;

  const monthlyEquiv = billing === 'annual'
    ? Math.round(plan.price.annual / 12)
    : null;

  return (
    <motion.article
      className={`pricing-card ${plan.featured ? 'pricing-card--featured' : ''}`}
      initial={{ opacity: 0, y: 20 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true }}
      transition={{ duration: 0.5, ease: [0.16,1,0.3,1] }}
      aria-label={`Plano ${plan.name}${plan.featured ? ' — mais popular' : ''}`}
    >
      {plan.featured && (
        <div className="pricing-badge" aria-label="Plano mais popular">
          Mais popular
        </div>
      )}

      <div className="pricing-header">
        <h3 className="pricing-name">{plan.name}</h3>
        <p className="pricing-desc">{plan.description}</p>
      </div>

      <div className="pricing-price">
        <div className="price-amount">
          <span className="price-currency">{plan.currency || 'R$'}</span>
          <AnimatePresence mode="wait">
            <motion.span
              key={`${plan.id}-${billing}`}
              className="price-value"
              initial={{ opacity: 0, y: -10 }}
              animate={{ opacity: 1, y: 0 }}
              exit={{ opacity: 0, y: 10 }}
              transition={{ duration: 0.2 }}
            >
              {price === 0 ? 'Grátis' : price.toLocaleString()}
            </motion.span>
          </AnimatePresence>
          {price > 0 && (
            <span className="price-period">
              /{billing === 'annual' ? 'ano' : 'mês'}
            </span>
          )}
        </div>
        {monthlyEquiv && price > 0 && (
          <p className="price-monthly-equiv">
            {plan.currency || 'R$'} {monthlyEquiv.toLocaleString()}/mês
          </p>
        )}
      </div>

      <a
        href={plan.cta.href}
        className={`btn pricing-cta ${plan.featured ? 'btn-primary' : 'btn-outline'}`}
        aria-label={`${plan.cta.label} — Plano ${plan.name}`}
      >
        {plan.cta.label}
      </a>

      {plan.trialDays && (
        <p className="pricing-trial">
          {plan.trialDays} dias grátis · Sem cartão de crédito
        </p>
      )}

      <ul className="pricing-features" aria-label={`Recursos do plano ${plan.name}`}>
        {plan.features.map((feature, i) => (
          <li key={i} className={`pricing-feature ${feature.included === false ? 'pricing-feature--excluded' : ''}`}>
            <span aria-hidden="true">{feature.included === false ? '✕' : '✓'}</span>
            <span>
              {feature.label}
              {feature.tooltip && <Tooltip content={feature.tooltip}><InfoIcon /></Tooltip>}
            </span>
          </li>
        ))}
      </ul>
    </motion.article>
  );
}
```

```css
.pricing { display: flex; flex-direction: column; align-items: center; gap: 2.5rem; }

/* Toggle */
.pricing-toggle {
  display: inline-flex; background: var(--color-surface-raised);
  border-radius: 99px; padding: 4px; gap: 4px;
}
.pricing-toggle-btn {
  padding: 0.5rem 1.25rem; border-radius: 99px; border: none;
  font-size: var(--text-sm); font-weight: 500; cursor: pointer;
  color: var(--color-text-secondary); background: transparent;
  display: flex; align-items: center; gap: 0.5rem;
  transition: all var(--motion-fast) var(--ease-smooth);
}
.pricing-toggle-btn.active {
  background: var(--color-surface); color: var(--color-text-primary);
  box-shadow: var(--shadow-sm);
}
.savings-badge {
  font-size: 10px; font-weight: 700; padding: 2px 6px;
  background: var(--color-success); color: white; border-radius: 99px;
}

/* Grid */
.pricing-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 300px), 1fr));
  gap: 1.5rem; width: 100%; align-items: start;
}

/* Card */
.pricing-card {
  position: relative; padding: 2rem;
  background: var(--color-surface); border: 1px solid var(--color-border);
  border-radius: var(--radius-xl, 20px);
  display: flex; flex-direction: column; gap: 1.5rem;
}
.pricing-card--featured {
  border-color: var(--color-primary);
  box-shadow: 0 0 0 1px var(--color-primary), var(--shadow-xl);
}

.pricing-badge {
  position: absolute; top: -13px; left: 50%; transform: translateX(-50%);
  padding: 4px 14px; background: var(--color-primary); color: white;
  font-size: 12px; font-weight: 600; border-radius: 99px; white-space: nowrap;
}

.pricing-name { font-size: var(--text-xl); font-weight: 700; }
.pricing-desc { font-size: var(--text-sm); color: var(--color-text-secondary); margin-top: 0.25rem; }

.pricing-price { display: flex; flex-direction: column; gap: 0.25rem; }
.price-amount { display: flex; align-items: baseline; gap: 0.125rem; }
.price-currency { font-size: var(--text-lg); font-weight: 500; color: var(--color-text-secondary); }
.price-value { font-size: clamp(2rem, 5vw, 2.75rem); font-weight: 700; font-variant-numeric: tabular-nums; line-height: 1; }
.price-period { font-size: var(--text-sm); color: var(--color-text-secondary); margin-left: 2px; }
.price-monthly-equiv { font-size: var(--text-sm); color: var(--color-text-muted); }

.pricing-cta { width: 100%; justify-content: center; }
.pricing-trial { font-size: 12px; color: var(--color-text-muted); text-align: center; }

.pricing-features { list-style: none; display: flex; flex-direction: column; gap: 0.625rem; }
.pricing-feature {
  display: flex; align-items: flex-start; gap: 0.625rem;
  font-size: var(--text-sm); line-height: 1.5;
}
.pricing-feature span:first-child {
  color: var(--color-success); font-size: 12px; flex-shrink: 0; margin-top: 2px; font-weight: 700;
}
.pricing-feature--excluded { color: var(--color-text-muted); }
.pricing-feature--excluded span:first-child { color: var(--color-text-muted); }
```

## Uso — PricingTable

```jsx
<PricingTable
  plans={[
    {
      id: 'free',
      name: 'Grátis',
      description: 'Para começar sem compromisso.',
      price: { monthly: 0, annual: 0 },
      cta: { label: 'Criar conta grátis', href: '/signup' },
      features: [
        { label: '3 projetos' },
        { label: '1 usuário' },
        { label: '1GB de armazenamento' },
        { label: 'Suporte via e-mail', included: false },
        { label: 'Integrações avançadas', included: false },
      ],
    },
    {
      id: 'pro',
      name: 'Pro',
      description: 'Para times em crescimento.',
      price: { monthly: 79, annual: 758 },
      featured: true,
      trialDays: 14,
      cta: { label: 'Começar grátis', href: '/signup?plan=pro' },
      features: [
        { label: 'Projetos ilimitados' },
        { label: 'Até 10 usuários' },
        { label: '50GB de armazenamento' },
        { label: 'Suporte prioritário', tooltip: 'Resposta em até 4 horas úteis' },
        { label: '50+ integrações' },
      ],
    },
    {
      id: 'enterprise',
      name: 'Enterprise',
      description: 'Para grandes organizações.',
      price: { monthly: 299, annual: 2868 },
      cta: { label: 'Falar com vendas', href: '/contact' },
      features: [
        { label: 'Projetos ilimitados' },
        { label: 'Usuários ilimitados' },
        { label: 'Armazenamento ilimitado' },
        { label: 'SLA 99.99% uptime' },
        { label: 'SSO + SAML + SCIM' },
      ],
    },
  ]}
/>
```
