# Design System — BI de Supermercado (v3.1)

> **Fonte de verdade:** `design/tela-bi.html` (referência navegável: sistema + documentação viva).
> Este documento descreve exatamente o que ela faz. **Se houver divergência, vale a tela.**
> Tokens prontos para copiar: `design/design-tokens.md`. Instruções ao agente: `CLAUDE.md` (raiz).
>
> **Legenda:** **[Tela]** aprovado na tela original · **[Especificado]** derivado dos tokens, implementado na referência, pode ser usado · **[Pendente]** precisa de decisão antes de usar.

---

## 0. O que é referência e o que é dado

| A referência define (FORMA) | O backend fornece (CONTEÚDO) |
| --- | --- |
| Tokens, layout, componentes, estados, comportamento, formatos de exibição, mapa de cores por categoria | Todos os números, nomes de lojas/produtos/fornecedores, metas, períodos disponíveis, usuário, alertas, listas de filtro, polaridade dos KPIs |

- Os valores que aparecem em `tela-bi.html` são **de demonstração**. Saem de `api.get()` / `FIXTURES`, que simulam o backend com latência, vazio e erro. **Nada disso vai para o produto.**
- **Não construir mockup.** Cada bloco da tela busca seus dados e trata carregando, vazio e erro (§12.6).
- Detalhe do contrato com o backend: §14.

### 0.1 Vocabulário (domínio supermercado)

| Tipo | Termos padrão |
| --- | --- |
| Hierarquia | Rede › Região › Loja › Seção › Categoria › Produto (SKU) |
| Seções | Mercearia, Hortifruti, Padaria, Açougue, Bebidas, Limpeza, Laticínios |
| Métricas | Faturamento, Margem bruta, Ticket médio, Clientes atendidos (cupons), Ruptura de estoque, Perdas e quebras, Giro de estoque, Cobertura (dias) |
| Atores | Fornecedor, Comprador, Gerente de loja |
| Situação | Acima · Na meta · Abaixo (da meta); Ao vivo; Atualizado há X |
| Filtros | Loja, Região, Seção, Fornecedor, Canal (loja física / e-commerce), Período |

As referências visuais (Supabase) definem **forma**, nunca **conteúdo**: não reaproveitar nomes de telas ou produtos delas.

---

## 1. Princípios

1. **Borda antes de sombra.** Hierarquia por contorno de 1 px. Sombra só em tooltip, popover e toast.
2. **Um acento.** Verde é marca e ação primária. Vermelho, laranja e azul só têm função semântica — exceto como cor de série de dados (§6.1).
3. **Rótulo técnico, valor humano.** Rótulo em mono caixa-alta pequeno; valor em sans maior.
4. **Nada pula.** Sidebar, modal, tooltip e popover nunca deslocam o layout.
5. **Contexto sob demanda.** Explicações em tooltips (`?` e `i`), nunca no corpo da página.
6. **Tudo por token.** Nenhuma cor, raio ou espaçamento fora das tabelas abaixo.

---

## 2. Foundations

### 2.1 Cores

| Token | Claro | Escuro | Uso |
| --- | --- | --- | --- |
| `bg` | #FCFCFC | #171717 | Fundo da página, topbar, sidebar, menu secundário |
| `surface` | #FFFFFF | #1C1C1C | Cards, modal, drawer, tooltip, popover, toast |
| `hover` | #F0F0F0 | #262626 | Hover, item ativo, segmento ativo, trilhos, skeleton |
| `border` | #E6E6E6 | #2E2E2E | Todas as bordas e divisores |
| `text` | #171717 | #EDEDED | Texto principal, valores |
| `muted` | #6B6B6B | #8F8F8F | Rótulos, descrições, eixos, tipos de coluna |
| `brand` | #3ECF8E | #3ECF8E | Logo, “saudável”, gráficos positivos, série 1, checkbox/switch |
| `brand-text` | #0E7C4E | #3ECF8E | **Texto** verde: delta positivo, badge ok, links, ✓ de menu |
| `focus` | #0E7C4E | #3ECF8E | Anel de foco de 2 px |
| `action` | #0F6B45 | #0F6B45 | Fundo do botão primário; início/fim no calendário |
| `on-action` | #FFFFFF | #FFFFFF | Texto sobre `action` |
| `on-accent` | #171717 | #171717 | Texto sobre áreas preenchidas com `brand` forte |
| `warning` | #D97706 | #F59E0B | Borda/ícone/gráfico de atenção |
| `warning-text` | #B45309 | #F59E0B | **Texto** de badge de atenção |
| `error` | #DC2626 | #E5484D | Delta negativo, erro, ponto de notificação |
| `danger-bg` | #DC2626 | #DC2626 | Fundo do botão destrutivo (texto #FFF) |
| `info` | #0B8CFF | #0B8CFF | Toast informativo, série 2 |
| `magenta` | #D6409F | #D6409F | Série 4 |
| `gray-series` | #C9C9C9 | #4A4A4A | Série 3 |
| `series-5` | #8B5CF6 | #A78BFA | Série 5 |
| `series-6` | #0891B2 | #06B6D4 | Série 6 |
| `series-7` | #8D6E63 | #A1887F | Série 7 |
| `series-other` | = `muted` | = `muted` | “Outros” |
| `overlay` | rgba(255,255,255,.55) | rgba(0,0,0,.6) | Fundo de modal/drawer (+ blur 3 px) |
| `shadow` | 0 4px 14px rgba(0,0,0,.18) | idem | Tooltip, popover, toast |

