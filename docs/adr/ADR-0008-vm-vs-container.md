# ADR-0008 — Adotar container como padrão para workloads stateless e novos, com VM/bare metal reservado a exceções declaradas

> **Como usar este documento:** histórico completo das rodadas de revisão em §11. Revisão corrente (2026-10-06), classe **S**: aplica a devolução "Alterações solicitadas" do gate do Arquiteto Principal sobre o retrabalho de [ARC-68](/ARC/issues/ARC-68) — pergunta do hypervisor (Q5) efetivamente encaminhada ao banco via Arch Master em 2026-10-06, sem prazo pós-vigência; cabeçalho "ADRs relacionadas" corrigido (sem exceção de operador de container aprovado); §4 corrigido para Opção B falhar apenas D2/D3; §4.2/C7 corrigidos sobre o control plane do OpenShift on-prem não ter provedor gerenciando; Opção C com pontuação de D6/D7; links vazios corrigidos (ver §11 para o detalhe completo). Etapa 3 (revisão por pares) nas cláusulas tocadas concluída em 2026-10-06. Nada aqui está aprovado. Esta ADR decide a **forma de compute** (VM vs. container) dentro de um ambiente já escolhido por [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework); ela não decide onde um workload roda. Coordenada com o Technical Architect no lado de padrão de workload/aplicação (campo "Colaboradores").

## 0. Cabeçalho

| Campo | Valor |
| --- | --- |
| **ID da ADR** | `ADR-0008` |
| **Status** | `proposta` |
| **Provisória** | Não |
| **Responsável** | Infrastructure Architect |
| **Colaboradores** | Technical Architect (coordenação de padrão de workload/aplicação; ver G4) |
| **Domínio** | Infrastructure |
| **Data da proposta** | 2026-10-05 |
| **Data da última mudança de status** | 2026-10-05 |
| **Data de vigência** | na aceitação |
| **Substitui** | Nenhuma |
| **Substituída por** | Nenhuma |
| **ADRs relacionadas** | [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework) (decide o ambiente; esta ADR decide a forma de compute dentro dele). [ADR-0014](/ARC/issues/ARC-7) (segmentação e zonas de confiança, Security Architect — certifica qual forma de compute um enclave CDE suporta; esta ADR não sobrepõe essa certificação). [ADR-0011](/ARC/issues/ARC-6) (guardrails de persistência, Technical Architect — a camada de dados segue a baseline desta ADR para `DATA-REL`/`DATA-NREL`, sem exceção de operador de container aprovado — nenhum existe hoje, C5/C8). |
| **Implementada por ARs** | Nenhuma ainda |
| **Idioma** | Português do Brasil (conforme `PARAM-LANG`) |
| **Data de submissão ao comitê** | não submetida |
| **Resultado do comitê** | `pendente` |

## 1. Contexto

**A situação.** O banco opera VMs e bare metal convivendo com containers (OpenShift on-premises e Kubernetes gerenciado nas três nuvens) como uma decisão já tomada antes deste Escritório, não revisitada aqui. O que falta é um critério comum de **quando** um novo workload vai para container e quando vai para VM/bare metal — hoje essa escolha depende do time que propõe o workload, não de um padrão.

**O gatilho.** [ARC-5](/ARC/issues/ARC-5) exige esta ADR como parte da primeira onda de infraestrutura, com coordenação explícita com o Technical Architect no lado de padrão de workload/aplicação (ex.: o que torna uma aplicação "cloud-native" o suficiente para container vs. o que exige o controle de um VM dedicado).

**Custo de não decidir.** Sem um critério comum: (a) workloads legados ou dependentes de licenciamento atado a host físico/virtual acabam forçados para container sem suporte de fornecedor, criando risco operacional; (b) workloads novos, stateless, que deveriam ir para container por padrão continuam sendo provisionados como VM por hábito, perdendo os benefícios de densidade, elasticidade e padronização de patch que o container oferece; (c) decisões de forma de compute para workloads dentro ou adjacentes ao CDE podem ser tomadas sem checar se o enclave de segmentação aprovado sustenta aquela forma de compute.

**Restrições que não podemos mudar.** OpenShift on-premises e Kubernetes gerenciado nas três nuvens já existem como plataformas contratadas; VMs e bare metal já existem como plataforma legada em produção. Esta ADR não decide se o banco continua operando as duas formas — decide o critério de quando usar cada uma para um novo workload.

## 2. Fatores de decisão

| ID | Fator | Tipo | ID de obrigação | Peso |
| --- | --- | --- | --- | --- |
| D1 | Statefulness e necessidade de armazenamento persistente de baixa latência diretamente atado ao host | Operabilidade | `N/A` | Forte |
| D2 | Restrição de licenciamento ou suporte de fornecedor atado a VM/bare metal nomeado | Risco de fornecedor | `N/A` | Obrigatório |
| D3 | Necessidade de customização em nível de kernel/SO que a plataforma de container não expõe | Operabilidade | `N/A` | Obrigatório |
| D4 | O workload está dentro ou adjacente ao CDE, e a forma de compute precisa estar certificada pela segmentação aprovada daquele enclave | Segurança / Regulatório | `OBL-PCI` | Obrigatório |
| D5 | Capacidade de escala horizontal sem estado compartilhado entre instâncias | Velocidade de entrega | `N/A` | Forte |
| D6 | Gravidade de plataforma existente — onde workloads vizinhos diretos já rodam | Operabilidade | `N/A` | Desejável |
| D7 | Competência da equipe de entrega para operar container vs. VM | Competências | `N/A` | Desejável |

## 3. Alternativas consideradas

### Opção A — Status quo: cada time escolhe livremente

- **O que é:** nenhum critério comum; cada time decide VM ou container por preferência ou hábito.
- **Como pontua:** D1 — neutro, depende do time. D2/D3/D4 — falha sistematicamente, porque nada garante que um workload com restrição de fornecedor ou dentro do CDE seja verificado antes da escolha. D5 — neutro. D6 — neutro, nenhuma disciplina de classificação exigida. D7 — pontua bem no curto prazo (cada time usa a competência que já tem), mas às custas de D2/D3/D4.
- **Custo e esforço:** nominalmente zero — é o estado atual, sem etapa de classificação nova. O custo real está escondido: retrabalho e incidentes de suporte quando um workload errado acaba em container (ou vice-versa), custo que a Opção C troca por uma etapa de classificação barata e recorrente.
- **Riscos:** risco desta opção especificamente — decisão de forma de compute sem verificação sistemática de restrição de fornecedor ou de certificação de CDE é o cenário exato em que um workload perde suporte contratual ou amplia o escopo PCI-DSS sem ninguém ter decidido isso deliberadamente.
- **Por que não foi escolhida:** não atende nenhum fator obrigatório de forma sistemática.

### Opção B — Container obrigatório para todo workload novo, sem exceção

