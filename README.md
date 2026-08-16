# ReviewLens

What Netflix users say on the Google Play Store, gathered automatically into a SWOT matrix once a month.

**Live dashboard:** https://apolitidou.github.io/netflix-swot-analysis/

## What it does

It takes Netflix reviews from the Google Play Store, splits each one into separate opinions, and labels each opinion as a strength, weakness, opportunity or threat. Then it finds the themes most people are talking about within each category and shows them on a dashboard, with the users' own reviews underneath.

The `index.html` reads a `data.json` file next to it. If it finds one, it shows the real data. That `data.json` is rebuilt on its own once a month.

## How it updates itself

Once a month:

1. The Kaggle dataset with the reviews refreshes.
2. A GitHub Action starts the notebook on Kaggle (it runs on a GPU).
3. The notebook classifies the new reviews, derives the themes, and writes a fresh `data.json`.
4. The Action downloads `data.json` and commits it to the repo.
5. GitHub Pages rebuilds the site and the dashboard shows the fresh data.

## The files

```
index.html                     the dashboard
data.json                      the data (written by the Action each month)
kaggle/
  kernel-metadata.json         points to the notebook that runs on Kaggle
  netflix-monthly.ipynb        the notebook that gets uploaded and run
.github/workflows/
  monthly-update.yml           the monthly schedule
```

## How the themes are built

There is no fixed list of themes. For each category, the system finds the opinions that recur across the most reviews, reads them, and proposes the themes that stand out. It then matches every opinion to its closest theme by meaning rather than by matching words. That way each theme title comes from what users actually write.

## Setting it up from scratch

1. Create a public repo and upload these files.
2. **GitHub Pages:** Settings -> Pages -> Source = Deploy from a branch -> `main` / root.
3. **Kaggle API token:** kaggle.com -> Account -> Create New API Token (downloads `kaggle.json`).
4. **Secrets** (repo -> Settings -> Secrets and variables -> Actions -> New secret):
   - `KAGGLE_USERNAME` is your username
   - `KAGGLE_KEY` is the key from `kaggle.json`
5. In `kernel-metadata.json`, set your own slug: `your-username/netflix-monthly`.

After that it runs on its own each month. You can also start it by hand from the Actions tab.

## What is behind it

A RoBERTa classifier, trained on reviews first labelled by a larger model and by hand, gives each opinion its SWOT category. The themes come from the data itself, and each theme's description is written from the reviews belonging to it.

Built as a diploma thesis for the MBADS master's programme at the University of Macedonia. It uses publicly available Google Play reviews for research and educational purposes. It is not affiliated with Netflix, Inc. The names and marks belong to their owners and are referenced only for identification.
