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
![alt text](https://github.com/Ash-Tao/pandas-challenge/blob/main/HeroesOfPymoli/Screen%20Shot/Player%20Count.png)<br />
  ``` python
  purchase_data.columns
  ```

### Targets<br />
- Player Count
  - `len()` get the size of the unique value on "SN".
  - convert the results to a DataFrame.
  ``` python
  PlayerCount_df = pd.DataFrame(
    {"Total Players": [len(purchase_data["SN"].unique())]})
  ```
  
- Purchasing Analysis (Total)
  - calculate each values.
  - create a Dictionary to hold the results.
  - cleaner formatting for the display dtat. 
  ``` python
  UniqueItems=len(purchase_data["Item ID"].unique())
  AveragePrice = purchase_data["Price"].mean()
  Purchases = purchase_data["Purchase ID"].count()
  TotalRevenue = purchase_data["Price"].sum()

  PurchasingAnalysis_df = pd.DataFrame({
     "Number of Unique Items": [UniqueItems],
     "Average Price":AveragePrice,
     "Number of Purchases":Purchases,
     "Total Revenue":TotalRevenue
  })

  PurchasingAnalysis_df["Average Price"] = PurchasingAnalysis_df["Average Price"].map("${:,.2f}".format)
  PurchasingAnalysis_df["Total Revenue"] = PurchasingAnalysis_df["Total Revenue"].map("${:,.2f}".format)
  ```
- Gender Demographics
### The way to approach<br />
> *It is divided into two parts:*<br />
- FIRST PART: Use loop to find the value.<br />
  ``` python
  for row in csvreader:
  ```
  - The total number of months.<br />
    ``` python
    totalline = 0
    totalline = totalline + 1
    ```
  - The net total amount of "Profit/Losses" over the entire period<br />
    ``` Python
    ProfitLosses = []
    ProfitLosses.append(int(row[1]))
    totalPL = f"Total: ${sum(ProfitLosses)}"
    ```
  - The changes in "Profit/Losses" over the entire period, and then the average of those changes.<br />
    ``` Python
    change = []
    lastProfitLosses = 0
    change.append(int(row[1]) - lastProfitLosses)
    if lastProfitLosses != 0:
        if (int(row[1]) - lastProfitLosses) == max(change):
            maxmonth = row[0]
        if (int(row[1]) - lastProfitLosses) == min(change):
            minmonth = row[0]
    else:
        change.pop(0)
    lastProfitLosses=int(row[1])
    ```
  ``` Python
  averagechange = f'Average Change: ${"{:.2f}".format(sum(change)/(totalline-1))}'
  ```
  - SECOND PART: The greatest increase in profits (date and amount) over the entire period.<br />
  ``` Python
  greatestinPL = f"Greatest Increase in Profits: {maxmonth} (${max(change)})"
  ```
  - The greatest decrease in profits (date and amount) over the entire period.<br />
  ``` Python
  greatestdePL = f"Greatest Decrease in Profits: {minmonth} (${min(change)})"
  ```
- Combine values into a list<br />
  ``` Python
  completelist = (" ","Financial Analysis","-----------------------------------",totalmonths,totalPL,averagechange,greatestinPL,greatestdePL)
  ```
---

## PyPoll<br />
The given dataset is composed of three columns: "Voter ID", "County", and "Candidate".<br />
![alt text](https://github.com/Ash-Tao/python-challenge/blob/main/Image/PyPoll%20Resources%20Datasets%20.png)<br />
Final Exported Report: <br />
![alt text](https://github.com/Ash-Tao/python-challenge/blob/main/Image/Results%20for%20PyPoll.png)<br />
### The way to approach<br />
- The total number of votes cast.<br />
  ``` Python
  votesdetails = []
  totalvotes = 0
  for row in csvreader:
      totalvotes = totalvotes + 1
      votesdetails.append(row[2])   
    ```
- And a complete list of candidates who received votes<br />
  ``` python
  candidate = []
  candidate = list(dict.fromkeys(votesdetails))
  ```
- The total number and percentage of votes each candidate won.<br />
  ``` python
  voteresult = []
  for x in candidate:
      voteresult.append(votesdetails.count(x))
      eachcandidate = voteresult[candidate.index(x)]
      eachcandidatepercent = "{:.3%}".format(eachcandidate/totalvotes)
- The winner of the election based on popular vote.<br />
  ``` Python
  candidate[voteresult.index(max(voteresult))]
  ```
- Combine values into a list<br />

## How to Run<br />
- Download two files.<br />
  - [PyBank](https://github.com/Ash-Tao/python-challenge/tree/main/PyBank)<br />
  - [PyPoll](https://github.com/Ash-Tao/python-challenge/tree/main/PyPoll)<br />
- Run the `main.py` in terminal<br />
- Go to the subfolder `analysis`, you will find a `Analysis Results.txt` for the final report.<br />
