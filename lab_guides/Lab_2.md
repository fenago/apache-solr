

[]{#ch02}Chapter 2. Getting Started {#chapter-2.-getting-started .title}
-----------------------------------

</div>

</div>
:::

::: {.blockquote}
> [*\"Information is the oil of the 21st Century, and analytics is the
> combustion engine.\"*]{.emphasis}
:::

-- [*Peter Sondergaard*]{.emphasis}

Apache Solr is one tool that has some common features such as full-text
search, facets-based search, clustering on demand, the ability to select
highlighters, indexing on demand, database integration, geospatial
searches, NoSQL features, fault tolerance, scaling on demand, and the
ability to handle all sorts of documents (such as PDF, docx, ppt, and
more). Solr finds its intense use in enterprise searches and huge
analytics. Due to such powerful features, major players in the world
such as Instagram, Netflix, NASA, and eBay are using Solr.

Solr runs as a standalone server with the option of having clusters as
per demand. Internally, it makes use of the Lucene Java search library
and has full support of REST like HTTP/XML and JSON APIs. This makes it
possible to use most of the available programming languages even without
Java coding. You feed Solr data over HTTP and Solr responds with an
output in formats of JSON, CSV, and binary. Apache Lucene and Apache
Solr were merged in 2010 and they are collectively termed Lucene/Solr or
Solr/Lucene. This chapter focuses mainly on the following points:

::: {.itemizedlist}
-   Solr installation
-   Understanding various files and the folder structure
-   Running and configuring Solr
-   Loading sample data
-   Understanding the browser interface
-   Using the Solr admin interface



[]{#ch02lvl1sec15}Solr installation {#solr-installation .title style="clear: both"}
-----------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Let\'s get up and running[]{#id288622898 .indexterm} with Solr. At the
time of writing of this book, the latest stable version of Solr was
7.1.0. This book focuses all its aspects on Apache Solr 7.1.0.

Getting up and running with Apache Solr requires the following
prerequisites:

::: {.itemizedlist}
-   Java 8 (mandatory)
-   Cygwin (optional, recommended for Windows)
-   curl (optional, recommended)
:::

So, you will necessarily require JRE version 1.8 or higher for Solr to
run. Go ahead! Open Command Prompt, type the following command, and
check your Java version:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
java -version
```
:::

It should show the following output:

::: {.mediaobject}
![](2_files/e7753bbc-17c6-4ba8-9752-e09629ac74b2.png)
:::

If your output is something different, it means Java is not properly
installed and it needs to be installed properly. Also, the
`PATH`{.literal} variable should point to JRE 1.8. You can
download[]{#id288626547 .indexterm} Java
from <http://www.oracle.com/technetwork/java/javase/downloads/index.html>
if needed.

Cygwin and curl utilities are not mandatory but will be helpful when we
go deeper.

The next step is to download Apache Solr. An important point to keep in
mind is that Apache Solr can also run as a standalone unit and is
operating system independent. It would be the same across the operating
systems; the difference would be in the way we reach there. To install
Solr, follow along steps:

::: {.orderedlist}
1.  Go to <http://lucene.apache.org/solr/>.
2.  Click on **`DOWNLOAD`**.
:::

::: {.orderedlist}
3.  You will be redirected to
    <http://www.apache.org/dyn/closer.lua/lucene/solr/7.1.0>.
4.  Select the mirror site, which will open up the following page:
:::

::: {.mediaobject}
![](2_files/d348364e-c01c-4103-94eb-255b9a76fb41.jpg)
:::

::: {.orderedlist}
5.  If you are on Windows, download the `solr-7.1.0.zip`{.literal} file
    and extract it to a location of your choice.
6.  If you are on Linux/Unix or macOS, download the `.tgz`{.literal}file
    and extract it to the local home directory:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
wget http://www-eu.apache.org/dist/lucene/solr/7.1.0/solr-7.1.0.tgz

cd ~/

tar zxf solr-7.1.0.tgz
```
:::

The same command should[]{#id288377002 .indexterm} work for macOS also,
provided the `wget`{.literal} utility is installed. If not, you can use
another alternative.

::: {.orderedlist}
7.  For macOS, if you don\'t have `wget`{.literal} installed, you can
    use the native `brew`{.literal} command:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
brew install solr

solr start
```
:::

Irrespective of the way you install Solr for your OS, the extracted
location should be something like this, with the folder structure as
follows:

::: {.mediaobject}
![](2_files/feb03e12-8d55-4fa2-95e5-fec6b8bcacd7.jpg)
:::

Now that we have Solr installed, the next step is to get it up and
running. Navigate to the `bin`{.literal} folder, which contains the Solr
startup script. Open the terminal and hit the following command, which
will open up an interactive prompt screen, as shown in the following
screenshot:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
solr -e cloud
```
:::

::: {.mediaobject}
![](2_files/1bddc411-3d2a-4ede-934b-37e9447ca940.jpg)
:::

Let\'s look at the following steps:

::: {.orderedlist}
1.  The very first question it asks us is the number of nodes we want to
    run in parallel for Solr. Let\'s keep it at `2`{.literal} since
    `2`{.literal} is the default. Just press [*Enter*]{.emphasis}
    without typing anything.

2.  Next, we need to specify the port on which Solr will run. The
    default ports are `8983`{.literal} and `7574`{.literal}. Change the
    port if they are already occupied, or simply press
    [*Enter*]{.emphasis} to go ahead with the default ports.

3.  Next, it will ask us to provide the name of a collection in which we
    want to add data. Let\'s go with the default one.

4.  The `bin/solr`{.literal} and `bin/solr.cmd`{.literal} contains Solr
    preconfigurations. When we start Solr, it asks us whether we want to
    change those configurations. We can simply press
    [*Enter*]{.emphasis} to keep the preconfigurations as default, or we
    can type in to define our own configurations.
:::

If everything goes well, you should see the following screen:

::: {.mediaobject}
![](2_files/a2d726bd-f003-4af8-bf7e-df25dd86f01c.jpg)
:::

You can now navigate either to `http://localhost:8983/solr/#/`{.literal}
or `http://localhost:7574/solr/#/`{.literal} as you have started Solr in
cluster mode. You should be able to see the following admin interface:

::: {.mediaobject}
![](2_files/31b17a18-1f0a-4550-818a-017a08776b8c.jpg)
:::

What did we just do? While running this command, we started Solr in
cloud mode. When we specified the number of hosts, port number, and
collection name, it created an initial configuration set. Solr comes
with an example configuration set, which we can use[]{#id288377259
.indexterm} with cloud mode. It can be found at:
`$SOLR_HOME/server/solr/configsets`{.literal}.

If you navigate to that folder, you will be able to see
the `_default`{.literal} and `sample_techproducts_configs`{.literal}
config sets.

However, cloud mode is not the only mode Solr comes with. Solr comes
with tailor made configuration sets, which we can use if they fit our
purpose. The following config sets are available in Solr 7.1.0 and they
can be viewed at `$SOLR_HOME/example`{.literal}. If you navigate to that
folder, you will be able to find config sets for an instance
of `cloud`{.literal}, `example-DIH`{.literal}, `films`{.literal}, and
`techproducts`{.literal}.

To start Solr with any of the example config sets, you have to navigate
to the `bin`{.literal} directory and type the following command in the
terminal:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
solr start -e <name_of_example_config_set>
```
:::

Now that we are up and running with Solr, let\'s look at the folder
structure to get a deeper idea of Solr.


[]{#ch02lvl1sec16}Understanding various files and the folder structure {#understanding-various-files-and-the-folder-structure .title style="clear: both"}
----------------------------------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Let\'s now understand the[]{#id288339996 .indexterm} constituents of
Solr, its directory structure, and the significance of each folder and
the configurations involved. This diagram shows the parent-level
directory of Solr, along with an explanation of each folder:

::: {.mediaobject}
![](3_files/5598bebb-eb3c-449e-9d78-3b0e9e25263a.jpg)
:::

Let\'s look at the major folders and files we will be dealing with.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec23}bin {#bin .title}

</div>

</div>
:::

The bin folder has all the[]{#id288376070 .indexterm} scripts primarily
needed to get up and running with Solr. Mainly, we will be using Solr
and post scripts for our day-to-day purposes. It is also the location to
place the replication scripts for more advanced setup, if needed. The
`bin`{.literal} folder is home to the following utilities: it contains
the `solr.in.sh`{.literal} and `solr.in.cmd`{.literal} files, from where
you can provide input parameters to Solr. It has
the `install_solr_service.sh`{.literal} script and
the `oom_solr.sh`{.literal} and `init.d`{.literal} folders, if you want
to run it as a service on any Linux/Unix service.

Since we will be using Solr scripts very often, it is advisable to add
`$SOLR_HOME/bin`{.literal} (for
example, `E:\solr-7.1.0\solr-7.1.0\bin`{.literal}) in the path
environment variable so that we can use it anywhere.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec24}Solr script {#solr-script .title}

</div>

</div>
:::

The Solr script in this[]{#id288377139 .indexterm} folder is used for
various utilities for managing Solr. You can pass various parameters to
Solr from here. Now we will see a list of some of the things you can do
with Solr script. Go to the `bin`{.literal} folder and execute the
commands in the terminal to check the output. Based on the operating
system, use the `.cmd`{.literal} or `.sh`{.literal} version accordingly:

::: {.itemizedlist}
-   If you are on Windows, use the following command:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin\solr.cmd start
```
:::

::: {.itemizedlist}
-   If you are on Linux, use this command:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin/solr.sh start
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec25}Post script {#post-script .title}

</div>

</div>
:::

Solr, by default, includes a post[]{#id288377328 .indexterm} script for
indexing various kinds of documents directly to the Solr server. This
utility is very helpful when you have huge files present and you want to
end up writing a program that reads each line and then sends it to the
Solr server via HTTP end points.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip3}Note {#note .title}

To run this utility on Windows, please install Cygwin. Otherwise, you
will have to navigate to
`$solr_home\example\exampledocs\post.jar`{.literal}.
:::

The following screenshot helps us to understand how we can execute a
post script from the command line:

::: {.mediaobject}
![](3_files/690de324-f8e7-4980-84ae-469ac708e351.jpg)
:::

This table consists of some example commands used for the same purpose:

::: {.informaltable}
  --------------------------------------------------------------- ---------------------------------------------------------------------------------------------
  [**Command**]{.strong}                                          [**Description**]{.strong}
  `post -c chintan *.xml`{.literal}                               This adds all files with extension `.xml`{.literal} to the core or collection named chintan
  `post -c chintan -d '<delete><id>42</id></delete>'`{.literal}   Deletes a document from the chintan collection or core that has ID `42`{.literal}
  `post -c chintan *.csv`{.literal}                               Indexes all CSV files with auto field mappings on
  `post -c chintan *.json`{.literal}                              Indexed all JSON files
  `post -c chintan *.pdf`{.literal}                               Indexes all PDF files
  `post -c chintan abc/`{.literal}                                Indexes all files inside the `abc`{.literal} folder
  `post -c chintan -filetypes ppt,html abc/`{.literal}            Indexes only PPT and HTML files inside the `abc`{.literal} folder
  --------------------------------------------------------------- ---------------------------------------------------------------------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec26}contrib {#contrib .title}

</div>

</div>
:::

The `contrib`{.literal} folder is one where[]{#id288617261 .indexterm}
you will be able to see all of Solr\'s contribution modules. All the
extensions to Solr, which do advanced things beyond core Solr, can be
found here.

Note that the runnable Java files of all of these extensions can be
found in the `dist`{.literal} folder; here it\'s just the source and the
`README.md`{.literal} files.

Some of the useful extensions are as follows.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch02lvl3sec2}DataImportHandler {#dataimporthandler .title}

</div>

</div>
:::

`DataImportHandler`{.literal} is an import[]{#id288617289 .indexterm}
tool for Solr that helps in importing data from databases, XML files,
and HTTP data sources very smoothly and easily. The data sources for
importing go beyond relational databases and cover filesystems,
websites, emails, FTP servers, NoSQL databases, LDAP, and so on. You can
set default locales, time zones, or charsets via this extension. You can
find two JAR files of this extension in the `dist`{.literal} folder:
`solr-dataimporthandler-7.1.0.jar`{.literal} and
`solr-dataimporthandler-extras-7.1.0.jar`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch02lvl3sec3}ContentExtractionLibrary {#contentextractionlibrary .title}

</div>

</div>
:::

This contrib module provides[]{#id288622840 .indexterm} a way to extract
and index content contained in rich documents, such as Word, Excel, PDF,
and so on. This module uses Apache Tika (a toolkit that extracts
metadata and text from over a thousand different files). This contrib
module provides automatic MIME type detection so that it can use that
based on the file provided.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch02lvl3sec4}LanguageIdentifier {#languageidentifier .title}

</div>

</div>
:::

This module is meant to be used[]{#id288622855 .indexterm} while
indexing documents with a multitude of languages. The implementation of
this document is such that it creates an `UpdateProcessor`{.literal},
which can be placed in `UpdateChain`{.literal}. It identifies the
language and tags that document with that `languageId`{.literal}. For
example, if the input is `surname`{.literal} and it detects English as
the language, then it will be `surname_en`{.literal}. Language detection
can be per field or for the whole document.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch02lvl3sec5}Clustering {#clustering .title}

</div>

</div>
:::

This module is used[]{#id288377384 .indexterm} when we want to add
third-party implementations. At the time of writing this book, it
provides clustering support for search results[]{#id288377394
.indexterm} using the Carrot2 project (<https://project.carrot2.org/>).
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch02lvl3sec6}VelocityIntegration {#velocityintegration .title}

</div>

</div>
:::

This contrib helps[]{#id288377413 .indexterm} us to integrate Solr with
Apache Velocity. It gives us a writer that, behind the scenes, uses the
Apache Velocity template engine on the GUI to render Solr responses.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec27}dist and docs {#dist-and-docs .title}

</div>

</div>
:::

The `dist`{.literal} folder contains all the[]{#id288377431 .indexterm}
distributions that can[]{#id288377440 .indexterm} be used as deployment
artifacts in other servers. It contains the main Solr file, which is
`solr-core.7.1.0.jar`{.literal}. This folder also contains JAR files of
all the contribs we discussed earlier. You can deploy these artifacts to
any application server as per your needs.

The `docs`{.literal} folder contains online documentation for all the
help needed in Solr. It has Wiki Docs, new changes in the current
version, minimum system requirements, tutorials, Lucene documentation,
and Java API docs for all the contrib modules.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec28}example {#example .title}

</div>

</div>
:::

This directory contains[]{#id288377498 .indexterm} all Solr examples and
config sets that are provided by default. Each example is self-contained
in a separate directory. It contains a fully self-contained Solr
installation. It has a sample configuration, some documents, and
ZooKeeper data.

Solr provides the following sample data out of the box:

::: {.itemizedlist}
-   `films`{.literal}
-   `files`{.literal}
-   `exampledocs`{.literal}
:::

Solr provides the following config sets:

::: {.itemizedlist}
-   `schemaless`{.literal}
-   `DIH`{.literal}
-   `techproducts`{.literal}
:::

Let\'s look at one such directory structure of an out-of-the-box example
of Solr `cloud`{.literal}:

::: {.mediaobject}
![](3_files/276a41df-5385-4103-a9fe-d53bfe4ae026.jpg)
:::

Here is the detailed directory structure of our out-of-the-box example
of Solr `cloud`{.literal} :

::: {.itemizedlist}
-   The parent-level directory contains `node1`{.literal} and
    `node2`{.literal}, stating that Solr has been started in cluster
    mode for this config set.
-   Each node contains two folders, `solr`{.literal} and
    `logs`{.literal}.
-   The `solr`{.literal} folder contains two
    folders, `test_shard1_replica_n1`{.literal} and
    `zoo_data`{.literal}. Since we have started Solr in clusters, it
    creates one replication folder, `test_shard1_replica_n1`{.literal}.
    It contains the `data`{.literal} folder and one
    file, `core.properties`{.literal}, which has information about the
    other node.
-   Apache Solr comes with embedded ZooKeeper. `zoo_data`{.literal} is
    the ZooKeeper data directory.
-   In the `solr`{.literal} directory, there are two configuration
    files: `solr.xml`{.literal} and `zoo.cfg`{.literal}.
-   `solr.xml`{.literal} has some global configuration options that
    apply to all or defined cores.
-   `zoo.cfg`{.literal} contains the ZooKeeper-related files.
:::

Let\'s look at all the configuration files that we would be using on a
day-to-day basis.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec29}core.properties {#core.properties .title}

</div>

</div>
:::

The core.properties contains[]{#id288377635 .indexterm} some of the
following properties, which we can configure as per our needs for our
Solr core:

::: {.informaltable}
  --------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------
  [**Property**]{.strong}     [**Description**]{.strong}
  `name`{.literal}             The name of the Solr core. Whenever you need to reference `SolrCore`{.literal} with `CoreAdminHandler`{.literal}, you will need this name.
  `config`{.literal}          Configuration filename and path. By default, this is `solrconfig.xml`{.literal}.
  `schema`{.literal}          Schema filename of a core.
  `dataDir`{.literal}         Data directory where indexes are stored.
  `configSet`{.literal}       The config set that should be picked up to configure the core.
  `properties`{.literal}      The properties file path for this core.
  `transient`{.literal}       This decides whether the core can be unloaded or not if Solr reaches `transientCacheSize`{.literal}.
  `loadOnStartUp`{.literal}   This decides whether or not to load the core on startup.
  `coreCodeName`{.literal}    A unique identifier for the node hosting the core. It can be helpful when you are replacing a machine that has had a hardware failure.
  `ulogDir`{.literal}         Directory for the update log.
  `Shard`{.literal}           The name of the shard to which the core would be assigned.
  `Roles`{.literal}           The name of the collection of the core in which you will index data.
  --------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec30}zoo.cfg {#zoo.cfg .title}

</div>

</div>
:::

This contains[]{#id288340132 .indexterm} some of the following
properties, by which we can configure for ZooKeeper:

::: {.informaltable}
  --------------------------------------- ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  [**Property**]{.strong}                 [**Description**]{.strong}
  `tickTime`{.literal}                    The `tickTime`{.literal} decides actually how long `eachTick`{.literal} has to be. This part of ZooKeeper determines which servers are up and running at any point in time by sending ticks.
  `initLimit`{.literal}                   The amount of time in ticks for a forwarder to connect to the leader. It\'s the number of ticks that can be allowed for the initial synchronization phase to take place.
  `syncLimit`{.literal}                   The amount of time that can be allowed for followers to keep in sync with ZooKeeper. If the followers cross this limit and yet don\'t sync up, they will be dropped.
  `dataDir`{.literal}                     This is the directory in which cluster data information would be stored in ZooKeeper. It should initially be empty.
  `clientPort`{.literal}                  This is the port at which Solr will access ZooKeeper; when this file is in place, you can start a ZooKeeper instance.
  `maxClientCnxns`{.literal}              The maximum number of client connections you want to handle.
  `autopurge.snapRetainCount`{.literal}   The number of snapshots to retain in the data directory.
  `Autopurge.purgeInterval`{.literal}     The purge task interval in hours. After this much time, it will start the retain task.
  --------------------------------------- ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip4}Note {#note-1 .title}

It is not recommended to use embedded ZooKeeper in production.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec31}solr.xml {#solr.xml .title}

</div>

</div>
:::

The `solr.xml`{.literal} contains Solr configuration that[]{#id288340276
.indexterm} can apply to single or multiple cores as needed. The
following table briefly summarizes the configurations available in this
file:

::: {.informaltable}
  --------------------------------- ----------------------------------------------------------------- ---------------------------------------------------------------------------------------------
  [**Parent element**]{.strong}     [**Property**]{.strong}                                           [**Description**]{.strong}
  `solrCloud`{.literal}             `distribUpdateConnTimeout`{.literal}                              Used to define the connection timeout limit for intra-cluster updates
  `solrCloud`{.literal}             `distribUpdateSoTimeout`{.literal}                                Used to define the socket time for intra-cluster updates
  `solrCloud`{.literal}             `host`{.literal}                                                  The hostname that Solr will use to access cores
  `solrCloud`{.literal}             `hostContext`{.literal}                                           The context path
  `solrCloud`{.literal}             `hostPort`{.literal}                                              The port that Solr will use to access cores
  `solrCloud`{.literal}             `genericCoreNodeNames`{.literal}                                  Decides whether the node names should be based on the address of the node or not
  `solrCloud`{.literal}             `zkClientTimeout`{.literal}                                       The timeout limit for connection to the ZooKeeper server
  `solrCloud`{.literal}             `zkCredentialsProvider`{.literal} and `zkACLProvider`{.literal}   The parameters used for ZooKeeper access control
  `solrCloud`{.literal}             `leaderVoteWait`{.literal}                                        The Solr node will wait for this much time for all known replicas of that shard to be found
  `solrCloud`{.literal}             `leaderConflictResolveWait`{.literal}                             The maximum time the replica will wait to see conflicting state information to be resolved
  `shardHandlerFactory`{.literal}   `socketTimeOut`{.literal}                                         The read time out for querying among intra-clusters
  `shardHandlerFactory`{.literal}   `connTimeOut`{.literal}                                           The connection timeout for intra-cluster queries and administrative requests
  `shardHandlerFactory`{.literal}   `urlScheme`{.literal}                                             The `urlScheme`{.literal} to be used in distributed search
  `shardHandlerFactory`{.literal}   `maxConnectionsPerHost`{.literal}                                 Maximum connections allowed per hosts
  `shardHandlerFactory`{.literal}   `maxConnections`{.literal}                                        Maximum total connections allowed
  `shardHandlerFactory`{.literal}   `corePoolSize`{.literal}                                          Initial core size of threadpool servicing requests
  `shardHandlerFactory`{.literal}   `maximumPoolSize`{.literal}                                       Maximum size of threadpool servicing requests
  `shardHandlerFactory`{.literal}   `maxThreadIdleTime`{.literal}                                     The amount of time in seconds that a thread persists in queue before getting killed
  --------------------------------- ----------------------------------------------------------------- ---------------------------------------------------------------------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec32}server {#server .title}

</div>

</div>
:::

This directory contains an instance[]{#id288340631 .indexterm} of the
Jetty servlet and a container[]{#id288340640 .indexterm} setup to run
Solr. Given here is the `server`{.literal} directory layout:

::: {.itemizedlist}
-   `server/contexts`{.literal}: This contains[]{#id288340659
    .indexterm} the Jetty web application deployment descriptors
    required by the Solr web app.
-   `server/etc`{.literal}: This contains[]{#id288340672 .indexterm}
    Jetty configurations and has an example SSL keystore.
-   `server/lib`{.literal}: This has Jetty and other[]{#id288340720
    .indexterm} third-party libraries that are needed. It has folder
    `ext`{.literal}; any external JAR you want you can keep there.
-   `server/logs`{.literal}: This has the[]{#id288340736
    .indexterm} Solr log files.
-   `server/solr-webapp`{.literal}: This contains files used by
    the[]{#id288340748 .indexterm} Solr server. As Solr is not a Java
    web application, don\'t edit the files in this directory.
-   `server/solr`{.literal}: This is the[]{#id288340761 .indexterm}
    default `solr.home`{.literal} directory where Solr will create the
    core directories. It must contain `solr.xml`{.literal}.
-   `server/resources`{.literal}: This must[]{#id288340780 .indexterm}
    contain configuration files such as various Log4j configurations
    (`log4j.properties`{.literal}) required for configuring Solr
    loggers.
-   `server/scripts/cloud-scripts`{.literal}: This is the[]{#id288340797
    .indexterm} command-line utility for working with ZooKeeper when we
    are running Solr with SolrCloud mode. You can check
    out `zkcli.sh`{.literal} or `zkcli.bat`{.literal} for usage
    information.
-   `server/solr/configsets`{.literal}: Contains various[]{#id288340818
    .indexterm} directories; they contain configuration options
    essential for running Solr.
-   `_default`{.literal}: This has some[]{#id288340830 .indexterm} bare
    minimum configurations, with the options of field guessing and
    managed schema turned on by default so as to start indexing data in
    Solr without having to design any schema upfront. Schema management
    can be done via the REST API as you refine your index.
-   `sample_techproducts_configs`{.literal}: This has
    example[]{#id288340843 .indexterm} configurations that show many
    powerful features based on the use case of building a search
    solution for tech products.
:::

Now that we have a detailed idea about Solr\'s directory and file
structure, let us get acquainted with running and configuring Solr for
our needs.



[]{#ch02lvl1sec17}Running Solr {#running-solr .title style="clear: both"}
------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Let\'s start with running and[]{#id288339943 .indexterm} configuring our
Solr. We will see several ways of running Solr, some configurations
needed for Solr to be in production mode, and more. The following topics
will be covered:

::: {.itemizedlist}
-   Solr startup
-   Production-level Solr setup and configurations
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec33}Running basic Solr commands {#running-basic-solr-commands .title}

</div>

</div>
:::

Earlier, we started Solr in interactive[]{#id288339965 .indexterm} mode,
where we picked up the cloud as the default config set. Let\'s revisit
the Solr startup commands.

If you want to start Solr with a custom port, then you can use the
following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin\solr.cmd start -p 8984
```
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip5}Note {#note .title}

Based on your operating system, you have to use `bin/solr`{.literal} or
`bin\solr.cmd`{.literal}.
:::

Similar to `start`{.literal}, there\'s a command `stop`{.literal}. To
stop Solr, simply use the following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin\solr.cmd stop -all
```
:::

The `-all`{.literal} parameter stops all Solr instances; if you want to
stop a specific port, then pass that port number with an argument with
parameter `-p`{.literal}. Let\'s say you start Solr with any key; then
you can stop that particular instance by passing the key as a parameter:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin\solr.cmd stop -k <key_name>
```
:::

As seen earlier, you can launch examples by passing the `-e`{.literal}
flag:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin\solr.cmd -e <example_name>
```
:::

Let\'s start Solr in cloud mode; hitting `bin\solr.cmd -e`{.literal}
cloud will start the interactive process as follows:

::: {.itemizedlist}
-   Specifying the number of nodes on which we would like to run Solr in
    the local cluster
-   Port number for each Solr instance
-   Creating a Solr instance home directory with node 1, node 2, and
    node 3
-   Specifying the name for a new collection
-   Specifying the shards, the new collection should split
-   The replicas per shard we want to create
-   The configuration for collection which we want
:::

After this, the config API will be called and updated with the selected
configurations. Then you can see the Solr running status on hitting
`http://localhost:8983/solr/#/~cloud`{.literal}. It should show
something like this:

::: {.mediaobject}
![](4_files/21b61f80-33b1-4ce1-9df0-53e2f85afdb3.jpg)
:::

Now, to check the status of the running instances of Solr, it provides
the command status. Let\'s check out the number of Solr instances we
have up and running:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin\solr.cmd status
```
:::

The following screenshot is the output that shows the status of the Solr
service, with a few more details such as `uptime`{.literal},
`solr_home`{.literal}, `memory`{.literal}, and so on:

::: {.mediaobject}
![](4_files/a24b3170-adc4-44d3-bd85-77a964281e03.jpg)
:::

Now, if you haven\'t started Solr with example config sets, you will
find no core created. Simply put, a core is just like a database in an
SQL table. To create[]{#id288626550 .indexterm} a new core at any time,
just use the following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin\solr.cmd create -c <name>
```
:::

This command essentially creates a core or collection based on whether
Solr is running in standalone (core) mode or SolrCloud (collection)
mode. Based on the running instance of Solr, this command does its
action.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip6}Note {#note-1 .title}

By default, any new core created follows the `_default`{.literal} config
set, which is not a recommended practice in production.
:::

If you want to delete a collection, just invoke the following rest
point:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
 http://localhost:8983/solr/admin/collections?action=DELETE&name=<name_of_collection>
```
:::

Now let\'s add some sample documents to our collection. I have some PDF
documents on microservices and Node.js, and I am indexing in Solr. Open
up your terminal.

If you are on Linux, hit the following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin/post -c chintan <path_to_documents>/*.pdf
```
:::

If you are on Windows, do this:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
java -Dauto -Dc=chintan -jar post.jar E:\\books\\solr\\chintan\\*.pdf
```
:::

In both cases, it looks for all the PDF documents in the folder, indexes
them, and adds them into the collection or core: `chintan`{.literal}.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note7}Note {#note-2 .title}

If you want to use the post utility in Windows, download Cygwin and try
the same command as in Linux.
:::

Now that the documents are successfully indexed, let\'s verify by asking
Solr questions. Open up the terminal and query collection getting
started by asking questions[]{#id288376978 .indexterm} about the query
parameter `nodejs`{.literal} by hitting the following end point, which
would be
`http://localhost:8983/solr/gettingstarted/select?q=nodejs`{.literal}.

You should get a response like this:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{"responseHeader":{ "zkConnected":true, "status":0, "QTime":22, "params":{ "q":"nodejs"}}, "response":{"numFound":2,"start":0,"maxScore":2.283722,"docs":[ {},{}]
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec34}Production Solr setup {#production-solr-setup .title}

</div>

</div>
:::

Now that we are done playing[]{#id288377008 .indexterm} around in Solr,
let\'s take a step further and look into installing Solr as a service.
This is specifically useful in servers and Linux systems. Follow these
steps:

::: {.orderedlist}
1.  The Solr bundle you downloaded earlier contains the `solr`{.literal}
    directory, which includes a shell script
    `bin/install_solr_service.sh`{.literal}. This will help to install
    Solr as a service on Linux. As some prerequisites, it will need the
    path where you want to install Solr and the user who should be
    owning Solr, that is, privileges.

2.  While installing Solr, make sure that the `indexed`{.literal}
    directory and `logs`{.literal} directory stay out of the
    `solr`{.literal} installation folder; this will be very helpful when
    we upgrade Solr.

3.  The Solr service script, by default, extracts the archive in
    `/opt`{.literal}. If you want to change that path, just add
    the `-i`{.literal} option while running the Solr script. You can
    install the Solr service by simply hitting:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
sudo bash ./install_solr_service.sh solr-7.1.0.tgz
```
:::

It will install in the `opt`{.literal} folder and also create a symbolic
link:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
/opt/solr --> /opt/solr-7.1.0
```
:::

This helps in easy and smooth upgrades.

::: {.orderedlist}
4.  The data directory should be different; the script takes care of it.
    By default, it adds a directory at `/var/solr`{.literal}, but if you
    want to change that, you can pass a location using `-d`{.literal}.
5.  Running Solr as a root user is not recommended. This script creates
    a Solr user by default, so `/var/solr`{.literal} and
    `/opt/solr`{.literal} can be fully owned by the Solr admin. No need
    to give them root privileges!
:::

Now that the script is installed, you can start/stop Solr at any time
with the following commands:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
 sudo service solr start
 sudo service solr stop
```
:::

So after getting up and running with basic utilities in Solr, covering
the various available options in Solr, and getting to install and run a
Solr service in Linux, let us now play with data. We will load some
sample data and understand the various ways to load and query data.


[]{#ch02lvl1sec18}Loading sample data {#loading-sample-data .title style="clear: both"}
-------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Now that we\'ve got acquainted[]{#id288339995 .indexterm} with Solr and
the various commands involved in Solr\'s day-to-day usage, let\'s
populate it with data so that we can query it as needed. Solr comes with
sample data in examples. We will use the same
`$solr_home/example/films`{.literal} for our queries.

Fire up the terminal and create a collection `films`{.literal} with
`10`{.literal} shards:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
 solr\bin create -c films -shards 10
```
:::

Now, in `$solr_home/example/films`{.literal} there is file called
`file.json`{.literal}. Let\'s import it into our collection,
`films`{.literal}. Based on your OS, hit the appropriate command for the
post script or `post.jar`{.literal}.

Uh Oh!! It throws an error, as follows:

::: {.mediaobject}
![](5_files/2607e86c-1e5d-48af-a0a7-a84f4c94aac4.jpg)
:::

What must have gone wrong? By checking the logs while creating the
collection, you must have seen a warning.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note8}Note {#note .title}

**`Warning`** Using `_default`{.literal} config set data driven schema
functionality is enabled by default, which is not recommended for
production use.
:::

To turn it off, use the following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
curl http://localhost:8983/solr/test11/config -d '{"set-user-property": {"update.autoCreateFields":"false"}}'
```
:::

While creating the collection, we went on with the `_default`{.literal}
config set. The `_default`{.literal} config set does two things: we make
use of the managed schema meaning, the schema can be modified only
through Solr\'s schema API. We don\'t specify the field mapping and let
the config set to do the guessing. It may seem advantageous at one point
because we don\'t have to restrict Solr to know any pre-fields and we
can adopt the concept of schemaless. Solr will create fields on demand
as it encounters documents. Now, this very advantage had become a
problem. If you open up `films.json`{.literal} and check the name of the
first field, you will see it as `.45`{.literal}. Solr, guessing it as
Float type, keeps the datatype of the filmname as Float; the moment it
encounters text, it spits out the error. We end up with huge trouble as
we can\'t change the mapping fields once the index contains data.

For the same reason, it is not recommended to go with the
`_default`{.literal} schema config set. So, let\'s solve this issue by
leveraging the schema API to modify our schema definition.

Delete the films collection by hitting the rest point:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
http://localhost:8983/solr/admin/collections?action=DELETE&name=films
```
:::

Create the collection again using the following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin\solr.cmd -c films -shards 10 -n schemaless
```
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note9}Note {#note-1 .title}

We will be passing `-n`{.literal} so that it will pick up the schemaless
configuration instead of the previous default configuration.
:::

Now make a post call with the following details, which will basically
tell Solr to expect a field\'s name as a text rather than letting it
auto-guess as Float:

::: {.informaltable}
  -------------------------- -------------------------------------------------------------------------------------------------------
  [**End point**]{.strong}   `http://localhost:8983/solr/films/schema`{.literal}
  [**Header**]{.strong}      [**`{“Content-Type”:application/json}`{.literal}**]{.strong}
  [**Body**]{.strong}        `{"add-field": {"name":"name", "type":"text_general", "multiValued":false, "stored":true}}`{.literal}
  -------------------------- -------------------------------------------------------------------------------------------------------
:::

 

You should get a successful response.

Now try hitting the import command
again:`java -Dauto -Dc=films -jar post.jar E:\solr-7.1.0\solr-7.1.0\example\films\films.json`{.literal}

You should be able to do so successfully. Go to the browser and open
`http://localhost:8983/solr/films/select?q=*:*`{.literal}. You should be
able to see 1,100 records. You can similarly import CSV and XML files.

While importing a CSV file, you need to specify that if a field has
multiple values, it should split; and you need to specify what the
separator would be. If you check out `films.csv`{.literal}, then you
will notice that genres and `directed_by`{.literal} are such fields. In
this case, our import command would be:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
java -jar -Dc=films -Dparams=f.genre.split=true & f.directed_by.split=true &  f.genre.separator=| & f.directed_by.separator=| -Dauto example\exampledocs\post.jar example\films\*.csv
```
:::

This is how we can load unstructured[]{#id288601054 .indexterm} data in
Solr. Let\'s now look at how to load structured data in Solr.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec35}Loading data from MySQL {#loading-data-from-mysql .title}

</div>

</div>
:::

Solr\'s contrib provides[]{#id288601074 .indexterm}
the `datahandlerimport`{.literal} module, and one[]{#id288605562
.indexterm} of the examples in Solr is also focused on
`DataImportHandler`{.literal}, also known as DIH. Let\'s run the DIH
example given by default. Hit `solr -e dih`{.literal} to start Solr with
the example of DIH. It will pick the configuration set in
`$solr_home/example/example-DIH`{.literal}[**.**]{.strong} Create sample
data in MySQL as follows:

::: {.mediaobject}
![](5_files/672e9ef9-5137-4ae9-be15-fc34619fb8fb.jpg)
:::

Now it\'s time to integrate it into Solr. Follow these steps to find out
the necessary configurations:

::: {.orderedlist}
1.  As stated earlier, any Solr installation would
    require the `solrconfig.xml`{.literal} file, which has the necessary
    config information. This file usually resides
    in `$solr_home/<config_set_selected>/conf`{.literal}. As we have
    selected `-e dih`{.literal} and we are specifically looking for the
    database utility, we will find our `solrconfig.xml`{.literal} at
    `$solr_home/example/example-DIH/solr/db/conf/solrconfig.xml`{.literal}.
    Add the following lines of code in this file.
:::

Since we are going to use MySql, we need to download the MySQL connector
JAR
([http://central.maven.org/maven2/mysql/mysql-connector-java/8.0.8-dmr/mysql-connector-java-8.0.8-dmr.jar](http://www.google.com/url?q=http%3A%2F%2Fcentral.maven.org%2Fmaven2%2Fmysql%2Fmysql-connector-java%2F8.0.8-dmr%2Fmysql-connector-java-8.0.8-dmr.jar&sa=D&sntz=1&usg=AFQjCNGO3122cUUPDB2JY_F8OyrFY1Lr0Q){.ulink}),
place it in the `dist`{.literal} folder `$solr_home/dist`{.literal}, and
let Solr know where to find the connector JAR by adding the following
lines of code. Make sure that the `dataimporthandler`{.literal} module
is available in `dist`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-dataimporthandler-.*\.jar" />
  <lib dir="${solr.install.dir:../../../..}/contrib/extraction/lib" regex=".*\.jar" />
  <lib dir="${solr.install.dir:../../../..}/dist" regex="mysql-connector-java-\d.*\.jar" />
```
:::

::: {.orderedlist}
2.  Next, we need to tell Solr where our database configuration file is.
    Add the following lines of code and create a file
    `db-data-config.xml`{.literal} parallel to
    `solrconfig.xml`{.literal}:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<requestHandler name="/dataimport" class="solr.DataImportHandler">
    <lst name="defaults">
      <str name="config">db-data-config.xml</str>
    </lst>
  </requestHandler>
```
:::

::: {.orderedlist}
3.  `db-data-config.xml`{.literal} is the file where we will define our
    database and Solr mapping. Create one file and define that file
    with the following schema:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<dataConfig>
    <dataSource driver="com.mysql.jdbc.Driver"
          url="jdbc:mysql://localhost:3306/chintan" user="root" password="root" />
         <document>
         <entity name="archives" query="select * from archives"
 deltaImportQuery="SELECT * from archives WHERE category_id='${dih.delta.id}'">
 <field column="category_id" name="category_id" />
 <field column="category_name" name="category_name" />
 <field column="remarks" name="remarks" />
 </entity>
 </document>
 </dataConfig>
```
:::

::: {.orderedlist}
4.  The last part is to let Solr know about the new entity we have
    added. Go to
    `${solr_home}/example/example-DIH/solr/db/conf/managed-schema`{.literal}
    and make the following changes. Change the primary key from
    `id`{.literal} to `category_id`{.literal}:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<field name="category_id" type="string" indexed="true" stored="true" required="true" multiValued="false" />
 …
 <uniqueKey>category_id</uniqueKey>
```
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip10}Note {#note-2 .title}

This is just a method to show how to import data from MySQL, and hence
we are changing the primary key. In the real world, doing this is
strongly not recommended.
:::

::: {.orderedlist}
5.  Now let\'s add the new fields you introduced in
    `db-data-config.xml`{.literal}:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<field name="category_name" type="string" indexed="true" stored="true"/>
<field name="remarks" type="string" indexed="true" stored="true"/>
```
:::

That\'s done. Now restart the server by hitting
`solr/bin stop -all && solr/bin start -e dih`{.literal}.

Go to the Solr admin panel. On the core selector, select the database
and follow the steps shown in this screenshot:

::: {.mediaobject}
![](5_files/a347a745-fcda-41af-8242-d7bb57d349e1.png)
:::

You can hit on the browser to see MySQL data in Solr.

Let\'s look at ways[]{#id288378472 .indexterm} to browse
data[]{#id288378481 .indexterm} in Solr through the browse interface.


[]{#ch02lvl1sec19}Understanding the browse interface {#understanding-the-browse-interface .title style="clear: both"}
----------------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Now that our Solr is loaded up with[]{#id288339943 .indexterm} data, we
will look at multiple queries and the browse interface, through which we
can query without actually knowing the end points. The data provided in
`techproducts`{.literal} includes a wide variety of fields along with
geospatial indexes. So let\'s use that for a change. Open up the
terminal and hit `bin\solr.cmd -e techproducts -p 4202`{.literal}. As we
have loaded a sample `techproducts`{.literal} config set, it will import
a bunch of files into the collection while starting up the server.

Once the server is up and running, hit
`http://localhost:4202/solr/techproducts/browse`{.literal} to check out
the browse interface provided by Solr, as shown in the following
screenshot:

::: {.mediaobject}
![](6_files/c86e54f6-6293-4ff5-b2bd-3aa143dba6cd.png)
:::

It is just another Google search for electronics now. Go ahead and type
the query string parameter. It will autocomplete and display the results
as needed.

The following screenshot explains the browse interface in a lot of
detail:

::: {.mediaobject}
![](6_files/afd8ee02-b2a6-428a-9cff-5bdb91152e02.jpg)
:::

This feature makes use of the solariatis[]{#id288377120 .indexterm}
velocity contrib, which we saw earlier. This is useful for generating
textual (it may or may not be HTML) from the Solr request.



[]{#ch02lvl1sec20}Using the Solr admin interface {#using-the-solr-admin-interface .title style="clear: both"}
------------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Solr provides a web interface for feasibility[]{#id288339995 .indexterm}
of Solr administrators and programmers to perform the following
operations easily:

::: {.itemizedlist}
-   View Solr configuration details
-   Run different queries against indexes
-   Analyze document fields to fine-tune the Solr config set
-   Provide a schema browser for easy querying
-   Show the java properties of each core
:::

And much more\... Under the hood, Solr reuses the same HTTP APIs that
are available to all clients. Let us look at the Solr admin panel in
detail. Go and start up Solr with the config set we created earlier.
Accessing `http://localhost:7574/solr`{.literal} or
`http://localhost:8983/solr`{.literal} will open up the Solr admin
dashboard. The whole screen is divided into two parts:

::: {.itemizedlist}
-   The left part showing the navigational menu
-   The right part showing the interface selected menu
:::

Let\'s take a deep dive into each of the sections.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec36}Dashboard {#dashboard .title}

</div>

</div>
:::

This is the default page that opens[]{#id288377114 .indexterm} up
whenever we open Solr. The dashboard contains various pieces of
information:

::: {.itemizedlist}
-   [**Instance details**]{.strong}: Tells us when[]{#id288377186
    .indexterm} the instance was started.
-   [**Versions information**]{.strong}: This gives us detailed
    information about the Solr and Lucene specs and implementation
    versions.
-   [**JVM information**]{.strong}: This details out[]{#id288377205
    .indexterm} information about JVM. It covers the JVM runtime
    environment found, processors, and arguments supplied to Solr. All
    the configurational arguments passed in various Solr config files
    can be seen combined here.
-   [**System**]{.strong}: This shows a pictorial[]{#id288377219
    .indexterm} view of the system status occupied right now and how
    much is available. The first and last gray bars show the physical
    and JVM memory available. The first is a measure of the amount of
    memory available in the hosting machine. The second shows the amount
    assigned to the startup of Solr in units of `-Xms`{.literal} and
    `-Xmx`{.literal} options.
-   [**JVM memory**]{.strong}: This shows[]{#id288377239 .indexterm}
    info about the JVM memory and the memory and percentage that Solr
    occupies at any point in time.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec37}Logging {#logging .title}

</div>

</div>
:::

Logs are a way to know[]{#id288377255 .indexterm} what\'s going in the
system at any point in time. When you click on logging, a screen similar
to the following screenshot comes up. This is the interface where
real-time logs of Solr are shown. It even supports multi-core logs. You
can see various log levels, time, message, the core from which it comes,
and so on:

::: {.mediaobject}
![](7_files/9c38c623-df6e-4314-907a-2437216b9c01.jpg)
:::

Here are a few highlights of the logging details of our out-of-the-box
Solr example:

::: {.itemizedlist}
-   It shows logs based on time, level, core, logger class, and message.
-   Some of the messages have an informative icon at the end. Clicking
    on it prints the stack trace.
-   There are different log levels. The red ones are error level logs,
    and the yellow ones are warning level logs.
:::

Solr provides a way to change the log level at any running system
instance. You can adjust the level of logs for any class. The various
options for the log level you can select are all, trace, debug, info,
warn, error, fatal, off, and unset.

You can change the log levels of any class at any point of the running
instance.

When you open up the level in Solr, you will see all of the hierarchy of
class paths and class names. Any class that has logging capabilities
would be marked in yellow. Click on the highlighted row and you will be
able to change the log level of that class.

One interesting parameter you will see is unset. Any category that is
unset will have log levels of its parents. This simple feature allows us
to change many categories at once by just changing the log level of the
parent:

::: {.mediaobject}
![](7_files/cd7ba97d-4d19-4e26-9d74-288a43fd2772.jpg)
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip11}Note {#note .title}

This is a runtime setting and is not persisted, so if you open Solr
again, it will be lost.
:::

This isn\'t the only way we can change the log levels. Log levels can
also be changed in the following ways:

::: {.itemizedlist}
-   Using the log level API as mentioned here:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
# Set the root logger to level WARN curl -s http://localhost:8983/solr/admin/info/logging --data-binary "set=root:WARN"
```
:::

::: {.itemizedlist}
-   Log levels can also be selected at startup. Again, this can be done
    in two ways:
    ::: {.itemizedlist}
    -   Search for the string `SOLR_LOGS_LEVEL`{.literal} in
        `bin/solr.in.cmd`{.literal} or `bin\solr.in.sh`{.literal}.
        Change it as needed.
    -   The second alternative is to pass at startup with the
        `-v`{.literal} or `-q`{.literal} options. For example:
    :::
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin\solr start -f -v
bin\solr start -f -q
```
:::

::: {.itemizedlist}
-   A more permanent solution[]{#id288377405 .indexterm} can be to
    change `log4j.properties`{.literal} directly, which can be found at
    `$solr_home/server/resources`{.literal}.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec38}Cloud screens {#cloud-screens .title}

</div>

</div>
:::

When we run Solr in cloud mode, a cloud[]{#id288377428 .indexterm}
option will appear between logging and collections. This screen gives us
the details of each collection and node in the cluster. It gives
information about the level data stored in ZooKeeper. An important point
to note is that this option is not visible when a single node is running
or master-slave replication instances of Solr are running. It provides
three different views: tree, graph, and graph (radial).

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch02lvl3sec7}Tree view {#tree-view .title}

</div>

</div>
:::

This shows the directory structure[]{#id288377443 .indexterm} of data
residing in ZooKeeper and according to ZooKeeper configurations. It has
cluster-wide information such as the number of live nodes, overseer, and
overseer election status. It also has collection-specific information,
such as `state.json`{.literal} (which has a definition of the
collection), the shard leaders, and the configuration files in use.

::: {.mediaobject}
![](7_files/26288f44-26d7-483e-a79b-0d8a8172d4a4.jpg)
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch02lvl3sec8}Graph view {#graph-view .title}

</div>

</div>
:::

This shows a graph of each[]{#id288377470 .indexterm} collection, the
number of shards that constitute that collection, and the address of
each of the replicas of that shard. At the bottom of the screen, it
shows labels for the leader shard, active shards, recovering shards,
failed shards, inactive shards, and gone shards:

::: {.mediaobject}
![](7_files/5b7316c8-915e-4d2f-b799-5474f06e93c0.jpg)
:::

As you can see, there are multiple collections: `films`{.literal},
`gettingstarted`{.literal}, `chintan`{.literal}, `pictures`{.literal},
and so on. Most of the shards are active but notice a difference in
the `gettingstarted`{.literal} collection. Its shard 2 node has one
blacked-out circle and another empty circle. This means that it is just
an active node and not the leader. Also, if you check out shard 1 of
`gettingstarted`{.literal}, you will see a faint color; this means that
this shard is no longer active.

Now let\'s look at some of the core admin stuff that we will be using in
day-to-day life. The next section of collections is just a visual
interface for the available collections API. Behind the scenes, this
interface calls the very same APIs exposed over the rest. As a matter of
fact, the Solr admin UI is made in AngularJS.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec39}Collections or core admin {#collections-or-core-admin .title}

</div>

</div>
:::

This screen provides some[]{#id288377758 .indexterm} of the basic
functionalities for managing[]{#id288377767 .indexterm} collections in
Solr, which runs the collections API under the hood.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip12}Note {#note-1 .title}

Based on the number of instances you are running, you will see the
option as collections or core admin. If you are running a single
instance, you will see the option as core admin, which runs
`CoreAdminApi`{.literal} under the hood.
:::

Clicking on **`Collections`** for the first time opens the list of
collections you have. Clicking on any of the collections will give you
metadata about it, which has various options such as shard count, config
set name, and so on. It enables various options, as seen in this
diagram:

::: {.mediaobject}
![](7_files/12735590-17e7-4bf2-aaf1-2615d5d17695.jpg)
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec40}Java properties {#java-properties .title}

</div>

</div>
:::

With this screen, you can see[]{#id288377838 .indexterm} all configured
properties of JVM, which is running Solr. It includes the class path,
encoding type, external libraries, and so on.

::: {.mediaobject}
![](7_files/7eff47af-f345-4799-b030-950403f13cb7.jpg)
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec41}Thread dump {#thread-dump .title}

</div>

</div>
:::

This is the screen that lets you[]{#id288377863 .indexterm} view and
analyze the active threads on the server in Solr. Each thread is
mentioned with a number and the stacktrace access if applicable. Each
icon preceding the thread name indicates the state of the thread. A
thread can be in any one of `new`{.literal}, `runnable`{.literal},
`blocked`{.literal}, `waiting`{.literal}, `timed_waiting`{.literal} or
`terminated`{.literal} states.

::: {.mediaobject}
![](7_files/ba87b90a-5033-494d-abc9-bb5efb020e8e.jpg)
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec42}Collection-specific tools {#collection-specific-tools .title}

</div>

</div>
:::

Clicking on any one of the[]{#id288377940 .indexterm} collections in the
collection dropdown opens up the various things that we can do on any
collection. It has the following suboptions.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch02lvl3sec9}Overview {#overview .title}

</div>

</div>
:::

This just contains basic metadata about the[]{#id288377955 .indexterm}
collection: the number of shards, the replica per shard, the range of
each shard, the config set of the instance, and whether auto-add
replicas is enabled or not.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch02lvl3sec10}Analysis {#analysis .title}

</div>

</div>
:::

This is the[]{#id288377970 .indexterm} screen that helps us analyze data
in the collections. We can inspect the field, field type, and
configurations in our schema; furthermore, we will be able to know how
the content would be applied during indexing or query processing. Let\'s
analyze one such query that gives us a detailed output, as follows:

::: {.mediaobject}
![](7_files/d6c2aa53-9e5d-4611-921e-beda44f07746.jpg)
:::

This screen is useful to study the analyzers applied on a collection.
Analysis can be done in various phases where transformations such as
uppercase, lowercase, singular/plural, synonyms, tenses, and so on will
be taken into consideration.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec43}DataImport {#dataimport .title}

</div>

</div>
:::

We saw this screen when we imported from MySQL. This screen allows us to
monitor the status of all the import commands and the[]{#id288377998
.indexterm} entities that we have defined in `managed_schema`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec44}Documents {#documents .title}

</div>

</div>
:::

This screen allows us to directly[]{#id288378017 .indexterm} run various
Solr indexing commands right from the browser. You can do the following
set of tasks:

::: {.orderedlist}
1.  Copy documents in any format, select the document type, and then
    index
2.  Upload documents
3.  Construct documents by the process of selecting field type and field
    values
:::

::: {.mediaobject}
![](7_files/1326134b-7082-4985-857d-1407472d9211.jpg)
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec45}Files {#files .title}

</div>

</div>
:::

The **`Files`** screen lets us browse and view various configuration and
language-related files. These files cannot be edited with this screen.
If you want[]{#id288378054 .indexterm} to edit them, you have to visit
the **`Schema Browser`** screen.

::: {.mediaobject}
![](7_files/37d6b45e-a67c-48cb-a2a4-f3cfc8cac9ea.jpg)
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec46}Query {#query .title}

</div>

</div>
:::

You use this screen to query your[]{#id288378081 .indexterm} existing
collections. Various kinds of queries are available in Solr, right from
normal queries to geospatial queries and faceted search.

Let\'s do a simple faceted query on genres in our `films`{.literal}
collection. Go to the **`Query`** screen; at the bottom, click on
**`facet`**, and in `facet.field`{.literal}, enter `genre`{.literal}.
Click on **`execute query`**:

::: {.mediaobject}
![](7_files/96f3e767-616c-41d6-b2b6-e5234d0c431d.jpg)
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec47}Stream {#stream .title}

</div>

</div>
:::

The streaming screen helps[]{#id288378124 .indexterm} you understand the
explanation of a query. It is very similar to the **`Query`** screen; it
just adds an explanation part.

::: {.mediaobject}
![](7_files/d90aa8cb-d00c-4d19-bf36-195abe346fd8.png)
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec48}Schema {#schema .title}

</div>

</div>
:::

This screen lets us view[]{#id288378185 .indexterm} the schema in a
browser window. It provides in-depth information about each of the
fields and its field type. It has options to add dynamic fields and copy
field mapping from one field to another.

::: {.mediaobject}
![](7_files/d46695ba-b102-4854-95d5-82f0010e15d5.jpg)
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch02lvl2sec49}Core-specific tools {#core-specific-tools .title}

</div>

</div>
:::

These are a group of UI utilities that give[]{#id288378210 .indexterm}
information about the core level. On selecting a **`Core`** in the
dropdown, you will see the following screens:

::: {.itemizedlist}
-   **`Overview`**: This will display some of the basic metadata about
    the running Solr core. You can add a file
    `admin-extra.html`{.literal}, which consists of additional
    information if you would like to display in the
    **`Admin Extra block.`**
-   **`Ping`**: This lets you send a ping to the selected core to
    determine whether the core is active or not.
-   **`Plugins / Stats`**: Shows all the plugins installed and
    statistics. The performance factor of the Solr cache can be checked
    here.
:::

::: {.mediaobject}
![](7_files/29ca8e54-1570-4381-b086-bf431af5ac60.jpg)
:::

::: {.itemizedlist}
-   **`Segments info`**: This lets you see visualizations of different
    segments by Lucene for the core selected. It shows information about
    the size of each segment, with units in both bytes and number of
    documents. You can also check out the number of deleted documents.



[]{#ch02lvl1sec21}Summary
-------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In this chapter, we got started with Solr. We saw the various options
available to start Solr and saw the directory and folder structure of
Solr in detail. We learned how to run Solr as service, Solr and
ZooKeeper configurations, how to load some sample data from documents
and databases, the browse interface, and the Admin UI in detail.

Let\'s move on to designing a schema in the next chapter. We will learn
how to design it using documents and fields. We will go through various
field types and see the schema API. We will also look at the schemaless
mode.

 