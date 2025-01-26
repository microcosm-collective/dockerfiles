# dockerfiles

The repo contains a docker-compose file to get a development instance of Microcosm up and running locally.

It's not an ideal development environment, but it should work. And it provides a kind of reference implementation
that can be used to build a more appropriate dev environment if necessary.

# Steps

## 1. Install docker

You'll need a recent version that supports "watch".

## 2. Download the three repos

Download them into the same directory:

```
git clone https://github.com/microcosm-collective/dockerfiles.git
git clone https://github.com/microcosm-collective/microcosm.git
git clone https://github.com/microcosm-collective/microweb.git
```

## 3. Start the service

From the directory that contains all three repos, run the following:

```
docker compose -f dockerfiles/docker-compose.yaml up --build --watch
```

You should see:

- the base images being downloaded.
- our images being built.
- the database being bootstrapped.

It'll take a little while the first time, but subsequent runs should be a lot faster.

## 4. Verifying

The API should be running on port `8080`, the Django frontend should be on port `80`.

To make sure the API is running, you should get a JSON response when running the following:

```
curl --resolve dev1.microcosm.app:8080:127.0.0.1 -v http://dev1.microcosm.app:8080/api/v1/microcosms | jq
```

To test the Django front-end, just navigate to one of the sites:

- http://localhost/
- http://dev1.localhost/
- http://dev2.localhost/

To interact with the database:

```
docker exec -it microcosm-db-1 psql -U microcosm microcosm_development
```

Try `\dt *.*` to list tables, or `SELECT * FROM sites;` to list available sites on the Microcosm instance.

## Misc

The postgres data is stored in a persistent docker volume. To delete that volume (and reset the database),
you need to tear down the services before you can delete the volume:

```
docker compose -f dockerfiles/docker-compose.yaml down
docker volume rm microcosm_postgres-data
```

# Caveats

I've only tested this on Linux/x86, and I suspect there will be issues with ARM.
