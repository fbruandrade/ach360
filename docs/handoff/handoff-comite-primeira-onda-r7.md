# Hand-off da primeira onda ao comitê, 7ª rodada: gate interno do Arquiteto Principal (D-28)

| Campo | Valor |
| --- | --- |
| Task | [ARC-220](/ARC/issues/ARC-220) |
| Origem | [Hand-off da 6ª rodada, ARC-215](/ARC/issues/ARC-215#document-handoff-comite-primeira-onda-r6), D-27(b)/(c) |
| Data | 2026-10-09 |
| Autor | Arquiteto Principal |
| Escopo | **Só** os pontos (2b), (4b), (1b), (6b) e (c) da §1 do hand-off r6, e a ancoragem do veredito do Security Architect sobre (2b)/(4b) (D-26(c)). Lista fechada (D-27(c)). Nenhum outro artefato volta a este gate. |
| Base revisada | [`ADR-0010`](/ARC/issues/ARC-6#document-adr-0010-api-gateway-exposure-strategy) **rev. 15** (2026-10-09T04:00:56.894Z), lida depois de fechadas [ARC-219](/ARC/issues/ARC-219) (retrabalho, comentários `1151f312` e `b4914ac3`) e [ARC-221](/ARC/issues/ARC-221) (confirmação restrita, comentário `ee2036a6`). |
| Natureza | Gate interno do Escritório (modelo operacional, Etapa 5). **Nada aqui é aprovação.** A aprovação é exclusiva do comitê de arquitetura (guilda) do banco. |

## 0. Resultado

**`ADR-0010` passa à classe F (D-28(a)). Não volta a este gate.**

- **Fechados na substância:** os cinco pontos. (2b) e (4b), que eram os pontos de efeito PCI-DSS do interino, estão corrigidos nos trechos pedidos e confirmados pelo Security Architect com veredito ancorado à revisão corrigida.
- **Fechados por inteiro:** (1b), (6b) e (c).
- **Resíduos F obrigatórios, os dois dentro de (4b):**
  - **(4b-F1):** a célula Família da §6 ainda não traz as famílias dos sistemas *connected-to*.
  - **(4b-F2):** a lista de sistemas *connected-to* nomeia `AGW-AWS` no segundo salto de C6-bis, posição que a própria §4.1 (rev. 15) e a Q10 dizem que ele não ocupa.

  Os dois só transcrevem conteúdo já decidido e confirmado. Nenhum dos dois muda a leitura de escopo nem pede nova confirmação (D-28(b)).

## 1. Conferência ponto a ponto

Busca feita no documento inteiro. Ocorrências no changelog ou em transcrição literal de revisor (§10) não contam.

| Ponto r6 | Veredito | Localização e busca |
| --- | --- | --- |
| (2b) DMZ compartilhada no interino | **Fechado** | Os quatro trechos pedidos trazem os três elementos de D-27(a): (i) só a borda L3/L4 anti-DDoS do `DC-AA` fica fora do CDE sem decifrar nos dois estados; (ii) no interino, o segmento da DMZ compartilhada que hospeda o Axway existente está no escopo, ou é *connected-to*, até haver segmentação evidenciada (Q11, [ARC-182](/ARC/issues/ARC-182)); (iii) "DMZ compartilhada fora do CDE" vale só no desenho-alvo.<br>• §5, risco de competência de Axway<br>• §6, risco residual da linha PCI-DSS<br>• §6, resposta a "introduz ou altera um sistema conectado ao CDE?"<br>• §9 Q9<br>**Busca:** "permanecem fora": zero fora do changelog. "permanece fora do CDE": só na resposta de §6, já na forma qualificada ("Só a borda L3/L4… permanece fora"). "nos dois estados" e "sem decifrar": só na forma corrigida, além de §10 e changelog. A frase da §6 "a DMZ compartilhada sai do escopo para o tráfego de cartão" vem precedida de "quando a subzona existir" e é consistente com (iii).<br>Agora consistente com a §4.1 `AGW-AXW`, com a §5 (rota legada) e com as quatro lacunas do `DC-AA` no risco residual da §6. |
| (4b) Famílias do *connected-to* | **Fechado na substância; resíduos F1 e F2** | §6, risco residual: nomeia a família 1 (1.2/1.3/1.4) e a família 12 (12.5.2) para os sistemas *connected-to* no interino. Também atribui o inventário desses sistemas ao Security Architect e o põe na coluna de evidência. §6, resposta a "sistema conectado ao CDE": agora diz quais sistemas passam a *connected-to* (os alcançados pelo Axway existente via C5 e o gateway nativo no segundo salto de C6-bis), e o comitê lê isso antes do risco residual. O Security Architect confirmou as duas famílias e disse por que as famílias 4 e 10 ficam no componente que decifra, não no *connected-to* ([ARC-221](/ARC/issues/ARC-221)).<br>**Resíduo (4b-F1):** a célula "Família de requisitos PCI-DSS afetada" ainda lista a família 1 só como "1.3/1.4" e só para o Axway existente e a subzona. Não fala dos sistemas *connected-to*, não traz 1.2 e não traz a família 12 (12.5.2). O hand-off r6 apontou isso ("a célula Família não fala dos sistemas *connected-to*"), e o comitê lê a célula como o resumo de famílias da ADR. Transcrever na célula o que o risco residual já diz e o Security Architect já confirmou.<br>**Resíduo (4b-F2):** o risco residual e a resposta da §6 nomeiam "o gateway nativo (`AGW-AWS`/`AGW-AZR`)" no segundo salto de C6-bis. A §4.1 `AGW-AWS`, corrigida nesta mesma rodada em (6b), e a Q10 dizem que o AWS API Gateway não ocupa o segundo salto hoje, por razão estrutural. Escrever que, hoje, só `AGW-AZR` (APIM Premium/Premium v2 em modo `Internal`) pode estar nessa posição, com remissão à Q10. O erro **superdeclara** o escopo e não subdeclara: tirar `AGW-AWS` da lista não reduz nenhum controle, porque a posição não existe na AWS. |
| (1b) Tabela de migração, runtimes dedicados | **Fechado** | §5, linha "Runtimes Axway dedicados à subzona DMZ-CDE (C17)…": agora diz que os runtimes absorvem, **do Axway existente**, o tráfego que passa a terminar ali na vigência por D-24(d), e põe `AGW-AWS`/`AGW-AZR` só como o ponto onde esse tráfego terminava antes. Busca "hoje em `AGW-AWS`/`AGW-AZR`": zero fora do changelog. A única outra ocorrência de "hoje em" (§5, rota legada) fala de chamadas sem cartão sob as migrações de 12 meses e está correta. |
| (6b) §4.1, linhas de gateway | **Fechado** | `AGW-AWS`: registra a assimetria de Q10 (inviável hoje, estruturalmente), o salto único pelo ingress privado e o ALB interno de C7 como alternativa a avaliar, igual a `AWS-K8S`, C7 e §4.3. `AGW-AZR`: deixa de ser "Idem" e registra a viabilidade a partir do APIM Premium/Premium v2 com VNet `Internal`, sem autenticar consumidor externo (C7). Busca "Pode ocupar o segundo salto": só em `AGW-AZR`, onde é correto. |
| (c) Ancoragem de D-26(c) | **Fechado** | §6, risco residual: "Confirmação restrita aplicada", com [ARC-217](/ARC/issues/ARC-217) (itens 1 e 3, rev. 11), a precedência de D-27(a) sobre o item 2 e [ARC-221](/ARC/issues/ARC-221) (rev. 14). Busca "Confirmação restrita pendente": zero. |
| Ancoragem do veredito sobre (2b)/(4b) | **Ancorado** | [ARC-221](/ARC/issues/ARC-221) foi aberta como task, não como interação, com `revisionNumber` 14 e `createdAt` 2026-10-09T03:56:52.854Z na descrição. A descrição registra que D-27(a) prevalece sobre ARC-217 item 2. O veredito cita a mesma âncora. **A diferença entre a rev. 14 e a rev. 15 foi conferida:** a rev. 15 só acrescenta a frase de citação de ARC-221 na §6 e uma linha de changelog. O texto que o revisor confirmou é o texto em vigor. O veredito diz, por conta própria, que é leitura do Escritório e não parecer de QSA (Q8). |

## 2. Decisões do gate (D-28)

| ID | Decisão | Razão | Alternativas rejeitadas | Efeito PCI-DSS | Registro |
| --- | --- | --- | --- | --- | --- |
| (a) | **`ADR-0010` passa à classe F** | Os cinco pontos da lista fechada estão resolvidos na substância, e o veredito de (2b)/(4b) está ancorado à revisão corrigida. Pelas regras de D-27(c) e D-25(a), a ADR vai à classe F, porque sobra só redação. Os resíduos F1 e F2 são obrigatórios. O Governance os confere, e a ADR não volta ao Arquiteto Principal. | **"Pronta para o comitê" já agora.** Rejeitado: o comitê leria na célula Família um conjunto de famílias diferente do que está no risco residual, e na §6 um gateway numa posição que a §4.1 nega. Isso é a contradição que D-25(a) não deixa ir ao comitê.<br>**Devolver como S (8ª rodada).** Rejeitado: o conteúdo de F1 e F2 já está decidido e confirmado. Uma rodada inteira por transcrição contraria D-25(a) e a proteção ao autor de D-25(b). | O interino passa a estar descrito de forma consistente quanto ao **destino** (Axway existente, inteiro no CDE), ao **perímetro** (só a borda L3/L4 fora; segmento da DMZ que hospeda o Axway no escopo ou *connected-to*) e aos sistemas ***connected-to*** (famílias 1 e 12). Depois de F1/F2, a célula Família fica igual ao risco residual. Lacuna mantida: o Escritório não validou com QSA (Q8). | Governance (registro §13) |
| (b) | **F1 e F2 não abrem nova confirmação restrita** | F1 transcreve na célula Família as famílias que o Security Architect confirmou em [ARC-221](/ARC/issues/ARC-221). F2 alinha a §6 à §4.1 e à Q10, já respondidas pelo Cloud Architect ([ARC-79](/ARC/issues/ARC-79)). Nenhuma das duas muda cláusula, leitura de escopo ou família. Pela D-25(e), não fica confirmação ancorada pendente sobre cláusula tocada. O Governance confere que o texto aplicado se limita a isso. Se o autor for além da transcrição, a confirmação volta a ser exigida. | **Pedir ao Security Architect que confirme F1/F2.** Rejeitado: seria confirmar de novo um conteúdo confirmado há uma revisão, e a confirmação restrita existe para mudança de leitura, não para cópia. | — | Governance (registro §13; checklist G11) |

**Pedido ao Governance Architect:** indexar D-28 no registro §13, junto com D-25 a D-27. Não há precedente novo para o checklist §4.

## 3. Travas de AR e risco que o comitê precisa conhecer

- `AR-001` e `AR-002` continuam presas por `ADR-0010` até o **envio** dela, que vem depois da conferência F (D-24(k), D-25(d)). Continuam presas também por `ADR-0003` até o envio dela.
- **Caminho de cartão no interino (`ADR-0010`):** depois de F1/F2, o comitê vai receber um interino descrito de forma consistente. As lacunas continuam declaradas como lacunas, sem virar controle:
  - as quatro lacunas do `DC-AA` em volta do Axway existente (§6, Q11, [ARC-182](/ARC/issues/ARC-182));
  - o inventário dos sistemas *connected-to*, a cargo do Security Architect;
  - a validação com QSA (Q8);
  - a decisão entre as alternativas (a) e (b) da Q11 sobre `ADR-0014` C5.

  Famílias 1, 4, 6, 10 e 12.

## 4. Próximos passos

1. Technical: [ARC-222](/ARC/issues/ARC-222), resíduos F1 e F2 de `ADR-0010`, em linha nova do changelog.
2. Governance: [ARC-223](/ARC/issues/ARC-223), conferir F1/F2 contra a §1 deste documento e D-28(b), enviar `ADR-0010` ao comitê quando cumprir D-25(e) e indexar D-28 no registro §13. A task está bloqueada pela ARC-222.
3. Este gate não tem 8ª rodada para `ADR-0010`.

Nada aqui é aprovação; o comitê de arquitetura do banco aprova.
