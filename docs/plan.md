# Plan — Stand up the Arch360 Architecture Office

## Goal

Build an agent team that runs the Arch360 Architecture Office for a Brazilian bank, and
produce the first wave of architecture artifacts — ADRs, Architecture References (ARs),
and the governance scaffolding around them — drafted to a state where the architecture
guild committee can review and approve them.

## What changed in this revision

You asked me to hire **Cloud, Infrastructure, and Governance architects too**. The team is
now **eight agents** instead of five, and I re-cut the role boundaries so eight people
don't write the same document three times. Two consequences worth naming up front:

- **The Technical Architect's scope narrowed.** It used to absorb cloud and infrastructure.
  Now it owns the runtime and integration layer only; Cloud owns the three clouds, and
  Infrastructure owns the on-prem active-active estate.
- **Eight agents is a real team and costs more to run than five.** I've kept the Principal
  Architect as the single funnel into the guild committee so parallel work converges
  instead of multiplying. You can still uncheck any hire on the card.

## Scope

**In scope**

- Hiring the architecture team (eight roles below).
- The artifact system itself: ADR template, AR template, decision registry, intake and
  approval flow into the guild committee.
- A first wave of real ADRs and ARs grounded in the bank's current landscape, covering
  infrastructure, cloud, security, architecture, and governance.

**Out of scope for now**

- Implementing or changing anything in the bank's runtime environment.
- Final approval itself — that stays with the architecture guild committee (human).
- Tool/library development work Arch360 sometimes takes on; we can add that later.

## Landscape the decisions must account for

As you described it:

- Active-active data center, on-premises.
- OpenShift on-prem; Kubernetes across all three clouds.
- Multi-cloud: Azure, OCI, AWS.
- VMs alongside containers.
- Relational and non-relational databases.
- Mixed API gateways: AWS API Gateway, Azure APIM, Axway API Gateway.

Every ADR and AR below is written against this reality, not a greenfield one.

## Proposed team

Eight agents, each owning a distinct altitude or domain. All report to me (Arch Master);
I stay your single point of contact.

| Agent | Owns | Boundary — what it does *not* own |
| --- | --- | --- |
| **Principal Architect** | Runs the Office. Sets the ADR/AR standard, prioritizes the backlog, resolves disagreements between the domain architects, and is the last internal quality gate before anything reaches the guild committee. | Doesn't author domain content; reviews it. |
| **Enterprise Architect** | Cross-bank view: capability and domain maps, target-state architecture, the workload placement *criteria*, bank-wide standards. | Doesn't pick per-cloud services or per-DC infrastructure. |
| **Solution Architect** | Per-initiative solution designs and HLDs; integration patterns spanning on-prem OpenShift and the three clouds. | Doesn't set platform standards; consumes them. |
| **Technical Architect** | Runtime and integration depth: OpenShift/Kubernetes workload patterns, the API gateway mix, application data access, NFRs and resilience patterns at the service level. | Doesn't own cloud accounts/landing zones or physical/DC infrastructure. |
| **Cloud Architect** _(new, your request)_ | Azure, OCI, AWS: landing zones, subscription/tenancy/account structure, per-cloud managed service selection (including managed Kubernetes), cloud networking and connectivity, cloud resilience and cost guardrails. | Doesn't own on-prem; doesn't own security policy (partners with Security). |
| **Infrastructure Architect** _(new, your request)_ | On-prem estate: the active-active data center topology, network, storage, compute and VM standards, hybrid connectivity to the three clouds, DR and capacity. | Doesn't own cloud-native service choices. |
| **Security Architect** _(my recommendation from revision 1)_ | Identity and access, secrets and key management, cryptography, network segmentation and zero-trust posture, and the security side of regulatory requirements (BACEN/CMN cyber and cloud-outsourcing rules, LGPD). | Doesn't own the governance *process* — that's the Governance Architect. |
| **Governance Architect** _(new, your request)_ | The operating model: ADR/AR lifecycle, decision registry and supersession rules, guild committee intake and readiness criteria, standards conformance, and the exception/waiver path when a team can't comply. Maps regulatory obligations into architecture controls. | Doesn't author technical content; governs how it's produced and approved. |

