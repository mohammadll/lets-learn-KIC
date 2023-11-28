FROM python:3.8-slim-buster
WORKDIR /kong-ingress
ENV FLASK_APP kong-ingress-multi-backends.py
COPY requirements.txt . 
RUN pip3 install -r requirements.txt
COPY kong-ingress-multi-backends.py .
COPY templates ./templates
CMD ["flask", "run", "--host=0.0.0.0"]
