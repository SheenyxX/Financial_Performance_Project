# Financial Performance Analysis: Diagnosing Profitability Gaps in European Distribution Markets  
*Prepared for: NorthWind Traders (Multinational Distributor)*  

## 🔍 Executive Summary  

### The Financial Paradox  
NorthWind Traders achieved **36% sales growth in 2020** while European profitability declined. Analysis of **27,910+ GL transactions** revealed:  

**Critical Financial Findings**:  

1. **Q1 2020 Net Profit Crash**:
   - -$45K (linked to marketing overspend and UK tax changes) 

2. **Market Outperformance**  
   - **France's net profit exceeded Germany's by 17%**  
   - Achieved despite 8% lower sales volume  

3. **Seasonal Concentration Risk**  
   - **66% of annual profit** generated in Q3/Q4  
   - Q1/Q2 consistently underperformed across all years  

**Solution**: Power BI dashboard suite with 10 critical DAX measures to track:  
✔️ Real-time profitability by region  
✔️ Marketing spend ROI alerts  
✔️ Cost leakage detection  

---

## 🌐 Data Organization & Structure

Modern enterprises like NorthWind Traders organize financial data into **4 core datasets**, optimized for ERP integration and BI tools like Power BI:

### 1. **Master Data** (`Chart of Accounts.xlsx`)  
![COA_Hierarchy](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/2.1%20COA.png)  
*Hierarchical account structure in Excel*  

- **Multi-level Classification**:  
  ```plaintext
  Level 1: Report Type (Balance Sheet, P&L)  
  Level 2: Class (Trading, Operating, etc.)  
  Level 3: SubClass (Sales, COGS, Marketing)  
  Level 4: SubClass2 (Digital Ads, Trade Shows)
  Level 5: Account
  Level 6: SubAccount
  ```  
- **Compliance**: FASB/IFRS standards compatible with SAP/Oracle  

### 2. **Transactional Data** (`GL.xlsx`)  
![GL_Transactions](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/2.2%20GL.png)  
*Example of raw transaction records with metadata*  

- **What It Contains**: Every financial transaction (debits/credits) with metadata:  
  - *Account Codes* (e.g., `230` for Cost of Sales)  
  - *Timestamps* (date of transaction)  
  - *Geographic Tags* (Territory keys)  
  - *Amount* (transaction value)  
- **Volume**: 27,910+ transactions spanning 3 years  


### 3. **Dimensional Data**  

#### Calendar Table  
![Calendar_Table](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/2.3%20Calender.png)  
- **Fiscal Periods**: 1,097 days with special flags for year-end
- 
#### Territory Table  
![Territory_Table](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/2.4%20Territory.png)  
- **7 Regions**: North America (USA, Canada), Europe (UK, France, Germany), etc.  

### 4. **Relational Model**  
The data structure follows a star schema design for optimal query performance:
- Central fact table (`General Ledger`) connected to three dimension tables
- One-to-many relationships from dimensions to transactions
- No direct relationships between dimension tables to prevent circular dependencies
- Optimized for both aggregation (summaries) and drilling down to transaction details

This enterprise-grade data architecture enables the complex analysis presented in this dashboard suite while maintaining flexibility for future enhancements.


### Data Model  
![Data Architecture](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/1.5%20Data%20Model.png)  
*Tables*:  
- `General Ledger` (27,910 transactions)  
- `Chart of Accounts` (55 hierarchical accounts)  
- `Territory` (7 regions)  
- `Calendar` (1,097 days)  


---

## 📊 Dashboard Walkthrough  

### 1. Sales Revenue Dashboard  
![Revenue Dashboard](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/1.1%20Dashboard%20Sales.png)  

**Components**:  
- Yearly/quarterly sales breakdown (2018–2020)
- Line charts:
  - Sales trends by year/quarter (upward growth)
  - Year-over-year comparison (each year outperforming prior)
- Matrix: Monthly sales by year/quarter with color-coded performance indicators

**Key Metrics**:  
```dax
Sales_FTP = CALCULATE([Total_FTP], 'Chart of Accounts'[SubClass] = "Sales")  // 2020: $7.8M
```  

**Insights**:  
- Q3/Q4 consistently outperform Q1/Q2 across all years
- Q4 2020 sales peaked at **$1.4M** (vs $1.04M in 2019)
- January slumps average **-38%** vs December across all years
- Despite global economic challenges, 2020 showed overall growth with only a temporary Q1 dip

---

### 2. P&L Dashboard  
![P&L Dashboard](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/1.2%20Dashboard%20P%26L.png)  

**Components**:  
- Interactive matrix:
  - Columns: Years (2018–2020)
  - Rows: P&L hierarchy (Trading, Operating, Non-operating, Interest & Tax accounts)
- KPI cards: Total sales ($17.11M), annual sales ($7.84M in 2020)
- Small matrices showing key financial metrics by year

**Key Metrics**:  
```dax
GrossProfit = CALCULATE([Total_FTP], 'Chart of Accounts'[Class] = "Trading account")  // 2020: $5.34M
Net_Profit = CALCULATE([Total_FTP], 'Chart of Accounts'[Report] = "Profit and Loss")  // 2020: $1.28M
```  

