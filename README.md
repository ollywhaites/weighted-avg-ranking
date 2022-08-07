# Ranking Players using Weighted Averages

Due to the complexity of football, there is a vast number of different statistics collected in football. These range from defensive based statistics through to goal scoring statistics. To identify statistically valuable players, often players are ranked in a particular statistic to highlight the best player in a particular attribute. However, the ability of a player is not solely based on the performance in one statitsic.

How valuable a player is often attributed to their excellence in multiple areas of their game. For example, a midfielder may be judged on their ability to win possession back and additionally to retain possession. High performance in each of these attributes is usually shown in multiple different statistics, such as winning possession which can be shown in defensive duels won, aerial duels won and interceptions.

Moreover, teams play systems that require different position specific roles with contrasting attributes. For example, some teams may require one central midfielder who sits deep, breaks up play and does not venchure forward or overly contribute to build-up play. Other teams may want two compitently defensive midfielders who are equally good at bring the ball up the field and contributing to build-up play. These attributes are contained in much more than a few statistics and identifying statistically top players who fit this profile may be challenging. 

Here is presented a method identifying players using statitsics based on a bespoke profile created by the user. This method takes an input of multiple statistics from a dataset of players and reduces them all to one number for each player using a weighted average. This is then ranked to highlight the most suitable players for this position. The construction is based on a collection of weights inputed by the user which measures the importance that catagory of the player's game is to the final ranking. 

This report explains the details of this method and applies it to centre backs in the Premier League. 


*All methods in this document are not assumed to be unique, but instead showcase personal constructions used to evaluate players. Specific details of code and presentation have been created by Oliver Whaites.*

*Data used for visualisations in this document has been sourced from Wyscout.*


## Normalise Scores

Consider a dataset of size $N$. Each datapoint is scored in $n$ different catagories where the value of the score is denoted $x^{(j)}_i$ for the $i$ th score in the $j$ th datapoint. Although scoring each datapoint in separate catagories is good, it would be useful to combine all these scores into one score, making all the data points easier to compare. One of the simplest ways of doing this is just by taking the sum of all the scores, such that 

$$\begin{equation}X_j = \sum^{n}_i x_i^{(j)}\end{equation}$$

This reduces all the scores to one number, however there is a few slight issues. Firstly, if these scores aren't in the same range (e.g $x^{(j)}_i \in [0,100]$ but $x^{(j)}_m \in [0,5]$ for $i \neq m$) then there may be some bias towards scores with larger ranges. Also, all scores are treated identically, which may not be desirable.

There are two ways the bias of unequal ranges can be solved. Either the scores can be normalised, or they can be ranked. Both methods involve mapping all the scores ranges to $[0,1]$ or $[a,b]f:\rightarrow[0,1]$ however, the mapping $f$ can be constructed in two different ways. First, the scores can be normalised, such that 

$$\bar{x}_i^{(j)} = f(x^{(j)}_i) = \frac{x_i^{(j)} - \min(x_i)}{ \max(x_i) - \min(x_i)}$$

This essentially shifts all scores, so that the minimum score is zero whilst also dividing out the maximum value so that all scores are between 0 and 1. 

Alternatively, the scores can be ranked. This involves simply sorting the datapoints and numbering from largest (1) to smallest (N). Then the rank is taken away from number of datapoints N and normalised. Explicitly, this is 

$$\tilde{x}_i^{(j)} = g(x^{(j)}_i) = \frac{N - \text{rank}(x_i)}{N - 1}$$

where $\text{rank}(x)$ ranks a dataset of points from largest to smallest.

These mappings produce slightly different distributions of the dataset. The normalisation method is more representitive of the dataset, but may overvalue players who have outlying scores. On the other hand, the ranking method does not over reward players who perform dramatically better in some statitsics. In some cases, it may be useful to use the normalisation method as players with vastly greater statistics should be rewarded. In this report, the ranking method will be used.

Additionally, penalising factors can be added by taking away 0.5 from all ranked datapoints. This means score above average (or 0.5) will be positive and any score below average will be negative. By doing this, players are penalised for scoring poorly in areas which are valuable to the user.

By adapting the scores in this way all scoring catagories are put on the same level, regadless of their range. This helps combine statistics evenly into one score using the aforementioned averaging method. 

## Weighted Average

Although it is useful to normalise scores so that they are all treated evenly, in cases where some scores are more important than others it is practical to artifically prioritise certain scores. This is done by performing a weighted average. 

To perform a weighted average, initially a set of weights must be constructed for each of the different catagories of scoring. This entails choosing a number between $0$ and $b$ for each catagory of the dataset where $b$ is the most important. In this report, $b = 10$ is chosen. The $n$ weights chosen are denoted as $w_i$ for the $i$ th catagory. 

A score for each datapoint, taking into account the catagories which are prioritised, is constructed by a weighted average as 

$$\tilde{X}_j = \sum_i^n w_i \tilde{x}_i^{(j)}$$

## Profiling Players

This can be applied to football statistics, where the scores mentioned previously are the values for each statistic. Then, the weighted average weights are chosen in order to value particular statitsics over others. Valued statistics are chosen based on the profile of player needed. For example, if a more defensive player is needed, then defensive statistics must be valued higher than offensive statistics. An example of a profile of these weights for a fullback is shown bellow.

![FB Profile](images/FB_Profile.png)

In this figure, the catagories used (such as defence) contain a collection of statistics such as defensive duels won ect. This can be changed to suit the user. For this particular profile, a strong defensive minded fullback who can set-up chances is needed. Hence, defenisve statistics are ranked highly in importance (weighted in 10's), then crossing ability and creative statistics are ranked as or next to most highly. This is then followed by dribbling or bringing the ball out from defence, which is not as desired for this profile but is taken into account. All other statistics involving goals and getting into the box are not needed for this profile of fullback.

## Resulting Ranking

Finally, to demonstrate how it can be used to highlight valuable players, a profile for central defenders applied. This is a simple profile, which requires a defender who excells defensively, reasonably good with the ball and possibly chips in with the a few goals. After applying this profile (or weights), the single score found from the weighted average is sorted and the highest scoring players for this profile are shown below.

![](images/CB_NLS_Ranking_leaflet_2021.png)

## Final Remarks

Here is a list of considerations when using this method:

  - Be careful not to include too many catagories when applying a profile. If too many statistics are condsidered, the end score may become dependent on too many factors and introduce co-linearlity.

  - Explicit positions in rankings do not necessarily represent better players. It is just a guide to evaluate which players are more suited to the profile used.
  
  - When looking at final scores, the top 20 are the most suited. Anything lower than this may not suit the profile at all and scores highly because they perform well in a particular statistic.
  
  - 0-10 scoring system is not presented to be the optimal choice, it is just the most common scoring system. A smaller or large range is also applicable.
