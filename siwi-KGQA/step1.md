## Boostrap a Nebula Cluster in K8s

It's quite easy to do so with [Nebula-Operator](https://github.com/vesoft-inc/nebula-operator) already, while we could make it even easier with [Nebula-Operator-KIND](https://github.com/wey-gu/nebula-operator-kind).

Just call this one-liner: `curl -sL nebula-kind.siwei.io/install-on-k8s.sh | bash`{{execute}} and... that's it!

Then you probably will see this:

```
┌────────────────────────────────────────┐
│ 🌌 Nebula-K8s Playground is Up now!    │
├────────────────────────────────────────┤
│                                        │
│ 🎉 Congrats!                           │
│    $ cd ~/.nebula-k8s                  │
│                                        │
│ 🔥 Or access via Nebula Console:       │
│    $ ~/.nebula-k8s/bin/console         │
│                                        │
│    To remove the playground:           │
│    $ ~/.nebula-k8s/uninstall.sh        │
│                                        │
│ 🚀 Have Fun!                           │
│                                        │
└────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│ 🔥 You can access its console as with following command                      │
├──────────────────────────────────────────────────────────────────────────────┤
│~/.nebula-k8s/bin/console -u root -p password --address=127.0.0.1 --port=32669│
└──────────────────────────────────────────────────────────────────────────────┘
```

Would like to access the cluster? Just do what it hints: `~/.nebula-k8s/bin/console -u root -p password --address=127.0.0.1 --port=32669`{{execute}}

Then, you could query something like... `SHOW HOSTS`{{execute}}, we have 3 storaged right there now.

```
(root@nebula) [(none)]> show hosts
+------------------------------------------------------------------------+------+----------+--------------+----------------------+------------------------+
| Host                                                                   | Port | Status   | Leader count | Leader distribution  | Partition distribution |
+------------------------------------------------------------------------+------+----------+--------------+----------------------+------------------------+
| "nebula-storaged-0.nebula-storaged-headless.default.svc.cluster.local" | 9779 | "ONLINE" | 0            | "No valid partition" | "No valid partition"   |
+------------------------------------------------------------------------+------+----------+--------------+----------------------+------------------------+
| "nebula-storaged-1.nebula-storaged-headless.default.svc.cluster.local" | 9779 | "ONLINE" | 0            | "No valid partition" | "No valid partition"   |
+------------------------------------------------------------------------+------+----------+--------------+----------------------+------------------------+
| "nebula-storaged-2.nebula-storaged-headless.default.svc.cluster.local" | 9779 | "ONLINE" | 0            | "No valid partition" | "No valid partition"   |
+------------------------------------------------------------------------+------+----------+--------------+----------------------+------------------------+
```



OK, let's hold on for a while before we looking into the nebula itself. Let's see what we got running in K8s now?

- First exit the console `exit`{{execute}}

- Get the pods `kubectl get pods`{{execute}}

  ```
  $ kubectl get pods
  NAME                READY   STATUS    RESTARTS   AGE
  nebula-graphd-0     1/1     Running   0          10m
  nebula-metad-0      1/1     Running   0          10m
  nebula-storaged-0   1/1     Running   0          10m
  nebula-storaged-1   1/1     Running   0          10m
  nebula-storaged-2   1/1     Running   0          10m
  ```

- Get the nebula control plane workloads `kubectl get pods -n nebula-operator-system`{{execute}}

  ```
  $ kubectl get pods -n nebula-operator-system
  NAME                                                             READY   STATUS    RESTARTS   AGE
  nebula-operator-controller-manager-deployment-656467d58b-22l5x   2/2     Running   0          18m
  nebula-operator-controller-manager-deployment-656467d58b-gqhq2   2/2     Running   0          18m
  nebula-operator-scheduler-deployment-974b8c45d-5f5xj             2/2     Running   0          18m
  nebula-operator-scheduler-deployment-974b8c45d-9tlrf             2/2     Running   0          18m
  ```

- And our `kind: nebulaCluster` by `kubectl get nebulacluster`

  ```
  $ kubectl get nebulacluster
  NAME     GRAPHD-DESIRED   GRAPHD-READY   METAD-DESIRED   METAD-READY   STORAGED-DESIRED   STORAGED-READY   AGE
  nebula   1                1              1               1             3                  3                1m
  ```

- And that's all reflected with the CRD defined in this small YAML file: `cat ~/.nebula-k8s/nebula-operator/config/samples/apps_v1alpha1_nebulacluster.yaml`{{execute}}

Congrats! Now you have your first Nebula Cluster on K8s, that's easy yet fun, right?

