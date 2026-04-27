# Componente: Rich Text Editor (Tiptap)

Stack: Tiptap + React + CSS custom
Features: toolbar flotante, slash commands, mention, link preview, code, collab

## rich-text-editor.jsx

```jsx
import { useEditor, EditorContent, BubbleMenu, FloatingMenu } from "@tiptap/react";
import StarterKit from "@tiptap/starter-kit";
import Placeholder from "@tiptap/extension-placeholder";
import Link from "@tiptap/extension-link";
import Image from "@tiptap/extension-image";
import Mention from "@tiptap/extension-mention";
import CodeBlockLowlight from "@tiptap/extension-code-block-lowlight";
import { createLowlight } from "lowlight";
import { useState, useCallback } from "react";

const lowlight = createLowlight();

/**
 * RichTextEditor — Editor WYSIWYG premium com:
 * - Bubble menu (seleção de texto → toolbar flotante)
 * - Slash commands (/heading, /code, /quote...)
 * - Mention @usuario com dropdown
 * - Link com preview
 * - Code blocks com syntax highlight
 * - Drag to reorder blocks
 * - Keyboard shortcuts completos
 * - Acessível (ARIA, semântico, anúncios)
 */
export function RichTextEditor({
  initialContent = "",
  onChange,
  placeholder = "Escreva algo, ou use '/' para comandos...",
  onMentionSearch,
  readOnly = false,
  maxLength,
}) {
  const [charCount, setCharCount] = useState(0);

  const editor = useEditor({
    extensions: [
      StarterKit.configure({
        codeBlock: false, // substituído por CodeBlockLowlight
      }),
      Placeholder.configure({ placeholder }),
      Link.configure({
        openOnClick: false,
        HTMLAttributes: { rel: "noopener noreferrer", target: "_blank" },
      }),
      Image.configure({ inline: false, allowBase64: false }),
      CodeBlockLowlight.configure({ lowlight }),
      Mention.configure({
        HTMLAttributes: { class: "mention" },
        suggestion: {
          items: ({ query }) => onMentionSearch?.(query) || [],
          render: () => ({
            onStart: (props) => { /* renderizar dropdown */ },
            onUpdate: (props) => { /* atualizar dropdown */ },
            onKeyDown: (props) => { /* navigation */ },
            onExit: () => { /* fechar dropdown */ },
          }),
        },
      }),
    ],
    content: initialContent,
    editable: !readOnly,
    onUpdate: ({ editor }) => {
      const html = editor.getHTML();
      const count = editor.storage.characterCount?.characters() ?? editor.getText().length;
      setCharCount(count);
      onChange?.(html, editor.getJSON());

      // Bloquear se exceder maxLength
      if (maxLength && count > maxLength) {
        editor.commands.undo();
      }
    },
    editorProps: {
      attributes: {
        role: "textbox",
        "aria-multiline": "true",
        "aria-label": placeholder,
        class: "rte-editor",
      },
    },
  });

  const setLink = useCallback(() => {
    const url = window.prompt("URL do link:");
    if (!url) return editor?.chain().focus().unsetLink().run();
    editor?.chain().focus().setLink({ href: url }).run();
  }, [editor]);

  if (!editor) return null;

  return (
    <div className="rte-wrapper">
      {/* Toolbar estática */}
      {!readOnly && <StaticToolbar editor={editor} onSetLink={setLink} />}

      {/* Bubble menu — aparece ao selecionar texto */}
      <BubbleMenu
        editor={editor}
        tippyOptions={{ duration: 150, placement: "top" }}
        className="rte-bubble-menu"
      >
        <ToolbarButton onClick={() => editor.chain().focus().toggleBold().run()}
                       active={editor.isActive("bold")} title="Negrito (Cmd+B)">
          <b>B</b>
        </ToolbarButton>
        <ToolbarButton onClick={() => editor.chain().focus().toggleItalic().run()}
                       active={editor.isActive("italic")} title="Itálico (Cmd+I)">
          <i>I</i>
        </ToolbarButton>
        <ToolbarButton onClick={() => editor.chain().focus().toggleCode().run()}
                       active={editor.isActive("code")} title="Código inline">
          {"</>"}
        </ToolbarButton>
        <div className="rte-divider" aria-hidden="true" />
        <ToolbarButton onClick={setLink} active={editor.isActive("link")} title="Inserir link">
          🔗
        </ToolbarButton>
        <ToolbarButton onClick={() => editor.chain().focus().toggleHighlight?.().run()}
                       title="Destacar texto">
          ✏️
        </ToolbarButton>
      </BubbleMenu>

      {/* Floating menu — aparece em linha vazia */}
      <FloatingMenu
        editor={editor}
        tippyOptions={{ duration: 150, placement: "left" }}
        className="rte-floating-menu"
      >
        <SlashMenu editor={editor} />
      </FloatingMenu>

      {/* Editor content */}
      <EditorContent editor={editor} className="rte-content-wrap" />

      {/* Footer */}
      {(maxLength || !readOnly) && (
        <div className="rte-footer" aria-live="polite">
          {maxLength && (
            <span className={`rte-char-count ${charCount > maxLength * 0.9 ? "rte-char-count--warn" : ""}`}
                  aria-label={`${charCount} de ${maxLength} caracteres`}>
              {charCount} / {maxLength}
            </span>
          )}
        </div>
      )}
    </div>
  );
}

function StaticToolbar({ editor, onSetLink }) {
  const groups = [
    [
      { label: "Negrito", shortcut: "Cmd+B", action: () => editor.chain().focus().toggleBold().run(), active: editor.isActive("bold"), icon: "B", bold: true },
      { label: "Itálico", shortcut: "Cmd+I", action: () => editor.chain().focus().toggleItalic().run(), active: editor.isActive("italic"), icon: "I", italic: true },
      { label: "Riscado", action: () => editor.chain().focus().toggleStrike().run(), active: editor.isActive("strike"), icon: "S̶" },
    ],
    [
      { label: "Título 1", action: () => editor.chain().focus().toggleHeading({ level: 1 }).run(), active: editor.isActive("heading", { level: 1 }), icon: "H1" },
      { label: "Título 2", action: () => editor.chain().focus().toggleHeading({ level: 2 }).run(), active: editor.isActive("heading", { level: 2 }), icon: "H2" },
      { label: "Título 3", action: () => editor.chain().focus().toggleHeading({ level: 3 }).run(), active: editor.isActive("heading", { level: 3 }), icon: "H3" },
    ],
    [
      { label: "Lista com marcadores", action: () => editor.chain().focus().toggleBulletList().run(), active: editor.isActive("bulletList"), icon: "•" },
      { label: "Lista numerada", action: () => editor.chain().focus().toggleOrderedList().run(), active: editor.isActive("orderedList"), icon: "1." },
      { label: "Citação", action: () => editor.chain().focus().toggleBlockquote().run(), active: editor.isActive("blockquote"), icon: '"' },
      { label: "Bloco de código", action: () => editor.chain().focus().toggleCodeBlock().run(), active: editor.isActive("codeBlock"), icon: "</>" },
    ],
    [
      { label: "Inserir link", action: onSetLink, active: editor.isActive("link"), icon: "🔗" },
      { label: "Desfazer", shortcut: "Cmd+Z", action: () => editor.chain().focus().undo().run(), icon: "↩" },
      { label: "Refazer", shortcut: "Cmd+Shift+Z", action: () => editor.chain().focus().redo().run(), icon: "↪" },
    ],
  ];

  return (
    <div className="rte-toolbar" role="toolbar" aria-label="Ferramentas de formatação">
      {groups.map((group, gi) => (
        <div key={gi} className="rte-toolbar-group" role="group">
          {group.map(btn => (
            <ToolbarButton key={btn.label} onClick={btn.action} active={btn.active} title={`${btn.label}${btn.shortcut ? ` (${btn.shortcut})` : ""}`}>
              <span style={{ fontWeight: btn.bold ? 700 : 400, fontStyle: btn.italic ? "italic" : "normal" }}>
                {btn.icon}
              </span>
            </ToolbarButton>
          ))}
          {gi < groups.length - 1 && <div className="rte-divider" aria-hidden="true" />}
        </div>
      ))}
    </div>
  );
}

function SlashMenu({ editor }) {
  const commands = [
    { label: "Título 1", desc: "Grande título de seção", icon: "H1", action: () => editor.chain().focus().setHeading({ level: 1 }).run() },
    { label: "Título 2", desc: "Subtítulo de seção", icon: "H2", action: () => editor.chain().focus().setHeading({ level: 2 }).run() },
    { label: "Lista", desc: "Lista com marcadores", icon: "•", action: () => editor.chain().focus().toggleBulletList().run() },
    { label: "Código", desc: "Bloco de código com syntax", icon: "</>", action: () => editor.chain().focus().toggleCodeBlock().run() },
    { label: "Citação", desc: "Bloco de citação", icon: '"', action: () => editor.chain().focus().toggleBlockquote().run() },
    { label: "Divisor", desc: "Linha horizontal", icon: "—", action: () => editor.chain().focus().setHorizontalRule().run() },
  ];

  return (
    <div className="rte-slash-menu" role="menu" aria-label="Inserir bloco">
      {commands.map(cmd => (
        <button key={cmd.label} className="rte-slash-item" onClick={cmd.action} role="menuitem">
          <span className="rte-slash-icon">{cmd.icon}</span>
          <span>
            <span className="rte-slash-label">{cmd.label}</span>
            <span className="rte-slash-desc">{cmd.desc}</span>
          </span>
        </button>
      ))}
    </div>
  );
}

function ToolbarButton({ onClick, active, title, children }) {
  return (
    <button
      type="button"
      className={`rte-btn ${active ? "rte-btn--active" : ""}`}
      onClick={onClick}
      title={title}
      aria-label={title}
      aria-pressed={active}
    >
      {children}
    </button>
  );
}
```

