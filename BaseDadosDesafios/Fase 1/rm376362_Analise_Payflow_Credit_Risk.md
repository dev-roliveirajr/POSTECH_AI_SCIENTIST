# Análise PayFlow

## CRISP-DM na Prática

## 1. Contexto e dor do negócio

A PayFlow (fintech fictícia) oferece crédito digital (empréstimo pessoal, cartão e BNPL). Nos últimos meses, a empresa percebeu um aumento de **inadimplência nos primeiros 90 dias** após a concessão.

Isso gera três dores simultâneas:

- Perda financeira direta (default)
- Risco operacional (time de cobrança sobrecarregado)
- Crescimento travado (medo de liberar crédito e piorar a carteira)

A diretoria quer uma solução que ajude a tomar decisões de crédito mais consistentes e escaláveis — sem depender apenas de regras manuais.

## 2. Business Understanding:

### 2.1. Problema de Negócio

Nos últimos meses, a PayFlow identificou um aumento na inadimplência de clientes nos primeiros 90 dias após a concessão de crédito. Esse cenário impacta diretamente a rentabilidade da empresa, aumenta a carga operacional da equipe de cobrança e reduz a confiança na expansão da carteira de crédito.

Atualmente, parte das decisões de concessão depende de regras de negócio estáticas, que possuem baixa capacidade de adaptação ao comportamento dos clientes. Como consequência, clientes com elevado risco de inadimplência podem ser aprovados, enquanto clientes com bom perfil podem ter crédito negado ou receber condições menos competitivas.

Diante desse contexto, torna-se necessário adotar uma abordagem baseada em dados que permita estimar o risco de inadimplência de forma consistente, apoiando decisões mais precisas, escaláveis e alinhadas aos objetivos estratégicos da empresa.

### 2.2. Stakeholders

Os principais stakeholders envolvidos no projeto são:

| Stakeholder | Interesse no projeto |
|---|---|
| *Diretoria Executiva* | Reduzir perdas financeiras e manter o crescimento sustentável da carteira de crédito. |
| *Área de Crédito* | Apoiar a decisão de aprovação, rejeição ou definição das condições de concessão de crédito. |
| *Área de Risco* | Melhorar a qualidade da carteira por meio de uma avaliação mais precisa do risco de inadimplência. |
| *Equipe de Cobrança* | Reduzir o volume de contratos inadimplentes e otimizar a utilização dos recursos operacionais. |
| *Clientes* | Receber decisões de crédito mais consistentes, adequadas ao seu perfil de risco e com menor dependência de análises subjetivas. |

### 2.3.Objetivos do Negócio

O principal objetivo deste projeto é apoiar a tomada de decisão na concessão de crédito por meio da estimativa da probabilidade de inadimplência dos clientes.

Para isso, busca-se atingir os seguintes objetivos:

- Reduzir a perda esperada da carteira de crédito;
- Tornar as decisões de concessão mais consistentes e menos dependentes de regras manuais;
- Manter o crescimento sustentável da carteira, equilibrando risco e oportunidade de negócio;
- Reduzir o impacto operacional causado pelo aumento da inadimplência;
- Fornecer uma base analítica para apoiar futuras estratégias de precificação, definição de limites de crédito e políticas de concessão.

### 2.4. Decisão de Negócio

O modelo desenvolvido não substituirá a política de crédito da PayFlow, mas atuará como um mecanismo de apoio à decisão. Para cada solicitação de crédito, o modelo estimará a probabilidade de inadimplência nos primeiros 90 dias, permitindo que a política de crédito determine a ação mais adequada.

Com base nessa estimativa, será possível decidir, por exemplo, pela aprovação ou rejeição da solicitação, pela concessão de limites diferenciados, pelo ajuste da taxa de juros de acordo com o nível de risco ou pelo encaminhamento de casos específicos para análise manual.

### 2.5.KPIs

