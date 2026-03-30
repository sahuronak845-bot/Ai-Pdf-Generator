# Ai-Pdf-Generator 
import streamlit as st
import google.generativeai as genai
from fpdf import FPDF

# UI Setup
st.set_page_config(page_title="Free AI PDF Maker", page_icon="📄")
st.title("📄 AI Topic to PDF Generator")
st.write("एक टॉपिक लिखें और तुरंत PDF डाउनलोड करें!")

# API Key Input (सुरक्षा के लिए इसे बाद में Streamlit Settings में डालेंगे)
api_key = st.sidebar.text_input("Enter Gemini API Key", type="password")

if api_key:
    genai.configure(api_key=api_key)
    model = genai.GenerativeModel('gemini-1.5-flash')

    topic = st.text_input("अपना टॉपिक या प्रॉम्प्ट यहाँ लिखें:")

    if st.button("Generate Content & PDF"):
        if topic:
            with st.spinner("AI लिख रहा है... कृपया इंतज़ार करें।"):
                # 1. Content Generation
                response = model.generate_content(f"Write a detailed article about {topic} in simple English/Hindi. Keep it professional.")
                text_content = response.text
                
                # 2. PDF Creation
                pdf = FPDF()
                pdf.add_page()
                pdf.set_font("Arial", size=12)
                
                # सफाई से टेक्स्ट लिखने के लिए (Multi-line support)
                pdf.multi_cell(0, 10, txt=text_content.encode('latin-1', 'ignore').decode('latin-1'))
                
                # Save PDF to binary
                pdf_output = pdf.output(dest='S').encode('latin-1')
                
                st.success("कंटेंट तैयार है!")
                st.download_button(label="📥 Download PDF", data=pdf_output, file_name=f"{topic}.pdf", mime="application/pdf")
        else:
            st.warning("कृपया कोई टॉपिक लिखें।")
else:
    st.info("शुरुआत करने के लिए बाईं तरफ (Sidebar) में अपनी Gemini API Key डालें।")
