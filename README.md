# pandas-challenge<br />
## Purpose<br />
Create and manipulate Pandas DataFrames to analyze customer purchasing and item sale data.<br />
### Before start
- Review the table structure.<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/purchase_data.png)<br />
  ``` python
  purchase_data.head()  # Resources file is readed as "purchase_data"
  ``` 
- Read the header to know the column when you need it.<br />
  ![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Column%20Names.png)<br />
  ``` python
  purchase_data.columns
  ```

### Targets<br />
- Player Count<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Player%20Count.png)<br />
  - Get the size of the unique value on "SN".<br />
  - Convert the results to a DataFrame.<br />
    ``` python
    PlayerCount_df = pd.DataFrame(
    {"Total Players": [len(purchase_data["SN"].unique())]})
    ```
- Purchasing Analysis (Total)<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Purchasing%20Analysis%20(Total).png)<br />
  - Calculate each values.<br />
    ``` python 
    UniqueItems=len(purchase_data["Item ID"].unique())
    AveragePrice = purchase_data["Price"].mean()
    Purchases = purchase_data["Purchase ID"].count()
    TotalRevenue = purchase_data["Price"].sum()
    ```
  - Create a Dictionary to hold the results, and convert it to a DataFrame.<br />
    ``` python
    PurchasingAnalysis_df = pd.DataFrame({
    "Number of Unique Items": [UniqueItems],
    "Average Price":AveragePrice,
    "Number of Purchases":Purchases,
    "Total Revenue":TotalRevenue})
    ```
  - Cleaner formatting for the display dtat.<br />
    ``` python
    PurchasingAnalysis_df["Average Price"] = PurchasingAnalysis_df["Average Price"].map("${:,.2f}".format)
    PurchasingAnalysis_df["Total Revenue"] = PurchasingAnalysis_df["Total Revenue"].map("${:,.2f}".format)
    ```
- Gender Demographics<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Gender%20Demographics.png)<br />
  - Remove the deplicated data on gender.(some people might purchase twice.)
    ``` python
    Gender_df = purchase_data.drop_duplicates("SN")
    ```
  - Find the count values.<br />
    ``` python
    GenderDemographics_df = pd.DataFrame(Gender_df["Gender"].value_counts())
    ```
  - Insert the have the percentage of the count values on the overall.`.value_counts(normalize=True)`<br />
    ``` python
    GenderDemographics_df.insert(1,"Percentage of Players",pd.DataFrame(Gender_df["Gender"].value_counts(normalize=True)))
    ```
  - Rename the column.<br />
    ``` python
    GenderDemographics_df=GenderDemographics_df.rename(columns={"Gender":"Total Count"})
    ```
  - Cleaner formatting for the display dtat.<br />
    ``` python
    GenderDemographics_df["Percentage of Players"] = GenderDemographics_df["Percentage of Players"].map("{:.02%}".format)
    ```
- Purchasing Analysis (Gender)<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Purchasing%20Analysis%20(Gender).png)<br />
   > This script will be reused in the following tasks.<br />
  - Combine both `.groupby`(grouping function) and `.agg`(aggregating function) have a summarize data on different calculation requests.<br />
    ``` python
    K = purchase_data.groupby('Gender').agg({'Purchase ID': ["count"], 'Price': ['mean', 'sum']})
    ```
  - Reduce multi-index headers level - `.join()`<br />
    ``` python
    K.columns = ["_".join(col) for col in K.columns.values]
    ```
  - Rename the column.<br />
    ``` python
    K.rename({'Purchase ID_count': 'Purchase Count', 'Price_mean': 'Average Purchase Price', 'Price_sum': 'Total Purchase Value'}, axis=1, inplace=True)
    ```
  - Insert the value if "Avg Total Purchase per Person".<br />
    ``` python
    K.insert(3,"Avg Total Purchase per Person",K["Total Purchase Value"]/GenderDemographics_df["Total Count"])
    ```
  - Cleaner formatting for the display dtat.<br />
    ``` python
    K["Average Purchase Price"] = K["Average Purchase Price"].map("${:,.2f}".format)
    K["Total Purchase Value"] = K["Total Purchase Value"].map("${:,.2f}".format)
    K["Avg Total Purchase per Person"] = K["Avg Total Purchase per Person"].map("${:,.2f}".format)
    ```
- Age Demographics<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Age%20Demographics.png)<br />
  - Used cut method to create new series of binned data.<br />
    ``` python
    bins=[0,9,14,19,24,29,34,39,50]
    group_name = ["<10","10-14","15-19","20-24","25-29","30-34","35-39","+40"]
    ```
  - Export column "Age" from Gender_df and insert the binned data, named "Age Summary"<br />
    ``` python
    H = Gender_df[["Age"]]
    H.insert(0,"Age Summary",pd.cut(H["Age"], bins, labels=group_name, include_lowest=True))
    ```
  - Count value on "Age Summary" without sort the data.<br />
    ``` python
    HH = pd.DataFrame(H["Age Summary"].value_counts(sort=False))
    ```
  - Insert percentage `.value_counts(normalize=True)` and format the results.<br />
    ``` python
    HH.insert(1,"Percentage of Players",pd.DataFrame(H["Age Summary"].value_counts(normalize=True)))
    HH["Percentage of Players"] = HH["Percentage of Players"].map("{:,.2%}".format)
    ```
- Purchasing Analysis (Age)<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Purchasing%20Analysis%20(Age).png)<br />
   > The solution to this task is a combination of the above two tasks - "Age Demographics" & "Purchasing Analysis (Gender)". <br />
  - Used cut method to create new series of binned data on the "Age"<br />
  - Use `.groupby` on the series you created and `.agg` to have a summarized data on the different calculation requests.<br />

- Top Spenders<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Top%20Spenders.png)<br />
  - Similar to the solution of "Purchasing Analysis (Gender)", without inserting a column.<br />
  - But you need use to `.sort_values` on "Total Purchase Value" & "Purchase Count" to get the desired data arrangement.<br />

- Most Popular Items<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Most%20Popular%20Items.png)<br />
  - Similar to "Top Spenders".<br />
  - But<br />
    - Need to groupby on two columns, 'Item ID' & 'Item Name'.<br />
    - Use `df.astype()` to convert the datatype for "Purchase Count" before sort on it.<br />
    - Insert a item price based on the calculation on the existing column values.<br />

- Most Profitable Items<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Most%20Profitable%20Items.png)<br />
  - Similar to "Most Popular Items".<br />
  - But need to convert the datatype for "Total Purchase Value" before sort on it.<br />


## Analysis Report<br />
Download: [Final Analysis Report](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/AnalysisReport.docx)<br />
