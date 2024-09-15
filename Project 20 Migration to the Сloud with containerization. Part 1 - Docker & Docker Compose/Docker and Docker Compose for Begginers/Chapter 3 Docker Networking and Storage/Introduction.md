# Docker Networking

Docker provides several networking modes that allow containers to communicate with each other and the outside world. Here are the four networking modes:

**1. Bridge Network**

- The default networking mode
- Creates a bridge between the container and the host machine
- Allows containers to communicate with each other and the host machine
- Containers are assigned an IP address from the bridge network

**2. Host Network**

- Bypasses the bridge network and connects the container directly to the host machine's network
- Containers share the host machine's IP address and can communicate directly with the outside world
- Not recommended for production use due to security concerns

**3. None Network**

- Disables networking for the container
- Containers cannot communicate with the outside world or other containers
- Useful for testing or development environments where networking is not required

**4. Overlay Network**

- Allows multiple Docker daemons to communicate with each other
- Enables containers to communicate with each other across multiple hosts
- Requires a Docker swarm or a third-party networking solution

## Creating and Managing Docker Networks

You can create and manage Docker networks using the following commands:

**Create a Network**

`docker network create mynetwork`

**List Networks**

`docker network ls`

**Inspect a Network**

`docker network inspect mynetwork`

**Delete a Network**

`docker network rm mynetwork`

**Connect a Container to a Network**

`docker run -d --network=mynetwork mycontainer`

**Disconnect a Container from a Network**

`docker network disconnect mynetwork mycontainer`

# Docker Storage

Docker provides several storage options that allow you to persist data even after a container is deleted. Here are the three storage options:

### 1. Volumes

- A directory that is shared between the host machine and the container
- Data is persisted even after the container is deleted
- Can be used to share data between containers

**Create a Volume**

`docker volume create myvolume`

**List Volumes**

`docker volume ls`

**Inspect a Volume**

`docker volume inspect myvolume`

**Delete a Volume**

`docker volume rm myvolume`

### 2. Bind Mounts

- A directory on the host machine that is mounted inside the container
- Data is persisted even after the container is deleted
- Can be used to share data between the host machine and the container

**Create a Bind Mount**

`docker run -d -v /host/path:/container/path mycontainer`

### 3. Tmpfs Mounts

- A temporary file system that is mounted inside the container
- Data is not persisted after the container is deleted
- Can be used for temporary storage or caching

**Create a Tmpfs Mount**

`docker run -d --tmpfs /container/path mycontainer`

## Managing Data in Docker Containers

Here are some best practices for managing data in Docker containers:

- Use volumes or bind mounts to persist data
- Avoid using the container's file system for persistent data
- Use environment variables or configuration files to manage application data
- Use Docker Compose to manage multi-container applications and their data

## Volumes and bind mounts Differences

Volumes and bind mounts are both used to persist data in Docker containers, but they have different performance characteristics.

### Volumes

**Performance Characteristics:**

- **Read/Write Performance:** Volumes have good read/write performance because they are stored on the host machine's file system, which is typically a fast storage device.
- **File System Overhead:** Volumes have some file system overhead because Docker has to manage the volume's metadata, such as permissions and ownership.
- **Data Consistency:** Volumes ensure data consistency because Docker manages the data stored in the volume.
- **Portability:** Volumes are portable because they are stored on the host machine's file system, which means you can easily move the volume to another host machine if needed.

### Bind Mounts

**Performance Characteristics:**

- **Read/Write Performance:** Bind mounts have excellent read/write performance because they are simply a reference to a directory on the host machine's file system. There is no overhead of storing data in a separate volume.
- **File System Overhead:** Bind mounts have minimal file system overhead because the host machine's file system is responsible for managing the data stored in the bind mount.
- **Data Consistency:** Bind mounts do not ensure data consistency because the host machine's file system is responsible for managing the data stored in the bind mount.
- **Portability:** Bind mounts are not portable because they are tied to a specific directory on the host machine's file system. If you move the container to another host machine, the bind mount will not work unless the same directory exists on the new host machine.

**Comparison**

In terms of performance, bind mounts are generally faster than volumes because they do not have the overhead of managing metadata and ensuring data consistency. However, volumes provide better data consistency and portability than bind mounts.

Here are some general guidelines to help you decide between volumes and bind mounts:

- Use volumes when:

  - You need to persist data even after the container is deleted.
  - You need to ensure data consistency and portability.
  - You are working with large amounts of data that need to be stored in a managed file system.

- Use bind mounts when:
  - You need high-performance read/write access to a directory on the host machine's file system.
  - You need to share data between the host machine and the container.
  - You are working with small amounts of data that do not need to be persisted after the container is deleted.

In summary, volumes provide better data consistency and portability, but bind mounts offer better performance. The choice between volumes and bind mounts depends on your specific use case and performance requirements.

## Create and manage Docker volumes

Docker volumes are a way to persist data even after a container is deleted. Here's a step-by-step guide on how to create and manage Docker volumes:

**Creating a Docker Volume**

You can create a Docker volume using the following command:

```bash
docker volume create myvolume
```

This will create a new volume named `myvolume`.

**Listing Docker Volumes**

You can list all Docker volumes using the following command:

```bash
docker volume ls
```

This will display a list of all volumes, including their names, drivers, and mount points.

**Inspecting a Docker Volume**

You can inspect a Docker volume using the following command:

```bash
docker volume inspect myvolume
```

This will display detailed information about the volume, including its name, driver, mount point, and options.

