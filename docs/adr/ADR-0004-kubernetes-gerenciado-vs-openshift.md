# ADR-0004 — Usar Kubernetes gerenciado nativo em cada nuvem, mantendo o OpenShift restrito ao data center on-premises

> **Como usar este documento:** decide o runtime de container dentro de um ambiente de nuvem já escolhido pela [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework). Não reabre a decisão de posicionamento (DC vs. nuvem) — assume-a como dada. Depende de [ADR-0003](/ARC/issues/ARC-4#document-adr-0003-landing-zone-padrao-por-nuvem) para a estrutura de conta/subscription/compartment em que o cluster roda.

## 0. Cabeçalho

| Campo | Valor |
| --- | --- |
| **ID da ADR** | `ADR-0004` |
| **Status** | `proposta` |
| **Provisória** | Não |
| **Responsável** | Cloud Architect |
| **Domínio** | Cloud |
| **Data da proposta** | 2026-10-05 |
| **Data da última mudança de status** | 2026-10-06 |
| **Data de vigência** | na aceitação |
| **Substitui** | Nenhuma |
| **Substituída por** | Nenhuma |
| **ADRs relacionadas** | [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework) (depende de — framework de posicionamento, que decide *onde*, não *qual runtime*). [ADR-0003](/ARC/issues/ARC-4#document-adr-0003-landing-zone-padrao-por-nuvem) (depende de — estrutura de conta que hospeda o cluster). [ADR-0008](/ARC/issues/ARC-5) (VM vs. container, Infrastructure Architect — decide essa forma de compute nos quatro ambientes, incluindo nuvem; esta ADR pressupõe que a forma já foi decidida como container e decide apenas o orquestrador em nuvem). |
| **Implementada por ARs** | [AR-003](/ARC/issues/ARC-4#document-ar-003-landing-zone-aws) (AWS), [AR-004](/ARC/issues/ARC-4#document-ar-004-landing-zone-azure) (Azure), [AR-005](/ARC/issues/ARC-4#document-ar-005-landing-zone-oci) (OCI) |
| **Idioma** | Português do Brasil (conforme `PARAM-LANG`) |
| **Data de submissão ao comitê** | não submetida |
| **Resultado do comitê** | `pendente` |

## 1. Contexto

**A situação.** O banco roda OpenShift on-premises e já roda Kubernetes gerenciado (EKS, AKS e OKE) nas três nuvens — não se trata de uma escolha hipotética para workloads futuros, mas de um fato presente que esta ADR precisa governar junto com o que vem a seguir. A pergunta que esta ADR fecha é se, dentro de um ambiente de nuvem, o container orchestrator deve ser Kubernetes gerenciado nativo (EKS/AKS/OKE), OpenShift gerenciado pelo provedor (ROSA na AWS, ARO no Azure) ou OpenShift auto-gerenciado sobre VMs de nuvem — e não "qual nuvem" ou "nuvem vs. DC", que são as ADR-0002 e ADR-0003. A pressão natural de um time que já opera OpenShift é replicá-lo em nuvem por familiaridade e por portabilidade de manifestos — mas essa pressão, por si só, não é um critério de custo, e esta ADR existe para não aceitar portabilidade como justificativa sem quantificar o que ela custa (§4).

**O gatilho.** ADR-0003 (landing zone por nuvem) está em rascunho na mesma task ([ARC-4](/ARC/issues/ARC-4)) e precisa de uma resposta sobre qual runtime de container as landing zones devem suportar como padrão, para que a automação de vending (ADR-0003 C6) saiba o que provisionar.

**Custo de não decidir.** Sem esta ADR, cada time de entrega escolheria entre Kubernetes gerenciado, OpenShift gerenciado e OpenShift-em-nuvem auto-gerenciado caso a caso, por preferência pessoal ou familiaridade — reproduzindo, na dimensão de runtime, o mesmo problema que ADR-0002 resolveu na dimensão de posicionamento: decisões não auditáveis, inconsistentes entre times, e o custo operacional de manter múltiplos modelos de operação em nuvem sem um motivo declarado para cada caso.

**Restrições que não podemos mudar.** O OpenShift on-premises já está em produção e não é revisitado por esta ADR — é o Infrastructure Architect, via [ARC-5](/ARC/issues/ARC-5) (`ADR-0008`), quem decide VM vs. container no DC. As três nuvens oferecem serviços de Kubernetes gerenciado maduros (EKS, AKS, OKE) com SLA de control plane, o que já é um fato de mercado, não uma escolha desta ADR. A AWS e o Azure oferecem, além disso, OpenShift como serviço gerenciado conjuntamente com a Red Hat (ROSA e ARO, respectivamente); a OCI não tem, até a data desta proposta, um serviço gerenciado de OpenShift first-party equivalente — OpenShift na OCI só existe hoje na forma auto-gerenciada sobre VMs.

**Inventário de Kubernetes já em produção nas três nuvens.** Os clusters EKS, AKS e OKE que já existem hoje — independentemente desta ADR — entram no escopo de governança desta decisão (§4.2 e §5.3) em vez de ficarem fora dela só por terem sido provisionados antes da data de vigência: esta ADR não governa apenas o que ainda não existe.

## 2. Fatores de decisão

| ID | Fator | Tipo | ID de obrigação | Peso |
| --- | --- | --- | --- | --- |
| D1 | Custo operacional recorrente de manter múltiplos modelos de operação de container (OpenShift auto-gerenciado, OpenShift gerenciado, Kubernetes gerenciado) em paralelo | Custo | `N/A` | Forte |
| D2 | Disponibilidade e maturidade do serviço de Kubernetes gerenciado nativo de cada nuvem (SLA de control plane, versão suportada, integração com IAM nativo) | Operabilidade | `N/A` | Forte |
| D3 | Portabilidade de manifestos e políticas entre OpenShift on-prem e o runtime de nuvem — quantificada, não assumida | Operabilidade | `N/A` | Desejável |
| D4 | Competências da equipe de plataforma para operar runtimes adicionais em nuvem, além do OpenShift on-prem | Competências | `N/A` | Forte |
| D5 | Modelo de licenciamento — custo por nó/core do OpenShift (assinatura Red Hat, auto-gerenciado ou embutida em ROSA/ARO) vs. taxa de control plane gerenciado + compute | Custo | `N/A` | Forte |
| D6 | Controles de segurança equivalentes entre o modelo de Security Context Constraints (SCC) do OpenShift e o modelo de Pod Security Admission/políticas de nuvem (ligação com o Security Architect, `ADR-0012`–`ADR-0014`) | Segurança | `N/A` | Forte |

Nenhum fator aqui é `Obrigatório`: esta é uma decisão de eficiência operacional e custo, não uma decisão que, isoladamente, viole uma obrigação regulatória mapeada — a conformidade regulatória do workload é tratada pela ADR-0002 (posicionamento) e pelas ADRs de segurança (`ADR-0012`–`ADR-0014`), não pelo runtime de container em si.

## 3. Alternativas consideradas

### Opção A — Padronizar OpenShift auto-gerenciado sobre VMs de nuvem nas três nuvens (status quo estendido)

- **O que é:** replicar o OpenShift on-premises em cada nuvem, rodando sobre VMs gerenciadas pelo próprio time de plataforma, para manter um único runtime em todos os quatro ambientes.
- **Como pontua:** D1 — falha; mantém o custo operacional total de administrar o OpenShift (patching, upgrade, HA do control plane) em quatro ambientes em vez de um. D2 — neutro; não aproveita o serviço gerenciado nativo que o banco já paga indiretamente via o contrato de nuvem. D3 — atendido plenamente; quantificado em §4, não compensa D1/D4/D5. D4 — falha; exige que a equipe de plataforma opere o control plane do OpenShift também em nuvem, sem reduzir o esforço existente. D5 — falha; soma o licenciamento Red Hat por nó ao custo pleno de compute de VM, sem a taxa de control plane gerenciado que reduziria o total. D6 — atendido; mesmo modelo SCC em todos os ambientes.
- **Custo e esforço:** alto — ver §4. Triplica (uma vez por nuvem) o esforço de manter um control plane auto-gerenciado, além do que já existe on-premises.
- **Riscos:** a equipe de plataforma acumula a operação de quatro control planes auto-gerenciados (DC + 3 nuvens) em vez de um auto-gerenciado (DC) e três gerenciados pelo provedor ou pela parceria provedor/Red Hat.
- **Por que não foi escolhida:** o único fator que favorece claramente esta opção é portabilidade (D3); quantificado em §4, esse ganho não compensa o custo operacional e de licenciamento de D1/D4/D5 — e a Opção C entrega a mesma portabilidade de D3 sem o custo operacional de D1/D4 que esta opção carrega.

### Opção B — Kubernetes gerenciado nativo em cada nuvem (EKS, AKS, OKE), com padronização de manifestos via camada de abstração (Helm/Kustomize + políticas comuns), mantendo OpenShift apenas on-premises (escolhida)

- **O que é:** cada nuvem usa seu serviço de Kubernetes gerenciado nativo para o control plane; a portabilidade entre OpenShift on-prem e Kubernetes em nuvem é obtida na camada de aplicação (manifestos Kubernetes padrão, Helm charts, políticas de admissão comuns como Kyverno/OPA), não pela identidade do runtime.
- **Como pontua:** D1 — atendido; o control plane é operado pelo provedor, reduzindo o esforço de patching/upgrade/HA a zero para essa camada, mas introduz o custo de manter dois modelos de política/build/ingress em paralelo (quantificado em §4). D2 — atendido; os três serviços (EKS, AKS, OKE) têm SLA de control plane maduro e integração nativa com IAM. D3 — atendido parcialmente e quantificado em §4: manifestos Kubernetes puros (Deployments, Services, NetworkPolicies) portam sem alteração; recursos específicos do OpenShift (Routes, SCC, BuildConfig, ImageStream) não portam e precisam de um equivalente por nuvem (Ingress/Gateway API, Pod Security Admission, pipeline de build externo). D4 — atendido parcialmente; a equipe não precisa operar um control plane auto-gerenciado adicional, mas precisa de competência no modelo de IAM e rede nativo de cada nuvem, e passa a manter dois modelos de política em paralelo (quantificado em §4). D5 — atendido; elimina o licenciamento Red Hat por nó em nuvem, substituindo por taxa de control plane gerenciado (tipicamente menor) mais compute padrão. D6 — parcialmente atendido; exige uma camada de política equivalente ao SCC (Pod Security Admission + Kyverno/OPA), a ser definida com o Security Architect.
- **Custo e esforço:** ver modelo de custo quantificado em §4 — esforço de tradução de recursos específicos do OpenShift (Routes, BuildConfig, SCC) para equivalentes de Kubernetes puro, por aplicação migrada; custo recorrente de FTE de plataforma para manter dois modelos de política, build e ingress.
- **Riscos:** times acostumados ao OpenShift precisam aprender o modelo de rede/IAM nativo de cada nuvem; a equivalência de SCC ainda não está fechada com o Security Architect (questão aberta, §10).
- **Por que foi escolhida:** no modelo de custo de §4, o custo operacional recorrente evitado (D1, D5) supera o custo de tradução único e o custo recorrente de manter dois modelos de política (D3, D4) para o volume de clusters estimado para o primeiro ano, e usa o SLA de control plane que o banco já paga ao contratar a nuvem. Fica abaixo da Opção C (ROSA/ARO) em custo recorrente de licenciamento, ao preço de D6 ainda não estar fechado — por isso C4 (§5) é `MUST` antes de qualquer migração Tier 1.

### Opção C — OpenShift gerenciado pelo provedor (ROSA na AWS, ARO no Azure); sem equivalente first-party na OCI

- **O que é:** usar o serviço de OpenShift operado conjuntamente pelo provedor de nuvem e pela Red Hat — Red Hat OpenShift Service on AWS (ROSA) e Azure Red Hat OpenShift (ARO) — em que o control plane é gerenciado pelo provedor/Red Hat (upgrade, patching, HA cobertos por SLA), mas o cluster continua sendo OpenShift: SCC, Routes e BuildConfig/S2I continuam disponíveis nativamente, preservando a portabilidade de manifesto com o `OCP` on-premises sem o custo operacional de D1/D4 da Opção A. Esta opção não existe na OCI: até a data desta proposta não há um serviço gerenciado de OpenShift first-party na OCI, apenas OpenShift auto-gerenciado sobre VMs (Opção A) ou Kubernetes gerenciado via OKE (Opção B).
- **Como pontua (AWS e Azure apenas — ver conclusão por nuvem abaixo):** D1 — atendido; o control plane é operado pelo provedor/Red Hat, eliminando o esforço de patching/upgrade/HA que a Opção A mantém, sem introduzir o custo de manter um segundo modelo de política (SCC permanece único). D2 — atendido; ROSA e ARO têm SLA de control plane publicados pelo provedor. D3 — atendido plenamente e sem custo de tradução: SCC, Routes e BuildConfig/S2I continuam nativos, o que elimina o custo de tradução por aplicação que a Opção B carrega (quantificado em §4). D4 — atendido; a equipe de plataforma opera o mesmo modelo SCC/Routes/BuildConfig que já opera no DC, sem aprender um segundo modelo de política por nuvem. D5 — pontua pior que a Opção B: a assinatura Red Hat continua embutida na tarifa do serviço gerenciado (ROSA e ARO cobram uma tarifa que inclui a licença OpenShift, além da tarifa de infraestrutura), o que fica acima do custo de controle plane gerenciado + compute puro da Opção B (quantificado em §4). D6 — atendido plenamente; o modelo SCC é idêntico ao do DC, sem necessidade de equivalência a definir com o Security Architect.
- **Custo e esforço:** ver §4 — mais caro em licenciamento recorrente que a Opção B, mais barato em esforço operacional e em custo de tradução por aplicação que a Opção A.
- **Riscos:** disponibilidade de versão OpenShift em ROSA/ARO segue o ciclo de releases suportado pela Red Hat/provedor, que pode ficar atrás da versão mais recente do upstream Kubernetes disponível em EKS/AKS; dependência adicional do contrato tripartite banco–provedor–Red Hat.
- **Por que foi avaliada e rejeitada como padrão, mas não eliminada:** no modelo de custo de §4.4, nos três cenários de volume considerados (Baixo/Base/Alto), o prêmio de licenciamento recorrente de D5 deixa a Opção C (combinada com B na OCI, que não tem ROSA/ARO) entre ≈2,5× e ≈4,6× mais cara em 3 anos que a Opção B pura (≈2,0×–3,3× se as VMs de control plane do ARO forem excluídas) — a diferença cresce com o volume. O custo de tradução de D3 nunca se aproxima desse prêmio por cluster, mesmo para a classe de aplicação mais complexa (§4.4), então a reabertura por aplicação via C7 não pode ser justificada como decisão de custo. ROSA/ARO fica registrada como a alternativa a reabrir por aplicação apenas quando a lacuna real — a equivalência SCC ↔ Pod Security Admission/Kyverno (D6) não estiver aprovada pelo Security Architect para aquele workload específico, tipicamente um workload CDE/sistema conectado ao PCI-DSS — tornar inaceitável o risco de migrar para Kubernetes gerenciado antes da aprovação — ver C7 (§5). Na OCI, a opção simplesmente não existe hoje como serviço first-party, então a decisão ali é binária entre as Opções A e B, e recai em B pelos mesmos fatores D1/D4/D5.

### Opções consideradas e descartadas precocemente

| Opção | Descartada porque |
| --- | --- |
| Usar um runtime de container de terceiro, não nativo de nenhuma nuvem e não OpenShift (ex.: Rancher sobre VMs) | Adiciona um quinto modelo de operação sem resolver o problema de duplicidade — pelo contrário, agrava D4. |
| Migrar o OpenShift on-premises para Kubernetes puro, eliminando o OpenShift inteiramente | Fora do escopo desta ADR e da task — a decisão sobre o runtime on-premises é do Infrastructure Architect ([ARC-5](/ARC/issues/ARC-5)), não desta. |

## 4. Modelo de custo quantificado

Este modelo é paramétrico. Os preços citados são preço de lista público dos provedores e da Red Hat na data desta proposta (2026-10-05) — **a confirmar contra o contrato vigente do banco com cada provedor e com a Red Hat**, que tipicamente trazem descontos sobre lista. Os volumes de cluster e as taxas de FTE são premissas declaradas explicitamente abaixo, não medições — ajustar quando o inventário real (§5.3) e o orçamento de RH confirmado estiverem disponíveis.

### 4.1 Premissas declaradas

| Premissa | Valor assumido | Fonte / confiança |
| --- | --- | --- |
| Clusters no primeiro ano, AWS | 6 | Estimativa do Cloud Architect a partir da carteira de workloads candidatos a nuvem na ADR-0002 — **a confirmar com os times de entrega** |
| Clusters no primeiro ano, Azure | 5 | Mesma base — **a confirmar** |
| Clusters no primeiro ano, OCI | 3 | Mesma base — **a confirmar** |
| Nós por cluster (média) | 6 nós, 4 vCPU cada (24 vCPU/cluster) | Premissa de dimensionamento médio — **a confirmar por classe de aplicação** |
| Taxa de controle plane gerenciado (EKS; AKS Standard; OKE Enhanced) | USD 0,10/hora/cluster (≈ USD 876/ano/cluster) | Preço de lista público dos três provedores na data desta proposta |
| Taxa de controle plane (AKS Free tier; OKE Basic) | USD 0/hora/cluster | Preço de lista público — sem SLA de uptime formal; usado apenas para Tier 2 ou Tier 3 (ver C1a) |
| Assinatura Red Hat OpenShift (Standard), auto-gerenciado | ≈ USD 1.350/ano por par de licença ("core-pair"), onde 1 core-pair cobre até 4 vCPUs em nuvem (métrica de licenciamento por vCPU da Red Hat para cloud, distinta da métrica "2 cores físicos" usada on-premises) | Ordem de grandeza de lista pública da Red Hat para OpenShift Container Platform — **a confirmar contra o contrato ELA do banco com a Red Hat e contra a métrica de licenciamento efetivamente aplicada em cloud**, que normalmente traz desconto sobre esse valor |
| Taxa de serviço/licença sobre worker nodes — ROSA | USD 0,171 **por 4 vCPU-hora** de worker (on-demand), cluster operando 24/7 (8.760 h/ano). Para 24 vCPU/cluster: 6 × 0,171 × 8.760 ≈ USD 8.988/ano/cluster | Preço de lista público da AWS, página de preços do ROSA (aws.amazon.com/rosa/pricing), consultada em 2026-10-05 — **a confirmar contra contrato/tabela vigente**; contratos anuais comprometidos têm desconto sobre o on-demand |
| Modelo ROSA adotado no cálculo e taxa de control plane | **ROSA HCP** (hosted control plane): USD 0,25/hora/cluster (≈ USD 2.190/ano/cluster). O ROSA Classic não cobra taxa por cluster, mas exige que o banco pague as VMs de control plane e infra na própria conta — não usado no cálculo | Mesma fonte (aws.amazon.com/rosa/pricing), 2026-10-05 — **a confirmar** |
| Taxa de licença sobre worker nodes — ARO | USD 0,171 **por 4 vCPU-hora** de worker (mesma métrica do ROSA, confirmada separadamente). Para 24 vCPU/cluster ≈ USD 8.988/ano/cluster | Preço de lista público da Microsoft, página de preços do Azure Red Hat OpenShift (azure.microsoft.com/pricing/details/openshift), consultada em 2026-10-05 — **a confirmar** |
| Control plane do ARO (cluster padrão, não-HCP) | Sem taxa de gestão por cluster, mas o banco paga as 3 VMs de control plane a preço de VM Linux. Premissa: 3 × D8s_v5 ≈ USD 0,40/hora cada ≈ USD 1,20/hora ≈ USD 10.512/ano/cluster | Mesma fonte (modelo de cobrança); preço da VM é ordem de grandeza de lista em região dos EUA — **a confirmar em `Brazil South`, que tipicamente é mais cara**. Sensibilidade sem essa linha em §4.4 |
| Custo de FTE de plataforma (carregado, Brasil) | ≈ BRL 300.000/ano (≈ USD 60.000/ano a BRL 5,0/USD) por FTE sênior | Ordem de grandeza de mercado para engenheiro de plataforma sênior, carregado — **a confirmar contra o orçamento de RH do banco** |
| Custo de dia-pessoa de tradução (carregado) | ≈ BRL 1.200/dia-pessoa | Derivado da mesma premissa de FTE anual ÷ dias úteis — **a confirmar** |
| Aplicações migradas por cluster no primeiro ano (classe média) | 1 aplicação por cluster | Premissa simplificadora para o cenário Base — clusters iniciais tendem a ser dedicados a uma aplicação; **a confirmar quando o inventário real (§5.3) estiver fechado** |

### 4.2 Custo recorrente de operar dois modelos (D1, D4) — Opção B

Manter Kubernetes gerenciado em nuvem e OpenShift on-premises em paralelo exige a equipe de plataforma sustentar, de forma contínua:

| Dimensão duplicada | O que precisa ser mantido em paralelo | Estimativa de esforço recorrente |
| --- | --- | --- |
| Modelo de política | SCC no `OCP` vs. Pod Security Admission + Kyverno/OPA nas três nuvens (C4) | ≈ 0,3 FTE/ano — definição inicial com o Security Architect mais manutenção de duas baselines de política |
| Trilha de build | BuildConfig/S2I no `OCP` vs. pipeline de build externo ao cluster em nuvem | ≈ 0,2 FTE/ano — manter dois padrões de pipeline documentados e suportados |
| Trilha de ingress | Routes no `OCP` vs. Ingress/Gateway API em nuvem (ligado a `ADR-0010`, ainda pendente) | ≈ 0,2 FTE/ano — manter dois padrões de exposição de serviço |
| **Total estimado** | | **≈ 0,7 FTE/ano ≈ BRL 210.000/ano (≈ USD 42.000/ano)**, **a confirmar** |

Contra isso, a Opção B evita o esforço de operar o control plane do OpenShift (patching, upgrade, HA) em cada nuvem — estimado, pela mesma ordem de grandeza observada hoje para o control plane on-premises, em **≈ 0,5 FTE/ano por nuvem (≈ 1,5 FTE/ano total) se replicado via Opção A**, ou **zero** sob Opção B (responsabilidade do provedor) e Opção C (responsabilidade do provedor/Red Hat).

### 4.3 Custo de tradução único por aplicação migrada (D3, C3) — Opção B

| Classe de aplicação | Característica | Esforço estimado | Custo estimado (a BRL 1.200/dia-pessoa) |
| --- | --- | --- | --- |
| Simples | Sem Routes customizadas, sem BuildConfig, manifesto já próximo de Kubernetes puro | 2–5 dias-pessoa | BRL 2.400–6.000 |
| Média | Usa Routes com customização (TLS passthrough, annotations específicas) ou S2I simples | 5–10 dias-pessoa | BRL 6.000–12.000 |
| Complexa | BuildConfig customizado, SCC não padrão, integrações profundas com recursos exclusivos do OpenShift | 10–20+ dias-pessoa | BRL 12.000–24.000+ |

A Opção C elimina este custo de tradução inteiramente, porque SCC/Routes/BuildConfig continuam nativos — essa é a principal razão por que C7 (§5) mantém a reabertura por aplicação em vez de descartar ROSA/ARO de forma permanente.

### 4.4 Comparação de custo total projetado, 3 anos, por cenário de volume

Usando as premissas de §4.1 (24 vCPU/cluster = 6 "core-pairs" equivalentes por cluster para fins de licenciamento Red Hat, 1 aplicação migrada por cluster). Três cenários de volume, derivados do mesmo mix proporcional AWS/Azure/OCI (6/5/3) estimado para o primeiro ano em §4.1:

| Cenário | Clusters AWS | Clusters Azure | Clusters OCI | Total de clusters |
| --- | --- | --- | --- | --- |
| Baixo | 3 | 3 | 2 | 8 |
| Base | 6 | 5 | 3 | 14 |
| Alto | 12 | 10 | 6 | 28 |

Para cada opção, o licenciamento/controle plane é calculado por cluster e somado; a Opção C só se aplica aos clusters AWS/Azure (não existe na OCI, que permanece na Opção B em qualquer cenário misto). A linha "C + B na OCI" é a comparação like-for-like: Opção C nos clusters AWS/Azure, Opção B nos clusters OCI — contra a linha B, que é Opção B nas três nuvens. Custo por cluster em 3 anos usado na Opção C: **ROSA HCP ≈ USD 33.500** (6 × 0,171 × 8.760 + 0,25 × 8.760 ≈ USD 11.178/ano); **ARO ≈ USD 58.500** com as VMs de control plane (≈ USD 8.988 + 10.512 ≈ USD 19.500/ano), ou **≈ USD 27.000** só a licença de worker. O compute dos worker nodes não entra em nenhuma linha, por ser o mesmo nas três opções.

| Opção | Baixo (8 clusters) | Base (14 clusters) | Alto (28 clusters) |
| --- | --- | --- | --- |
| **A** — OpenShift auto-gerenciado em todas as nuvens | ≈ USD 464.000 (licença: 8 × 6 pares × USD 1.350 × 3 ≈ USD 194.400 + FTE operação de control plane: 1,5 FTE/ano × USD 60.000 × 3 ≈ USD 270.000) | ≈ USD 610.000 (licença: 14 × 6 × 1.350 × 3 ≈ USD 340.200 + FTE ≈ USD 270.000) | ≈ USD 950.000 (licença: 28 × 6 × 1.350 × 3 ≈ USD 680.400 + FTE ≈ USD 270.000 — **nota:** a 28 clusters a premissa de FTE fixo fica otimista; tratar como piso) |
| **B** — Kubernetes gerenciado nas três nuvens (escolhida) | ≈ USD 161.000 (control plane: 8 × USD 876 × 3 ≈ USD 21.000 + FTE dois modelos: 0,7 FTE/ano × USD 60.000 × 3 ≈ USD 126.000 + tradução: 8 apps classe média × BRL 9.000 ÷ 5,0 ≈ USD 14.400) | ≈ USD 188.000 (control plane ≈ USD 36.800 + FTE ≈ USD 126.000 + tradução: 14 × BRL 9.000 ÷ 5,0 ≈ USD 25.200) | ≈ USD 250.000 (control plane ≈ USD 73.600 + FTE ≈ USD 126.000 + tradução: 28 × BRL 9.000 ÷ 5,0 ≈ USD 50.400) |
| **C + B na OCI** — ROSA HCP/ARO nos clusters AWS/Azure, Kubernetes gerenciado na OCI | ≈ USD 411.000 (ROSA 3 × ≈ USD 33.500 ≈ USD 100.600; ARO 3 × ≈ USD 58.500 ≈ USD 175.500; + OCI/B 2 clusters × [USD 2.628 control plane + USD 1.800 tradução] ≈ USD 8.900; + FTE dois modelos ≈ USD 126.000, ainda necessário porque a OCI segue na Opção B) | ≈ USD 633.000 (ROSA 6× ≈ USD 201.200; ARO 5× ≈ USD 292.500; + OCI/B 3 clusters ≈ USD 13.300; + FTE ≈ USD 126.000) | ≈ USD 1.140.000 (ROSA 12× ≈ USD 402.400; ARO 10× ≈ USD 585.000; + OCI/B 6 clusters ≈ USD 26.600; + FTE ≈ USD 126.000) |
| **Razão A / B** | ≈ 2,9× | ≈ 3,2× | ≈ 3,8× |
| **Razão (C + B na OCI) / B** | ≈ 2,5× | ≈ 3,4× | ≈ 4,6× |
| *Sensibilidade: razão (C + B na OCI) / B sem as VMs de control plane do ARO* | *≈ 2,0× (≈ USD 316.000)* | *≈ 2,5× (≈ USD 475.000)* | *≈ 3,3× (≈ USD 825.000)* |

Nota sobre a Opção A: a linha não inclui as VMs de control plane do OpenShift auto-gerenciado (3 por cluster), que o banco também pagaria; é um piso, o que só reforça a rejeição. No cenário Alto, o misto C+B passa a custar mais que A (≈ USD 1,14M contra ≈ USD 0,95M de piso), porque o prêmio ROSA/ARO escala por vCPU enquanto o FTE de A foi mantido fixo; isso não altera a ordem em relação a B.

**Leitura:** nos três cenários, a Opção B fica mais barata que A (≈2,9×–3,8×) e mais barata que o misto C+B-na-OCI (≈2,5×–4,6×; ≈2,0×–3,3× na sensibilidade sem as VMs de control plane do ARO), e a distância cresce com o volume porque o prêmio de licenciamento de A e C escala linearmente com o número de clusters, enquanto o custo de FTE de B fica relativamente fixo na faixa considerada. Isso confirma D1/D5 com números, não com a alegação de portabilidade. A reabertura por aplicação (C7, §5) não é defensável como critério de custo: mesmo uma aplicação de classe "complexa" (tradução única ≤ BRL 24.000, ≈ USD 4.800) custa uma fração do prêmio ROSA/ARO de um único cluster em 3 anos (≈ USD 27.000–58.500 no ARO, ≈ USD 33.500 no ROSA HCP) — pelo menos 5× menos, mesmo no limite inferior; a comparação de custo por aplicação nunca se aproxima desse prêmio por cluster. A razão real para preservar C7 é a lacuna de D6 (equivalência SCC ↔ Pod Security Admission/Kyverno ainda não formalizada com o Security Architect), não uma vantagem de custo — ver a reformulação do gatilho de C7 em §5.

## 5. Decisão

> **Vamos usar o serviço de Kubernetes gerenciado nativo de cada nuvem — EKS na AWS, AKS no Azure, OKE na OCI — como o runtime padrão de container em nuvem, manter o OpenShift restrito ao data center on-premises, e reservar o OpenShift gerenciado (ROSA/ARO) como alternativa por aplicação apenas quando a equivalência SCC ↔ Pod Security Admission/Kyverno (D6/C4) ainda não estiver aprovada pelo Security Architect para aquele workload CDE/sistema conectado específico — com portabilidade obtida, no caso padrão, na camada de manifestos e políticas, não na identidade do runtime.**

| # | Cláusula | Palavra-chave |
| --- | --- | --- |
| C1 | Todo novo workload em container posicionado em nuvem (ADR-0002) usa o serviço de Kubernetes gerenciado nativo daquela nuvem — EKS, AKS ou OKE — como control plane. | MUST |
| C1a | Clusters que hospedam workload Tier 0 ou Tier 1 usam o tier com SLA de uptime do control plane (AKS Standard; OKE Enhanced); Tier 2 ou Tier 3 pode usar o tier sem SLA formal (AKS Free; OKE Basic) por decisão do time de entrega. | SHOULD |
| C2 | Nenhum novo cluster OpenShift auto-gerenciado é provisionado em nenhuma das três nuvens sem um waiver nomeado (§9). | MUST NOT |
| C3 | Toda aplicação migrada do OpenShift on-prem para Kubernetes gerenciado em nuvem substitui recursos específicos do OpenShift (Routes, BuildConfig, ImageStream, SCC) por seus equivalentes de Kubernetes puro ou de nuvem nativa (Ingress/Gateway API, pipeline de build externo ao cluster, Pod Security Admission), documentados por aplicação, usando as faixas de esforço de §4.3 como referência de estimativa. | MUST |
| C4 | A camada de política de admissão equivalente ao SCC do OpenShift (Pod Security Admission + motor de política como Kyverno ou OPA Gatekeeper) é padronizada entre as três nuvens antes que qualquer workload Tier 1 seja migrado para Kubernetes gerenciado. Piso proposto pelo Security Architect ([ARC-33](/ARC/issues/ARC-33)): Pod Security Admission no nível `restricted`, com Kyverno ou OPA Gatekeeper cobrindo o que o PSA não cobre (registries permitidos, seccomp/AppArmor, volumes), **sempre em modo `enforce`/`deny`, nunca apenas `audit`/`warn`**. A cláusula só se considera atendida com a matriz de mapeamento regra a regra SCC → PSA/Kyverno, por nuvem, assinada pelo Security Architect. Workload CDE exige, além disso, a aprovação do enclave (`ADR-0003` C2, `ADR-0012`–`ADR-0014`). | MUST |
| C5 | Manifestos de aplicação (Deployments, Services, ConfigMaps, NetworkPolicies) são escritos em Kubernetes puro (ou Helm/Kustomize sobre Kubernetes puro), nunca em formato exclusivo do OpenShift, para preservar a portabilidade entre on-prem e nuvem que D3 quantifica como viável nesta camada. | SHOULD |
| C6 | Um time de entrega pode solicitar um cluster OpenShift auto-gerenciado em nuvem, via waiver, apenas quando a aplicação depende de uma funcionalidade específica do OpenShift (ex.: BuildConfig/S2I integrado) sem equivalente viável em Kubernetes gerenciado documentado, e quando a Opção C (C7) também não resolve o caso. | MAY (sujeito a waiver, §9) |
| C7 | Na AWS e no Azure, um time de entrega pode solicitar ROSA ou ARO, respectivamente, em vez de Kubernetes gerenciado padrão (C1), quando a aplicação for um workload CDE ou sistema conectado ao ambiente de dados de cartão (PCI-DSS) e a equivalência entre SCC e Pod Security Admission/Kyverno (C4, D6) ainda não estiver aprovada pelo Security Architect para aquele workload especificamente — sujeito a aprovação conjunta do Cloud Architect e do Security Architect, sem necessidade de waiver formal por ser uma alternativa já avaliada e prevista nesta ADR. Não é um critério de custo: o modelo de §4.4 mostra que o prêmio do serviço gerenciado por cluster (≈ USD 27.000–58.500 em 3 anos no ARO; ≈ USD 33.500 no ROSA HCP) é pelo menos 5× maior que qualquer custo de tradução por aplicação (§4.3, até ≈ USD 4.800), inclusive na classe "complexa". Não se aplica à OCI, que não tem serviço gerenciado de OpenShift first-party. | MAY |
| C8 | Clusters EKS, AKS ou OKE já em produção na data de vigência desta ADR entram no inventário de §5.3 e recebem prazo para atingir conformidade com C4 (camada de política) e C5 (manifestos). | MUST |

- **Por que esta opção:** o trade-off aceito é investir em uma camada de abstração de manifestos e políticas comuns, uma vez, em troca de eliminar o custo operacional recorrente e o licenciamento de operar um OpenShift auto-gerenciado em cada uma das três nuvens — com números em §4, não apenas a alegação de portabilidade.
- **Alternativas rejeitadas:** Opção A (OpenShift auto-gerenciado em todas as nuvens) rejeitada por ficar, no modelo de §4.4, entre ≈2,9× e ≈3,8× mais cara em 3 anos que a Opção B nos três cenários de volume, sem ganho de portabilidade que a Opção B já não entregue na camada de manifestos. Opção C (OpenShift gerenciado, ROSA/ARO) rejeitada **como padrão** pelo mesmo tipo de motivo — o misto C+B-na-OCI fica entre ≈2,5× e ≈4,6× mais caro que B pura (≈2,0×–3,3× sem as VMs de control plane do ARO) — mas mantida como alternativa formal por aplicação via C7, não por custo (o próprio modelo mostra que o custo de tradução nunca se aproxima do prêmio de licenciamento por cluster), e sim pela lacuna de D6 ainda não fechada com o Security Architect para workload CDE/conectado.

### 5.1 Aplicabilidade no estado

| Plataforma | Aplica-se? | Restrição, diferença ou exclusão |
| --- | --- | --- |
| `DC-AA` — DC on-prem, active-active | N/A | O runtime on-premises (OpenShift) não é decidido por esta ADR — é [ARC-5](/ARC/issues/ARC-5), `ADR-0008`, Infrastructure Architect. |
| `OCP` — OpenShift on-prem | N/A | Mesma observação; esta ADR apenas declara que o OpenShift auto-gerenciado permanece restrito a este ambiente por padrão, sem decidir sua operação interna. |
| `VM-ON` — VMs / bare metal on-prem | N/A | Fora de escopo — decisão on-premises. |
| `AWS-K8S` — Kubernetes gerenciado na AWS | Sim | EKS é o padrão (C1); ROSA é alternativa por aplicação (C7); a estrutura de conta que o hospeda é `ADR-0003`. |
| `AWS-VM` — compute AWS | N/A | Esta ADR trata do runtime de container; compute de VM pura na AWS não é afetado. |
| `AZR-K8S` — Kubernetes gerenciado no Azure | Sim | AKS é o padrão (C1); ARO é alternativa por aplicação (C7). |
| `AZR-VM` — compute Azure | N/A | Mesma observação de `AWS-VM`. |
| `OCI-K8S` — Kubernetes gerenciado na OCI | Sim | OKE é o padrão (C1); não há alternativa de OpenShift gerenciado na OCI — a única alternativa, quando necessária, é o waiver de C6 (OpenShift auto-gerenciado). |
| `OCI-VM` — compute OCI | N/A | Mesma observação. |
| `DATA-REL` — bases de dados relacionais | N/A | Esta ADR trata de runtime de container de workload de aplicação, não da camada de dados. |
| `DATA-NREL` — bases de dados não relacionais | N/A | Mesma observação. |
| `AGW-AWS` — AWS API Gateway | N/A | Decisão de gateway é `ADR-0010`, [ARC-6](/ARC/issues/ARC-6). |
| `AGW-AZR` — Azure API Management | N/A | Mesma observação. |
| `AGW-AXW` — Axway API Gateway | N/A | Mesma observação. |

### 5.2 Escopo de workloads

- **Aplica-se a:** todo novo workload em container posicionado em nuvem a partir da data de vigência, e a todo cluster EKS/AKS/OKE já existente (C8).
- **Workloads existentes:** nenhum cluster OpenShift em nuvem foi identificado em produção até a data desta proposta; se algum existir, deve ser inventariado e migrado para Kubernetes gerenciado dentro de 270 dias, ou receber um waiver nomeado (C6) dentro de 90 dias. Os clusters EKS/AKS/OKE já em produção (§5.3) seguem o cronograma de C8.
- **Explicitamente fora de escopo:** o runtime de container on-premises (ADR-0008, Infrastructure Architect); a escolha de ferramentas de CI/CD (G3, lacuna consciente da primeira onda, [registro de decisões §12](/ARC/issues/ARC-2#document-decision-registry)).

### 5.3 Inventário de Kubernetes já em produção

| Nuvem | Clusters EKS/AKS/OKE confirmados em produção na data desta proposta | Prazo para conformidade com C4 (camada de política) | Prazo para conformidade com C5 (manifestos) |
| --- | --- | --- | --- |
| AWS | A confirmar — inventário formal pendente (Q2, §10) | 180 dias após confirmação do inventário | 270 dias após confirmação do inventário |
| Azure | A confirmar — inventário formal pendente (Q2, §10) | 180 dias após confirmação do inventário | 270 dias após confirmação do inventário |
| OCI | A confirmar — inventário formal pendente (Q2, §10) | 180 dias após confirmação do inventário | 270 dias após confirmação do inventário |

Esta ADR não presume o número de clusters existentes — o inventário formal é a Q2 de §10, e os prazos acima começam a contar da confirmação desse inventário, não da data de vigência da ADR.

## 6. Consequências

### Positivas

- Elimina o custo operacional recorrente de operar um control plane auto-gerenciado em cada uma das três nuvens, usando o SLA de control plane gerenciado que o banco já paga ao contratar a nuvem — quantificado em §4.4 (Opção B entre ≈2,9× e ≈3,8× mais barata que a Opção A em 3 anos, conforme o volume).
- Remove o licenciamento Red Hat por nó em nuvem, substituindo-o por taxa de control plane gerenciado tipicamente menor.
- Preserva a portabilidade que de fato importa — manifestos de aplicação em Kubernetes puro — sem pagar o custo de operar um segundo OpenShift auto-gerenciado só para portabilidade de recursos que a maioria das aplicações não usa (Routes, BuildConfig).
- Mantém o OpenShift gerenciado (ROSA/ARO) disponível como resposta formal, não ad hoc, para o subconjunto de aplicações CDE/conectadas onde a equivalência SCC ↔ Pod Security Admission/Kyverno ainda não estiver aprovada pelo Security Architect (C7) — não como escolha de custo.

### Negativas, e o que fica mais difícil

- A equipe de plataforma opera dois modelos de identidade/rede diferentes (SCC no DC, Pod Security Admission + motor de política em nuvem) até a equivalência ser formalmente definida com o Security Architect (questão aberta, §10) — custo recorrente estimado em §4.2.
- Aplicações que dependem de recursos específicos do OpenShift (BuildConfig/S2I, Routes com funcionalidades proprietárias) precisam de esforço de tradução por aplicação migrada — não é automático, e o esforço está nas faixas de §4.3.
- Times perdem a opção de operar um runtime idêntico em todos os quatro ambientes; ganham, em troca, o SLA de control plane gerenciado e, quando necessário, a opção C7 sem perder a economia agregada de §4.4.

### O que esta decisão impede no futuro

Impede que o banco acumule múltiplos control planes auto-gerenciados (custo operacional e de licenciamento crescente); reverter essa decisão depois — migrar workloads já em Kubernetes gerenciado para OpenShift auto-gerenciado em nuvem — teria custo de migração completo por aplicação, sem benefício declarado além de uniformidade de ferramenta, que o modelo de §4 já mostra não compensar o custo.

### Impacto de migração no estado existente

| O que existe hoje | O que precisa mudar | Responsável | Até quando |
| --- | --- | --- | --- |
| Clusters EKS/AKS/OKE já em produção, ainda não inventariados formalmente | Confirmar inventário completo (§5.3) | Cloud Architect | 90 dias após a data de vigência |
| Nenhum cluster OpenShift em nuvem confirmado como existente | Confirmar inventário; se existir algum, iniciar migração ou solicitar waiver | Cloud Architect | 90 dias após a data de vigência (inventário); 270 dias (migração, se aplicável) |
| Piso de equivalência SCC → Pod Security Admission/Kyverno proposto pelo Security Architect (C4), sem a matriz de mapeamento | Produzir a **matriz SCC → PSA/Kyverno regra a regra, por nuvem** (EKS, AKS, OKE) e obter a assinatura do Security Architect | Cloud Architect produz; Security Architect assina | Antes da migração do primeiro workload Tier 1 para Kubernetes gerenciado |
| Clusters existentes sem a camada de política C4 ou sem manifestos C5 conformes | Trazer à conformidade conforme cronograma de §5.3 | Times de entrega donos de cada cluster, com o Cloud Architect | Conforme §5.3, a partir da confirmação do inventário |

## 7. Impacto de conformidade e regulatório

Profundidade conforme `PARAM-REGDEPTH`. IDs de obrigação do [modelo operacional](/ARC/issues/ARC-2#document-operating-model) §3.

| Regime | ID(s) de obrigação aplicável(is) | O que a obrigação exige, em substância | Como esta decisão a endereça | Risco residual | Controle / evidência de que se mantém |
| --- | --- | --- | --- | --- | --- |
| BACEN/CMN | `OBL-CLOUD` (condicional) | Notificação ao BACEN de contratação ou alteração relevante de serviço em nuvem (território da Res. CMN 4.893/2021) | No caso padrão (C1, Kubernetes gerenciado nativo), esta ADR não cria nem altera, por si só, um arranjo de contratação de serviço em nuvem — isso é tratado por `ADR-0002`/`ADR-0003`. **Condicional:** quando C7 for exercida (ROSA ou ARO, por aplicação) e, para aquele caso, a resposta à pergunta de escopo "Ela muda um arranjo relevante de processamento de dados, armazenamento ou serviço em nuvem?" (abaixo) passar a `Sim`, o arranjo de OpenShift operado conjuntamente pelo provedor de nuvem e pela Red Hat pode configurar contratação/alteração relevante de serviço em nuvem sob a Res. CMN 4.893/2021 — isso só se aplica quando C7 é efetivamente exercida, não ao caso padrão | Se C7 for exercida sem confirmação da função de Compliance sobre o gatilho de notificação ao BACEN, o banco corre o risco de deixar de identificar um evento regulatoriamente relevante | Quando C7 for exercida: registro do exercício (mesmo registro de aprovação de C7 em §8) **mais** confirmação explícita da função de Compliance do banco sobre se aquele arranjo aciona a notificação ao BACEN sob a Res. CMN 4.893/2021 e, em caso positivo, o prazo aplicável — o Escritório não determina essa timing nem emite parecer jurídico (ver nota do Escritório, abaixo) |
| LGPD | `N/A` — o runtime de container não altera, por si só, onde dados pessoais são processados ou como são protegidos | N/A — mesmo motivo | O runtime de container não altera, por si só, onde dados pessoais são processados ou como são protegidos — isso é tratado por `ADR-0002` (residência) e `ADR-0012`–`ADR-0014` (segurança) | N/A — mesmo motivo | N/A — mesmo motivo |
| PCI-DSS | `OBL-PCI` | Controles de acesso e configuração segura de qualquer sistema dentro ou conectado ao CDE | A equivalência entre SCC e Pod Security Admission/Kyverno (C4) precisa, quando aplicada a um workload CDE, atender ao mesmo padrão de configuração segura que o SCC garante hoje no OpenShift — ainda não formalizada (§10). Para workload CDE, C7 (ROSA/ARO) evita a lacuna de equivalência SCC ↔ PSA/Kyverno ao preservar o SCC nativo — mas não evita exposição de PCI-DSS inteiramente: ROSA e ARO são operados conjuntamente pelo provedor de nuvem e pela Red Hat, cuja equipe de SRE tem acesso operacional ao cluster. Isso deve ser tratado como escopo conectado-a/impactante de segurança (não como risco evitado) na decisão por aplicação quando o workload for CDE (ver família de requisitos PCI-DSS afetada, abaixo) | A migração de um workload CDE para Kubernetes gerenciado antes de C4 estar formalizada e aprovada pelo Security Architect ampliaria risco de configuração insegura; quando C7 é usada, o acesso operacional da SRE da Red Hat ao cluster é um risco residual próprio, de gestão de prestador de serviço, que não é eliminado pelo uso de C7 | C4 como cláusula `MUST` (nível de waiver `W3` — controle que implementa diretamente `OBL-PCI`, por precedente A23); nenhum workload CDE migra para nuvem antes de `ADR-0003` C2 (unidade dedicada) e da aprovação do enclave pelo Security Architect; C7 disponível como mitigação estrutural para a lacuna de equivalência SCC ↔ PSA/Kyverno em CDE na AWS/Azure, condicionada ao registro do acesso operacional da SRE da Red Hat como escopo conectado-a (§8) |

Linha de PCI-DSS não é `N/A`:

| Pergunta de escopo PCI-DSS | Resposta |
| --- | --- |
| Esta decisão armazena, transmite ou processa dados de portador de cartão (cardholder data)? | Não — esta ADR decide o runtime de container, não o posicionamento de dados. |
| Esta decisão introduz ou altera um sistema conectado ao ambiente de dados de cartão (CDE)? | Potencialmente, indiretamente — se um workload CDE for posicionado em nuvem no futuro (sujeito a `ADR-0002` C2 e `ADR-0003` C2), ele usará o runtime definido aqui (C1) ou, por decisão explícita, ROSA/ARO (C7), e a equivalência de controles de C4 passa a valer para ele no caso padrão. |
| Efeito sobre o escopo do PCI-DSS (amplia / reduz / não altera) | **Não altera** no caso padrão (C1, Kubernetes gerenciado nativo). **Amplia, condicionado ao exercício de C7** em workload CDE: ao acionar ROSA/ARO, o acesso operacional da equipe de SRE da Red Hat ao cluster traz esse arranjo para o escopo *connected-to* — ver família de requisitos PCI-DSS afetada, abaixo —, onde antes (sob o caso padrão C1) não havia essa população de terceiro com acesso ao control plane. Mesma lógica de vocabulário de `ADR-0003` (D-22(m)). |
| Família de requisitos PCI-DSS afetada | Configuração segura / hardening de baseline (Requisito 2); controle de acesso (Requisito 7), quando e se C4 vier a cobrir um workload CDE, ou nativamente via SCC se C7 for usado. Quando C7 (ROSA/ARO) for exercida, o acesso operacional da equipe de SRE da Red Hat ao cluster conecta o arranjo, para fins de PCI-DSS, aos Requisitos 7 (controle de acesso), 8 (identificação e autenticação) e 12 (gestão de prestador de serviço) — escopo conectado-a/impactante de segurança, não evitado pelo uso de C7. |

Perguntas gerais de conformidade:

| Pergunta | Resposta |
| --- | --- |
| Esta decisão toca dados pessoais? | Não — esta ADR decide o runtime de container; a categorização de dado pessoal é tratada por `ADR-0002`. |
| Ela envolve processamento ou armazenamento fora do Brasil? | Não — a região de processamento é decidida por `ADR-0002`/`ADR-0003`, não por esta ADR. |
| Ela muda um arranjo relevante de processamento de dados, armazenamento ou serviço em nuvem? | Sim — quando C7 é exercida (ROSA/ARO), cria um arranjo de serviço de OpenShift operado conjuntamente pelo provedor de nuvem e pela Red Hat, com a equipe de SRE da Red Hat operando o cluster, que pode acionar `OBL-CLOUD` (§7) sob a Res. CMN 4.893/2021. No caso padrão (C1), esse arranjo não existe. |
| Ela muda a postura de resiliência ou recuperação de um serviço crítico? | Não — tiers de DR são `ADR-0009`, Infrastructure Architect; o SLA de control plane gerenciado (C1a) é um insumo, não uma mudança de tier. |
| Ela muda a detecção de incidentes, logging ou rastreabilidade? | Não — logging de cluster segue o baseline de `ADR-0003` C3, independentemente do runtime escolhido aqui. |
| Ela cria ou muda uma dependência de terceiro / fornecedor? | Sim — quando C7 é exercida, formaliza uma dependência contratual adicional com a Red Hat via ROSA/ARO, com acesso operacional da equipe de SRE da Red Hat ao cluster, tratado como escopo PCI-DSS conectado-a (§7). |

> O Escritório mapeia obrigações para controles de arquitetura em nível geral. Ele não emite parecer jurídico, e a suficiência jurídica é confirmada pelas funções de compliance e jurídico do banco.

## 8. Conformidade e verificação

| Cláusula | Como a conformidade é verificada | Onde a evidência vive | Automatizável? |
| --- | --- | --- | --- |
| C1 | Inspeção do tipo de cluster provisionado (EKS/AKS/OKE) via API de cada nuvem | Inventário de clusters, mantido pelo Cloud Architect | Sim |
| C1a | Inspeção do tier do cluster (AKS Standard/Free; OKE Enhanced/Basic) contra o tier do workload hospedado | Inventário de clusters | Sim |
| C2 | Ausência de cluster OpenShift auto-gerenciado em nuvem sem waiver registrado | Inventário de clusters; registro de waivers | Sim |
| C3 | Revisão de manifesto por aplicação migrada confirma ausência de recursos exclusivos do OpenShift sem equivalente documentado | Repositório de manifestos da aplicação | Parcialmente — presença de recurso é automatizável; adequação do equivalente exige revisão humana |
| C4 | Confirmação de que a política de admissão equivalente está implantada antes de qualquer workload Tier 1 migrar | Configuração do motor de política (Kyverno/OPA) por cluster | Sim |
| C5 | Revisão por amostragem de manifestos confirma uso de Kubernetes puro/Helm/Kustomize | Repositório de manifestos | Não — é `SHOULD`, avaliado por amostragem |
| C6 | Todo cluster OpenShift auto-gerenciado em nuvem tem um waiver correspondente | Registro de waivers | Sim |
| C7 | Toda instância de ROSA/ARO tem a lacuna de equivalência SCC ↔ Pod Security Admission/Kyverno para aquele workload CDE/conectado, registrada e aprovada pelo Cloud Architect e pelo Security Architect | Registro de decisão por aplicação, mantido pelo Cloud Architect | Sim — aprovação do Security Architect condiciona o uso, não é só registro de negócio |
| C8 | Inventário de clusters existentes (§5.3) confirmado e com prazos de conformidade C4/C5 atribuídos | Inventário de clusters | Sim, para a existência do registro; não para a conformidade em si |

## 9. Exceções

- **Waivers possíveis contra esta ADR:** Sim, com os níveis abaixo.
- **Nível por cláusula:**
  - C1 — `W2` (estrutural, afeta mais de um time).
  - C1a — `W1` (processual, por cluster).
  - C2 — `W2` (mesma classe; não implementa diretamente uma obrigação regulatória mapeada, mas é sistemática).
  - C3 — `W1` (processual, por aplicação).
  - C4 — `W3` (corrigido de `W2`: por precedente A23, C4 é explicitamente nomeado na coluna de controle/evidência da obrigação `OBL-PCI` em §7 — é controle que implementa diretamente uma obrigação PCI-DSS mapeada, não apenas um controle sistemático genérico; a formalização final da equivalência continua sendo do Security Architect via suas próprias ADRs).
  - C5 — `W1` (processual, de baixo risco).
  - C6 — não aplicável a waiver por ser `MAY` já condicionado a waiver em sua própria redação.
  - C7 — não aplicável a waiver por ser `MAY` com aprovação direta do Cloud Architect, não um desvio da regra.
  - C8 — `W1` (processual, por cluster, quando o prazo não for exequível).
- **Cláusulas que não podem receber waiver:** nenhuma.

## 10. Questões abertas

| # | Questão | Quem deve responder | Necessário até |
| --- | --- | --- | --- |
| Q1 | Equivalência formal entre Security Context Constraints (OpenShift) e Pod Security Admission + motor de política (Kyverno/OPA) em cada nuvem. **Respondida em parte pelo Security Architect ([ARC-33](/ARC/issues/ARC-33)):** piso PSA `restricted` + Kyverno/Gatekeeper em `enforce`/`deny` (C4). **Em aberto:** a matriz regra a regra SCC → PSA/Kyverno por nuvem, que o Security Architect assina antes do primeiro workload Tier 1 | Cloud Architect produz a matriz; Security Architect assina | Antes da migração do primeiro workload Tier 1 (não bloqueia a submissão ao comitê, porque C4 já fixa o piso e a condição) |
| Q2 | Inventário formal de clusters EKS/AKS/OKE já em produção (para §5.3) e de qualquer cluster OpenShift em nuvem (para C2) | Cloud Architect | 90 dias após a data de vigência |
| Q3 | Preço de lista confirmado contra o contrato vigente do banco (Red Hat ELA; tabela de nuvem negociada) para refinar o modelo de custo de §4 além das premissas de lista pública | Cloud Architect, com Procurement/Sourcing do banco | **2026-10-21** (data absoluta, não "antes da submissão ao comitê" — mesmo cálculo de D-22(s): 10 dias úteis a partir de 2026-10-06, com 2026-10-12 como feriado nacional) |

## 11. Revisão por pares e discordâncias

| Revisor | Papel | Veredito | Comentário / discordância |
| --- | --- | --- | --- |
| Infrastructure Architect | obrigatório — verifica coerência com `ADR-0008` | **Concorda com comentário** | Revisão concluída em 2026-10-05 (ARC-34). **Coerência com ADR-0008:** sem conflito de decisão — esta ADR pressupõe corretamente que a forma de compute já foi decidida como container antes de escolher o orquestrador. Imprecisão a corrigir, sem efeito na decisão: a nota de relacionamento no cabeçalho descreve `ADR-0008` como decidindo "o lado on-premises", mas ADR-0008 decide VM vs. container nos quatro ambientes, incluindo nuvem (seu §4.1 marca `AWS-K8S`/`AWS-VM`, `AZR-K8S`/`AZR-VM`, `OCI-K8S`/`OCI-VM` como "Sim") — ADR-0004 decide apenas o orquestrador dado que a forma de compute já é container. **C1a e a escala de criticidade — correção pendente antes da submissão ao comitê:** confirmo que Tier 0–3 é de fato a escala vigente de `ADR-0009` (quatro tiers, Tier 0 o mais crítico). Apesar de a rodada ter sido descrita como já corrigida, o texto ainda não reflete essa escala: C1a (§5) e a tabela de premissas de custo (§4.1) ainda referem-se a "Tier 3/4" — `Tier 4` não existe na escala de ADR-0009 — e `Tier 0`, o mais crítico, fica fora da cláusula inteiramente. Peço correção para a escala real: o tier de control plane com SLA deveria cobrir no mínimo Tier 0 e Tier 1, com Tier 2/Tier 3 elegíveis ao tier sem SLA formal — a decisão exata de onde cortar é do Cloud Architect, mas "Tier 3/4" e a omissão de Tier 0 não podem permanecer como estão. |
| Security Architect | obrigatório — a ADR toca controles de segurança equivalentes ao SCC (Q1, C4, C7) | **Concorda com comentário** | Revisão concluída em 2026-10-05 ([ARC-33](/ARC/issues/ARC-33); [veredito](/ARC/issues/ARC-4#comment-76da2d70-e250-43cd-bf51-6a5e4fdd0834)). **C7:** concorda que o gatilho reescrito (CDE/sistema conectado + equivalência SCC ↔ PSA não aprovada para aquele escopo) reflete a lacuna de segurança real. **Q1:** propõe PSA `restricted` como piso equivalente ao SCC `restricted`, com Kyverno/OPA Gatekeeper fechando o que o PSA não cobre (registries permitidos, seccomp/AppArmor, volumes), sempre em `enforce`/`deny`, nunca só `audit`/`warn`. Isso fecha o piso de admissão para Tier 1 não-CDE; workload CDE continua exigindo a aprovação do enclave. **Condição para a aprovação formal de C4:** matriz de mapeamento regra a regra SCC → PSA/Kyverno por nuvem, produzida pelo Cloud Architect e assinada pelo Security Architect antes da migração do primeiro workload Tier 1 — registrada em C4, na tabela de migração (§6) e em Q1. |
| Enterprise Architect | obrigatório — verificação de não conflito com ADR aceita | **pedido ancorado aberto em 2026-10-06** (ver nota abaixo) | Esta ADR nunca teve pedido de revisão geral ao Enterprise Architect registrado — o pedido de 2026-10-06 a [ARC-77](/ARC/issues/ARC-77) cobria apenas `ADR-0003`, `AR-003`, `AR-004` e `AR-005`, não `ADR-0004`. O Cloud Architect abre agora pedido próprio, ancorado por precedente 14 (ver nota abaixo); prazo de 10 dias úteis, vence em **2026-10-21** (D-22(s)). |

> **Nota de edição (fora do veredito do revisor):** em 2026-10-05 (4ª rodada), a correção solicitada pelo Infrastructure Architect nesta mesma rodada de revisão — nota de relacionamento com `ADR-0008` e escala Tier 0–3 em C1a/§4.1 — foi aplicada ao texto do ADR. Ver §12 (Histórico de status) para o registro da mudança. Esta nota foi deslocada para fora da célula de veredito do Infrastructure Architect nesta revisão (2026-10-06), por precedente: a célula de veredito deve conter apenas o veredito e o comentário/discordância genuínos do revisor, não notas editoriais de acompanhamento.

**Pedido de revisão por pares registrado em 2026-10-06 para C4, C7 e o bloco de consequências/BACEN — respondido no mesmo dia, dentro do prazo de 10 dias úteis (D-22(s)):**

- **Security Architect** ([ARC-78](/ARC/issues/ARC-78), veredito de 2026-10-06T01:34:46Z, transcrito literalmente): **Concorda.** "C4 em `W3` está correto pelo precedente A23 — a cláusula é nomeada explicitamente na coluna de controle/evidência da linha `OBL-PCI`. C7: o aprovador está agora alinhado entre §5 (aprovação conjunta Cloud Architect + Security Architect) e §8 (verificação). O tratamento do acesso operacional da SRE da Red Hat em ROSA/ARO como escopo connected-to/impactante de segurança sob os Requisitos 7, 8 e 12 — não como risco eliminado pelo uso de C7 — está correto e é exatamente a leitura que eu daria. Bloco OBL-CLOUD: a notificação ao BACEN sob a Res. CMN 4.893/2021 corretamente tratada como condicional ao exercício de C7, não ao caso padrão."
- **Infrastructure Architect** ([ARC-84](/ARC/issues/ARC-84), veredito de 2026-10-06T01:37:02Z, transcrito literalmente): **Concorda.** "Cláusulas tocadas nesta rodada (C4 — waiver `W2`→`W3` por precedente A23; C7 — alinhamento do aprovador entre §5/§8; bloco de consequências/BACEN de §7, reformulado como `OBL-CLOUD` condicional ao exercício de C7) não tocam a fronteira com `ADR-0008` (VM vs. container, meu domínio) nem introduzem dependência nova de conectividade híbrida ou tiering de DR. Sem objeção."

**Pedido ancorado ao Enterprise Architect, aberto em 2026-10-06 (precedente 14):** documento `adr-0004-kubernetes-gerenciado-vs-openshift`, `revisionNumber` e `createdAt` da revisão vigente no momento da abertura (ver §12); versão do artefato conforme §0. Foco do pedido: revisão geral de não conflito com ADR aceita (nunca executada). Prazo de 10 dias úteis, vence em 2026-10-21.

Nenhuma discordância registrada nesta rodada. Os vereditos já registrados para a rodada de 2026-10-05 (acima) permanecem válidos para o restante do documento, não revisitado nesta rodada.

## 12. Histórico de status

| Data | De → Para | Quem | Por quê |
| --- | --- | --- | --- |
| 2026-10-05 | `—` → `proposta` | Cloud Architect | Rascunho iniciado |
| 2026-10-05 | `proposta` → `proposta` | Cloud Architect | Mudanças solicitadas pelo Arquiteto Principal endereçadas: adicionado modelo de custo quantificado (§4), adicionada a alternativa de OpenShift gerenciado ROSA/ARO (Opção C, C7), trazido o Kubernetes já em produção nas três nuvens para o escopo (§5.3, C8), corrigidos erros de digitação ("nuvines"), e solicitada revisão ao Infrastructure Architect e ao Security Architect. |
| 2026-10-05 | `proposta` → `proposta` | Cloud Architect (com Arch Master) | Bloqueantes 1–2 da [3ª revisão](/ARC/issues/ARC-4#comment-d6a72b2a-473d-4f6f-979f-d3828f70f37a) efetivamente corrigidos no texto salvo (revisão anterior descrevia correções que não estavam no documento): §4.1 corrige a métrica core-pair (4 vCPUs, não 2) e separa a taxa de control plane do ROSA da do ARO; §4.4 reescrita com 6 core-pairs/cluster (não 12), FTE de Opção A em USD 270.000 (não 180.000), e três cenários de volume reais (Baixo/Base/Alto) com a comparação like-for-like "C + B na OCI" vs. "B nas três nuvens"; removida a frase de resposta à revisão que tinha entrado no corpo do ADR. C7 reescrita nas quatro ocorrências (§3/Opção C, §5 decisão, §5 tabela C7, §9 tabela de conformidade): o gatilho deixa de ser custo (o modelo mostra que nunca dispara) e passa a ser a lacuna real de equivalência SCC ↔ Pod Security Admission/Kyverno para workload CDE/conectado, não aprovada pelo Security Architect. C1a e a premissa de §4.1 corrigidas para a escala Tier 0–3 (Tier 0/1 com SLA; Tier 2/3 sem SLA formal), e a nota de relacionamento com `ADR-0008` corrigida para refletir que ele decide VM vs. container nos quatro ambientes, não só on-premises — ambas as correções pedidas pelo Infrastructure Architect em [ARC-34](/ARC/issues/ARC-34). |
| 2026-10-05 | `proposta` → `proposta` | Arquiteto Principal (correção direta, autorizada pelo board em [ARC-4](/ARC/issues/ARC-4#comment-d20735e2-91fc-449e-9805-066bf835d40a)) | 4ª rodada: premissa de preço ROSA/ARO corrigida para USD 0,171 por **4** vCPU-hora (fonte: páginas públicas de preço da AWS e da Microsoft, 2026-10-05); modelo ROSA HCP declarado (USD 0,25/h/cluster); ARO calculado separadamente, com as VMs de control plane pagas pelo banco e sensibilidade sem elas; razões (C + B na OCI)/B recalculadas para ≈2,5×/3,4×/4,6× (≈2,0×/2,5×/3,3× na sensibilidade) em §3, §4.4 e §5; prêmio por cluster em C7 corrigido. Veredito do Security Architect (ARC-33) transcrito em §11, C4, Q1 e na tabela de migração (matriz SCC → PSA/Kyverno por nuvem). A decisão não muda. |
| 2026-10-06 | `proposta` → `proposta` | Cloud Architect | Retrabalho de alinhamento após devolução no gate do Escritório (classe M, [ARC-67](/ARC/issues/ARC-67)) — nenhuma decisão de mérito revisitada. Mudanças: (1) pergunta de escopo sobre arranjo relevante de serviço em nuvem (§7) corrigida para "Sim — C7", nomeando o arranjo ROSA/ARO operado pela Red Hat; (2) C4 explicitamente nomeado na coluna de controle/evidência de `OBL-PCI` (§7) e nível de waiver elevado de `W2` para `W3` em §9, por precedente A23; (3) "aceita nesta ADR" (C7, §5) corrigido para "prevista nesta ADR", já que uma ADR `proposta` não pode se autodeclarar aceita; (4) aprovador de C7 alinhado entre §5 e §8 — aprovação conjunta do Cloud Architect e do Security Architect nos dois lugares; (5) linha BACEN de §7 reformulada como `OBL-CLOUD` condicional, disparada apenas quando C7 for exercida e a pergunta de arranjo em nuvem responder "Sim", com confirmação de Compliance exigida sobre o gatilho de notificação da Res. CMN 4.893/2021, sem o Escritório determinar prazo; (6) "C7 evita essa lacuna inteiramente" (§7, `OBL-PCI`) corrigido — C7 evita a lacuna de equivalência SCC ↔ PSA/Kyverno, mas não evita escopo PCI-DSS: o acesso operacional da SRE da Red Hat ao cluster é nomeado como conectado-a/impactante de segurança sob os Requisitos 7, 8 e 12 (Família de requisitos, §7); (7) cabeçalho "Implementada por ARs" corrigido para referenciar AR-003 (AWS), AR-004 (Azure) e AR-005 (OCI); (8) nota editorial "Corrigido em 2026-10-05 (4ª rodada)" removida da célula de veredito do Infrastructure Architect em §11 e deslocada para uma nota separada fora da tabela. Registrado em §11 um pedido de revisão por pares datado (vence 2026-10-20) ao Security Architect e ao Infrastructure Architect, restrito às cláusulas C4, C7 e ao bloco de consequências/BACEN tocados nesta revisão — sem novo veredito até o prazo vencer. |
| 2026-10-07 | `proposta` → `proposta` | Cloud Architect | **Achados de prontidão de [ARC-138](/ARC/issues/ARC-138) (veredito consolidado de 06:29), retrabalho [ARC-171](/ARC/issues/ARC-171).** G7: removidos "Requisitos 7, 8 e 12" de §7 (linha de efeito de escopo PCI-DSS), fora da célula de Família permitida; texto remete à família de requisitos já citada na linha de substância. G2: linha LGPD de §7 tinha três células `—` sem motivo declarado na própria célula; cada uma agora traz `N/A — mesmo motivo` ou o motivo completo. A23 reconferida: C4 já está em `W3` em §9 (corrigido em revisão anterior) e é a única cláusula nomeada na coluna de controle/evidência da linha `OBL-PCI`; nenhuma outra célula de controle/evidência nomeia cláusula fora de `W3`/não sujeita a waiver. G11: pedido de revisão geral ao Enterprise Architect (ARC-77) permanece sem veredito — bloqueador genuíno de terceiro, prazo não vencido, não é falha de prontidão desta ADR. Varredura obrigatória executada; nenhuma outra ocorrência dos termos antigos fora deste changelog. Status permanece `proposta`; nada aqui é aprovação. |
| 2026-10-06 | `proposta` → `proposta` | Cloud Architect | Retrabalho do gate interno ARC-76 (2ª rodada), tarefa [ARC-130](/ARC/issues/ARC-130) — nenhuma decisão de mérito revisitada; esta ADR segue classe **F** (substância aceita, só forma e transcrição). Mudanças: (1) vereditos de Security Architect ([ARC-78](/ARC/issues/ARC-78)) e Infrastructure Architect ([ARC-84](/ARC/issues/ARC-84)), já emitidos em 2026-10-06 sobre C4/C7/BACEN, transcritos literalmente em §11 — a afirmação anterior de que "nenhum veredito é registrado até o prazo vencer" estava desatualizada: os revisores responderam no mesmo dia, dentro do prazo; (2) pedido de revisão geral ao Enterprise Architect aberto, ancorado por precedente 14 — nunca havia sido aberto (o pedido de [ARC-77](/ARC/issues/ARC-77) cobria `ADR-0003`/`AR-003`/`AR-004`/`AR-005`, não esta ADR); (3) efeito PCI-DSS (§7) corrigido para o vocabulário de D-22(m): "não altera" no caso padrão, "amplia, condicionado ao exercício de C7"; (4) as seis perguntas gerais de conformidade (§7) reescritas para resposta estritamente `Sim`/`Não` (A19); (5) Q3 (§10) redatada com data absoluta (2026-10-21), em vez de "antes da submissão ao comitê". Status permanece `proposta`; nada aqui é aprovação. |
