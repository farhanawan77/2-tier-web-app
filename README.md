# Building docker images and deploying docker container on AWS

![architecture](https://user-images.githubusercontent.com/44845754/218274105-fb48c2d9-f04d-4258-87e2-c3db0eafa0ac.png)


This projects consists in:
- A Flask web application that connects to a MySQL database and performs various operations such as adding and retrieving employees information from the database. 
- A Terraform code that provisions an EC2 instance with the latest Amazon Linux AMI, adds an SSH key to the EC2 instance, creates a security group that allows SSH and certain inbound traffic, creates an elastic IP, and creates ECR repositories.
- A GitHub Actions that build, test, tag, and push images to Amazon ECR. This workflow is triggered by any push to the main branch. 


## Running the application 

1. Terraform
- Clone this repo
- Create a SSH key named A1-dev
- Run Terraform (plan, apply)

2. GitHub Actions
- The Terraform code will create the ERC webapp and db. Make a pull request to the main branch and trigger the GitHub Actions. This will push the images to the ECR.

3. EC2
- Connect to the EC2 instance
- Update yum and install docker
- Insert your AWS credentials, if needed.
- Login to ECR
- Pull both images (db and webapp) to your local environment. 

4. Running the Containers
- Create a custom bridge network and use any name you want.
- Run bd (MySql) container.
- Use the following variables:
  ```
  export DBHOST=(your container IP)
  export DBPORT=3306
  export DBUSER=root
  export DATABASE=employees
  export DBPWD=your password)
  ```
  Example:
  ```
  docker run -d -e MYSQL_ROOT_PASSWORD=123 --network A1-network 123456789.dkr.ecr.us-east-1.amazonaws.com/db:latest         
  ```
- Run the webapp containers and change the variable APP_COLOR to see a different background
  Exemple:
  ```
  docker run --name blue -p 8081:8080 -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e  DBUSER=$DBUSER -e DBPWD=$DBPWD -e APP_COLOR=blue --network A1-network 12345678.dkr.ecr.us-east-1.amazonaws.com/webapp:latest
  ```
  
  Ran as many webapps containers you want, just remember to change the ports (the Terraform code already opens the connection to 8081, 8082 and 8083).
