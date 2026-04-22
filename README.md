# 💰 Controle Financeiro Pessoal

> Aplicação web completa para controle de finanças pessoais — receitas, despesas, parcelamentos, contas recorrentes, metas de gastos e visão de patrimônio. Tudo em um único arquivo HTML, sem backend e sem dependências externas.

---

## ✨ Funcionalidades

### 📊 Dashboard
- Visão geral do mês com métricas de receitas, despesas avulsas, parcelas, recorrentes, saldo e taxa de economia
- Alerta automático quando gastos se aproximam ou ultrapassam metas definidas
- Gráfico de fluxo diário com receitas, despesas, parcelas e fixos
- Breakdown de despesas por categoria com barra de progresso e comparativo com metas
- Painel de compras parceladas ativas com progresso de pagamento
- Lista completa de lançamentos do mês com filtros por tipo
- Navegação entre meses (anterior / próximo)

### 📈 Evolução
- Histórico dos últimos 12 meses
- Gráfico de receitas vs. total de saídas vs. saldo
- Gráfico de taxa de economia mensal (%)
- Gráfico de saldo acumulado ao longo do tempo
- Métricas de melhor mês, pior mês e média de economia

### 🎯 Metas
- Defina limites de gasto por categoria
- Progresso visual com alertas em amber (80%) e vermelho (100%)
- Gráfico comparativo entre gasto atual e limite da meta

### 🔁 Recorrentes
- Cadastro de contas fixas mensais (aluguel, assinaturas, academia, etc.)
- Total mensal e projeção anual comprometidos
- As contas aparecem automaticamente em todos os meses seguintes ao início

### 💰 Patrimônio
- Saldo acumulado de todos os meses registrados
- Total de parcelas futuras ainda em aberto
- Comprometimento mensal fixo (recorrentes)
- Tabela histórica completa mês a mês

### Outros recursos
- **Tema claro/escuro** — alterna com um clique e persiste entre sessões
- **Exportação CSV** — exporta todos os lançamentos dos últimos 12 meses
- **Dados persistidos** no `localStorage` do navegador (sem perda ao fechar)
- **Dados de exemplo** pré-carregados para demonstração imediata

---

## 🚀 Como usar

1. Baixe o arquivo `index.html`
2. Abra-o em qualquer navegador moderno (Chrome, Firefox, Edge, Safari)
3. Pronto — nenhuma instalação, servidor ou internet necessários

---

## 🛠️ Como foi feito

### Tecnologias

| Tecnologia | Uso |
|---|---|
| **HTML5** | Estrutura da aplicação |
| **CSS3 puro** | Layout, temas claro/escuro, animações, responsividade |
| **JavaScript vanilla** | Toda a lógica de negócio, estado e renderização |
| **Chart.js 4.4** | Gráficos de barras e linhas (carregado via CDN) |
| **localStorage** | Persistência dos dados no navegador |

Sem frameworks, sem build tools, sem Node.js — um único arquivo `.html` autocontido.

### Arquitetura

O estado da aplicação é mantido em quatro arrays em memória, sincronizados com o `localStorage`:

```
transactions[]   → lançamentos avulsos (receitas e despesas)
parcelamentos[]  → compras parceladas com metadata de parcelas
recorrentes[]    → contas fixas mensais sem data de fim
metas[]          → limites de gasto por categoria
```

### Lógica de parcelamentos

Parcelamentos não são "expandidos" em múltiplos registros. Em vez disso, a função `getParcForMonth()` calcula dinamicamente se uma parcela pertence a determinado mês:

```javascript
// Calcula o índice da parcela para um dado mês/ano
const startM = (startYear * 12) + (startMonth - 1);
const thisM  = (year * 12) + month;
const idx    = thisM - startM;

if (idx < 0 || idx >= numParc) return null; // mês fora do range
```

Isso garante que adicionar um parcelamento em abril já o projeta automaticamente em maio, junho e nos demais meses futuros, sem duplicar dados.

### Lógica de recorrentes

Semelhante aos parcelamentos, `getRecForMonth()` verifica se o mês solicitado é igual ou posterior ao mês de início do recorrente, e o retorna com a data de vencimento correta para aquele mês (respeitando meses com menos de 31 dias).

### Sistema de metas e alertas

O dashboard recalcula os gastos por categoria a cada `refreshDashboard()` e compara com as metas cadastradas. O alerta banner aparece quando qualquer categoria atinge 80% do limite, e muda para vermelho ao ultrapassar 100%.

### Renderização

A aplicação usa renderização imperativa direta via `innerHTML`, sem virtual DOM. Cada `refresh` reconstrói os elementos afetados. Os gráficos são destruídos (`chart.destroy()`) e recriados a cada atualização para evitar conflitos de instâncias do Chart.js.

### Tema claro/escuro

Implementado com CSS custom properties (variáveis) no seletor `[data-theme=dark]`. A troca de tema é feita alterando o atributo `data-theme` no elemento `<html>`, o que propaga automaticamente todas as cores da interface.

```css
:root          { --bg: #f5f4f0; --text: #1a1a18; ... } /* light */
[data-theme=dark] { --bg: #141413; --text: #f0ede8; ... }
```

---

## 📁 Estrutura do projeto

```
controle-financeiro/
└── index.html    ← aplicação completa (HTML + CSS + JS)
└── README.md     ← este arquivo
```

---

## 💾 Dados e privacidade

Todos os dados são armazenados **exclusivamente no `localStorage` do seu navegador**, na máquina local. Nenhuma informação é enviada para servidores externos. Limpar os dados do site no navegador apagará os registros.

---

## 📤 Exportação

O botão **⬇ CSV** exporta todos os lançamentos dos últimos 12 meses em formato `.csv` compatível com Excel e Google Sheets, com separador `;` e encoding UTF-8 com BOM para exibição correta de caracteres especiais.
