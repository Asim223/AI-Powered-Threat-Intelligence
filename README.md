# ThrearFusion-AI : AI-Powered-Threat-Intelligence-

# ThreatFusion AI — Capstone Report (AICS-107)

This repository contains the capstone report submitted for **AICS-107: AI-Powered Threat Intelligence (Advanced)**, part of the ICDFA AI-Driven Cybersecurity & Digital Forensics Fellowship.

`TLP:CLEAR` · Defensive training exercise · synthetic data only

## Overview

ThreatFusion AI is a defensive cyber threat intelligence (CTI) fusion pipeline. It ingests CTI reporting, extracts indicators of compromise, proposes MITRE ATT&CK technique mappings with a supervised classifier, assigns a risk score, assembles a knowledge graph, exports a STIX 2.1 bundle, and writes analyst-ready reports.

This report documents a complete end-to-end execution over a 60-report synthetic training corpus, run on 17 July 2026 in Google Colab (Python 3.12.13, CPU runtime). Rather than presenting the pipeline's headline metrics at face value, the report's central contribution is an **honest, adversarial evaluation** of its own AI classifier — showing that a reported 0.90 macro-F1 score is a measurement artefact, not evidence of real capability, and tracing that finding through every downstream product it touches.

## The headline finding

The ATT&CK classifier reports a macro F1 of **0.9000** on the shipped evaluation split. Three independent diagnostics show this number is an illusion:

1. **Template identification, not technique mapping.** The 60-report corpus contains only five distinct label combinations, each fixed by the report template that produced it — so the model only has to recognise which of five templates it's looking at.
2. **Label leakage via metadata.** The feature text includes the report title, which embeds the template name. The model's top-weighted tokens for several techniques are literal filename fragments (e.g. `'bravo defensive'`), not threat-behaviour language.
3. **Collapse under honest evaluation.** Holding out an entire template family drops micro-F1 from 0.900 to a mean of **0.273**. On five plainly-worded analyst sentences with no template keywords, the model scored **0 out of 5**.

By contrast, a zero-shot embedding model (`all-MiniLM-L6-v2`, never trained on this corpus) correctly ranked a valid technique first on all five sampled cases — outperforming the trained classifier because it has nothing to memorise. That contrast is the report's central piece of evidence: the classifier learned filenames, not tradecraft.

## What the report covers

| Section | Focus |
|---|---|
| 1–2 | Executive summary and Priority Intelligence Requirements (PIRs) — what questions the pipeline can and cannot answer |
| 3 | Data sources and handling — synthetic, RFC-reserved indicators only; no live feeds queried |
| 4 | System architecture — five-stage pipeline (ingest → model → enrich → fuse → report), Colab environment setup |
| 5 | AI model design — task formulation, TF-IDF + one-vs-rest logistic regression, and why each design decision was later shown to be wrong |
| 6 | **Evaluation results** — six diagnostics dismantling the reported 0.90 score, including leave-one-template-out testing and a novel-phrasing probe |
| 7 | ATT&CK mapping — technique frequency, co-occurrence structure, and a documented case of a risk-scoring patch that silently failed to apply |
| 8 | Knowledge graph and campaign analysis — why a "one cluster of 60 cases" result is a graph artefact, not evidence of a shared threat actor |
| 9 | STIX/MISP/OpenCTI sharing — a STIX bundle that is structurally sound but unshareable (duplicated objects, zero relationships) |
| 10 | Analyst reports — five generated reports, with an honest assessment of their remaining defects |
| 11 | Defensive recommendations — a five-ticket detection engineering backlog built from **analyst-validated labels, not model output** |
| 12 | Limitations and future work — 13 named limitations, and a case for zero-shot semantic matching over supervised training on small corpora |
| Appendix A | Evidence portfolio index — submission checklist, open items, full case ledger, figure index |

## Key results at a glance

| Dimension | Result |
|---|---|
| Cases processed | 60 (5 template families) |
| IOCs extracted | 72 instances / 15 unique — all RFC 5737 / reserved-namespace, non-resolvable |
| ATT&CK techniques modelled | 10, all evidenced in the corpus |
| Reported macro F1 | 0.9000 — shown to be an artefact (§6) |
| Honest generalisation (leave-one-template-out) | 0.273 mean micro-F1; 0/5 on novel phrasing |
| Knowledge graph | 85 nodes · 302 edges |
| Campaign clusters | 1 cluster of 60 cases — rejected as a graph artefact, no campaign declared |
| STIX bundle | 362 objects · 85 distinct IDs · 0 relationships — not shareable |
| Analyst reports | 5 generated (requirement: ≥3) |
| Detection backlog | 5 DRAFT tickets, built from validated labels only |

## Safety and scope

Every indicator in this report is synthetic by construction — IPv4 addresses from RFC 5737 documentation ranges, domains from reserved namespaces (`.invalid`, `.test`, `example.com/.net/.org`), and file hashes as repeated-digit placeholders. None resolve and none are hostile. No URL or domain was browsed, no sample was retrieved, and no live threat feed was queried. Every ATT&CK mapping produced by the classifier is explicitly treated as a suggestion requiring analyst validation — a constraint the report enforces structurally (the detection backlog is built from validated labels, not model predictions) rather than merely stating.


## License / Academic notice

This report was produced as an academic capstone submission for a defensive training exercise. All data referenced is synthetic; no real network, organizational, or threat-actor data was used. Shared here for academic and research purposes only.
