# **MailDev Setup Guide**

This guide walks you through setting up [Maildev](https://github.com/maildev/maildev) using Docker. Maildev is a simple email server and web interface for testing and development purposes. It allows you to capture and view emails sent from your application during development.

### **Prerequisites**
Before starting, ensure you have the following installed on your system:
- [Docker](https://www.docker.com/get-started) (version 20.10 or later recommended)

## **Step 1: Pull the Maildev Image from Docker Hub**

Open your terminal and pull the official Maildev image from Docker Hub.

```bash
docker pull maildev/maildev
```
## **Step 2: Create Dockerfile**
If you want more control over the setup or need a custom version, you can create your own Dockerfile. This step is optional if you're fine with using the default image.
```dockerfile
# Base image with Node.js
FROM node:18-alpine

# Set environment to production
ENV NODE_ENV production

# Set working directory
WORKDIR /root

# Copy and install dependencies
COPY package*.json ./
RUN npm install \
  && npm prune \
  && npm cache clean --force

# Set the user and working directory for Maildev
USER node
WORKDIR /home/node

# Copy source files and set correct permissions
COPY --chown=node:node . /home/node
COPY --chown=node:node --from=build /root/node_modules /home/node/node_modules

# Expose Maildev's web and SMTP ports
EXPOSE 1080 1025

# Environment variables for Maildev
ENV MAILDEV_WEB_PORT 1080
ENV MAILDEV_SMTP_PORT 1025

# Command to start Maildev
ENTRYPOINT ["bin/maildev"]

```

## **Step 3: Run the Maildev Container**
Now, run the container with the following command. This will bind the Maildev web interface to port 1080 and the SMTP server to port 1025 on your local machine.
```bash
$ docker run -p "1080:1080" -p "1025:1025" maildev/maildev
```
## **Step 4: Access the Web Interface**
Once the container is running, open your browser and navigate to http://localhost:1080. You should see the Maildev web interface, where you can view incoming emails.

## **Step 5: Configure Your Application to Send Emails to Maildev**
In your application's email configuration, set the SMTP server to localhost and the port to 1025. This directs outgoing emails from your app to Maildev for testing.

### **Troubleshooting Tips**

- **Port Conflicts:** If port 1080 or 1025 is already in use, you can specify alternative ports by modifying the -p options when running the container:
```bash
docker run -p "8080:1080" -p "2025:1025" maildev/maildev
```
Then access the web interface at http://localhost:8080.

- **Restarting the Container:** If you stop the container and need to start it again, you can list and restart the container using Docker's CLI commands:

```bash
docker ps -a        # Lists all containers
docker start <container-id>  # Restarts the stopped container
```

## **Step 6: Stop and Clean Up**
When you're done using Maildev, you can stop and remove the container to free up resources:
```bash
docker stop <container-id>   # Stops the running container
docker rm <container-id>     # Removes the container
```
To remove the image from your system if no longer needed:
```bash
docker rmi maildev/maildev
```
