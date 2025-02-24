
# Tópicos abordados 📑

1. **Exemplo de cálculo 🧮**
2. **Contrato de payload 📝**
3. **Cadastros necessários 📚**
4. **Tecnologias/configuraçoes ⚙️**
5. **Diagramas 📊**


---

## 🧮 Exemplo de cálculo de impostos para cerveja (engradado)

###  ⭐ Cenário: Venda de Cerveja de SP para MG
- **UF do Emitente:** SP  
- **UF do Destinatário:** MG  
- **idDest:** 2 (Operação Interestadual)  
- **indPres:** 1 (Venda presencial)  
- **Produto:**  
  - **Descrição:** Engradado de Cerveja (12 garrafas de 600ml)  
  - **Quantidade:** 5 engradados  
  - **Valor Unitário:** R$90,00 (por engradado)  
  - **CFOP:** 6102 (Venda Interestadual)  
  - **NCM:** 2203.00.00 (Cerveja de Malte)  
  - **CST:** 10 (Tributado com Substituição Tributária)  


###  💰 **Alíquotas Aplicáveis**
- **ICMS SP → MG:** 12%  
- **ICMS ST MG:** 25%  
- **FCP MG:** 2%  
- **MVA (Margem de Valor Agregado) para cerveja:** 45%  
- **PIS:** 1,65%  
- **COFINS:** 7,60%  
- **ICMS Interno MG:** 25% (para cálculo do DIFAL)  




### ✅ **1. Cálculo do ICMS Próprio**
**Base de cálculo do ICMS:**  
Base = quantidade × valorUnitário = 5 × 90 = R$450,00

**ICMS próprio (SP - origem):**  
ICMS = 450 x 12% = R$54,00




### ✅ **2. Cálculo do ICMS ST**
A base do ICMS ST considera o **MVA (45%)**:  
Base ICMS ST = 450 x (1 + 45%) = 450 x 1.45 = R$652,50

**ICMS destino (MG - 25%)**:  
ICMS MG = 652,50 x 25% = R$163,13

**ICMS ST a pagar**:  
ICMS ST = 163,13 - 54,00 = R$109,13




### ✅ **3. Cálculo do DIFAL**
DIFAL é devido pois é uma venda para consumidor final **não contribuinte**.  

DIFAL = (Base ICMS) x (Alíquota Interna MG - Alíquota Interestadual SP -> MG)
DIFAL = 450 x (25% - 12%) = 450 x 13% = R$58,50

Inclusão do FCP no DIFAL (se aplicável): FCP DIFAL = 450 x 2% = R$9,00


### ✅ **4. Cálculo do FCP**
FCP = 652,50 x 2% = R$13,05



### ✅ **5. Cálculo do PIS e COFINS**
**Base PIS/COFINS = R$450,00**  

PIS = 450 x 1,65% = R$7,43


COFINS = 450 x 7,60% = R$34,20


##  🔍 **Resumo dos Impostos Calculados**

| Imposto         | Valor (R$) |
|----------------|-----------|
| **ICMS SP (12%)** | 54,00 |
| **ICMS ST MG** | 109,13 |
| **FCP MG (2%)** | 13,05 |
| **PIS (1,65%)** | 7,43 |
| **COFINS (7,60%)** | 34,20 |
| **DIFAL (13%)** | 58,50 |



## 📝 **Conclusão**
- Como a venda é **interestadual para consumidor final**, o **DIFAL deve ser calculado**.  
- O **ICMS ST (CST 10) é aplicado**, pois a cerveja está na lista de produtos com substituição tributária.  
- O **FCP é calculado sobre a base do ICMS ST**, pois MG cobra o fundo de combate à pobreza.  
- O **PIS e COFINS são calculados normalmente sobre a base de venda**.  

---

# 📝**Contrato de payload (recebido dos erps)**

