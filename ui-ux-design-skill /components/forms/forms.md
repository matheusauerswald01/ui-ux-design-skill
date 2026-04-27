# Componente: Formulários Complexos

## 1. Sistema de campos com todos os estados

```css
/* Estados de campo */
.field { display: flex; flex-direction: column; gap: 0.375rem; }

.field-label {
  font-size: var(--text-sm); font-weight: 500;
  color: var(--color-text-primary);
}
.field-label.required::after {
  content: " *"; color: var(--color-error); font-weight: 400;
}

.field-input {
  padding: 0.625rem 0.875rem;
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  background: var(--color-surface);
  color: var(--color-text-primary);
  font-size: max(16px, 1rem); /* evita zoom iOS */
  line-height: 1.5;
  transition: border-color var(--motion-fast) var(--ease-smooth),
              box-shadow var(--motion-fast) var(--ease-smooth);
  width: 100%;
}

/* Estados */
.field-input:hover:not(:disabled) { border-color: var(--color-border-strong); }
.field-input:focus-visible {
  outline: none;
  border-color: var(--color-primary);
  box-shadow: 0 0 0 3px rgba(99,102,241,0.15);
}
.field-input:disabled {
  opacity: 0.5; cursor: not-allowed;
  background: var(--color-surface-raised);
}
.field-input.is-error {
  border-color: var(--color-error);
  box-shadow: 0 0 0 3px rgba(239,68,68,0.15);
}
.field-input.is-success {
  border-color: var(--color-success);
  box-shadow: 0 0 0 3px rgba(34,197,94,0.10);
}

.field-hint { font-size: var(--text-sm); color: var(--color-text-muted); }
.field-error { font-size: var(--text-sm); color: var(--color-error); }
.field-success { font-size: var(--text-sm); color: var(--color-success); }
```

## 2. Componente de campo com validação em tempo real

```jsx
function Field({
  name, label, type = "text", placeholder, required,
  validate, hint, value, onChange
}) {
  const [touched, setTouched] = useState(false);
  const [error, setError] = useState(null);
  const [success, setSuccess] = useState(false);
  const id = `field-${name}`;
  const errorId = `${id}-error`;
  const hintId = `${id}-hint`;

  const handleChange = (e) => {
    const val = e.target.value;
    onChange(val);
    if (touched && validate) {
      const err = validate(val);
      setError(err);
      setSuccess(!err && val.length > 0);
    }
  };

  const handleBlur = () => {
    setTouched(true);
    if (validate) {
      const err = validate(value);
      setError(err);
      setSuccess(!err && value.length > 0);
    }
  };

  const status = error && touched ? 'is-error' : success ? 'is-success' : '';

  return (
    <div className="field">
      <label htmlFor={id} className={`field-label${required ? ' required' : ''}`}>
        {label}
      </label>
      <div style={{ position: 'relative' }}>
        <input
          id={id}
          type={type}
          name={name}
          value={value}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder={placeholder}
          required={required}
          className={`field-input ${status}`}
          aria-required={required}
          aria-invalid={!!error && touched}
          aria-describedby={[errorId, hintId].filter(Boolean).join(' ')}
        />
        {/* Ícone de status */}
        {touched && (
          <span style={{ position:'absolute', right:'0.75rem', top:'50%',
                         transform:'translateY(-50%)', pointerEvents:'none' }}
                aria-hidden="true">
            {error ? '✕' : success ? '✓' : null}
          </span>
        )}
      </div>
      {error && touched && (
        <p id={errorId} className="field-error" role="alert">{error}</p>
      )}
      {!error && hint && (
        <p id={hintId} className="field-hint">{hint}</p>
      )}
    </div>
  );
}

// Validadores padrão
export const validators = {
  required: (msg = 'Campo obrigatório') => (v) => !v?.trim() ? msg : null,
  email: (v) => !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(v) ? 'E-mail inválido' : null,
  minLength: (n) => (v) => v?.length < n ? `Mínimo ${n} caracteres` : null,
  maxLength: (n) => (v) => v?.length > n ? `Máximo ${n} caracteres` : null,
  pattern: (re, msg) => (v) => !re.test(v) ? msg : null,
  match: (other, msg = 'Os campos não coincidem') => (v) => v !== other ? msg : null,
  compose: (...fns) => (v) => fns.reduce((err, fn) => err || fn(v), null),
};
```

## 3. Multi-step form com progresso visual

