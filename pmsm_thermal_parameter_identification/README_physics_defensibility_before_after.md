# PMSM Electro-Thermal Physics Defensibility — Before vs After Analysis

## Purpose

This document records how the physics analysis changed the interpretation of the PMSM electro-thermal research, what was originally assumed, what the data actually supports, and how the physics-informed contribution should be framed.

---

## 1. Finding

The derivative analysis **does not invalidate the physics-informed research**. Instead, it identifies an important limitation in the current dataset representation:

> Copper-loss is moderately associated with winding temperature (`r = 0.533`), but its correlation with the calculated temperature derivatives is essentially absent (`r ≈ 0.007–0.023`).

The calculated derivatives are based on **CSV row order rather than physical time**, because the dataset contains no timestamp/time-interval column. Therefore, they should be interpreted as **temperature change per sample**, not physical `dT/dt`.

This means the research should **not claim that the current derivative feature establishes a direct first-principles transient thermal law**. The stronger and more defensible contribution is a **physics-informed learning framework that incorporates physically motivated electrical, thermal, mechanical, and cross-domain constraints/features**, while explicitly acknowledging that true temporal thermal dynamics cannot be established from this dataset alone.

---

# 2. BEFORE: Original Research Assumption

Before the defensibility analysis, the physics-informed approach was conceptually based on the following assumptions:

1. Electrical quantities such as voltage, current, speed, and torque contain meaningful information about motor thermal behavior.
2. Copper loss can be represented using a current-squared proxy:

   `P_copper ∝ i_d² + i_q²`

3. Higher electrical loading should be related to higher winding temperature.
4. Temperature differences between winding, tooth, yoke, PM, coolant, and ambient represent meaningful thermal relationships.
5. Derivatives of temperature could provide a representation of thermal dynamics.
6. A physics-informed loss could encourage the neural network to produce predictions consistent with these physical relationships.
7. The physics-informed CNN/Transformer/CNN-ViT comparison could therefore be presented as learning electro-thermal behavior rather than purely statistical regression.

### Original intended interpretation

The derivative features were potentially treated as evidence of thermal dynamics and as a possible mechanism through which the model could learn physically meaningful transient behavior.

---

# 3. AFTER: What the Dataset Actually Shows

The defensibility analysis provides a more precise picture.

## 3.1 Dataset structure is strong for physics-informed learning

- Records: **1,330,816**
- Profiles: **69**
- Required variables: present
- Missing/non-numeric rows removed: **0**
- Profile sizes vary substantially.
- Thermal measurements cover broad operating conditions.

The dataset therefore provides a substantial basis for studying electro-thermal relationships.

---

## 3.2 Thermal subsystem coupling is strong

The measured thermal variables are strongly coupled.

Examples:

- Winding ↔ Tooth: **0.970**
- Tooth ↔ Yoke: **0.953**
- Winding ↔ Yoke: **0.860**
- Winding ↔ PM: **0.796**
- Yoke ↔ Coolant: **0.861**

The overall thermal coupling score is approximately:

**0.869 — Strong**

### Interpretation

The thermal subsystem contains clear internal structure. A model that predicts multiple thermal states jointly is therefore not operating on unrelated targets; the targets exhibit physically meaningful relationships.

This supports retaining **multi-target electro-thermal physics constraints**.

---

# 4. Electrical-Mechanical Relationships Remain Strong

One of the strongest findings is:

**Iq ↔ Torque: r = 0.9965**

This is highly consistent with the expected relationship between q-axis current and electromagnetic torque in the operating regime represented by the dataset.

Other meaningful relationships include:

- `i_d ↔ motor_speed = -0.701`
- `u_q ↔ motor_speed = 0.684`
- `u_d ↔ torque = -0.754`
- `i_d ↔ stator_winding = -0.624`

### Interpretation

The electrical/mechanical subsystem contains strong structure that can legitimately support physics-informed feature engineering and constraints.

This part of the original physics motivation remains strong.

---

# 5. Copper Loss: The Important Revision

The copper-loss proxy was defined as:

`P_copper ∝ i_d² + i_q²`

The analysis finds:

**P_copper ↔ stator winding temperature: r = 0.533**

This is a **moderate and meaningful association**.

Copper loss also correlates with:

- Stator tooth: **0.370**
- Stator yoke: **0.194**
- PM: **0.194**

### Interpretation

The data supports the statement:

> Electrical loading represented by the current-squared copper-loss proxy is associated with thermal state, particularly winding temperature.

