# stockmarket-data-engineering
An ETL pipeline for stock market data using Pandas, PostgreSQL, and FastAPI
# 📈 Stock Market Data Engineering Pipeline 🚀

## 🔹 Project Overview
This project builds an automated **ETL pipeline** for stock market data using **Alpha Vantage API** and **Pandas** for cleaning and processing. The pipeline stores data in **CSV** and exposes predictions through a **FastAPI** API.

## 🏗 Tech Stack
- **Data Extraction** → `Alpha Vantage API`, `Yahoo Finance API`
- **Data Cleaning & Processing** → `Pandas`
- **API Deployment** → `FastAPI`
- **Automation** → `Apache Airflow`
- **Storage** → `CSV` files for now (can be extended to PostgreSQL or AWS S3)

## 📊 Key Features
✅ Extract stock data (e.g., AAPL) from Alpha Vantage API  
✅ Clean and transform the data using Pandas  
✅ Deploy an API to predict stock trends using FastAPI

## 🛠 How to Run
### 1️⃣ Install Dependencies  
```bash
pip install -r requirements.txt
