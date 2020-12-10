# soccer_analysis


## Data source:
https://figshare.com/collections/Soccer_match_event_dataset/4415000

## Purpose
Sports analytics have been gaining traction over the last several years to support improved decision making. This can include insight into team building, tactics, and game preparation. For this assessment I aquired a large collection of spatio-temporal data on soccer (football) matches in five top-flight professional leagues in Europe that covers every game from the 2017-2018 season. This data contains a record for every on-field event, including the type of event (pass, foul, shot) and the location where it happened on the field. This was all collected through a process involving individuals watching matches and creating a record for each event based on these specific metrics.

### Samples of event categories
<img src="https://github.com/estieve/soccer_analysis/blob/main/images/event_mapping.PNG" width="300" height="400" >

The goal is to leverage this information to analyze in-game tactics and also predict match outcomes (wins/losses).

## Code
There are several collections of code that were used to execute this process. They were kept separate to maximize the ability to keep the processing organized into a handful of "tools". The order of activity for the tools being run in order to follow the path of this discussion is:
### 1. Compile (https://github.com/estieve/soccer_analysis/blob/main/Compile.ipynb)
### 2. Visualization (https://github.com/estieve/soccer_analysis/blob/main/Visualization.ipynb)
### 3. Predictive_Data_Prep (https://github.com/estieve/soccer_analysis/blob/main/Predictive_Data_Prep.ipynb)
### 4. EDA (https://github.com/estieve/soccer_analysis/blob/main/EDA.ipynb)
### 5. Machine_Learning (https://github.com/estieve/soccer_analysis/blob/main/Machine_Learning.ipynb)
### 6. Machine_Learning_noDraw (https://github.com/estieve/soccer_analysis/blob/main/Machine_Learning_noDraw.ipynb)

