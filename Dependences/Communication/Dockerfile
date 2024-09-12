# Base
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