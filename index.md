This is Kubernetes Book

This is Book on Kubernetes that will help you understand Kubernetes and become comfortable running Kubernetes applications.

A few key technologies such as Django and Next.js have been used in this Book because I really think these technologies will be helpful to you as they are to me.

Django is a framework made by Adrian Holovaty and Simon Willison that helps us create restful api's in .Python Programming Language.
Next.js is a framework made by Vercel Team that helps us in writing Reactjs based Frontend.

In case you understand Django and Next.js, Great you are good to go. If not, it is important to understand else you will be able to follow along with the Book leading to Gaps in your Kubernetes's knowledge.

For Django you can watch Mosh Hamedani's Django Course. It is paid but well worth the money.
Link: https://www.youtube.com/watch?v=rHux0gMZ3Eg

For Next.js you can start from these tutorials created by Vercel Team. They are free and a great resource to learn Next.js.
Link: https://nextjs.org/learn/basics/create-nextjs-app

I really think that Django is the best framework for making backends and Next.js is the best framework for making frontends, and will be really helpful to you.

From now onwards I will be assuming you know these technologies and you also understand Docker and programming in general.
Now let us learn Kubernetes.

---

So what is Kubernetes?
Kubernetes is a technology made by Google, that helps manage and orchestrate our applications running in containers.
It is like the glue that links our applications running in Containers.

For example:
So you have created a Software as a Service product whose name is 'vareniyam'. It is a web application and you want the following delieverables.

- It's website should be hosted on 'vareniyam.cloud' domain. The website will be running in a Next.js server.

- The website also has a Backend Server running Django which be accessible over path starting with 'backend/'. So 'vareniyam.cloud/backend/', 'vareniyam.cloud/backend/auth/sign-in/' ,'vareniyam.cloud/backend/projects/' etc will be routed to the Backend Server running Django.

- The documentation of the website should be hosted at 'docs.vareniyam.cloud'.

- You also want SSL certificates to get 🔒 icon on your website.

- You want a CI/CD pipeline such that when you commit on master branch of github. Your application automatically get's redeployed and latest version takes effect.

With Kubernetes and it's ecosystem you can exactly get these delieverables. By the time you have finished this book you will know exactly how to do it.

Mesos and Borg

---

Before understanding Kubernetes you need to understand the following Terms:

## LoadBalancer

A single server can handle a limited amount of traffic. It you want to go beyond that limit you will need to use what is called a LoadBalancer.

LoadBalancer is made of two terms "Load" + "Balancer". The idea that is being communicated here is that a Load Balancer is used to balance the traffic on server.

![](/images/load-balancer.png)

As you can see in the above image whenever a load balancer recieves traffic from Internet It distributes that traffic between a number of servers.
There are multiple Load Balancer available such as Nginx and Apache Server.
The most popular load balancer is Nginx at the time of writing.

## Pod

![](/images/pod.jpg)

As you can see in the image the flower petals are being held together by a Pod.

Similary In kubernetes the containers are being held and ran together by pod.

If we want to run a docker container, we run it in a Pod. A pod is what holds and run our containers. Also a pod can have multiple containers inside of it.

Here is a diagram of a container running inside a pod

![](/images/pod-container.png)

## Replicas

We can have multiple pods in a kubernetes cluster.

So for example, if in kubernetes if we say we want 3 replicas of a given Pod. Kubernetes will create and run 3 Pods for us.

The number of replicas to choose depends on your workload, higher the workload the higher the number of replicas.

## Kubernetes Object

Kubernetes objects are persistent entities in the Kubernetes system. Kubernetes uses these entities to represent the state of your cluster.

Example of some Kubernetes Objects are "Deployment", "Service", "Config"

A kubernetes object can be represented in JSON or YAML format and has the following properties

- apiVersion - Which version of the Kubernetes API you're using to create this object.
- kind - What kind of object you want to create
- metadata - Data that helps uniquely identify the object, including a name string.

- spec - What state you desire for the object

We will be making many Kubernetes Objects. So they will become clear to you.
Here is an Example Kubernetes Object

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-depl
spec:
  replicas: 1
```

## Deployment

A deployment is a description that answers the following questions:

- What is the name of your pod and what containers should be run in that pod?
- How many such Pods you want?

Here is an example of a deployment Kubernetes Object:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-depl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: chetan1111/any3
```

In this deployment we are saying we want to create a deployment object named 'frontend-depl'.

It should run 1 replica of a pod and the pod should be running a container named 'frontend' with an image called 'chetan1111/any3'.

## Node

A node is a virtual machine running our Pods. A node can run many pods inside it.

Here is a diagram of a Node

![](/images/node.png)

## Services

Services helps our pods communicate between themeselves or recieve traffic from outside the cluster (say Internet).

There are 3 types of Services in Kubernetes made to support different use cases:
ClusterIp Service
NodePort Service
LoadBalancer Service

Say you have the following Cluster in which you have 2 pods running your Frontend Next.js Application and 2 pods running your Backend Django Application.
Let us say your frontend wants to reach the backend via rest api. How will it reach it?
![](/images/pods.png)

In order for the frontend pod to reach the backend pod you can expose the Backend Pod via a ClusterIP Service. Now Next.js will make a rest api call to the cluster IP service and that request will be routed to one of the pods.
![](/images/pods-2.png)

Below is an example of a ClusterIP Service Yaml file.

```
apiVersion: v1
kind: Service
metadata:
  name: backend-srv
spec:
  selector:
    app: backend
  ports:
    - name: backend
      protocol: TCP
      port: 8000
      targetPort: 8000
```

Here we are creating a ClusterIP Service named 'backend-srv' which is saying If I recieve traffic on port 8000 I will be redirecting traffic to 'backend' pods on port 8000.

