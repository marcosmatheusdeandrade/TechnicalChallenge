
# Tópicos abordados 📑

1. **Exemplo de cálculo 🧮**
2. **Contrato de payload 📝**
3. **Cadastros necessários 📚**
4. **RPS**
5. **Consumo de banda**
6. **Armazenamento**
7. **Diagramas 📊**


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

# Requisições Simultâneas (RPS)

**A Carga de trabalho esperada é:**
- 10 mil requests por minuto  
- Cada requisição tem um payload médio de 10 KB
- Pico de faturamento ocorre em um período de 4 horas diariamente  

**Então, vamos considerar um volume esperado  de 2.400.000 notas em 4 horas, no pico de faturamento, o que significa:**
- 2.400.000 / 4 horas = 600.000 notas/hora
- 600.000 / 3.600 segundos = ~167 RPS (média)
- Considerando uma margem de seguraça, 2 x 167 → ~335 RPS
- **Objetivo, sistema suportar ~335 RPS**


**Tempo médio de uma requisição**
- Publicação do payload de entrada no Service Bus ~5ms
- Redis (cache de alíquotas) ~1-2 ms
- Cálculo (Java) ~5 ms
- Leitura do SQL Server: ~5-10 ms.
- Escrita no SQL Server: ~5-10 ms.
- Webhook para ERP (notificação do cálculo): ~10-15 ms.
- **Total estimado:** ~40 ms por requisição

**Cálculo por thread:**
- 1 thread = 1s / 40ms = ~25 RPS
- Para 335 RPS, então **precisamos de ~14 threads rodando em paralelo**.


**Infraestrutura necessária (para suportar 335 RPS):**
- CPU e Threads
    - Cada core suporta ~25 RPS.
    - 335 RPS / 25 RPS por core = 14 vCPUs.
        - Trabalhar com minimo de 2 vCPUs(período ocioso) à 18 vCPUs(momentos de pico).

**Memória RAM**
- Redis precisa de ~2 GB para armazenar cache.
- SQL Server precisa de ~8 GB para evitar swap.
- Aplicação Java (Spring Boot) precisa de 1 a 1.5 GB por pod.

**Kubernetes e Auto Scaling**
- Mínimo de 2 pods (cada um com 1 vCPUs e 1 GB RAM) e permitir escalonamento automático até 9 pods e cada pod até 2vCPUs.
- Trigger de auto scaling: CPU acima de 70%

**Banco de Dados**
- Redis (Cache de alíquotas)
    - Configurar TTL dinâmico para evitar uso excessivo de memória.
    - Replicação para alta disponibilidade.

**SQL Server** (Outbox Pattern)
- Tabela particionada para evitar locking excessivo.
- Gravação assíncrona para melhor performance.
- Base de leitura e base de escrita
- Outbox Pattern com tempo de job pré configurado, para replicação em batchs e não sobrecarregar as operações de escrita/leitura de cada base


-----

# Consumo de Banda

- Se cada documento tem 10 KB, e recebemos 2.400.000 por dia:
    - Entrada total/dia = 2.400.000 × 10 KB = 24 GB/dia
    - 24 GB / 4 horas = 6 GB/hora
    - 6 GB / 3600 seg = ~1.7 MB/s.
    - Então, precisamos de uma banda mínima de 15 Mbps (1.7 MB x 8 = 13.6Mbps).

- Cálculo de tráfego de leitura
    - Considerando a leitura de cada registro, vamos considerar margem de reprocessamento (2X), cada leitura retornando ~10 KB.
        - Saída total/dia = 2.400.000 × 10 KB × 2 = 48 GB/dia
        - Se distribuirmos ao longo do dia (24h):
        - 48 GB / 24 horas = 2 GB/hora
        - 2 GB / 3600s = ~4.5Mbps

## Banda Total Estimada
| Tipo                   | Dados/dia | Pico/hora | Largura de banda necessária |
|------------------------|-----------|-----------|-----------------------------|
| Entrada (ERPs → API)   | 24 GB     | 6 GB      | ~15 Mbps                    |
| Saída (API → Consultas)| 48 GB     | 2 GB      | ~4.5 Mbps                   |
| **Total**              | **72 GB** | **8 GB**  | **~20 Mbps**                |



------

# Armazenamento


---
📊 Diagramas:
