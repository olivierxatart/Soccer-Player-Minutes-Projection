# Predicting Future Soccer Player Minutes Using Machine Learning

## Abstract

This project develops a machine learning model to predict future soccer player minutes using seven seasons of data from the Premier League, La Liga, and Serie A. The model uses lagged playing-time statistics, age-related variables, and team-context metrics to forecast future league minutes across multiple positions. Predictive performance is evaluated using \(R^2\), mean absolute error (MAE), and root mean squared error (RMSE). The project also explores practical applications in squad planning, recruitment, and player development.

---

# Background

In soccer, many metrics are used to evaluate player quality, but one of the most important and difficult to model is minutes played. Total minutes across a league season can reveal how consistently a player is selected, how physically available they are, and how much trust they earn from their manager. While minutes are not a perfect measure of ability, they often reflect a player’s role within a team. For example, two defenders on the same club may compete for the same position, but the more reliable or higher-quality player may receive substantially more minutes over the course of the season.

Because minutes are influenced by both performance and context, they are a complex but valuable target variable. A player’s minutes may be affected by age, injuries, team strength, squad competition, tactical role, and previous playing time. This makes future minutes prediction an interesting sports analytics problem.

---

# Project Idea

For this project, I built a model that predicts how many minutes a player will play in a future league season. I chose to focus on the Premier League, La Liga, and Serie A because they are all top European leagues with 38-game seasons, making total minutes more comparable across competitions.

My data source was FBref. I collected player and team-level data from the 2018-19 season through the 2024-25 season, giving me seven seasons of data. Earlier planning for the project focused on collecting standard, shooting, miscellaneous, and playing-time statistics, with minutes played as the primary target variable.

---

# Exploratory Data Analysis and Feature Selection

A major challenge in this project was deciding which variables should be used to predict future minutes. I purposely avoided highly position-specific performance metrics because they are not equally meaningful across all players. For example, goals are useful for evaluating forwards and attacking midfielders, but they are much less informative for center backs, fullbacks, or goalkeepers.

Because I wanted the model to generalize across positions, I focused on more position-agnostic indicators.

## Selected Features

| Feature | Why it may help predict future minutes |
|---|---|
| **Age** | Captures development, peak years, and possible decline |
| **Previous Minutes Played** | Strong proxy for manager trust, availability, and squad role |
| **Minutes per Match Played** | Shows whether a player usually starts matches or appears briefly as a substitute |
| **Minutes Trend** | Captures whether a player’s role is increasing or decreasing over time |
| **Points per Match When the Player Played** | Measures team results during the player’s appearances |
| **Team Goals For While Player Was On the Pitch (OnG)** | Captures attacking team context during the player’s minutes |
| **Team Goals Against While Player Was On the Pitch (OnGA)** | Captures defensive team context during the player’s minutes |
| **Goal Difference per 90 While Player Was On the Pitch** | Measures overall team performance while the player was on the pitch |

---

# Methodology

## Why I Used a Time-Based Modeling Approach

I used a time-based supervised learning approach because the goal of the project is to use past player seasons to predict future player minutes. Since player development, decline, and role changes occur over time, it was important that the model used historical information from previous seasons rather than treating every player-season as completely independent.

For example, if a young player played very few minutes in one season but became a regular starter the following season, the model should recognize that the more recent season is more relevant for predicting the player’s future role.

To account for this, I created lagged variables, meaning that each player’s previous-season statistics were used as predictors for their next-season minutes.

---

## Dataset Description

My dataset was organized at the player-season level, meaning that each row represented one player in one league season.

One issue I encountered was that some players transferred clubs during the same season. This created duplicate player-season rows because the same player could appear once for each club they played for. To solve this, I merged those rows into one player-season observation. For the player’s team, I assigned the club where the player played the most minutes that season.

I also added team-level information from league tables for each season. This allowed me to include team context, such as team strength and performance, alongside individual player statistics.

---

## Feature Engineering

The most important feature engineering step was creating lagged variables.

Instead of using a player’s current-season statistics to predict current-season minutes, I used previous seasons to predict future seasons. This helped avoid data leakage, where the model accidentally learns from information that would not be available at prediction time.

The trend feature was defined as:

\[
\text{Minutes Trend} = \text{Minutes}_{t-1} - \text{Minutes}_{t-2}
\]