Two boundaries I want to be explicit about, because they're where eight agents could
collide:

- **Cloud vs. Infrastructure** splits at the data center boundary. Hybrid connectivity is
  co-authored, with Infrastructure holding the pen.
- **Security vs. Governance** splits content from process. Security says what the control
  must be; Governance says how the decision gets recorded, reviewed, and enforced.

## Steps

**Phase 1 — Hire and seat the team**

1. Draft each agent's role, reporting line, and configuration.
2. Submit the hire requests. Hiring may route through a board approval card; I'll link
   each one here as it goes out.
3. Once seated, each architect gets their own Paperclip tasks, so you can watch the work
   per-agent rather than as one opaque blob.

**Phase 2 — Artifact foundation (before any ADR is written)**

4. ADR template and AR template, in the format your guild committee expects.
5. Decision registry: the index of every ADR with status (proposed / accepted /
   superseded / deprecated) and its supersession chain.
6. Office operating model: how a question becomes an ADR, who reviews at which stage, what
   "ready for committee" means as a checklist, and the exception/waiver path.

Phase 2 is now owned by the **Governance Architect**, with the Principal Architect
approving. In revision 1 this work had no clear owner.

**Phase 3 — First wave of ADRs and ARs**

Candidate backlog, grouped by the domains your definition of done names, with the owning
architect. The team will confirm and reprioritize this with you before drafting.

_Cloud_

- Workload placement decision framework: what belongs in the active-active DC vs. Azure
  vs. OCI vs. AWS, and on what criteria. _(Enterprise + Cloud)_
- Landing zone standard per cloud, with the guardrails that apply to each. _(Cloud)_
- Managed Kubernetes per cloud vs. OpenShift: where each is the right answer. _(Cloud + Technical)_
- Cloud cost and capacity guardrails. _(Cloud)_

_Infrastructure_

- Active-active data center topology and failure-domain model. _(Infrastructure)_
- Hybrid connectivity: DC to Azure, OCI, and AWS. _(Infrastructure + Cloud)_
- VM vs. container placement standard, and the compute baseline for each. _(Infrastructure + Technical)_
- Resilience and DR tiering: what RTO/RPO class each workload tier gets. _(Infrastructure + Technical)_

_Architecture_

- API gateway strategy: which of AWS API Gateway, Azure APIM, and Axway handles which
  exposure pattern (external north-south, internal east-west, partner, in-cloud). _(Technical)_
- Persistence selection guardrails: relational vs. non-relational, and the data strategy
  for active-active. _(Technical + Infrastructure)_

_Security_

- Identity, secrets, and key management across on-prem and three clouds. _(Security)_
- Network segmentation and trust zones across the hybrid estate. _(Security + Infrastructure)_

_Governance_

- ADR lifecycle and guild committee intake process. _(Governance)_
- Architecture standards conformance and the exception/waiver process. _(Governance)_
- Regulatory obligation → architecture control mapping. _(Governance + Security)_

_Architecture References (ARs)_

- Externally exposed API across the gateway mix. _(Solution + Technical)_
- Resilient active-active transactional service. _(Solution + Infrastructure)_
- Cloud landing zone reference, one per cloud. _(Cloud)_

**Phase 4 — Hand off for approval**

Each artifact ends as a Paperclip document, checked by the Governance Architect against
the readiness checklist, reviewed by the Principal Architect on substance, then surfaced
to you for the guild committee.

## Definition of done

Using your wording: the artifacts are drafted and prepared for approval, covering
infrastructure, cloud, security, architecture, and governance — finalized to the point of
being ready for final human review and approval by the architecture guild committee.

Concretely, for this plan:

- The team is hired and seated.
- ADR template, AR template, and decision registry exist.
- Each Phase 3 artifact exists as a document, passes the readiness check, and is
  explicitly marked ready for committee.
- Nothing claims to be "approved" — approval is the committee's, not ours.

## Decisions on the record

This section records answers as they arrive. It changes no scope and needs no re-approval —
revision 2 remains the accepted plan.

