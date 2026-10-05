# Design tokens — BI de Supermercado (v3.1)

> Únicos valores de cor, raio, sombra, fonte, espaçamento e tempo permitidos.
> Regras de uso: `DESIGN_SYSTEM.md` §2. Implementação de referência: `tela-bi.html` (bloco `1. TOKENS`).
> Se este arquivo e a tela divergirem, vale a tela.

## 1. CSS (copiar como está)

```css
:root{
  color-scheme:light;
  --bg:#FCFCFC; --surface:#FFFFFF; --hover:#F0F0F0; --border:#E6E6E6;
  --text:#171717; --muted:#6B6B6B;
  --brand:#3ECF8E; --brand-text:#0E7C4E; --focus:#0E7C4E;
  --action:#0F6B45; --on-action:#FFFFFF; --on-accent:#171717;
  --warning:#D97706; --warning-text:#B45309;
  --error:#DC2626; --danger-bg:#DC2626;
  --info:#0B8CFF; --magenta:#D6409F; --gray-series:#C9C9C9;
  --series-5:#8B5CF6; --series-6:#0891B2; --series-7:#8D6E63;
  --overlay:rgba(255,255,255,.55);
  --shadow:0 4px 14px rgba(0,0,0,.18);

  --series-1:var(--brand); --series-2:var(--info); --series-3:var(--gray-series); --series-4:var(--magenta);
  --series-other:var(--muted);

  --font-sans:system-ui,-apple-system,"Segoe UI",Roboto,sans-serif;
  --font-mono:ui-monospace,"Source Code Pro",Menlo,monospace;
  --r-chart:4px; --r-control:6px; --r-surface:8px; --r-pill:999px;
  --ease:cubic-bezier(.2,0,0,1);
}
/* Escuro: segue o sistema, salvo escolha manual (data-theme) */
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){
  color-scheme:dark;
  --bg:#171717; --surface:#1C1C1C; --hover:#262626; --border:#2E2E2E;
  --text:#EDEDED; --muted:#8F8F8F; --brand-text:#3ECF8E; --focus:#3ECF8E;
  --warning:#F59E0B; --warning-text:#F59E0B; --error:#E5484D;
  --gray-series:#4A4A4A; --series-5:#A78BFA; --series-6:#06B6D4; --series-7:#A1887F;
  --overlay:rgba(0,0,0,.6);
}}
:root[data-theme="dark"]{
  color-scheme:dark;
  --bg:#171717; --surface:#1C1C1C; --hover:#262626; --border:#2E2E2E;
  --text:#EDEDED; --muted:#8F8F8F; --brand-text:#3ECF8E; --focus:#3ECF8E;
  --warning:#F59E0B; --warning-text:#F59E0B; --error:#E5484D;
  --gray-series:#4A4A4A; --series-5:#A78BFA; --series-6:#06B6D4; --series-7:#A1887F;
  --overlay:rgba(0,0,0,.6);
}
```

Script no `<head>` para aplicar a escolha manual antes da pintura (evita piscar):

```html
<script>try{const t=localStorage.getItem('bi-theme');if(t==='light'||t==='dark')document.documentElement.dataset.theme=t}catch(e){}</script>
```

## 2. JSON (para Tailwind, tema de biblioteca ou outro stack)

