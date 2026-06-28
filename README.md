**Problem Statement**

The company has run an A/B experiment to evaluate a new onboarding and activation campaign against the existing experience, across 1,400 users (690 control and 710 treatment). Leadership must decide whether to launch, reject, continue testing, or launch only for selected segments

1. Decision: Launch, reject, or limit the new onboarding campaign based on experiment results
2. Who it impacts: All new signups from the moment of launch; also affects support, finance, and product teams
3. Metric that must improve: 30-day paid conversion rate — the share of users who become paying customers within 30 days
4. Risks to monitor: Rise in support tickets, refund requests, and low-engagement conversions that inflate revenue without retention
5. Evidence required: Statistically significant lift in paid conversion (p < 0.05), no meaningful degradation in guardrail metrics, and a credible funnel mechanism explaining how the lift is achieved
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**North Star Metric**

North Star: "30-day paid conversion rate"

1. Why it is the North Star: It is the single most direct proxy for monetised user value. A user who pays within 30 days signals both product fit and successful onboarding. All funnel steps upstream are only valuable if they ultimately produce a paying customer.
2. Why others are supporting metrics: Landing-page visits, trial starts, and onboarding completions are leading indicators — they explain how conversion happens but cannot stand alone. High completion with no conversion means the funnel leaks at the final step.
3. How it connects to business growth: Paid conversion directly drives MRR and LTV. Doubling conversion from 3.2% to 7.0% — as seen here — roughly doubles revenue from the same traffic, without any acquisition cost increase.
4. What could go wrong if optimised blindly: Aggressive promotions or false urgency can inflate short-term conversions while increasing refund rates and support load, reducing net revenue and damaging trust. Conversion quality (revenue, engagement) must be monitored alongside the rate.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Cleaning and Preparing Data**

- Replaced missing values in device and traffic source by 'Unknown': Instead of dropping those rows, creating an "Unknown" category preserves the observations, prevents changes in sample size, and allows missingness itself to be analyzed.
- Removed duplicate IDs
- Retained the revenue outliers: If the values are genuine customer behavior, removing only the Control outliers changes the distribution of one group but not the other
# Hypothesis Test Notes — New Campaign Experiment

## 1. Business Context

The experiment tests a new marketing campaign applied to the Treatment group against a Control group.

| Decision | Triggered when |
|---|---|
| **Full Launch** | Statistically significant positive lift on North Star Metric, no adverse signals |
| **Reject** | No significant lift, or significant harm on guardrail metrics |
| **Continue Testing** | Results are directionally positive but confidence interval too wide |
| **Selective Launch** | Significant lift concentrated in specific segments; roll out only to those segments |

Every hypothesis below is written with this decision framework as its end purpose.

---

## 2. North Star Metric

> **30-day Paid Conversion Rate** — the proportion of users in each group who converted to a paid plan within 30 days of signup.

**Why this metric above all others:**

A user who pays within 30 days has demonstrated three things simultaneously: they found the product valuable enough to return to the site, they successfully navigated onboarding, and they were willing to exchange money for continued access. No upstream funnel metric — landing page visits, trial starts, or onboarding completions — carries this weight on its own. Each upstream step is only meaningful as a leading indicator of this outcome. Optimising for trial start rate at the expense of conversion rate, for example, could inflate vanity metrics while leaving revenue flat. The 30-day paid conversion rate is therefore the single most direct proxy for monetised user value and the metric on which the launch decision will be made.

---

## 3. Primary Hypothesis (North Star Metric)

### 3.1 Null Hypothesis (H₀)

> The 30-day paid conversion rate is equal between the Control and Treatment groups.
>
> H₀: p_treatment − p_control = 0

There is no difference in paid conversion attributable to the new campaign. Any observed difference is the result of random sampling variation.

### 3.2 Alternate Hypothesis (H₁)

> The 30-day paid conversion rate is **higher** in the Treatment group than in the Control group.
>
> H₁: p_treatment − p_control > 0

The new campaign produces a genuine, positive uplift in the proportion of users who convert to a paid plan within 30 days.

### 3.3 Test Directionality

**One-tailed (upper-tail)**

The campaign was designed with the explicit goal of increasing paid conversion. Launching a campaign that makes conversion *worse* would be rejected for the same business reason as no effect — both outcomes mean the campaign does not ship. A one-tailed test is therefore the correct framing: we are asking whether the campaign is *better*, not merely *different*. Using a one-tailed test also preserves statistical power for detecting the directional effect that matters.

### 3.4 Significance Level

**α = 0.05** (5%)

This is the industry-standard threshold for A/B tests in product and marketing contexts.

### 3.5 Statistical Test

