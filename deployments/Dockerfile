#!/bin/python3
FROM python:3.10

# COPY . .
COPY src src
COPY config.py .
COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

EXPOSE 8501

CMD ["streamlit", "run", "src/streamlit.py"]