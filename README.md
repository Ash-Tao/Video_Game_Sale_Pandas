# Video_Game_Sales_Pandas<br />
## Purpose<br />
Create and manipulate Pandas DataFrames to analyze customer purchasing and item sales data.<br />
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
  - Insert the percentage of the overall for count values.`.value_counts(normalize=True)`<br />
    ``` python
    GenderDemographics_df.insert(1,"Percentage of Players",pd.DataFrame(Gender_df["Gender"].value_counts(normalize=True)))
    ```
  - Rename the column.<br />
    ``` python
    GenderDemographics_df=GenderDemographics_df.rename(columns={"Gender":"Total Count"})
    ```
  - Format the data.<br />
    ``` python
    GenderDemographics_df["Percentage of Players"] = GenderDemographics_df["Percentage of Players"].map("{:.02%}".format)
    ```
- Purchasing Analysis (Gender)<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Purchasing%20Analysis%20(Gender).png)<br />
   > This script will be reused in the following tasks.<br />
  - Combine both `.groupby`(grouping function) and `.agg`(aggregating function) to have a summarize data on different calculation requests.<br />
    ``` python
    Gender_df = purchase_data.groupby('Gender').agg({'Purchase ID': ["count"], 'Price': ['mean', 'sum']})
    ```
  - Reduce multi-index headers level - `.join()`<br />
    ``` python
    Gender_df.columns = ["_".join(col) for col in Gender_df.columns.values]
    ```
  - Rename the column.<br />
    ``` python
    Gender_df.rename({'Purchase ID_count': 'Purchase Count', 'Price_mean': 'Average Purchase Price', 'Price_sum': 'Total Purchase Value'}, axis=1, inplace=True)
    ```
  - Insert the value of "Avg Total Purchase per Person".<br />
    ``` python
    Gender_df.insert(3,"Avg Total Purchase per Person",Gender_df["Total Purchase Value"]/GenderDemographics_df["Total Count"])
    ```
  - Format the data.<br />
    ``` python
    Gender_df["Average Purchase Price"] = Gender_df["Average Purchase Price"].map("${:,.2f}".format)
    Gender_df["Total Purchase Value"] = Gender_df["Total Purchase Value"].map("${:,.2f}".format)
    Gender_df["Avg Total Purchase per Person"] = Gender_df["Avg Total Purchase per Person"].map("${:,.2f}".format)
    ```
- Age Demographics<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Age%20Demographics.png)<br />
  - Used cut method to create new series of binned data.<br />
    ``` python
    bins=[0,9,14,19,24,29,34,39,50]
    group_name = ["<10","10-14","15-19","20-24","25-29","30-34","35-39","+40"]
    ```
  - Export column "Age" from AgeSN (unique player) and insert the binned data, named "Age Summary"<br />
    ``` python
    AgeSN = GenderSN_df[["Age"]]
    AgeSN.insert(0,"Age Summary",pd.cut(AgeSN["Age"], bins, labels=group_name, include_lowest=True))
    ```
  - Count value on "Age Summary" without sort the data.<br />
    ``` python
    AgeDemographics_df = pd.DataFrame(AgeSN["Age Summary"].value_counts(sort=False))
    ```
  - Insert percentage `.value_counts(normalize=True)` and format the results.<br />
    ``` python
    AgeDemographics_df.insert(1,"Percentage of Players",pd.DataFrame(AgeSN["Age Summary"].value_counts(normalize=True)))
    AgeDemographics_df["Percentage of Players"] = AgeDemographics_df["Percentage of Players"].map("{:,.2%}".format)
    ```
- Purchasing Analysis (Age)<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Purchasing%20Analysis%20(Age).png)<br />
   > The solution to this task is a combination of the above two tasks - "Age Demographics" & "Purchasing Analysis (Gender)". <br />
  - Used cut method to create new series of binned data on the "Age" (This time keep the duplicated records for players)<br />
  - Use `.groupby` on the series you created and `.agg` to have a summarized data on the different calculation requests.<br />

- Top Spenders<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Top%20Spenders.png)<br />
  - Similar to the solution of "Purchasing Analysis (Gender)", without inserting a column.<br />
  - But:
    - Groupby "SN"
    - use `.sort_values` on "Total Purchase Value" & "Purchase Count" to get the desired data arrangement.<br />

- Most Popular Items<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Most%20Popular%20Items.png)<br />
  - Similar to "Top Spenders".<br />
  - But<br />
    - Need to groupby on two columns, 'Item ID' & 'Item Name'.<br />
    - Use `df.astype()` to convert the datatype for "Purchase Count" before sort on it.<br />
    - Insert a item price based on the calculation on the existing column values.<br />

- Most Profitable Items<br />
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Most%20Profitable%20Items.png)<br />
  - Base on "Most Popular Items", replace "Total Purchase Value" from `str`to `float` format and sort on it.<br />
    ``` python
    MostPopularItems_df["Total Purchase Value"] = MostPopularItems_df["Total Purchase Value"].str.replace("$", "").astype('float')
    MostProfitableItems_df = MostPopularItems_df.sort_values(["Total Purchase Value"], ascending= False)
    ```
  - Format "Total Purchase Value" to `str` again with "$".<br />
    ``` python
    MostProfitableItems_df["Total Purchase Value"] = MostProfitableItems_df["Total Purchase Value"].map("${:,.2f}".format)
    ```




## Analysis Report<br />
Download: [Final Analysis Report](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/AnalysisReport.docx)<br />
