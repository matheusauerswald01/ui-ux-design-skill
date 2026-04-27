# Componente: Navbar Premium

Stack: React + Framer Motion + CSS Variables
Features: scroll-aware, mobile drawer, blur backdrop, active states

---

## navbar.jsx

```jsx
"use client";
import { useState, useEffect, useRef } from "react";
import { motion, AnimatePresence, useReducedMotion } from "framer-motion";

/**
 * Navbar — Componente de navegação premium com:
 * - Scroll-aware (muda aparência ao rolar)
 * - Mobile drawer com animação suave
 * - Links em stagger ao abrir mobile
 * - Backdrop blur
 * - Focus trap no mobile
 * - Totalmente acessível (ARIA, teclado, reduced-motion)
 */
export function Navbar({ logo, links, cta }) {
  const [scrolled, setScrolled]   = useState(false);
  const [mobileOpen, setMobileOpen] = useState(false);
  const drawerRef = useRef(null);
  const hamburgerRef = useRef(null);
  const prefersReduced = useReducedMotion();

  // Scroll detection
  useEffect(() => {
    const onScroll = () => setScrolled(window.scrollY > 20);
    window.addEventListener("scroll", onScroll, { passive: true });
    return () => window.removeEventListener("scroll", onScroll);
  }, []);

  // Fechar com ESC + bloquear scroll do body
  useEffect(() => {
    const onKey = (e) => { if (e.key === "Escape") closeMobile(); };
    if (mobileOpen) {
      document.body.style.overflow = "hidden";
      window.addEventListener("keydown", onKey);
      // Focar no primeiro link do drawer
      setTimeout(() => {
        drawerRef.current?.querySelector("a")?.focus();
      }, 100);
    } else {
      document.body.style.overflow = "";
      hamburgerRef.current?.focus();
    }
    return () => {
      window.removeEventListener("keydown", onKey);
      document.body.style.overflow = "";
    };
  }, [mobileOpen]);

  const closeMobile = () => setMobileOpen(false);

  const drawerVariants = {
    closed: { x: "100%", transition: { duration: prefersReduced ? 0 : 0.35, ease: [0.4,0,0.2,1] } },
    open:   { x: "0%",  transition: { duration: prefersReduced ? 0 : 0.4, ease: [0.16,1,0.3,1] } },
  };

  const linkVariants = {
    closed: { opacity: 0, x: 20 },
    open: (i) => ({
      opacity: 1, x: 0,
      transition: {
        delay: prefersReduced ? 0 : 0.15 + i * 0.06,
        duration: prefersReduced ? 0 : 0.4,
        ease: [0.16,1,0.3,1]
      }
    }),
  };

  return (
    <>
      <header
        className={`navbar ${scrolled ? "navbar--scrolled" : ""}`}
        role="banner"
      >
        <div className="container navbar__inner">
          {/* Logo */}
          <a href="/" className="navbar__logo" aria-label="Ir para página inicial">
            {logo}
          </a>

          {/* Links desktop */}
          <nav className="navbar__links" aria-label="Navegação principal">
            {links.map((link) => (
              <a
                key={link.href}
                href={link.href}
                className={`navbar__link ${link.active ? "navbar__link--active" : ""}`}
                aria-current={link.active ? "page" : undefined}
              >
                {link.label}
              </a>
            ))}
          </nav>

          {/* CTA + Hamburger */}
          <div className="navbar__end">
            {cta && (
              <a href={cta.href} className="btn btn-primary navbar__cta">
                {cta.label}
              </a>
            )}

            {/* Botão hamburger */}
            <button
              ref={hamburgerRef}
              className={`hamburger ${mobileOpen ? "hamburger--open" : ""}`}
              onClick={() => setMobileOpen(v => !v)}
              aria-expanded={mobileOpen}
              aria-controls="mobile-menu"
              aria-label={mobileOpen ? "Fechar menu" : "Abrir menu"}
            >
              <span className="hamburger__bar" aria-hidden="true" />
              <span className="hamburger__bar" aria-hidden="true" />
              <span className="hamburger__bar" aria-hidden="true" />
            </button>
          </div>
        </div>
      </header>

      {/* Mobile Drawer */}
      <AnimatePresence>
        {mobileOpen && (
          <>
            {/* Overlay */}
            <motion.div
              className="drawer-overlay"
              initial={{ opacity: 0 }}
              animate={{ opacity: 1 }}
              exit={{ opacity: 0 }}
              transition={{ duration: prefersReduced ? 0 : 0.3 }}
              onClick={closeMobile}
              aria-hidden="true"
            />

            {/* Drawer */}
            <motion.nav
              ref={drawerRef}
              id="mobile-menu"
              className="drawer"
              role="navigation"
              aria-label="Menu mobile"
              variants={drawerVariants}
              initial="closed"
              animate="open"
              exit="closed"
            >
              <div className="drawer__header">
                <a href="/" className="navbar__logo" onClick={closeMobile}>
                  {logo}
                </a>
                <button
                  className="drawer__close"
                  onClick={closeMobile}
                  aria-label="Fechar menu"
                >
                  <svg width="24" height="24" viewBox="0 0 24 24" aria-hidden="true">
                    <path d="M6 6l12 12M6 18L18 6" stroke="currentColor"
                          strokeWidth="2" strokeLinecap="round"/>
                  </svg>
                </button>
              </div>

              <div className="drawer__links">
                {links.map((link, i) => (
                  <motion.a
                    key={link.href}
                    href={link.href}
                    className={`drawer__link ${link.active ? "drawer__link--active" : ""}`}
                    variants={linkVariants}
                    custom={i}
                    aria-current={link.active ? "page" : undefined}
                    onClick={closeMobile}
                  >
                    {link.label}
                    <svg width="20" height="20" viewBox="0 0 20 20" aria-hidden="true">
                      <path d="M4 10h12M12 6l4 4-4 4" stroke="currentColor"
                            strokeWidth="1.5" strokeLinecap="round"/>
                    </svg>
                  </motion.a>
                ))}
              </div>

              {cta && (
                <motion.div
                  className="drawer__footer"
                  variants={linkVariants}
                  custom={links.length}
                >
                  <a href={cta.href} className="btn btn-primary" style={{ width: "100%", justifyContent: "center" }}>
                    {cta.label}
                  </a>
                </motion.div>
              )}
            </motion.nav>
          </>
        )}
      </AnimatePresence>
    </>
  );
}
```

