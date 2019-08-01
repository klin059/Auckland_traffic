# Auckland_traffic
In this project, we analyse the change of Auckland traffic over time. 

Interactive visualization of the data is available at https://akl-traffic-vis.herokuapp.com/. 

[<img src="images/app_screen_shot.png" width = "800">](https://auckland-traffic-vis.herokuapp.com/)

## Data
Data was obtained from 
https://at.govt.nz/about-us/reports-publications/traffic-counts/ on 2019/07/02. 
There were two files available. File `traffic-counts-to-march-2018-2019.csv` contains records from
2015-11-04 to 2019-04-09 and the fields are explained on the website. File 
`traffic-counts-to-march-2018-2019.csv` contains data from 1958-10-01 to 2018-11-16, however the fields
are different from the other file and were not explained. The two files were merged by a common traffic measure: `7-day average traffic count`(ADT) along with 
the coordinates and the road name. We transfer the coordinates from NZTM to latitudes and longitudes since they are easier to work with python mapping packages (e.g. folium or mapbox). 
Highway data were removed since the they are quite sparse and have a much higher traffic volume than the majority of road data. 
Details on data cleaning and merging can be found at [1_data_cleaning.ipynb](1_data_cleaning.ipynb). 

## Exploratory analysis
In this analysis we use ADT as the representitive measure of traffic volume since ADT is one of the common fields in both files. We note that from the initial exploratory analysis of file `traffic-counts-to-march-2018-2019.csv`, we found all other traffic volume measures are highly correlated with ADT, so ADT is likely to be a good representative measure for traffic volume, at least for years 2018 and 2019.  

The merged data contains records from 1975 to 2019. By plotting the changes of ADT over time, we 
can see gradual increase in traffic volume clearly. 

![](images/volume_change_over_time.png)

In particular, we notice that the sampling density is much higher after around 2010. 
The traffic volume reached 
a peak around 2012, followed by a noticable decrease, both in the high percentile traffic volumes and in the number of traffic counts. 
The volume and the number of traffic counts then increase again and becomes more stable after 2015. 
We will focus on the records collected after 2010, since these records have similar sampling frequency and therefore are 
more comparible. The following figures presents a closer view at the data after 2010.

![](images/volume_change_over_time_after_2010.png)

The following figure depicts the historical ADT and the coordinate index. The coordinate indices are sorted by 
increasing maximum ADT and the points are colored by days elapesd from 2010-01-01. 
We can see that the maximum ADT of a location is usually the most recent ADT record.

[<img src="images/ADT_sorted_by_location.png" width = "500">](images/ADT_sorted_by_location.png)

Plotting the scatter plot of date versus coordinate index shows that the sampling pattern varys 
over time. For example, we can see there is a dense sampling of the coordinates with high ADT volumes around 
2011 and 2012 (circled in red) and a sparse sampling of coordinates with high ADT from mid 2012 
to early 2015 (circled in green).

[<img src="images/date_vs_coord_edit.PNG" width = "400">](images/date_vs_coord_edit.PNG)

Adding ADT as color scale shows that ADT over time for a coordinate appears to be stable. Note that there is 
a high sampling density near central Auckland.

![](images/date_vs_coord_colored_by_ADT.png)

Looking at the records after 2015 and index larger than 12000 shows that there are a few 
variations of ADT over time but a systematical change in ADT is not observed.

![](images/date_vs_coord_colored_by_ADT_subset.png)

The same conclusion is observed from the plot of ADT and coordinates. 

![](images/Yearly_ADT_and_cooredinates.png)

We also found certain locations are repeatly sampled and the variations between historical samples are 
reasonably consistant.

![](images/sampling_count_vs_coord.png) ![](images/index_with_filtered_sampling_count.png)

## Insights from exploratory analysis
Since both traffic count coordinates and traffic count frequencies vary over time, finding two 
comparible samples from the dataset to conduct statistical testing is difficult. An alternative approach to find insights or to identify 
traffic trends from the dataset is to construct a model on top of the data and 
make inference from model outputs.

# Predictive modelling
Notebooks for model building are available at [5 Data cleaning - new data obtained 20190728.ipynb](5%20Data%20cleaning%20-%20new%20data%20obtained%2020190728.ipynb) and [6_predictive_modelling.ipynb](6_predictive_modelling.ipynb). 

We use both [traffic count data](https://data-atgis.opendata.arcgis.com/datasets/average-daily-traffic-counts) and [traffic management level](https://data-atgis.opendata.arcgis.com/datasets/traffic-management-levels) data to build the predictive model. We focus on traffic count data after 2010 since the sampling pattern obtained before 2010 is very different from those after 2010 (as observed in the exploratory analysis). We add in date parts (i.e. year, month, week of the year, time elapsed etc) to the dataset and use random forest as the predictive model driven by  model interpretability.

We apply back-testing validation to see how well the predictive model works for forecasting. Using expanding-window back-testing with data after 2017 as the test set and with time steps of 2 months leads to a validation R-squared score of 86.86%. The validation score varies from 75% to 94%. The variations in validation scores could be caused by the change of road infrastructures. In fact, there were 3318 [infrastructure projects](https://data-atgis.opendata.arcgis.com/datasets/at-infrastructure-projects) in Auckland between 2017 and 2019. Adding data of the infrastructure projects could potentially improve the back-testing score. Unfortunately, the coordinate details are not available in tabular form so the data is not used. 

For the analysis of traffic change over time, we do not need to fit a time-series model since we are interested in the historical change of traffic volume, not the future change of traffic volume. By fitting the model with a shuffled train-validation split, we obtain a validation score of 97% and an out-of-bag validation score of 96.7%, indicating an excellent model fit. 

# Partial dependent plot
[Partial dependent plot](https://christophm.github.io/interpretable-ml-book/pdp.html) depicts the average effects of one or more features to the predicted value of interest. We apply Partial dependent plot to see how the traffic volume changes over time. 

![](images/pdp_plot.png)

From the plot, we can see the averaged increase in ADT is most observant during 2013 to 2015. We also notice the rapid increase of standard deviation (shaded area) after 2013, which can be caused by an increased variation of traffic volumes.

Work in progress...

# Summary
Work in progress...






