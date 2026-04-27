# Componente: Hero Section Completa

Stack: React + Framer Motion + GSAP + CSS Variables
Nível: Awwwards-ready

---

## hero.jsx

```jsx
"use client";
import { useEffect, useRef } from "react";
import { motion, useReducedMotion } from "framer-motion";
import { gsap } from "gsap";
import { SplitText } from "gsap/SplitText";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(SplitText, ScrollTrigger);

/**
 * HeroSection — Componente de hero premium com:
 * - Animação de entrada de título com SplitText
 * - Subtítulo e CTA em stagger
 * - Noise texture overlay
 * - Gradient radial de fundo
 * - Scroll indicator animado
 * - Totalmente responsivo
 * - prefers-reduced-motion respeitado
 */
export function HeroSection({ headline, subheadline, cta, ctaSecondary, badge }) {
  const containerRef = useRef(null);
  const prefersReduced = useReducedMotion();

  useEffect(() => {
    if (prefersReduced) {
      // Mostrar tudo sem animação
      gsap.set([".hero-badge", ".hero-title", ".hero-sub", ".hero-actions", ".hero-scroll"], {
        opacity: 1, y: 0, autoAlpha: 1,
      });
      return;
    }

    const ctx = gsap.context(() => {
      const split = new SplitText(".hero-title", { type: "chars,words" });
      const tl = gsap.timeline({ defaults: { ease: "power3.out" } });

      tl.set(".hero-content", { autoAlpha: 1 })
        .from(".hero-badge", { y: 16, opacity: 0, duration: 0.5 })
        .from(split.chars, {
          y: 100,
          opacity: 0,
          stagger: { amount: 0.5, from: "start" },
          duration: 1.0,
        }, "-=0.2")
        .from(".hero-sub", { y: 24, opacity: 0, duration: 0.7 }, "-=0.4")
        .from(".hero-actions", { y: 20, opacity: 0, duration: 0.6 }, "-=0.3")
        .from(".hero-scroll", { y: 10, opacity: 0, duration: 0.5 }, "-=0.1");
    }, containerRef);

    return () => ctx.revert();
  }, [prefersReduced]);

  return (
    <section ref={containerRef} className="hero" aria-label="Seção principal">
      {/* Fundo */}
      <div className="hero-bg" aria-hidden="true">
        <div className="hero-bg__gradient" />
        <div className="hero-bg__noise" />
        <div className="hero-bg__grid" />
      </div>

      {/* Conteúdo */}
      <div className="hero-content" style={{ opacity: 0 }}>
        <div className="container">
          <div className="hero-inner">

            {/* Badge */}
            {badge && (
              <div className="hero-badge">
                <span className="hero-badge__dot" aria-hidden="true" />
                {badge}
              </div>
            )}

            {/* Headline */}
            <h1 className="hero-title">{headline}</h1>

            {/* Subheadline */}
            <p className="hero-sub">{subheadline}</p>

            {/* Actions */}
            <div className="hero-actions">
              <a href={cta.href} className="btn btn-primary" role="button">
                {cta.label}
              </a>
              {ctaSecondary && (
                <a href={ctaSecondary.href} className="btn btn-ghost" role="button">
                  {ctaSecondary.label}
                  <svg width="16" height="16" viewBox="0 0 16 16" aria-hidden="true">
                    <path d="M3 8h10M9 4l4 4-4 4" stroke="currentColor"
                          strokeWidth="1.5" strokeLinecap="round"/>
                  </svg>
                </a>
              )}
            </div>

          </div>
        </div>
      </div>

      {/* Scroll indicator */}
      <div className="hero-scroll" aria-hidden="true">
        <div className="hero-scroll__line" />
      </div>
    </section>
  );
}
```

## hero.css

