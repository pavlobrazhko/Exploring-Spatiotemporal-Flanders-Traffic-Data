# Exploring-Spatiotemporal-Flanders-Traffic-Data

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
The output below provides info on the parameters. Some important info summarized:

Ident_8 = Reference points along the numbered roads #Pavlo. Highway name and direction
lve_nr = Number of the LVE (Local Processing Unit). An LVE processes the data of a group of measuring points.
Kmp_Rsys = Reference point used in the systems of the Flemish Traffic Center.
Lane = The letter indicates the type of lane.

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

(The measurement data for TR10 come from the same measurement loops as those for R10.)


| ![Map](/images/output_11_1.png) |
|:--|
| *Placement of sensors in the Flemish region* |

- Voertuigklasse 1 = This vehicle class was provided vehicles with approximate lengths between 0m and 1.00m. This data is no longer used by AWV and the traffic center. (E.g., motorcycles.) The sporadic measurements in this vehicle class are little or not reliable..

- Voertuigklasse 2 = Passenger cars = vehicles with approximate length between 1.00m and 4.90m
- Voertuigklasse 3 Vans = vehicles with approximate length between 4.90m and 6.90m
- Voertuigklasse 4 = Unarticulated trucks = vehicles with estimated length between 6.90m and 12.00m (e.g.:Truck or tractor)
- Voertuigklasse 5 = Articulated trucks or buses= vehicles with approximate length longer than 12.00m (e.g.: truck+trailer, tractor+trailer or bus) voertuigsnelheid rekenkundig (vehicle speed arithmetic)= Sum (vi) / n (with vi = individual speed of a vehicle within this vehicle class)

Waarde bereik 0..200 km/h

voertuigsnelheid harmonisch = n / Som(1/vi) (met vi = individuele snelheid van een voertuig binnen deze voertuigklasse)

Bijzondere waarden: # Special values:
- 251: Initiële waarde # Initial value
- 254: Berekening niet mogelijk # Calculation not possible
- 252: geen voertuigen binnen deze voertuigklasse gepasseerd. #no vehicles passed within this vehicle class

![Distribution of Some Feature](/images/output_00_0.png)

## Grouping sensors

The concept of grouping sensors installed on the same road and in the same direction of travel was addressed using two distinct features, denoted as Ident_8 and
Kmp_Rsys. Feature Ident_8 represents a unique road name, while feature Kmp_Rsys indicates the position from the beginning of the road. By grouping sensors based on
features Ident_8 and Kmp_Rsys, we form clusters of sensors situated on the same road (whether single or multi-lane) and in the same travel direction.

![Visual representation of the grouping by Ident_8 and Kmp_Rsys](/images/output_00_5.png)

```bash
grouped_df = locations_df.groupby('lve_nr')['unieke_id'].unique().reset_index()
```

![Grouping sensors](/images/output_15_0.png)




