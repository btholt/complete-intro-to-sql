---
title: pgAdmin
---

I'm going to show you a quick tool that you can continue using through out the rest of the course or you can just know it's there and stop using it. Some people and need and love it, some people (like me) got used to using the command line (psql) and don't use it as much.

I speak of [pgAdmin][pgadmin]. Very similar to [phpMyAdmin][phpmyadmin] for MySQL, it exposes a GUI to interact with a PostgreSQL database. Again, I'm not super familiar with its workings but I can help you get it running and you can toy with it a bit.

In your app that you cloned, there is [a Docker Compose file][compose] that you can use to get PostgreSQL spun up with pgAdmin.

In the root directory of your sql-apps directory, run the following:

> Note that the following will download the pgAdmin container, about ~400MB in size.

> You also may need to `docker kill sql` since they'll both try to listen on port `5432`.

```bash
docker compose up
```

After you see the PostgreSQL database start up then the pgAdmin container start up, try going to [localhost:1234](http://localhost:1234). From there log in with `admin@example.com` and `example`.

Once in the menu, follow these instructions:

Click "Add New Server".

![Add new server](/images/add-new-server.png)

Add a name. You can call it whatever you want, it's just internal to pgAdmin. I called mine db. After that, click connection.

![Add a name then click connection](/images/connection.png)

Here you need to

- Put `db` ad the hostname/address (as this is what Docker Compose is exposing the host as to the pgAdmin container)
- Put port as `5432`
- Leave maintenance database as `postgres`
- Put username as `postgres`
- Put password as `lol`
- Save password
- Click save

![Config of adding a server in pgAdmin](/images/config.png)

> If save is grayed out, you likely didn't add a name on the General tab.

From here you can click around the pgAdmin page and see what you can do. Maybe start by writing some queries by using the Tools > Query Tool.

[pgadmin]: https://www.pgadmin.org/
[phpmyadmin]: https://www.phpmyadmin.net/
[compose]: https://github.com/btholt/sql-apps/blob/main/docker-compose.yml
