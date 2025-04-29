
# 📈 Stock Market Data Engineering Pipeline 🚀  
**Multi-source ETL pipeline for financial data (Stocks, Economic Indicators, News) using Python, PostgreSQL, and FastAPI**  

---

## 🔍 Project Overview  
This pipeline integrates data from **three key sources**:  
1. **Stock Data** (AAPL) → Alpha Vantage API/Yahoo Finance  
2. **Economic Indicators** → [FRED API](https://fred.stlouisfed.org/docs/api/fred/)  
3. **Market News** → [News API](https://newsapi.org/)  

Processed data is stored in **PostgreSQL** and served via **FastAPI** for analytics.  

---

## 🛠 Tech Stack  
| Component          | Tools & Technologies                          |  
|--------------------|-----------------------------------------------|  
| **Data Sources**   | Alpha Vantage, FRED API, News API             |  
| **Processing**     | Pandas, NumPy                                 |  
| **Storage**        | PostgreSQL (AWS RDS)                          |  
| **API**            | FastAPI                                       |  
| **Orchestration**  | Apache Airflow (Optional)                     |  

---

## 🚀 Key Features  
✅ **Multi-Source Extraction**  
- **Stocks**: OHLC data for AAPL (Open/High/Low/Close).  
- **Economic Data**: FRED indicators (e.g., GDP, Unemployment Rate).  
- **News**: Headlines/sentiment analysis via News API.  

✅ **Unified Storage**  
- All data merged into a **PostgreSQL** database with tables:  
  ```sql
  CREATE TABLE stocks (symbol VARCHAR, date TIMESTAMP, open FLOAT, ...);
  CREATE TABLE news (title TEXT, sentiment FLOAT, source VARCHAR, ...);
  ```

✅ **API Endpoints**  
| Endpoint                | Description                          |  
|-------------------------|--------------------------------------|  
| `GET /stock/{ticker}`   | Fetch stock data (e.g., `/stock/AAPL`) |  
| `GET /news`             | Latest market news                   |  

---

## 🏃‍♂️ Quick Start  
1. **Set API Keys** (`.env`):  
   ```ini
   ALPHA_VANTAGE_KEY=your_key
   FRED_API_KEY=your_key
   NEWS_API_KEY=your_key
   ```

2. **Run ETL Pipeline**:  
   ```bash
   python src/etl.py  # Extracts AAPL, FRED, News data → PostgreSQL
   ```

3. **Start FastAPI**:  
   ```bash
   uvicorn src.api:app --reload
   ```

---

## 📂 Example Queries  
**1. Fetch AAPL Stock + Related News**  
```python
import pandas as pd
from src.db import get_db_connection

conn = get_db_connection()
df = pd.read_sql("""
    SELECT s.date, s.close, n.title 
    FROM stocks s
    JOIN news n ON s.date::DATE = n.published_at::DATE
    WHERE s.symbol = 'AAPL'
""", conn)
```

**2. FRED Economic Data Integration**  
```python
from fredapi import Fred
fred = Fred(api_key=os.getenv("FRED_API_KEY"))
gdp = fred.get_series('GDP')  # Pandas DataFrame
```

---

## 🌐 Data Flow  
![image](https://github.com/user-attachments/assets/1e933324-31d7-4aa0-a220-bd869f3bc4d0)


---

**💡 Note**: Use `python-dotenv` to manage secrets. For production, deploy with **Docker** and **AWS RDS**.  

