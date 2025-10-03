# Exploring Spatiotemporal Flanders Traffic Data

The main objective of this work was to prepare Spatiotemporal Trajectory data using a methodology developed by Sirris. Focusing on mobility data for the Small Ring Road of Brussels. However, the current
task is even more significant: to study traffic data structures and prepare data for the entire Flemish region. This work discusses the application of various techniques for data reduction and initial processing. Challenges encountered during the work are discussed, as well as an analysis of the pros and cons of the employed tools. It is worth noting that the task of studying data preprocessing and primary processing to reduce input data was successfully completed. Throughout this experience, I gained valuable skills in working with real-world data. Collaborative efforts with my mentor to address ongoing challenges proved instrumental in enhancing the quality of our approach and the depth of our insights.

## Mission Objectives

- Initial data loading and exploration of the data structure
- Data cleaning
- Interpolation of missing data
- Data reduction approach
- Grouping of data to reduce dataset size
- Analysis of seasonality

## Installation

To run this project, you will need to install several Python libraries.
It is recommended to use a virtual environment.
Here’s how to set it up and install the required packages:

```bash
python -m venv venv
source venv/bin/activate   # On Linux/Mac
venv\Scripts\activate      # On Windows

pip install numpy pandas plotly seaborn matplotlib

import json
import warnings
import numpy as np
import pandas as pd
import plotly.express as px
import seaborn as sns; sns.set()
import matplotlib.pyplot as plt

```
## Exploration of Flanders road data

### Metadata parameters

At the moment, the number of sensors involved in the collection of traffic information in the Flemish region is 4477 positions.

| ![Map](/images/output_11_1.png) |
|:--|
| *Placement of sensors in the Flemish region* |

The output below provides info on the parameters.

- **R**:   ring lane
- **B**:   Special Crossing Bedding (BOB) or bus lane  
- **TR**:  measurement of traffic in the opposite direction (usually at Tunnels) in the R lane  
- **P**:   breakdown lane  
- **W**:   parking or other road  
- **S**:   rush hour lane  
- **A**:   measuring point on a shaded part of the road
 
Numbering starts at R10 for the first regular lane on the main roadway. Numbering increases from right/trailer to left/fast lane.

Lanes with 09, 08, 07, ... are then to the right of this and usually represent entrance/exit lanes, merge lanes, newly added lanes, rush-hour lanes or PDOs.

Lanes 11, 12, 13, ... are then located to the left of lane R10.

The number 00 is used for a measuring point on the breakdown lane (P00).

The TR lane is identical to the corresponding R lane (TR10=R10,TR11=R11,TR12=R12,...), only the measuring point transmits only the minute data of the "ghost traffic".

| ![Map](/images/output_10_0.png) |
|:--|
| *Order and designation of lines on the main roadway* |

Each of the five classes of vehicles has the following characteristics:

- Voertuigklasse 1 = This vehicle class was provided vehicles with approximate lengths between 0m and 1.00m. This data is no longer used by AWV and the traffic center. (E.g., motorcycles.) The sporadic measurements in this vehicle class are little or not reliable..

- Voertuigklasse 2 = Passenger cars = vehicles with approximate length between 1.00m and 4.90m
- Voertuigklasse 3 Vans = vehicles with approximate length between 4.90m and 6.90m
- Voertuigklasse 4 = Unarticulated trucks = vehicles with estimated length between 6.90m and 12.00m (e.g.:Truck or tractor)
- Voertuigklasse 5 = Articulated trucks or buses= vehicles with approximate length longer than 12.00m (e.g.: truck+trailer, tractor+trailer or bus) voertuigsnelheid rekenkundig (vehicle speed arithmetic)= Sum (vi) / n (with vi = individual speed of a vehicle within this vehicle class)

Waarde bereik 0..200 km/h

voertuigsnelheid harmonisch = n / Som(1/vi) (met vi = individuele snelheid van een voertuig binnen deze voertuigklasse)

Special values:
- 251: Initiële waarde # Initial value
- 254: Berekening niet mogelijk # Calculation not possible
- 252: geen voertuigen binnen deze voertuigklasse gepasseerd. #no vehicles passed within this vehicle class


