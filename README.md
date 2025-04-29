
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
Here’s the updated `README.md` section incorporating your **Sentiment Analysis** workflow with NewsAPI and PostgreSQL integration:

---

# 📰 Sentiment Analysis Pipeline  
**Automated news sentiment scoring and storage in AWS RDS PostgreSQL**  

## 🔍 Workflow Overview  
1. **Extract**: Fetch financial news from [NewsAPI](https://newsapi.org/).  
2. **Analyze**: Score sentiment using `NLTK`'s VADER.  
3. **Store**: Save results to PostgreSQL with sentiment labels.  

```python
# Key Steps (src/sentiment_analysis.py)
from nltk.sentiment import SentimentIntensityAnalyzer

# 1. Fetch NewsAPI data
articles = requests.get(f"https://newsapi.org/v2/everything?q=stock&apiKey={API_KEY}").json()

# 2. Calculate Sentiment
df_news["sentiment_score"] = df_news["title"].apply(
    lambda x: sia.polarity_scores(x)["compound"] if isinstance(x, str) else 0
)
df_news["sentiment_label"] = df_news["sentiment_score"].apply(
    lambda x: "positive" if x > 0 else ("negative" if x < 0 else "neutral")
)

# 3. Store in PostgreSQL
engine = create_engine(f'postgresql://{rds_user}:{rds_password}@{rds_host}:{rds_port}/{rds_db}')
df_news.to_sql('financial_news_sentiment', engine, if_exists='replace', index=False)
```

---

## 🗃 Database Schema (PostgreSQL)  
```sql
CREATE TABLE financial_news_sentiment (
    publishedAt TIMESTAMP,        -- Article timestamp
    title TEXT,                  -- News headline
    description TEXT,            -- Summary content
    url TEXT PRIMARY KEY,         -- Unique article URL
    sentiment_score REAL,         -- Compound score (-1 to 1)
    sentiment_label TEXT          -- "positive"/"neutral"/"negative"
);
```

---

## 📊 Sentiment Classification Logic  
| Score Range  | Label      | Interpretation              |  
|--------------|------------|-----------------------------|  
| `> 0.05`     | Positive   | Bullish market sentiment    |  
| `-0.05 to 0.05` | Neutral  | No strong sentiment        |  
| `< -0.05`    | Negative   | Bearish market sentiment    |  

---

## 🚀 How to Use  
1. **Set Environment Variables** (`.env`):  
   ```ini
   NEWS_API_KEY=your_key
   RDS_HOST=your-rds-endpoint.rds.amazonaws.com
   RDS_DB=your_db_name
   ```

2. **Run the Script**:  
   ```bash
   python src/sentiment_analysis.py
   ```

3. **Query Results**:  
   ```python
   # Get most negative news
   pd.read_sql(
       "SELECT * FROM financial_news_sentiment WHERE sentiment_score < -0.3 ORDER BY publishedAt DESC LIMIT 5",
       engine
   )
   ```

---



**💡 Note**: Use `python-dotenv` to manage secrets. For production, deploy with **Docker** and **AWS RDS**.  

