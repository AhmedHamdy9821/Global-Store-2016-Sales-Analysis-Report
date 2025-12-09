# Global-Store-2016-Sales-Analysis-Report 📊

## Contents

-  [Project Overview](#project-overview)
 - [Data Source & Tools](#data-source--tools)
- [Technical Implementation & Workflow](#technical-implementation--workflow-️)
 -  [Key Findings and Business Recommendations](#key-findings-and-business-recommendations)
 - [Report Screenshots](#report-screenshots)
  - [Author & Call to Action](#author--call-to-action)





---

## Project Overview

A professional Power BI project. End-to-end Sales and Profitability analysis for a Global Retail Store, leveraging DAX and a star schema model to provide actionable BI insights.

The final Power BI dashboard allows executive stakeholders to:

- **Track Key Performance Indicators (KPIs):** Monitor sales, profit margins, and order fulfillment across various dimensions and YOY growth%.
- **Identify Strategic Opportunities:** Pinpoint the highest-performing Regions, Segments, and Product Categories.
- **Mitigate Risks:** Detect specific low-margin or unprofitable product lines and understand the underlying reasons for negative performance.

---

## Data Source & Tools

- **Source:**   <a href="https://github.com/AhmedHamdy9821/Global-Store-2016-Sales-Analysis-Report/blob/main/global_superstore_2016.xlsx">Dataset Download</a> 

- **Tools:**  `Excel`, `Power Query` and `Power BI` 

---

## Technical Implementation & Workflow 🛠️


### 1- Data Cleaning & Transformation ( Power Query ):

- **Handling Missing Values:** Specifically for *Manager Column*.

- **Merge Queries :** Merge *Returns* and *Manager* sheets with *sales* sheet to have one fact table.

- **Data Type Correction:** Ensured consistent data types.

- **Products Dimension Creation:** Generate a separate *Product table* to normalize the *Fact Sales* table.


### 2- Data Modeling and Relationship Management:


- **Architecture:** Implemented a *Star Schema* data model to optimize query performance and ensure data accuracy.

     - **Fact Table:** `Sales` (containing transactional metrics like Sales, Profit, Quantity).
 
     - **Dimension Tables:** `Product`, `Customer`, `Date`.

- **Relationships:** Established one-to-many relationships (e.g., `Product[Product ID]` to `Sales[Product ID])` with correct cardinality and cross-filter direction. This ensures measures calculate correctly across all slicers and visuals.

### 3- Advanced DAX Measures and Calculations:

- **Calulated Tables**: Creat Dimension tables To ensure a highly functional and efficient Star Schema and optimize model

     - **Date Table** : A dedicated Date table was created using the CALENDAR function. 
                        This table includes hierarchical columns (Year, Quarter, Month Name, etc.) and is essential for all time intelligence calculations.
                        

     - **Customer Table:** A separate Customer dimension table was generated using the SUMMARIZE function to aggregate sales and profit metrics at the customer level.

- **Time Intelligence:**  Utilized DAX functions like `PREVIOUSYEAR` and `DATEADD` to calculate Total Sales Year-over-Year (YoY) Change.

- **Key Performance Indicators (KPIs):** Defined critical measures such as:
    - Profit Margin %
    - Return Rate
    - Basket Size
     

- **Unique Dax For Analysis :**  More deep and detailed Analysis.
  
  - **Customer Segmentation** : based on a Pareto % Analysis as how customer contribute in total sales.

    #### 💡 Customer Segmentation ( Hidden table):

     ```dax
       Customer classes = 
       VAR CustomerClasses =
       DATATABLE (
        "Customer class number", INTEGER,
        "Customer class", STRING,
        "Min Sales", CURRENCY,
        {
            { 1, "Silver", 0 },
            { 2, "Gold", 1309.86 },
            { 3, "Platinum", 2135.28 },
            { 4, "Titanium", 3486.32 }
          }
        )
      VAR MaxSalesEver =
       CALCULATE (
        [Total Sales],
        REMOVEFILTERS ()
       )
     RETURN
      ADDCOLUMNS (
        CustomerClasses,
        "Max Sales",
        VAR CurrMinSales = [Min Sales]
        RETURN
            COALESCE (
                MINX (
                    FILTER (
                        CustomerClasses,
                        [Min Sales] > CurrMinSales
                    ),
                    [Min Sales]
                ),
                MaxSalesEver
            )
     )


- **Product Segmentation** : based on Avg. Sales and Avg. Profit metrics.
     
  #### 💡 Product Segmentation ( Calculated Column) :

   ```dax
    Product Segment Static = 
    VAR AVGSales =
    AVERAGEX( 
        ALL(Sales),
        [Total Sales]
    )
    VAR AVGProfit = 
    AVERAGEX(
        ALL(Sales),
        [Total Profit]
    )
    VAR ProductSales = Products[Product Sales]
    VAR ProductProfit = Products[Product Profit]
    RETURN
    IF(
        ProductSales >= AVGSales && ProductProfit >= AVGProfit, "High Sales - High Profit",
        IF(
        ProductSales >= AVGSales && ProductProfit < AVGProfit, "High Sales - Low Profit",
        IF(
        ProductSales < AVGSales && ProductProfit >= AVGProfit, "Low Sales - High Profit",
        "Low Sales - Low Profit"
    )))

 - **Cohort Analysis:** To visualize customer retention across years.

    #### 💡Cohort:

    ```dax
     Customer Retention % = 
     DIVIDE(
        [Total Customers],
        CALCULATE(
            [Total Customers],
            FILTER(
                ALL(Sales),
                Sales[First Year Purchase] = MIN(Sales[First Year Purchase])
            )
        )
     )
    

---

## Visualization and User Experience (UX)

   - **Report Structure:**  Designed a five-page report focusing on *`Sales`, `Returns`, `Customers`, `Products` and `Insights`*
   - **Design Principles:** Followed best practices for data visualization ( fixed color pallet, consistent sync filtering, clean aesthetic) to ensure maximum usability and readability.
   - **Interactivity:** Implemented dynamic features like tooltips, bookmarks for navigation, and Info icons.

---

## Key Findings and Business Recommendations

  ### Insights: 

  - **Sales:**
  
      - Sales exhibit steady YOY growth over years with a significant decline in Q1 each year.
      
      - Western Europe leads in profit, while Western Asia shows consistent losses.
      
      - Asia Pacific has the highest market share and total customers.

  - **Returns:**

  
      - Sales shows return rate by 4.19% 
      - Sub-category  Labels has the highest return rate by 5.16%

  - **Customers:**
   
     - Pareto analysis reveals that about 32% of customers drive 80% of total sales.
     
     - Customer retention analysis reveals a low customer retention as majority are one-time buyers.

  - **Products:**
     
      - Technology category & Copiers sub-category lead in profit.
      
      - We have about 686 products with in segment of "High Sales - Low Profit" which account for 21% of total sales but with total low profit
      
      - We found that decreasing total discount of products by 0.1 % will increase the average of total profit  by 257$

      ---

  ### Recommendations:
      
- Launch marketing campaigns and discount offers in Q1 period to encounters decline in sales.

- Focus efforts on the most profitable market regions as Western Europe.

- Reduce returns through quality control and feedback loops especially for high-return products.

- Enhance loyalty programs for high-value customers (Titanium, Platinum).

- Review Discount Strategy as it drives significant profit loss.

- Launch initiative to increase repeat purchases and gain customers trust.

- Focus on top-performing products to maximize ROI.

- Manage products in class "High Sales- Low Profit" to improve their profit value by many approaches like :
    Price changing, Cost reducing, Vendor changing.
    

  --- 
  
##  Report Screenshots

### 1- Overview- Sales Analysis

<img width="1327" height="743" alt="1- Overview- Sales Analysis" src="https://github.com/user-attachments/assets/881e7f17-2336-45df-a4c9-75137c4e059a" />

### 2- Returns Analysis

<img width="1327" height="739" alt="2- Returns Analysis" src="https://github.com/user-attachments/assets/87aafcc2-9e12-4ca2-b810-11a03714306e" />

### 3- Customers Analysis

<img width="1325" height="736" alt="3- Customers Analysis" src="https://github.com/user-attachments/assets/bd14f25f-4d62-4ec8-a8e5-4c6290b9e466" />

### 4- Products Analysis

<img width="1326" height="744" alt="4- Products Analysis" src="https://github.com/user-attachments/assets/e671002f-2b71-4164-a6f3-0fb2970c7fdc" />

### 5- Insights % Recommendations

<img width="1327" height="744" alt="5- Insights % Recommendations" src="https://github.com/user-attachments/assets/9b2947a1-809c-4453-b826-3f261aed10ce" />

### Model Schema

<img width="1481" height="747" alt="Model Schema" src="https://github.com/user-attachments/assets/034dca62-21d5-4ff6-ad39-6cce86726824" />


--- 

# Author & Call to Action

This project showcases my disciplined approach to leveraging advanced analytics (DAX, Cohort, Pareto) to deliver tangible business value and actionable strategy.
I am actively seeking **Data Analyst, BI Developer, and related freelance opportunities.**

| Field | Details |
| -----| -------- |
| Name | Ahmed Hamdy |
| Professional Title | PL-300 Certified Data Analyst |
| Email | ahmedhamdy9821@gmail.com |
| LinkedIn | <a href="https://www.linkedin.com/in/ahmed-hamdy-a3b927196/">linkedin Profile →</a> |
| Phone (WhatsApp)	| (+966)-534955363 |
| Resident in | Jeddah, Saudi Arabia |








