# Significance of The First 10 Minutes In League of Legends Matches
A comprehensive data science project conducted at UCSD focusing on the significance of the first 10 minutes in League of Legends matches and its impact on the outcome of matches.

Author: Tony Bai

## Introduction

League of Legends is a popular multiplayer online battle arena game released in 2009 by Riot Games. With 132 million active monthly players in 2024 it is one of the most popular video games and one of the most popular games in the electronic sports scene even today. The dataset I will be working with is collected by *Oracle's Elixer* containing match data from numerous professional tournaments hosted in 2022.

Each League of Legends match consists of 2 teams of 5 players where the goal is to destroy a crystal called The Nexus located in the opposing base while protecting your own. Over the course of each match each player will kill monsters and opposing players to gain experience and gold in order to become stronger than the opposing players to eventually overwhelm and destroy the opposing Nexus.

Each match lasts an average of around 30 minutes, but in games like League of Legends the consensus is that matches are often won within the first 10 minutes by the team that is more efficient and collect the most experience and gold, allowing them to gain an edge over the opponent early and making it easier to overwhelm the opposing team and win the match. The first 10 minutes are so important, many veteran and professional players have their own playbook to make the first 10 minutes the most efficient for themselves and for their team.

The central question I am interested in is **How significant is the first 10 minutes of each League of Legends match in regards to gold collected?** I want to apply data analysis techniques to test the significance of the first 10 minutes and try to predict the outcome of each match just by looking at statistics gathered at the start of each match. This analysis is significant because it will reveal whether or not having an efficient start of a League of Legends match is as crutial as all its players claim it is.

The dataset I will be working with is Oracle's Elixer's 2022 dataset containing match data from professional League of Legends tournaments. There are a total of 127872 rows in this dataset, and below are the columns I will be working with for this analysis:

- `gameid`: contains a unique identifier for each match. Each `gameid` corresponds to 12 rows, one for each of the 5 players on both teams and 2 more corresponding to each teams' summary data.

- `league`: contains the abbreviation of the professional tournament the match was played in.

- `result`: contains the outcome of the match stored as 0s and 1s. 0 indicates loss and 1 indicates win.

- `gamelength`: contains the duration of the match in seconds.

- `firstdragon`: contains whether or not a team killed the first dragon that spawns, which grants their team a buff, stored as 0s and 1s. 0 indicates the team did not kill the first dragon and 1 indicates the team did.

- `firstblood`: contains if a player or team claimed the first kill of the match as 0s and 1s. 0 indicates the player or team did not claim the first kill and 1 indicates the player or team did claim the first kill.

- `firsttower`: contains if a team took down one of the opposing team's tower protecting The Nexus as 0s and 1s. 0 indicates the team was not the first to take down an opposing tower and 1 indicates the team did.

- `earnedgold`: contains the amount of gold earned actively for an individual or a team through killing monsters, using abilities, killing opposing players, etc.

- `goldat10`: contains the amount of gold earned actively for an individual or a team after 10 minutes.

- `xpat10`: contains the amount of experienced earned for an individual or a team after 10 minutes.

- `killsat10`: contains the amount of kills for an individual or a team after 10 minutes.

- `goldat25`: contains the amount of gold earned actively for an individual or a team after 25 minutes.

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
First, I will filter to keep only the columns mentioned above since the rest are not useful to the rest of the analysis. Like I mentioned before, for each `gameid` there are 12 rows but for my analysis I am interested in the 2 rows that have summary data for the teams, so I will filter to only keep those 2 rows for each `gameid`, taking the row count down to 25098. Additionally, in the dataset there is a column `datacompleteness` which indicates if there are missing data within the row entry. Almost all of the entries that are labeled 'partial' have missing data within the columns I mentioned above. Moreover, it is impossible to impute the missing data due to all of them having a dependence on 5 specific leagues in the `league` column that have no filled entries. Due to this, I will also be removing any rows that are marked 'partial' in the `datacompleteness` column, dropping the row count to 21312.

Next, the columns `firstdragon`, `firstblood`, `firsttower`, and `result` are stored as 1s and 0s when they should be stored as boolean values, so I will change these 3 columns to hold bools instead of integers. Finally, I decided to change `gamelength` from storing the time of matches in seconds into minutes, which will make analysis simpler later.

This is what the first 5 rows of the dataframe looks like after all the data cleaning steps:

