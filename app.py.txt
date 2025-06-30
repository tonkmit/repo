import streamlit as st
import pandas as pd

# โหลดข้อมูลจากไฟล์ Excel
FILE_PATH = 'สารบัญแฟ้มอด1(ลบขายหนี้ABCแล้ว)1.7.68update.xlsx'

@st.cache_data
def load_data():
    df = pd.read_excel(FILE_PATH, sheet_name='ค้นแฟ้ม', header=None, skiprows=4)
    df = df.dropna(how='all', axis=0).dropna(how='all', axis=1)
    df.columns = df.iloc[0]
    df = df[1:]
    return df

df = load_data()

st.title("ค้นหาแฟ้มจากเลขที่สัญญา")

contract_id = st.text_input("กรอกเลขที่สัญญา (บัญชี):")

if contract_id:
    # หาคอลัมน์ที่เป็นเลขที่บัญชี
    matched_rows = df[df.astype(str).apply(lambda row: contract_id in row.values, axis=1)]

    if not matched_rows.empty:
        for idx, row in matched_rows.iterrows():
            st.success("พบข้อมูล:")
            st.write(f"📁 **ตู้:** {row.get('ตู้', 'ไม่พบ')}")
            st.write(f"🗄️ **ชั้น:** {row.get('ชั้น', 'ไม่พบ')}")
            st.write(f"🔢 **ลำดับ:** {row.get('ลำดับ', 'ไม่พบ')}")
    else:
        st.warning("ไม่พบเลขที่สัญญาในระบบ")