# AR-NNN (pendente) — Fluxo de atendimento a direito do titular: acesso, correção, eliminação e portabilidade

> **Status desta AR.** Rascunho `proposta`, pronto para a revisão interna do Escritório e, por meio dela, para a revisão do comitê de arquitetura (guilda) do banco. **Nada aqui está aprovado.** A aprovação final não pertence ao Escritório.
>
> **ID.** Esta AR é de **segunda onda** e não tem ID reservado no [registro de decisões §8](/ARC/issues/ARC-2#document-decision-registry), que só reservou `AR-001` e `AR-002`. Mantenho `AR-NNN (pendente)` — a mesma convenção já usada pelas três ARs de landing zone em [ARC-4](/ARC/issues/ARC-4) — e peço ao Arquiteto de Governança a atribuição do ID na triagem. Há hoje **quatro** ARs esperando ID; atribuir um número por conta própria colidiria com elas.
>
> **R3.** Esta AR realiza decisões que estão todas em status `proposta`. Pelo item de prontidão **R3**, ela própria não pode passar de `proposta` nem ser submetida ao comitê antes delas.
>
> **Gate interno — liberado em 2026-10-05.** O card de confirmação desta issue ("Gate interno: AR de direitos do titular v0.1 pronta para a revisão do comitê?", endereçado ao Arquiteto Principal) foi **aceito** com o rótulo *"Pronta para o comitê, com as lacunas declaradas"*, resolvido por usuário do board em 2026-10-05, sem pedido de alteração. Isso significa **exatamente** duas coisas: (a) a v0.1 está pronta para entrar no pacote do comitê de arquitetura (guilda) **quando o pré-requisito **R3** for satisfeito**, e (b) as oito lacunas da §10.3 ficam declaradas como lacunas, e não como itens pendentes de desenho. **Não** significa aprovação de nada: a aprovação é do comitê, e nem a liberação do gate nem esta AR a antecipam. O que continua faltando depois do gate está em §0.2.

**Origem:** [decisão D-11](/ARC/issues/ARC-39#document-decisao-d7-d11), posição 2 do backlog de segunda onda (Arquiteto Principal, 2026-10-05). Pré-requisito de interpretação jurídica resolvido em [ARC-46](/ARC/issues/ARC-46). Compromisso de autoria assumido em [ARC-38](/ARC/issues/ARC-38) item 9 / lacuna **L6** de `AR-001`.

**Documentos de referência:** [Template de AR](/ARC/issues/ARC-2#document-ar-template) · [Checklist de prontidão](/ARC/issues/ARC-2#document-readiness-checklist) · [Registro de decisões](/ARC/issues/ARC-2#document-decision-registry) · [Modelo operacional](/ARC/issues/ARC-2#document-operating-model) · [Catálogo de obrigações](/ARC/issues/ARC-8#document-catalogo-obrigacoes-regulatorias) · [Processo de exceção e waiver](/ARC/issues/ARC-2#document-exception-and-waiver-path) · [`AR-001`](/ARC/issues/ARC-9#document-ar-001-api-exposta-externamente) · [`AR-002`](/ARC/issues/ARC-9#document-ar-002-servico-transacional-active-active)

---

## 0. Cabeçalho

| Campo | Valor |
| --- | --- |
| **ID da AR** | `AR-NNN` — **pendente de atribuição** pelo Arquiteto de Governança na triagem (ver nota acima) |
| **Status** | `proposta` |
| **Versão** | 0.1 |
| **Responsável** | Solution Architect |
| **Coautoria requerida** | DPO do banco, conforme D-11 posição 2 ("Solution Architect, com DPO"). **Esta versão 0.1 foi escrita sem a coparticipação do DPO** — o DPO não é um papel do Escritório e não houve interação disponível nesta data. Os pontos que dependem dele estão nomeados como lacunas **N1**, **N6** e **N8** da §10.3, não resolvidos em silêncio |
| **Realiza as ADRs** | `ADR-0002`, `ADR-0006`, `ADR-0007`, `ADR-0009`, `ADR-0010`, `ADR-0011`, `ADR-0012`, `ADR-0013`, `ADR-0014`, `ADR-0015` |
| **Decisões ainda `proposta`** | **Todas as dez acima.** Nenhuma ADR realizada por esta AR está `aceita` nesta data |
| **Substitui / Substituída por** | Nenhuma / Nenhuma |
| **Consumidor pretendido** | Time de entrega responsável pelo serviço corporativo de atendimento a direito do titular, e todo time dono de um domínio de dado pessoal que precise expor um **executor de domínio** (§3) para ser alcançado por esse serviço |
| **Maturidade** | `Apenas referência` — nenhum workload foi construído a partir desta AR |
| **Idioma** | Português do Brasil (conforme `PARAM-LANG`) |
| **Gate interno do Escritório** | **Liberado** em 2026-10-05 — card de confirmação desta issue aceito ("Pronta para o comitê, com as lacunas declaradas"), sem pedido de alteração. Liberação de **prontidão**, não aprovação |
| **Data de submissão ao comitê / resultado** | Não submetida / `pendente`. Pré-requisito **não satisfeito**: aceitação das dez ADRs acima (**R3**). O gate interno está liberado; a submissão continua bloqueada por R3 |
| **Tier reivindicável** | No máximo **Tier 1**, por D-7 (nenhum workload reivindica Tier 0 na primeira onda). Ver §7 |

### 0.1 A interpretação jurídica que esta AR realiza

Esta AR não interpreta a LGPD. Ela realiza, como condição de desenho, a interpretação que o board confirmou em [ARC-46](/ARC/issues/ARC-46) (2026-10-05), em resposta à pergunta que D-11 encaminhou ao DPO/Jurídico via Arch Master:

> Sob a LGPD, o atendimento a um pedido de eliminação **pode** ser cumprido pela expiração do backup imutável com trava de tempo (retenção ≥ 35 dias, `ADR-0009` C8), em domínio replicado entre dois sítios, **com bloqueio de restauração do registro eliminado e reaplicação da eliminação após qualquer restauração**.

As duas condições em negrito não são recomendações: são **o que torna a interpretação válida**. Um desenho que elimine do sistema ativo e deixe o backup expirar, sem o bloqueio de restauração e sem a reaplicação pós-restore, **não** está coberto por essa resposta. É por isso que as §4.3, §4.6 e §6 desta AR tratam o bloqueio e a reaplicação como componentes de primeira classe, e não como passos operacionais de runbook.

Com isso, `ADR-0011` C9 — "C9 em nenhuma hipótese se sobrepõe a um pedido de eliminação de titular já atendido sob LGPD" — passa a ter um mecanismo concreto de cumprimento sob backup imutável, que era exatamente a tensão nomeada em [ARC-38](/ARC/issues/ARC-38) item 9 e em [ARC-42](/ARC/issues/ARC-42).

> **Ressalva obrigatória.** O Escritório mapeia obrigações para controles de arquitetura em nível geral e **não emite parecer jurídico**. A interpretação acima é do board/DPO/Jurídico do banco; esta AR apenas a implementa.

### 0.2 O que continua faltando depois do gate interno

O gate interno liberou a **prontidão** da v0.1. Quatro coisas continuam pendentes, e nenhuma delas é minha para fechar:

| # | O que falta | De quem | Onde está |
| --- | --- | --- | --- |
| 1 | **Aceitação das dez ADRs realizadas.** Enquanto estiverem `proposta`, **R3** impede esta AR de passar de `proposta` e de ser submetida ao comitê | Arquiteto Principal / comitê, por ADR | §0, linha *Decisões ainda `proposta`* |
| 2 | **Atribuição do ID da AR** (`AR-NNN` → número). Pela §1 do registro é da triagem do Arquiteto de Governança; há quatro ARs na fila de número | Arquiteto de Governança | pedido em [ARC-2](/ARC/issues/ARC-2); linha pronta em [registro-ar-direitos-do-titular](/ARC/issues/ARC-50#document-registro-ar-direitos-do-titular) |
| 3 | **Decisão sobre N3 e N5** — RTO de `ADR-0009` C9 sem o passo de reaplicação pós-restore, e `ADR-0010` Q7 ainda aberta. São candidatas a ADR nova / revisão de ADR, não notas de registro | Arquiteto Principal | [ARC-51](/ARC/issues/ARC-51) |
| 4 | **Insumos do DPO/Jurídico** para **N1**, **N6** e **N8**, e a coautoria que D-11 exigiu e que a v0.1 não teve | DPO, via Arquiteto de Governança / Arch Master | [ARC-2](/ARC/issues/ARC-2) |

E uma ressalva que o gate não altera: **nenhum cenário foi testado**, em particular a restauração completa com reaplicação de eliminações dentro do RTO de `ADR-0009` C9 (**N3**).

---

## 1. Quando usar esta referência — e quando não usar

**Use esta referência quando todos estes forem verdadeiros:**

- O que está sendo construído é o **atendimento a um pedido de titular de dado pessoal** sob LGPD — acesso/confirmação de tratamento, correção, eliminação, ou portabilidade.
- O pedido chega por um canal do banco (app próprio, internet banking, agência, central de atendimento, ou encaminhamento da ANPD) e precisa ser **executado em mais de um sistema** que guarda dado pessoal do mesmo titular.
- Pelo menos um dos domínios alcançados persiste dado no `DC-AA` com replicação entre os dois sítios, e tem **backup imutável com trava de tempo** sob `ADR-0009` C8.
- O time aceita que o atendimento de eliminação é declarado em **duas etapas** (§4.3): efetivo nos sistemas ativos em `D+n`, e definitivo na expiração da última cópia imutável.

**Não a use quando qualquer um destes for verdadeiro** (e use a alternativa indicada):

| Se … | Use em vez disso |
| --- | --- |
| O pedido é de **um único sistema**, sem dado pessoal replicado em outro domínio, e sem backup imutável sob C8 | Não há AR. Aplique `ADR-0011` C9 diretamente no desenho do serviço: classificação e critério de descarte declarados. O custo desta AR não se justifica para um domínio único |
| O que falta é o **esquema de autenticação do cliente do banco** no canal digital | Fora de escopo aqui por exclusão de `ADR-0012` §4.2 — ver §2.2 e lacuna **N2**. É a ADR da **posição 1** do backlog de D-11 (Security Architect) |
| O que falta é o **registro das operações de tratamento (ROPA)** | Fora do alcance arquitetural — [catálogo §7 e §12](/ARC/issues/ARC-8#document-catalogo-obrigacoes-regulatorias). DPO/Compliance. D-11 declara explicitamente que não entra no backlog do Escritório |
| O que falta é a **base legal e a finalidade** de um tratamento | Fora do alcance arquitetural ([catálogo §7](/ARC/issues/ARC-8#document-catalogo-obrigacoes-regulatorias)). DPO/Jurídico/negócio. Esta AR **consome** a finalidade declarada; não a define |
| O que falta é o **esquema corporativo de classificação de dado** | [ARC-8](/ARC/issues/ARC-8) / lacuna consciente **G4-class**. Esta AR consome a classificação — ver §10.2 e lacuna **N7** |
| O pedido é de **descarte por fim de retenção**, sem pedido do titular | Não é esta AR. É `ADR-0011` C9 (critério de descarte por domínio) e, para log de auditoria, `ADR-0015` C11 — que `ADR-0011` C10 proíbe antecipar |
| O dado a eliminar é **log de auditoria** | Proibido por esta via. `ADR-0011` C10 é explícito: C9 não altera prazo de retenção de log de auditoria, que é de `ADR-0015`. Ver §4.5 |
| A API de pedidos é exposta a consumidor externo e o time quer saber **como expô-la** | Componha com [`AR-001`](/ARC/issues/ARC-9#document-ar-001-api-exposta-externamente). Esta AR cobre o fluxo de direitos; `AR-001` cobre a borda |
| O domínio alcançado é um **sistema de registro transacional active-active** e a dúvida é de failover | Componha com [`AR-002`](/ARC/issues/ARC-9#document-ar-002-servico-transacional-active-active) |

**Anti-padrões que esta referência existe para prevenir:**

- **Apagar, ou tentar apagar, a cópia de backup imutável para "cumprir" a eliminação.** Viola `ADR-0009` C8 (`MUST`) e destrói a recuperação contra corrupção lógica e ransomware de C9. A resposta confirmada em [ARC-46](/ARC/issues/ARC-46) existe justamente para que isso **não** seja necessário.
- **Reduzir a retenção da cópia imutável abaixo de 35 dias para "antecipar" a eliminação.** Mesma violação de C8, com o agravante de ser silenciosa.
- **Restaurar seletivamente um registro eliminado** a partir do backup, por pedido de operação, de negócio ou de suporte. Proibido por `C-DT-04` (§6.1). É o modo de falha mais provável de todo este desenho.
- **Liberar um ambiente restaurado para uso antes de reaplicar as eliminações.** Proibido por `C-DT-05`. Um restore limpo que recoloca registros eliminados em produção reabre todos os pedidos já respondidos ao titular — e o banco já afirmou ao titular que o dado foi eliminado.
- **Declarar ao titular "eliminado" no instante do pedido**, antes da confirmação de aplicação nos dois sítios. Com replicação assíncrona e RPO > 0 (`ADR-0006` C7, `ADR-0011` C11), o registro ainda existe no sítio secundário por algum intervalo.
- **Tirar PAN em claro do enclave** para responder a um pedido de acesso ou de portabilidade. Proibido por `ADR-0011` C7/C8 e `ADR-0010` C9/C10. Ver §4.4 e §8.2.
- **Devolver no pedido de acesso tudo o que o banco tem**, porque é mais fácil do que escolher. É o contrário da minimização — ver §6.3.
- **Guardar a lista de supressão com o dado pessoal em claro.** A lista é o artefato que sobrevive à eliminação; se ela guarda o dado, a eliminação não aconteceu. Ver `C-DT-03`.
- **Eliminar dado sujeito a retenção legal** porque o titular pediu, sem passar pelo filtro de retenção legal da §4.3 passo 3. Ver §8.1 e lacuna **N8**.

---

## 2. O arquétipo de workload

### 2.1 Forma e dimensionamento

| Atributo | Valor desta referência | Base declarada |
| --- | --- | --- |
| Forma funcional | **Orquestração de processo de longa duração**, não transação síncrona. Um pedido abre um caso que pode levar dias, alcança N domínios, e fecha com evidência | Natureza do prazo legal de resposta — ver **N1** |
| Interação de entrada | API REST síncrona para **registrar** o pedido e **consultar** seu andamento; execução assíncrona por domínio | `ADR-0010` C1 (consumidor externo) e C5 (travessia entre ambientes) |
| Perfil de transação | Baixo volume, alta sensibilidade, alta exigência de evidência. Não é um arquétipo de throughput | — |
| Faixa de volume de dimensionamento desta AR | **Ordem de 10² a 10³ pedidos/mês** para a base de um banco de varejo brasileiro, com picos após campanha de mídia ou incidente público. Pico de desenho: **10× a média diária em um dia**. A base é declarada e não medida — ver §11 | Base de dimensionamento desta AR; o banco deve substituí-la pelo volume real quando existir |
| Fan-out por pedido | 1 pedido → N executores de domínio. **N é a variável de custo e de risco dominante**, não o volume de pedidos | §3, §11 |
| Sensibilidade do dado | Dado pessoal por definição; **possivelmente** dado pessoal sensível e **possivelmente** dado de portador de cartão, quando o titular também for portador | `ADR-0011` C7, D-11 ("interseção com a família 3") |
| Tier de criticidade | **Alvo de desenho Tier 1; reivindicável no máximo Tier 1** (D-7). Ver §7 para a decomposição por domínio de dado — o registro de pedidos e a lista de supressão **não** têm o mesmo tier | `ADR-0009` §4, D-7 |
| Consumidores | O próprio titular (por canal digital), operador de agência/central agindo em nome do titular, DPO, e a ANPD por encaminhamento | §2.2 |

### 2.2 Identidade do titular — confirmação explícita de escopo

**Confirmo que a autenticação de usuário final do titular permanece FORA do escopo desta AR**, por exclusão literal de [`ADR-0012` §4.2](/ARC/issues/ARC-7#document-adr-0012-identidade-federada-quatro-ambientes):

> "**Explicitamente fora de escopo:** autenticação de usuário final (cliente do banco) em canais digitais; esta ADR cobre identidade humana interna (colaboradores, operação, administração) e identidade de workload."

Esta AR **não escolhe** o esquema de autenticação do cliente, o fator, o nível de garantia, nem o produto. Essa é a ADR da **posição 1** do backlog de D-11, do Security Architect, e é pré-requisito de maturidade desta AR além de `Apenas referência` (lacuna **N2**).

O que esta AR **precisa** declarar, para ser concreta o suficiente para um time construir, é **onde** e **como** a identidade do titular é verificada antes de o pedido ser processado. Declaro o **contrato**, não o esquema:

| Pergunta | Resposta desta AR |
| --- | --- |
| **Onde** a identidade do titular é verificada? | **No canal, antes da borda.** Nunca no serviço de direitos do titular. O orquestrador `SVC-DT` (§3) **MUST NOT** autenticar o titular, guardar credencial de titular, ou possuir repositório de credencial de cliente — é a mesma proibição que `ADR-0010` C7 impõe ao gateway in-cloud, aplicada aqui a um serviço de aplicação |
| **Como** o serviço sabe que foi verificada? | Pela asserção que acompanha o pedido, validada no Axway (`ADR-0010` C1/C7 — autenticação do consumidor externo exatamente uma vez, na borda). O orquestrador consome a asserção já validada e **não** a revalida contra o IdP do canal |
| O que a asserção **MUST** carregar? | (a) identificador do titular no padrão do canal; (b) **método de verificação** usado; (c) **nível de garantia de identidade** declarado pelo canal; (d) timestamp da verificação; (e) o identificador de correlação de `ADR-0010` C8 |
| Qual **nível mínimo** por tipo de direito? | **Esta AR não fixa os níveis.** Fixar uma escala de garantia de identidade é decisão de segurança, não de solução — seria o Solution Architect legislando sobre autenticação de cliente. O que esta AR fixa é que a escala **MUST existir** e que eliminação e portabilidade **MUST** exigir nível **estritamente maior** do que consulta de andamento (`C-DT-02`). Lacuna **N2** |
| Canal **não digital** (agência, central de atendimento) | A verificação do titular segue o procedimento vigente do canal, fora desta AR. O **operador** que registra o pedido em nome do titular autentica-se por identidade interna federada com MFA (`ADR-0012` C1/C2), e o registro do pedido guarda **as duas** identidades — titular e operador — como evidência (`C-DT-07`). Um pedido registrado por operador sem identidade interna federada é desvio de `ADR-0012` C1, não um caso especial desta AR |
| Encaminhamento da **ANPD** ou do Judiciário | Entra pelo mesmo intake mediado por operador, com a identidade do titular verificada pelo próprio ofício. O campo de método de verificação registra essa origem. Não cria um caminho técnico próprio |

**Consequência honesta:** enquanto a ADR da posição 1 não existir, um time que copie esta AR pode construir todo o fluxo, mas **não pode** afirmar que a verificação de identidade do titular atende a um padrão do Escritório — porque não há padrão. Isso é **N2**, e não deve ser escondido atrás de uma escolha local silenciosa.

---

## 3. Visão geral da arquitetura

### 3.1 Visão lógica

| Componente | Responsabilidade (uma frase) | Plataforma (da §5) | Responsável |
| --- | --- | --- | --- |
| `CANAL` — canal digital ou mediado | Verifica a identidade do titular e emite a asserção da §2.2 | Fora desta AR | Time de canal digital |
| `AGW-AXW` — borda Axway | Ponto único de entrada externa, autentica o consumidor exatamente uma vez e gera o identificador de correlação | `AGW-AXW` | Technical Architect / operação do Axway |
| `API-DT` — API de pedidos | Registra o pedido, devolve protocolo, responde consulta de andamento, entrega o resultado | `OCP` | Time de entrega do serviço |
| `SVC-DT` — orquestrador de pedidos | Máquina de estados do caso: resolve o escopo, aciona executores, coleta confirmações, calcula prazos, fecha com evidência | `OCP` | Time de entrega do serviço |
| `REG-PED` — registro de pedidos | Sistema de registro do pedido, do seu estado e da evidência de atendimento | `DATA-REL` no `DC-AA` | Time de entrega do serviço |
| `CAT-DOM` — catálogo de domínios de dado pessoal | Diz **quais** domínios podem conter dado pessoal do titular, quem é o dono, qual o executor, e qual a retenção legal aplicável | `DATA-REL` no `DC-AA` | DPO (conteúdo) + Solution Architect (modelo) |
| `EXEC-<dom>` — executor de domínio | Adaptador **dentro** do domínio que aplica acesso, correção ou eliminação no dado daquele domínio e devolve confirmação | Plataforma do próprio domínio | Time dono do domínio |
| `EXEC-CDE` — executor dentro do enclave | Executor de domínio que vive **dentro** do enclave PCI-DSS e nunca devolve PAN para fora dele | `DC-AA`, dentro do enclave | Time dono do domínio de cartão, com Security Architect |
| `LST-SUP` — lista de supressão | Guarda **quais chaves** foram eliminadas, em forma pseudonimizada, para bloquear restauração e permitir reaplicação | `DATA-REL` no `DC-AA`, domínio próprio | Time de entrega do serviço |
| `GRD-RST` — guarda de restauração | Controle no processo de restauração que consulta `LST-SUP`, bloqueia restauração seletiva de chave eliminada e dispara a reaplicação pós-restore | `VM-ON` no `DC-AA`, junto à plataforma de backup | Infrastructure Architect |
| `ART-PORT` — artefato de portabilidade | Pacote de dado para portabilidade, cifrado, de vida curta, entregue por referência e não embutido na resposta da API | `VM-ON` / armazenamento de objeto on-premises | Time de entrega do serviço |
| `PLT-LOG` — plataforma central de trilha | Recebe os eventos de auditoria do pedido e da eliminação | `DC-AA` (`ADR-0015` C2/C3) | Security Architect |

> **Nota de altitude.** `SVC-DT` **orquestra**; ele **não** apaga dado de domínio alheio. Quem apaga é o `EXEC-<dom>` do time dono do domínio. Um orquestrador com credencial de escrita direta nos bancos de dados de N domínios é uma concentração de privilégio que nenhuma ADR autoriza e que `ADR-0012` C5/C6 tornaria indefensável (credencial de longa duração atravessando fronteira de ambiente).

### 3.2 Diagrama

Diagrama formal não produzido nesta versão 0.1 — declaro isso em vez de prometê-lo. O fluxo textual da §4 e a tabela da §3.1 são a descrição normativa. Quando o diagrama existir, ele **MUST NOT** contradizer a §3.1 nem a §5 (exigência do template de AR §3).

### 3.3 Fronteiras de confiança

| Fronteira | O que a atravessa | Controle |
| --- | --- | --- |
| Internet → Zona Pública/DMZ | Pedido do titular, com a asserção da §2.2 | `AGW-AXW` (`ADR-0010` C1), terminação em DMZ (`ADR-0014` C5) |
| DMZ → zona de aplicação | Chamada já autenticada para `API-DT` | `ADR-0014` C5 (gateway não termina na zona de aplicação) |
| `SVC-DT` → `EXEC-<dom>` no mesmo ambiente | Comando de execução por domínio | mTLS / service mesh sem gateway (`ADR-0010` C4) |
| `SVC-DT` → `EXEC-<dom>` em outro ambiente (nuvem) | Mesmo comando, atravessando fronteira | Axway como ponto único de travessia (`ADR-0010` C5) + federação de identidade de workload (`ADR-0012` C5), **nunca** chave estática |
| Zona de aplicação → **enclave PCI-DSS** | Comando para `EXEC-CDE` e resposta **sem PAN** | `ADR-0011` C7/C8, `ADR-0010` C9/C10, `ADR-0014` C6. **Esta é a fronteira que determina o efeito desta AR sobre o escopo PCI — ver §8.2** |
| `GRD-RST` → `LST-SUP` | Consulta de chave eliminada durante restauração | Domínio administrativo de backup é **distinto** do de produção (`ADR-0009` C8(a)) — ver **N3** |
| Qualquer componente → `PLT-LOG` | Eventos de auditoria | `ADR-0015` C4 (coletor local com store-and-forward) |

---

## 4. Fluxos de ponta a ponta

### 4.1 Caminho feliz — registro de qualquer pedido

1. O titular (ou o operador em seu nome) é verificado no `CANAL` (§2.2). O canal emite a asserção.
2. O pedido chega ao `AGW-AXW`, que autentica o consumidor **uma vez** (`ADR-0010` C7) e **gera o identificador de correlação** (`ADR-0010` C8). Esse identificador é o eixo de toda a evidência deste caso e **MUST NOT** ser regerado adiante.
3. `API-DT` valida o contrato do pedido: tipo de direito, escopo pedido, asserção presente com método e nível de garantia (§2.2). Pedido sem nível de garantia declarado é **rejeitado**, não processado com um default.
4. `SVC-DT` grava o caso em `REG-PED`: protocolo, titular, tipo de direito, escopo, identidade verificada (método, nível, timestamp), identidade do operador se houver, identificador de correlação, data/hora de recebimento, **e o prazo-alvo de resposta** calculado pela tabela de prazos configurada (§4.7, **N1**).
5. `SVC-DT` resolve o escopo em `CAT-DOM`: a lista de domínios que podem conter dado pessoal deste titular, com dono, executor e **retenção legal aplicável** por domínio.
6. **Limite da transação:** o passo 4 é a única fronteira transacional síncrona do fluxo. Dali em diante o caso é assíncrono, por domínio, com estado em `REG-PED`. A resposta síncrona ao titular é **o protocolo e o prazo**, nunca o resultado.
7. Evento de auditoria emitido a cada transição de estado (`ADR-0015` C1, classe "auditoria de transação de negócio"), com identidade e correlação (`ADR-0015` C8).

### 4.2 Acesso e confirmação de tratamento

1. Passos 1–7 da §4.1.
2. `SVC-DT` aciona cada `EXEC-<dom>` em modo **leitura**, pedindo o conjunto **mínimo** definido no contrato de resposta do domínio (§6.3) — não "tudo o que existe".
3. `EXEC-CDE`, quando acionado, devolve **PAN mascarado** (últimos quatro dígitos), nunca PAN em claro, nunca SAD (`ADR-0011` C7/C8, `ADR-0015` C5/C6, PCI-DSS família 3).
4. `SVC-DT` compõe a resposta e a disponibiliza por **referência** (o titular busca o resultado autenticado no canal), não por anexo em canal não controlado.
5. Caso fechado em `REG-PED` com evidência: quais domínios responderam, quando, e o que foi entregue — **sem** copiar o conteúdo entregue para dentro de `REG-PED` (§6.3, `C-DT-09`).

### 4.3 Eliminação — o fluxo que a decisão de [ARC-46](/ARC/issues/ARC-46) habilita

Este é o fluxo central desta AR.

1. Passos 1–7 da §4.1, com nível de garantia de identidade **maior** do que o de acesso (`C-DT-02`).
2. `SVC-DT` resolve o escopo em `CAT-DOM`.
3. **Filtro de retenção legal.** Para cada domínio, `CAT-DOM` declara se há **obrigação legal ou regulatória de retenção** que se sobreponha ao pedido (guarda de registro bancário sob BACEN/CMN, retenção de dado de transação de cartão, obrigação fiscal, processo judicial em curso). O conteúdo desse campo é do DPO/Jurídico, **não** desta AR nem do time (**N8**). Domínio com retenção legal ativa **não** é eliminado: é **marcado como retido com motivo**, e o motivo entra na resposta ao titular. Eliminar dado sob obrigação legal porque o titular pediu é tão errado quanto não eliminar o que deve ser eliminado.
4. **Eliminação no domínio ativo.** Para cada domínio elegível, `EXEC-<dom>` aplica a eliminação **no primário único de escrita daquele domínio** (`ADR-0011` C6, herdado de `ADR-0006`). Não há escrita no sítio secundário: ela chega por replicação.
5. **Confirmação nos dois sítios.** `EXEC-<dom>` só confirma a `SVC-DT` quando a eliminação está observável **nos dois sítios** do `DC-AA`. Como a replicação entre sítios é **assíncrona com RPO > 0** até o RTT ser medido (`ADR-0006` C7, `ADR-0011` C11, `ADR-0006` Q1), existe um intervalo em que o registro já não está no primário e ainda está no secundário. Esse intervalo é **parte do atendimento**, não um detalhe de infraestrutura: declarar "eliminado" antes dele é uma afirmação falsa. Se a confirmação do segundo sítio não vier dentro do limite configurado, o caso vai para `pendente-convergência`, com alerta — não para `atendido`.
6. **Inscrição na lista de supressão.** `SVC-DT` grava em `LST-SUP`, **antes** de fechar o caso: a chave do registro eliminado em forma **pseudonimizada** (`C-DT-03`), o domínio, a data da eliminação, o protocolo do pedido, e a **data prevista de eliminação definitiva** (passo 8). A ordem importa: inscrever **depois** de fechar o caso abre uma janela em que um restore reintroduz um registro que o banco já declarou eliminado.
7. **O backup imutável não é tocado.** Nenhum passo deste fluxo apaga, encurta ou tenta contornar a trava de tempo da cópia imutável. `ADR-0009` C8 é `MUST` com retenção mínima de 35 dias corridos, e C9 depende dessa cópia para a recuperação contra corrupção lógica e ransomware. A cópia que contém o registro eliminado permanece íntegra até expirar por si.
8. **Data prevista de eliminação definitiva.** `SVC-DT` calcula, por domínio: `data da última cópia imutável que contém o registro` + `retenção configurada da cópia naquele domínio`. O **máximo** entre os domínios é a data prevista de eliminação definitiva do pedido. Essa data depende da retenção **efetiva** de cada domínio, e `ADR-0009` C8 fixa apenas um **piso** de 35 dias, sem teto — por isso a data não é universalmente 35 dias, e por isso **N6** existe.
9. **Resposta ao titular, em duas etapas declaradas.** Dentro do prazo legal, a resposta diz, em linguagem comum: (a) o que foi eliminado dos sistemas ativos e em que data; (b) o que **não** foi eliminado e por qual obrigação legal (passo 3); (c) que existem cópias de segurança imutáveis, com trava de tempo, mantidas por obrigação de resiliência cibernética; (d) que essas cópias **expiram até a data prevista** e que o registro eliminado **não será restaurado a partir delas** — com o bloqueio e a reaplicação como o mecanismo que garante (c)+(d). Prometer eliminação instantânea e total seria a afirmação que a interpretação de [ARC-46](/ARC/issues/ARC-46) justamente dispensa, e que o desenho não cumpre.
10. **Fechamento definitivo.** Na data prevista, `SVC-DT` verifica a expiração efetiva da última cópia (evidência da plataforma de backup) e move o caso para `eliminação-definitiva-confirmada`. A entrada em `LST-SUP` **permanece** — ver `C-DT-06` e §4.6.
11. Eventos de auditoria em cada transição (`ADR-0015` C1/C8).

### 4.4 Correção e portabilidade

**Correção:** igual à §4.3 nos passos 1–5, substituindo "eliminar" por "atualizar", **sem** inscrição em `LST-SUP` — correção não é eliminação, e um registro corrigido deve voltar corrigido de um restore, não ausente. Consequência que o desenho precisa assumir: **um restore completo reverte uma correção** aplicada depois da cópia restaurada. O tratamento disso é o mesmo da §4.6 — reprocessamento das correções aplicadas após a data da cópia, a partir de `REG-PED` — e é por isso que `REG-PED` precisa guardar **que** houve correção e **em qual campo**, ainda que não guarde o valor (§6.3).

**Portabilidade:** igual à §4.2 nos passos 1–4, com três diferenças: (a) o resultado é um `ART-PORT` em formato estruturado legível por máquina; (b) o artefato é **cifrado em repouso** com chave sob `ADR-0013`, tem **vida curta** e é entregue por referência autenticada, nunca por anexo; (c) dado de cartão **não** vai no artefato em forma reversível — vale `ADR-0011` C7 integralmente, e o artefato carrega PAN mascarado. O **formato** concreto é decisão local (§10.1); quando o domínio for de open finance, o padrão de interoperabilidade daquele regime prevalece sobre qualquer escolha local.

### 4.5 Caminho de falha primário

| Falha | O que o titular experimenta | O que o sistema faz |
| --- | --- | --- |
| Um `EXEC-<dom>` indisponível | Nada muda no prazo: o protocolo e o prazo-alvo já foram dados | Caso fica `parcial`, com retentativa idempotente. O prazo legal **não para**; a aproximação do prazo-alvo escala ao DPO (`C-DT-10`) |
| `CAT-DOM` incompleto — existe domínio com dado pessoal fora do catálogo | O titular recebe uma resposta que **parece** completa e não é | **O modo de falha mais grave desta AR.** Mitigação: `CAT-DOM` é reconciliado contra o inventário de domínios de dado persistido de `ADR-0011` C9 e, quando existir, contra o esquema de [ARC-8](/ARC/issues/ARC-8); a divergência é **achado de auditoria**, não ajuste silencioso. Não é fechável por esta AR — **N7** |
| Pedido de eliminação sobre dado que também é log de auditoria | Resposta declara o dado como retido, com motivo | `ADR-0011` C10 proíbe usar C9 para descarte antecipado de log de auditoria; o prazo é de `ADR-0015` C11. Ver lacuna **L8** de `AR-001`: a tabela de retenção invocada por `ADR-0015` C11 **não está presente** na §5 daquela ADR, então esta AR não pode citar prazo |
| `LST-SUP` indisponível | Invisível ao titular | **Nenhuma eliminação nova é confirmada** enquanto `LST-SUP` não aceitar escrita (passo 6 da §4.3 é pré-condição de fechamento). E nenhuma restauração é liberada — ver §4.6 |
| Asserção de identidade com nível de garantia abaixo do exigido | Pedido rejeitado com motivo, com instrução de reautenticar no nível adequado | `C-DT-02`. Nunca "processar com o que veio" |

### 4.6 Restauração — bloqueio e reaplicação (item 2 do escopo desta AR)

Este fluxo é a **segunda condição** da interpretação de [ARC-46](/ARC/issues/ARC-46). Sem ele, a primeira não vale.

**4.6.a — Restauração seletiva de registro individual: proibida.**

1. Qualquer pedido de restauração de registro individual — operação, suporte, negócio, time dono do domínio — passa por `GRD-RST`.
2. `GRD-RST` consulta `LST-SUP` pela chave pseudonimizada.
3. Chave presente → **restauração negada**, sem exceção operacional e sem via de override por privilégio. É `C-DT-04`, `MUST NOT`. Um override para "um caso urgente" é exatamente a via pela qual um registro eliminado volta a existir.
4. A negativa é um **evento de segurança** (`ADR-0015` C1), não apenas um log de aplicação: alguém tentou recuperar um dado eliminado.

**4.6.b — Restauração completa, por outro motivo (corrupção lógica, ransomware, teste de `ADR-0009` C9).**

1. A restauração ocorre normalmente, a partir da cópia imutável de C8. **Esta AR não interfere no RPO nem no procedimento de restauração** — interferir seria legislar sobre `ADR-0009`.
2. O ambiente restaurado entra em **quarentena**: sem rota de aplicação, sem publicação em gateway, sem consumidor. Não é "produção restaurada" ainda.
3. `GRD-RST` dispara a **reaplicação**: para cada chave de `LST-SUP` pertinente ao domínio restaurado e com data de eliminação **anterior ou igual** à data do dado restaurado, a eliminação é reaplicada no dado restaurado.
4. **Correções** aplicadas após a data da cópia são reprocessadas a partir de `REG-PED` (§4.4), no mesmo passo.
5. A quarentena só é levantada com **evidência de conclusão** da reaplicação: contagem de chaves processadas, contagem de divergências, e assinatura do responsável do plantão. É `C-DT-05`, `MUST`.
6. Evento de auditoria do lote de reaplicação (`ADR-0015` C1/C8), correlacionado a cada protocolo de pedido afetado em `REG-PED`.

**Três consequências que esta AR precisa declarar em voz alta:**

- **(i) A reaplicação consome RTO.** `ADR-0009` C9 fixa RTO de restauração após corrupção lógica em **≤ 4 h (Tier 0)** e **≤ 8 h (Tier 1)**. O passo 3 acima é um passo **novo**, obrigatório pela interpretação de [ARC-46](/ARC/issues/ARC-46), que nenhuma dessas contas inclui — e a quarentena do passo 2 significa que ele está **no caminho crítico**, não em paralelo. Esta AR não pode alterar o orçamento de RTO de `ADR-0009`. Lacuna **N3**.
- **(ii) `LST-SUP` precisa ser recuperável ANTES do dado que ela limpa.** Se o incidente que motivou o restore também atingiu `LST-SUP`, a reaplicação é impossível e a quarentena não pode ser levantada — o banco fica sem poder restaurar, ou restaura reintroduzindo dado eliminado. Portanto `LST-SUP` tem sua **própria** cópia imutável sob C8, em ordem de recuperação **anterior** à dos domínios que ela governa (`C-DT-08`).
- **(iii) `LST-SUP` é um serviço compartilhado novo.** Pela leitura de `ADR-0009` C11, a capacidade de restaurar um domínio Tier 1 passa a depender de `LST-SUP`; logo `LST-SUP` **MUST** ter tier **≥** o maior tier entre os domínios que governa, ou ela rebaixa todos eles. `LST-SUP` **não** está no inventário de serviços compartilhados do `DC-AA` (`ADR-0006` Q5, [ARC-43](/ARC/issues/ARC-43)), porque ainda não existia quando o inventário foi definido. Lacuna **N4**.

### 4.7 Prazo legal e evidência de atendimento (item 4 do escopo desta AR)

| Elemento | Desenho desta AR |
| --- | --- |
| **Relógio do prazo** | Inicia no **recebimento** registrado em `REG-PED` (§4.1 passo 4), não na triagem. Fuso e calendário (dias corridos vs. úteis) são parâmetros de configuração, não constantes de código |
| **Prazo por tipo de direito** | **Parametrizado, não fixado por esta AR.** O único prazo numérico que o Escritório lê com segurança no texto da LGPD, em nível geral, é o de **15 dias** para a resposta completa a pedido de confirmação de tratamento e de acesso (art. 19, II e §2º). Para eliminação, correção e portabilidade a lei não fixa um prazo numérico único de forma igualmente clara, e **o Escritório não vai inventar um**. A tabela de prazos é insumo do DPO/Jurídico — lacuna **N1**. Arquiteturalmente, o que importa é que o prazo seja **configurável por tipo de direito**, auditável, e que a aproximação dele gere alerta |
| **Alerta de prazo** | Marcos configuráveis (ex.: 50% e 80% do prazo-alvo) escalam ao DPO. É `C-DT-10` |
| **Evidência de atendimento** | Em `REG-PED`, por caso: protocolo, identidade verificada (método, nível, timestamp), identidade do operador se houver, identificador de correlação, escopo resolvido com a versão de `CAT-DOM` usada, confirmação **por domínio** com timestamp e confirmação de convergência entre sítios, domínios retidos **com motivo legal**, data prevista e data confirmada de eliminação definitiva, e a resposta enviada ao titular com data de envio |
| **Trilha independente** | A mesma cadeia de eventos vai para `PLT-LOG` (`ADR-0015` C1/C2/C8), que é protegida contra adulteração (C9) e onde o administrador não tem via padrão de exclusão (C10). `REG-PED` é o estado; `PLT-LOG` é a trilha. Um caso em que as duas divergem é achado de auditoria |
| **Retenção da evidência** | A evidência de atendimento **não** é eliminada pelo pedido que a originou: ela é mantida por interesse legítimo de demonstração de conformidade e por obrigação de guarda — e é exatamente por isso que `LST-SUP` guarda **chave pseudonimizada** e `REG-PED` guarda **metadado**, não o dado pessoal eliminado (§6.3, `C-DT-03`, `C-DT-09`). Essa é a diferença entre guardar a prova de que se eliminou e não ter eliminado. O **prazo** de retenção dessa evidência é insumo do DPO — **N1** |

### 4.8 Failover de sítio

- **Postura:** `active-active` em leitura e compute; **primário único de escrita** por domínio (`ADR-0011` C6, herdado de `ADR-0006`). `REG-PED` e `LST-SUP` seguem exatamente essa postura — são sistemas de registro por `ADR-0011` C1 e não admitem multi-master (`ADR-0011` C4/C6).
- **Perda de um sítio do `DC-AA`:** failover controlado, **promoção não automática** por padrão, com fencing obrigatório (`ADR-0006` C9); promoção automatizada só com confirmação de quorum por testemunha em terceiro domínio de falha (`ADR-0006` C10), cuja contratação o board aprovou em 2026-10-05 ([ARC-46](/ARC/issues/ARC-46)) e que **não está ativável em produção** nesta data (prazo estimado de 4–5 meses, [ARC-42](/ARC/issues/ARC-42)). Até então, o failover é manual pelo papel de plantão, pelo [runbook de promoção manual](/ARC/issues/ARC-45#document-runbook-promocao-manual-sitio).
- **Transações em andamento:** um pedido em `pendente-convergência` no momento do failover **MUST NOT** ser promovido a `atendido` pelo failover. A confirmação de dois sítios é reavaliada após a convergência. Um pedido cuja eliminação foi aplicada no primário e não replicada no instante da perda do sítio pode **regredir** — RPO > 0 significa isso literalmente. O caso volta para `pendente-convergência` e a eliminação é **reaplicada**, pelo mesmo mecanismo da §4.6.b. É o mesmo controle resolvendo um segundo problema, e é a razão de ele ser um componente e não um script.
- **Perda de uma região de nuvem:** afeta apenas `EXEC-<dom>` hospedado em nuvem. O caso fica `parcial` com retentativa; o núcleo do fluxo (`REG-PED`, `LST-SUP`, `SVC-DT`) é on-premises por desenho (§5).

---

## 5. Realização na plataforma

| Plataforma | Nesta referência? | O que você implanta / como difere aqui |
| --- | --- | --- |
| `DC-AA` — DC on-prem, active-active | **Sim — padrão** | Núcleo do fluxo: `REG-PED`, `LST-SUP`, `CAT-DOM`, `GRD-RST`. Replicação assíncrona entre sítios, RPO > 0 (`ADR-0006` C7) |
| `OCP` — OpenShift on-prem | **Sim — padrão** | `API-DT` e `SVC-DT` como serviços stateless em container (`ADR-0008` C2, `ADR-0004` C1) |
| `VM-ON` — VMs / bare metal on-prem | **Sim** | `GRD-RST` junto à plataforma de backup e o armazenamento de `ART-PORT`. `ADR-0008` C5/C8 fixa motor de dado e componente de infraestrutura em VM por padrão |
| `AWS-K8S` — Kubernetes gerenciado na AWS | **Sim, só para `EXEC-<dom>`** | Executor de um domínio que já vive na AWS. Alcançado via Axway (`ADR-0010` C5) com federação de identidade de workload (`ADR-0012` C5) |
| `AWS-VM` — compute AWS | **Sim, só para `EXEC-<dom>`** | Idem |
| `AZR-K8S` — Kubernetes gerenciado no Azure | **Sim, só para `EXEC-<dom>`** | Idem |
| `AZR-VM` — compute Azure | **Sim, só para `EXEC-<dom>`** | Idem |
| `OCI-K8S` — Kubernetes gerenciado na OCI | **Sim, só para `EXEC-<dom>`** | Idem. Atenção a `ADR-0010` Q6: não há gateway in-cloud atribuído na OCI |
| `OCI-VM` — compute OCI | **Sim, só para `EXEC-<dom>`** | Idem |
| `DATA-REL` — bases relacionais | **Sim — padrão** | `REG-PED`, `LST-SUP`, `CAT-DOM`. São sistemas de registro: relacional por padrão (`ADR-0011` C1), desvio exige revisão do Technical Architect (C2) |
| `DATA-NREL` — bases não relacionais | **Não para o núcleo** | Permitido apenas como **origem** alcançada por `EXEC-<dom>`, quando o domínio já usa esse motor dentro de `ADR-0011` C3. O núcleo do fluxo não usa motor não relacional |
| `AGW-AWS` — AWS API Gateway | **Não** | `ADR-0010` C3: nenhuma rota alcançável da internet. Pedido de titular é consumidor externo |
| `AGW-AZR` — Azure API Management | **Não** | Idem |
| `AGW-AXW` — Axway API Gateway | **Sim — borda única** | `ADR-0010` C1/C2/C7/C8. Compõe com [`AR-001`](/ARC/issues/ARC-9#document-ar-001-api-exposta-externamente) |

**Divisão entre container e VM.** `API-DT` e `SVC-DT` são **containers** em `OCP`: stateless, escalam com o volume de pedidos, ciclo de release rápido (`ADR-0008` C2). `REG-PED`, `LST-SUP` e `CAT-DOM` são **motores de dado em VM** (`ADR-0008` C5/C8). `GRD-RST` é **VM**, por viver no domínio administrativo da plataforma de backup, que `ADR-0009` C8(a) exige **separado** do de produção — um `GRD-RST` em container no mesmo `OCP` de produção violaria essa separação, e é um erro fácil de cometer.

**Combinação padrão e variantes.** A combinação **padrão é única**: núcleo on-premises (`OCP` + `DC-AA`/`DATA-REL`), borda no Axway, executores onde o domínio já está. Não há variante em nuvem para o núcleo, e isso **não** é conservadorismo:

- `REG-PED` e `LST-SUP` são sistemas de registro de um processo com prazo legal e são **pré-requisito de restauração** de domínios Tier 1 (§4.6, consequência iii). Um workload Tier 0/1 em nuvem não pode reivindicar o tier hoje — regra interina de `ADR-0009` Q3 e decisão **D-8**.
- `GRD-RST` tem de estar junto à plataforma de backup, que é on-premises.
- Executor de domínio **de cartão** é obrigatoriamente `EXEC-CDE` dentro do enclave no `DC-AA`: nenhuma landing zone de nuvem tem enclave PCI-DSS aprovado (`ADR-0011` Q2, `ADR-0003` §9 Q1).

Um time que receba três realizações igualmente endossadas recebeu um cardápio. Aqui há uma.

---

## 6. Preocupações transversais

### 6.1 Cláusulas próprias desta AR

Uma AR não decide; ela **compõe**. As dez linhas abaixo não criam regra nova: cada uma é a aplicação de uma cláusula de ADR já proposta, ou a implementação literal de uma das duas condições que o board fixou em [ARC-46](/ARC/issues/ARC-46). Onde eu precisaria de uma regra que não existe, está na §10.3 como lacuna — não aqui como cláusula.

| # | Prescrição | Força | Vem de |
| --- | --- | --- | --- |
| `C-DT-01` | `SVC-DT` e `API-DT` **MUST NOT** autenticar o titular, armazenar credencial de titular, ou manter repositório de credencial de cliente. A verificação é do canal; a validação do consumidor é do Axway | `MUST NOT` | `ADR-0012` §4.2 (exclusão), `ADR-0010` C7 (autenticação exatamente uma vez na borda) |
| `C-DT-02` | Todo pedido **MUST** carregar método e nível de garantia de identidade. Eliminação e portabilidade **MUST** exigir nível estritamente maior do que consulta de andamento. Pedido sem nível declarado é rejeitado, nunca processado com default | `MUST` | §2.2; a **escala** é lacuna **N2** |
| `C-DT-03` | `LST-SUP` **MUST** guardar a chave em forma **pseudonimizada irreversível sem segredo** (ex.: função unidirecional com sal corporativo custodiado sob `ADR-0013`), e **MUST NOT** guardar atributo pessoal do titular além do necessário para casar a chave em um restore | `MUST` / `MUST NOT` | `ADR-0015` C7 (identificador, não atributo), aplicado por analogia direta; minimização sob `OBL-LGPD` |
| `C-DT-04` | Restauração **seletiva** de registro cuja chave consta em `LST-SUP` **MUST NOT** ocorrer, por nenhuma via, inclusive administrativa privilegiada. A tentativa é evento de segurança | `MUST NOT` | Condição 1 da interpretação de [ARC-46](/ARC/issues/ARC-46); `ADR-0015` C1/C12 |
| `C-DT-05` | Após restauração **completa**, o ambiente restaurado **MUST** permanecer em quarentena — sem rota de aplicação e sem consumidor — até a reaplicação das eliminações concluir com evidência de contagem e divergências | `MUST` | Condição 2 da interpretação de [ARC-46](/ARC/issues/ARC-46) |
| `C-DT-06` | A entrada em `LST-SUP` **MUST NOT** ser removida na confirmação da eliminação definitiva. Ela é a prova e o bloqueio; removê-la reabre a restauração de uma cópia que ainda possa existir fora do ciclo previsto | `MUST NOT` | `ADR-0009` C8 (não há garantia de cópia única); §4.7 |
| `C-DT-07` | Pedido registrado por operador em nome do titular **MUST** gravar **as duas** identidades, e o operador **MUST** autenticar-se por identidade interna federada com MFA | `MUST` | `ADR-0012` C1/C2 |
| `C-DT-08` | `LST-SUP` **MUST** ter cópia imutável própria sob `ADR-0009` C8, e **MUST** constar no procedimento de recuperação em ordem **anterior** à dos domínios que governa | `MUST` | §4.6 consequência (ii); `ADR-0009` C8/C9 |
| `C-DT-09` | `REG-PED` **MUST** guardar metadado de atendimento, e **MUST NOT** guardar cópia do dado pessoal entregue ao titular nem do dado eliminado. Um registro de pedidos que acumula o que foi eliminado é a própria falha que o fluxo existe para evitar | `MUST` / `MUST NOT` | Minimização sob `OBL-LGPD`; `ADR-0015` C7 por analogia |
| `C-DT-10` | O prazo-alvo de resposta **MUST** ser configurável por tipo de direito, e sua aproximação **MUST** gerar alerta ao DPO em marcos configuráveis | `MUST` | §4.7; os **valores** são lacuna **N1** |

### 6.2 Tabela transversal do template

| Preocupação | O que esta referência prescreve | De qual ADR |
| --- | --- | --- |
| Identidade e acesso (workload e humano) | Titular: verificado no canal, fora de escopo (`C-DT-01`). Operador: federado com MFA (`C-DT-07`). Workload: identidade nativa local, federação na travessia de ambiente, nenhuma credencial estática, vida ≤ 24 h. Administração de `LST-SUP`/`GRD-RST` sob PAM com elevação just-in-time | `ADR-0012` C1–C6, §4.2 |
| Segredos e gestão de chaves | Sal corporativo da pseudonimização de `C-DT-03` e chave de cifragem de `ART-PORT` sob custódia de `ADR-0013`; nenhum segredo em configuração versionada ou variável de pipeline (`ADR-0013` C4) | `ADR-0013` |
| Segmentação e exposição de rede | Borda termina na Zona Pública/DMZ, nunca na zona de aplicação; administração de backup pela Zona de Gestão; enclave PCI alcançado apenas por `EXEC-CDE` | `ADR-0014` C5, C6 |
| Exposição de API | Axway como borda única, autenticação do consumidor exatamente uma vez, identificador de correlação gerado na borda e **nunca** regerado; nenhuma rota em gateway de nuvem | `ADR-0010` C1, C3, C7, C8 |
| Dados: store, schema, retenção, classificação | `REG-PED`/`LST-SUP`/`CAT-DOM` são sistemas de registro relacionais; classificação e critério de descarte declarados por domínio; **esta AR é o mecanismo pelo qual C9 não se sobrepõe a pedido já atendido** | `ADR-0011` C1, C6, C9 |
| Proteção em trânsito e em repouso | TLS na borda, mTLS entre serviços, IPsec no caminho híbrido, cifragem em repouso de `REG-PED`/`LST-SUP`/`ART-PORT`. **Criptografia no link inter-sítio do `DC-AA` para dado de CDE permanece lacuna L4 de `AR-001`** | `ADR-0007` C11, `ADR-0013`; lacuna **L4** de `AR-001` |
| Observabilidade: logs, métricas, traces | Lado de segurança por `ADR-0015`. Observabilidade operacional (métricas, traces, SLO, APM) é lacuna consciente **G1-op**: `Não endereçado` — decidido localmente | `ADR-0015`; **G1-op** (§10.2) |
| Auditoria e rastreabilidade de transação de negócio | Cada transição de estado é evento de auditoria de transação de negócio, com identidade, correlação e timestamp sincronizado; trilha protegida contra adulteração, sem via de exclusão administrativa | `ADR-0015` C1, C2, C8, C9, C10 |
| Configuração e deployment | `Não endereçado` — lacuna consciente **G3** | **G3** (§10.2) |
| Promoção entre ambientes | `Não endereçado` — lacuna consciente **G3**. Efeito concreto em escopo PCI: mesma leitura da lacuna **L7** de `AR-001` | **G3**; **L7** de `AR-001` |

### 6.3 Minimização de dado pessoal no contrato da API (item 3 do escopo desta AR)

D-11 posição 2 exige este tema como **seção própria**, e não como "fora de escopo". Então eu **decido o que está ao meu alcance** e **declaro o que não está** — em vez de declarar o todo fora de escopo com uma justificativa de conveniência.

`ADR-0010` Q7 está aberta nestes termos:

> "Minimização de dado pessoal no contrato da API: uma API pode cumprir todas as cláusulas desta ADR e ainda expor mais dado pessoal do que a finalidade exige. Isso é desenho de contrato de API e não tem decisão de primeira onda."

**O que esta AR decide (dentro do seu alcance):** a minimização nos contratos **deste fluxo**, porque eles são meus.

| Regra de minimização | Força | Por quê |
| --- | --- | --- |
| O contrato de `API-DT` transporta **protocolo, tipo de direito, escopo e asserção** — e **nenhum** atributo pessoal além do identificador do titular necessário para resolver o escopo | `MUST` | Um pedido de eliminação não precisa transportar o dado a eliminar |
| O contrato de `EXEC-<dom>` em modo leitura declara, **por domínio**, o conjunto **mínimo** de campos que responde à finalidade — e `SVC-DT` **MUST** recusar resposta com campo fora desse conjunto, em vez de repassá-la | `MUST` | Impede que "devolver tudo" chegue ao titular por omissão do orquestrador |
| O resultado de acesso e o `ART-PORT` são entregues **por referência autenticada e de vida curta**, nunca embutidos na resposta síncrona da API nem em anexo de canal não controlado | `MUST` | Reduz a superfície de dado pessoal em cache, log e histórico de gateway, na mesma linha de `ADR-0010` C11 |
| `REG-PED` e `LST-SUP` guardam **metadado e chave pseudonimizada**, nunca o dado pessoal (`C-DT-03`, `C-DT-09`) | `MUST` | A evidência de eliminação não pode ser uma cópia do que foi eliminado |
| Nenhum atributo pessoal além de identificador vai para o evento de auditoria; atributo adicional é obtido por consulta à origem no momento da investigação | `MUST` | `ADR-0015` C7, aplicado sem reinterpretação |
| Dado de cartão sai do enclave apenas **mascarado**; PAN em claro e SAD nunca | `MUST` | `ADR-0011` C7/C8, `ADR-0010` C10, `ADR-0015` C5/C6 |

**O que esta AR NÃO decide, e declaro explicitamente:** o **padrão corporativo de minimização para todo contrato de API do banco**. Isso é uma regra transversal sobre como todo time desenha contrato de API, com gate de revisão e critério de verificação — ou seja, é **uma ADR**, não uma AR. Decidi-la aqui seria o Solution Architect legislando sobre `ADR-0010` por via de exemplo, que é exatamente o que o template de AR proíbe ("se uma AR precisa tomar uma nova decisão para ficar completa, pare").

Portanto: **`ADR-0010` Q7 permanece aberta depois desta AR**, fechada apenas para este arquétipo. Encaminho como candidata a ADR de segunda onda — lacuna **N5**. Registro também que a redação de D-11 ("incluindo a minimização no contrato da API (`ADR-0010` Q7) como seção própria") é satisfeita por esta seção, mas que a **Q7 em si** não se fecha com uma seção de AR; isso é uma informação que o Arquiteto Principal precisa ter, não uma licença para eu alargar o escopo.

---

## 7. Resiliência e recuperação

O fluxo tem **dois** perfis de criticidade, e tratá-los como um só é um erro de desenho.

| Atributo | `REG-PED` + `SVC-DT` + `API-DT` (atendimento) | `LST-SUP` + `GRD-RST` (bloqueio/reaplicação) |
| --- | --- | --- |
| Criticidade de negócio | Prazo legal medido em **dias** → **Tier 2** pela escala de `ADR-0009` §4 | Pré-requisito de restauração de domínio Tier 1 → **Tier 1** por `ADR-0009` C11 (§4.6, consequência iii) |
| Tier **alvo de desenho** | Tier 2 | Tier 1 |
| Tier **reivindicável hoje** | Tier 2 | **Tier 1** — e nunca Tier 0, por **D-7** |
| RTO | ≤ 8 h (Tier 2) | ≤ 1 h (Tier 1) |
| RPO | ≤ 4 h (Tier 2) | ≤ 15 min (Tier 1) |
| Postura | Active-active em leitura/compute, **primário único de escrita** | Idem |
| Perda de um sítio do `DC-AA` | Failover controlado, promoção manual com fencing (`ADR-0006` C9); testemunha de quorum (C10) aprovada e **não ativável** nesta data | Idem, com prioridade de recuperação **anterior** (`C-DT-08`) |
| Perda de região de nuvem | Afeta só `EXEC-<dom>` em nuvem; caso fica `parcial` com retentativa | Não afetado — é on-premises |
| Replicação e consistência | Assíncrona entre sítios, **RPO > 0** até o RTT ser medido (`ADR-0006` C7, Q1; `ADR-0011` C11) | Idem — e é por isso que a §4.8 exige reavaliar `pendente-convergência` após failover |
| Janela de perda de dado conhecida | Sim: até o RPO do tier. Um pedido registrado e não replicado pode ser perdido no failover — o titular teria protocolo de um caso inexistente. Mitigação: reconciliação contra `PLT-LOG` (§4.7) | Sim: uma eliminação inscrita e não replicada pode regredir, e é reaplicada pelo mecanismo da §4.6.b |

> **Reserva obrigatória por D-7, item 2.** A conformidade com `ADR-0009` C11 (herança de tier por dependência de serviço compartilhado) **não está verificada para tier algum**, porque o inventário de serviços compartilhados do `DC-AA` não existe (`ADR-0006` Q5, regra interina: serviço não inventariado é violação de C1 não confirmada). O tier declarado acima é **condicional** a essa auditoria cruzada. Esta AR agrega um agravante próprio: ela **introduz** um serviço compartilhado novo (`LST-SUP`) que não está nesse inventário — lacuna **N4**.

**Modos de falha e resposta:**

| Modo de falha | Detecção | Resposta automática | Resposta manual |
| --- | --- | --- | --- |
| `EXEC-<dom>` indisponível | Timeout / health check | Retentativa idempotente; caso `parcial` | Escala ao dono do domínio; se o prazo-alvo se aproxima, ao DPO (`C-DT-10`) |
| Eliminação não converge no segundo sítio | Verificação de convergência do passo 5 da §4.3 | Caso em `pendente-convergência`; **não** fecha como atendido | Plantão de infraestrutura investiga o atraso de replicação |
| Tentativa de restauração seletiva de chave eliminada | `GRD-RST` consulta `LST-SUP` | **Negada** + evento de segurança (`ADR-0015` C12) | Revisão de segurança de quem tentou e por quê |
| Restauração completa executada | Evento da plataforma de backup | Quarentena + reaplicação automática (§4.6.b) | Plantão assina a evidência de conclusão antes de levantar a quarentena (`C-DT-05`) |
| `LST-SUP` perdida ou corrompida | Verificação de integridade | **Nenhuma eliminação nova confirmada; nenhuma quarentena levantada** | Recuperação de `LST-SUP` pela cópia imutável própria, **antes** dos domínios (`C-DT-08`) |
| `CAT-DOM` incompleto | Reconciliação periódica contra o inventário de `ADR-0011` C9 | Divergência registrada como achado | DPO + donos de domínio. Não fechável por esta AR — **N7** |
| Cópia imutável não expira na data prevista | Verificação do passo 10 da §4.3 | Caso **não** avança para `eliminação-definitiva-confirmada` | Investigação com a plataforma de backup; se a retenção efetiva for maior do que a configurada, a data informada ao titular estava errada — ver **N6** |

**Cenários testados:** **nenhum.** Nenhuma das dez ADRs realizadas está aceita e nenhum workload foi construído a partir desta AR. Em particular, o cenário que mais importa aqui — **restauração completa com reaplicação de eliminações dentro do RTO de `ADR-0009` C9** — nunca foi exercitado, e a lacuna **N3** diz exatamente que o orçamento de RTO vigente não o contempla. "Resiliente" sem registro de teste não é uma afirmação que o Escritório possa defender.

---

## 8. Controles de segurança e regulatórios

Profundidade conforme `PARAM-REGDEPTH`; IDs de obrigação do [catálogo](/ARC/issues/ARC-8#document-catalogo-obrigacoes-regulatorias) §3.

### 8.1 As três linhas obrigatórias

| Regime | Obrigação | O que exige, em substância | Controle nesta referência | Onde é implementado | Evidência que um time pode produzir |
| --- | --- | --- | --- | --- | --- |
| **BACEN/CMN** | `OBL-CYB`, `OBL-RES`, `OBL-CLOUD` | `OBL-CYB`: política de segurança cibernética, controle e rastreabilidade sobre dado, tratamento de incidente. `OBL-RES`: continuidade com objetivos de recuperação definidos e **testados**, incluindo resiliência cibernética a corrupção/indisponibilidade maliciosa de dado. `OBL-CLOUD`: serviço relevante de processamento/armazenamento contratado com continuidade demonstrável | A AR **preserva** integralmente o backup imutável com trava de tempo de `ADR-0009` C8 e o cenário de restauração de C9 — é a razão de o desenho usar expiração em vez de apagar a cópia. Acesso administrativo a `LST-SUP`/`GRD-RST` sob PAM (`ADR-0012` C3). Trilha protegida contra adulteração (`ADR-0015` C9/C10). `OBL-CLOUD`: o núcleo do fluxo é **on-premises** (§5); nenhum arranjo de nuvem novo é criado — apenas `EXEC-<dom>` em nuvem já existente | `ADR-0009` C8/C9; `ADR-0012` C3; `ADR-0015` C9/C10; §5 | Configuração de retenção e trava de tempo da cópia imutável; relatório de teste de restauração de C9 **incluindo o passo de reaplicação**; log do PAM; meta-auditoria da trilha |
| **LGPD** | `OBL-LGPD` | Atendimento a direito do titular (acesso, correção, eliminação, portabilidade) dentro do prazo legal; minimização e limitação de finalidade; medidas de segurança e rastreabilidade sobre o tratamento; término do tratamento e eliminação | **É o objeto desta AR.** Fluxo com prazo parametrizado e alerta (`C-DT-10`), evidência por caso (§4.7), minimização nos contratos do fluxo (§6.3), e o mecanismo que torna `ADR-0011` C9 cumprível sob backup imutável: expiração por trava de tempo + bloqueio de restauração (`C-DT-04`) + reaplicação pós-restore (`C-DT-05`), conforme a interpretação confirmada em [ARC-46](/ARC/issues/ARC-46) | §4.3, §4.6, §4.7, §6.3 | `REG-PED` por caso; eventos de auditoria em `PLT-LOG`; registro de negativa de restauração; evidência de lote de reaplicação; evidência de expiração da cópia |
| **PCI-DSS** | `OBL-PCI` | **Não é `N/A`.** Proteger dado de portador de cartão armazenado, mascaramento na exibição, retenção e descarte definidos, acesso por necessidade de conhecer, trilha de auditoria | `EXEC-CDE` é o **único** componente dentro do enclave; `SVC-DT` nunca recebe PAN; resposta de acesso e `ART-PORT` carregam PAN mascarado; nenhuma réplica ou cópia derivada de dado de cartão fora do enclave; nenhum gateway de nuvem no caminho de cartão; PAN/SAD nunca em evento de auditoria | `ADR-0011` C7/C8; `ADR-0010` C9/C10/C11; `ADR-0015` C5/C6; `ADR-0014` C6 | Contrato da API na saída do enclave mostrando campo mascarado; varredura de destino de log por padrão de PAN; diagrama de segmentação do enclave |

**Conflito LGPD × retenção legal, declarado e não escondido.** O pedido de eliminação **não** se sobrepõe a dado sujeito a obrigação legal ou regulatória de guarda — registro de transação bancária sob BACEN/CMN, obrigação fiscal, processo judicial, e retenção de dado de transação de cartão. O desenho resolve isso no **passo 3 da §4.3**: `CAT-DOM` declara a retenção legal por domínio, o domínio retido não é eliminado, e o motivo entra na resposta ao titular. **O conteúdo desse campo é do DPO/Jurídico; esta AR fornece o lugar, não a regra.** Lacuna **N8**.

### 8.2 Escopo PCI-DSS

| Pergunta de escopo PCI-DSS | Resposta |
| --- | --- |
| Esta referência armazena, transmite ou processa dado de portador de cartão? | **Sim, por alcance** — quando o titular também é portador de cartão, um pedido de acesso, portabilidade ou eliminação **alcança** um domínio de dado de cartão. D-11 antecipou exatamente este caso |
| Algum componente da §3/§5 fica dentro do CDE ou conectado a ele? | **Sim: `EXEC-CDE`**, que é o componente de domínio **dentro** do enclave e já está em escopo por ser parte do domínio de cartão. **`SVC-DT`, `API-DT`, `REG-PED`, `LST-SUP` e `ART-PORT` ficam FORA do CDE por desenho** — eles nunca recebem PAN nem SAD, apenas valor mascarado e confirmação de execução. `PLT-LOG` já é tratada como sistema conectado ao CDE por `ADR-0015` C14, independentemente desta AR |
| **Efeito sobre o escopo (amplia / reduz / não altera)** | **Não altera**, e é uma decisão de desenho, não um acidente. A alternativa — `SVC-DT` lendo e escrevendo diretamente no dado de cartão para "simplificar" — **ampliaria** o CDE para o orquestrador, para `REG-PED`, para `LST-SUP` e para todo o `OCP` que os hospeda. O padrão executor-dentro-do-enclave existe para que essa ampliação não aconteça. Se um time inverter isso, ele amplia o CDE sem perceber, e é o anti-padrão da §1 |
| Famílias de requisito PCI-DSS afetadas | **3** (proteção de dado armazenado — retenção/descarte e mascaramento na exibição: é a interseção que D-11 nomeou); **7** (acesso por necessidade de conhecer: `SVC-DT` **não** precisa conhecer PAN); **10** (trilha de auditoria do pedido e da eliminação, sem PAN/SAD no evento). Indiretamente **6**, via lacuna consciente **G3** (sem padrão de arquitetura para promoção de mudança) — mesma leitura de **L7** de `AR-001` |
| Ressalva | A **suficiência** da resposta de eliminação para a família 3, quando o dado também é dado de cartão retido por obrigação de scheme ou regulatória, é interpretação de compliance de cartões e do QSA do banco, não do Escritório. Lacuna **N8** |

### 8.3 Perguntas gerais do template

| Pergunta | Resposta |
| --- | --- |
| Dado pessoal processado? | **Sim** — por definição. Possivelmente dado pessoal sensível e dado de portador de cartão, conforme o domínio alcançado |
| Processamento ou armazenamento fora do Brasil? | **Não** para o núcleo: `REG-PED`, `LST-SUP`, `CAT-DOM`, `SVC-DT`, `GRD-RST` e `ART-PORT` são on-premises no `DC-AA`. **Possivelmente sim** para um `EXEC-<dom>` cujo domínio já esteja hospedado em região de nuvem fora do Brasil — nesse caso vale `ADR-0002` D1 (residência de dado na escolha de ambiente), e o mecanismo legal de transferência internacional **não** é decidido por nenhuma ADR ([catálogo §7](/ARC/issues/ARC-8#document-catalogo-obrigacoes-regulatorias)): é do Jurídico/DPO. Esta AR **sinaliza**; não resolve |
| Depende de arranjo relevante de processamento/armazenamento/serviço em nuvem? | **Não cria nenhum.** O núcleo é on-premises. Sinalizar sob `OBL-CLOUD` apenas se um `EXEC-<dom>` em nuvem for, ele próprio, um arranjo relevante — o que é propriedade do domínio, não desta AR |
| Exposta externamente? | **Sim**, por `AGW-AXW` (`ADR-0010` C1). Compõe com [`AR-001`](/ARC/issues/ARC-9#document-ar-001-api-exposta-externamente) |
| Logging suficiente para reconstruir uma transação de negócio? | **Sim por desenho, não por evidência.** A cadeia de eventos da §4.7 reconstrói o caso de ponta a ponta com o identificador de correlação de `ADR-0010` C8. Mas: o prazo de retenção invocado por `ADR-0015` C11 **não está presente** na §5 daquela ADR (lacuna **L8** de `AR-001`, ainda aberta), então esta AR **não pode** declarar por quanto tempo a trilha permanece disponível |

> **Ressalva do Escritório, reproduzida conforme o template:** o Escritório mapeia obrigações regulatórias para controles de arquitetura em nível geral e **não emite parecer jurídico**.

---

## 9. Checklist de conformidade para o time que copia a referência

Binário, respondível por inspeção.

| # | O time tem … | Evidência | Sim/Não |
| --- | --- | --- | --- |
| 1 | Um registro de pedidos (`REG-PED`) que grava protocolo, tipo de direito, escopo, identidade verificada (método + nível + timestamp), identificador de correlação e data de recebimento | Schema de `REG-PED` + caso de amostra | |
| 2 | Rejeição de pedido sem nível de garantia de identidade declarado, sem default (`C-DT-02`) | Teste de contrato com asserção incompleta | |
| 3 | Nível de garantia exigido para eliminação/portabilidade **estritamente maior** do que para consulta de andamento (`C-DT-02`) | Configuração de política + teste | |
| 4 | Confirmação de que `SVC-DT`/`API-DT` não têm repositório de credencial de titular (`C-DT-01`) | Inspeção de configuração e de schema | |
| 5 | Identidade de operador federada com MFA e **as duas** identidades gravadas no pedido mediado (`C-DT-07`) | Log do IdP + caso de amostra de pedido mediado | |
| 6 | `CAT-DOM` com dono, executor e **campo de retenção legal** preenchido por domínio | Conteúdo de `CAT-DOM` | |
| 7 | Reconciliação periódica de `CAT-DOM` contra o inventário de domínios de dado persistido de `ADR-0011` C9, com divergência registrada como achado | Relatório de reconciliação | |
| 8 | Eliminação aplicada apenas no **primário único de escrita** do domínio, nunca nos dois sítios em paralelo (`ADR-0011` C6) | Desenho do `EXEC-<dom>` + configuração do motor | |
| 9 | Verificação de convergência nos **dois sítios** antes de fechar o caso como atendido (§4.3 passo 5) | Teste dirigido com atraso de replicação injetado | |
| 10 | `LST-SUP` com chave **pseudonimizada irreversível sem segredo**, sem atributo pessoal excedente (`C-DT-03`) | Schema de `LST-SUP` + inspeção de amostra | |
| 11 | Inscrição em `LST-SUP` **antes** do fechamento do caso (§4.3 passo 6) | Ordem das transições no log de auditoria do caso | |
| 12 | `GRD-RST` negando restauração seletiva de chave presente em `LST-SUP`, **sem via de override** (`C-DT-04`) | Teste de restauração seletiva negada + inspeção de ausência de override | |
| 13 | Negativa de restauração emitida como **evento de segurança**, não só log de aplicação | Amostra do evento em `PLT-LOG` | |
| 14 | Quarentena do ambiente restaurado — sem rota e sem consumidor — até a reaplicação concluir (`C-DT-05`) | Procedimento de restauração + teste | |
| 15 | Evidência de conclusão da reaplicação com contagem de chaves e de divergências, assinada pelo plantão | Relatório de lote de reaplicação | |
| 16 | Reprocessamento das **correções** aplicadas após a data da cópia restaurada (§4.4, §4.6.b passo 4) | Procedimento + teste | |
| 17 | Cópia imutável **própria** de `LST-SUP` sob `ADR-0009` C8, em ordem de recuperação anterior à dos domínios (`C-DT-08`) | Configuração de backup + procedimento de recuperação ordenado | |
| 18 | Nenhum passo do fluxo apaga, encurta ou contorna a trava de tempo da cópia imutável (`ADR-0009` C8) | Inspeção do fluxo + configuração de retenção | |
| 19 | Cálculo e registro da **data prevista de eliminação definitiva** por pedido (§4.3 passo 8) | Caso de amostra em `REG-PED` | |
| 20 | Verificação da expiração **efetiva** da cópia antes de confirmar a eliminação definitiva (§4.3 passo 10) | Evidência da plataforma de backup correlacionada ao caso | |
| 21 | Resposta ao titular em **duas etapas declaradas**, incluindo a existência das cópias imutáveis e a data prevista (§4.3 passo 9) | Template de resposta aprovado pelo DPO | |
| 22 | Domínio com retenção legal ativa **não** eliminado, com motivo na resposta (§4.3 passo 3) | Caso de amostra + conteúdo de `CAT-DOM` | |
| 23 | Prazo-alvo configurável por tipo de direito e alerta em marcos ao DPO (`C-DT-10`) | Configuração + evidência de alerta disparado | |
| 24 | `REG-PED` **sem** cópia do dado pessoal entregue ou eliminado (`C-DT-09`) | Schema de `REG-PED` | |
| 25 | Conjunto mínimo de campos declarado **por domínio** no contrato de `EXEC-<dom>`, com recusa de campo fora do conjunto (§6.3) | Especificação dos contratos + teste de contrato | |
| 26 | Resultado de acesso e `ART-PORT` entregues **por referência** de vida curta e cifrados em repouso (§4.4, §6.3) | Configuração do armazenamento + chave de `ADR-0013` | |
| 27 | Nenhum atributo pessoal além de identificador nos eventos de auditoria (`ADR-0015` C7) | Amostra de evento | |
| 28 | `EXEC-CDE` dentro do enclave, e `SVC-DT` **nunca** recebendo PAN ou SAD (§8.2) | Contrato na saída do enclave + varredura de destino de log por padrão de PAN | |
| 29 | PAN **mascarado** na resposta de acesso e no `ART-PORT` (`ADR-0011` C7, `ADR-0015` C5/C6) | Teste de contrato | |
| 30 | Nenhuma rota do fluxo em `AGW-AWS`/`AGW-AZR`; borda apenas no Axway (`ADR-0010` C1/C3) | Configuração exportada dos três gateways | |
| 31 | Travessia de ambiente para `EXEC-<dom>` em nuvem por federação de identidade de workload, sem chave estática (`ADR-0012` C5/C6) | Varredura de segredo + inventário de integrações cross-ambiente | |
| 32 | Identificador de correlação gerado **só** na borda e nunca regerado (`ADR-0010` C8) | Teste sintético ponta a ponta | |
| 33 | Administração de `LST-SUP` e `GRD-RST` sob PAM com elevação just-in-time e sessão registrada (`ADR-0012` C3) | Log do PAM | |
| 34 | Teste de restauração completa **com** reaplicação, medido contra o RTO de `ADR-0009` C9 — e o resultado reportado mesmo se exceder (**N3**) | Relatório de teste com tempos decompostos | |

> Um time que responde `Não` a qualquer item ou corrige, ou solicita waiver **contra a cláusula da ADR subjacente** — nunca contra esta AR. ARs não recebem waiver; as decisões que elas realizam, sim. Os itens que apontam para `C-DT-*` da §6.1 remetem, por construção, à ADR citada na coluna "Vem de" daquela tabela.

---

## 10. O que o time ainda precisa decidir localmente

### 10.1 Decisões deixadas ao time

| Decisão deixada ao time | Orientação | Registrada localmente? |
| --- | --- | --- |
| Função de pseudonimização de `LST-SUP` e custódia do sal | Unidirecional, sem reversão possível com o material disponível fora do custodiante; sal sob `ADR-0013`. Se a função for reversível, `LST-SUP` passa a ser dado pessoal e todo o desenho muda | **Sim** |
| Formato concreto do `ART-PORT` | Estruturado e legível por máquina. Se o domínio for de open finance, o padrão daquele regime prevalece. Nenhuma ADR escolhe formato | **Sim** |
| Limite de tempo para `pendente-convergência` antes de alertar | Derive do RPO do tier do domínio (§7), não de um número redondo | **Sim** |
| Marcos de alerta de prazo (`C-DT-10`) | Sugestão de 50% e 80%; os **valores de prazo** vêm do DPO (**N1**) | **Sim** |
| Vida útil do `ART-PORT` e da referência de entrega | A mais curta que o canal suporte. Artefato de portabilidade é uma cópia concentrada de dado pessoal | **Sim** |
| Granularidade da chave em `LST-SUP` | Por registro, por titular+domínio, ou por agregado. Afeta diretamente o custo e o tempo da reaplicação (§11) | **Sim** |
| Produto de motor de banco para `REG-PED`/`LST-SUP`/`CAT-DOM` | `ADR-0011` §4.2 declara escolha de produto fora de escopo | **Sim** |
| Tier concreto por domínio alcançado | Vem da criticidade de negócio (`ADR-0009` §4, D-3). Pertencer ao CDE **não** eleva o tier | **Sim** — e tag `classe-criticidade` |
| Observabilidade operacional | Lacuna consciente **G1-op** — §10.2 | **Sim** |
| Configuração, deployment e promoção | Lacuna consciente **G3** — §10.2 | **Sim** |
| Classificação de sensibilidade por domínio de `CAT-DOM` | Lacuna consciente **G4-class** — §10.2. A arquitetura consome; o dono do dado fornece | **Sim** |
| Texto da resposta ao titular | Aprovação do DPO. A **estrutura** de quatro partes do passo 9 da §4.3 é prescrição desta AR; a redação não | **Sim** |

### 10.2 Lacunas conscientes já decididas, com a redação de origem preservada

> **G1-op — Observabilidade operacional.** Não há decisão do Escritório na primeira onda para métricas, traces, SLO e APM. O time de entrega decide localmente. O lado de segurança está endereçado por `ADR-0015`. Candidata à segunda onda. *(Origem: [decisão ARC-11 §6](/ARC/issues/ARC-11#document-decisao-lacunas-g1-g4); registro central em [registro de decisões §12](/ARC/issues/ARC-2#document-decision-registry).)*

> **G3 — Configuração, deployment e promoção entre ambientes.** Não há decisão do Escritório na primeira onda. **Nota PCI-DSS:** toca a família 6 — gestão de mudança e separação dev/teste/produção — para qualquer workload no CDE ou conectado a ele. Candidata à segunda onda. *(Mesma origem.)* **Efeito concreto nesta AR:** `GRD-RST` é um controle cuja alteração indevida desfaz o bloqueio de restauração, e não há padrão do Escritório para promoção de mudança sobre ele.

> **G4-class — Esquema corporativo de classificação de dados.** A classificação é política do banco (`OBL-CYB`; `OBL-LGPD`, minimização). A arquitetura **consome** a classificação e não a define. Endereçada a [ARC-8](/ARC/issues/ARC-8) como insumo de mapeamento de obrigação. *(Mesma origem.)* **Efeito concreto nesta AR:** ver **N7** — sem o esquema, `CAT-DOM` não tem como provar completude.

### 10.3 Lacunas que esta AR encontrou e **não pode** fechar — escaladas ao Arquiteto Principal

O template de AR §12 e a acceptance criteria desta task exigem que uma referência que não consegue satisfazer um padrão **diga isso e escale**, em vez de desenhar em volta do padrão em silêncio. São oito.

| # | Lacuna | Por que esta AR não pode fechá-la | Encaminhamento proposto | Responsável |
| --- | --- | --- | --- | --- |
| **N1** | **Prazo legal de resposta por tipo de direito, e prazo de retenção da evidência de atendimento.** O fluxo precisa de números para o relógio de `C-DT-10` e para a retenção de `REG-PED`/`LST-SUP`. O Escritório lê com segurança, em nível geral, apenas o prazo de 15 dias do art. 19 da LGPD para confirmação/acesso; para eliminação, correção e portabilidade não há um prazo numérico único igualmente claro | É interpretação jurídica, não desenho. O [catálogo §13](/ARC/issues/ARC-8#document-catalogo-obrigacoes-regulatorias) já registra que a suficiência jurídica das citações de `OBL-LGPD` depende de confirmação do Jurídico. Inventar prazo seria fabricar conformidade | **Tabela de prazos por tipo de direito** e **prazo de retenção da evidência**, fornecidos como insumo. Até existirem, o parâmetro fica declarado como não confirmado, e a AR não sai de `Apenas referência` | **DPO / Jurídico**, via Governance Architect; Arch Master se precisar ir ao board |
| **N2** | **Nível de garantia de identidade do titular, e o esquema de autenticação do cliente em canal digital.** `C-DT-02` exige que a escala exista e que eliminação/portabilidade exijam nível maior. **A escala não existe** | `ADR-0012` §4.2 exclui explicitamente autenticação de usuário final em canal digital. Fixar a escala aqui seria o Solution Architect legislando sobre autenticação de cliente | É a **ADR da posição 1** do backlog de D-11, já priorizada **à frente** desta AR. Esta AR declara o contrato de asserção (§2.2) para que a ADR possa preenchê-lo sem retrabalho | **Security Architect** (ADR posição 1) |
| **N3** | **O orçamento de RTO de `ADR-0009` C9 não inclui a reaplicação pós-restore.** C9 fixa RTO ≤ 4 h (Tier 0) e ≤ 8 h (Tier 1) para restauração após corrupção lógica. A interpretação de [ARC-46](/ARC/issues/ARC-46) **acrescenta** um passo obrigatório no caminho crítico (`C-DT-05`: quarentena + reaplicação) que nenhuma dessas contas contempla | Alterar o orçamento de RTO de `ADR-0009`, ou acrescentar cláusula a C9, é decisão de resiliência — não de solução. Esta AR não pode mudar `ADR-0009` por via de exemplo | **Candidata a cláusula nova em `ADR-0009`** (ou revisão de C9): o cenário de teste de C10(c) passa a incluir o passo de reaplicação, e o orçamento de RTO passa a declará-lo. Alternativa: declarar ao comitê que o RTO de C9 não cobre domínios sujeitos a eliminação de titular — pior, porque é a maioria deles | **Infrastructure Architect**, com Technical Architect; **Arquiteto Principal** para decidir se é revisão de `ADR-0009` ou ADR nova |
| **N4** | **`LST-SUP` é um serviço compartilhado novo, fora do inventário de `ADR-0006` Q5.** A capacidade de restaurar qualquer domínio sujeito a eliminação passa a depender dela. Por `ADR-0009` C11, `LST-SUP` **MUST** ter tier ≥ o maior tier entre os domínios que governa, ou rebaixa todos eles. Ela não existia quando o inventário foi especificado ([ARC-43](/ARC/issues/ARC-43)) | Esta AR não atribui tier a serviço compartilhado de plataforma — é a mesma fronteira da lacuna **L3** de `AR-001` | **Incluir `LST-SUP` no inventário de serviços compartilhados do `DC-AA`** e atribuir-lhe tier, no mesmo trabalho que D-7 já atribuiu ao Infrastructure Architect (60 dias após a vigência) | **Infrastructure Architect** (inventário e tier), acionado pelo Arquiteto Principal |
| **N5** | **`ADR-0010` Q7 continua aberta depois desta AR.** A §6.3 fecha a minimização **deste** fluxo. O padrão corporativo de minimização para **todo** contrato de API do banco — com gate de revisão e critério de verificação — não é fechável por uma AR | O template de AR é explícito: "se uma AR precisa tomar uma nova decisão para ficar completa, pare". Decidir aqui seria legislar sobre `ADR-0010` por via de exemplo | **ADR de segunda onda sobre minimização de dado pessoal em contrato de API**, com a §6.3 desta AR como insumo de forma. D-11 pediu a seção própria, e ela está entregue — mas a Q7 em si precisa de ADR | **Technical Architect** (dono de `ADR-0010`) com **Governance Architect** e Security Architect; priorização do **Arquiteto Principal** |
| **N6** | **`ADR-0009` C8 fixa piso de retenção da cópia imutável (35 dias), não teto — e a "data prevista de eliminação definitiva" prometida ao titular depende do teto.** Se a retenção efetiva de um domínio for de meses ou anos, a data informada ao titular no passo 9 da §4.3 é muito mais distante do que "35 dias", e ninguém hoje sabe qual é, por domínio | Fixar retenção máxima de backup por domínio é decisão de resiliência e de custo, não de solução. E a **aceitabilidade** de uma janela longa perante a LGPD é interpretação jurídica — a resposta de [ARC-46](/ARC/issues/ARC-46) validou o **mecanismo**, não uma duração específica | (a) **Levantar a retenção efetiva** da cópia imutável por domínio e declará-la; (b) decidir se `ADR-0009` fixa **teto** de retenção para domínio com dado pessoal; (c) confirmar com DPO/Jurídico se a janela resultante é aceitável. Enquanto (a) não existir, o fluxo **pode** calcular a data mas **não pode** prometer uma ordem de grandeza | **Infrastructure Architect** (a, b); **DPO/Jurídico** (c), via Governance Architect |
| **N7** | **Sem o esquema corporativo de classificação, `CAT-DOM` não tem como provar completude.** O modo de falha mais grave desta AR (§4.5) é um domínio com dado pessoal fora do catálogo: o titular recebe resposta que parece completa e não é — e o banco afirma ter eliminado o que não eliminou | É **G4-class**, já decidida como fora do alcance arquitetural. O que registro é o **efeito concreto**: a completude do atendimento de um direito do titular depende de um insumo que a arquitetura consome e não produz | Priorizar [ARC-8](/ARC/issues/ARC-8); até lá, a reconciliação de `CAT-DOM` contra o inventário de `ADR-0011` C9 (item 7 da §9) é a **única** mitigação, e é parcial. Isso deve ser dito ao comitê como limite do fluxo, não como detalhe | **CISO / Governança de Dados** (esquema); **DPO** (conteúdo de `CAT-DOM`) |
| **N8** | **Conflito entre eliminação sob LGPD e retenção obrigatória — inclusive de dado de cartão sob PCI-DSS família 3 e regras de scheme.** A §4.3 passo 3 fornece o **lugar** (campo de retenção legal em `CAT-DOM`) e o comportamento (retém, não elimina, informa o motivo). O **conteúdo** por domínio, e a suficiência da resposta perante o QSA, não são do Escritório | Processo de waiver §2: obrigação regulatória não é objeto de waiver de arquitetura. E `ADR-0011` Q2 / `ADR-0010` Q8 já registram que a interpretação de tokenização não foi validada com QSA — mesma natureza de lacuna | **Matriz de retenção legal por domínio**, fornecida pelo DPO/Jurídico com compliance de cartões, carregada em `CAT-DOM`. Confirmação com o **QSA** de que a resposta de eliminação em duas etapas é suficiente para a família 3 quando o dado também é dado de cartão | **DPO / Jurídico** com **compliance de cartões e QSA**; Security Architect reflete o resultado em `ADR-0011` |

**Resumo honesto do que estas oito significam.** Um time pode construir este fluxo hoje, de ponta a ponta, e ele estará arquiteturalmente coerente com as dez ADRs que a AR realiza. Mas ele **não** poderá afirmar: (a) que respondeu no prazo legal correto (**N1**); (b) que verificou a identidade do titular em nível adequado (**N2**); (c) que a restauração com reaplicação caberá no RTO declarado (**N3**); (d) que a data de eliminação definitiva informada ao titular é a real (**N6**); nem (e) que o atendimento foi **completo** (**N7**). As três primeiras e a quinta são lacunas de outros donos; a quarta é de infraestrutura. Nenhuma delas se fecha escrevendo melhor esta AR, e é por isso que estão aqui e não escondidas em uma escolha local.

---

## 11. Notas de custo e capacidade

**Base de dimensionamento declarada:** 10²–10³ pedidos/mês, pico de 10× a média diária, `N` domínios no escopo por pedido. Nenhum destes números é medido — são a base desta AR, a ser substituída pelo volume real do banco. Ordem de grandeza com base declarada vale mais do que precisão sem nenhuma.

| Direcionador | Comportamento | Penhasco conhecido |
| --- | --- | --- |
| **`N` — fan-out de domínios por pedido** | **O direcionador dominante**, mais do que o volume de pedidos. Cada domínio novo em `CAT-DOM` é um `EXEC-<dom>` a construir, operar e testar | Um banco com dezenas de domínios de dado pessoal paga `N` integrações, não uma. É o custo real desta AR e ele não aparece no volume de pedidos |
| **Granularidade da chave em `LST-SUP`** | Define o volume da lista e o tempo da reaplicação | Chave por registro em um domínio de alto volume faz `LST-SUP` crescer indefinidamente — e ela **nunca** encolhe (`C-DT-06`). Granularidade por titular+domínio é ordens de magnitude menor |
| **Tempo de reaplicação pós-restore** | Cresce com o tamanho de `LST-SUP` × tamanho do domínio restaurado, e está **no caminho crítico** do RTO (**N3**) | É o penhasco de custo mais sério: a quarentena de `C-DT-05` converte tempo de reaplicação em **indisponibilidade** durante um incidente, exatamente quando ela é mais cara |
| **Armazenamento de `LST-SUP` e sua cópia imutável própria** | Crescimento monotônico, com cópia imutável dedicada (`C-DT-08`) | Pequeno em volume absoluto, mas é armazenamento **que nunca é liberado** e com cópia imutável adicional sob C8 |
| **Requisições no Axway** | Baixo — volume de pedidos é baixo | Nenhum relevante nesta faixa |
| **Capacidade on-premises** | Núcleo é on-premises por desenho (§5), somando-se à pressão de capacidade que **D-8** já registrou em `AR-001` §11: o `DC-AA` está dimensionado em 2× com descarte de Tier 2/3 no failover de sítio (`ADR-0009` §4). `REG-PED`/`SVC-DT` são Tier 2 e portanto **candidatos a descarte** no failover; `LST-SUP` é Tier 1 e **não** é | Um failover de sítio durante um pico de pedidos degrada o atendimento (Tier 2) **sem** degradar o bloqueio de restauração (Tier 1). Essa assimetria é intencional e deve ser dita ao negócio: o prazo legal continua correndo |
| **Transferência entre sítios e entre regiões** | Replicação de `REG-PED`/`LST-SUP` entre os dois sítios é baixa. Chamada a `EXEC-<dom>` em nuvem é baixa em volume | Nenhum relevante nesta faixa |
| **Esforço humano do DPO** | Triagem de exceção, aprovação de texto de resposta, matriz de retenção legal (**N8**) | **É provavelmente o maior custo recorrente do fluxo**, e não é um custo de plataforma. Dizê-lo é mais útil do que omiti-lo |

---

## 12. Orientação sobre desvios

- Desvio de cláusula `SHOULD` de ADR realizada: registre localmente, sem waiver.
- Desvio de cláusula `MUST`: **waiver necessário** — [processo de exceção e waiver](/ARC/issues/ARC-2#document-exception-and-waiver-path).
- **Nenhum waiver é concedido contra esta AR.** Os `C-DT-*` da §6.1 não são cláusulas de ADR: cada um remete à cláusula de ADR da coluna "Vem de". Um desvio de `C-DT-04` ou `C-DT-05`, especificamente, é um desvio da **condição que torna válida a interpretação de [ARC-46](/ARC/issues/ARC-46)** — e, pela regra 2 do processo de exceção, **obrigação regulatória não é objeto de waiver de arquitetura**. Não há via de waiver para "restaurar seletivamente um registro eliminado" ou "levantar a quarentena sem reaplicar". A via correta, se o banco julgar necessário, é uma nova pergunta ao DPO/Jurídico pelo mesmo caminho que produziu [ARC-46](/ARC/issues/ARC-46).
- Encontrou caso que esta referência não cobre? Pergunta de intake (modelo operacional §5). É lacuna da referência e o Escritório quer saber.

---

## 13. Revisão por pares e discordâncias

Revisão **não** realizada nesta versão 0.1. Os revisores abaixo são os que considero obrigatórios, pelas fronteiras que a AR atravessa; não estou registrando veredito que não existe.

| Revisor | Papel | Veredito | Comentário |
| --- | --- | --- | --- |
| **Arquiteto Principal** | Gate interno do Escritório; destinatário das oito lacunas da §10.3, sendo **N3** e **N5** candidatas a ADR/revisão de ADR | `liberado para o comitê` (2026-10-05) | Card de confirmação desta issue aceito com o rótulo "Pronta para o comitê, com as lacunas declaradas", sem pedido de alteração. Veredito de **prontidão**; não é aprovação, e não dispensa **R3**. As cinco lacunas de decisão seguem abertas em [ARC-51](/ARC/issues/ARC-51) |
| **Security Architect** | Dono da ADR da posição 1 de D-11 (**N2**); dado pessoal, dado de cartão, CDE (§8); dono de `ADR-0015` | `pendente` | |
| **Infrastructure Architect** | Dono de `ADR-0009` C8/C9 — **N3**, **N4**, **N6** incidem diretamente sobre sua ADR e seu inventário | `pendente` | |
| **Technical Architect** | Dono de `ADR-0010` (Q7 / **N5**) e de `ADR-0011` (C9, C10) | `pendente` | |
| **Governance Architect** | Atribuição de ID da AR; conformidade com o template; encaminhamento de **N1**, **N6**, **N8** ao DPO/Jurídico; linha no registro de decisões | `pendente` | |
| **Enterprise Architect** | Revisão obrigatória de conflito com ADR aceita (modelo operacional) | `pendente` | |
| **DPO do banco** | Coautoria exigida por D-11; insumos de **N1**, **N6**, **N8**; aprovação do texto de resposta ao titular | `pendente` — **não é papel do Escritório**; acesso depende de encaminhamento via Governance Architect / Arch Master | |

---

## 14. Changelog e histórico de status

| Data | Versão | De → Para | Quem | O que mudou |
| --- | --- | --- | --- | --- |
| 2026-10-05 | 0.1 | — → `proposta` | Solution Architect | Versão inicial. Escrita sob a interpretação LGPD confirmada pelo board em [ARC-46](/ARC/issues/ARC-46) e a prioridade fixada por [D-11](/ARC/issues/ARC-39#document-decisao-d7-d11) posição 2. Cobre os cinco itens de escopo de [ARC-50](/ARC/issues/ARC-50): fluxo de eliminação sob expiração de backup imutável (§4.3), bloqueio de restauração seletiva e reaplicação pós-restore (§4.6), minimização no contrato da API (§6.3, com `ADR-0010` Q7 declarada aberta em **N5**), registro do pedido e evidência de atendimento (§4.7), e confirmação explícita de que a autenticação de usuário final do titular fica fora de escopo por `ADR-0012` §4.2 com o ponto de verificação declarado (§2.2). Oito lacunas escaladas ao Arquiteto Principal (§10.3). ID da AR pendente de atribuição |

| 2026-10-05 | 0.1 | `proposta` → `proposta` | Solution Architect | **Registro do gate interno, mudança editorial — a versão não muda.** O card de confirmação de [ARC-50](/ARC/issues/ARC-50) foi aceito ("Pronta para o comitê, com as lacunas declaradas"), sem pedido de alteração. Registrado no bloco de status, no §0 (nova linha **Gate interno do Escritório**), em §0.2 (o que continua faltando) e em §13 (veredito do Arquiteto Principal). **Nenhuma prescrição de §5, §6, §7 ou §8 foi alterada**, e o status segue `proposta` por **R3** |

> Mudança que altere prescrição na §5, §6, §7 ou §8 é nova versão e passa pelo caminho padrão de novo. Mudança editorial não.
