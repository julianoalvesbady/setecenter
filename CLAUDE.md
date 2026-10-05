# Instruções para o agente — BI de Supermercado

## Design: referência obrigatória

Toda interface deste projeto segue o design system em `design/`. **Não invente estilo, não fuja da referência.**

| Arquivo | Papel |
| --- | --- |
| `design/tela-bi.html` | **Fonte de verdade.** Sistema navegável (Visão geral, Dashboards, Alertas, Configurações) + documentação viva (item “Design System” da sidebar). Abra no navegador e imite exatamente. |
| `design/DESIGN_SYSTEM.md` | Regras escritas com todas as medidas, estados e comportamentos. |
| `design/design-tokens.md` | Tokens em CSS e JSON, prontos para copiar. |

Ordem de precedência: **tela-bi.html > DESIGN_SYSTEM.md > design-tokens.md**. Se algo não estiver coberto, combine componentes existentes; só crie um novo depois de documentá-lo no `DESIGN_SYSTEM.md`.

## A referência é FORMA, não CONTEÚDO

- Os números, lojas, produtos, metas, alertas e o usuário que aparecem em `tela-bi.html` são **dados de demonstração**. Eles saem de `api.get()` / `FIXTURES` no próprio arquivo, que **simulam** o backend.
- **Nunca** copie esses valores para o produto. **Nunca** construa telas com dados fixos (mockup). Todo conteúdo vem do backend.
- Reaproveite da referência: tokens, layout, componentes, estados, interações, formatos de exibição e o mapa de cor por categoria.

## Regras inegociáveis

1. **Tokens:** só as cores, raios, espaçamentos, fontes e tempos de `design-tokens.md`. Nenhum hex solto no código.
2. **Tema claro e escuro** com os mesmos tokens; segue o sistema; escolha manual prevalece e fica salva.
3. **Layout fixo:** topbar 48 px, sidebar 48/202 px que **sobrepõe** (nunca empurra), conteúdo com padding 40 e largura máx. 1280.
4. **Borda, não sombra:** cards com borda 1 px; sombra só em tooltip, popover e toast.
5. **Um botão primário por área**; destrutivo só em confirmação, à direita.
6. **KPI:** rótulo mono caixa-alta + `?`; seta do delta = direção; cor do delta = significado (`sinal(delta) × polaridade`, polaridade vem do backend).
7. **Gráficos:** anatomia completa (título + `i`, valor de destaque, período, plotagem, legenda, tooltip padrão), sem gridlines, cor fixa por categoria (Mercearia sempre `series-1`, etc.) — nunca cor por índice.
8. **Tabelas:** cabeçalho com tipo, linhas 35 px, numéricos à direita, rodapé padrão, detalhe em drawer.
9. **Estados:** todo bloco que busca dados mostra skeleton → dados | vazio | erro com “Tentar novamente”. Um bloco com erro não derruba a página.
10. **Formatos pt-BR no front:** `R$ 1.234,56`, `26,6%`, `p.p.`, `dd/mm/aaaa`, sinal `−`. Ausente = `—`, nunca `0`.
11. **Acessibilidade:** foco visível (`focus`), controles são `<button>`/`<a href>` reais, ESC fecha, foco preso em modal/drawer, cor nunca é o único indicador.
12. **Desktop ≥ 1280 px.**

## Contrato com o backend (resumo — detalhes em DESIGN_SYSTEM.md §14)

- Backend envia valores **crus** (número, ISO 8601 UTC, enum) e `null` quando não há dado. Nada de strings formatadas nem cores.
- KPIs trazem `valor`, `formato`, `delta`, `delta_formato`, `polaridade` e `ajuda`.
- Séries chegam pelo nome da categoria; o front aplica a cor.
- Respostas informam `atualizado_em` para badge/banner de dados desatualizados.
- Filtros e período globais viram parâmetros de todas as chamadas da página.

## Antes de entregar uma tela

- [ ] Comparei lado a lado com a página equivalente de `tela-bi.html`, nos dois temas.
- [ ] Nenhum valor de negócio fixo no código; tudo vem da API.
- [ ] Carregando, vazio e erro testados (simule-os na página “Dados e backend” da referência para comparar).
- [ ] Nenhuma cor/raio/espaçamento fora dos tokens.
- [ ] Navegável por teclado, foco visível.
