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
  2. Create a .dockerignore. Input the following:
     ----------------------------------------------------------------
     .env
     venv/
     Dockerfile
     .DS_Store
     ----------------------------------------------------------------
     Notes:
     - a virtual environment was used to develop the app so for safety and future proofing, venv files are ignored
  3. Pull the Docker Image into your local machine
     ----------------------------------------------------------------
     docker pull marvinjaeestrella/iscs30.23-xw_project_group_9
  4. Create Kubernetes Manifests by creating kubernetes-manifests in the project directory. These .yaml files were created in this folder with these in each file:
     ----------------------------------------------------------------
     deployment.yaml

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: my-app
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: my-app
        template:
          metadata:
            labels:
              app: my-app
          spec:
            containers:
            - name: my-app-container
              image: marvinjaeestrella/iscs30.23-xw_project_group_9
              ports:
              - containerPort: 80

     service.yaml

      apiVersion: v1
      kind: Service
      metadata:
        name: my-app-service
      spec:
        selector:
          app: my-app
        ports:
        - protocol: TCP
          port: 80
          targetPort: 80
        type: NodePort

     ingress.yaml

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: my-app-ingress
        annotations:
          kubernetes.io/ingress.class: "nginx"
      spec:
        rules:
        - host: localhost
          http:
            paths:
            - path: /
              pathType: Prefix
              backend:
                service:
                  name: my-app-service
                  port:
                    number: 80

     hpa.yaml

      apiVersion: autoscaling/v2
      kind: HorizontalPodAutoscaler
      metadata:
        name: my-app-hpa
      spec:
        scaleTargetRef:
          apiVersion: apps/v1
          kind: Deployment
          name: my-app
        minReplicas: 1
        maxReplicas: 5
        metrics:
        - type: Resource
          resource:
            name: cpu
            target:
              type: Utilization
              averageUtilization: 50

      5. Deploy the Manifests by navigating to kubernetes-manifests folder and input the following:
      ----------------------------------------------------------------
        kubectl apply -f . 

      6. Verify Deployment by check the status of the pods, services, and other resources through inputting the following:
      ----------------------------------------------------------------
        kubectl get pods
        kubectl get svc
        kubectl get ingress
