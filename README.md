# Unsupervised Machine Learning - Clusterização e ACM da Produção de Biodiesel

![Python](https://img.shields.io/badge/Python-3.10-blue) ![Pandas](https://img.shields.io/badge/Pandas-green) ![Scikit-learn](https://img.shields.io/badge/ScikitLearn-orange) ![Plotly](https://img.shields.io/badge/Plotly-purple) ![Prince](https://img.shields.io/badge/Prince-red)

Este projeto aplica Análise de Correspondência Múltipla (ACM) e clusterização K-means para identificar padrões entre matéria-prima, estado e região na produção de biodiesel no Brasil, a partir de dados abertos da [ANP](https://www.gov.br/anp/pt-br).

---

## 📋 Etapas do Projeto

### 1. Coleta dos Dados
- **Fonte:** [Painel de Produção de Etanol e de Biodiesel](https://www.gov.br/anp/pt-br)
  - Arquivo: Matéria-Prima utilizadas na Produção de Biodiesel (CSV)

### 2. Metodologia
- Limpeza e padronização dos dados (Python + Pandas)
- Padronização da variável `quantidade_m3` (Z-score)
- K-means com 2 clusters (k-means++)
  - Método Elbow (WCSS) e Coeficiente de Silhueta para definição do número de clusters
- ACM com variáveis qualitativas: matéria-prima, estado e região
  - Mapa Fatorial 1: Dim 1 × Dim 2 (53,1% de inércia)
  - Mapa Fatorial 2: Dim 3 × Dim 4 (39,88% de inércia)
- Cluster pós-ACM: proporção de grandes produtores por estado e matéria-prima

### 3. Conclusão

#### K-means
- Dois clusters: majoritário (volumes regulares) e minoritário (maiores volumes)
- Cluster minoritário composto em 99,62% por óleo de soja
- Média do cluster de grandes volumes ~9× maior que a média geral

#### ACM
- Padrão geográfico claro na escolha da matéria-prima:
  - **Sul** (RS, SC, PR) → óleo de colza/canola
  - **Centro-Oeste** (Goiás) → óleo de girassol e óleo de algodão
  - **Norte** (PA, RO, TO) → óleo de palma/dendê
  - **Nordeste** (PI, BA) → óleo de algodão
- Sem padrão regional: gordura bovina, óleo de fritura usado e outros materiais graxos — subprodutos disponíveis em todo o território nacional

#### Cluster pós-ACM
- Óleo de soja lidera com 29,6% dos grandes produtores, concentrado no Sul e Centro-Oeste
- Estados com maior concentração de grandes volumes:
  - Rio Grande do Sul: 15,0%
  - Mato Grosso: 14,9%
  - Goiás: 11,7%
  - Paraná: 7,8%

#### Síntese
- K-means evidenciou a assimetria dos volumes de produção
- ACM revelou o padrão regional na escolha da matéria-prima, coerente com a geografia agrícola brasileira
- As técnicas se mostraram complementares

---

## 🗂 Estrutura do Repositório

```
ul-biodiesel-anacor/
│
├── data/                          # Dados brutos
├── outputs/                       # Gráficos e tabelas gerados
├── src/
│   └── script_biodiesel.ipynb    # Notebook principal
└── README.md
```

---

## 🔗 Projeto Anterior
[unsupervised-learning-clustering](https://github.com/bavsvalle/unsupervised-learning-clustering) — clusterização hierárquica e K-means aplicados aos mesmos dados
