We will import a dataset, with console itself, as it's a tiny one from the documentations!

> Check documentations [here](https://docs.nebula-graph.io/2.5.0/3.ngql-guide/1.nGQL-overview/1.overview/#basketballplayer), or [这里](https://docs.nebula-graph.com.cn/2.5.0/3.ngql-guide/1.nGQL-overview/1.overview/#basketballplayer).

Let's download the data file first: `wget https://docs.nebula-graph.io/2.0/basketballplayer-2.X.ngql `{{execute}}

> Do you know there is `.ngql` Syntax Highlight VS Code extension? Guess who created it(wink)?
>
> Check out here: https://marketplace.visualstudio.com/items?itemName=wey-gu.vscode-ngql

Let's import the data with console: `~/.nebula-k8s/bin/console -u root -p password --address=127.0.0.1 --port=32669 -e ":play basketballplayer"`{{execute}}

So.... what's next? Introducing Siwi, the world least powerful KGQA powerred by Nebula Graph.
