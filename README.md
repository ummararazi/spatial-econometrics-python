# Spatial Econometrics in Python: US Counties

A hands-on learning series in spatial econometrics, built on about 3,100 US counties and free public data.

> **Note:** a *county* is a local administrative district inside a US state (similar to a district within a province), not a country. All data come from one country, the United States, divided into about 3,100 counties. Each week is one Jupyter notebook that runs from raw data to publication-style tables and maps.

**Author:** Dr. Ummara Razi, Center for Sustainable Development and Green Innovation Research, Bahçeşehir Cyprus University, and Institute of Energy Policy and Research (IEPRe), UNITEN Business School, Universiti Tenaga Nasional

[Website](https://ummararazi.github.io) · [LinkedIn](https://www.linkedin.com/in/dr-ummara-razi-45919752/) · [ORCID](https://orcid.org/0000-0002-5981-5736) · [Google Scholar](https://scholar.google.com/citations?user=3RthPUsAAAAJ)

## New to spatial econometrics?

Start with the **[Beginner's Guide](Beginners_Guide_Spatial_Econometrics.md)**. It explains what "spatial" means, why ordinary regression fails with location data, how the weights matrix works (with a worked example), the main models in plain language, and how to read spillover effects.

## Week 1: Foundations

`Week1_Spatial_Econometrics_US_Counties.ipynb` covers:

1. Spatial weights matrices: Queen contiguity, k-nearest neighbours, inverse distance
2. Global Moran's I, Moran scatterplot and LISA cluster maps
3. OLS with LM and robust LM diagnostics
4. SLX, SAR, SEM and SDM estimated by maximum likelihood, with LR tests
5. Direct, indirect (spillover) and total effects with simulated inference (LeSage and Pace, 2009)
6. Robustness of effects to the choice of W
7. The Gibbons and Overman (2012) critique and how causal designs answer it

Practice model: county median home value explained by income, population density and the share of homes heated by electricity.

## Data (free, no API key)

- County boundaries: US Census Bureau, Cartographic Boundary Files 2020 (1:20m)
- County attributes: American Community Survey 5-year estimates, 2018–2022 (Census API)

The notebook downloads both automatically.

> **You need a free Census API key.** Each user must get their own key, otherwise the Census server may block the download (especially on Google Colab) and you will see an error such as `JSONDecodeError`.
>
> 1. Request a key at https://api.census.gov/data/key_signup.html (free, about 2 minutes).
> 2. Click the **activation link** in the email from the Census Bureau.
> 3. In the notebook's data cell, paste it into `CENSUS_KEY = 'your-key-here'`.
>
> Keep your key private: do not share it or commit it to GitHub.

## How to run

**On your computer**

```bash
pip install -r requirements.txt
jupyter notebook Week1_Spatial_Econometrics_US_Counties.ipynb
```

**On Google Colab**

1. Open https://colab.research.google.com and choose **File → Upload notebook**.
2. Run the first cell after removing the `#` from the `%pip install ...` line.
3. Add your Census API key in the data cell, then choose **Runtime → Run all**.
4. Download the `outputs` folder before closing, as Colab deletes files when the session ends.

Outputs (tables as CSV, maps as PNG, the cleaned sample as GeoPackage) are saved to `outputs/`.

## Coming next

- Week 2: Model choice, SDEM and choosing W on economic grounds
- Week 3–4: Spatial panel models with fixed effects
- Week 5–6: Difference-in-differences with spatial spillovers and Conley standard errors

## Key references

- LeSage, J. and Pace, R.K. (2009). *Introduction to Spatial Econometrics*. CRC Press.
- Elhorst, J.P. (2014). *Spatial Econometrics: From Cross-Sectional Data to Spatial Panels*. Springer.
- Gibbons, S. and Overman, H. (2012). Mostly pointless spatial econometrics? *Journal of Regional Science*, 52(2), 172–191.
- Rey, S., Arribas-Bel, D. and Wolf, L. *Geographic Data Science with Python*.

## How to cite

If you use or adapt this material, please cite:

> Razi, U. (2026). *Spatial Econometrics in Python: US Counties* [Computer software and teaching materials]. GitHub. ORCID: https://orcid.org/0000-0002-5981-5736

```bibtex
@misc{razi2026spatial,
  author       = {Razi, Ummara},
  title        = {Spatial Econometrics in Python: US Counties},
  year         = {2026},
  howpublished = {GitHub repository},
  note         = {Teaching materials and code. ORCID: 0000-0002-5981-5736}
}
```

GitHub also shows a **"Cite this repository"** button (from `CITATION.cff`).

## Licence

MIT. See `LICENSE`.
