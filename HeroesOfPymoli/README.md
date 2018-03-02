

```python
import pandas as pd
import os
```


```python
jsonpath = "purchase_data.json"
heroes_df = pd.read_json(jsonpath)
heroes_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Function to format floats as money
def money(n):
    return f"${round(n,2)}"
```

### Player Count


```python
user_count = heroes_df["SN"].nunique()
pd.DataFrame({"Total Number of Players":[user_count]})
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Number of Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



### Purchasing Analysis (Total)


```python
#Number of Unique Items
item_count = heroes_df["Item ID"].nunique()
#Average Purchase Price
avg_price = money(heroes_df["Price"].mean())
#Total Number of Purchases
total_purchases = len(heroes_df)
#Total Revenue
total_revenue = money(heroes_df["Price"].sum())

pd.DataFrame.from_items([("Number of Unique Items",[item_count]), ("Average Purchase Price",[avg_price]),
             ("Total Number of Purchases",[total_purchases]), ("Total Revenue",[total_revenue])])
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Purchase Price</th>
      <th>Total Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>



### Gender Demographics


```python
#Percentage and Count of Male, Female and Other/Non-Disclosed Players

#Counts from GroupBy and nunique
gender_counts_df = pd.DataFrame(heroes_df.groupby(by="Gender").nunique()["SN"]).rename(columns = {"SN":"Total Count"})
#Percentages dividing Total Count by user_count, rounded to 2 decimal places
gender_counts_df["Percentage"] = (gender_counts_df["Total Count"]/user_count*100).map(lambda x: round(x,2))

gender_counts_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Count</th>
      <th>Percentage</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.45</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.15</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.40</td>
    </tr>
  </tbody>
</table>
</div>



### Purchasing Analysis (by Gender)


```python
gender_group = heroes_df.groupby(by="Gender")

#Purchase Count
gender_stats = pd.DataFrame(gender_group.count()["Price"]).rename(columns={"Price":"Purchase Count"})

#Average Purchase Price
gender_stats["Average Purchase Price"] = gender_group.mean()["Price"].map(money)

#Total Purchase Value
gender_stats["Total Purchase Value"] = gender_group.sum()["Price"].map(money)

#Normalized Totals
gender_stats["Normalized Totals"] = (gender_group.sum()["Price"]/gender_counts_df["Total Count"]).map(money)
    
gender_stats
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



### Age Demographics


```python
age_bins = [0,10,15,20,25,30,35,40,200]
age_group_names = ["<10","10-14","15-19","20-24","25-29","30-34","35-39","40+"]

heroes_df["Age Group"] = pd.cut(heroes_df["Age"], age_bins, labels = age_group_names, right= False)

#Counts from GroupBy and nunique
age_group = heroes_df.groupby(by="Age Group")
age_counts_df = pd.DataFrame(age_group.nunique()["SN"]).rename(columns = {"SN":"Total Count"})
#Percentages dividing Total Count by user_count, rounded to 2 decimal places
age_counts_df["Percentage"] = (age_counts_df["Total Count"]/user_count*100).map(lambda x: round(x,2))

age_counts_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Count</th>
      <th>Percentage</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>19</td>
      <td>3.32</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>23</td>
      <td>4.01</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>100</td>
      <td>17.45</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>259</td>
      <td>45.20</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>87</td>
      <td>15.18</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>47</td>
      <td>8.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>27</td>
      <td>4.71</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>11</td>
      <td>1.92</td>
    </tr>
  </tbody>
</table>
</div>



### Purchasing Analysis (by Age)


```python
#Purchase Count
age_stats = pd.DataFrame(age_group.count()["Price"]).rename(columns={"Price":"Purchase Count"})

#Average Purchase Price
age_stats["Average Purchase Price"] = age_group.mean()["Price"].map(money)

#Total Purchase Value
age_stats["Total Purchase Value"] = age_group.sum()["Price"].map(money)

#Normalized Totals
age_stats["Normalized Totals"] = (age_group.sum()["Price"]/age_counts_df["Total Count"]).map(money)
    
