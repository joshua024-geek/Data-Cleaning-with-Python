# Data-Cleaning-with-Python
This repository shows a data cleaning project completed with Python.  
The dataset was sourced from [Kaggle](https://www.kaggle.com/datasets/ahmedmohamed2003/cafe-sales-dirty-data-for-cleaning-training). It contains about 10,000 rows of synthetic data representing sales transactions in a cafe.   This dataset is intentionally "dirty," with missing values, inconsistent data, and errors.  <br/> The figure below shows a snapshot of the dirty data.
![Dirty Cafe Data Snapshot](https://github.com/joshua024-geek/Data-Cleaning-with-Python/blob/main/sample%20dirty%20cafe%20data.PNG)  

## Data Cleaning Activities  
The following activities were completed to clean the data:-   

1. Clean the Item Column. It has blank, ERROR, and UNKNOWN. We can identify the item name by looking at the Price Per Unit, which we can tell from other items.

  ```  
# Identify the correct mapping of 'Price per Unit' to 'Item'
price_to_item_mapping = df.loc[df['Item'].notna() & ~df['Item'].isin(['ERROR', 'UNKNOWN']), ['Price Per Unit', 'Item']]
price_to_item_mapping = price_to_item_mapping.drop_duplicates().set_index('Price Per Unit')['Item'].to_dict()

# Define a function to fill missing or erroneous items
def fill_item(row):
    if pd.isna(row['Item']) or row['Item'] in ['', 'ERROR', 'UNKNOWN']:
        return price_to_item_mapping.get(row['Price Per Unit'], row['Item'])
    return row['Item']

# Apply the function to fill the 'Item' column
df['Item'] = df.apply(fill_item, axis=1)
print(df.to_string())

```
2. Filled missing or erraneous quantities, price per unit, and total spent. Functions were defined to calculate these values. For instance, we could easily calculate the values of missing quantities by dividing corresponding Total Spent by Price Per Unit.
Sample code filling missing Price Per Unit.
```
 #Fill price for per unit for blank, ERROR and UNKOWN rows
def fill_price(row):
    if pd.isna(row['Price Per Unit']) or row['Price Per Unit'] in ['', 'ERROR', 'UNKNOWN']:
        try:
            return row['Total Spent'] / row['Quantity']
        except (ZeroDivisionError, TypeError):
            return row['Price Per Unit']
    return row['Price Per Unit']

# Apply the function to fill the 'Quantity' column
df['Price Per Unit'] = df.apply(fill_price, axis=1)
print(df.to_string())

   ```
  
3. Some calculations did not give the values for the columns, mainly because more than one values were missing. For instance, when Price Per Unit and Quantity are misisng, we could not calculate the Total Spent. Such rows were dropped.
4. The Payment Method and Location had many missing values. Since we could not tell/guess the missing/erraneous values, we replaced them with 'Unspecified' to retain data integrity and allow for further action.
5. We assumed that the data is not time sensitive. Therefore, for the missing Transaction Dates, we replaced with randomly generated dates within the minimum and maximum dates.

A snapshot of the clean dataset is shown below.  
![Clean Data Snapshot](https://github.com/joshua024-geek/Data-Cleaning-with-Python/blob/main/cleaned%20cafe%20data.PNG)  

NB: See the attached files for the dirty and clean datasets, and the jupyter notebook file with the entire code used in the dataset cleaning process. 
