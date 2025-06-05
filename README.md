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
  src="assets/total_gold_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>