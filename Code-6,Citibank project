######## IMPORTANTE #### ANTERIORMENTE EN UN CUADRO DE CÓDIGO: Instalar: "pip install reportlab" 

# LIBRERÍA PARA EL PROYECTO
import yfinance as yf
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
import seaborn as sns
from datetime import datetime, timedelta
from io import BytesIO
from reportlab.lib.pagesizes import A4
from reportlab.lib import colors
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, Image, Table, TableStyle, PageBreak
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib.enums import TA_CENTER, TA_JUSTIFY, TA_LEFT

# 1. INPUTS ESTRATÉGICOS
# MODIFICAR EL ANÁLISIS PARA DESCRIPCIONES DE CADA HOJA Y CADA PIE DE FIGURA
ANALYSIS = {
    "MACRO_HEADER": """<b>SNAPSHOT DIARIO:</b> Los mercados de renta variable continúan su expansión de múltiplos. Sin embargo, la fortaleza del Dólar (DXY) y el repunte en las tasas reales sugieren que condiciones financieras duras.""",

    "MACRO_BODY": """El entorno actual se define como la expansión de múltiplos, donde el crecimiento del S&P500 parece estar respaldado por la resiliencia del sentimiento del mercado, a pesar del endurecimiento financiero. El S&P500 mantiene una tendencia alcista estructural, la volatilidad (VIX) se mantiene en niveles históricamente bajos. Esto es una señal de complacencia, donde el mercado ignora el riesgo de cola.""",

    "ALPHA_BODY": """<b>Forecast (for 1 month):</b> El Implied Cone proyecta un rango de movimiento de 2.5% para el próximo mes. Esto indica que el activo está operando en el límite superior de lo que la volatilidad implícita considera normal. Con un RSI en 68, el mercado está en la frontera técnica de la sobrecompra por lo cual se sugiere cautela: la relación riesgo-recompensa para nuevas posiciones largas es desfavorable.""",

    "RATES_BODY": """La inversión de la curva (Spread 10Y-3M) persiste. Sin embargo, la atención se centra en los 'Breakevens' de inflación (<b>ver Fig 3.2.</b>). Esto indica que el mercado ya ha descontado una normalización, pero el repunte reciente en el tramo largo sugiere una inflación creciente. El ratio TIP/IEF muestra una tendencia al alza, sugiriendo que el mercado espera que la inflación real supere a la inflación nominal en el mediano plazo.""",

    "RISK_BODY": """El efecto de la diversificación se ha deteriorado. La correlación SPY/TLT ha roto al alza (+0.45), eliminando el beneficio de la diversificación y aumentando el riesgo sistémico de la cartera. La correlación de 0.91 entre TLT (Bonos largos) e IEF (Bonos medios), lo cual es esperado, pero la correlación negativa entre el SPY y el VIX confirma  su cobertura limpia contra una caída de renta variable.""",

    # TEXTO EN PIES DE FIGURA

    "CAPTION_MACRO": """<b>Fig 1.1:</b> La divergencia entre el S&P500 (Azul) subiendo y el VIX (Gris) en mínimos indica un entorno de 'Risk-On' frágil.""",
    "CAPTION_VIX": """<b>Fig 1.2:</b> Mide la volatilidad relativa. Valores < -1.5 (Verde) indican opciones baratas; > +2.0 (Rojo) indican pánico excesivo.""",
    "CAPTION_CONE": """<b>Fig 2.1:</b> Proyección a 1 mes basada en volatilidad implícita. El 68% del tiempo el precio se mantendrá en la zona azul clara.""",
    "CAPTION_CURVE": """<b>Fig 3.1:</b> Comparativa de la estructura de tasas hoy vs el pasado. Una curva invertida (cortas > largas) alerta recesión.""",
    "CAPTION_RECESSION": """<b>Fig 3.2:</b> Spread 10Y-3M negativo (Rojo) predice recesión. Ratio TIP/IEF (Rojo) mide expectativas de inflación.""",
    "CAPTION_CORR": """<b>Fig 4.1:</b> Correlación móvil SPY/TLT. Zonas Verdes = Cobertura efectiva. Zonas Rojas = Fallo de diversificación.""",
    "CAPTION_HEATMAP": """<b>Fig 4.2:</b> Mapa de calor de 90 días. Colores cálidos indican activos moviéndose al unísono (riesgo sistémico).""",
    "CAPTION_DD": """<b>Fig 5.1:</b> Muestra la profundidad de caída desde máximos. Note cómo los bonos (Rojo) siguen en drawdown profundo.""",
    "CAPTION_STRESS": """<b>Fig 5.2:</b> Impacto estimado en P&L ($) bajo escenarios de choque. El 'Bear Flattener' es el riesgo central actual.""",

    #MARKET MMONITORING IMPLICATIONS
    "TAKEAWAYS": """CONDITIONS: Iniciar cortos tácticos en activos de alto beta si el VIX supera 15.
    SUGGEST: Mantenerse en la parte corta de la curva en bonos de 1-3 años (SHY).
    OPPORTUNITIES: Usar la banda superior del Cono como nivel de Take Profit parcial.
    MONITOR: Los datos de CPI (Feb-13) y Nonfarm Payrolls (Feb-11) serán  críticos para validar la tesis de inflación pegajosa y forzar ajuste  en la curva de tasas"""
}
# MODIFICAR PARA PROXIMOS EVENTOS (REVISAR https://es.investing.com/economic-calendar )
UPCOMING_EVENTS = [
    ['Date', 'Time (ET)', 'Event / Release', 'Consensus / Impact'],
    ['Feb-10', '08:30 AM', 'Retail Sales MoM', 'Exp: 0.4% | Consumer Health'],
    ['Feb-11', '08:30 AM', 'Unemployment Rate', 'Exp: 4.4% | High Impact'],
    ['Feb-11', '08:30 AM', 'Nonfarm Payrolls', 'Exp: 70k  | High Impact'],
    ['Feb-13', '08:30 AM', 'CPI YoY (Inflation)', 'Exp: 2.5% | High Impact'],
    ['Feb-20', '08:30 AM', 'GDP (Q4) QoQ', 'Exp: 4.0% | Production'],
    ['Feb-20', '09:45 AM', 'PMI Global', 'Exp: 52.4  | Production']
]

