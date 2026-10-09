# Modelo Operacional do Escritório de Arquitetura

**Tipo de artefato:** Padrão de processo do Escritório
**Responsável:** Arquiteto de Governança
**Status:** Rascunho — pronto para revisão do comitê de arquitetura (guild committee). Nada neste documento está aprovado.
**Versão:** 0.1
**Relacionados:** [Template de ADR](/ARC/issues/ARC-2#document-adr-template) · [Template de AR](/ARC/issues/ARC-2#document-ar-template) · [Registro de decisões](/ARC/issues/ARC-2#document-decision-registry) · [Checklist de prontidão](/ARC/issues/ARC-2#document-readiness-checklist) · [Processo de exceção e waiver](/ARC/issues/ARC-2#document-exception-and-waiver-path)

---

## 1. Para que serve este documento

O Escritório produz dois tipos de artefato e nada além disso:

| Artefato | O que é | Quando se escreve um |
| --- | --- | --- |
| **ADR** (Architecture Decision Record) | Uma decisão, com as opções rejeitadas e o motivo | Uma escolha precisa ser feita e é difícil revertê-la |
| **AR** (Architecture Reference) | Uma referência completa, de ponta a ponta, que um time de entrega pode copiar | Um padrão será construído mais de uma vez e queremos que seja construído da mesma forma |

Tudo o mais que o Escritório escreve — este documento, o registro, o checklist, o processo de waiver — existe para fazer com que ADRs e ARs sejam produzidas, revisadas e registradas. Se uma etapa de processo aqui não torna mais provável que uma decisão seja registrada, ela é desperdício e deve ser removida.

**O princípio que governa isto:** um processo que os times contornam é pior do que nenhum processo, porque esconde as decisões em vez de registrá-las. Cada etapa abaixo tem um prazo (timebox), e existe um caminho rápido legítimo (§6) para trabalho urgente. Se um time está decidindo no escuro, a falha é nossa.

---

## 2. Parâmetros do Escritório (mude aqui, em nenhum outro lugar)

Estes são os dois valores que os templates intencionalmente **não** fixam no texto. Mudar um valor aqui muda a expectativa de todo artefato sem qualquer edição de template.

| Parâmetro | ID | Valor atual | Status |
| --- | --- | --- | --- |
| Idioma dos artefatos | `PARAM-LANG` | Português do Brasil (pt-BR) | **Decidido pelo board em 2026-10-04** — substitui o padrão provisório em inglês |
| Profundidade da citação regulatória | `PARAM-REGDEPTH` | Nível geral: nomear o regulador, a família normativa e a obrigação em substância; não afirmar citações em nível de artigo ou inciso | **Provisório** — pendente de confirmação pelo usuário/comitê |

Os templates referenciam `PARAM-LANG` e `PARAM-REGDEPTH` pelo nome. Se qualquer um deles mudar — por exemplo, se a profundidade de citação passar a exigir nível de artigo — basta atualizar esta tabela e o Conjunto de Referência Regulatória (§3). Nenhuma mudança de template, registro ou checklist é necessária.

> **Nota sobre esta conversão:** os campos, cabeçalhos e vocabulário de status de todos os artefatos do Escritório foram convertidos para pt-BR por decisão do board. Termos técnicos já estabelecidos em inglês (`active-active`, `landing zone`, `API gateway`, `OpenShift`, `Kubernetes`, `RTO/RPO`, `zero trust`) permanecem em inglês. Identificadores de ADR/AR e chaves de metadados permanecem neutros em idioma, para que o registro e as ferramentas não dependam de prosa.

---

## 3. Conjunto de Referência Regulatória (interino)

Os artefatos **nunca** contêm suas próprias citações regulatórias. Eles carregam um *ID de obrigação* em uma única coluna, e o texto da obrigação vive aqui, em um único catálogo. Isso é o que torna a profundidade de citação alterável sem retrabalhar os artefatos.

O mapeamento autoritativo, em nível de artigo, de obrigações regulatórias para controles de arquitetura é um entregável separado — [ARC-8](/ARC/issues/ARC-8). Até que ARC-8 seja publicado, os autores usam o conjunto interino abaixo, no nível definido por `PARAM-REGDEPTH`. Quando ARC-8 for publicado, estes IDs serão substituídos pelos IDs do catálogo de ARC-8, e os artefatos atualizarão apenas a coluna de obrigação.

A base regulatória do Escritório é **BACEN/CMN, LGPD e PCI-DSS** — decisão do board de 2026-10-04. O banco está no escopo do PCI-DSS. Qualquer artefato que toque dados de portador de cartão (cardholder data) — seu armazenamento, sua transmissão, ou um sistema conectado ao ambiente que os manipula — deve declarar o efeito da decisão sobre o escopo do PCI-DSS e a qual família de requisitos ela se relaciona.

| ID | Obrigação (declarada em nível geral) |
| --- | --- |
| `OBL-CYB` | Requisitos de cibersegurança do BACEN/CMN para instituições financeiras: política de cibersegurança documentada; classificação e proteção de dados e informações; prevenção, detecção e resposta a incidentes; registro de incidentes e comunicação ao regulador; testes periódicos; e gestão de risco decorrente de terceiros e prestadores de serviço. |
| `OBL-CLOUD` | Requisitos do BACEN/CMN para a contratação de serviços relevantes de processamento e armazenamento de dados e computação em nuvem: comunicação prévia ao regulador antes da contratação; diligência sobre o provedor; cláusulas contratuais cobrindo acesso a dados e informações, direito de auditoria, controle de subcontratação, continuidade do serviço, e rescisão com devolução e portabilidade de dados; e, quando o processamento ocorrer no exterior, mecanismos que viabilizem o acesso regulatório e identifiquem os países de processamento. |
| `OBL-RES` | Expectativas do BACEN/CMN sobre resiliência operacional e continuidade de negócios: planos de continuidade e recuperação, objetivos de recuperação definidos, testes de cenário, e estratégias de saída para serviços críticos. |
| `OBL-LGPD` | LGPD: base legal para o tratamento; limitação de finalidade e minimização de dados; direitos do titular; medidas de segurança, controle de acesso e rastreabilidade; registros das operações de tratamento; condições para transferência internacional; tratamento de incidentes e comunicação à autoridade nacional; e o papel do encarregado de proteção de dados (DPO). |
| `OBL-PCI` | PCI-DSS: proteção de dados de portador de cartão (cardholder data) armazenados, transmitidos ou processados, e de qualquer sistema conectado a esse ambiente (CDE); segmentação de rede para reduzir o escopo; controles de acesso lógico e físico com base em necessidade de conhecer; criptografia de dados de cartão em trânsito e em repouso; gestão de vulnerabilidades e testes de penetração periódicos; logging e monitoramento do CDE; e validação periódica de conformidade (SAQ ou avaliação por QSA, conforme o nível do comerciante/processador). |

> **Ressalva, a ser reproduzida em todo lugar em que estes IDs forem usados:** este conjunto é uma leitura arquitetural em nível geral, não um parecer jurídico. O Escritório mapeia obrigações para *controles*; ele não interpreta a lei. A citação em nível de artigo e a suficiência jurídica são de responsabilidade das funções de compliance e jurídico do banco, via [ARC-8](/ARC/issues/ARC-8).

---

## 4. O estado (o escopo que todo artefato deve endereçar)

Os artefatos são escritos contra o estado real do banco, nunca contra um ambiente greenfield. Esta é a lista canônica de plataformas usada pelas matrizes de aplicabilidade dos templates de ADR e AR. Um artefato que omite silenciosamente uma plataforma está incompleto — por isso a matriz é uma lista fixa de linhas que o autor deve marcar, não prosa livre.

| ID | Plataforma |
| --- | --- |
| `DC-AA` | Data center on-premises, active-active entre sites |
| `OCP` | OpenShift, on-premises |
| `VM-ON` | Máquinas virtuais e bare metal on-premises |
| `AWS-K8S` | Kubernetes gerenciado na AWS |
| `AWS-VM` | Compute AWS (VM/IaaS) |
| `AZR-K8S` | Kubernetes gerenciado no Azure |
| `AZR-VM` | Compute Azure (VM/IaaS) |
| `OCI-K8S` | Kubernetes gerenciado na OCI |
| `OCI-VM` | Compute OCI (VM/IaaS) |
| `DATA-REL` | Bases de dados relacionais (on-premises e gerenciadas em nuvem) |
| `DATA-NREL` | Bases de dados não relacionais (on-premises e gerenciadas em nuvem) |
| `AGW-AWS` | AWS API Gateway |
| `AGW-AZR` | Azure API Management |
| `AGW-AXW` | Axway API Gateway |

Três API gateways e quatro ambientes de hospedagem coexistem por design, não por acidente. Um artefato que assume um único gateway ou uma única nuvem não está descrevendo este banco.

---

## 5. O caminho padrão: como uma pergunta se torna uma ADR

```
  Intake   ──►  Triagem  ──►  Rascunho  ──►  Revisão por pares  ──►  Revisão de governança
 (qualquer um)   (5 du)        (responsável)   (pares nomeados)         (checklist, pass/fail)
                                                                                 │
                                                                                 ▼
                                                   Resultado do comitê ◄──  Validação do Escritório
                                                 (aceito / alterações          (Arquiteto
                                                  solicitadas / não              Principal
                                                      aceito)                    endossa)
```

### Etapa 0 — Intake

**Quem pode levantar:** qualquer pessoa. Um time de entrega, qualquer arquiteto, risco, compliance, operações, ou o próprio comitê de arquitetura.
**Como:** abrir uma task no projeto do Escritório descrevendo a pergunta, o workload afetado, e a data até quando a resposta é necessária. Uma pergunta por task.
**Sem barreira na entrada.** O intake é livre. A filtragem acontece na triagem, de forma aberta, com um resultado registrado.

### Etapa 1 — Triagem (Arquiteto de Governança, em até 5 dias úteis)

O Arquiteto de Governança atribui exatamente um resultado e o registra na task de intake:

| Resultado | Significado | O que acontece |
| --- | --- | --- |
| **Já respondido por artefato existente** | Uma ADR aceita ou AR publicada já decide isso | Responder com o link do artefato. Nenhum artefato novo. Registrado no *log de apontamentos* do registro, para que perguntas repetidas se tornem visíveis. |
| **Precisa de uma ADR** | Uma decisão é necessária | Linha de registro criada como `proposta`; responsável designado; segue para a Etapa 2 |
| **Precisa de uma AR** | A decisão já existe, mas ninguém a trabalhou de ponta a ponta | AR designada a um arquiteto de domínio; segue para a Etapa 2 |
| **Decisão local** | O próprio time decide isso | Responder dizendo isso. O time registra em seu próprio repositório. O Escritório não revisa. |
| **Não é uma questão de arquitetura** | Pertence a outra função | Direcionar, nomear a função, encerrar o intake |

**O teste da ADR.** É necessária uma ADR se **um ou mais** dos itens a seguir for verdadeiro:

1. Revertê-la depois custa mais do que refazer o trabalho que ela viabiliza (gravidade de dados, lock-in contratual, uma migração).
2. Ela vincula mais de um domínio ou mais de uma plataforma da §4.
3. Ela toca uma obrigação regulatória mapeada na §3, dados pessoais, a postura active-active, APIs expostas externamente, ou identidade/segredos/gestão de chaves.
4. Mais de um time de entrega enfrentará a mesma pergunta.

Se nenhum item for verdadeiro, é uma **decisão local**. Dizer isso claramente e seguir adiante — a autoridade do Escritório vem de ser restrita.

### Etapa 2 — Rascunho (responsável nomeado)

Um arquiteto de domínio nomeado é responsável pelo rascunho. A responsabilidade é de uma pessoa, nunca de um time.

| Domínio da pergunta | Responsável padrão pelo rascunho |
| --- | --- |
| Forma do estado como um todo, coerência entre domínios, posicionamento de workload | [Enterprise Architect](/ARC/agents/enterprise-architect) |
| Uma solução específica ou capacidade de negócio, referências de ponta a ponta | [Solution Architect](/ARC/agents/solution-architect) |
| Tecnologia em nível de aplicação, APIs, integração, escolhas de persistência | [Technical Architect](/ARC/agents/technical-architect) |
| Plataformas de nuvem, landing zones, Kubernetes gerenciado, custo e capacidade em nuvem | [Cloud Architect](/ARC/agents/cloud-architect) |
| Topologia de data center, conectividade híbrida, VM vs. container, tiering de DR | [Infrastructure Architect](/ARC/agents/infrastructure-architect) |
| Identidade, segredos e chaves, segmentação, controles de segurança | [Security Architect](/ARC/agents/security-architect) |
| Processo do Escritório, registro, conformidade, mapeamento de obrigação para controle | [Governance Architect](/ARC/agents/governance-architect) |

A linha do registro existe desde o momento em que o rascunho começa, com status `proposta`. **Rascunhos são públicos dentro do Escritório.** Uma decisão em andamento é visível, porque um rascunho invisível é a forma pela qual uma decisão é tomada sem deixar registro.

### Etapa 3 — Revisão por pares

Revisores obrigatórios, conforme o que o artefato toca:

| Gatilho | Revisor obrigatório |
| --- | --- |
| Sempre | Ao menos um arquiteto de domínio **diferente** do autor |
| Sempre | [Enterprise Architect](/ARC/agents/enterprise-architect) — verifica que não há conflito com uma ADR aceita |
| Identidade, segredos, chaves, exposição de rede, dados pessoais, APIs externas | [Security Architect](/ARC/agents/security-architect) |
| Qualquer plataforma da §4 marcada como *Aplica-se* em uma linha de nuvem | [Cloud Architect](/ARC/agents/cloud-architect) |
| Qualquer linha on-premises, tiering de DR, ou a postura active-active | [Infrastructure Architect](/ARC/agents/infrastructure-architect) |
| Qualquer linha `DATA-REL` / `DATA-NREL` marcada como *Aplica-se* | [Technical Architect](/ARC/agents/technical-architect) |

Os revisores registram **concorda**, **concorda com comentário**, ou **discorda**. A discordância nunca é resolvida ignorando-a silenciosamente: ela é levada ao pacote do comitê literalmente (§7). Um comitê que só vê consenso está sendo gerenciado, não assessorado.

**Prazo:** 10 dias úteis. A ausência de resposta até o prazo é registrada como *sem resposta*, não como concordância, e o artefato segue adiante com esse fato visível no pacote.

### Etapa 4 — Revisão de governança (Arquiteto de Governança)

O Arquiteto de Governança executa o [checklist de prontidão](/ARC/issues/ARC-2#document-readiness-checklist) e retorna um veredito escrito de **APROVADO** ou **REPROVADO** com os IDs dos itens que falharam. Prazo: 5 dias úteis.

REPROVADO devolve o artefato ao responsável com os itens específicos. Não é um julgamento sobre o conteúdo técnico — a revisão de governança do Escritório verifica se a decisão está *registrada bem o suficiente para ser revisada e auditada*, nunca se é a decisão certa. Isso é trabalho do comitê.

### Etapa 5 — Validação do Escritório (Arquiteto Principal)

O [Arquiteto Principal](/ARC/agents/principal-architect) endossa o artefato como a recomendação do Escritório e confirma que o pacote de submissão está completo. Prazo: 5 dias úteis.

**Isto é um endosso, não uma aprovação.** O Escritório não tem autoridade para aceitar uma decisão em nome do banco.

### Etapa 6 — Intake e resultado do comitê

O artefato é submetido ao comitê de arquitetura do banco com o pacote da §7. Os resultados do comitê mapeiam para o status do registro da seguinte forma:

| Resultado do comitê | Status no registro | Próximo passo |
| --- | --- | --- |
| **Aceito** | `proposta` → `aceita` | Data de vigência registrada; sinais de conformidade entram em vigor |
| **Alterações solicitadas** | permanece `proposta` | Retorna à Etapa 2 ou 3 com os apontamentos anexados |
| **Não aceito** | permanece `proposta`, resultado registrado | O Escritório revisa (Etapa 2) ou a retira para `descontinuada`, com o motivo |

**Um artefato está finalizado quando está pronto para a revisão do comitê.** Ele nunca é descrito como aprovado pelo Escritório, e nenhuma ADR é vinculante para os times de entrega até que o comitê a aceite.

---

## 6. O caminho rápido (decisões urgentes e provisórias)

A produção não espera por um ciclo de revisão. Sem um caminho rápido legítimo, os times decidem de qualquer forma e o Escritório nunca fica sabendo — por isso o caminho rápido existe e é registrado como tudo o mais.

**Elegibilidade:** uma decisão é necessária antes do próximo ciclo do comitê por causa de um risco de produção ativo, uma exposição de segurança, um prazo regulatório, ou um compromisso de entrega já assumido com o negócio.

**Quem emite:** [Arquiteto Principal](/ARC/agents/principal-architect) **e** [Arquiteto de Governança](/ARC/agents/governance-architect), mais o [Security Architect](/ARC/agents/security-architect) quando a decisão toca identidade, segredos, chaves, exposição de rede ou dados pessoais. Três pessoas nomeadas, no mesmo dia quando necessário.

**O que produz:** uma ADR preenchida no *mínimo provisório* — Contexto, Decisão, a matriz de aplicabilidade da §4, Impacto de conformidade e regulatório, e Consequências. A análise de alternativas pode ser rasa e deve dizer isso explicitamente.

**Restrições, todas rígidas:**

- O status no registro permanece `proposta`, marcado como `Provisório`, com **data de expiração de no máximo 60 dias corridos**.
- **Deve** ser submetida ao próximo ciclo do comitê. Sem exceções, sem um segundo período provisório.
- Ao expirar sem um resultado do comitê, deixa de ser orientação do Escritório. O registro mostra que expirou, e o Arquiteto de Governança a leva ao comitê como uma exceção de processo — uma decisão provisória expirada é uma falha de governança, e é registrada como tal.
- Uma decisão provisória **não pode** se desviar de uma obrigação regulatória mapeada (§3). Esse caminho é um waiver W3 ([processo de exceção](/ARC/issues/ARC-2#document-exception-and-waiver-path)) e pertence ao comitê, nunca ao Escritório.

---

## 7. Pacote de submissão ao comitê

O Escritório submete exatamente estes itens. Um pacote sem algum item não é submetido.

1. O documento de ADR ou AR, na revisão que está sendo submetida.
2. O [checklist de prontidão](/ARC/issues/ARC-2#document-readiness-checklist) preenchido com veredito APROVADO, a data do veredito, e o nome do revisor.
3. **Registro de revisão por pares:** cada revisor obrigatório, com concorda / concorda com comentário / discorda / sem resposta.
4. **Log de discordâncias:** cada discordância, literalmente, com a resposta do responsável. Não resumida.
5. **Impacto sobre decisões aceitas:** quais ADRs aceitas este artefato conflita, altera, ou substitui — e, para cada substituição, a mudança de registro que será feita na aceitação.
6. **Waivers:** qualquer waiver solicitado junto com o artefato, com seu nível e expiração proposta.
7. **Resumo de uma página:** a decisão, as alternativas rejeitadas, as obrigações regulatórias tocadas, e o que está sendo pedido ao comitê para aceitar.
8. **Questões abertas** que o Escritório não conseguiu encerrar, e quem deve encerrá-las — inclui as lacunas conscientes do [registro de decisões §12](/ARC/issues/ARC-2#document-decision-registry), quando aplicáveis ao artefato submetido.

**Cadência do comitê e prazo de submissão:** *a confirmar com o comitê.* A suposição de trabalho do Escritório é um ciclo regular, com o pacote congelado alguns dias antes dele; o caminho rápido (§6) existe justamente porque essa cadência ainda não está fixada.

---

## 8. Quem faz o quê

| Papel | É responsável por | Não é responsável por |
| --- | --- | --- |
| **Comitê de arquitetura (guild committee)** | Aprovação final de todas as ADRs e ARs; waivers W3; o mandato do Escritório | Redigir artefatos |
| **[Arquiteto Principal](/ARC/agents/principal-architect)** | Validação do Escritório; emissão do caminho rápido; waivers W2; arbitragem entre arquitetos de domínio | Aprovação em nome do banco |
| **[Arquiteto de Governança](/ARC/agents/governance-architect)** | Templates, registro, ciclo de vida, triagem de intake, veredito de prontidão, registro de waivers, mapeamento de obrigação para controle | Conteúdo técnico de qualquer artefato |
| **Arquitetos de domínio** (Enterprise, Solution, Technical, Cloud, Infrastructure, Security) | Redação e correção técnica em seu domínio; revisão por pares nos demais | Alterar o processo do Escritório unilateralmente |
| **Times de entrega** | Conformidade; levantar perguntas de intake; solicitar waivers honestamente | Decidir o que precisa de uma ADR |

---

## 9. Conformidade (como o Escritório sabe se algo disto é real)

O Escritório não policia a entrega. Ele torna a não conformidade *visível* e deixa o registro visível fazer o trabalho.

| Sinal | Fonte | Cadência | Quem lê |
| --- | --- | --- | --- |
| Decisões tomadas sem ADR | Volume de intake vs. ADRs redigidas; descoberta retrospectiva | Mensal | Arquiteto de Governança |
| ADRs presas em `proposta` por mais de 60 dias | Registro de decisões | Mensal | Arquiteto de Governança → Arquiteto Principal |
| Decisões provisórias expiradas | Marcador `Provisório` do registro vs. expiração | Mensal | Reportado ao comitê como exceção de processo |
| Waivers vencendo e vencidos | Registro de waivers | Mensal | Arquiteto de Governança → concedente |
| **Três ou mais waivers contra a mesma cláusula de uma ADR** | Registro de waivers | Mensal | **Reabre a ADR** — ver §10 |
| Artefatos reprovando repetidamente no mesmo item do checklist | Veredito de prontidão | Trimestral | Arquiteto de Governança — o checklist ou o template está pouco claro; corrigir |

## 10. Quando o padrão está errado

Se três ou mais waivers forem concedidos contra a mesma cláusula da mesma ADR, o Arquiteto de Governança **deve** reabrir aquela ADR em vez de conceder um quarto. Não conformidade repetida é evidência sobre o padrão, não sobre os times. A ADR reaberta segue o caminho padrão e, ao ser aceita, substitui integralmente sua antecessora.

## 11. Alterando este modelo operacional

Este documento é, ele próprio, governado. Alterações seguem o caminho padrão como uma ADR que substitui a [ADR-0001](/ARC/issues/ARC-2#document-adr-0001-architecture-office-artifact-system). O Arquiteto de Governança pode corrigir erros de digitação, links quebrados e os valores de parâmetro da §2 por instrução; qualquer coisa que altere uma etapa, um papel, um prazo ou um nível de waiver requer aceitação do comitê.

## 12. Questões abertas para o comitê

| # | Questão | Por que importa | Suposição de trabalho do Escritório |
| --- | --- | --- | --- |
| 1 | Confirmar `PARAM-REGDEPTH` = nível geral | Citação em nível de artigo requer validação jurídica que o Escritório não pode dar | Nível geral; [ARC-8](/ARC/issues/ARC-8) é responsável pelo mapeamento autoritativo |
| 2 | Cadência do comitê e data de congelamento do pacote (§7) | Define se o caminho rápido é raro ou rotineiro | Um ciclo regular; caminho rápido para o que não pode esperar |
| 3 | Um quinto status de registro (`rejeitada`) é desejado? | Hoje uma ADR não aceita permanece `proposta` com um resultado registrado | Quatro status, como especificado; resultado rastreado em sua própria coluna |
| 4 | Qual função do banco corrobora waivers W3 junto com o comitê? | W3 cobre exposição regulatória; o Escritório não deve decidir isso isoladamente | Funções de segurança e compliance consultadas, comitê concede |
| 5 | A profundidade de avaliação de escopo PCI-DSS exigida dos autores (§3) é suficiente, ou o comitê quer um parecer formal do QSA/compliance de cartões antes da aceitação de qualquer ADR que toque CDE? | PCI-DSS foi adicionado à base regulatória em 2026-10-04; o Escritório ainda não tem orientação do comitê sobre o nível de rigor exigido | Leitura em nível de controle pelo Escritório, como para os demais regimes; validação formal de escopo fica com compliance de cartões/QSA |

**Nota:** a questão sobre `PARAM-LANG` foi removida desta lista porque o board já decidiu, em 2026-10-04, que o idioma dos artefatos é pt-BR (§2). A linha do reporte do Arquiteto de Governança (ao Arquiteto Principal) também já foi decidida e está refletida na tabela da §8; não é mais uma questão aberta.
