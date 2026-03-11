
# Bank Application Deployment using Docker

This guide explains how to build and run the **Spring Boot Bank Application with MySQL using Docker**.

---

# 1. Clone the Project

Clone the repository:

git clone https://github.com/jaiswaladi246/Github-Actions-Project.git

Go to the project folder:

cd Github-Actions-Project

Check files:

ls

You should see:

Dockerfile  
pom.xml  
src  
kubernetes  
mvnw  

---

# 2. Install Java 17 on Ubuntu

Update package list:

sudo apt update

Install Java 17:

sudo apt install openjdk-17-jdk -y

---

# 3. Verify Java Installation

Check Java version:

java -version

Expected output:

openjdk version "17"

Check Java compiler:

javac -version

Expected output:

javac 17

---

# 4. Set Java 17 as Default

Run:

sudo update-alternatives --config java

Example output:

There are 2 choices for java

1 /usr/lib/jvm/java-11-openjdk-amd64/bin/java  
2 /usr/lib/jvm/java-17-openjdk-amd64/bin/java  

Select:
2

Configure javac:
sudo update-alternatives --config javac

Select Java 17 again.

---

# 5. Set JAVA_HOME

Set JAVA_HOME environment variable:
echo "export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64" >> ~/.bashrc
echo "export PATH=\$JAVA_HOME/bin:\$PATH" >> ~/.bashrc

Reload shell:
source ~/.bashrc

Verify:
echo \$JAVA_HOME

Expected output:
/usr/lib/jvm/java-17-openjdk-amd64

---

# 6. Build the Java Application

This project is a Spring Boot Maven project.

Build the JAR file:

./mvnw clean package


After the build completes you will see:

target/bankapp.jar

---

# 7. Prepare Dockerfile Build Context

Your Dockerfile expects:

app/*.jar

Create folder:
mkdir app

Copy the JAR file:
cp target/*.jar app/

Check:
ls app

Expected output:
bankapp.jar

---

# 8. Build Docker Image

Build the Docker image:
docker build -t bankapp .

Check image:
docker images

Example output:
bankapp        latest

---

# 9. Create Docker Network

Create a network for communication between containers:
docker network create bankapp-network

---

# 10. Run MySQL Container

Start MySQL container:

docker run -d \
--name mysql \
--network bankapp-network \
-e MYSQL_ROOT_PASSWORD=Test@123 \
-e MYSQL_DATABASE=bankappdb \
-p 3306:3306 \
mysql:8

Check running containers:
docker ps

---

# 11. Run BankApp Container

Run the Spring Boot container:

docker run -d \
--name bankapp \
--network bankapp-network \
-p 8080:8080 \
-e SPRING_DATASOURCE_URL="jdbc:mysql://mysql:3306/bankappdb?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true" \
-e SPRING_DATASOURCE_USERNAME=root \
-e SPRING_DATASOURCE_PASSWORD=Test@123 \
bankapp

Check running containers:
docker ps

---

# 12. Check Application Logs

If the application fails to start, check logs:
docker logs bankapp

---

# 13. Access the Application

Open browser:
http://<server-ip>:8080

Example:
http://localhost:8080

---

# 14. Stop Containers

Stop containers:
docker stop bankapp
docker stop mysql

Remove containers:
docker rm bankapp
docker rm mysql

---

# Application Architecture

Spring Boot Application → MySQL Database → Docker Network
