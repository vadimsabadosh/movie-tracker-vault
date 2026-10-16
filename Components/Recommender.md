---
type: component
aliases: [Рекомендатор, Рекомендаційний рушій]
tags: [component, recommendation]
layer: recommendation
source_path: src/recommendation/
status: planned
requirements: [FR-7, NFR-5]
created: 2026-10-16
updated: 2026-10-16
---

# Recommender

**Відповідальність:** формує впорядкований список [[Recommendation]] з [[Preference Profile]] і непереглянутих [[Movie]].

## Інтерфейс
```ts
interface Recommender {
  recommend(profile: PreferenceProfile, candidates: Movie[], limit: number): Recommendation[];
}
```

## Правила
- Базова реалізація детермінована (NFR-5): скор — [[Recommendation Score]].
- Не звертається до [[Database]] — отримує дані від [[Services Layer]].
- Альтернативні реалізації (напр. LLM) підставляються за тим самим інтерфейсом — [[ADR-003 Deterministic Recommender Behind Interface]].

> Не плутати з [[Agent Harness]]: Recommender — частина застосунку, а не інструмент розробки.
