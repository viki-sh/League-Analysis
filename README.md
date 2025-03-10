# Was it Over Before it Started?

# General Introduction 
Welcome to League of Legends! A video game published and developed by Riot Games, widely considered one of the greatest video games ever made. Furthermore, it is the world’s largest esport, with events drawing hundreds of millions of viewers. This analysis aims to analyze eight years of professional league matches, and answer the question, "Do pre-game choices have an affect on the whether a team wins or not"

In a league of legends game, two teams, consisting of 5 players each compete to destroy the enemys "nexus", a home base. The game is extremely complex, and there are many factors in a game including over 150 possible champions, a "store" to buy items to benefit a players performance, gold, jungle monsters, roles, to state a few. 

However, for the sake of our analysis, we will be focusing on the Pre-Game. Before a game begins, each player chooses a role, a champion, and a ban. The teams are also randomly assigned a side of the map, Red or Blue.

## What are the roles? 
There are 5 roles, Top, Middle, Bot, Support ADC, and Jungle. Each role typically has champions best suited for the role, for example a support champion will have a kit consisting of healing, whereas a top laner will have lots of health. There are currenltly 170 champions in League, each with their own abilities, roles, and counters. 

## What is a counter? 
In League of Legends, a "counter" refers to a champion that has a significant advantage over another champion, meaning their abilities and playstyle are particularly effective at negating the enemy champion's strengths and exploiting their weaknesses, essentially making it much harder for the enemy champion to perform well in the matchup. 

## What is a ban?
In addition to selecting a role and a repective champion, each player can also ban a champion. This can be effective when wanting to ensure a player doesnt want to play against a particular character.

## What are the sides? 
The 'Rift', aka the map a match is played on, is parallel, with three lanes and a mass jungle. The red team plays from the top, and aims to destroy the enemys nexus on the bottom, and vise versa. Typically in the league community, most players prefer to be on the blue side, as playing upwards is easier than playing downward. However, these sides are assignmed at random and there is no in game advantage for either.

## Introduction of dataset 
- **Number of Rows:** 86899
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
Our dataset comes from Oracle’s Elixir, encompassing thousands of professional-level esports matches from top-tier leagues such as the LCS, LEC, LCK, and LPL. For our analysis, we chose to only use 2017, 2019, 2020, 2021, 2022, 2024, and 2025, as the data from other years only had 3 bans, and we would like to analyze across all 5.

We dropped columns that referenced the in-process game statistics, such as kills, gold, and game objectives, since we are interested in the pre-game. 

Additionally, we dropped all rows that had the same PGA for both teams. This way, we could accurately gauge if having a **higher** PGA increased a teams ability to win their match. 

We added five columns to this dataset : 

- `num_counters_picked` : Referencing our webscraped data containing champions and their top five counters, we observed each opponents picks, and incremented by 1 if any of the teams picks were in the opponents counters. 
- `num_counters_banned` : Referencing our webscraped data containing champions and their top five counters, we observed each teams picks, and incremented by 1 if any of the teams bans were a counter to a pick. 
- `PGA` : This amount was obtained by adding `num_counters_picked` and `num_counters_banned`, and is an indicator of how strong the overall pre-game choices are for a team
- `higher_PGA` : This value was obtained to be able to indicate which team had a stronger pre-game advantage, and was used to see the relationship between winning and having a higher pga than your opponent. 
- `mean_champ_wr` : Using our cleaned dataframe, we obtained a wr for each champ by calculating the number of winning games a champion was played in, divided by the total number of games the champion was played. Then, each pick's respective win rate was added and divided by 5 to obtain the mean. 
- `mean_team_wr` : Using our cleaned dataframe, we obtained a wr for each team by calculating the number of winning games a team has, divided by the total number of games the team played. 

Our head of our final dataframe is as follows: 