|	|gameid	                |league |gamelength |result	|firstdragon|firstblood |firsttower |earnedgold|goldat10| xpat10|killsat10|goldat25|
|--:|:----------------------|:------|----------:|:------|:----------|:----------|:----------|---------:|-------:|------:|--------:|-------:|
|  0|ESPORTSTMNT01_2690210	|LCKC   |28.55	    |False	|False	    |True	    |True	    |   28222.0| 16218.0|18213.0|      3.0| 40224.0|
|  1|ESPORTSTMNT01_2690210	|LCKC	|28.55	    |True	|True	    |False	    |False	    |   33769.0| 14695.0|18076.0|      0.0| 40136.0|
|  2|ESPORTSTMNT01_2690219	|LCKC	|35.23	    |False	|False	    |False	    |False	    |   34688.0| 14939.0|17462.0|      1.0| 39335.0|
|  3|ESPORTSTMNT01_2690219	|LCKC	|35.23	    |True	|True	    |True	    |True	    |   48063.0| 16558.0|19048.0|      3.0| 46615.0|
|  4|ESPORTSTMNT01_2690227	|LCKC	|32.87	    |True	|True	    |False	    |True	    |   41372.0| 15466.0|19600.0|      0.0| 43989.0|

### Univariate Analysis
I first examined the distribution of gold earned throughout entire matches.

<iframe
  src="assets/total-gold-hist.html"
  width="800"
  height="530"
  frameborder="0"
></iframe>

The histogram plotted with regards to the `earnedgold` column is very close to being a normal distribution, which is good for future analysis, but my main focus is for gold earned after 10 minutes, so I also examined the distribution of the `goldat10` column.

<iframe
  src="assets/goldat10-hist.html"
  width="800"
  height="530"
  frameborder="0"
></iframe>

The histogram plotted with regards to the `goldat10` column has a roughly normal distribution with a right skew. The right skew makes sense in the context of League of Legends because there are many ways to gain an advantage over your opponents, not just gold, and many teams balance what they invest their first 10 minutes in, making the upper extremes of gold collected rarer.

### Bivariate Analysis
Next, I want to examine if there is a distinction between the total gold earned by winning teams verses losing teams

<iframe
  src="assets/total-gold-hist-on-result.html"
  width="800"
  height="530"
  frameborder="0"
></iframe>

From the overlapping histogram we can see that on average winning teams earned more gold compared to losing teams, suggesting that there is some level of connection between the amount of gold a team earned and whether or not that team won. Let's see if the trend holds up for gold earned after 10 minutes

<iframe
  src="assets/goldat10-pie.html"
  width="800"
  height="430"
  frameborder="0"
></iframe>

As seen from this pie chart, 69.6% of the teams that won had more gold than their opponent after 10 minutes compared to only 30.4% of teams who won with less gold than their opponent. Evidently, if you have earned more gold than your opponent, even after only 10 minutes, you are more likely to win. Obviously it is not impossible to win if you have less gold than your opponent, as seen from 30.4% of teams in 2022 won with less gold, but it is less likely to happen.

### Interesting Aggregates
Here is an interesting aggregate on the `results` column, aggregating by mean:

|result|earnedgold|	goldat10|	  xpat10|	goldat25|
|:-----|---------:|--------:|--------:|--------:|
|True	 |  41235.56|	16035.23|	18412.38|	45710.68|
|False |  31278.94|	15343.22|	17986.11|	40939.26|

Here I've included some of the columns that I think are interesting to mention. For all of the columns presented here the means for the row corresponding to winning teams are all greater than the row corresponding to losing teams. This once again shows that there is some level of connection between these whether or not a team wins their match and how much gold and experience they've collected.

## Assessment of Missingness

### NMAR Analysis

Although this column was removed in the data cleaning section, I believe the `teamid` column is not missing at random. Upon inspection of the rows with the `teamid` column missing I cannot find any distinct connection between any of the other rows and this column. In professional League esports matches each team has their own team name but the team could be newly created, meaning although they have a team name and is allowed to play matches they haven't received their team id yet, thus making the missingness of this column dependent on its own values. One method of making this column MAR is to create a new column `newteam`, where a 1 indicates the team was just created and a 0 indicates the team was not just created.

### Missingness Dependency

Here, I am going to test the missingness dependency of `goldat25` column and see if it depends on other columns, specifically `firstdragon` and `league`.

First I will test if `goldat25` is MAR on `firstdragon` column using Total Variation Distance on 0.05 significance level.

**Null Hypothesis:** distribution of `firstdragon` when `goldat25` is missing is the same distribution when `goldat25` is not missing.

**Alternative Hypothesis:** distribution of `firstdragon` when `goldat25` is missing is **not** the same distribution when `goldat25` is not missing.

Below is the observed distribution:

