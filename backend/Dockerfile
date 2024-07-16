FROM python:3.11-slim AS builder

WORKDIR /app

# Copy requirements.txt
COPY requirements.txt ./

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the backend application code
#COPY backend/ .

# Stage 2: Production environment for running the application
FROM python:3.11-slim

WORKDIR /app

# Copy application code
COPY . .

# Copy installed dependencies from the builder stage
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY --from=builder /usr/local/bin /usr/local/bin

# Expose port for the Flask application
EXPOSE 5000


# Command to run the application (using Gunicorn)
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]
