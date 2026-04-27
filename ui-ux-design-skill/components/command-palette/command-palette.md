# Componente: Command Palette (Cmd+K)

Stack: React + Framer Motion + Fuse.js
Features: fuzzy search, grupos, recentes, ações, keyboard navigation

## command-palette.jsx

```jsx
"use client";
import { useState, useEffect, useRef, useCallback, useId } from "react";
import { motion, AnimatePresence } from "framer-motion";
import Fuse from "fuse.js";

/**
 * CommandPalette — Power user interface com:
 * - Fuzzy search com Fuse.js
 * - Grupos de comandos (navegação, ações, configurações)
 * - Ações recentes persistidas no localStorage
 * - Keyboard navigation (↑↓ Enter Escape)
 * - Shortcut display (Cmd/Ctrl+K)
 * - Ação de item com callback
 * - Totalmente acessível (ARIA combobox pattern)
 */

export function CommandPalette({ commands, isOpen, onClose }) {
  const [query, setQuery] = useState("");
  const [selected, setSelected] = useState(0);
  const [recents, setRecents] = useState(() => {
    try { return JSON.parse(localStorage.getItem("cmd-recents") || "[]"); }
    catch { return []; }
  });

  const inputRef = useRef(null);
  const listRef = useRef(null);
  const listboxId = useId();

  // Fuzzy search
  const fuse = useRef(new Fuse(commands, {
    keys: ["label", "description", "keywords"],
    threshold: 0.4,
    includeScore: true,
  }));

  const results = query.trim()
    ? fuse.current.search(query).map(r => r.item)
    : recents.length
    ? [
        { id: "__recents__", isHeader: true, label: "Recentes" },
        ...recents,
        { id: "__all__", isHeader: true, label: "Todos os comandos" },
        ...commands,
      ]
    : commands;

  // Agrupar por group
  const grouped = groupResults(results);
  const flatItems = grouped.filter(i => !i.isHeader);

  // Reset ao abrir
  useEffect(() => {
    if (isOpen) {
      setQuery("");
      setSelected(0);
      setTimeout(() => inputRef.current?.focus(), 50);
    }
  }, [isOpen]);

  // Keyboard navigation
  useEffect(() => {
    if (!isOpen) return;
    const handler = (e) => {
      if (e.key === "ArrowDown") {
        e.preventDefault();
        setSelected(s => Math.min(s + 1, flatItems.length - 1));
      } else if (e.key === "ArrowUp") {
        e.preventDefault();
        setSelected(s => Math.max(s - 1, 0));
      } else if (e.key === "Enter") {
        e.preventDefault();
        runAction(flatItems[selected]);
      } else if (e.key === "Escape") {
        onClose();
      }
    };
    window.addEventListener("keydown", handler);
    return () => window.removeEventListener("keydown", handler);
  }, [isOpen, flatItems, selected]);

  // Scroll item selecionado para view
  useEffect(() => {
    const el = listRef.current?.querySelector(`[data-index="${selected}"]`);
    el?.scrollIntoView({ block: "nearest" });
  }, [selected]);

  const runAction = useCallback((item) => {
    if (!item || item.isHeader) return;
    item.action?.();
    // Salvar nos recentes
    const next = [item, ...recents.filter(r => r.id !== item.id)].slice(0, 5);
    setRecents(next);
    try { localStorage.setItem("cmd-recents", JSON.stringify(next)); } catch {}
    onClose();
  }, [recents, onClose]);

  return (
    <AnimatePresence>
      {isOpen && (
        <>
          {/* Backdrop */}
          <motion.div
            className="cmd-backdrop"
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            exit={{ opacity: 0 }}
            transition={{ duration: 0.15 }}
            onClick={onClose}
            aria-hidden="true"
          />

          {/* Palette */}
          <motion.div
            className="cmd-palette"
            role="dialog"
            aria-label="Paleta de comandos"
            aria-modal="true"
            initial={{ opacity: 0, scale: 0.96, y: -10 }}
            animate={{ opacity: 1, scale: 1, y: 0 }}
            exit={{ opacity: 0, scale: 0.96, y: -10 }}
            transition={{ duration: 0.15, ease: [0.16, 1, 0.3, 1] }}
          >
            {/* Input */}
            <div className="cmd-input-wrap">
              <svg className="cmd-search-icon" width="18" height="18" viewBox="0 0 18 18" aria-hidden="true">
                <circle cx="8" cy="8" r="5.5" fill="none" stroke="currentColor" strokeWidth="1.5"/>
                <path d="M12 12l3.5 3.5" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round"/>
              </svg>
              <input
                ref={inputRef}
                type="text"
                className="cmd-input"
                placeholder="Buscar comando..."
                value={query}
                onChange={e => { setQuery(e.target.value); setSelected(0); }}
                aria-label="Buscar comando"
                aria-autocomplete="list"
                aria-controls={listboxId}
                aria-activedescendant={flatItems[selected] ? `cmd-item-${flatItems[selected].id}` : undefined}
                role="combobox"
                aria-expanded="true"
                autoComplete="off"
                spellCheck="false"
              />
              <kbd className="cmd-shortcut" aria-hidden="true">ESC</kbd>
            </div>

            {/* Results */}
            <div
              ref={listRef}
              id={listboxId}
              className="cmd-list"
              role="listbox"
              aria-label="Comandos disponíveis"
            >
              {grouped.length === 0 && (
                <div className="cmd-empty" role="status">
                  <span>Nenhum resultado para "<strong>{query}</strong>"</span>
                </div>
              )}

              {grouped.map((item, i) => {
                if (item.isHeader) {
                  return (
                    <div key={item.id} className="cmd-group-label" role="presentation">
                      {item.label}
                    </div>
                  );
                }

                const flatIdx = flatItems.indexOf(item);
                return (
                  <div
                    key={item.id}
                    id={`cmd-item-${item.id}`}
                    role="option"
                    aria-selected={flatIdx === selected}
                    data-index={flatIdx}
                    className={`cmd-item ${flatIdx === selected ? "cmd-item--selected" : ""}`}
                    onClick={() => runAction(item)}
                    onMouseEnter={() => setSelected(flatIdx)}
                  >
                    <span className="cmd-item-icon" aria-hidden="true">
                      {item.icon}
                    </span>
                    <span className="cmd-item-content">
                      <span className="cmd-item-label">{highlightMatch(item.label, query)}</span>
                      {item.description && (
                        <span className="cmd-item-desc">{item.description}</span>
                      )}
                    </span>
                    {item.shortcut && (
                      <span className="cmd-item-shortcut" aria-label={`Atalho: ${item.shortcut}`}>
                        {item.shortcut.split("+").map(k => (
                          <kbd key={k}>{k}</kbd>
                        ))}
                      </span>
                    )}
                  </div>
                );
              })}
            </div>

            {/* Footer */}
            <div className="cmd-footer" aria-hidden="true">
              <span><kbd>↑</kbd><kbd>↓</kbd> navegar</span>
              <span><kbd>↵</kbd> executar</span>
              <span><kbd>ESC</kbd> fechar</span>
            </div>
          </motion.div>
        </>
      )}
    </AnimatePresence>
  );
}

function groupResults(items) {
  const groups = new Map();
  items.forEach(item => {
    if (item.isHeader) { groups.set(item.id, [item]); return; }
    const g = item.group || "Geral";
    if (!groups.has(g)) groups.set(g, [{ id: `hdr-${g}`, isHeader: true, label: g }]);
    groups.get(g).push(item);
  });
  return Array.from(groups.values()).flat();
}

function highlightMatch(text, query) {
  if (!query.trim()) return text;
  const regex = new RegExp(`(${query.replace(/[.*+?^${}()|[\]\\]/g, "\\$&")})`, "gi");
  const parts = text.split(regex);
  return parts.map((p, i) =>
    regex.test(p) ? <mark key={i} className="cmd-highlight">{p}</mark> : p
  );
}

// Hook global para Cmd+K
export function useCommandPalette() {
  const [open, setOpen] = useState(false);
  useEffect(() => {
    const handler = (e) => {
      if ((e.metaKey || e.ctrlKey) && e.key === "k") {
        e.preventDefault();
        setOpen(o => !o);
      }
    };
    window.addEventListener("keydown", handler);
    return () => window.removeEventListener("keydown", handler);
  }, []);
  return { open, setOpen, close: () => setOpen(false) };
}
```