## Exploratory Data Analysis

### The Types of roads in Flanders region
The Flanders road transportation network stands as one of the densest in Europe, reflecting its pivotal role in facilitating connectivity and mobility. 

| ![Map](/images/output_00_6a.png) |
|:--|
| *Unique roads of Flander region*  |                             |


### The Sensor Locations by Local Processing Unit

Since one of the main tasks in the study of the data was to find the parameters by which it is possible to significantly simplify the spatiotemoral data, the analysis was carried out as grouped sensors according to the lve_nr1 feature .

| ![Map](/images/output_15_0.png) |
|:--|
| *The Sensor Locations by Local Processing Unit*  |

As the analysis showed, the number of sensors included in the group can be quite diverse. This is due to the technical parameters of connecting sensors. The most common LVE groups are three, four, six and seven sensors.

###  The Sensor Locations by Traffic Lines

The encoding of a specific lane of the road is coded in the 'Rijstrook' column
and has the following values:

- (B07) - special Crossing Bedding (BOB) or bus lane
- (B08) - special Crossing Bedding (BOB) or bus lane
- (B09) - special Crossing Bedding (BOB) or bus lane
- (P00) - breakdown lane
- (R06) - 4th additional lane on the right
- (R07) - 3rd additional lane on the right
- (R08) - 2nd additional lane on the right
- (R09) - 1st additional lane on the right
- (R10) - 1st lane
- (R11) - 2nd lane
- (R12) - 3rd lane
- (R13) - 4th lane
- (S09) - rush hour lane
- (TR09) - 1st additional lane on the right
- (TR10) - 1st ghost line
- (TR11) - 2nd ghost line
- (TR12) - 3rd ghost line
- (W09) - parking or other way


| ![Map](/images/output_18_0.png) |
|:--|
| *Types of roads included in the system* |

During the study of the Ident_8 characteristics, it was discovered that two different
direction of ”main road” having the last four digits 0001 and 0001 are quite balanced
(1447 verse 1521 respectively).

### Brussels Big Ring Data

In our work, we will mainly focus on examining the data related to the zone of the Brussels Ring Road, which is represented by the road R0. In this section surrounding Brussels, there are 193 sensors installed, making it the largest cluster among all the ones presented in the region.


| ![Map](/images/output_32_0.png) |
|:--|
| *Brussels Big Ring - R0*       |

At the initial stage of data exploration for the Brussels Ring Road, three sensors [’3162’, ’3163’, ’3164’] were selected, which belong to the same group (lve_nr) of
sensors located at the largest of the five multi-level interchanges, Knooppunt Groot-Bijgaarden west

| ![Map](/images/output_32_1.png)   |
|:--|
| *Roundabout of Groot-Bijgaarden* |

At this interchange, the central traffic lanes were selected as they were considered to best represent the average traffic characteristics along the Brussels Ring Road.

| ![Map](/images/output_32_2.png) |
|:--|
| *Locations 3162, 3163, 3164* |

The choice of multiple sensors was motivated by the goal of reducing the amount of data while retaining the ability to analyze the system as a whole.

### Road Types of R0

| ![Map](/images/output_28_0.png) |
|:--|
| *Road Types of R0* |

Let’s examine the types of roads represented in the R0 cluster for the Brussels Big Ring. There are only four types of roads: R10, R11, R12, and R13, with one, two, three, and four lanes, respectively. Additionally, the four-lane road (R13) is represented by only one instance in the data set.

### Missing Timestamps

During the study of missing data from sensors, for example, we analyzed the number of monthly gaps for the sensor [’3162’]. The total time when the sensor did not send a signal to the system was also calculated. This time can be quite significant. These gaps cannot be easily recovered using common date mining methods.

| ![Map](/images/output_40_0.png) |
|:--|
| *Extracting information about missing data* |

## Data Mining Techniques

### Data Reduction Approach

Ultimately, the main strategy was to reduce the amount of data for subsequent work with them and identify insights. The idea is to merge information about different types of vehicles together. Since the traffic database in the Flemish region contains information about four different types of vehicles, we therefore have data on the number of vehicles passing and the harmonic speed for all four types (8 features). When we combine these data, the output we have is the combined traffic flow — the number of passing cars and their harmonic speed. In other words, we are preparing data for consideration in a more general way, which is logical for the initial stage of work on this large project.

