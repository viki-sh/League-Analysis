# Was it Over Before it Started?
A Statistical Analysis of Pre-Game Factors in League of Legends

Author: Viki Shi

# General Introduction 
Welcome to League of Legends—a video game developed and published by Riot Games, widely regarded as one of the greatest video games ever made. Beyond its popularity as a game, League of Legends is the world’s largest esport, with professional tournaments drawing hundreds of millions of viewers.

This analysis examines eight years of professional League of Legends matches to explore a central question:

***Do pre-game decisions impact match outcomes?***

In League of Legends, two teams of five players compete to destroy the enemy’s Nexus, their home base. With over 150 playable champions, in-game items, a gold economy, jungle monsters, and various team roles, the game is highly complex. However, for the purpose of this analysis, we focus solely on pre-game factors, which include:

- Role selection
- Champion selection
- Champion bans
- Team side assignment (Red or Blue)

By analyzing how these pre-game choices influence match outcomes, we aim to determine whether a game is significantly influenced before it even begins.

## Roles and Champion Selection
There are five primary roles in League of Legends:
- Top – Typically tanky champions with high durability.
- Jungle – Roaming champions who secure objectives and assist teammates.
- Mid – Burst damage or utility champions who control the center of the map.
- Bot (ADC) – Ranged damage dealers who scale into the late game.
- Support – Champions that provide healing, shielding, or crowd control to assist the ADC.
There are currently 170 champions in League of Legends, each with unique abilities and playstyles suited for specific roles.

## What is a Counter? 
A counter is a champion that has a strategic advantage over another champion. Counters can:
- Exploit an opponent’s weaknesses
- Reduce an enemy’s effectiveness in lane
- Provide stronger matchups throughout the game
For example, a champion with strong crowd control abilities might counter a mobile assassin who relies on quick movements.

## What is a Ban?
Before the game begins, each player has the opportunity to ban one champion, preventing both teams from selecting that character. Bans are often used to:

- Remove strong meta champions
- Prevent opponents from selecting favorable matchups
- Target specific players’ comfort picks

## What Are the Sides?
League of Legends is played on a symmetrical map called Summoner’s Rift, which has three lanes and a jungle area. Teams are randomly assigned to one of two sides:
- Blue Side (bottom-left)
= Red Side (top-right)
While most players prefer the Blue Side, as playing upward is considered visually easier, there is no inherent  advantage between the two sides—assignments are purely random.