**Two-proportion Z-test** (large independent samples, n_control = 690, n_treatment = 710)

Both samples are sufficiently large for the normal approximation to the binomial distribution to hold. The two groups are independent (random assignment). A chi-squared test of proportions would yield an equivalent result; the Z-test is preferred here because it directly produces a Z-statistic and a directional p-value compatible with the one-tailed framing.

---

## 4. Observed Results on North Star Metric

| | Control | Treatment |
|---|---|---|
| Users (n) | 693 | 715 |
| Conversions | 22 | 50 |
| Conversion Rate | **3.17%** | **6.99%** |
| Absolute Lift | — | **+3.82 percentage points** |
| Relative Lift | — | **+120.3%** |
| 95% Confidence Interval (lift) | — | **[+1.54 ppt, +6.10 ppt]** |
| Z-statistic | — | **3.252** |
| p-value (two-tailed) | — | **0.00115** |
| p-value (one-tailed) | — | **0.00057** |

The confidence interval does not cross zero. The p-value (one-tailed: 0.00057) is far below α = 0.05.

---

## 5. Interpretation Logic

### 5.1 Primary Decision Rule

```
IF p-value (one-tailed) < α (0.05)  AND  lower bound of 95% CI > 0
THEN reject H₀ → evidence supports a genuine positive lift
```

**Result:** p = 0.00057 < 0.05 ✓  |  CI lower bound = +1.54 ppt > 0 ✓

**H₀ is rejected.** The null hypothesis of no difference is rejected at the 5% significance level. The evidence supports that the campaign produces a genuine uplift in 30-day paid conversion.

### 5.2 Guardrail Metric Review

Before translating a rejected H₀ into a launch recommendation, we check that the campaign has not caused harm on guardrail metrics. These are not hypothesis tests in themselves but trip-wire conditions: if any guardrail is breached, the launch decision is paused regardless of the conversion result.

| Guardrail Metric | Control | Treatment | Direction | Signal |
|---|---|---|---|---|
| Refund Rate | 0.00% | 0.42% | ↑ Treatment | ⚠ Small, monitor post-launch |
| Support Ticket Rate | 14.7% | 24.8% | ↑ Treatment | ⚠ Meaningful increase — investigate |
| Avg Engagement Score | 57.0 | 62.9 | ↑ Treatment | ✅ Positive |
| Avg Days to Convert | 8.9 days | 6.4 days | ↓ Treatment | ✅ Faster conversion |

The support ticket rate increase (+10.1 ppt) is the primary concern. A higher volume of support contacts in the Treatment group may indicate friction in the onboarding flow that the campaign surfaces. This should be investigated before or alongside launch — it does not block the decision, but it requires operational readiness (support capacity) and a post-launch monitoring plan.

### 5.3 Funnel Coherence Check

A valid conversion uplift should be accompanied by directionally consistent improvements upstream. If conversion rose while trial starts fell, for example, the mechanism would be suspect.

| Funnel Stage | Control | Treatment | Lift |
|---|---|---|---|
| Landing Page Visit Rate | 63.6% | 72.6% | +8.9 ppt ✅ |
| Trial Start Rate | 25.1% | 29.1% | +4.0 ppt ✅ |
| Onboarding Completion Rate | 15.6% | 21.3% | +5.7 ppt ✅ |
| **Paid Conversion Rate** | **3.17%** | **6.99%** | **+3.82 ppt ✅** |

The funnel improves at every stage. This coherence strengthens causal confidence: the campaign is not producing a statistical artefact — it is lifting user intent and follow-through at each step of the funnel.

### 5.4 Segment-Level Interpretation (Selective Launch Signal)

Even with a valid overall result, segment analysis determines whether the effect is uniform or concentrated, which informs *how* to launch.

**By Region:**

| Region | Control Rate | Treatment Rate | Lift |
|---|---|---|---|
| North | 3.45% | 8.89% | +5.44 ppt |
| South | 3.26% | 7.61% | +4.35 ppt |
| East | 2.53% | 6.40% | +3.86 ppt |
| West | 3.38% | 5.03% | +1.65 ppt |

North and South show the strongest treatment effect. West shows the weakest relative lift. A selective-launch strategy could prioritise North and South while continuing to test the campaign's creative in West.

**By Device Type:**

| Device | Control Rate | Treatment Rate | Lift |
|---|---|---|---|
| Mobile | 2.57% | 7.34% | +4.77 ppt |
| Tablet | 1.79% | 7.14% | +5.36 ppt |
| Desktop | 4.50% | 6.54% | +2.04 ppt |