### Enter-exit sensors

Since only major roads have 0001 and 0002 endings in the Ident_8 parameter, we can exclude all other sensors to start the traffic analysis. Excluded sensors are installed on auxiliary roads intended for entry and exit from main roads. But first, let’s look at these types of sensors on a map.




## Rolling Window

Time-series data often contain fluctuations and irregularities that can make analysis and interpretation challenging. In such cases, applying techniques like Rolling windows with a 5-minute interval can significantly contribute to stabilizing the data. The application of Rolling windows also facilitates data interpolation, enabling the filling of missing values or gaps within the time-series. By allowing interpolation intervals of up to 4 minutes, the technique helps reduce the number of empty rows, effectively maximizing the data utilization. The strategic application of rolling windows with a 5-minute interval presents a powerful solution for stabilizing time-series data, mitigating short-term fluctuations.

| ![Map](/images/output_70_1.png) |
|:--|
| *Sensor [’3162’] data before applying Rolling window* |

As can be seen that the data on traffic intensity changes during two days (from 2022-10-01 to 2022-10-03 for class 'class_2_verkeersintensiteit') have a discrete nature, and interpreting seasonality (in this case, daily) is possible but quite challenging.Retry

| ![Map](/images/output_70_2.png) |
|:--|
| *Sensor [’3162’] data after applying Rolling window size=5* |

Furthermore, the following table presents statistics regarding the reduction in the number of rows with missing data, based on the size of the Rolling Window. It is important, however, to strike a balance between data smoothing and the inevitable information loss it entails.

| ![Map](/images/output_70_3.png) |
|:--|
| *Sensor[’3162’] - NaN rows and window size dependency correlation* |

As much as we may desire, Rolling Windows is not a miraculous solution for handling missing data. Even when expanding the window size up to 300 minutes, a substantial amount of missing data still persists. These data gaps are characterized by prolonged time periods and can be attributed to technical sensor malfunctions requiring significant time for resolution, or road surface replacement, which also leads to significant data gaps.

| ![Map](/images/output_70_4.png) |
|:--|
| *Sensor [’3162’] Rolling window size=300* |


## Seasonality

One of the benefits of using a tool like Rolling Windows is that when you apply enough smoothing to the data, it becomes easier to detect seasonality. So Figure 6.6 illustrates the data received from the ['3162'] sensor for three weeks in October 2022. Seasonality by days of the week is easy to read on the graph.

| ![Map](/images/output_80_0.png) |
|:--|
| *Seasonality. Sensor [’3162’] Rolling window size=100* |

## Grouping sensors by Ident_8 and Kmp_Rsys

The concept of grouping sensors installed on the same road and in the same direction of travel was addressed using two distinct features, denoted as Ident_8 and
Kmp_Rsys. Feature Ident_8 represents a unique road name, while feature Kmp_Rsys indicates the position from the beginning of the road. By grouping sensors based on
features Ident_8 and Kmp_Rsys, we form clusters of sensors situated on the same road (whether single or multi-lane) and in the same travel direction.

| ![Map](/images/output_00_5.png) |
|:--|
| *Visual representation of the grouping by Ident_8 and Kmp_Rsys* |

```bash
grouped_df = locations_df.groupby('lve_nr')['unieke_id'].unique().reset_index()
```

## Workflow

| ![Map](/images/output_90_0.png) |
|:--|
| *Data Processing Workflow Flowchart* |

## Results

The key outcome of my internship experience is the preparation of a transportation traffic database for the Flemish region. Through this work, a significant reduction in data volume was successfully achieved.  For instance, raw data solely from the Big Brussels Ring (R0) since April 2021 amounted to 26.3 GB, which is just 4.5% of the total number of sensors installed across the entire Flemish region (193 out of 4477 items). The reduction in data volume was accomplished by consolidating data from sensors placed on the same road segment and operating in the same direction (sensor grouping). Furthermore, the decision was made to abandon the categorization of vehicles based on their dimensions ('verkeersintensiteit').







