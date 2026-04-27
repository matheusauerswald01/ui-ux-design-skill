# Componente: Data Visualization com Identidade

## 1. Sistema de cores para dados

```js
// Nunca usar cores aleatórias em charts — usar sistema
export const chartColors = {
  // Sequencial (uma variável)
  sequential: {
    primary: ['#e0e7ff','#c7d2fe','#a5b4fc','#818cf8','#6366f1','#4f46e5','#4338ca'],
    success:  ['#dcfce7','#bbf7d0','#86efac','#4ade80','#22c55e','#16a34a','#15803d'],
    warning:  ['#fef9c3','#fef08a','#fde047','#facc15','#eab308','#ca8a04','#a16207'],
    danger:   ['#fee2e2','#fecaca','#fca5a5','#f87171','#ef4444','#dc2626','#b91c1c'],
  },
  // Categórico (múltiplas variáveis)
  categorical: [
    '#6366f1','#22c55e','#f59e0b','#ec4899','#14b8a6','#f97316','#8b5cf6','#06b6d4',
  ],
  // Divergente (positivo/negativo)
  diverging: {
    negative: '#ef4444',
    neutral:  '#94a3b8',
    positive: '#22c55e',
  },
};

// Paleta acessível (daltonismo)
export const accessibleColors = [
  '#0077bb', // azul
  '#ee7733', // laranja
  '#009988', // teal
  '#ee3377', // magenta
  '#cc3311', // vermelho
  '#33bbee', // ciano
  '#bbbbbb', // cinza
];
```

## 2. Recharts com identidade

```jsx
import {
  LineChart, Line, BarChart, Bar, AreaChart, Area,
  XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer,
  Legend, ReferenceLine
} from 'recharts';

// Tooltip customizado com identidade
function CustomTooltip({ active, payload, label, formatter }) {
  if (!active || !payload?.length) return null;
  return (
    <div className="chart-tooltip" role="tooltip">
      <p className="chart-tooltip-label">{label}</p>
      {payload.map((entry, i) => (
        <div key={i} className="chart-tooltip-row">
          <span className="chart-tooltip-dot"
                style={{ background: entry.color }} aria-hidden="true"/>
          <span className="chart-tooltip-name">{entry.name}</span>
          <span className="chart-tooltip-value">
            {formatter ? formatter(entry.value) : entry.value.toLocaleString()}
          </span>
        </div>
      ))}
    </div>
  );
}

// Chart base configurado com identidade
const chartDefaults = {
  // Remoção de bordas desnecessárias
  cartesianGrid: {
    stroke: 'var(--color-border)',
    strokeDasharray: '3 3',
    vertical: false, // apenas linhas horizontais
  },
  xAxis: {
    tick: { fill: 'var(--color-text-muted)', fontSize: 12 },
    axisLine: { stroke: 'var(--color-border)' },
    tickLine: false,
  },
  yAxis: {
    tick: { fill: 'var(--color-text-muted)', fontSize: 12 },
    axisLine: false,
    tickLine: false,
    width: 60,
  },
};

// Área chart premium
function AreaChartPremium({ data, dataKey, color, label, formatter }) {
  return (
    <div className="chart-wrapper">
      <ResponsiveContainer width="100%" height={280}>
        <AreaChart data={data} margin={{ top: 8, right: 8, bottom: 0, left: 0 }}>
          <defs>
            <linearGradient id={`grad-${dataKey}`} x1="0" y1="0" x2="0" y2="1">
              <stop offset="5%" stopColor={color} stopOpacity={0.2}/>
              <stop offset="95%" stopColor={color} stopOpacity={0}/>
            </linearGradient>
          </defs>
          <CartesianGrid {...chartDefaults.cartesianGrid} />
          <XAxis dataKey="label" {...chartDefaults.xAxis} />
          <YAxis {...chartDefaults.yAxis}
                 tickFormatter={formatter || (v => v.toLocaleString())} />
          <Tooltip
            content={<CustomTooltip formatter={formatter} />}
            cursor={{ stroke: 'var(--color-border-strong)', strokeWidth: 1 }}
          />
          <Area
            type="monotone"
            dataKey={dataKey}
            name={label}
            stroke={color}
            strokeWidth={2}
            fill={`url(#grad-${dataKey})`}
            dot={false}
            activeDot={{ r: 4, fill: color, stroke: 'var(--color-bg)', strokeWidth: 2 }}
          />
        </AreaChart>
      </ResponsiveContainer>
    </div>
  );
}

