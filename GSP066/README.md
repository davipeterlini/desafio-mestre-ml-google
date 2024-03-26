# Awwvision: Cloud Vision API from a Kubernetes Cluster

## Dados
username: student-01-250573a09643@qwiklabs.net
password: JEbKQ6g4nuWh
project ID: qwiklabs-gcp-04-aad3e281454d

### Verificar a conta 
```shell script
gcloud auth list
```

<!-- 
ACTIVE: *
ACCOUNT: student-01-a7e6a48d69a8@qwiklabs.net

To set the active account, run:
    $ gcloud config set account `ACCOUNT` 
-->

### Verificar a conta 
```shell script
gcloud config list project
```

<!-- 
[core]
project = qwiklabs-gcp-04-48c2e2ff0dbd 
-->

# Task 1. Create a Kubernetes Engine cluster
<!-- 
In this lab you will use gcloud, Google Cloud's command-line tool, to set up a Kubernetes Engine cluster. You can specify as many nodes as you want, but you need at least one. The cloud platform scope is used to allow access to the Pub/Sub and Vision APIs.

Click Check my progress to verify your performed task. If you have successfully created a Kubernetes cluster, you will see an assessment score.

-->

### In Cloud Shell, run the following to create a cluster in the Zone zone:
```shell script
gcloud config set compute/zone us-central1-f
```

### Then start up the cluster by running:
```shell script
gcloud container clusters create awwvision \
    --num-nodes 2 \
    --scopes cloud-platform
```

### Run the following to use the container's credentials:
```shell script
gcloud container clusters get-credentials awwvision
```

### Verify that everything is working using the kubectl command-line tool:
```shell script
kubectl cluster-info
```

# Task 2. Create a virtual environment
<!-- 
Python virtual environments are used to isolate package installation from the system.
-->

### Install the virtualenv environment:
```shell script
sudo apt-get install -y virtualenv
```

### Build the virtual environment:
```shell script
python3 -m venv venv
```

### Activate the virtual environment
```shell script
source venv/bin/activate
```


# Task 3. Get the sample

### Add sample data to your project by running:
```shell script
gsutil -m cp -r gs://spls/gsp066/cloud-vision .
```


# Task 4. Deploy the sample

<!-- 
As part of the process, Docker images will be built and uploaded to the Google Container Registry private container registry.

In addition, yaml files will be generated from templates, filled in with information specific to your project, and used to deploy the redis, webapp, and worker Kubernetes resources for the lab. 
-->

### In Cloud Shell, change to the python/awwvision directory in the cloned cloud-vision repo:
```shell script
cd cloud-vision/python/awwvision
```

### Once in the awwvision directory, run make all to build and deploy everything:
```shell script
make all
```

# Task 5. Check the Kubernetes resources on the cluster

<!--  
After you've deployed, check that the Kubernetes resources are up and running.
-->

### First, list the pods by running
```shell script
kubectl get pods
```

### Next, list the deployments by running:
```shell script
kubectl get deployments -o wide
```

### Once deployed, get the external IP address of the webapp service by running:
```shell script
kubectl get svc awwvision-webapp
```

# Task 6. Visit your new webapp and start its crawler

<!-- 
Copy and paste the external IP of the awwvision-webapp service into a new browser to open the webapp, then click Start the Crawler button.

Next, click go back and you should start to see images from the /r/aww subreddit classified by the labels provided by the Vision API. You will see some of the images classified multiple times, when multiple labels are detected for them. (You can reload in a bit, in case you brought up the page before the crawler was finished).

Your results will look something like this:
-->

# Task 7. Test your understanding

<!-- 
Below is a multiple choice question to reinforce your understanding of this lab's concepts. Answer it to the best of your abilities
-->
Cloud ML

# Congratulations!
<!-- 
You used Kubernetes and Cloud Vision API to classify images from Reddit's /r/aww subreddit and displayed the results in a web app.

Google Cloud training and certification
...helps you make the most of Google Cloud technologies. Our classes include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. Certifications help you validate and prove your skill and expertise in Google Cloud technologies.

Manual Last Updated February 14, 2024

Lab Last Tested October 09, 2023

Copyright 2024 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated. 
-->