* **Motivações do contrato de payload a receber dos ERPs:** 
   - Receberemos somente os campos pertinentes aos cálculos de impostos dos produtos, para evitarmos alguns cenários como:
      - Responsábilidades extras, além de calcular imposto, ter de ler/alterar xml completo de nota fiscal, o que seria mais custoso e resultaria em uma maior latência.
      - Não termos um acoplamento tão grande com a sefaz, exemplo de cenário hipotético: tag de imposto muda de local dentro do xml da nota, teríamos de alterar nosso sistema. Tendo um contrato "alto nível", a responsabilidade de passar os campos corretamente conforme contrato, é dos ERPs.

* **Contrato de payload recebido para cálculo**:
    - Chave da nota    (para rastreabilidade de cálculos)
    - UF do emitente   (para identificação de alíquotas, considerando a uf do destinatário)
    - UF destinatário  (para identificação de alíquotas, considerando a uf do emitente)
    - idDest           (Op. interna/interestadual/exportação)
    - indPres          (Indica como a venda foi realizada, o que pode impactar na tributação, principalmente para PIS/COFINS e possíveis incentivos fiscais)
    - Produto
        - nItem     (sequência do item presente na nota fiscal, para posterior identificação, por parte do erp, que ficará com a responsábilidade de acoplar no xml da nota, o imposto já calculado)
        - quantidade
        - valorUnitario
        - cfop      (para identificação do tipo de operação)
        - ncm
        - cst


---

# 📚 **Cadastros necessários**

* Cadastro de alíquotas por uf (Cadastro necessário, para fácil manutenção das regras)
    - Exemplo: Venda de SP -> RJ = 12% icms, 18% st
    - Necessária uma tabela, onde teriam as ufs e os possíveis impostos para (uf emit X uf dest):
        - UfEmitente
        - UfDestinatario
        - AliquotaIcms
        - AliquotaIcmsSt
        - AliquotaFcp
        - AliquotaMva

* Tabelas para histórico de cálculo, contendo campos chave para os cálculos e os valores de impostos calculados:
    - Tabela "HistoricoCalculoNfe":
        - Chave da nota (PK)
        - UF do emitente
        - UF destinatário
        - idDest
        - indPres
        - totalIcms
        - totalIcmsSt
        - totalPis
        - totalCofins
        - totalFcp
        - totalDifal
        - versao (versão do sistema no momento do cálculo, para num futuro reprocessamento, não precisar refazer o cálculo, sendo a mesma versão)
    - Tabela "HistoricoCalculoItem":
        - HistoricoCalculoNfe (FK)
            - nItem
            - CFOP
            - quantidade
            - valorUnitario
            - ncm
            - cst
            - icmsCalculado
            - icmsStCalculado
            - pisCalculado
            - cofinsCalculado
            - fcpCalculado
            - difalCalculado


---

#  ⚙️ **Configuração/Ferramentas:**
* **Servicebus**: 
    - 1 fila por ERP
        - Considerando baixa latência de resposta por ERP, onde as filas serão consumidas em paralelo
        - Isolamento de consumo de mensagens de erps, facilitando identificação de gargalos, erros, necessidade de reprocessamentos 
    - Prefetch count: 5
       - Valor baixo, pensando no cenário de 6 erps, 6 consumidores, momento de pico, sobrecarga de ms e baixa latência
    - Habilitar partitioned queues (para maior throughput, disponibilidade, escalabilidade e concorrência)

* **Kubernets**
    - Liveness e Readiness Probes (para garantir que os pods não fiquem presos)
    - autoscaling:
        - cpu 85%
        - memória 80%
        - minimo de 2 instâncias
        - máximo de 6 instâncias
* **Java/spring**:
    - Redis: para obter os parâmetros de alíquotas(por ufs emit x dest) pré carregados da base em cálculos anteriores.
    - Spring Cloud Stream e project reactor: Para consumo de mensagens de forma reativa, não bloqueante.
    - Datadog: (Para monitoramento, rastreabilidade de erros, visualização de métricas e garantir que as configurações de infraestrutura estarão atendendo as necessidades do produto)
        - Logs
        - Traces
        - Métricas
        - Alertas

---
📊 Diagramas:
