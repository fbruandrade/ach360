# ADR-0005 — Adotar um baseline comum de guardrails de custo e capacidade em nuvem, aplicado por mecanismo nativo de cada provedor

> **Como usar este documento:** aplica-se a toda unidade de isolamento provisionada sob [ADR-0003](/ARC/issues/ARC-4#document-adr-0003-landing-zone-padrao-por-nuvem) (landing zone por nuvem). Não decide orçamento específico por workload — isso é decisão local do time de entrega, dentro dos limites estruturais que esta ADR fixa.

## 0. Cabeçalho

| Campo | Valor |
| --- | --- |
| **ID da ADR** | `ADR-0005` |
| **Status** | `proposta` |
| **Provisória** | Não |
| **Responsável** | Cloud Architect |
| **Domínio** | Cloud |
| **Data da proposta** | 2026-10-05 |
| **Data da última mudança de status** | 2026-10-06 (retrabalho, gate ARC-76 2ª rodada, D-22; status permanece `proposta`) |
| **Data de vigência** | na aceitação |
| **Substitui** | Nenhuma |
| **Substituída por** | Nenhuma |
| **ADRs relacionadas** | [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework) (D6 — risco de concentração; esta ADR fornece o dado de custo que sustenta o relatório de concentração de ADR-0002 C5). [ADR-0003](/ARC/issues/ARC-4#document-adr-0003-landing-zone-padrao-por-nuvem) (depende de — tagueamento aplicado no vending, C7). [ADR-0004](/ARC/issues/ARC-4#document-adr-0004-kubernetes-gerenciado-vs-openshift) (o custo comparativo de runtime alimenta esta ADR, não o contrário). |
| **Implementada por ARs** | [AR-003](/ARC/issues/ARC-4#document-ar-003-landing-zone-aws) (AWS), [AR-004](/ARC/issues/ARC-4#document-ar-004-landing-zone-azure) (Azure), [AR-005](/ARC/issues/ARC-4#document-ar-005-landing-zone-oci) (OCI) — realizam a tag obrigatória `escopo-pci` e demais tags de C1 em cada provedor |
| **Idioma** | Português do Brasil (conforme `PARAM-LANG`) |
| **Data de submissão ao comitê** | não submetida |
| **Resultado do comitê** | `pendente` |

## 1. Contexto

**A situação.** O banco opera em três nuvens com contratos comerciais distintos, e nenhum guardrail comum de custo ou capacidade existe hoje. Sem tagueamento consistente (ADR-0003 C7 depende desta ADR para definir o esquema), o custo por workload, por time, ou por classe de criticidade não é atribuível — o que torna impossível responder, com dados, à pergunta de concentração por provedor que a ADR-0002 (C5) exige antes de aceitar posicionar um workload Tier 1 em um único provedor público.

**O gatilho.** ADR-0003 (landing zone por nuvem) cita esta ADR como pré-requisito de seu próprio C7 (tagueamento no vending); esta ADR precisa existir para que ADR-0003 tenha algo concreto a aplicar.

**Custo de não decidir.** Sem guardrails de custo e capacidade: (a) nenhuma visibilidade de gasto por workload/time/criticidade existe para sustentar decisões de concentração (ADR-0002 C5); (b) nenhum limite de capacidade (quota, autoscaling) é aplicado de forma consistente, deixando um workload com erro de configuração capaz de consumir capacidade/custo sem alarme; (c) o banco não tem uma cadência declarada de revisão de custo (FinOps), o que historicamente leva a superprovisionamento silencioso.

**Restrições que não podemos mudar.** Os contratos comerciais com as três nuvens já existem e não são renegociados por esta ADR. O esquema de classificação de dados corporativo (`G4-class`, lacuna consciente, [registro de decisões §12](/ARC/issues/ARC-2#document-decision-registry)) ainda não existe — o tagueamento de criticidade usado aqui é o mínimo necessário para custo e capacidade, não o esquema de classificação de dados completo.

## 2. Fatores de decisão

| ID | Fator | Tipo | ID de obrigação | Peso |
| --- | --- | --- | --- | --- |
| D1 | Atribuição de custo por workload, time e classe de criticidade, desde o provisionamento | Custo | `N/A` (indireto — sustenta `OBL-CLOUD`, D6 de ADR-0002) | Obrigatório |
| D2 | Limites de capacidade (quota de conta/subscription/compartment, limites de autoscaling) que previnem consumo descontrolado | Operabilidade | `N/A` | Forte |
| D3 | Cadência de revisão de custo (FinOps) com dono nomeado, não apenas um relatório estático | Operabilidade | `N/A` | Forte |
| D4 | Uso de capacidade reservada/compromissada (reserved instances, savings plans, capacity reservations) onde o padrão de consumo é previsível | Custo | `N/A` | Desejável |
| D5 | Dados de custo por provedor alimentando o relatório de concentração exigido por `ADR-0002` C5 | Risco de fornecedor | `OBL-CLOUD` | Obrigatório |

D1 e D5 são `Obrigatório`: sem atribuição de custo, a ADR-0002 (C5) não tem dado para operar, e sem isso o banco não consegue demonstrar, a um regulador, controle sobre concentração de fornecedor.

## 3. Alternativas consideradas

### Opção A — Continuar sem guardrail de custo/capacidade comum (status quo)

- **O que é:** cada time monitora seu próprio gasto, sem esquema de tag comum nem limites de capacidade padronizados.
- **Como pontua:** D1 — falha. D2 — falha. D3 — falha. D4 — neutro (pode ocorrer isoladamente, sem coordenação). D5 — falha; sem dado agregado, ADR-0002 C5 não é verificável.
- **Custo e esforço:** custo de implementação zero; custo de superprovisionamento invisível crescente.
- **Riscos:** nenhuma visibilidade para sustentar o relatório de concentração que ADR-0002 já exige.
- **Por que não foi escolhida:** falha nos dois fatores obrigatórios.

### Opção B — Ferramenta de FinOps de terceiro, agnóstica de nuvem, sem usar as ferramentas nativas de billing de cada provedor

- **O que é:** adotar uma plataforma de FinOps externa como fonte única de verdade de custo, substituindo os relatórios nativos de cada nuvem (Cost Explorer, Azure Cost Management, OCI Cost Analysis).
- **Como pontua:** D1 — atendido, se a ferramenta suportar as tags definidas. D2 — não atendido; ferramentas de FinOps de terceiro tipicamente reportam custo, mas não aplicam quota/limite de capacidade nativamente. D3 — atendido. D4 — atendido. D5 — atendido.
- **Custo e esforço:** custo de licenciamento adicional de uma ferramenta terceira, mais integração com três APIs de billing nativas, cuja granularidade pode divergir.
- **Riscos:** cria uma dependência de uma quarta ferramenta, além das três nuvens, e não resolve D2 (capacidade), que precisa dos mecanismos nativos de cada provedor de qualquer forma.
- **Por que não foi escolhida:** não resolve D2 por si só, e adiciona uma dependência sem eliminar a necessidade de usar os mecanismos nativos — melhor usá-los diretamente e, se necessário, agregar os dados depois.

### Opção C — Guardrails nativos de cada nuvem (tag, budget/alerta, quota, autoscaling) com um esquema de tag comum entre as três e um relatório agregado mantido pelo Cloud Architect (escolhida)

- **O que é:** cada nuvem usa seu próprio mecanismo de orçamento/alerta (AWS Budgets, Azure Cost Management budgets, OCI Budgets) e de quota (service quotas/limits nativos), com um esquema de tag comum (workload, time, ambiente, criticidade, centro de custo) aplicado no vending (ADR-0003 C7). O Cloud Architect agrega os três relatórios nativos mensalmente para alimentar o relatório de concentração de ADR-0002 C5.
- **Como pontua:** D1 — atendido; o esquema de tag comum é a chave de atribuição. D2 — atendido; quota nativa de cada nuvem é usada diretamente. D3 — atendido; cadência mensal nomeada com dono. D4 — atendido como critério de revisão, não como mandato absoluto (depende do padrão de consumo de cada workload). D5 — atendido; a agregação mensal alimenta ADR-0002 C5 diretamente.
- **Custo e esforço:** esforço de manter um esquema de tag sincronizado nas três nuvens e um processo manual de agregação mensal, até que uma ferramenta de agregação (se justificada depois) seja avaliada.
- **Riscos:** agregação manual mensal é um processo que pode falhar sem automação; mitigado por ser uma tarefa recorrente nomeada (D3), não um relatório ad hoc.
- **Por que foi escolhida:** usa os mecanismos nativos de cada nuvem (que o banco já paga) em vez de adicionar uma ferramenta de terceiro, e resolve D1, D2 e D5 diretamente.

### Opções consideradas e descartadas precocemente

| Opção | Descartada porque |
| --- | --- |
| Orçamento fixo único por nuvem, sem granularidade por workload/time | Não resolve D1 (atribuição) nem sustenta D5 (dado para ADR-0002 C5), que precisa de granularidade por classe de criticidade, não apenas por nuvem. |

## 4. Decisão

> **Vamos exigir um esquema de tag comum, aplicado no vending de cada unidade de isolamento, e usar os mecanismos nativos de orçamento, alerta e quota de cada nuvem para custo e capacidade, com agregação mensal mantida pelo Cloud Architect para alimentar o relatório de concentração de ADR-0002.**

| # | Cláusula | Palavra-chave |
| --- | --- | --- |
| C1 | Toda conta AWS, subscription Azure ou compartment OCI provisionada sob `ADR-0003` carrega, desde a criação, as tags obrigatórias: `workload`, `time-responsavel`, `ambiente` (produção/não-produção), `classe-criticidade` (Tier 0–3, conforme tiering de DR de `ADR-0009` quando disponível; até então, autodeclarada pelo time), `centro-de-custo`, e `escopo-pci` (`cde` / `conectado` / `fora`, autodeclarada pelo time até o Security Architect formalizar um critério, `ADR-0012`–`ADR-0014` — um valor binário `sim`/`não` perderia a distinção entre workload CDE e sistema conectado que suporta o inventário de ativos de escopo PCI-DSS, conforme detalhado, com o número de requisito/família, no bloco de escopo PCI-DSS de §6.1). **A forma de garantir que a tag chega ao recurso, não só à unidade, difere nas três nuvens** e cada uma precisa do seu próprio mecanismo, não de uma convenção só de nomenclatura: AWS — ativar cost allocation tags na Organization torna a tag **visível no relatório de billing**, mas não **aplica** a tag a um recurso que não a tenha; a aplicação em si exige Tag Policies da AWS Organizations (definindo valores permitidos) combinadas com uma SCP que negue a criação de recurso sem a tag obrigatória presente (`aws:RequestTag`), como passo do factory de vending (`ADR-0003` C6) — alternativamente, quando a granularidade por recurso não for exigida, a atribuição pode ser feita no nível de conta via Cost Categories, já que a conta é a unidade de workload nesta ADR; a escolha entre as duas abordagens deve ser declarada por tipo de recurso, não presumida. Azure — uma tag na subscription não é herdada por um recurso criado dentro dela; a propagação exige Azure Policy com efeito `modify` ou `append` aplicando a tag no momento da criação do recurso. OCI — o mecanismo é **tag defaults** no compartment (não "herança" de defined tags): um tag default pode ser marcado como obrigatório, aplicando automaticamente o valor a todo recurso criado no compartment ou em sua subárvore; a OCI limita o número de cost-tracking tags simultâneas por tenancy (hoje 10), o que exige priorizar quais das tags desta cláusula entram nesse conjunto limitado. | MUST |
| C2 | Toda unidade de isolamento tem um orçamento (budget) configurado no mecanismo nativo da nuvem (AWS Budgets, Azure Cost Management, OCI Budgets), com alerta em pelo menos dois limiares (ex.: 80% e 100% do orçamento previsto) notificando o time responsável e o Cloud Architect. | MUST |
| C3 | Toda unidade de isolamento tem um teto de consumo para os recursos de maior risco de consumo descontrolado (cômputo elástico, armazenamento, chamadas de API de gateway) — mas **o mecanismo que produz esse teto não é o mesmo nas três nuvens, e nem todos são preventivos**: na **OCI**, compartment quotas são definíveis e rígidas por compartment, e funcionam como teto preventivo real, declarado no provisionamento. Na **AWS** e no **Azure**, o cliente não reduz service quotas/vCPU quotas nativas abaixo do padrão do provedor — essas quotas limitam o teto superior absoluto da conta/subscription, não um teto de negócio mais baixo. O teto efetivo nessas duas nuvens vem de outro mecanismo: AWS — SCP/IAM negando a criação de recursos acima de um parâmetro (ex.: tipo de instância, contagem) combinado com AWS Budgets Actions, que pode aplicar uma SCP restritiva ou parar recursos automaticamente ao estourar um limiar (preventivo/corretivo, não apenas alerta); Azure — Azure Policy restringindo SKUs e regiões permitidos (preventivo, `deny`) combinado com action groups de Azure Cost Management Budgets, que podem disparar uma Automation Runbook ou Logic App para parar recursos (corretivo). Onde esse mecanismo corretivo/preventivo não estiver configurado, o guardrail de capacidade da AWS/Azure é apenas **detectivo** (alerta de orçamento, C2), não preventivo — isso deve ser declarado explicitamente por unidade, não presumido como equivalente à quota rígida da OCI. | MUST |
| C4 | O Cloud Architect agrega mensalmente o custo por nuvem, por classe de criticidade e por time, e entrega esse dado ao Enterprise Architect para o relatório de concentração exigido por `ADR-0002` C5. | MUST |
| C5 | Capacidade reservada/compromissada (reserved instances, savings plans, capacity reservations) é avaliada para qualquer workload com padrão de consumo estável por mais de 6 meses, com a decisão e a justificativa registradas pelo time responsável. Para um workload Tier 1 com failover em nuvem, "capacidade" não se resume a quota: a reserva de capacidade de cômputo na região de destino do failover (ex.: AWS On-Demand Capacity Reservation, Azure On-demand Capacity Reservation, OCI capacity reservation) precisa ser nomeada explicitamente para esse workload, ou a lacuna fica registrada e ligada a `ADR-0009` (tiering de DR, [ARC-5](/ARC/issues/ARC-5)) em vez de presumida como coberta por esta cláusula `SHOULD`. **Tier 0 está explicitamente no escopo desta cláusula, não omitido**: hoje nenhum workload Tier 0 é claimável em nuvem (D-7, regra interina de `ADR-0009` Q3 — ver [ARC-5](/ARC/issues/ARC-5) para a regra autoritativa de claimability de Tier 0/1 até que `ADR-0009` Q3/Q6 sejam resolvidas), portanto o guardrail de capacidade reservada de C5 se aplica a Tier 0 apenas de forma vazia (vacuamente) enquanto essa restrição persistir — isso deve ser declarado assim, e revisitado quando a regra interina de `ADR-0009` Q3 mudar, em vez de simplesmente não mencionar Tier 0. | SHOULD |
| C6 | Um time de entrega pode exceder a quota padrão de um recurso específico mediante justificativa registrada e aprovação do Cloud Architect, sem necessidade de waiver formal quando não envolver um fator obrigatório de `ADR-0002`. | MAY |

- **Por que esta opção:** o trade-off aceito é um processo de agregação mensal inicialmente manual, em troca de evitar uma quarta ferramenta de FinOps sem eliminar a necessidade dos mecanismos nativos de qualquer forma.
- **Alternativas rejeitadas:** Opção A (status quo) falha nos fatores obrigatórios. Opção B (ferramenta de terceiro) não resolve a necessidade de quota nativa (D2) e adiciona uma dependência sem benefício líquido declarado. Dentro da Opção C, a escolha entre **enforcement automático** (budget action que bloqueia/para recursos ao estourar o limiar) e **apenas alerta** (notificação, sem ação automática) é uma decisão real, não uma variação cosmética: esta ADR adota enforcement automático (C3, via SCP/Budgets Actions na AWS e Azure Policy/action groups no Azure) para os recursos de maior risco de consumo descontrolado, e apenas alerta (C2) para o orçamento geral da unidade — porque bloquear/parar automaticamente todo gasto acima do orçamento geral arriscaria interromper produção por um erro de estimativa de orçamento, enquanto travar um recurso específico de alto risco (ex.: autoscaling sem teto) tem um raio de impacto mais previsível e um custo de falso positivo menor.

### 4.1 Aplicabilidade no estado

| Plataforma | Aplica-se? | Restrição, diferença ou exclusão |
| --- | --- | --- |
| `DC-AA` — DC on-prem, active-active | Não | Guardrails de custo on-premises são orçamento de capex/opex do Infrastructure Architect, fora do escopo desta ADR. |
| `OCP` — OpenShift on-prem | Não | Mesma observação. |
| `VM-ON` — VMs / bare metal on-prem | Não | Mesma observação. |
| `AWS-K8S` — Kubernetes gerenciado na AWS | Sim | Tags e quotas aplicam-se à conta que hospeda o cluster; quotas específicas de nó/pod são adicionais, não substitutas, das quotas de conta. |
| `AWS-VM` — compute AWS | Sim | Idem. |
| `AZR-K8S` — Kubernetes gerenciado no Azure | Sim | Idem, para a subscription. |
| `AZR-VM` — compute Azure | Sim | Idem. |
| `OCI-K8S` — Kubernetes gerenciado na OCI | Sim | Idem, para o compartment. |
| `OCI-VM` — compute OCI | Sim | Idem. |
| `DATA-REL` — bases de dados relacionais | Sim | Bases de dados gerenciadas em nuvem seguem o mesmo esquema de tag e orçamento de sua unidade de isolamento. |
| `DATA-NREL` — bases de dados não relacionais | Sim | Mesma observação. |
| `AGW-AWS` — AWS API Gateway | Sim | Chamadas de API de gateway são um recurso de alto risco de consumo descontrolado (C3) — quota de taxa de requisição é exigida. |
| `AGW-AZR` — Azure API Management | Sim | Mesma observação. |
| `AGW-AXW` — Axway API Gateway | Não | O Axway é hospedado no `DC-AA`; mudança só via [ADR-0010](/ARC/issues/ARC-6#document-adr-0010-estrategia-de-api-gateway) (D-22(j)). Não há previsão de implantação do Axway em nenhuma das três nuvens. |

### 4.2 Escopo de workloads

- **Aplica-se a:** toda unidade de isolamento provisionada a partir da data de vigência.
- **Workloads existentes:** qualquer unidade existente (ver inventário de `ADR-0003` §5) recebe o esquema de tag e orçamento dentro de 180 dias da data de vigência desta ADR, alinhado ao mesmo prazo de `ADR-0003`.
- **Explicitamente fora de escopo:** o orçamento específico de cada workload (valor em reais/dólares) é decisão local do time dentro do processo orçamentário do banco; esta ADR decide o mecanismo de controle, não o valor.

## 5. Consequências

### Positivas

- Torna o custo por workload, time e criticidade atribuível desde o primeiro recurso, viabilizando o relatório de concentração de `ADR-0002` C5 com dados reais.
- Usa mecanismos nativos de cada nuvem, que o banco já paga indiretamente via o contrato, em vez de uma ferramenta de FinOps adicional.
- Cria um alarme antecipado (C2 — alerta em 80%/100%) antes que um gasto descontrolado seja descoberto apenas na fatura mensal.

### Negativas, e o que fica mais difícil

- Agregação mensal entre três relatórios nativos de billing é, inicialmente, um processo manual — risco de erro humano ou atraso até que seja automatizado.
- Equipes de entrega ganham uma etapa adicional (tagueamento obrigatório) no provisionamento, ainda que de baixo custo individual.
- A classe de criticidade usada para tag (C1) é autodeclarada até `ADR-0009` (tiering de DR) existir — risco de classificação inconsistente entre times nesse período de transição.

### O que esta decisão impede no futuro

Impede que o banco descubra, apenas na fatura anual, uma concentração de gasto não planejada em um único provedor — o que, sem o dado agregado desta ADR, só seria percebido tarde, quando a migração para reequilibrar já teria custo de saída mais alto. Reverter esta decisão (voltar a não ter tagueamento) tornaria qualquer auditoria de custo retroativa cara, pela falta de atribuição histórica.

### Impacto de migração no estado existente

| O que existe hoje | O que precisa mudar | Responsável | Até quando |
| --- | --- | --- | --- |
| Nenhum esquema de tag comum confirmado como aplicado | Aplicar o esquema de tag (C1) a toda unidade existente, via o inventário de `ADR-0003` | Cloud Architect | 180 dias após a data de vigência |
| Nenhum orçamento/alerta nativo confirmado como configurado | Configurar orçamento e alerta (C2) para toda unidade existente | Cloud Architect | 180 dias após a data de vigência |

## 6. Impacto de conformidade e regulatório

Profundidade conforme `PARAM-REGDEPTH`. IDs de obrigação do [modelo operacional](/ARC/issues/ARC-2#document-operating-model) §3.

| Regime | ID(s) de obrigação aplicável(is) | O que a obrigação exige, em substância | Como esta decisão a endereça | Risco residual | Controle / evidência de que se mantém |
| --- | --- | --- | --- | --- | --- |
| BACEN/CMN | `OBL-CLOUD` | Diligência sobre o provedor; visibilidade de concentração e viabilidade de saída para serviços críticos | C4 produz o dado agregado de custo por provedor e por criticidade que sustenta o relatório de concentração de `ADR-0002` C5 | A agregação mensal é manual até ser automatizada — risco de atraso ou erro no período de transição | Relatório mensal (C4), com dono nomeado (Cloud Architect) |
| LGPD | `N/A` — guardrails de custo e capacidade não tratam, por si só, de dados pessoais | N/A — mesmo motivo | Guardrails de custo e capacidade não tratam, por si só, de dados pessoais | N/A — mesmo motivo | N/A — mesmo motivo |
| PCI-DSS | `OBL-PCI` | Manter um inventário atualizado de componentes do sistema em escopo (família 12) | Esta ADR não altera segmentação, acesso ou proteção de dados de portador de cartão em si — isso continua sendo `ADR-0002` C2/C3, `ADR-0003` C2, e `ADR-0012`–`ADR-0014` — mas a tag `escopo-pci` (C1) torna o inventário de unidades potencialmente em escopo consultável por tag, em vez de depender só do inventário manual do Security Architect | A tag é autodeclarada até o Security Architect formalizar um critério objetivo; autodeclaração incorreta não é detectada por esta ADR | Relatório de tagueamento (C1), cruzado com o inventário de unidades CDE mantido sob `ADR-0003` C2 |

### 6.1 Escopo PCI-DSS

| Pergunta | Resposta |
| --- | --- |
| Esta decisão armazena, transmite ou processa dados de portador de cartão? | Não. |
| Ela introduz ou altera um sistema conectado ao CDE, ou o próprio perímetro do CDE? | Não, por si só. Esta ADR é um mecanismo de tagueamento, orçamento e quota — ela não decide o que está dentro ou fora do CDE nem cria/remove conectividade com ele; isso permanece com `ADR-0002` C2/C3, `ADR-0003` C2 e `ADR-0012`–`ADR-0014`. |
| Efeito no escopo PCI-DSS | Indireto e habilitador, não direto: a tag `escopo-pci` criada por C1 (realizada pelos ARs `AR-003`/`AR-004`/`AR-005`) é o insumo que outras ADRs e ARs usam para marcar e consultar recursos potencialmente em escopo PCI — sem ela, o inventário de ativos em escopo dependeria só de processo manual do Security Architect. Esta ADR não altera, por si só, o escopo do CDE. |
| Família de requisito PCI-DSS afetada | Família 12 (controles organizacionais, gestão de prestador de serviço e inventário de ativos) — especificamente, Requisito 12.5.2 (confirmação periódica do escopo PCI-DSS): a tag `escopo-pci` criada por C1 é o insumo que alimenta o inventário de ativos em nuvem usado nessa confirmação de escopo, substituindo um levantamento manual. Esta é a única célula desta ADR em que um número de requisito PCI-DSS é citado (precedente G7) — nem C1 (§4) nem a célula de substância de `OBL-PCI` em §6 citam número de requisito ou família. |

Perguntas gerais de conformidade:

| Pergunta | Resposta |
| --- | --- |
| Esta decisão toca dados pessoais? | Não. |
| Ela envolve processamento ou armazenamento fora do Brasil? | Não — a região de processamento é decidida por `ADR-0002`/`ADR-0003`, não por esta ADR. |
| Ela muda um arranjo relevante de processamento de dados, armazenamento ou serviço em nuvem? | Não — não altera o arranjo contratual, apenas a visibilidade de custo e capacidade sobre ele. |
| Ela muda a postura de resiliência ou recuperação de um serviço crítico? | Não — C5 (capacidade reservada) é insumo para o tiering de DR de `ADR-0009`, não uma mudança de tier. |
| Ela muda a detecção de incidentes, logging ou rastreabilidade? | Não — logging segue o baseline de `ADR-0003` C3, independente desta ADR. |
| Ela cria ou muda uma dependência de terceiro / fornecedor? | Não. |

> O Escritório mapeia obrigações para controles de arquitetura em nível geral. Ele não emite parecer jurídico, e a suficiência jurídica é confirmada pelas funções de compliance e jurídico do banco.

## 7. Conformidade e verificação

| Cláusula | Como a conformidade é verificada | Onde a evidência vive | Automatizável? |
| --- | --- | --- | --- |
| C1 | Inspeção das tags aplicadas a cada unidade e, por amostragem, aos recursos dentro dela (não só à unidade) via API de billing/resource manager de cada nuvem; confirmação de que cost allocation tags estão ativadas (AWS) e de que a Azure Policy de propagação de tag está atribuída (Azure) | Relatório de tagueamento, mantido pelo Cloud Architect | Sim |
| C2 | Confirmação de que um budget e alertas em dois limiares existem para cada unidade | Console/API de orçamento nativo de cada nuvem | Sim |
| C3 | OCI: confirmação de que compartment quotas foram configuradas explicitamente. AWS/Azure: confirmação de que a SCP/Azure Policy de teto e a Budget Action/action group correspondente existem e estão em modo de aplicação, não apenas de alerta — **C3 é `MUST`; a ausência desse par em uma unidade com recurso de alto risco de consumo descontrolado é falha de conformidade com C3**, sujeita a waiver `W2` (§8), não uma nota informativa. Onde o par não estiver configurado, o guardrail daquela unidade é apenas detectivo (alerta de orçamento, C2) em vez de preventivo, e essa lacuna deve ser registrada explicitamente por unidade como não-conformidade, não presumida como aceitável | Console/API de quota e de política de cada nuvem; registro de Budget Actions / action groups | Sim |
| C4 | Relatório mensal agregado existe e foi entregue ao Enterprise Architect | Repositório de relatórios do Cloud Architect | Parcialmente — a existência é verificável; a qualidade exige revisão humana |
| C5 | Revisão por amostragem confirma que workloads com consumo estável por 6+ meses têm uma decisão registrada sobre capacidade reservada | Documentação de decisão do time responsável | Não — é `SHOULD`, avaliado por amostragem |
| C6 | Toda exceção de quota aprovada tem registro da justificativa e da aprovação do Cloud Architect | Registro de aprovações do Cloud Architect | Sim |

## 8. Exceções

- **Waivers possíveis contra esta ADR:** Sim, com os níveis abaixo.
- **Nível por cláusula:**
  - C1 — `W3` (nomeado explicitamente no controle/evidência da linha `OBL-PCI` em §6 — "Relatório de tagueamento (C1)" — portanto, pelo precedente A23/D-17(A), é o controle real da obrigação, não um elemento processual livre de waiver amplo).
  - C2 — `W1` (processual).
  - C3 — `W2` (afeta mais de um time se ausente sistematicamente, e sustenta a visibilidade de concentração).
  - C4 — `W3` (nomeado explicitamente no controle/evidência da linha `OBL-CLOUD` em §6 — "Relatório mensal (C4), com dono nomeado" — pelo mesmo precedente A23/D-17(A), é o controle real de `OBL-CLOUD`, não apenas um processo de suporte).
  - C5 — `W1` (processual, de otimização).
  - C6 — não aplicável a waiver por já ser `MAY` com aprovação própria.
- **Cláusulas que não podem receber waiver:** nenhuma.

## 9. Questões abertas

| # | Questão | Quem deve responder | Necessário até |
| --- | --- | --- | --- |
| Q1 | Ferramenta ou processo definitivo de agregação mensal entre os três relatórios nativos de billing — manual, planilha versionada, ou automação a construir | Cloud Architect | 90 dias após a data de vigência |
| Q2 | Limiar quantitativo de concentração por provedor (mesma questão aberta em `ADR-0002` §9, Q3) — esta ADR fornece o dado, mas não fixa o limiar | Arquiteto Principal, com a função de risco do banco (já aberta em `ADR-0002`) | Antes da submissão ao comitê de `ADR-0002` |
| Q3 | Classe de criticidade (C1) usa tiering autodeclarado até `ADR-0009` existir — prazo para `ADR-0009` fechar essa lacuna | Infrastructure Architect | Conforme cronograma de [ARC-5](/ARC/issues/ARC-5) |

## 10. Revisão por pares e discordâncias

| Revisor | Papel | Veredito | Comentário / discordância |
| --- | --- | --- | --- |
| Enterprise Architect | obrigatório — verificação de não conflito com ADR aceita, e consumidor do relatório de C4 | **pedido ancorado aberto em 2026-10-06** (ver nota abaixo) | Esta ADR nunca teve pedido de revisão geral ao Enterprise Architect registrado. O Cloud Architect abre pedido próprio, ancorado por precedente 14; prazo de 10 dias úteis, vence em **2026-10-21** (D-22(s)). |
| Infrastructure Architect | obrigatório — dependência de `ADR-0009` para tiering de criticidade (C1, C5) | **Concorda** (rodada de 2026-10-05, [ARC-34](/ARC/issues/ARC-34)); **confirmação restrita pendente sobre a adição a C5** (ver nota abaixo) | Revisão concluída em 2026-10-05. Confirmo que Tier 0–3 é de fato a escala vigente de `ADR-0009` — C1 já reflete essa escala corretamente (`classe-criticidade`, Tier 0–3) na revisão mais recente desta ADR. C5 (reserva de capacidade de failover para Tier 1) é coerente com o tiering formal: ADR-0009 define Tier 1 com RTO ≤ 1h / RPO ≤ 15min via failover controlado com replicação assíncrona, o que sustenta a necessidade de capacidade reservada no destino do failover que C5 nomeia. Q3 (§9) permanece válida: ADR-0009 ainda está `proposta`, não `aceita`. **Nota do Cloud Architect (2026-10-06):** este veredito de 2026-10-05 é anterior à frase que C5 ganhou nesta revisão, declarando Tier 0 explicitamente no escopo da cláusula (vazio enquanto D-7/`ADR-0009` Q3 proibir claim de Tier 0 em nuvem) — essa frase especificamente ainda não foi confirmada pelo Infrastructure Architect. Pedido de confirmação restrita aberto em 2026-10-06, ancorado (precedente 14), restrito a essa frase de C5; prazo de 10 dias úteis, vence em 2026-10-21. |
| Technical Architect | revisão solicitada — cláusula C1, por criar a tag `escopo-pci` via mecanismos técnicos específicos de cada nuvem (Tag Policies/SCP na AWS, Azure Policy `modify`/`append`, tag defaults na OCI) | **Concorda** ([ARC-85](/ARC/issues/ARC-85), veredito de 2026-10-06T01:36:49Z, transcrito literalmente) | "Os mecanismos descritos por nuvem estão tecnicamente corretos — AWS: Tag Policies (valores permitidos) combinadas com SCP usando `aws:RequestTag` (preventivo) ou, no nível de conta, Cost Categories; Azure: a tag na subscription não é herdada, exigindo Azure Policy com efeito `modify`/`append` para propagar ao recurso; OCI: tag defaults no compartment (não 'herança' de defined tags), com o limite de 10 cost-tracking tags simultâneas por tenancy corretamente nomeado como restrição a priorizar (pendência já refletida em `AR-005` §10.2, item 2). §6.1 (bloco de escopo PCI-DSS): o enquadramento da tag `escopo-pci` como insumo indireto/habilitador do inventário de ativos (Req. 12.5.2, família 12), não como determinação direta de escopo do CDE, está correto e consistente com o que `ADR-0003` C2 e `ADR-0012`–`ADR-0014` decidem sobre segmentação. Sem objeção." |
| Security Architect | revisão solicitada — cláusula C1, domínio direto por ser quem formalizará o critério objetivo de `escopo-pci` (`ADR-0012`–`ADR-0014`) e por ser o dono do inventário de unidades CDE referenciado em §6 | **Concorda** ([ARC-78](/ARC/issues/ARC-78), veredito de 2026-10-06T01:34:46Z, transcrito literalmente) | "O valor de três estados (`cde`/`conectado`/`fora`) é a escolha certa — um binário `sim`/`não` perderia a distinção que a família 12 do PCI-DSS exige para inventário de ativos. §6.1 corretamente trata esta ADR como habilitadora/neutra de escopo, não como definidora do perímetro do CDE. Autodeclaração até eu formalizar um critério objetivo (`ADR-0012`–`ADR-0014`) já está registrada como lacuna conhecida — sem objeção." |

**Pedido ancorado ao Enterprise Architect e confirmação restrita ao Infrastructure Architect, abertos em 2026-10-06 (precedente 14):** documento `adr-0005-guardrails-custo-capacidade-nuvem`, `revisionNumber` e `createdAt` da revisão vigente no momento da abertura (ver §11); versão do artefato conforme §0. Prazo de 10 dias úteis para ambos, vence em 2026-10-21.

Nenhuma discordância registrada nesta rodada.

## 11. Histórico de status

| Data | De → Para | Quem | Por quê |
| --- | --- | --- | --- |
| 2026-10-05 | `—` → `proposta` | Cloud Architect | Rascunho iniciado |
| 2026-10-07 | `proposta` → `proposta` | Cloud Architect | **Achado de prontidão de [ARC-138](/ARC/issues/ARC-138) (veredito consolidado de 06:29), retrabalho [ARC-171](/ARC/issues/ARC-171).** G2: linha LGPD de §6 tinha três células `—` sem motivo declarado na própria célula; cada uma agora traz `N/A — mesmo motivo` ou o motivo completo. G7 reconferido: a única célula com número de requisito PCI-DSS é a de Família (§6.1), como já declarado explicitamente no próprio texto; nenhuma outra célula cita número de requisito ou de norma BACEN/CMN fora de transcrição literal de revisor (§10) ou changelog (§11). G11: pedido de revisão geral ao Enterprise Architect permanece sem veredito — bloqueador genuíno de terceiro, prazo não vencido, não é falha de prontidão desta ADR. Varredura obrigatória executada; nenhuma outra ocorrência encontrada. Status permanece `proposta`; nada aqui é aprovação. |
| 2026-10-05 | `proposta` → `proposta` | Cloud Architect | Mudanças solicitadas pelo Arquiteto Principal endereçadas: C3 reescrito com três respostas por nuvem (quota rígida na OCI; SCP/Budgets Actions e Azure Policy/action groups como teto efetivo na AWS/Azure, com a distinção preventivo vs. detectivo declarada), C1 detalha a herança de tag por nuvem e adiciona a tag `escopo-pci`, C5 nomeia a reserva de capacidade de failover para Tier 1, §3 registra a decisão real entre enforcement automático e apenas alerta, linha PCI-DSS de §6 deixa de ser `N/A`, corrigidos os erros de digitação "nuvines"/"nuvine". Revisão solicitada ao Infrastructure Architect. |
| 2026-10-05 | `proposta` → `proposta` | Cloud Architect | 2ª rodada de mudanças solicitadas endereçadas: resolvida a contradição entre §3 ("adota enforcement automático") e §7 C3 (ausência tratada como "não falha de conformidade por si só") — C3 é `MUST`, e a ausência do par preventivo/corretivo em AWS/Azure é agora declarada como falha de conformidade sujeita a waiver `W2`; C1 corrige os mecanismos de tag (AWS: Tag Policies/SCP com `aws:RequestTag`, não apenas ativação de cost allocation tags, que só afeta visibilidade de billing; OCI: tag defaults, não "herança" de defined tags) e troca `escopo-pci` de binário `sim`/`não` para `cde`/`conectado`/`fora`; corrigida a escala de criticidade para Tier 0–3 (`ADR-0009`); revisão por pares do Infrastructure Architect vinculada à task real [ARC-34](/ARC/issues/ARC-34). |
| 2026-10-05 | `proposta` → `proposta` | Cloud Architect | Alinhamento classe M em devolução ARC-67 (gate de hand-off ARC-10 do Arquiteto Principal): adicionado o bloco de escopo PCI-DSS (§6.1), respondendo armazenamento/transmissão de dados de portador de cartão, alteração de sistema conectado ao CDE, efeito no escopo e família PCI-DSS afetada (família 12, inventário de ativos) para a tag `escopo-pci` criada por C1; removida a citação a número de sub-requisito PCI específico de C1 e da célula de substância de `OBL-PCI` em §6, mantendo citação numérica apenas na célula de Família/bloco de escopo PCI-DSS; C1 e C4 elevadas a waiver `W3` em §8 por estarem nomeadas no controle/evidência das linhas `OBL-PCI`/`OBL-CLOUD` de §6 (precedente A23/D-17(A)); C5 passa a tratar Tier 0 explicitamente (não-claimável hoje, D-7/`ADR-0009` Q3) com referência à regra interina de `ADR-0009` Q3; cabeçalho atualizado com `AR-003`/`AR-004`/`AR-005` como ARs que realizam a tag `escopo-pci` de C1; solicitada revisão por pares, datada e com prazo, ao Technical Architect e ao Security Architect para C1 (tópico `escopo-pci`). |
| 2026-10-06 | `proposta` → `proposta` | Cloud Architect | Fechamento do alinhamento classe M em devolução ARC-67 (gate ARC-10): corrigida a citação de requisito PCI-DSS no bloco de escopo (§6.1) de "12.5.1" para "12.5.2" (confirmação periódica de escopo PCI-DSS, que é o efeito real da tag `escopo-pci` de C1 sobre o inventário de ativos — família 12, precedente G7, número mantido apenas nessa célula de Família); cabeçalho ("Implementada por ARs") corrigido para linkar `AR-003`, `AR-004` e `AR-005` aos documentos reais em ARC-4, em vez de citá-los como texto simples; pedido de revisão por pares ao Technical Architect e ao Security Architect (cláusula C1, tag `escopo-pci`, §10) redatado para a data correta desta rodada — registrado em 2026-10-06, prazo de 10 dias úteis, vencendo em 2026-10-20 (nenhum veredito registrado, pois o prazo ainda não se esgotou). |
| 2026-10-06 | `proposta` → `proposta` | Cloud Architect | Retrabalho do gate interno ARC-76 (2ª rodada), tarefa [ARC-130](/ARC/issues/ARC-130) — esta ADR segue classe **F**. Mudanças: (1) vereditos de Technical Architect ([ARC-85](/ARC/issues/ARC-85)) e Security Architect ([ARC-78](/ARC/issues/ARC-78)) transcritos literalmente em §10 — ambos "Concorda"; (2) pedido de revisão geral ao Enterprise Architect aberto, ancorado por precedente 14 — nunca havia sido aberto; (3) confirmação restrita ao Infrastructure Architect aberta, ancorada, restrita à frase sobre Tier 0 em C5 — o veredito de 2026-10-05 do Infrastructure Architect antecede essa frase; (4) G7: removida a citação a "família 12" de C1 (§4), mantendo número/família apenas na célula de Família PCI-DSS de §6.1, tornando verdadeira a afirmação de que §6.1 é "a única célula" com citação numérica; (5) §4.1 `AGW-AXW` corrigida para D-22(j); (6) as seis perguntas gerais de conformidade (§6.1) reescritas para resposta estritamente `Sim`/`Não` (A19). Status permanece `proposta`; nada aqui é aprovação. |
