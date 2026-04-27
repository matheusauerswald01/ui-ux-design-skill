# Componentes: Loading, Empty e Error States

Biblioteca completa de estados para todos os tipos de componente.

---

## 1. Skeleton Screens

### Variantes de Skeleton

```jsx
// Primitivo base
function Skeleton({ width = "100%", height = 16, radius = 4, className = "" }) {
  return (
    <div
      className={`skeleton ${className}`}
      style={{ width, height, borderRadius: radius }}
      aria-hidden="true"
    />
  );
}

// Skeletons compostos prontos
function SkeletonText({ lines = 3 }) {
  const widths = ["95%", "85%", "60%"];
  return (
    <div style={{ display: "flex", flexDirection: "column", gap: 8 }}>
      {Array.from({ length: lines }).map((_, i) => (
        <Skeleton key={i} width={widths[i % widths.length]} height={14} />
      ))}
    </div>
  );
}

function SkeletonAvatar({ size = 40 }) {
  return <Skeleton width={size} height={size} radius={9999} />;
}

function SkeletonCard() {
  return (
    <div className="skeleton-card">
      <Skeleton height={200} radius={12} />
      <div style={{ padding: 16, display: "flex", flexDirection: "column", gap: 10 }}>
        <Skeleton width="40%" height={12} />
        <Skeleton width="80%" height={20} />
        <SkeletonText lines={2} />
      </div>
    </div>
  );
}

function SkeletonTable({ rows = 5, cols = 4 }) {
  return (
    <div className="skeleton-table">
      {/* Header */}
      <div className="skeleton-table__row skeleton-table__row--header">
        {Array.from({ length: cols }).map((_, i) => (
          <Skeleton key={i} width={`${60 + i * 5}%`} height={14} />
        ))}
      </div>
      {/* Rows */}
      {Array.from({ length: rows }).map((_, row) => (
        <div key={row} className="skeleton-table__row">
          {Array.from({ length: cols }).map((_, col) => (
            <Skeleton key={col} width={`${50 + (row + col) * 7 % 40}%`} height={14} />
          ))}
        </div>
      ))}
    </div>
  );
}

function SkeletonProfile() {
  return (
    <div style={{ display: "flex", alignItems: "center", gap: 12 }}>
      <SkeletonAvatar size={48} />
      <div style={{ flex: 1, display: "flex", flexDirection: "column", gap: 8 }}>
        <Skeleton width="35%" height={16} />
        <Skeleton width="55%" height={13} />
      </div>
    </div>
  );
}
```

```css
/* CSS do skeleton */
.skeleton {
  position: relative;
  overflow: hidden;
  background: rgba(255,255,255,0.06);
}

.skeleton::after {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(
    90deg,
    transparent 20%,
    rgba(255,255,255,0.08) 50%,
    transparent 80%
  );
  background-size: 200% 100%;
  animation: shimmer 1.8s ease-in-out infinite;
}

@keyframes shimmer {
  0%   { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}

/* Dark mode (light surface) */
@media (prefers-color-scheme: light) {
  .skeleton { background: rgba(0,0,0,0.06); }
  .skeleton::after { background: linear-gradient(90deg, transparent 20%, rgba(0,0,0,0.08) 50%, transparent 80%); background-size: 200% 100%; }
}

/* Tabela */
.skeleton-table { display: flex; flex-direction: column; gap: 0; }
.skeleton-table__row {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
  gap: 16px;
  padding: 12px 16px;
  border-bottom: 1px solid var(--color-border);
}
.skeleton-table__row--header {
  background: rgba(255,255,255,0.02);
  padding-block: 14px;
}

@media (prefers-reduced-motion: reduce) {
  .skeleton::after { animation: none; }
}
```

---

## 2. Empty States — Biblioteca Completa

