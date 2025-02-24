# **Avaliação Técnica – Arquiteto II**
 
## **Contexto**  
A companhia opera com **seis diferentes ERPs**, responsáveis por gerar **Notas Fiscais Eletrônicas (NFes)** para diferentes tipos de operações e produtos. Cada ERP possui sua própria estrutura de dados e regras de negócio, tornando o **cálculo de impostos inconsistente** e **dificultando a conformidade fiscal**.  
 
A legislação tributária brasileira é complexa e dinâmica, variando conforme:
- **Localização do remetente e destinatário**  
- **Tipo de operação (venda, transferência, devolução, etc.)**  
- **Categoria do produto (isenções, substituição tributária, alíquotas especiais, etc.)**  
 
Diante disso, a empresa identificou a necessidade de desenvolver um **sistema centralizado de cálculo de impostos**, garantindo que todas as notas fiscais sejam processadas com as regras tributárias corretas e sempre atualizadas. Esse sistema precisa **integrar-se com os diferentes ERPs** e ser capaz de escalar conforme a demanda, que **varia ao longo do dia**.
 
---
 
## **Objetivo do Desafio**  
O candidato deve propor uma **arquitetura de alto nível** para um **sistema centralizado de cálculo de impostos**, levando em consideração:
 
### **1. Requisitos Funcionais**
✅ **Cálculo de impostos na Nota Fiscal** com base em regras tributárias atualizadas.  
✅ **Integração com os 3 ERPs** da companhia para receber os dados fiscais em tempo real.  
✅ **Reprocessamento de documentos**, permitindo ajuste em caso de erro.  
✅ **Consulta do histórico de cálculos**, garantindo rastreabilidade para auditoria.  
 
### **2. Requisitos Não Funcionais**
⚡ **Baixa latência**, garantindo tempo de resposta rápido mesmo em alta carga.  
📈 **Escalabilidade**, suportando picos diários durante o faturamento.  
🛡️ **Alta disponibilidade**, garantindo continuidade do serviço sem downtime significativo.  
✅ **Consistência dos dados**, assegurando que cálculos sejam reprocessáveis e auditáveis.  
 
---
 
## **Cenário Técnico**  
O novo sistema deverá lidar com **grande volume de dados**, exigindo um design arquitetural robusto. Abaixo estão alguns números para orientar a solução:
 
🔹 **Carga de trabalho esperada:**  
- **10 mil requests por minuto**  
- Cada requisição tem um payload médio de **10 KB**  
- **Pico de faturamento ocorre em um período de 4 horas** diariamente  
 
🔹 **Padrão de carga:**  
- **80% leitura | 20% escrita**  
- **Leituras incluem consultas de cálculos anteriores e reprocessamentos**  
 
🔹 **Plano de capacidade:**  
- **Requisições simultâneas**
- **Consumo de banda**
- **Armazenamento**
 
---
 
## **Tarefa do Candidato**  
1. **Desenvolver uma proposta arquitetural**, explicando os principais componentes do sistema.  
2. **Definir tecnologias e frameworks** que poderiam ser utilizados para resolver o problema.  
3. **Elaborar um plano de escalabilidade e alta disponibilidade**, considerando os picos de carga.  
4. **Sugerir estratégias para otimização do cálculo dos impostos**, garantindo eficiência e atualização das regras tributárias.  
5. **Indicar como garantir rastreabilidade e auditoria dos cálculos fiscais**.  
 
O candidato pode apresentar sua solução em formato textual, diagramas de arquitetura ou uma combinação de ambos.  
 
---
 
## **Critérios de Avaliação**  
✔ **Clareza e coerência da arquitetura proposta**  
✔ **Capacidade de escalar e suportar alta demanda**  
✔ **Adoção de boas práticas de desenvolvimento e arquitetura**  
✔ **Viabilidade da solução no contexto de negócio e tecnologia**  
✔ **Inovação e eficiência na abordagem**  