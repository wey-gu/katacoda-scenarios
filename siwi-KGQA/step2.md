## Scale out the Nebula Cluster in K8s way

Let's try scaling out the storage, shall we? How about scaling out to 4 instances?

- Let's modify the CRD of `Kind:nebulaCluster.spec.storaged.replicas` from `3` to `4`

  `sed -i "s/replicas: 3/replicas: 4/g" ~/.nebula-k8s/nebula-operator/config/samples/apps_v1alpha1_nebulacluster.yaml`{{execute}}

- See if it's changed? `grep storaged: -A 9 ~/.nebula-k8s/nebula-operator/config/samples/apps_v1alpha1_nebulacluster.yaml`{{execute}} 

- OK, let's apply this change! `kubectl apply -f ~/.nebula-k8s/nebula-operator/config/samples/apps_v1alpha1_nebulacluster.yaml;`{{execute}}

- And let's check what we got now! `kubectl get pods;kubectl get nebulaCluster`{{execute}}

  ```
  $ kubectl get pods;kubectl get nebulaCluster
  NAME                READY   STATUS    RESTARTS   AGE
  nebula-graphd-0     1/1     Running   0          36m
  nebula-metad-0      1/1     Running   0          36m
  nebula-storaged-0   1/1     Running   0          36m
  nebula-storaged-1   1/1     Running   0          36m
  nebula-storaged-2   1/1     Running   0          36m
  nebula-storaged-3   1/1     Running   0          2m35s
  NAME     GRAPHD-DESIRED   GRAPHD-READY   METAD-DESIRED   METAD-READY   STORAGED-DESIRED   STORAGED-READY   AGE
  nebula   1                1              1               1             4                  4                36m
  ```
- Boom! It's 4 of them now!

So why not continue to check from the nebula cluster?

`~/.nebula-k8s/bin/console -u root -p password --address=127.0.0.1 --port=32669`{{execute}}

And it's quite handy, uh? So we will continue the Magical Journey~