|gold25Missing|False|	 True|
|firstdragon  |     |      |
|------------:|----:|-----:|
|False	      |  0.5|	  0.5|
|True	        |  0.5|   0.5|

Below is the emperical distribution of the permutation test:

<iframe
  src="assets/goldat25-firstdragon-hist.html"
  width="800"
  height="430"
  frameborder="0"
></iframe>

The p-value of this statistic test is 0.914. Since the p-value is much greater than the 0.05 significance level, I accept the null hypothesis, thus missingness of `goldat25` **not MAR** on `firstdragon` column.

Next I will test if `goldat25` is MAR on `league` column.

**Null Hypothesis:** distribution of `league` when `goldat25` is missing is the same distribution when `goldat25` is not missing

**Alternative Hypothesis:** distribution of `league` when `goldat25` is missing is **not** the same distribution when `goldat25` is not missing

Below is the observed distribution:

|gold25Missing	|   False|      True|
|league	        |        |          |
|--------------:|:-------|:---------|
|CBLOL	        |2.35e-02|	1.20e-02|
|CBLOLA	        |2.11e-02|	7.47e-03|
|CDF	          |6.81e-03|	1.20e-02|
|CT	            |2.30e-03|	4.48e-03|
|DDH	          |1.96e-02|	1.94e-02|
|EBL	          |1.60e-02|	8.97e-03|
|EBLPA	        |1.90e-03|	     NaN|
|EL	            |1.18e-02|	2.54e-02|
|ESLOL	        |2.24e-02|	2.39e-02|
|EUM	          |2.48e-02|	2.84e-02|
|GL	            |1.65e-02|	1.35e-02|
|GLL	          |1.50e-02|	8.97e-03|
|GLLPA	        |4.41e-03|	4.48e-03|
|HC	            |1.56e-02|	8.97e-03|
|HM	            |1.45e-02|	1.20e-02|
|IC	            |6.11e-03|	2.09e-02|
|LAS	          |2.01e-02|	3.89e-02|
|LCK	          |4.54e-02|	2.09e-02|
|LCKC	          |3.84e-02|	1.64e-02|
|LCL	          |1.50e-03|	1.49e-03|
|LCO	          |1.91e-02|	3.14e-02|
|LCS	          |3.02e-02|	5.98e-03|
|LEC	          |2.33e-02|	1.49e-02|
|LFL	          |2.43e-02|	5.98e-03|
|LFL2	          |2.33e-02|	1.20e-02|
|LHE	          |2.30e-02|	2.09e-02|
|LJL	          |2.07e-02|	1.20e-02|
|LJLA	          |3.50e-03|	4.48e-03|
|LLA	          |1.85e-02|	2.99e-03|
|LMF	          |3.08e-02|	1.79e-02|
|LPLOL	        |2.02e-02|	1.64e-02|
|LVP SL	        |2.39e-02|	8.97e-03|
|MSI	          |6.21e-03|	2.69e-02|
|NEXO	          |1.85e-02|	1.35e-02|
|NLC	          |2.30e-02|	2.09e-02|
|NLC Aurora Open|1.23e-02|	2.54e-02|
|PCS	          |2.48e-02|	3.74e-02|
|PGC	          |5.08e-02|	8.52e-02|
|PGN	          |1.44e-02|	8.97e-03|
|PRM	          |2.28e-02|	1.35e-02|
|PRMP	          |1.11e-02|	3.59e-02|
|SL (LATAM)	    |1.56e-02|	1.35e-02|
|TAL	          |1.93e-02|	1.79e-02|
|TCL	          |2.02e-02|	3.14e-02|
|UL	            |2.36e-02|	1.20e-02|
|UPL	          |3.36e-02|	1.14e-01|
|USP	          |3.30e-03|	1.49e-03|
|VCS	          |3.02e-02|	3.44e-02|
|VL	            |1.56e-02|	2.09e-02|
|WLDs	          |1.34e-02|	1.05e-02|

Below is the emperical distribution of the permutation test:

<iframe
  src="assets/goldat25-league-hist.html"
  width="800"
  height="430"
  frameborder="0"
></iframe>

The p-value of this statistic test is 0. Since the p-value is smaller than the 0.05 significance level, I reject the null hypothesis, thus the missingness of `goldat25` is MAR on `league` column.

## Hypothesis Testing
For this section, I want to see if there is a significance difference in the distribution of the amount of gold earned after 10 minutes for winning teams compared to losing teams. This permutation test will reveal if gold is a significant factor in determining if a team will ultimately win or lose their match.

