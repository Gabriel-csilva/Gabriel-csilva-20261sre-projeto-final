# Requisitos Não Funcionais (RNF)

Este documento detalha os requisitos não funcionais, SLOs e SLIs para o pipeline de dados Northwind, focando em confiabilidade, performance e observabilidade.

## 1. Confiabilidade e Disponibilidade (SRE)

### RNF-001: Resiliência a Falhas de Infraestrutura
- **Descrição:** O sistema deve ser capaz de lidar com quedas temporárias na conexão com o banco de dados Postgres ou com a AWS sem perda de dados.
- **Métrica/Alvo:** Implementação de retentativas automáticas (retry logic) para conexões de rede.

### RNF-002: Disponibilidade do Pipeline
- **Descrição:** O pipeline deve estar operacional para execução conforme o agendamento diário definido.
- **SLO:** 99.9% de execuções diárias bem-sucedidas ao longo do mês.

## 2. Performance e Escalabilidade

### RNF-003: Tempo de Processamento (Latência)
- **Descrição:** O processamento total do lote diário de 100k pedidos deve ser concluído dentro de uma janela que não impacte a disponibilidade dos dashboards.
- **Métrica/Alvo:** Processamento concluído em menos de 15 minutos em uma instância t3.medium.

### RNF-004: Escalabilidade de Volume
- **Descrição:** O sistema deve ser capaz de processar picos de volume (ex: Black Friday) de até 1 milhão de registros sem falhas catastróficas.
- **Métrica/Alvo:** Consumo de memória constante (streaming/chunking) independentemente do tamanho do arquivo.

## 3. Observabilidade

### RNF-005: Centralização de Logs e Retenção
- **Descrição:** Todos os logs gerados devem ser persistidos para fins de auditoria e depuração.
- **Métrica/Alvo:** Retenção mínima de 15 dias para logs de erro e avisos.

### RNF-006: Monitoramento de Métricas em Tempo Real
- **Descrição:** Métricas de saúde do pipeline devem ser enviadas para visualização no Grafana.
- **Métrica/Alvo:** Latência de atualização do dashboard inferior a 5 minutos após o término da carga.

## 4. Segurança e Conformidade

### RNF-007: Gestão Segura de Segredos
- **Descrição:** Credenciais de banco de dados e chaves de API da AWS não devem ser armazenadas no código-fonte.
- **Métrica/Alvo:** 100% das credenciais geridas via Variáveis de Ambiente ou AWS Secrets Manager.

## 5. Integridade e Idempotência

### RNF-008: Garantia de Integridade Referencial
- **Descrição:** O sistema deve garantir que nenhum dado seja corrompido durante a fase de transformação.
- **SLI:** Taxa de integridade de dados (linhas válidas vs. linhas carregadas) de 100%.
