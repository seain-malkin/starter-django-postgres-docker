# syntax=docker/dockerfile:1

ARG VERSION=3.9-slim
FROM python:${VERSION} AS python

# Optional arguments with defaults
ARG APP_NAME=app
ARG APP_USER=app_user
ARG ENTRYPOINT=entrypoint.sh

# Python Configuration
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Build Variables
ENV APP_DIR=/usr/src/${APP_NAME}

# Install OS dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    netcat \
    && rm -rf /var/lib/apt/lists/*

# Install app dependencies
RUN pip install --upgrade pip
COPY ./requirements.txt /tmp/requirements.txt 
RUN pip install -r /tmp/requirements.txt \
    && rm -f /tmp/requirements.txt

# Create and change to a non-root user
RUN useradd -U -r ${APP_USER} 
USER ${APP_USER}:${APP_USER}

# Switch pwd to app install dir
WORKDIR ${APP_DIR}

# Configure entrypoint script
COPY ./${ENTRYPOINT} ./entrypoint.sh
RUN sed -i 's/\r$//g' ./entrypoint.sh \
    && chmod +x ./entrypoint.sh

# Copy source files into app directory
COPY --chown=${APP_USER}:${APP_USER} . ${APP_DIR}

# Execute
ENTRYPOINT [ "./entrypoint.sh" ]