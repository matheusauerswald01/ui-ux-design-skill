# Componentes: Kanban + Gantt + Segmentos Adicionais

---

## KANBAN BOARD (@dnd-kit)

```jsx
import {
  DndContext, DragOverlay, closestCorners,
  KeyboardSensor, PointerSensor, useSensor, useSensors,
} from "@dnd-kit/core";
import {
  SortableContext, sortableKeyboardCoordinates,
  verticalListSortingStrategy, useSortable,
} from "@dnd-kit/sortable";
import { CSS } from "@dnd-kit/utilities";

function KanbanBoard({ initialColumns, onUpdate }) {
  const [columns, setColumns] = useState(initialColumns);
  const [activeCard, setActiveCard] = useState(null);

  const sensors = useSensors(
    useSensor(PointerSensor, { activationConstraint: { distance: 5 } }),
    useSensor(KeyboardSensor, { coordinateGetter: sortableKeyboardCoordinates })
  );

  const handleDragStart = (e) => {
    const { active } = e;
    for (const col of columns) {
      const card = col.cards.find(c => c.id === active.id);
      if (card) { setActiveCard(card); break; }
    }
  };

  const handleDragOver = (e) => {
    const { active, over } = e;
    if (!over) return;

    const activeColId = findColumnId(active.id, columns);
    const overColId = over.data.current?.type === "column" ? over.id : findColumnId(over.id, columns);

    if (!activeColId || !overColId || activeColId === overColId) return;

    setColumns(prev => {
      const activeCol = prev.find(c => c.id === activeColId);
      const card = activeCol.cards.find(c => c.id === active.id);

      return prev.map(col => {
        if (col.id === activeColId) return { ...col, cards: col.cards.filter(c => c.id !== active.id) };
        if (col.id === overColId) return { ...col, cards: [...col.cards, card] };
        return col;
      });
    });
  };

  const handleDragEnd = (e) => {
    setActiveCard(null);
    onUpdate?.(columns);
  };

  return (
    <DndContext
      sensors={sensors}
      collisionDetection={closestCorners}
      onDragStart={handleDragStart}
      onDragOver={handleDragOver}
      onDragEnd={handleDragEnd}
    >
      <div className="kanban-board" role="region" aria-label="Kanban board">
        {columns.map(col => (
          <KanbanColumn key={col.id} column={col} />
        ))}
      </div>
      <DragOverlay>
        {activeCard && <KanbanCard card={activeCard} isDragging />}
      </DragOverlay>
    </DndContext>
  );
}

function KanbanColumn({ column }) {
  return (
    <div className="kanban-column" data-type="column">
      <div className="kanban-column-header">
        <h3 className="kanban-column-title">{column.title}</h3>
        <span className="kanban-count" aria-label={`${column.cards.length} cards`}>
          {column.cards.length}
        </span>
      </div>
      <SortableContext items={column.cards.map(c => c.id)} strategy={verticalListSortingStrategy}>
        <div className="kanban-cards" role="list" aria-label={`Cards em ${column.title}`}>
          {column.cards.map(card => (
            <SortableCard key={card.id} card={card} />
          ))}
        </div>
      </SortableContext>
    </div>
  );
}

function SortableCard({ card }) {
  const { attributes, listeners, setNodeRef, transform, transition, isDragging } = useSortable({
    id: card.id,
    data: { type: "card" },
  });

  return (
    <div
      ref={setNodeRef}
      style={{ transform: CSS.Transform.toString(transform), transition, opacity: isDragging ? 0.4 : 1 }}
      role="listitem"
      {...attributes}
      {...listeners}
    >
      <KanbanCard card={card} />
    </div>
  );
}

function KanbanCard({ card, isDragging }) {
  const priorityColors = {
    high: "var(--color-error)",
    medium: "var(--color-warning)",
    low: "var(--color-success)",
  };

  return (
    <div className={`kanban-card ${isDragging ? "kanban-card--dragging" : ""}`}
         aria-label={`Card: ${card.title}. Prioridade: ${card.priority}`}>
      {card.priority && (
        <div className="kanban-priority" style={{ background: priorityColors[card.priority] }}
             aria-hidden="true" />
      )}
      <h4 className="kanban-card-title">{card.title}</h4>
      {card.description && <p className="kanban-card-desc">{card.description}</p>}
      <div className="kanban-card-footer">
        {card.assignee && (
          <img src={card.assignee.avatar} alt={card.assignee.name} className="kanban-avatar" />
        )}
        {card.dueDate && (
          <time className="kanban-due" dateTime={card.dueDate}>
            {new Date(card.dueDate).toLocaleDateString("pt-BR", { day:"2-digit", month:"short" })}
          </time>
        )}
        {card.tags?.map(tag => <span key={tag} className="kanban-tag">{tag}</span>)}
      </div>
    </div>
  );
}

function findColumnId(cardId, columns) {
  for (const col of columns) {
    if (col.cards.find(c => c.id === cardId)) return col.id;
  }
  return null;
}
```

