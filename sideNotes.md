# Recipe App Api
## 0. Overview
**Django**: 
- Python Web Framework
- Handles:
  - URL mappings
  - Object Relational mapper
  - Admin site
**Django REST Framework**: Django add-on
**Postgre SQL**: Database
**Docker**: 
- containerization software that allows us to run services for each our applications: we will run docker-sized service of our API & docer-sized services of our database
- create a development environment
- easily deploy our application to a server
**Swagger**: Automated documentation for API
**Github Actions**: handle Automation 
- Testing and Linting
**TDD(test driven development)**: write test first, then write code

## 1. SetUp
### 1.1 Project Setup
#### 1.1.1 Create Docker file
The **Dockerfile** is used to build our image, which contains a mini Linux Operating System with all the dependencies needed to run our project.  
The **Docker Compose** is a tool for defining and running multi-container Docker applications. It allows you to define an entire application stack, including services, networks, and volumes, in a single *docker-compose.yml* file. 

#### 1.1.2 Linting and Testing
**Linting** is used to ensure code is formatted correctly. It highlights issues like invalid tab spacing and line lengths.  
use **flake8**. *requirments.dev.txt*: requirements used during the development but not in actual application: only need to format code when developmenting. 

#### 1.1.3 Create Django Project
***docker-compose run --rm app sh -c "django-admin startproject app ."***  
-> auto add Django Project to the root of the project  
  
***docker-compose up***  
-> start the project at: 127.0.0.1:8000/localhost:8000
  
***ctrl+c***  
-> end the project  

### 1.2 Github Actions
- automation tool
- run jobs when code changes
- automate tasks
### common uses:
- handle developments
- code linting
- unit tests
### how it work:
**Trigger** (anything happends to your proj on Github) e.g. push trigger (push to github)
-> set up **Job** when Trigger is hit e.g. run unit tests
-> **Result** (successful/fail)

### how to config it
- create a config file at: .github/workflows/checks.yml:set triggers, set up unit tests
***docker-compose run --rm app sh -c "python manage.py test"***
-> run unit test for our project

- configure Docker Hub authentication
**access tokens**: a username and password that are set up specifically for automated tasks
using Docker Hub access token, create a temporary password for logging into Docker Hub  
-> go to Github project-Settings-Repository secrets, create a new repo secret using the temporary password

### 1.3 Test
***app/calc.py, app/tests.py***
Django Test Framework
- based on the *unittest* library
- Django adds features:
  - Test client - dummy web browser
  - simulate authentication
  - create a temporary database
Django REST Framework adds features
- API test client

**Where do you put your test?**  
- when create an app, Django automatically adds a **test.py** module which is a placeholder that you can add test
- OR, you can create **test/**  subdir to split tests up
- Keep in mind: 
  - only use test.py **or** test/, NOT BOTH
  - Test module must start with **test_**
  - Test dir must contain **__init__.py**

**Test Classes**
- SimpleTestCase
  - No databse integration
  - useful if no databse is requred for your test
  - save time executing tests
- **TestCase**
  - databse integration
  - useful to test code that uses database

**Writing Tests**
- import test classes (*from django.test import SimpleTestCase*)
- import object to test (e.g. *from app2 import views*)
- define test classes (e.g. *class ViewTests(SimpleTestCase):*)
- add test methods

#### 1.3.1 Mocking
- override or change behaviours of dependencies
- avoid unintended side effects
- isolate code being tested
**Why use mocking?**
- avoid relying on external services
  - can't guarantee they will be available
  - make tests unpredictable and inconsistent
- avoid unintended consequences
  - accidentally send an email
  - overloading external services
**How to mock code?**
use *unittest.mock*
- **MagicMock/Mock** - replace real objects
- **patch** - overrides code for tests
**Django REST framework APIClient**
- make requests
- check results
- override authentication
**Use APIClient**
- import APIClient (*from rest_framework.test import APIClient*)
- create client (*client = APIClient*)
- make requests (*res = client.get('/greetings/')*)
- check results

#### 1.3.2 Common Issues
**tests not running:**
- missing *__init__.py* in tests dir
- *identation* of test cases
- missing *test* prefix in method
**import error:**
- both tests/ dir & tests.py exist

  








