import streamlit as st
import pandas as pd
import FinanceDataReader as fdr
import datetime
import matplotlib.pyplot as plt
import matplotlib 
from io import BytesIO
import plotly.graph_objects as go
import pandas as pd

@st.cache_data
def get_stock_info():
    base_url =  "http://kind.krx.co.kr/corpgeneral/corpList.do"    
    method = "download"
    url = "{0}?method={1}".format(base_url, method)   
    df = pd.read_html(url, header=0, encoding='euc-kr')[0]
    df['종목코드']= df['종목코드'].apply(lambda x: f"{x:06d}")     
    df = df[['회사명','종목코드']]
    return df

def get_ticker_symbol(company_name):     
    df = get_stock_info()
    code = df[df['회사명']==company_name]['종목코드'].values    
    ticker_symbol = code[0]
    return ticker_symbol

st.title("무슨 주식을 사야 부자가 되려나...") 
st.markdown(
    """     <style>
    [data-testid="stSidebar"][aria-expanded="true"] > div:first-child{width:250px;}     </style>
    """, unsafe_allow_html=True )

st.sidebar.header("회사 이름과 기간을 입력하세요")
stock_name = st.sidebar.text_input('회사 이름', value="삼성전자").upper()

date_range = st.sidebar.date_input("시작일 - 종료일",
                 [datetime.date(2019, 1, 1), datetime.date(2021, 12, 31)]) 
clicked = st.sidebar.button("주가 데이터 확인")


if(clicked == True):
    ticker_symbol = get_ticker_symbol(stock_name)     
    start_p = date_range[0]               
    end_p = date_range[1] + datetime.timedelta(days=1) 
    df = fdr.DataReader(ticker_symbol, start_p, end_p, exchange="KRX")
    df.index = df.index.date
    st.subheader(f"[{stock_name}] 주가 데이터")
    st.dataframe(df.head())


    # 선 그래프 그리기 - matplotlib
    ax = df['Close'].plot(grid=True, figsize=(15, 5))
    ax.set_title("주가(종가) 그래프", fontsize=30) # 그래프 제목을 지정
    ax.set_xlabel("기간", fontsize=20)             # x축 라벨을 지정
    ax.set_ylabel("주가(원)", fontsize=20)         # y축 라벨을 지정
    plt.xticks(fontsize=15)                        # X축 눈금값의 폰트 크기 지정
    plt.yticks(fontsize=15)                        # Y축 눈금값의 폰트 크기 지정    
    fig = ax.get_figure()                          # fig 객체 가져오기    
    st.pyplot(fig)                                 # 스트림릿 웹 앱에 그래프 그리기

    # 선 그래프 그리기 - plotly
    # 캔들차트 만들기: https://plotly.com/python/candlestick-charts/
    # Plotly에서 제목, x축 제목, y축 제목 등 변경: https://plotly.com/python/figure-labels/
    #st.subheader(f"[{stock_name}] 주가 그래프")
    #fig = go.Figure(data=go.Ohlc(x=df.index,
    #                open=df['Open'],
    #                high=df['High'],
    #                low=df['Low'],
    #                close=df['Close']))
    #fig.update_layout(
    #    xaxis_title="기간",
    #    yaxis_title="주가",
    #    )

    #st.plotly_chart(fig, use_container_width=True)
    
    
    csv_data = df.to_csv()  
    excel_data = BytesIO()      
    df.to_excel(excel_data)     
    columns = st.columns(2) 
    with columns[0]:
        st.download_button("CSV 파일 다운로드", csv_data, file_name='stock_data.csv')   
    with columns[1]:
        st.download_button("엑셀 파일 다운로드", 
        excel_data, file_name='stock_data.xlsx')
