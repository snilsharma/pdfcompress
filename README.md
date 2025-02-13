# pdfcompress
import streamlit as st
from PyPDF2 import PdfReader, PdfWriter
import tempfile
import os

def compress_pdf(input_pdf, compression_level):
    output_pdf = tempfile.NamedTemporaryFile(delete=False, suffix=".pdf")
    
    reader = PdfReader(input_pdf)
    writer = PdfWriter()
    
    for page in reader.pages:
        page.compress_content_streams()  # Basic compression
        writer.add_page(page)
    
    with open(output_pdf.name, "wb") as f:
        writer.write(f)
    
    return output_pdf.name

st.title("Web-Based PDF Compressor")

compression_options = {
    "Low (40%)": 0.6,
    "Medium (70%)": 0.3,
    "High (90%)": 0.1
}

uploaded_file = st.file_uploader("Upload a PDF file", type="pdf")
compression_level = st.selectbox("Select Compression Level", list(compression_options.keys()))

if uploaded_file and st.button("Compress PDF"):
    with tempfile.NamedTemporaryFile(delete=False, suffix=".pdf") as temp_pdf:
        temp_pdf.write(uploaded_file.read())
        compressed_pdf_path = compress_pdf(temp_pdf.name, compression_options[compression_level])
        
        with open(compressed_pdf_path, "rb") as f:
            st.download_button("Download Compressed PDF", f, file_name="compressed.pdf", mime="application/pdf")
        
    os.remove(compressed_pdf_path)
