# Predicting Future Soccer Player Minutes Using Machine Learning

# Abstract

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

## Dataset Description

My dataset was organized at the player-season level, meaning that each row represented one player in one league season.

Here is a snipit of the player data avaliable coming from the La Liga 2019-2020 season. Not all columns and rows from that file are in the image.

![La Liga Player Data 2019-2020](La%20Liga%20Player%20Data%202019-2020.png)

One issue I encountered was that some players transferred clubs during the same season. This created duplicate player-season rows because the same player could appear once for each club they played for. To solve this, I merged those rows into one player-season observation. For the player’s team, I assigned the club where the player played the most minutes that season.

I also added team-level information from league tables for each season. This allowed me to include team context, such as team strength and performance, alongside individual player statistics.

Here is the full leaderboard from the Serie A 2022-2023 season.

![Italy 2022-23 Leaderboard](Italy%202022-23%20Leaderboard.png)

---

## Exploratory Data Analysis

Before building the model, I explored the player-level data to better understand the distribution of minutes across player-seasons.

![Minutes Distribution](Minutes%20Distribution.png)

The distribution shows that most player-seasons involve relatively low minutes, while a smaller group of players accumulates very high minutes. This makes sense because most squads have many rotation players, substitutes, and players who appear only occasionally, while only a limited number of regular starters play close to a full season. The distribution is therefore right-skewed.


---


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

The model achieved an \(R^2\) value of approximately \(0.301\), meaning that it explained about \(30.1\%\) of the variation in future player minutes. This suggests that the model was meaningfully predictive, but not highly accurate. In the context of soccer minutes prediction, this is still a useful result because future playing time is affected by many unpredictable factors, including injuries, transfers, tactical changes, suspensions, fixture congestion, and managerial preferences.

The model’s MAE was approximately \(675.81\) minutes. This means that, on average, the model’s prediction was about \(676\) minutes away from a player’s actual season minutes total. Since a full league season contains a maximum of \(3420\) minutes, the average error was about:

\[
\frac{675.81}{3420} \approx 19.8\%
\]

of a full league season.

The RMSE was \(829.56\), which was higher than the MAE. This shows that the model made some large prediction errors. These larger errors likely occurred in cases where a player’s role changed suddenly, such as a young player breaking into the first team, a starter losing their place, a player transferring clubs, or a player missing time due to injury.

---

## Performance by Position

| Position | Sample Size | \(R^2\) | MAE | RMSE |
|---|---|---|---|---|
| GK | 44 | 0.506 | 730.89 | 880.74 |
| DF | 195 | 0.234 | 708.30 | 861.26 |
| MF | 283 | 0.259 | 646.53 | 798.60 |
| FW | 134 | 0.315 | 632.69 | 771.64 |

The model performed differently across positions. Goalkeepers had the highest \(R^2\), with a value of \(0.506\). This suggests that the model explained goalkeeper minutes better than it explained outfield player minutes. This makes sense because goalkeeper roles are usually more stable: teams often have a clear first-choice goalkeeper, and substitutions are rare compared with outfield positions.

However, the goalkeeper sample size was much smaller, with only \(44\) test observations. Because of this, the goalkeeper result should be interpreted carefully. A smaller sample can make the performance estimate less stable.

For outfield players, the model performed best for forwards by \(R^2\), with a value of \(0.315\). Midfielders and defenders were harder to predict, with \(R^2\) values of \(0.259\) and \(0.234\). This likely reflects the greater tactical flexibility and rotation among outfield positions. Defenders, midfielders, and forwards may be substituted more often, rotated based on opponent matchups, or affected by changes in formation.

The model had the lowest MAE for forwards and midfielders. This means that although goalkeeper minutes had the highest explained variance, the average prediction error was smaller for some outfield positions. Overall, the positional results show that prediction accuracy depends heavily on role stability, sample size, and the tactical demands of each position.

---

## Feature Importance

![Feature Importances](Feature%20Importances.png)

The feature importance results show that previous playing time was the strongest predictor of future minutes. The most important feature was \(\text{Min}_{t-1}\), meaning minutes from the previous season. This supports the idea that past manager trust, availability, and squad role are highly informative when predicting future playing time.

