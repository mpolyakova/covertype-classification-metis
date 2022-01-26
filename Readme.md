# Spruce Fir Forest Classification 

## Abstract 
Many species in the United States are endangered. One of the more controvertial tactics is relocation - physically moving the endangered species to a new location. This is a risky tactic, because it puts both the animal at a disadvantage in a familiar territory, and risks the existing ecosystem in the new place by introducting something new. In recent years, however, as human activity and climate change threatens more habitats, the US Forest Service and National Park Service have been investigating this option more. Some success stories include the introduction of wolves into the Yellowstone park ecosystem, and bison relocation. 

This is, however, a very manual process for the researchers examining potential relocation places. It requires physically scouting locations, which are frequently remote and difficult to get it. In an effort to aid the researchers, I've estimated the forest type from cartographic variables collected by the USFS yearly to help narrow down the potential locations. 


## Design 
The Spruce Fir Appalachian forest is home to many endangered species. Some of them are only found there, such as the North Carolina Flying Squirrel and Gnome Rock Lichen. Using data collected from forests in Colorado, I attempt to find Spruce Fir forest areas based on cartographic variable collected by USFS, such as elevation, and soil types. Large areas of Spruce Fir forest in Colorado can be investigated as potential relocation areas for endangered species from the Appalachian forests. 

## Data 
The dataset contains 580K points from the Theodore Roosevelt National Forest. Each point represents a 30 by 30 meter area in a wilderness. The actual forest type is labeled using GIS data collected by the USFS. The data set contains 12 features, including 2 categorical features (Soil type and wilderness). Using the ELU number for soil type, I was able to also collect the climate zone, and geo zone for each gridpoint, and well as a description of the soil. Additionally, I converted the aspect (range 0-360 degrees) into a direction (NSEW) to narrow the possible range of values, and remediate that 360 and 0 are equivalent. 
The dataset is part of the UCI Machine Learning Repository and was donated in 1998.
The Spruce Fir forest is common in this dataset, (46% of observations)

Since no location data is available in the dataset, additional features such as updated fire locations, yearly temperatures, and potential new roads are not available. 

### Feature Description
Elevation / quantitative /meters / Elevation in meters 
Aspect / quantitative / azimuth / Aspect in degrees azimuth 
Slope / quantitative / degrees / Slope in degrees 
Horizontal_Distance_To_Hydrology / quantitative / meters / Horz Dist to nearest surface water features 
Vertical_Distance_To_Hydrology / quantitative / meters / Vert Dist to nearest surface water features 
Horizontal_Distance_To_Roadways / quantitative / meters / Horz Dist to nearest roadway 
Hillshade_9am / quantitative / 0 to 255 index / Hillshade index at 9am, summer solstice 
Hillshade_Noon / quantitative / 0 to 255 index / Hillshade index at noon, summer soltice 
Hillshade_3pm / quantitative / 0 to 255 index / Hillshade index at 3pm, summer solstice 
Horizontal_Distance_To_Fire_Points / quantitative / meters / Horz Dist to nearest wildfire ignition points 
Wilderness_Area (4 binary columns) / qualitative / 0 (absence) or 1 (presence) / Wilderness area designation 
Soil_Type (40 binary columns) / qualitative / 0 (absence) or 1 (presence) / Soil Type designation 
Climate_Zone(7 binary columns, only 5 used)/climate zone designation 
Geo_Zone(4 binary columns)/Ingeous and metamorphic, Glacial, Alluvium and Mixed Sedimentary. 
Tree(Binary TARGET VARIABLE) - 1 if Spruce Fir, 0 if not 

## Algorithms

### Feature Engineering 
I mapped the Soil type to ELU data to gather climate zone and geo zone. 
I converted aspect to a direction 
I removed unnecessary soil types from the model when I determined they were unused by the model in order to decrease feature space. 

The data was split into training, validation and test datasets. Training and validation were used to help with parameter tuning, and testing to gather the final results. 


### Metrics 
The dataset is balanced, and the Spruce Fir forest is common in this area. Because the selected locations must be visited physically by researchers, and the target is common in this area, we are more concerned with limiting false positives than eliminating false negatives. For this iteration, accuracy and precision were chosen as the metrics of choice. 

If this project were extended to much rarer forest types, false negatives would become more important, since we would be more interested in not missing a potential area. In that case, the metric of choice would be changed to F1. 

### Tools 
Sklearn was used heavily for modeling Logistic Regression and Random Forest 
Sklearn metrics and confusion matrix was used to determine metrics 
XGBoost to run and tune the XGBoost model. 
Sklearn GridSearchCV to help tune XGBoost parameters. 
Seaborn and matplotlib for eda graphs and displays 