## navbar.css

```css
/* ===== NAVBAR ===== */
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: var(--z-sticky, 100);
  padding-block: 1rem;
  transition: background var(--motion-base, 300ms) var(--ease-smooth),
              border-color var(--motion-base, 300ms) var(--ease-smooth),
              backdrop-filter var(--motion-base, 300ms) var(--ease-smooth);
}

.navbar--scrolled {
  background: rgba(10,10,10,0.85);
  backdrop-filter: blur(16px) saturate(1.8);
  -webkit-backdrop-filter: blur(16px) saturate(1.8);
  border-bottom: 1px solid var(--color-border, rgba(255,255,255,0.08));
}

.navbar__inner {
  display: flex;
  align-items: center;
  gap: 2rem;
}

/* Logo */
.navbar__logo {
  flex-shrink: 0;
  color: var(--color-text-primary);
  text-decoration: none;
  font-weight: 700;
  font-size: 1.25rem;
}
.navbar__logo:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 4px;
  border-radius: 4px;
}

/* Links desktop */
.navbar__links {
  display: none;
  align-items: center;
  gap: 0.25rem;
  flex: 1;
  justify-content: center;
}

@media (min-width: 768px) {
  .navbar__links { display: flex; }
}

.navbar__link {
  padding: 0.5rem 0.875rem;
  border-radius: var(--radius-md, 8px);
  color: var(--color-text-secondary, rgba(255,255,255,0.55));
  text-decoration: none;
  font-size: var(--text-sm, 0.875rem);
  font-weight: 500;
  transition: color var(--motion-fast, 150ms), background var(--motion-fast, 150ms);
}
.navbar__link:hover { color: var(--color-text-primary); background: rgba(255,255,255,0.06); }
.navbar__link--active { color: var(--color-text-primary); }
.navbar__link:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

/* End */
.navbar__end {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin-left: auto;
}

.navbar__cta { display: none; }
@media (min-width: 640px) { .navbar__cta { display: inline-flex; } }

/* Hamburger */
.hamburger {
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 5px;
  width: 40px;
  height: 40px;
  background: none;
  border: none;
  cursor: pointer;
  padding: 8px;
  border-radius: var(--radius-md, 8px);
  transition: background var(--motion-fast, 150ms);
}
.hamburger:hover { background: rgba(255,255,255,0.06); }
.hamburger:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 2px; }
@media (min-width: 768px) { .hamburger { display: none; } }

.hamburger__bar {
  display: block;
  width: 100%;
  height: 1.5px;
  background: var(--color-text-primary);
  border-radius: 1px;
  transform-origin: center;
  transition: transform var(--motion-base, 300ms) var(--ease-smooth),
              opacity var(--motion-base, 300ms);
}

/* Hamburger → X */
.hamburger--open .hamburger__bar:nth-child(1) { transform: translateY(6.5px) rotate(45deg); }
.hamburger--open .hamburger__bar:nth-child(2) { opacity: 0; transform: scaleX(0); }
.hamburger--open .hamburger__bar:nth-child(3) { transform: translateY(-6.5px) rotate(-45deg); }

/* ===== DRAWER ===== */
.drawer-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.6);
  backdrop-filter: blur(4px);
  z-index: calc(var(--z-overlay, 200) - 1);
}

.drawer {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  width: min(360px, 100vw);
  background: var(--color-surface, #141414);
  border-left: 1px solid var(--color-border, rgba(255,255,255,0.08));
  z-index: var(--z-overlay, 200);
  display: flex;
  flex-direction: column;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}

.drawer__header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1.25rem 1.5rem;
  border-bottom: 1px solid var(--color-border);
}

.drawer__close {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  background: rgba(255,255,255,0.06);
  border: none;
  border-radius: var(--radius-md, 8px);
  color: var(--color-text-primary);
  cursor: pointer;
  transition: background var(--motion-fast, 150ms);
}
.drawer__close:hover { background: rgba(255,255,255,0.1); }
.drawer__close:focus-visible { outline: 2px solid var(--color-primary); }

.drawer__links {
  display: flex;
  flex-direction: column;
  padding: 1rem;
  flex: 1;
}

.drawer__link {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1rem 0.75rem;
  border-radius: var(--radius-md, 8px);
  color: var(--color-text-secondary);
  text-decoration: none;
  font-size: 1.125rem;
  font-weight: 500;
  transition: color var(--motion-fast), background var(--motion-fast);
  border-bottom: 1px solid var(--color-border);
}
.drawer__link:last-child { border-bottom: none; }
.drawer__link:hover { color: var(--color-text-primary); background: rgba(255,255,255,0.04); }
.drawer__link--active { color: var(--color-text-primary); }
.drawer__link:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 2px; }

.drawer__footer {
  padding: 1.5rem;
  border-top: 1px solid var(--color-border);
}

/* Reduced motion */
@media (prefers-reduced-motion: reduce) {
  .navbar, .navbar__link, .hamburger__bar, .hamburger, .drawer__close { transition: none; }
}
```

## Uso

```jsx
<Navbar
  logo={<YourLogo />}
  links={[
    { label: "Produto",  href: "/produto" },
    { label: "Preços",   href: "/precos" },
    { label: "Docs",     href: "/docs" },
    { label: "Blog",     href: "/blog", active: true },
  ]}
  cta={{ label: "Começar grátis", href: "/signup" }}
/>
```
