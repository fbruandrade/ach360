# Contrato de insumos para AR-001 e AR-002

**Tipo de artefato:** Nota de trabalho do Solution Architect — **não é uma AR e não é uma ADR**
**Responsável:** Solution Architect
**Status:** Nota de trabalho. Nada aqui está aprovado, e nada aqui vincula nenhum time de entrega.
**Versão:** 0.2
**Data:** 2026-10-04
**Relacionados:** [Template de AR](/ARC/issues/ARC-2#document-ar-template) · [Registro de decisões](/ARC/issues/ARC-2#document-decision-registry) · [Checklist de prontidão](/ARC/issues/ARC-2#document-readiness-checklist) · [Modelo operacional](/ARC/issues/ARC-2#document-operating-model) · [Decisão das lacunas G1–G4](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4)

---

## 1. O que este documento é — e o que ele deliberadamente não é

Este documento **não decide nada**. Ele declara, seção por seção do [template de AR](/ARC/issues/ARC-2#document-ar-template), **quais decisões `aceita` as duas ARs desta task precisam consumir**, de qual ADR reservada cada uma vem, e qual é o estado real desse insumo hoje.

Ele existe por três motivos:

1. O template de AR é explícito: *"Se uma AR precisa tomar uma nova decisão para ficar completa, pare: essa decisão precisa de sua própria ADR primeiro, ou a AR está legislando silenciosamente."* Escrever AR-001 e AR-002 agora, com as ADRs das quais dependem inexistentes, seria exatamente isso — o Solution Architect legislando sobre gateway, persistência, identidade, segmentação e topologia, que não são dele.
2. O [registro de decisões](/ARC/issues/ARC-2#document-decision-registry) §8 autoriza explicitamente o rascunho **em paralelo**: *"As duas ARs podem ser rascunhadas em paralelo com as ADRs das quais dependem — elas simplesmente não podem ser submetidas ao comitê antes delas."* Este contrato de insumos é o que pode ser produzido em paralelo sem inventar as decisões.
3. Os responsáveis por [ARC-5](/ARC/issues/ARC-5), [ARC-6](/ARC/issues/ARC-6), [ARC-7](/ARC/issues/ARC-7) e agora [ARC-13](/ARC/issues/ARC-13) estão escrevendo ADRs que estas ARs vão realizar. É mais útil para eles saberem **agora** o que a referência precisa extrair de cada decisão do que descobrirem depois, quando a AR não fechar.

> **Ressalva regulatória, reproduzida conforme o modelo operacional §3:** este documento usa o conjunto interino de IDs de obrigação (`OBL-*`) na profundidade definida por `PARAM-REGDEPTH` (nível geral). É uma leitura arquitetural, não um parecer jurídico. A citação em nível de artigo e a suficiência jurídica são de responsabilidade das funções de compliance e jurídico do banco, via [ARC-8](/ARC/issues/ARC-8). As famílias de requisito PCI-DSS são citadas em **nível de família**; a avaliação formal de escopo do CDE é de compliance de cartões/QSA.

## 2. Estado verificado dos insumos em 2026-10-04 (revisão 0.2)

Verificado diretamente nos documentos e no estado das tasks, não presumido:

| Insumo | Task | Responsável | Estado verificado | Consequência para esta task |
| --- | --- | --- | --- | --- |
| Templates de ADR/AR, registro, checklist, modelo operacional, caminho de waiver | [ARC-2](/ARC/issues/ARC-2) | [Governance Architect](/ARC/agents/governance-architect) | **Disponíveis**, 7 documentos, em pt-BR | Desbloqueado. Este contrato já usa o template de AR real. |
| `ADR-0006` topologia active-active, `ADR-0007` conectividade híbrida, `ADR-0008` posicionamento VM vs. container, `ADR-0009` tiering de DR | [ARC-5](/ARC/issues/ARC-5) | [Infrastructure Architect](/ARC/agents/infrastructure-architect) | **Nenhum documento.** Task `blocked` | AR-002 não pode ser completada; AR-001 fica sem §5 e §7 |
| `ADR-0010` estratégia de API gateway, `ADR-0011` guardrails de persistência | [ARC-6](/ARC/issues/ARC-6) | [Technical Architect](/ARC/agents/technical-architect) | **Nenhum documento.** Task `in_progress` (antes `blocked`) | AR-001 não pode ser completada |
| `ADR-0012` identidade, `ADR-0013` segredos e chaves, `ADR-0014` segmentação de rede | [ARC-7](/ARC/issues/ARC-7) | [Security Architect](/ARC/agents/security-architect) | **Nenhum documento.** Task `in_progress` (antes `blocked`) | Ambas as ARs ficam sem §6 e §8 |
| **ADR de logging de segurança e trilha de auditoria** (G1-sec + G2), ID esperado `ADR-0015` | [ARC-13](/ARC/issues/ARC-13) | [Security Architect](/ARC/agents/security-architect), com [Technical Architect](/ARC/agents/technical-architect) como revisor obrigatório | **Encomendada** pela decisão de [ARC-11](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4); task `in_progress`, **nenhum documento ainda** | §6 e §8 das duas ARs passam a citá-la como `proposta`; **não** cria aresta de bloqueio nesta task (§5.3) |
| Reserva do ID da ADR acima e registro das lacunas conscientes | [ARC-12](/ARC/issues/ARC-12) | [Governance Architect](/ARC/agents/governance-architect) | Task `in_progress`, **ID ainda não reservado** | Até a reserva, as ARs referenciam `ADR-00NN` (esperado `ADR-0015`), não um ID fixo |
| Catálogo autoritativo de obrigações regulatórias | [ARC-8](/ARC/issues/ARC-8) | [Governance Architect](/ARC/agents/governance-architect) | **Não iniciado** (registro §7) | Ambas as ARs usam o conjunto interino `OBL-*`; a coluna de obrigação será trocada depois |

**Leitura honesta:** `ADR-0001` está `proposta` e nenhuma das treze ADRs de domínio reservadas existe em forma de rascunho — nem a décima quarta, encomendada hoje. Pelo item de prontidão **R3** — *"Se qualquer ADR realizada não está `aceita`, o status desta AR é `proposta`"* — AR-001 e AR-002 não poderiam passar de `proposta` nem se estivessem escritas hoje. O bloqueio desta task está correto e não deve ser contornado. O que mudou de 0.1 para 0.2 é o **movimento** das tasks de domínio ([ARC-6](/ARC/issues/ARC-6) e [ARC-7](/ARC/issues/ARC-7) saíram de `blocked`) e o **fechamento da lacuna de maior gravidade** (§5), não a chegada de insumo.

## 3. AR-001 — API exposta externamente através do mix de gateways

### 3.1 Escopo que a referência vai cobrir

Um time de entrega construindo uma **API REST síncrona exposta a consumidores fora do banco** — app mobile próprio, parceiro via contrato, ou canal público — servida por um serviço no `OCP` ou em Kubernetes gerenciado, com acesso a dado persistente e observabilidade de ponta a ponta.

### 3.2 Arquétipo proposto, com a base que o item R5 exige

O item **R5** exige ao menos um número quantificado **com base declarada**. Os números abaixo são a faixa para a qual pretendo dimensionar a referência; **a base ainda não existe** e está registrada como questão aberta na §6.

| Atributo do arquétipo | Faixa proposta | Base |
| --- | --- | --- |
| Perfil de transação | Leitura predominante, escrita transacional em minoria | Proposta do autor — **a confirmar** |
| Taxa de pico | Faixa média: 500–1.500 TPS agregados no gateway | **Sem base medida.** Precisa de volumetria real de um canal existente |
| Latência alvo | p95 ≤ 400 ms fim a fim no gateway | Proposta do autor — **a confirmar** |
| Sensibilidade do dado | Dado pessoal (LGPD) **e** potencialmente dado de portador de cartão | Decorre da §3.4 |
| Tier de criticidade | A definir conforme `ADR-0009` | Depende de [ARC-5](/ARC/issues/ARC-5) |
| Consumidores | Mobile próprio, parceiro, público | Escopo da task |

Uma referência dimensionada para 50 TPS e uma para 5.000 TPS não são a mesma arquitetura. Prefiro declarar a faixa e marcar a base como ausente a publicar um número sem procedência.

### 3.3 Contrato de insumos, por seção do template

| Seção do template de AR | Decisão que a AR precisa consumir | ADR reservada | Task / responsável | Estado |
| --- | --- | --- | --- | --- |
| §5 Realização na plataforma | Qual gateway é o **padrão** para exposição externa entre `AGW-AWS`, `AGW-AZR` e `AGW-AXW`, e qual é o critério de escolha. A AR precisa de **um** padrão e variantes nomeadas; se receber três opções igualmente endossadas, ela entrega um cardápio e não uma referência | `ADR-0010` | [ARC-6](/ARC/issues/ARC-6) / [Technical Architect](/ARC/agents/technical-architect) | Ausente |
| §5 | Se o serviço roda em `OCP` ou em Kubernetes gerenciado, e qual é o default | `ADR-0004` | [ARC-4](/ARC/issues/ARC-4) / [Cloud Architect](/ARC/agents/cloud-architect) | Ausente |
| §5 | Em qual landing zone por nuvem a exposição externa é permitida | `ADR-0003` | [ARC-4](/ARC/issues/ARC-4) | Ausente |
| §5 | Divisão container/VM dos componentes do caminho de exposição | `ADR-0008` | [ARC-5](/ARC/issues/ARC-5) | Ausente |
| §6 Exposição de API | Políticas obrigatórias no gateway: rate limiting, validação de schema, mTLS ou não, terminação de TLS, onde o token é validado | `ADR-0010` | [ARC-6](/ARC/issues/ARC-6) | Ausente |
| §6 Identidade | Autenticação do consumidor externo e identidade de workload entre gateway e serviço nos quatro ambientes | `ADR-0012` | [ARC-7](/ARC/issues/ARC-7) / [Security Architect](/ARC/agents/security-architect) | Ausente |
| §6 Segredos e chaves | Onde vivem as chaves de TLS e os segredos do serviço, e como diferem entre `OCP` e as três nuvens | `ADR-0013` | [ARC-7](/ARC/issues/ARC-7) | Ausente |
| §6 Segmentação | Onde fica a fronteira de exposição, qual zona recebe tráfego externo, o que atravessa cada fronteira de confiança | `ADR-0014` | [ARC-7](/ARC/issues/ARC-7) | Ausente |
| §6 Dados | Escolha entre `DATA-REL` e `DATA-NREL` para este arquétipo, titularidade de schema, retenção e descarte do dado persistido | `ADR-0011` | [ARC-6](/ARC/issues/ARC-6) | Ausente. **Fronteira de G4 a ser declarada na própria `ADR-0011`** (encaminhamento 3 de [ARC-11](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4)) |
| §6 Auditoria e logging de segurança | O que o caminho deve emitir, destino e integridade da trilha, correlação através dos três gateways, o que **não** pode ser logado, e a evidência que um time produz para um auditor | `ADR-00NN` (esperado `ADR-0015`) | [ARC-13](/ARC/issues/ARC-13) / [Security Architect](/ARC/agents/security-architect) | **ADR encomendada, ainda sem rascunho.** A AR cita como `proposta`; se não houver rascunho no fechamento de [ARC-10](/ARC/issues/ARC-10), §6 vai `Não endereçado` + §10 (§5.3) |
| §6 Observabilidade operacional (métricas, traces, SLO, APM) | — | **Nenhuma. Lacuna consciente G1-op** | — | `Não endereçado` na §6 + linha na §10 (§5.2) |
| §6 Configuração e deployment | — | **Nenhuma. Lacuna consciente G3** | — | §10, com a nota PCI família 6 (§5.2) |
| §6 Classificação de dado | — | **Nenhuma. Lacuna consciente G4-class**, endereçada a [ARC-8](/ARC/issues/ARC-8) | — | §10 (§5.2) |
| §7 Resiliência | RTO/RPO por tier, e comportamento na perda de um lado do `DC-AA` ou de uma região | `ADR-0006`, `ADR-0009` | [ARC-5](/ARC/issues/ARC-5) | Ausente |
| §8 Conformidade — família PCI 10, coluna de evidência | Que evidência de logging e monitoramento do CDE um time copiando esta AR pode mostrar | `ADR-00NN` (esperado `ADR-0015`) | [ARC-13](/ARC/issues/ARC-13) | **ADR encomendada, ainda sem rascunho** |
| §11 Custo | Precificação por requisição do gateway e penhascos de licença entre os três gateways | `ADR-0005`, `ADR-0010` | [ARC-4](/ARC/issues/ARC-4), [ARC-6](/ARC/issues/ARC-6) | Ausente |

### 3.4 Efeito no escopo PCI-DSS

A descrição desta task é direta: *"a referência de API exposta externamente mostra os controles relevantes para PCI-DSS em vigor no caminho que ela documenta, de modo que um time de entrega que a copie os herde."* Isso tem uma consequência de design que registro agora, antes das ADRs:

- **A referência vai declarar explicitamente a fronteira do CDE.** Uma API externa de um banco em escopo PCI-DSS frequentemente transporta, ou é conectada a sistemas que transportam, dado de portador de cartão. Uma AR que não disser onde o CDE começa e termina deixa o time copiando-a sem saber se entrou no escopo.
- **Decisão estrutural que isto impõe:** se o caminho documentado transmitir dado de cartão, então gateway, serviço, store e o transporte entre eles entram no CDE ou são sistemas conectados. A alternativa é a referência **manter o dado de cartão fora do caminho padrão** — tokenização antes do gateway — e tratar o caminho que carrega cartão como variante nomeada. **Esta é uma escolha de arquitetura, não uma escolha de redação, e ela depende de `ADR-0010`, `ADR-0011` e `ADR-0014`.** Não vou decidi-la sozinho nesta AR.
- **Famílias de requisito em que a referência obrigatoriamente toca**, na profundidade de `PARAM-REGDEPTH` (substância, não artigo): segmentação de rede para redução de escopo (família 1); proteção de dado de cartão armazenado (família 3); criptografia em trânsito em redes públicas (família 4); desenvolvimento seguro e gestão de vulnerabilidades (famílias 6 e 11); controle de acesso por necessidade de conhecer e autenticação (famílias 7 e 8); **logging e monitoramento do CDE (família 10)**.
- **Família 10 — estado em 0.2:** era a única das famílias acima sem nenhuma ADR que a endereçasse. A decisão de [ARC-11](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4) encomendou a ADR ([ARC-13](/ARC/issues/ARC-13)), então a lacuna **tem dono e escopo definido**, mas ainda **não tem conteúdo**: nenhum rascunho existe hoje. Até existir, AR-001 não consegue mostrar o controle de logging "em vigor no caminho que documenta" — ela só pode citar uma ADR `proposta`. Declaro isso como estado, não como cobertura.
- **Família 6 — lacuna consciente, declarada:** gestão de mudança e separação entre ambientes de desenvolvimento/teste e produção não têm padrão de arquitetura na primeira onda (G3, §5.2). Para workloads no CDE ou conectados a ele, esse controle fica com o processo de mudança vigente do banco e com o time de entrega. A AR diz isso na §10; não implica cobertura.

## 4. AR-002 — Serviço transacional resiliente, active-active

### 4.1 O que esta referência precisa ser honesta sobre

A acceptance criteria desta task é inusualmente específica, e com razão: *"uma referência que implica que dois sites são transparentes para a aplicação é pior do que nenhuma referência."* Portanto AR-002 será organizada em torno do que a **aplicação** tem de fazer de diferente, não em torno do diagrama de infraestrutura. O custo em nível de aplicação que a referência vai tornar explícito:

| Obrigação que cai na aplicação | Por que a infraestrutura não resolve |
| --- | --- |
| Idempotência de toda operação de escrita, com chave de idempotência durável | Em um failover, o cliente reenvia. Sem chave de idempotência, um failover produz duplicidade de transação financeira |
| Limite de transação dentro de um site, não atravessando sites | Transação distribuída síncrona entre dois sites paga latência de round-trip em cada commit e transforma uma falha de link em indisponibilidade dos dois lados |
| Afinidade de sessão e de dado por chave de negócio | Escrita concorrente na mesma conta nos dois sites é conflito de dado, e nenhuma camada de replicação resolve isso sem perder uma das escritas |
| Tratamento explícito de transação em andamento no momento da perda do site | O estado "enviado, sem resposta" existe sempre. A aplicação precisa de reconciliação, não de esperança |
| Reconciliação e detecção de divergência entre sites | Replicação assíncrona tem janela de perda. Alguém precisa descobrir o que caiu nela |
| Comportamento sob split-brain, incluindo qual lado recusa escrita | Se os dois lados aceitam escrita durante uma partição, o banco tem dois livros-razão |
| Emissão de evento de auditoria correlacionável **através** do failover — identificador de correlação propagado, ordenação e deduplicação | Correlacionar uma transação que trocou de site exige que a aplicação carregue o identificador; a plataforma de log não o inventa depois. O contrato do evento vem de `ADR-00NN` ([ARC-13](/ARC/issues/ARC-13)); **carregá-lo é custo de aplicação** |

Nenhum item acima é uma decisão minha de arquitetura de plataforma — todos dependem de `ADR-0006`, `ADR-0011` e, para o último, de `ADR-00NN`. O que **é** meu é tornar o custo visível, e isso eu posso declarar agora.

### 4.2 Contrato de insumos, por seção do template

| Seção do template de AR | Decisão que a AR precisa consumir | ADR reservada | Task / responsável | Estado |
| --- | --- | --- | --- | --- |
| §4 Fluxos, §7 Resiliência | A topologia real do `DC-AA`: os dois sites aceitam escrita, ou um é ativo para escrita? Distância, latência entre sites, mecanismo de quórum | `ADR-0006` | [ARC-5](/ARC/issues/ARC-5) / [Infrastructure Architect](/ARC/agents/infrastructure-architect) | Ausente |
| §7 | Modelo de replicação e garantia de consistência para `DATA-REL` e `DATA-NREL`, e a janela de perda de dado conhecida | `ADR-0011`, `ADR-0006` | [ARC-6](/ARC/issues/ARC-6), [ARC-5](/ARC/issues/ARC-5) | Ausente |
| §7 | RTO/RPO por tier de criticidade, e a qual tier este arquétipo pertence | `ADR-0009` | [ARC-5](/ARC/issues/ARC-5) | Ausente |
| §4, §7 | Comportamento declarado sob split-brain e quem recusa escrita | `ADR-0006` | [ARC-5](/ARC/issues/ARC-5) | Ausente |
| §5 | Conectividade entre sites e entre DC e nuvem, e o que o serviço pode assumir sobre ela | `ADR-0007` | [ARC-5](/ARC/issues/ARC-5) | Ausente |
| §5 | Quais componentes transacionais rodam em VM e quais em container | `ADR-0008` | [ARC-5](/ARC/issues/ARC-5) | Ausente |
| §5 | Se este arquétipo pode sair do `DC-AA` para nuvem, e sob qual critério | `ADR-0002` | [ARC-3](/ARC/issues/ARC-3) / [Enterprise Architect](/ARC/agents/enterprise-architect) | Ausente |
| §6 | Identidade, segredos e segmentação replicados de forma consistente entre os dois sites | `ADR-0012`, `ADR-0013`, `ADR-0014` | [ARC-7](/ARC/issues/ARC-7) | Ausente |
| §6 Auditoria | Rastreabilidade suficiente para reconstruir uma transação que atravessou um failover: contrato do evento, fonte de tempo sincronizada, identificador de correlação, deduplicação, e continuidade da própria trilha na perda de um site | `ADR-00NN` (esperado `ADR-0015`), com `ADR-0009` para o tier de DR da plataforma de log | [ARC-13](/ARC/issues/ARC-13) / [Security Architect](/ARC/agents/security-architect) | **ADR encomendada, ainda sem rascunho.** Citada como `proposta`; contingência em §5.3 |
| §6 Retenção de log de auditoria | Prazos por classe de evento | `ADR-00NN` (esperado `ADR-0015`) | [ARC-13](/ARC/issues/ARC-13) | **ADR encomendada, ainda sem rascunho** |
| §6 Retenção e descarte do dado persistido | Fronteira declarada do guardrail de persistência | `ADR-0011` | [ARC-6](/ARC/issues/ARC-6) | Ausente |
| §6 Observabilidade operacional / configuração e deployment / classificação de dado | — | **Lacunas conscientes G1-op, G3, G4-class** | — | §10 (§5.2) |

### 4.3 Efeito no escopo PCI-DSS

AR-002 descreve um arquétipo transacional genérico, então o escopo depende de uma decisão de arquétipo que registro aqui como aberta (§6, questão 3). A regra que a referência vai aplicar, qualquer que seja a resposta:

- **Se o serviço transacional processar transação de cartão**, então o CDE existe nos **dois** sites do `DC-AA` **e no transporte de replicação entre eles**. Esta é a consequência de escopo mais fácil de perder em uma arquitetura active-active: a replicação de dado de cartão entre sites estende o CDE ao link, e o link passa a exigir criptografia em trânsito e monitoramento como qualquer outro componente do CDE.
- **Famílias de requisito em que isso toca:** segmentação e redução de escopo (família 1) — duplicada, porque há duas zonas; proteção do dado armazenado (família 3) em dois conjuntos de mídia; criptografia em trânsito (família 4) no link entre sites; logging e monitoramento (família 10) correlacionável **através** de um failover, o que é materialmente mais difícil do que logging em um site só.
- **Consequência de escopo que vem com a nova ADR:** a plataforma que recebe log do CDE é sistema conectado e **entra em escopo PCI-DSS**. Isso é decisão de `ADR-00NN` ([ARC-13](/ARC/issues/ARC-13)), não minha — mas AR-002 vai declarar o efeito, porque um time que copia a referência e aponta seus logs para um coletor fora do perímetro muda o escopo sem perceber.
- **Se o arquétipo padrão for declarado fora do CDE**, a linha de PCI-DSS da §8 não será silenciosamente omitida: o template obriga a manter a linha com `N/A` mais o motivo, e a AR vai declarar explicitamente o que um time muda se levar dado de cartão para este arquétipo.

## 5. Lacunas G1–G4 — decididas pelo Arquiteto Principal em [ARC-11](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4)

A revisão 0.1 deste documento levantou quatro lacunas da §6 do template sem ADR reservada e as escalou em vez de preencher a célula. **A decisão saiu em 2026-10-04** e está registrada em [ARC-11](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4). Esta seção substitui a §5 da revisão 0.1: ela não reabre o que foi decidido, apenas registra como as duas ARs passam a se comportar.

### 5.1 Resultado, por lacuna

| # | Lacuna | Decisão | Responsável | Efeito nas minhas ARs |
| --- | --- | --- | --- | --- |
| G2 | Auditoria e rastreabilidade de transação de negócio | **ADR na primeira onda** | [Security Architect](/ARC/agents/security-architect), [ARC-13](/ARC/issues/ARC-13); revisor obrigatório [Technical Architect](/ARC/agents/technical-architect) | §6 e §8 citam `ADR-00NN` como `proposta` |
| G1-sec | Observabilidade na parte de logging de segurança e detecção | **Mesma ADR que G2** | idem | idem |
| G1-op | Observabilidade operacional (métricas, traces, SLO, APM) | **Lacuna consciente**, candidata à segunda onda | — | §6 `Não endereçado` + §10 |
| G3 | Configuração, deployment, promoção entre ambientes | **Lacuna consciente**, candidata à segunda onda | — | §10, **com nota PCI família 6** |
| G4 | Retenção e classificação de dado | **Dividida:** retenção de log de auditoria → `ADR-00NN`; retenção/descarte do dado persistido → fronteira declarada em `ADR-0011`; esquema corporativo de classificação → política do banco, [ARC-8](/ARC/issues/ARC-8) | Security / Technical / Governance | §6 cita `ADR-0011` e `ADR-00NN`; classificação na §10 |

**Sobre o ID:** a decisão não atribui ID — só o [Governance Architect](/ARC/agents/governance-architect) escreve no registro. O ID esperado é `ADR-0015` ([ARC-12](/ARC/issues/ARC-12)), e verifiquei hoje que **a reserva ainda não foi publicada**. Até lá, as duas ARs referenciam `ADR-00NN (esperado ADR-0015)` e trocam pelo ID real quando o registro o publicar. Não vou fixar `ADR-0015` em texto de AR antes disso.

### 5.2 Redação de referência para a §10 das duas ARs

Transcrevo a redação decidida em [ARC-11](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4) §6, adaptada ao sujeito de cada AR. Estas linhas entram na §10 (*lacunas e questões abertas*) de AR-001 e AR-002, e o pacote ao comitê as repete como questões abertas:

> **G1-op — Observabilidade operacional.** Não há decisão do Escritório na primeira onda para métricas, traces, SLO e APM. O time de entrega decide localmente. Não há obrigação regulatória mapeada diretamente sobre esta lacuna; o lado de segurança da observabilidade está endereçado por `ADR-00NN` (esperado `ADR-0015`). Candidata à segunda onda.

> **G3 — Configuração, deployment e promoção entre ambientes.** Não há decisão do Escritório na primeira onda. **Nota PCI-DSS:** o tema toca a família de requisito 6 — gestão de mudança e separação entre ambientes de desenvolvimento/teste e produção — para qualquer workload no CDE ou conectado a ele. Esse controle fica com o processo de mudança vigente do banco e com o time de entrega; **não há padrão de arquitetura para ele nesta onda**. Candidata à segunda onda. Se o comitê considerar isso inaceitável para workloads em escopo PCI, a resposta é uma ADR na segunda onda, não um remendo em `ADR-0004`.

> **G4-class — Esquema corporativo de classificação de dados.** A classificação é política do banco (`OBL-CYB`, classificação e proteção de dados; `OBL-LGPD`, minimização). A arquitetura **consome** a classificação e não a define. Endereçada a [ARC-8](/ARC/issues/ARC-8) como insumo de mapeamento de obrigação, não como ADR. Esta AR assume que a classificação do dado do arquétipo é fornecida pelo dono do dado.

### 5.3 Contingência, já decidida — e por que ela não vira aresta de bloqueio aqui

A decisão é explícita: **a nova ADR não segura a onda.** Se `ADR-00NN` não tiver passado na revisão de governança quando o Escritório fechar [ARC-10](/ARC/issues/ARC-10), então:

1. §6 de AR-001 e AR-002 vai com `Não endereçado` para G1/G2;
2. §10 declara a lacuna com a nota **"ADR em rascunho, `ADR-00NN`"**, nomeando [ARC-13](/ARC/issues/ARC-13) e seu responsável;
3. o pacote ao comitê diz isso explicitamente.

Portanto **não acrescentei [ARC-13](/ARC/issues/ARC-13) aos bloqueadores desta task.** Os bloqueadores de [ARC-9](/ARC/issues/ARC-9) permanecem [ARC-5](/ARC/issues/ARC-5), [ARC-6](/ARC/issues/ARC-6) e [ARC-7](/ARC/issues/ARC-7); a dependência da nova ADR fica em [ARC-10](/ARC/issues/ARC-10), conforme decidido. Uma lacuna declarada com ADR em andamento é defensável; uma lacuna sem dono não é.

## 6. Questões que precisam de interpretação do banco, não do Escritório

| # | Questão | Por que o Escritório não pode responder sozinho | Quem |
| --- | --- | --- | --- |
| 1 | Volumetria real para fixar a faixa de dimensionamento de AR-001 (§3.2) | O item R5 exige base declarada. O Escritório não tem a medição de nenhum canal em produção | Time de canal / dono do produto |
| 2 | O caminho externo padrão de AR-001 transporta dado de portador de cartão, ou o dado de cartão é tokenizado antes do gateway? | Muda a fronteira do CDE e, com ela, a §5, §6 e §8 inteiras da referência | Compliance PCI-DSS do banco + [ARC-6](/ARC/issues/ARC-6) |
| 3 | O arquétipo transacional de AR-002 é, por padrão, dentro ou fora do CDE? | Determina se o CDE se estende aos dois sites e ao link de replicação (§4.3) | Compliance PCI-DSS do banco + [Principal Architect](/ARC/agents/principal-architect) |
| 4 | `PARAM-REGDEPTH` permanece em nível geral? | Já é questão aberta 1 do modelo operacional §12. Se passar a exigir nível de artigo, a §8 das duas ARs é reescrita | Comitê de arquitetura, via [ARC-8](/ARC/issues/ARC-8) |

Nenhuma das quatro foi respondida pela decisão de [ARC-11](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4), e as questões 2 e 3 continuam sendo as que mais mudam o conteúdo das ARs. Elas não bloqueiam o rascunho; bloqueiam o fechamento da §8.

## 7. O que eu faço quando os insumos chegarem

Ordem de execução, para que os responsáveis das ADRs saibam o que desbloqueia o quê:

1. **AR-001 fica escrevível** quando `ADR-0010` e `ADR-0011` ([ARC-6](/ARC/issues/ARC-6)) existirem em rascunho, com `ADR-0012`–`ADR-0014` ([ARC-7](/ARC/issues/ARC-7)) em paralelo. Não preciso que estejam `aceita` para rascunhar — preciso que existam, para não inventá-las.
2. **AR-002 fica escrevível** quando `ADR-0006` e `ADR-0009` ([ARC-5](/ARC/issues/ARC-5)) existirem em rascunho. `ADR-0006` é o insumo único mais crítico desta task: sem a topologia real, a §4 e a §7 de AR-002 são ficção.
3. **`ADR-00NN` ([ARC-13](/ARC/issues/ARC-13)) é desejável, não pré-requisito.** Se existir em rascunho quando eu escrever, a §6 e a coluna de evidência da §8 citam-na como `proposta`; se não existir, aplico a contingência da §5.3. Em nenhum dos casos eu espero por ela.
4. As duas ARs entram no [registro](/ARC/issues/ARC-2#document-decision-registry) §6 como `proposta` quando o rascunho começar, com `Realiza as ADRs` preenchido e `Decisões ainda proposta` listando tudo o que ainda não foi aceito — e, por **R3**, permanecem `proposta` até que as ADRs sejam aceitas.
5. Nenhuma das duas vai ao comitê antes das ADRs que realizam. Isso não é cautela; é o que o registro §8 e o item **R3** determinam.

## 8. Changelog

| Data | Versão | Quem | O que mudou |
| --- | --- | --- | --- |
| 2026-10-04 | 0.1 | Solution Architect | Versão inicial. Insumos verificados nos documentos de [ARC-2](/ARC/issues/ARC-2), [ARC-5](/ARC/issues/ARC-5), [ARC-6](/ARC/issues/ARC-6) e [ARC-7](/ARC/issues/ARC-7); lacunas G1–G4 levantadas ao [Principal Architect](/ARC/agents/principal-architect) |
| 2026-10-04 | 0.2 | Solution Architect | Decisão de [ARC-11](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4) incorporada (encaminhamento 4). §5 reescrita de "escalada" para "decisão registrada", com a redação de §10 para G1-op, G3 e G4-class e a contingência de [ARC-10](/ARC/issues/ARC-10). §3.3 e §4.2 passam a citar `ADR-00NN` (esperado `ADR-0015`, [ARC-13](/ARC/issues/ARC-13)) em §6 auditoria e na coluna de evidência da família PCI 10; linhas de lacuna consciente acrescentadas. §4.1 ganha o custo de aplicação da correlação de auditoria através do failover. §2 reverificada: [ARC-6](/ARC/issues/ARC-6) e [ARC-7](/ARC/issues/ARC-7) agora `in_progress`, nenhuma ADR em documento, ID `ADR-0015` ainda não reservado |