|   year | gameid    | league   | teamname            | side   | ban1    | ban2     | ban3     | ban4       | ban5    | pick1   | pick2   | pick3      | pick4   | pick5      |   num_counters_picked |   num_counters_banned |   PGA |   higher_PGA |   mean_champ_wr |   mean_team_wr |   result |
|-------:|:----------|:---------|:--------------------|:-------|:--------|:---------|:---------|:-----------|:--------|:--------|:--------|:-----------|:--------|:-----------|----------------------:|----------------------:|------:|-------------:|----------------:|---------------:|---------:|
|   2017 | 1506-1540 | LPL      | I May               | Blue   | Syndra  | Malzahar | Ashe     | Karma      | Poppy   | Maokai  | Kha'Zix | Cassiopeia | Varus   | Tahm Kench |                     0 |                     2 |     2 |            1 |        0.508666 |       0.438776 |        1 |
|   2017 | 1506-1540 | LPL      | Royal Never Give Up | Red    | Camille | Rengar   | Zyra     | Elise      | Rek'Sai | Kled    | Lee Sin | Ryze       | Caitlyn | Nautilus   |                     0 |                     0 |     0 |            0 |        0.493086 |       0.598582 |        0 |
|   2017 | 1506-1541 | LPL      | I May               | Blue   | Syndra  | Malzahar | Ashe     | Rek'Sai    | Kha'Zix | Maokai  | Lee Sin | Corki      | Caitlyn | Thresh     |                     0 |                     0 |     0 |            0 |        0.510833 |       0.438776 |        1 |
|   2017 | 1506-1541 | LPL      | Royal Never Give Up | Red    | Rengar  | Camille  | Varus    | Cassiopeia | Orianna | Trundle | Rumble  | Ryze       | Jhin    | Zyra       |                     1 |                     0 |     1 |            1 |        0.492558 |       0.598582 |        0 |
|   2017 | 1507-1544 | LPL      | Invictus Gaming     | Blue   | Jayce   | Elise    | Malzahar | Kha'Zix    | Lee Sin | Singed  | Rengar  | LeBlanc    | Varus   | Tahm Kench |                     0 |                     1 |     1 |            0 |        0.49238  |       0.523126 |        1 |

## Univariate Analysis 
For our univariate analysis, lets look at the distribution of PGA across all games. Note that 0 means a team did not counter ban nor counter pick, whereas 10 meant they counter picked every single champ. 


<iframe
  src="uni.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

There are no values of PGA over 8, because there is randomness to when picking and banning. The process usually goes as follows :  

- Both teams ban 5 champions each

- **Blue** team will pick 1 champion
- **Red** team will pick 2 champions 
- **Blue** team will pick 2 champions
- **Red** team will pick 2 champions
- **Blue** team will pick 2 champion
- **Red** team will pick 1 champion

This process ensures each team has equal likelihood of picking a counter; there isnt a "blue team picks all" and then the red team can counter every champion.  As a result, it is very unlikely to have a PGA of 10, as it would insinuate a team choosing to play against their counter, which is not strategic. 

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

It looks like a teams PGA doesnt necessarily mean its more likely to win. However, note that this only shows wins and losses of pga, not necessarily if a higher pga meant winning against their opponent. Lets see that here :

<iframe
  src="bi2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This visualization shows a 50/50 split. So, it looks like having a higher PGA doesnt significantly affect your ability to beat your opponent.

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

Based on the hypothesis test performed, with a p-value of 0.0219, we reject the null hypothesis, as it is less than our significance level of 0.05

# Framing a Prediction Problem 

Our classification model aims to predict the outcome of a League of Legends match using pre-game data. Given that match outcomes are influenced by multiple factors, we explored various features and encodings to improve predictive performance.

## Baseline Model 
To establish a starting point, we trained a baseline Decision Tree model on the following features:

`num_counters_picked` (Quantitative) : A respective team's number of counter picks
`num_counters_banned` (Quantitative) : A respective team's number of counter bans
`side` (Nominal) : The side of the map the team played on, Blue or Red 

Since our feature set included both quantitative and categorical data, we applied the following transformations:

**OneHotEncoding**: Used for the side feature since it is nominal and does not have an intrinsic order.
**Standardization**: Applied to our quantitative features (num_counters_picked and num_counters_banned) to bring them onto a similar scale, ensuring that no single feature dominates due to differences in magnitude.

These were all implemented into a single ski-kit learn pipeline, and we then trained a Random Forest classifier with the following hyperparameters:

Criterion: entropy
Max Depth: 2
Min Samples Split: 2
Accuracy : 53%

