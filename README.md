# Desafio Técnico: Sistema Centralizado de Cálculo de Impostos

## **Contexto**  
A compania opera com **seis diferentes ERPs**, responsáveis por gerar **Notas Fiscais Eletrônicas (NFes)** para diferentes tipos de operações e produtos. Cada ERP possui sua própria estrutura de dados e regras de negócio, tornando o **cálculo de impostos inconsistente** e **dificultando a conformidade fiscal**.  

## **Objetivo do Desafio**  
Propor uma **arquitetura de alto nível** para um **sistema centralizado de cálculo de impostos**, levando em consideração:
 
### **1. Requisitos Funcionais**
✅ **Cálculo de impostos na Nota Fiscal** com base em regras tributárias atualizadas.  
✅ **Integração com os 36ERPs** da companhia para receber os dados fiscais em tempo real.  
✅ **Reprocessamento de documentos**, permitindo ajuste em caso de erro.  
✅ **Consulta do histórico de cálculos**, garantindo rastreabilidade para auditoria.  
 
### **2. Requisitos Não Funcionais**
⚡ **Baixa latência**, garantindo tempo de resposta rápido mesmo em alta carga.  
📈 **Escalabilidade**, suportando picos diários durante o faturamento.  
🛡️ **Alta disponibilidade**, garantindo continuidade do serviço sem downtime significativo.  
✅ **Consistência dos dados**, assegurando que cálculos sejam reprocessáveis e auditáveis.  