#MODIFICAR A PORTAFOLIO A TESTEAR: TICKET,TEXTO,VALOR DE INVERSIÓN,DURATION DE SER F.I.
PORTFOLIO = {
    'SPY': {'Notional': 5_000_000, 'Duration': 0.0},
    'TLT': {'Notional': 6_000_000, 'Duration': 17.0},
    'IEF': {'Notional': 3_000_000, 'Duration': 7.5},
    'SHY': {'Notional': 4_000_000, 'Duration': 1.9}
}

# 2. DATA ENGINE
print("Descargando Datos...")
TICKERS_CURVE = {'3M': '^IRX','5Y': '^FVX', '10Y': '^TNX', '30Y': '^TYX'}
TICKERS_RISK = ['SPY', 'TLT', 'IEF', 'SHY', 'TIP', 'DX-Y.NYB', '^VIX']
ALL_TICKERS = list(TICKERS_CURVE.values()) + TICKERS_RISK

end_date = datetime.now()
start_date = end_date - timedelta(days=365*3)
raw = yf.download(ALL_TICKERS, start=start_date, end=end_date)

data = pd.DataFrame()
try:
    src = raw['Adj Close'] if 'Adj Close' in raw.columns else raw['Close']
    data = src.ffill().dropna()
except:
    data = raw.ffill().dropna()

for t in TICKERS_CURVE.values():
    if t in data.columns: data[t] = data[t].apply(lambda x: x/10 if x > 20 else x)

# 3. CÁLCULOS
print("⚙️ Calculando Métricas...")
def calc_changes(df):
    chg = pd.DataFrame(index=df.columns)
    chg['Last'] = df.iloc[-1]
    chg['1M %'] = df.pct_change(21).iloc[-1]
    chg['3M %'] = df.pct_change(63).iloc[-1]
    chg['6M %'] = df.pct_change(126).iloc[-1]
    chg['1Y %'] = df.pct_change(252).iloc[-1]
    return chg
metrics_df = calc_changes(data[['SPY', '^VIX', 'DX-Y.NYB', 'TLT']])

