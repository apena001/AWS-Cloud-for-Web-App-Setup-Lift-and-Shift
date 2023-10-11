# AWS-Cloud-for-Web-App-Setup-Lift-and-Shift
## This project is to run application workload on AWS using lift and shift strategy

### First creat security group for Load Balancer

![Alt text](<images/Screenshot 2023-10-09 173243.png>)

### creating security group for Tomcat Instance will run on port 8080 and allow traffic only on Load Balancer and 22 from MyIp also 8080 from MyIP

![Alt text](<images/Screenshot 2023-10-09 173243111.png>)

### Creating another security group for all backend services(Mysql,Memcache,Rabbit MQ) runing on port 3306,11211 and 5672 respectivelty and allowing traffic from application server(Tomcat Instance)

![Alt text](<images/Screenshot 2023-10-09 175337222.png>)

### Creating the last security group in the backend service to allow services in the group interacte with each other and also allow runing on port 22 from My IP

![Alt text](<images/Screenshot 2023-10-09 175842333333.png>)

### Creating key pairs

### Next step is to lunch instance (CentOS) and copying the source code from the Mysql to the user data section in the advance details making sure the security group is backend

![Alt text](images/5555555.png)

![Alt text](images/666.png)

### To retrieve user data from within a running instance, use the following URI.

`http://169.254.169.254/latest/user-data`

### verifying database

`mysql -u admin -padmin123 accounts`

![Alt text](images/777.png)

`show tables`

![Alt text](images/888.png)

### Verifying memcahe server

`ss -tunlp | grep 11211`

![Alt text](images/memcha.png)

### Verifying RabbitMq Server

`systemctl status rabbitmq-server`

![Alt text](images/Rabbitmq.png)

### Verifying Tomcat9

`systemctl status tomcat9`

![Alt text](images/tomcat9.png)

### Creating host zone on Route 53 allocating all backend servers with their IP 

![Alt text](images/route.png)address

## Build and Deploy Artifact

### Before build the Artifact we will make change to the application properties in the source code for backend to be the same as the IP in Route 53

![Alt text](images/appli1.png)


![Alt text](images/appli2.png)

![Alt text](images/aplli3.png)

### Build Artifact

`mvn install`

![Alt text](images/arctifact.png)

### Pushimg the Artifact to s3 bucket, but we will need authantication thru iAm user
### creating iAM USER
![Alt text](images/fullaces.png)

### We will use the acess key to acess s3 bucket

`aws s3 mb s3://lift-shift-artifact`

![Alt text](images/makeatifact.png)

### Copying the artifact to the s3 bucket

`aws s3 cp target/vprofile-v2.war s3://lift-shift-artifact`

![Alt text](images/Upload.png)

![Alt text](images/up.png)

### Creating Roles and attached to EC2 instance(App01 server) instead of access keys, we are giving roles to the instance

![Alt text](images/roles.png)

### Login into the App01(Tomcat) instance

`apt update`

![Alt text](images/Aptupdate.png)

`apt install awscli -y`

![Alt text](images/awscli.png)

### Confirm awscli has been install

`aws s3 ls`

![Alt text](images/s333.png)

### Copy the artifact to tMP folder

` aws s3 cp s3://lift-shift-artifact/vprofile-v2.war /tmp`

![Alt text](images/temp.png)

### To deploy we have to stop the Tomcat9 service and remove the default application /var/lib/tomcat9/webapps/ROOT 

`systemctl stop tomcat9`

`rm -rf /var/lib/tomcat9/webapps/ROOT`

### Copy artifact from tmp/ to /var/lib/tomcat9/webapps/ROOT.war

`cp /tmp/vprofile-v2.war /var/lib/tomcat9/webapps/ROOT.war`

![Alt text](images/vali.png)

### Validation

`cat /var/lib/tomcat9/webapps/ROOT/WEB-INF/classes/application.properties`

![Alt text](images/vali222.png)

## Setting up a Load Balancer

### Crrating application Load Balancer from Target Group
### create target group and create the load balancer making sure to add your certificate

### Validate to make sure it is pointing to the endpoint of the load balancer and load balancer routed toward Tomcat, the page is coming from Tomcat 

![Alt text](images/web.png)
![Alt text](images/web2.png)

## Setting up Autoscaling group for application server(Tomcat ec2 instance)

![Alt text](images/AutoScaling.png)


![Alt text](<images/AWS Lift&Shift project.png>)

The End




