```css
.kanban-board { display: flex; gap: 1rem; overflow-x: auto; padding: 1rem; min-height: 70vh; align-items: flex-start; }
.kanban-column { width: 280px; flex-shrink: 0; background: var(--color-surface-raised); border-radius: var(--radius-lg); padding: 0.875rem; display: flex; flex-direction: column; gap: 0.5rem; }
.kanban-column-header { display: flex; align-items: center; justify-content: space-between; padding-bottom: 0.5rem; border-bottom: 1px solid var(--color-border); }
.kanban-column-title { font-size: 0.875rem; font-weight: 600; }
.kanban-count { font-size: 0.75rem; background: var(--color-border); border-radius: 99px; padding: 1px 7px; color: var(--color-text-secondary); }
.kanban-cards { display: flex; flex-direction: column; gap: 0.5rem; min-height: 100px; }
.kanban-card { background: var(--color-surface); border: 1px solid var(--color-border); border-radius: var(--radius-md); padding: 0.875rem; cursor: grab; transition: box-shadow var(--motion-fast), transform var(--motion-fast); position: relative; overflow: hidden; }
.kanban-card:hover { box-shadow: var(--shadow-md); transform: translateY(-1px); }
.kanban-card--dragging { cursor: grabbing; box-shadow: var(--shadow-xl); transform: rotate(2deg); }
.kanban-priority { position: absolute; top: 0; left: 0; width: 3px; height: 100%; }
.kanban-card-title { font-size: 0.875rem; font-weight: 500; line-height: 1.4; padding-left: 0.5rem; }
.kanban-card-desc { font-size: 0.75rem; color: var(--color-text-secondary); margin-top: 0.375rem; padding-left: 0.5rem; line-height: 1.4; display: -webkit-box; -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden; }
.kanban-card-footer { display: flex; align-items: center; gap: 0.5rem; margin-top: 0.75rem; flex-wrap: wrap; }
.kanban-avatar { width: 24px; height: 24px; border-radius: 50%; object-fit: cover; border: 1.5px solid var(--color-bg); }
.kanban-due { font-size: 0.7rem; color: var(--color-text-muted); margin-left: auto; }
.kanban-tag { font-size: 0.7rem; padding: 1px 6px; background: rgba(99,102,241,0.1); color: var(--color-primary); border-radius: 3px; }
```

---

## GANTT CHART SIMPLIFICADO

