# FROM python
FROM python
RUN mkdir /app
WORKDIR /app
COPY requirements.txt /app
RUN pip install google_images_download
RUN pip install flask-jwt-extended
RUN pip install --no-cache-dir -r requirements.txt

COPY . /app
ENV SECRET_TEXT the_super_secret_text
EXPOSE 8000

CMD ["python", "manage.py", "runserver"]

