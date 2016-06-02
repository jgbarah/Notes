# Developing for Kibana 5

These are some notes about setting up a development environment with Kibana 5, suitable to develop plugins, modify Kibana itself, etc.

## Installing and running

The instructions for installing the development environment are in the [CONTRIBUTING.md file of the Kibana git repository](https://github.com/elastic/kibana/blob/master/CONTRIBUTING.md). Some explanation about them can be found in [Writing Kibana 4 Plugins – Basics](https://www.timroes.de/2015/12/02/writing-kibana-4-plugins-basics/) by Tim Roes. Although it mentions Kibana 4, I've found the procedure basically the same as for current (as of May 2016) Kibana master HEAD, which is close to what will be Kibana 5.

I'm working with Debian testing, but I guess the procedure would work the same for other Linux-based distros. I have already installed the Oracle Java 8 environment (jre-8-oracle-x64), which is needed by ElasticSearch (see below).

In short, the procedure is as follows:

### Install the nvm tool

Install the nvm tool, if you don't have it already installed, for handling the installation of a specific version of node.js. You can skip this one if you already have the needed node.js, but probably it is a good idea to use nvm, so that you can keep separate versions of nvm if you need some other for other projects. Installation instructions for nvm are in their [README.markdown file](https://github.com/creationix/nvm/blob/master/README.markdown). I opted for manually installing from the git repo:

```
git clone https://github.com/creationix/nvm.git nvm
cd nvm
git checkout `git describe --abbrev=0 --tags`
```

The latest command (note the inverted quotes) checks out the latest tag.

Now, when I want to "activate" the nvm environment (which will allow for installing an specific version of node.js), I just source the nvm.sh script (from the cloned directory):

```
source nvm/nvm.sh
```

This will "activate" (prepare for installing / running an specific node.js environment) only the shell where the script is sourced. I prefer to use this than to modify my '~/.bashrc', because I want to control when I'm using it. But in this case, I better don't forget to run it before installing node.js modules or running Kibana...

### Clone the Kibana git repository:

The source code for Kibana is in its git repository:

```
git clone https://github.com/elastic/kibana.git kibana
```

This will produce the kibana directory.

### Install node.js and dependencies

Now, in the kibana directory, I will run nvm to install the appropriate version of node.js, and then npm to install the required JavaScript dependencies. Before that, I "activate" nvm (see above):

```
source ../nvm/nvm.sh
nvm install "$(cat .node-version)"
npm install
```

### Install and run ElasticSearch

Two options here. You can install a binary version of ElasticSearch which is compatible with the version of Kibana that we will run, or use npm to retrieve and install the specific ElasticSearch version as specified by the Kibana configuration.

* First option: ElasticSearch binary.

In my case, the current release candidate (or almost) for ElasticSearch 5 is appropriate. It is elasticsearch-5.0.0-alpha2 and can be installed from https://www.elastic.co/downloads/elasticsearch

After installation I get a elasticsearch directory, and I only have to run elassticsearch in it:

```
cd elasticsearch
bin/elasticsearch
```

In my case, I'm short on disk space. ElasticSearch comes configured with a "watermark" of 90% for disk space, which means that if the disk is more than 90% busy, it won't work as it should. Fortunately, I can change this in the config/elasticsearch.yml file, just by adding the following lines:

```
cluster.name: jgbarah
cluster.routing.allocation.disk.watermark.low: 95%
cluster.routing.allocation.disk.watermark.high: 98%
```

`cluster.name` is not really needed, but just in case... (it just gives a name to the ElasticSearch cluster)

* Second option: install using npm.

In this case, the appropriate version of ElasticSearch will be installed and run automatically. I have to remember to "activate" the nvm environment in the shell where I want ElasticSearch to be installed and run. The following commands are run in the kibana directory (the cloned git repository for Kibana):

```
source ../nvm/nvm.sh
npm run elasticsearch
```

`npm run elasticsearch` uses  is [esvm](https://github.com/simianhacker/esvm) to run ElasticSearch once it is installed, which is installed as `esvm/dev/branch-master`. Therefore, after running that npm command, next times you want to run it without reinstalling, you can just run

```
esvm/dev/branch-master/bin/elasticsearch
```

If you need to change the ElasticSearch configuration file, as I did for the watermark stuff (see above), you can change `esvm/dev/branch-master/config/elasticsearch.yml`, but have in mind that it will be overwritten if you reinstall ElasticSearch by running npm as shown above.

Usually, you'll leave ElasticSearch running in one shell while you run Kibana itself in some other (See below). You can check that ElasticSearch is running by pointing your browser to http://localhost:9200 which will cause it to display something similar to:

```
{
  "name" : "Solarr",
  "cluster_name" : "jgbarah",
  "version" : {
    "number" : "5.0.0-alpha2",
    "build_hash" : "e3126df",
    "build_date" : "2016-04-26T12:08:58.960Z",
    "build_snapshot" : false,
    "lucene_version" : "6.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

### Running Kibana

This is simple stuff (remember to activate nvm in the shell before running the command). In the kibana directory:

```
source ../nvm/nvm.sh
npm start
```

This will launch Kibana. You can browse it at https://localhost:5601

Warning: messages by Kibana may suggest that it is listening at https://localhost:5603 (port 5603), but it is listening at port 5601 instead.
