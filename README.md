# GlobalTrade Analytics: Financial Performance Suite  
*Prepared for: NorthWind Traders (Multinational Distributor)*  

## 🔍 Executive Summary  
**Business Challenge**:  
NorthWind Traders faced declining profitability in European markets despite 12% revenue growth in 2020. Using 27,910+ GL transactions, we identified:  
- **Q1 2020 Net Profit Crash**: -$45K (linked to marketing overspend and UK tax changes)  
- **Hidden Star Market**: France's **22% net margins** (vs Germany's 14%)  
- **Seasonal Cash Flow Gaps**: 68% of annual profit concentrated in Q3/Q4  

**Solution**: Power BI dashboard suite with 10 critical DAX measures to track:  
✔️ Real-time profitability by region  
✔️ Marketing spend ROI alerts  
✔️ Cost leakage detection  

---

## 🌐 Data Organization & Structure
Modern enterprises like NorthWind Traders organize financial data into **4 core datasets**, optimized for ERP integration and BI tools like Power BI:

### 1. **Transactional Data** (`GL.xlsx`)  
- **What It Contains**: Every financial transaction (debits/credits) with metadata:  
  - *Account Codes* (e.g., `230` for Cost of Sales)  
  - *Timestamps* (date of transaction)  
  - *Geographic Tags* (Territory keys)
  - *Amount* (transaction value)
- **Volume**: 27,910+ transactions spanning 3 years
- **Typical Use**: Foundation for P&L statements, balance sheets, and cash flow analysis

### 2. **Master Data** (`Chart of Accounts.xlsx`)  
- **What It Contains**: Hierarchical mapping of accounts with multi-level classification:
  - **Level 1**: Report Type (Balance Sheet, Profit & Loss)
  - **Level 2**: Class (Trading, Operating, Non-operating)
  - **Level 3**: SubClass (Sales, COGS, Marketing, etc.)
  - **Level 4**: SubClass2 (Detailed categorization)
- **Corporate Standard**: Follows FASB/IFRS compliance structures used by SAP/Oracle

### 3. **Dimensional Data** (`Territory.xlsx`, `Calendar.xlsx`)  
- **Territory**: Geographic segmentation with 7 regions:
  - North America: USA, Canada
  - Europe: UK, France, Germany
  - Asia-Pacific: Australia, New Zealand
- **Calendar**: 3-year fiscal periods (1,097 days):
  - Year → Quarter → Month → Day
  - Special flags for fiscal year boundaries
- **Why It Matters**: Enables multi-dimensional analysis across time/region

### 4. **Relational Model**  
The data structure follows a star schema design for optimal query performance:
- Central fact table (`General Ledger`) connected to three dimension tables
- One-to-many relationships from dimensions to transactions
- No direct relationships between dimension tables to prevent circular dependencies
- Optimized for both aggregation (summaries) and drilling down to transaction details

This enterprise-grade data architecture enables the complex analysis presented in this dashboard suite while maintaining flexibility for future enhancements.

---

## 📊 Dashboard Walkthrough  

### 1. Sales Revenue Dashboard  
![Revenue Dashboard](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/1%20Dashboard%20Sales.png)  

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
- Q4 2020 sales peaked at **$4.1M** (vs $3.2M in 2019)
- January slumps average **-38%** vs December across all years
- Despite global economic challenges, 2020 showed overall growth with only a temporary Q1 dip

---

### 2. P&L Dashboard  
![P&L Dashboard](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/2%20Dashboard%20P%26L.png)  

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
- Operating costs grew **2.3x faster** than revenue
- Germany's tax burden = **28%** of net profit vs France's 19%

---

### 3. Profitability Deep Dive (P&L v2)  
![Profitability Dashboard](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/3%20Dashboard%20P%26L2.png)  

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
![Regional Dashboard](https://github.com/SheenyxX/Financial_Performance_Project/blob/main/4%20Dashboard%20CrossCountry.png)  

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

### Data Model  
![Data Architecture](images/data_model.png)  
*Tables*:  
- `General Ledger` (27,910 transactions)  
- `Chart of Accounts` (55 hierarchical accounts)  
- `Territory` (7 regions)  
- `Calendar` (1,097 days)  

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

