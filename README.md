# From Nairobi to New York: Mapping the Cost of Global Education Dreams
When Amina from Kenya received her acceptance letter to a university in Canada, her excitement was quickly tempered by the daunting reality of tuition fees, accommodation costs, visa expenses, and living abroad. Her story is not unique. Every year, thousands of students across the globe face similar challenges as they chase the promise of better education and brighter futures—often at great financial cost.

International education has become both a dream and a dilemma for students worldwide. From the staggering fees in the United States and the UK, to the more affordable—but still significant—costs in countries like Germany or Malaysia, the price of crossing borders for knowledge varies dramatically.

In this project, I dive deep into the data behind the cost of international education across different regions. Through numbers and narratives, I aim to uncover not just how much it costs—but what it truly means to invest in education beyond one’s homeland. Whose dreams are within reach? Who’s priced out before they can begin?

## Sources and Scope

### 1. Data Source

I found a comprehensive dataset titled 'Cost of International Education' on Kaggle that compiles information about the cost of international education across various countries. This dataset includes key indicators such as average annual tuition fees for international students, cost of living estimates, visa expenses, and in some cases, scholarship availability. It provided a solid foundation for analyzing global trends in education affordability.

### 2. Geographic Scope

The dataset spans a wide range of countries across multiple continents, allowing for a balanced global perspective. To ensure comparability, all monetary values were standardized to USD.

### 3. Data Points/Variables
The dataset consisted of 12 columns inclusing: Country, City, University, Program, Level, Duration_Years, Tuition_USD, Living_Cost_Index, Rent_USD, Visa_Fee_USD, Insurance_USD, Exchange_Rate.

## Loading the Data
```use cost of international education;
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy.stats import linregress     # Linear regression analysis
import geopandas as gpd     # Geographic data handling


# Suppress warnings for clear output
import warnings 
warnings.filterwarnings('ignore')

# Load data
df=pd.read_csv('C:/Users/Administrator/OneDrive/Desktop/Data Projects/International_Education_Costs.csv')
```
## Data Cleaning & Validation
Although the dataset I sourced from Kaggle was already well-structured and clean, I didn’t take that for granted. As a first step, I conducted a basic data validation process to ensure the quality and integrity of the information before proceeding with analysis.

This included:

Checking for missing values across key columns such as tuition fees, cost of living, and visa expenses.

Inspecting for duplicates or inconsistencies.

Verifying that numerical values fell within realistic ranges (e.g., tuition wasn’t accidentally entered as $500,000).

Ensuring consistent formatting for categorical data like country names and education levels.

No major issues were found, which allowed me to proceed confidently with analysis. Still, this step was essential—not just for accuracy, but to maintain transparency and trust in the insights derived from the data.
#### 1. Quick glance at the data
```
df.head()
```
#### 2. Check for Missing Values
```
df.isnull().sum()
```
#### 3. Check for Duplicate Rows
```
df.duplicated().sum()
```
#### 4.Summary Statistics for Numeric Columns
```
df.describe()
```
#### 5. Consistency in Categorical Fields
```
df['Country'].unique()
```
## Exploratory Data Analysis
Before diving into comparisons and visualizations, I explored the dataset to understand the patterns and relationships hidden in the numbers. This phase helped shape the story of international education costs—not just in terms of tuition, but in total experience.
```
# Set the figure size for better readability
plt.figure(figsize=(10, 6))

# Plot the histogram with a kernel density estimate (KDE)
sns.histplot(df['Tuition_USD'], bins=30, kde=True, color='skyblue')

# Add labels and title
plt.title('Distribution of Tuition Fees Across Countries')
plt.xlabel('Tuition Fee (USD)')
plt.ylabel('Number of Countries')

# Show the plot
plt.show()

```
![distribution of fees across countries](https://github.com/user-attachments/assets/207979ff-2822-48f2-b687-c1ee95adcd44)

The distribution of tuition fees across countries reveals that the majority of countries offer affordable education, typically under $10,000. However, a small number of countries significantly exceed this range, with some institutions charging as high as $60,000. This right-skewed distribution suggests that while international education is accessible in many parts of the world, a few countries stand out as outliers in terms of cost.


  I then focussed on the ten most expensive countries
```
# Group and get average tuition per country
top_countries = df.groupby('Country')['Tuition_USD'].mean().sort_values(ascending=False).head(10).index

# Filter the dataframe
df_top = df[df['Country'].isin(top_countries)]

# Plot box plot
plt.figure(figsize=(12, 6))
sns.boxplot(x='Country', y='Tuition_USD', data=df_top)
plt.xticks(rotation=45)
plt.title('Tuition Fee Distribution in Top 10 Most Expensive Countries')
plt.show()
```
![top 10 most expensive countries](https://github.com/user-attachments/assets/385f9552-0603-4e87-b0f9-b26c2393081a)

From my analysis of the boxplot, I observed several key trends in tuition fees across different countries. The USA stands out with the highest median tuition fees of around 50,000 USD, showing the greatest variability across institutions. This suggests that tuition fees in the USA can vary significantly depending on the institution. On the other hand, Lebanon has the lowest median tuition fees, approximately 15,000 USD, with a much narrower range, indicating that costs are more consistent within the country. The USA, UK, and Canada also show the highest variability, with large interquartile ranges (IQRs) and wide whisker spans, highlighting the broad differences in tuition fees within these countries. In terms of outliers, I found that most countries have low-end outliers, where some institutions charge significantly less than the typical range, while countries like Singapore, New Zealand, and Ireland show a few high-end outliers. Interestingly, countries like Ireland, Hong Kong, and Lebanon have tuition medians clustered around 15,000–20,000 USD, which makes them relatively more affordable within the otherwise expensive group of countries. This analysis gives me a better understanding of the global tuition fee landscape, particularly the extremes and variability that exist.

```
# Calculate the average cost for each component
cost_components = {
    'Tuition': df['Tuition_USD'].mean(),
    'Living Expenses': df['Living_Cost_Index'].mean(),
    'Accommodation': df['Rent_USD'].mean(),
    'Visa Fees': df['Visa_Fee_USD'].mean(),
    'Insurance': df['Insurance_USD'].mean()
}

# Remove any components with NaN values (if applicable)
cost_components = {k: v for k, v in cost_components.items() if pd.notna(v) and v > 0}

# Data for the pie chart
labels = cost_components.keys()
sizes = cost_components.values()
colors = ['#FF9999', '#66B2FF', '#99FF99', '#FFCC99', '#FFD700']
explode = (0.1, 0, 0, 0, 0)  # Highlight the first slice (e.g., Tuition)

# Create pie chart
plt.figure(figsize=(8, 8))
plt.pie(sizes, explode=explode, labels=labels, colors=colors, autopct='%1.1f%%', startangle=90)
plt.title('Average Cost Distribution for International Education')
plt.axis('equal')  # Equal aspect ratio ensures a circular pie
plt.tight_layout()

# Show plot
plt.show()
```

![piechart](https://github.com/user-attachments/assets/891a1565-b929-4282-93e4-3187cfcb6148)

Based on my pie chart analysis, it's clear that tuition fees make up the largest portion of the total cost for international students. This suggests that any meaningful efforts to reduce the financial burden should primarily target tuition-related expenses. In contrast, components like visa fees and insurance contribute only a small percentage to the overall cost, indicating that they are relatively consistent and less impactful across different programs and locations. This insight led me to consider deeper analysis opportunities—for example, comparing how the cost distribution shifts across countries, degree levels, or specific fields of study. It would also be interesting to explore possible correlations between living expenses and external factors such as a country’s living-cost index to gain more context around these financial patterns.