```css
/* ===== HERO SECTION ===== */
.hero {
  position: relative;
  min-height: 100svh;         /* svh para mobile (exclui barra do browser) */
  display: flex;
  align-items: center;
  overflow: hidden;
}

/* --- Fundo --- */
.hero-bg {
  position: absolute;
  inset: 0;
  z-index: 0;
}

.hero-bg__gradient {
  position: absolute;
  inset: 0;
  background:
    radial-gradient(ellipse 80% 50% at 50% -10%, rgba(99,102,241,0.25) 0%, transparent 70%),
    radial-gradient(ellipse 40% 30% at 80% 80%,  rgba(139,92,246,0.15) 0%, transparent 60%);
}

/* Noise texture via SVG inline */
.hero-bg__noise {
  position: absolute;
  inset: -200%;
  width: 400%;
  height: 400%;
  opacity: 0.04;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='1'/%3E%3C/svg%3E");
  animation: noise-move 0.5s steps(2) infinite;
}

@keyframes noise-move {
  0%   { transform: translate(0, 0); }
  25%  { transform: translate(-5%, 5%); }
  50%  { transform: translate(5%, -5%); }
  75%  { transform: translate(-3%, -3%); }
  100% { transform: translate(0, 0); }
}

/* Grid sutil */
.hero-bg__grid {
  position: absolute;
  inset: 0;
  background-image:
    linear-gradient(rgba(255,255,255,0.03) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255,255,255,0.03) 1px, transparent 1px);
  background-size: 80px 80px;
  mask-image: radial-gradient(ellipse at center, black 30%, transparent 80%);
}

/* --- Conteúdo --- */
.hero-content {
  position: relative;
  z-index: 1;
  width: 100%;
}

.hero-inner {
  max-width: 800px;
  display: flex;
  flex-direction: column;
  gap: clamp(1rem, 3vw, 1.5rem);
  padding-block: clamp(5rem, 15vh, 8rem);
}

/* --- Badge --- */
.hero-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.375rem 0.875rem;
  background: rgba(99,102,241,0.12);
  border: 1px solid rgba(99,102,241,0.3);
  border-radius: var(--radius-full, 9999px);
  font-size: var(--text-sm, 0.875rem);
  color: rgba(165,180,252,1);
  font-weight: 500;
  width: fit-content;
}

.hero-badge__dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: currentColor;
  animation: pulse-dot 2s ease-in-out infinite;
}

@keyframes pulse-dot {
  0%, 100% { opacity: 1; transform: scale(1); }
  50%       { opacity: 0.5; transform: scale(0.8); }
}

/* --- Headline --- */
.hero-title {
  font-size: var(--text-hero, clamp(3.5rem, 10vw, 7rem));
  font-weight: 700;
  line-height: 1.0;
  letter-spacing: -0.03em;
  color: var(--color-text-primary, rgba(255,255,255,0.95));
  overflow: hidden; /* para SplitText não vazar */
}

/* --- Subheadline --- */
.hero-sub {
  font-size: var(--text-lg, clamp(1.125rem, 2.5vw, 1.375rem));
  line-height: 1.6;
  color: var(--color-text-secondary, rgba(255,255,255,0.55));
  max-width: 55ch;
}

/* --- Actions --- */
.hero-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
  align-items: center;
}

/* --- Botões --- */
.btn {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem 1.5rem;
  border-radius: var(--radius-md, 8px);
  font-weight: 600;
  font-size: var(--text-base, 1rem);
  cursor: pointer;
  border: none;
  text-decoration: none;
  transition:
    background var(--motion-fast, 150ms) var(--ease-smooth),
    transform   var(--motion-fast, 150ms) var(--ease-smooth),
    box-shadow  var(--motion-fast, 150ms) var(--ease-smooth);
  will-change: transform;
}

.btn:focus-visible {
  outline: 2px solid var(--color-primary, #6366f1);
  outline-offset: 3px;
}

.btn-primary {
  background: var(--color-primary, #6366f1);
  color: #ffffff;
}

.btn-primary:hover {
  background: #4f46e5;
  transform: translateY(-1px);
  box-shadow: 0 8px 24px rgba(99,102,241,0.4);
}

.btn-primary:active {
  transform: translateY(0);
  box-shadow: none;
}

.btn-ghost {
  background: rgba(255,255,255,0.05);
  color: var(--color-text-primary, rgba(255,255,255,0.9));
  border: 1px solid var(--color-border, rgba(255,255,255,0.08));
}

.btn-ghost:hover {
  background: rgba(255,255,255,0.08);
  transform: translateY(-1px);
}

/* --- Scroll indicator --- */
.hero-scroll {
  position: absolute;
  bottom: 2rem;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.hero-scroll__line {
  width: 1px;
  height: 60px;
  background: linear-gradient(to bottom, rgba(255,255,255,0.4), transparent);
  animation: scroll-line 2s ease-in-out infinite;
}

@keyframes scroll-line {
  0%   { transform: scaleY(0); transform-origin: top; opacity: 0; }
  50%  { transform: scaleY(1); transform-origin: top; opacity: 1; }
  51%  { transform-origin: bottom; }
  100% { transform: scaleY(0); transform-origin: bottom; opacity: 0; }
}

/* ===== RESPONSIVO ===== */

/* Mobile: ajustes específicos */
@media (max-width: 480px) {
  .hero-actions {
    flex-direction: column;
    align-items: stretch;
  }
  .btn { justify-content: center; }
  .hero-scroll { display: none; } /* esconder em mobile pequeno */
}

/* Reduced motion */
@media (prefers-reduced-motion: reduce) {
  .hero-bg__noise,
  .hero-badge__dot,
  .hero-scroll__line { animation: none; }
  .btn { transition: none; }
}
```

## Uso

```jsx
<HeroSection
  badge="Novo — Versão 2.0 disponível"
  headline="Produtos entregues. Sem o caos."
  subheadline="A plataforma que une seu time e acelera entregas — do briefing ao deploy, sem friction."
  cta={{ label: "Começar grátis", href: "/signup" }}
  ctaSecondary={{ label: "Ver demo", href: "/demo" }}
/>
```

## Variantes

**Sem badge:**
```jsx
<HeroSection
  headline="Design que converte."
  subheadline="..."
  cta={{ label: "Falar com vendas", href: "/contact" }}
/>
```

**Apenas CTA primário:**
```jsx
<HeroSection
  headline="Cresça sem complicação."
  subheadline="..."
  cta={{ label: "Criar conta grátis", href: "/signup" }}
/>
```