- **O que é:** todo workload novo, sem exceção, vai para container.
- **Como pontua:** D1 — falha: workload com necessidade real de storage atado ao host não tem para onde ir. D2/D3 — falha: alguns workloads legados/COTS têm suporte de fornecedor restrito a VM/bare metal nomeado, e forçá-los para container quebra o suporte contratual. **D4 — não falha pela razão original.** O rascunho anterior temia que um enclave CDE aprovado não certificasse container; o veredito do Security Architect (§9, Q1; [ARC-30](/ARC/issues/ARC-30)) confirma que [ADR-0014](/ARC/issues/ARC-7) §4.1 já permite qualquer forma de compute (VM ou Kubernetes/OpenShift) dentro da Zona CDE, desde que as cláusulas de segmentação C1–C9 daquela ADR sejam satisfeitas — logo D4 por si não eliminaria a Opção B. D5 — pontua **pelo menos tão bem quanto** a Opção C, não pior: container obrigatório para todo workload novo não impõe atrito de etapa de classificação (que a Opção C exige, C1) e não restringe escala horizontal de forma alguma — a formulação anterior ("nenhuma folga de classificação, mas também nenhum atrito de etapa") descrevia D5 como neutro sem justificar uma pontuação pior que a da Opção C; corrigido para "pelo menos tão bem". D6 — pontua bem (menos dispersão de forma de compute que o status quo). D7 — pontua mal para times sem competência de container ainda desenvolvida.
- **Custo e esforço:** menor atrito de processo que a Opção C (sem etapa de classificação), mas custo de migração/quebra de suporte para workloads legados que dependem de VM/bare metal nomeado.
- **Riscos:** risco desta opção especificamente, que persiste mesmo após a reavaliação de D4 — nenhuma exceção declarada para restrição de fornecedor (D2/D3) significa forçar container sobre um workload cujo fornecedor não dá suporte, quebrando o contrato de suporte, independentemente de D4.
- **Por que não foi escolhida:** falha em D2/D3, obrigatórios — o banco não pode prometer algo que o fornecedor não sustenta, mesmo que D4 não seja mais um motivo de falha.

### Opção C — Container como padrão (SHOULD) para workload novo stateless/escalável, VM reservado a exceções declaradas (escolhida)

- **O que é:** todo workload novo stateless e horizontalmente escalável é desenhado, por padrão, para container (OpenShift on-premises ou Kubernetes gerenciado na nuvem escolhida por [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework)); VM/bare metal é usado quando uma exceção declarada (D2, D3 ou D4 negativo) se aplica.
- **Como pontua:** D1 — atende: workloads com necessidade real de storage atado ao host continuam em VM. D2/D3 — atende: a exceção é declarada e nomeada, não contornada. D4 — atende: a forma de compute dentro de um enclave CDE segue a certificação daquele enclave, não esta ADR isoladamente. D5 — atende: container é o padrão para o caso que mais se beneficia dele. D6 — atende: menos dispersão de forma de compute do que o status quo (Opção A), aproveitando a gravidade de plataforma já existente (OpenShift on-prem e Kubernetes gerenciado nas três nuvens já contratados). D7 — atende parcialmente: times sem competência de container ainda desenvolvida enfrentam uma curva de aprendizado, mitigada por C6 (workload existente em VM não é obrigado a migrar) e pelo fato de a exceção declarada (C3) continuar disponível para quem genuinamente precisa de VM.
- **Custo e esforço:** exige uma etapa de classificação explícita por workload (baixo custo, recorrente), e uma baseline de compute mantida para as duas formas.
- **Riscos:** se a exceção (D2/D3/D4) se tornar a regra de fato por hábito, a ADR perde força — mitigado por C1 (classificação explícita obrigatória).
- **Por que foi escolhida:** é a única opção que atende D2, D3 e D4 (obrigatórios) sem abandonar o benefício de padronização que a Opção B buscava.

### 3.1 Alternativa não avaliada no rascunho anterior: VM sobre o próprio OpenShift (OpenShift Virtualization)

Esta avaliação fica na seção de alternativas, não deslocada para depois de §4.2. A task exige uma baseline ancorada no estado real, e o estado tem OpenShift on-prem contratado — o que torna "rodar VM sobre o próprio OpenShift" uma alternativa real de plataforma e de custo de licenciamento, não uma curiosidade teórica.

- **O que é:** usar OpenShift Virtualization para hospedar cargas que hoje exigiriam VM/bare metal tradicional (ex.: por D2/D3), unificando o plano de operação sob um único cluster por site, em vez de manter hypervisor e OpenShift como duas plataformas separadas.
- **Avaliação:** reduziria a dispersão operacional de manter duas plataformas de compute (um dos custos nomeados em §5), e aproveitaria a licença de OpenShift já contratada. Mas não resolve D2 (restrição de suporte de fornecedor nomeada a um hypervisor específico) — um fornecedor que certifica apenas um hypervisor nomeado não passa a certificar OpenShift Virtualization porque o banco trocou de plataforma de virtualização; e workloads legados com dependência de kernel/SO não exposta (D3) têm o mesmo problema em qualquer plataforma de virtualização.
- **Como pontua:** D1 — neutro: a mesma consideração de storage de baixa latência atado ao host se aplica a VM tradicional e a KubeVirt. D2 — falha, pela razão acima (fornecedor não certifica a mudança de hypervisor). D3 — falha, mesma razão: kernel/SO não exposto permanece não exposto em qualquer plataforma de virtualização. D4 — neutro: não decidido por esta alternativa; a forma de compute dentro de um enclave CDE segue a certificação daquele enclave, como na Opção C. D5 — neutro: não é o caso de workload stateless/escalável que motiva esta alternativa. D6 — atende parcialmente: unifica o plano de operação sob OpenShift, reduzindo a dispersão de plataforma melhor do que manter hypervisor separado, mas introduz a pré-condição de migrar os workers para bare metal. D7 — pontua mal nesta primeira onda: exige competência nova de KubeVirt, e a migração de workers para bare metal não está dimensionada.
- **Decisão:** **rejeitada para esta primeira onda**, mas não descartada permanentemente. Motivo: o custo de migrar o hypervisor atual para OpenShift Virtualization não está dimensionado nesta ADR, e fazer essa migração ao mesmo tempo em que se fixam os demais padrões desta primeira onda introduziria risco de execução desnecessário. **Motivo adicional:** OpenShift Virtualization exige **workers bare metal** (KubeVirt depende de virtualização aninhada ou acesso direto a KVM, não suportado em workers que já são VMs) — **confirmado** que o cluster OpenShift on-prem atual roda sobre VM (resposta do banco via Arch Master, 2026-10-06, ver §9 Q5 e [ARC-96](/ARC/issues/ARC-96)), adotar OpenShift Virtualization exigiria primeiro migrar os próprios workers do OpenShift para bare metal, um segundo projeto de migração que esta ADR não dimensiona. Registrada como candidata a uma revisão futura desta ADR, quando houver dimensionamento de custo e de esforço de migração (incluindo essa pré-condição de workers bare metal).

### Opções consideradas e descartadas precocemente

| Opção | Descartada porque |
| --- | --- |
| VM como padrão universal, container como exceção | Inverteria o incentivo errado para um banco que já paga pelas plataformas de container nas quatro localidades e quer reduzir dispersão de forma de compute para workloads novos; não avaliada a fundo por contradizer o objetivo citado na task-mãe de ter uma baseline de compute por forma. |

## 4. Decisão

