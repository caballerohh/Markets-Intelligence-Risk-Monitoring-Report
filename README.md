# Markets-Intelligence-Risk-Monitoring-Report
Este repositorio contiene un ecosistema avanzado de **Inteligencia de Mercados** que automatiza la extracción de datos, el análisis de regímenes macroeconómicos y la generación de reportes ejecutivos en formato  proyecto integra métricas de volatilidad implícita, modelos de estrés y análisis de la curva de tasas para transformar datos crudos en *insights* estratégicos.
🎯 **Objetivo:** Desarrollar una herramienta integral de monitoreo de riesgos y oportunidades tácticas que centralice la visión macro y el impacto de escenarios de estrés en capital institucional[..

---

## 📖 Extended Overview
El sistema está diseñado para profesionales que requieren una visión técnica del mercado global. Mediante el uso de bibliotecas de manipulación de datos y maquetación de documentos (ReportLab), el motor genera reportes de 6 páginas que cubren desde el sentimiento del mercado (VIX) hasta proyecciones probabilísticas de precios mediante **Implied Cones**.



### 🎯 Key Objectives of the Analysis
* **Macro Regime Analysis:** Monitoreo de la divergencia entre el S&P 500 y el VIX para identificar entornos de "complacencia" o fragilidad en el sentimiento.
* **Predictive Alpha Forecasting:** Uso de la volatilidad implícita para proyectar rangos de movimiento esperados ($1\sigma$ y $2\sigma$) a 21 días.
* **Rates Structure & Recession Watch:** Seguimiento del spread 10Y-3M y el ratio **TIP/IEF** para evaluar expectativas de inflación y señales de recesión.
* **Diversification Health:** Análisis de correlaciones móviles (60D) para validar la efectividad de las coberturas tradicionales frente al riesgo sistémico.
* **Portfolio Stress Testing:** Cuantificación del impacto en el P&L ante choques de inflación, caídas de equity o aumentos paralelos en las tasas.

---

## 🔍 Assets & Indicators Analyzed
El motor procesa un universo cruzado de activos para una visión holística:

* **🚀 Equities & Sentiment:** S&P 500 (SPY) y el índice de volatilidad VIX.
* **🛡️ Fixed Income & Hedge:** US Treasuries (3M a 30Y), TLT (Bonos largos), IEF (Medio plazo) y TIP (Protección contra inflación).
* **📊 Macro Proxies:** Dólar Americano (DXY) para medir las condiciones financieras globales.

---

## 📈 Key Intelligence Results
* **Regime Detection:** Identificación de una fase de "expansión de múltiplos" con un VIX en niveles históricamente bajos, sugiriendo complacencia ante riesgos de cola
* **Tactical Warning:** Con un **RSI en 68**, el mercado se sitúa en la frontera técnica de sobrecompra, indicando una relación riesgo-recompensa desfavorable
* **Correlation Breakdown:** La correlación SPY/TLT rompió al alza (+0.45), eliminando el beneficio de la diversificación convencional
* **Scenario Impact:** Un choque de inflación se identifica como el escenario más devastador, con una pérdida estimada de -$700,000 en el portafolio modelo

---

## 🛠️ Code Structure & Logic

### 1. Data Engine 🔍
* Ingesta automatizada mediante `yfinance` y `pandas_datareader` con limpieza de datos y sincronización de frecuencias

### 2. Predictive Core 🧬
* Cálculo de **Conos de Volatilidad** basados en la volatilidad diaria derivada del VIX para establecer niveles de "Take Profit" parcial

### 3. Reporting Layer (ReportLab) 📑
* Maquetación profesional en PDF con estilos corporativos, tablas dinámicas y renderizado automático de gráficos

---

## 🚀 Technologies & Concepts Used
* **Quantitative Finance:** Volatilidad Implícita, Z-Score de Volatilidad, RSI, y Spread de Tasas.
* **Risk Management:** Stress Testing (Twists de curva), Análisis de Drawdowns, y Matrices de Correlación
* **Python Stack:** ReportLab (Generación de PDF), Pandas (Series temporales), Seaborn & Matplotlib (Visualización técnica).

---

## ⚙️ Installation & Requirements

### 1. Requisitos previos
```bash
pip install yfinance pandas numpy matplotlib seaborn reportlab