Age was the second most important feature, which suggests that the model captured some relationship between player development, peak years, and decline. This is important because minutes are not only determined by past role, but also by where a player is in their career arc.

The lagged minutes variables, including \(\text{Min}_{t-1}\), \(\text{Min}_{t-2}\), and \(\text{Min}_{t-3}\), were all among the most important features. This shows that the model relied strongly on a player’s historical usage across multiple seasons. The importance of \(\text{Min Trend}\) also suggests that changes in a player’s role over time were useful for predicting whether their future minutes would increase or decrease.

Team-context variables, such as \(\text{On-Off}\), \(\text{PPM}\), \(\text{Team GA}\), and \(\text{Team GF}\), were also included among the important predictors. These features were less influential than previous minutes, but they still added context about team performance while the player was on the pitch. This suggests that the model was not only learning how much a player had played in the past, but also some information about the team environment surrounding those minutes.

Overall, the feature importance results support the modeling approach. Future minutes were mostly driven by historical playing time, age, and recent role trends, while team-context variables provided additional predictive information.


---

# Conclusions

Overall, the model demonstrated that future player minutes can be predicted to a meaningful extent using historical playing-time variables, age, and team-context statistics. The model achieved an \(R^2\) of approximately \(0.301\), meaning it explained about \(30.1\%\) of the variation in future minutes. While this level of accuracy is not high enough to perfectly predict individual player seasons, it is still valuable because soccer minutes are influenced by many factors that are difficult to observe in public data.

The model’s average error was approximately \(676\) minutes, or about \(19.8\%\) of a full \(3420\)-minute league season. This means the model can give a useful estimate of a player’s expected role, but it should not be treated as a precise prediction. In practical terms, the model is better suited for identifying broad usage patterns, such as likely starters, rotation players, and low-minute squad players, rather than predicting exact season-minute totals.

The positional results showed that goalkeeper minutes were the most explainable by the model, likely because goalkeeper roles are more stable and less affected by substitutions. Outfield positions were more difficult to predict because defenders, midfielders, and forwards experience more rotation, tactical variation, and competition for places. This suggests that future versions of the model could benefit from separate position-specific models.

The feature importance results showed that previous minutes were the strongest predictor of future minutes. This confirms that historical playing time is a strong proxy for manager trust, availability, and squad status. Age and minutes trend were also important, suggesting that the model captured both career-stage effects and changes in a player’s role over time.

Despite its limitations, the model shows that machine learning and time-based feature engineering can provide useful insight into player usage trends. The results could support recruitment, squad planning, loan decisions, and player development analysis. However, the model should be used alongside scouting knowledge and contextual information because injuries, transfers, managerial changes, and sudden player development can cause large deviations from historical patterns.

---

# Example Players

## Rodri Example


## Rodri Case Study
![Rodri Example](Rodri%20Example.png)

Rodri was the model’s largest error in the test set. The model predicted that he would play approximately \(3001.3\) minutes in the 2024-25 season, but he only played \(73.0\) minutes, creating an absolute error of \(2928.3\) minutes.


This example reveals one of the most important limitations of the model: it is strong at learning historical usage patterns, but it cannot account for unexpected injuries. Rodri’s input variables made him look like one of the safest high-minute predictions in the dataset. In the previous three seasons, he played \(3982\), \(4512\), and \(3765\) minutes, showing that he had been one of Manchester City’s most trusted and heavily used players. His strong points-per-match values and positive on-off metrics also suggested that he was highly important to the team when on the pitch.

However, Rodri suffered a serious knee injury in September 2024, and Manchester City later confirmed that he had undergone surgery for an ACL and meniscus injury and would miss the rest of the season. Because the model did not include injury information, it had no way to anticipate such a sudden drop in playing time. As a result, the model treated Rodri as a stable elite midfielder whose future minutes would remain close to his previous workload.

The Rodri case study shows that the model is most reliable when player roles remain relatively stable from season to season. It can identify players with strong historical usage, manager trust, and consistent roles, but it struggles with sudden shocks that are not present in the data. Long-term injuries, major tactical changes, transfers, and unexpected benchings can all cause a player’s actual minutes to differ greatly from the model’s prediction. Therefore, the model should be interpreted as a baseline projection of expected playing time rather than a complete forecast of every player’s season.

---

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


