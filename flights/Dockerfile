# Use the official Python 3.8 image as the base image
FROM python:3.8-slim-buster

# Set the working directory inside the container
WORKDIR /python-docker

# Copy the requirements file into the container
COPY requirements.txt requirements.txt

# Install the required dependencies
RUN pip3 install -r requirements.txt

# Copy the rest of your application code into the container
COPY . .

# Expose port 5003
EXPOSE 5003

# --- OpenTelemetry configuration ---
# Identify service
ENV OTEL_SERVICE_NAME=flights \
    OTEL_RESOURCE_ATTRIBUTES=deployment.environment=staging,service.namespace=flight-simulator,service.version=1.0 \
    \
    # Enable all 3 signals
    OTEL_TRACES_EXPORTER=otlp \
    OTEL_METRICS_EXPORTER=otlp \
    OTEL_LOGS_EXPORTER=otlp \
    \
    # Send to Alloy gRPC endpoint
    OTEL_EXPORTER_OTLP_PROTOCOL=grpc \
    OTEL_EXPORTER_OTLP_ENDPOINT=http://alloy:4317 \
    \
    # Enable logs and correlate logs with traces
    OTEL_PYTHON_LOGGING_AUTO_INSTRUMENTATION_ENABLED=true \
    OTEL_PYTHON_LOG_CORRELATION=true \
    OTEL_PROPAGATORS=tracecontext,baggage


#ENTRYPOINT ["opentelemetry-instrument", "python3", "-m", "flask", "run", "--host=0.0.0.0", "--port=5003"]
ENTRYPOINT ["opentelemetry-instrument", "python3", "app.py"]