**Tema:** dois temas com os mesmos nomes de token. Segue o sistema operacional por padrão; o botão da topbar (ou Configurações › Aparência) alterna e **a escolha manual prevalece e fica salva**. Nunca usar cor fixa fora dos tokens.

**Contraste:** `brand` sobre branco tem 2:1 e `warning` 3,1:1 — por isso texto usa `brand-text` (5,2:1) e `warning-text` (5:1). `brand`/`warning` continuam em gráficos, pontos e bordas.

### 2.2 Tipografia

- **Sans:** `system-ui, -apple-system, "Segoe UI", Roboto, sans-serif`. Base 14 px / 1.45. **Números tabulares** em toda a interface.
- **Mono:** `ui-monospace, "Source Code Pro", Menlo, monospace`.
- **Pesos:** somente 400 e 500.

| Papel | Tamanho | Peso | Cor |
| --- | --- | --- | --- |
| Título de página (H1) | 24 | 500 | text |
| Título de seção (H2) | 18 | 500 | text |
| Valor de gráfico (destaque) | 22 | 500 | text |
| Valor de KPI | 20 | 500 | text |
| Título de modal / drawer | 15 | 500 | text |
| Corpo, células, botões | 13–14 | 400 | text |
| Descrição / subtítulo | 13–14 | 400 | muted |
| Legenda, tooltip, delta, timestamp | 12 | 400 | muted (valores em text) |
| Eixos | 11 | 400 | muted |
| Rótulo mono (KPI, grupos) | 11, tracking .08em, caixa-alta | 400 | muted |
| Badge | 10,5 mono, tracking .06em, caixa-alta | 400 | muted ou semântica |

### 2.3 Espaçamento, raios, ícones, camadas

- **Escala (base 4):** 4 · 8 · 12 · 16 · 20 · 24 · 32 · 40 · 48 · 64.
- **Raios:** **4** (só gráficos) · **6** (botões, inputs, itens de menu, segmented) · **8** (cards, modal, drawer, tooltip, popover, toast) · **total** (pills, badges, botões circulares, switch).
- **Bordas:** sempre 1 px `border`.
- **Ícones:** traço 1,6 px, cantos arredondados, 18 px, cor herdada. Status: 16 px com cor semântica. Botão circular: 32 px.
- **Z-index:** menu secundário 10 · sidebar 20 · topbar 30 · popover 50 · tooltip 60 · modal/drawer 70 · toast 80.

---

## 3. Estrutura da página [Tela]

- **Topbar** fixa, 48 px, padding 0 16, gap 10, borda inferior, fundo `bg`.
  Esquerda: logo 22 px › rede + badge `Matriz` › setor › loja + badge `Ao vivo` (atenção) · botão pill “Visões salvas”.
  Direita: “Feedback” (texto muted) · busca pill 210 px com `Ctrl K` · botão de tema · sino com ponto vermelho · avatar (fundo `text`, letra `bg`). Todo o conteúdo da topbar vem da sessão (backend).
- **Sidebar** fixa à esquerda (§4).
- **Menu secundário** (opcional, só em módulos com subníveis): 257 px, à direita da sidebar.
- **Conteúdo:** margem esquerda 48 (305 com menu secundário), padding 88 topo · 40 laterais · 64 base; largura máxima 1280.
- **Cabeçalho de página:** H1 (+ badge) e subtítulo muted à esquerda; ações à direita (Ajuda, Atualizar, período). **32 abaixo**; **16** quando seguido de chips de filtro ou abas.
- **Chips de filtro** logo abaixo do cabeçalho, 32 abaixo deles. **Banner** (se houver) logo depois.
- **Seções:** H2 com 40 acima e 16 abaixo.
- **Grids:** KPIs 3 colunas, gap 8. Gráfico + painel 2fr / 1fr, gap 8. Dashboards 2 colunas, gap 8.