**Null Hypothesis:** the distribution for gold earned after 10 minutes is the same for winning and losing teams

**Alternative Hypothesis:** the distribution for gold earned after 10 minutes is **not** the same for winning and losing teams

**Test Statistic:** absolute difference in group means, because the histogram in the EDA section shows that the earnedgold column for winning and losing teams are shifted versions of each other, which the goldat10 column should follow the same pattern

**Significance Level:** 0.05

Below is the emperical distribution for the permutation test:

<iframe
  src="assets/goldat10-permtest-hist.html"
  width="800"
  height="430"
  frameborder="0"
></iframe>

The observed statistic for this permutation test is 692.01 and the p-value is 0. Since the p-value is lower than the significance level of 0.05,thus the distribution of gold earned after 10 minutes for winning and losing teams are **not** the same. In addition, with how different the observed statistic is to the simulated statistics, it seems that gold earned has a significant impact on whether a team wins or loses a match. 

## Framing a Prediction Problem
As seen from the last section, the distribution between the amount of gold earned after 10 minutes by winning teams is significantly different from the amount of gold earned after 10 minutes by losing teams. Due to this difference I want to see **if it is possible to predict if a team will win or lose their match by only examining stats collected after 10 minutes**. Some of the statistics that can be collected after 10 minutes are gold and experience collected, if a team claimed the first kill of the game, if a team destroyed the first guard turret of the game, etc..

For the base prediction model, I will be trying to perform binary classification on the `results` column, using accuracy as the metric for evaluating my model, and splitting my data into training and testing set on a 75-25 split. I'm choosing accuracy as the metric for evaluation because the columns I will be using to predict `results` are roughly normal, thus I don't need to use other metrics.

## Baseline Model
For this base binary classification model I will be using `goldat10`, `xpat10`, `firstblood`, and `firstdragon` columns as my features. I'm choosing these columns because these all contain statistics that can be collected within 10 minutes of a match. The columns `goldat10` and `xpat10` are both quantitative so I do not have to do any encoding on them, but the columns `firstblood` and `firstdragon` are ordinal so I will first use OneHotEncoder to transform these 2 columns before training a DecisionTreeClassifier with max_depth of 5.

After fitting the model my accuracy on the training data is 66.43% while the accuracy on the testing data is 65.48%. The accuracy is better than randomly guessing if a team will win or lose but it is not much better and this model can be improved.

## Final Model
For the final model, I will add 2 new features: `killsat10` and `firsttower`. I'm adding these 2 features because I think these 2 features are a good metric to see the pacing of the game. League of Legends is a team versus team game so eventually you will have to fight your opponents and start working on taking down their Nexus, and these 2 new features are a good representation of who is fighting and has a slight advantage. 

For encoding the columns, `killsat10` column is a quantitative column but it is highly right skewered so I will use StandardScalar to standardize the column. `firsttower` is an ordinal column so I will OneHotEncode the column. Finally, I want to tune max_depth and max_features for the final model because I think tuning these 2 hyperparameters will benefit my model the most. Using GridSearchCV the best hyperparameter to use is 2 for max_depth and 11 for max_features.

With the additional features and tuned hyperparameters for the binary classification model the accuracy for the training data is now 68.52% and the accuracy for the testing data is now 67.83%. This is an improvement over the accuracy of the base model but unfortunately still not great compared to simple random guesses.

## Fairness Analysis
For this section I want to see if my model is fair towards different groups. The question I want to answer is **does my model perform worse for matches that are longer than 28 minutes than it does for matches that are shorter than 28 minutes?**

**Null Hypothesis:** my model is fair, meaning its accuracy for matches lasting longer than 28 minutes is the same for matches lasting shorter tahn 28 minutes.

**Alternative Hypothesis:** my model is unfair, meaning its accuracy for matches lasting longer than 28 minutes is **not** the same for matches lasting shorter than 28 minutes, where the accuracy for shorter matches is better than longer matches

**Test Statistic:** difference in accuracy between matches that last shorter/longer than 28 minutes

**Significance Level:** 0.05

Below is the emperical distribution for the fairness analysis:

<iframe
  src="assets/fairness-hist.html"
  width="800"
  height="430"
  frameborder="0"
></iframe>

The observed statistic is -0.253 and the p-value is 0. Since the p-value is lower than the significance level of 0.05, I reject the null hypothesis, thus my model is unfair towards matches longer than 28 minutes. 

In the context of League of Legends this result makes sense, the longer the matches go on the more chances your opponent can nullify your advantage, thus gaining an advantage within the first 10 minutes is of less significance the longer the matches go on and it is more important to keep your advantage.