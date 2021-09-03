In this step, we will bootstrap a Nebula Graph Cluster and Studio Web App in Container with Nebula-Up, please note Nebula-Up is not built for Productions but only a playground setup tool.

## Deploy Nebula Graph and Nebula Studio

There are quite a few deployment methods supported for Nebula Graph, please read them all from the Documentations: [Docs](https://docs.nebula-graph.io) , [文档](https://docs.nebula-graph.com.cn).

Today, in our course, it's simple and easy with [Nebula-Up](https://siwei.io/nebula-up), just an one-liner call like:

```bash
curl -fsSL nebula-up.siwei.io/install.sh | bash
```{{execute}}

It will take one miniute or two, you should see a congratulation message after it's finished.

## Get to know what you just deployed.

Congrats! Now you have deployed a bunch of Docker Containers:
- Nebula Cluster v2.x: [`GraphD * 3`, `MetaD * 3`,  `StorageD * 3`]
- Nebula Studio v2.x: [`Studio WebApp`, `Nebula-http-gateway`, `Nebula-importer`, `Nginx`]

> Note, in Nebula Studio 3.x, the situation will be different.

For Studio, the access point is `host_ip:7001`.
For Nebula Cluster, the typical exposed access point to user is the GraphD, by default it's `host_ip:9669`, please note this is also what you need to fill in the Studio's Form for `host` field later.

```
           │              │
           ▼              ▼
 host_ip:7001    console -addr -ip -port 9669
┌───────[GUI]    [CLI/Graph Client]─────────┐
│  ┌─────┐ ┌─────────────┐                  │
│  │     ├─►Studio WebApp│                  │
│  │     │ └─────────────┘                  │
└──►nginX│                                  │
   │     │ ┌────────────┐ ┌───────────────┐ │
   │     ├─►Http Gateway├─►Nebula-Importer│ │
   └─────┘ └─────┬──────┘ └───────┬───────┘ │
     ┌───────────┴────────────────┘         │
     ├───────────┬───────────┬──────────────┘
 ┌───▼──┐    ┌───▼──┐    ┌───▼──┐    ┌─────┐
 │GraphD│    │GraphD│    │GraphD│    │MetaD│
 └──────┘    └──────┘    └──────┘    └─────┘
 host_ip:9669
                                     ┌─────┐
                                     │MetaD│
 ┌────────┐ ┌────────┐ ┌────────┐    └─────┘
 │        │ │        │ │        │
 │StorageD│ │StorageD│ │StorageD│    ┌─────┐
 │        │ │        │ │        │    │MetaD│
 └────────┘ └────────┘ └────────┘    └─────┘
```

Now, let's get the `host_ip` of your lab environment by executing this command:
```
ifconfig | grep -Eo 'inet (addr:)?([0-9]*\.){3}[0-9]*' | grep -Eo '([0-9]*\.){3}[0-9]*' | grep $(/sbin/ip route | awk '/default/ { print $3 }' | cut -c1-8) 
```{{execute}}

## Connect to Nebula Studio!
Now Click the studio tab in your terminal window zone, or click: https://[[HOST_SUBDOMAIN]]-7001-[[KATACODA_HOST]].environments.katacoda.com/
You should see the Nebula Studio Web App, and in the `Config Server` or `配置服务器` form, you should fill in three fields:

- Host
- Username
- Password

For Host part, it's `<host_ip>:9669`, where to replace the `<host_ip>` with the IP address you get in last step.

For Username, Password it could be `root` and `nebula`, actually, the authentication was disabled in this cluster, thus any user/password will make you get into the cluster!

After you see an option pop-up window for selecting spaces, it means you now are conncting to the Nebula Studio.

## Connect to Nebula Cluster with Console.

This time we can get back to the terminal. With Nebula-Up, a script to call nebula console in a docker container is easy as the follow line:

```
~/.nebula-up/console.sh
```{{execute}}

Then, you could connect to with the `nebula-console` binary inside the container by executing:
`nebula-console -u root -p nebula -port 9669 -addr <host_ip>`{{copy}}

> Please replace the `<host_ip>` with the IP you got just before.

Execute a Nebula nGQL command now!
```
show hosts
```{{execute}}

Before we step to next part, let's execute the console first:
```
exit
exit
```{{execute}}

Congrats! Now you have a up and running Nebula Cluster and you could access it with both GUI and CLI~
```