> **Vamos adotar a Opção C: container é o padrão para workload novo stateless e horizontalmente escalável; VM/bare metal é reservado a exceções declaradas por restrição de fornecedor, necessidade de customização de kernel, ou certificação de enclave que não suporte container.**

| # | Cláusula | Palavra-chave |
| --- | --- | --- |
| C1 | Toda proposta de workload novo declara explicitamente statefulness, escalabilidade horizontal, e restrições de fornecedor/kernel antes da escolha de forma de compute. | MUST |
| C2 | Workload novo, stateless e horizontalmente escalável, deve ser desenhado para container (OpenShift on-premises ou Kubernetes gerenciado na nuvem escolhida por [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework)). | SHOULD |
| C3 | Workload com restrição de suporte de fornecedor nomeada restrita a VM/bare metal, ou que exija customização de kernel/SO não exposta pela plataforma de container, deve ser posicionado em VM/bare metal. | MUST |
| C4 | A forma de compute de um workload dentro ou adjacente ao CDE segue a certificação do enclave de segmentação aprovado para aquele ambiente ([ADR-0014](/ARC/issues/ARC-7), Security Architect); esta ADR não decide essa certificação, apenas a respeita. | MUST |
| C5 | Motor de banco de dados relacional ou não relacional (`DATA-REL`/`DATA-NREL`) **autogerido pelo banco** usa a baseline de VM/bare metal, sempre — sem exceção de "operador de container aprovado", porque nenhum caminho de aprovação existe hoje (§9 Q2) e porque isso misturaria forma de compute com decisão de motor. **DBaaS gerenciado pelo provedor de nuvem está fora do escopo desta ADR** — é questão de posicionamento ([ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework)) e de motor ([ADR-0011](/ARC/issues/ARC-6#document-adr-0011-persistence-guardrails)), não de forma de compute; para o CDE, DBaaS continua barrado por [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework) C2 até existir enclave aprovado, independentemente desta cláusula. | MUST |
| C6 | Workload existente em VM não é obrigado a migrar para container sem um motivador de negócio nomeado. | MAY |
| C7 | Patch crítico de segurança, em qualquer forma de compute, é aplicado dentro de 30 dias da disponibilização, independentemente da cadência regular de patch (mensal para VM; para container, ciclo gerenciado do provedor **apenas para o control plane das três nuvens (EKS/AKS/OKE)** — o control plane do OpenShift on-prem não tem provedor gerenciando, é operado e patcheado pelo banco. Patch/imagem dos worker nodes de EKS/AKS/OKE, e todo o OpenShift on-prem (control plane e workers), é responsabilidade do banco, não do provedor, e segue o mesmo SLA de 30 dias para patch crítico, com rebuild de imagem de aplicação por CVE crítico incluído). | MUST |
| C8 | Todo workload **autogerido pelo banco** com papel de fonte de verdade para dados em memória ou em fila — mesmo quando não é um motor de banco de dados (ex.: fila de mensagens, cache usado como fonte de verdade, armazenamento de sessão que não pode ser recriado sem perda) — usa a mesma baseline de VM/bare metal por padrão que C5 fixa, sem a saída de "operador de container aprovado" (removida, mesma razão de C5). Serviço de fila/cache gerenciado pelo provedor de nuvem segue a mesma lógica de C5: fora do escopo desta ADR, é questão de posicionamento/motor. Fila ou cache meramente volátil, recriável sem perda de dado de negócio, não se qualifica e pode seguir o padrão geral de C2. | MUST |
| C9 | Proteção anti-malware é obrigatória no nível de host para VM/bare metal; para container, a proteção equivalente é escaneamento de imagem antes do deploy mais monitoramento de runtime no nó do cluster — as duas formas de compute não usam o mesmo controle, mas ambas precisam de um controle declarado, não de ausência de controle por "ser container". | MUST |

- **Por que esta opção:** o trade-off aceito é exigir uma etapa de classificação explícita (custo baixo, recorrente) em troca de eliminar a escolha de forma de compute por hábito em vez de critério, em particular para os casos em que uma escolha errada custa suporte de fornecedor ou certificação de segmentação.
- **Alternativas rejeitadas:** Opção A (status quo) rejeitada por não atender nenhum fator obrigatório; Opção B (container sem exceção) rejeitada por falhar D2/D3 — D4 não é mais motivo de falha para a Opção B, por força da resposta a Q1 (§3).

### 4.1 Aplicabilidade no estado

| Plataforma | Aplica-se? | Restrição, diferença ou exclusão |
| --- | --- | --- |
| `DC-AA` — DC on-prem, active-active | N/A | Esta ADR é sobre forma de compute, não sobre o site; aplica-se igualmente nos dois sites. |
| `OCP` — OpenShift on-prem | Sim | Destino padrão para workload stateless/escalável on-premises. |
| `VM-ON` — VMs / bare metal on-prem | Sim | Destino para exceções declaradas on-premises (C3) e para `DATA-REL`/`DATA-NREL` por padrão (C5). |
| `AWS-K8S` — Kubernetes gerenciado na AWS | Sim | Destino padrão para workload stateless/escalável posicionado na AWS por [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework). |
| `AWS-VM` — compute AWS | Sim | Destino para exceções declaradas na AWS. |
| `AZR-K8S` | Sim | Mesma observação, para Azure. |
| `AZR-VM` | Sim | Mesma observação, para Azure. |
| `OCI-K8S` | Sim | Mesma observação, para OCI. |
| `OCI-VM` | Sim | Mesma observação, para OCI. |
| `DATA-REL` — bases de dados relacionais | Sim | Baseline VM/bare metal por padrão (C5). |
| `DATA-NREL` — bases de dados não relacionais | Sim | Mesma observação. |
| `AGW-AWS` | N/A | A forma de compute do gateway gerenciado da AWS é definida pelo serviço do provedor, fora do escopo desta ADR. |
| `AGW-AZR` | N/A | Mesma observação, para o serviço gerenciado do Azure. |
| `AGW-AXW` | **Sim** | **O Axway é autogerido pelo banco** (VM ou container, conforme C3/C5), não um serviço gerenciado de provedor. Como forma de compute autogerida, o Axway segue a baseline geral desta ADR (C1–C3; C5/C8 se o papel for de fonte de verdade, o que não é o caso típico de um gateway stateless). |

### 4.2 Escopo de workloads

- **Aplica-se a:** todo workload novo a partir da data de vigência, e a toda mudança material de forma de compute de um workload existente.
- **Workloads existentes:** permanecem na forma de compute atual; esta ADR não exige migração retroativa (C6).
- **Explicitamente fora de escopo:** ferramentas de desenvolvimento local; apliances de fornecedor entregues apenas como VM/imagem fechada (a forma de compute já vem decidida pelo fornecedor).

### Baseline de compute

A baseline do rascunho anterior era fina demais para esse nome — cobria patch e imagem, mas não classes de tamanho, hypervisor, topologia de cluster, anti-afinidade nem hardening. A tabela abaixo substitui a anterior.

| Dimensão | Container (OpenShift / Kubernetes gerenciado) | VM / bare metal |
| --- | --- | --- |
| Classes de tamanho | Pools de node padronizados por classe de workload (ex.: geral, otimizado para memória, otimizado para I/O), definidos pelo Infrastructure Architect por site/nuvem | Catálogo de tamanhos padronizados (pequeno/médio/grande/customizado sob exceção) pelo Infrastructure Architect; tamanho customizado exige justificativa registrada |
| Hypervisor / plataforma on-prem | Cada site do `DC-AA` roda **um cluster OpenShift por site, não esticado** — decisão fixada em [ADR-0006 §1.1.1](/ARC/issues/ARC-5#document-adr-0006-topologia-active-active), porque etcd exige quorum de maioria e dois sites não formam um terceiro voto independente | **Baseline** (resposta do banco via Arch Master, 2026-10-06, [ARC-96](/ARC/issues/ARC-96)): hypervisor on-premises em produção é VMware vSphere/ESXi 9.1; os workers do cluster OpenShift on-premises rodam em VM sobre esse hypervisor, não em bare metal; contagem aproximada hoje em operação: **160 nós** e ~7.000 pods. **Leitura declarada (a resposta do banco não desambiguou):** "nós" é lido como a contagem de nós do cluster OpenShift (as VMs que atuam como worker/control-plane nodes), não a contagem de hosts físicos ESXi — a resposta cita "nós" ao lado de "pods", vocabulário de cluster Kubernetes/OpenShift, não de hypervisor. Se essa leitura estiver errada, o número de hosts ESXi físicos é um fato distinto, ainda não levantado. Fonte citada pelo respondente: relatório de inventário (nome do sistema/console não especificado na resposta). **Forma de compute do Axway:** container (OpenShift) no `DC-AA` — resposta do banco via Arch Master, 2026-10-07 ([ARC-141](/ARC/issues/ARC-141)). Fonte citada pelo respondente: relatório de inventário (sistema/console específico não nomeado na resposta) |
| Topologia de cluster por site | Um cluster OpenShift por site; workloads não migram automaticamente entre clusters num failover de site — reconciliação via GitOps/configuração declarativa replicada, não control plane compartilhado ([ADR-0006](/ARC/issues/ARC-5#document-adr-0006-topologia-active-active) §1.1.1) | VMs de um mesmo workload distribuídas entre hosts do mesmo site; failover entre sites segue sempre o failover controlado de domínio de dado ([ADR-0006](/ARC/issues/ARC-5#document-adr-0006-topologia-active-active)), nunca migração ao vivo cross-site |
| Anti-afinidade por domínio de falha | Réplicas de um mesmo workload não podem compartilhar o mesmo rack/pod (domínio de falha de [ADR-0006 §1.1](/ARC/issues/ARC-5#document-adr-0006-topologia-active-active)); regra de anti-afinidade obrigatória no agendador para workload Tier 0/1 ([ADR-0009](/ARC/issues/ARC-5#document-adr-0009-tiering-de-dr)) | Mesma regra, aplicada via regra de anti-afinidade do hypervisor entre hosts físicos de racks/pods distintos |
| Hardening de imagem | Imagem base com padrão de hardening do banco (CIS Benchmark ou equivalente adotado), escaneada antes do deploy; responsabilidade do time de entrega sobre a camada de aplicação | Imagem de SO com o mesmo padrão de hardening, mantida e escaneada pelo catálogo do Infrastructure Architect |
| SLA de patch | O control plane gerenciado (API server, etcd) segue o ciclo do provedor **apenas nas três nuvens (EKS/AKS/OKE)** — no OpenShift on-prem **não existe provedor gerenciando o control plane**; o banco opera e patcheia o próprio control plane (API server, etcd), com o mesmo SLA de 30 dias para patch crítico que se aplica a qualquer outra forma de compute autogerida. **Os workers/nodes, nas três nuvens, também não são patcheados pelo provedor** — a atualização de versão/imagem de SO dos node groups/worker pools em EKS, AKS e OKE é responsabilidade do cliente (o banco), da mesma forma que o patch de imagem de aplicação; dono do SLA de 30 dias para patch crítico de SO dos workers e do rebuild de imagem de aplicação por CVE crítico é o Infrastructure Architect (workers e control plane on-prem) com os times de entrega (imagem de aplicação) | Patch de SO em cadência mensal; patch crítico de segurança aplicado dentro de 30 dias, independentemente do ciclo mensal — a cadência mensal não substitui o SLA de crítico |

## 5. Consequências

### Positivas

- Dá um critério auditável para parar a dispersão de forma de compute decidida por hábito.
- Protege workloads com restrição real de fornecedor ou kernel de serem forçados para container sem suporte.
- Alinha a forma de compute de workloads CDE à certificação de segmentação real, em vez de assumir que container é sempre seguro para esse escopo.

### Negativas, e o que fica mais difícil

- Toda proposta de workload novo passa por uma etapa de classificação adicional — atrito real, de baixo custo.
- Times que preferem VM por familiaridade perdem a opção de escolher livremente quando o workload se qualifica como stateless/escalável — perda real de autonomia local, aceita deliberadamente (ver Opção C, §3).
- Manter duas baselines de compute (container e VM) com padrões próprios de patch, imagem e HA é overhead operacional permanente — não desaparece com esta ADR, é nomeado, não resolvido.

### O que esta decisão impede no futuro

Impede que um workload CDE seja movido para container sem checar a certificação do enclave — reverter um posicionamento desse tipo, se feito incorretamente, custa uma reavaliação de escopo PCI-DSS. Também torna mais difícil, no futuro, justificar ao comitê a escolha de VM para um workload novo claramente stateless/escalável sem nomear qual exceção de C3/C4 se aplica.

### Impacto de migração no estado existente

| O que existe hoje | O que precisa mudar | Responsável | Até quando |
| --- | --- | --- | --- |
| Workloads existentes não classificados contra D1–D7 | Não exigido retroativamente (C6); recomendado junto com o inventário de workloads **Tier 0/1** da escala formal de [ADR-0009 §4](/ARC/issues/ARC-5#document-adr-0009-tiering-de-dr) — não a nomenclatura informal "Tier 1" de [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework), cuja correção de colisão de nomenclatura é responsabilidade do Enterprise Architect naquela ADR, não desta | Infrastructure Architect, com apoio do Technical Architect | 180 dias após a data de vigência, alinhado ao prazo já fixado em ADR-0002 |

## 6. Impacto de conformidade e regulatório

| Regime | ID(s) de obrigação aplicável(is) | O que a obrigação exige, em substância | Como esta decisão a endereça | Risco residual | Controle / evidência de que se mantém |
| --- | --- | --- | --- | --- | --- |
| BACEN/CMN | `OBL-CYB` | Classificação e proteção de dados; gestão de risco de terceiros (fornecedor de licença/suporte) | C3 garante que a forma de compute respeita restrição de suporte de fornecedor, evitando operar sem suporte contratual válido | Nenhum residual específico desta ADR | Inventário de exceções C3 mantido pelo Infrastructure Architect |
| LGPD | `OBL-LGPD` | Segurança do tratamento | Esta ADR não decide qual dado é pessoal; a forma de compute em si não altera o tratamento, mas container mal configurado pode expandir superfície de acesso — mitigado pela baseline (§4.2) | **N/A nesta ADR — motivo (G2):** critério de forma de compute não decide categoria de dado pessoal nem workload específico; controle de acesso é tratado pelas ADRs de segurança | ADRs de segurança ([ARC-7](/ARC/issues/ARC-7)) tratam controle de acesso |
| PCI-DSS | `OBL-PCI` | Esta é uma ADR de compute, não de rede — segmentação e controle de acesso são secundários aqui e pertencem a [ADR-0014](/ARC/issues/ARC-7). O que esta ADR realmente governa é: padrões de configuração segura por forma de compute; prazo de patch crítico (até 30 dias; C7 confirma que a cadência mensal/gerenciada não substitui esse prazo para patch crítico, inclusive para workers de Kubernetes gerenciado); anti-malware por forma de compute; e logging por forma de compute — **números de requisito movidos para a linha "Família de requisitos PCI-DSS afetada" do bloco de escopo PCI abaixo, por G7** | A baseline de compute revisada (§4.2) fixa hardening de imagem e SLA de patch crítico (C7); C9 fixa controle anti-malware equivalente por forma de compute; logging por forma de compute não tem cláusula própria nesta ADR — é tratado pelas ADRs de segurança/observabilidade ([ARC-7](/ARC/issues/ARC-7)), e esta ADR aponta a lacuna de coordenação, não a resolve. C4 ainda amarra a forma de compute de workload CDE à certificação de [ADR-0014](/ARC/issues/ARC-7) | Logging por forma de compute não tem dono nomeado nesta ADR — lacuna a fechar com o Security Architect; certificação de [ADR-0014](/ARC/issues/ARC-7) para C4 ainda não publicada | C7/C9 como cláusulas `MUST`; revisão obrigatória do Security Architect (§10) para C4 e para a lacuna de logging |

Se a linha de PCI-DSS não for `N/A`, responda também:

| Pergunta de escopo PCI-DSS | Resposta |
| --- | --- |
| Esta decisão armazena, transmite ou processa dados de portador de cartão (cardholder data)? | Não diretamente — é um critério de forma de compute, não um workload específico. |
| Esta decisão introduz ou altera um sistema conectado ao ambiente de dados de cartão (CDE)? | Não introduz; governa qual forma de compute um sistema CDE futuro pode usar, condicionado à certificação de [ADR-0014](/ARC/issues/ARC-7). |
| Efeito sobre o escopo do PCI-DSS (amplia / reduz / não altera) | Não altera por si só; reduz o risco de um workload CDE usar uma forma de compute não certificada, ao amarrar a escolha à certificação do enclave (C4). |
| Família de requisitos PCI-DSS afetada | Padrões de configuração segura (Requisito 2); prazo de patch crítico (Requisito 6.3.3); anti-malware por forma de compute (Requisito 5); logging por forma de compute (Requisito 10, lacuna de coordenação); segmentação e controle de acesso (Requisitos 1 e 7), secundariamente, na medida em que a forma de compute escolhida precisa estar dentro do enclave certificado por [ADR-0014](/ARC/issues/ARC-7). |

| Pergunta | Resposta |
| --- | --- |
| Esta decisão toca dados pessoais? | **Não** — critério de forma de compute não decide qual dado é pessoal; isso é workload a workload. |
| Ela envolve processamento ou armazenamento fora do Brasil? | **Não** — é independente de região, segue o ambiente escolhido por [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework). |
| Ela muda um arranjo relevante de processamento de dados, armazenamento ou serviço em nuvem? | **Não**. |
| Ela muda a postura de resiliência ou recuperação de um serviço crítico? | **Sim** — a forma de compute afeta como o tiering de DR ([ADR-0009](/ARC/issues/ARC-5#document-adr-0009-tiering-de-dr)) testa failover (container tende a ter RTO menor que VM, mas não é assumido aqui sem teste). |
| Ela muda a detecção de incidentes, logging ou rastreabilidade? | **Não**. |
| Ela cria ou muda uma dependência de terceiro / fornecedor? | **Não** — C3 nomeia explicitamente onde essa dependência já existe (fornecedor de licença/suporte atado a VM/bare metal) e a respeita, em vez de criá-la. |

> O Escritório mapeia obrigações para controles de arquitetura em nível geral. Ele não emite parecer jurídico, e a suficiência jurídica é confirmada pelas funções de compliance e jurídico do banco.

## 7. Conformidade e verificação

| Cláusula | Como a conformidade é verificada | Onde a evidência vive | Automatizável? |
| --- | --- | --- | --- |
| C1 | Revisão da proposta do workload confirma que D1–D4 foram declarados | Documento de intake do workload | Parcialmente |
| C2 | Revisão por amostragem confirma que workload stateless/escalável novo foi desenhado para container, ou há exceção nomeada | Documentação de arquitetura do workload | Não — é `SHOULD`, avaliado por amostragem |
| C3 | Para todo workload em VM/bare metal, confirmar que uma exceção C3 está registrada | Inventário de exceções, Infrastructure Architect | Parcialmente |
| C4 | Para todo workload CDE, confirmar que a forma de compute está na lista certificada por [ADR-0014](/ARC/issues/ARC-7) | Registro de certificação de enclave, Security Architect | Parcialmente |
| C5 | Para todo motor `DATA-REL`/`DATA-NREL` autogerido, confirmar VM/bare metal (sem exceção); para motor gerenciado pelo provedor (DBaaS), confirmar que está fora do escopo desta cláusula e tratado em [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework)/[ADR-0011](/ARC/issues/ARC-6) | Inventário de motores de banco de dados | Parcialmente |
| C6 | Não verificável como não conformidade — é uma permissão (`MAY`) | — | Não aplicável |
| C7 | Relatório de patch confirma aplicação de patch crítico dentro de 30 dias, para as duas formas de compute | Sistema de gestão de patch, Infrastructure Architect | Sim |
| C8 | Inventário de filas/caches/sessões autogeridas com papel de fonte de verdade confirma baseline VM/bare metal, sem exceção | Inventário de sistemas de registro ([ADR-0006](/ARC/issues/ARC-5#document-adr-0006-topologia-active-active) §1.1), estendido a filas/caches | Parcialmente |
| C9 | Confirmação de controle anti-malware (host para VM; escaneamento de imagem + runtime para container) registrado por workload | Documentação de segurança do workload, Security Architect | Parcialmente |

## 8. Exceções

- **Waivers possíveis contra esta ADR:** Sim.
- **Nível por cláusula:**
  - C1 — `W1` (processual).
  - C2 — não exige nível de waiver (é `SHOULD`).
  - C3 — **`W3` (teste de presença, A23): citada explicitamente na coluna de controle/evidência da linha `OBL-CYB` em §6 ["C3 garante que a forma de compute respeita restrição de suporte de fornecedor..."]; por presença nessa coluna, o waiver passa a ser do comitê, não W2.**
  - C4 — `W3` (implementa `OBL-PCI`; qualquer desvio arrisca o escopo do CDE).
  - C5 — `W2`.
  - C6 — não exige nível de waiver (é `MAY`).
  - C7 — `W3` (implementa `OBL-PCI`; atraso em patch crítico é exatamente o tipo de risco que o requisito existe para eliminar).
  - C8 — `W2` (mesma lógica de risco de C5, estendida a filas/caches/sessões com papel de fonte de verdade).
  - C9 — `W3` (implementa `OBL-PCI`).
- **Cláusulas que não podem receber waiver:** nenhuma — todas as `MUST`/`MUST NOT` têm nível atribuído.

## 9. Questões abertas

| # | Questão | Quem deve responder | Necessário até |
| --- | --- | --- | --- |
| Q1 | **Resolvida pelo veredito do Security Architect (2026-10-05, [ARC-30](/ARC/issues/ARC-30)):** a certificação de C4 é satisfeita pela aplicabilidade de [ADR-0014 §4.1](/ARC/issues/ARC-7#document-adr-0014-segmentacao-rede-zonas-confianca-cde) — que já permite qualquer forma de compute (VM, Kubernetes/OpenShift) dentro da Zona CDE, desde que as cláusulas C1–C9 de segmentação sejam satisfeitas — combinada com o levantamento de sistemas CDE (mesmo prazo de 45 dias de [ADR-0014](/ARC/issues/ARC-7) §5). Não é necessário um novo artefato de certificação separado | Security Architect | Resolvida — depende do levantamento de 45 dias de [ADR-0014](/ARC/issues/ARC-7) §5 |
| Q2 | **Corrigida (D-17(f)) — não mais tratada como dependente de operador aprovado.** C5/C8 não preveem mais exceção de "operador de container aprovado": motor/workload autogerido usa VM/bare metal sempre, sem condicional. DBaaS gerenciado está fora do escopo desta ADR. Não há mais questão em aberto sobre um operador a ser aprovado — essa trilha foi removida, não resolvida | Technical Architect | Encerrada — sem prazo, a saída foi removida do texto |
| Q3 | **Resolvida (decisão D-5 do Arquiteto Principal, trabalho de mesa); referência corrigida para nomear o artefato, não apenas a task.** O Security Architect assume a lacuna de logging por forma de compute, nomeada em §6; ela será fechada em [ADR-0015](/ARC/issues/ARC-7#document-adr-0015-logging-trilha-auditoria) (Logging e trilha de auditoria), sob [ARC-13](/ARC/issues/ARC-13), não nesta ADR | Security Architect | Resolvida quanto a dono e destino — prazo de publicação de [ADR-0015](/ARC/issues/ARC-7#document-adr-0015-logging-trilha-auditoria) é do Security Architect sob [ARC-13](/ARC/issues/ARC-13) |
| Q4 | Dimensionamento de custo e esforço de migração para OpenShift Virtualization (§3.1) ainda não existe — quando essa alternativa deve ser reavaliada formalmente? | Infrastructure Architect | Sem prazo fixado nesta primeira onda; candidata a revisão futura |
| Q5 | **Resolvida.** Nome e versão do hypervisor on-premises, se os workers do OpenShift on-prem rodam em VM ou bare metal, e a contagem aproximada de VMs/containers: respondido pelo banco via Arch Master em 2026-10-06 ([ARC-96](/ARC/issues/ARC-96)) — VMware vSphere/ESXi 9.1; workers em VM sobre esse hypervisor (não bare metal, o que mantém de pé o motivo adicional de rejeição de §3.1); 160 nós, ~7.000 pods. Fonte citada: relatório de inventário (sistema/console específico não nomeado pelo respondente) | Infrastructure Architect, via Arch Master | Resolvida — baseline incorporada em §4.2 e §3.1 |

| Q6 | **Resolvida.** Qual é a forma de compute atual do Axway no `DC-AA` — VM ou container? Respondido pelo banco via Arch Master em 2026-10-07 ([ARC-141](/ARC/issues/ARC-141)): **container (OpenShift)**. Fonte citada pelo respondente: relatório de inventário (sistema/console específico não nomeado na resposta) | Infrastructure Architect, via Arch Master | Resolvida — baseline incorporada em §4.2 |

Esta ADR é submetida com estas questões abertas; nenhuma delas está escondida.

## 10. Revisão por pares e discordâncias

**Nota de vocabulário (D-17(A)/G11/G16):** os vereditos abaixo foram transcritos mecanicamente para o vocabulário fixo (concorda / concorda com comentário / discorda / sem resposta); o texto original de cada veredito permanece, literal e integralmente, na coluna de comentário. **Correção de G4 (ARC-68):** a coordenação do Technical Architect saiu do campo "Responsável" do cabeçalho e passou a constar em "Colaboradores".

| Revisor | Papel | Veredito | Comentário / discordância |
| --- | --- | --- | --- |
| Technical Architect | obrigatório — coordenação de padrão de workload/aplicação (campo "Colaboradores" do cabeçalho) | **concorda com comentário** | Veredito original: "Aprovado, com resposta explícita à Q2". Revisão concluída em 2026-10-05 ([ARC-29](/ARC/issues/ARC-29)). C5/C8 fixavam baseline VM/bare metal por padrão para `DATA-REL`/`DATA-NREL` e para workload com papel de fonte de verdade em memória/fila, salvo operador de container aprovado sob [ADR-0011](/ARC/issues/ARC-6#document-adr-0011-persistence-guardrails) — **essa saída foi removida (D-17(f)); o veredito original é preservado literalmente, mas não descreve mais o texto vigente de C5/C8**. §3.1 (OpenShift Virtualization rejeitada nesta onda) e C9 (anti-malware por forma de compute) não têm efeito sobre os guardrails de persistência. Ver §9, Q2. **Confirmação datada em 2026-10-06 (ARC-68), concluída em 2026-10-06 ([ARC-99](/ARC/issues/ARC-99)): concorda.** Confirma a remoção da saída "operador aprovado sob ADR-0011" em C5/C8 — [ADR-0011](/ARC/issues/ARC-6#document-adr-0011-persistence-guardrails) (guardrails de persistência, de responsabilidade do Technical Architect) nunca definiu nem aprovou um caminho de operador de container aprovado para motor de banco de dados ou workload com papel de fonte de verdade em memória/fila; a saída condicional anterior referenciava algo que nunca foi criado. A redação corrigida (motor/workload autogerido usa sempre VM/bare metal; DBaaS gerenciado pelo provedor fica fora do escopo desta ADR) está coerente com o texto vigente de ADR-0011 e não deixa pendência aberta do lado do Technical Architect. |
| Enterprise Architect | obrigatório — verifica não conflito com ADR aceita | **concorda com comentário** | Veredito original: "Aprovado com ressalvas". Revisão concluída em 2026-10-05 ([ARC-28](/ARC/issues/ARC-28)). Respeita corretamente a divisão de escopo com ADR-0002 (decide forma de compute dentro do ambiente já escolhido, não o ambiente) e referencia corretamente [ADR-0006 §1.1.1](/ARC/issues/ARC-5#document-adr-0006-topologia-active-active). Ressalva (Achado 1): tabela de migração de §5 cita "Tier 1" de ADR-0002, nomenclatura realinhada para Tier 0 nesta revisão — correção definitiva de ADR-0002 é responsabilidade do Enterprise Architect. |
| Security Architect | obrigatório — a cláusula C4 depende de [ADR-0014](/ARC/issues/ARC-7); família de requisitos revisada em §6 | **concorda** | Veredito original: "Aprovado". Revisão concluída em 2026-10-05 ([ARC-30](/ARC/issues/ARC-30)). A correção de família em §6 está correta. C4 amarra a forma de compute de workload CDE a [ADR-0014](/ARC/issues/ARC-7) sem contradição — a tabela §4.1 de ADR-0014 já permite qualquer forma de compute dentro da Zona CDE, satisfazendo C1–C9. Sem ressalva bloqueante. Ver §9, Q1 resolvida. **Confirmação datada em 2026-10-06 (ARC-68), concluída em 2026-10-06 ([ARC-97](/ARC/issues/ARC-97)): concorda.** A correção de `AGW-AXW` de "serviço gerenciado"/N/A para autogerido pelo banco está de acordo com a realidade operacional (o Axway roda on-premises no `DC-AA`, sob a baseline geral da ADR). A atribuição de C7 (patch de workers de EKS/AKS/OKE, e todo o OpenShift on-prem incluindo workers, como responsabilidade do banco, com o mesmo SLA de 30 dias para patch crítico) é a leitura correta — apenas o control plane das três nuvens é ciclo gerenciado do provedor; o control plane do OpenShift on-prem e os workers em qualquer plataforma não têm outro responsável possível além do banco. |
| Cloud Architect | obrigatório — linhas `AWS-*`/`AZR-*`/`OCI-*` marcadas `Sim` em §4.1 | **concorda** | Veredito original: "Concorda, sem ressalvas". Revisão concluída em 2026-10-05 ([ARC-31](/ARC/issues/ARC-31)). Linhas `AWS-K8S`/`AWS-VM`, `AZR-K8S`/`AZR-VM`, `OCI-K8S`/`OCI-VM` marcadas `Sim` em §4.1 — Kubernetes gerenciado (EKS, AKS, OKE) é o destino padrão correto para workload stateless/escalável, VM/compute é o destino correto de exceção, espelhando exatamente a baseline on-premises que a ADR já fixa. **Confirmação datada em 2026-10-06 (ARC-68), concluída em 2026-10-06 ([ARC-98](/ARC/issues/ARC-98)): concorda.** Confirma que o patch/atualização de SO dos node groups/worker pools em EKS, AKS e OKE é responsabilidade do banco (cliente), não do provedor — consistente com o modelo de responsabilidade compartilhada das três nuvens, em que o provedor gerencia apenas o control plane das ofertas gerenciadas. SLA de 30 dias para patch crítico e cadência mensal de patch de SO estão corretos. |

A discordância é registrada literalmente e segue ao comitê sem edição. As quatro revisões por pares obrigatórias originais desta ADR foram concluídas em 2026-10-05 ([ARC-28](/ARC/issues/ARC-28), [ARC-29](/ARC/issues/ARC-29), [ARC-30](/ARC/issues/ARC-30), [ARC-31](/ARC/issues/ARC-31)). Os três pedidos de confirmação adicionais, datados em 2026-10-06, sobre as cláusulas de mérito tocadas nesta revisão (C5/C7/C8, `AGW-AXW`) chegaram antes do prazo, todos em 2026-10-06 ([ARC-97](/ARC/issues/ARC-97) Security Architect, [ARC-98](/ARC/issues/ARC-98) Cloud Architect, [ARC-99](/ARC/issues/ARC-99) Technical Architect) — todas com concorda, nenhum discorda, nenhum bloqueante.

## 11. Histórico de status

| Data | De → Para | Quem | Por quê |
| --- | --- | --- | --- |
| 2026-10-07 | `proposta` → `proposta` (revisão) | Infrastructure Architect | Q6 resolvida: forma de compute atual do Axway no `DC-AA` respondida pelo banco via Arch Master ([ARC-141](/ARC/issues/ARC-141)) — **container (OpenShift)**, fonte citada pelo respondente: relatório de inventário (sistema/console específico não nomeado na resposta). §4.2 (linha "Hypervisor / plataforma on-prem") atualizada de "pendente" para o fato incorporado. Nenhuma cláusula C1–C9 muda de mérito. |
| 2026-10-06 | `proposta` → `proposta` (revisão) | Infrastructure Architect | Vereditos de revisão por pares das cláusulas tocadas por ARC-68 (C5/C7/C8, `AGW-AXW`) incorporados literalmente em §10 — Technical Architect, Security Architect e Cloud Architect, todos concorda, nenhum discorda, nenhum bloqueante ([ARC-97](/ARC/issues/ARC-97)/[ARC-98](/ARC/issues/ARC-98)/[ARC-99](/ARC/issues/ARC-99)). Pronta para resubmissão ao Arquiteto Principal. |
| 2026-10-05 | `—` → `proposta` | Infrastructure Architect | Rascunho iniciado. |
| 2026-10-05 | `proposta` → `proposta` (revisão) | Infrastructure Architect | Alterações solicitadas pelo Arquiteto Principal incorporadas: baseline de compute detalhada (classes de tamanho, hypervisor, topologia de cluster por site, anti-afinidade, hardening, SLA de patch), avaliação da alternativa OpenShift Virtualization (§3.1, rejeitada nesta onda com motivo), regra para workloads stateful não-banco (C8), controle anti-malware por forma de compute (C9), família PCI-DSS corrigida (Req. 2, 5, 6.3.3, 10, em vez de 1/7 como principais), e solicitação formal de revisão por pares a Technical, Enterprise, Security e Cloud Architect. |
| 2026-10-05 | `proposta` → `proposta` (revisão) | Infrastructure Architect | Vereditos de revisão por pares incorporados literalmente em §10 (Technical, Enterprise, Security e Cloud Architect, todos aprovando ou concordando, com uma ressalva de nomenclatura não bloqueante); §9 Q1–Q2 resolvidas com as respostas do Security e do Technical Architect; §5 corrigida para desambiguar "Tier 1"/Tier 0; registro de decisões confirmado em [decision-registry §5](/ARC/issues/ARC-2#document-decision-registry) via [ARC-27](/ARC/issues/ARC-27). Pronta para resubmissão ao Arquiteto Principal. |
| 2026-10-05 | `proposta` → `proposta` (revisão) | Infrastructure Architect | 2ª rodada de alterações solicitadas pelo Arquiteto Principal incorporadas: palavra-chave de C5 corrigida de `SHOULD` para `MUST` (coerência com C8); hypervisor on-premises nomeado como gap explícito em vez de baseline presumida (§4.2, nova Q5); §3.1 (OpenShift Virtualization) reposicionada dentro da §3 (Alternativas consideradas); Q3 (logging por forma de compute) resolvida nomeando Security Architect e [ARC-13](/ARC/issues/ARC-13) como destino. |
| 2026-10-06 | `proposta` → `proposta` (revisão) | Infrastructure Architect | **Retrabalho de [ARC-68](/ARC/issues/ARC-68), devolvida no gate [ARC-10](/ARC/issues/ARC-10), classe S:** Q5 corrigida para consulta de 5 dias úteis (hypervisor e contagem aproximada de VM/container), não medição de 30 dias. C5/C8 corrigidas por D-17(f): aplicam-se apenas a motor/workload autogerido; DBaaS gerenciado pelo provedor fica fora do escopo desta ADR (é [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework)/[ADR-0011](/ARC/issues/ARC-6)); saída "operador aprovado sob ADR-0011" removida de C5/C8/§7/Q2. Axway corrigido de "serviço gerenciado" para autogerido, `AGW-AXW` de `N/A` para `Sim` em §4.1. C7 corrigido: patch/imagem de workers de EKS/AKS/OKE e rebuild de imagem de aplicação por CVE crítico são do banco, não do ciclo gerenciado do provedor (só o control plane é gerenciado). OpenShift Virtualization (§3) ganha motivo adicional de rejeição: exige workers bare metal. Opções A e B (§3) completas com custo, risco e pontuação de todos os fatores D1–D7; D4 da Opção B reavaliado à luz da resposta a Q1 (ADR-0014 §4.1 já permite qualquer forma de compute na Zona CDE). Itens de prontidão: G4 (coordenação movida para "Colaboradores"), G7 (números de requisito PCI consolidados na "Família"), G11/G16 e vocabulário de pares (concorda/concorda com comentário), G13 (Q3 nomeia ADR-0015 explicitamente), A6/A7 (custo/risco de A e B), A10 (C5 mantida `MUST` puro), A19 (consistência de referências cruzadas revisada), A23 (C3 corrigida para `W3` por presença na coluna de controle de `OBL-CYB`), §4.1 desdobrada em 14 linhas, G2 (motivo de N/A LGPD). Pedidos de confirmação datados (2026-10-06, prazo até 2026-10-20) ao Technical, Security e Cloud Architect sobre as cláusulas tocadas, registrados em §10. |
| 2026-10-06 | `proposta` → `proposta` (revisão) | Infrastructure Architect | **Gate do Arquiteto Principal sobre o retrabalho de ARC-68 — "Alterações solicitadas" (2026-10-06), B4.** Q5 corrigida: o item 1 da devolução anterior exigia encaminhar a pergunta do hypervisor ao banco, não apenas adiar um fato que é consulta — o texto anterior adiava por "5 dias úteis após a vigência", mas a vigência é a aceitação pelo comitê, que então leria uma baseline de VM sem conhecer o hypervisor do banco. A pergunta (produto/versão do hypervisor, se os workers do OpenShift on-prem rodam em VM ou bare metal, contagem aproximada de VMs/containers) foi encaminhada ao banco via Arch Master em 2026-10-06 (ver [ARC-96](/ARC/issues/ARC-96)); até a resposta chegar com fonte citada, Q5 permanece "pergunta ao banco, datada 2026-10-06, sem resposta", nunca com prazo pós-vigência. Confirmado: nenhum valor numérico de hypervisor/contagem de VM/container sem fonte estava presente no texto salvo desta ADR — item já correto, sem alteração necessária. Não-bloqueantes: cabeçalho "ADRs relacionadas" corrigido (removida a saída "salvo operador de container aprovado", que contradizia C5/C8); §4 "Alternativas rejeitadas" corrigido para Opção B falhar apenas D2/D3 (D4 já reavaliado favoravelmente em §3); Opção B D5 corrigido de "pontua pior que a Opção C" para "pontua pelo menos tão bem", com justificativa; §4.2 "SLA de patch" e C7 corrigidos — não existe provedor gerenciando o control plane do OpenShift on-prem, o banco opera e patcheia esse control plane, com o mesmo SLA de 30 dias; Opção C (§3) com pontuação de D6/D7 adicionada, que faltava. Transversal: os 7 links vazios `(#)` para ADR-0002/0006/0009 corrigidos para as âncoras corretas. Classe **S** — volta pela Etapa 3 nas cláusulas tocadas (C5, C7, C8, Q5, §4.1 `AGW-AXW`), pendente até 2026-10-20; [ARC-68](/ARC/issues/ARC-68) permanece `in_progress`. |
| 2026-10-06 | `proposta` → `proposta` (revisão) | Infrastructure Architect | **Gate do Arquiteto Principal (ARC-76), 2ª rodada — correções de forma, sem mudança de mérito.** A19: bloco das seis perguntas gerais de conformidade (§6) corrigido para respostas estritas `Sim`/`Não`, em vez de "indiretamente"/"não diretamente". §4.1: marcas "nesta revisão (ARC-68)" removidas das linhas `AZR-K8S` e `AGW-AWS`. §5 (impacto de migração): referência à nomenclatura informal "Tier 1" de [ADR-0002](/ARC/issues/ARC-3#document-adr-0002-workload-placement-decision-framework) substituída pela referência direta à escala Tier 0/1 de [ADR-0009 §4](/ARC/issues/ARC-5#document-adr-0009-tiering-de-dr). §11 (linha anterior): "pendente de revisão por pares pelo Arquiteto Principal" corrigido para "pendente do gate interno do Arquiteto Principal" — o gate do Escritório (Etapa 5) não é revisão por pares (Etapa 3, já concluída em §10). §4.2: declarada a leitura de que "160 nós" se refere à contagem de nós do cluster OpenShift (vocabulário de cluster, ao lado de "pods"), não à contagem de hosts físicos ESXi, que permanece não levantada se essa leitura estiver errada. Nova Q6: forma de compute atual do Axway (VM ou container), não coberta pelo inventário de [ARC-96](/ARC/issues/ARC-96), encaminhada ao banco via Arch Master pelo mesmo caminho de courier de Q5. Nenhuma cláusula C1–C9 muda de mérito; classe **F** mantida. |
| 2026-10-06 | `proposta` → `proposta` (revisão) | Arch Master | **Resposta do banco recebida e incorporada ([ARC-96](/ARC/issues/ARC-96)).** Q5 resolvida: hypervisor on-premises em produção é VMware vSphere/ESXi 9.1; os workers do cluster OpenShift on-premises rodam em VM sobre esse hypervisor, não em bare metal; contagem aproximada hoje: 160 nós, ~7.000 pods. Fonte citada pelo respondente: relatório de inventário (sistema/console específico não nomeado). Baseline incorporada em §4.2 (linha "Hypervisor / plataforma on-prem"), que deixa de ser placeholder; §3.1 atualizada para tratar a premissa de workers em VM como confirmada, não mais condicional, mantendo de pé o motivo adicional de rejeição do OpenShift Virtualization nesta onda. Ainda pendente do gate interno do Arquiteto Principal (Etapa 5) antes de seguir ao comitê — a revisão por pares das cláusulas tocadas (Etapa 3) já está concluída em §10; o gate interno do Escritório não é revisão por pares. |

| 2026-10-07 | `proposta` → `proposta` (revisão) | Infrastructure Architect | **Retrabalho de prontidão, achado de [ARC-138](/ARC/issues/ARC-138) (veredito consolidado de 2026-10-07 06:29): REPROVADO, item A6 — [ARC-172](/ARC/issues/ARC-172).** §3.1 (VM sobre o próprio OpenShift/OpenShift Virtualization) ganha o bloco "Como pontua", endereçando D1–D7 por ID, como já ocorria nas Opções A, B e C — a alternativa só trazia prosa de avaliação, sem pontuação por fator. Nenhuma cláusula `MUST`/`MUST NOT` muda de mérito; a decisão de rejeitar esta alternativa nesta primeira onda não muda. |