After creating this service we will be able to make calls from backend pods to frontend pods via a freindly url such as "http://backend-srv:8000/backend".

Lastly a ClusterIP Service is used to expose the traffic within the cluster. Making a ClusterIP Service does not expose the pod outside the cluster (eg: Internet). ClusterIP Service is the right and the default way to expose traffic withing a cluster.

---

There is another service called NodePort Service that helps get traffic directly into a pod.
![](/images/pod-3.png)
As you can see in the diagram that a User is sending traffic to NodePort Service which is then sending the traffic to Pod.

Example of creating a NodePort Service

Cluster IP

http://192.165.1.1:30163/sfefe

```yaml
kind: Service
apiVersion: v1
metadata:
  name: backend-srv
spec:
  type: NodePort
  selector:
    app: backend
  ports:
    - nodePort: 30163
      port: 8000
      targetPort: 8000
```

Use NodePort service only for debugging purposes.

LoadBalancer Service:
The service becomes accessible externally through a cloud provider's load balancer functionality. GCP, AWS offer this functionality. The cloud provider will assign an IP address to the service.

Example of creating a LoadBalancer Service

```yaml
kind: Service
apiVersion: v1
metadata:
  name: backend-srv
spec:
  type: LoadBalancer
  selector:
    app: backend
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: web
```

---

Ingress Controller:
An Ingress Controller is not actually a service but has smart routing rules to direct traffic.

For example you can say that if the incomming traffic for website "vareniyam.cloud" starts with 'backend/' it should be send to backend service otherwise it should be send to frontend service.

Example of creating a Ingress Controller

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-service
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  rules:
      vareniyam.cloud
    - host: "vareniyam.cloud"
      http:
        paths:


          - path: /backend
            pathType: Prefix
            backend:
              service:
                name: backend-srv
                port:
                  number: 8000


          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-srv
                port:
                  number: 3000
```

There are many Ingress Controller available we will be using "kubernetes.github.io/ingress-nginx" controller. As it is the most popular ingress controller.

# Kubernetes ConfigMap

# Kubernetes Secrets

Kubernetes Secrets are used to store sensitive information such as your DATABASE_USER, DATABASE_PASSWORD, JWT_SECRET_KEY. Secrets can be created via JSON files but usually created via commands for security reasons.

You can create a secret by running

```
kubectl create secret generic secretname --from-literal=SECRET_ACCESS_TOKEN='YOUR_SECRET_ACCESS_TOKEN' --from-literal=SECRET_REFRESH_TOKEN='YOUR_SECRET_REFRESH_TOKEN'
```

You can delete a secret by running

```
kubectl delete secret secretname
```

---

Below is an example of a Cluster

<!-- todo add -->

![](/images/example-cluster.png)

In this Cluster we are exposing our application via an Ingress Controller. The ingress controller is routing traffic to ClusterIP service. The Cluster IP Service is then routing the traffic to the pods. The pods gets the traffic into the container.

---

Now starts the Practical section. Throughout the rest of the book we will do the following things:

1. Create "vareniyam.cloud" app and run it locally.
2. Deploy it on Google Cloud
3. GET SSL certificates
4. Create a CI/CD Pipeline

The application that we will be making is called "vareniyam". The pupose on Kubernetes so we will keep this application simple.
The frontend of the application will be available at 'vareniyam.cloud'. It will be a Next.js application. The backend of the application will be available at 'vareniyam.cloud/backend/'. It will be a Django application. The Documentaion of the website will be available at 'docs.vareniyam.cloud'. It will be powered by a technology called Docusuarus which is made by Facebook to make documentation websites.

To keep our work consolidated create a folder called "/Knowledge/Courses/Kubernetes/" in your home folder. Now open the folder in your Code Editor.

Now we will create the Documentaion website and run it in a container.

1. Run the following commands in root folder to skaffold a starter project for our documentation website.

```sh
npx create-docusaurus@latest docs classic --typescript
```

2. Now go to docs directory and in 'package.json' Delete the "start" and "serve" script.
   Add the following scripts

```
"dev": "docusaurus start --port 5000 --host 0.0.0.0",
"serve": "docusaurus serve --port 5000 --host 0.0.0.0"
```

What the script says is that it runs the application on port 5000 rather than the default 3000 port.

3. Now Dockerize the app. Create a file called Dockerfile.dev. We are calling it Dockerfile.dev because this docker container it is for development purposes. Later we will a Dockerfile for production as well.
   Now write the following contents in the 'Dockerfile.dev'

```
FROM node:16-alpine

WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
CMD npm run dev
```

4. To test it build run the container using the following command

```
docker build -f Dockerfile.dev -t docs .
docker run -p 5000:5000 docs
```

Now visit http://localhost:5000/ to see your application running.

5. Close the application by running CTRL+C.

---

Now we will create the Frontend website and run it in a container.

1. Run the following commands in root folder to skaffold a starter project for our frontend website. When prompted for app name write 'frontend'.

```
npx create-next-app@latest --ts
```

3. Now Dockerize the app. Create a file called Dockerfile.dev. and write the following contents in the 'Dockerfile.dev'

```
FROM node:16-alpine

WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
CMD npm run dev
```

4. To test it build run the container using the following command

```
docker build -f Dockerfile.dev -t frontend .
docker run -p 3000:3000 frontend
```

Now visit http://localhost:3000/ to see your application running.

5. Close the application by running CTRL+C.

---

Now we will create the Backend website and run it in a container.

1. Run the following commands in root folder to skaffold a starter project for our django api.

```
django-admin startproject config
```

Rename 'config' to 'backend'

```
mv config/ backend/
```

2. Create a package.json in backend folder. Add the following contents to it

```json
{
  "version": "1.0.11",
  "scripts": {
    "dev": "python3 manage.py runserver 0.0.0.0:8000",
    "test": "python3 manage.py test",
    "shell": "python3 manage.py shell",
    "db:makemigrations": "python3 manage.py makemigrations && python3 manage.py makemigrations backend",
    "db:migrate": "python3 manage.py migrate && python3 manage.py migrate backend",
    "db:delete:": "rm -rf db.sqlite3"
  }
}
```

These commands will be frequently used in the project so it is a good idea to write them as scripts.

3. Create a file called requirements.txt and add following contents to it.

```
Django==4.0.3
wheel==0.37.1
gunicorn==19.10.0
psycopg2-binary==2.9.3
```

3. Now Dockerize the app. Create a file called Dockerfile.dev. and write the following contents in the 'Dockerfile.dev'

```
FROM python:3.9
ENV PYTHONBUFFERED 1

RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt-get install -y nodejs

COPY requirements.txt .
RUN python -m pip install -r requirements.txt

RUN mkdir app
WORKDIR /app
COPY . /app

CMD npm run db:migrate && npm run dev
```

4. To test it build run the container using the following command

```
docker build -f Dockerfile.dev -t backend .
docker run -p 8000:8000 backend
```

Now visit http://localhost:8000/ to see your application running.

5. Close the application by running CTRL+C.

---

As a best practice, add the following files to ignore files which should be ignored.

docs/.gitignore
docs/.dockerignore

```
# Dependencies
/node_modules

# Production
/build

# Generated files
.docusaurus
.cache-loader

# Misc
.DS_Store
.env.local
.env.development.local
.env.test.local
.env.production.local

npm-debug.log*
yarn-debug.log*
yarn-error.log*
```

frontend/.gitignore
frontend/.dockerignore

```
# See https://help.github.com/articles/ignoring-files/ for more about ignoring files.

# dependencies
/node_modules
/.pnp
.pnp.js

# testing
/coverage

# next.js
/.next/
/out/

# production
/build

# misc
.DS_Store
*.pem

# debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnpm-debug.log*

# local env files
.env*.local

# vercel
.vercel

# typescript
*.tsbuildinfo
```

backend/.gitignore
backend/.dockerignore

```
# source:  https://djangowaves.com/tips-tricks/gitignore-for-a-django-project/
# Django #
*.log
*.pot
*.pyc
__pycache__
db.sqlite3
media

# Backup files #
*.bak

# If you are using PyCharm #
# User-specific stuff
.idea/**/workspace.xml
.idea/**/tasks.xml
.idea/**/usage.statistics.xml
.idea/**/dictionaries
.idea/**/shelf

# AWS User-specific
.idea/**/aws.xml

# Generated files
.idea/**/contentModel.xml

# Sensitive or high-churn files
.idea/**/dataSources/
.idea/**/dataSources.ids
.idea/**/dataSources.local.xml
.idea/**/sqlDataSources.xml
.idea/**/dynamic.xml
.idea/**/uiDesigner.xml
.idea/**/dbnavigator.xml

# Gradle
.idea/**/gradle.xml
.idea/**/libraries

# File-based project format
*.iws

# IntelliJ
out/

# JIRA plugin
atlassian-ide-plugin.xml

# Python #
*.py[cod]
*$py.class

# Distribution / packaging
.Python build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg
*.manifest
*.spec

# Installer logs
pip-log.txt
pip-delete-this-directory.txt

# Unit test / coverage reports
htmlcov/
.tox/
.coverage
.coverage.*
.cache
.pytest_cache/
nosetests.xml
coverage.xml
*.cover
.hypothesis/

# Jupyter Notebook
.ipynb_checkpoints

# pyenv
.python-version

# celery
celerybeat-schedule.*

# SageMath parsed files
*.sage.py

# Environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

# mkdocs documentation
/site

# mypy
.mypy_cache/

# Sublime Text #
*.tmlanguage.cache
*.tmPreferences.cache
*.stTheme.cache
*.sublime-workspace
*.sublime-project

# sftp configuration file
sftp-config.json

# Package control specific files Package
Control.last-run
Control.ca-list
Control.ca-bundle
Control.system-ca-bundle
GitHub.sublime-settings

# Visual Studio Code #
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
.history


*.DS_Store
**/__pycache__
**/.DS_Store
cloud_sql_proxy
```

---

Now we have successfully created the containers. It is time to run the application locally in a Kubernetes Cluster.

Follow the following steps to get things up and running.

1. Install the following Tools from their respective urls.
   kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
   Minikube: https://minikube.sigs.k8s.io/docs/start/
   Skaffold: https://skaffold.dev/docs/install/

kubectl will enable us admister our cluster.
Minikuber will enable us create a cluster
Skaffold will enable us run kubernetes in a development environment. It is made by google and enables hot relead in Kubernetes applications.

2. Verify their installation by running these commands

```
kubectl  --help
minikube --help
skaffold --help
```

Their output should be similar to the output in the following picture.
![](/images/install-verify.png)

3. Now install ingress-nginx. ingress-nginx is the Ingress Controller we will be using in our application for smart routing rules.

```
minikube addons enable ingress
```

---

Now our goal is to run Kubernetes Cluster Locally. Follow below steps to do it.

1. Create a folder called k8s/app in the root of your directory.
   Add the following deployment files.
   docs-depl.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: docs-depl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: docs
  template:
    metadata:
      labels:
        app: docs
    spec:
      containers:
        - name: docs
          image: chetan1111/any1
---
apiVersion: v1
kind: Service
metadata:
  name: docs-srv
spec:
  selector:
    app: docs
  ports:
    - name: docs
      protocol: TCP
      port: 5000
      targetPort: 5000
```