```jsx
// Componente base
function EmptyState({ illustration, title, description, actions, size = "md" }) {
  const sizes = {
    sm: { padding: "2rem 1.5rem", iconSize: 40, titleSize: "1rem" },
    md: { padding: "4rem 2rem", iconSize: 56, titleSize: "1.125rem" },
    lg: { padding: "6rem 2rem", iconSize: 72, titleSize: "1.25rem" },
  };
  const s = sizes[size];

  return (
    <motion.div
      role="status"
      initial={{ opacity: 0, y: 16 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ duration: 0.4, ease: [0.16, 1, 0.3, 1] }}
      style={{
        display: "flex",
        flexDirection: "column",
        alignItems: "center",
        textAlign: "center",
        gap: "1rem",
        padding: s.padding,
        color: "var(--color-text-secondary)",
      }}
    >
      {illustration && (
        <div style={{ opacity: 0.6, color: "var(--color-text-muted)" }}>
          {illustration}
        </div>
      )}
      <div>
        <h3 style={{ fontSize: s.titleSize, fontWeight: 600, color: "var(--color-text-primary)", marginBottom: "0.5rem" }}>
          {title}
        </h3>
        {description && (
          <p style={{ fontSize: "0.9rem", maxWidth: "36ch", margin: "0 auto" }}>
            {description}
          </p>
        )}
      </div>
      {actions && (
        <div style={{ display: "flex", gap: "0.75rem", flexWrap: "wrap", justifyContent: "center" }}>
          {actions}
        </div>
      )}
    </motion.div>
  );
}

// Variantes prontas por contexto
export const EmptySearch = ({ query, onClear }) => (
  <EmptyState
    illustration={<SearchIllustration />}
    title="Sem resultados"
    description={query ? `Nenhum resultado para "${query}". Tente outros termos.` : "Tente buscar por algo."}
    actions={query && (
      <button className="btn btn-ghost" onClick={onClear}>Limpar busca</button>
    )}
  />
);

export const EmptyList = ({ noun = "itens", onCreate }) => (
  <EmptyState
    illustration={<ListIllustration />}
    title={`Nenhum ${noun} ainda`}
    description={`Comece criando seu primeiro ${noun}.`}
    actions={onCreate && (
      <button className="btn btn-primary" onClick={onCreate}>Criar {noun}</button>
    )}
  />
);

export const EmptyNotifications = () => (
  <EmptyState
    illustration={<BellIllustration />}
    title="Tudo em dia"
    description="Você não tem notificações no momento."
    size="sm"
  />
);

export const EmptyInbox = () => (
  <EmptyState
    illustration={<InboxIllustration />}
    title="Caixa de entrada vazia"
    description="Quando você receber mensagens, elas aparecerão aqui."
  />
);

// Ilustrações SVG simples (substituir por Lottie em produção)
const SearchIllustration = () => (
  <svg width="64" height="64" viewBox="0 0 64 64" fill="none" stroke="currentColor" strokeWidth="2">
    <circle cx="28" cy="28" r="18"/>
    <path d="M42 42l14 14" strokeLinecap="round"/>
    <path d="M22 28h12M28 22v12" strokeLinecap="round"/>
  </svg>
);

const ListIllustration = () => (
  <svg width="64" height="64" viewBox="0 0 64 64" fill="none" stroke="currentColor" strokeWidth="2">
    <rect x="8" y="8" width="48" height="48" rx="8" strokeDasharray="4 4"/>
    <path d="M20 32h24M20 22h16M20 42h20" strokeLinecap="round"/>
  </svg>
);

const BellIllustration = () => (
  <svg width="48" height="48" viewBox="0 0 48 48" fill="none" stroke="currentColor" strokeWidth="2">
    <path d="M24 6C18 6 14 11 14 17v10l-4 6h28l-4-6V17c0-6-4-11-10-11z"/>
    <path d="M20 39a4 4 0 008 0" strokeLinecap="round"/>
  </svg>
);

const InboxIllustration = () => (
  <svg width="64" height="64" viewBox="0 0 64 64" fill="none" stroke="currentColor" strokeWidth="2">
    <rect x="8" y="8" width="48" height="48" rx="8"/>
    <path d="M8 36h12l6 8 6-8h12" strokeLinecap="round" strokeLinejoin="round"/>
  </svg>
);
```

---

## 3. Error States — Variantes

```jsx
// Base
function ErrorState({ type = "generic", error, onRetry, onHome }) {
  const messages = {
    generic:  { title: "Algo deu errado", desc: "Não foi possível carregar o conteúdo." },
    network:  { title: "Sem conexão",     desc: "Verifique sua conexão com a internet." },
    notFound: { title: "Não encontrado",  desc: "Esta página não existe ou foi movida." },
    forbidden:{ title: "Acesso negado",   desc: "Você não tem permissão para acessar." },
    server:   { title: "Erro do servidor",desc: "Estamos trabalhando para resolver. Tente em breve." },
  };

  const msg = messages[type] || messages.generic;

  return (
    <motion.div
      role="alert"
      aria-live="assertive"
      initial={{ opacity: 0, scale: 0.96 }}
      animate={{ opacity: 1, scale: 1 }}
      transition={{ duration: 0.3, ease: [0.16, 1, 0.3, 1] }}
      style={{
        display: "flex",
        flexDirection: "column",
        alignItems: "center",
        textAlign: "center",
        gap: "1.25rem",
        padding: "3rem 2rem",
        border: "1px solid rgba(239,68,68,0.15)",
        borderRadius: 16,
        background: "rgba(239,68,68,0.04)",
      }}
    >
      <div style={{ color: "var(--color-error, #ef4444)", opacity: 0.8 }}>
        <ErrorIllustration type={type} />
      </div>
      <div>
        <h3 style={{ fontWeight: 600, marginBottom: "0.5rem" }}>{msg.title}</h3>
        <p style={{ color: "var(--color-text-secondary)", fontSize: "0.9rem" }}>
          {error?.message || msg.desc}
        </p>
      </div>
      <div style={{ display: "flex", gap: "0.75rem", flexWrap: "wrap", justifyContent: "center" }}>
        {onRetry && (
          <button className="btn btn-primary" onClick={onRetry}>Tentar novamente</button>
        )}
        {onHome && (
          <button className="btn btn-ghost" onClick={onHome}>Ir para início</button>
        )}
        {!onRetry && !onHome && (
          <button className="btn btn-ghost" onClick={() => window.location.reload()}>
            Recarregar página
          </button>
        )}
      </div>
    </motion.div>
  );
}

// Error boundary para React
class ErrorBoundary extends React.Component {
  constructor(props) { super(props); this.state = { hasError: false, error: null }; }
  static getDerivedStateFromError(error) { return { hasError: true, error }; }
  componentDidCatch(error, info) { console.error("ErrorBoundary:", error, info); }
  render() {
    if (this.state.hasError) {
      return (
        <ErrorState
          error={this.state.error}
          onRetry={() => this.setState({ hasError: false, error: null })}
        />
      );
    }
    return this.props.children;
  }
}
```

