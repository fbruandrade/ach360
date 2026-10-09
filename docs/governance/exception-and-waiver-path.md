# Processo de Exceção e Waiver

**Tipo de artefato:** Padrão de processo do Escritório
**Responsável:** Arquiteto de Governança
**Status:** Rascunho — pronto para revisão do comitê de arquitetura. Nada neste documento está aprovado.
**Versão:** 0.1
**Relacionados:** [Modelo operacional](/ARC/issues/ARC-2#document-operating-model) · [Registro de decisões](/ARC/issues/ARC-2#document-decision-registry)

---

## 1. O que é um waiver

Um **waiver** é uma permissão registrada, com prazo definido, concedida individualmente, para que um workload nomeado deixe de cumprir uma cláusula nomeada de uma ADR aceita, com um plano de remediação e uma data de expiração.

Cada palavra dessa frase tem peso:

| Palavra | Consequência |
| --- | --- |
| **registrado** | Existe no registro de waivers (§7) ou não existe. |
| **com prazo definido** | Tem uma expiração. Não existe waiver permanente, e o silêncio não renova um. |
| **concedido individualmente** | Um concedente nomeado decidiu. Não um comitê de ninguém. |
| **um workload nomeado** | Um waiver nunca se aplica a um time, um departamento ou uma plataforma. |
| **uma cláusula nomeada** | Waivers são concedidos contra IDs de cláusula (`C1`, `C2`), nunca contra uma ADR inteira. |
| **plano de remediação** | Um waiver é um cronograma para se tornar conforme, não uma decisão de permanecer não conforme. |
| **expiração** | Na expiração, o workload fica não conforme, salvo remediação ou renovação. Nada acontece automaticamente a favor do time. |

**Por que o Escritório se importa com isso:** um waiver que nunca é revisitado é um padrão que mudou silenciosamente. O registro e a revisão mensal da §8 são o ponto central — a aprovação é a parte fácil.

## 2. O que não é um waiver

Direcionar esses casos para fora mantém o processo de waiver pequeno o suficiente para realmente funcionar.

| Situação | Não é um waiver — faça isto em vez disso |
| --- | --- |
| Nenhuma ADR aceita cobre a questão | Levante uma pergunta de intake (modelo operacional §5). Não há nada para receber waiver. |
| A ADR ainda está `proposta` | Nada ainda o vincula. Comente no rascunho — seu caso é insumo de design. |
| Desviar de uma cláusula `SHOULD` ou `MAY` | Registre o motivo em seu próprio repositório. Sem waiver, sem envolvimento do Escritório. |
| Você discorda do padrão | Conteste-o: pergunta de intake, depois uma ADR substituta. Um waiver não é um recurso de apelação. |
| Você quer que a regra mude para todos | O mesmo — um waiver para um workload não é um veículo para mudar um padrão. |
| Você quer uma isenção permanente | Não existe. Se a conformidade está permanentemente errada para uma classe de workload, a ADR está errada: ver §9. |
| Você não pode cumprir uma **obrigação regulatória** | Não é um waiver de arquitetura em nenhum nível. Pare e escale para as funções de compliance e risco do banco, pelo processo próprio delas. O Escritório não pode conceder waiver de uma obrigação legal e não vai fingir que pode. |

## 3. Níveis de waiver

O nível é determinado pelo que o desvio toca, **não** pela urgência que o time alega. Onde mais de um nível se aplica, o mais alto prevalece.

### W1 — Baixo

**Todas** as condições a seguir são verdadeiras: um único workload; nenhum dado pessoal; nenhuma obrigação regulatória mapeada (`OBL-*`); nenhuma mudança na postura active-active ou de DR; nenhuma exposição externa; nenhuma plataforma compartilhada afetada; nenhum envolvimento de identidade, segredos ou gestão de chaves.

| | |
| --- | --- |
| **Concedente** | [Arquiteto de Governança](/ARC/agents/governance-architect) |
| **Consultado** | O arquiteto de domínio responsável pela ADR |
| **Prazo inicial máximo** | 90 dias |
| **Renovações** | Uma, por até 90 dias adicionais, com evidência de progresso na remediação |
| **Depois disso** | Escala para W2. Não há terceiro prazo em W1. |
| **Registrado em** | Registro de waivers |

### W2 — Médio

Qualquer um dos seguintes: desvio de uma cláusula `MUST` ou `MUST NOT`; mais de um workload afetado; uma plataforma ou serviço compartilhado afetado; impacto material de custo ou capacidade; uma nova dependência de terceiro ou fornecedor; uma mudança em uma interface consumida por outros times.

| | |
| --- | --- |
| **Concedente** | [Arquiteto Principal](/ARC/agents/principal-architect), em conjunto com o arquiteto de domínio responsável pela ADR |
| **Recomendação de** | Arquiteto de Governança, por escrito, incluindo o histórico do registro para esta cláusula |
| **Prazo inicial máximo** | 180 dias |
| **Renovações** | Uma, por até 90 dias adicionais |
| **Depois disso** | Escala para W3 — o comitê toma conhecimento |
| **Registrado em** | Registro de waivers, e reportado ao comitê no resumo mensal de exceções |

### W3 — Alto

Qualquer um dos seguintes: uma cláusula que implementa uma obrigação regulatória mapeada (`OBL-CYB`, `OBL-CLOUD`, `OBL-RES`, `OBL-LGPD`, `OBL-PCI`); dados pessoais em escala ou dados pessoais sensíveis; processamento ou armazenamento fora do Brasil; a postura active-active ou os objetivos de recuperação de um serviço crítico; APIs expostas externamente; identidade, segredos ou gestão de chaves.

| | |
| --- | --- |
| **Concedente** | **Somente o comitê de arquitetura.** Nenhuma parte do Escritório pode conceder um W3. |
| **Consultado** | As funções de segurança, risco e compliance do banco. *Qual função corrobora formalmente é uma questão aberta para o comitê.* |
| **Recomendação de** | Arquiteto de Governança e o arquiteto de domínio responsável, com uma declaração de risco por escrito |
| **Prazo inicial máximo** | 12 meses |
| **Renovações** | Somente o comitê, com rejustificativa completa. Sem renovação administrativa. |
| **Registrado em** | Registro de waivers, reportado ao comitê a cada ciclo até ser encerrado |

> **PCI-DSS:** qualquer cláusula de ADR que implemente `OBL-PCI` — ou que, de outra forma, afete o escopo do ambiente de dados de cartão (CDE) — é automaticamente `W3`. O Escritório não pode, isoladamente, autorizar um desvio que amplie o escopo PCI-DSS ou deixe dados de cartão sem o controle exigido.

### Resumo dos níveis

| | W1 | W2 | W3 |
| --- | --- | --- | --- |
| Concedente | Arquiteto de Governança | Arquiteto Principal + arquiteto de domínio | Comitê de arquitetura |
| Prazo inicial máximo | 90 dias | 180 dias | 12 meses |
| Renovação | 1 × 90 dias | 1 × 90 dias | Somente o comitê |
| No esgotamento | → W2 | → W3 | Rejustificar ao comitê |

### Regras que se aplicam em todos os níveis

1. **Ninguém concede o próprio waiver.** Se o solicitante, o arquiteto do workload e o concedente seriam a mesma pessoa, o waiver escala um nível.
2. **Nenhuma cláusula recebe waiver a menos que sua ADR diga que pode.** A §8 da ADR nomeia o nível por cláusula e lista as cláusulas não sujeitas a waiver.
3. **Um waiver não pode ser concedido retroativamente para cobrir um achado de auditoria passado.** Pode ser concedido a partir de hoje em diante para um workload atualmente não conforme, e o registro anota que a não conformidade antecedeu o waiver. Retroagir um waiver é falsificar o registro.
4. **Um waiver não se transfere.** Outro time fazendo a mesma coisa abre sua própria solicitação. Isso é deliberado: é como a §9 detecta que o problema é o padrão.

## 4. Como um time solicita um waiver

### Etapa 1 — Submissão

Abra uma task no projeto do Escritório, intitulada `Solicitação de waiver: <workload> — <ADR-NNNN cláusula Cn>`, contendo todos os campos abaixo. Uma solicitação incompleta é devolvida, não avaliada.

| Campo | |
| --- | --- |
| Time solicitante e solicitante nomeado | |
| Nome do workload / sistema e seu tier de criticidade | |
| ID da ADR e **ID da cláusula** sendo objeto do waiver | |
| Texto exato da cláusula | |
| O que o workload fará em vez disso | Seja específico. "Uma abordagem diferente" não é uma resposta. |
| Por que a conformidade não é possível agora | Motivo técnico, contratual, de prazo ou de capacidade |
| O que foi tentado | Opções consideradas para cumprir, e por que cada uma falhou |
| Risco criado, e quem o assume | Incluindo o impacto no negócio se o risco se materializar |
| Controles compensatórios em vigor | O que reduz o risco enquanto não conforme, ou `nenhum` |
| Data de expiração solicitada | |
| **Plano de remediação** | Marcos com datas e um responsável nomeado. Obrigatório em todos os níveis. |
| Plataformas afetadas | Da lista do modelo operacional §4 |
| Envolve dados pessoais? | Sim/Não, categorias em nível geral |
| Processamento fora do Brasil? | Sim/Não, quais países |
| Esta solicitação toca o ambiente de dados de cartão (CDE) ou `OBL-PCI`? | Sim/Não — se sim, o nível é automaticamente W3 |
| IDs de obrigação tocados | `OBL-*` ou `nenhum` |

### Etapa 2 — Completude e classificação (Arquiteto de Governança, em até 5 dias úteis)

Verifica se os campos estão presentes, atribui o **nível**, e declara a base do nível em uma frase. A classificação não é negociável pelo solicitante, mas é recorrível ao Arquiteto Principal — e o recurso é registrado.

### Etapa 3 — Recomendação (Arquiteto de Governança)

Uma recomendação por escrito ao concedente: conceder / conceder com prazo menor / conceder com controles compensatórios adicionais / recusar. **Deve** incluir quantos waivers já existem contra esta cláusula desta ADR (§9).

### Etapa 4 — Decisão (concedente conforme o nível)

Registrada como: **concedido** (com prazo e condições), **concedido com condições**, ou **recusado** (com o motivo e o que o time deve fazer em vez disso). Prazo: 10 dias úteis para W1 e W2; próximo ciclo do comitê para W3.

Uma recusa é um resultado legítimo e deve dizer o que o time deve fazer em vez disso. Uma recusa sem alternativa é como os times aprendem a parar de perguntar.

### Etapa 5 — Registro

O Arquiteto de Governança escreve a linha do registro (§7) no mesmo dia. **O waiver entra em vigor a partir do registro, não da decisão.** Se não está no registro, o time não está coberto.

### Etapa 6 — Revisão intermediária

Em 50% do prazo, o Arquiteto de Governança verifica a remediação contra os marcos e registra em andamento / em risco / parado. Um W2 ou W3 parado é reportado ao concedente imediatamente, não apenas na expiração — na expiração já é tarde para ajudar.

### Etapa 7 — Expiração

Uma de três coisas acontece, e é registrada:

- **Remediado** → waiver encerrado, workload conforme, linha do registro encerrada com a data.
- **Renovado** → dentro dos limites de renovação da §3, com evidência de progresso. Uma renovação é uma nova decisão do concedente, nunca uma extensão por padrão.
- **Expirado** → o workload fica não conforme com uma ADR aceita. O Arquiteto de Governança o leva ao comitê no próximo relatório de exceções, nomeando o workload, o time e o tempo transcorrido.

**Não há uma quarta opção.** Silêncio na expiração é uma expiração não tratada, e é reportado como tal.

## 5. Waivers de emergência

Para um incidente de produção em andamento, onde cumprir a cláusula prolongaria o incidente.

- Concedido verbalmente pelo [Arquiteto Principal](/ARC/agents/principal-architect), ou pelo [Security Architect](/ARC/agents/security-architect) quando a cláusula é relacionada a segurança.
- **Prazo máximo: 15 dias**, sem renovação neste nível.
- Deve ser escrito no registro em até 2 dias úteis com os campos da §4, retroativamente.
- Antes de expirar, deve ser convertido em uma solicitação normal W1/W2/W3 ou remediado.
- Um waiver de emergência não pode cobrir uma matéria de nível W3 além dos seus 15 dias. Exposição regulatória vai ao comitê, com urgência se necessário, nunca silenciosamente.

Waivers de emergência que nunca são formalizados por escrito são a forma mais provável de este processo falhar. O Arquiteto de Governança reporta a contagem de waivers de emergência e sua latência de formalização em todo resumo mensal.

## 6. Quem pode fazer o quê

| | Solicitar | Classificar | Recomendar | Conceder W1 | Conceder W2 | Conceder W3 | Encerrar / registrar expiração |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Time de entrega | ✅ | | | | | | |
| Arquiteto de domínio | ✅ | | ✅ (própria ADR) | | concede em conjunto | | |
| Arquiteto de Governança | | ✅ | ✅ | ✅ | | | ✅ |
| Arquiteto Principal | | recurso | | | ✅ | | |
| Comitê de arquitetura | | | | | | ✅ | ✅ |

## 7. Registro de waivers

O registro. Uma linha por waiver, nunca excluída — um waiver encerrado permanece visível, porque o histórico do que foi objeto de waiver contra uma cláusula é evidência sobre a cláusula.

| Campo | Significado |
| --- | --- |
| `ID do waiver` | `W-AAAA-NNN`, sequencial, nunca reutilizado |
| `Status` | `solicitado` \| `concedido` \| `recusado` \| `remediado` \| `renovado` \| `expirado` |
| `Nível` | `W1` \| `W2` \| `W3` \| `emergência` |
| `Workload` | O sistema único nomeado |
| `Time` / `Solicitante` | |
| `ADR` / `Cláusula` | ex.: `ADR-0007 / C3` |
| `Concedido por` | Indivíduo nomeado, ou `comitê` |
| `Concedido em` | Data |
| `Expira em` | Data |
| `Data de revisão intermediária` | Data, e o resultado registrado |
| `Contagem de renovações` | 0, 1, … contra o limite da §3 |
| `IDs de obrigação` | `OBL-*` ou `nenhum` |
| `Dados pessoais` | Sim/Não |
| `Responsável pela remediação` | Indivíduo nomeado |
| `Remediação prevista para` | Data |
| `Encerrado em` / `Motivo do encerramento` | |

### Registro atual

| ID do waiver | Status | Nível | Workload | Time | ADR / Cláusula | Concedido por | Concedido em | Expira em | Revisão intermediária | Renovações | IDs de obrigação | Dados pessoais | Responsável pela remediação | Remediação prevista para | Encerrado em / motivo |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| — | — | — | *Nenhum waiver. Nenhuma ADR foi aceita ainda, então não há nada que possa receber waiver.* | — | — | — | — | — | — | — | — | — | — | — | — |

## 8. Revisão mensal (Arquiteto de Governança)

Executada contra o registro todo mês, com o resultado registrado em uma task do Escritório:

| Verificação | Ação |
| --- | --- |
| Waivers expirando em até 30 dias | Notificar o responsável pela remediação e o concedente |
| Waivers vencidos sem encerramento | Marcar `expirado`; incluir no relatório de exceções ao comitê |
| Revisões intermediárias devidas ou atrasadas | Executá-las; registrar em andamento / em risco / parado |
| Remediações W2 / W3 paradas | Reportar ao concedente imediatamente |
| Waivers de emergência ainda não formalizados | Cobrar; reportar a latência |
| Limites de renovação esgotados | Escalar o nível conforme a §3 |
| **Três ou mais waivers contra a mesma cláusula de uma ADR** | Acionar a §9 |

**O relatório mensal de exceções ao comitê** declara: waivers concedidos por nível, recusados, expirados, contagem de emergências e latência de formalização, e toda cláusula que atingiu o limite da §9.

## 9. Três waivers contra uma cláusula reabre a ADR

Quando um terceiro waiver é concedido contra a mesma cláusula da mesma ADR, o Arquiteto de Governança **não deve** conceder um quarto. Em vez disso:

1. A cláusula é sinalizada no [registro de decisões](/ARC/issues/ARC-2#document-decision-registry).
2. O arquiteto de domínio responsável reabre a ADR como um novo rascunho, pelo caminho padrão.
3. A nova ADR, ao ser aceita, substitui sua antecessora **por completo** (substituição parcial não é permitida — ver as regras de substituição do registro).
4. Waivers existentes contra a cláusula antiga seguem até sua expiração ou até que a sucessora seja aceita, o que ocorrer primeiro.

Não conformidade repetida é evidência de que o padrão está errado, não os times. Uma função de governança que concede o mesmo waiver cinco vezes não aprendeu nada e substituiu silenciosamente seu próprio padrão por um não documentado — exatamente a falha que este documento existe para prevenir.

## 10. Questões abertas para o comitê

| # | Questão | Suposição de trabalho do Escritório |
| --- | --- | --- |
| 1 | Qual função do banco corrobora formalmente um waiver W3 junto com o comitê? | Segurança, risco e compliance consultados; comitê concede |
| 2 | Os limites de prazo (90 / 180 / 365 dias) são aceitáveis para o apetite de risco do banco? | Como declarado na §3 |
| 3 | Waivers expirados devem gerar um evento formal de risco no sistema de risco do banco? | Escritório reporta ao comitê; sem evento de risco automático |
| 4 | A janela de 15 dias para waiver de emergência é operacionalmente viável? | Como declarado na §5 |
| 5 | Três é o limite correto para reabrir uma ADR (§9)? | Três |
| 6 | Para waivers que tocam `OBL-PCI`, o comitê quer exigir validação formal de um QSA antes da concessão, além da consulta a compliance de cartões já prevista em W3? | Consulta a compliance de cartões/segurança como para os demais W3; validação formal de QSA fica a critério do comitê |
