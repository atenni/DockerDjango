FROM python:3.5

ENV PYTHONUNBUFFERED 1

# Create base environment
RUN mkdir /app
WORKDIR /app
ADD ./app/requirements.txt /app/
RUN pip install --upgrade -r requirements.txt

# Add environment variables. (Used by django-environ.)
ADD ../secrets.env /app/

# Add source code
ADD ./app /app/