# Predictive Cone
last_price = data['SPY'].iloc[-1]
last_vix = data['^VIX'].iloc[-1]
daily_vol = (last_vix / 100) / np.sqrt(252)
days_fwd = 21
future_dates = [data.index[-1] + timedelta(days=i) for i in range(1, days_fwd+1)]
upper_1std = [last_price * (1 + daily_vol * np.sqrt(i)) for i in range(1, days_fwd+1)]
lower_1std = [last_price * (1 - daily_vol * np.sqrt(i)) for i in range(1, days_fwd+1)]
upper_2std = [last_price * (1 + 2 * daily_vol * np.sqrt(i)) for i in range(1, days_fwd+1)]
lower_2std = [last_price * (1 - 2 * daily_vol * np.sqrt(i)) for i in range(1, days_fwd+1)]

# RSI
delta = data['SPY'].diff()
gain = (delta.where(delta > 0, 0)).rolling(14).mean()
loss = (-delta.where(delta < 0, 0)).rolling(14).mean()
rs = gain / loss
data['RSI'] = 100 - (100 / (1 + rs))
# Indicators
vix_mean = data['^VIX'].rolling(126).mean()
vix_std = data['^VIX'].rolling(126).std()
data['VIX_Z'] = (data['^VIX'] - vix_mean) / vix_std
data['Spread_10Y3M'] = data[TICKERS_CURVE['10Y']] - data[TICKERS_CURVE['3M']]
data['Inflation_Proxy'] = data['TIP'] / data['IEF']
# Correlation & Stress
rets = np.log(data / data.shift(1))
roll_corr = rets['SPY'].rolling(63).corr(rets['TLT'])
def get_dd(s): return (s / s.cummax()) - 1.0
dd_spy = get_dd(data['SPY'])
dd_tlt = get_dd(data['TLT'])

stress_impact = {
    'Parallel +50bps': sum(-p['Notional']*p['Duration']*0.0050 for p in PORTFOLIO.values()),
    'Bear Flattener': (-PORTFOLIO['SHY']['Notional']*1.9*0.0050) + (-PORTFOLIO['TLT']['Notional']*17*0.0010),
    'Equity Crash -10%': PORTFOLIO['SPY']['Notional']*-0.10,
    'Inflation Shock': (PORTFOLIO['TLT']['Notional']*-0.05) + (PORTFOLIO['SPY']['Notional']*-0.08)
}

# 4. MOTOR GRÁFICO
print("Generando Gráficos V11 ...")
plt.style.use('seaborn-v0_8-whitegrid')
plt.rcParams['axes.grid'] = False
plt.rcParams['axes.spines.top'] = False
plt.rcParams['axes.spines.right'] = False
plt.rcParams['axes.spines.left'] = False
plt.rcParams['axes.spines.bottom'] = True
plt.rcParams['font.family'] = 'sans-serif'

CB = '#002D72'; CR = '#DA291C'
date_fmt = mdates.DateFormatter('%m-%Y')

def get_img():
    buf = BytesIO()
    plt.savefig(buf, format='png', dpi=120, bbox_inches='tight')
    buf.seek(0); plt.close()
    return buf

# Función auxiliar para estilos de eje
def style_ax(ax):
    ax.xaxis.set_major_formatter(date_fmt)
    ax.tick_params(axis='x', rotation=0, labelsize=6)
    ax.legend(frameon=False, loc='upper left', fontsize=6)

# FIG 1: MACRO
fig, ax1 = plt.subplots(figsize=(9, 3.5))
ax1.plot(data.index[-252:], data['SPY'].tail(252), color=CB, lw=2, label='S&P 500')
ax1.set_ylabel('S&P 500', color=CB, fontweight='bold')
ax2 = ax1.twinx()
ax2.fill_between(data.index[-252:], data['^VIX'].tail(252), color='grey', alpha=0.3, label='VIX')
ax2.axis('off')
style_ax(ax1)
img_macro = get_img()

# FIG 2: VIX Z
fig, ax = plt.subplots(figsize=(9, 3.0))
z = data['VIX_Z'].tail(252)
ax.plot(z.index, z, color=CB, lw=1.5, label='Z-Score')
ax.fill_between(z.index, z, 2, where=(z>2), color=CR, alpha=0.3)
ax.fill_between(z.index, z, -2, where=(z<-2), color='green', alpha=0.3)
ax.axhline(2, color=CR, ls='--', lw=0.8); ax.axhline(-2, color='green', ls='--', lw=0.8)
style_ax(ax)
img_vix_z = get_img()

