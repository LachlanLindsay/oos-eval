# Errata: known label issues in `data_full.json`

Found during an independent model-calibration audit that examined every row
where a high-accuracy classifier disagreed with the gold label at maximum
confidence, then verified each disagreement against this dataset's own usage
of the competing intent. Most disagreements confirmed the gold labels — the
taxonomy's fine distinctions (`reminder` vs `reminder_update`,
`last_maintenance` vs `oil_change_when`, etc.) are deliberate and applied
consistently, and the dataset holds up very well overall. The residue is
listed here.

**The data files are deliberately left untouched.** Six years of published
results are comparable because these bytes are frozen; this file just travels
beside them. Rows are given as `split[index]` into `data_full.json` (0-based).

## A duplicated utterance with contradictory labels

The same utterance appears in two splits with two different labels — and the
one from val also crosses the train/val boundary:

| row | utterance | label |
| --- | --- | --- |
| `train[7424]` | "what is on my to do list" | `todo_list` |
| `val[1011]` | "what is on my to do list" | `reminder` |

The `todo_list` labelling matches that intent's other rows ("what does my
to-do list look like", "list my to-do list").

## Rows whose label contradicts the dataset's own usage of another intent

| row | utterance | labelled | consistent with |
| --- | --- | --- | --- |
| `val[498]` | "allow me to turn on the lights" | `restaurant_reservation` | `smart_home` |
| `test[3881]` | "what is my current location" | `share_location` | `current_location` — every other `share_location` row names a person to tell |
| `test[392]` | "can you call the help desk line for my credit card company" | `replacement_card_duration` | `make_call` — `replacement_card_duration` rows all ask how long a replacement takes |
| `test[393]` | "how can i request a new credit card" | `replacement_card_duration` | `new_card` |

## Intent pairs with inconsistent labelling at the boundary

- **`ingredients_list` / `recipe`** — e.g. `train[10242]` "show me the recipe
  for broccoli cheddar soup" and `test[3065]` "give me a recipe for tacos" are
  labelled `ingredients_list`, while `recipe` contains "find a recipe for
  baked ziti".
- **`pto_used` / `pto_balance`** — e.g. `train[6193]` "how much paid time off
  do i have left" and `test[1844]` "how much of my pto is left" are labelled
  `pto_used`, while `pto_balance` contains "how many pto days do i have left"
  ("left" describes a balance).

## Scale

These plus a handful of genuinely ambiguous boundary rows amount to roughly
0.1–0.3% of the in-scope data — low by published benchmark standards
(cf. Northcutt et al., NeurIPS 2021, who measured ~3.3% average label error
across ten widely used test sets). Methodology and per-decision data:
<https://github.com/LachlanLindsay/is-jev-calibrated>.