## 4. Sidebar [Tela]

| Propriedade | Valor |
| --- | --- |
| Largura recolhida / expandida | 48 / 202 px |
| Comportamento | **Sobrepõe** o conteúdo; nunca empurra |
| Abertura | Hover com atraso de 80 ms, **ou foco de teclado** |
| Fechamento | 150 ms após sair |
| Transição | 200 ms, `cubic-bezier(.2, 0, 0, 1)` |
| Labels | Opacidade 0 → 1; entrada 150 ms com 90 ms de atraso; saída 120 ms; nunca quebram linha |
| Item | Link real (`<a href>`), altura 32, margem 2 / 4, raio 6; célula do ícone 40 px fixa |
| Estados | Padrão muted · hover e ativo: fundo `hover` + texto `text`; ativo com `aria-current="page"` |
| Divisores | 1 px, margem 8 vertical, entre grupos |
| Badge de alerta | Ponto vermelho 7 px no canto do ícone |

**Grupos:** Visão geral · Vendas · Dashboards | Produtos · Fornecedores · Metas | Alertas · Logs | Configurações.
O item **Design System** existe só na referência / ambiente de desenvolvimento — **não levar ao produto**.

**Menu secundário [Especificado]:** 257 px, fundo `bg`, borda direita; título 15/500; grupos em mono caixa-alta; item 32 px, 13 px, raio 6; ativo com fundo `hover`.

---

## 5. Componentes

### Botões [Tela]

| Tipo | Especificação |
| --- | --- |
| Base (secundário) | Altura 28, padding 0 10, raio 6, borda 1 px, 13 px, gap 6; hover = fundo `hover` (120 ms) |
| Primário | Fundo/borda `action`, texto `on-action`, hover brilho +15%. **Um por área** |
| Destrutivo | Fundo `danger-bg`, texto #FFF, hover +10%. Só em confirmações, sempre à direita |
| Pequeno | Altura 24 (ações em toast, banner, paginação) |
| Pill | Raio total (Visões salvas) |
| Circular | 32 px, borda 1 px |
| Texto | Sem borda, muted → text no hover (“Limpar filtros”) |
| Carregando | Spinner 14 px à esquerda do texto + desabilitado |
| Desabilitado | Opacidade 50%, `not-allowed`, sem hover |
| Foco | Anel 2 px `focus`, offset 1 px, em **todos** os controles (`:focus-visible`) |

### Badge [Tela]
Raio total, borda 1 px, padding 3 × 7. Variantes: **neutra** (Matriz, Na meta) · **atenção** borda `warning` + texto `warning-text` (Ao vivo, Novo, Atualizado há X) · **erro** `error` (Abaixo, Crítico) · **ok** borda `brand` + texto `brand-text` (Acima).

### Input de toolbar [Tela]
Altura 28, raio 6, borda 1 px, lupa 14 px à esquerda, 13 px; ocupa o espaço restante da toolbar. Foco: anel no contêiner.

### Segmented [Tela]
Segmentos com borda 1 px e raio 6; ativo com fundo `hover` e texto `text` (Tabela / Gráfico). Alterna a visão sem recarregar dados.

### Cards [Tela]
Fundo `surface`, borda 1 px, raio 8, **sem sombra**. Padding: KPI 14 × 16 · gráfico 16 × 20 × 14 · conteúdo 16 × 20.

### KPI [Tela]
- Card de altura 92. Linha superior: rótulo mono + `?` (14 px, abre ajuda) + `›` à direita (drill-down). Valor 20/500 com 10 px de margem. Hover: borda `muted`.
- **Delta** ao lado do valor, 12 px:
  - **Seta = direção** da variação (↑ subiu, ↓ caiu, → estável).
  - **Cor = significado** = sinal(delta) × polaridade da métrica (vem do backend: +1 maior é melhor, −1 menor é melhor). Ruptura ↑ +0,7 p.p. = **vermelho**; Perdas ↓ −0,2 p.p. = **verde**.
- Valor ausente: `—`; sem delta.

### Tooltips [Tela]
- **Gráfico:** abre sem atraso, segue o cursor (14 px à direita, acima), inverte se faltar espaço (margem 8) e desce se encostar na topbar. Fade 120 ms. Superfície `surface`, borda, raio 8, padding 10 × 12, 12 px, largura mínima 150, sombra.
  Ordem: fuso (muted, quando o eixo é tempo) → período/categoria (500) → uma linha por série (ponto 8 px, nome, **valor 500 + % muted entre parênteses** quando for composição) → divisor → **Total (500)** quando empilhado.