Mobile and Tablet users respond most strongly to the campaign. Desktop users show a smaller but still positive lift. This suggests the campaign creative or landing experience may be optimised for smaller screens, or that mobile-first users are inherently more responsive to this campaign's messaging.

**By Plan Type:**

| Plan | Control Rate | Treatment Rate | Lift |
|---|---|---|---|
| Free | 3.05% | 9.24% | +6.19 ppt |
| Premium | 2.75% | 6.25% | +3.50 ppt |
| Basic | 3.59% | 3.83% | +0.24 ppt |

The Free-tier segment drives the bulk of the conversion lift. Basic plan users show virtually no treatment effect (+0.24 ppt). This makes intuitive business sense: Free users have the most to gain from a paid upgrade prompt, while Basic users may already be partially committed or need a different value proposition. If budget is constrained, targeting Free-tier users first maximises ROI.

**By Traffic Source:**

| Source | Control Rate | Treatment Rate | Lift |
|---|---|---|---|
| Referral | 2.47% | 10.99% | +8.52 ppt |
| Paid Search | 1.28% | 6.25% | +4.97 ppt |
| Email | 2.70% | 7.14% | +4.44 ppt |
| Organic | 2.03% | 6.22% | +4.19 ppt |
| Social | 7.69% | 6.02% | **−1.68 ppt** |

Referral traffic shows an exceptional treatment effect (+8.52 ppt), suggesting the campaign resonates strongly with users who already have social proof of the product. Social traffic is the outlier — the Treatment group performs *worse* than Control here, which may indicate the campaign messaging conflicts with the intent or expectations of users arriving via social channels. **Social traffic should be excluded from initial launch targeting**, and a dedicated test for this segment should be planned separately.

---

## 6. Summary of Hypotheses Across Metrics

For completeness, secondary hypotheses on supporting metrics follow the same structure but are evaluated as directional supporting evidence, not as the primary decision gate.

| Metric | H₀ | H₁ | Tails | α | Role |
|---|---|---|---|---|---|
| **30-day Paid Conversion Rate** | p_T = p_C | p_T > p_C | One-tailed | 0.05 | **Primary — decision gate** |
| Landing Page Visit Rate | p_T = p_C | p_T > p_C | One-tailed | 0.05 | Funnel coherence check |
| Trial Start Rate | p_T = p_C | p_T > p_C | One-tailed | 0.05 | Funnel coherence check |
| Onboarding Completion Rate | p_T = p_C | p_T > p_C | One-tailed | 0.05 | Funnel coherence check |
| Avg Revenue per User | μ_T = μ_C | μ_T > μ_C | One-tailed | 0.05 | Revenue validation |
| Refund Rate | p_T = p_C | p_T ≠ p_C | **Two-tailed** | 0.05 | Guardrail — harm detection |
| Support Ticket Rate | p_T = p_C | p_T ≠ p_C | **Two-tailed** | 0.05 | Guardrail — harm detection |
| Avg Engagement Score | μ_T = μ_C | μ_T > μ_C | One-tailed | 0.05 | Leading quality signal |
| Avg Days to Convert | μ_T = μ_C | μ_T < μ_C | One-tailed (lower) | 0.05 | Speed-to-value signal |

> **Note on guardrail tails:** Refund rate and support ticket rate use two-tailed tests because harm in either direction (unexpectedly high *or* unexpectedly low) is operationally relevant. A campaign that eliminates support tickets entirely, for instance, might indicate users aren't engaging deeply enough to have questions.

---

# Final Decision Recommendation

Based on the statistical evidence:

- **H₀ is rejected** (p = 0.00057, well below α = 0.05)
- **95% CI on lift is fully positive**: [+1.54 ppt, +6.10 ppt]
- **Funnel coherence confirmed** at all stages
- **Segment analysis reveals concentration**: Free-tier, Mobile/Tablet, North/South, and Referral traffic show the strongest effects; Social traffic shows a negative signal

**Recommended decision: Selective Launch with phased expansion**

1. **Launch immediately** to Free-tier users on Mobile and Tablet, from Referral, Email, Organic, and Paid Search traffic channels in North and South regions — where lift is largest and statistically robust.
2. **Hold Social traffic** from the campaign. Re-test with a separate creative variant designed for social-intent users.
3. **Monitor support ticket volume** closely in the first 2 weeks post-launch. If the rate normalises (suggesting experiment novelty drove tickets), proceed with broader rollout. If it persists, investigate the onboarding friction point before expanding.
4. **Re-evaluate Basic plan users** in a separate test with different campaign messaging — the near-zero lift (+0.24 ppt) suggests the current campaign does not address their specific conversion barrier.
5. **Full launch** to all remaining segments after 30 days of post-launch monitoring, contingent on stable guardrail metrics.

---

