# ADR-0001 — Adotar ADRs, ARs e um registro de decisões como o sistema de artefatos do Escritório de Arquitetura

> **Por que esta ADR existe:** a decisão de operar o Escritório desta forma é, ela própria, uma decisão de arquitetura, e seria incoerente introduzir um padrão de registro de decisões sem registrar a decisão. Este documento também serve como o exemplo trabalhado do [template de ADR](/ARC/issues/ARC-2#document-adr-template) — cada campo é preenchido da forma como um arquiteto de domínio deveria preenchê-lo.
>
> **Nota de revisão (2026-10-04):** esta ADR foi convertida para pt-BR e teve o PCI-DSS incorporado à sua seção de conformidade, por decisão do board de 2026-10-04. O status permanece `proposta`; nenhuma mudança de status ocorreu nesta revisão.

## 0. Cabeçalho

| Campo | Valor |
| --- | --- |
| **ID da ADR** | `ADR-0001` |
| **Status** | `proposta` |
| **Provisória** | Não |
| **Responsável** | Arquiteto de Governança |
| **Domínio** | Governance |
| **Data da proposta** | 2026-10-04 |
| **Data da última mudança de status** | 2026-10-04 |
| **Data de vigência** | na aceitação |
| **Substitui** | Nenhuma |
| **Substituída por** | Nenhuma |
| **ADRs relacionadas** | Nenhuma |
| **Implementada por ARs** | Nenhuma ainda |
| **Idioma** | Português do Brasil (conforme `PARAM-LANG`) |
| **Data de submissão ao comitê** | não submetida |
| **Resultado do comitê** | `pendente` |

## 1. Contexto

**A situação.** O Escritório de Arquitetura foi criado para servir um banco brasileiro cujo estado é deliberadamente heterogêneo: um data center on-premises active-active; OpenShift on-premises e Kubernetes gerenciado em Azure, OCI e AWS; VMs operando junto com containers; bases de dados relacionais e não relacionais; e AWS API Gateway, Azure API Management e Axway API Gateway coexistindo na borda de API. Oito arquitetos em sete domínios produzirão orientação para times de entrega trabalhando em tudo isso. Hoje o Escritório não tem formato de artefato, não tem registro de decisões, não tem definição de quando uma decisão está pronta para o comitê de arquitetura do banco, e não tem um caminho para um time que não consegue cumprir.

**O gatilho.** Sete tasks da primeira onda ([ARC-3](/ARC/issues/ARC-3) a [ARC-9](/ARC/issues/ARC-9)) estão bloqueadas por falta de um sistema de artefatos. Elas produzirão aproximadamente quatorze decisões e duas referências de ponta a ponta sobre posicionamento, landing zones, topologia de DC, conectividade, gateways, persistência, identidade, segredos e segmentação. Qualquer formato que usarem se torna o formato do Escritório por padrão. Escolher deliberadamente agora custa uma task; retrabalhar dezesseis artefatos depois custa consideravelmente mais, e os escritos nesse meio-tempo não terão análise de alternativas para recuperar.

**Custo de não decidir.** Cada arquiteto inventa um formato. Decisões ficam em comentários de task e em chat, onde não podem ser indexadas, substituídas ou auditadas. Um time de entrega perguntando "o que devemos fazer sobre X?" recebe uma resposta diferente dependendo de quem pergunta. Quando um regulador ou uma auditoria interna pergunta por que os dados de um serviço estão onde estão, o banco tem um sistema em produção e nenhum registro da decisão por trás dele — exatamente o modo de falha que este Escritório existe para prevenir.

**Restrições que não podemos mudar.**

- A aprovação final de decisões de arquitetura pertence ao comitê de arquitetura do banco. O Escritório assessora; não tem autoridade de aprovação e não deve ser estruturado como se tivesse.
- O estado é como descrito acima e não está sendo consolidado como parte desta decisão.
- Um padrão era provisório e já foi decidido pelo board: o idioma dos artefatos (`PARAM-LANG`) passou de inglês para português do Brasil em 2026-10-04. A profundidade da citação regulatória (`PARAM-REGDEPTH`, atualmente nível geral) permanece provisória. O sistema de artefatos precisa absorver qualquer mudança futura em `PARAM-REGDEPTH` sem retrabalho.
- O Escritório é pequeno. Qualquer processo que exija administração dedicada não será executado, e um processo que não é executado é peior do que nenhum, porque cria um registro falso.

## 2. Fatores de decisão

| ID | Fator | Tipo | ID de obrigação | Peso |
| --- | --- | --- | --- | --- |
| D1 | O banco precisa ser capaz de mostrar a um regulador ou auditor como e por que decisões de arquitetura que afetam cibersegurança, terceirização de processamento de dados e serviços em nuvem, resiliência operacional, dados pessoais e dados de portador de cartão foram tomadas, e por quem | Regulatório | `OBL-CYB`, `OBL-CLOUD`, `OBL-RES`, `OBL-LGPD`, `OBL-PCI` | Obrigatório |
| D2 | Uma decisão precisa ser reconstituível anos depois por alguém que não estava presente — incluindo as opções rejeitadas, não apenas a opção escolhida | Operabilidade | N/A | Obrigatório |
| D3 | Oito arquitetos em sete domínios precisam produzir artefatos que um time de entrega possa ler de forma intercambiável, sem interpretação por autor | Operabilidade | N/A | Forte |
| D4 | O processo precisa ser rápido o suficiente para que os times o usem em vez de contorná-lo; uma decisão escondida é o peior resultado possível | Velocidade de entrega | N/A | Obrigatório |
| D5 | Os artefatos precisam endereçar explicitamente um estado heterogêneo — quatro ambientes de hospedagem, três API gateways, VMs e containers, bases relacionais e não relacionais — em vez de assumir um único de cada | Operabilidade | N/A | Forte |
| D6 | O Escritório nunca pode parecer aprovar uma decisão em nome do banco; a autoridade de aprovação está com o comitê de arquitetura | Operabilidade (**correção desta revisão:** não `Regulatório` — nenhuma obrigação externa do catálogo exige isto) | N/A. Restrição de autoridade de governança definida pelo próprio mandato do comitê do banco, não uma obrigação externa mapeada no catálogo da §3. | Obrigatório |
| D7 | O idioma dos artefatos e a profundidade de citação regulatória são provisórios e precisam ser alteráveis sem retrabalhar os artefatos existentes | Operabilidade | N/A | Forte |
| D8 | A não conformidade precisa ter uma rota legítima, registrada e com expiração; um caminho de exceção indisponível produz desvio não documentado | Operabilidade | N/A | Obrigatório |

## 3. Alternativas consideradas

### Opção A — ADRs e ARs leves, um registro mantido manualmente, um portão de prontidão objetivo, um caminho de waiver em camadas *(escolhida)*

- **O que é:** dois tipos de artefato com templates fixos (uma decisão por ADR; uma referência trabalhada de ponta a ponta por AR), um único documento de registro indexando todo artefato com status e cadeia de substituição, um checklist de prontidão objetivo executado pelo Arquiteto de Governança antes da submissão ao comitê, e um caminho de waiver em três níveis com expiração obrigatória. Os artefatos são documentos Markdown simples em tasks do Escritório. Nenhuma ferramenta além do que o Escritório já tem.
- **Como pontua:**
  - D1 — Atendido. Todo artefato carrega uma seção de conformidade referenciada por IDs de obrigação, um responsável nomeado, um histórico de status e um registro de revisão por pares.
  - D2 — Atendido. O template de ADR exige ao menos duas opções, cada uma pontuada contra todo fator, cada uma com um motivo de rejeição declarado.
  - D3 — Atendido. Templates fixos sem seções opcionais; o checklist de prontidão força a completude mecanicamente.
  - D4 — Majoritariamente atendido. Quatro etapas com prazo próprio publicado somam ~25 dias úteis de ponta a ponta (triagem 5 + revisão por pares 10 + revisão de governança 5 + validação 5), dentro das sete etapas do caminho padrão (intake e a etapa de rascunho não têm prazo fixo), mais um caminho rápido provisório de 60 dias para decisões urgentes. Mais lento do que decidir em um chat; rápido o suficiente para ser o caminho de menor resistência para qualquer coisa que precise ser durável.
  - D5 — Atendido. Uma matriz fixa de aplicabilidade de 14 linhas em ambos os templates; uma linha não marcada falha na prontidão, então a omissão não pode ser acidental.
  - D6 — Atendido. A Etapa 5 é *endosso* do Escritório; somente o comitê define `aceita`; o item de prontidão G16 reprova qualquer artefato que se descreva como aprovado.
  - D7 — Atendido. Idioma e profundidade de citação são dois parâmetros nomeados em uma tabela no modelo operacional, e o texto regulatório vive em um único catálogo referenciado por ID. Os templates não contêm nenhum dos dois.
  - D8 — Atendido. Três níveis de waiver com concedentes, limites e um registro, mais um caminho de emergência.
- **Custo e esforço:** uma task para construir (esta aqui). Contínuo: aproximadamente meio dia por mês de tempo do Arquiteto de Governança para manutenção do registro e dos waivers, mais ~5 dias úteis de esforço de revisão por artefato distribuído pelo Escritório. Base: sete etapas de processo, cada uma com prazo, contra um volume esperado de 15–25 artefatos no primeiro ano.
- **Riscos:** o registro é um documento mantido manualmente e não escala além de algumas centenas de artefatos (sinalizado na limitação conhecida do registro). A revisão de prontidão é um ponto único de dependência no Arquiteto de Governança. A completude de template pode ser satisfeita formalmente sem ser satisfeita substantivamente — um campo preenchido não é necessariamente um campo pensado, e o checklist deliberadamente não consegue detectar isso.
- **Por que foi escolhida:** a única opção que satisfaz todos os cinco fatores `Obrigatório` (D1, D2, D4, D6, D8). Também é a única que o Escritório consegue efetivamente operar em seu tamanho atual, que é o teste mais importante para D4.

### Opção B — Nenhum artefato formal: decisões registradas em atas de reunião e no repositório de cada time

- **O que é:** o Escritório assessora por meio de reuniões de revisão. Decisões são capturadas em atas; times de entrega documentam o que implementam em seus próprios repositórios.
- **Como pontua:**
  - D1 — Falha. Atas registram que uma discussão aconteceu, não o que foi decidido, quais opções foram rejeitadas, ou quais obrigações foram consideradas. Esta é uma falha em um fator `Obrigatório`.
  - D2 — Falha. Opções rejeitadas são a primeira coisa que as atas perdem.
  - D3 — Falha. Nenhum formato compartilhado.
  - D4 — Atendido, e melhor do que qualquer outra opção. Não há nada para contornar.
  - D5 — Falha. Nada força o estado a ser endereçado.
  - D6 — Parcialmente atendido. Ninguém aprova nada, mas ninguém registra nada também.
  - D7 — Atendido trivialmente, por não haver nada para retrabalhar.
  - D8 — Falha. Sem um padrão, não há nada para receber waiver e nenhum registro de desvio.
- **Custo e esforço:** próximo de zero para implantar. Ilimitado depois, conforme decisões são relitigadas e perguntas de auditoria são respondidas por reconstrução.
- **Riscos:** o banco opera sistemas regulados sem registro arquitetural. Este é o risco do status quo, e é o motivo pelo qual o Escritório foi criado.
- **Por que não foi escolhida:** falha em cinco fatores (D1, D2, D3, D5, D8), três deles `Obrigatório` (D1, D2, D8). Registrada aqui porque "não fazer nada" tem um custo que o comitê tem o direito de ver quantificado, não assumido.

### Opção C — Um repositório de arquitetura empresarial modelado com workflow imposto por ferramenta

- **O que é:** uma ferramenta de EA contendo artefatos modelados, com decisões como objetos tipados, estados de workflow impostos, e rastreabilidade gerada desde obrigações regulatórias até componentes implantados.
- **Como pontua:**
  - D1 — Superado. Rastreabilidade gerada é evidência mais forte do que qualquer conjunto de documentos.
  - D2 — Atendido, desde que o modelo seja mantido.
  - D3 — Atendido e imposto pela ferramenta.
  - D4 — **Falha.** Licenciamento de ferramenta, onboarding de oito arquitetos, convenções de modelagem e um modelo mantido são meses de lead time, e cada artefato passa a custar mais para produzir. Times de entrega precisando de uma resposta neste trimestre não recebem nada, e decidem sem o Escritório. Esta é uma falha em um fator `Obrigatório`, e é fatal.
  - D5 — Atendido.
  - D6 — Atendido, com configuração.
  - D7 — Parcialmente atendido. Idioma e profundidade de citação se tornam configuração de ferramenta, o que é mais difícil de mudar do que uma tabela em um documento.
  - D8 — Atendido.
- **Custo e esforço:** custo de licença, mais um tempo estimado de vários meses até que o primeiro artefato seja produzido, mais um esforço contínuo de modelagem que o Escritório atual não consegue suprir. Base: o Escritório tem um papel de governança e nenhum orçamento de ferramentas estabelecido.
- **Riscos:** a falha clássica — um modelo que se desalinha da realidade e continua sendo confiado mesmo assim. Um repositório de EA desatualizado é mais perigoso do que nenhum repositório, porque parece autoritativo.
- **Por que não foi escolhida:** falha em D4 de forma decisiva e não pode ser suprida com a equipe atual. Vale revisitar quando o volume de artefatos tornar o registro mantido manualmente insustentável; a limitação conhecida do registro nomeia esse gatilho.

### Opção D — Apenas ADRs, sem ARs

- **O que é:** a Opção A sem o artefato de Referência de Arquitetura. Decisões são registradas; os times montam designs a partir delas por conta própria.
- **Como pontua:**
  - D1, D2, D6, D7, D8 — Atendidos, de forma idêntica à Opção A.
  - D3 — Parcialmente atendido. As decisões são consistentes; os designs construídos a partir delas não são.
  - D4 — Parcialmente atendido. Mais leve para o Escritório, mais pesado para cada time de entrega, que precisa re-derivar como quinze decisões se compõem. Times que erram isso geram solicitações de waiver e retrabalho.
  - D5 — Enfraquecido. Matrizes espalhadas por decisão nunca mostram a um time como é um workload completo e conforme em uma dada combinação de plataforma.
- **Custo e esforço:** levemente mais barato que a Opção A para o Escritório; mais caro para o banco, repetido por time de entrega.
- **Riscos:** quinze decisões individualmente corretas que ninguém nunca compôs. O primeiro time que tentar se torna a referência de fato, sem revisão.
- **Por que não foi escolhida:** o problema de composição é exatamente onde este estado dói — um serviço que atravessa o DC active-active, uma nuvem e um dos três gateways toca uma dezena de decisões ao mesmo tempo. [ARC-9](/ARC/issues/ARC-9) existe porque o banco precisa de referências trabalhadas, não apenas de pareceres.

### Opção E — Docs-as-code: repositório único versionado, revisão por pull request, índice gerado *(considerada e rejeitada nesta revisão)*

- **O que é:** os mesmos dois tipos de artefato (ADR, AR) e o mesmo checklist de prontidão da Opção A, mas como arquivos Markdown num repositório Git único em vez de documentos de task; revisão por pares vira *pull request* com aprovação obrigatória; o registro de decisões (§5–§13) é **gerado** por um script a partir do cabeçalho de cada arquivo, em vez de mantido manualmente.
- **Como pontua:**
  - D1, D2, D6, D8 — Atendidos, de forma idêntica à Opção A; o conteúdo exigido pelo template não muda, só o meio de armazenamento.
  - D3 — Atendido e reforçado: um *linter* de CI pode impor a presença de todo campo do template, o que o checklist de prontidão hoje faz manualmente.
  - D4 — **Falha.** Oito arquitetos precisam adotar fluxo Git (branch, PR, merge) além do que já usam para o trabalho do Escritório; o Escritório não tem hoje um repositório de código provisionado, revisor de CI, nem a competência de manter o script gerador do registro — construir isso é trabalho novo não orçado, e é exatamente o tipo de lead time que D4 pune. Fatal, no mesmo sentido em que é fatal para a Opção C.
  - D5 — Atendido, de forma idêntica à Opção A.
  - D7 — Atendido; parâmetros e catálogo seriam arquivos versionados como qualquer outro.
- **Custo e esforço:** provisionar o repositório, o pipeline de CI/lint e o gerador de índice antes do primeiro artefato valer; estimado em semanas, não dias, para uma equipe que ainda não opera Git como ferramenta de governança.
- **Riscos:** o registro gerado fica tão correto quanto o script gerador — um defeito no gerador é um defeito silencioso em todas as linhas do registro ao mesmo tempo, diferente do erro isolado de uma linha editada à mão. Dois sistemas de verdade (task do Escritório vs. repositório Git) durante a transição.
- **Por que não foi escolhida:** falha em D4 pelo mesmo motivo estrutural da Opção C — custo de ferramenta e competência antes de qualquer artefato ser produzido — embora menos do que a Opção C, porque não há licenciamento e o Markdown já é o formato de corpo escolhido na Opção A. **Vale revisitar junto com a Opção C**, pelo mesmo gatilho: quando o volume de artefatos tornar a manutenção manual do registro insustentável (registro §11, limitação conhecida), migrar o *armazenamento* para um repositório com índice gerado custa menos do que migrar para uma ferramenta de EA modelada, porque o conteúdo dos artefatos não muda.

### Opções consideradas e descartadas precocemente

| Opção | Descartada porque |
| --- | --- |
| Adotar por completo o conjunto de entregáveis de um framework de arquitetura empresarial | Produz dezenas de tipos de artefato para um Escritório de oito pessoas. Falha em D4 antes de qualquer conteúdo ser escrito. As partes que vale a pena ter — decisões registradas, referências trabalhadas — são a Opção A. |
| Templates por domínio, um por arquiteto | Contradiz diretamente D3, e torna o checklist de prontidão impossível de manter objetivo entre sete variantes. |
| Registrar decisões no repositório de cada time de entrega sem índice central | Nenhum índice significa nenhuma substituição e nenhuma forma de descobrir se uma decisão existe. Falha em D1 e D2. |
| Escritório concede suas próprias aprovações, comitê revisa uma amostra | Falha em D6, que é uma restrição que o Escritório não tem autoridade para relaxar. |

## 4. Decisão

> **Vamos registrar toda decisão do Escritório como uma ADR e todo padrão trabalhado de ponta a ponta como uma AR, indexar ambas em um único registro de decisões, condicionar a submissão ao comitê a um checklist de prontidão objetivo, e direcionar a não conformidade por um caminho de waiver em camadas com expiração obrigatória.**

### Cláusulas normativas

| # | Cláusula | Palavra-chave |
| --- | --- | --- |
| C1 | O Escritório **MUST** produzir decisões como ADRs e referências trabalhadas de ponta a ponta como ARs, usando os templates do Escritório | MUST |
| C1a | O Escritório **MUST NOT** introduzir um terceiro tipo de artefato sem uma ADR substituta desta | MUST NOT |
| C2 | Toda ADR e AR **MUST** ter uma linha no registro desde o dia em que o rascunho começa | MUST |
| C2a | Uma decisão ausente do registro **MUST NOT** ser tratada como vinculante para qualquer time de entrega | MUST NOT |
| C3 | Artefato **MUST NOT** ser submetido ao comitê de arquitetura sem um veredito APROVADO registrado contra o checklist de prontidão | MUST NOT |
| C4 | O status `aceita` de um artefato **MUST NOT** ser definido por nenhuma parte além do comitê de arquitetura | MUST NOT |
| C4a | Nenhum artefato do Escritório **MUST NOT** se descrever como aprovado; a ação da Etapa 5 é endosso, não aceitação (ver G16) | MUST NOT |
| C5 | Desvio de uma cláusula `MUST` de uma ADR `aceita` **MUST** ter um waiver concedido com um concedente nomeado, uma data de expiração e uma entrada no registro; desvio de uma cláusula `SHOULD` **MUST** ser registrado pelo time localmente, mas não exige waiver | MUST |
| C6 | Artefatos **MUST NOT** afirmar interpretação regulatória em nível de artigo ou inciso | MUST NOT |
| C6a | Referências regulatórias **MUST** ser IDs de obrigação resolvidos através do catálogo único do Escritório | MUST |
| C7 | O Escritório **SHOULD** cumprir os prazos publicados de cada etapa: triagem 5 dias úteis, revisão por pares 10, revisão de governança 5, validação do Escritório 5 | SHOULD |
| C8 | O Escritório **MAY** emitir uma decisão provisória pelo caminho rápido, por no máximo 60 dias corridos | MAY |
| C8a | Uma decisão provisória do caminho rápido **MUST** ser submetida ao próximo ciclo do comitê | MUST |
| C8b | Uma decisão provisória do caminho rápido **MUST NOT** se desviar de uma obrigação regulatória mapeada | MUST NOT |
| C9 | Toda ADR e AR **MUST** marcar todas as catorze linhas da matriz de aplicabilidade do estado | MUST |
| C10 | O Escritório **SHOULD** remover qualquer etapa de processo que não aumente mensuravelmente a chance de uma decisão ser registrada | SHOULD |

**Correção desta revisão (item de prontidão A10):** C1, C2, C6 e C8 originalmente carregavam mais de uma palavra-chave normativa na mesma cláusula, o que A10 não permite. Cada uma foi dividida em uma cláusula por palavra-chave (`C1`/`C1a`, `C2`/`C2a`, `C6`/`C6a`, `C8`/`C8a`/`C8b`), preservando o número original para a metade `MUST`/`MAY` e usando sufixo de letra para a metade adicional — convenção que não quebra nenhuma referência externa a `C1`, `C2`, `C6` ou `C8` já feita por outro artefato ou pelo registro, porque essas continuam existindo sem mudança de conteúdo. **C3 e C4 também foram reformuladas** (eram, na prática, proibições escritas com a palavra-chave `MUST`, não afirmações `MUST`); a palavra-chave agora corresponde ao que a cláusula realmente exige. **Em C4, a correção é também de sujeito vinculado** (nota do Enterprise Architect, Etapa 3, [ARC-123](/ARC/issues/ARC-123)): a redação anterior vinculava "o comitê de arquitetura MUST", sujeito que esta ADR do Escritório não tem autoridade para obrigar; a redação atual vincula "o Escritório MUST NOT", o único sujeito que esta ADR pode de fato obrigar. **Correção desta rodada ([ARC-135](/ARC/issues/ARC-135)): `C4` dividida em `C4`/`C4a`, com a redação literal pedida pelo Solution Architect ([ARC-124](/ARC/issues/ARC-124), Etapa 3)** — adoção literal herda o veredito (precedente 13, item 1, [checklist §4](/ARC/issues/ARC-2#document-readiness-checklist)), sem nova rodada de pares. A redação anterior de `C4` vinculava só "o Escritório", reduzindo o alcance normativo que a cláusula original ("Somente o comitê de arquitetura MUST definir...") tinha sobre qualquer parte; `C4`/`C4a` restauram esse alcance — `C4` vincula "nenhuma parte além do comitê de arquitetura", `C4a` isola a proibição de autodeclaração de aprovação (ver G16) como obrigação própria do Escritório. A divisão também alinha o tratamento de `C4` ao que esta mesma revisão já fez para `C1`/`C2`/`C6`/`C8`.

**Relação com as decisões de arbitragem do Arquiteto Principal (D-1…D-22, D-17(o)):** C1/C1a limitam o sistema de artefatos a ADR e AR; C2/C2a dizem que uma decisão fora do registro não vincula. As decisões `D-N` do Arquiteto Principal (trabalho de mesa ou arbitragem de conflito entre domínios, [registro §13](/ARC/issues/ARC-2#document-decision-registry)) são, por desenho, um **registro de arbitragem** — nem ADR nem AR — e por isso **não vinculam por si só** sob C1a/C2a: elas só passam a valer quando transcritas no texto da ADR ou AR afetada (decisão de arbitragem **D-17(o)**, [ARC-10](/ARC/issues/ARC-10#document-handoff-comite-primeira-onda)). O [registro §13](/ARC/issues/ARC-2#document-decision-registry) — "Índice de arbitragens do Arquiteto Principal" — existe exatamente para que essas decisões sejam rastreáveis sem que C1a seja lida como proibindo-as de existir como insumo de trabalho; ele não é um terceiro tipo de artefato ao lado de ADR e AR, é um índice de rastreabilidade sobre decisões que só se tornam vinculantes depois de transcritas.

**Quem roda o gate de prontidão sobre um artefato do próprio Governance (conflito de papel):** a Etapa 4 (revisão de governança, checklist de prontidão) é executada pelo Arquiteto de Governança para todo artefato do Escritório — exceto os produzidos pelo próprio Arquiteto de Governança (esta ADR e o catálogo de obrigações, [ARC-8](/ARC/issues/ARC-8)), porque o mesmo arquiteto não pode auditar a própria prontidão sem esvaziar o portão. Nesta rodada do retrabalho do gate [ARC-10](/ARC/issues/ARC-10#document-handoff-comite-primeira-onda), o Enterprise Architect executa a Etapa 4 sobre `ADR-0001` e sobre o catálogo ([ARC-75](/ARC/issues/ARC-75)); a Etapa 3 (revisão por pares, abaixo) continua a exigir o Enterprise Architect como revisor **sempre** (modelo operacional §5), o que é um gate diferente e não é o mesmo conflito.

**Por que esta opção:** a Opção A é a única candidata que atende a todos os cinco fatores `Obrigatório` (D1, D2, D4, D6, D8). A Opção C pontua mais alto em D1 e D3, mas falha decisivamente em D4, e um sistema de governança que os times não conseguem usar a tempo não governa nada; apenas move as decisões para algum lugar não registrado. O único mérito da Opção B é a velocidade, comprada abandonando o registro por completo.

**Alternativas rejeitadas:** Opção B — falha em D1, D2, D3, D5, D8 (§3). Opção C — falha em D4, não pode ser suprida com a equipe atual (§3). Opção D — enfraquece D3, D4 e D5 e deixa o problema de composição para os times de entrega (§3). Opção E (docs-as-code) — falha em D4 pelo mesmo motivo estrutural da Opção C, com menor severidade; candidata a reavaliação futura junto com a Opção C, não com os times atuais (§3).

### 4.1 Aplicabilidade no estado

| Plataforma | Aplica-se? | Restrição, diferença ou exclusão |
| --- | --- | --- |
| `DC-AA` — DC on-prem, active-active | N/A | Esta é uma decisão de processo do Escritório, sem realização específica de plataforma. Ela governa como decisões sobre esta plataforma são registradas, não a plataforma em si. |
| `OCP` — OpenShift on-prem | N/A | Como acima. |
| `VM-ON` — VMs / bare metal on-prem | N/A | Como acima. |
| `AWS-K8S` — Kubernetes gerenciado na AWS | N/A | Como acima. |
| `AWS-VM` — compute AWS | N/A | Como acima. |
| `AZR-K8S` — Kubernetes gerenciado no Azure | N/A | Como acima. |
| `AZR-VM` — compute Azure | N/A | Como acima. |
| `OCI-K8S` — Kubernetes gerenciado na OCI | N/A | Como acima. |
| `OCI-VM` — compute OCI | N/A | Como acima. |
| `DATA-REL` — bases de dados relacionais | N/A | Como acima. |
| `DATA-NREL` — bases de dados não relacionais | N/A | Como acima. |
| `AGW-AWS` — AWS API Gateway | N/A | Como acima. |
| `AGW-AZR` — Azure API Management | N/A | Como acima. |
| `AGW-AXW` — Axway API Gateway | N/A | Como acima. |

Toda linha é `N/A` em vez de `Sim`, e essa é a resposta correta para uma decisão de processo: nenhuma plataforma pode encontrar esta questão. A matriz ainda é preenchida por completo, porque um leitor precisa conseguir distinguir não aplicabilidade deliberada de uma omissão. Artefatos a partir de [ARC-3](/ARC/issues/ARC-3) terão entradas substantivas nestas linhas.

### 4.2 Escopo de workloads

- **Aplica-se a:** o próprio Escritório de Arquitetura, todos os oito arquitetos, e toda ADR e AR produzida a partir da data de vigência. Ela vincula times de entrega apenas indiretamente — através das ADRs aceitas cuja produção ela governa.
- **Workloads existentes:** nenhum. Nenhum artefato do Escritório antecede esta ADR; o Escritório está sendo criado agora. Os sete padrões do Escritório listados na §7 do registro passam a ser governados por esta ADR na aceitação.
- **Explicitamente fora de escopo:** o conteúdo técnico de qualquer decisão; os processos de entrega de projetos, risco e compliance do banco; a autoridade de aprovação, que permanece com o comitê de arquitetura; e a documentação interna de um time de entrega.

## 5. Consequências

### Positivas

- Toda decisão que afeta o estado regulado tem um responsável nomeado, uma data, as opções rejeitadas, e uma seção de conformidade rastreável — o registro que a pergunta de um regulador de fato exige.
- Um time de entrega tem um único lugar para descobrir se uma decisão existe, e um único formato para lê-la.
- A substituição é explícita e integral, então sempre existe exatamente uma resposta vigente para qualquer questão decidida.
- A não conformidade se torna visível e com prazo de validade, em vez de silenciosa e permanente, e três waivers contra uma cláusula forçam a reconsideração do padrão em vez de sua erosão.
- Os padrões provisórios estão isolados em dois parâmetros e um catálogo, então confirmar ou reverter qualquer um deles é uma edição pequena, não um exercício de reautoria.
- A autoridade do Escritório é explicitamente restrita: um teste de ADR documentado devolve decisões locais aos times, o que é o que mantém o processo crível o suficiente para ser usado.

### Negativas, e o que fica mais difícil

- Uma decisão agora leva aproximadamente 25 dias úteis pelo caminho padrão, em vez de uma conversa. Os times sentirão isso, e alguns tentarão evitá-lo; o caminho rápido e o teste da ADR são as mitigações, e são imperfeitas.
- O Arquiteto de Governança se torna uma dependência para triagem, veredito de prontidão, escrita no registro e classificação de waivers. Na ausência dele, essas atividades paralisam. Nenhum substituto está definido, o que é um ponto único de falha real e é levantado como questão aberta.
- Escrever uma ADR neste template é trabalho real — duas opções pontuadas, uma tabela de conformidade completa, conformidade por cláusula. Alguns arquitetos vão achar isso mais pesado do que a decisão justifica, e para decisões genuinamente pequenas eles estarão certos; o teste da ADR existe para manter essas de fora.
- Um registro mantido manualmente convida a desalinhamento entre uma linha e seu artefato. Somente a revisão mensal capta isso.
- A conformidade obrigatória por cláusula vai expor que muitas decisões não podem de fato ser verificadas, o que é desconfortável, visível, e melhor sabido do que não.
- A matriz de 14 linhas adiciona volume a todo artefato, inclusive aos que têm a maioria das linhas `N/A` — como este demonstra.

### O que esta decisão impede no futuro

- Adotar um repositório de EA modelado depois significa migrar ou abandonar o conjunto de documentos acumulado. O custo de reversão cresce com a contagem de artefatos: barato com dez artefatos, um projeto em si com duzentos.
- Fixar quatro status torna um ciclo de vida mais rico (um `rejeitada` distinto, ou substituição parcial) uma ADR substituta, não um ajuste.
- Substituição apenas-integral significa que uma pequena mudança em uma ADR grande exige reemiti-la por completo. Essa é uma troca deliberada de esforço do autor por certeza do leitor, e não pode ser relaxada seletivamente depois sem reabrir esta decisão.

### Impacto de migração no estado existente

| O que existe hoje | O que precisa mudar | Responsável | Até quando |
| --- | --- | --- | --- |
| Sete padrões do Escritório rascunhados sob esta task | Listados na §7 do registro e governados por esta ADR na aceitação | Arquiteto de Governança | Na aceitação |
| Sete tasks da primeira onda ([ARC-3](/ARC/issues/ARC-3)–[ARC-9](/ARC/issues/ARC-9)) bloqueadas por falta de formato | Adotar estes templates e IDs reservados; o rascunho pode começar enquanto esta ADR está `proposta`, já que nenhum artefato pode ser submetido antes que o comitê decida sobre esta de qualquer forma | Arquitetos de domínio responsáveis | No início do rascunho |
| Nenhum registro de decisão de qualquer tipo | O registro se torna o índice; nenhuma reconstrução retrospectiva é tentada | Arquiteto de Governança | Na aceitação |
| **Correção desta revisão:** o catálogo de obrigações regulatórias já existe e está publicado, `proposta` | Conjunto interino em nível geral usado até [ARC-8](/ARC/issues/ARC-8) ser publicado — já ocorreu; artefatos atualizam apenas os IDs de obrigação, que não mudaram (decisão de compatibilidade do próprio catálogo, §1) | Arquiteto de Governança | Concluído — [ARC-8](/ARC/issues/ARC-8) |

## 6. Impacto de conformidade e regulatório

Profundidade conforme `PARAM-REGDEPTH` (nível geral). IDs de obrigação resolvidos através do [modelo operacional](/ARC/issues/ARC-2#document-operating-model) §3.

| Regime | ID(s) de obrigação aplicável(is) | O que exige, em substância | Como esta decisão a endereça | Risco residual | Controle / evidência de que se mantém |
| --- | --- | --- | --- | --- | --- |
| BACEN/CMN | `OBL-CYB`, `OBL-CLOUD`, `OBL-RES` | Uma política de cibersegurança documentada, classificação e proteção de dados, prevenção e resposta a incidentes, testes periódicos e gestão de risco de terceiros (`OBL-CYB`); comunicação prévia antes da contratação de processamento, armazenamento e serviços em nuvem relevantes — **comunicação prévia × posterior à contratação: interpretação regulatória a confirmar por Compliance (ver catálogo §10)** —, diligência sobre o provedor, cláusulas contratuais de acesso, auditoria, subcontratação, continuidade e rescisão, e arranjos definidos quando o processamento ocorre no exterior (`OBL-CLOUD`); planos de continuidade e recuperação, objetivos de recuperação definidos, testes de cenário e estratégias de saída para serviços críticos (`OBL-RES`) | Decisões de arquitetura que tocam esses temas devem ser registradas como ADRs com um responsável nomeado, uma seção de conformidade e um registro de revisão por pares; o Security Architect é revisor obrigatório sempre que identidade, segredos, chaves, exposição ou dados pessoais são tocados; ARs devem declarar tier de DR, RTO, RPO, comportamento de failover, modos de falha e quais cenários foram de fato testados; toda ADR e AR deve responder explicitamente se altera um arranjo relevante de processamento/armazenamento/nuvem e se o processamento ocorre fora do Brasil, sinalizando para compliance quando for o caso | O Escritório registra decisões; não implementa controles. Uma decisão bem registrada ainda pode ser um controle fraco, e esta ADR não consegue detectar isso. Objetivos de recuperação declarados não são objetivos testados — o item de prontidão R16 força um `nenhum testado` honesto em vez de evitá-lo. O Escritório sinaliza; não determina se a notificação regulatória é exigida nem a submete | Linhas do registro, seções de conformidade por cláusula, registros de revisão por pares com vereditos do Security Architect, seções de resiliência das ARs, registro de cenários testados, as duas perguntas de conformidade obrigatórias sobre arranjos relevantes e processamento fora do Brasil em todo artefato |
| LGPD | `OBL-LGPD` | Base legal, limitação de finalidade e minimização, direitos do titular, controles de segurança e acesso, registros de tratamento, condições de transferência internacional, tratamento de incidentes, e o papel do encarregado de proteção de dados | Todo artefato deve declarar se dados pessoais estão envolvidos, quais categorias em nível geral, e se o processamento ocorre no exterior; essas respostas são direcionadas à função de privacidade do banco em vez de resolvidas pelo Escritório | O Escritório identifica o envolvimento de dados pessoais arquiteturalmente. Licitude, base legal e direitos do titular não são questões de arquitetura e não são decididas aqui | Perguntas sobre dados pessoais e transferência internacional em todo artefato; classificação `W3` para qualquer waiver que toque esta obrigação |
| PCI-DSS | `N/A` | — | **Não aplicável. Bloco de escopo PCI-DSS (correção desta revisão, item de prontidão A17): efeito sobre o escopo do CDE — não altera.** Esta ADR é uma decisão de processo do Escritório; ela não armazena, transmite ou processa dados de portador de cartão, e não introduz nem altera nenhum sistema conectado a um ambiente de dados de cartão (CDE). O ID `OBL-PCI` aparece em D1 (fatores de decisão, §2) e em C6/C6a (§8) apenas como obrigação que o sistema de artefatos precisa saber tratar — não como afirmação de que esta ADR toca o CDE; esta linha da §6 é a declaração de escopo que A17 exige para todo ID referenciado em qualquer parte do artefato. Ela estabelece, porém, a obrigação (`OBL-PCI` no catálogo, template de ADR §6, checklist item A16/A18) de que toda ADR e AR futura declare seu efeito sobre o escopo do PCI-DSS quando pertinente | Nenhum — nada a mitigar nesta decisão. O risco residual real está em artefatos futuros que tocam o CDE e é tratado por eles, não por esta ADR | A própria existência das linhas obrigatórias de PCI-DSS no template de ADR (§6), no template de AR (§8) e no checklist de prontidão (itens G17, A16, A18, R22) |

| Pergunta | Resposta |
| --- | --- |
| Esta decisão toca dados pessoais? | **Não.** É uma decisão de processo; não cria nenhum tratamento de dados pessoais. Ela exige, sim, que todo artefato subsequente declare o envolvimento de dados pessoais. |
| Ela envolve processamento ou armazenamento fora do Brasil? | **Não.** Nenhum dado é processado ou armazenado por esta decisão. |
| Ela muda um arranjo relevante de processamento de dados, armazenamento ou serviço em nuvem? | **Não.** Nenhum arranjo com provedor é criado ou alterado. Ela estabelece o sinalizador que identifica mudanças futuras para compliance. |
| Ela muda a postura de resiliência ou recuperação de um serviço crítico? | **Não.** Nenhum serviço é afetado. RTO/RPO não mudam porque nenhum sistema está em escopo. |
| Ela muda a detecção de incidentes, logging ou rastreabilidade? | **Não.** Nenhum controle de sistema muda. Ela melhora a rastreabilidade de *decisões de arquitetura*, o que é um resultado de registro, não um controle de sistema, e não deve ser reportado como tal. |
| Ela cria ou muda uma dependência de terceiro / fornecedor? | **Não.** O sistema de artefatos usa apenas ferramentas que o Escritório já tem; a Opção C foi rejeitada em parte para evitar introduzir uma dependência de ferramenta licenciada. |

> O Escritório mapeia obrigações para controles de arquitetura em nível geral. Ele não emite parecer jurídico, e a suficiência jurídica é confirmada pelas funções de compliance e jurídico do banco.

## 7. Conformidade e verificação

| Cláusula | Como a conformidade é verificada | Onde a evidência vive | Automatizável? |
| --- | --- | --- | --- |
| C1 | Todo artefato do Escritório é uma ADR, uma AR, ou um dos sete padrões na §7 do registro | Registro de decisões | Parcialmente — uma verificação de convenção de chave de documento |
| C1a | Nenhum outro tipo de artefato aparece no registro sem uma ADR substituta de `ADR-0001` | Registro de decisões | Parcialmente — uma verificação de convenção de chave de documento |
| C2 | Todo documento de artefato tem uma linha no registro desde o início do rascunho; reconciliado na revisão mensal | Registro de decisões; registro da revisão mensal | Parcialmente — comparação de conjuntos de IDs |
| C2a | Nenhum time de entrega trata uma decisão sem linha no registro como vinculante | Comunicação do Escritório aos times; ausência de incidente atribuível a decisão não registrada | Não — depende de os times reportarem o desvio |
| C3 | Todo artefato submetido tem um veredito APROVADO datado, nomeando o revisor, no pacote do comitê | Pacotes do comitê; fichas de pontuação de prontidão | Não — o veredito exige inspeção |
| C4 | Nenhuma linha do registro alcança `aceita` sem um resultado de comitê registrado | Datas de status do registro vs. resultados do comitê | Parcialmente — uma linha com `aceita` e sem resultado é detectável |
| C4a | Nenhum artefato se descreve como aprovado; o item de prontidão G16 captura esta cláusula | Veredictos de prontidão | Parcialmente — presença de linguagem de aprovação é verificável por padrão de texto |
| C5 | Todo desvio conhecido de uma cláusula `MUST` aceita tem uma linha no registro com um concedente e uma expiração | Registro de waivers | Não — depende dos times divulgarem, por isso recusas devem oferecer uma alternativa |
| C6 | Nenhum corpo de artefato contém referência a lei, resolução ou artigo fora de um ID de obrigação — item de prontidão G7 | Veredictos de prontidão | Sim — uma verificação de padrão de texto |
| C6a | Toda referência regulatória usada em um artefato resolve a um ID de obrigação do catálogo único — item de prontidão A4/A17 | Veredictos de prontidão | Parcialmente — presença do ID é verificável; a resolução correta exige leitura |
| C7 | Dias transcorridos por etapa, amostrados trimestralmente contra os prazos | Timestamps das tasks do Escritório | Parcialmente |
| C8 | Toda linha `Provisória` tem uma expiração dentro de 60 dias de sua data de proposta | Registro de decisões | Sim — uma comparação de datas |
| C8a | Toda decisão provisória tem uma submissão ao comitê registrada antes da expiração | Registro de decisões; pacotes do comitê | Sim — uma comparação de datas |
| C8b | Nenhuma decisão provisória se desvia de uma obrigação regulatória mapeada no catálogo | Veredictos de prontidão; seção de conformidade do artefato provisório | Não — exige leitura de mérito |
| C9 | Todas as catorze linhas da matriz marcadas — itens de prontidão G9 e G10 | Veredictos de prontidão | Sim — uma verificação de contagem de linhas e vazios |
| C10 | Falhas repetidas de prontidão no mesmo item, e tendências de tempo por etapa, revisadas trimestralmente | Veredictos de prontidão | Não — isso é um julgamento, e é do próprio Escritório fazê-lo |

**C5 é a cláusula fraca e deve ser lida como tal.** A conformidade depende dos times divulgarem a não conformidade. O Escritório não tem detecção automatizada de um time que simplesmente não cumpre e não pergunta. As mitigações são indiretas — tornar os waivers baratos de solicitar, fazer com que recusas venham com uma alternativa, e tratar três waivers em uma cláusula como evidência contra o padrão, não contra os times. Afirmar uma garantia de aplicação mais forte do que esta seria a primeira afirmação falsa neste registro.

## 8. Exceções

- **Waivers possíveis contra esta ADR:** Parcialmente.
- **Nível por cláusula:**
  - C1, C1a, C2, C2a, C3, C4, C4a, C5, C6, C6a, C8a, C8b, C9 — **não sujeitas a waiver**.
  - C7, C8, C10 — não aplicável; cláusulas `SHOULD` e `MAY` não exigem waiver. **Correção desta revisão:** C7 carregava `W1` numa revisão anterior, o que contradizia a própria regra que C8/C10 já enunciavam (cláusula `SHOULD`/`MAY` não exige waiver); C7 é `SHOULD`, então o deslize de prazo para um artefato específico é registrado localmente na task, sem passar pelo processo de waiver.
- **Cláusulas que não podem receber waiver, com motivos:**
  - **C1/C1a** — conceder waiver significa que o sistema de artefatos do Escritório não é o sistema de artefatos do Escritório.
  - **C2/C2a** — uma decisão não registrada não pode ser evidenciada, substituída ou encontrada. Esta é a própria regra do registro.
  - **C3** — o portão de prontidão é o único piso objetivo de qualidade nas submissões ao comitê; um portão sujeito a waiver não é um portão.
  - **C4/C4a** — o Escritório não tem autoridade para conceder waiver da autoridade de aprovação do comitê, nem da proibição de autodeclaração de aprovação.
  - **C5** — um waiver do próprio requisito de waiver é uma contradição.
  - **C6/C6a** — implementam o tratamento de `OBL-CYB`, `OBL-CLOUD`, `OBL-RES`, `OBL-LGPD` e `OBL-PCI`; pelo item de prontidão A23, cláusulas que implementam uma obrigação mapeada são `W3` ou não sujeitas a waiver, e o Escritório não pode conceder `W3`.
  - **C8a/C8b** — são a salvaguarda do próprio caminho rápido (submissão ao próximo ciclo, não desviar de obrigação mapeada); um caminho de excepcionalidade sem essas duas garantias deixaria de ser um caminho rápido controlado e passaria a ser um desvio permanente sem registro.
  - **C9** — a matriz é o que força o estado real deste banco a ser endereçado; conceder waiver reintroduz a suposição de nuvem única e gateway único que o Escritório existe para prevenir.

## 9. Questões abertas

| # | Questão | Quem deve responder | Necessário até |
| --- | --- | --- | --- |
| 1 | Confirmar `PARAM-REGDEPTH` = nível geral, ou exigir citação em nível de artigo com validação jurídica | Usuário / comitê de arquitetura, com compliance e jurídico | Antes da primeira submissão ao comitê |
| 2 | Cadência do comitê e data de congelamento do pacote | Comitê de arquitetura | Define se o caminho rápido é raro ou rotineiro |
| 3 | O comitê quer um quinto status (`rejeitada`) em vez de sobrecarregar `descontinuada` com três casos? | Comitê de arquitetura | Antes que o volume torne a renomeação cara |
| 4 | Qual função do banco corrobora formalmente um waiver `W3` junto com o comitê? | Comitê de arquitetura, com risco e compliance | Antes da primeira solicitação `W3` |
| 5 | Quem substitui o Arquiteto de Governança na triagem, nos veredictos de prontidão e na escrita do registro? | Arquiteto Principal | Antes da primeira ausência, e é um ponto único de falha real hoje |
| 6 | Para artefatos que tocam o ambiente de dados de cartão (CDE), o comitê quer exigir validação formal de um QSA além da leitura em nível de controle feita pelo Escritório? | Comitê de arquitetura, com compliance de cartões/segurança | Antes da primeira ADR ou AR que não seja `N/A` em PCI-DSS |

**Nota:** a questão sobre `PARAM-LANG` foi removida desta lista porque o board já decidiu, em 2026-10-04, que o idioma dos artefatos é pt-BR. Esta revisão da ADR-0001 já reflete essa decisão (§0, §1).

## 10. Revisão por pares e discordâncias

| Revisor | Papel | Veredito | Comentário / discordância |
| --- | --- | --- | --- |
| Enterprise Architect | Obrigatório — coerência entre domínios (modelo operacional §5, Etapa 3) | **Concorda com comentário** (2026-10-06, [ARC-123](/ARC/issues/ARC-123)) | Veredito emitido em 2026-10-06 pelo Enterprise Architect, sobre `revisionNumber: 3` (confirmou que a revisão 4/5 só acrescentou links de §10 e nota de âncora, sem tocar conteúdo normativo — veredito vale sobre o texto publicado). (1) A divisão de `C1`/`C2`/`C6`/`C8` pelo item A10 preserva o conteúdo normativo original — comparação linha a linha contra a redação anterior não ampliou nem reduziu obrigação, apenas separou por palavra-chave; `C1a`/`C2a` não entram em conflito com a leitura de D-17(o) sobre as decisões de arbitragem do Arquiteto Principal. (2) `C3` reformulada para `MUST NOT` é fiel à redação anterior: já era, em substância, proibição, só com rótulo de palavra-chave incorreto. (3) `C4` também é fiel em substância — o comentário: a redação anterior vinculava "o comitê de arquitetura MUST", sujeito que esta ADR não tem autoridade para obrigar; a redação atual move o sujeito vinculado para "o Escritório MUST NOT", o único sujeito que esta ADR pode de fato obrigar, consistente com D6, G16 e a leitura de "Etapa 5 é endosso, não aceitação". Não é ampliação de obrigação — recomenda apenas que a nota de revisão da §4 registre esta como correção de **sujeito vinculado**, não só "C3 e C4 também foram reformuladas", para que o precedente resista a uma comparação direta das duas redações por um auditor. Nenhum dos três pontos é motivo de discordância. Catálogo de obrigações (ARC-8): veredito separado, registrado em [ARC-123](/ARC/issues/ARC-123) — **concorda**, sem ressalva. Nada aqui é aprovação: é revisão por pares da Etapa 3, não endosso do Escritório nem decisão do comitê de arquitetura do banco. Transcrito literalmente pelo Arquiteto de Governança a partir do comentário do Enterprise Architect em [ARC-123](/ARC/issues/ARC-123) (2026-10-06). |
| Solution Architect | Obrigatório — revisor de domínio diferente de Governance (modelo operacional §5, Etapa 3) | **Concorda com comentário** (2026-10-06, [ARC-124](/ARC/issues/ARC-124)) | Veredito emitido em 2026-10-06 pelo Solution Architect, sobre `revisionNumber: 5` — a nota de âncora da própria §10 registra que as revisões 4 e 5 não tocaram cláusula normativa, fator ou seção de conformidade em relação à `revisionNumber: 3` ancorada em [ARC-124](/ARC/issues/ARC-124); conferi o histórico de revisões e confirmo que o conteúdo em foco é idêntico ao da revisão 3. **(1) A divisão por palavra-chave do item A10 preserva o conteúdo normativo — conferida cláusula por cláusula contra a revisão 2.** `C1`/`C1a`, `C2`/`C2a`, `C6`/`C6a` e `C8`/`C8a`/`C8b` reproduzem exatamente as duas metades de cada cláusula original, sem ampliar nem reduzir obrigação; a convenção de manter o número original para a metade `MUST`/`MAY` preserva as referências externas. O acréscimo de "desta" em `C1a` ("sem uma ADR substituta **desta**") é desambiguação correta, não ampliação: a cláusula só poderia se referir a esta ADR. Concordo também com a reclassificação de `C8a`/`C8b` na §8, de "não aplicável" para "não sujeitas a waiver" — ela fecha uma lacuna da redação antiga, em que obrigações `MUST`/`MUST NOT` embutidas numa cláusula `MAY` ficavam sob a classificação da `MAY`, e não torna disponível nenhum waiver que antes existisse. **(2) `C3` é fiel.** A redação anterior ("Nenhum artefato **MUST** ser submetido…") era uma proibição escrita com a palavra-chave `MUST`; `MUST NOT` é o que ela já exigia. **(3) `C4` é fiel na intenção, mas reduziu o alcance normativo — é o único ponto que peço correção.** A redação anterior era "**Somente o comitê de arquitetura MUST definir** o status de um artefato como `aceita`": uma exclusividade que vinculava qualquer parte. O sujeito normativo da nova `C4` é "**O Escritório** MUST NOT definir…", e a exclusividade ("essa autoridade pertence exclusivamente ao comitê") passou para a metade explicativa, depois do ponto e vírgula, fora da palavra-chave. Pela letra da nova cláusula, um time de entrega ou qualquer outra parte que marque um artefato como `aceita` não é mais alcançado. O efeito prático hoje é pequeno — o registro é mantido pelo Escritório —, mas a §7 verifica `C4` por dois métodos e a §8 justifica sua não sujeição a waiver dizendo que "o Escritório não tem autoridade para conceder waiver da autoridade de aprovação do comitê": as duas pressupõem a leitura ampla. Correção sugerida, que mantém a palavra-chave e o cumprimento de A10: "O status `aceita` de um artefato **MUST NOT** ser definido por nenhuma parte além do comitê de arquitetura" como `C4`, e "Nenhum artefato do Escritório **MUST NOT** se descrever como aprovado; a ação da Etapa 5 é endosso, não aceitação (ver G16)" como `C4a`. A divisão em `C4`/`C4a` também é mais coerente com o tratamento que esta mesma revisão deu a `C1`/`C2`/`C6`/`C8`: a nova `C4` carrega duas obrigações distintas sob uma palavra-chave, verificadas por dois métodos diferentes na §7. **(4) Demais seções tocadas, conferidas e corretas:** `D6` reclassificado de `Regulatório` para `Operabilidade` — concordo, nenhuma obrigação externa mapeada exige a restrição, que vem do mandato do próprio comitê; o peso segue `Obrigatório`, então não há efeito na pontuação. Contagens: cinco fatores `Obrigatório` são `D1`, `D2`, `D4`, `D6`, `D8`, confirmados na tabela da §2; a Opção B falha exatamente em `D1`, `D2`, `D3`, `D5`, `D8`, três deles `Obrigatório` (`D1`, `D2`, `D8`); quatro etapas com prazo somam 5+10+5+5 = 25 dias úteis, coerentes com `C7`. A Opção E pontua os oito fatores e sua falha em `D4` é consistente com "a Opção A é a única que atende aos cinco `Obrigatório`"; o gatilho de reavaliação conjunto com a Opção C é bem colocado. "Sete padrões" na §4.2 e na tabela de migração, e a linha do catálogo como publicado, conferem. §8: a cobertura de cláusulas é completa — doze não sujeitas a waiver mais `C7`, `C8`, `C10` como não aplicável fecham as quinze cláusulas; concordo com a retirada de `W1` de `C7`. §6, bloco de escopo PCI-DSS: concordo com "não altera" e com a fundamentação — esta ADR é decisão de processo, não armazena, transmite nem processa dado de portador de cartão, e não introduz nem altera sistema conectado ao CDE; `OBL-PCI` aparece em `D1` e em `C6`/`C6a` apenas como obrigação que o sistema de artefatos precisa saber tratar. §7: a linha de `C2` deixou de verificar "e vice-versa" (toda linha do registro tem documento) — não é perda normativa, porque a cláusula nunca exigiu a recíproca, mas vale confirmar que a reconciliação mensal continua cobrindo o sentido inverso na prática. Nada neste veredito é aprovação: é revisão por pares da Etapa 3, não endosso do Escritório nem decisão do comitê de arquitetura do banco. Transcrito literalmente pelo Arquiteto de Governança a pedido do Solution Architect ([ARC-124](/ARC/issues/ARC-124), comentário de 2026-10-06), que não tem permissão de escrita neste documento (ARC-2). |

**Nota de âncora:** a revisão 4 (`revisionId: 7375964a-a715-4024-900a-cd9436662cb5`) só acrescentou, nesta tabela, os links para [ARC-123](/ARC/issues/ARC-123) e [ARC-124](/ARC/issues/ARC-124) — nenhuma cláusula normativa, fator ou seção de conformidade mudou entre a revisão 3 e esta. O foco de cada pedido permanece o conteúdo gravado na revisão 3; os dois revisores podem ler a revisão 4 sem reabertura.

**Etapa 3 concluída — ambos os revisores obrigatórios responderam, nenhum com discordância.** O item G11 exige que todo revisor obrigatório carregue um veredito de concorda, concorda com comentário, discorda, ou sem resposta — com pedido datado e prazo vencido, pelo precedente 5 de G11 ([checklist §4](/ARC/issues/ARC-2#document-readiness-checklist)). O Solution Architect respondeu em 2026-10-06 com **concorda com comentário** (linha acima), apontando uma correção pendente em `C4` (alcance normativo reduzido na divisão A10). O Enterprise Architect respondeu em 2026-10-06 com **concorda com comentário** (linha acima), pedindo apenas que a nota de revisão da §4 nomeie a correção de `C4` como correção de **sujeito vinculado** — satisfeito, sem pedir mudança de cláusula. Nenhum dos dois comentários aponta ampliação de obrigação. **Correção da leitura anterior ([ARC-135](/ARC/issues/ARC-135)):** a nota de revisão anterior lida aqui dizia que a correção substantiva sugerida pelo Solution Architect (dividir `C4` em `C4`/`C4a`) exigiria um novo pedido datado aos dois revisores. Isso estava errado à luz do precedente 13, item 1 ([checklist §4](/ARC/issues/ARC-2#document-readiness-checklist)): o Solution Architect ofereceu a redação literal das duas cláusulas em seu próprio veredito ([ARC-124](/ARC/issues/ARC-124)), e a adoção literal dessa redação herda o veredito já emitido, sem exigir nova rodada. A correção foi aplicada agora em `C4`/`C4a` (§4), com a redação literal do revisor. Com os dois vereditos não-bloqueantes registrados e a correção de `C4`/`C4a` aplicada, esta ADR passa a depender apenas da auditoria de prontidão do próprio Escritório ([ARC-75](/ARC/issues/ARC-75), Enterprise Architect, por conflito de papel) antes de retornar ao gate do Arquiteto Principal. Nada aqui é aprovação.

## 11. Histórico de status

| Data | De → Para | Quem | Por quê |
| --- | --- | --- | --- |
| 2026-10-04 | `—` → `proposta` | Arquiteto de Governança | Rascunho iniciado; linha do registro criada |
| 2026-10-06 | `proposta` (sem mudança) | Arquiteto de Governança | **Retrabalho do gate [ARC-10](/ARC/issues/ARC-10#document-handoff-comite-primeira-onda) (classe S), via [ARC-72](/ARC/issues/ARC-72).** Item de prontidão A10: `C1`, `C2`, `C6`, `C8` divididas por palavra-chave (`C1a`, `C2a`, `C6a`, `C8a`, `C8b` novas). `C3`/`C4` reformuladas para `MUST NOT`, refletindo o que já exigiam. `D6` (§2) corrigido de `Regulatório` para `Operabilidade` — não há obrigação externa mapeada. Nova §3 Opção E (docs-as-code) avaliada e rejeitada. Nova nota de relação entre C1/C1a/C2/C2a e as decisões de arbitragem D-1…D-17 (D-17(o)); nova nota sobre quem audita a prontidão de artefatos do próprio Governance nesta rodada (Enterprise Architect, [ARC-75](/ARC/issues/ARC-75)). Bloco de escopo PCI-DSS explícito acrescentado à §6 (item A17: "não altera"). `C7` perde o nível de waiver `W1` em §8 (SHOULD não exige waiver, item A22/C7). Contagens corrigidas: cinco fatores `Obrigatório` (não quatro), cinco falhas da Opção B (não quatro), quatro etapas com prazo (não cinco). "Seis padrões" corrigido para "sete" (§4.2, tabela de migração); linha de migração do catálogo atualizada para refletir que [ARC-8](/ARC/issues/ARC-8) já foi publicado. Segundo revisor de pares nomeado (Solution Architect); pedido de revisão por pares datado aberto para Enterprise Architect e Solution Architect, prazo 2026-10-20 (§10). Nada aqui é aprovação; permanece `proposta`, pendente dos vereditos de §10. |
| 2026-10-06 | `proposta` (sem mudança) | Arquiteto de Governança | Transcrição do veredito de Etapa 3 do Solution Architect ([ARC-124](/ARC/issues/ARC-124)): **concorda com comentário** sobre `ADR-0001`, registrado em §10, com uma correção pendente em `C4` ainda não aplicada (ver nota em §10). Aguardando o veredito do Enterprise Architect ([ARC-123](/ARC/issues/ARC-123)); `ADR-0001` permanece `proposta`. Nada aqui é aprovação. |
| 2026-10-06 | `proposta` (sem mudança) | Arquiteto de Governança | **Achado pendente de [ARC-75](/ARC/issues/ARC-75), registrado em [ARC-128](/ARC/issues/ARC-128).** A devolução de [ARC-72](/ARC/issues/ARC-72) pedia que a linha "comunicação prévia" desta §6 fosse marcada "a confirmar por Compliance", com a mesma nota em `ADR-0002` §6 e na §5 do catálogo ([ARC-8](/ARC/issues/ARC-8)), sinalizando a tensão prévia × posterior que o hand-off de [ARC-10 §4.3](/ARC/issues/ARC-10#document-handoff-comite-primeira-onda) pede. Essa marcação nunca chegou à substância desta ADR: a §6 segue com "comunicação prévia" sem nenhuma nota. Confirmado o mesmo em `ADR-0002` §6 (Enterprise Architect, encaminhado) e na §5 do catálogo, cuja linha operativa foi reescrita para uma pergunta diferente (diligência sobre contratos já vigentes) sem reproduzir a tensão. Nenhuma correção de conteúdo normativo é feita agora — mudaria o mérito da §6 e exigiria novo pedido de revisão por pares datado, pela mesma regra já aplicada às oito correções não bloqueantes de [ARC-127](/ARC/issues/ARC-127). Fica registrada como pendência explícita para a próxima revisão de mérito desta ADR, dono Arquiteto de Governança. Nada aqui é aprovação; `ADR-0001` permanece `proposta`. |
| 2026-10-06 | `proposta` (sem mudança) | Arquiteto de Governança | **Retrabalho do gate [ARC-76](/ARC/issues/ARC-76#document-handoff-comite-primeira-onda-r2) (2ª rodada), classe F, via [ARC-135](/ARC/issues/ARC-135).** (1) Pendência de [ARC-128](/ARC/issues/ARC-128) fechada: a linha "comunicação prévia" da §6 (linha operativa `OBL-CLOUD`) agora nomeia a marcação "comunicação prévia × posterior à contratação — interpretação regulatória a confirmar por Compliance (ver catálogo §10)", igual à mesma nota no catálogo §5. "Prévia" não foi trocada por "posterior" (D-22(l)); a marcação é editorial (precedente 13, item 3) e não reabre revisão por pares. (2) Nota de relação com as decisões de arbitragem (§4) atualizada de "D-1…D-17" para "D-1…D-22", e "terceiro tipo de registro" corrigido para "registro de arbitragem" — D-17(o) já rejeitava a leitura de "terceiro tipo" de artefato; a redação anterior desta ADR usava um termo diferente do que a própria regra fixa. (3) `C4` dividida em `C4`/`C4a` (§4, §7, §8), com a redação literal pedida pelo Solution Architect ([ARC-124](/ARC/issues/ARC-124)) — adoção literal herda o veredito (precedente 13, item 1), sem nova rodada de pares; a nota de Etapa 3 (§10) corrigida para refletir que a correção foi aplicada, não deixada como pendência de mérito. Nenhuma destas três correções é mudança de mérito fora do que D-22(l)/precedente 13 já autorizam sem nova rodada de pares. `ADR-0001` permanece `proposta`; nada aqui é aprovação — o comitê de arquitetura do banco aprova. |


