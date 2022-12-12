 # Agile Development with Azure: Build, Test, Deploy and Operationalize a Machine Learning Project
## Introduction

In this project, I will build a Github repository from scratch and create a scaffolding that will assist me in performing both Continuous Integration and Continuous Delivery. I'll use Github Actions along with a Makefile, requirements.txt and application code to perform an initial lint, test, and install cycle. Next, I'll integrate this project with Azure Pipelines to enable Continuous Delivery to Azure App Service.


## Project Plan

### Trello board

https://trello.com/b/xxZta7Ds/building-a-ci-cd-pipeline

### Spreadsheet Project Plan

https://docs.google.com/spreadsheets/d/1y6JtaH0gPq7kVTwbCoZKk3wia-XyDDzLTqvos0Is5NY/edit?usp=sharing


## CI: Set Up Azure Cloud Shell

### : Creating the Cloud-Based Development Environment

:white_check_mark: Setup a Github repository

:white_check_mark: Launch an Azure Cloud Shell environment and create the ssh-keys. Upload these keys to your GitHub account.

**Steps:**

- Go to Azure Portal and  Click Azure Cloud Shell
- To generate a key, type the following
```
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub
```

![ssh keygen](./images/sshkeygen.png)

- Copy the generated key and go to GitHub. Click the settings and paste the key.

**Clone the Repository**

Then repository from the Azure Shell can be cloned.
```
git clone https://github.com/theomotola/Agile-Development-with-Azure.git
```

![GitHub Setting](./images/gitclone.png)

**Create the Makefile**

Create a file named Makefile and copy the below code into it. Makefile is a great way to create shortcuts to build, test, and deploy a project.

```makefile
install:
    pip install --upgrade pip &&\
        pip install -r requirements.txt

test:
    python -m pytest -vv test_hello.py

lint:
    pylint --disable=R,C hello.py

all: install lint test
```

**Create requirements.txt**

Create a file named requirements.txt. This is a convenient way to include what packages needed by the project. 

```
pylint
pytest
```

**Create the Python Virtual Environment**
This can be done both locally and inside your Azure Cloud Shell environment. Creating the virtual environment in a home directory avoids accidental checks into the project.

```bash
pip install virtualenv
virtualenv ~/.udacity-devops
source ~/.udacity-devops/bin/activate
```

I  created the `.udacity-devops` virtual environment in my mac, so I will simply activate it. You can check whether which python are we using by tying `which python` in your terminal.

```bash
source ~/.udacity-devops/bin/activate
```

![activate env](./images/activateenv.png)

**Create the script file and test file.**

The next step is to create the script file and test file. This is a boilerplate code to get the initial continuous integration process working. It will later be replaced by the real application code.

First, you will need to create [hello.py](http://hello.py/) with the following code at the top level of your Github repo:

```python
def toyou(x):
    return "hi %s" % x

def add(x):
    return x + 1

def subtract(x):
    return x - 1
```

Next, you will need to create test_hello.py with the following code at the top level of your Github repo:

```python
from hello import toyou, add, subtract

def setup_function(function):
    print("Running Setup: %s" % function.__name__)
    function.x = 10

def teardown_function(function):
    print("Running Teardown: %s" % function.__name__)
    del function.x

### Run to see failed test
#def test_hello_add():
#    assert add(test_hello_add.x) == 12

def test_hello_subtract():
    assert subtract(test_hello_subtract.x) == 9
```

### Local Test

Now it is time to run `make all` which will install, lint, and test code. This enables us to ensure we don't check in broken code to GitHub as it installs, lints, and tests the code in one command. Later we will have a remote build server perform the same step.

What is important to keep in mind is that we need to test our code locally first before we clone it to the Azure Cloud Shell. So I will install all the packages and test whether I can run the app.py application and make housing prediction successfully in my local machine first. 

![make all file](./images/makeall.png)

After running my tests locally, I want to run my python web application. Once it is successfully, you will see Sklearn Prediction Home in your browser.

```python
Python app.py
```

![local host browser](./images/localhosting.png)

Then, we want to make sure whether we call call our ML API. Open another terminal and type `./make_prediction.sh` in our terminal. We will be able to see the prediction.

![machine learning](./images/makepredict.png)


### Replace yml code
```yaml
name: Python application test with Github Actions

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
    - name: Install dependencies
      run: |
        make install
    - name: Lint with pylint
      run: |
        make lint
    - name: Test with pytest
      run: |
        make test
```
![install](./images/installpytesting.png)
![lint build test](./images/appconfig2.png)
![clear run](./images/appconfig.png)

## Continuous Delivery on Azure

This part will involve setting up Azure Pipelines to deploy the Flask starter code to Azure App Services. After we have enabled the source control integration, we can select the Azure Pipelines to build provider, and then configure the App Services permissions.
### Load Test with locust

```
locust
```

## 1. Authorize Azure App Service

Azure App Service is like our localhost but it is hosted in Azure. It is like a black-box localhost. Azure APP service is PaaS so we do not need to set up and maintain the Virtual Machines.It is easy to use. 

To start with, we need to authorize Azure APP Service. You can create a APP Service from Azure Portal or in the Azure Cloud Shell. I will use the Portal here since it is more clear and easy for me to understand. Alternatively, you can use the script below to set up a Azure App Service in the Azure Cloud Shell.

```bash
az webapp up -n <your-appservice>az webapp config set -g <your-resource-group> -n <your-appservice> --startup-file <your-startup-file-or-command>
```

## 2. Enable Continuous Deployment with Azure Pipelines

Then we want to use Azure pipelines to deploy our flask ML web application. Create an Azure DevOps Project and then set a service connection for Azure Pipelines and Azure App Service first. 

Next, the Flask ML Web Application is deployed successful with Azure Pipelines. 

![successdeploywithazurepipeline](./images/azpipelinedeploy.png)

Go to the App Service, and click the URL under the Essentials , we should be able to visit the website now. 

![app service](./images/appauthorize.png)


## 3. Stream Logs**

Here is the output of streamed log files from deployed application.

https://flaskmlapp.scm.azurewebsites.net/api/logs/docker 

View the log file in App Service - Log Stream
![logfiles](./images/logfile.png)



## Demo

https://www.youtube.com/watch?v=p5jgTdOToW0