However, this does **not** establish that the proxy alone determines temperature.

Thermal behavior is clearly influenced by additional variables such as operating condition, speed, coolant, ambient temperature, thermal coupling, and the history of the system.

---

# 6. The Major New Finding: Derivatives

The temperature derivatives were calculated using **row order within each profile**.

There is no timestamp or physical sampling interval in the supplied dataset.

Therefore:

`ΔT / Δsample`

is available, but:

`dT / dt`

is **not physically identifiable** from the supplied data.

This is a critical methodological distinction.

---

## 6.1 Derivative correlations

The calculated derivatives show very strong correlation with one another:

- `dTw ↔ dTt = 0.950`
- `dTt ↔ dTy = 0.930`
- `dTw ↔ dTy = 0.814`
- `dTPM` also has moderate-to-strong relationships with the other thermal derivatives.

However, copper-loss has almost no direct correlation with these derivatives:

| Relationship | Correlation |
|---|---:|
| P_copper ↔ dTw | 0.023 |
| P_copper ↔ dTt | 0.013 |
| P_copper ↔ dTy | 0.007 |
| P_copper ↔ dTPM | 0.007 |

### Interpretation

The derivative features appear to primarily capture **coordinated changes among thermal sensors**, rather than a directly identifiable instantaneous response to copper loss.

This is the central finding of the defensibility analysis.

---

# 7. What This Means for the Mathematics

## BEFORE

The research could have been interpreted as if the derivative term represented:

`dT/dt`

and therefore directly represented physical thermal dynamics.

## AFTER

The mathematically defensible interpretation is:

`ΔT/Δsample`

because the physical time interval is unknown.

Therefore:

### The core neural-network mathematics does NOT need to be discarded.

The following can remain:

- CNN formulation
- LSTM formulation
- Transformer formulation
- CNN-ViT formulation
- multi-output regression
- MSE-based prediction objective
- physics-informed loss framework
- weighted combination of data loss and physics loss

What must change is the **physical interpretation of the derivative term**.

---

# 8. What Should Change in the Physics Loss

The physics loss should be divided conceptually into two categories.

## A. Strongly defensible constraints

These can remain central:

### Electrical relationships

- Current/voltage relationships
- q-axis current ↔ torque relationship
- electrical/mechanical power relationships, where appropriately defined
- current magnitude and electrical loading

### Thermal relationships

- Temperature ordering
- Thermal-state consistency
- Cross-temperature coupling
- Thermal differences between motor components
- Copper-loss ↔ thermal-state association

These relationships are supported by the observed dataset structure.

---

## B. Weakly defensible transient constraint

The current derivative constraint should be treated carefully.

Instead of claiming:

`dT/dt = f(P_copper, ...)`

the paper should state:

> A sample-order thermal change feature was investigated as a proxy for local thermal variation; however, because physical sampling intervals were unavailable, it cannot be interpreted as a true temporal derivative.

This is a **limitation**, not a failure of the entire physics-informed methodology.

---

# 9. What Happens to the Novel Contribution?

The novelty should become **more precise**, not weaker.

## BEFORE

Potentially framed as:

> A physics-informed deep learning framework that learns PMSM electro-thermal dynamics using thermal derivatives and physical laws.

This is too strong given the available temporal information.

## AFTER

> A physics-informed multi-domain deep learning framework for PMSM electro-thermal state prediction that integrates electrical, mechanical, thermal, and physically motivated loss constraints into CNN, LSTM, Transformer, and CNN-ViT architectures.

Focus:
1. **Multi-domain physics integration**
2. **Physics-informed multi-output thermal prediction**
3. **Comparison of temporal, convolutional, attention-based, and hybrid architectures under the same physics framework**
4. **Physics-informed CNN-ViT as the proposed hybrid architecture**
5. **Evaluation of physics consistency in addition to predictive accuracy**
6. **Robustness to reduced training data / fragmented training**
7. **Explicit physics defensibility analysis rather than assuming that engineered features are physically valid**

---

# 10. What the CNN-ViT Novelty Becomes

The CNN-ViT contribution does not depend exclusively on the derivative.

The stronger argument is:

### CNN

Captures local patterns in the multivariate motor signal.

### ViT / Transformer component

Captures longer-range relationships among sequential/structured motor states.

### CNN-ViT

Combines local representation learning with global attention.

### Physics-informed CNN-ViT

Adds physically motivated constraints/features so that the model is evaluated not only on numerical prediction but also on consistency with known PMSM relationships.

