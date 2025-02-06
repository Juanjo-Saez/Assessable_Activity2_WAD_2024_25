# Assessable_Activity2_WAD_2024_25 Juanjo Sáez

In order to evaluate the correct deployment of both methods and to facilitate the location of the links, i will share them here.

[EC2 AWS vite-server instance](http://3.210.19.233/)

[adivina-el-numero-s3 Juanjo Saez](http://juanjo-demo-s3.s3-website-us-east-1.amazonaws.com)

[Juanjo-Saez github repository](https://github.com/Juanjo-Saez/Assessable_Activity2_WAD_2024_25/edit/main/README.md)

[Video tutorial of the process](https://youtu.be/Lb2jnoI0Pao)

## EC2 Instance

For this activity, we have to create an EC2 Instance on Amazon Web Services (AWS) with an elastic IP address that runs the latest version of Ubuntu Server and install all the necessary packages to have a web server with nodejs and npm and all the additional tools required to deploy the adivina-el-numero React project with Vite

First of all, we create and EC2 Instance with Ubuntu Server in its lastest version. 
![imagen](https://github.com/user-attachments/assets/8df8e4e3-c1cd-4f52-af52-3da7f689b8f6)

As we can see in the image, that instance of ubuntu already have an elastic IP address. In order to do that, we have to first go to Network & Security and Elastic IPs, where we will be able to Allocate a new Elastic IP Address. Once it is allocated, we can associate that to our instance

![imagen](https://github.com/user-attachments/assets/b353dd93-556b-4655-ac6f-f7ad2c320da8)

After we initialized an Instance we then connect to that instance using the .pem we downloaded and the public IPv4 DNS with:

`ssh -i labsuser.pem ubuntu@ec2-3-210-19-233.compute-1.amazonaws.com`

This means that we are connected to our ubuntu machine through our windows cmd and facilitates the rest of the actions, making it easier to copy paste any command we need. Do not forget that with a clean Image of Ubuntu with the latest version, we need to prepare the enviroment. First of all, we have to update everything, because otherwise, we won't be able to install npm or nodejs So the first step is to:

`sudo apt-get update`

After this, remember that what we want is to deploy our react app, so we do need to clone the repository inside our Instance

`sudo git clone https://github.com/gisgarme/adivina-el-numero`

Now we need to make sure we have nodejs and npm for this project, so we install them with `sudo apt-get install nodejs npm` (in this case, showing we had already done so in this machine) 

![imagen](https://github.com/user-attachments/assets/b613336f-ffde-4152-9053-77f8a6019bed)

We will also need 

`sudo npm install -g pm2 express`

Our requirement is "The web server has to run on port 80" so we will create an inbound rule for that. At EC2 we make sure we are at the security group used for our insance

![imagen](https://github.com/user-attachments/assets/34c76cb6-5828-4935-b6ea-18b3d4f7140c)

and then edit the Inbound Rules to assert that port 80 is open for us. With this inbound rule, we do not depend of Apache and it is already running in port 80

![imagen](https://github.com/user-attachments/assets/2de729b5-8fe2-4730-9795-78cc64b2075d)

To run the project we need to execute the following line

`pm2 start npm --name "vite-server" -- run dev -- --host`

![imagen](https://github.com/user-attachments/assets/1448ae38-4e2c-40bd-b092-3e6e610b3398)

We can check that everything runs with a log

![imagen](https://github.com/user-attachments/assets/e6f7940f-07d7-436d-a77e-7c63955940fe)

And then check the running project at [EC2 AWS vite-server instance](http://3.210.19.233/)
The url will be the HTTP we chosed and then the public IPv4 address that we had associated before. With the inbound rule of port 80, we do not need to specify with :80 in the url that it is that port.

![imagen](https://github.com/user-attachments/assets/451b6699-3ae1-4f75-a424-e54a02975eee)

There is a strong argument to deploy it with pm2 instead of Vite, in order to improve our independency of dev servers. I have tried to do that by going to dist directory and then serving the static files ensuring that it is deployed at port 80 with:

`sudo pm2 serve -- . 80`

![imagen](https://github.com/user-attachments/assets/a23063d0-750a-4e14-920d-b9d519084f6a)




## AWS S3 Static Website Deployment Guide  

### 1. Initial Preparation  
- **AWS Account**: Ensure you have an active AWS account.  

### 2. Access the S3 Service  
- In the AWS console, search for **"S3"** in the search bar and click on the service.

![imagen](https://github.com/user-attachments/assets/e3d8a8eb-f409-488c-8c6d-be35e6bf388d)
  

### 3. Create a Bucket  
- Click on the **"Create bucket"** button.  
- Configure the following parameters:  
  - **Bucket Name**: It must be unique (e.g., `my-name-demo-s3`).  
  - **Region**: Select the region closest to your location.  
- Keep the other configurations as default and click **"Create bucket."** 

![imagen](https://github.com/user-attachments/assets/8e229f3c-7461-49e0-85d3-c0a2b10dc86a)
 

### 4. Configure the Bucket for Static Website Hosting  
- Go to the **Properties** tab of the created bucket.  
- In the **Static website hosting** section, click **"Edit"** and enable the option.  
- Configure the following parameters:  
  - **Index document**: `index.html`  
- Save the changes.  

![imagen](https://github.com/user-attachments/assets/7cd6e189-bd46-4e6d-abbb-cb97cc5c7a9c)


### 5. Modify the Bucket's Permissions  
- Go to the **Permissions** tab.  
- In the **Block public access** section:  
  - Edit the configuration and disable the option that blocks public access.  
  - Confirm by typing **"confirm"** and save the changes.  

![imagen](https://github.com/user-attachments/assets/ac040d93-f60b-4d40-abdb-2608ebdf69f6)


- **Add a bucket policy**:  
  - Click **"Edit"** within the **Bucket policy** section.  
  - Use the **policy generator** to create a policy that allows public access to the bucket’s objects:  
    - **Service**: `S3`  
    - **Principal**: `*`  
    - **Actions**: `GetObject`  
    - **Amazon Resource Name (ARN)**: Copy the bucket's ARN from the settings and add `/*` at the end (e.g., `arn:aws:s3:::my-name-demo-s3/*`).  
  - Generate the policy, copy it, and paste it into the bucket’s policy section.  
  - Save the changes.  

![imagen](https://github.com/user-attachments/assets/df27c19a-445d-46da-9193-4da7c76709a3)

![imagen](https://github.com/user-attachments/assets/91968df0-1aea-454e-bed1-5806920b8457)


### 6. Upload the Application Files  
- Go to the **Objects** tab in the bucket.  
- Upload the files available in [this GitHub repository](https://github.com/gisgarme/adivina-el-numero-s3):  
  - Drag the files or manually select the objects.  
  - Click **"Upload"** and wait for the process to complete.  

![imagen](https://github.com/user-attachments/assets/974893b2-0a29-46c4-acc2-cc50ff79d05a)


### 7. Obtain the Website URL  
- Go to the **Properties** tab of the bucket.  
- In the **Static website hosting** section, copy the generated URL.  
- Open the URL in a browser to view your deployed React application.  

![imagen](https://github.com/user-attachments/assets/0a389dad-eb5f-4576-8c5d-464b0187c6a6)

[adivina-el-numero-s3 Juanjo Saez](http://juanjo-demo-s3.s3-website-us-east-1.amazonaws.com)

![imagen](https://github.com/user-attachments/assets/ab68841c-3f9c-4f99-9b53-ffaa6afc22e4)

 
