#Img from Docker Hub
FROM nginx:latest
# Move the static HTML site into the image to the directory Nginx
COPY nginx-html/ /usr/share/nginx/html/
#copy the personal configuration file to the Nginx configuration directory
COPY nginx-config/nginx.conf /etc/nginx/nginx.conf
# Port80 to web page
EXPOSE 80
# Run Nginx and keep it running
CMD ["nginx", "-g", "daemon off;"]

