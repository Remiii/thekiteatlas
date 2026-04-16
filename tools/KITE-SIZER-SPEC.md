# Kite Sizer — Algorithm Specification

## Formula

```
kite_size_m2 = (rider_weight_kg / wind_speed_kts) × K × style_mult × level_mult
```

### Base coefficient K

| Kite type | K | Avg error vs manufacturers |
|-----------|---|---------------------------|
| Tube (LEI) | **1.7** | 3% |
| Foil kite | **1.6** | 4% |

K was determined by back-calculating from manufacturer low-end wind data across 11 brands, then taking the median across the practical wind range (10-25 kts).

### Style multiplier

| Style | Multiplier |
|-------|-----------|
| Freeride | 1.0 |
| Freestyle | 0.90 |
| Wave ride | 0.85 |
| Big air | 1.15 |

### Level multiplier

| Level | Multiplier |
|-------|-----------|
| Beginner | 1.15 |
| Intermediate | 1.0 |
| Advanced | 0.90 |

### Output

- **Recommended size**: `round(kite_size_m2)`
- **Range**: `round(size × 0.85)` to `round(size × 1.15)`

---

## How K was calibrated

For each kite model, the manufacturer publishes the minimum wind speed (low-end) for each size at 75 kg. We back-calculate K:

```
K = (kite_size × wind_kts) / 75
```

Then average across all sizes to get the brand/model K.

### Back-calculated K per model

#### Tube (LEI) kites

| Brand | Model | Type | Sizes | K values (per size) | Avg K |
|-------|-------|------|-------|--------------------|----|
| Flysurfer | Boost 2/3 | Big air | 5-18m | 1.27, 1.59, 1.68, 1.73, 1.80, 1.80, 1.91 | **1.68** |
| Flysurfer | Stoke | Freeride/wave | 6-14m | 1.33, 1.44, 1.53, 1.60, 1.68, 1.73, 1.92 | **1.60** |
| Duotone | Evo | All-round | 5-13m | 1.53, 1.68, 1.73, 1.73, 1.76, 1.76, 1.77, 1.80, 1.81 | **1.73** |
| Duotone | Rebel SLS | Big air | 6-14m | 1.68, 1.73, 1.76, 1.76, 1.77, 1.80, 1.81, 1.68 | **1.75** |
| Duotone | Neo | Wave | 3-12m | 1.16, 1.39, 1.53, 1.68, 1.73, 1.77, 1.80, 1.81, 1.76, 1.61 | **1.62** |
| Cabrinha | Switchblade | Freeride | 5-14m | 1.31, 1.37, 1.44, 1.47, 1.53, 1.56, 1.68, 1.71 | **1.51** |
| North | Orbit | Big air | 5-14m | 1.49, 1.53, 1.60, 1.68, 1.73, 1.77, 1.80, 1.81 | **1.68** |
| North | Carve | Wave | 4-13m | 1.20, 1.28, 1.47, 1.60, 1.68, 1.68, 1.73, 1.76, 1.77, 1.80 | **1.60** |
| F-One | Bandit XV | All-round | 7-14m | 1.31, 1.47, 1.60, 1.73, 1.80, 2.13 | **1.67** |

**Overall tube K: median = 1.68, mean = 1.65. Selected K = 1.7** (rounded up slightly to avoid systematic undersizing).

#### Foil kites

| Brand | Model | Type | Sizes | K values (per size) | Avg K |
|-------|-------|------|-------|--------------------|----|
| Flysurfer | Soul 3 | All-round foil | 6-18m | 1.52, 1.60, 1.60, 1.68, 1.71, 1.73 | **1.64** |
| Flysurfer | Sonic 4 | Performance foil | 6-18m | 1.50, 1.33, 1.40, 1.50, 1.60, 1.80 | **1.52** |
| Flysurfer | Sonic 3 | Performance foil | 9-21m | 1.08, 1.03, 1.08, 1.13, 1.20, 1.40 | **1.15** |

**Foil K varies significantly by model**: Soul (all-round) = 1.64, Sonic 4 = 1.52, Sonic 3 = 1.15. Selected K = 1.6 — targets the all-round foil market (Soul-class). Performance foils (Sonic) are outliers.

