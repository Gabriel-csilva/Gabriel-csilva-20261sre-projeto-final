# Arquitetura do Sistema (RM-ODP)

Este documento descreve a arquitetura do pipeline de dados Northwind utilizando os cinco pontos de vista do framework RM-ODP, com foco em uma stack moderna de dados: MinIO, ClickHouse, Python e Streamlit.

---

## 1. Enterprise Viewpoint (Ponto de Vista de Negócio)
**Objetivo:** Processar ~100k pedidos diários para alimentar dashboards analíticos com dados confiáveis, utilizando armazenamento de objetos e um banco colunar de alta performance.

- **Stakeholders:** Operação Northwind, Time de Dados, SRE.
- **Processo:** Upload para MinIO -> ETL Python -> Carga no ClickHouse -> Visualização no Streamlit.
- **Valor:** Insights em tempo real com baixa latência de consulta.
- **Atendimento:** RF-001 (Ingestão), RF-005 (SLA), RNF-003 (Latência).

## 2. Information Viewpoint (Ponto de Vista de Informação)
**Objetivo:** Definir a semântica e os estados do dado em um ambiente de Data Lakehouse.

- **Modelo de Dados:**
  - **Bronze Layer (MinIO):** Arquivos CSV brutos (imutáveis).
  - **Silver Layer (ClickHouse):** Dados limpos e tipados, prontos para consulta.
- **Estados:** `Landing` (MinIO), `Processing` (Python), `Analytical` (ClickHouse).
- **Atendimento:** RF-003 (Validação), RNF-008 (Integridade), RF-002 (Idempotência).

## 3. Computational Viewpoint (Ponto de Vista Computacional)
**Objetivo:** Decomposição em componentes funcionais distribuídos.

- **Storage Component (MinIO):** Gerencia o ciclo de vida dos arquivos e serve como fonte da verdade.
- **ETL Engine (Python):** Orquestra a leitura do MinIO, transformações e carga no ClickHouse.
- **Analytical Engine (ClickHouse):** Armazenamento colunar otimizado para agregações rápidas.
- **UI Component (Streamlit):** Dashboard interativo para consumo final dos dados.

## 4. Engineering Viewpoint (Ponto de Vista de Engenharia)
**Objetivo:** Infraestrutura e distribuição dos serviços.

- **Storage:** MinIO rodando em container (Docker) ou instância dedicada, expondo API S3.
- **Database:** ClickHouse otimizado para processamento analítico paralelo (OLAP).
- **App:** Streamlit acessível via navegador para os stakeholders.
- **Atendimento:** RNF-004 (Escalabilidade), RNF-001 (Resiliência).

## 5. Technology Viewpoint (Ponto de Vista de Tecnologia)
**Objetivo:** Escolhas tecnológicas específicas.

- **Storage:** MinIO (S3 Compatible).
- **Linguagem:** Python 3.x (Boto3 para MinIO, Clickhouse-Connect para o banco).
- **Banco de Dados:** ClickHouse (Engine: ReplacingMergeTree para idempotência).
- **Visualização:** Streamlit.
- **Monitoramento:** Prometheus + Grafana integrados aos logs do Python.

---

## ADR (Architecture Decision Records)

### ADR-01: Idempotência via ReplacingMergeTree (ClickHouse)
- **Contexto:** Necessidade de evitar duplicatas em um banco que prioriza escrita rápida.
- **Decisão:** Utilizar a engine `ReplacingMergeTree` do ClickHouse com a chave primária `order_id`.
- **Consequência:** Linhas duplicadas são removidas durante o processo de merge do banco, garantindo integridade sem overhead de transação ACID tradicional.

### ADR-02: MinIO como Landing Zone (Data Lake)
- **Contexto:** Necessidade de desacoplar a recepção dos arquivos do processamento imediato.
- **Decisão:** Todo arquivo recebido deve ser persistido primeiro no MinIO antes de ser processado.
- **Consequência:** Facilita o reprocessamento histórico (replay) e aumenta a resiliência a falhas no motor de ETL.

### ADR-03: Streamlit para Visualização Ágil
- **Contexto:** Necessidade de dashboards customizados e integrados diretamente com scripts Python.
- **Decisão:** Substituir Grafana por Streamlit para maior flexibilidade na lógica de apresentação.
- **Consequência:** Menor curva de aprendizado para o time de dados e integração nativa com o ecossistema Python.
