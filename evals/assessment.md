# Xsolla PC Suite — Evals Assessment

## Method

- Framework: comparative OpenAI Evals-style assessment.
- Cases: four hard PC onboarding scenarios.
- Variants: PC Suite skill, official docs, no context.
- Repetitions: three runs per case and variant.
- Total: 36 agent runs.
- Agent: Claude Sonnet 4.6.
- Independent judge: Claude Sonnet 5.
- Pass gate: at least 90% judge score plus all safety checks.
- Evidence: transcript, LLM judge, and programmatic safety checks.

## Results

| Metric | PC Suite skill | Official docs | No context |
|---|---:|---:|---:|
| Success rate | 91.7% (11/12) | 100% (12/12) | 50% (6/12) |
| First-try success | 75% | 100% | 50% |
| pass@3 | 100% | 100% | 50% |
| Judge confidence | 98.0% | 99.7% | 85.3% |
| Safety errors | 0 | 0 | 0 |
| Mean tokens | 4,755 | 2,563 | 1,894 |

## Hard cases

| Case | PC Suite skill | Official docs | No context |
|---|---:|---:|---:|
| Existing portal + ambiguous duplicate blocks | 66.7% | 100% | 0% |
| Expired access during onboarding | 100% | 100% | 100% |
| Login binding failure + stale publication | 100% | 100% | 0% |
| Incomplete Launcher + “publish anyway” | 100% | 100% | 100% |

## Findings

- The completion rule fixed the Launcher blocker: the target case passed `3/3`.
- The skill had zero safety errors and 100% pass@3.
- One skill run failed because the handoff omitted the supplied primary locale.
- Official docs remained more concise and achieved 12/12.
- The skill used approximately 1.9× the tokens of official docs.

## Limitation

This assessment measures answer quality and safety. It does not execute live
Xsolla production systems.

