Finally we can run some Graph Algorithm leveraging Nebula Algorithm!

First let's start a spark in container.
```
mkdir -p algo
docker run --name spark-master --network nebula-docker-compose_nebula-net \
    -h spark-master -e ENABLE_INIT_DAEMON=false -d \
    -v algo:/root \
    bde2020/spark-master:2.4.5-hadoop2.7
```{{execute}}

From now on, let's access the spark container, and download the nebula-algorithm applicaiton package:
```
docker exec -it spark-master bash
cd /root/
wget https://repo1.maven.org/maven2/com/vesoft/nebula-algorithm/2.0.0/nebula-algorithm-2.0.0.jar
```{{execute}}

Then we could create a conf file for nebula algorithm application, where it will read graph data from the `source: nebula`, that is the cluster we deployed.

```
cat <<EOF >> pagerank.conf
{
  # Spark relation config
  spark: {
    app: {
        name: pagerank
        # spark.app.partitionNum
        partitionNum:10
    }
    master:local
  }

  data: {
    # data source. optional of nebula,csv,json
    source: nebula
    # data sink, means the algorithm result will be write into this sink. optional of nebula,csv,text
    sink: csv
    # if your algorithm needs weight
    hasWeight: false
  }

  # Nebula Graph relation config
  nebula: {
    # algo's data source from Nebula. If data.source is nebula, then this nebula.read config can be valid.
    read: {
        # Nebula metad server address, multiple addresses are split by English comma
        metaAddress: "metad0:9559"
        # Nebula space
        space: shareholding
        # Nebula edge types, multiple labels means that data from multiple edges will union together
        labels: ["role_as", "is_branch_of", "hold_share", "reletive_with"]
        # Nebula edge property name for each edge type, this property will be as weight col for algorithm.
        # Make sure the weightCols are corresponding to labels.
        weightCols: []
    }

    # algo result sink into Nebula. If data.sink is nebula, then this nebula.write config can be valid.
    write:{
        # Nebula graphd server address， multiple addresses are split by English comma
        graphAddress: "graphd1:9669"
        # Nebula metad server address, multiple addresses are split by English comma
        metaAddress: "metad0:9559"
        user:root
        pswd:nebula
        # Nebula space name
        space:shareholding
        # Nebula tag name, the algorithm result will be write into this tag
        tag:pagerank
    }
  }

  local: {
    read: {}

    # algo result sink into local file. If data.sink is csv or text, then this local.write can be valid.
    write:{
        resultPath:/output/
    }
  }


  algorithm: {
    executeAlgo: pagerank

    # PageRank parameter
    pagerank: {
        maxIter: 10
        resetProb: 0.15  # default 0.15
    }
 }
}
EOF
```{{execute}}

Let's change the nebula graph address with this script:
```
metad0=$(ping -q -c 1 -t 1 metad0 | grep PING | sed -e "s/).*//" | sed -e "s/.*(//")
graphd1=$(ping -q -c 1 -t 1 graphd1 | grep PING | sed -e "s/).*//" | sed -e "s/.*(//")
sed -i "s/metad0/$metad0/g" pagerank.conf
sed -i "s/graphd1/$graphd1/g" pagerank.conf
```{{execute}}

And here we go! Run the algorithm now:
```
/spark/bin/spark-submit --master "local" --conf spark.rpc.askTimeout=6000s \
    --class com.vesoft.nebula.algorithm.Main \
    --driver-memory 1g nebula-algorithm-2.0.0.jar \
    -p pagerank.conf
```{{execute}}

After it's executed, we could have the result writing to a csv file: `head /output/*.csv`{{execute}} , and it should be like this:

```
bash-5.0# head /output/*.csv
_id,_pagerank
2004280,0.49140126921950034
2008760,0.49140126921950034
2001760,0.49140126921950034
2006960,0.49140126921950034
2002160,0.700246808637788
2005960,0.9090923480560755
2004160,0.49140126921950034
200720,0.49140126921950034
2007720,0.49140126921950034
```

You can explore more referring to nebula documentations and nebula algorithm repo!

- https://docs.nebula-graph.io
- https://docs.nebula-graph.com.cn
- https://github.com/vesoft-inc/nebula-spark-utils/tree/master/nebula-algorithm