## Compile Data
### Source Data
The data is stored in a relational database and each table is downloaded from the source as a JSON file. In order to utilize it we will need to assemble the tables into a single dataframe. In this scenario we are leveraging tables for events, matches, competitions, players, and teams. The Compile code handles this process (https://github.com/estieve/soccer_analysis/blob/main/Compile.ipynb) and outputs a single csv (soccer.csv) that can be utilized for the remainder of the processes. 

### Updating Columns
A key part of the analysis is based on the available spatial information, this is stored as a dictionary in the "positions" field that includes the start and end location as coordinates based on a percentage of the field from the origin point (0,0). This can be parsed into two columns ('x' and 'y'), with only the start location coordinates being utilized. It is important to note that these (x,y) values are based on a percentage of the field from the origin (0,0) rather than an absolute location. This needs to be converted to a value between 0 and 120 for the 'x' and 0 and 80 for the 'y' to properly represent a location on the field. Then to better group this information and make it more friendly for the machine learning process the field is divided into grids and each event is assigned to one of these grids based on where the (x,y) coordinates fall.

![position](https://github.com/estieve/soccer_analysis/blob/main/images/position.PNG)

![grid](https://github.com/estieve/soccer_analysis/blob/main/images/grid.PNG)

## Visualization
The first analysis involves taking the (x,y) coordinates to create a visualization of in-game tactics. For this scenario the data for a single team during a single game is extracted from our dataframe. Then a centroid is calculated for each player which can be displayed on a visualization of the pitch. This can then be leveraged to compare opposing teams in the same game to see how their tactics complement the on-field action.

### Centroids
![centroid](https://github.com/estieve/soccer_analysis/blob/main/images/centroid.png)

### Visualize tactics
![tactics](https://github.com/estieve/soccer_analysis/blob/main/images/tactics.png)
### Compare to the official formation
![formation](https://github.com/estieve/soccer_analysis/blob/main/images/formation.png)

-------------------------------------------------------------------------------------------------------------------------
### Compare Tactics
For this sample we are looking at a game that occured between Tottenham Hot Spur and Arsenal during week 12.

### Tottenham tactics
![Tott](https://github.com/estieve/soccer_analysis/blob/main/images/Tott.png)
-------------------------------------------------------------------------------------------------------------------------
### Arsenal tactics
![Ars](https://github.com/estieve/soccer_analysis/blob/main/images/Ars.png)

This provides unique insight as to how the play on the field evolved and where the action was happening for the various players. Arsenal appears to have played a more spread out and attacking style, while Tottenham was very condenced in the middle and attacking from the left wing. The visualization code (https://github.com/estieve/soccer_analysis/blob/main/Visualization.ipynb) allows for a user to enter a team name, game week, and game half to then return a dataframe of centroids that can be graphed on the pitch visualization. The flip_field function then allows to chart the opposing team in a way that they are flipped relative to the initial team. This optimizes the ability to assess the impact each team's on field tactis has on the other.

## Predictive Data Preparation
The output from the compile step provides us with a baseline dataframe that can be leveraged for the visualizations and EDA, but in order to leverage the data for machine learning additional data engineering is required utilizing the predictive_data_prep tool (https://github.com/estieve/soccer_analysis/blob/main/Predictive_Data_Prep.ipynb). To begin with there are some events that are unlikely to have an impact on predicting game outcomes; these include events like whistles, throw ins, protests, and ball out of field. These get dropped and then all events from a unique game are compiled together into a single record with a result of win, loss or draw. The data is aggregated by event and grid location, this means that each grid-action pair creates a series that sums up the total number of time the given event happened in the grid. Some events will never happen in certain grids, for example a save is not going to happen in the middle of the field. To handle this the final dataframe is examined for any series that contain only zero values, and these are then dropped. The final output is a dataframe with 210 unique fields.

## Predictive Analysis
Utilizing the output of the predictive data prep step the predictive process begins with the testing of four different machine learning algorithms: decision tree, random forest, k-nearest neighbors, and neural network.
### Decision Tree
Utilizing pruning methods the model was optimized to reduce overfitting.
![pruning](https://github.com/estieve/soccer_analysis/blob/main/images/pruning.PNG)

#### Results
![dec_tree](https://github.com/estieve/soccer_analysis/blob/main/images/dec_tree.PNG)

### Random Forest
Using the same parameters from the decision tree to run the random forest.

#### Results
![ran_forest](https://github.com/estieve/soccer_analysis/blob/main/images/ran_forest.PNG)

### K-Nearest Neighbors
Testing for optimal k-value returns a value of 19.

![k-value](https://github.com/estieve/soccer_analysis/blob/main/images/KNN_k.PNG)

#### Results
![KNN](https://github.com/estieve/soccer_analysis/blob/main/images/KNN.PNG)

### Neural Network
Grid search was used to optimize the parameters for the MLP Classifier. 

![loss](https://github.com/estieve/soccer_analysis/blob/main/images/loss_curve.PNG)

#### Results
![NN](https://github.com/estieve/soccer_analysis/blob/main/images/NN.PNG)

### Assessment
The models seem to have trouble classifying draws, in every one we see this category throwing off the accuracy. Further analysis would be beneficial here.

## EDA - Draws
With the noticable ambiguity in our models related to draws it is beneficial to look at the data around the three outcome categories. Using the EDA tool (https://github.com/estieve/soccer_analysis/blob/main/EDA.ipynb) will help to visualize more information on what may be happening here. In this process the event data is modified to simply examine counts for each event in a given game without taking location into account. The purpose here is to see how each event stacks up accross the various outcomes and determine where the ambiguity with draws may be coming from.

This chart shows the frequency of specific events for all three categories plotted on top of each other. We can see that there are times where draws closely mirror wins and others where they closely mirror losses. This is likely why the models are struggling with classifying draws, they tend to not have unique characteristics. 

![EDA](https://github.com/estieve/soccer_analysis/blob/main/images/EDA.PNG)

## Predictive Analysis - Remove Draws
Taking the evaluation above into account, the next step is to remove the draws and re-run the machine learning models to see if there is an improvement with draws removed from the results. The same processes were run to optimize the models.

### Results 

![no_Draw](https://github.com/estieve/soccer_analysis/blob/main/images/no_draw.PNG)

These results are much better, and it is apparent that the neural network is the optimal model for predictive classification on this dataset.

## Challenges
The data utilized here is very complex, and as such there is a high likelyhood that it isn't being utilized to its full potential in this analysis. Handling the spatial aspects of the dataset is a unique challenge and adds nuance that really needs to be leveraged for any assessments. Further research and experimentation would be likely to result in improvements to how this aspect of the data is utilized in the project. There is also the challenge around draws and how best to classify these, it is possible that anyone wanting to perform similar assessments would prefer not to predict such occurances. How often do teams really go into games asking whether they have the potential of drawing? Most likely the question is "are we going to win or lose"? Either way, more investigation into the ambiguity around this is definitely in order.

## Conclusions
The analysis performed here is a great start, but we are only scratching the surface of what can be accomplished with this incredibly detailed dataset. The opportunities to leverage things such as player and team quality would be significant. No two teams are created equally, and the same can be said for players. Having some sort of metric that allows us to incorporate these features could lead to more significant results. Another thought is to produce a value representing home-field-advantage, there is significant evidence to support the assumption that playing at a home field in front of supporters has a positive outcome on a team's success. The last important piece would be to incorporate team play insights to properly predict results as the season progresses. Previous outcomes could be compiled into a metric as the season goes on to further enhance predictions.

There is a lot to be gained from complex analytics provided in this dataset, and the opportunities to leverage the data for insight are enormous. 

## References
Pappalardo et al., (2019) A public data set of spatio-temporal match events in soccer competitions, Nature Scientific Data 6:236, https://www.nature.com/articles/s41597-019-0247-7

Pappalardo et al. (2019) PlayeRank: Data-driven Performance Evaluation and Player Ranking in Soccer via a Machine Learning Approach. ACM Transactions on Intellingent Systems and Technologies (TIST) 10, 5, Article 59 (September 2019), 27 pages. DOI: https://doi.org/10.1145/3343172