- **Ajuda (`?` e `i`):** mesma superfície, texto corrido, largura máx. 240, **abre após 300 ms**, fecha ao sair; abre também por **foco de teclado** (`?`/`i` são `<button>`).
- Tooltip nunca recebe clique.

### Modal [Tela]
Overlay `overlay` + blur 3 px, centralizado. Caixa 320 (confirmação), raio 8, borda 1 px. Três faixas com divisor: **título** 15/500 (12 × 16) · **corpo** muted (14 × 16) · **rodapé** ações à direita (gap 8, 12 × 16). Segura à esquerda, destrutiva/principal à direita. Entrada 180 ms (sobe 6 px + fade). Fecha com ESC, clique fora ou botão. **Foco preso** dentro do modal e devolvido ao gatilho ao fechar. Extras: §12.7.

### Toast [Tela]
Canto inferior direito (20 px), largura 340, raio 8, borda 1 px, sombra, padding 12 × 14, gap 10. Ícone 16 colorido · título 500 · descrição 13 muted · ação opcional (botão 24) · `×`. Empilha com gap 8, **máximo 3** (o mais antigo sai). Entrada 200 ms (sobe 6 px + fade), saída 150 ms. Duração: **4 s** sucesso/info, **6 s** aviso, **erro e processando persistem**; pausa no hover. Ícone: sucesso `brand` · info `info` · aviso `warning` · erro `error`. Extras: §12.9.

### Tabela [Tela]
- **Toolbar:** padding 8, gap 8, borda inferior; filtro (ocupa o espaço) + Ordenar + ação secundária + **primária à direita**.
- **Cabeçalho:** 36 px, 500; **nome + tipo em 12 px muted** (`meta num`).
- **Linhas:** 35 px, padding 0 12, 13 px, **truncadas com reticências** (máx. 260 px, texto completo no `title`). Hover `hover`.
- **Primeira coluna:** checkbox (`brand`), 40 px; 64 px quando há expansão (checkbox + chevron).
- **Numéricos à direita**, tabulares, moeda pt-BR; ausente `—`.
- **Rodapé:** 8 × 12, 13 px muted, borda superior — `‹ Página [1] de N ›`, seletor de linhas, contagem de registros, segmented à direita.
- Status sempre em **badge**; variação em texto com cor semântica.
- **Clique na linha** abre o drawer de detalhe (§12.5).
- Rolagem horizontal dentro do card.
- Ordenação, seleção em lote e expansão: §12.10.

---

## 6. Gráficos [Tela]

**Anatomia obrigatória (todo gráfico):** título (500) + `i` com ajuda → **valor de destaque** (22/500) → período / fuso (12 muted, + badge “Atualizado há X” se desatualizado) → área de plotagem → legenda → tooltip padrão.

- **Desenho em pixels reais:** o SVG é desenhado na largura do card (re-render no resize). Texto de eixo nunca escala com a largura.
- **Plotagem:** altura 190; eixo Y 52 px, 5 marcas, rótulos `x mil` / `x mi` 11 px muted; **sem gridlines**; eixo X 11 px muted.
- **Barras empilhadas:** uma coluna por período, gap 10; **cantos externos da pilha** 2 px; hover da coluna com fundo `hover`, raio 4 + tooltip com %.
- **Linha de meta:** tracejada 1 px `muted`, rótulo “Meta R$ …” à direita acima da linha, com halo `surface` para não colidir com barras.
- **Legenda:** centralizada, 12 px muted, ponto 8 px, gap 18 (série tracejada = traço tracejado).
- **Indicador de saúde:** ponto de status 7 px + rótulo mono + total à direita; barras de 48 px, gap 3, **verde = OK, vermelho = erro empilhado**; apoio 12 muted.
- **Formatos:** moeda `R$ 1.234,56`; percentual 1 casa (`26,6%`); diferença de percentuais em `p.p.`; sinal negativo `−` (menos tipográfico); mesmas casas em KPI, tooltip e tabela; unidade junto do valor.
- **Dado ausente:** `—`, nunca zero.

### 6.1 Cor fixa por categoria (obrigatória)

A cor pertence à **categoria**, não à posição da série. A mesma categoria tem a mesma cor em qualquer gráfico do sistema.

