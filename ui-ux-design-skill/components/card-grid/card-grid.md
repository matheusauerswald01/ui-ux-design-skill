# Componente: Card Grid Responsivo

Stack: React + Framer Motion + CSS Variables
Features: stagger on viewport, hover lift, skeleton loading, empty state

---

## card-grid.jsx

```jsx
"use client";
import { motion, useReducedMotion } from "framer-motion";

/**
 * CardGrid — Grid responsivo de cards premium com:
 * - Stagger de entrada no viewport
 * - Hover com elevação e glow
 * - Estado de loading (skeleton)
 * - Estado empty
 * - Sem breakpoints — usa auto-fill grid
 */
export function CardGrid({ items, loading = false, emptyMessage = "Nenhum item encontrado." }) {
  const prefersReduced = useReducedMotion();

  const containerVariants = {
    hidden: {},
    visible: {
      transition: { staggerChildren: prefersReduced ? 0 : 0.08, delayChildren: 0.1 }
    }
  };

  const cardVariants = {
    hidden:  { opacity: 0, y: prefersReduced ? 0 : 32 },
    visible: {
      opacity: 1, y: 0,
      transition: {
        duration: prefersReduced ? 0 : 0.5,
        ease: [0.16, 1, 0.3, 1]
      }
    }
  };

  // Estado de loading — renderiza skeletons
  if (loading) {
    return (
      <div className="card-grid" aria-busy="true" aria-label="Carregando itens...">
        {Array.from({ length: 6 }).map((_, i) => (
          <SkeletonCard key={i} />
        ))}
      </div>
    );
  }

  // Estado vazio
  if (!items || items.length === 0) {
    return (
      <motion.div
        className="card-grid-empty"
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        role="status"
      >
        <div className="empty-icon" aria-hidden="true">
          <svg width="48" height="48" viewBox="0 0 48 48" fill="none">
            <rect x="8" y="8" width="32" height="32" rx="8" stroke="currentColor" strokeWidth="2" strokeDasharray="4 4"/>
            <path d="M18 24h12M24 18v12" stroke="currentColor" strokeWidth="2" strokeLinecap="round"/>
          </svg>
        </div>
        <p className="empty-message">{emptyMessage}</p>
      </motion.div>
    );
  }

  return (
    <motion.div
      className="card-grid"
      variants={containerVariants}
      initial="hidden"
      whileInView="visible"
      viewport={{ once: true, margin: "-60px" }}
      role="list"
    >
      {items.map((item) => (
        <motion.article
          key={item.id}
          className="card"
          variants={cardVariants}
          role="listitem"
        >
          <Card item={item} />
        </motion.article>
      ))}
    </motion.div>
  );
}

function Card({ item }) {
  return (
    <a href={item.href} className="card-link" aria-label={`Ver ${item.title}`}>
      {/* Imagem */}
      {item.image && (
        <div className="card-image-wrapper">
          <img
            src={item.image}
            alt={item.imageAlt || ""}
            className="card-image"
            loading="lazy"
            width={400}
            height={240}
          />
          {item.badge && (
            <span className="card-badge" aria-label={`Categoria: ${item.badge}`}>
              {item.badge}
            </span>
          )}
        </div>
      )}

      {/* Conteúdo */}
      <div className="card-body">
        {item.meta && (
          <div className="card-meta">
            <time dateTime={item.meta.dateISO}>{item.meta.date}</time>
            {item.meta.readTime && <span>· {item.meta.readTime}</span>}
          </div>
        )}
        <h3 className="card-title">{item.title}</h3>
        {item.description && (
          <p className="card-description">{item.description}</p>
        )}
        {item.tags && (
          <div className="card-tags" aria-label="Tags">
            {item.tags.map(tag => (
              <span key={tag} className="card-tag">{tag}</span>
            ))}
          </div>
        )}
      </div>

      {/* Arrow indicator */}
      <div className="card-arrow" aria-hidden="true">
        <svg width="20" height="20" viewBox="0 0 20 20">
          <path d="M4 10h12M12 6l4 4-4 4" stroke="currentColor"
                strokeWidth="1.5" strokeLinecap="round"/>
        </svg>
      </div>
    </a>
  );
}

function SkeletonCard() {
  return (
    <div className="card card--skeleton" aria-hidden="true">
      <div className="skeleton" style={{ height: 200 }} />
      <div className="card-body" style={{ display: "flex", flexDirection: "column", gap: 12 }}>
        <div className="skeleton" style={{ height: 12, width: "40%" }} />
        <div className="skeleton" style={{ height: 20, width: "85%" }} />
        <div className="skeleton" style={{ height: 16, width: "95%" }} />
        <div className="skeleton" style={{ height: 16, width: "70%" }} />
      </div>
    </div>
  );
}
```

## card-grid.css

