#COPY requirements.txt /app/requirements.txt
#ENTRYPOINT ["python", "./launch.py"]

# Use the official Python image
FROM python:alpine3.10

# Set the working directory
WORKDIR /app 

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 5000 available to the world outside this container
EXPOSE 5000

# Define the command to run your app using the app.py file
CMD ["python", "./app.py"]
