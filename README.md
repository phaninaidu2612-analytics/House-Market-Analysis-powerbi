## Housing Market Analysis Dashboard (Data Source : Google Big Query)

### Dashboard Link :

https://app.powerbi.com/links/OODDQGpxsK?ctid=06cd4ed5-3b7f-47b0-8ef6-29dee1c5060b&pbi_source=linkShare&bookmarkGuid=af4ab754-663c-4ab7-be76-bbc227ba2de2

### Dashboard Preview :

![page1](https://github.com/user-attachments/assets/77f402f7-eb46-4fc3-b0f8-ced99a12e3f1)
![page2](https://github.com/user-attachments/assets/3f8c83a2-8aa6-45aa-bf8f-44cb912ed082)
![page3](https://github.com/user-attachments/assets/74f07894-e221-4d63-8169-c5affc070edf)
---
### Problem Statement :

This dashboard is designed to analyze housing market trends using
transactional and economic data. It helps stakeholders understand: - How
housing prices change over time
- Regional performance of property sales
- Market trends such as Year-over-Year growth and median price changes
- The relationship between property size, price, and economic indicators

---

### Dataset Description :

The dataset contains 100,000 records sourced from Google BigQuery, representing housing transactions along with economic indicators.

### Key Columns:
- date – Transaction date
- house_id – Unique identifier
- house_type / sales_type – Property classification
- purchase_price – Final transaction price
- sqm / sqm_price – Property size and price per sqm
- region / city / area – Location hierarchy
- year_build – Construction year
- interest & inflation rates – Economic indicators

----

### Data Source & Data Preparation :
Data Source:
Data was uploaded to Google BigQuery via Google Cloud Console
SQL was used to validate and explore data (SELECT * FROM table)

Data Connection:
Connected Power BI → Google BigQuery
Cleared previous permissions before establishing connection
Imported data into Power BI and transformed using Power Query Editor.

![cloud](https://github.com/user-attachments/assets/ecc42a64-1b7f-4873-869b-a58dd607ec4f)

#### Data Cleaning:
Verified data types for all columns

#### Handled missing values:
yield_on_mortgage_credit_bonds% → replaced with appropriate values
dk_ann_infl_rate% → replaced with appropriate values


#### Feature Engineering (Calculated Columns):
Offer Price Calculation :

Derived offer price using percentage difference between offer and purchase:
Offer Price = 
100 * 'Housing'[purchase_price] / 
(100 - 'Housing'[%_change_between_offer_and_purchase])

![col_1](https://github.com/user-attachments/assets/550bb327-de87-40f9-acfc-beeb44232909)


Age of Property:

Calculated the age of each house based on transaction date and build year:
Age = 
ABS(YEAR('Housing'[date].[Date]) - 'Housing'[year_build])

![col2](https://github.com/user-attachments/assets/dfb786db-22bd-4309-8819-c3dc7d3f4b0d)

Optionally performed:
Cleaning either in BigQuery (SQL) or Power Query

-------

### Steps Followed :-
-Loaded dataset from Google BigQuery into Power BI
-Opened Power Query Editor to validate:
-Column quality
-Column distribution
-Data types
-Handled missing values in economic columns
-Created calculated columns and DAX measures
-Built visuals such as:
-KPI Cards (Sales, Growth, Units Sold)
-Line Charts (Trends over time)
-Bar Charts (Region-wise analysis)
-Filters (Region, Year, House Type)
-Applied report theme and formatting
-Published report to Power BI Service




----

### DAX Measures & Calculations :


- Average Price SQM = AVERAGE(Housing[sqm_price])

- Last 12 Month Sales =
CALCULATE(
    SUM(Housing[purchase_price]),
    DATESINPERIOD(Housing[date], MAX(Housing[date]), -12, MONTH)
)


Median Sales Price Change
(Median comparison logic using MEDIANX for current vs previous year)
- Median Sales Price change = 
var CurrMedianPrice = MEDIANX(FILTER('Housing',YEAR('Housing'[date]) = YEAR(max('Housing'[date].[Date]))) , 'Housing'[purchase_price])
Var PrevMedianPrice = MEDIANX(FILTER('Housing',YEAR('Housing'[date]) = YEAR(MAX('Housing'[date].[Date]))-1), 'Housing'[purchase_price])
return 
    IF(PrevMedianPrice <> 0 , (CurrMedianPrice-PrevMedianPrice)/PrevMedianPrice , BLANK())


![median](https://github.com/user-attachments/assets/5c777cc4-fb68-4e9e-a42c-15a8f2241b10)




- Offer to SQM Ratio = DIVIDE(SUM(Housing[Offer Price]), SUM(Housing[sqm]))


- Sales by Region =
CALCULATE(
    SUM(Housing[purchase_price]),
    ALLEXCEPT(Housing, Housing[region])
)


- TotalYTD Sales =
TOTALYTD(SUM(Housing[purchase_price]), Housing[date])
![ytd](https://github.com/user-attachments/assets/2dcc159e-7930-43ed-b7e8-7715a25608ec)


- Units Sold (Latest Period):
DISTINCTCOUNT based on latest year & quarter

- YOY Sales Growth =
(CurrYearSales - PrevYearSales) / PrevYearSales

![yoy](https://github.com/user-attachments/assets/e92a20ec-9ecf-4663-8d64-4e0b734ead05)


---

## Snapshot of Dashboard (Power BI Service):

![pbi](https://github.com/user-attachments/assets/fbd7d5e7-846d-4112-83cc-dddb9d8000bf)



### Insights :

#### Economic Indicators by House Type:
- Farmhouses show the highest mortgage yield (~4.6%), along with slightly higher inflation and interest values.
This indicates that farmhouse properties are likely associated with higher-value investments and stronger return expectations.
Villas follow next, with moderately high yield (~4.2%), suggesting they are also premium housing segments.
Apartments, Townhouses, and Summerhouses show relatively lower yield values (~3.8–3.9%), indicating:
More standardized pricing
Lower investment return compared to premium property types

- The variation in yield on mortgage credit bonds across house types suggests:

- Higher-end properties (like farmhouses and villas) are linked with greater financial returns and risk-adjusted pricing
More common housing types (apartments, townhouses) tend to have stable but lower yields

![is1](https://github.com/user-attachments/assets/5bac6fc1-3dc5-49eb-ae57-07a6f629a73f)



#### Regional Analysis:
- Zealand dominates the housing market with the highest total sales (~95bn), followed by Jutland (~81bn).
In contrast, Fyn & Islands (~15bn) and Bornholm contribute significantly less to overall sales.

- Key Interpretation:
The large gap in sales indicates that housing demand and transaction volume are heavily concentrated in specific regions, particularly Zealand.
This is likely due to:
Higher urbanization and population density
Greater economic activity and job opportunities
Higher property values and transaction frequency
Regions like Fyn & Islands and Bornholm show:
Lower transaction volumes
Potentially smaller or less active housing markets

- Business Insight:
Real estate investments and market strategies should focus more on high-performing regions like Zealand and Jutland, where:
Sales volume is high
Market liquidity is stronger
Lower-performing regions may represent:
Niche investment opportunities
Or areas with slower growth potential

![is2](https://github.com/user-attachments/assets/c5fdb84c-7ce4-4b97-9bb3-4cb9b67c1e74)


#### YOY Sales Growth by Sales Type:
- Auction sales show the highest Year-over-Year growth (~+29%), indicating a rapid increase in transaction activity in this segment.
Regular sales and other sales both show a decline (~-21%), suggesting a slowdown in traditional property transactions.
Family sales have the lowest growth (~-75%), indicating a significant drop in activity or value in intra-family transactions.

- Key Interpretation:
The strong growth in auction sales suggests:
Increased market activity in distressed or time-sensitive sales
Buyers may be attracted to competitive pricing opportunities
The decline in regular and other sales may indicate:
Market cooling
Reduced buyer demand or affordability challenges
The sharp drop in family sales could be due to:
These transactions being less market-driven
Prices possibly not reflecting current market value
Lower frequency of such transfers year-over-year

![is3](https://github.com/user-attachments/assets/2b4d27bc-3480-4f29-9e19-441f3faf58f0)



#### Impact of Property Age on Purchase Price:
- Properties with lower age (newer houses, ~2–16 years) show a significant increase in average purchase price (~639K).
As the age of the property increases, the purchase price tends to decrease, indicating a negative relationship between property age and price.

- Key Interpretation:
Newer properties are priced higher due to:
Better construction quality and modern design
Lower maintenance costs
Higher buyer preference for recently built homes
Older properties experience price decline due to:
Depreciation over time
Increased maintenance and renovation requirements
Possible mismatch with modern housing standards

- Business Impact:
Property age is a key driver of pricing strategy in the housing market
Developers and investors can:
Focus on new constructions for higher returns
Identify older properties as renovation or value-investment opportunities


![is4](https://github.com/user-attachments/assets/0b3f7ffd-3f82-4059-9f46-d1b17af1764d)



#### Market Trends:
Housing prices show fluctuations based on region and time
Median price comparison highlights yearly growth patterns

#### Regional Analysis:
Certain regions contribute significantly higher sales
Regional filtering reveals localized market performance


#### Sales Performance:
Year-over-Year growth helps identify expanding or declining markets
Last 12-month sales provide a rolling performance indicator


#### Property Insights:
Larger properties (higher sqm) tend to have different pricing patterns
Offer vs purchase price analysis shows negotiation trends


#### Economic Impact:
Inflation and mortgage rates influence housing prices
Missing economic data was handled to maintain accuracy

----

## Tools & Technologies Used :
- Power BI Desktop – Data visualization & dashboard creation
- Power Query Editor – Data transformation & cleaning
- DAX (Data Analysis Expressions) – Calculations & measures
- Google BigQuery – Cloud data warehouse
- Google Cloud Platform (GCP) – Data storage & processing
- MS SQL Server

---

## Conclusion:

This dashboard provides a comprehensive view of the housing market by combining transactional and economic data. 
It enables users to:
Track sales trends
Compare regional performance
Understand pricing dynamics
Make data-driven investment decisions.
