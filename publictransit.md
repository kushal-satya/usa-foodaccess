
## Analyzing SNAP and FBST Pantry Location Data with Public Transit Accessibility

### Introduction
A quick documentation of analyzing the accessibility of SNAP and FBST pantry locations concerning public transit in six counties: Chemung, Broome, Tioga, Steuben, Tompkins, and Schuyler. I use different data sources, processing steps, and mapping to provide some insight into how public transit can impact access to essential services like food.

### Data Sources
1. **SNAP Historical Location Data**
   - **Source**: USDA and CBPP
   - **Links**:
     - [Historical SNAP Retailer Locator Data (2023.12.31)](https://www.fns.usda.gov/sites/default/files/resource-files/historical-snap-retailer-locator-data-2023.12.31.zip)
     - [CBPP SNAP Retailers Database](https://www.cbpp.org/snap-retailers-database)
   
2. **FBST Pantry Location Data**
   - **Source**: Geocoded from addresses obtained from the Food Bank of the Southern Tier (FBST)
   
3. **Bus Stop Data for Six Counties**
   
| County   | Source                                                                                             | Date Added        | Source Type                                                                                       |
|----------|----------------------------------------------------------------------------------------------------|-------------------|--------------------------------------------------------------------------------------------------------|
| Chemung  | [GTFS feed](https://s3.amazonaws.com/datatools-511ny/public/Chemung_C_Tran.zip)                     | 2020-03-10 (over 4 years ago) | [Transit Land](https://www.transit.land/feeds/f-chemung~c~tran/versions/0bcf1c8169f040c13d9aec6124fb964a33bfb12c) |
| Broome   | [GIS Data Portal](https://gis.broomecountyny.gov/Website/GISWeb/Portal.htm?tab=data) \| [Bus Stops](https://gobroomecounty.com/transit/bus-stops) | No date provided | City GIS |
| Tioga    | No data available                                                                                  | -                 | N/A |
| Steuben  | [GTFS feed](https://s3.amazonaws.com/datatools-511ny/public/Steuben_County_Transit.zip)             | 2020-03-10 (over 4 years ago) | [Transit Land](https://www.transit.land/feeds/f-steuben~county~ny/versions/2020-03-10) |
| Tompkins | [GTFS feed](https://s3.amazonaws.com/tcat-gtfs/tcat-ny-us.zip)                                      | 2020-09-01 (almost 4 years ago) | [Transit Land](https://www.transit.land/feeds/f-dr997-tompkinsconsolidatedareatransit/versions/2020-09-01) |
| Schuyler | [GTFS feed](https://s3.amazonaws.com/datatools-511ny/public/Schuyler_County_Public_Transit.zip)     | 2021-01-22 (over 3 years ago) | [Transit Land](https://www.transit.land/feeds/f-schuyler~county~public~transit/versions/2021-01-22) |


### Data Processing Steps

#### 1. Load Data
The first step involves loading the necessary datasets into Python for processing. The `pandas` library is used to read CSV files containing SNAP and FBST pantry location data. For the bus stop data, GTFS feeds are processed using the `gtfs_kit` library, and shapefiles are read using the `geopandas` library.

#### 2. Data Cleaning and Preparation
- **Column Consistency**: Ensure that column names are consistent across different datasets to facilitate merging and analysis.
- **Duplicate Removal**: Duplicate bus stops within a specified distance (e.g., 100 meters) are removed using geodesic distance calculations from the `geopy` library to ensure accurate isochrone calculations.

#### 3. Generate Isochrones
Isochrones represent areas that can be reached within a certain time from a given point. The `osmnx` library is used to generate isochrones around each bus stop. Key steps include:
- **Graph Creation**: Create a graph of walkable paths around each bus stop.
- **Node Identification**: Identify the graph node closest to each bus stop.
- **Subgraph Extraction**: Extract subgraphs representing reachable areas within specified time thresholds (5, 15, and 30 minutes).
- **Polygon Generation**: Convert subgraphs to GeoDataFrames and generate convex hull polygons representing isochrones.

#### 4. Count Bus Stops within Isochrones
The `shapely` library is used to perform spatial operations. For each SNAP and FBST pantry location, the number of bus stops within the generated isochrones is counted:
- **Point Creation**: Convert SNAP and FBST pantry locations into `shapely` points.
- **Isochrone Containment Check**: Check if each point lies within the isochrone polygons.
- **Count Aggregation**: Aggregate the counts for each time threshold.

#### 5. Merge Results
The results are merged back with the initial SNAP and FBST datasets to include the bus stop counts for each location. This step ensures that the final datasets contain both the original information and the newly calculated accessibility metrics.

#### 6. Create Interactive Maps
The `folium` library is used to create interactive maps visualizing the SNAP and FBST pantry locations along with the isochrones and bus stops. Key elements include:
- **Marker Clustering**: Group markers for SNAP and FBST locations to enhance map readability.
- **Isochrone Visualization**: Display isochrone polygons with different colors representing different time thresholds.
- **Custom Legend**: Add a custom legend to explain the map symbols and colors, making the map more user-friendly and informative.

### Algorithms and Packages Used

#### Algorithms
- **Geodesic Distance Calculation**: Used for removing duplicate bus stops within a specified distance.
- **Convex Hull Generation**: Used to create isochrone polygons from reachable nodes in the subgraph.
- **Spatial Containment Check**: Used to count bus stops within isochrones by checking if points lie within polygons.

#### Packages
- **pandas**: Data manipulation and analysis.
- **geopandas**: Handling geospatial data.
- **osmnx**: Graph-based operations and isochrone generation.
- **networkx**: Network analysis and graph manipulation.
- **shapely**: Spatial operations and geometry manipulation.
- **folium**: Creating interactive maps.
- **geopy**: Geodesic distance calculations.


