# STAGE I - Build App
FROM python:3.6-alpine3.12 as build

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE 1

# Copy requirements.txt
COPY ./django/requirements.txt /code

# Install dependencies
RUN apk add --update --no-cache --virtual .build-deps \
    build-base \
    postgresql-dev \
    python3-dev \
    libffi-dev \
    jpeg-dev \
    zlib-dev \
    musl-dev \
    libpq \
    graphviz-dev \	
    && pip install --no-cache-dir -r /code/requirements.txt \
    && find /usr/local \
        \( -type d -a -name test -o -name tests \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' +

# STAGE II - Run App 
FROM python:3.6-alpine3.12

# Set environment variables for the final build
ENV PYTHONUNBUFFERED 1
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONPATH /code:$

# Set working directory
WORKDIR /code

# Install libs required
RUN apk add --update --no-cache libpq libjpeg-turbo

# Copy files from build 
COPY --from=build /usr/local/lib/python3.6/site-packages/ /usr/local/lib/python3.6/site-packages/
COPY --from=build /usr/local/bin/ /usr/local/bin/

# Copy files from django application to working directory
COPY ./django .

# Run server
EXPOSE 8000
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