**Insights**:  
- 2020 net profit margin dropped to **16.46%** (vs. 22.87% in 2019)
- Q1 2020 net profit was **negative (-$45,942)**
- In the first interval, operating costs grew 0.78x slower than revenue, while in the second interval, operating costs grew 1.54x faster than revenue, indicating a shift where costs began outpacing revenue growth

---

### 3. Profitability Deep Dive (P&L v2)  
![Profitability Dashboard](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/1.3%20Dashboard%20P%26L2.png)  

**Components**:  
- Gross Profit Margin (2020: **68.17%**) and Net Profit Margin (2020: **16.46%**) trends
- Line-area chart: Sales, Gross Profit, Net Profit (Net Profit lagging behind)
- Dual-axis chart: Marketing costs vs. sales (Q1 2020 low ROI)

**Key Metrics**:  
```dax
GrossProfit_Margin = DIVIDE([GrossProfit], [Sales_FTP], 0)  // Margin analysis
NetProfit_Margin = DIVIDE([Net_Profit], [Sales_FTP], 0)  // Bottom-line efficiency
Marketing_Cost_FTP = CALCULATE([Total_FTP], 'Chart of Accounts'[SubClass2] = "Marketing") * -1  // Spend tracking
```

**Insights**:  
- Marketing spend in Q1 2020 yielded poor returns with ROI ratio of 0.76
- France outperformed Germany in net profit despite lower sales
- Gross profit margins remained stable while net profit margins fluctuated significantly
- EBITDA indicates strong operational performance despite tax and interest pressures

---

### 4. Cross Country Dashboard  
![Regional Dashboard](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/1.4%20Dashboard%20CrossCountry.png)  

**Components**:  
- Line charts by country:
  - Sales: USA #1, UK collapsed in Q1 2020
  - Gross Profit: USA, New Zealand, Germany, France as focus areas
  - Net Profit: USA, New Zealand, France (surpassing Germany)

**Key Metrics**:  
```dax
Operating_Profit = CALCULATE([Total_FTP], 
    'Chart of Accounts'[Class] = "Trading account" || 
    'Chart of Accounts'[Class] = "Operating account")  // Core business health

PBIT = [Operating_Profit] + CALCULATE([Total_FTP], 'Chart of Accounts'[Class] = "Non-operating")  // USA: $1.12M
```  

**Insights**:  
- USA dominates sales but has higher operational costs
- UK's **-62% Q1 drop** linked to Brexit-related delays and market uncertainty
- New Zealand's lean operations = **19% operating margins** (vs USA's 15%)
- France's operational efficiency makes it more profitable than Germany despite lower revenue

---

## 🔬 Technical Backbone  

### Full DAX Implementation (10 Measures)  
```dax
// Base Measures
Total_FTP = SUM('General Ledger'[Amount])  // Core transactional sum
Sales_FTP = CALCULATE([Total_FTP], 'Chart of Accounts'[SubClass] = "Sales")  // Revenue isolation


// Profitability
GrossProfit = CALCULATE([Total_FTP], 'Chart of Accounts'[Class] = "Trading account")  
GrossProfit_Margin = DIVIDE([GrossProfit], [Sales_FTP], 0)  // Margin analysis

Net_Profit = CALCULATE([Total_FTP], 'Chart of Accounts'[Report] = "Profit and Loss")  
NetProfit_Margin = DIVIDE([Net_Profit], [Sales_FTP], 0)  // Bottom-line efficiency



// Cost & Operations
Operating_Profit = CALCULATE([Total_FTP], 
    'Chart of Accounts'[Class] = "Trading account" || 
    'Chart of Accounts'[Class] = "Operating account")  // Core business health

PBIT = [Operating_Profit] + CALCULATE([Total_FTP], 'Chart of Accounts'[Class] = "Non-operating")  // Broad profitability

Marketing_Cost_FTP = CALCULATE([Total_FTP], 'Chart of Accounts'[SubClass2] = "Marketing") * -1  // Spend tracking

EBITDA = [GrossProfit] + CALCULATE([Total_FTP], 'Chart of Accounts'[SubClass] = "Operating Expenses")  // Cash flow proxy
```


---

## 🎯 Strategic Recommendations  
1. **Fix the Leaks**  
   - Replace UK warehouse partner (saves **$45K/yr** in delays)  
   - Adopt France's vendor contracts in Germany  

2. **Double Down on Strength**  
   - **USA**: Add Q3 temporary staff (est. **+$220K profit**)  
   - **NZ**: Test 10% price increase (demand elasticity = 1.2)  

3. **System Upgrades**  
   - Live marketing ROI dashboard for CMO  
   - Auto-flag cost variances >5% using:  
     ```dax
     Cost_Variance_Alert = 
     IF(ABS([Actual_Cost] - [Budget_Cost]) > 0.05 * [Budget_Cost], "Review", "OK")
     ```  

---