### Model
Logistic regression, random forest and xgboost were fitted to the dataset, before setting on XGBoost as the slightly best model. Random Forest was very close, but slightly lower in all categories on the test set. 
XGBoost performed w/ accuracy score of .8 before parameter turning, and .97 after parameter tuning. 
I tuned the parameters by hand and using sklearns GridSearchCV. The ideal max depth was 11 with the models potentially using all features at all times - not feature bagging. The number of iterators was chosen as 1000, since improvements on the validation set score were negligible higher than this, but the model was slow to train. 

XGBoost feature importance was directly used to eliminate unnecessary features. The most important features were climate zone, and soil types which are very rocky and igneous, as well as elevation. This is expected, as Spruce Fir forests tend to thrive at higher elevations due to thin, spread out root systems. 

The training set was 20% of the dataset, validation at 40, and test at 40. I retrained the models on the combination of validation and training sets before testing on the testing set. 

The metrics on the test set are: 


#### xgboost 
accuracy : 0.977044383726684  
precision: 0.9727319031994409  
recall : 0.9643813416009019  
f1 : 0.9685386234836915 

#### random forest
accuracy : 0.958309847034272  
precision : 0.9599663541831747  
recall : 0.9247815670800451  
f1  : 0.942045542908070  

#### logistic regression 
accuracy : 0.7526344097588262  
precision : 0.6846637561581287  
recall : 0.6022406989853438  
f1 : 0.6408127307829281  

XGBoost outperforms random forest slightly. 
The Confusion matrix for XGBoost shows 2303 false positive observations, 3301 false negative observations, and 82K true positive observations, correctly identifying Spruce Fir forests in the dataset. 

## Conclusion 
The model by itself has a higher false positive rate than would likely be required by researchers. This can be mitigated by additional filtering, selecting large areas of continuous Spruce Fir forest are targets, rather than individual grid points. Additionally, many species require additional contraints above the forest type. An example is the Spruce Fir Moss Spider, which lives mostly on north facing steep slopes. Finding blocks of such area will help narrow down the areas to visit. Additional temperature, and forest information will help select the area as well. 


## Future Data Work
### Newer Data and More Features 
The dataset is from 1998. While the forest remains a wilderness area, this is old data. New data must be collected from the US Forest Service GIS, which is updated and tracked yearly. This data is in a geographic format, which would need to be overlaid and divided into sections. Features must be parsed out. This, however would provide location data, and therefore allow for additional features, such as temperature, forest maturity, animal migration patterns. Potential rates of change in the forest could also be estimated using historic data. 

### Rarer Ecosystems 
The Spruce Fir forest is common in the Colorado Wilderness area. This is a very general descriptions. As more precise ecosystems are identified, we could update our models to train on rarer ecosystems, and more precise conditional. Additional sample handling would be needed to mitigate the class size differences, but we could further help narrow the search are for researchers. 


# Appendix 

## Metrics for Models Graphed
XGBoost performs slightly better than Random Forest. Logistic Regression trails by a lot. 
![Alt text](/classificationgraphs/metrics_for_models.png?raw=true "Metrics per Model")

## Spruce Fir Forest histogram by Elevation 
Orange represents the Spruce Fir Forest, our positive class. Blue all other forest types. This shows that Spruce Fir forests are typically found at higher elevations, but with a lot of crossover with other forest types. 
![Alt text](/classificationgraphs/elevation_hist.png?raw=true "Elevation Breakdown")


## Feature Importance Ranked 
Numeric Soil types are replaced with their descriptions. 
Top features include very stony soil types, subglacial climate zone, and igneous and metamorphic geo zone, as well as elevation. Least important are shade amounts, slope, and aspect. 
![Alt text](/classificationgraphs/feature-importance.png?raw=true "Feature Importance")

## Confusion Matrics for XGBoost Tested using the Test Set 
Confusion matrix shows 2302 false positive observations, and 3033 false negative observations. This is a small percentage compared to 150K correctly predicted negative class, and 82K correctly predicted positive class observations, however many still be problematic for researchers. 
![Alt text](/classificationgraphs/confusion.png?raw=true "XGBoost Confusion Matrix")

## Breakdown by Climate Zones
Subalpine and alpine climate zone contain the larger percentage of Spruce Fir forests. 
Subalpine is our most represented climate zone in the dataset. Alpine is highest elevation, subalpine similar to montane slightly below. 
![Alt text](/classificationgraphs/climate-hist.png?raw=true "Climate Zones")
![Alt text](/classificationgraphs/geo-elevation.png?raw=true "Climate Zones by Elevation")

## Geozone to Elevation
Plotting boxplots of geozones to elevation by our target variable shows a lot of overlaying between positive and negative classes, however our positive classe tends to sit at slightly higher elevation than negative. The Igneous and metamorphic class has the largest spread of elevation values by both positive and negative classes. 
![Alt text](/classificationgraphs/geozone-elevation.png?raw=true "Geozone to Elevation")



## Other Features Exploration
Distributions between Spruce Fir (orange) and not (blue) overlay each other very well, therefore by themlselves are not great for predicting forest type. 
![Alt text](/classificationgraphs/slope-hist.png?raw=true "Slopes")











