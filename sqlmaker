import sqlite3
import requests
from datetime import datetime
currencies = [
    "MOVE", "ETH", "BTC", "XRP", "ONDO", "ADA", "HBAR", "TRX", "DOGE",
    "BNB", "TON", "FIL", "CVC", "GMX", "BICO", "ENA", "AGLD", "JASMY",
    "USDT", "GMT", "CVX", "PAXG", "CATI", "LPT", "UNI"
]
conn = sqlite3.connect("crypto_prices.db")
cursor = conn.cursor()
cursor.execute("""
    CREATE TABLE IF NOT EXISTS prices (
        currency TEXT PRIMARY KEY
    )
""")
conn.commit()
date_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
averages = {}

for currency in currencies:
    try:
        response = requests.get(f"https://api.nobitex.ir/v2/trades/{currency}USDT")
        data = response.json()
        if data.get("status") == "ok":
            prices = [float(trade["price"]) for trade in data.get("trades", [])]
            averages[currency] = round(sum(prices) / len(prices), 2) if prices else None
        else:
            averages[currency] = None
    except Exception as e:
        averages[currency] = None
try:
    cursor.execute(f"ALTER TABLE prices ADD COLUMN '{date_time}' REAL")
except sqlite3.OperationalError:
    pass
for currency, avg in averages.items():
    if avg is not None:
        cursor.execute(f"""
            INSERT INTO prices (currency, '{date_time}')
            VALUES (?, ?)
            ON CONFLICT(currency) DO UPDATE SET '{date_time}' = excluded.'{date_time}'
        """, (currency, avg))
conn.commit()
conn.close()

