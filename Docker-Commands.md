# WATCHTOWER ONE TIME UPDATE

NOTE: update the container name at the bottom

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --run-once  <containernames>
```

# To cleanup the Overlay2 folder

```bash
docker system prune -af
```

# To run commands inside docker container

```bash
docker exec -it <container name> <command>
```

# To open bash terminal inside docker container

```bash
docker exec -it <container name> bash
```

# To copy files from docker container to local

```bash
docker cp <container name>:<path_inside_container> <path_local>
```