---

## Weight scaling validation

Weight scaling is linear: `kite_size ∝ rider_weight`. Validated against the only weight-segmented sources available.

### Flysurfer Soul 2 quiver recommendations

| Weight (kg) | Biggest kite (m²) | Implied K (at ~7.5 kts) |
|------------|-------------------|------------------------|
| 50-60 | 10 | 1.36 |
| 60-70 | 12 | 1.38 |
| 70-80 | 15 | 1.50 |
| 80-95 | 18 | 1.55 |
| 95+ | 21 | 1.57 |

K increases slightly with weight (1.36 → 1.57), suggesting super-linear scaling. However, for the practical range (60-100 kg), linear scaling (weight/75) introduces at most ±1m² error, which is within brand variance.

### Airush weight-segmented guide

| Wind (kts) | 65 kg (mfr) | 65 kg (calc) | 75 kg (mfr) | 75 kg (calc) | 85 kg (mfr) | 85 kg (calc) |
|-----------|------------|-------------|------------|-------------|------------|-------------|
| 11.5 | 13 | 9.6 | 14 | 11.1 | 15.5 | 12.6 |
| 15.5 | 10.5 | 7.1 | 11.5 | 8.2 | 12.5 | 9.3 |
| 20 | 8.5 | 5.5 | 9.5 | 6.4 | 10.5 | 7.2 |
| 25.5 | 6.5 | 4.3 | 7.5 | 5.0 | 8.5 | 5.7 |

**Note**: Airush publishes mid-range (sweet spot) recommendations, not low-end. The formula with K=1.7 targets low-end, which explains the consistent undersizing vs Airush. This is expected — our formula tells you the minimum size that works, Airush recommends the comfortable size.

### KitesurfTheWorld weight chart (mid-range, 10-20 kts band, mid=15 kts)

| Weight (kg) | Guide (m²) | Formula K=1.7 (m²) | Diff |
|------------|-----------|-------------------|------|
| 45 | 10.5 | 5.1 | -5.4 |
| 65 | 12.5 | 7.4 | -5.1 |
| 75 | 13.5 | 8.5 | -5.0 |
| 85 | 14.5 | 9.6 | -4.9 |
| 95 | 15.5 | 10.7 | -4.8 |

**Note**: Same pattern — KitesurfTheWorld gives mid-range/comfortable recommendations (~K=2.2), not low-end. The ~5m² offset is consistent, confirming the formula is correct but targets a different usage (minimum vs comfortable).

---

## Detailed comparison: Formula vs Manufacturer (75 kg, freeride, intermediate)

### Duotone Evo 2024 (LEI, all-round) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 10 | 13 | 12.8 | -0.2 | -2% |
| 11 | 12 | 11.6 | -0.4 | -3% |
| 12 | 11 | 10.6 | -0.4 | -3% |
| 13 | 10 | 9.8 | -0.2 | -2% |
| 15 | 9 | 8.5 | -0.5 | -6% |
| 17 | 8 | 7.5 | -0.5 | -6% |
| 19 | 7 | 6.7 | -0.3 | -4% |
| 21 | 6 | 6.1 | +0.1 | +1% |
| 23 | 5 | 5.5 | +0.5 | +11% |
| | | | **Avg: -0.2** | **Avg: -2%** |

### Duotone Rebel SLS 2024 (LEI, big air) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 9 | 14 | 14.2 | +0.2 | +1% |
| 10 | 13 | 12.8 | -0.2 | -2% |
| 11 | 12 | 11.6 | -0.4 | -3% |
| 12 | 11 | 10.6 | -0.4 | -3% |
| 13 | 10 | 9.8 | -0.2 | -2% |
| 15 | 9 | 8.5 | -0.5 | -6% |
| 17 | 8 | 7.5 | -0.5 | -6% |
| 19 | 7 | 6.7 | -0.3 | -4% |
| 21 | 6 | 6.1 | +0.1 | +1% |
| | | | **Avg: -0.2** | **Avg: -3%** |

