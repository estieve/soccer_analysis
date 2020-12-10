# soccer_analysis


## Data source:
https://figshare.com/collections/Soccer_match_event_dataset/4415000

## Purpose
Sports analytics have been gaining traction over the last several years to support improved decision making. This can include insight into team building, tactics, and game preparation. For this assessment I aquired a large collection of spatio-temporal data on soccer (football) matches in five top-flight professional leagues in Europe that covers every game from the 2017-2018 season. This data contains a record for every on-field event, including the type of event (pass, foul, shot) and the location where it happened on the field. 

### Samples of events
<img src="https://github.com/estieve/soccer_analysis/blob/main/images/event_mapping.PNG" width="300" height="400" >

The goal is to leverage this information to analyze in-game tactics and also predict match outcomes (wins/losses).

## Code
There are several collections of code that were used to execute this process. They were kept separate to maximize the ability to keep the processing organized into a handful of "tools". The order of activity for the tools being run based to follow the path of the following discussion is:
### 1. Compile (https://github.com/estieve/soccer_analysis/blob/main/Compile.ipynb)
### 2. Predictive_Data_Prep (https://github.com/estieve/soccer_analysis/blob/main/Predictive_Data_Prep.ipynb)
### 3. Visualization (https://github.com/estieve/soccer_analysis/blob/main/Visualization.ipynb)
### 4. EDA (https://github.com/estieve/soccer_analysis/blob/main/EDA.ipynb)
### 5. Machine_Learning (https://github.com/estieve/soccer_analysis/blob/main/Machine_Learning.ipynb)
### 6. Machine_Learning_noDraw (https://github.com/estieve/soccer_analysis/blob/main/Machine_Learning_noDraw.ipynb)

## Compile Data
### Source Data
The data is stored in a relational database and each table is downloaded from the source as a JSON file. In order to utilize it we will need to assemble the tables into a single dataframe. The Compile code handles this process (https://github.com/estieve/soccer_analysis/blob/main/Compile.ipynb) and outputs a single csv (soccer.csv) that can be utilized for the remainder of the processes. 

### Updating Columns
A key part of the analysis is based on the available spatial information, this is stored as a dictionary in the "positions" field that includes the start and end grid location based on a percentage of the field from (0,0). This can be parsed into two columns ('x' and 'y'), with only the start location utilized. It is important to note that the (x,y) values in this field are based on a percentage of the field from the origin (0,0). This needs to be converted to a value between 0 and 120 for the 'x' and 0 and 80 for the 'y'. Then to better group this information and make it more friendly for the machine learning process the field is divided into grids and each event is assigned to one of these grids based on where the starting coordinates fall.

![position](https://github.com/estieve/soccer_analysis/blob/main/images/position.PNG)

![grid](https://github.com/estieve/soccer_analysis/blob/main/images/grid.PNG)

## Predictive Data Preparation
The output from the compile step provides us with a baseline dataframe that can be leveraged for the visualizations and EDA, but in order to leverage the data for machine learning additional data engineering is required. 

## Visualization
The first analysis involves taking the (x,y) coordinates to create a visualization of in-game tactics. For this scenario the data for a single team during a single game is extracted from our dataframe. Then a centroid is calculated for each player which can be displayed on a visualization of the pitch. This can then be leveraged to compare opposing teams in the same game to see how their tactics complement the on-field action.

### Centroids
![centroid](https://github.com/estieve/soccer_analysis/blob/main/images/centroid.png)

### Visualize tactics
![tactics](https://github.com/estieve/soccer_analysis/blob/main/images/tactics.png)
### Compare to the official formation
![formation](https://github.com/estieve/soccer_analysis/blob/main/images/formation.png)

-------------------------------------------------------------------------------------------------------------------------
### Tottenham tactics
![Tott](https://github.com/estieve/soccer_analysis/blob/main/images/Tott.png)
-------------------------------------------------------------------------------------------------------------------------
### Arsenal tactics
![Ars](https://github.com/estieve/soccer_analysis/blob/main/images/Ars.png)

This provides unique insight as to how the play on the field evolved and where the action was happening for the various players. Arsenal appears to have played a more spread out and attacking style, while Tottenham was very condenced in the middle and attacking from the left wing. The visualization code (https://github.com/estieve/soccer_analysis/blob/main/Visualization.ipynb) allows for a user to enter a team name, game week, and game half to then return a dataframe of centroids that can be graphed on the pitch visualization. The flip_field function then allows to chart the opposing team in a way that they are flipped relative to the initial team. This optimizes the ability to assess the impact each team's on field tactis has on the other.

## Predictive Analysis


## References
Pappalardo et al., (2019) A public data set of spatio-temporal match events in soccer competitions, Nature Scientific Data 6:236, https://www.nature.com/articles/s41597-019-0247-7

Pappalardo et al. (2019) PlayeRank: Data-driven Performance Evaluation and Player Ranking in Soccer via a Machine Learning Approach. ACM Transactions on Intellingent Systems and Technologies (TIST) 10, 5, Article 59 (September 2019), 27 pages. DOI: https://doi.org/10.1145/3343172
