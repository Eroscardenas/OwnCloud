# Welcome to the area Cloud Computing with docker :whale:

![ReferenceImage](/images/✉ Message_Queue 📥.png)
# Message queue/broker
### How to set up a message broker using RabbitMQ and docker

we will be using various technologies
> * [Docker](https://www.docker.com)
> * [RabbitMQ](https://www.rabbitmq.com/docs/download)
> * ***Python and the library Pika*** 

Here we assume you already have **docker** installed. we will use one of the las [RabbitMQ](https://hub.docker.com/_/rabbitmq) docker container versions, if you need managment or not alpine version feel free to change it you can check out other verions in the [*DockerHub*](https://hub.docker.com/_/rabbitmq) official image:

 https://hub.docker.com/_/rabbitmq  the version in this project is [4.0-rc-alpine](https://github.com/docker-library/rabbitmq/blob/8049e562768aa2f2ff8b3b84e03cd111cd212ba2/4.0-rc/alpine/Dockerfile).

In the docker file we set some parameters ot use in the managmen mode given by RabbitMQ:

 `ENV RABBITMQ_DEFAULT_USER=admin` and `ENV RABBITMQ_DEFAULT_PASS=password` if they are not set before building, both will be `ENV RABBITMQ_DEFAULT_USER=guest` and `ENV RABBITMQ_DEFAULT_PASS=guest` as a good practice, they are chaged here.

Also a `VOLUME` is set up in our ***Dockerfile***, to make the messages persistent while transactions.

As you will see in the root of this project is a *scripts* folder, those examples are used to test, they were just took from the [RabbitMQ docs](https://www.rabbitmq.com/tutorials), to be able to execute them u need to install [**pika**](https://pypi.org/project/pika/) from the docs or just use the command given by **RabbitMQ** docs:
```
python -m pip install pika --upgrade
```
You can go pick to install this globaly or as we did, inside a virtual enviroment as a good practice in case you will not reuse that library again or to avoid versions issues:

#### Venv instructions
```
python -m venv myenv
```
Change the `myenv` as you prefer
```
source .myenv/bin/activate
```
Now you can work in you root folder, as a tip once you finish you just deactivate it `deactivate` in you terminal.

## docker build

Inside you root you need to build and run 
```
docker build -t yourDockerImage .
docker run -d --name rabbit-containerName -p 5672:5672 -p 15672:15672 youDockerImage
```
**Congrats** you  have your own message brocker inside a *Docker* container.
To make use of it, go to `localhost:15672` there you will see the managmen set up given by [RabbitMQ](https://www.rabbitmq.com/docs/download), now if you run the scripts they will be communicating one to another:
```
python client.py
python publisher.py
```
The output in the client should be something like this:
```
[x]recibed hellow
[*] Waiting for messages. To exit press CTRL+C
