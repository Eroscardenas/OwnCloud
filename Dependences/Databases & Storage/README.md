# Welcome to the area Cloud Computing with docker :whale:

![ReferenceImage](/images/🗄DataBases_&_Storage 📁.png)
# Setting Up MongoDB Server Using Docker

This guide will walk you through setting up a [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/) server using [Docker](https://www.docker.com) and **Python** with the **pymongo** library.

### Technologies Used:
- [Docker](https://www.docker.com)
- [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/)
- **Python and the Pymongo Library**

Assuming you already have Docker installed. We will use one of the latest [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/) and [Docker](https://www.docker.com) container versions. If you need to check out other versions, you can find them on the official [DockerHub](https://hub.docker.com) page: [MongoDB on DockerHub](https://hub.docker.com/_/mongo). This project uses version `6.0`.

### Step 1: Docker Setup

1. **Create a Dockerfile**

```dockerfile
FROM mongo:6.0

# Set environment variables for user authentication
ENV MONGO_INITDB_ROOT_USERNAME=admin
ENV MONGO_INITDB_ROOT_PASSWORD=password

# Optional: Expose MongoDB default port
EXPOSE 27017

# Optional: Add a volume for data persistence
VOLUME /data/db
```

This *Dockerfile* sets up a [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/)container with a root user (`admin`) and password (`password`). If these values are not specified, [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/) will use default settings. It is a good practice to change these values for security reasons.

2. **Build and Run the Docker Container**

From your project root, run the following commands:

```bash
# Build the Docker image
docker build -t myMongoImage .

# Run the MongoDB container
docker run -d --name mongodb-container -p 27017:27017 myMongoImage
```

After running these commands, your [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/)server will be running on `localhost:27017`.

### Step 2: Setting Up Python with PyMongo

1. **Install PyMongo**

You can install the **pymongo** library globally or within a virtual environment. Here’s how to set it up in a virtual environment for best practices:

```bash
# Create a virtual environment
python -m venv myenv

# Activate the virtual environment (Linux/Mac)
source myenv/bin/activate

# Activate the virtual environment (Windows)
myenv\Scripts\activate

# Install pymongo inside the virtual environment
python -m pip install pymongo --upgrade
```

2. **Run Example Scripts**

To test the connection to [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/), use the following *Python* script to insert and retrieve a document:

```python
from pymongo import MongoClient

# Set up the MongoDB client
client = MongoClient('mongodb://admin:password@localhost:27017/')

# Connect to the 'test' database
db = client['test']

# Insert a document into 'test_collection'
collection = db['test_collection']
document = {'name': 'Alice', 'age': 25}
result = collection.insert_one(document)

# Print the ID of the inserted document
print(f"Document inserted with ID: {result.inserted_id}")

# Retrieve and print the document
retrieved_document = collection.find_one({'name': 'Alice'})
print("Retrieved Document:", retrieved_document)
```

This script:
- Connects to the [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/) server at `localhost:27017` using the credentials set up in the *Dockerfile*.
- Inserts a document into the `test_collection`.
- Retrieves and prints the document.

### Step 3: Deactivate the Virtual Environment

When you’re finished, deactivate the virtual environment with:

```bash
deactivate
```

### Step 4: MongoDB Management

You can manage [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/)using its command-line interface or a graphical tool such as [MongoDB Compass](https://www.mongodb.com/products/compass) to explore and manage your data.

### Conclusion

Congratulations! You now have a [MongoDB](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-community-with-docker/) server running inside a [Docker](https://www.docker.com) container. You can connect to it from *Python* using the `pymongo` library and manage your database through `localhost:27017`.