```css
/* ===== CARD GRID ===== */
.card-grid {
  display: grid;
  /* Sem breakpoints — auto-fill adapta ao espaço disponível */
  grid-template-columns: repeat(auto-fill, minmax(min(100%, 320px), 1fr));
  gap: clamp(1rem, 3vw, 1.5rem);
}

/* Estado vazio */
.card-grid-empty {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  padding: 4rem 2rem;
  text-align: center;
  color: var(--color-text-muted, rgba(255,255,255,0.3));
  grid-column: 1 / -1; /* ocupa todas as colunas */
}
.empty-icon { opacity: 0.4; }
.empty-message { font-size: var(--text-base); }

/* ===== CARD ===== */
.card {
  position: relative;
  background: var(--color-surface, #141414);
  border: 1px solid var(--color-border, rgba(255,255,255,0.08));
  border-radius: var(--radius-lg, 16px);
  overflow: hidden;
  transition:
    transform    var(--motion-base, 300ms) var(--ease-smooth),
    border-color var(--motion-base, 300ms) var(--ease-smooth),
    box-shadow   var(--motion-base, 300ms) var(--ease-smooth);
  will-change: transform;
}

.card-link {
  display: flex;
  flex-direction: column;
  height: 100%;
  text-decoration: none;
  color: inherit;
}

.card:hover {
  transform: translateY(-4px);
  border-color: rgba(255,255,255,0.15);
  box-shadow:
    0 20px 40px rgba(0,0,0,0.4),
    0 0 0 1px rgba(255,255,255,0.05) inset;
}

.card:focus-within {
  outline: 2px solid var(--color-primary, #6366f1);
  outline-offset: 2px;
}

/* Imagem */
.card-image-wrapper {
  position: relative;
  overflow: hidden;
  aspect-ratio: 16 / 9;
}

.card-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform var(--motion-slow, 600ms) var(--ease-smooth);
}

.card:hover .card-image { transform: scale(1.04); }

.card-badge {
  position: absolute;
  top: 0.75rem;
  left: 0.75rem;
  padding: 0.25rem 0.625rem;
  background: var(--color-primary, #6366f1);
  color: white;
  border-radius: var(--radius-full, 9999px);
  font-size: 0.75rem;
  font-weight: 600;
  letter-spacing: 0.02em;
  text-transform: uppercase;
}

/* Corpo */
.card-body {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  padding: clamp(1rem, 3vw, 1.5rem);
  flex: 1;
}

.card-meta {
  display: flex;
  gap: 0.5rem;
  font-size: var(--text-sm, 0.875rem);
  color: var(--color-text-muted, rgba(255,255,255,0.3));
}

.card-title {
  font-size: var(--text-lg, 1.125rem);
  font-weight: 600;
  line-height: 1.3;
  color: var(--color-text-primary, rgba(255,255,255,0.95));
  /* Clamp para 2 linhas */
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.card-description {
  font-size: var(--text-sm, 0.875rem);
  line-height: 1.6;
  color: var(--color-text-secondary, rgba(255,255,255,0.55));
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

/* Tags */
.card-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.375rem;
  margin-top: auto;
  padding-top: 0.5rem;
}

.card-tag {
  padding: 0.2rem 0.5rem;
  background: rgba(255,255,255,0.06);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-sm, 4px);
  font-size: 0.75rem;
  color: var(--color-text-secondary);
}

/* Arrow */
.card-arrow {
  position: absolute;
  bottom: 1rem;
  right: 1rem;
  color: var(--color-text-muted);
  transform: translateX(-4px);
  opacity: 0;
  transition:
    transform var(--motion-base, 300ms) var(--ease-smooth),
    opacity   var(--motion-base, 300ms);
}

.card:hover .card-arrow { transform: translateX(0); opacity: 1; }

/* ===== SKELETON ===== */
.skeleton {
  position: relative;
  overflow: hidden;
  background: rgba(255,255,255,0.06);
  border-radius: 4px;
}
.skeleton::after {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(90deg, transparent 25%, rgba(255,255,255,0.08) 50%, transparent 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}

@keyframes shimmer {
  0%   { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}

.card--skeleton { pointer-events: none; }

/* Reduced motion */
@media (prefers-reduced-motion: reduce) {
  .card { transition: none; will-change: auto; }
  .card-image { transition: none; }
  .card-arrow { transition: none; }
  .skeleton::after { animation: none; }
}
```

## Uso

```jsx
// Loading
<CardGrid loading={true} />

// Empty
<CardGrid items={[]} emptyMessage="Nenhum artigo encontrado. Tente outros filtros." />

// Com dados
<CardGrid
  items={[
    {
      id: "1",
      href: "/posts/design-systems",
      image: "/images/post-1.jpg",
      imageAlt: "Imagem sobre design systems",
      badge: "Design",
      title: "Como construir um design system escalável",
      description: "Um guia prático para criar tokens, componentes e documentação que duram.",
      meta: { date: "15 jan 2025", dateISO: "2025-01-15", readTime: "8 min" },
      tags: ["Design System", "CSS", "React"],
    },
    // ...mais items
  ]}
/>
```