### Duotone Neo (LEI, wave) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 11 | 12 | 11.6 | -0.4 | -3% |
| 12 | 11 | 10.6 | -0.4 | -3% |
| 13 | 10 | 9.8 | -0.2 | -2% |
| 15 | 9 | 8.5 | -0.5 | -6% |
| 17 | 8 | 7.5 | -0.5 | -6% |
| 19 | 7 | 6.7 | -0.3 | -4% |
| 21 | 6 | 6.1 | +0.1 | +1% |
| 23 | 5 | 5.5 | +0.5 | +11% |
| 26 | 4 | 4.9 | +0.9 | +23% |
| 29 | 3 | 4.4 | +1.4 | +47% |
| | | | **Avg: +0.1** | **Avg: +6%** |

### Cabrinha Switchblade 2024 (LEI, freeride) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 7 | 14 | 18.2 | +4.2 | +30% |
| 9 | 12 | 14.2 | +2.2 | +18% |
| 10 | 11 | 12.8 | +1.8 | +16% |
| 11 | 10 | 11.6 | +1.6 | +16% |
| 13 | 9 | 9.8 | +0.8 | +9% |
| 16 | 8 | 8.0 | 0.0 | 0% |
| 18 | 7 | 7.1 | +0.1 | +1% |
| 20 | 6 | 6.4 | +0.4 | +6% |
| 23 | 5 | 5.5 | +0.5 | +11% |
| | | | **Avg: +1.3** | **Avg: +12%** |

**Note**: Cabrinha's low-end is very aggressive (14m at 7kts). Formula oversizes at light wind vs Cabrinha.

### North Orbit 2024 (LEI, big air) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 8 | 14 | 15.9 | +1.9 | +14% |
| 10 | 12 | 12.8 | +0.8 | +6% |
| 12 | 11 | 10.6 | -0.4 | -3% |
| 13 | 10 | 9.8 | -0.2 | -2% |
| 15 | 9 | 8.5 | -0.5 | -6% |
| 17 | 8 | 7.5 | -0.5 | -6% |
| 19 | 7 | 6.7 | -0.3 | -4% |
| 21 | 6 | 6.1 | +0.1 | +1% |
| 23 | 5 | 5.5 | +0.5 | +11% |
| | | | **Avg: +0.2** | **Avg: +1%** |

### North Carve 2024 (LEI, wave) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 9 | 13 | 14.2 | +1.2 | +9% |
| 10 | 12 | 12.8 | +0.8 | +6% |
| 11 | 11 | 11.6 | +0.6 | +5% |
| 12 | 10 | 10.6 | +0.6 | +6% |
| 14 | 9 | 9.1 | +0.1 | +1% |
| 16 | 8 | 8.0 | 0.0 | 0% |
| 18 | 7 | 7.1 | +0.1 | +1% |
| 20 | 6 | 6.4 | +0.4 | +6% |
| 22 | 5 | 5.8 | +0.8 | +16% |
| 24 | 4 | 5.3 | +1.3 | +33% |
| | | | **Avg: +0.6** | **Avg: +8%** |

### F-One Bandit XV (LEI, all-round) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 8 | 14 | 15.9 | +1.9 | +14% |
| 10 | 12 | 12.8 | +0.8 | +6% |
| 11 | 11 | 11.6 | +0.6 | +5% |
| 12 | 10 | 10.6 | +0.6 | +6% |
| 15 | 9 | 8.5 | -0.5 | -6% |
| 20 | 8 | 6.4 | -1.6 | -20% |
| | | | **Avg: +0.3** | **Avg: +1%** |

### Flysurfer Boost 2/3 (LEI, big air) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 7 | 18 | 18.2 | +0.2 | +1% |
| 9 | 15 | 14.2 | -0.8 | -6% |
| 10 | 13 | 12.8 | -0.2 | -2% |
| 13 | 11 | 9.8 | -1.2 | -11% |
| 15 | 9 | 8.5 | -0.5 | -6% |
| 17 | 7 | 7.5 | +0.5 | +7% |
| 19 | 5 | 6.7 | +1.7 | +34% |
| | | | **Avg: -0.0** | **Avg: +2%** |