Here we are creating a deployment for our documentation app. We are specifying we need 1 pod running 1 container with image 'chetan1111/any1'.
'chetan1111/any1' is a simple docker image I made with the following Dockerfile.

```
FROM busybox
CMD echo "Hello World!"
```

We can choose any image we want here as later Skaffold will swap the image when we run the cluster.

We are also exposing our pod via a ClusterIP Service (It is the default service) in our cluster.

frontend-depl.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-depl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: chetan1111/any3
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-srv
spec:
  selector:
    app: frontend
  ports:
    - name: frontend
      protocol: TCP
      port: 3000
      targetPort: 3000
```

Similar to docs deployment, above file deploys the frontend application in the cluster.

backend-depl.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-depl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: chetan1111/any2
---
apiVersion: v1
kind: Service
metadata:
  name: backend-srv
spec:
  selector:
    app: backend
  ports:
    - name: backend
      protocol: TCP
      port: 8000
      targetPort: 8000
```

Similar to docs deployment, above file deploys the backend application in the cluster.

2. Inside k8s/app/ create 'ingress-srv.yaml' filw with the following contents

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-service
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  rules:
    - host: "docs.vareniyam.dev"
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: docs-srv
                port:
                  number: 5000

    - host: "vareniyam.dev"
      http:
        paths:
          - path: /backend
            pathType: Prefix
            backend:
              service:
                name: backend-srv
                port:
                  number: 8000
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-srv
                port:
                  number: 3000
```

In the above file we are creating an ingress controller with the following routing rules:
If traffic comes to 'docs.vareniyam.dev' route it to 'docs-srv' on port 5000.
If traffic comes to 'vareniyam.dev/backend' route it to 'backend-srv' on port 8000.
If traffic comes to 'vareniyam.dev' route it to 'frontend-srv' on port 3000.

3. Skaffold is a tool that helps us in our development process. So if we make a change to a typescript file in you frontend folder. It will be picked up skaffold and it will put in a container running our application, causing a hot reload. Giving a smooth developer experience.

To set up skaffold create a file called skaffold.yaml in the root directory
with following contents. Read the comments to understand it.

```yaml
apiVersion: skaffold/v2beta25
kind: Config
deploy:
  kubectl:
    manifests:
      # this shows where our kubernetes files our located
      - ./k8s/app/*

build:
  local:
    push: false
  artifacts:
    # in the docs directory build image from Dockerfile.dev and watch typescript and css files.
    - image: chetan1111/any1
      context: docs
      docker:
        dockerfile: Dockerfile.dev
      sync:
        infer:
          - "**/*.ts"
          - "**/*.tsx"
          - "**/*.css"

    # in the backend directory build image from Dockerfile.dev and watch python files.
    - image: chetan1111/any2
      context: backend
      docker:
        dockerfile: Dockerfile.dev
      sync:
        infer:
          - "**/*.py"

    # in the frontend directory build image from Dockerfile.dev and watch typescript and css files.
    - image: chetan1111/any3
      context: frontend
      docker:
        dockerfile: Dockerfile.dev
      sync:
        infer:
          - "**/*.ts"
          - "**/*.tsx"
          - "**/*.css"
```

4. Run the minikube cluster by running the following command

```
minikube start
```

5. Now, If you run your cluster with 'skaffold dev'. Your cluster will start running. In case you see some error messages. Kill the process by pressing Ctrl + C and run 'skaffold dev' again.

6. Get the minikube ip address by running 'minikube ip' if you visit this ip you will be greeted with an unfriendly message similar to this.

![](/images/404.png)

This is because for our cluster to work we need to set up our routing rules, We need to trick our system to redirect vareniyam.dev to our minikube cluster's ip address. To do so, first know your minikuber cliuster ip by running 'minikube ip'. Note the IP and put it somewhere temporarily.

If you are on mac/linux open /etc/hosts, if you are on windows open C:\Windows\System32\Drivers\etc\hosts.

In your 'hosts' at the end of file enter the following content. Replace '192.168.49.2' with the ip you got by running 'minikube ip'

```
192.168.49.2 vareniyam.dev
192.168.49.2 docs.vareniyam.dev
```

Save the file and restart your system.
If you visit 'vareniyam.dev' you should be routed to the minikube cluster and see the following page

![](/images/welcome-to-nextjs.png)

```
Note: I am using Ubuntu OS. In my case etc/hosts did not work as expected. So I have to troubleshoot it by googling and I came across this answer that solved my problem. https://askubuntu.com/questions/347152/why-is-the-etc-hosts-file-not-working


