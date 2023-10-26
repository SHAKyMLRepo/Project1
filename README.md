# Project: House Price Prediction using population densities using Linear Regression

![House Prediction](Images/house.jpg)

## Introduction

The real estate market is a dynamic and complex environment, and predicting house prices accurately is a valuable tool for homeowners, buyers, and real estate professionals. This project aims to explore the links between property prices, population and population density. It aims to try and establish the degree of correlation between such factors and determine if it is possible to estimate how house prices will behave into the future based on factors such as land area, population and population density.

## Project Overview

**Objective**: The primary objective of this project is to create a House Price Prediction algorithms that employs Linear Regression to estimate the selling price of houses based on the available land space in a region and its population density.

## Key Components

### Data Sourcing 
<br>
In this project, I sourced publically available datasets with the necessary information required to begin to develop the inputs for this project. This proved to be a challenging task as while there is plenty of high quality data on Irish Population statistics, due to privacy concerns many of the publically avaiable datasets regarding House Prices cannont contain any PII so have many of the fields you might wish for to answer these questions removed or lowered in fidelity to protect privacy. In the end, a number of seperate datasets were required to collate the fields necessary for the project.

**Datasets**:
1. **FY001: Population by each census by Sex and County, 1841 to 2022**
     - **Description** This dataset, named FY001, was sourced from the Central Statistics Office (CSO) datasets. It provides comprehensive population data recorded during every Census conducted from 1841 to 2022. The dataset includes population statistics categorized by sex and county.
     - **Usage**: This dataset was crucial for obtaining accurate and detailed population statistics, which were essential for understanding how population dynamics over time may correlate with other Housing Price factors. The main fields used were the Census Year, County and Population figure.
     - **Source Quality**: The data was sourced from the official CSO website, a reputable and trusted data provider.
     - **Link**: [here](https://www.cso.ie/en/releasesandpublications/ep/p-cpsr/censusofpopulation2022-summaryresults/data/)* 

2. **Residential Property Price Index 2010 - 2023**
   - **Description** This dataset, PPR_ALL, was sourced from the Central Statistics Office (CSO) datasets. It provides a listing of all dwellings purchased by households in the residential property market in these years. Due to privacy however, precise details on house characteristics are not provided.
   - **Usage**: This dataset was used to give a baseline on the general trends in housing prices per county in Ireland over this timeframe. Main data fields used were the House Price, County and Dwelling type.
   - **Source Quality**: The data was sourced from the official CSO website, a reputable and trusted data provider.
   - **Link**: [here](https://www.propertypriceregister.ie/website/npsra/pprweb.nsf/PPRDownloads?OpenForm&File=PPR-2020.csv&County=ALL&Year=2020&Month=ALL)*

3. **Counties - OSi National Statutory Boundaries - 2019**
   - **Description** This dataset, was sourced from the offical government (data.gov.ie) datasets. It provides a detailed geographical boundary information for Irish counnties.
   - **Usage**: This dataset was used to find the area of Irish counties in km2. Main data fields used were the County and Area.
   - **Source Quality**: The data was sourced from the government website, a reputable and trusted data provider.
   - **Link**: [here](https://data.gov.ie/dataset/counties-osi-national-statutory-boundaries-20191/resource/66ed6ea5-a98c-4fd5-8185-7d21ca7f3bce)*

4. **Residential Property Prices for Ireland (QIEN628BIS)**
   - **Description** This dataset, was sourced from the Bank for International Settlements. This is a secondary source which according to source was collated from National sources, BIS Residential Property Price database.
   - **Usage**: This dataset was used to find average house price inflation figures. Main data fields used were the Year and QIEN628BIS fields.
   - **Source Quality**: The data is a secondary source but was obtained from reputable institution.
   - **Link**: [here](https://data.gov.ie/dataset/counties-osi-national-statutory-boundaries-20191/resource/66ed6ea5-a98c-4fd5-8185-7d21ca7f3bce](https://fred.stlouisfed.org/series/QIEN628BIS)*
   
### Data Preprocessing
As this project could not use a prepared dataset as none was available with the information required regarding the Irish market, the Data Preprocessing stage was one of the most involved stage of this project. This involved tasks such as handling missing values, dealing with outliers, and removing unneeded data. As multiple datasets had to be combined it also required datasets formats to be synchronised before combination. The goal was to prepare the data for model development by ensuring it was clean and structured. For the data preparation, the Pandas and NumPy libraries were used.

1. **Read Data**: At this stage, each csv was transformed into a Panda DataFrame respecting the various encoding standards.
2. **Data Exploration**: An initial exploration of the data was performed to establish the data types included and what were the necessary steps to form the final needed dataset for analysis. The .info() and .describe() methods were used to form a picture of a summary of the datasets included and some basic statistics.
3. **Data Cleaning**:

#### Residential Property Price Index 2010 - 2023
-**Removing Null Rows**: Rows with missing values were identified and removed. This was done to ensure data completeness and integrity.
- **Filtering by Specific Value**: A subset of the dataset containing properties marked with "Not Full Market Price" was created. This was used to isolate properties whose price may not be representative of their market price. These rows were thus removed for data integrity reasons.
- **Handling VAT Exclusivity**: Properties marked as "VAT Exclusive" had their prices adjusted to account for their VAT exclusion. The VAT added was 13.5% which is the VAT price for house sales in Ireland.
- **Data Type Conversion**: The "Date of Sale (dd/mm/yyyy)" column was converted to a datetime format for accurate date-based analysis.
- **Renaming Columns**: The column name "Date of Sale (dd/mm/yyyy)" was renamed to "Date of Sale" for clarity.
- **Address Cleaning**: Extra spaces and non-string data types were removed from the "Address" column to ensure consistency.
- **Price Column Cleaning**: Euro symbols and commas in the "Price (€)" column were removed, and the column was renamed to "Price." The data type was changed to float for numerical analysis.
- **Language Standardisation**: Columns "Description of Property" and "Property Size Description" were cleaned and standardized. This involved translating Irish descriptions to English and removing irregular or non-standard entries.
- **Splitting Data**: The dataset was filtered to include only new build properties as only these properties contained size descriptions which were needed for the later analsysis
- **Data Binning**: Dates were split into quarters to align with the fiscal quarters and facilitate time-based analysis.
- **Data Transformation** : Unnecessary columns, including "Date of Sale," "Address," "Eircode," and "Description of Property," were removed from the DataFrame.
- **Standardise Capitalisation**: County values were capitalised to facilitate later merge conditions.

#### Residential Property Prices for Ireland (QIEN628BIS)
- **Normalization**: Data normalization was applied to the "QIEN628BIS" column from the secondary dataset. This ensured that values were on a common scale for meaningful analysis and comparison.

#### FY001: Population by each census by Sex and County, 1841 to 2022
- **Filling Missing Values**: As the census has a varying number of gaps between data points, missing values were created by taking the previous and next population number and dividing it by the number of years between available census data.
    
#### Combining Datasets into final output
- **House Price Inflation Integration**: Property Price dataset was combined with QIEN628BIS on the "Year" and "Quarter" columns, ensuring that relevant economic data was added to property records.
- **Census Data Integration**: Data from the census dataset was joined on "CensusYear" and "County" columns into output dataset adding Population data.
- **County Area Integration**: County Area dataset was joined on "County" fields to add areas of county. This was added to calculate population density per county.
  
These merges resulted in a comprehensive dataset that combined property details, economic indicators, and population statistics.
        
### Model Development
Implement a Linear Regression model using the machine learning library Scikit-Learn. Train the model on the prepared dataset to learn the relationships between the selected factors and house prices.

### Model Evaluation
Evaluate the performance of the Linear Regression model a subset of the data which will be held back during training.

### User Interface
Create a simple text interface to allow Users to query the model.

### Benefits

- **Accurate Predictions**: The project offers a reliable tool for estimating house prices based on various factors and explores the weight by which different factors effect house prices into the future. 

- **Data-Driven Insights**: Users can gain insights into the factors influencing house prices, helping them make informed choices.

- **Real-World Application**: The project demonstrates the application of machine learning in a real-world scenario and highlights the potential for predictive modeling in the real estate industry based on population dynamics.

### Ethics and Regulations

- **Data Privacy and GDPR Compliance**: The data from this project should have no PPI included so will not have GDPR compliance implications.

- **Data Attribution and Terms of Use**: The project will use publically available datasets and will give full attribution of the data sets used  and comply with any terms of use.

