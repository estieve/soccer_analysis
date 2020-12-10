# soccer_analysis


## Data source:
https://figshare.com/collections/Soccer_match_event_dataset/4415000

## Purpose
Sports analytics have been gaining traction over the last several years to support improved decision making. This can include insight into team building, tactics, and game preparation. For this assessment I aquired a large collection of spatio-temporal data on soccer (football) matches in five top-flight professional leagues in Europe. This data includes a record for every on-field event during each game througout the season. Events include features such as passes, fouls, shots, duels, and touches:

<img src="https://github.com/estieve/soccer_analysis/blob/main/images/event_mapping.PNG" width="300" height="400" >

The goal is to leverage this information to analyze in-game tactics and also predict match outcomes (wins/losses).

## Compile Data
### Source Data
The data is stored in a relational database and each table is downloaded from the source as a JSON file. In order to utilize it we will need to assemble the tables into a single dataframe. The Compile code handles this process (https://github.com/estieve/soccer_analysis/blob/main/Compile.ipynb) and outputs a single csv (soccer.csv) that can be utilized for the remainder of the processes. 

### Updating
A key part of the analysis is based on the available spatial information, this is stored as a dictionary in the "positions" field that includes the start and end grid location based on a percentage of the field from (0,0). This can be parsed into two columns ('x' and 'y'), with only the start location utilized. Then to better group this information and make it more friendly for the machine learning process the field is divided into grids and each event is assigned to one of these grids based on where the starting coordinates fall.

<img src="https://github.com/estieve/soccer_analysis/blob/main/images/position.PNG" width="100" height="100" >

![grid](https://github.com/estieve/soccer_analysis/blob/main/images/grid.PNG)

## Visualization


## Predictive Analysis


## References
Pappalardo et al., (2019) A public data set of spatio-temporal match events in soccer competitions, Nature Scientific Data 6:236, https://www.nature.com/articles/s41597-019-0247-7

Pappalardo et al. (2019) PlayeRank: Data-driven Performance Evaluation and Player Ranking in Soccer via a Machine Learning Approach. ACM Transactions on Intellingent Systems and Technologies (TIST) 10, 5, Article 59 (September 2019), 27 pages. DOI: https://doi.org/10.1145/3343172
