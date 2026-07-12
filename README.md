# ReviewLens — αυτοματοποιημένη μηνιαία ανανέωση

Το dashboard (`index.html`) διαβάζει ένα διπλανό αρχείο **`data.json`** όταν
φορτώνει. Αν το βρει, δείχνει τα πραγματικά δεδομένα (badge **LIVE**)· αν όχι,
πέφτει στα ενσωματωμένα demo δεδομένα (badge **DEMO**). Η αυτοματοποίηση απλώς
φροντίζει ώστε κάθε μήνα να παράγεται φρέσκο `data.json` και να δημοσιεύεται εδώ.

## Ροή (μία φορά τον μήνα)

1. Το Kaggle dataset `netflix-reviews-playstore-daily-updated` ανανεώνεται μόνο του.
2. Το GitHub Action ξεκινά το notebook στο Kaggle (GPU).
3. Το notebook γράφει `data.json` στο output (κελί 21).
4. Το Action κατεβάζει το `data.json` και το κάνει commit στο repo.
5. Το GitHub Pages ξαναχτίζει το site — το dashboard δείχνει τα νέα δεδομένα.

## Δομή του repo

```
index.html                     # το dashboard (μετονομασμένο)
data.json                      # ανανεώνεται από το Action (μπορεί να λείπει στην αρχή)
kaggle/
  kernel-metadata.json         # δείχνει στο notebook σου στο Kaggle
  netflix-v2.ipynb             # το notebook που ανεβαίνει/τρέχει
.github/workflows/
  monthly-update.yml           # ο μηνιαίος προγραμματισμός
```

## Πρώτη φορά — setup (5 βήματα)

1. **Φτιάξε ένα public repo** και ανέβασε αυτά τα αρχεία.
2. **GitHub Pages**: Settings → Pages → Source = `Deploy from a branch` → `main` / root.
   Το site θα βγει στο `https://<user>.github.io/<repo>/`.
3. **Kaggle API token**: kaggle.com → Account → *Create New API Token* (κατεβάζει `kaggle.json`).
4. **Secrets** (repo → Settings → Secrets and variables → Actions → New secret):
   - `KAGGLE_USERNAME` = το username σου
   - `KAGGLE_KEY` = το `key` από το `kaggle.json`
5. Στο `kernel-metadata.json` και στο `monthly-update.yml` άλλαξε το
   `YOUR_KAGGLE_USERNAME/netflix-v2` στο δικό σου slug.

Δοκίμασέ το χειροκίνητα: Actions → *Monthly SWOT refresh* → **Run workflow**.

## Σημαντικά

- Το dashboard **πρέπει** να σερβίρεται μέσω http(s) (GitHub Pages). Αν το ανοίξεις
  με διπλό κλικ (`file://`), ο browser μπλοκάρει το `fetch('data.json')` και θα
  μείνει στο DEMO.
- Για τον **μηνιαίο** run κράτησέ τον *inference-only*: φόρτωσε το εκπαιδευμένο
  RoBERTa από το Hugging Face Hub αντί να το ξαναεκπαιδεύεις — αλλιώς κάθε run
  αργεί ώρες και μπορεί να ξεπεράσει το όριο των 6 ωρών του runner.
- Το GitHub Pages έχει ~10 λεπτά CDN cache· το νέο `data.json` μπορεί να αργήσει
  λίγο να φανεί.