O principal indicador de negócio para avaliação do sucesso da solução será a **redução da perda esperada da carteira de crédito**.

A perda esperada representa a exposição financeira estimada decorrente da possibilidade de inadimplência e está relacionada principalmente aos seguintes componentes:

- **Probability of Default (PD):** representa a probabilidade de ocorrência de default dentro de um determinado horizonte de tempo;
- **Loss Given Default (LGD):** representa o percentual da exposição financeira que não será recuperado após a ocorrência do default;
- **Exposure at Default (EAD):** representa o valor financeiro exposto no momento em que ocorre o default.

Embora a perda esperada considere esses três componentes, o foco principal deste projeto será a estimativa da Probability of Default (PD), permitindo que a PayFlow identifique previamente situações de maior risco e aprimore suas decisões de concessão de crédito.

Como o objetivo de negócio é reduzir a perda esperada da carteira, as metas analíticas devem avaliar se a estimativa de risco produzida pelo modelo é útil para a decisão de crédito. São Elas:

- AUC será utilizada para avaliar a capacidade de ordenar clientes segundo seu risco de default;
- Recall permitirá acompanhar a capacidade de identificar os casos que efetivamente entram em default;
- Calibração será importante para verificar se as probabilidades estimadas são coerentes com a ocorrência observada de default;
- Custo dos erros de decisão permitirá avaliar o impacto econômico de falsos positivos e falsos negativos, conectando o desempenho analítico à redução da perda esperada.

## 3. Data Understanding:

### 3.1. Concepção dos dados

A PayFlow possui diferentes fontes de dados que representam o perfil dos clientes, seu histórico de crédito, relacionamento com a instituição e características das operações de crédito. Essas informações são integradas para formar uma visão consolidada dos clientes e disponibilizadas para o processo de análise de risco.

#### 3.1.1. Fontes de dados

As principais fontes consideradas no processo são:

- CRM e cadastro de clientes: idade, renda e informações profissionais;
- Core bancário e sistemas de crédito: valor solicitado, prazo, taxa de juros e contratos ativos;
- Bureaus de crédito: score, inadimplências anteriores e histórico de atrasos;
- Aplicativo e canais de aquisição: canal de aquisição e informações relacionadas à origem da solicitação;
- Sistemas de atendimento: reclamações e atendimentos realizados;
- Sistemas de crédito e garantias: existência de avalista ou garantidor.

#### 3.1.2. Disponibilidade no momento da decisão

Temos informações que representam o estado do cliente no momento da decisão e informações que representam eventos posteriores.

#### 3.1.3. Integração dos dados

As informações provenientes das diferentes fontes são integradas por identificadores comuns, formando uma base analítica utilizada pelo pipeline de modelagem.

#### 3.1.4. Granularidade e composição

A base está estruturada em nível de cliente, com um registro por `id_cliente`.

As variáveis disponíveis são organizadas em quatro grandes dimensões:

| Dimensão | Variáveis |
|---|---|
| *Identificação* | id_cliente |
| *Perfil do cliente* | idade, renda_mensal, tempo_emprego_anos, autonomo |
| *Histórico e exposição de crédito* | score_credito, qtde_cartoes, qtde_contratos_abertos, utilizacao_credito, inadimplencias_anteriores, dias_atraso_max_12m |
| *Relacionamento* | reclamacoes_6m |
| *Características da operação* | valor_solicitado, prazo_meses, juros_mensal_pct, tipo_produto |
| *Contexto e garantias* | possui_avalista, canal_aquisicao, regiao |
| *Eventos posteriores — leakage* | parcelas_pagas_ate_3m, atraso_primeira_parcela_dias, status_apos_90d |
| *Variável-alvo* | default_90d |

### 3.2.Análise Exploratória dos Dados