// Bar chart com rótulos
function BarChartPremium({ data, dataKey, color, label, formatter, horizontal }) {
  return (
    <div className="chart-wrapper">
      <ResponsiveContainer width="100%" height={Math.max(280, data.length * 40 + 80)}>
        {horizontal ? (
          <BarChart data={data} layout="vertical" margin={{ top: 0, right: 80, bottom: 0, left: 8 }}>
            <XAxis type="number" {...chartDefaults.xAxis}
                   tickFormatter={formatter || (v => v.toLocaleString())} />
            <YAxis type="category" dataKey="label" {...chartDefaults.yAxis} width={120} />
            <Tooltip content={<CustomTooltip formatter={formatter} />} cursor={{ fill: 'var(--color-border)' }} />
            <Bar dataKey={dataKey} name={label} fill={color} radius={[0,4,4,0]}>
              <LabelList dataKey={dataKey} position="right"
                         formatter={formatter || (v => v.toLocaleString())}
                         style={{ fill:'var(--color-text-secondary)', fontSize:12 }} />
            </Bar>
          </BarChart>
        ) : (
          <BarChart data={data} margin={{ top: 20, right: 8, bottom: 0, left: 0 }}>
            <CartesianGrid {...chartDefaults.cartesianGrid} />
            <XAxis dataKey="label" {...chartDefaults.xAxis} />
            <YAxis {...chartDefaults.yAxis} tickFormatter={formatter || (v => v.toLocaleString())} />
            <Tooltip content={<CustomTooltip formatter={formatter} />} cursor={{ fill: 'var(--color-border)' }} />
            <Bar dataKey={dataKey} name={label} fill={color} radius={[4,4,0,0]}>
              <LabelList dataKey={dataKey} position="top"
                         formatter={formatter || (v => v.toLocaleString())}
                         style={{ fill:'var(--color-text-secondary)', fontSize:12 }} />
            </Bar>
          </BarChart>
        )}
      </ResponsiveContainer>
    </div>
  );
}

