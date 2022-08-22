This is not intended to be a Docker course. If you do want a complete course on containers on Docker, [please watch my other course on Frontend Masters][fem].

However, the _fastest_ way we can get everyone up and running on PostgreSQL is to have everyone use Docker containers. Dockers allows any computer to run a lightweight emulation of a pre-made environment (typically Linux). In our case we're going to be using a premade Linux environment that has PostgreSQL already installed on it.

## Docker Desktop

[Please go install Docker Desktop][docker]. Docker Desktop is a product that gets Docker all set up for you without much effort regardless what OS you're using.

You are welcome to use Docker without Docker Desktop but you're on your own that point.

## The Command Line

You will need to interact a bit with the command line, either via Linux/macOS's zsh/bash or via Window's PowerShell. If you need help with CLIs or Linux, [click here to see my course on Frontend Masters][linux].

The amount of CLI usage should be fairly minimal and you should be able to just copy/paste exactly what I give you.

## The Two Containers You'll Need

We're going to use two containers:

- [PostgreSQL 14][pg]
- [btholt/complete-intro-to-sql][btholt]

The first is the base, official image of PostgreSQL 14 (the latest stable version as of writing.) I'll be using 14.3 but you can likely use anything that's 14.X. Normally they don't break things between version. If there's a new major stable version (e.g. 15.X, 16.X, etc.) I would not recommend taking the class using those. Things can break between major versions. Use 14.X for this course and then go see what's different after.

The latter container is based on the same Postgres 14 container but preload it with a bunch of movie data from the [Open Movie Database][omdb] as well as the complete RecipeGuru database. This is a dump of that database for us to play around with. This is based on [this setup script by credativ][credativ]. If you ever break anything, just shut down your container and restart it.

## Get Running

> Do note, these containers are quite large. Each is ~0.5GB.

Run the following to get yourself prepped for the course (optionally, you can let Docker do it for you under the hood too)

```bash
docker pull postgres:14
docker pull btholt/complete-intro-to-sql:14
```

To make sure you're working, run the following:

```bash
docker run -e POSTGRES_PASSWORD=lol --name=pg --rm -d -p 5432:5432 postgres:14
```

This should run PostgreSQL in the background.

- We gave it a password of "lol" (feel free to change it to something different, I just remember lol because lol)
- We ran it with a name of `pg` so we can refer it with that shorthand
- We used the `--rm` flag so the container deletes itself afterwards (rather than leave its logs and such around)
- We ran it in the background with `-d`. Otherwise it'll start in the foreground.
- The `-p` allows us to expose port 5432 locally which is the port Postgres runs on by default.

Run `docker ps` to see it running. You can also see it in the Docker Desktop app running under the containers tab.

Now run `docker kill pg` to kill the container. This will shut down the container and since we ran it with `--rm` it'll clean itself up afterwards too. We can run the `docker run …` to start it again.

Okay, let's try connecting to it with psql, the CLI tool for connecting to Postgres.

```bash
# Only run this if you don't have the container running. It'll error otherwise
docker run -e POSTGRES_PASSWORD=lol --name=pg --rm -d -p 5432:5432 postgres:14

docker exec -u postgres -it pg psql
```

Now you should be connected to Postgres and ready to run queries on a fresh Postgres instance!

[fem]: https://frontendmasters.com/courses/complete-intro-containers/
[docker]: https://www.docker.com/products/docker-desktop/
[linux]: https://frontendmasters.com/courses/linux-command-line/
[btholt]: https://hub.docker.com/r/btholt/complete-intro-to-sql
[pg]: https://hub.docker.com/_/postgres/
[omdb]: https://www.omdbapi.com/
[credativ]: https://github.com/credativ/omdb-postgresql
