<div align="center">

# 📊 Controle de Vendas 
### Projeto de nível **Expert / Sênior** em Business Intelligence

Modelagem de dados, medidas DAX avançadas, tooltip de página, menu de filtros interativo e navegação com indicadores (bookmarks).

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0078D4?style=for-the-badge)
![Nível](https://img.shields.io/badge/Nível-Expert%20%2F%20Sênior-critical?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Concluído-success?style=for-the-badge)


</div>

---

<h2 align="center"> ## 📑 Sumário </h2>

1. [Sobre o projeto](#-sobre-o-projeto)
2. [Objetivos e perguntas de negócio](#-objetivos-e-perguntas-de-negócio)
3. [Tecnologias e recursos utilizados](#-tecnologias-e-recursos-utilizados)
4. [Modelo de dados](#-modelo-de-dados)
5. [Medidas DAX](#-medidas-dax)
6. [Interatividade e experiência do usuário](#-interatividade-e-experiência-do-usuário)
7. [Tooltip personalizado](#-tooltip-personalizado)
8. [O dashboard, visual por visual](#-o-dashboard-visual-por-visual)
9. [Principais insights](#-principais-insights)
10. [Por que este é um projeto Expert / Sênior](#-por-que-este-é-um-projeto-expert--sênior)
11. [Como utilizar](#-como-utilizar)
12. [Autor](#-autor)

---

<h2 align="center"> 📌 Sobre o projeto </h2>

<em>O **Controle de Vendas** é um relatório gerencial desenvolvido em **Power BI** que consolida as vendas de três anos (**2017, 2018 e 2019**) em uma única visão analítica. O objetivo é permitir que gestores acompanhem o faturamento, comparem períodos, avaliem o desempenho de cada vendedor, monitorem cancelamentos e entendam o comportamento das **formas de pagamento**, tudo com uma navegação limpa e interativa.

O relatório foi pensado como um produto completo, e não apenas como um conjunto de gráficos: possui **capa de navegação**, **menu de filtros retrátil**, **botões de alternância de visual**, **tooltip de página** e uma camada de **medidas DAX organizadas em tabelas dedicadas**.</em>
</p>

---

<h2 align="center">🎯 Objetivos e perguntas de negócio</h2>

<em>O dashboard foi construído para responder, de forma rápida, perguntas como:</em>

- Qual foi o faturamento total e como ele se distribui por **ano**?
- Qual o **crescimento percentual** entre um ano e outro?
- Quais **formas de pagamento** concentram a maior parte da receita?
- Quem são os **vendedores** com maior faturamento e qual o volume de **cancelamentos** de cada um?
- Como o faturamento se comporta **dia a dia** dentro do mês, comparando 2017 x 2018?
- As **metas** anuais foram atingidas?
- Qual o detalhe de cada **nota fiscal** vendida?

---

<h2 align="center"> 🛠️ Tecnologias e recursos utilizados </h2>

| Categoria | Recurso |
|---|---|
| Ferramenta | Power BI Desktop |
| Linguagem de medidas | DAX (Data Analysis Expressions) |
| Modelagem | Modelo dimensional com tabela Calendário e tabelas de fatos |
| Organização | Tabelas de medidas dedicadas (`MEDIDAS` e `FORMAS PGTO`) |
| Navegação | Indicadores (Bookmarks), Painel de Seleção, botões e imagens |
| Interatividade | Segmentações de dados, Tooltip de página, visual de reprodução (Play Axis) |
| Visuais | Medidores (gauge), colunas + linha, barras, cartões, tabela e gráfico 100% empilhado |

---

<h2 align="center"> 🧩 Modelo de dados</h2>

<em> O modelo segue a lógica de **esquema dimensional**: uma tabela **CALENDARIO** (dimensão de tempo) se relaciona em **1:N** com as tabelas de fatos, garantindo que todas as análises temporais respondam de forma consistente aos mesmos filtros de data.</em>

<!-- 📷 INSERIR IMAGEM: exibição de modelo -->
<img width="1037" height="697" alt="Print  Exibicao de modelo-con vendas" src="https://github.com/user-attachments/assets/7ce06aa7-82f9-46c8-ae2f-d9a423eaf04e" /> 


### Tabelas

| Tabela | Tipo | Descrição |
|---|---|---|
| `CALENDARIO` | Dimensão | Tabela de datas com `Ano`, `DATA`, `Nome do Dia`, `Nome do Mês` e `Trimestre`. Base para toda inteligência de tempo. |
| `META_2017` | Fato | Vendas de 2017: `DATA`, `FORMA DE PAGAMENTO`, `NUMERO_CUPOM_FISCAL`, `TOTAL_VENDA`, `VALOR_CANCELADO`, `VENDEDOR`. |
| `META_2018` | Fato | Vendas de 2018, com a mesma estrutura de colunas. |
| `META_2019` | Fato | Vendas de 2019, com a mesma estrutura de colunas. |
| `CONSOLIDADA` | Fato | Visão consolidada das vendas: `DATA`, `FORMA DE PAGAMENTO`, `N FISCAL`, `RECEBIMENTO`, `TOTAL_VENDA`, `VALOR_CANCELADO`. |
| `MEDIDAS` | Tabela de medidas | Concentra as medidas gerais (`FAT 2017`, `FAT 2018`, `CANCELADO`, `CRESCIMENTO PORC`, `2017 X 2018`, entre outras). |
| `FORMAS PGTO` | Tabela de medidas | Concentra as medidas por forma de pagamento (faturamento e quantidade). |

### Relacionamentos

- `CALENDARIO[DATA]` → `META_2017[DATA]` (1:N)
- `CALENDARIO[DATA]` → `META_2018[DATA]` (1:N)
- `CALENDARIO[DATA]` → `META_2019[DATA]` (1:N)
- `CALENDARIO[DATA]` → `CONSOLIDADA[DATA]` (1:N)

### Organização das medidas

<em>Todas as medidas ficam em **tabelas próprias**, separadas dos dados brutos. Isso deixa o painel de campos limpo, facilita a manutenção e é uma forma prática de se localizar.</em>

Medidas DAX <h2 align="center"> 🧮 Medidas DAX </h2>

<em>Abaixo estão as medidas que considero **centrais** para o projeto, explicadas uma a uma.</em>

### 1. `FAT CARTAO PRESENTE`: faturamento filtrado por forma de pagamento

```dax
FAT CARTAO PRESENTE =
CALCULATE(
    SUM( CONSOLIDADA[TOTAL_VENDA] ),
    FILTER(
        CONSOLIDADA,
        CONSOLIDADA[FORMA DE PAGAMENTO] = "CARTÃO PRESENTE"
    )
) + 0
```

**Como funciona**

- `SUM` soma o valor total das vendas.
- `CALCULATE` altera o contexto de filtro da soma.
- `FILTER` percorre a tabela `CONSOLIDADA` e mantém apenas as linhas em que a forma de pagamento é *CARTÃO PRESENTE*.
- O `+ 0` transforma o resultado em branco (`BLANK`) em **zero**. Assim, formas de pagamento sem venda no período aparecem como `R$ 0,00` no visual, em vez de sumirem. É o caso de `FATURAMENTO DINHEIRO` no relatório.

**Forma alternativa (sintaxe de filtro booleano)**

```dax
CALCULATE(
    SUM( CONSOLIDADA[TOTAL_VENDA] ),
    CONSOLIDADA[FORMA DE PAGAMENTO] = "CARTÃO PRESENTE"
)
```

Os dois formatos produzem o mesmo resultado neste cenário. A versão booleana é mais enxuta e, em geral, mais performática. A versão com `FILTER` é a mais flexível, pois aceita condições mais complexas, como comparar colunas ou usar medidas dentro do filtro.

<!-- 📷 INSERIR IMAGEM: código DAX da medida FAT CARTAO PRESENTE -->
<img width="1037" height="182" alt="DAX MEDIDA1 con vendas" src="https://github.com/user-attachments/assets/4f20248a-11e4-4499-883c-ca76dde6e0a3" />

---

### 2. `FAT CREDITO 2017`: filtro por forma de pagamento + intervalo de datas

```dax
FAT CREDITO 2017 =
CALCULATE(
    SUM( META_2017[TOTAL_VENDA] ),
    FILTER(
        META_2017,
        META_2017[FORMA DE PAGAMENTO] = "CARTÃO CRÉDITO"
    ),
    DATESBETWEEN(
        CALENDARIO[DATA],
        ("01/01/2017"),
        ("31/12/2017") - 10
    )
) + 0
```

**Como funciona**

- Combina **dois filtros** dentro do mesmo `CALCULATE`: um por forma de pagamento (`FILTER`) e outro por período (`DATESBETWEEN`).
- `DATESBETWEEN` retorna a lista de datas entre duas datas informadas, usando a tabela `CALENDARIO`. Por isso a relação entre o calendário e a tabela de fatos é essencial.
- A subtração `- 10` desloca a data final em 10 dias, e é essa lógica que alimenta os indicadores de **faturamento dos últimos 10 dias** exibidos no relatório.
- O mesmo padrão foi replicado para 2018 e 2019 (`FAT CREDITO 2018` e `FAT CREDITO 2019`), permitindo comparar os anos lado a lado.

<!-- 📷 INSERIR IMAGEM: código DAX da medida FAT CREDITO 2017 -->
<img width="797" height="311" alt="DAX MEDIDA2 con vendas" src="https://github.com/user-attachments/assets/6af00e2d-450f-4cc2-bb32-8bda6139232c" />


---

### 3. `CRESCIMENTO PORC`: variação percentual entre anos

```dax
CRESCIMENTO PORC =
DIVIDE(
    [FAT 2019] - [FAT 2018],
    [FAT 2018]
)
```

**Como funciona**

- Calcula a variação relativa: `(FAT 2019 − FAT 2018) ÷ FAT 2018`.
- Usa `DIVIDE` em vez do operador `/`, pois `DIVIDE` trata a divisão por zero automaticamente (retorna vazio em vez de erro), deixando o relatório mais robusto.
- A medida reaproveita outras medidas (`[FAT 2019]` e `[FAT 2018]`), o que evita repetição de lógica e mantém o modelo fácil de manter.

<!-- 📷 INSERIR IMAGEM: código DAX da medida CRESCIMENTO PORC -->
<img width="432" height="51" alt="DAX MEDIDA3 con vendas - Copia" src="https://github.com/user-attachments/assets/91cb1cb2-ed92-4fe7-8812-19847a837b90" />


---

### 4. Conjunto de medidas do projeto

Além das três medidas detalhadas acima, o projeto conta com uma biblioteca completa de medidas de apoio, todas visíveis na imagem abaixo.

<!-- 📷 INSERIR IMAGEM: diversas medidas e DAX -->
<img width="306" height="761" alt="Diversas medidasEdax-con vendas" src="https://github.com/user-attachments/assets/b51bf89a-f4b5-40ea-a5d5-ae9313999ec3" />


| Grupo | Medidas | Finalidade |
|---|---|---|
| **Faturamento por forma de pagamento** | `FAT CREDITO`, `FAT DEBITO`, `FAT DINHEIRO`, `FAT CARTAO PRESENTE`, `FAT NAO INFORMADO` | Receita segmentada por meio de pagamento |
| **Quantidade por forma de pagamento** | `QTDE CREDITO`, `QTDE DEBITO`, `QTDE DINHEIRO`, `QTDE C. PRESENTE`, `QTDE NÃO INFO` | Volume de transações por meio de pagamento |
| **Faturamento por ano** | `FAT 2017`, `FAT 2018`, `FAT CREDITO 2017/2018/2019` | Comparativos anuais |
| **Análise de desempenho** | `CRESCIMENTO PORC`, `2017 X 2018`, `CANCELADO` | Crescimento, comparação e perdas por cancelamento |
| **Apoio à interação** | `FILTRO VENDA`, `FILTRO VENDEDOR` | Suporte aos cartões dinâmicos de seleção de venda e vendedor |

---

<h2 align="center"> 🕹️ Interatividade e experiência do usuário </h2>
<p align ="center">
Um dos diferenciais do projeto é a camada de navegação.</p>
<p align ="center"> O relatório se comporta como uma pequena aplicação, e não como uma página estática.</p>


<h2 align="center"> 🏠 Capa de navegação </h2>

A capa apresenta o título do relatório e o botão **ABRIR RELATÓRIO**, que leva o usuário à página principal.

<!-- 📷 INSERIR IMAGEM: capa -->
<img width="1297" height="727" alt="CAPA" src="https://github.com/user-attachments/assets/e50620a5-d1ba-4282-b201-66010ab931a1" />



<h2 align="center"> 🔎 Menu de filtros interativo </h2>

Um **menu de filtros retrátil**, aberto por um botão com ícone de hambúrguer no canto do painel, ocupa a tela como uma sobreposição e concentra todas as segmentações de dados. Assim, o dashboard fica visualmente limpo quando o menu está fechado.

Conteúdo do menu:

| Componente | Função |
|---|---|
| **Vendedor** | Segmentação com lista de vendedores (Antônio, Cláudio, Júlio, Larissa, Lucia, Maria, Natália, Paloma, entre outros) |
| **Forma de Pagamento** | Segmentação por Cartão Crédito, Cartão Débito, Cartão Presente e Não Informado |
| **Data** | Segmentação por intervalo, com campos de data e controle deslizante (01/01/2017 a 31/12/2019) |
| **FECHAR** | Botão que oculta o menu e retorna ao dashboard |
| **LIMPAR FILTRO** | Botão que remove todas as seleções de uma só vez |

<!-- 📷 INSERIR IMAGEM: menu de filtro interativo -->
<img width="581" height="345" alt="PRINT MENU-FILTRO-INTERATIVO" src="https://github.com/user-attachments/assets/1710f09b-6e12-4848-95b9-52288e9719a1" />


<h2 align="center"> 🔖 Indicadores (Bookmarks) </h2>

Os indicadores guardam estados específicos do relatório e são acionados por botões. Eles são a base da navegação do projeto.

| Indicador | O que faz |
|---|---|
| `EXIBIR` | Exibe o menu de filtros |
| `OCULTAR` | Oculta o menu de filtros |
| `LIMPAR FILTROS` | Restaura o relatório ao estado sem filtros |
| `BARRAS` | Alterna o visual para a versão em barras/colunas |
| `PORC` | Alterna o visual para a versão em porcentagem |

<h2 align="center">🗂️ Painel de Seleção</h2>

O Painel de Seleção organiza e nomeia cada objeto da página, permitindo controlar **o que fica visível em cada indicador**. Objetos como `MENU`, `BOTAO FILTRO + COR`, `BOTAO FECHAR + COR`, as três segmentações de dados, `BT_BARRAS` e `BT_PORC` são mostrados ou ocultados conforme o indicador acionado. Os elementos permanentes (medidores `META 2017`, `META 2018`, `META 2019`, cartões de faturamento e imagens) permanecem sempre visíveis.

Dar nomes claros aos objetos é uma boa prática de organização e facilita a manutenção do relatório.

<!-- 📷 INSERIR IMAGEM: seleção e indicadores -->
<img width="442" height="642" alt="PRINT  Selecao Indicadores- Controle  de vendas" src="https://github.com/user-attachments/assets/cf658afe-9d73-472c-aa58-9aa42cc8748c" />


---

<h2 align="center">💬 Tooltip personalizado </h2>

O relatório utiliza um **tooltip de página**: uma página inteira do Power BI, configurada com tamanho de tooltip e oculta da navegação, que aparece quando o usuário posiciona o mouse sobre um visual.

**Tooltip "RESUMO POR CARTÕES"**

- Ao passar o mouse sobre um vendedor no gráfico **Faturado x Cancelado | Vendedor**, aparece um gráfico de linhas comparando **Cartão Crédito** e **Cartão Débito** ao longo do tempo.
- O tooltip **herda o contexto do item sobre o qual o mouse está**, então mostra o comportamento dos cartões apenas daquele vendedor.
- Com isso, o usuário obtém um segundo nível de detalhe **sem sair da página e sem poluir o layout** com mais gráficos.

<!-- 📷 INSERIR IMAGEM: tooltip -->
<img width="777" height="330" alt="PRINT TOOLTIP" src="https://github.com/user-attachments/assets/9a0c6d1e-bc45-4db2-a146-1a14736866d9" />


---

<h2 align="center"> 🖥️ O dashboard, visual por visual </h2>


<!-- 📷 INSERIR IMAGEM: relatório de vendas (página principal) -->
![Página principal do relatório] <img width="1047" height="696" alt="Print Relatorio-vendas" src="https://github.com/user-attachments/assets/62f5d732-a9af-485c-a641-1ebb9f168721" />


| Visual | O que mostra |
|---|---|
| **META 2017 / META 2018 / META 2019** | Três medidores (gauge) que comparam o faturamento realizado com a meta de cada ano, com marcador da meta e escala de 0 a 1 Mi. |
| **FATURAMENTO \| 2017 x 2018** | Gráfico combinado (colunas + linha) que compara o faturamento de 2017 e 2018 por **dia do mês** (1 a 31). Possui botão de reprodução (Play Axis) e destaque de cor em dias específicos. |
| **FATURAMENTO POR ANO** | Colunas com o total faturado em 2017, 2018 e 2019. |
| **FATURADO x CANCELADO \| VENDEDOR** | Barras por vendedor comparando o valor faturado e o cancelado. Ao passar o mouse, aciona o tooltip. |
| **FATURAMENTO x CANCELADO** | Barra 100% empilhada que mostra a proporção de cancelamento sobre o total faturado. |
| **FATURAMENTO POR FORMA PGTO** | Colunas por forma de pagamento, com **linha de média** de referência e chave **RECEBIMENTO PARCELADO**. |
| **FATURAMENTO \| QUANTIDADE** | Cartões com o faturamento por forma de pagamento (crédito, débito, dinheiro, cartão presente e não informado). |
| **FATURAMENTO ÚLTIMOS 10 DIAS** | Cartões com o faturamento de crédito de cada ano no intervalo de 10 dias, usando as medidas com `DATESBETWEEN`. |
| **VENDEDOR / VALOR VENDA** | Cartões dinâmicos que exibem o vendedor e a venda selecionados. Sem seleção, mostram a orientação "Selecione um Vendedor" / "Selecione uma Venda". |
| **RESUMO DE VENDAS** | Tabela detalhada com nota fiscal, data, vendedor, forma de pagamento e valor da venda, com total no rodapé. |

---

<h2 align="center"> 💡 Principais insights </h2>

Com os filtros zerados (período completo de 01/01/2017 a 31/12/2019), o relatório revela:

- 💰 **Faturamento total:** R$ 1.777.783,00.
- 📅 **2017 foi o ano de maior faturamento** (R$ 928.406), cerca de 52% do total do período.
- 📉 **Queda de 2017 para 2018** de aproximadamente 57%, seguida de **recuperação de cerca de 13,7% em 2019** (R$ 397.467 → R$ 451.910), valor obtido pela medida `CRESCIMENTO PORC`.
- 💳 **Cartão de crédito lidera** com cerca de 51% da receita, seguido por débito (cerca de 33%) e cartão presente (cerca de 15%).
- 🏆 **Natália é a vendedora com maior faturamento** (R$ 568.008), aproximadamente 32% do total, e o painel deixa visível o volume cancelado de cada vendedor.
- ❗ Há registros de faturamento **"Não informado"** (R$ 921), o que aponta para uma oportunidade de melhoria na qualidade do dado na origem.

> Os percentuais acima foram calculados a partir dos valores exibidos no dashboard.

---

<h2 align="center"> 🏅 Por que este é um projeto Expert / Sênior </h2>

- **Modelagem dimensional** com tabela Calendário e relacionamentos 1:N.
- **Tabelas de medidas dedicadas**, mantendo o modelo organizado e escalável.
- **DAX com manipulação de contexto:** `CALCULATE`, `FILTER`, `DATESBETWEEN` e `DIVIDE`, com reaproveitamento de medidas e tratamento de vazios (`+ 0`).
- **Comparativos temporais** entre 2017, 2018 e 2019.
- **Tooltip de página** com contexto dinâmico.
- **Navegação com indicadores, botões e Painel de Seleção**, entregando uma experiência de aplicação.
- **Menu de filtros retrátil**, que preserva a área útil do dashboard.
- **Foco em UX e storytelling de dados**, com hierarquia visual, uso de cor para destaque e metas em evidência.

---

<h2 align="center"> Como utilizar </h2>

1. Faça o download ou clone este repositório:
   ```bash
   git clone https://github.com/RPessotti/NOME-DO-REPOSITORIO.git
   ```
2. Abra o arquivo `.pbix` no **Power BI Desktop**.
3. Use o botão **ABRIR RELATÓRIO** na capa para acessar o dashboard.
4. Explore os filtros pelo ícone de menu, passe o mouse sobre os vendedores para ver o tooltip e utilize os botões de alternância de visual.

> **Requisito:** Power BI Desktop (versão gratuita), disponível no site oficial da Microsoft.

---

## 👤 Autor

**Rafael Pessotti**
Estudante de Ciência da Computação | Suporte de TI | Em transição para Dados e BI

[![GitHub](https://img.shields.io/badge/GitHub-RPessotti-181717?style=for-the-badge&logo=github)](https://github.com/RPessotti)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Rafael%20Pessotti-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/rafael-pessotti-563240323)

---

<div align="center">

⭐ Se este projeto foi útil ou interessante, deixe uma estrela no repositório!

</div>
