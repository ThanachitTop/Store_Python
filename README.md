# Store_Python
วิเคราะห์ข้อมูล "sample-store.csv" ตั้งคำถาม 10 ข้อ
![image](https://github.com/user-attachments/assets/f52f23d4-3cb0-4c14-b16a-4c78898ccd01)

## Prepare Data
### Convert order date and ship date to datetime 
```python
  datetime01 = pd.to_datetime(df["Order Date"],format= "%m/%d/%Y")

  datetime02 = pd.to_datetime(df["Ship Date"],format= "%m/%d/%Y")

  df["Order Date"] = datetime01.dt.date
  df["Ship Date"] = datetime02.dt.date
```

### edit column name 
```python
  df.columns = df.columns.map(lambda x : x.replace(" ", "_")\
  .replace("/", "_").replace("-", "_")).str.lower()
```
### filter rows with missing values
```python
  df[df["postal_code"].isna()]
```

## Analysis Data 
### 1. how many columns, row in this dataset 
```python
  df.shape
```
![image](https://github.com/user-attachments/assets/430464c5-4271-4a97-b3d9-3a4e024d9f03)


### 2. is there any missing values?, if there is, which colunm? how many nan values? 
```python
  df.info()
```
![image](https://github.com/user-attachments/assets/5c8c5093-14c4-428b-af43-e66aa38de24e)


### 3. your friend ask for `California` data, filter it and export csv for him 
```python
  df_california = df[ df["state"] == 'California' ]
  df_california.to_csv("df_california.csv")
```
![image](https://github.com/user-attachments/assets/c3741505-7303-4073-93f9-ac54bf40f957)


### 4. your friend ask for all order data in `California` and `Texas` in 2017 (look at Order Date), send him csv file 
```python
  df["year"] = pd.DatetimeIndex(df["order_date"]).year
  df_cari_tex_2017 = df.query("year == 2017 and state == ('California', 'Texas')")
  df_cari_tex_2017.to_csv("df_cari_tex_2017")
```

### 5. how much total sales, average sales, and standard deviation of sales your company make in 2017 
```python
  df[df["year"] == 2017]["sales"].agg(["sum", "mean", "std"])
```
![image](https://github.com/user-attachments/assets/e37c8e93-2179-468c-8884-f0ce47adc3e5)

### 6. which Segment has the highest profit in 2018 
```python
  df[df["year"] == 2018].groupby("segment")["profit"].agg("sum").reset_index()
```
![image](https://github.com/user-attachments/assets/539c7e27-99a0-471e-a7cf-5395e4831378)


### 7. which top 5 States have the least total sales between 15 April 2019 - 31 December 2019
```python
  start_date = datetime(2019, 4, 15).date()
  end_date = datetime(2019, 12, 31).date()
  df[(df["order_date"] >= start_date) & (df["order_date"] <= end_date)]\
  .groupby("state")["sales"].agg("sum").sort_values().head(5).reset_index()
```
![image](https://github.com/user-attachments/assets/1b0e68ea-d173-4fd8-953a-4050bc4ba329)

### 8. what is the proportion of total sales (%) in West + Central in 2019 e.g. 25% 
```python
  df2 = df[df["year"] == 2019 ].groupby("region")["sales"].agg("sum")
  df2 = pd.DataFrame(df2)
  df2["percent"] = df2["sales"]/sum(df2["sales"])
  df2["region"] = ["Central", "East", "South", "West"]

  df2[(df2["region"] == "West") | (df2["region"] == "Central")]["percent"].agg("sum")
```
![image](https://github.com/user-attachments/assets/70def21d-3995-468a-a58c-92f1874f7499)

### 9. find top 10 popular products in terms of number of orders vs. total sales during 2019-2020 
```python
  df[(df["year"] >=2019) | (df["year"] <= 2020)]\
  .groupby("product_name")["sales"].agg(["count","sum"])\
  .sort_values(by= "count", ascending=False).head(10)
```

### 10. plot at least 2 plots, any plot you think interesting0 
```python
  df.groupby("region")["sales"].agg("sum").plot(kind="bar")
```
![image](https://github.com/user-attachments/assets/b6a48c5d-9c4a-49c1-b4f0-58c796a31b2b)

### 11. use np.where() to create new column in dataframe to help you answer your own questions
```python
  df["sales"].agg("mean") ## 229.8580
  df3 = df.groupby("customer_name")["sales"].agg("mean").reset_index()
  df3_pre = df3["sales"] >= 350
  df3_pla = df3["sales"] >= 230

  df3["segment_customer"] = np.where(df3_pre, "premiem",np.where(df3_pla, "platinum", "standard"))
  df["segment_customer"] = df3["segment_customer"]

  df[df["segment_customer"] == "premiem"]  
```
![image](https://github.com/user-attachments/assets/41aca960-da0d-4b5c-898d-ceb0621e175e)














