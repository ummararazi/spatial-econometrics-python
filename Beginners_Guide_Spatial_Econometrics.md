# Spatial Econometrics: A Beginner's Guide

*Companion notes to the Week 1 notebook. No prior knowledge of spatial methods is needed; basic regression (OLS) is enough.*

> **County vs country:** the notebook uses US **counties**, which are local districts inside US states (Los Angeles County, Cook County, and so on), similar to districts within a province. All the data come from one country, the United States, divided into about 3,100 counties.

**Author:** Dr. Ummara Razi, Center for Sustainable Development and Green Innovation Research, Bahçeşehir Cyprus University, and Institute of Energy Policy and Research (IEPRe), UNITEN Business School, Universiti Tenaga Nasional · [ummararazi.github.io](https://ummararazi.github.io) · [ORCID](https://orcid.org/0000-0002-5981-5736)

---

## 1. What does "spatial" mean?

"Spatial" simply means **related to location**. Spatial data is any data where each observation has a place attached to it: a country, a province, a county, a city, a postcode, or a point with latitude and longitude.

The key idea comes from the geographer Waldo Tobler, often called the **First Law of Geography**:

> Everything is related to everything else, but near things are more related than distant things.

Think of your own life. House prices on your street look more like house prices on the next street than like prices in a city 500 km away. The weather in Karachi tomorrow is more similar to Hyderabad's than to Lahore's. Your neighbourhood shares the same schools, roads, markets and air.

Spatial econometrics is the set of statistical tools that **takes this "neighbourhood effect" seriously** instead of ignoring it.

---

## 2. Why can't we just use ordinary regression?

Ordinary least squares (OLS) assumes every observation is **independent**: what happens in one region tells you nothing about another. With spatial data this assumption usually fails, for two reasons.

**Reason 1: Things really do spread across borders (spillovers).**
- A new factory in one district creates jobs that people in the next district commute to.
- Air pollution from one province blows into its neighbours.
- A tax cut in one state pulls businesses from nearby states.
- An infectious disease spreads from one town to the towns next to it.

**Reason 2: Neighbours share things we cannot measure (common shocks).**
- Nearby regions share climate, culture, soil, infrastructure and history. If these are missing from the model, the errors of neighbouring regions move together.

**What goes wrong if you ignore it?**
- Standard errors are usually **too small**, so you find "significant" results that are not real.
- Coefficients can be **biased**, because the effect of neighbours is wrongly loaded onto your own variables.
- You **miss the spillover**, which is often the most interesting part of the story for policy.

---

## 3. Real-world examples of spatial econometrics

| Field | Question | What the spatial part adds |
|---|---|---|
| Housing | Does a new metro line raise house prices? | Prices rise not only next to stations but in nearby neighbourhoods too |
| Transport | Does a new airport raise local employment? | Jobs may also grow in surrounding areas through commuting |
| Environment | Do pollution rules in one province reduce emissions? | Polluters may move next door ("pollution haven" spillover) |
| Public finance | Do states copy each other's tax rates? | Tax competition is a spatial interaction by definition |
| Health | Why do disease rates cluster? | Separates contagion from shared local conditions |
| Agriculture | What drives crop yields? | Neighbouring farms share weather, pests and knowledge |
| Growth | Do rich regions help their neighbours grow? | Measures regional growth spillovers |
| Finance | Do bank branch closures hurt local lending? | Borrowers may switch to branches in nearby areas |

In every case the question is the same: **does what happens here depend on what happens nearby?**

---

## 4. The heart of the method: the spatial weights matrix (W)

To include neighbours in a regression, we first have to tell the computer **who is a neighbour of whom**. This is done with a table called the **spatial weights matrix**, written **W**.

### A tiny example

Imagine four regions in a row:

```
  [ A ] — [ B ] — [ C ] — [ D ]
```

A touches only B. B touches A and C. C touches B and D. D touches only C.

W has one row and one column per region. Put 1 if two regions are neighbours, 0 otherwise:

|   | A | B | C | D |
|---|---|---|---|---|
| **A** | 0 | 1 | 0 | 0 |
| **B** | 1 | 0 | 1 | 0 |
| **C** | 0 | 1 | 0 | 1 |
| **D** | 0 | 0 | 1 | 0 |

The diagonal is always 0: a region is not its own neighbour.

### Row-standardisation

We usually divide each row by its total so each row sums to 1:

|   | A | B | C | D |
|---|---|---|---|---|
| **A** | 0 | 1 | 0 | 0 |
| **B** | 0.5 | 0 | 0.5 | 0 |
| **C** | 0 | 0.5 | 0 | 0.5 |
| **D** | 0 | 0 | 1 | 0 |

Now multiplying W by any variable gives the **average value among the neighbours**.

### The spatial lag (Wy)

Suppose house prices are A = 10, B = 20, C = 30, D = 40. Then:

| Region | Own price (y) | Neighbours' average (Wy) |
|---|---|---|
| A | 10 | 20 (just B) |
| B | 20 | 20 (average of A and C) |
| C | 30 | 30 (average of B and D) |
| D | 40 | 30 (just C) |

**Wy is called the "spatial lag".** It is the single most important building block. Every spatial model is basically ordinary regression plus Wy, WX, or both.

### Different ways to define neighbours

| Type | Rule | Good for |
|---|---|---|
| Rook contiguity | Share a border | Grid-like regions |
| Queen contiguity | Share a border or a corner | Counties, districts, provinces (most common) |
| k-nearest neighbours | The k closest regions | Ensuring every region has the same number of neighbours |
| Distance band | Everyone within d km | Commuting, markets with a clear range |
| Inverse distance | Weight = 1 / distance | Effects that fade smoothly with distance |
| Economic distance | Trade flows, flights, shared grid | When influence is not purely geographic |

**Choosing W is a modelling decision, and you must justify it.** Good practice is to choose one W based on economic reasoning and then show your results hold with others.

---

## 5. Is there spatial pattern in my data? Moran's I

Before modelling, check whether your variable is actually clustered in space.

**Global Moran's I** is like a correlation between each region's value and its neighbours' average:

| Moran's I | Meaning | Picture |
|---|---|---|
| Clearly above 0 | Positive spatial autocorrelation: similar values cluster | Rich areas next to rich areas |
| Around 0 | Random: no spatial pattern | Salt-and-pepper map |
| Below 0 | Negative: unlike values sit side by side | Chessboard pattern (rare in practice) |

A **Moran scatterplot** shows each region's (standardised) value on the x-axis and its neighbours' average on the y-axis. The slope of the line through the points equals Moran's I.

**Local Moran's I (LISA)** goes one step further and gives a result for every single region, which lets you draw a cluster map:

| LISA group | Meaning | Example |
|---|---|---|
| High-High | High value, high neighbours | A hot spot, such as a wealthy metro area |
| Low-Low | Low value, low neighbours | A cold spot, such as a lagging rural belt |
| High-Low | High value, low neighbours | An outlier "island", such as a mining town |
| Low-High | Low value, high neighbours | A "doughnut hole", such as a poor district inside a rich region |

---

## 6. The main spatial models in plain language

Let y be the outcome (for example house prices), X the explanatory variables (for example income), and ε the error.

### OLS (no space)
**y = Xβ + ε**
"Your house price depends only on your own income."

### SLX: Spatial lag of X
**y = Xβ + WXθ + ε**
"Your house price depends on your own income **and your neighbours' income**."
Spillovers are local: they reach direct neighbours only. Simple, easy to interpret, and estimated with OLS.

### SAR: Spatial autoregressive (spatial lag) model
**y = ρWy + Xβ + ε**
"Your house price depends on **your neighbours' house prices**."
Because your neighbours' prices also depend on their neighbours, effects ripple through the whole map. ρ (rho) measures the strength of this ripple.

### SEM: Spatial error model
**y = Xβ + u, with u = λWu + ε**
"There is no real spillover; neighbours just share unmeasured shocks (like the same weather)."
Space is treated as a nuisance in the errors. λ (lambda) measures how correlated neighbouring errors are.

### SDM: Spatial Durbin model
**y = ρWy + Xβ + WXθ + ε**
"Your house price depends on your neighbours' prices **and** their characteristics."
SDM contains SAR, SLX and (under a restriction) SEM as special cases, so most modern research starts here and tests whether a simpler model is enough.

### Quick comparison

| Model | Neighbours' outcome (Wy) | Neighbours' X (WX) | Spatial errors | Spillover type |
|---|---|---|---|---|
| OLS | No | No | No | None |
| SLX | No | Yes | No | Local |
| SAR | Yes | No | No | Global (ripples) |
| SEM | No | No | Yes | None (nuisance only) |
| SDM | Yes | Yes | No | Global + local |

---

## 7. The most misunderstood part: direct and indirect effects

In OLS, a coefficient of 0.5 on income means "1 unit more income → 0.5 units higher y". **In SAR and SDM this is no longer true.**

### The ripple analogy

Drop a stone into a pond. The splash is biggest where the stone lands, but ripples spread outwards and even bounce back.

Raise income in county A:
1. A's house prices rise (the splash).
2. A's neighbours' prices rise, because they depend on A's prices (the ripples).
3. A's own prices rise a little more, because A depends on its neighbours (the bounce-back, or **feedback**).

So we report three numbers for each variable (LeSage and Pace, 2009):

| Effect | Question it answers |
|---|---|
| **Direct effect** | If my region's X changes, how much does my own y change (including feedback)? |
| **Indirect effect (spillover)** | If my region's X changes, how much does y change in all other regions combined? |
| **Total effect** | Direct + indirect: the full impact across the whole system |

### A simple number example

In a SAR model with ρ = 0.5 and β = 1, the total effect is β / (1 − ρ) = 1 / 0.5 = **2**. The direct effect is a little above 1, and the indirect (spillover) effect is the rest, a little below 1. Spillovers can be as large as the direct effect.

**Rule:** never interpret the raw β or θ of SAR or SDM as marginal effects. Always report direct, indirect and total effects. Referees check this first.

---

## 8. How do I choose a model?

There are two common routes. Use both and report them.

**Route 1: Specific-to-general (Anselin).** Run OLS, then use Lagrange Multiplier (LM) tests on the residuals:
- Neither LM-lag nor LM-error is significant → OLS is fine.
- Only one is significant → use that model (SAR or SEM).
- Both are significant → compare the robust versions and pick the stronger one.

**Route 2: General-to-specific (Elhorst).** Start with SDM and use likelihood-ratio (LR) tests:
- Test whether θ = 0. If not rejected → SAR is enough.
- Test the common-factor restriction. If not rejected → SEM is enough.
- If both are rejected → keep SDM.

Also compare AIC across models and across different W matrices, but let **economic reasoning** have the final word.

---

## 9. A warning: correlation is still not causation

Spatial models describe **how outcomes are connected across space**. On their own they do not prove that one region **causes** changes in another.

In a well-known paper, Gibbons and Overman (2012) point out that ρ can pick up sorting (rich people moving near other rich people), shared amenities or omitted regional factors, not a true spillover.

Modern research answers this by combining spatial models with causal designs:
- **Panel data with fixed effects**, which remove everything about a region that does not change over time.
- **Natural experiments**, such as a policy, plant opening or shock that hits some places and not others.
- **Difference-in-differences with spillover rings**, which compare treated areas, their near neighbours and areas far away.
- **Conley standard errors**, which allow errors to be correlated within a given distance.

This series follows that path: Week 1 learns the tools, and later weeks add the causal design.

---

## 10. Common beginner mistakes

1. **Building W on a different sample** than the regression (for example, after dropping missing rows).
2. **Interpreting SAR or SDM coefficients as marginal effects** instead of reporting direct, indirect and total effects.
3. **Using only one W** with no justification or robustness check.
4. **Leaving "islands"** (regions with no neighbours) in W without fixing them.
5. **Treating a high Moran's I as proof of spillovers.** It only shows clustering, whatever the cause.
6. **Using spatial models when N is very small** (say, fewer than 30 regions), where estimates are unreliable.
7. **Ignoring map projection** when calculating distances. Use a projected coordinate system in metres, not raw latitude and longitude.

---

## 11. Where spatial econometrics is published

Spatial methods appear in top journals across economics, energy and the environment, for example: *Journal of Regional Science*, *Regional Science and Urban Economics*, *Journal of Urban Economics*, *Energy Economics*, *Journal of Environmental Economics and Management*, *Spatial Economic Analysis*, *Papers in Regional Science* and *Journal of Economic Geography*.

---

## 12. Glossary

| Term | Plain meaning |
|---|---|
| Spatial autocorrelation | Values at nearby locations are more alike (or less alike) than chance would give |
| Spatial weights matrix (W) | A table saying who is a neighbour of whom, and how strongly |
| Row-standardisation | Scaling each row of W to sum to 1, so Wy is a neighbours' average |
| Spatial lag (Wy) | The average value of y among a region's neighbours |
| ρ (rho) | Strength of dependence on neighbours' outcomes |
| λ (lambda) | Strength of correlation in neighbouring errors |
| θ (theta) | Effect of neighbours' characteristics (WX) |
| Spillover | The effect of a change in one region on other regions |
| Feedback | The part of a change that comes back to the original region via its neighbours |
| Moran's I | A single number measuring overall spatial clustering |
| LISA | Local Moran's I, one clustering statistic per region |
| Island | A region with no neighbours in W |
| Projection | The way the curved Earth is drawn flat; needed for accurate distances |

---

## Before running the notebook: get a Census API key

The Week 1 notebook downloads data directly from the US Census Bureau. Each user needs their own **free API key**, otherwise the download may be blocked, especially on Google Colab.

1. Request a key at https://api.census.gov/data/key_signup.html.
2. Activate it using the link in the confirmation email.
3. Paste it into the notebook's data cell: `CENSUS_KEY = 'your-key-here'`.

Keep the key private and remove it before sharing your notebook.

---

## 13. How this guide maps to the Week 1 notebook

| Notebook section | Guide section |
|---|---|
| 1. Data | Section 1 |
| 2. Spatial weights | Section 4 |
| 3. Moran's I and LISA | Section 5 |
| 4. OLS and LM tests | Sections 2 and 8 |
| 5. SLX, SAR, SEM, SDM | Sections 6 and 8 |
| 6. Direct, indirect, total effects | Section 7 |
| 7. Robustness to W | Sections 4 and 10 |
| 8. Gibbons and Overman critique | Section 9 |

---

## Further reading (in order of difficulty)

1. Rey, S., Arribas-Bel, D. and Wolf, L. *Geographic Data Science with Python*. Free online. Start here.
2. Anselin, L. and Rey, S. (2014). *Modern Spatial Econometrics in Practice*. GeoDa Press.
3. LeSage, J. and Pace, R.K. (2009). *Introduction to Spatial Econometrics*. CRC Press.
4. Elhorst, J.P. (2014). *Spatial Econometrics: From Cross-Sectional Data to Spatial Panels*. Springer.
5. Gibbons, S. and Overman, H. (2012). Mostly pointless spatial econometrics? *Journal of Regional Science*, 52(2), 172–191.

---

## How to cite this guide

> Razi, U. (2026). *Spatial Econometrics in Python: US Counties* [Computer software and teaching materials]. GitHub. ORCID: https://orcid.org/0000-0002-5981-5736

© 2026 Ummara Razi. Shared under the MIT licence; please keep this attribution when reusing or adapting.
