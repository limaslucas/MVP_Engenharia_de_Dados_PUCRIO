# MVP | Engenharia de Dados | Pós-Graduação em Ciência de Dados e Analytics

## Apresentação
Este repositório contém o Minimum Viable Product (MVP) desenvolvido para a disciplina de Engenharia de Dados da Pós-Graduação em Ciência de Dados e Analytics da PUC-Rio. O objetivo deste projeto é construir um pipeline de dados completo utilizando tecnologias na nuvem, contemplando as etapas de busca, coleta, modelagem, carga e análise dos dados. Todo o desenvolvimento foi realizado na plataforma **Databricks** (Community Edition).

---

## 1. Objetivo
Antes da ingestão dos dados, o escopo do projeto foi definido com foco em extrair inteligência de negócios a partir de uma base de dados hospitalar fictícia. O projeto visa responder às seguintes perguntas estratégicas para a gestão do hospital:

1. Qual condição de saúde gera o maior tempo médio de internação?
2. Qual é o custo médio de cada tipo de procedimento realizado?
3. Existe uma relação entre o tempo de estadia e a satisfação do paciente?
4. Qual faixa etária tem a maior taxa de readmissão?
5. Como o gênero impacta o desfecho do tratamento?

---

## 2. Coleta e Armazenamento
Os dados utilizados consistem em uma base hospitalar fictícia estruturada em formato `.csv` (contendo 1000 registros originais). A coleta foi realizada de forma direta (upload manual) e os dados foram armazenados de forma persistente na nuvem através do **DBFS (Databricks File System)**, atuando como a nossa camada Raw/Bronze.

![Evidência da Coleta no DBFS](COLOQUE_O_LINK_DA_SUA_IMAGEM_AQUI)
*(Substitua pela sua imagem do upload no Databricks)*

---

## 🏗️3. Modelagem e Catálogo de Dados
Para otimizar as consultas e simular um ambiente analítico (Data Warehouse), os dados passaram por uma modelagem dimensional em **Esquema Estrela (Star Schema)**. A tabela original (flat) foi dividida em três tabelas: duas Dimensões e uma Fato.

### Catálogo de Dados (Dicionário)
* **Origem/Linhagem:** Arquivo CSV inserido no DBFS e transformado via PySpark.

**Tabela: `dim_paciente` (Dimensão)**
* **Patient_ID:** Identificador único do paciente. *(Numérico | Min: 1 / Max: 1000)*
* **Age:** Idade do paciente na admissão. *(Numérico | Min: 25 / Max: 78)* 
* **Gender:** Gênero biológico/identidade. *(Categórico | Valores: Female, Male)*

**Tabela: `dim_procedimento` (Dimensão)**
* **Procedimento_ID:** Chave primária artificial gerada no ETL. *(Numérico)*
* **Condition:** Condição clínica de entrada. *(Categórico | Valores de 'Allergic Reaction' a 'Stroke')*
* **Procedure:** Tratamento realizado. *(Categórico | Valores de 'Angioplasty' a 'X-Ray and Splint')*

**Tabela: `fato_internacao` (Fato)**
* **Patient_ID:** Chave estrangeira (FK).
* **Procedimento_ID:** Chave estrangeira (FK).
* **Cost:** Custo total do procedimento. *(Numérico | Min: 100 / Max: 25.000)*
* **Length_of_Stay:** Tempo de internação em dias. *(Numérico | Min: 1 / Max: 76)*
* **Satisfaction:** Satisfação do paciente. *(Numérico | Min: 2 / Max: 5)*
* **Readmission:** Indicador de retorno ao hospital. *(Categórico | Valores: No, Yes)*
* **Outcome:** Desfecho clínico. *(Categórico | Valores: Recovered, Stable)*

---

## 4. Carga (ETL)
A extração, transformação e carga (ETL) foram realizadas utilizando a linguagem **PySpark** dentro de Notebooks no Databricks. O processo incluiu a remoção de duplicatas, geração de chaves primárias artificiais (`monotonically_increasing_id`) e a junção dos conjuntos de dados para formar as tabelas `dim_paciente`, `dim_procedimento` e `fato_internacao`.

![Evidência do Código de ETL](COLOQUE_O_LINK_DA_SUA_IMAGEM_AQUI)
*(Substitua pelo print do seu código PySpark gerando as tabelas)*

---

## 5. Análise de Dados

### 5.1. Qualidade de Dados
Durante a fase de exploração, foi realizada uma auditoria de qualidade nos atributos do dataset[cite: 2]. Verificou-se que a base original possuía 1000 registros, mas continha 16 linhas com valores nulos espalhados pelas colunas. Como esses registros incompletos correspondiam a apenas 1,6% da base e poderiam distorcer cálculos sensíveis (como Custo Médio), optou-se pela estratégia de deleção (drop) dessas linhas nulas. O pipeline prosseguiu com 984 registros íntegros[cite: 2].

![Evidência da Qualidade de Dados](COLOQUE_O_LINK_DA_SUA_IMAGEM_AQUI)
*(Substitua pelo print do código onde você verifica e remove os nulos)*

### 5.2. Solução do Problema
Com os dados limpos e modelados, utilizamos a linguagem **SQL** diretamente no Databricks para responder às perguntas de negócio definidas no objetivo[cite: 2].

**Pergunta 1: Qual condição de saúde gera o maior tempo médio de internação?**
![Print SQL Pergunta 1](COLOQUE_O_LINK_DA_SUA_IMAGEM_AQUI)
* **Discussão:** [ESCREVA AQUI 1 OU 2 FRASES SOBRE O RESULTADO. Ex: "Notamos que a condição X exige mais tempo de leito, indicando que o hospital precisa focar mais recursos nessa área..."]

**Pergunta 2: Qual é o custo médio de cada tipo de procedimento realizado?**
![Print SQL Pergunta 2](COLOQUE_O_LINK_DA_SUA_IMAGEM_AQUI)
* **Discussão:** [ESCREVA AQUI SUA ANÁLISE SOBRE O CUSTO]

**Pergunta 3: Existe uma relação entre o tempo de estadia e a satisfação?**
![Print SQL Pergunta 3](COLOQUE_O_LINK_DA_SUA_IMAGEM_AQUI)
* **Discussão:** [ESCREVA AQUI SUA ANÁLISE SOBRE A SATISFAÇÃO]

**Pergunta 4: Qual faixa etária tem a maior taxa de readmissão?**
![Print SQL Pergunta 4](COLOQUE_O_LINK_DA_SUA_IMAGEM_AQUI)
* **Discussão:** [ESCREVA AQUI SUA ANÁLISE SOBRE READMISSÃO]

**Pergunta 5: Como o gênero impacta o desfecho do tratamento?**
![Print SQL Pergunta 5](COLOQUE_O_LINK_DA_SUA_IMAGEM_AQUI)
* **Discussão:** [ESCREVA AQUI SUA ANÁLISE SOBRE O DESFECHO]

---

## 6. Autoavaliação
*Dica do que escrever (exigência do trabalho[cite: 2]): Fale brevemente se você achou que conseguiu atingir os objetivos propostos lá no início. Comente qual foi a maior dificuldade que você teve (ex: aprender PySpark, entender a modelagem, etc.) e o que você faria no futuro para melhorar esse projeto (ex: conectar com uma ferramenta de BI como Power BI, automatizar o fluxo, etc).*
