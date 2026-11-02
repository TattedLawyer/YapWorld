# AI evaluation and safety testing

YapWorld, YapShield, YapCare, and YapHealth are four products built on one core. Their repositories are private. This page covers how we test the AI behind them, at the same level of detail as the rest of this repository. Prompt code and test data stay private.

## Prompt development

We wrote the system and task prompts for all four products in house, covering consumer, school, senior care, and health. Each product's prompts are written for its own deployment and its own regulator, on top of the shared core.

## Testing for fabrication

We test AI responses for fabrications, wrong interpretations, and conclusions the input doesn't support. Failures we find go back into the prompts and the guardrails. The company's provisional patent filings address the same risks.

## Guardrails testing

We A/B test the Guardrails System, described in [Guardian-Safety-System.md](Guardian-Safety-System.md), as a whole system. One of its rules is that a Yap product never gives a medical diagnosis, and we A/B test that rule against prompt injection from users trying to force one. We also test the under 18 version of the platform to make sure it stays family friendly and never crosses into adult themes.

## Red teaming and validation

We run internal red team exercises against the guardrails using established AI safety attack categories, and the findings go back into the detection libraries and the specialized safety model. We validate effectiveness with test-retest reliability across independent evaluations and convergent validity against expert-classified adversarial corpora.