A base disponibilizada possui **5.000 registros e 23 variáveis**, apresentando um registro por cliente. A variável `default_90d` representa o target do projeto, enquanto as demais variáveis descrevem características cadastrais, financeiras, de crédito, relacionamento e operação. O dataset possui **5.000 identificadores de clientes distintos**, não apresentando duplicidade nessa dimensão.

Quanto aos tipos de dados, a base é composta principalmente por variáveis numéricas, além das variáveis categóricas `canal_aquisicao`, `regiao`, `tipo_produto` e `status_apos_90d`. Essa estrutura permitirá a aplicação de técnicas de transformação e codificação na etapa de preparação dos dados.

A distribuição do target apresenta **4.391 clientes sem default (87,82%) e 609 com default (12,18%)**. Observa-se, portanto, um desbalanceamento da variável-alvo, aspecto que será considerado posteriormente na modelagem e na definição das métricas de avaliação.

Foram identificados valores ausentes em duas variáveis: `renda_mensal`, com 3,84% de ausência, e `tempo_emprego_anos`, com 10,52%. As demais variáveis não apresentam valores ausentes. A análise das estatísticas descritivas também indica distribuições assimétricas em algumas variáveis, como `renda_mensal` e `valor_solicitado`, além da presença de valores elevados em `dias_atraso_max_12m`, que serão investigados na etapa de preparação.

Na análise inicial das relações com o target, algumas variáveis apresentam associação relevante com a ocorrência de default. Entre elas, destacam-se `dias_atraso_max_12m`, `valor_solicitado` e `inadimplencias_anteriores`, enquanto `score_credito` apresenta relação inversa com o evento. Essas associações são exploratórias e não representam, isoladamente, relações causais ou critérios definitivos para seleção de variáveis.

Também foram observadas associações muito fortes entre o target e as variáveis `parcelas_pagas_ate_3m`, `atraso_primeira_parcela_dias` e `status_apos_90d`. Como essas informações estão relacionadas a eventos posteriores à concessão, elas caracterizam data leakage e serão tratadas como tal na etapa de preparação dos dados.

### 3.3. Problemas Identificados nos Dados

A análise exploratória permitiu identificar alguns pontos que precisam ser considerados nas etapas seguintes do projeto.

Foram identificados valores ausentes em `renda_mensal` (3,84%) e `tempo_emprego_anos` (10,52%). As demais variáveis não apresentam valores ausentes, sendo necessário definir posteriormente a estratégia de tratamento dessas duas variáveis.

Também foram observadas distribuições assimétricas e valores extremos em algumas variáveis, como `renda_mensal`, `valor_solicitado` e `dias_atraso_max_12m`. Entretanto, essas observações permanecem dentro dos domínios definidos para as respectivas variáveis e não foram caracterizadas automaticamente como erros. A necessidade de tratamento será avaliada na etapa de preparação dos dados, considerando o comportamento das variáveis e o algoritmo utilizado.

A variável `default_90d` apresenta desbalanceamento, com 12,18% de registros positivos e 87,82% negativos. Esse aspecto será considerado na preparação e, principalmente, na definição das métricas e critérios de avaliação dos modelos.

Por fim, foram identificadas três variáveis que representam informações posteriores ao momento da decisão: `parcelas_pagas_ate_3m`, `atraso_primeira_parcela_dias` e `status_apos_90d`. Essas variáveis caracterizam data leakage, pois não estariam disponíveis no momento em que a decisão de crédito é tomada.

Não foram identificadas, na análise inicial, inconsistências evidentes nos domínios e categorias das variáveis, como valores negativos indevidos, categorias não previstas ou valores fora dos limites definidos no dicionário de dados.

## 4. Data Preparation:

### 4.1. Definição da Variável-Alvo e dos Preditores

A variável-alvo do modelo será `default_90d`, fornecida no dataset como uma variável binária que indica se o cliente entrou em default dentro dos primeiros 90 dias após a concessão. O valor 1 representa a ocorrência do evento e o valor 0 representa sua não ocorrência.

