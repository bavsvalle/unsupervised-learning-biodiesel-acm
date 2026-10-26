# Unsupervised Machine Learning - Clusterização e ACM da Produção de Biodiesel

![Python](https://img.shields.io/badge/Python-3.10-blue)
![Pandas](https://img.shields.io/badge/Pandas-black?logo=pandas)
![Scikit-learn](https://img.shields.io/badge/ScikitLearn-orange)
![Plotly](https://img.shields.io/badge/Plotly-purple)
![Prince](https://img.shields.io/badge/Prince-red)

## 🎯 Sobre o projeto

**O problema:** Quem planeja a compra de insumos ou a oferta de biodiesel precisa saber quais matérias-primas dominam o volume produzido e como elas se distribuem entre estados e regiões. Neste projeto usei os dados públicos da ANP para procurar esses padrões sem definir uma variável resposta: um K-means para separar os registros por volume e uma Análise de Correspondência Múltipla (ACM) para ver quais matérias-primas se associam a quais estados e regiões.

**Quem usaria:** Um cenário plausível é a área de planejamento de compras ou de oferta de um produtor ou distribuidor de biodiesel. O projeto usa dados públicos e não tem cliente real, então esse uso é um cenário, não um caso aplicado.

**O resultado:** O K-means separa um grupo pequeno de registros de volume muito alto (266 de 4.745), formado quase só por óleo de soja. A ACM mostra uma associação clara do óleo de colza com o Sul (só o Rio Grande do Sul tem registros) e do óleo de palma/dendê com o Norte (Pará e Rondônia). O óleo de algodão aparece com fatias parecidas no Centro-Oeste e no Nordeste, e o óleo de soja está presente em todas as regiões.

**Ressalvas importantes:** o K-means usa uma única variável (o volume), então funciona como um corte por faixa de volume, e a leitura dos mapas da ACM é visual, sem teste estatístico. Parte dos padrões regionais se apoia em matérias-primas com pouquíssimos registros (veja Limitações).

---

## 📖 Etapas do Projeto

### 1. Coleta dos Dados

- **Fonte:** [Painel de Produção de Etanol e Biodiesel](https://dados.gov.br/dados/conjuntos-dados/paineis-de-producao-de-etanol-e-de-biodiesel), ANP
- **Arquivo:** Matéria-Prima utilizada na Produção de Biodiesel (CSV)
- **Período:** jan/2017 a ago/2023 (80 meses)
- **Tamanho:** 4.745 linhas, uma por mês, estado e produto (14 produtos, 14 estados, 5 regiões e 7 anos)

### 2. Metodologia

- Limpeza e padronização dos dados (Python + Pandas).
- **K-means** sobre `quantidade_m3` padronizada (Z-score), com k-means++. Testei de 2 a 5 clusters com o Elbow (WCSS) e a Silhueta. O Elbow não mostra um cotovelo nítido, e a silhueta é maior com k = 2 (cerca de 0,90, contra 0,78 com k = 3), que foi a escolha.
- **Teste qui-quadrado** entre as variáveis categóricas (região, estado, produto e ano): todos os pares têm associação significativa (p < 0,05). Região × Estado é esperado, porque cada estado pertence a uma região.
- **ACM** (Matriz de Burt, com correção de Benzécri) sobre quatro variáveis: região, estado, produto e ano. Retive 4 dimensões pelo critério de Greenacre (92,98% da inércia acumulada). Dois mapas fatoriais: Dim 1 × Dim 2 (53,10% da inércia) e Dim 3 × Dim 4 (39,88%). O ano ficou no centro dos mapas, sem poder discriminante.
- **Cluster pós-ACM:** para cada categoria, calculei a proporção dos seus registros que caem no cluster de grandes volumes e coloquei essa proporção nos mapas.

### 3. Resultados

**K-means** (2 clusters):

| Cluster | Registros | Média de `quantidade_m3` |
|---|---|---|
| Volumes regulares | 4.479 | cerca de 4.272 m³ |
| Grandes volumes | 266 | cerca de 78.119 m³ |

A média geral é de 8.411 m³, então o cluster de grandes volumes tem cerca de 9 vezes a média geral. Ele é formado em 99,62% por óleo de soja (265 dos 266 registros).

**ACM** (leitura dos mapas, conferida com a distribuição dos dados):

- **Sul → óleo de colza/canola.** Os 40 registros de colza são todos do Rio Grande do Sul.
- **Norte → óleo de palma/dendê.** É a maior fatia das linhas no Pará (36,0%) e em Rondônia (24,0%), seguidos pela Bahia (17,7%) e pelo Tocantins (11,8%).
- **Óleo de algodão:** fatias parecidas no Centro-Oeste (Mato Grosso 14,6%, Goiás 11,1%) e no Nordeste (Bahia 13,6%, Piauí 10,5%), então não é exclusivo de uma região.
- **Centro-Oeste e girassol:** o óleo de girassol aparece perto do Centro-Oeste no mapa, mas tem um único registro em toda a base (em Mato Grosso), então não sustenta um padrão.
- **Sem padrão regional:** gordura bovina, óleo de fritura usado e outros materiais graxos ficam perto da origem dos mapas. Possivelmente por serem subprodutos disponíveis em todo o território, mas não testei essa hipótese.

**Cluster pós-ACM** (% dos registros de cada categoria que estão no cluster de grandes volumes):

- **Óleo de soja:** 29,6% dos registros de soja (265 de 894) estão no cluster de grandes volumes. Nenhuma outra matéria-prima passa de 0,6%.
- **Estados:** Mato Grosso 15,0%, Rio Grande do Sul 14,9%, Goiás 11,7% e Paraná 7,8%. Os demais estados não têm nenhum registro nesse cluster.

**Síntese:** o K-means evidenciou a assimetria dos volumes de produção, e a ACM mostrou os padrões regionais na escolha da matéria-prima, coerentes com a geografia agrícola brasileira. As técnicas se mostraram complementares.

---

## ⚠️ Limitações

- **O K-means usa uma única variável.** Com só `quantidade_m3`, os clusters são faixas de volume, e não grupos definidos por várias características.
- **Os mapas da ACM são lidos visualmente.** Não fiz teste de estabilidade, e a proximidade no mapa não é a mesma coisa que uma associação forte nos dados.
- **Categorias raras pesam nos mapas.** O óleo de girassol tem 1 registro, o óleo de palmiste 7 e o ácido graxo de óleo de palma 15. A ACM tende a colocar categorias raras em posições extremas.
- **Estado e região entram juntos na ACM.** Como cada estado pertence a uma região, parte da "associação regional" vem dessa redundância.

<details>
<summary><b>Outras limitações</b></summary>

- **O número de clusters foi testado só de 2 a 5**, e o Elbow não tem cotovelo claro.
- **Há 13 registros com volume zero**, que entram no K-means e na ACM.
- **A análise não usa a ordem do tempo.** O ano entra só como categoria na ACM, e a unidade de análise é o registro de estado × produto × mês.
- **Associação, não causalidade.** Os padrões descrevem o período jan/2017 a ago/2023.

</details>

---

## ⚙️ Como Rodar

Testado com Python 3.10.

```bash
pip install -r requirements.txt
jupyter notebook src/script_biodiesel.ipynb
```

O notebook lê `data/biodiesel-materia-prima.csv` e grava os gráficos e mapas em `outputs/`. Os mapas fatoriais são arquivos HTML interativos: baixe o arquivo e abra no navegador.

<details>
<summary><b>📁 Estrutura do repositório</b></summary>

    unsupervised-learning-biodiesel-acm/
    ├── data/
    │   └── biodiesel-materia-prima.csv
    ├── src/
    │   └── script_biodiesel.ipynb
    ├── outputs/
    │   ├── kmeans_elbow.png
    │   ├── kmeans_silhueta.png
    │   ├── kmeans_centroides.png
    │   ├── acm_scree_plot.png
    │   ├── acm_mapa1_dim1_dim2.html
    │   ├── acm_mapa2_dim3_dim4.html
    │   ├── acm_mapa_cluster_mp_volume.html
    │   ├── acm_mapa_fatorial_cluster_produto.html
    │   ├── acm_mapa_fatorial_cluster_estado.html
    │   ├── hist_quantidade_m3_30.html
    │   └── boxplot_quantidade_m3.html
    ├── requirements.txt
    ├── .gitattributes
    └── README.md

</details>

---

## 🔗 Projetos relacionados

- [unsupervised-learning-clustering](https://github.com/bavsvalle/unsupervised-learning-clustering): clusterização hierárquica e K-means aplicados aos mesmos dados.
- [supervised-learning-biodiesel-regressao](https://github.com/bavsvalle/supervised-learning-biodiesel-regressao): regressão linear múltipla sobre os mesmos dados.