| Dimensão | Mapa |
| --- | --- |
| Seção | Mercearia `series-1` · Hortifruti `series-2` · Padaria `series-3` · Açougue `series-4` · Bebidas `series-5` · Limpeza `series-6` · Laticínios `series-7` · Outros `series-other` |
| Canal | Loja física `series-1` · E-commerce `series-2` · Delivery `series-4` |
| Comparação | Realizado `series-1` · Meta `series-3` · Ano anterior `series-2` · período anterior = mesma cor, tracejado |
| Campanha | Promoção `series-1` · Sazonal `series-4` · Institucional `series-2` |
| Meta / estado | ≥ 100% `brand` · 80–99% `warning` · < 80% `error` |
| Escala sequencial | 5 passos de `brand` (10, 30, 50, 75, 100% sobre `hover`); sem dado = `hover`; texto nos 2 passos mais fortes em `on-accent` |

**Máximo de séries:** seções são um conjunto fechado de 7 e podem aparecer todas. Dimensões abertas (produtos, fornecedores, lojas) mostram até 5 + “Outros”.

---

## 7. Motion

| Interação | Duração | Observação |
| --- | --- | --- |
| Hover de botão/menu/KPI | 120 ms | Só cor e borda |
| Tooltip | 120 ms fade | Ajuda: 300 ms de atraso |
| Popover / dropdown | 120 ms | Fade + sobe 4 px |
| Submenu | — | Abre após 150 ms |
| Modal | 180 ms ease-out | Sobe 6 px + fade |
| Drawer | 200 ms | Desliza 24 px + fade |
| Toast | 200 / 150 ms | Sobe 6 px + fade / fade |
| Sidebar | 200 ms, (.2, 0, 0, 1) | 80 ms abre / 150 ms fecha |
| Skeleton | 1,2 s | Opacidade 60% ↔ 100% |
| Spinner | 700 ms linear | — |

Sem animação decorativa. Gráficos sem animação de entrada. Respeitar `prefers-reduced-motion`.

## 8. Responsividade e acessibilidade

- **Uso exclusivo em desktop.** Alvo ≥ 1280 px; conteúdo limitado a 1280. Tabelas rolam na horizontal dentro do card.
- **[Pendente]** comportamento abaixo de ~1100 px (sugestão: KPIs em 2 colunas).
- Contraste AA nos dois temas (texto verde/âmbar via `brand-text`/`warning-text`).
- Foco visível em todos os controles; sidebar abre por foco; tooltips de ajuda abrem por foco; menus navegáveis por setas; ESC fecha popover → modal/drawer; foco preso em modal/drawer.
- Cor nunca é o único indicador (seta, texto ou badge junto).
- Controles interativos são `<button>`/`<a href>` reais, com `aria-label` quando só ícone.

## 9. Regras de consistência (obrigatórias)

1. Raio por papel: **6** controles · **8** superfícies · **total** pills/badges · **4** só em gráfico.
2. Card = borda 1 px, sem sombra. Sombra só em tooltip, popover e toast.
3. Rótulo de KPI e de grupo **sempre** mono caixa-alta; valor **sempre** sans.
4. **Um** botão primário por área; destrutivo só em confirmações, sempre à direita.
5. Verde = marca e positivo. Nunca verde para neutro ou negativo (a série Mercearia é a única exceção categórica).
6. Mesma categoria = mesma cor em todos os gráficos (§6.1). Cor por índice de série é proibida.
7. Todo gráfico segue a anatomia do §6; todo KPI tem `?`.
8. Toda tabela: cabeçalho com tipo, linhas de 35 px, numéricos à direita, rodapé padrão.
9. Todo modal em três faixas; todo toast com a mesma estrutura.
10. Cores, espaçamentos e raios só via tokens (§2).
11. **Todo bloco com dados tem carregando, vazio e erro** (§12.6). Um bloco com erro não derruba a página.
12. **Nenhum dado de negócio fixo no código** (§14).
13. **Nova necessidade:** primeiro combinar componentes existentes; só então criar um novo, documentando aqui antes de usar.

## 10. Como montar uma nova página

1. Reutilizar topbar, sidebar e margens (§3).
2. Cabeçalho: H1 + subtítulo + ações à direita.
3. Chips de filtro globais; banner (se houver).
4. KPIs em grid de 3 colunas.
5. Gráficos em cards com a anatomia padrão.
6. Tabela detalhada ao final, com toolbar e rodapé; detalhe da linha em drawer.
7. Cada bloco busca seus dados e trata carregando, vazio e erro.
8. Feedback via toast; confirmações destrutivas via modal.
9. Testar nos dois temas e por teclado antes de aprovar.

## 11. Inventário

