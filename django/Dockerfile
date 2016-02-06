FROM python:3.5

ENV PYTHONUNBUFFERED 1

# Create base environment
RUN mkdir /app
WORKDIR /app
ADD ./app_dir/requirements.txt /app/
RUN pip install --upgrade -r requirements.txt

# Add source code
ADD ./app_dir/ /app/