```jsx
function GanttChart({ tasks, startDate, endDate }) {
  const [zoom, setZoom] = useState("week"); // day | week | month | quarter

  const days = getDaysBetween(startDate, endDate);
  const colWidth = { day: 40, week: 120, month: 200, quarter: 300 }[zoom];

  const totalWidth = days.length * (colWidth / getDaysInUnit(zoom));

  return (
    <div className="gantt-wrapper">
      {/* Toolbar */}
      <div className="gantt-toolbar">
        <div className="gantt-zoom" role="group" aria-label="Zoom">
          {["day","week","month","quarter"].map(z => (
            <button key={z} className={`gantt-zoom-btn ${zoom === z ? "active" : ""}`}
                    onClick={() => setZoom(z)} aria-pressed={zoom === z}>
              {z === "day" ? "Dia" : z === "week" ? "Semana" : z === "month" ? "Mês" : "Trimestre"}
            </button>
          ))}
        </div>
        <div className="gantt-today-line-label">
          Hoje: {new Date().toLocaleDateString("pt-BR")}
        </div>
      </div>

      <div className="gantt-layout">
        {/* Sidebar com nomes das tarefas */}
        <div className="gantt-sidebar" aria-label="Lista de tarefas">
          <div className="gantt-sidebar-header">Tarefa</div>
          {tasks.map(task => (
            <div key={task.id} className="gantt-task-label" title={task.name}>
              <span className="gantt-task-name">{task.name}</span>
              {task.assignee && <img src={task.assignee.avatar} alt={task.assignee.name} className="gantt-avatar" />}
            </div>
          ))}
        </div>

        {/* Timeline grid */}
        <div className="gantt-timeline" style={{ overflowX: "auto" }}>
          {/* Header de datas */}
          <GanttHeader days={days} zoom={zoom} colWidth={colWidth} />

          {/* Linhas de tarefas */}
          <div className="gantt-rows" style={{ width: totalWidth }}>
            {tasks.map((task, i) => (
              <GanttRow
                key={task.id}
                task={task}
                startDate={startDate}
                totalWidth={totalWidth}
                days={days}
                i={i}
              />
            ))}

            {/* Linha do "Hoje" */}
            <TodayLine startDate={startDate} days={days} totalWidth={totalWidth} />
          </div>
        </div>
      </div>
    </div>
  );
}

function GanttRow({ task, startDate, days, totalWidth }) {
  const start = getDayIndex(startDate, task.startDate);
  const dur = getDaysBetween(task.startDate, task.endDate).length;
  const left = (start / days.length) * 100;
  const width = (dur / days.length) * 100;

  return (
    <div className="gantt-row">
      <div
        className={`gantt-bar ${task.progress === 100 ? "gantt-bar--complete" : ""}`}
        style={{ left: `${left}%`, width: `${width}%` }}
        title={`${task.name}: ${task.startDate} → ${task.endDate} (${task.progress}%)`}
        role="img"
        aria-label={`${task.name}: início ${task.startDate}, término ${task.endDate}, ${task.progress}% concluído`}
      >
        <div className="gantt-bar-progress" style={{ width: `${task.progress}%` }} />
        <span className="gantt-bar-label">{task.name}</span>
      </div>
    </div>
  );
}

function TodayLine({ startDate, days, totalWidth }) {
  const idx = getDayIndex(startDate, new Date().toISOString().split("T")[0]);
  if (idx < 0 || idx >= days.length) return null;
  const left = (idx / days.length) * 100;
  return (
    <div className="gantt-today" style={{ left: `${left}%` }} aria-hidden="true">
      <div className="gantt-today-dot" />
    </div>
  );
}

// Helpers
function getDaysBetween(start, end) {
  const days = [];
  const s = new Date(start);
  const e = new Date(end);
  for (let d = new Date(s); d <= e; d.setDate(d.getDate() + 1)) {
    days.push(new Date(d));
  }
  return days;
}

function getDayIndex(startDate, targetDate) {
  return Math.floor((new Date(targetDate) - new Date(startDate)) / 86400000);
}

function getDaysInUnit(zoom) {
  return { day: 1, week: 7, month: 30, quarter: 90 }[zoom];
}
```

