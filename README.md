# FIFA World Cup 2026 Match Predictor

A machine learning project that predicts **Home Win, Draw, and Away Win probabilities** for scheduled FIFA World Cup 2026 matches.

Given two teams, the project automatically identifies their scheduled fixture, including the match date, group, and stadium. It then uses historical international football results to generate outcome probabilities and a match-level visualization.

## Example Prediction

**Colombia vs Portugal**
**27 June 2026 · Group K · Miami Stadium**

| Outcome      | Probability |
| ------------ | ----------: |
| Colombia Win |       38.5% |
| Draw         |       25.2% |
| Portugal Win |       36.3% |

**Prediction:** Colombia Win
**Confidence:** TOSS-UP

The result is intentionally labelled `TOSS-UP` because Colombia’s predicted probability is only slightly higher than Portugal’s. The model output should be interpreted as a probability estimate, not a guaranteed match result.

---

## Model Performance

The model was evaluated using a time-based validation split to prevent future match information from entering the training data.

| Metric                             | Result |
| ---------------------------------- | -----: |
| Validation Accuracy                |  60.5% |
| Validation Log-Loss                |  0.860 |
| Baseline Log-Loss                  |  1.053 |
| Log-Loss Improvement over Baseline |  18.3% |

The lower log-loss compared with the baseline indicates that the model produces more informative probability estimates than predicting only the overall class distribution.

---

## How It Works

The project frames match prediction as a three-class classification problem:

* Home Win
* Draw
* Away Win

An **XGBoost multiclass classifier** is trained on historical international football results from 2006 onward.

For every match, the project engineers the following features:

* **Elo ratings:** Sequential team-strength ratings updated after every historical match.
* **Recent form:** Win rate over the last 5 and 10 matches.
* **Goal-difference trend:** Average goal difference across the previous 5 matches.
* **Rest days:** Days since each team’s previous match.
* **Head-to-head statistics:** Previous meetings, historical win rate, and goal difference.
* **Match context:** Neutral-venue indicator and tournament importance.

To reduce bias caused by team ordering, the model predicts the match in both possible team orders and averages the corresponding probabilities.

---

## Project Structure

```text
world_cup_predictions/
│
├── assets/
│   └── colombia_vs_portugal_prediction.png
│
├── data_cache/
│   └── fixtures.csv
│
├── predictions/
│   └── <match-date>/
│       └── viz_<team1>_vs_<team2>.png
│
├── world_cup_match_predictor.ipynb
├── predict_today.py
├── requirements.txt
├── .gitignore
├── README.md
└── LICENSE
```

* `predict_today.py` — Main command-line prediction script
* `world_cup_match_predictor.ipynb` — Interactive Jupyter Notebook implementation
* `data_cache/fixtures.csv` — FIFA World Cup 2026 fixture schedule
* `data_cache/results.csv` — Downloaded automatically on the first run
* `predictions/` — Generated probability charts
* `assets/` — Selected chart image used in this README

---

## Installation

```bash
git clone https://github.com/<your-github-username>/world_cup_predictions.git
cd world_cup_predictions
pip install -r requirements.txt
```

---

## Usage

### Command Line

Run a prediction by providing any two teams that have a fixture in `fixtures.csv`:

```bash
python predict_today.py Portugal Colombia
```

You can also run the script without arguments:

```bash
python predict_today.py
```

Then enter the two teams interactively.

### Jupyter Notebook

Open and run:

```text
world_cup_match_predictor.ipynb
```

In the notebook version, enter the two teams when prompted.

---

## Output

For each valid World Cup fixture, the project prints:

* Fixture date, group, and stadium
* Home win probability
* Draw probability
* Away win probability
* Most likely predicted outcome
* Confidence category: `LOCK`, `LEAN`, or `TOSS-UP`
* Upset flag if the model’s predicted favorite differs from the Elo favorite

It also saves a chart to:

```text
predictions/<match-date>/viz_<team1>_vs_<team2>.png
```

---

## What the Project Does Not Model

This is a historical team-level prediction model. It does not currently include:

* Player injuries or suspensions
* Confirmed starting lineups
* Player-level statistics
* Expected goals (xG)
* Betting odds
* Tactical or formation changes
* Weather, travel, crowd, or home-support effects
* Tournament incentives, such as needing only a draw to qualify

Football has high randomness and relatively few goals, so even a useful probability model will be wrong on many individual matches. The model should be assessed over many predictions using probability metrics such as log-loss, rather than by a single match outcome.

---

## Future Improvements

* Integrate FIFA rankings and player-level statistics
* Add expected-goals (xG) data
* Incorporate injuries, suspensions, and predicted lineups
* Add betting odds as an external market-based feature
* Build a Poisson goal model to predict scorelines and better represent draws
* Compare XGBoost against Logistic Regression, Random Forest, and Elo-only baselines
* Add probability calibration plots and reliability analysis
* Deploy the predictor as a Streamlit web application

---

## Data Sources

Historical international football match results are downloaded automatically from the open-source [martj42/international_results](https://github.com/martj42/international_results) dataset.

The dataset provides historical international match information including:

* Match date
* Home and away teams
* Scores
* Tournament
* Neutral-venue indicator

The FIFA World Cup 2026 fixture schedule is stored locally in `data_cache/fixtures.csv`.

---

## License

This project is released under the MIT License.
