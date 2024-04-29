# 1. For building Django app
FROM python:3.9 AS development

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Set working directory
WORKDIR /app

# Copy requirements and install dependencies
COPY requirements.txt /app/
RUN pip install -r requirements.txt

# Copy Django app
COPY . /app

# Collect static files
RUN python manage.py collectstatic --noinput

# Set environment variables
ENV DJANGO_SETTINGS_MODULE=api.settings

# Run Daphne
# CMD ["daphne", "-u", "/tmp/daphne.sock", "api.asgi:application"]
CMD ["daphne", "-b", "0.0.0.0", "-p", "8000", "api.asgi:application"]
EXPOSE 8000

# 2. For Nginx setup
FROM nginx:alpine AS nginx

# Copy Nginx configuration
COPY ./.nginx/nginx.conf /etc/nginx/conf.d/default.conf

# Remove default nginx static assets
RUN rm -rf /usr/share/nginx/html/*

# Copy static files from builder stage
COPY --from=development /app/static /usr/share/nginx/html

# Containers run nginx with global directives and daemon off
ENTRYPOINT ["nginx", "-g", "daemon off;"]