```css
.gantt-wrapper { border: 1px solid var(--color-border); border-radius: var(--radius-lg); overflow: hidden; background: var(--color-surface); }
.gantt-toolbar { display: flex; align-items: center; justify-content: space-between; padding: 0.75rem 1rem; border-bottom: 1px solid var(--color-border); flex-wrap: wrap; gap: 0.5rem; }
.gantt-zoom { display: flex; gap: 2px; }
.gantt-zoom-btn { padding: 4px 10px; font-size: 0.8rem; border: 1px solid var(--color-border); background: none; border-radius: var(--radius-sm); cursor: pointer; color: var(--color-text-secondary); transition: all var(--motion-fast); }
.gantt-zoom-btn.active, .gantt-zoom-btn:hover { background: var(--color-primary); color: white; border-color: var(--color-primary); }
.gantt-layout { display: flex; overflow: hidden; }
.gantt-sidebar { width: 200px; flex-shrink: 0; border-right: 1px solid var(--color-border); }
.gantt-sidebar-header { padding: 0.625rem 0.875rem; font-size: 0.75rem; font-weight: 600; color: var(--color-text-muted); text-transform: uppercase; letter-spacing: 0.05em; border-bottom: 1px solid var(--color-border); }
.gantt-task-label { display: flex; align-items: center; justify-content: space-between; padding: 0.75rem 0.875rem; height: 48px; border-bottom: 1px solid var(--color-border); }
.gantt-task-name { font-size: 0.8rem; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; flex: 1; }
.gantt-avatar { width: 20px; height: 20px; border-radius: 50%; object-fit: cover; flex-shrink: 0; }
.gantt-timeline { flex: 1; overflow-x: auto; }
.gantt-rows { position: relative; }
.gantt-row { height: 48px; border-bottom: 1px solid var(--color-border); position: relative; }
.gantt-bar { position: absolute; top: 50%; transform: translateY(-50%); height: 28px; background: var(--color-primary); border-radius: 4px; overflow: hidden; display: flex; align-items: center; min-width: 20px; cursor: pointer; transition: box-shadow var(--motion-fast); }
.gantt-bar:hover { box-shadow: 0 2px 8px rgba(99,102,241,0.4); }
.gantt-bar--complete { background: var(--color-success); }
.gantt-bar-progress { position: absolute; inset: 0; background: rgba(255,255,255,0.2); transform-origin: left; }
.gantt-bar-label { position: relative; padding: 0 8px; font-size: 0.7rem; white-space: nowrap; color: white; overflow: hidden; text-overflow: ellipsis; }
.gantt-today { position: absolute; top: 0; bottom: 0; width: 2px; background: var(--color-error); opacity: 0.8; pointer-events: none; }
.gantt-today-dot { width: 8px; height: 8px; border-radius: 50%; background: var(--color-error); margin-left: -3px; margin-top: -4px; }
```

---

## SEGMENTOS v4 — Completos

### Climate Tech / Sustainability
```
Valores:  Impacto positivo, transparência de dados, urgência calibrada
Tipo:     Verde terra (não neon), terracota, creme, cinza quente
Tipog:    Fraunces (confiança editorial) + DM Mono (dados precisos)
Layout:   Dados de impacto em destaque, métricas de CO2, progresso visual
Motion:   Suave e orgânico — natural como a natureza
Evitar:   Verde saturado demais (greenwashing), urgência alarmista
KPIs:     CO2 evitado, árvores plantadas, energia renovável %
Refs:     Stripe Climate, Pachama, Tomorrow.io, Watershed
```

### Mental Health / Wellness
```
Valores:  Acolhimento, sem julgamento, segurança, progresso gentil
Tipo:     Sage, terracota suave, lavanda, creme — NUNCA vermelho
Tipog:    Nunito (arredondado, acolhedor) + Source Serif (humanismo)
Layout:   Muito espaço em branco, hierarquia ultra-clara
Motion:   Extremamente suave, 0 flashes, transições quasi-imperceptíveis
Copy:     Primeira pessoa, sem jargão clínico, validação constante
WCAG:     Contraste AAA obrigatório, reduced-motion sempre respeitado
Refs:     Headspace, Woebot, Calm, Bearable, Youper
```

### PropTech / Imóveis
```
Valores:  Confiança, clareza, decisão informada
Tipo:     Neutro premium + accent azul-marinho
Dados:    Mapas interativos, comparação de imóveis, gráficos de preço
Fotos:    Galeria imersiva, tour virtual 360°, planta baixa interativa
CTA:      "Agendar visita" — fricção mínima (apenas nome + telefone)
Refs:     Zillow, Loft, QuintoAndar, Airbnb Rooms
```

### Logistics / Supply Chain
```
Valores:  Precisão, visibilidade em tempo real, eficiência
Tipo:     Industrial — cinza escuro + laranja operacional
Dados:    Mapas de rastreamento, ETAs, status de entrega
Motion:   Animações de status (truck se movendo, package chegando)
Density:  Alta — operadores precisam de informação densa
Refs:     Flexport, Convoy, project44
```

### EdTech avançado — Micro-learning
```
Gamification: Streaks diários (perigo de quebrar), XP, leaderboard
UX:      Sessões de 5 minutos — baixa fricção, alto valor percebido
Mobile:  90% do uso é mobile — thumb-friendly em tudo
Progress: Barra de progresso por lição, % do curso, certificado ao fim
Reward:  Animação de celebração ao completar — peak-end rule
Refs:    Duolingo, Brilliant, Mimo, SoloLearn
```