```jsx
function MultiStepForm({ steps, onSubmit }) {
  const [currentStep, setCurrentStep] = useState(0);
  const [formData, setFormData] = useState({});
  const [errors, setErrors] = useState({});
  const isFirst = currentStep === 0;
  const isLast = currentStep === steps.length - 1;

  const updateData = (key, value) =>
    setFormData(prev => ({ ...prev, [key]: value }));

  const validateStep = () => {
    const step = steps[currentStep];
    const newErrors = {};
    step.fields?.forEach(field => {
      if (field.validate) {
        const err = field.validate(formData[field.name]);
        if (err) newErrors[field.name] = err;
      }
    });
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const next = () => { if (validateStep()) setCurrentStep(s => s + 1); };
  const back = () => setCurrentStep(s => s - 1);
  const submit = () => { if (validateStep()) onSubmit(formData); };

  return (
    <div className="multistep-form">
      {/* Barra de progresso */}
      <StepProgress steps={steps} current={currentStep} />

      {/* Conteúdo do step atual */}
      <AnimatePresence mode="wait">
        <motion.div
          key={currentStep}
          initial={{ opacity: 0, x: 20 }}
          animate={{ opacity: 1, x: 0 }}
          exit={{ opacity: 0, x: -20 }}
          transition={{ duration: 0.25, ease: [0.4,0,0.2,1] }}
          className="step-content"
        >
          <h2 className="step-title">{steps[currentStep].title}</h2>
          {steps[currentStep].description && (
            <p className="step-desc">{steps[currentStep].description}</p>
          )}
          {steps[currentStep].component?.({
            data: formData, errors, updateData
          })}
        </motion.div>
      </AnimatePresence>

      {/* Navegação */}
      <div className="step-nav">
        {!isFirst && (
          <button className="btn btn-ghost" onClick={back} type="button">
            Voltar
          </button>
        )}
        <button
          className="btn btn-primary"
          onClick={isLast ? submit : next}
          type="button"
        >
          {isLast ? 'Concluir' : 'Próximo'}
        </button>
      </div>
    </div>
  );
}

function StepProgress({ steps, current }) {
  return (
    <nav aria-label="Progresso do formulário" className="step-progress">
      <ol style={{ display:'flex', alignItems:'center', gap:'0', listStyle:'none' }}>
        {steps.map((step, i) => (
          <li key={i} style={{ display:'flex', alignItems:'center', flex: i < steps.length-1 ? 1 : 0 }}>
            <div
              className={`step-dot ${i < current ? 'done' : i === current ? 'active' : 'pending'}`}
              aria-current={i === current ? 'step' : undefined}
              aria-label={`Passo ${i+1}: ${step.title} — ${
                i < current ? 'concluído' : i === current ? 'atual' : 'pendente'}`}
            >
              {i < current ? '✓' : i + 1}
            </div>
            {i < steps.length - 1 && (
              <div className={`step-line ${i < current ? 'done' : ''}`} aria-hidden="true" />
            )}
          </li>
        ))}
      </ol>
    </nav>
  );
}
```

```css
/* Multi-step progress */
.step-progress { padding: 0 0 2rem; }

.step-dot {
  width: 32px; height: 32px; border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  font-size: 13px; font-weight: 600; flex-shrink: 0;
  transition: all var(--motion-base) var(--ease-smooth);
}
.step-dot.done {
  background: var(--color-primary); color: white;
}
.step-dot.active {
  background: var(--color-primary); color: white;
  box-shadow: 0 0 0 4px rgba(99,102,241,0.2);
}
.step-dot.pending {
  background: var(--color-surface-raised); color: var(--color-text-muted);
  border: 1px solid var(--color-border);
}

.step-line {
  flex: 1; height: 1px; margin: 0 8px;
  background: var(--color-border);
  transition: background var(--motion-slow) var(--ease-smooth);
}
.step-line.done { background: var(--color-primary); }

.step-nav {
  display: flex; gap: 0.75rem; justify-content: flex-end;
  padding-top: 1.5rem; border-top: 1px solid var(--color-border);
}
```

## 4. Form com auto-save

```jsx
function useAutoSave(data, saveFn, delay = 1000) {
  const [status, setStatus] = useState('idle');

  useEffect(() => {
    if (JSON.stringify(data) === '{}') return;
    setStatus('saving');
    const timer = setTimeout(async () => {
      try {
        await saveFn(data);
        setStatus('saved');
        setTimeout(() => setStatus('idle'), 2000);
      } catch {
        setStatus('error');
      }
    }, delay);
    return () => clearTimeout(timer);
  }, [data]);

  return status;
}

// AutoSave indicator
function SaveStatus({ status }) {
  const messages = {
    saving: '⟳ Salvando...',
    saved:  '✓ Salvo automaticamente',
    error:  '⚠ Erro ao salvar',
    idle:   null,
  };
  if (!messages[status]) return null;
  return (
    <p role="status" aria-live="polite" className={`save-status save-status--${status}`}>
      {messages[status]}
    </p>
  );
}
```

## 5. File Upload com preview