**Deleting a Docker Volume**

You can delete a Docker volume using the following command:

```bash
docker volume rm myvolume
```

This will delete the volume and all its contents.

**Using a Docker Volume with a Container**

You can use a Docker volume with a container by specifying the volume name in the container's command:

```bash
docker run -d --name mycontainer -v myvolume:/app/data myimage
```

This will create a new container named `mycontainer` and mount the `myvolume` volume to the `/app/data` directory inside the container.

### Types of Docker Volumes

Docker provides three types of volumes:

1. **Local Volumes**: These are stored on the host machine's file system and are specific to the host machine.
2. **Named Volumes**: These are stored on the host machine's file system and can be shared between containers.
3. **Bind Mounts**: These are directories on the host machine's file system that are mounted into a container.

### Docker Volume Drivers

Docker provides several volume drivers that allow you to store volumes in different storage systems, such as:

1. **Local**: Stores volumes on the host machine's file system.
2. **Azure File Storage**: Stores volumes in Azure File Storage.
3. **Amazon S3**: Stores volumes in Amazon S3.
4. **NFS**: Stores volumes in an NFS file system.

You can specify a volume driver when creating a volume using the `-d` or `--driver` flag:

```bash
docker volume create -d azurefile myvolume
```

This will create a new volume named `myvolume` using the Azure File Storage driver.

## Best Practices for Docker Volumes

1. **Use named volumes**: Named volumes make it easy to share data between containers and persist data even after a container is deleted.
2. **Use volume drivers**: Volume drivers allow you to store volumes in different storage systems, making it easy to integrate with existing infrastructure.
3. **Mount volumes correctly**: Make sure to mount volumes to the correct directory inside the container to avoid data loss or corruption.
4. **Regularly back up volumes**: Regularly back up your volumes to prevent data loss in case of a failure.

## Common use cases for tmpfs mounts in docker

**1. Caching**: Tmpfs mounts can be used to cache frequently accessed data, such as database query results or rendered web pages, to improve performance.

**2. Temporary Storage**: Tmpfs mounts can be used as a temporary storage location for data that is generated or processed during the execution of a container, such as temporary files or logs.

**3. Scratch Space**: Tmpfs mounts can be used as a scratch space for containers that require a temporary location to store files or data during execution, such as compilers or build tools.

**4. RAM-based Databases**: Tmpfs mounts can be used to store database files or data in RAM, providing a significant performance boost for applications that require fast data access.

**5. In-Memory Caching Layers**: Tmpfs mounts can be used to implement in-memory caching layers for applications, such as caching layers for web applications or APIs.

**6. Development and Testing**: Tmpfs mounts can be used to speed up development and testing workflows by providing a fast and temporary storage location for data and files.

**7. Ephemeral Data**: Tmpfs mounts can be used to store ephemeral data, such as session data or temporary user data, that does not need to be persisted across container restarts.

**8. CI/CD Pipelines**: Tmpfs mounts can be used in CI/CD pipelines to provide a temporary storage location for build artifacts or test data.

**9. Data Processing**: Tmpfs mounts can be used to process large datasets in memory, providing a significant performance boost for data-intensive applications.

**10. Security**: Tmpfs mounts can be used to store sensitive data, such as encryption keys or passwords, in a secure and temporary location that is not persisted to disk.

To use a tmpfs mount in Docker, you can use the `--tmpfs` flag when running a container, like this:

```bash
docker run -d --tmpfs /app/data myimage
```

This will create a tmpfs mount at `/app/data` inside the container.

Keep in mind that tmpfs mounts are temporary and will be lost when the container is restarted or deleted. Therefore, they should not be used to store persistent data.

### Monitor tmpfs

Monitoring tmpfs usage in a running container can be a bit tricky, but there are a few ways to do it. Here are some methods:

**Method 1: Using `docker exec` and `df`**

You can use `docker exec` to execute a command inside the running container, and then use `df` to display disk usage statistics, including tmpfs usage.

Here's an example:

```
docker exec -it <container_id> df -h --type=tmpfs
```

This will display the tmpfs usage in the container, including the total size, used space, and available space.

**Method 2: Using `docker inspect`**

You can use `docker inspect` to inspect the container's configuration and runtime information, including tmpfs usage.

Here's an example:

```
docker inspect -f '{{.GraphDriver.Data.DeviceName}}' <container_id>
```

This will display the device name of the tmpfs mount, which you can then use to query the tmpfs usage using `df`.

**Method 3: Using `docker stats`**

You can use `docker stats` to display live statistics about the container's resource usage, including memory and disk usage, including tmpfs usage.

Here's an example:

```
docker stats <container_id>
```

This will display a table with various statistics, including memory usage, CPU usage, and disk usage, including tmpfs usage.

**Method 4: Using a monitoring tool**

You can use a monitoring tool like `docker-monitor` or `cAdvisor` to monitor the container's resource usage, including tmpfs usage.

Here's an example with `docker-monitor`:

```
docker-monitor -c <container_id> -m tmpfs
```

This will display a graph with the tmpfs usage over time.

**Method 5: Using a Linux command inside the container**

You can use a Linux command like `cat /proc/mounts` or `mount` to display information about the tmpfs mount inside the container.

Here's an example:

```
docker exec -it <container_id> cat /proc/mounts
```

This will display a list of mounted file systems, including the tmpfs mount, with information about the mount point, device, and usage.

These are some of the ways to monitor tmpfs usage in a running container. The method you choose will depend on your specific use case and requirements.