# FIG 3: ALPHA CONE
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(9, 6), gridspec_kw={'height_ratios': [3, 1]})
# Cone
hist_days = 180
ax1.plot(data.index[-hist_days:], data['SPY'].tail(hist_days), color=CB, lw=2, label='History')
ax1.plot(future_dates, upper_1std, color='grey', alpha=0.5, ls='--')
ax1.plot(future_dates, lower_1std, color='grey', alpha=0.5, ls='--')
ax1.fill_between(future_dates, upper_1std, lower_1std, color=CB, alpha=0.1, label='1σ (68%)')
ax1.fill_between(future_dates, upper_2std, lower_2std, color=CB, alpha=0.05, label='2σ (95%)')
style_ax(ax1)
# RSI
ax2.plot(data.index[-hist_days:], data['RSI'].tail(hist_days), color='mediumorchid', lw=1.5, label='RSI (14 days)')
ax2.axhline(70, color=CR, ls=':', lw=1); ax2.axhline(30, color='green', ls=':', lw=1)
ax2.fill_between(data.index[-hist_days:], 70, 100, color=CR, alpha=0.1)
ax2.set_ylim(0, 100)
style_ax(ax2)
img_alpha = get_img()

# FIG 4: RATES CURVE
fig, ax = plt.subplots(figsize=(9, 4))
terms = ['3M', '5Y', '10Y', '30Y']
vals = list(TICKERS_CURVE.values())
ax.plot(terms, data[vals].iloc[-1], 'o-', color=CB, lw=2, label='Hoy')
ax.plot(terms, data[vals].iloc[-63], '^:', color='#0073CF', lw=1.5, label='Hace 1 Mes')
ax.plot(terms, data[vals].iloc[-252], '^:', color='grey', lw=1.5, label='Hace 1 Año')
# Sin titulo interno
ax.legend(frameon=False, loc='upper left', fontsize=8)
img_curve = get_img()

# FIG 5: RECESSION
data_24 = data.loc['2024-01-01':]
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(9, 8))
# Spread
ax1.plot(data_24.index, data_24['Spread_10Y3M'], color=CB, label='10Y-3M Spread')
ax1.fill_between(data_24.index, data_24['Spread_10Y3M'], 0, where=data_24['Spread_10Y3M']<0, color=CR, alpha=0.3)
ax1.axhline(0, color='black', lw=1)
style_ax(ax1)
# Inflation
ax2.plot(data_24.index, data_24['Inflation_Proxy'], color='firebrick', lw=1, label='TIP/IEF Ratio')
style_ax(ax2)
img_recession = get_img()

# FIG 6: CORRELATION
fig, ax = plt.subplots(figsize=(8, 3.5))
rc = roll_corr.tail(300)
ax.plot(rc.index, rc, color=CB, lw=1., label='60D Correlation')
ax.fill_between(rc.index, rc, 0, where=(rc>0), color=CR, alpha=0.2, label='Peligro (Corr+)')
ax.fill_between(rc.index, rc, 0, where=(rc<0), color='green', alpha=0.2, label='Seguro (Corr-)')
ax.axhline(0, color='black', lw=1)
style_ax(ax)
img_corr = get_img()

# FIG 7: HEATMAP
fig, ax = plt.subplots(figsize=(8, 5))
sns.heatmap(rets[TICKERS_RISK].tail(90).corr(), annot=True, fmt='.2f', cmap='RdBu', center=0, cbar=False)
img_heatmap = get_img()

# FIG 8: DRAWDOWNS
fig, ax = plt.subplots(figsize=(9, 3.5))
ax.plot(dd_spy.index[-300:], dd_spy.tail(300)*100, color=CB, label='S&P 500')
ax.plot(dd_tlt.index[-300:], dd_tlt.tail(300)*100, color=CR, label='Treasuries')
ax.fill_between(dd_spy.index[-300:], dd_spy.tail(300)*100, 0, color=CB, alpha=0.1)
style_ax(ax)
img_dd = get_img()

# FIG 9: STRESS TEST ($)
fig, ax = plt.subplots(figsize=(4.0, 3.5))
names = list(stress_impact.keys())
vals = list(stress_impact.values())
cols = ['firebrick' if x < 0 else 'green' for x in vals]
ax.barh(names, vals, color=cols)
ax.axvline(0, color='black')

