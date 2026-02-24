FROM python:3.12-slim AS backend

# Destination to copy all assets to during the build process.
ARG _WORKDIR=/app
# Set working directory to WORKDIR Argument
WORKDIR ${_WORKDIR}

# Default port for the app to start with
ENV APP_PORT=8000

# Avoid writing .pyc files and flush logs immediately in containers
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Copy all non-ignored files to image
COPY . ${_WORKDIR}

# Install package dependencies for app
RUN pip install -r requirements.txt \
    && chmod +x /app/entrypoint.sh \
    && sed -i 's/\r$//' /app/entrypoint.sh


# Expose the port the app runs on and start the app with the entrypoint script
EXPOSE $APP_PORT

# Start the app with the entrypoint script, which will run the necessary commands to start the app
ENTRYPOINT [ "/bin/sh", "-c", "/app/entrypoint.sh" ]