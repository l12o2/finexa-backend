# finexa-backend
finexa-backend
python -m venv venv
source venv/bin/activate  # o venv\\Scripts\\activate en Windows
pip install -r requirements.txt
python backend.py
streamlit run main.py

### Estructura base de plataforma web simple para Finexa (Streamlit + Backend)

# 1. main.py (Frontend - Streamlit)
import streamlit as st
import requests
import pandas as pd
import plotly.express as px

st.set_page_config(page_title="Finexa Dashboard", layout="wide")
st.title("Finexa - Plataforma de Gestión de Carteras con IA")

# Obtener datos del backend (simulados)
res = requests.get("http://localhost:8000/portfolio")
data = res.json()
portfolio_df = pd.DataFrame(data['positions'])

# Mostrar cartera actual
st.subheader("Estado actual de la cartera")
st.dataframe(portfolio_df)

# Gráfico de distribución
fig = px.pie(portfolio_df, names='asset', values='weight', title='Distribución de activos')
st.plotly_chart(fig)

# Decisión del algoritmo
st.subheader("Última decisión del algoritmo")
st.text(data['last_decision'])

# Métricas de desempeño
st.subheader("Rendimiento")
st.metric("Rentabilidad acumulada", f"{data['metrics']['return']}%")
st.metric("Máximo Drawdown", f"{data['metrics']['drawdown']}%")


# 2. backend.py (FastAPI Backend)
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
import uvicorn

app = FastAPI()

# Permitir acceso desde Streamlit
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/portfolio")
def get_portfolio():
    return {
        "positions": [
            {"asset": "AAPL", "weight": 30},
            {"asset": "GOOGL", "weight": 25},
            {"asset": "TSLA", "weight": 20},
            {"asset": "AMZN", "weight": 25}
        ],
        "last_decision": "Comprar más TSLA, reducir GOOGL",
        "metrics": {
            "return": 12.5,
            "drawdown": -4.3
        }
    }

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)


# Requisitos (requirements.txt)
# streamlit
# fastapi
# uvicorn
# plotly
# pandas
# requests

# Para correr:
# 1. Ejecuta backend: python backend.py
# 2. Ejecuta frontend: streamlit run main.py


