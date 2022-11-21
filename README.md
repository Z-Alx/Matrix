# Matrix Server

## Getting started
Install docker, docker compose and Git

https://docs.docker.com/engine/install/

https://docs.docker.com/compose/install/

Go to directory /opt
```text
cd /opt/
```

Clone the repository

```text
git clone git@github.com:Z-Alx/matrix.git
```

Go to directory
```text
cd /opt/matrix
```

Create network for services
```text
sudo docker network create --driver=bridge --subnet=10.10.10.0/24 --gateway=10.10.10.1 matrix_net
```

## Add your files
Generate the homeserver config
```text
sudo docker run -it --rm \
    -v "/opt/matrix/synapse:/data" \
    -e SYNAPSE_SERVER_NAME=matrix.YourDomain.com \
    -e SYNAPSE_REPORT_STATS=yes \
    matrixdotorg/synapse:latest generate
```

Create file .env and add text
```
touch /opt/matrix/.env
```

Change credential
```text
POSTGRES_DB=YourNameDatabase
POSTGRES_USER=YourUsername
POSTGRES_PASSWORD=YourPassword
```

Add the Postgres config to homeserver
```text
sudo nano /opt/matrix/synapse/homeserver.yaml
```

Change section database
```yaml
...
database:
#  name: sqlite3
#  args:
#    database: /data/homeserver.db
  name: psycopg2
  args:
    user: YourUsername
    password: YourPassword
    database: YourNameDatabase
    host: postgres
    cp_min: 5
    cp_max: 10
...
```

Deploy
```
sudo docker-compose up -d
```

Create New Users
Access docker shell:
```text
sudo docker exec -it matrix_synapse_1 bash
register_new_matrix_user -c /data/homeserver.yaml http://localhost:8008
```

Follow the on screen prompts
Enter exit to leave the container's shell

Pull the new docker images and then restart the containers:
```text
sudo docker-compose pull && sudo docker-compose up -d
```