| Componente | Status |
| --- | --- |
| Topbar, sidebar, botões, badges, input, segmented, cards, KPI, tabela, tooltips, modal 320, toast, barras empilhadas, linha de meta, indicador de saúde, tema claro/escuro | **Tela** |
| Menu secundário, período, dropdown/submenu/contexto, chips e multisseleção, abas, breadcrumb, drawer, skeleton/spinner/vazio/erro, modais 480/720 e variantes, toasts extras, tabela avançada, formulários, banner, todos os gráficos do §12.8 | **Especificado** (implementado em `tela-bi.html`) |
| Breakpoints abaixo de ~1100 px; fonte definitiva (hoje: pilha do sistema) | **Pendente** |

---

## 12. Componentes secundários [Especificado]

> Usam apenas os tokens do §2 e as regras do §9.

### 12.1 Seletor de período
- **Gatilho:** botão base com ícone de calendário + rótulo + ⌄. **Popover** (`surface`, borda, raio 8, sombra, 120 ms).
- **Estrutura:** atalhos à esquerda (coluna de **160 px**: Hoje, Ontem, Últimos 7 dias, Últimos 30 dias, Mês atual, Mês anterior, Últimos 12 meses, Personalizado; selecionado com ✓) + calendário à direita + rodapé Cancelar / Aplicar.
- **Dia:** 32 × 32, raio 6. Hover `hover` · hoje = borda `brand` · início/fim = fundo `action` + `on-action` · intervalo = `hover` · fora do mês 50% · futuro desabilitado. Semana começa no domingo.
- **Comparação:** switch “Comparar com período anterior”; o comparativo aparece tracejado nos gráficos.
- Aplicar sem intervalo completo → toast de aviso.

### 12.2 Dropdown, menu de contexto e submenu
- Largura mínima = gatilho; altura máx. 280 com rolagem; mesma superfície do popover; reposiciona para caber na tela.
- **Item:** altura 28, padding 0 8, raio 6, 13 px; hover `hover`; selecionado com ✓ em `brand-text`; desabilitado 50%; atalho/sufixo em mono muted à direita.
- **Grupo:** rótulo mono caixa-alta; separador 1 px `border`.
- **Submenu:** abre à direita após 150 ms (ou à esquerda se não couber), `›` no item pai, sobreposto 4 px.
- **Teclado:** ↑ ↓ navegam · → abre submenu · ← / ESC fecham · Enter seleciona.
- **Select de formulário** = botão base com ⌄ abrindo este dropdown (nunca o `<select>` nativo).

### 12.3 Filtros
- **Barra de chips** em linha, gap 8, abaixo do cabeçalho. Chip = botão base com rótulo muted + valor (`Loja: Todas ⌄`).
- **Chip ativo:** fundo `hover`, valor `text`, `×` para remover. “Limpar filtros” em texto muted à direita.
- **Multisseleção:** popover com busca no topo, checkboxes `brand` (com ponto da cor da categoria) e rodapé Limpar / Aplicar. Chip mostra o nome (1 item) ou a contagem (`Seção: 3`).
- Globais no cabeçalho; locais na toolbar do card/tabela. **Nunca** o mesmo filtro nos dois lugares.
- Filtros e período globais viram **parâmetros de todas as chamadas** da página.

### 12.4 Abas e breadcrumb
- **Abas:** altura 36, borda inferior 1 px, 24 abaixo; aba com padding 0 12, muted → `text` no hover; **ativa = `text` + sublinhado 2 px `brand`**. Cada aba tem rota própria (`#/dashboards/composicao`). Troca sem animação.
- **Breadcrumb:** 13 px, 8 acima do H1; anteriores muted (hover `text`), separador `/` em `border`, último em 500.

### 12.5 Drawer
- Entra pela direita; 480 (padrão) ou 720; altura total, borda esquerda 1 px, `surface`. Overlay e blur do modal.
- Faixas: **cabeçalho** 48 px (15/500, `×` à direita) · **corpo** padding 16 com rolagem · **rodapé** 12 × 16, ações à direita, borda superior.
- Entrada 200 ms (24 px + fade). ESC, clique fora ou `×`. Foco preso.
- **Usar para:** detalhe de linha/produto/fornecedor, ajuda da página, formulários longos. **Não usar para:** confirmação (modal) nem feedback (toast).

### 12.6 Carregando, vazio e erro
- **Skeleton:** blocos `hover` com o raio do componente; pulso 60% ↔ 100% em 1,2 s. KPI: 80 × 10 + 120 × 20 · gráfico: 12 colunas fantasma · tabela: 8 linhas de 35 px.
- **Spinner:** 14 px (16 no toast), borda 2 px `border`, arco `brand`, 700 ms linear.
- **Vazio:** centralizado, altura mín. 160: ícone 24 muted · título 14/500 · descrição 13 muted (máx. 320) · ação secundária (“Limpar filtros”).
- **Erro de card:** ícone 16 `error` · “Não foi possível carregar” · descrição · “Tentar novamente” (refaz só aquele bloco). O cabeçalho do gráfico mostra `—` como valor.
- **Erro de página:** mesma estrutura, centralizada (altura mín. 360), com código de referência em mono.
- **Erro de campo:** borda `error` + mensagem 12 px `error` abaixo; `aria-invalid`.
- **Dados desatualizados:** badge atenção “Atualizado há X” ao lado do período do gráfico.
- **Desabilitado:** 50%, `not-allowed`, sem hover.