However, based on our exploratory data analysis and previous hypothesis testing, pre-game factors alone do not have a strong influence on match outcomes. As a result, our model only achieved 53% accuracy on the test set, which is only slightly better than a random guess (50%).

Our current model is not considered good due to its poor performance. A classification accuracy of 53% indicates that our model struggles to differentiate between wins and losses based on the provided pre-game features. This suggests that other in-game factors, such as player kills, deaths, assists, and economic decisions likely have a more significant impact on match outcomes.

## Final Model 

Our baseline model only considered pre-game factors, which resulted in poor predictive performance (53% accuracy). However, early-game events can significantly influence match outcomes. By incorporating gold, XP, creep score, and KDA metrics at the 10-minute mark, we redefined our classification problem to capture early-game momentum.

Our final model is a Random Forest Classifier trained on the following features:
	
### Features from Data 
`side` (Nominal) : The side of the map the team played on, Blue or Red 
`golddiffat10` (Quantitative): Difference between the team's total gold and the opponent’s total gold. Measures economic strength, which correlates with better inventory and power spikes.
`xpdiffat10` (Quantitative): Difference in team XP compared to opponents. Higher XP leads to stronger champions through better abilities and stats.
`csdiffat10`(Quantitative): Difference in creep score (CS) between teams. CS directly impacts gold income and scaling potential.
`mean_champ_wr`(Quantitative):  The average win rate of the champions selected by a team. Measures overall champion strength based on historical performance.
`mean_team_wr`(Quantitative): The average win rate of the players in a team. Helps quantify player skill and historical performance.

### Features Engineered 
`kda_10` (Quantitative): (`killsat10` + `assistsat10`) / `deathsat10`. KDA (Kill/Death/Assist ratio) is a direct measure of player performance; a high KDA signifies strong early-game presence.
`opp_kda_10` (Quantitative): (`opp_killsat10` + `opp_assistsat10`) / `opp_deathsat10`. The opponent’s KDA provides context on whether our team’s early-game advantage is truly significant.
`pga_diff` (Quantitative): Measures the difference in Pre-Game Advantage (PGA) between our team and the opponent. A larger PGA difference indicates a stronger team composition.

Note that we omitted the original features `num_counters_picked` and `num_counters_banned`, as `pga_diff` rendered those columns slightly redundant. 

In addition to one-hot-encoding side, to ensure fair weight distribution across features, we standardized all numerical values using StandardScaler(), which centers the mean at 0 and scales to unit variance.

The features needed of our model is as follows: 

| side   |   pga_diff |   kda_10 |   opp_kda_10 |   mean_champ_wr |   mean_team_wr |   golddiffat10 |   xpdiffat10 |   csdiffat10 |
|:-------|-----------:|---------:|-------------:|----------------:|---------------:|---------------:|-------------:|-------------:|
| Blue   |         -1 |        0 |            0 |        0.513931 |       0.466667 |             31 |         -139 |            2 |
| Red    |          1 |        0 |            0 |        0.502757 |       0.555556 |            -31 |          139 |           -2 |
| Blue   |         -1 |        0 |            0 |        0.497727 |       0.606061 |            122 |           64 |            6 |
| Red    |          1 |        0 |            0 |        0.508189 |       0.397167 |           -122 |          -64 |           -6 |
| Blue   |         -1 |        1 |            1 |        0.500533 |       0.444444 |           -314 |        -1023 |          -27 |

These were all implemented into a single ski-kit learn pipeline, and we then trained a Random Forest classifier, finding the hyperparameters using GridSearchCV. 

Criterion: gini
Max Depth: 10
Min Samples Split: 22
Num Estimators : 300

The model achieves an accuracy of 62%, marking a clear improvement over our baseline but still falling short of being highly reliable. Predicting a match outcome at the 10-minute mark proves challenging, which aligns with our understanding of early-game dynamics. During this phase, lanes remain largely isolated, limiting team interactions and strategic synergy. Additionally, major objectives—such as dragons, Rift Herald, and towers—are rarely contested before 15 minutes, meaning early advantages may not yet translate into definitive outcomes.

To assess how predictive power evolves as the game progresses, we applied the same feature set but replaced KDA, XP, and gold differences at 10 minutes with their corresponding values at later time marks. The resulting testing accuracies are shown below:

<iframe
  src="models.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Fairness Analysis