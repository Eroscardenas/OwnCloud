# MAILDEV

### Step 1: Maildev Setup

### 1. **Pull image fromk Dockerhub**

```
$ docker pull maildev/maildev
```

### 2. **Create Dockerfile**

```dockerfile
FROM node:18-alpine
ENV NODE_ENV production
WORKDIR /root
COPY package*.json ./
RUN npm install \
  && npm prune \
  && npm cache clean --force
USER node
WORKDIR /home/node
COPY --chown=node:node . /home/node
COPY --chown=node:node --from=build /root/node_modules /home/node/node_modules
EXPOSE 1080 1025
ENV MAILDEV_WEB_PORT 1080
ENV MAILDEV_SMTP_PORT 1025
ENTRYPOINT ["bin/maildev"]
```

### 3. **Run Docker**

```
$ docker run -p "1080:1080" -p "1025:1025" maildev/maildev
```