---

## 4. Toast System

```jsx
import { AnimatePresence } from "framer-motion";
import { createContext, useContext, useState, useCallback } from "react";

const ToastContext = createContext(null);

export function ToastProvider({ children }) {
  const [toasts, setToasts] = useState([]);

  const show = useCallback(({ message, type = "info", duration = 4000 }) => {
    const id = Date.now();
    setToasts(prev => [...prev, { id, message, type }]);
    if (duration > 0) {
      setTimeout(() => setToasts(prev => prev.filter(t => t.id !== id)), duration);
    }
    return id;
  }, []);

  const dismiss = useCallback((id) => {
    setToasts(prev => prev.filter(t => t.id !== id));
  }, []);

  return (
    <ToastContext.Provider value={{ show, dismiss }}>
      {children}
      <div
        className="toast-container"
        aria-live="polite"
        aria-atomic="false"
        style={{
          position: "fixed", bottom: "1.5rem", right: "1.5rem",
          display: "flex", flexDirection: "column", gap: "0.5rem",
          zIndex: "var(--z-toast, 400)",
          maxWidth: "min(360px, calc(100vw - 2rem))",
        }}
      >
        <AnimatePresence>
          {toasts.map(toast => (
            <Toast key={toast.id} {...toast} onClose={() => dismiss(toast.id)} />
          ))}
        </AnimatePresence>
      </div>
    </ToastContext.Provider>
  );
}

export const useToast = () => useContext(ToastContext);

function Toast({ message, type, onClose }) {
  const icons = {
    success: "✓", error: "✕", warning: "!", info: "i"
  };
  const colors = {
    success: "var(--color-success, #22c55e)",
    error:   "var(--color-error, #ef4444)",
    warning: "var(--color-warning, #f59e0b)",
    info:    "var(--color-info, #3b82f6)",
  };

  return (
    <motion.div
      role="status"
      initial={{ opacity: 0, y: 20, scale: 0.9 }}
      animate={{ opacity: 1, y: 0, scale: 1, transition: { type: "spring", stiffness: 400, damping: 30 } }}
      exit={{ opacity: 0, x: 20, transition: { duration: 0.2 } }}
      style={{
        display: "flex", alignItems: "flex-start", gap: "0.75rem",
        padding: "0.875rem 1rem",
        background: "var(--color-surface, #141414)",
        border: `1px solid ${colors[type]}33`,
        borderLeft: `3px solid ${colors[type]}`,
        borderRadius: 10,
        boxShadow: "0 8px 32px rgba(0,0,0,0.3), 0 0 0 1px rgba(255,255,255,0.04) inset",
        backdropFilter: "blur(8px)",
      }}
    >
      <span
        style={{
          width: 20, height: 20, borderRadius: "50%",
          background: `${colors[type]}22`, color: colors[type],
          display: "flex", alignItems: "center", justifyContent: "center",
          fontSize: "0.7rem", fontWeight: 700, flexShrink: 0,
        }}
        aria-hidden="true"
      >
        {icons[type]}
      </span>
      <span style={{ flex: 1, fontSize: "0.9rem", lineHeight: 1.5 }}>{message}</span>
      <button
        onClick={onClose}
        aria-label="Fechar notificação"
        style={{
          background: "none", border: "none", cursor: "pointer",
          color: "var(--color-text-muted)", padding: 2, flexShrink: 0,
          fontSize: "1rem", lineHeight: 1,
        }}
      >
        ✕
      </button>
    </motion.div>
  );
}

// Uso:
// const toast = useToast();
// toast.show({ message: "Salvo com sucesso!", type: "success" });
// toast.show({ message: "Erro ao salvar.", type: "error", duration: 6000 });
```