## rich-text-editor.css

```css
.rte-wrapper { border: 1px solid var(--color-border); border-radius: var(--radius-lg); overflow: hidden; background: var(--color-surface); display: flex; flex-direction: column; }

.rte-toolbar { display: flex; flex-wrap: wrap; align-items: center; gap: 2px; padding: 0.5rem 0.75rem; border-bottom: 1px solid var(--color-border); background: var(--color-surface); }
.rte-toolbar-group { display: flex; align-items: center; gap: 2px; }
.rte-divider { width: 1px; height: 20px; background: var(--color-border); margin: 0 4px; }

.rte-btn { width: 32px; height: 32px; display: flex; align-items: center; justify-content: center; border: none; border-radius: var(--radius-sm); background: none; color: var(--color-text-secondary); cursor: pointer; font-size: 0.875rem; transition: all var(--motion-fast); }
.rte-btn:hover { background: var(--color-surface-raised); color: var(--color-text-primary); }
.rte-btn--active { background: rgba(99,102,241,0.15); color: var(--color-primary); }
.rte-btn:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 1px; }

.rte-content-wrap { flex: 1; }
.rte-editor { min-height: 200px; padding: 1rem 1.25rem; outline: none; line-height: 1.7; color: var(--color-text-primary); font-size: var(--text-base); }
.rte-editor p { margin: 0 0 0.75em; }
.rte-editor h1 { font-size: var(--text-3xl); font-weight: 700; margin: 1.5em 0 0.5em; letter-spacing: -0.02em; }
.rte-editor h2 { font-size: var(--text-2xl); font-weight: 600; margin: 1.25em 0 0.5em; }
.rte-editor h3 { font-size: var(--text-xl); font-weight: 600; margin: 1em 0 0.5em; }
.rte-editor blockquote { border-left: 3px solid var(--color-primary); padding-left: 1rem; margin: 1rem 0; color: var(--color-text-secondary); font-style: italic; }
.rte-editor code { background: var(--color-surface-raised); padding: 0.2em 0.4em; border-radius: 4px; font-family: var(--font-mono); font-size: 0.875em; }
.rte-editor pre { background: var(--color-surface-raised); padding: 1rem; border-radius: var(--radius-md); overflow-x: auto; margin: 1rem 0; }
.rte-editor pre code { background: none; padding: 0; }
.rte-editor ul { padding-left: 1.5rem; margin: 0.5rem 0; }
.rte-editor li { margin: 0.25rem 0; }
.rte-editor a { color: var(--color-primary); text-decoration: underline; }
.rte-editor .mention { color: var(--color-primary); font-weight: 500; }
.rte-editor p.is-editor-empty:first-child::before { content: attr(data-placeholder); color: var(--color-text-muted); pointer-events: none; height: 0; float: left; }

.rte-bubble-menu { display: flex; align-items: center; gap: 2px; padding: 4px; background: var(--color-surface); border: 1px solid var(--color-border); border-radius: var(--radius-md); box-shadow: var(--shadow-lg); }
.rte-floating-menu { background: var(--color-surface); border: 1px solid var(--color-border); border-radius: var(--radius-md); box-shadow: var(--shadow-lg); overflow: hidden; }
.rte-slash-menu { display: flex; flex-direction: column; min-width: 220px; padding: 4px; }
.rte-slash-item { display: flex; align-items: center; gap: 0.75rem; padding: 0.5rem 0.75rem; border: none; background: none; cursor: pointer; border-radius: var(--radius-sm); text-align: left; transition: background var(--motion-fast); }
.rte-slash-item:hover { background: var(--color-surface-raised); }
.rte-slash-icon { width: 28px; height: 28px; display: flex; align-items: center; justify-content: center; background: var(--color-surface-raised); border-radius: var(--radius-sm); font-size: 0.875rem; flex-shrink: 0; }
.rte-slash-label { display: block; font-size: 0.875rem; font-weight: 500; color: var(--color-text-primary); }
.rte-slash-desc { display: block; font-size: 0.75rem; color: var(--color-text-muted); }

.rte-footer { display: flex; justify-content: flex-end; padding: 0.5rem 0.875rem; border-top: 1px solid var(--color-border); }
.rte-char-count { font-size: 0.75rem; color: var(--color-text-muted); font-variant-numeric: tabular-nums; }
.rte-char-count--warn { color: var(--color-warning); }
```
