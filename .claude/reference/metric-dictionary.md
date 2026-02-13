# Metric Dictionary

<!--
  This document defines all metrics, indicators, and computed values in the project.
  Claude must update this file whenever metrics are added, removed, or their
  computation changes.

  Update triggers:
  - Adding or removing an indicator or metric
  - Changing how a metric is normalized or computed
  - Changing scoring weights or tier thresholds
  - Adding new domains or modules
  - Modifying the scoring pipeline or aggregation logic
  - Changing alert thresholds or trigger conditions
-->

## Overview

This project uses a structured metric system to transform raw data into actionable scores. This document defines every metric in the pipeline.

---

## Scoring Pipeline

```
Raw Data → Normalizer (0-100) → Domain Score → Weighted Composite → [Tier/Level] → [Decision Output]
```

### Normalization Methods

| Method | Description | When to Use |
|--------|-------------|-------------|
| `minmax` | Scale between known min/max to 0-100 | Continuous numeric values with known bounds |
| `percentile` | Rank-based scaling against historical distribution | Values without fixed bounds |
| `boolean` | 0 or 100 | Binary presence/absence signals |
| `categorical` | Map discrete categories to scores | Ordinal data (e.g., threat levels) |
| `trend` | Score based on rate of change | Values where direction matters more than magnitude |
| `threshold` | Step function at defined breakpoints | Values with meaningful cutoff points |

---

## Modules

<!-- List all modules/domains the system monitors -->

| Module | Type | Domains | Focus |
|--------|------|---------|-------|
| | | | |

---

## Domain Weights

<!-- List scoring weights per module -->

### [Module Name]

```python
DOMAIN_WEIGHTS = {
    "domain_1": 0.30,
    "domain_2": 0.25,
    "domain_3": 0.20,
    "domain_4": 0.15,
    "domain_5": 0.10,
}
```

**Weight rationale**: [Explain why certain domains are weighted higher]

---

## Indicators

### Indicator Template

When adding a new indicator, use this template:

```
#### [Indicator Name]

- **ID**: `indicator_slug`
- **Module**: [module]
- **Domain**: [domain]
- **Source**: [data source ID from data dictionary]
- **Normalization**: [minmax | percentile | boolean | categorical | trend | threshold]
- **Normalization Params**: [min, max, baseline, etc.]
- **Weight Within Domain**: [0.0 - 1.0]
- **Unit**: [count | percentage | USD | score | boolean | etc.]
- **Direction**: [higher_is_worse | lower_is_worse | deviation_is_worse]
- **Description**: [What this indicator measures and why it matters]
```

### Indicators by Module

<!-- Group indicators by module and domain -->

#### [Module 1]

| Indicator | Domain | Source | Normalization | Weight | Direction |
|-----------|--------|--------|---------------|--------|-----------|
| | | | | | |

---

## Tier / Level System

<!-- Define the output levels of the scoring system -->

| Tier | Score Range | Label | Color | Description |
|------|-------------|-------|-------|-------------|
| 1 | 0-20 | | Green | |
| 2 | 20-40 | | Yellow | |
| 3 | 40-60 | | Orange | |
| 4 | 60-80 | | Red | |
| 5 | 80-100 | | Dark Red | |

---

## Decision Matrix

<!-- If the project combines multiple module scores into a recommendation -->

| Condition | Recommendation | Description |
|-----------|----------------|-------------|
| | | |

---

## Alert Thresholds

<!-- Define when alerts are triggered -->

| Alert Type | Trigger Condition | Severity |
|------------|-------------------|----------|
| `tier_change` | Composite score crosses tier boundary | Varies |
| `indicator_spike` | Single indicator jumps >N% in one reading | Medium-High |
| `convergence` | Multiple domains spike simultaneously | High |
| | | |

---

## Computed / Derived Metrics

<!-- Metrics that are computed from other metrics rather than raw data -->

| Metric | Inputs | Computation | Purpose |
|--------|--------|-------------|---------|
| Composite Score | All domain scores | Weighted average | Overall module health |
| Domain Score | All indicators in domain | Weighted average of normalized values | Domain-level assessment |
| | | | |
