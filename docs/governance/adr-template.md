# Template de ADR

**Tipo de artefato:** Template do Escritório
**Responsável:** Arquiteto de Governança
**Status:** Rascunho — pronto para revisão do comitê de arquitetura. Nada neste documento está aprovado.
**Versão:** 0.1
**Como usar:** copie tudo abaixo da linha divisória para um novo documento de issue nomeado `adr-NNNN-<slug-curto>`. Preencha todos os campos. Não exclua nenhum título. Onde um campo não se aplicar, escreva `N/A` e uma linha dizendo o motivo — um campo em branco é indistinguível de um esquecimento, e um auditor também não consegue fazer essa distinção.

Os termos usados abaixo estão definidos no [modelo operacional](/ARC/issues/ARC-2#document-operating-model): `PARAM-LANG`, `PARAM-REGDEPTH`, os IDs de obrigação (`OBL-*`), e os IDs de plataforma do estado (`DC-AA` … `AGW-AXW`).

Exemplo trabalhado: [ADR-0001](/ARC/issues/ARC-2#document-adr-0001-architecture-office-artifact-system).

---

# ADR-NNNN — <Decisão declarada como uma frase afirmativa>

> Regra de título: declare a decisão, não o tema. "Usar o Axway para APIs expostas externamente" é um título. "Estratégia de API gateway" é um tema — isso é uma AR ou uma pergunta de intake, não um título de ADR.

## 0. Cabeçalho

| Campo | Valor | Notas para o autor |
| --- | --- | --- |
| **ID da ADR** | `ADR-NNNN` | Atribuído pelo Arquiteto de Governança na triagem. Nunca reutilizado, nunca renumerado. |
| **Status** | `proposta` \| `aceita` \| `substituída` \| `descontinuada` | Começa em `proposta` no dia em que o rascunho é iniciado. Somente o comitê a move para `aceita`. Ver o [registro](/ARC/issues/ARC-2#document-decision-registry). |
| **Provisória** | `Não` \| `Sim — expira em AAAA-MM-DD` | `Sim` apenas no caminho rápido. Máximo de 60 dias, sem renovação. |
| **Responsável** | Papel + nome | Uma pessoa. Não um time. |
| **Domínio** | Enterprise \| Solution \| Technical \| Cloud \| Infrastructure \| Security \| Governance | Define o conjunto de revisores obrigatórios. |
| **Data da proposta** | AAAA-MM-DD | |
| **Data da última mudança de status** | AAAA-MM-DD | |
| **Data de vigência** | AAAA-MM-DD \| `na aceitação` | Quando os times devem começar a cumprir. Não pode ficar em branco. |
| **Substitui** | `ADR-NNNN` \| `Nenhuma` | |
| **Substituída por** | `ADR-NNNN` \| `Nenhuma` | |
| **ADRs relacionadas** | IDs, ou `Nenhuma` | Decisões das quais esta depende ou que esta restringe. |
| **Implementada por ARs** | IDs, ou `Nenhuma ainda` | As referências que mostram esta decisão construída de ponta a ponta. |
| **Idioma** | conforme `PARAM-LANG` | |
| **Data de submissão ao comitê** | AAAA-MM-DD \| `não submetida` | |
| **Resultado do comitê** | `pendente` \| `aceito` \| `alterações solicitadas` \| `não aceito` | |

## 1. Contexto

O que é verdade hoje, o que força uma decisão agora, e o que quebra se ninguém decidir.

- **A situação:** o estado atual no ambiente do banco. Nomeie os sistemas, plataformas e times realmente afetados.
- **O gatilho:** por que isso está sendo decidido agora — um projeto, um incidente, uma obrigação regulatória, um contrato vencendo, um limite de capacidade.
- **Custo de não decidir:** o que cada time faz na ausência desta ADR, e quanto isso custa. Se a resposta for "quase nada", isto pode ser uma decisão local — verifique o teste da ADR no modelo operacional §5.
- **Restrições que não podemos mudar:** contratos existentes, programas em andamento, compromissos regulatórios, competências, prazos.

Escreva isso de forma que um arquiteto que entre no banco em dois anos entenda por que a decisão tomou a forma que tomou. O contexto é escrito no passado e no presente; não coloque a decisão aqui.

## 2. Fatores de decisão

Numere-os `D1`, `D2`, … Eles são referenciados por número nas §3 e §4, então a numeração não é decoração.

| ID | Fator | Tipo | ID de obrigação | Peso |
| --- | --- | --- | --- | --- |
| D1 | | Regulatório \| Resiliência \| Segurança \| Custo \| Operabilidade \| Velocidade de entrega \| Competências \| Risco de fornecedor \| Dados | `OBL-*` ou `N/A` | Obrigatório \| Forte \| Desejável |
| D2 | | | | |

Regras:

- Pelo menos um fator, ou a decisão não tem base declarada.
- Todo fator **Regulatório** carrega um ID de obrigação do modelo operacional §3. Se nenhum ID existente se encaixar, diga isso — é uma lacuna no catálogo e o Arquiteto de Governança precisa saber.
- Fatores `Obrigatório` são pass/fail: uma opção que falha em um deles não é uma opção viável e é registrada como rejeitada por esse motivo.

## 3. Alternativas consideradas

Pelo menos **duas** opções reais. Uma delas deve normalmente ser o status quo, nomeado honestamente ("continuar operando os dois gateways como estão"), porque "não fazer nada" sempre tem um custo e o comitê tem o direito de vê-lo.

Uma lista de opções com apenas uma opção não é um registro de decisão — é uma proposta, e falha na prontidão.

### Opção A — <nome>

- **O que é:** um parágrafo. Concreto o suficiente para ser custeado.
- **Como pontua:** D1 — …, D2 — … Endereçar cada fator pelo número. "Neutro" é uma pontuação aceitável; silêncio não é.
- **Custo e esforço:** construção, operação, migração. Ordem de grandeza é aceitável; declare a base.
- **Riscos:** incluindo no que estaríamos apostando.
- **Por que não foi escolhida / por que foi escolhida:**

### Opção B — <nome>

*(mesma estrutura)*

### Opções consideradas e descartadas precocemente

| Opção | Descartada porque |
| --- | --- |
| | |

## 4. Decisão

> **Vamos …**

Uma frase afirmativa no presente, seguida pelas especificidades:

- **Conteúdo normativo** — use `MUST` / `MUST NOT` / `SHOULD` / `SHOULD NOT` / `MAY` deliberadamente, e somente estes. Cada `MUST` é algo pelo qual um time pode ser responsabilizado e contra o qual um waiver pode ser solicitado; cada `SHOULD` é algo de que um time pode se desviar com um motivo registrado. Se tudo em uma decisão é `MUST`, provavelmente não é implementável; se nada é, não é uma decisão.

  | # | Cláusula | Palavra-chave |
  | --- | --- | --- |
  | C1 | | MUST |
  | C2 | | SHOULD |

  Os IDs de cláusula importam: waivers são concedidos contra uma cláusula, nunca contra uma ADR inteira.

- **Por que esta opção:** o trade-off aceito, amarrado aos fatores `Obrigatório`.
- **Alternativas rejeitadas:** uma linha cada, apontando para a §3.

### 4.1 Aplicabilidade no estado

Marque **todas** as linhas. Esta é uma lista fixa, não um prompt — uma linha não marcada falha na prontidão. Três API gateways e quatro ambientes de hospedagem coexistem neste banco; uma ADR que endereça implicitamente apenas um deles causa exatamente a ambiguidade que esta tabela existe para prevenir.

| Plataforma | Aplica-se? | Restrição, diferença ou exclusão |
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

`Não` significa que a decisão deliberadamente não se aplica ali e os times naquela plataforma não são afetados. `N/A` significa que a plataforma não pode encontrar esta questão. A diferença importa para quem lê o registro para saber se uma decisão o vincula.

### 4.2 Escopo de workloads

- **Aplica-se a:** novos designs a partir da data de vigência \| todos os workloads \| uma classe de criticidade ou tier nomeado \| …
- **Workloads existentes:** mantidos sob uma ADR predecessora nomeada \| devem migrar até AAAA-MM-DD \| sem necessidade de migração.
- **Explicitamente fora de escopo:**

## 5. Consequências

Ambas as colunas são obrigatórias. Uma ADR com apenas consequências positivas não foi revisada; foi defendida.

### Positivas

- …

### Negativas, e o que fica mais difícil

- … Inclua sobrecarga operacional, novas competências necessárias, novos pontos únicos de dependência, aumentos de custo, e times que perdem uma opção que tinham.

### O que esta decisão impede no futuro

O que se torna caro ou impossível depois, e aproximadamente quanto custaria revertê-la. Este é o campo que faz uma ADR valer a pena anos depois.

### Impacto de migração no estado existente

| O que existe hoje | O que precisa mudar | Responsável | Até quando |
| --- | --- | --- | --- |
| | | | |

Escreva `Nenhum` apenas se nada no estado atual for afetado — raro em um banco com este estado.

## 6. Impacto de conformidade e regulatório

Profundidade conforme `PARAM-REGDEPTH`. Os IDs de obrigação vêm do modelo operacional §3 — não escreva citações neste documento, carregue o ID.

**As três linhas abaixo são obrigatórias para toda ADR — BACEN/CMN, LGPD e PCI-DSS. Nenhuma pode ser omitida silenciosamente: se um regime não é tocado, a linha permanece e o autor escreve `N/A` com o motivo.**

| Regime | ID(s) de obrigação aplicável(is) | O que a obrigação exige, em substância | Como esta decisão a endereça | Risco residual | Controle / evidência de que se mantém |
| --- | --- | --- | --- | --- | --- |
| BACEN/CMN | `OBL-CYB` / `OBL-CLOUD` / `OBL-RES` (ou `N/A`) | | | | |
| LGPD | `OBL-LGPD` (ou `N/A`) | | | | |
| PCI-DSS | `OBL-PCI` (ou `N/A`) | | | | |

Se a linha de PCI-DSS não for `N/A`, responda também:

| Pergunta de escopo PCI-DSS | Resposta |
| --- | --- |
| Esta decisão armazena, transmite ou processa dados de portador de cartão (cardholder data)? | Sim \| Não |
| Esta decisão introduz ou altera um sistema conectado ao ambiente de dados de cartão (CDE)? | Sim \| Não |
| Efeito sobre o escopo do PCI-DSS (amplia / reduz / não altera) | |
| Família de requisitos PCI-DSS afetada (ex.: segmentação de rede, controle de acesso, criptografia, logging/monitoramento, gestão de vulnerabilidades) | |

Em seguida, responda a cada pergunta geral, explicitamente:

| Pergunta | Resposta |
| --- | --- |
| Esta decisão toca dados pessoais? | Sim \| Não. Se sim: quais categorias, em nível geral. |
| Ela envolve processamento ou armazenamento fora do Brasil? | Sim \| Não. Se sim: quais países, e quais plataformas da §4.1. |
| Ela muda um arranjo relevante de processamento de dados, armazenamento ou serviço em nuvem? | Sim \| Não. Se sim, pode acionar uma obrigação de comunicação regulatória sob `OBL-CLOUD` — sinalize para compliance. O Escritório sinaliza; não decide. |
| Ela muda a postura de resiliência ou recuperação de um serviço crítico? | Sim \| Não. Se sim: o tier de RTO/RPO antes e depois. |
| Ela muda a detecção de incidentes, logging ou rastreabilidade? | Sim \| Não. |
| Ela cria ou muda uma dependência de terceiro / fornecedor? | Sim \| Não. |

> Reproduza esta ressalva: o Escritório mapeia obrigações para controles de arquitetura em nível geral. Ele não emite parecer jurídico, e a suficiência jurídica é confirmada pelas funções de compliance e jurídico do banco.

## 7. Conformidade e verificação

Como um revisor, um auditor ou um pipeline estabelece que um time realmente segue isto — por cláusula. Uma ADR que ninguém consegue verificar é apenas um conselho.

| Cláusula | Como a conformidade é verificada | Onde a evidência vive | Automatizável? |
| --- | --- | --- | --- |
| C1 | | | Sim \| Não \| Parcialmente |

Se nenhuma cláusula for verificável, diga isso aqui e explique por quê. Essa é uma resposta honesta e útil.

## 8. Exceções

- **Waivers possíveis contra esta ADR:** Sim \| Não.
- **Nível por cláusula:** `W1` / `W2` / `W3` — ver o [processo de exceção e waiver](/ARC/issues/ARC-2#document-exception-and-waiver-path). Qualquer cláusula que implemente uma obrigação regulatória mapeada é `W3`, e somente o comitê pode concedê-la.
- **Cláusulas que não podem receber waiver:** liste-as, com o motivo.

## 9. Questões abertas

| # | Questão | Quem deve responder | Necessário até |
| --- | --- | --- | --- |

Uma ADR pode ser submetida com questões abertas. Não pode ser submetida com questões escondidas.

## 10. Revisão por pares e discordâncias

| Revisor | Papel | Veredito | Comentário / discordância |
| --- | --- | --- | --- |
| | | concorda \| concorda com comentário \| discorda \| sem resposta | |

A discordância é registrada literalmente e segue ao comitê sem edição.

## 11. Histórico de status

| Data | De → Para | Quem | Por quê |
| --- | --- | --- | --- |
| | `—` → `proposta` | | Rascunho iniciado |

Toda mudança de status gera uma linha, incluindo substituição e descontinuação. O registro é o índice; isto é a trilha de auditoria.
