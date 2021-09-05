Let's connect the Graph DB from the Nebula Console for the initial exploration.

> Please get the `<host_ip>` with the IP you got just before:

```
ifconfig | grep -Eo 'inet (addr:)?([0-9]*\.){3}[0-9]*' | grep -Eo '([0-9]*\.){3}[0-9]*' | grep $(/sbin/ip route | awk '/default/ { print $3 }' | cut -c1-8) 
```{{execute}}

```
~/.nebula-up/console.sh
```{{execute}}

Then, you could connect to with the `nebula-console` binary inside the container by executing:
`nebula-console -u root -p nebula -port 9669 -addr <host_ip>`{{copy}}



## Query the Graph!

Nebula Graph supports physically isolated setup of multi graph spaces, you can use `show spaces`{{execute}} to query spaces in the cluster now, it should be like, before querying them, we need to `use <space_name>`, and it will be `use shareholding`{{execute}} in our case.

```
(user@nebula) [(none)]> show spaces
+--------------------+
| Name               |
+--------------------+
| "shareholding"     |
+--------------------+
Got 1 rows (time spent 3851/4595 us)

(user@nebula) [(none)]> use shareholding
Execution succeeded (time spent 1822/2342 us)
```

Then, you could query it here:
`GO 1 TO 3 STEPS FROM "10000132" over * BIDIRECT`{{execute}}

This query, means to walk you through the vertex(a made up company named Chambers LLC.) with id `10000132` from 1 upto 3 jumps from all relationships in all directions.

And it could be like this:
```
(root@nebula) [shareholding]> GO 1 TO 3 STEPS FROM "10000132" over * BIDIRECT | limit 100
+--------------+-------------------+-----------------+--------------------+
| role_as._dst | is_branch_of._dst | hold_share._dst | reletive_with._dst |
+--------------+-------------------+-----------------+--------------------+
| "200002024"  |                   |                 |                    |
+--------------+-------------------+-----------------+--------------------+
| "200003144"  |                   |                 |                    |
+--------------+-------------------+-----------------+--------------------+
| "200004000"  |                   |                 |                    |
+--------------+-------------------+-----------------+--------------------+
...
```

Isn't that cool? you can explore more queries referring to nebula documentations:
- https://docs.nebula-graph.io
- https://docs.nebula-graph.com.cn

In next step, we will try exploring that in a visual way!

Before we step to next part, let's execute the console first, just click twice:
```
exit
```{{execute}}