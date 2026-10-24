# Week 2 Guide: Choosing the Model and Choosing W

*Companion notes to the Week 2 notebook. Read the [Beginner's Guide](Beginners_Guide_Spatial_Econometrics.md) first if you are new to spatial econometrics.*

**Author:** Dr. Ummara Razi, Center for Sustainable Development and Green Innovation Research, Bahçeşehir Cyprus University, and Institute of Energy Policy and Research (IEPRe), UNITEN Business School, Universiti Tenaga Nasional · [ummararazi.github.io](https://ummararazi.github.io) · [ORCID](https://orcid.org/0000-0002-5981-5736)

---

## 1. The two questions every referee asks

After Week 1 you know how to estimate spatial models. Week 2 is about defending your choices. In almost every review of a spatial paper, two questions come up:

1. **Why this model?** Why a spatial Durbin model and not a spatial error model, or the reverse?
2. **Why this W?** Why should "neighbour" mean sharing a border, rather than being within 100 km, or being in the same state?

Neither question has a purely statistical answer. Good practice is to start from the economic mechanism, then use the data to check that the choice is reasonable and that the conclusions do not depend on it.

---

## 2. Global versus local spillovers

The most important modelling choice is whether spillovers are **global** or **local**.

**Global spillovers (SDM, SAR).** A change in one county affects its neighbours, which affects their neighbours, and so on, with feedback back to the original county. Think of a stone dropped in a pond: the ripples reach the whole surface, getting weaker with distance.

*Example:* house prices. If prices rise in one county, buyers move to the next county, raising prices there, which pushes buyers further out. The effect travels.

**Local spillovers (SLX, SDEM).** A change in one county affects only its immediate neighbours and stops there. Think of a fence: what happens on your side affects the house next door, but not the street beyond.

*Example:* a new hospital. People in the county and the counties next to it use it; people three counties away do not.

| | SDM | SDEM | SLX |
|---|---|---|---|
| Neighbours' outcome (Wy) | Yes | No | No |
| Neighbours' characteristics (WX) | Yes | Yes | Yes |
| Spatially correlated errors | No | Yes | No |
| Spillover reach | Whole map | Immediate neighbours | Immediate neighbours |
| Can you read θ directly as the spillover? | No, compute effects | Yes | Yes |
| Estimation | Maximum likelihood or GMM | Maximum likelihood or GMM | OLS |

A useful rule (LeSage, 2014): if you cannot tell a convincing story about why effects should travel beyond immediate neighbours, prefer a local-spillover model.

---

## 3. How to choose between models with data

**Nested models: likelihood-ratio tests.** When one model is a special case of another, compare them with an LR test:

LR = 2 × (log-likelihood of the general model − log-likelihood of the restricted model)

Compare LR with a χ² distribution whose degrees of freedom equal the number of restrictions. A small p-value means the restriction is rejected and the general model is preferred.

| Test | What it asks |
|---|---|
| SDM vs SAR | Do neighbours' characteristics (WX) matter? |
| SDM vs SLX | Do neighbours' outcomes (Wy) matter once WX is included? |
| SDM vs SEM | Is the spatial correlation only in the errors? (common-factor test) |
| SDEM vs SLX | Are the errors spatially correlated once WX is included? |

**Non-nested models: compare fit.** SDM and SDEM are not special cases of each other. When they have the same number of parameters, compare their log-likelihoods directly. The higher one fits better. AIC does the same while penalising extra parameters.

**But fit is not everything.** If SDM fits slightly better but SDEM has the more plausible economic story, report both and show whether the conclusions differ.

---

## 4. Choosing W

### Why it matters

W defines the channel through which spillovers travel. Change W and you change the question being answered.

US counties are a good illustration. In the East, counties are small, so a Queen neighbour may be 30 km away. In the West, counties can be huge, so a Queen neighbour may be 150 km or more away. The same definition of "neighbour" therefore means very different things in different places. The Week 2 notebook maps this.

### Common choices and the stories behind them

| W | Rule | Story it fits |
|---|---|---|
| Contiguity (Queen, Rook) | Share a border | Administrative adjacency; the conventional default |
| k-nearest neighbours | The k closest units | Everyone has the same number of contacts |
| Distance band | Everyone within d km | A market, commuting or service range of d km |
| Inverse distance | Weight falls as 1/distance^α | Influence that fades smoothly with distance |
| Institutional | Same state, same utility area, same trade bloc | Shared rules, prices or institutions |
| Economic | Trade flows, migration, commuting | Influence follows real economic links |

### Practical rules

1. **Start from the mechanism.** If the channel is commuting, use distance or commuting flows. If it is state policy, an institutional W makes sense.
2. **Compare fit across reasonable alternatives.** With the same model and number of parameters, a higher log-likelihood means a better fit.
3. **Watch for W that is too dense.** When every unit has hundreds of neighbours, W starts to capture broad regional trends, and ρ or λ drift towards 1. That is a warning sign, not a strong spillover.
4. **Compare effects, not coefficients.** LeSage and Pace (2014) show that results often look much more stable across W once you compare direct and indirect effects.
5. **Report the sensitivity honestly.** If the spillover changes sign or significance across reasonable W, that is a finding readers need to know.

### Letting the data choose the distance decay

Because SLX is estimated by OLS, it is cheap to try many versions of W. Halleck Vega and Elhorst (2015) suggest varying the decay parameter α in an inverse-distance W and picking the value with the best fit. The notebook does this in Section 5.3.

---

## 5. Is W capturing spillovers or just regional differences?

High values of ρ or λ can mean real interaction between neighbours. They can also mean the model is missing something that varies smoothly across space: climate, amenities, regional housing cycles, state taxes. W then picks up these omitted factors and labels them "spillovers".

**Fixed effects help.** Adding a dummy for each state removes everything that is common to counties within a state. What remains is county-to-county interaction within states. If ρ falls sharply once state effects are added, much of the original "spatial dependence" was regional heterogeneity.

This is the practical response to Gibbons and Overman (2012): control for what you can observe and what fixed effects can absorb, then interpret the remaining spatial parameters with care. With panel data (Week 3), county fixed effects go further and remove all time-invariant local factors.

---

## 6. Robust standard errors

Maximum likelihood assumes errors have the same variance everywhere. County data rarely meet this: averages for small counties are noisier than for large ones. Three robust alternatives are used in the notebook:

| Method | What it is robust to | When to use |
|---|---|---|
| SDM by spatial 2SLS with White SE | Heteroskedasticity | SDM when error variance differs across units |
| SDEM by GMM (Kelejian–Prucha) | Heteroskedasticity | SDEM in the same situation |
| SLX with spatial HAC | Heteroskedasticity and correlation among nearby units | When you do not want to model the error correlation; the basis of Conley standard errors |

A simple rule: if a spillover is significant only under the homoskedastic ML standard errors and loses significance under robust ones, do not build your conclusion on it.

---

## 7. A model-choice paragraph you can adapt

> We model [outcome] as a function of [variables] and allow for spillovers across [units]. Because the channel we have in mind is [mechanism], we define neighbours using [W] and expect spillovers to be [local/global]. Likelihood-ratio tests reject the restrictions that reduce the model to [SAR/SEM/SLX] (Table X). The [SDM/SDEM] fits better than the [SDEM/SDM] under [W] and under most alternative weights matrices (Table Y). We report direct, indirect and total effects rather than raw coefficients (LeSage and Pace, 2009). Results are robust to [k-nearest, distance-band, inverse-distance] weights, to state fixed effects, and to heteroskedasticity-robust and spatial HAC standard errors (Tables Z1–Z3).

---

## 8. Common mistakes in model choice

1. **Choosing W after seeing which one gives significant results.** Decide on the main W from theory first.
2. **Comparing log-likelihoods across different samples or outcomes.** Fit comparisons only make sense for the same data and dependent variable.
3. **Reading SDM coefficients as spillovers.** Always compute effects.
4. **Ignoring ρ or λ close to 1.** This usually means W is too dense or important regional controls are missing.
5. **Reporting only homoskedastic ML standard errors** for county or regional data.

---

## 9. Glossary for Week 2

| Term | Plain meaning |
|---|---|
| Global spillover | An effect that travels beyond immediate neighbours through feedback (SDM, SAR) |
| Local spillover | An effect that reaches only immediate neighbours (SLX, SDEM) |
| SDEM | Spatial Durbin error model: WX plus spatially correlated errors |
| SAC | Model with both a spatial lag of y and spatially correlated errors |
| GNS | General nesting spatial model, containing every other model; rarely estimated |
| Nested models | One model is a special case of the other |
| Likelihood-ratio test | Compares fit of nested models |
| AIC | Fit measure that penalises extra parameters; lower is better |
| Distance decay | How fast influence falls with distance |
| Fixed effects | Dummies that absorb everything common to a group, such as a state |
| GMM / 2SLS | Estimators that use instruments and can be made robust to heteroskedasticity |
| Spatial HAC | Standard errors robust to heteroskedasticity and to correlation among nearby units |

---

## Key references

- Elhorst, J.P. (2010). Applied spatial econometrics: raising the bar. *Spatial Economic Analysis*, 5(1), 9–28.
- Gibbons, S. and Overman, H. (2012). Mostly pointless spatial econometrics? *Journal of Regional Science*, 52(2), 172–191.
- Halleck Vega, S. and Elhorst, J.P. (2015). The SLX model. *Journal of Regional Science*, 55(3), 339–363.
- Kelejian, H.H. and Prucha, I.R. (2007). HAC estimation in a spatial framework. *Journal of Econometrics*, 140(1), 131–154.
- LeSage, J.P. (2014). What regional scientists need to know about spatial econometrics. *The Review of Regional Studies*, 44(1), 13–32.
- LeSage, J.P. and Pace, R.K. (2014). The biggest myth in spatial econometrics. *Econometrics*, 2(4), 217–249.

---

## How to cite this guide

> Razi, U. (2026). *Spatial Econometrics in Python: US Counties* [Computer software and teaching materials]. GitHub. https://github.com/ummararazi/spatial-econometrics-python

© 2026 Ummara Razi. Shared under the MIT licence; please keep this attribution when reusing or adapting.
