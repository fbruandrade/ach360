# Hand-off da primeira onda ao comitê, 5ª rodada: gate interno do Arquiteto Principal (D-26)

| Campo | Valor |
| --- | --- |
| Task | [ARC-207](/ARC/issues/ARC-207) |
| Origem | [Hand-off da 4ª rodada, ARC-179](/ARC/issues/ARC-179#document-handoff-comite-primeira-onda-r4) (§1.1, §1.2 e D-25) |
| Data | 2026-10-09 |
| Autor | Arquiteto Principal |
| Escopo | **Só** os itens da §1.1 (`ADR-0003`, `ADR-0010`) e da §1.2 (emendas `ADR-0006`, `0012`, `0015`) do hand-off r4, e a transcrição de D-25. A classe F (`ADR-0002`, `0011`, `0013`, `0014`, `AR-001` a `AR-005`) não volta a este gate. |
| Base revisada | Texto atual de cada documento, lido em 2026-10-09 depois das 03:34 UTC: `ADR-0003` rev. 11; `ADR-0010` rev. 10; `ADR-0006` rev. 15; `ADR-0012` rev. 13; `ADR-0015` rev. 13; registro de decisões ([ARC-2](/ARC/issues/ARC-2#document-decision-registry)) rev. 34. Conferência do Governance sobre `ADR-0012`/`ADR-0015` em [ARC-206](/ARC/issues/ARC-206) (comentário `8119fa75`). |
| Re-auditoria de prontidão | [ARC-206](/ARC/issues/ARC-206) foi **cancelada a pedido do usuário do board**, sem veredito formal de prontidão. Para `ADR-0012`/`ADR-0015`, a conferência item a item do Governance existe e foi usada. Para `ADR-0003` e `ADR-0006`, este gate conferiu os itens direto no texto. Os resíduos que o Governance confere seguem com ele (§1.3). |
| Natureza | Gate interno do Escritório (modelo operacional, Etapa 5). **Nada aqui é aprovação.** A aprovação é exclusiva do comitê de arquitetura (guilda) do banco. |

## 0. Resultado

1. **Duas emendas prontas para o comitê:** emenda à `ADR-0012` (aceita, rev. 4), texto da emenda na rev. 13; e emenda à `ADR-0015` (aceita, rev. 4), texto da emenda na rev. 13. As três linhas da §1.2 de cada uma estão fechadas no texto. O status das duas segue `aceita` na rev. 4 até o comitê decidir a emenda (D-24(c)).
2. **Duas passam à classe F**, com resíduo obrigatório que o Governance confere. Não voltam a este gate.
   - `ADR-0003` (`proposta`): os cinco itens da §1.1 estão fechados na substância. **Condição de envio D-25(e) não cumprida:** as três confirmações restritas sobre cláusula de mérito tocada (C8/Q5, C3/C2 e C9) expiraram sem resposta (§1.1).
   - Emenda à `ADR-0006` (aceita, rev. 12): itens (1), (2) e (4) fechados; item (3) só na forma (§1.2). Classe F para emenda: D-26(a).
3. **Uma volta, sem mudança de lista:** `ADR-0010` (`proposta`). Nada da §1.1 foi corrigido. A rev. 10 é anterior ao hand-off r4, e a 5ª rodada não teve task de retrabalho para ela. **A falha é do Escritório, não do autor** (D-26(b)).
4. **Transcrição de D-25: não conforme no registro.** D-25(c) está transcrita em `ADR-0012` e `ADR-0015`. D-25 não está indexada no registro §13, D-25(b) não virou precedente 22 e a re-auditoria de [ARC-177](/ARC/issues/ARC-177) não foi corrigida conforme D-25(d). Os três itens eram do Governance em ARC-206, que foi cancelada.

## 1. Conferência item a item

Para cada item, a seção corrigida e o resultado da busca pelos termos antigos entre aspas no documento inteiro, conforme a §3 do hand-off r4. Ocorrências dentro de changelog ou de transcrição literal de revisor não contam.

### 1.1 Devolvidas (S)

| Artefato | Revisão lida | Item | Veredito | Localização e busca |
| --- | --- | --- | --- | --- |
| `ADR-0003` | rev. 11 | (1) C9 | Fechado | C9 separa a geo-redundância **nativa automática** (GRS/GZRS), que não existe in-country, do caminho (iii), replicação explícita gerida pelo workload. A §7 C9 lista os três caminhos separados. §6 BACEN/CMN e §10 não descrevem mais `Brazil Southeast` como par configurável. A restauração do Key Vault exige `Brazil Southeast` para workload com restrição de residência. O changelog da rev. 10 foi corrigido em linha nova (2026-10-09). "não há um modo de geo-redundância" aparece só qualificado por "nativa automática"; "restringindo o par" e "pareada explicitamente" não aparecem; "outra região escolhida" aparece só com a restrição. |
| | | (2) D-22(f) | Fechado | C3 restringe o `deny` a controle de **recurso** e remete a C8 ao PIM/authentication context. A Q5 cita o trio `ADR-0003`/`ADR-0012`/`AR-004`. "independentemente do que esta" não aparece fora do changelog. O rótulo "Resposta condicional" declara que as condições não foram confirmadas pelo Security Architect. |
| | | (3) Q1/Q2 | Fechado | "bloqueiam" ficou só em C3 (Security Zones da OCI), que é outro sentido. A §5 provisiona a unidade **antes** da aprovação do enclave, na mesma ordem da Q1. |
| | | (4) Âncora | Fechado na substância; resíduo F | O pedido da 4ª rodada cita a rev. 10 (2026-10-07T06:46:24Z) e a interação `b64ad59f`. **Resíduo:** as duas confirmações novas desta rodada (C9 → Infrastructure; C3/C2 → Security) são citadas só como "ver interações vinculadas em ARC-201", sem `revisionNumber`/`createdAt`, que é o mesmo defeito do item. O texto também diz que a de C8/Q5 "permanece aberta", e ela consta como **expirada**. |
| | | (5) C2 | Fechado | C2 não leva mais o *connected-to* à unidade dedicada, e cita D-22(c) e `ADR-0002` C2. "ou que se conecta a um sistema que o faz" não aparece. |
| `ADR-0010` | rev. 10 (2026-10-09T03:06Z, anterior ao hand-off r4) | (1)–(6) | **Não corrigidos** | Os termos antigos estão todos no corpo. "runtime Axway dedicado na subzona DMZ-CDE" como destino vigente: §4.1 `DC-AA`. "12 meses da data de vigência": §4.2 e três linhas da §5. "status quo, sem redução": §6, risco residual, e Q9. Os itens (4)–(6) não foram tocados. |

**Confirmações restritas de `ADR-0003`: todas expiraram sem resposta.** `b64ad59f` (C8/Q5 e C3/§6/§6.1, criada em 2026-10-07T06:54:15Z) expirou em 53 s. `28dd4523` (C9, Infrastructure, 2026-10-09T03:27:03Z) expirou em 14 s. `d8089188` (C3/C2, Security, 2026-10-09T03:27:16Z) expirou quando [ARC-201](/ARC/issues/ARC-201) foi fechada. O pedido foi aberto como interação na task do **autor**, e a interação expira quando essa task fecha. Assim, o revisor não tem tempo de responder. Esse é o terceiro artefato com o mesmo padrão (ver também os pedidos reancorados de `AR-003`/`AR-004`/`AR-005` em [ARC-171](/ARC/issues/ARC-171)). D-26(c) corrige o mecanismo.

### 1.2 Emendas a ADR aceita

| Artefato | Revisão aceita / lida | Item | Veredito | Localização e busca |
| --- | --- | --- | --- | --- |
| Emenda à `ADR-0006` | aceita: rev. 12; lida: rev. 15 | (1) Testemunha | Fechado | §5 ("contratação já aprovada ([ARC-46]), ainda não efetivada — especificação técnica entregue ([ARC-42])"), §6 dependências, `OBL-CLOUD`, §7 C10 e §4.1 `OCI-K8S` dizem o mesmo estado. "já contratada", "for adotada", "se adotado", "testemunha de quorum opcional" e "se contratada" só aparecem no changelog de 2026-10-09. |
| | | (2) Lista de diferenças | Fechado | Nova entrada na §11 com o 2º/3º itens da §5, a frase de `OBL-CLOUD` ("todo primário de todo domínio, nos dois sítios, se cerca"), os campos do cabeçalho §0 e G15. |
| | | (3) A19 e marca de rodada | **Só na forma; resíduo F** | A Q7 não tem mais a marca de rodada. Mas as respostas da §6 ficaram "**Sim** — indiretamente, qualquer dado pessoal…" e "**Não** — diretamente, isso é tratado por `ADR-0015`…". O negrito mudou e a ressalva continua na resposta. O pedido era responder `Sim`/`Não` e pôr a ressalva na frase seguinte, por exemplo: "**Sim.** O efeito é indireto: …". Vale para as três perguntas (dado pessoal, dado de cartão, rastreabilidade). |
| | | (4) Leitura local | Fechado | §1.1, linha de partição completa: "Leitura local em cada site, servida do dado que já tinha replicado até a partição — nenhuma escrita, em nenhum domínio". "servindo o que já tinha" só aparece no changelog. |
| | | Cabeçalho de emenda | Resíduo F | O bloco "Como usar" ainda diz "Esta revisão (2026-10-07) é uma emenda… registrada sob o gate ARC-139". A emenda agora vai até a rev. 15 e passou pelos gates ARC-179/ARC-207. Atualizar a data e o gate, sem mudar o status `aceita` (rev. 12). |
| Emenda à `ADR-0012` | aceita: rev. 4; lida: rev. 13 | (1) D-24(a) | Fechado | O blockquote e a §0 `revisao_aceita` dizem "proposta na emenda, nunca vigente (D-24(a))", com link para a rev. 4. |
| | | (2) Rev. 12 / D-25(c) | Fechado | A diferença 11 registra o encerramento de ARC-60. A redação de D-25(c) está em §6/§6.1/§9 Q5. "lacuna definitiva" só aparece na diferença 11, citada para dizer o que foi substituído. "seguem em ARC-60" e "vier a ser confirmado" não aparecem. |
| | | (3) Data ARC-133 | Fechado | Agora é 2026-10-06 no corpo e na §0. |
| Emenda à `ADR-0015` | aceita: rev. 4; lida: rev. 13 | (1) D-24(a) | Fechado | "rev. 4 ou rev. 5" e "até confirmação do Arquiteto" não aparecem. A rev. 4 é a vigente, com link. |
| | | (2) C16 | Fechado | "já vigente" não aparece. A C16 é proposta na emenda. Na rev. 4, a C15 é `MAY`. |
| | | (3) Rev. 12 | Fechado | A diferença 11 registra a mudança da C3. "Não altera nenhuma cláusula" foi corrigida em linha nova. "seguem em ARC-60" e "confirmação em ARC-60" não aparecem. A §5 não amarra mais o prazo de 2026-12-06 à confirmação de tier. A frase "essa confirmação, quando feita" (§6.1) é compatível com D-25(c) ("reabre com evidência de campo nova"), não é resíduo. A menção na §10 (Infrastructure) é transcrição literal de revisor. |
| | | (4) D-24(g) | Fechado | "Reduz, em vez de deslocar" não aparece. Os efeitos de conexão (amplia) e de dado (reduz) estão separados entre §5 e §6. |

### 1.3 Resíduos F obrigatórios (o Governance confere; não voltam a este gate)

| Artefato | Responsável | Resíduos | Condição de envio |
| --- | --- | --- | --- |
| `ADR-0003` | Cloud | (a) §10: ancorar as confirmações novas com `revisionNumber` (11) e `createdAt` reais da revisão, e o ID da interação ou task que vier a substituí-las (D-26(c)). (b) §10 e changelog: declarar que a confirmação de C8/Q5 (`b64ad59f`) **expirou sem resposta**, e não que "permanece aberta", e citar o novo pedido. | D-25(e): vereditos do Security Architect sobre C8/Q5 e C3/C2, e do Infrastructure Architect sobre C9, **ancorados à rev. 11 ou posterior**. Se algum veredito pedir mudança de mérito, a ADR volta como S. |
| Emenda à `ADR-0006` | Infrastructure | (a) A19 nas três perguntas da §6, como na §1.2. (b) Bloco "Como usar": data e gate da emenda. Registrar as duas correções na lista de diferenças da §11, em linha nova. | Sem confirmação restrita, porque a correção é só de redação. Vai como "emenda à `ADR-0006` (aceita, rev. 12)". |

## 2. Decisões do gate (D-26)

Mesma autoridade de D-17, D-22, D-24 e D-25. Vinculam quando transcritas. **Nenhuma delas é aprovação.**

| # | Questão | Decisão | Raciocínio e alternativas rejeitadas | Efeito PCI-DSS | Quem transcreve |
| --- | --- | --- | --- | --- | --- |
| (a) | **Classe F para emenda a ADR aceita** | D-25(a) vale também para emenda: com todos os itens da lista fechada resolvidos na substância e só resíduo de redação, a emenda passa à classe F. O Governance confere e ela não volta a este gate. | Rejeitado: devolver a emenda de `ADR-0006` por formatação de A19, porque custaria uma 6ª rodada a uma ADR que já está `aceita`. Rejeitado também: enviar com o resíduo, porque o comitê leria a ressalva dentro da resposta de escopo. | — | Governance (registro §6) |
| (b) | **`ADR-0010` sem retrabalho na 5ª rodada** | A §1.1 do hand-off r4, linha `ADR-0010`, vale **sem mudança** para a 6ª rodada: sem item novo e sem tirar item. A devolução não conta contra o autor. A lista ampliada foi postada em [ARC-200](/ARC/issues/ARC-200) depois de essa task já estar `done`, e o Escritório não abriu task da 5ª rodada para a ADR. | Rejeitado: passar `ADR-0010` à classe F, porque os itens (1)–(6) são de substância (destino do caminho de cartão, efeito PCI-DSS do interino) e estão abertos. Rejeitado também: ampliar a lista agora, porque a lista fechada protege o autor (D-25(b)). | Sem mudança. O caminho de cartão no interino continua descrito de forma contraditória no texto. Famílias 1, 4 e 6. | Governance (registro §13) |
| (c) | **Confirmação restrita como task, não interação na task do autor** | A partir desta data, a confirmação restrita (precedente 14) é aberta como **task atribuída ao revisor**, com a revisão-âncora (`revisionNumber`, `createdAt`) na descrição. A ADR cita o ID da task. O veredito é comentário na task do revisor. A interação em task do autor fica proibida para esse fim. | Rejeitado: manter a interação e pedir ao autor que não feche a task, porque prende o autor a uma espera fora do controle dele e já falhou em `ADR-0003`, `AR-003`, `AR-004` e `AR-005`. Rejeitado também: aceitar a confirmação expirada como tácita, porque silêncio não é veredito de par (D-25(e)). | — | Governance (checklist, novo item em G11; precedente 23) |

**Pedido ao Governance Architect** (os pedidos da §2 do hand-off r4 continuam valendo, porque ARC-206 foi cancelada antes deles): indexar D-25 e D-26 no registro §13. Registrar D-25(b) como precedente 22 e D-26(c) como precedente 23. Corrigir a re-auditoria de [ARC-177](/ARC/issues/ARC-177) §4/§6 conforme D-25(d). Conferir os resíduos F da §1.3 e enviar `ADR-0003` e a emenda à `ADR-0006` quando cada uma cumprir a condição.

## 3. Pacote ao comitê nesta data

| Item | Como vai ao comitê | Revisão | Status atual |
| --- | --- | --- | --- |
| Emenda à `ADR-0012` (aceita, rev. 4) | Pronta | rev. 13 | `aceita` (rev. 4) até a decisão do comitê |
| Emenda à `ADR-0015` (aceita, rev. 4) | Pronta | rev. 13 | `aceita` (rev. 4) até a decisão do comitê |

Classe F da 4ª rodada (`ADR-0002`, `0011`, `0013`, `0014`), fora deste gate: o Governance envia cada uma quando cumprir D-25(e). `ADR-0002` depende dos vereditos de par de [ARC-67](/ARC/issues/ARC-67)–[ARC-70](/ARC/issues/ARC-70), com vencimento em 2026-10-21.

Travas de AR (D-24(k), D-25(d)): `AR-003`, `AR-004` e `AR-005` continuam presas por `ADR-0003` até ela ir ao comitê. `AR-001` e `AR-002` continuam presas por `ADR-0003` e `ADR-0010`.

## 4. Observações para o comitê (só o que mudou desde a 4ª rodada)

- **PAN em telemetria:** a emenda à `ADR-0015` que propõe a C16 (`MUST NOT`) está pronta neste pacote. Até o comitê aceitá-la, a única cláusula vigente sobre PAN em APM/trace é `MAY` (rev. 4, C15). Família 3 (e 10).
- **Tier do IdP federado e da plataforma central de log:** **Desconhecido**, lacuna aberta, sem coleta ativa desde 2026-10-09. A redação de D-25(c) está transcrita nas duas emendas. Família 12 (e 10 para `ADR-0015`).
- **Caminho de cartão no interino de API gateway (`ADR-0010`):** ainda não vai ao comitê. Até lá, nenhum texto do Escritório descreve de forma consistente se o Axway existente (DMZ compartilhada) atende o tráfego de cartão no interino, nem quais sistemas ele leva a *connected-to*. Famílias 1, 4 e 6.

## 5. Próximos passos

1. Technical: retrabalho de `ADR-0010`, 6ª rodada, com a lista da §1.1 do hand-off r4 sem mudança (D-26(b)).
2. Cloud: resíduos F de `ADR-0003` (§1.3).
3. Security e Infrastructure: confirmações restritas de `ADR-0003` rev. 11, cada uma como task própria (D-26(c)).
4. Infrastructure: resíduos F da emenda à `ADR-0006` (§1.3).
5. Governance: registro (D-25, D-26, precedentes 22 e 23), correção de ARC-177, conferência dos resíduos F e envio sob D-25(e).
6. **6ª rodada deste gate**, restrita a `ADR-0010`, numa task minha bloqueada pelo retrabalho.

Nada neste documento descreve artefato algum como aprovado. A aprovação é do comitê de arquitetura do banco.
