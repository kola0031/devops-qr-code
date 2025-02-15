FROM python:3.9-slim

# Set the working directory
WORKDIR /usr/src/app

# Install dependencies
COPY requirements.txt ./

# Install dependencies  
RUN pip install --no-cache-dir -r requirements.txt

# Copy the source code
COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"] 