age_stats
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.2</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.4</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>



### Top Spenders


```python
#construct a table with both totals and purchase counts
#totals of all numeric values per user, sorted by price and truncated to top 5 spenders
top_five_names = heroes_df.groupby("SN").sum().sort_values("Price", ascending = False).head()
#get purchase counts for each user
user_purchase_counts = pd.DataFrame(heroes_df.groupby("SN").count()["Item Name"])
#merge tables
top_five_names = pd.merge(top_five_names, user_purchase_counts, left_index = True, right_index = True)

```


```python
#extract Total Value and Purchase count, calculate Average
top_spenders = top_five_names.loc[:,["Item Name","Price"]].rename(
                                                columns={"Price":"Total Purchase Value", "Item Name":"Purchase Count"})
top_spenders["Average Purchase Price"] = (top_spenders["Total Purchase Value"]/top_spenders["Purchase Count"]).map(money)
top_spenders["Total Purchase Value"] = top_spenders["Total Purchase Value"].map(money)
top_spenders
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Average Purchase Price</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$17.06</td>
      <td>$3.41</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$13.56</td>
      <td>$3.39</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$12.74</td>
      <td>$3.18</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$12.73</td>
      <td>$4.24</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$11.58</td>
      <td>$3.86</td>
    </tr>
  </tbody>
</table>
</div>



### Most Popular Items


```python
#Top five items by count
top_five_items = heroes_df.groupby("Item ID").count().sort_values("Price", ascending = False).head()[["SN"]]
top_five_items = top_five_items.rename(columns={"SN":"Purchase Count"})
#Name and price of all items
item_constants = heroes_df.groupby("Item ID").max()[["Item Name","Price"]]
item_constants = item_constants.rename(columns={"Price":"Item Price"})
#Total purchase value of all items
item_sums = heroes_df.groupby("Item ID").sum()[["Price"]].rename(columns={"Price":"Total Purchase Value"})

#merge tables
top_items_df = pd.merge(top_five_items, item_constants, left_index = True, right_index = True)
top_items_df = pd.merge(top_items_df, item_sums, left_index = True, right_index = True)
#rearrange columns
top_items_df = top_items_df[['Item Name', 'Purchase Count', 'Item Price', 'Total Purchase Value']]
#convert prices
top_items_df["Item Price"] = top_items_df["Item Price"].map(money)
top_items_df["Total Purchase Value"] = top_items_df["Total Purchase Value"].map(money)

top_items_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Arcane Gem</td>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Trickster</td>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <td>Woeful Adamantite Claymore</td>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Serenity</td>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



### Most Profitable Items


```python
#Top five items by count
top_five_items_rev = heroes_df.groupby("Item ID").sum().sort_values("Price", ascending = False).head()[["Price"]]
top_five_items_rev = top_five_items_rev.rename(columns={"Price":"Total Purchase Value"})

item_counts = heroes_df.groupby("Item ID").count()[["Price"]].rename(columns={"Price":"Purchase Count"})

top_items_rev_df = pd.merge(top_five_items_rev, item_constants, left_index = True, right_index = True)
top_items_rev_df = pd.merge(top_items_rev_df, item_counts, left_index = True, right_index = True)
#rearrange columns
top_items_rev_df = top_items_rev_df[['Item Name', 'Purchase Count', 'Item Price', 'Total Purchase Value']]
#convert prices
top_items_rev_df["Item Price"] = top_items_rev_df["Item Price"].map(money)
top_items_rev_df["Total Purchase Value"] = top_items_rev_df["Total Purchase Value"].map(money)

top_items_rev_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Retribution Axe</td>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <td>Spectral Diamond Doomblade</td>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Orenmir</td>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.7</td>
    </tr>
    <tr>
      <th>103</th>
      <td>Singed Scalpel</td>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>



### Observable Trends:
1) The game is most popular among males in their late teens to mid-twenties.

2) This demographic also makes a similar proportion of in-game purchases.

3) A majority of users make only one purchase, with very few users buying more than 3 separate items.