A definição dos preditores seguirá uma regra temporal: somente informações disponíveis no momento da decisão de crédito poderão ser utilizadas como variáveis de entrada do modelo. Dessa forma, além da relevância para a previsão do risco, será considerada a disponibilidade da informação no momento em que a decisão é tomada.

Variáveis que representam eventos posteriores à concessão serão identificadas e tratadas na etapa de Data Leakage. O objetivo é garantir que o conjunto final de preditores represente as informações que efetivamente estariam disponíveis em uma situação real de decisão de crédito, evitando que informações futuras produzam uma estimativa de risco artificialmente otimista.

O `id_cliente` será utilizado apenas para identificação e rastreabilidade dos registros, não sendo utilizado como variável preditora.

### 4.2.Tratamento de Data Leakage

As variáveis `parcelas_pagas_ate_3m`, `atraso_primeira_parcela_dias` e `status_apos_90d` serão excluídas do conjunto de preditores antes da etapa de modelagem, pois representam informações posteriores ao momento da decisão de crédito.

Embora apresentem forte relação com o `default_90d`, essas variáveis não estariam disponíveis no momento da concessão e, portanto, seu uso produziria uma estimativa de risco artificialmente otimista.

### 4.3.Tratamento de Valores Ausentes

Foram identificados valores ausentes nas variáveis `renda_mensal` e `tempo_emprego_anos`, com maior concentração nesta última. As demais variáveis da base não apresentam valores ausentes.

O tratamento será realizado sem utilização de informações do conjunto de teste ou validação, evitando que o processo de imputação introduza data leakage. Para as variáveis numéricas, será avaliada a utilização da mediana calculada exclusivamente sobre os dados de treinamento, por ser menos sensível a distribuições assimétricas e valores extremos.

Além da imputação, será considerada a criação de um indicador de ausência quando houver evidência de que a própria falta da informação possa carregar sinal relevante para o risco de crédito. Essa decisão será validada posteriormente durante a modelagem, comparando o desempenho das alternativas.

### 4.4.Tratamento de Variáveis Categóricas

As variáveis categóricas `canal_aquisicao`, `regiao` e `tipo_produto` serão transformadas para um formato adequado aos algoritmos de modelagem. Como suas categorias não possuem uma relação de ordem natural, será utilizado One-Hot Encoding, evitando a criação de relações numéricas artificiais entre as categorias.

As variáveis binárias `autonomo` e `possui_avalista` já estão representadas numericamente pelos valores 0 e 1 e serão mantidas nessa forma, sem necessidade de aplicação de One-Hot Encoding.

O encoding será ajustado exclusivamente sobre os dados de treinamento e posteriormente aplicado aos conjuntos de validação e teste, evitando que informações desses conjuntos sejam incorporadas ao processo de preparação dos dados.

### 4.5.Tratamento de Valores Extremos (outliers)

A análise exploratória identificou distribuições assimétricas e valores extremos em algumas variáveis numéricas, como `renda_mensal`, `valor_solicitado` e `dias_atraso_max_12m`. Entretanto, as observações estão dentro dos domínios definidos para as respectivas variáveis e, portanto, não serão consideradas erros automaticamente.

Os valores extremos serão preservados inicialmente, evitando a exclusão de informações potencialmente relevantes para a avaliação de risco. Durante a preparação dos dados, será avaliada a aplicação de capping para variáveis com caudas excessivas, utilizando limites calculados exclusivamente sobre os dados de treinamento. A necessidade e o impacto desse tratamento serão posteriormente avaliados pelo desempenho dos modelos.

### 4.6. Padronização das Variáveis Numéricas

As variáveis numéricas serão padronizadas quando necessário para os algoritmos que apresentam sensibilidade à escala das variáveis, especialmente a Regressão Logística.