In case in you also face such problem.
Google `etc hosts not working ubuntu/windows/mac/`
```

7. Visit
   'vareniyam.dev' you will be routed to Next.js. Now make a edit to '/frontend/pages/index.tsx'. your changes should be reflected immediately.

---

Now our goal is to Deploy it on the Cloud with SSL certificates.
DNS:
When you visit a website such as 'stackoverflow.com'. What happens is your browser sends a request To DNS server which responds with an IP address. Then your browser visits that IP Address

DNS Host
Given that you purchased a domain name 'vareniyam.cloud'.
A Host value of '@' refers to 'vareniyam.cloud'.

A Host value of 'blog' refers to 'blog.vareniyam.cloud'.
A Host value of 'docs' refers to 'docs.vareniyam.cloud'.
A Host value of 'www' refers to 'www.vareniyam.cloud'.
Here 'blog.vareniyam.cloud', 'docs.vareniyam.cloud' and 'www.vareniyam.cloud' are called subdomains of 'vareniyam.cloud'. Also 'www' is the most common subdomain on Internet.

A Host value of '\*' signifies wildcard subdomain. A wildcard subdomain can refer to infinite subdomains. For example 'a.vareniyam.cloud','b.vareniyam.cloud', 'c.vareniyam.cloud', 'any.vareniyam.cloud', 'any.any.vareniyam.cloud'.

DNS Record:
DNS Records are of different types such as A Record, CNAME Records, MX Record which are used for different purposes. The most coomonly used record are A record and CNAME records.

An A record is used to map a host to an IP address. For example a host name of '@' mapped to '34.100.208.226'.

An CNAME record is used to map a host to another host. For example a host name of 'www' mapped to 'vareniyam.cloud.'. The CNAME record's full form is canonical name and it must alwasys end with a dot('.') .

TTL:
Time To Live. TTL means for how many seconds should the Doman Name Server cache the record. If you use a lower TTL(say 1 minute). The changes you make to DNS will be reflected fast but it will also increase the load on server. A higher TTL(say 1 day) will lead to caching of DNS records for longer time.

When you are actively developing use a lower TTL (say 1 minute) but in production use a higher TTL such as 1 day to reduce load on Domain Name Server.

GeoDNS:
GeoDNS is used to map a user to the IP address of the server located closest to him.
So given that we have 2 servers located in Mumbai(in India) and Warsaw(in Poland). In case if a user from Bangalore(India) sends a request for the IP address to Domain Name Server. He will given the IP address of Mumbia Server. If a user from Zurich(Switzerland) sends a request to the Domain Name Server. He weill be sent the IP address of Warsaw Server. GeoDNS routing is widely used by content delivery networks to bring their content closer to end users.
GeoDNS is a great solution to solve problem of latency for users accross the world.

AnyCast
Anycast is a network addressing and routing methodology in which a single destination IP address is shared by devices (generally servers) in multiple locations (Source: Wikipedia)
In simple words it means an AnyCast IP can reperent a server located bot in Mumbia and Warsaw at the same time. It's purpose is similar to GeoDNS that is to bring their content closer to users.

A Good question is should I use AnyCast or GeoDNS as their pupose is similatr that is to bring their content closer to users?
GeoDNS is simpler to implement, so use GeoDNS for sake of simplicity.

Ephemeral IP:
The word "Ephemeral" means "lasting for a very short time."

When you create a Virtual Machine in GCP. You get assigned what's called an Emphereal IP Address. An Ephemeral IP Address is subject to change and is volatile in nature. For example if you delete a VM Instance and create a new the Ephemeral IP Address of the old instance will be lost forever.

Static IP:

A static IP address is an IP address that remains static and is reserved by you. So, if you create a VM and assign it a static IP address and then delete the VM. You will not lose the IP address it will remain with you. When you are deploying your application in Production you should always use a static IP address.

A Static IP can be of two type a global IP or a regional IP. The differnence between them is that a gloabl IP address can be used as an AnyCast IP address whereas a regional IP address cannot be used for that purpose.

Domain Name Registrar:
Domain Name Registrar are companies that sell domain names and gives us an ability to manage DNS records. There are many registrars available in the market such as GoDaddy, Namecheap, Google Domains etc.
Note that if you search on youtube for 'godaddy scam' you will find many people you who are condemening godaddy for it's unfair buisness policy. Believing in the wisdon of crowd you should absolutely refrain from using Godaddy.

Big Companies such as Google and Amazon are often critized for their Vendor Lockins, in which they make it easy to onboard their service but harder to user their competitor service. So you should be cautious of using their Domain Name Services also. If you

In this book I will be using Namecheap because they are popular for Domain Name Registrar's and I have personally used them and it worked well.

---

We will be deploying our application on the cloud.
3 popular clouds are Google Cloud Platform, Amazon Web Services and Azure Cloud.

I am not familiar with Azure, AWS is very hard and inconvinient to use. So we will be using Google Cloud Platform.

Google Cloud gives $300 credit for new users but you need to have a debit/credit card.

Now Moving forward you will need to pay for Google Cloud and Domain Name.
For Google Cloud if you are using $300 credit you are covered under that credit.
For Domain name you will need to pay atleast $1.18.

So at minimum you will need to pay $1.18

Now we will purchase Domain Name.
Some of the cheapest domain names are '.xyz', '.cloud', '.me'. They usually cost around 1-3 dollars.
To purchase domain name go to namecheap.com and choose a domain name like your name or your dream company name and checkout it they will ask for your card details and email etc to create an account. It is usually a straightforward process.
Namecheap

Now go https://cloud.google.com/ to create an account you will need a debit/credit card.

---

Deploying on Google Cloud.

1. Moving forward we will be using these api's. So enable these api's.

https://console.cloud.google.com/marketplace/product/google/artifactregistry.googleapis.com
https://console.cloud.google.com/marketplace/product/google/dns.googleapis.com
https://console.cloud.google.com/marketplace/product/google/container.googleapis.com
https://console.cloud.google.com/marketplace/product/google-cloud-platform/cloud-sql
https://console.cloud.google.com/marketplace/product/google-cloud-platform/compute-engine

<!-- 2. We need to connect our Django application to an SQL Database. We will be using Cloud SQL. Go To GCloud > SQL press Create Instance. Choose Postgress when prompted for databse.

Add the following configuration for SQL Database.
Note for 'password' field use a differnet password from https://djecrety.ir/ and store it in a file.

```
InstanceID: vareniyam
Password: z+fie0vo(r7#88=n(=+tbtwjizdlt(tdo6ita)!bx!*p1-^c%3 # (GET FROM https://djecrety.ir/ and store it securely)
Regios: asia-south-1
Zone Availability: Single Zone

Customize your instance:
Machine Type: Shared Core 1vcpu 0.614 GB RAM
Storage: SSD 10GB
```

3. Wait for Database to get provisioned it will take 10 to 15 minutes.

4. Once database has been provisioned click on the instance and create a Database user with following configuration.

```
username: 32I1M338DsfGgRY4 # (GET FROM https://passwordsgenerator.net/, Select only Include Numbers, Include Lowercase Characters, Include Uppercase Characters, and of length 16. Also store it securely)
password: z+fie0vo(r7#88=n(=+tbtwjizdlt(tdo6ita)!bx!*p1-^c%3 # (GET FROM https://djecrety.ir/ ans store it securely.)
```

5. Create a Database with name 'vareniyam'. -->

6. Go to GCloud > Kubernetes Engine and create a Kubernetes cluster with following settings.

```
Type: GKE Standard
Name: vareniyam
Location type: zonal
zone: asia-south1-a
```

7. Go to GCloud > Networking Services > Cloud DNS and Create a zone with following settings. Replace vareniyam.cloud with your domain name.

```
Zone Type: Public
Zone Name: vareniyam
DNS Name: vareniyam.cloud
DNSSEC: Off
Cloud Logging: Off
```

8. Click on Zone and click on Registrar Setup Button located on top right side.

9. Go to namecheap and add those nameservers. What that essentially does is give Google Cloud control over managing your DNS records. Namecheap says it take upto 48 hrs for propogation but that is the max limit. Usually it is done within 1 hour.

10. Create a regional IP address by running

```
gcloud compute addresses create vareniyam-ip --region asia-south1
```

11. Know your create IP address by running

```
gcloud compute addresses describe vareniyam-ip --region asia-south1
```

12. In Cloud DNS add these records.

Replace vareniyam.cloud with your domain name and '' with IP address you got in previous step. In GCP we represent '@' with an empty string. So in place of @ use an empty string.

```
Type          Host        Value
A             @           34.100.208.226
CNAME         www         vareniyam.cloud.
A             docs        34.100.208.226
```

13. Install the Gcloud by using this link
    Link: https://cloud.google.com/sdk/docs/install

Verify Installation by running 'gcloud'

14. Login into your account by running 'gcloud auth login'

15. Now get into the Kubernetes Cluster you made by running

```
gcloud container clusters get-credentials vareniyam --zone asia-south1-a --project vareniyam
```

16. Google Cloud will be hosting our docker images. Configure Docker to use gcloud to host our images:

```
gcloud auth configure-docker
```

17. Now build the images and push to google cloud.

```
cd docs
docker build -f Dockerfile.dev --tag gcr.io/vareniyam/docs .
docker push gcr.io/vareniyam/docs
```

```
cd frontend
docker build -f Dockerfile.dev --tag gcr.io/vareniyam/frontend .
docker push gcr.io/vareniyam/frontend
```

```
cd backend
docker build -f Dockerfile.dev --tag gcr.io/vareniyam/backend .
docker push gcr.io/vareniyam/backend
```

Verify that they have been push successfully by going to gcloud > Container Registry. You should see them listed there

18. Go to k8s/app/ and replace the image with their gcr images equivilant.

```
chetan1111/any1 => gcr.io/vareniyam/docs
chetan1111/any3 => gcr.io/vareniyam/frontend
chetan1111/any2 => gcr.io/vareniyam/backend
```

This will break skaffold we will fix skaffold later.

18.2 Go to k8s/app/ingress-srv.yaml and replace the vareniyam.dev and \*.vareniyam.dev with domain name you purchased.

19. Helm is the package manager for Kubernetes. We will use it to install ingress-nginx in our Kubernetes Cluster.

It you are from windows you can think of helm as chocolatey.
It you are from mac you can think of helm as brew.
It you are from linux you can think of helm as snap.

To install helm go to https://helm.sh/docs/intro/install/

Verify your installion by running 'helm --help'

20. Install ingress-nginx in your Google Kubernetes Cluster by running following Command. Replace 34.100.208.226 with your IP address obtained earlier.

```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm upgrade --install ingress-nginx-chart ingress-nginx/ingress-nginx --set controller.service.loadBalancerIP=34.100.208.226
```

Verify that the IP address was applied successfully by Running. You should see your IP address listed there.

```
kubectl get ingress
```

21. Apply the yaml files by running 'kubectl apply -f k8s/app'

Run 'kubectl get pods' to know status of your pods
Wait for your Pods to get go from 'ContainerCreating' stage to 'Running.'

22. Visit the domain you purchased you should see the website up and running.

Now we will get an SSL certificate for our website.
SSL certificate is used to encrypt traffic between server and client. So no third party can understand the traffic.

Let's Encrypt is an organization that gives free SSL certificates. Let's Encrypt are valid for 90 days, after which they needs to be renewed. To get an SSL certificate we need to prove Let's Encrypt that we own the domain. For that we need to solve an http01 or dns01 challenge.

Use http01 challenge for non wildcard domains such as 'vareniyam.cloud', 'docs.vareniyam.cloud', 'blog.vareniyam.cloud'.

Use dns01 challenge for wildcard domains such as '\*.vareniyam.cloud'.

Now we have 2 options.
We can get an SSL certificate for 'vareniyam.cloud', 'www.vareniyam.cloud', 'docs.vareniyam.cloud'
or
We can get an SSL certificate for 'vareniyam.cloud' and '\*.vareniyam.cloud'.

We will be going with second option as if we later need to add another website such as 'blog.vareniyam.cloud' the SSL certficate will just work for it.

cert-manager helps us get SSL certificate in a Kubernetes cluster. We will be using cert-manager to get SSL certificate.

We are using dns01 challenge so cert-manager will need access to our Google Cloud Account to make necessary DNS changes.

1. Create Service Account by going to https://console.cloud.google.com/iam-admin/serviceaccounts with the permission of Owner. This gives full access to all resources in Gcloud. Keep it securely.

2. Rename the file to account.json and bring it in Kubernetes folder. Also add the file to .gitignore to prevent commiting it to Github.

3. Create Kubernetes secret named 'secrets' by running

```
kubectl create secret generic secrets --from-file=./account.json
```

4. Create k8s/certificates/certificates.yaml file with following contents. Replace vareniyam.cloud with your domain name.

Here we are basically instructing cert-manager to get SSL certificate from letsencrypt for 'vareniyam.cloud' and '\*.vareniyam.cloud' and telling that the Google Cloud Service Account is located in a secret named 'secrets' with key 'account.json'

```yaml
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: chetansirsa@gmail.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
      - dns01:
          cloudDNS:
            project: vareniyam
            serviceAccountSecretRef:
              name: secrets
              key: account.json
        selector:
          dnsNames:
            - "*.vareniyam.cloud"
      - http01:
          ingress:
            class: nginx
        selector:
          dnsNames:
            - "vareniyam.cloud"
```

5. Go to k8s/app/ingress-srv.yaml
   In the spec object add

```
tls:
  - hosts:
      - "vareniyam.cloud"
      - "*.vareniyam.cloud"
    secretName: ssl-certificate
```

and in the metadata.annotations object add

```
cert-manager.io/issuer: "letsencrypt-prod"
```

Final file should look like this

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-service
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/issuer: "letsencrypt-prod"
spec:
  rules:
    - host: "docs.vareniyam.cloud"
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: docs-srv
                port:
                  number: 5000

    - host: "vareniyam.cloud"
      http:
        paths:
          - path: /backend
            pathType: Prefix
            backend:
              service:
                name: backend-srv
                port:
                  number: 8000
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-srv
                port:
                  number: 3000
  tls:
    - hosts:
        - "vareniyam.cloud"
        - "*.vareniyam.cloud"
      secretName: ssl-certificate
```

6. Install cert-manager in your Google Kubernetes Cluster by running

```
helm repo add jetstack https://charts.jetstack.io
helm repo update
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.0/cert-manager.crds.yaml
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.8.0
```

7. Now apply our changes

```
kubectl apply -f k8s/certificates
kubectl apply -f k8s/app
```

8. Inspect the status of certificate by running`

```
kubectl get certificates
kubectl describe certificates ssl-certificate
```

Once the status changes to Ready: True. Go to you domain name and you should see a Lock Icon meaning you have got an SSL certificate.

Earlier we broke skaffold. Fix skaffold by going to k8s/app/ and replace the gcr images with these images.

```
gcr.io/vareniyam/docs       =>     chetan1111/any1
gcr.io/vareniyam/frontend   =>     chetan1111/any3
gcr.io/vareniyam/backend    =>     chetan1111/any2
```

9. Go to k8s/app/ingress-srv.yaml and replace your domain name with vareniyam.dev and \*.vareniyam.dev appropriately.

---

![todo](/images/dns-flow.png)

Now our goal is to get our application ready for production and set up a CI/CD pipeline by following these steps:

1. Containerize our application and make them ready for production.
2. Use Github Actions to redeploy on commit.

# Contanerizing apps

Create 'Dockerfile' in /docs with following Contents

```
FROM node:16-alpine

WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
RUN npm run build
CMD npm run serve
```

Create 'Dockerfile' in /frontend with following Contents
This Dockerfile is a complex to understand and I myself not able to understand what is happening here but this is what I got from Next.js themselves told in this article'https://nextjs.org/docs/deployment'.
It's ok if this file sounds confusing to you.

```
# Install dependencies only when needed
FROM node:16-alpine AS deps
# Check https://github.com/nodejs/docker-node/tree/b4117f9333da4138b03a546ec926ef50a31506c3#nodealpine to understand why libc6-compat might be needed.
RUN apk add --no-cache libc6-compat
WORKDIR /app

# If using npm with a `package-lock.json` comment out above and use below instead
COPY package.json .
RUN npm install
# RUN npm ci

# Rebuild the source code only when needed
FROM node:16-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Next.js collects completely anonymous telemetry data about general usage.
# Learn more here: https://nextjs.org/telemetry
# Uncomment the following line in case you want to disable telemetry during the build.
# ENV NEXT_TELEMETRY_DISABLED 1

RUN npm run build

# If using npm comment out above and use below instead
# RUN npm run build

# Production image, copy all the files and run next
FROM node:16-alpine AS runner
WORKDIR /app

ENV NODE_ENV production
# Uncomment the following line in case you want to disable telemetry during runtime.
# ENV NEXT_TELEMETRY_DISABLED 1

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# You only need to copy next.config.js if you are NOT using the default configuration
COPY --from=builder /app/next.config.js ./
COPY --from=builder /app/public ./public
COPY --from=builder /app/package.json ./package.json

# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000

CMD ["node", "server.js"]
```

Create 'Dockerfile' in /backend with following Contents

```
FROM python:3.9

COPY requirements.txt .
RUN python -m pip install -r requirements.txt

RUN mkdir app
WORKDIR /app
COPY . /app

CMD gunicorn --workers 3 -b 0.0.0.0:8000 config.wsgi
```

Now we have dockerized out application for production.

We will use Github Actions to deploy the application on commit master branch. Bascially we will give Github the access to our GCP Account. In Github Action we store sensitive information in Github Secrets. Which we eill use to store our GCP credentials.

Prerequisites:
Create a Github Repository with master branch as the default branch and push this folder to Github. Keep the Repository Private.

Steps:

1. Go to Github Settings > Secrets > Actions and Create three secrets.

Use your GKE_EMAIL which is located in account.json file.
Your GKE_KEY is your service account encoded as base64 which you can get by running "cat account.json | base64"

```
base64". Only copy the characters.
GKE_PROJECT="vareniyam"
GKE_EMAIL="owner-709@vareniyam.iam.gserviceaccount.com"
GKE_KEY="JodHRwczov...L3d3dy5n"
```

2. Got to Actions Tab > New Workflow > set up a workflow yourself.

Replace the contents with following contents. Read the comments to understand it.

```yaml
name: Build and Deploy to GKE

# Run this whenever there is commit on master branch
on:
  push:
    branches:
      - master

# Setup the enviroment variables
env:
  GKE_PROJECT: ${{ secrets.GKE_PROJECT }}
  GKE_CLUSTER_NAME: vareniyam
  GKE_ZONE: asia-south1-a
  GKE_EMAIL: ${{ secrets.GKE_EMAIL }}
  GKE_KEY: ${{ secrets.GKE_KEY }}
  GITHUB_SHA: ${{ github.sha }}
  # Needed for authentication to work
  ACTIONS_ALLOW_UNSECURE_COMMANDS: "true"

jobs:
  setup-build-publish-deploy:
    name: Setup, Build, Publish, and Deploy
    runs-on: ubuntu-latest
    environment: production

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      # Create the Image variables Names as the will be used frequently
      - name: Set Images as enviroment variables
        run: |
          echo "DOCS=$(echo "gcr.io/""$GKE_PROJECT""/docs:""$GITHUB_SHA")" >> $GITHUB_ENV
          echo "FRONTEND=$(echo "gcr.io/""$GKE_PROJECT""/frontend:""$GITHUB_SHA")" >> $GITHUB_ENV
          echo "BACKEND=$(echo "gcr.io/""$GKE_PROJECT""/backend:""$GITHUB_SHA")" >> $GITHUB_ENV

      # Replacing the images
      - name: update images
        run: |
          DOCS_ESCAPE=$(printf '%s\n' "$DOCS" | sed -e 's/[\/&]/\\&/g')
          FRONTEND_ESCAPE=$(printf '%s\n' "$FRONTEND" | sed -e 's/[\/&]/\\&/g')
          BACKEND_ESCAPE=$(printf '%s\n' "$BACKEND" | sed -e 's/[\/&]/\\&/g')
          sed -i  's/chetan1111\/omkar-app:1.0.12/'"$DOCS"'/g' docs-depl.yaml
          sed -i -e 's/chetan1111\/frontend:1.0.4/'"$FRONTEND_ESCAPE"'/g' frontend-depl.yaml
          sed -i -e 's/chetan1111\/backend:1.0.5/'"$BACKEND_ESCAPE"'/g' backend-depl.yaml
        working-directory: k8s/app

      # Use the correct domain names for production.
      - name: sed hosts
        run: |
          sed -i 's/vareniyam.dev/vareniyam.cloud/g' app/ingress-srv.yaml certificates/certificates.yaml
          sed -i 's/docs.vareniyam.dev/docs.vareniyam.cloud/g' app/ingress-srv.yaml certificates/certificates.yaml

        working-directory: k8s/

      # Get GCloud credentials
      - uses: google-github-actions/setup-gcloud@94337306dda8180d967a56932ceb4ddcf01edae7
        with:
          version: "290.0.1"
          service_account_email: ${{ env.GKE_EMAIL }}
          service_account_key: ${{ env.GKE_KEY }}
          project_id: ${{ env.GKE_PROJECT }}

      # This makes docker uses Google Cloud Container Registry to push images to.
      - name: Configure Docker
        run: gcloud --quiet auth configure-docker

      # Get access to Kubernetes Cluster
      - uses: google-github-actions/get-gke-credentials@fb08709ba27618c31c09e014e1d8364b02e5042e
        with:
          cluster_name: ${{ env.GKE_CLUSTER_NAME }}
          location: ${{ env.GKE_ZONE }}
          credentials: ${{ env.GKE_KEY }}

      # Build and push Images to Docker Hub
      - run: docker build --tag "$DOCS" .
        working-directory: docs

      - run: docker build --tag "$FRONTEND" .
        working-directory: frontend

      - run: docker build --tag "$BACKEND" .
        working-directory: backend

      - name: Push Images
        run: |
          docker push "$DOCS"
          docker push "$FRONTEND"
          docker push "$BACKEND"

      # Deploy to Google Cloud
      - name: Deploy
        run: |
          gcloud container clusters get-credentials $GKE_CLUSTER_NAME --zone $GKE_ZONE --project $GKE_PROJECT
          kubectl apply --recursive -f k8s/
          kubectl get pods
```

3. Commit it

4. Locally pull the changes

```
git pull
```

5. Make a change in 'frontend/pages/index.tsx' and push it your changes should be reflected automatically.

---

Faq:
Q: How to connect Postgresql to Django in Kubernetes?
Follow this tutorial
Link: https://cloud.google.com/python/django/kubernetes-engine

Q: When I restart my PC how should I run my local cluster?
Bascially you need to run 'minikuber start' and then 'skaffold dev'.
To streamline this create package.json in root folder with following contents

```json
{
  "scripts": {
    "pre:dev": "minikube start",
    "dev": "skaffold dev",
    "stop": "minikube stop"
  }
}
```

So whenever you need to start run local cluster run
npm run pre:dev && npm run dev

---

This brings us to end of the course. I Congratulate you for reaching the end of this course. Hopefully, you have understood Kubernetes now.

Great gratitude to my Guru Stephen Grinder who taught me what I have taught you.

धन्यवाद॥
