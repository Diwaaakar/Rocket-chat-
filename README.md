# Rocekt chat 
http://52.41.91.26:3000/home
http://54.149.219.182:3000/channel/general




# AMI 
![image](https://user-images.githubusercontent.com/82823452/116425260-d5073e00-a84a-11eb-9029-dcf49db8c898.png)
# Snapshot 
![image](https://user-images.githubusercontent.com/82823452/116425388-ee0fef00-a84a-11eb-8a2a-7cabcad6404a.png)


# step 0
# Launch EC2 instance 
search for "Ubuntu Server 18.04 LTS" with "64-bit (x86)" architecture and click on "Select"then review and launch 
# Allocate an Elastic IP 
Click on "Allocate New Address"
Select "Amazon's pool of IPv4 addresses" and click on "Allocate"
Click on the newly created IP address and select "Associate Elastic IP address"
Select your instance and click "Associate"
In the details below, copy the "Public DNS". You will need it in the DNS step.
(Public DNS:ec2-52-41-91-26.us-west-2.compute.amazonaws.com)
# Configure DNS w/ AWS Route 53
Create a new hosted zone by clicking on "Create Hosted Zone":
Enter your domain name and select "Public Hosted Zone" as type, then click on "Create"
Select your newly created zone and click on "Create Record Set"
Domain name - guptacottage.com
# for getting an SSH 
ssh -i "rocketchat.pem" ubuntu@ec2-52-41-91-26.us-west-2.compute.amazonaws.com
# Install certbot using apt:
 sudo apt update
 sudo apt install certbot
# Obtain certificate from Let's Encrypt:
 sudo certbot certonly --standalone --email diwakargupta5@email.com -d guptacottage.com

# Steps for running the deployement 
# Go to secure shell 
![image](https://user-images.githubusercontent.com/82823452/116426912-34198280-a84c-11eb-89c6-b8f088943526.png)


# Rocket-chat-
rocket chat system 
# Step 1 – Create Atlantic.Net Cloud Server
apt-get update -y
 # Step 2 – Install Node.js
 curl -sL https://deb.nodesource.com/setup_12.x — Node.js 12 LTS "Erbium"| bash -
 apt-get update -y
apt-get install nodejs build-essential curl software-properties-common graphicsmagick -y
 # Step 3 – Install MongoDB
 apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
 # Next, add the MongoDB repository by running the following command:
 add-apt-repository 'deb [arch=amd64] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse'
 # Once the repository is added, update the repository and install MongoDB with the following command:
 apt-get update -y
apt-get install mongodb-org -y
# Once the MongoDB is installed, start the MongoDB service and enable it to start after system reboot:
systemctl start mongod
systemctl enable mongod
# By default, MongoDB listening on port 27017. You can check it with the following command:
netstat -ant
 # Step 4 – Configure MongoDB
echo -e "replication:\n replSetName: \"rs01\"" | tee -a /etc/mongod.conf
# Next, restart the MongoDB service to apply the changes:
systemctl restart mongod
 # Next, log in to MongoDB shell and initiate the replica set with the following command:
 mongo
rs.initiate()
# At this point, MongoDB is installed and configured with the replica set.
# Type Exit to leave MongoDB.
# Step 5 – Install Rocket.Chat
# First, create a new user with the name rocket with the following command:
adduser rocket
# Next, allow Nginx to access the Rocket.Chat directory by adding the rocket user to www-data group:
usermod -a -G rocket www-data
# Next, switch the user to rocket and download the latest version of Rocket.Chat with the following command:
su - rocket
curl -L https://releases.rocket.chat/latest/download -o rocket.chat.tgz
# Once the download is completed, extract the downloaded file with the following command:
tar zxf rocket.chat.tgz
# Next, move the extracted directory to Rocket.Chat using the following command:
mv bundle Rocket.Chat
 # Next, change the directory to Rocket.Chat/programs/server directory and install all required npm packages with the following command:
 cd Rocket.Chat/programs/server
npm install
# Next, set the required environment variables to test the Rocket.Chat installation:
export PORT=3000
export ROOT_URL=http://0.0.0.0:3000/
export MONGO_URL=mongodb://localhost:27017/rocketchat
export MONGO_OPLOG_URL=mongodb://localhost:27017/local?replSet=rs01
# Next, change the directory to Rocket.Chat and start the Rocket.Chat server using the following command:
cd ~/Rocket.Chat
node main.js
#  Next, stop the Rocket.Chat server with CTRL+C then run the following command to exit from the rocket user:
exit
# Step 6 – Create a Systemd Service File for Rocket.Chat
# Next, create a systemd service file to start and stop the Rocket.Chat service:

nano /etc/systemd/system/rocketchat.service

# Add the following lines:

[Unit]
Description=Rocket.Chat server
After=network.target nss-lookup.target mongod.target


[Service]
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=rocketchat
User=rocket
Environment=MONGO_URL=mongodb://localhost:27017/rocketchat MONGO_OPLOG_URL=mongodb://localhost:27017/local?replSet=rs01 ROOT_URL=http://node1.example.com PORT=3000
ExecStart=/usr/bin/nodejs /home/rocket/Rocket.Chat/main.js


[Install]
WantedBy=multi-user.target 
# Save and close the file when you are finished. Then, reload the systemd daemon with the following command:
systemctl daemon-reload
# Next, start the Rocket.Chat service and enable it to start after system reboot:
systemctl start rocketchat
systemctl enable rocketchat
# You can now verify the Rocket.Chat service using the following command:
systemctl status rocketchat
# Step 7 – Set Up a Reverse Proxy with Nginx
# Next, you will need to install and configure Nginx as a reverse proxy to forward the client requests coming on port 3000 to the Nginx port 80.
# First, install the Nginx web server with the following command:

apt-get install nginx -y
# Next, create an Nginx virtual host configuration file with the following command:

nano /etc/nginx/sites-available/rocket
# Add the following lines:

server {
  listen 80;

  server_name    node1.example.com;
  access_log /var/log/nginx/rocket-access.log;
  error_log /var/log/nginx/rocket-error.log;

  location / {
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://127.0.0.1:3000/;
  }
}
# Save and close the file when you are finished. Then, enable the virtual host with the following command:

ln -s /etc/nginx/sites-available/rocket /etc/nginx/sites-enabled/
Finally, restart the Nginx service to apply the configuration changes:

systemctl restart nginx
# Step 8 – Access Rocket.Chat Web Interface
# Rocket chat is ready to use after login and putting some personal informatio 
# qus 3 How to remove the deployment and cleanup all resources created for it
for removing the deployement , we can simply terminate the instance and disassociate the elastic ip and then release it. 

# For converting AMI to docker image, In secure shell type( command prompt, putty)
docker login
docker images
docker push diwaakar2205/rocket-chat:tagname
# for pulling it out 
docker pull diwaakar2205/rocket-chat:tagname



# Reference 
# https://docs.rocket.chat/installation/paas-deployments/aws
# https://atlantic.net/vps-hosting/how-to-deploy-rocket-chat-with-nginx-on-ubuntu-18-04/