A padronização será ajustada exclusivamente sobre os dados de treinamento e posteriormente aplicada aos conjuntos de validação e teste. Para os modelos baseados em árvores, como Random Forest e Gradient Boosting, a padronização não será necessária, pois esses algoritmos não dependem da escala das variáveis para realizar as divisões.

## 5. Modeling:

### 5.1. Formulação do Problema

O problema será formulado como uma tarefa de classificação binária supervisionada, tendo o cliente como unidade de análise e `default_90d` como variável-alvo.

O objetivo do modelo será estimar a probabilidade de que um cliente entre em default nos primeiros 90 dias após a concessão do crédito. O horizonte de 90 dias está alinhado tanto ao problema de negócio identificado pela PayFlow quanto ao target disponibilizado na base.

O evento a ser previsto será definido diretamente pelo `default_90d` fornecido no dataset:

- valor 1 representa a ocorrência de default no período de 90 dias;
- valor 0 representa sua não ocorrência.

A documentação disponibilizada não detalha a regra operacional utilizada para caracterizar o default; portanto, este projeto utilizará o target conforme fornecido, sem inferir ou redefinir essa regra.

A saída do modelo será uma probabilidade de default (PD) para cada cliente. Essa estimativa será utilizada como insumo para a política de crédito, apoiando decisões relacionadas à aprovação, condições e tratamento de diferentes níveis de risco.

### 5.2. Estratégia de Modelagem e Escolha dos Algoritmos

A estratégia de modelagem será baseada na comparação entre um modelo baseline e modelos candidatos com maior capacidade de capturar relações não lineares entre as características dos clientes e a ocorrência de default.

Como baseline, será utilizada a Regressão Logística, por ser amplamente utilizada em problemas de risco de crédito e permitir estimar diretamente a probabilidade de default.

Como modelos candidatos, serão avaliados:

- **Random Forest:** modelo baseado em múltiplas árvores de decisão, capaz de representar relações não lineares e interações entre variáveis, oferecendo maior flexibilidade em relação ao baseline.
- **Gradient Boosting:** modelo de ensemble que constrói árvores sequencialmente, buscando corrigir os erros dos modelos anteriores e potencialmente alcançar maior capacidade preditiva.

A comparação considerará o trade-off entre interpretabilidade e performance.

A Regressão Logística oferece maior facilidade de interpretação e explicação das relações entre as variáveis e o risco, enquanto os modelos baseados em árvores apresentam maior flexibilidade para capturar padrões complexos nos dados, potencialmente ao custo de maior complexidade de interpretação.

A avaliação dos modelos não será baseada apenas em uma métrica isolada. Serão considerados AUC, recall, calibração e custo dos erros de decisão, além da capacidade de gerar probabilidades de default úteis para a tomada de decisão. O modelo final será escolhido considerando o equilíbrio entre desempenho preditivo, qualidade das probabilidades estimadas, impacto econômico e interpretabilidade.

## 6. Evaluation:

### 6.1.Validação e Comparação dos Modelos

A validação será realizada por meio de holdout estratificado, separando os dados em conjuntos de treinamento e teste, combinado com validação cruzada estratificada no conjunto de treinamento. Como a base disponibilizada não possui uma variável temporal de concessão, não será aplicado split temporal.

Os modelos serão comparados utilizando AUC-ROC, recall e calibração, além do impacto econômico dos erros de classificação. A acurácia não será utilizada como principal métrica devido ao desbalanceamento do target.

O conjunto de teste permanecerá separado durante todo o desenvolvimento e será utilizado para a avaliação final do modelo selecionado.

Os três modelos: Regressão Logística, Random Forest e Gradient Boosting serão avaliados utilizando os mesmos conjuntos de dados e critérios, permitindo uma comparação consistente entre suas capacidades de discriminação, calibração e desempenho operacional.

### 6.2.Threshold e Seleção do Modelo

O threshold de decisão não será definido necessariamente em 50%. Serão avaliados diferentes pontos de corte considerando o impacto de falsos positivos e falsos negativos sobre a decisão de crédito.

