# Given statistical record of players  who were selected to the Hall-Of-Fame and those who weren't, can we predict who might later be selected?

### Source Data				
Lahman database		http://www.seanlahman.com/baseball-archive/statistics/		

## Batters  

Files needed:  
- Batting.csv  
- Fielding.csv
- HallOfFame.csv
- People.csv
- WinShares.csv - a Bill James statistic rather than from the Lahman database, this is used only to eliminate low-impact players from further consideration.

Run this data through the jupyter notebook:  HOF_Logistic_Regression_B.ipynb
The notebook will undertake these steps:

#### Select players for this evaluation…
- who are not pitchers.  They have a completely different set of statistics to be considered.
- who generally played from the 1920s on.  
*the 19th century was in many ways a different game  
the dead-ball era also shaped statistics in a way that had more variance from other decades than any other decade*  
- who played at least 10 seasons  
- with more than 10 win shares/season; any fewer would surely signify a lesser player.
- whose last season was was 2010 or earlier.  These would be eligible for Hall-Of-Fame selection.  Others can be considered for predictions.

Count and number the players' season.  For the sake of choosing significant seasons, we will skip any with 45 games played or less.  
*These skipped games represent the lowest decile.*   

We will take the first ten seasons for each player under further consideration.  
*We probably should take the entire career; this would be useful to predict whether a retired player might be selected.  
What we want to do here though is make predictions for current players who after all may not have complete careers yet*  

An exception is Babe Ruth, who mostly pitched through his first three seasons and so had fewer plate appearances.  
We'll pick him up starting in 1919.  If there were other such players, we'd make exceptions for them as well.
 
Seasonal records are under a different scope than career records, as there are many more of them and they might combine awkwardly at best. One way we can handle this is to quantify them in a way that stands up to being summed while allowing them to remain distinct from career records.  
For that, I've taken inspiration from Bill James' Hall Of Fame Monitor, which awards points to players based on seasonal and career achievements. For the present purpose, we'll represent seasonal stats with HOF Monitor points, which are describe on the following sheet.  
I have determined the rate that these type of seasons occur and found that points are awarded at a rate that is pretty commeasurate with frequency of occurance. Doubles are an exception,  but this is a somewhat less prominent stat.  To James' original specs, I am adding stolen bases and slugging percentage and proposing rating points accordingly.  

Next, sum up all per-year rows into a single career row for each player.  This is also the place where we identify those players who are in the Hall Of Fame.  
We identify and discard fields as necessary to reduce the amount of cross-column correlation, which if kept could lead to overfitting.  
Finally, we separate the data into two parts: one set of players whose last playing year was 2010 or earlier, and the balance.
We'll use the former to train and test a data model, and the other to make predictions on their HOF fate. 

#### Here, the task becomes a standard Logistic Regression Modeling and Fitting:  
- We split the data columns between y (HOF_Member, already converted to 1s and 0s) and X (the remaining numeric columns).
- Split the Xs and ys into train and test portions
- Scale the X data using StandardScaler
- Create the LogisticRegression model
- Fit the train data to the model
- Use the model to make predictions of y from the X_test data
- Score the model

Even though the X data has necessarily dropped identifying information (playerID), we can output X and y data for import into an Excel spreadsheet, then match the statistics back into batting4.csv.

Finally, we make predictions on the recent player data split off earlier.

# Pitchers

We have a jupyter notebook called HOF_Logistic_Regression_P.ipynb which was built to parallel the "B" version.
It does everything in similar fashion with a few minor exceptions:  
- fielding positions are irrelevant
- Babe Ruth is not primarily a pitcher, so even if his pitching stats are impressive they are still thrown out.

And the model fails to predict even one recent pitcher to be selected to the Hall Of Fame.  Why is this when there are a number of worthy candidates?

Perhaps it is because less is expected of recent pitchers.  They do not pitch every fourth day, they are not expected to pitch as deep into games as before (complete games and shutouts have become somewhat rare occurances).  So it's possible that pitchers from these different eras cannot be directly compared but must be judged in their own contexts.

## Modified "Gray Ink" Test for Pitchers  
But one way they can be compared:  Being among the league leaders is impressive whatever the year.  So we'll try using the Gray Ink test to recognize accomplishments.

I'm making a few modifications though:  
- the traditional Gray Ink test is said to be biased against current players because there are so many more of them, but still just a "top ten".  So I will expand the scope of top players proportionately with the size of the leagues.  For example, with eight teams in a league we'll consider the top ten, but after expansion to twelve teams we'll look at a top fifteen.  
- the traditional Gray Ink test weights certain statistics heavier than others (e.g. 4 pts for wins, 1 pt for starts).  I'd rather let the model sort this out, so I give each stat equal weight.
- the traditional Gray Ink test awards a constant number of points to a player regardless to where in the top ten they fall.  Instead, I will award 10 points for the first place leader and a linearly diminishing number of points through the rest of the scope.  Example: a league of 16 calls for the top 20 to be considered, so #1 gets 10 pts, #2 gets 9.5, and so on down to .5 pts for #20.

#### Files needed in db directory
- Pitching.csv  
- HallOfFame.csv  
- pitchers_years.csv - this simply identifies pitchers' last year in MLB, and their most prominent decade, can be created from Pitching.csv
- people.csv  

Intermediate Files:
- pitching_phase2.csv
- pitching_phase3.csv

#### First, run pitching2_part2.ipnyb

This notebook simply drops unneeded columns and consolidates data into one row per player per year.  Output is db/pitching_phase2.csv

#### Process in Excel

Import the csv created above into Excel.  I used this to rank players for each stat within each year (being mindful that tied players should have the same rank), then assign points according to the above criteria.  Keep only these awarded point columns and write out as pitching_phase3.csv

#### Process in Excel

Import the csv created above into Excel.  I used this to rank players for each stat within each year (being mindful that tied players should have the same rank), then assign points according to the above criteria.  Keep only these awarded point columns and write out as pitching_phase3.csv
