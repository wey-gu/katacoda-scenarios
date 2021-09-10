In this step, we will import a graph of dataset on shareholding.

It's automatically generated in https://github.com/wey-gu/nebula-shareholding-example.

The graph schema is quite straightforward:

![data_moedeling](https://raw.githubusercontent.com/wey-gu/nebula-shareholding-example/main/images/modeling.png)

It comes with two kind of entities: person and corp, and with following relationship types.

- `person` can hold a `corp` in `{share} %`
- `person` can be relative with another `person`
- `corp` can hold another `corp` in `{share} %`
- `corp` can be a branch of another `corp`
- `person` can be as a role of a `corp`

Let's import them into our Nebula Graph Cluster.

- First clone the github repo: `git clone https://github.com/wey-gu/nebula-shareholding-example.git`{{execute}} 
- Then copy needed files to the $HOME path: `mkdir -p data;cp nebula-shareholding-example/{nebula-importer.yaml,data_sample/*} $HOME/data`{{execute}}
- And do minor changes on that to enable nebula-algorithm in later sessions. `sed -i "s/c_/10000/g" data/*.csv;sed -i "s/p_/20000/g" data/*.csv`{{execute}}
- Then let's call nebula-importer to import the data:

  ```
  docker run --rm -ti \
  --network=nebula-docker-compose_nebula-net \
  -v ${HOME}/data:/root \
  vesoft/nebula-importer:v2 \
  --config /root/nebula-importer.yaml
  ```{{execute}}

Now you will have imported a whole Graph in out Nebula Cluster, let's continue to explore it!