// Donut chart com total central
function DonutChart({ data, totalLabel = 'Total' }) {
  const total = data.reduce((sum, d) => sum + d.value, 0);

  return (
    <div className="donut-wrapper">
      <ResponsiveContainer width="100%" height={240}>
        <PieChart>
          <Pie
            data={data}
            cx="50%"
            cy="50%"
            innerRadius={70}
            outerRadius={100}
            paddingAngle={2}
            dataKey="value"
          >
            {data.map((entry, i) => (
              <Cell key={i} fill={chartColors.categorical[i % chartColors.categorical.length]} />
            ))}
          </Pie>
          <Tooltip content={<CustomTooltip formatter={v => `${((v/total)*100).toFixed(1)}%`} />} />
        </PieChart>
      </ResponsiveContainer>
      {/* Total central */}
      <div className="donut-center" aria-hidden="true">
        <span className="donut-total">{total.toLocaleString()}</span>
        <span className="donut-label">{totalLabel}</span>
      </div>
      {/* Legenda acessível */}
      <ul className="chart-legend" role="list">
        {data.map((d, i) => (
          <li key={i} className="chart-legend-item">
            <span className="chart-legend-dot"
                  style={{ background: chartColors.categorical[i % chartColors.categorical.length] }}
                  aria-hidden="true"/>
            <span className="chart-legend-name">{d.name}</span>
            <span className="chart-legend-value">
              {((d.value / total) * 100).toFixed(1)}%
            </span>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

```css
.chart-wrapper { position: relative; }

/* Tooltip */
.chart-tooltip {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  padding: 0.625rem 0.875rem;
  box-shadow: var(--shadow-lg);
  font-size: var(--text-sm);
}
.chart-tooltip-label {
  font-weight: 600; color: var(--color-text-primary); margin-bottom: 0.5rem;
  padding-bottom: 0.5rem; border-bottom: 1px solid var(--color-border);
}
.chart-tooltip-row {
  display: flex; align-items: center; gap: 0.5rem; margin-top: 0.375rem;
}
.chart-tooltip-dot {
  width: 8px; height: 8px; border-radius: 50%; flex-shrink: 0;
}
.chart-tooltip-name { color: var(--color-text-secondary); flex: 1; }
.chart-tooltip-value { font-weight: 500; font-variant-numeric: tabular-nums; }

/* Donut */
.donut-wrapper { position: relative; }
.donut-center {
  position: absolute; top: 50%; left: 50%; transform: translate(-50%, -55%);
  text-align: center; pointer-events: none;
}
.donut-total { font-size: var(--text-2xl); font-weight: 700; display: block; font-variant-numeric: tabular-nums; }
.donut-label { font-size: var(--text-sm); color: var(--color-text-secondary); }

/* Legenda */
.chart-legend { list-style: none; display: flex; flex-direction: column; gap: 0.5rem; margin-top: 1rem; }
.chart-legend-item { display: flex; align-items: center; gap: 0.5rem; font-size: var(--text-sm); }
.chart-legend-dot { width: 10px; height: 10px; border-radius: 2px; flex-shrink: 0; }
.chart-legend-name { flex: 1; color: var(--color-text-secondary); }
.chart-legend-value { font-weight: 500; font-variant-numeric: tabular-nums; }
```

## 3. Metric Cards para dashboards

```jsx
function MetricCard({ label, value, change, format, icon }) {
  const isPositive = change > 0;
  const isNeutral  = change === 0;

  return (
    <motion.div
      className="metric-card"
      initial={{ opacity:0, y:16 }}
      whileInView={{ opacity:1, y:0 }}
      viewport={{ once:true }}
    >
      <div className="metric-header">
        <span className="metric-label">{label}</span>
        {icon && <span className="metric-icon" aria-hidden="true">{icon}</span>}
      </div>
      <div className="metric-value">
        <AnimatedNumber value={value} format={format} />
      </div>
      {change !== undefined && (
        <div className={`metric-change ${isPositive ? 'positive' : isNeutral ? 'neutral' : 'negative'}`}
             aria-label={`${isPositive ? 'Aumento' : 'Queda'} de ${Math.abs(change)}% em relação ao período anterior`}>
          <span aria-hidden="true">{isPositive ? '↑' : isNeutral ? '→' : '↓'}</span>
          {Math.abs(change).toFixed(1)}%
          <span className="metric-period">vs. período anterior</span>
        </div>
      )}
    </motion.div>
  );
}
```

```css
.metric-card {
  background: var(--color-surface); border: 1px solid var(--color-border);
  border-radius: var(--radius-lg); padding: 1.25rem 1.5rem;
  display: flex; flex-direction: column; gap: 0.5rem;
}
.metric-header { display: flex; align-items: center; justify-content: space-between; }
.metric-label { font-size: var(--text-sm); color: var(--color-text-secondary); font-weight: 500; }
.metric-icon { color: var(--color-text-muted); }
.metric-value { font-size: clamp(1.5rem, 4vw, 2rem); font-weight: 700; font-variant-numeric: tabular-nums; }
.metric-change { display: flex; align-items: center; gap: 0.25rem; font-size: var(--text-sm); font-weight: 500; }
.metric-change.positive { color: var(--color-success); }
.metric-change.negative { color: var(--color-error); }
.metric-change.neutral  { color: var(--color-text-muted); }
.metric-period { font-weight: 400; color: var(--color-text-muted); font-size: 11px; margin-left: 2px; }
```
