What is the best way to get familiar with anything? Learning by doing-using it to create something!

Siwi (/ˈsɪwi/) is a PoC of Dialog System With Graph Database Backed Knowledge Graph. Its code is of course also open-sourced here: https://github.com/wey-gu/nebula-siwi.

It's constructed as follow:

```
┌────────────────┬──────────────────────────────────────┐
│                │                                      │
│                │  Speech                              │
│     ┌──────────▼──────────┐                           │
│     │            Frontend │   Siwi, /ˈsɪwi/           │
│     │ Web_Speech_API      │   A PoC of                │
│     │                     │   Dialog System           │
│     │ Vue.JS              │   With Graph Database     │
│     │                     │   Backed Knowledge Graph  │
│     └──────────┬──────────┘                           │
│                │  Sentence                            │
│                │                                      │
│   ┌────────────┼──────────────────────────────┐       │
│   │            │                              │       │
│   │            │              Backend         │       │
│   │ ┌──────────▼──────────┐                   │       │
│   │ │ Web API, Flask      │   ./app/          │       │
│   │ └──────────┬──────────┘                   │       │
│   │            │  Sentence    ./bot/          │       │
│   │ ┌──────────▼──────────┐                   │       │
│   │ │                     │                   │       │
│   │ │ Intent matching,    │   ./bot/classifier│       │
│   │ │ Symentic Processing │                   │       │
│   │ │                     │                   │       │
│   │ └──────────┬──────────┘                   │       │
│   │            │  Intent, Entities            │       │
│   │ ┌──────────▼──────────┐                   │       │
│   │ │                     │                   │       │
│   │ │ Intent Actor        │   ./bot/actions   │       │
│   │ │                     │                   │       │
│   └─┴──────────┬──────────┴───────────────────┘       │
│                │  Graph Query                         │
│     ┌──────────▼──────────┐                           │
│     │                     │                           │
│     │ Graph Database      │    Nebula Graph           │
│     │                     │                           │
│     └─────────────────────┘                           │
│                                                       │
└───────────────────────────────────────────────────────┘
```

Let's start the backend service `siwi-api` first.

```
docker run --rm --name siwi-api \
     --env=PORT=5000 \
     --env=NG_ENDPOINTS=127.0.0.1:32669 \
     --net=host \
     weygu/siwi-api
```{{execute}}

And we can give a test on that the `siwi-api`
```
curl --header "Content-Type: application/json" \
     --request POST \
     --data '{"question": "What is the relationship between Yao Ming and Lakers ?"}' \
     http://localhost:5000/query |jq
```{{execute}}

Then we setup the front-end part:
```
apt update; apt install nginx -y
curl https://raw.githubusercontent.com/wey-gu/nebula-siwi/main/conf/siwi-nginx.conf -o /etc/nginx/nginx.conf
systemctl restart nginx
git clone https://github.com/wey-gu/nebula-siwi.git
python3 -m http.server 8082 --directory nebula-siwi/src/siwi_frontend/dist/
```{{execute}}

Let's give a test of `siwi-api` through nginx:
```
curl --header "Content-Type: application/json" \
     --request POST \
     --data '{"question": "What is the relationship between Yao Ming and Lakers ?"}' \
     http://localhost:80/query |jq
```{{execute}}

Let's try our Siwi properly: https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com/

Did you see the circle on the page? Click the second one you'll see the agent!

Then try say some sentences by typing or even speak with the mic?

> " whom does Tim Duncan follow?

> " how does Tim Duncan and Lakers connected?

Now we finished the whole project!
It's quite silly and simple though, the real KGQA system will be complicated in many ways, while, it still share the major ideas of it. I hope you enjoy the siwi and how Nebula Graph works here on top of K8s.

See ya!