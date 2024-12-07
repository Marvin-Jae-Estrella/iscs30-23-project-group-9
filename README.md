# iscs30-23-project-group-9
This is the repository for the project of group 9 for ISCS 30.23-XW

docker build image command: docker build -t group9-iscs-xw .
docker run app in container command: docker run -d --name group9-lab1 -p 8000:8000 group9-iscs-xw

Final Deliverable "Deploy your app on Kubernetes" specifications:
A. Building of docker image for Lazapee 
  1. Create a Dockerfile. Input the following:
     ------------------------------------------------------------------
     FROM python:3.11

     ENV PYTHONWRITEBYTHECODE=1
     ENV PYTHONUNBUFFERED=1

     WORKDIR /app

     COPY requirements.txt .

     RUN pip install --upgrade pip
     RUN pip install -r requirements.txt

     COPY . .

     EXPOSE 8000

     CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
     ----------------------------------------------------------------
     Notes:
     - python environment is used as the application uses django
     - exposed port can be changed based on user needs
  2. 