O threshold escolhido deverá buscar o melhor equilíbrio entre a identificação de clientes de maior risco e a manutenção de clientes de bom risco na carteira, considerando o custo econômico dos erros e o objetivo de redução da perda esperada.

A seleção do modelo final considerará conjuntamente performance, calibração, custo dos erros e interpretabilidade. Dessa forma, o modelo escolhido não será necessariamente aquele com maior AUC, mas aquele que apresentar o melhor equilíbrio entre desempenho analítico e impacto para o negócio.

## 7. Deployment:

### 7.1. Disponibilização do Modelo

O modelo selecionado será disponibilizado como um serviço de inferência por meio de uma API REST, permitindo que os sistemas de crédito da PayFlow consultem o risco de uma nova solicitação de forma padronizada e automatizada.

A API receberá como entrada as variáveis disponíveis no momento da concessão, seguindo o mesmo processo de preparação utilizado durante o treinamento, incluindo tratamento de valores ausentes e transformação das variáveis categóricas.

Como resposta, o serviço retornará a Probability of Default (PD) estimada para o cliente e, quando aplicável, a classificação de risco ou decisão associada ao threshold definido pela política de crédito.

O serviço deverá utilizar a mesma versão do pipeline de preparação e do modelo validado, garantindo consistência entre treinamento e inferência. O modelo será versionado para permitir rastreabilidade das decisões e facilitar sua substituição em futuras atualizações.

A API funcionará como uma camada de integração entre o modelo e os sistemas de decisão de crédito, sem substituir a política de crédito da PayFlow. A política continuará responsável por utilizar a PD estimada, juntamente com as demais regras de negócio, para determinar a ação a ser tomada.

## 8. MLOps:

### 8.1. Monitoramento e Operação

O Após a disponibilização do modelo, será realizado monitoramento contínuo para acompanhar a qualidade dos dados e o comportamento do modelo em produção.

Serão monitorados indicadores de data drift, identificando alterações relevantes na distribuição das variáveis de entrada, e model drift, acompanhando possíveis mudanças na capacidade preditiva do modelo ao longo do tempo.

Quando os resultados observados estiverem disponíveis, também serão acompanhadas métricas como AUC, recall e calibração, permitindo identificar uma eventual degradação da performance em relação aos resultados obtidos durante a validação.

### 8.2. Retreinamento

O modelo será retreinado quando forem identificados sinais relevantes de degradação de performance ou mudanças significativas no perfil dos dados.

O processo de retreinamento deverá reproduzir as mesmas etapas de preparação, validação e avaliação utilizadas no desenvolvimento inicial. Uma nova versão somente será disponibilizada em produção após apresentar desempenho adequado nos critérios definidos para o projeto.

### 8.3. Logging e Rastreabilidade

As requisições realizadas à API e as respostas produzidas pelo modelo serão registradas para permitir monitoramento, auditoria e rastreabilidade das decisões.

Os registros deverão permitir identificar a versão do modelo utilizada, a data da inferência, a probabilidade de default estimada e os principais metadados necessários para investigação de eventuais problemas, respeitando as políticas de segurança e privacidade aplicáveis.

### 8.4. Governança e Versionamento

O modelo, o pipeline de preparação dos dados e seus respectivos parâmetros deverão ser versionados, permitindo reproduzir uma versão específica utilizada em produção.

Também deverá ser mantido o versionamento da definição do target, incluindo sua regra de construção e horizonte de observação. Essa prática é especialmente importante em modelos de risco de crédito, pois alterações na definição de default podem tornar os resultados de diferentes versões do modelo não diretamente comparáveis.

A documentação deverá registrar as versões do dataset, das variáveis utilizadas, do pipeline de preparação, do modelo e dos critérios de avaliação, garantindo maior transparência e rastreabilidade durante todo o ciclo de vida da solução.