for i, v in enumerate(vals): ax.text(v, i, f" ${v:,.0f}", va='center', fontweight='bold', fontsize=9)
img_stress = get_img()


# 5. ESTRUCTURA DEL PDF
print("Maquetando PDF Final V11 (Text Titles)...")
doc = SimpleDocTemplate("Markets_Intelligence_&_Risk_Monitoring_Report.pdf", pagesize=A4,
                        topMargin=20, bottomMargin=20, leftMargin=40, rightMargin=40)
styles = getSampleStyleSheet()
s_Title = ParagraphStyle('T', parent=styles['Heading1'], fontSize=24, textColor=colors.HexColor(CB))
s_Head = ParagraphStyle('H', parent=styles['Heading2'], fontSize=15, textColor=colors.white, backColor=colors.HexColor(CB), borderPadding=8, spaceBefore=10, spaceAfter=10)
s_Body = ParagraphStyle('B', parent=styles['Normal'], fontSize=10, leading=14, alignment=TA_JUSTIFY)
s_Caption = ParagraphStyle('C', parent=styles['Normal'], fontSize=9, textColor=colors.grey, alignment=TA_CENTER, spaceAfter=10)
s_ChartTitle = ParagraphStyle('CT', parent=styles['Heading3'], fontSize=11, textColor=colors.HexColor(CB), alignment=TA_LEFT, spaceAfter=2)

story = []

# --- HEADER ---
story.append(Paragraph("MARKETS REPORT | CROSS-ASSET STRATEGY", styles['Normal']))
story.append(Paragraph("Market Intelligence Monitor", s_Title))
story.append(Paragraph(f"Date: {datetime.now().strftime('%d %B %Y')} | Autor: Carlos Caballero (//www.linkedin.com/in/caballerohh/)", styles['Normal']))
story.append(Spacer(1, 15))

# --- PAGE 1: MACRO ---
story.append(Paragraph("1. Macro Overview & Regime Analysis", s_Head))
story.append(Paragraph(ANALYSIS["MACRO_HEADER"], s_Body))
story.append(Spacer(1, 5))
# Table
data_table = [['Ticker', 'Last', '1M %', '3M %', '6M %', '1Y %']]
for idx, row in metrics_df.iterrows():
    vals = [idx, f"{row['Last']:.2f}"]
    for col in ['1M %', '3M %', '6M %', '1Y %']: vals.append(f"{row[col]*100:.2f}%")
    data_table.append(vals)
t = Table(data_table, colWidths=['16%']*6)
t.setStyle(TableStyle([('BACKGROUND',(0,0),(-1,0),colors.HexColor('#E0E0E0')), ('GRID',(0,0),(-1,-1),0.5,colors.grey), ('ALIGN',(1,0),(-1,-1),'CENTER')]))
story.append(t)
story.append(Spacer(1, 10))
story.append(Paragraph(ANALYSIS["MACRO_BODY"], s_Body))

# Chart 1: Macro
story.append(Spacer(1, 5))
story.append(Paragraph("Macro Regime: Equity Trend vs Volatility Stress", s_ChartTitle))
story.append(Image(img_macro, width=480, height=200))
story.append(Paragraph(ANALYSIS["CAPTION_MACRO"], s_Caption))

# Chart 2: VIX
story.append(Paragraph("VIX Relative Value (Z-Score)", s_ChartTitle))
story.append(Image(img_vix_z, width=480, height=180))
story.append(Paragraph(ANALYSIS["CAPTION_VIX"], s_Caption))
story.append(PageBreak())

# --- PAGE 2: ALPHA ---
story.append(Paragraph("2. Tactical Alpha Dashboard", s_Head))
story.append(Paragraph(ANALYSIS["ALPHA_BODY"], s_Body))
story.append(Spacer(1, 10))

# Chart 3: Alpha
story.append(Paragraph("Predictive Alpha: Implied Cone & RSI", s_ChartTitle)) 
story.append(Image(img_alpha, width=480, height=350))
story.append(Paragraph(ANALYSIS["CAPTION_CONE"], s_Caption))
story.append(PageBreak())