Therefore, the derivative finding does **not eliminate the CNN-ViT contribution**.

It simply means the paper should avoid claiming that CNN-ViT has learned a physically exact thermal differential equation unless additional time information or a validated thermal model is introduced.

---

# 11. Power-Balance Finding

The simple electrical/mechanical power diagnostic also requires caution.

The analysis reports:

- Mean robust relative power error: **2.076**
- Median: **1.071**
- 90th percentile: **5.371**
- 95th percentile: **7.391**

The residual has a substantial negative mean.

### Interpretation

The simplified power formulation used in the diagnostic does not close the PMSM energy balance sufficiently well to be treated as a strict physical equality.

Therefore, it should **not be used as a hard equality constraint without further validation**.

Possible reasons include:

- sign conventions
- reference-frame conventions
- omitted losses
- parameter assumptions
- measurement characteristics
- generator/motor operating regions
- the simplified copper-loss definition
- missing sampling/time information

The correct research response is to treat this as a **diagnostic finding**, not to force the data to satisfy an inappropriate equation.

---

# 12. What Remains Strong

The analysis supports several important claims.

### Strongly supported

- Large-scale electro-thermal dataset
- 69 operating profiles
- Strong thermal subsystem coupling
- Strong electrical-mechanical relationships
- Very strong Iq-torque relationship
- Moderate copper-loss/winding-temperature association
- Consistent thermal ordering
- Significant profile-to-profile variability
- Meaningful electro-thermal interactions

### Moderately supported

- Current-squared copper-loss proxy as a thermal explanatory feature
- Physics-informed thermal prediction
- Multi-target thermal coupling constraints

### Not established by this dataset

- True physical `dT/dt`
- Direct instantaneous causal relationship between copper loss and temperature rate
- Exact thermal differential equation identification
- Exact power conservation from the simplified power equation

---

# 13. Research Before vs After

| Aspect | BEFORE | AFTER |
|---|---|---|
| Thermal derivative | Treated as potential `dT/dt` | `ΔT/Δsample` only |
| Physical time | Implicitly assumed | Explicitly unavailable |
| Copper loss | Physics feature | Physics feature with moderate thermal support |
| Pcu → temperature | Expected | Supported moderately, especially winding |
| Pcu → thermal derivative | Expected/assumed possible | Not supported by correlation |
| Thermal coupling | Assumed | Strongly demonstrated |
| Iq → torque | Expected | Very strongly demonstrated (`r=0.9965`) |
| Power balance | Potential physics constraint | Diagnostic only unless reformulated |
| Physics loss | Broad physics enforcement | Should emphasize defensible relationships |
| CNN-ViT novelty | Physics-informed hybrid | Still valid, with more precise physics claims |
| Research claim | Thermal dynamics learning | Physics-informed electro-thermal state learning |
| Main weakness | Not fully identified | Missing physical time interval |

---

# 14. Recommended Research Position

The strongest scientific position is **not** to hide the derivative result.

Instead, explicitly report it.

The paper can say that the study performed a physics-defensibility analysis and discovered that sample-order derivatives do not provide sufficient evidence for a physical transient thermal law.

This actually strengthens the credibility of the work because the physics assumptions are being **tested rather than merely imposed**.

The contribution becomes:

> Physics-informed learning with empirical validation of the defensibility of each incorporated physical relationship.

That is substantially more rigorous than simply adding physics-inspired features and calling the model physics-informed.

---

# 15. Recommended Next Experimental Step

If true thermal dynamics are important to the final claim, the strongest next step is to obtain or reconstruct a valid sampling interval.

With a known `Δt`, the derivative can become:

`dT/dt ≈ (T[t] - T[t-1]) / Δt`

Then the research can investigate relationships such as:

`dT/dt ↔ P_copper`

and, more importantly, evaluate whether a thermal balance or reduced-order thermal model provides a better physics constraint.

Until that information is available, the derivative should remain a **sample-order change feature**, not a physical time derivative.

---

# 16. Bottom Line

The analysis changes the research from:

**"We assume these engineered derivatives represent physical thermal dynamics."**

to:

**"We systematically tested the physical defensibility of the engineered relationships and found strong thermal/electromechanical structure, moderate copper-loss/temperature association, but insufficient evidence that sample-order thermal derivatives represent physical thermal dynamics."**

The core mathematics and CNN-ViT architecture can remain.

The main change is the **physics interpretation and the formulation/weighting of the physics loss**.

