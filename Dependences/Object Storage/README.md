![ReferenceImage](/images/▪Objects_Storage 🗃.png)
# MinIO Setup Guide with Docker

This guide will help you set up **MinIO**, a high-performance, distributed object storage service, using **Docker**.

### Technologies Used
- Docker
- MinIO
- Python (optional, for interacting with MinIO)

Assuming Docker is already installed, we’ll use a MinIO Docker container to create an object storage service. 

### Step 1: Docker Setup

1. **Create a Dockerfile (Optional)**

If you want to customize your MinIO container, you can create a Dockerfile. For most cases, using the official MinIO Docker image directly is sufficient.

2. **Run MinIO Container**

You can run MinIO directly using Docker with the following command:

```bash
docker run -d --name minio \
  -p 9000:9000 \
  -p 9001:9001 \
  -e MINIO_ROOT_USER=admin \
  -e MINIO_ROOT_PASSWORD=password \
  -v /mnt/data:/data \
  minio/minio server /data --console-address ":9001"
```

### Explanation:
- `-d`: Runs the container in detached mode.
- `--name minio`: Names the container `minio`.
- `-p 9000:9000`: Maps port 9000 on your host to port 9000 on the container (MinIO API).
- `-p 9001:9001`: Maps port 9001 on your host to port 9001 on the container (MinIO Console).
- `-e MINIO_ROOT_USER=admin`: Sets the MinIO root user.
- `-e MINIO_ROOT_PASSWORD=password`: Sets the MinIO root password.
- `-v /mnt/data:/data`: Mounts the host directory `/mnt/data` to `/data` in the container for persistent storage.
- `minio/minio server /data --console-address ":9001"`: Starts MinIO server and binds the MinIO console to port 9001.

### Step 2: Access MinIO

- **MinIO API**: Access the MinIO API at `http://localhost:9000`.
- **MinIO Console**: Access the MinIO web console at `http://localhost:9001`.

Log in using the credentials:
- **Username**: `admin`
- **Password**: `password`

### Step 3: Optional Python Setup

To interact with MinIO using Python, you can use the `minio` library. Here’s how to set it up:

1. **Install MinIO Python Client**

```bash
# Create a virtual environment
python -m venv myenv

# Activate the virtual environment (Linux/Mac)
source myenv/bin/activate

# Activate the virtual environment (Windows)
myenv\Scripts\activate

# Install the MinIO client library
python -m pip install minio
```

2. **Example Python Script**

Here’s a sample script to interact with MinIO:

```python
from minio import Minio
from minio.error import S3Error

# Initialize MinIO client
client = Minio(
    "localhost:9000",
    access_key="admin",
    secret_key="password",
    secure=False
)

# Create a new bucket
bucket_name = "my-bucket"
try:
    if not client.bucket_exists(bucket_name):
        client.make_bucket(bucket_name)
        print(f"Bucket '{bucket_name}' created.")
    else:
        print(f"Bucket '{bucket_name}' already exists.")
except S3Error as e:
    print(f"Error occurred: {e}")

# Upload a file
try:
    client.fput_object(bucket_name, "example.txt", "/path/to/example.txt")
    print("File uploaded successfully.")
except S3Error as e:
    print(f"Error occurred: {e}")
```

### Step 4: Manage MinIO

You can manage MinIO via the web console at `http://localhost:9001`, where you can create buckets, upload objects, and manage permissions.

### Conclusion

You now have a MinIO server running inside a Docker container. You can interact with it via its API and web console, and use the `minio` Python library to automate interactions and manage your object storage.
