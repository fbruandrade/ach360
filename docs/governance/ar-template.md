# Template de AR (Architecture Reference)

**Tipo de artefato:** Template do Escritório
**Responsável:** Arquiteto de Governança
**Status:** Rascunho — pronto para revisão do comitê de arquitetura. Nada neste documento está aprovado.
**Versão:** 0.1
**Como usar:** copie tudo abaixo da linha divisória para um novo documento de issue nomeado `ar-NNN-<slug-curto>`. Preencha todos os campos; escreva `N/A` mais uma linha de motivo em vez de deixar algo em branco.

Os termos usados abaixo estão definidos no [modelo operacional](/ARC/issues/ARC-2#document-operating-model): `PARAM-LANG`, `PARAM-REGDEPTH`, IDs de obrigação (`OBL-*`), IDs de plataforma do estado (`DC-AA` … `AGW-AXW`).

**Uma AR não é uma ADR.** Uma ADR decide uma coisa. Uma AR mostra a um time de entrega como construir uma coisa inteira, reunindo decisões já aceitas. Se uma AR precisa tomar uma nova decisão para ficar completa, pare: essa decisão precisa de sua própria ADR primeiro, ou a AR está legislando silenciosamente.

---

# AR-NNN — <Arquétipo de workload que esta referência cobre>

> Regra de título: nomeie a coisa que um time está construindo. "API REST exposta externamente no Axway" é um título. "Boas práticas de API" não é.

## 0. Cabeçalho

| Campo | Valor | Notas para o autor |
| --- | --- | --- |
| **ID da AR** | `AR-NNN` | Atribuído pelo Arquiteto de Governança na triagem. |
| **Status** | `proposta` \| `aceita` \| `substituída` \| `descontinuada` | Mesmo ciclo de vida e registro das ADRs. |
| **Versão** | 0.1 | |
| **Responsável** | Papel + nome | Uma pessoa. |
| **Realiza as ADRs** | `ADR-NNNN`, … | **Não pode ficar vazio.** Uma AR que não realiza nenhuma decisão aceita é a preferência de um único arquiteto. |
| **Decisões ainda `proposta`** | IDs, ou `Nenhuma` | Se qualquer ADR listada ainda não estiver `aceita`, esta AR também não pode passar de `proposta`. |
| **Substitui / Substituída por** | IDs ou `Nenhuma` | |
| **Consumidor pretendido** | Qual tipo de time de entrega e qual classe de workload | |
| **Maturidade** | `Apenas referência` \| `Construída uma vez em produção` \| `Construída mais de uma vez` | Diga honestamente se alguém já operou isto. |
| **Idioma** | conforme `PARAM-LANG` | |
| **Data de submissão ao comitê / resultado** | | |

## 1. Quando usar esta referência — e quando não usar

A seção mais útil de todas. Um time precisa conseguir dizer em dois minutos se isso se aplica a ele.

**Use esta referência quando todos estes forem verdadeiros:**

- …
- …

**Não a use quando qualquer um destes for verdadeiro** (e indique a alternativa):

| Se … | Use em vez disso |
| --- | --- |
| | |

**Anti-padrões que esta referência existe para prevenir:**

- …

## 2. O arquétipo de workload

Para que esta referência serve *de referência*: a forma funcional, o perfil de transação, a faixa de volume esperada, a sensibilidade dos dados, o tier de criticidade, e os consumidores (interno, parceiro, público, mobile). Dê números ou faixas — uma referência dimensionada para 50 TPS e uma para 5.000 TPS não são a mesma arquitetura, e um time precisa saber qual delas recebeu.

## 3. Visão geral da arquitetura

- **Visão lógica:** os componentes e suas responsabilidades. Uma frase de responsabilidade por componente; se um componente precisa de um parágrafo, são dois componentes.

  | Componente | Responsabilidade | Plataforma (da §5) | Responsável |
  | --- | --- | --- | --- |

- **Diagrama:** incorpore ou vincule. O Escritório não exige nenhuma ferramenta de diagramação específica. Exige, sim, que o diagrama e esta tabela não se contradigam.
- **Fronteiras de confiança (trust boundaries):** onde estão, e o que as atravessa.

## 4. Fluxos de ponta a ponta

No mínimo os três primeiros. Passos numerados, não prosa.

1. **Caminho feliz (happy path)** — a transação de negócio principal, do consumidor até o sistema de registro e de volta.
2. **Caminho de falha primário** — a falha mais provável, e o que o consumidor experimenta.
3. **Failover de site** — comportamento quando um lado do `DC-AA` é perdido, ou quando uma região de nuvem degrada. Declare se o fluxo é active-active, active-passive, ou fixo a um site (site-pinned), e o que acontece com as transações em andamento.
4. *(quando pertinente)* autenticação, batch, reconciliação, replay, idempotência.

Para cada fluxo, declare o que é síncrono, o que é assíncrono, onde o estado vive, e onde está o limite da transação.

## 5. Realização na plataforma

A parte copiável: o que um time de fato implanta em cada plataforma. Marque todas as linhas.

| Plataforma | Nesta referência? | O que você implanta / como difere aqui |
| --- | --- | --- |
| `DC-AA` — DC on-prem, active-active | Sim \| Não \| N/A | |
| `OCP` — OpenShift on-prem | | |
| `VM-ON` — VMs / bare metal on-prem | | |
| `AWS-K8S` — Kubernetes gerenciado na AWS | | |
| `AWS-VM` — compute AWS | | |
| `AZR-K8S` — Kubernetes gerenciado no Azure | | |
| `AZR-VM` — compute Azure | | |
| `OCI-K8S` — Kubernetes gerenciado na OCI | | |
| `OCI-VM` — compute OCI | | |
| `DATA-REL` — bases de dados relacionais | | |
| `DATA-NREL` — bases de dados não relacionais | | |
| `AGW-AWS` — AWS API Gateway | | |
| `AGW-AZR` — Azure API Management | | |
| `AGW-AXW` — Axway API Gateway | | |

**Divisão entre container e VM:** quais componentes correm como containers, quais como VMs, e por quê. Ambos coexistem neste estado; uma referência que assume que tudo é container não é usável pela metade do banco.

**Se a referência é multiplataforma:** diga qual combinação de plataforma é a *padrão* e quais são variantes. Um time que precisa escolher entre quatro realizações igualmente endossadas recebeu um cardápio, não uma referência.

## 6. Preocupações transversais

Cada subseção: o que a referência prescreve, e de qual ADR isso vem. `Não endereçado` é uma resposta permitida e é melhor do que uma inventada — mas então deve aparecer na §10.

| Preocupação | O que esta referência prescreve | De qual ADR |
| --- | --- | --- |
| Identidade e acesso (workload e humano) | | |
| Segredos e gestão de chaves | | |
| Segmentação e exposição de rede | | |
| Exposição de API: qual gateway, qual padrão, quais políticas | | |
| Dados: escolha de store, titularidade do schema, retenção, classificação | | |
| Proteção de dados em trânsito e em repouso | | |
| Observabilidade: logs, métricas, traces, o que deve ser emitido | | |
| Auditoria e rastreabilidade de transações de negócio | | |
| Configuração e deployment | | |
| Promoção entre ambientes | | |

## 7. Resiliência e recuperação

| Atributo | Valor |
| --- | --- |
| Criticidade / tier de DR | |
| RTO | |
| RPO | |
| Active-active, active-passive, ou site único | |
| Comportamento na perda de um site do DC | |
| Comportamento na perda de uma região de nuvem | |
| Modelo de replicação de dados e garantia de consistência | |
| Janela de perda de dados conhecida, se houver | |

**Modos de falha e o que a referência faz em cada caso:**

| Modo de falha | Detecção | Resposta automática | Resposta manual |
| --- | --- | --- | --- |

**Cenários testados:** quais dos itens acima já foram de fato exercitados, e quando. Não testado é uma resposta honesta e útil; "resiliente" sem um registro de teste não é.

## 8. Controles de segurança e regulatórios

Mesmo formato da seção de conformidade da ADR, para que um revisor leia um único padrão. Profundidade conforme `PARAM-REGDEPTH`; IDs de obrigação do modelo operacional §3.

**As três linhas abaixo são obrigatórias para toda AR — BACEN/CMN, LGPD e PCI-DSS. Nenhuma pode ser omitida silenciosamente: se um regime não é tocado, a linha permanece e o autor escreve `N/A` com o motivo.**

| Regime | ID(s) de obrigação aplicável(is) | O que exige, em substância | Controle nesta referência | Onde é implementado | Evidência que um time pode produzir |
| --- | --- | --- | --- | --- | --- |
| BACEN/CMN | `OBL-CYB` / `OBL-CLOUD` / `OBL-RES` (ou `N/A`) | | | | |
| LGPD | `OBL-LGPD` (ou `N/A`) | | | | |
| PCI-DSS | `OBL-PCI` (ou `N/A`) | | | | |

Se a linha de PCI-DSS não for `N/A`, responda também:

| Pergunta de escopo PCI-DSS | Resposta |
| --- | --- |
| Esta referência armazena, transmite ou processa dados de portador de cartão (cardholder data)? | Sim \| Não |
| Algum componente da §3/§5 fica dentro do ambiente de dados de cartão (CDE) ou conectado a ele? | Sim \| Não — quais componentes |
| Família de requisitos PCI-DSS afetada | |

| Pergunta | Resposta |
| --- | --- |
| Dados pessoais processados? | Sim \| Não — quais categorias, em nível geral |
| Processamento ou armazenamento fora do Brasil? | Sim \| Não — quais países, quais plataformas |
| Depende de um arranjo relevante de processamento de dados / armazenamento / serviço em nuvem? | Sim \| Não — sinalizar para compliance sob `OBL-CLOUD` |
| Exposta externamente? | Sim \| Não — por qual gateway |
| Logging suficiente para reconstruir uma transação de negócio? | Sim \| Não |

> Reproduza esta ressalva: o Escritório mapeia obrigações para controles de arquitetura em nível geral e não emite parecer jurídico.

## 9. Checklist de conformidade para o time que copia a referência

Itens binários que o time de entrega afirma sobre sua própria construção. Cada um deve ser respondível `Sim`/`Não` por inspeção, sem julgamento — a mesma disciplina do [checklist de prontidão](/ARC/issues/ARC-2#document-readiness-checklist).

| # | O time tem … | Evidência | Sim/Não |
| --- | --- | --- | --- |
| 1 | | | |

Um time que responde `Não` a qualquer item ou corrige, ou solicita um waiver contra a cláusula da ADR subjacente — nunca contra a AR. ARs não recebem waiver; as decisões que elas realizam, sim.

## 10. O que o time ainda precisa decidir localmente

As lacunas deliberadas. Ser explícito aqui é o que evita que um time ou leia demais a referência, ou invente silenciosamente. Item de prontidão **R19**: esta seção está preenchida, ou declara `Nenhuma — esta referência não deixa decisões locais`.

### 10.1 Decisões deixadas ao time

| Decisão deixada ao time | Orientação | Precisa ser registrada localmente? |
| --- | --- | --- |

### 10.x Questões abertas

Questões que esta referência não pode responder sozinha — porque exigem interpretação do banco (jurídico, compliance, risco), uma decisão de negócio fora do alcance do Escritório, ou um dado que só o time dono tem — em vez de uma decisão de arquitetura. Cada questão nomeia um respondente, nunca fica sem dono (item de prontidão **G14**); declare `Nenhuma` se não houver nenhuma. Formato herdado de `AR-001`/`AR-002` §10.4.

| # | Questão | Por que o Escritório não pode responder sozinho | Quem |
| --- | --- | --- | --- |

## 11. Notas de custo e capacidade

Direcionadores de custo, a base de dimensionamento, o que escala com volume, e os penhascos conhecidos (tiers de licença, precificação de requisições do gateway, transferência de dados entre sites e entre regiões, mínimos de serviço gerenciado). Ordem de grandeza com uma base declarada vale mais do que precisão sem nenhuma.

## 12. Orientação sobre desvios

- Desviar de uma cláusula `SHOULD` de uma ADR realizada: registre localmente, sem waiver.
- Desviar de uma cláusula `MUST`: waiver necessário — ver o [processo de exceção e waiver](/ARC/issues/ARC-2#document-exception-and-waiver-path).
- Encontrou um caso que esta referência não cobre? Levante uma pergunta de intake (modelo operacional §5). Isso é uma lacuna na referência, e o Escritório quer saber.

## 13. Revisão por pares e discordâncias

| Revisor | Papel | Veredito | Comentário / discordância |
| --- | --- | --- | --- |

## 14. Changelog e histórico de status

| Data | Versão | De → Para | Quem | O que mudou |
| --- | --- | --- | --- | --- |

Uma mudança que altera uma prescrição na §5, §6, §7 ou §8 é uma nova versão e passa pelo caminho padrão de novo. Mudanças editoriais não.
