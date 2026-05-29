# Instruções do Projeto: Northwind ETL

Este arquivo contém mandatos fundamentais para o projeto Northwind ETL. Todo o desenvolvimento deve aderir a estas instruções.

## Visão Geral do Projeto
- **Objetivo:** Construir um processo ETL confiável, idempotente e observável para processar pedidos do marketplace.
- **Volume de Dados:** ~100 mil pedidos por dia.
- **Destino:** Banco de dados Postgres analítico.
- **Visualização:** Dashboards no Grafana.

## Arquitetura & Convenções
- **Idempotência:** Todas as etapas de ingestão devem ser repetíveis sem duplicar dados.
- **Observabilidade:** Implementar logging e monitoramento para todos os fluxos críticos.
- **Resiliência:** Lidar com falhas parciais e garantir que nenhum dado seja perdido.

## Fluxo de Trabalho
- **Especificação:** Seguir os designs definidos no diretório `spec/`.
- **Desenvolvimento:** Garantir que todas as alterações sejam verificadas com testes.
- **Documentação:** Manter este arquivo e os arquivos em `spec/` atualizados com as decisões arquiteturais.