## Dataset Overview 
This study analyzes 86,899 professional League of Legends matches, extracting key variables to measure the impact of pre-game choices.
- **Key Columns:**
- `year`: Records the year a game was played  
- `gameid`: A unique identifier for each game. Shared by two teams
- `league` : Specifies the league tournament in which the match took place. There are 110 unique leagues in our dataset. 
- `teamname` : The name of the team competing. There are 1479 unique teams in our dataset
- `side` : The side distinguishes the 'red' and 'blue' teams 
- `ban1` : The first ban of the respective team, in the form of a champion's name
- `ban2`: The second ban of the respective team, in the form of a champion's name
- `ban3`: The third ban of the respective team, in the form of a champion's name
- `ban4`: The fourth ban of the respective team, in the form of a champion's name
- `ban5` : The fifth ban of the respective team, in the form of a champion's name
- `pick1`: The first pick of the respective team, in the form of a champion's name
- `pick2`: The first pick of the respective team, in the form of a champion's name
- `pick3`: The first pick of the respective team, in the form of a champion's name
- `pick4`: The first pick of the respective team, in the form of a champion's name
- `pick5`: The first pick of the respective team, in the form of a champion's name
- `result`: The outcome of a match. 1 indicates the team won, 0 indicates the team lost.
- `num_counters_picked` : The number champion picks that counter any of the opposing team picks, between 1-5. (Equivelent to choosing your opponent's weakness)
- `num_counters_banned` : The number champion bans that counter any of the respective team picks, between 1-5. (Equivelent to removing your own weakness)
- `PGA` : "Pre-game Advantage", The sum of `num_counters_picked` and `num_counters_banned`, between 1-10. 
- `higher_PGA` : Indication of which team has a higher `PGA`. 1 if yes, 0 if no. There are no ties, as those rows have been dropped from the dataset
- `mean_champ_wr`: The mean win rate of all picks in a team
- `mean_team_wr`: The mean win rate of the team playing 

Additionally, we scraped the following website https://www.counterstats.net/ to create our own dataframe consisting of: 

- `Champion` : Name of champion
- `counters champ` : The top 5 champions that counter `Champion`

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning 
Our dataset originates from Oracle’s Elixir, a comprehensive source of professional League of Legends esports data, covering thousands of matches from top-tier leagues such as the LCS, LEC, LCK, and LPL.

For this analysis, we selected data from the years 2017, 2019, 2020, 2021, 2022, 2024, and 2025. The excluded years only contained three bans per team, whereas we wanted to analyze games with all five bans consistently across all observations.

### Preprocessing Steps

Dropped in-game statistics
- We removed columns related to in-game performance (e.g., kills, gold, game objectives) since our focus is exclusively on pre-game factors.

Filtered out rows with equal PGA
- Matches where both teams had the same Pre-Game Advantage (PGA) were dropped. This ensures we can accurately assess whether a higher PGA correlates with a greater likelihood of winning.

### Newly Added Features
To enhance our analysis, we created the following five additional columns:

- `num_counters_picked` : Using our web-scraped counter data, we checked each opponent’s champion picks. If a team selected a champion that countered an opponent’s pick, we incremented this value by 1 (up to a maximum of 5).
- `num_counters_banned` : Similarly, we referenced the counter data to determine if a team’s banned champions countered any of their opponent’s picks. If so, we incremented this value by 1 (up to a maximum of 5).
- `PGA` : Defined as the sum of num_counters_picked and num_counters_banned. This metric serves as an indicator of how favorable a team's pre-game decisions were.
- `higher_PGA` : A binary variable indicating whether a team had a higher PGA than their opponent (1 = Yes, 0 = No). This allows us to examine the relationship between PGA superiority and match outcomes.
- `mean_champ_wr` : Calculated by determining each champion’s overall win rate across all games in the dataset. Then, for each match, we computed the average win rate of all five picked champions.
- `mean_team_wr` : Derived by calculating each team’s historical win rate

Our added features enhance the analysis by quantifying the impact of pre-game decisions on match outcomes.

- `num_counters_picked` & `num_counters_banned` measure strategic drafting by tracking how many counters a team selects or removes.
- `PGA` & `higher_PGA` quantify overall pre-game advantage, allowing us to test whether a higher PGA correlates with winning.
- `mean_champ_wr` & `mean_team_wr` provide historical win rate context, helping assess whether strong past performance influences results.

Our head of our final dataframe is as follows: 

|   year | gameid    | league   | teamname            | side   | ban1    | ban2     | ban3     | ban4       | ban5    | pick1   | pick2   | pick3      | pick4   | pick5      |   num_counters_picked |   num_counters_banned |   PGA |   higher_PGA |   mean_champ_wr |   mean_team_wr |   result |
|-------:|:----------|:---------|:--------------------|:-------|:--------|:---------|:---------|:-----------|:--------|:--------|:--------|:-----------|:--------|:-----------|----------------------:|----------------------:|------:|-------------:|----------------:|---------------:|---------:|
|   2017 | 1506-1540 | LPL      | I May               | Blue   | Syndra  | Malzahar | Ashe     | Karma      | Poppy   | Maokai  | Kha'Zix | Cassiopeia | Varus   | Tahm Kench |                     0 |                     2 |     2 |            1 |        0.508666 |       0.438776 |        1 |
|   2017 | 1506-1540 | LPL      | Royal Never Give Up | Red    | Camille | Rengar   | Zyra     | Elise      | Rek'Sai | Kled    | Lee Sin | Ryze       | Caitlyn | Nautilus   |                     0 |                     0 |     0 |            0 |        0.493086 |       0.598582 |        0 |
|   2017 | 1506-1541 | LPL      | I May               | Blue   | Syndra  | Malzahar | Ashe     | Rek'Sai    | Kha'Zix | Maokai  | Lee Sin | Corki      | Caitlyn | Thresh     |                     0 |                     0 |     0 |            0 |        0.510833 |       0.438776 |        1 |
|   2017 | 1506-1541 | LPL      | Royal Never Give Up | Red    | Rengar  | Camille  | Varus    | Cassiopeia | Orianna | Trundle | Rumble  | Ryze       | Jhin    | Zyra       |                     1 |                     0 |     1 |            1 |        0.492558 |       0.598582 |        0 |
|   2017 | 1507-1544 | LPL      | Invictus Gaming     | Blue   | Jayce   | Elise    | Malzahar | Kha'Zix    | Lee Sin | Singed  | Rengar  | LeBlanc    | Varus   | Tahm Kench |                     0 |                     1 |     1 |            0 |        0.49238  |       0.523126 |        1 |

## Univariate Analysis 
To begin our analysis, we examine the distribution of Pre-Game Advantage (PGA) across all games. A PGA of 0 indicates that a team neither counter-picked nor counter-banned, while a PGA of 10 would mean that a team countered every single champion possible.

<iframe
  src="uni.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Notably, PGA values do not exceed 8, due to the structured drafting process in professional League of Legends. The champion selection and banning process follows this sequence:

- Both teams ban 5 champions each

- **Blue** team will pick 1 champion
- **Red** team will pick 2 champions 
- **Blue** team will pick 2 champions
- **Red** team will pick 2 champions
- **Blue** team will pick 2 champion
- **Red** team will pick 1 champion

This format balances counter-picking opportunities, preventing one team from always picking first and the other from always countering. As a result, reaching a PGA of 10 is extremely rare, as it would require a team to deliberately choose champions that their opponents counter, which is unstrategic. 

<iframe
  src="uni2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


It seems that that there are more teams with PGA of at least 1, than none at all. Meaning, most teams either counter picked the enemy or banned one of their counters. This leads us to believe that players are considering the affect of counter matchups


## Multivariate Analysis 
Lets now see the affect on wins. 

<iframe
  src="bi.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

At first glance, PGA alone does not appear to strongly correlate with winning. However, this plot only shows general win rates for different PGA values—it does not assess whether having a higher PGA than the opposing team impacts victory rates.

To analyze this, we compare the win rates of teams with a higher PGA than their opponent.

<iframe
  src="bi2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The results reveal a 50/50 split, indicating that having a higher PGA does not significantly increase a team’s chances of winning. 

## Interesting Aggregates
To further explore the impact of pre-game factors on match outcomes, we calculated the mean aggregate values of key variables for both winning and losing teams. The results suggest that pre-game factors influence match outcomes.

|   result |   higher_PGA |   num_counters_picked |   num_counters_banned |   mean_champ_wr |   mean_team_wr |     blue |      red |
|---------:|-------------:|----------------------:|----------------------:|----------------:|---------------:|---------:|---------:|
|        0 |     0.49519  |              0.673699 |              0.643978 |        0.499192 |       0.471924 | 0.469407 | 0.530593 |
|        1 |     0.504811 |              0.675843 |              0.667331 |        0.500674 |       0.527778 | 0.530596 | 0.469404 |

While these aggregates support the idea that pre-game choices play a role in match outcomes, the differences are subtle. This suggests that while drafting strategy matters, in-game execution and adaptability likely have a greater influence on winning.

# Assessment of Missingness
## NMAR Analysis 
Upon examining our dataset, we identified missing values in the following columns:
`teamname`, `ban1`, `ban2`, `ban3`, `ban4`, `ban5`

The columns `ban1` through `ban5` contain missing values because players have the option to skip banning champions in a League of Legends game. Our preliminary analysis suggests that the missingness in these columns does not correlate with other observed variables, leading us to believe that the missing data is due to in-game choices rather than external factors, and as a result is Not Missing At Random (NMAR)

## Missingness Dpendency 
In this part, we are going to test if the missingness of `teamname` column depends on two other columns; `year` and `league`. The significance level for both permutation tests is 0.5, and the test statistic is Total Variance Distance (TVD).


First, we perform a permutation test on `teamname` and `year`

**Null Hypothesis**: Distribution of `teamname` when `year` is missing is the same as the distribution of `teamname` when `year` is not missing.

**Alternative Hypothesis**:  Distribution of `teamname` when `year` is missing not the same as the distribution of `teamname` when `year` is not missing.

Below is the observed distribution of `teamname` when `year` is missing and not missing.

|   year |   team_name_missing = False |   team_name_missing = True |
|-------:|----------------------------:|---------------------------:|
|   2017 |                  0.0924056  |                   0        |
|   2018 |                  0.00296622 |                   0        |
|   2019 |                  0.134793   |                   0        |
|   2020 |                  0.160153   |                   0        |
|   2021 |                  0.197978   |                   0.681818 |
|   2022 |                  0.215255   |                   0.318182 |
|   2023 |                  0.00613622 |                   0        |
|   2024 |                  0.157504   |                   0        |
|   2025 |                  0.0328095  |                   0        |

<iframe
  src="mm1-year.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The p-value is less than the 0.5 significance level, and so we reject the null hypothesis. Thus, we can conclude that the missingness of `teamname` depends on the `year`.



Next, lets perform a permutation test on `teamname` and `league`

**Null Hypothesis**: Distribution of `teamname` when `league` is missing is the same as the distribution of `teamname` when `league` is not missing.

**Alternative Hypothesis**:  Distribution of `teamname` when `league` is missing not the same as the distribution of `teamname` when `league` is not missing.

Below is the observed distribution of `teamname` when `league` is missing and not missing.

| league          |   team_name_missing = False |   team_name_missing = True |
|:----------------|----------------------------:|---------------------------:|
| AC              |                 0.000656643 |                   0        |
| AL              |                 0.00400779  |                   0        |
| AOL             |                 0.000634001 |                   0        |
| ASCI            |                 0.001268    |                   0        |
| BIG             |                 0.00600036  |                   0        |
| BL              |                 0.0033285   |                   0        |
| BM              |                 0.0039625   |                   0        |
| BRCC            |                 0.00595508  |                   0        |
| CBLOL           |                 0.0214654   |                   0        |
| CBLOLA          |                 0.0122272   |                   0        |
| CDF             |                 0.00246807  |                   0        |
| CISC            |                 0.000588715 |                   0        |
| CK              |                 0.0151707   |                   0        |
| CLS             |                 0.00312472  |                   0        |
| CT              |                 0.00267186  |                   0        |
| CU              |                 0.00355493  |                   0        |
| DCup            |                 0.00609093  |                   0        |
| DDH             |                 0.0119781   |                   0        |
| DL              |                 0.00310207  |                   0        |
| EBL             |                 0.0124762   |                   0        |
| EBLPA           |                 0.000656643 |                   0        |
| EGL             |                 0.00151707  |                   0        |
| EL              |                 0.00226429  |                   0        |
| EM              |                 0.00697401  |                   0        |
| EPL             |                 0.0026945   |                   0        |
| ESLOL           |                 0.00896658  |                   0        |
| EU CS           |                 0.00219636  |                   0        |
| EU LCS          |                 0.00638529  |                   0        |
| EUM             |                 0.0135178   |                   0        |
| EWC             |                 0.000271715 |                   0        |
| GL              |                 0.00323793  |                   0        |
| GLL             |                 0.0153066   |                   0        |
| GLLPA           |                 0.00144914  |                   0        |
| GPL             |                 0.00113214  |                   0        |
| GSG             |                 0.000815144 |                   0        |
| HC              |                 0.00898922  |                   0        |
| HLL             |                 0.00122272  |                   0        |
| HM              |                 0.01268     |                   0        |
| HS              |                 0.001268    |                   0        |
| HW              |                 0.00303415  |                   0        |
| IC              |                 0.00224164  |                   0        |
| IEM             |                 0.000588715 |                   0        |
| KeSPA           |                 0.00346436  |                   0        |
| LAS             |                 0.0132687   |                   0        |
| LCK             |                 0.0505615   |                   0        |
| LCKC            |                 0.020922    |                   0        |
| LCL             |                 0.00946472  |                   0        |
| LCO             |                 0.00892129  |                   0        |
| LCP             |                 0.00133593  |                   0        |
| LCS             |                 0.0228693   |                   0        |
| LCSA            |                 0.0232316   |                   0        |
| LDL             |                 0.0830767   |                   0        |
| LEC             |                 0.0233674   |                   0        |
| LFL             |                 0.0184766   |                   0        |
| LFL2            |                 0.00869486  |                   0        |
| LGL             |                 0.000113214 |                   0        |
| LHE             |                 0.0155557   |                   0        |
| LIT             |                 0.00305679  |                   0        |
| LJL             |                 0.0185672   |                   0        |
| LJLA            |                 0.00135857  |                   0        |
| LLA             |                 0.0159179   |                   0        |
| LLN             |                 0.00430215  |                   0        |
| LMF             |                 0.0167104   |                   0        |
| LMS             |                 0.00978172  |                   0        |
| LPL             |                 0.0715288   |                   0        |
| LPLOL           |                 0.0159632   |                   0        |
| LRN             |                 0.00163029  |                   0        |
| LRS             |                 0.00165293  |                   0        |
| LSPL            |                 0.0113667   |                   0        |
| LTA             |                 0.000249072 |                   0        |
| LTA N           |                 0.000430215 |                   0        |
| LTA S           |                 0.000452858 |                   0        |
| LVP SL          |                 0.0132687   |                   0        |
| MSC             |                 0.000362286 |                   0        |
| MSI             |                 0.00620415  |                   0        |
| NA CS           |                 0.00201522  |                   0        |
| NA LCS          |                 0.00889865  |                   0        |
| NACL            |                 0.00830994  |                   0        |
| NASG            |                 0.00194729  |                   0        |
| NERD            |                 0.000815144 |                   0        |
| NEST            |                 0.000498143 |                   0        |
| NEXO            |                 0.0107554   |                   0        |
| NLC             |                 0.0181822   |                   0        |
| NLC Aurora Open |                 0.00362286  |                   0        |
| OCS             |                 0.0036455   |                   0        |
| OPL             |                 0.0110271   |                   0        |
| OTBLX           |                 0.00656643  |                   0        |
| PCS             |                 0.0192464   |                   0        |
| PGC             |                 0.00978172  |                   0        |
| PGN             |                 0.011095    |                   0        |
| PRM             |                 0.0181936   |                   0.681818 |
| PRMP            |                 0.00426818  |                   0.318182 |
| RCL             |                 0.00905715  |                   0        |
| RL              |                 0.00124536  |                   0        |
| ROL             |                 0.000543429 |                   0        |
| Riot            |                 0.000317    |                   0        |
| SL (LATAM)      |                 0.00321529  |                   0        |
| SLO             |                 0.00724572  |                   0        |
| TAL             |                 0.0135857   |                   0        |
| TCL             |                 0.0230731   |                   0        |
| TPL             |                 0.00156236  |                   0        |
| TSC             |                 0.00178879  |                   0        |
| UGP             |                 0.0020605   |                   0        |
| UKLC            |                 0.00710986  |                   0        |
| UL              |                 0.0158727   |                   0        |
| UPL             |                 0.0176841   |                   0        |
| USP             |                 0.00122272  |                   0        |
| VCS             |                 0.0255185   |                   0        |
| VL              |                 0.00317     |                   0        |
| WLDs            |                 0.0125894   |                   0        |


<iframe
  src="mm1-league.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The p-value is less than the 0.5 significance level, and so we reject the null hypothesis. Thus, we can conclude that the missingness of `teamname` also depends on the `league`.

# Hypothesis Testing

We aim to determine whether teams with a higher Pre-Game Advantage (PGA) have a significantly greater win rate than teams with lower PGA. This test allows us to assess whether pre-game factors, including champion picks and bans, impact a team’s likelihood of winning.

**Null Hypothesis (H₀):**
The win rate of teams with a higher PGA is equal to 50%, meaning that PGA has no effect on winning. That is, having a higher PGA does not provide a statistical advantage in determining the outcome of the game.

**Alternative Hypothesis (H₁):**
Teams with a higher PGA win significantly more than 50% of the time, indicating that pre-game decisions (picks and bans) contribute to winning.

**Test Statistic** : # games won by team with higher PGA / total number of games

**Significance Level**: We set α = 0.05 (5%), meaning we will reject the null hypothesis if the probability of obtaining the observed win rate (or more extreme results) under the null is less than 5%.

Here is a histogram containing the distribution of our test statistics under the null hypothesis:

<iframe
  src="hyp.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Based on the hypothesis test performed, with a p-value of 0.0219, we reject the null hypothesis, as our significance level of 0.05. Meaning, under our trial, it appears that teams with a higher PGA win significantly more than 50% of the time, indicating that pre-game decisions (picks and bans) could contribute to winning.

# Framing a Prediction Problem 

We aim to predict the outcome of a League of Legends match using a binary classification model, where:

- 0 represents a loss
- 1 represents a win
Our goal is to determine whether pre-game metrics and in-game factors influence match outcomes as the game progresses. To improve predictive performance, we explored various features and encodings.

We will use accuracy as the primary evaluation metric for our model, as a good accuracy will indicate that our model is making correct predictions at a high rate.

## Baseline Model 
To establish a starting point, we trained a baseline Decision Tree model on the following features:

- `num_counters_picked` (Quantitative) : A respective team's number of counter picks
- `num_counters_banned` (Quantitative) : A respective team's number of counter bans
- `side` (Nominal) : The side of the map the team played on, Blue or Red 

Since our feature set included both quantitative and categorical data, we applied the following transformations:

**OneHotEncoding**: Used for the side feature since it is nominal and does not have an intrinsic order.
**Standardization**: Applied to our quantitative features (num_counters_picked and num_counters_banned) to bring them onto a similar scale, ensuring that no single feature dominates due to differences in magnitude.

These were all implemented into a single ski-kit learn pipeline, and we then trained a Random Forest classifier with the following hyperparameters:

- Criterion: entropy
- Max Depth: 2
- Min Samples Split: 2
- Accuracy : 53%

However, based on our exploratory data analysis and previous hypothesis testing, pre-game factors alone do not have a strong influence on match outcomes. As a result, our model only achieved 53% accuracy on the test set, which is only slightly better than a random guess (50%).

Our current model is not considered good due to its poor performance. A classification accuracy of 53% indicates that our model struggles to differentiate between wins and losses based on the provided pre-game features. This suggests that other in-game factors, such as player kills, deaths, assists, and economic decisions likely have a more significant impact on match outcomes.

Our baseline model only considered pre-game factors, which resulted in poor predictive performance (53% accuracy). However, early to mid game events can significantly influence match outcomes. By incorporating gold, XP, creep score, and KDA metrics at the 10-minute mark, we redefined our classification problem to capture early-game momentum.

Our second model is a Random Forest Classifier trained on the following features:
	
### Features from Data 
- `side` (Nominal) : The side of the map the team played on, Blue or Red 
- `golddiffat10` (Quantitative): Difference between the team's total gold and the opponent’s total gold. Measures economic strength, which correlates with better inventory and power spikes.
- `xpdiffat10` (Quantitative): Difference in team XP compared to opponents. Higher XP leads to stronger champions through better abilities and stats.
- `csdiffat10`(Quantitative): Difference in creep score (CS) between teams. CS directly impacts gold income and scaling potential.
- `mean_champ_wr`(Quantitative):  The average win rate of the champions selected by a team. Measures overall champion strength based on historical performance.
- `mean_team_wr`(Quantitative): The average win rate of the players in a team. Helps quantify player skill and historical performance.

### Features Engineered 
- `kda_10` (Quantitative): (`killsat10` + `assistsat10`) / `deathsat10`. KDA (Kill/Death/Assist ratio) is a direct measure of player performance; a high KDA signifies strong early-game presence.
- `opp_kda_10` (Quantitative): (`opp_killsat10` + `opp_assistsat10`) / `opp_deathsat10`. The opponent’s KDA provides context on whether our team’s early-game advantage is truly significant.
- `pga_diff` (Quantitative): Measures the difference in Pre-Game Advantage (PGA) between our team and the opponent. A larger PGA difference indicates a stronger team composition.

Note that we omitted the original features `num_counters_picked` and `num_counters_banned`, as - `pga_diff` rendered those columns slightly redundant. 

In addition to one-hot-encoding side, to ensure fair weight distribution across features, we standardized all numerical values using StandardScaler(), which centers the mean at 0 and scales to unit variance.

These were all implemented into a single ski-kit learn pipeline, and we then trained a Random Forest classifier, finding the hyperparameters using GridSearchCV. 

- Criterion: gini
- Max Depth: 10
- Min Samples Split: 22
- Num Estimators : 300

This model achieves an accuracy of 62%, marking a clear improvement over our baseline but still falling short of being highly reliable. Predicting a match outcome at the 10-minute mark proves challenging, which aligns with our understanding of early-game dynamics. During this phase, lanes remain largely isolated, limiting team interactions and strategic synergy. Additionally, major objectives—such as dragons, Rift Herald, and towers—are rarely contested before 15 minutes, meaning early advantages may not yet translate into definitive outcomes.

## Final Model

To assess how predictive power evolves as the game progresses, we applied the same feature set but replaced 10 minutes with 25. 

- `side` (Nominal) : The side of the map the team played on, Blue or Red 
- `golddiffat25` (Quantitative): Difference between the team's total gold and the opponent’s total gold. Measures economic strength, which correlates with better inventory and power spikes.
- `xpdiffat25` (Quantitative): Difference in team XP compared to opponents. Higher XP leads to stronger champions through better abilities and stats.
- `csdiffat25`(Quantitative): Difference in creep score (CS) between teams. CS directly impacts gold income and scaling potential.
- `mean_champ_wr`(Quantitative):  The average win rate of the champions selected by a team. Measures overall champion strength based on historical performance.
- `mean_team_wr`(Quantitative): The average win rate of the players in a team. Helps quantify player skill and historical performance.

### Features Engineered 
- `kda_25` (Quantitative): (`killsat25` + `assistsat25`) / `deathsat25`. KDA (Kill/Death/Assist ratio) is a direct measure of player performance; a high KDA signifies strong early-game presence.
- `opp_kda_25` (Quantitative): (`opp_killsat25` + `opp_assistsat25`) / `opp_deathsat25`. The opponent’s KDA provides context on whether our team’s early-game advantage is truly significant.
- `pga_diff` (Quantitative): Measures the difference in Pre-Game Advantage (PGA) between our team and the opponent. A larger PGA difference indicates a stronger team composition.

The head of our dataset was the following: 

| side   |   pga_diff |   kda_25 |   opp_kda_25 |   mean_champ_wr |   mean_team_wr |   golddiffat25 |   xpdiffat25 |   csdiffat25 |
|:-------|-----------:|---------:|-------------:|----------------:|---------------:|---------------:|-------------:|-------------:|
| Blue   |         -1 |        1 |      0       |        0.513931 |       0.466667 |           1560 |         1638 |           52 |
| Red    |          1 |        0 |      1       |        0.502757 |       0.555556 |          -1560 |        -1638 |          -52 |
| Blue   |         -1 |        3 |      3       |        0.497727 |       0.606061 |            776 |          943 |           39 |
| Red    |          1 |        3 |      3       |        0.508189 |       0.397167 |           -776 |         -943 |          -39 |
| Blue   |         -1 |        2 |      1.33333 |        0.500533 |       0.444444 |            -59 |          690 |           -3 |

- Criterion: gini
- Max Depth: 12
- Min Samples Split: 22
- Num Estimators : 200

Our final model achieves an accuracy of 76%! This demonstrates that as the game progresses, we can predict the winning team with 76% accuracy using only kills, deaths, assists, experience, economy, and pre-game factors.

The accuracy is limited because our model makes predictions at the midway point of the game, without incorporating crucial late-game objectives such as turrets and major monsters (e.g., Baron Nashor or Dragon), which significantly impact the outcome.

Lets visualize how accuracy improves with gametime. 

<iframe
  src="models.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Fairness Analysis
The fairness analysis seeks to determine whether our model treats the two sides of the game (Blue and Red) fairly. Specifically, we aim to evaluate whether the model’s precision is roughly the same for both sides or if there is a statistically significant difference.

- Hypothesis
Null Hypothesis (H₀):
Our model is fair. Its precision for the Red Side and Blue Side are roughly the same, and any observed difference is due to random chance.

Alternative Hypothesis (H₁):
Our model is unfair. Its precision for the Red Side is different from the precision for the Blue Side.

- Groups
Group X: Blue Side
Group Y: Red Side

- Evaluation Metric
The evaluation metric is the difference in precision between the two groups: Red and Blue Side

- Significance Level
The significance level (alpha) is set to 0.05, meaning we will reject the null hypothesis if the p-value is less than 0.05. 

The results are as shown: 

<iframe
  src="fairnesshyp.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Our p-value is 0.2720, which means that under a significance level of 0.05, we **fail to reject the null hypothesis**. This indicates that our model is likely fair for both the blue and red sides, and any observed difference is likely due to random chance.