1. **Artifact language — decided 2026-10-04: Brazilian Portuguese (pt-BR).** Prose,
   headings, field names and status vocabulary in pt-BR (*Contexto*, *Decisão*,
   *Consequências*, *Alternativas consideradas*; status *proposta / aceita / substituída /
   descontinuada*). Established English technical terms stay in English — `active-active`,
   `landing zone`, `API gateway`, `OpenShift`, `Kubernetes`, `RTO/RPO`, `zero trust` —
   because inventing Portuguese equivalents would make the artifacts harder to read for the
   engineers who depend on them, not easier. ADR/AR identifiers and metadata keys stay
   language-neutral so the registry and tooling do not depend on prose. **Verified in place:**
   all nine task descriptions (ARC-2 … ARC-10) carry the pt-BR requirement. **Correction:** an
   earlier update in this thread claimed the eight architects' standing instructions
   (system prompts) also carry this directive — that was checked this revision and is **not**
   true; Arch Master does not hold the `agents:configure` grant needed to edit agent system
   prompts and the write never went through. The task descriptions are the durable, verified
   carrier of this decision; each architect's task tells it to work in pt-BR regardless of
   what its standing instructions say.

2. **Reporting line — decided 2026-10-04: restructure.** The seven domain architects
   (Enterprise, Solution, Technical, Cloud, Infrastructure, Security, Governance) will report
   to the **Principal Architect** instead of Arch Master, matching how the Principal's role is
   written. Arch Master stays the board's single point of contact either way. **Blocked on
   execution:** changing an agent's `reportsTo` requires the `agents:configure` (or
   `agents:suggest-changes`) permission grant, which Arch Master does not hold — the API call
   returns `403 Missing permission: agents:configure or agents:suggest-changes`. This needs
   either (a) a board member applying the change directly in each agent's settings page
   (`reportsTo` → Principal Architect, id `d30887a2-bcb9-4afd-b5aa-604081c57863`), or (b)
   granting Arch Master that permission so this run can apply it via the API. Flagged on the
   source task; see the pending question there.

3. **Regulatory scope — decided 2026-10-04: add PCI-DSS.** Alongside BACEN/CMN cyber and
   cloud-outsourcing requirements and LGPD, the bank is in scope for **PCI-DSS**. Any artifact
   touching cardholder data — storage, transmission, or a connected system — must state how
   the decision affects PCI-DSS scope and which requirement family it bears on. **Verified in
   place:** all nine task descriptions carry this baseline; ARC-8 (the regulatory
   obligation-to-control mapping) now has a named PCI-DSS section with its own CDE scope
   boundary and gap list. Same caveat as above applies to agent system prompts — not updated,
   same permission gap — but the task descriptions are authoritative here.

4. **Escalation and decision authority — decided 2026-10-05: delegated to the Principal
   Architect.** Any process that would otherwise stop waiting on Arch Master or on user
   approval, the **Principal Architect** now has authority to resolve directly — without
   escalating back. This formalizes what the Principal Architect was already doing in
   practice (ARC-39, ARC-46, ARC-51: deciding between domain architects, closing gaps
   D-7 through D-16) and extends it to any future impasse. Only genuinely human-only
   matters still escalate: external hiring/procurement, regulatory interpretation with no
   recorded precedent, and final guild committee approval (never ours to give regardless
   of delegation).

   **Supersedes item 2's open status.** The technical `reportsTo` field for the seven
   domain architects remains blocked — `agents:configure` still isn't granted to Arch
   Master or the Principal Architect, confirmed unchanged again on this date — but that
   field is now housekeeping, not a blocker: the practical decision authority it was meant
   to confer is already granted by this item. No further repeated reporting on the
   permission gap is needed; apply the field opportunistically if the grant ever lands.

Items 1 and 3 remain verified in place. No item is open pending Arch Master/user action.

## Approval gate

Nothing in this plan executes until you accept it. No agent gets hired, no task gets
created. Use the confirmation card to pick which hires and phases you want — if you check
a subset, I do only that subset.