# --- PAGE 3: RATES ---
story.append(Paragraph("3. Rates Structure & Recession Watch", s_Head))
story.append(Paragraph(ANALYSIS["RATES_BODY"], s_Body))
story.append(Spacer(1, 10))

# Chart 4: Curve
story.append(Paragraph("US Treasury Yield Curve Structure", s_ChartTitle))
story.append(Image(img_curve, width=480, height=220))
story.append(Paragraph(ANALYSIS["CAPTION_CURVE"], s_Caption))

# Chart 5: Recession
story.append(Paragraph("Recession Signals (Spread 10Y-3M & Inflation)", s_ChartTitle))
story.append(Image(img_recession, width=480, height=250))
story.append(Paragraph(ANALYSIS["CAPTION_RECESSION"], s_Caption))
story.append(PageBreak())

# --- PAGE 4: RISK ---
story.append(Paragraph("4. Risk & Correlation", s_Head))
story.append(Paragraph(ANALYSIS["RISK_BODY"], s_Body))
story.append(Spacer(1, 10))

# Chart 6: Corr
story.append(Paragraph("Diversification Health: Correlations", s_ChartTitle))
story.append(Image(img_corr, width=480, height=200))
story.append(Paragraph(ANALYSIS["CAPTION_CORR"], s_Caption))
story.append(Spacer(1, 10))

# Chart 7: Heatmap
story.append(Paragraph("Cross-Asset Correlation Matrix", s_ChartTitle)) # 
story.append(Image(img_heatmap, width=480, height=280))
story.append(Paragraph(ANALYSIS["CAPTION_HEATMAP"], s_Caption))
story.append(PageBreak())

# --- PAGE 5: PORTFOLIO ---
story.append(Paragraph("5. Portfolio Fragility & Stress Test", s_Head))
story.append(Paragraph("Los bonos han sufrido una caída desde máximos más profunda y prolongada que las acciones. El S&P500 ha mostrado recuperación más rápida, los Bonos mantienen drawdowns prolongados impactando en la liquidez institucional. Esto deja en evidencia que la renta fija ha dejado de ser el activo refugio tradicional en este ciclo de tasas.", s_Body))
story.append(Spacer(1, 10))

# Chart 8: Drawdowns
story.append(Paragraph("Portfolio Fragility: Historical Drawdowns", s_ChartTitle))
story.append(Image(img_dd, width=480, height=200))
story.append(Paragraph(ANALYSIS["CAPTION_DD"], s_Caption))
story.append(Spacer(1, 15))

# Chart 9: Stress
story.append(Paragraph("Portfolio Stress: Test Impact (USD $)", s_ChartTitle))
story.append(Paragraph("Bajo escenarios de cola (Twists de curva) se evidencia que 1) Un choque de inflación es el escenario más devastador, 2) el aumento paralelo de la curva de tasas de +50 bps impactaría en -$660,500 y 3) las tasas cortas subiendo más que las largas, el riesgo actual, generan impacto de $140,000.", s_Body))

story.append(Image(img_stress, width=300, height=200))
story.append(Paragraph(ANALYSIS["CAPTION_STRESS"], s_Caption))
story.append(PageBreak())

# --- PAGE 6: TAKEAWAYS ---
story.append(Paragraph("6. Executive Summary & Events", s_Head))
story.append(Paragraph("MARKET MONITORING IMPLICATIONS (Non-Advisory):", styles['Heading3']))
for line in ANALYSIS['TAKEAWAYS'].split('\n'):
    if line.strip(): story.append(Paragraph(line.strip(), ParagraphStyle('b', parent=s_Body, leftIndent=20, bulletText='•', spaceAfter=5)))
story.append(Spacer(1, 20))
story.append(Paragraph("UPCOMING EVENTS (NEXT 14 DAYS):", styles['Heading3']))
t_ev = Table(UPCOMING_EVENTS, colWidths=['15%', '15%', '35%', '35%'])
t_ev.setStyle(TableStyle([('BACKGROUND',(0,0),(-1,0),colors.HexColor(CB)), ('TEXTCOLOR',(0,0),(-1,0),colors.white), ('GRID',(0,0),(-1,-1),0.5,colors.grey), ('FONTSIZE',(0,0),(-1,-1),9)]))
story.append(t_ev)

doc.build(story)
print("REPORTE GENERADO.")