## command-palette.css

```css
.cmd-backdrop {
  position: fixed; inset: 0; z-index: var(--z-modal, 300);
  background: rgba(0,0,0,0.5);
  backdrop-filter: blur(4px);
}

.cmd-palette {
  position: fixed;
  top: 20%;
  left: 50%;
  transform: translateX(-50%);
  width: min(640px, calc(100vw - 2rem));
  max-height: 60vh;
  z-index: calc(var(--z-modal, 300) + 1);
  background: var(--color-surface);
  border: 1px solid var(--color-border-strong, rgba(255,255,255,0.15));
  border-radius: var(--radius-xl, 16px);
  box-shadow: 0 24px 64px rgba(0,0,0,0.5), 0 0 0 1px rgba(255,255,255,0.05) inset;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.cmd-input-wrap {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 1rem 1.25rem;
  border-bottom: 1px solid var(--color-border);
}
.cmd-search-icon { color: var(--color-text-muted); flex-shrink: 0; }
.cmd-input {
  flex: 1; background: none; border: none; outline: none;
  font-size: 1rem; color: var(--color-text-primary);
  font-family: inherit;
}
.cmd-input::placeholder { color: var(--color-text-muted); }
.cmd-shortcut {
  font-size: 11px; padding: 2px 6px;
  background: var(--color-surface-raised);
  border: 1px solid var(--color-border);
  border-radius: 4px; color: var(--color-text-muted);
}

.cmd-list {
  flex: 1; overflow-y: auto; padding: 0.5rem;
  scrollbar-width: thin;
}
.cmd-group-label {
  padding: 0.375rem 0.75rem;
  font-size: 11px; font-weight: 600;
  color: var(--color-text-muted);
  text-transform: uppercase; letter-spacing: 0.06em;
  margin-top: 0.25rem;
}
.cmd-item {
  display: flex; align-items: center; gap: 0.75rem;
  padding: 0.625rem 0.75rem;
  border-radius: var(--radius-md, 8px);
  cursor: pointer;
  transition: background var(--motion-fast, 100ms);
}
.cmd-item--selected { background: var(--color-surface-raised); }
.cmd-item-icon { font-size: 1.1rem; flex-shrink: 0; width: 24px; text-align: center; }
.cmd-item-content { flex: 1; overflow: hidden; }
.cmd-item-label { font-size: 0.9rem; color: var(--color-text-primary); display: block; }
.cmd-item-desc { font-size: 0.75rem; color: var(--color-text-muted); display: block; }
.cmd-item-shortcut { display: flex; gap: 2px; }
.cmd-item-shortcut kbd {
  font-size: 10px; padding: 1px 5px;
  background: var(--color-surface-raised);
  border: 1px solid var(--color-border);
  border-radius: 3px; color: var(--color-text-muted);
}
.cmd-highlight { background: rgba(99,102,241,0.25); color: var(--color-primary); border-radius: 2px; }
.cmd-empty { padding: 2rem; text-align: center; color: var(--color-text-muted); font-size: 0.9rem; }
.cmd-footer {
  display: flex; gap: 1.25rem; padding: 0.625rem 1.25rem;
  border-top: 1px solid var(--color-border);
  font-size: 11px; color: var(--color-text-muted);
}
.cmd-footer kbd {
  font-size: 10px; padding: 1px 5px;
  background: var(--color-surface-raised);
  border: 1px solid var(--color-border); border-radius: 3px;
}
```

## Uso

```jsx
const commands = [
  {
    id: "nav-home", label: "Ir para Início", icon: "🏠",
    group: "Navegação", shortcut: "G+H",
    action: () => router.push("/"),
  },
  {
    id: "nav-settings", label: "Configurações", icon: "⚙️",
    group: "Navegação", shortcut: "G+S",
    description: "Conta, preferências e segurança",
    action: () => router.push("/settings"),
  },
  {
    id: "theme-dark", label: "Ativar Dark Mode", icon: "🌙",
    group: "Aparência",
    action: () => setTheme("dark"),
  },
  {
    id: "create-post", label: "Criar novo post", icon: "✍️",
    group: "Ações", shortcut: "⌘+N",
    action: () => openCreateModal(),
  },
];

function App() {
  const { open, setOpen, close } = useCommandPalette();
  return (
    <>
      <button onClick={() => setOpen(true)}>
        Cmd K <kbd>⌘K</kbd>
      </button>
      <CommandPalette commands={commands} isOpen={open} onClose={close} />
    </>
  );
}
```
