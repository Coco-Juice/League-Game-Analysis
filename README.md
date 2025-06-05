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
  height="500"
  frameborder="0"
></iframe>

The histogram plotted with regards to the `earnedgold` column is very close to being a normal distribution, which is good for future analysis, but my main focus is for gold earned after 10 minutes, so I also examined the distribution of the `goldat10` column.

<iframe
  src="assets/goldat10-hist.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The histogram plotted with regards to the `goldat10` column has a roughly normal distribution with a right skew. The right skew makes sense in the context of League of Legends because there are many ways to gain an advantage over your opponents, not just gold, and many teams balance what they invest their first 10 minutes in, making the upper extremes of gold collected rarer.

### Bivariate Analysis
Next, I want to examine if there is a distinction between the total gold earned by winning teams verses losing teams

<iframe
  src="assets/total-gold-hist-on-result.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

From the overlapping histogram we can see that on average winning teams earned more gold compared to losing teams, suggesting that there is some level of connection between the amount of gold a team earned and whether or not that team won. Let's see if the trend holds up for gold earned after 10 minutes

<iframe
  src="assets/goldat10-pie.html"
  width="800"
  height="450"
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

First I will test if `goldat25` is MAR on `firstdragon` column using Total Variation Distance on 0.05 significance level

**Null Hypothesis:** distribution of `firstdragon` when `goldat25` is missing is the same distribution when `goldat25` is not missing

**Alternative Hypothesis:** distribution of `firstdragon` when `goldat25` is missing is **not** the same distribution when `goldat25` is not missing

Below is the emperical distribution of the permutation test:

<iframe
  src="assets/goldat25-firstdragon-hist.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>