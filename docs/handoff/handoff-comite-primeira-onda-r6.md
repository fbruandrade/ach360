# Hand-off da primeira onda ao comitê, 6ª rodada: gate interno do Arquiteto Principal (D-27)

| Campo | Valor |
| --- | --- |
| Task | [ARC-215](/ARC/issues/ARC-215) |
| Origem | [Hand-off da 5ª rodada, ARC-207](/ARC/issues/ARC-207#document-handoff-comite-primeira-onda-r5), D-26(b) |
| Data | 2026-10-09 |
| Autor | Arquiteto Principal |
| Escopo | **Só** os itens (1)–(6) da linha `ADR-0010` da §1.1 do [hand-off r4](/ARC/issues/ARC-179#document-handoff-comite-primeira-onda-r4), sem item novo e sem tirar item (D-26(b)). Nenhum outro artefato volta a este gate. |
| Base revisada | [`ADR-0010`](/ARC/issues/ARC-6#document-adr-0010-api-gateway-exposure-strategy) **rev. 13** (2026-10-09T03:49:28.239Z), lida depois de fechadas [ARC-209](/ARC/issues/ARC-209) (retrabalho, comentário `4d6d014e`) e [ARC-217](/ARC/issues/ARC-217) (confirmação restrita do Security Architect, D-26(c)). |
| Natureza | Gate interno do Escritório (modelo operacional, Etapa 5). **Nada aqui é aprovação.** A aprovação é exclusiva do comitê de arquitetura (guilda) do banco. |

## 0. Resultado

**`ADR-0010` volta (`proposta`), com uma lista ainda mais curta. Não passa à classe F.**

- **Fechados:** os itens (3) (relógio dos 12 meses) e (5) (família 3 para D-9, já com o 3.7 pedido em ARC-217).
- **Fechados na substância, com resíduo de redação:** os itens (1) (varredura de D-24(d)) e (6) (Q10 na §4.1). Em cada um sobrou um trecho que o próprio item r4 citava literalmente.
- **Abertos na substância:** a metade (2b) do item (2) e a metade (4b) do item (4). Os dois tratam do efeito PCI-DSS do interino, o mesmo motivo que impediu a classe F em D-26(b).
  - **(2b):** o texto diz que a DMZ compartilhada fica fora do CDE "nos dois estados", e isso contradiz a própria §5.
  - **(4b):** o efeito *connected-to* está declarado, mas sem as famílias.

O retrabalho foi bem feito em quase tudo: quatro itens fechados ou quase, busca declarada item a item e confirmação aberta como task, conforme D-26(c). Os pontos que faltam são estreitos. Estão em [ARC-219](/ARC/issues/ARC-219), e a 7ª rodada deste gate ([ARC-220](/ARC/issues/ARC-220)) confere só esses pontos (D-27(c)).

## 1. Conferência item a item

Para cada item: a seção corrigida e o resultado da busca, no documento inteiro, pelos termos antigos entre aspas. Ocorrências em changelog ou em transcrição literal de revisor não contam.

| Item r4 | Veredito | Localização e busca |
| --- | --- | --- |
| (1) D-24(d), varredura | **Fechado na substância; resíduo F (1b)** | Os pontos corrigidos declaram o Axway existente, inteiro no CDE, até a confirmação de capacidade:<br>• §4.1 `DC-AA` e `AGW-AXW`<br>• §4.3, linha 9<br>• §4.2<br>• §5, linha "Chamadas com dado de portador de cartão atravessando `AGW-AWS`/`AGW-AZR`"<br>• §6, coluna de evidência, que agora traz a verificação documental do interino<br>• §6, célula Família (famílias 1, 4 e 6 ligadas também ao interino)<br>**Resíduo (1b):** na tabela de migração da §5, a linha "Runtimes Axway dedicados à subzona DMZ-CDE (C17)…" ainda diz que esses runtimes absorvem "o tráfego de dado de portador de cartão hoje em `AGW-AWS`/`AGW-AZR`". O item r4 cita esse trecho literalmente, e ele não foi tocado. A busca do autor procurou "runtime Axway dedicado na subzona DMZ-CDE", e esta linha usa outro termo ("Runtimes Axway dedicados à subzona"). |
| (2) "Status quo, sem redução" | **(2a) fechado; (2b) aberto, S** | **(2a):** "status quo, sem redução" só aparece na §5, em forma negada ("não é…"). A §5, a §6 (risco residual) e a Q9 usam **reduz** frente a hoje e **amplia** frente ao alvo.<br>**(2b):** o item r4 pedia: "A §6 também afirma, sem ressalva, que a DMZ compartilhada 'permanece fora' do CDE. No interino, o Axway existente, que fica na DMZ compartilhada, está inteiro no CDE. Qualificar." O texto foi para o lado oposto e generalizou a afirmação: a §5, a §6 (risco residual) e a Q9 dizem agora que, "nos dois estados, a DMZ compartilhada permanece fora do CDE, restrita a L3/L4 sem decifrar". A resposta da §6 sobre sistema conectado ao CDE repete que "a DMZ compartilhada e a borda L3/L4 anti-DDoS permanecem fora". Esses trechos contradizem três pontos da própria ADR:<br>• a §5, rota legada ([ARC-182](/ARC/issues/ARC-182)): "a partir da vigência de C17, é exatamente o Axway compartilhado que passa a participar do caminho de cartão";<br>• a §4.1 `AGW-AXW`;<br>• o risco residual da §6 (quatro lacunas do `DC-AA`, entre elas a segmentação leste-oeste em torno do Axway).<br>Decisão em D-27(a). |
| (3) Relógio dos 12 meses | **Fechado** | "12 meses da data de vigência" não aparece mais como regra de migração. A §4.2, as três linhas da §5 (borda, consumidor externo, encadeamento) e a Q9 contam a partir da confirmação de capacidade pelo banco. As ocorrências que ainda contam "da data de vigência" são outros relógios: revisão de C16, 60 e 90 dias, 6 meses da C13 e os cortes imediatos. |
| (4) Efeito PCI-DSS do interino sobre conectividade | **(4a) fechado; (4b) aberto, S** | **(4a):** a §6 (risco residual) diz que os sistemas alcançados pelo Axway existente pela travessia de C5 ou pelo segundo salto de C6-bis passam a *connected-to* no interino. Também nomeia o inventário como pendente do Security Architect. A leitura foi confirmada em [ARC-217](/ARC/issues/ARC-217), item 1.<br>**(4b):** o item r4 pedia "Dizer, com as famílias". O parágrafo não traz família, e a célula Família não fala dos sistemas *connected-to*. Além disso, a resposta da §6 a "introduz ou altera um sistema conectado ao CDE?" só diz que `AGW-AWS`/`AGW-AZR` ficam fora do caminho de cartão, e não diz que, no segundo salto de C6-bis, eles passam a *connected-to*. O comitê lê essa resposta antes do risco residual. |
| (5) D-9, família 3 | **Fechado** | A célula Família traz "família 3 — 3.5/3.6/3.7", com o ciclo de vida da chave de C10. O 3.7 foi pedido pelo Security Architect ([ARC-217](/ARC/issues/ARC-217), item 3) e aplicado na rev. 13. A remissão da resposta de efeito à célula Família agora encontra a família 3. |
| (6) Q10 na §4.1 | **Fechado na substância; resíduo F (6b)** | A linha `AWS-K8S` declara o salto único e o ALB interno, e a `AWS-VM` herda por "Idem". O lado AWS de C6-bis, C7 e §4.3 (linhas 2 e 4) já estava correto.<br>**Resíduo (6b):** a linha `AGW-AWS` ainda diz "Pode ocupar o segundo salto do único encadeamento permitido (C6-bis)", e a `AGW-AZR` repete com "Idem". O item r4 cita esse trecho literalmente. A busca do autor ficou nas linhas de plataforma e não chegou às linhas de gateway. |
| Conexo a D-26(c) | **Resíduo F (c)** | A §6 ainda diz "Confirmação restrita pendente (D-26(c))… [ARC-217](/ARC/issues/ARC-217), ancorada à rev. 11". A ARC-217 está `done`, com veredito aplicado na rev. 13 (changelog). Não é item novo: é a ancoragem que a própria D-26(c) exige. |

## 2. Decisões do gate (D-27)

| ID | Decisão | Razão | Alternativas rejeitadas | Efeito PCI-DSS | Registro |
| --- | --- | --- | --- | --- | --- |
| (a) | **A DMZ compartilhada no interino de C17** | O Security Architect confirmou, em [ARC-217](/ARC/issues/ARC-217) item 2, a qualificação "reduz/amplia" e, junto com ela, a frase "a DMZ compartilhada fora do CDE nos dois estados (L3/L4, sem decifrar)". A qualificação reduz/amplia fica confirmada. **A frase da DMZ não fica.** No interino, o Axway existente, que está na DMZ compartilhada, termina a TLS do tráfego de cartão e está inteiro no CDE. Isso está na própria ADR (§4.1 `AGW-AXW`; §5, rota legada, [ARC-182](/ARC/issues/ARC-182)). Para o PCI-DSS, o componente que decifra PAN está no escopo. O segmento de rede que o hospeda também entra no escopo, ou passa a *connected-to*, a menos que haja segmentação evidenciada. A segmentação leste-oeste em torno do Axway no `DC-AA` é uma das quatro lacunas que a própria §6 nomeia. O texto passa a dizer três coisas:<br>(i) só a borda L3/L4 anti-DDoS fica fora sem decifrar nos dois estados;<br>(ii) no interino, o segmento da DMZ compartilhada que hospeda o Axway existente está no escopo, ou é *connected-to*, até haver segmentação evidenciada (Q11, ARC-182);<br>(iii) "DMZ compartilhada fora do CDE" vale só no desenho-alvo. | **Tomar a confirmação de ARC-217 como fechamento.** Rejeitado: a pergunta feita ao revisor já trazia a frase da DMZ como dada, e o veredito foi sobre reduz/amplia. Uma confirmação de par também não resolve uma contradição entre duas seções da mesma ADR.<br>**Ler "DMZ compartilhada" como só a borda L3/L4.** Rejeitado: a ADR usa o termo para a zona onde o Axway fica (§4.1 `DC-AA`, `AGW-AXW`), e o comitê não teria como fazer essa leitura.<br>**Mover a correção para `ADR-0014` C5.** Rejeitado: é a Q11, decisão separada que continua aberta. A afirmação errada está nesta ADR. | Corrige uma afirmação de escopo que hoje **subdeclara** o CDE no interino. Famílias 1 (1.2/1.3/1.4, segmentação e controle de conexão entre CDE e rede não confiável) e 12 (12.5.2, confirmação documentada do escopo). Lacuna mantida como lacuna: o Escritório não validou com QSA (Q8). | Governance (registro §13) |
| (b) | **Classificação de `ADR-0010` na 6ª rodada** | Volta como S, com a lista restrita a (2b), (4b), (1b), (6b) e (c), que são as partes não fechadas dos itens r4. Não há item novo (D-25(b), D-26(b)). Desta vez a falha é de execução da varredura. Nos três resíduos, o item r4 citava o trecho literalmente. Na frase da DMZ, a própria pergunta de confirmação apresentou a frase como resolvida. | **Classe F, com o Governance conferindo.** Rejeitado: (2b) e (4b) são efeito PCI-DSS do interino, o mesmo motivo de D-26(b), e a conferência F é de redação, não de escopo PCI-DSS.<br>**Enviar com ressalva.** Rejeitado: o comitê leria na §6 uma afirmação de escopo contraditória (D-25(a)). | O caminho de cartão no interino está descrito de forma consistente quanto ao **destino**. Continua inconsistente quanto ao **perímetro** da DMZ e aos sistemas *connected-to*. Famílias 1, 4, 6, 10 e 12. | Governance (registro §13) |
| (c) | **Escopo da 7ª rodada** | O gate confere só os cinco pontos da §1. Se (2b)/(4b) estiverem fechados na substância e o veredito do Security Architect estiver ancorado à revisão corrigida (D-26(c)), `ADR-0010` vai direto a "pronta para o comitê", ou à classe F se sobrar só redação. Não se abre lista nova. Se o veredito pedir mudança de mérito, só esse ponto volta. | **Ampliar a lista com observações novas.** Rejeitado pela mesma proteção ao autor de D-25(b). | — | Governance (registro §13) |

**Pedido ao Governance Architect:** indexar D-27 no registro §13, junto com D-25 e D-26 ([ARC-214](/ARC/issues/ARC-214)). D-27(a) é leitura de escopo PCI-DSS. Registrá-la como precedente 24 do checklist §4: uma confirmação de par não fecha uma afirmação de escopo que contradiz outra seção da mesma ADR.

## 3. Travas de AR e risco que o comitê precisa conhecer

- `AR-001` e `AR-002` continuam presas por `ADR-0010` (D-24(k), D-25(d)), além de `ADR-0003` até o envio dela.
- **Caminho de cartão no interino (`ADR-0010`):** ainda não vai ao comitê. O destino do tráfego de cartão no interino já está consistente: o Axway existente, inteiro no CDE, com o gateway de nuvem fora do caminho. Continua não declarado, de forma consistente:
  - o perímetro da DMZ compartilhada em volta do Axway existente no interino;
  - as famílias dos sistemas que passam a *connected-to* por C5/C6-bis.

  Famílias 1, 10 e 12.

## 4. Próximos passos

1. Technical: [ARC-219](/ARC/issues/ARC-219), retrabalho dos cinco pontos da §1. A confirmação restrita de (2b)/(4b) vai ao Security Architect como task (D-26(c)).
2. Governance: registro de D-27 junto com D-25/D-26 ([ARC-214](/ARC/issues/ARC-214)).
3. Arquiteto Principal: [ARC-220](/ARC/issues/ARC-220), 7ª rodada deste gate, bloqueada por ARC-219.

Nada aqui é aprovação; o comitê de arquitetura do banco aprova.