### 12.7 Modais extras
- Tamanhos **320** (confirmação) · **480** (formulário curto) · **720** (detalhe). Corpo com altura máx. 70 vh e rolagem.
- **Variantes:** destrutiva (botão `danger-bg`) · informativa (primário) · **confirmação digitada** (campo; destrutivo só habilita quando o texto coincide).
- **Carregando:** botão principal com spinner; ESC, clique fora e demais botões desativados até terminar.

### 12.8 Gráficos adicionais
Herdam a anatomia do §6, o tooltip padrão e o mapa de cores do §6.1.

| Tipo | Regra |
| --- | --- |
| Barras agrupadas | Até 3 séries por grupo, barra com raio 2 |
| Linha | 2 px, segmentos retos; ponto 6 px (borda 2 `surface`) e cursor vertical `border` só no hover; comparativo tracejado na mesma cor; máx. 4 séries |
| Área | Linha 2 px + preenchimento 12% (empilhada 24%) |
| Barras divergentes | Eixo central `muted`; verde à direita (acima), vermelho à esquerda (abaixo); valor com sinal |
| Sparkline | Altura 24, 1,5 px, cor semântica do delta, sem eixos e sem tooltip |
| Donut | Ø 160, espessura 20, 2 px entre fatias; total 22/500 no centro + rótulo mono; legenda à direita com **valor e %**; hover +4 px; máx. 5 fatias + Outros |
| Treemap | Preenchimento da categoria a 22% sobre `surface` + borda 1 px da categoria; nome 500 + % muted em `text` |
| Ranking | Linha 28; posição mono; nome truncado 160 px; barra 8 px raio 4 `series-1`; valor 500 à direita; máx. 10, decrescente |
| Pareto (ABC) | Barras `series-1` (classe A, até 80%) e `series-3` (demais); acumulado `series-2`; linha de 80% tracejada; eixo % à direita |
| Bolhas | Eixos com unidade; medianas tracejadas `border`; raio proporcional ao valor (5–18 px); rótulo com halo |
| Mapa de calor | Células 28 × 28, raio 4, gap 2, escala sequencial de 5 passos; legenda “Menos ▢▢▢▢▢ Mais” |
| Mapa por UF | Grade de quadrados 64 × 44, raio 6, borda `border`, escala sequencial; sigla 13/500 + valor 10 |
| Meta radial | Arco 14 px sobre trilho `hover`, cor por status, valor 22/500 + rótulo mono |
| Bullet | Barra 8 px sobre trilho `hover`, cor por status, traço 2 px `text` na meta |
| Funil | Barra centralizada `series-2` proporcional + valor 500 + conversão da etapa anterior muted |
| Cascata | Totais `series-2`; reduções `error`; rótulo do valor acima; conectores tracejados `muted` |
| Linha do tempo | Barras 20 px raio 4 por tipo (§6.1); grade mensal `border`; “Hoje” tracejado `text` |

### 12.9 Toasts extras
- **Erro:** ícone `error`, não fecha sozinho, `×` + ação “Tentar novamente”.
- **Processando:** spinner 16 px no lugar do ícone, não fecha sozinho; ao concluir, o **mesmo** toast vira sucesso (4 s) ou erro.
- **Com ação:** botão secundário de 24 px abaixo da descrição; não substitui o `×`.

### 12.10 Tabela avançada
- **Ordenação:** clique (ou Enter/Espaço) no cabeçalho; seta ↑/↓ 12 px — após o nome em colunas de texto, **antes** do nome em colunas numéricas (alinhadas à direita); ativa em `text`, inativa só no hover. Um nível por vez; `aria-sort`.
- **Seleção em lote:** a toolbar vira “N selecionados” + ações + “Limpar seleção”, fundo `hover`. Checkbox do cabeçalho seleciona os registros filtrados.
- **Expansão:** chevron ao lado do checkbox (gira 90°); linhas filhas com fundo `bg` e recuo de 40 px.
- **Células truncadas:** `title` com o texto completo; cabeçalho com `?` quando a coluna precisa de definição.
- **Busca sem resultado:** vazio “Nenhum registro encontrado” + “Limpar busca”.

