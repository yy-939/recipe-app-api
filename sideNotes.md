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
### 1.3 Project Setup
#### 1.3.1 Create Docker file
The **Dockerfile** is used to build our image, which contains a mini Linux Operating System with all the dependencies needed to run our project.  
The **Docker Compose** is a tool for defining and running multi-container Docker applications. It allows you to define an entire application stack, including services, networks, and volumes, in a single *docker-compose.yml* file. 

#### 1.3.2 Linting and Testing
**Linting** is used to ensure code is formatted correctly. It highlights issues like invalid tab spacing and line lengths.  
use **flake8**. *requirments.dev.txt*: requirements used during the development but not in actual application: only need to format code when developmenting. 

#### 1.3.3 Create Django Project
***docker-compose run --rm app sh -c "django-admin startproject app ."***  
-> auto add Django Project to the root of the project  
  
***docker-compose up***  
-> start the project at: 127.0.0.1:8000/localhost:8000
  
***ctrl+c***  
-> end the project  