This allowed the model to capture whether a player’s role was increasing or decreasing over time.

---

# Model Evaluation Metrics

The model was evaluated using \(R^2\), mean absolute error (MAE), and root mean squared error (RMSE).

## Mean Absolute Error(MAE)

\[
MAE = \frac{1}{n}\sum_{i=1}^{n}|y_i - \hat{y}_i|
\]

MAE measures the average absolute difference between predicted minutes and actual minutes.

---

## Root Mean Squared Error(RMSE)

\[
RMSE = \sqrt{\frac{1}{n}\sum_{i=1}^{n}(y_i - \hat{y}_i)^2}
\]

RMSE penalizes larger prediction errors more heavily than MAE.

---

## Coefficient of Determination

\[
R^2 = 1 - \frac{\sum (y_i - \hat{y}_i)^2}{\sum (y_i - \bar{y})^2}
\]

\(R^2\) measures how much variance in player minutes is explained by the model.

---

# Results

## Overall Model Performance

| Metric | Value |
|---|---|
| \(R^2\) | 0.301 |
| MAE | 675.81 |
| RMSE | 829.56 |

---

## Performance by Position

| Position | Sample Size | \(R^2\) | MAE | RMSE |
|---|---|---|---|---|
| GK | 44 | 0.506 | 730.89 | 880.74 |
| DF | 195 | 0.234 | 708.30 | 861.26 |
| MF | 283 | 0.259 | 646.53 | 798.60 |
| FW | 134 | 0.315 | 632.69 | 771.64 |

---

## Model Visualization

![Minutes Model Results](Premier%20League%20Player%20Stats%20Predictions/Write-Up%20Images/Minutes%20Model%20Results.png)

*Figure 1: Overall model performance and positional evaluation metrics.*

---

# Conclusions

Although the model was not perfectly accurate, the results demonstrated that future player minutes can be predicted to a meaningful extent using historical playing-time and team-context variables. The model achieved an MAE value of approximately 675, meaning that on average the model was of 675 minutes with its predictions. Given the unpredictability of injuries, managerial changes, transfers, tactical adjustments, and player development, this level of predictive performance suggests that historical usage patterns still contain significant information about future playing time. The model performed especially well for outfield players, since outfield positions had a larger sample size than goalkeepers. Overall, the project demonstrates that machine learning and time-based feature engineering can provide useful insight into player usage trends, even in a highly unpredictable environment such as professional soccer.

# Example Players


# Practical Applications

This model could be useful for professional soccer clubs, including coaches, sporting directors, recruitment analysts, and player agents, because it helps estimate how many minutes a player is likely to play in a future season.

For coaches and technical staff, predicted minutes can help with squad planning and player development. If a young player’s projected minutes are increasing over time, the model may indicate that the player is progressing toward a larger first-team role. Coaches could use this information to decide whether a player should remain with the senior team, go on loan for additional playing time, or continue developing in academy competitions.

Recruitment departments and sporting directors could use the model during transfer windows to estimate how much a transfer target is likely to contribute in future seasons. A club may avoid signing players projected to have limited playing time while prioritizing players who appear capable of earning a consistent role.

Player agents could also use the model during contract negotiations. If the model predicts that a player is likely to earn substantial playing time, the agent could use that information to argue that the player will have significant value to the club in future seasons.

---

# Limitations and Conclusions

The model has several important limitations. First, it does not account for injuries, which are one of the largest factors affecting player minutes in professional soccer. A player who suffers a long-term injury would likely underperform their predicted minutes total significantly, while their replacement or backup may play far more than expected.

The model also only includes data from the Premier League, La Liga, and Serie A, which may limit its ability to accurately predict minutes for players arriving from leagues with different competition levels or tactical styles.

In addition, the model cannot fully capture managerial or tactical changes. A new coach may favor different formations, player profiles, or rotation strategies that historical data cannot anticipate. Younger players are also difficult to predict because development is not always linear, and some players experience sudden breakout seasons with much larger roles than previous seasons suggest.

Transfers between clubs may further reduce predictive accuracy because squad depth and competition for positions vary greatly between teams.

Despite these limitations, the project demonstrates that historical playing time, age, and team-context variables can still provide meaningful insight into future player usage. The results suggest that machine learning and time-series approaches can support recruitment, squad planning, and player development decisions in professional soccer.