```json
{
  "color": {
    "light": {
      "bg": "#FCFCFC", "surface": "#FFFFFF", "hover": "#F0F0F0", "border": "#E6E6E6",
      "text": "#171717", "muted": "#6B6B6B",
      "brand": "#3ECF8E", "brand-text": "#0E7C4E", "focus": "#0E7C4E",
      "action": "#0F6B45", "on-action": "#FFFFFF", "on-accent": "#171717",
      "warning": "#D97706", "warning-text": "#B45309",
      "error": "#DC2626", "danger-bg": "#DC2626", "info": "#0B8CFF",
      "magenta": "#D6409F", "gray-series": "#C9C9C9",
      "series-5": "#8B5CF6", "series-6": "#0891B2", "series-7": "#8D6E63",
      "overlay": "rgba(255,255,255,.55)"
    },
    "dark": {
      "bg": "#171717", "surface": "#1C1C1C", "hover": "#262626", "border": "#2E2E2E",
      "text": "#EDEDED", "muted": "#8F8F8F",
      "brand": "#3ECF8E", "brand-text": "#3ECF8E", "focus": "#3ECF8E",
      "action": "#0F6B45", "on-action": "#FFFFFF", "on-accent": "#171717",
      "warning": "#F59E0B", "warning-text": "#F59E0B",
      "error": "#E5484D", "danger-bg": "#DC2626", "info": "#0B8CFF",
      "magenta": "#D6409F", "gray-series": "#4A4A4A",
      "series-5": "#A78BFA", "series-6": "#06B6D4", "series-7": "#A1887F",
      "overlay": "rgba(0,0,0,.6)"
    }
  },
  "series": ["brand", "info", "gray-series", "magenta", "series-5", "series-6", "series-7"],
  "series-other": "muted",
  "shadow": "0 4px 14px rgba(0,0,0,.18)",
  "radius": { "chart": 4, "control": 6, "surface": 8, "pill": 999 },
  "space": [4, 8, 12, 16, 20, 24, 32, 40, 48, 64],
  "font": {
    "sans": "system-ui, -apple-system, \"Segoe UI\", Roboto, sans-serif",
    "mono": "ui-monospace, \"Source Code Pro\", Menlo, monospace",
    "weights": [400, 500],
    "numeric": "tabular-nums"
  },
  "size": {
    "h1": 24, "h2": 18, "chart-value": 22, "kpi-value": 20, "modal-title": 15,
    "body": 14, "control": 13, "caption": 12, "axis": 11, "mono-label": 11, "badge": 10.5
  },
  "layout": {
    "topbar": 48, "sidebar": 48, "sidebar-open": 202, "subnav": 257,
    "content-max": 1280, "page-padding": [88, 40, 64, 40], "grid-gap": 8,
    "control-h": 28, "field-h": 32, "row-h": 35, "head-h": 36, "kpi-h": 92, "plot-h": 190, "y-axis-w": 52
  },
  "z": { "subnav": 10, "sidebar": 20, "topbar": 30, "popover": 50, "tooltip": 60, "modal": 70, "drawer": 70, "toast": 80 },
  "motion": {
    "ease": "cubic-bezier(.2,0,0,1)",
    "hover": 120, "tooltip": 120, "help-delay": 300, "popover": 120, "submenu-delay": 150,
    "modal": 180, "drawer": 200, "toast-in": 200, "toast-out": 150,
    "sidebar": 200, "sidebar-open-delay": 80, "sidebar-close-delay": 150,
    "skeleton": 1200, "spinner": 700,
    "toast-success": 4000, "toast-info": 4000, "toast-warning": 6000
  }
}
```

## 3. Mapa fixo categoria → cor

| Dimensão | Valor → token |
| --- | --- |
| Seção | Mercearia `series-1` · Hortifruti `series-2` · Padaria `series-3` · Açougue `series-4` · Bebidas `series-5` · Limpeza `series-6` · Laticínios `series-7` · Outros `series-other` |
| Canal | Loja física `series-1` · E-commerce `series-2` · Delivery `series-4` |
| Comparação | Realizado `series-1` · Meta `series-3` · Ano anterior `series-2` · período anterior = mesma cor tracejada |
| Campanha | Promoção `series-1` · Sazonal `series-4` · Institucional `series-2` |
| Meta / estado | ≥ 100% `brand` · 80–99% `warning` · < 80% `error` |
| Sequencial | `color-mix(in srgb, var(--brand) P%, var(--hover))` com P = 10, 30, 50, 75, 100; sem dado = `hover` |

## 4. Uso de texto × gráfico

| Situação | Token |
| --- | --- |
| Texto verde (delta bom, badge ok, link, ✓) | `brand-text` |
| Barra, ponto, linha, borda verde | `brand` |
| Texto âmbar (badge atenção) | `warning-text` |
| Borda, ícone, barra âmbar | `warning` |
| Texto/ícone/barra vermelhos | `error` |
| Fundo do botão destrutivo | `danger-bg` |
| Anel de foco | `focus` |
