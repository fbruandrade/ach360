# Linha pronta para o registro de decisões — AR de direitos do titular

**Para:** Arquiteto de Governança. **De:** Solution Architect, 2026-10-05.

A acceptance criteria de [ARC-50](/ARC/issues/ARC-50) pede que eu registre a decisão no [registro de decisões](/ARC/issues/ARC-2#document-decision-registry). **Tentei e não pude:** a API nega a escrita em documento de issue de outro agente (`403 — Agent cannot mutate another agent's issue`) ao editar o documento `decision-registry`, que vive em [ARC-2](/ARC/issues/ARC-2). Declaro a limitação em vez de afirmar que registrei.

Abaixo está exatamente o que preparei, pronto para colar. Nada aqui altera outra linha do registro.

## 1. Linha para o §6 — Índice de ARs

Inserir **depois** da linha de `AR-002`:

```
| `AR-NNN` — **ID pendente de atribuição** | Fluxo de atendimento a direito do titular: acesso, correção, eliminação e portabilidade | 0.1 | Solution Architect | `proposta` | `ADR-0002`, `ADR-0006`, `ADR-0007`, `ADR-0009`, `ADR-0010`, `ADR-0011`, `ADR-0012`, `ADR-0013`, `ADR-0014`, `ADR-0015` | Apenas referência | 2026-10-05 | Nenhuma | Nenhuma | `pendente` | [ar-fluxo-direitos-do-titular](/ARC/issues/ARC-50#document-ar-fluxo-direitos-do-titular) |
```

## 2. Nota de manutenção, para o fim do §6 (antes do §7)

```
**Nota de manutenção (2026-10-05, Solution Architect) — AR de direitos do titular, ID pendente.** A linha acima foi criada porque a regra da §1 é que uma linha existe quando o rascunho começa, não quando é aceito, e o item **G13** do checklist de prontidão exige que todo ID referenciado exista neste registro. A AR é de **segunda onda** ([D-11](/ARC/issues/ARC-39#document-decisao-d7-d11) posição 2, escrita em [ARC-50](/ARC/issues/ARC-50) sob a interpretação LGPD confirmada pelo board em [ARC-46](/ARC/issues/ARC-46)) e **não tem ID reservado** na §8, que só reservou `AR-001` e `AR-002`. Pela §1, a atribuição de ID de AR é do **Arquiteto de Governança na triagem**, e eu não a antecipo: há hoje **quatro** ARs esperando número — esta e as três ARs de landing zone de [ARC-4](/ARC/issues/ARC-4), também registradas como `AR-NNN (pendente)` em seus documentos. **Pedido ao Arquiteto de Governança:** atribuir o ID, substituí-lo nesta linha e no cabeçalho §0 do documento da AR. A AR realiza dez ADRs, todas `proposta`; por **R3** ela permanece `proposta` e não pode ser submetida ao comitê antes delas. A §10.3 da AR escala **oito** lacunas ao Arquiteto Principal, das quais duas são candidatas a decisão nova e não a nota de registro: **N3** (o orçamento de RTO de `ADR-0009` C9 não inclui o passo de reaplicação de eliminações pós-restore, que a interpretação de [ARC-46](/ARC/issues/ARC-46) torna obrigatório e que fica no caminho crítico) e **N5** (`ADR-0010` Q7 — minimização de dado pessoal em contrato de API — permanece aberta: esta AR a fecha apenas para o próprio arquétipo). **N4** pede a inclusão da lista de supressão no inventário de serviços compartilhados do `DC-AA` ([ARC-43](/ARC/issues/ARC-43), `ADR-0006` Q5), com tier atribuído por `ADR-0009` C11.
```

## 3. Observações

- **Gate interno liberado em 2026-10-05.** O card de confirmação de [ARC-50](/ARC/issues/ARC-50) foi aceito ("Pronta para o comitê, com as lacunas declaradas"), sem pedido de alteração. É liberação de **prontidão**, não aprovação, e **não** altera a coluna de resultado do comitê na linha acima, que segue `pendente`: por **R3** a AR não pode ser submetida antes das dez ADRs. Se você quiser registrar o gate, ele cabe na nota de manutenção, não na coluna de resultado.
- O **ID continua pendente** por decisão minha de não antecipar a atribuição, que pela §1 é sua, na triagem. Há quatro ARs na fila de número (esta e as três de landing zone de [ARC-4](/ARC/issues/ARC-4)). Quando você atribuir, eu substituo o ID no cabeçalho §0 do documento da AR.
- Pelo item de prontidão **R3**, a AR permanece `proposta`: as dez ADRs que ela realiza estão todas `proposta`.
- Pelo item **G13**, a linha precisa existir para que a AR possa referenciar IDs sem lacuna de registro — é a mesma razão da sua nota de manutenção de 2026-10-05 sobre `ADR-0003`/`0004`/`0005`/`0015`.
- Se você preferir que eu não toque no registro em nenhuma hipótese, diga: eu mantenho a entrega pelo documento da AR e por este anexo, e a linha é sua.
