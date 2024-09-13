# Welcome to the area Cloud Computing wurh docker :whale:
![ReferenceImage](/images/⛓️‍💥 Serverless 📶.png)
# Serverless
### How to set up a serverless service using *AWS lambda* and docker

we will be using various technologies:
> * [Docker](https://www.docker.com)
> * [AWS Lambda](https://aws.amazon.com/es/lambda/)
> * *Python* 

Here we assume you already have **docker** installed. we are going to set an container to make petitions in a serverless contatiner, which actually is redundant coinsidering that a *docker container* is actually serverless. We are using the api of amazon web services *Lambda*, and their *docker image* in *linux*.

In the docker file we used the *python linux* distribution, which includes the required components to run functions packaged as container images on [AWS Lambda](https://aws.amazon.com/es/lambda/), the runtime for a given language(python in our case), dependencies and the Lambda Runtime Interface Client (RIC), which implements the Lambda Runtime API.

This is just an ilustrative implementation, and still needs a lot of work the official [**amazon docs**](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python.html)on this are a lot more extensive on process descriptions, and about the *Lambda* api which has it's own console: https://docs.aws.amazon.com/lambda/latest/dg/lambda-python.html 

or even for more info check the amazon guide:

 https://docs.aws.amazon.com/serverless/latest/devguide/welcome.html 

## Container
You need 3 files one `lambda_func.py`, obviously a `Dockerfile` and some config file for requeriments if thats the case
#### Dockerfile
```
FROM public.ecr.aws/lambda/python:3.11.2024.09.06.09
```
That is the source *linux* image, which you can change picking from here https://gallery.ecr.aws/lambda/python

```
CMD [ "lambda_func.handler" ]
```
This other line of code is important because you set the CMD to your handler 

### docker build and run
 
```
docker build --platform linux/amd64 -t docker-image:test .
docker run --platform linux/amd64 -p 9000:8080 docker-image:test
```
Doing that you can send the command to watch if your container is running fine
```
curl "http://localhost:9000/2015-03-31/functions/function/invocations" -d '{}'
```

the next step would be using the [AWS Lambda](https://aws.amazon.com/es/lambda/) resources line ECR to upload and having it not running locally 