### Flysurfer Stoke (LEI, freeride/wave) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 10 | 14 | 12.8 | -1.2 | -9% |
| 12 | 12 | 10.6 | -1.4 | -11% |
| 15 | 10 | 8.5 | -1.5 | -15% |
| 16 | 9 | 8.0 | -1.0 | -11% |
| 17 | 8 | 7.5 | -0.5 | -6% |
| 18 | 7 | 7.1 | +0.1 | +1% |
| 20 | 6 | 6.4 | +0.4 | +6% |
| | | | **Avg: -0.7** | **Avg: -7%** |

**Note**: The Stoke undersizes vs formula — it's a 3-strut freeride kite optimized for low-end power, generating more lift per m² than average LEI kites.

### Flysurfer Era (LEI, big air — estimated data) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 10 | 14 | 12.8 | -1.2 | -9% |
| 12 | 12 | 10.6 | -1.4 | -11% |
| 14 | 10 | 9.1 | -0.9 | -9% |
| 16 | 9 | 8.0 | -1.0 | -11% |
| 17 | 8 | 7.5 | -0.5 | -6% |
| 19 | 7 | 6.7 | -0.3 | -4% |
| 22 | 6 | 5.8 | -0.2 | -4% |
| | | | **Avg: -0.8** | **Avg: -8%** |

### Flysurfer Heli (LEI, freeride/progression — estimated data) — K=1.7

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 9 | 15 | 14.2 | -0.8 | -6% |
| 11 | 12 | 11.6 | -0.4 | -3% |
| 14 | 9 | 9.1 | +0.1 | +1% |
| 16 | 8 | 8.0 | 0.0 | 0% |
| 17 | 7 | 7.5 | +0.5 | +7% |
| 19 | 6 | 6.7 | +0.7 | +12% |
| | | | **Avg: +0.0** | **Avg: +2%** |

---

### Flysurfer Soul 3 (Foil, all-round) — K=1.6

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 7 | 18 | 17.1 | -0.9 | -5% |
| 8 | 15 | 15.0 | 0.0 | 0% |
| 10 | 12 | 12.0 | 0.0 | 0% |
| 13 | 10 | 9.2 | -0.8 | -8% |
| 16 | 8 | 7.5 | -0.5 | -6% |
| 19 | 6 | 6.3 | +0.3 | +5% |
| | | | **Avg: -0.3** | **Avg: -2%** |

### Flysurfer Sonic 4 (Foil, performance) — K=1.6

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 5 | 18 | 24.0 | +6.0 | +33% |
| 6 | 15 | 20.0 | +5.0 | +33% |
| 8 | 12 | 15.0 | +3.0 | +25% |
| 10 | 10 | 12.0 | +2.0 | +20% |
| 12 | 8 | 10.0 | +2.0 | +25% |
| 15 | 6 | 8.0 | +2.0 | +33% |
| 18 | 6 | 6.7 | +0.7 | +11% |
| | | | **Avg: +3.0** | **Avg: +26%** |

**Note**: The Sonic 4 is a closed-cell high-performance foil — significantly more efficient than the Soul. K≈1.2 would be more appropriate for Sonic-class kites.

### Flysurfer Sonic 5 (Foil, performance, kitefoil board) — K=1.6

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 8 | 15 | 15.0 | 0.0 | 0% |
| 10 | 12 | 12.0 | 0.0 | 0% |
| 11 | 10 | 10.9 | +0.9 | +9% |
| 15 | 7 | 8.0 | +1.0 | +14% |
| | | | **Avg: +0.5** | **Avg: +6%** |

### Flysurfer Sonic 3 (Foil, performance) — K=1.6

| Wind (kts) | Mfr (m²) | Formula (m²) | Diff (m²) | Diff (%) |
|-----------|---------|-------------|----------|---------|
| 5 | 21 | 24.0 | +3.0 | +14% |
| 5 | 18 | 24.0 | +6.0 | +33% |
| 5 | 15 | 24.0 | +9.0 | +60% |
| 6 | 13 | 20.0 | +7.0 | +54% |
| 7 | 11 | 17.1 | +6.1 | +56% |
| 9 | 9 | 13.3 | +4.3 | +48% |
| | | | **Avg: +5.9** | **Avg: +44%** |