### 12.11 Formulários
- **Rótulo** 13/500 acima; **ajuda** 12 muted abaixo; erro conforme 12.6. Campo altura **32** (toolbar e filtros: 28), raio 6, foco com anel `focus`. Textarea mín. 80. Largura máx. do grupo 420.
- **Checkbox / radio:** 14 px, `brand`. **Switch:** 28 × 16, raio total; desligado `hover` + borda `border`; ligado `brand`; botão interno 12 px `surface`.
- Ações no rodapé à direita: secundária à esquerda, primária à direita. Salvar com campo inválido → foca o campo + toast de aviso.

### 12.12 Banner de alerta de página
Card com borda 1 px na cor semântica (`info`, `warning`, `error`), ícone 16, texto 13, ação opcional (botão 24) à direita, `×` para dispensar (lembrado na sessão). Logo abaixo dos chips; **no máximo um por vez**. Ex.: “Dados de estoque atualizados há mais de 4 horas” — exibido quando o backend informa dados desatualizados.

---

## 13. Pontos pendentes

1. Comportamento abaixo de ~1100 px.
2. Fonte definitiva (hoje: pilha do sistema; Supabase usa Circular + Source Code Pro).
3. Contraste das séries `gray-series` (≈ 1,7–1,9:1) — aceito porque a série sempre tem legenda, tooltip e ordem fixa; revisar se houver queixa.

---

## 14. Contrato com o backend

1. **Nenhum valor de negócio no front.** Números, nomes, metas, períodos, usuário e listas de filtro vêm da API.
2. **Valores crus:** número, ISO 8601 (UTC), enum. O front formata em pt-BR. O backend **não** envia strings formatadas nem cores.
3. **Ausente = `null`** → UI mostra `—`.
4. **KPI** traz `rotulo`, `valor`, `formato` (`brl`/`pct`/`int`), `delta`, `delta_formato` (`pctDelta`/`pp`/`intDelta`), **`polaridade`** (+1/−1) e `ajuda`.
5. **Séries** chegam pelo **nome da categoria**; o front aplica o mapa de cor do §6.1.
6. **Frescor:** cada resposta informa `atualizado_em` (e, quando aplicável, idade do estoque) para badge/banner de desatualizado.
7. **Fuso:** datas em UTC; exibição no fuso da loja (padrão `America/Sao_Paulo`), informado no tooltip.
8. **Cada bloco é uma chamada independente** com skeleton, vazio e erro próprios; respostas de uma rota antiga são descartadas ao navegar.
9. Formatos de exemplo e lista de endpoints usados: página **Dados e backend** em `tela-bi.html`.

---

## Histórico

**v3.1 (consolidação)** — `tela-bi.html` e `galeria-graficos.html` unificados num único sistema navegável com documentação viva. Correções:
- **Delta do KPI:** a seta indicava o significado (Ruptura subiu e mostrava ↓). Agora seta = direção, cor = significado, via `polaridade` do backend.
- **Gráfico principal:** barras eram desenhadas a 90% da altura e não batiam com o eixo Y. Corrigido.
- **Galeria:** donut e treemap coloriam por posição (Bebidas azul, Hortifruti cinza/magenta), violando “mesma categoria, mesma cor”. Criado o mapa fixo do §6.1.
- **Paleta:** `series-7` laranja e `series-8` verde-limão colidiam com `warning` e com o verde de marca; paleta reduzida a 7 séries + `series-other`. “Outros” passou de `gray-series` (igual a Padaria) para `muted`.
- **Contraste:** novos tokens `brand-text`, `warning-text`, `focus`, `danger-bg`, `on-accent` (texto verde/âmbar e botão destrutivo reprovavam AA).
- **Tema:** o HTML forçava `data-theme="dark"`; agora segue o sistema e a escolha manual fica salva.
- **Acessibilidade:** itens da sidebar viraram links reais, sidebar abre por foco, `?`/`i` abrem por foco, foco preso em modal/drawer, menus por teclado.
- **Galeria:** texto branco hard-coded no treemap/funil/mapa (ilegível sobre cinza) substituído por tokens; donut sem valor na legenda; área empilhada 28% → 24%; heatmap contínuo → 5 passos; trilho do bullet `border` → `hover`; classe CSS `.seg` duplicada (segmented × donut) corrigida; gráficos sem valor de destaque ganharam anatomia completa.
- **Design-tokens.md** estava vazio — agora preenchido.
- Documentados: popover (z 50), menu secundário (z 10), período com coluna de 160 px, gauge, funil, cascata, Pareto, bolhas, mapa por UF, linha do tempo.
