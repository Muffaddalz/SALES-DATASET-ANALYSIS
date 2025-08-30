# SALES DATASET : EDA

### GOAL
The goal of this sales analysis is to explore and understand key patterns, trends, and insights within the sales dataset. This includes identifying top-performing products, analyzing revenue across categories and time periods, evaluating sales by region or customer segments, and uncovering factors that influence sales performance. The analysis aims to support data-driven decision-making for inventory planning, marketing strategies, and revenue optimization.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings('ignore')

## FETCHING DATA FROM CSV FILE
df = pd.read_csv("sales_data.csv")
df.head(5)
df.tail(5)

## CHECKING FOR NULL VALUES
df.isnull().sum()
df[df["price"].isnull()]

### FIXING NULL VALUES IN PRICE COLUMN
df["price"][df["price"].isnull()] = df["revenue"]/df["quantity"]
df.isnull().sum()

### FIXING NULL VALUES IN QUANTITY COLUMN
df["quantity"][df["quantity"].isnull()] = df["revenue"]/df["price"]
df.isnull().sum()

### FIXING NULL VALUES IN REVENUE COLUMN
df["revenue"][df["revenue"].isnull()] = df["quantity"]*df["price"]
df.isnull().sum()

df.info()

## FIXING DATA TYPES AS PER REQUIRED IN COLUMNS
df["price"] = df["price"].astype("int64")
df["revenue"] = df["revenue"].astype("int64")
df["quantity"] = df["quantity"].astype("int64")
df.info()

## CHECKING AND FIXING FOR DUPLICATE VALUES
df.duplicated().sum()
df=df.drop_duplicates()
df.duplicated().sum()

## CHECKING AND FIXING TYPING ERRORS
df["category"].unique()
df["category"][df["category"]=="Clohting"] = "Clothing"
df["category"][df["category"]=="Bgas"] = "Bags"
df["category"][df["category"]=="Shoeses"] = "Shoes"
df["category"].unique()

print("The Total revenue generated : ",df["revenue"].sum())

## MAXIMUM REVENUE GENERATED FROM PRODUCTS
rvbycat = df.groupby("product")["revenue"].sum()
rvbycat[rvbycat == rvbycat.max()]

avgpricebypro = df.groupby("product")["price"].mean()
avgpricebypro

df.groupby("product")["quantity"].sum()
avgrev = df.groupby("quantity")["revenue"].mean()
avgrev

df["date"] = pd.to_datetime(df["date"])
df.info()

df["quater"] = df["date"].dt.quarter
df.groupby("quater")["revenue"].sum()
df["quater"].value_counts().reset_index()

## VISUALIZATION OF ANALYSED DATA
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

colors_list = [
    '#ff9999','#66b3ff','#99ff99','#ffcc99','#c2c2f0','#ffb3e6',
    '#c2f0c2','#ff6666','#66ffe0','#ffdb4d','#a3a3c2','#ff85a2',
    '#7effa8','#ffa64d'
]

# Grouped data
y = df.groupby("category")["revenue"].sum()
x = y.index

z = list(df.groupby("quater")["revenue"].sum())

y1 = df.groupby("product")["price"].mean()
x1 = y1.index

y2 = df.groupby("product")["quantity"].sum()
x2 = y2.index
data2 = y2.reset_index()
data2 = data2.sort_values("quantity", ascending=False)

y3 = df.groupby("product")["revenue"].mean()
x3 = y3.index

# Create a DataFrame for scatter plot
scatter_df = pd.DataFrame({
    "product": x3,
    "avg_revenue": y3.values,
    "avg_price": df.groupby("product")["price"].mean().values
})

palette_color = sns.color_palette('bright')

# Create 2x3 subplots
fig, axes = plt.subplots(2, 3, figsize=(28, 18))

# 1. Bar plot: Category vs Revenue
bars0 = axes[0, 0].bar(x, y, color=colors_list)
axes[0, 0].bar_label(bars0)
axes[0, 0].set_title("Category vs Revenue")
axes[0, 0].set_xlabel("Category")
axes[0, 0].set_ylabel("Revenue")
axes[0, 0].tick_params(axis='x', rotation=90)

# 2. Product vs Average Price
bars1 = axes[0, 1].bar(x1, y1, color=colors_list)
axes[0, 1].bar_label(bars1)
axes[0, 1].set_title("Product vs Avg Price")
axes[0, 1].set_xlabel("Product")
axes[0, 1].set_ylabel("Average Price")
axes[0, 1].tick_params(axis='x', rotation=90)

# 3. Pie Chart: Revenue by Quarter
axes[0, 2].pie(z, labels=["Q1", "Q2", "Q3", "Q4"], colors=colors_list, autopct='%.0f%%')
axes[0, 2].set_title("Revenue Distribution by Quarter")

# 4. Product vs Quantity (horizontal barplot)
bars2 = sns.barplot(data=data2, x='quantity', y='product', ax=axes[1, 0], palette="viridis")
for container in axes[1, 0].containers:
    axes[1, 0].bar_label(container)

axes[1, 0].set_title("Product vs Quantity")
axes[1, 0].set_ylabel("Product")
axes[1, 0].set_xlabel("Quantity")

# 5. Scatter Plot: Product vs Avg Revenue (Colored by Avg Price)
sns.barplot(data=scatter_df, x="product", y="avg_revenue", hue="avg_price", palette=colors_list, ax=axes[1, 1])
axes[1, 1].set_title("Product vs Avg Revenue (Colored by Price)")
axes[1, 1].set_xlabel("Product")
axes[1, 1].set_ylabel("Avg Revenue")
axes[1, 1].tick_params(axis='x', rotation=90)

# 6. Heatmap: Correlation
corr = df.corr(numeric_only=True)
sns.heatmap(corr, annot=True, cmap=colors_list, ax=axes[1,2])

plt.tight_layout()
plt.show()
```