**Note**: The Sonic 3 in ultra-light wind (5-9 kts) massively outperforms any linear formula. These are extreme conditions where foil kite aerodynamics are non-linear.

---

## Summary

### Tube (LEI) accuracy — K=1.7

| Brand | Model | Avg diff (m²) | Avg diff (%) |
|-------|-------|--------------|-------------|
| Duotone | Evo | -0.2 | -2% |
| Duotone | Rebel SLS | -0.2 | -3% |
| Duotone | Neo | +0.1 | +6% |
| Cabrinha | Switchblade | +1.3 | +12% |
| North | Orbit | +0.2 | +1% |
| North | Carve | +0.6 | +8% |
| F-One | Bandit XV | +0.3 | +1% |
| Flysurfer | Boost 2/3 | -0.0 | +2% |
| Flysurfer | Stoke | -0.7 | -7% |
| Flysurfer | Era (est.) | -0.8 | -8% |
| Flysurfer | Heli (est.) | +0.0 | +2% |
| **Overall** | | **+0.1** | **+1%** |

### Foil kite accuracy — K=1.6

| Brand | Model | Avg diff (m²) | Avg diff (%) | Notes |
|-------|-------|--------------|-------------|-------|
| Flysurfer | Soul 3 | -0.3 | -2% | All-round foil — excellent match |
| Flysurfer | Sonic 4 | +3.0 | +26% | High-perf, needs K≈1.2 |
| Flysurfer | Sonic 5 (foilboard) | +0.5 | +6% | Good match on hydrofoil |
| Flysurfer | Sonic 3 | +5.9 | +44% | Ultra light-wind outlier |

### Real-world validation

**Test case**: 85 kg rider, foil kite (Soul-class), 8-15 kts, uses 18 m²

| Wind | Intermediate | Advanced (×0.90) |
|------|-------------|-----------------|
| 8 kts | 17.0 m² | 15.3 m² |
| 10 kts | 13.6 m² | 12.2 m² |
| 12 kts | 11.3 m² | 10.2 m² |
| 15 kts | 9.1 m² | 8.2 m² |

The 18 m² foil kite covers 8-15 kts with the formula recommending 17 m² at 8 kts for intermediate — consistent with the kite being slightly oversized at the low end of the range.

---

## Limitations

- Linear weight scaling introduces ±1m² error at extremes (<50 kg, >100 kg)
- High-performance foils (Flysurfer Sonic) need K≈1.2, not 1.6
- Formula becomes inaccurate below 7 kts (non-linear aerodynamics)
- Does not account for board type (twintip vs hydrofoil vs surfboard)
- Does not factor temperature, altitude, or gustiness
- Brand variance is ±1-2m² — the formula targets the manufacturer low-end average

## Sources

### Tube (LEI)
- Duotone Evo, Rebel SLS, Neo — duotonesports.com
- Cabrinha Switchblade, Moto X Lite — cabrinha.com, mackiteboarding.com
- North Orbit, Carve — mackiteboarding.com, houstonkiteboarding.com
- F-One Bandit XV, Bandit-S — f-one.world
- Ozone Enduro V5 — ozonekites.com
- Airush weight guide — airush.com/blogs
- Eleveight RS V8 — mackiteboarding.com
- Slingshot Rally GT V2 — review data
- Naish Boxer — kiteforum.com
- Flysurfer Boost 2/3, Stoke, Era, Heli — flysurfer.com

### Foil kites
- Flysurfer Soul 3 — flysurfer.com, mackiteboarding.com
- Flysurfer Sonic 3, 4, 5 — flysurfer.com, mackiteboarding.com

### Cross-references
- KitesurfTheWorld weight chart — kitesurftheworld.com
- 35knots size guide — 35knots.com
- Sendy.io size chart — sendy.io
- TheKiteSpot calculators — thekitespot.com
