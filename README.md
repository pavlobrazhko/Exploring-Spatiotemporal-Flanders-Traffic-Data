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

### documentation
- Voertuigklasse 1 = Deze voertuigklasse was voorzien voertuigen met geschatte lengte tussen 0m en 1,00m. Deze gegevens worden door AWV en het verkeerscentrum niet meer gebruikt. (vb. moto's.) De sporadische metingen in deze voertuigklasse zijn weinig tot niet betrouwbaar.

- Voertuigklasse 2 = Personenwagens = voertuigen met geschatte lengte tussen 1,00m en 4,90m

- Voertuigklasse 3 = Bestelwagens = voertuigen met geschatte lengte tussen 4,90m en 6,90m

- Voertuigklasse 4 = Ongelede vrachtwagens = voertuigen metvgeschatte lengte tussen 6,90m en 12,00m (bv.:Vrachtwagen of trekker)

- Voertuigklasse 5 = Gelede vrachtwagens of bussen= voertuigen met geschatte lengte langer dan 12,00m

bv.: vrachtwagen+aanhangwagen, trekker+aanhangwagen of bus

voertuigsnelheid rekenkundig = Som (vi) / n (met vi = individuele snelheid van een voertuig binnen deze voertuigklasse)
voertuigsnelheid harmonisch = n / Som(1/vi) (met vi = individuele snelheid van een voertuig binnen deze voertuigklasse)

Waarde bereik 0..200 km/h

Bijzondere waarden: # Special values:
- 251: Initiële waarde # Initial value
- 254: Berekening niet mogelijk # Calculation not possible
- 252: geen voertuigen binnen deze voertuigklasse gepasseerd  # No vehicles passed within this vehicle class
