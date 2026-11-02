## Docker Storage

- docker image has read-only layers
- when we spin up a container; a container layer(writable layer) is created.
- we can make changes to this container layer. Eg: adding a file to container.
- but these changes are retained only during the container lifetime
- volumes help to make the data persistent
- storage drivers is responsbile for writing data into container.
- read only layers are written by storage drivers
- volume drivers responsible for making data persistent
- docker selects the storage driver based on OS.
- there are 3rd party volume drivers too

### Docker Volume

- Docker manages the storage location.
- Data persists even after container deletion.
- Stored in Docker-managed storage.
- Suitable for databases and application data.

Commands:

```
# Create volume
docker volume create mydata

# Using -v
docker run -d --name mycontainer \
  -v mydata:/app/data nginx

# Using --mount
docker run -d --name mycontainer \
  --mount type=volume,source=mydata,target=/app/data nginx
```

### Bind Mount

- User specifies the host directory.
- Maps a host directory/file into the container.
- Changes are reflected on both host and container.
- Suitable for source code, configuration files and development.

Commands:

```
# Using -v
docker run -d --name mycontainer \
  -v C:/mydata:/app/data nginx

# Using --mount
docker run -d --name mycontainer \
  --mount type=bind,source=C:/mydata,target=/app/data nginx
```

### Differences

| Feature            | Volume         | Bind Mount         |
| ------------------ | -------------- | ------------------ |
| Managed by         | Docker         | User               |
| Location           | Docker-managed | User-defined       |
| Container deletion | Data persists  | Data persists      |
| Host path          | Not required   | Required           |
| Portability        | More portable  | Host dependent     |
| Common usage       | Database data  | Source code/config |



### 5. Useful Commands

```
# List volumes
docker volume ls

# Inspect volume
docker volume inspect mydata

# Remove volume
docker volume rm mydata

# Remove unused volumes
docker volume prune

# Inspect container mounts
docker inspect mycontainer
```