```jsx
function FileUpload({ accept, maxSize = 5 * 1024 * 1024, onUpload }) {
  const [files, setFiles] = useState([]);
  const [dragging, setDragging] = useState(false);
  const inputRef = useRef(null);

  const validate = (file) => {
    if (file.size > maxSize) return `Arquivo muito grande (máx ${maxSize/1024/1024}MB)`;
    return null;
  };

  const handleFiles = (fileList) => {
    const newFiles = Array.from(fileList).map(file => ({
      id: Math.random().toString(36).slice(2),
      file,
      preview: file.type.startsWith('image/') ? URL.createObjectURL(file) : null,
      error: validate(file),
      progress: 0,
    }));
    setFiles(prev => [...prev, ...newFiles]);
    newFiles.filter(f => !f.error).forEach(f => uploadFile(f));
  };

  const uploadFile = async (fileObj) => {
    const formData = new FormData();
    formData.append('file', fileObj.file);
    try {
      const result = await onUpload(formData, (progress) => {
        setFiles(prev => prev.map(f =>
          f.id === fileObj.id ? { ...f, progress } : f
        ));
      });
      setFiles(prev => prev.map(f =>
        f.id === fileObj.id ? { ...f, progress: 100, url: result.url } : f
      ));
    } catch {
      setFiles(prev => prev.map(f =>
        f.id === fileObj.id ? { ...f, error: 'Falha no upload' } : f
      ));
    }
  };

  return (
    <div
      className={`file-upload ${dragging ? 'is-dragging' : ''}`}
      onDragOver={(e) => { e.preventDefault(); setDragging(true); }}
      onDragLeave={() => setDragging(false)}
      onDrop={(e) => {
        e.preventDefault(); setDragging(false);
        handleFiles(e.dataTransfer.files);
      }}
      onClick={() => inputRef.current?.click()}
      role="button"
      tabIndex={0}
      aria-label="Área de upload de arquivo. Clique ou arraste arquivos."
      onKeyDown={(e) => e.key === 'Enter' && inputRef.current?.click()}
    >
      <input
        ref={inputRef}
        type="file"
        accept={accept}
        multiple
        style={{ display: 'none' }}
        onChange={(e) => handleFiles(e.target.files)}
        aria-hidden="true"
      />
      <div className="file-upload__prompt">
        <svg width="32" height="32" viewBox="0 0 32 32" aria-hidden="true">
          <path d="M16 4v16M8 12l8-8 8 8M6 24h20" stroke="currentColor"
                strokeWidth="2" strokeLinecap="round" fill="none"/>
        </svg>
        <p>Arraste arquivos aqui ou <span className="link">clique para selecionar</span></p>
        <p className="file-upload__hint">Máx. {maxSize/1024/1024}MB</p>
      </div>

      {files.length > 0 && (
        <ul className="file-list" onClick={e => e.stopPropagation()}>
          {files.map(f => (
            <li key={f.id} className="file-item">
              {f.preview && (
                <img src={f.preview} alt="" className="file-thumb" />
              )}
              <div className="file-info">
                <span className="file-name">{f.file.name}</span>
                {f.error ? (
                  <span className="file-error">{f.error}</span>
                ) : f.progress < 100 ? (
                  <div className="file-progress">
                    <div style={{ width: `${f.progress}%` }} />
                  </div>
                ) : (
                  <span className="file-success">✓ Enviado</span>
                )}
              </div>
              <button
                onClick={() => setFiles(prev => prev.filter(x => x.id !== f.id))}
                aria-label={`Remover ${f.file.name}`}
                className="file-remove"
              >✕</button>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

```css
.file-upload {
  border: 2px dashed var(--color-border);
  border-radius: var(--radius-lg);
  padding: 2rem;
  text-align: center;
  cursor: pointer;
  transition: border-color var(--motion-fast), background var(--motion-fast);
}
.file-upload:hover, .file-upload.is-dragging {
  border-color: var(--color-primary);
  background: rgba(99,102,241,0.04);
}
.file-upload:focus-visible {
  outline: 2px solid var(--color-primary); outline-offset: 2px;
}
.file-upload__prompt { color: var(--color-text-secondary); }
.file-upload__prompt .link { color: var(--color-primary); text-decoration: underline; }
.file-upload__hint { font-size: var(--text-sm); color: var(--color-text-muted); margin-top: 0.25rem; }
.file-list { list-style: none; margin-top: 1rem; display: flex; flex-direction: column; gap: 0.5rem; }
.file-item {
  display: flex; align-items: center; gap: 0.75rem;
  padding: 0.75rem; background: var(--color-surface-raised);
  border-radius: var(--radius-md); text-align: left;
}
.file-thumb { width: 40px; height: 40px; object-fit: cover; border-radius: 4px; }
.file-info { flex: 1; overflow: hidden; }
.file-name { font-size: var(--text-sm); font-weight: 500; display: block; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.file-progress { height: 4px; background: var(--color-border); border-radius: 2px; margin-top: 4px; }
.file-progress div { height: 100%; background: var(--color-primary); border-radius: 2px; transition: width 0.3s; }
.file-error { font-size: 12px; color: var(--color-error); }
.file-success { font-size: 12px; color: var(--color-success); }
.file-remove { background: none; border: none; cursor: pointer; color: var(--color-text-muted); padding: 4px; border-radius: 4px; }
.file-remove:hover { color: var(--color-error); background: rgba(239,68,68,0.1); }
```
