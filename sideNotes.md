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
`docker-compose run --rm app sh -c "django-admin startproject app ."` 
-> auto add Django Project to the root of the project  

`docker-compose build`
-> build container (when you add new dep (e.g. modify requirements), need to rebuild)

`docker-compose up` 
-> start the project at: 127.0.0.1:8000/localhost:8000
  
`ctrl+c` 
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
- import APIClient (`from rest_framework.test import APIClient`)
- create client (`client = APIClient`)
- make requests (`res = client.get('/greetings/')`)
- check results

#### 1.3.2 Common Issues
**tests not running:**
- missing *__init__.py* in tests dir
- *identation* of test cases
- missing *test* prefix in method
**import error:**
- both tests/ dir & tests.py exist

### 1.4 Database
- Postgre SQL
- Docker Compose
  - reusable
  - persistent data with volumes
  - handles network config
  - environment variable config

&emsp;&emsp;**Docker Compose**  
**Database** (Postgre SQL)&emsp;<->&emsp;**App** (Django)

**Network connectivity**
```
services:
  app:
    depends_on:
      - db
  
  db:
    image: postgres:13-alpine
```
- set *depends_on* on *app* service to start *db* first
- Docker Compose creates a network
- The *app* service can use *db* hostname

**Volumes**
- persistent data
- map directory in container to local machine

**Psycopg2**
most popular PostgreSQL adaptor for Python

**Problem with Docker Compose: Fix database race condition**
- Using *depends_on* ensures **services** (e.g. your laptop) starts
  - Does not ensure **application** (e.g. Postgres) is running
- Solution
  - Make Django "wait for db"
    - check database availability
    - continue when database ready
  - Create custom Django management command

#### 1.4.2 Database Migration
**Django ORM**
- Object Relational Mapper (ORM)
- serves as a abstraction layer for data
  - Django handles database structure and changes
  - Focuse on Python code
  - Use any database (within reason)
**Using ORM**
Define Models -> Generate migration files -> Setup database -> Store data
**Models**
- Each Model maps to a table
- Models contains:
  - Name
  - Field
  - Other metadata
  - Custom Python logic
**Creating Migration**
- Ensure app is enabled in settings.py
- Use Django CLI
  - `python manage.py makemigrations`
**Applying Migration**
- Use Django CLI
  - `python manage.py migrate`
- run `wait for db` first, then run it 
  - ```
      command: >
      sh -c "python manage.py wait_for_db &&
             python manage.py migrate
    ```
  
### 1.5 Create Use Model
**How to customize user model**
- Create model
  - Base from `AbstractBaseUser` and `PermissionMixin`
- Create custom manager
  - Used for CLI integration
- Set `AUTH_USER_MODEL` in setting.py
- Create and Run migrations
**AbstractBaseUser**
- Provides features for authentication
- doesn't include fields
**PermissionMixin**
- Support for Django permission system
- Includes fields and methods
**Common Issues**
Running migrations **before** setting custom models

**Design User Models**
- User Fields
  - email (EmailField)
  - name (CharField)
  - is_active (BooleanField)
  - is_staff (BooleanField)
- User Model Manager
  - Used to manage objects
  - Custom logic for creating objects
    - hash password
  - Uased by Django CLI
    - create super user
- BaseUser Manager
  - Base class for managing users
  - useful helper methods
    - `normalize_email`: for storing emails consistently
  - methods we will define
    - `create_user`:called when create a user
    - `create_superuser`: used by CLI to create a superuser (admin)

`docker-compose run --rm app sh -c "python manage.py createsuperuser"`
-> create a admin user

### 1.6 Django Admin
- Graphical User Interface for models
  - Create, Read, Update, Delete
- Very little coding required
**How to enable Django admin**
- Enabled per model
- inside admin.py
  - admin.site.register(model_you_want_to_register)
**Customising**
- Create class based off `ModelAdmin` or `UserAdmin`
- Override/set class variables

### 2. API documentation
**Why document?**
- APIs are designed for developers to use
- Need to know how to use it
- an API is only as good as its documentation
**What to document?**
- Everything needed to use the API
- Available endpoints (paths)
  - /api/recipes
- Supported methods
  - GET, POST, PUT, PATH, DELETE
- Format of payloads (inputs)
  - parameters
  - POST json format
- Format of responses (outputs)
  - Response json format
- Authentication process
**Options for documentation**
- Manual
  - word doc
  - markdown
- **Automated**
  - Use metadata from code (comments)
  - Generate documentation pages

### 2.1 Auto docs with DRF
**Docs in DRF**
- Auto generate docs (with third party library)
  - library `drf-spectacular`
- Generates *schema* (document in format of json or YAML)
- Browsable web interface
  - Make test requests
  - Handle auth
**How it works?**
1. generate "**schema**" file (opening up in tools (e.g. Swagger) that can convert it into human readable docs)
2. parse schema into GUI
**Using a Schema**
- Download and run in local Swagger instance
- Serve Swagger with API






