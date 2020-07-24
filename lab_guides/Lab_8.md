

[]{#ch08}Chapter 8. Managing and Fine-Tuning Solr {#chapter-8.-managing-and-fine-tuning-solr .title}
-------------------------------------------------

</div>

</div>
:::

Okay, so you have your brand new car up and running and have been using
it judiciously day in and day out, but you don\'t maintain it properly
from time to time! What will happen? Of course, the performance is going
to deteriorate over a period of time. Another thing could be that your
car supports automatic parking but you never found out how to override
the default setting. In such a case, a manual comes in handy for
learning and tweaking all the features that your car can provide.
Similarly, you need to fine-tune and manage your Solr so as to get the
most out of it. This is exactly what we are going to see in this
chapter.



[]{#ch08lvl1sec55}JVM configuration {#jvm-configuration .title style="clear: both"}
-----------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

One of the things that you need to take particular care of when you are
working on any Java-based application is configuring the JVM optimally,
and Solr is no exception.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec113}Managing the memory heap  {#managing-the-memory-heap .title}

</div>

</div>
:::

Anyone who has worked[]{#id288380761 .indexterm} with Java-based
applications would[]{#id288599639 .indexterm} have surely come across
setting the heap space. We do it using `-Xms`{.literal} and
`-Xmx`{.literal}. Suppose I set following the command-line option:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
-Xms256m-Xmx2048m
```
:::

Here, `Xms`{.literal} specifies our initial memory allocation pool,
whereas `Xmx`{.literal} specifies the maximum memory allocation pool for
JVM. In the case we just saw, our JVM will start with 256 MB of memory
and will be able to use up to 2 GB of memory.

If we require more heap space, then we can[]{#id288601120 .indexterm}
increase `-Xms`{.literal}. We can also decide not to give any initial
heap space at all and let JVM use the heap space as per the need, but
this may increase our startup time. Similarly, failing to set up the
maximum heap size properly can result
in `OutOfMemoryException`{.literal}. Proper garbage collection JVM
parameters should be set so that JVM can optimally try to reclaim any
available space that already exists in the heap. Also, the size of the
heap plays a crucial role in garbage collection. The larger the heap
size, the more the time spent by JVM to do garbage collection, leading
to [*stop the world*]{.emphasis} conditions.



[]{#ch08lvl1sec56}Managing solrconfig.xml {#managing-solrconfig.xml .title style="clear: both"}
-----------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

As we already know now, `solrconfig.xml`{.literal} forms
the[]{#id288339955 .indexterm} heart of Solr when it comes to
configuring Solr.

There are two ways in which this file is modified:

::: {.itemizedlist}
-   By making direct changes in `solrconfig.xml`{.literal}
-   Using the config API to create `configoverlay.json`{.literal}, which
    holds configuration overlays to modify the default values specified
    in `solrconfig.xml`{.literal}
:::

The `solrconfig.xml`{.literal} file is used to configure the admin web
interface. It can be used to change parameters for replication and
duplication. We can change the request dispatcher too using
`solrconfig.xml`{.literal}. Various listeners and request handlers can
be configured using `solrconfig.xml`{.literal}.

Go to any of the conf directories for a collection and you will find
`solrconfig.xml`{.literal} inside. Navigate to
`SOLR_HOME/server/solr/configsets`{.literal} and you will see various
configurations that follow best practices for configuring Solr.

Solr allows you to specify a variable for the property value, which can
be replaced at runtime with the following syntax:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
${propertyname[:default value]}
```
:::

Doing so will allow a default, which can be overridden when Solr is
started. If we don\'t specify a default value, then it we should make
sure that the property is specified at runtime or else we will get an
error.

For example, take a look at this config:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
 <autoCommit>
<maxTime>${solr.autoCommit.maxTime:15000}</maxTime>
     <openSearcher>false</openSearcher>
 </autoCommit>
```
:::

In the preceding snippet, we have specified to keep the[]{#id288376069
.indexterm} maximum time for doing a hard commit as 15 seconds.

This can be changed at runtime:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin/solr start -Dsolr.autoCommit.maxTime=20000
```
:::

In this way, we can set any Java system property at runtime.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec114}User-defined properties {#user-defined-properties .title}

</div>

</div>
:::

We can also add `solrcore.properties`{.literal} in the[]{#id288626516
.indexterm} configuration directory to specify user-defined properties
that can be set in `solrconfig.xml`{.literal}. 

For example, `solr.autoCommit.maxTime`{.literal} can be added to
`solrcore.properties`{.literal}.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note31}Note {#note .title}

The `solrcore.properties`{.literal} is deprecated in cloud mode.
:::

On the use of APIs, the Solr core will have its
`core.properties`{.literal} automatically created. In the case of the
SolrCloud collection, we can submit our own parameters, which will go
into the respective `core.properties`{.literal}. The only thing to make
sure is prefixing the parameter name with `property`{.literal} as a URL
parameter:

`http://localhost:8983/solr/admin/collections?action=CREATE&name=gettingstarted&numShards=1&property.user.name=Dharmesh Vasoya`{.literal}

 This will create `core.properties`{.literal} with the following entry:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
user.name=Dharmesh Vasoya
```
:::

Now this property can be used in `solrconfig.xml`{.literal} as
`${user.name}`{.literal}.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch08lvl3sec63}Implicit Solr core properties {#implicit-solr-core-properties .title}

</div>

</div>
:::

The following properties are available as implicit
properties[]{#id288628873 .indexterm} for the Solr core:

::: {.itemizedlist}
-   `solr.core.config`{.literal}
-   `solr.core.dataDir`{.literal}
-   `solr.core.loadOnStartup`{.literal}
-   `solr.core.name`{.literal}
-   `solr.core.schema`{.literal}
-   `solr.core.transient`{.literal}
:::

Since implicitly we do not need to specify them in
`core.properties`{.literal}, but they are implicitly available to be
used in `solrconfig.xml`{.literal}.



[]{#ch08lvl1sec57}Managing backups {#managing-backups .title style="clear: both"}
----------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Going into production, we obviously[]{#id288341307 .indexterm} need a
proper backup and restore plan. The last thing we would want is for our
hard disk to crash and all our index data to disappear or get corrupted.

Solr provides two ways to back up based on how you are running it:

::: {.itemizedlist}
-   Collections API in SolrCloud mode
-   Replication handler in standalone mode
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec115}Backup in SolrCloud {#backup-in-solrcloud .title}

</div>

</div>
:::

As mentioned earlier, using the collections API, we can take
backups[]{#id288341332 .indexterm} in SolrCloud. Doing so will ensure
that the backups are generated across multiple shards; and then, at the
time[]{#id288376081 .indexterm} of restore, we use the same number of
shards and replicas as the original collection. The commands are listed
here:

::: {.informaltable}
  ----------------------------- ------------------------------------------------
  [**Command name**]{.strong}   [**Description**]{.strong}
  `action=BACKUP`{.literal}     Used to back up Solr indexes and configuration
  `action=RESTORE`{.literal}    Used to restore Solr indexes and configuration
  ----------------------------- ------------------------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec116}Standalone mode backups {#standalone-mode-backups .title}

</div>

</div>
:::

In the case of standalone mode, backups and restoration[]{#id288601121
.indexterm} are done using replication handler. The configuration of
replication handler can be customized using our[]{#id288605568
.indexterm} own replication handler in `solrconfig.xml`{.literal};
however, we will use the out-of-the-box implicit support for replication
using the API.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch08lvl3sec64}Backup API {#backup-api .title}

</div>

</div>
:::

In order to back up, we will use[]{#id288617157 .indexterm} the
following command to the core that we would like to take a backup of:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
http://localhost:8983/solr/myschema/replication?command=backup&name=mybackup
```
:::

Here, `myschema`{.literal} is the name of the core that we are working
with and `/replication`{.literal} is the handler to the backup. In the
end, you can see we\'ve specified `command=backup`{.literal} to back up
our core.

The `backup`{.literal} command will bring data from the last committed
index. At any point in time, there can be only one backup call being
made. Otherwise, we will get an exception if there is a backup process
already going on.

A backup request can have the following parameters:

::: {.informaltable}
  ------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  [**Parameter name**]{.strong}   [**Description**]{.strong}
  `location`{.literal}            The location for the backup. Unless you specify an absolute path, everything will be relative to Solr\'s instance directory. The snapshot will be created in a directory named `snapshot.<name>`{.literal}. If you don\'t specify the name, then the directory name will have a timestamp as the suffix, such as `snapshot.<yyyyMMddHHmmssSSS>`{.literal}.
  `numberToKeep`{.literal}        Defines the number of backups. You cannot use this parameter if you have already defined `maxNumberOfBackups`{.literal} in `solrconfig.xml`{.literal}.
  `repository`{.literal}          Defines the name of the repository to be used for the backup. The default will be the filesystem repository.
  `commitName`{.literal}          Defines the name of the commit that was used while taking a snapshot with the `CREATESNAPSHOT`{.literal} command.
  ------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch08lvl3sec65}Backup status {#backup-status .title}

</div>

</div>
:::

We can monitor the backup operation to check[]{#id288341337 .indexterm}
the current status using the following call:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
http://localhost:8983/solr/myschema/replication?command=details&wt=xml
```
:::

The output will be something like this: 

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<lst name="backup">
    <str name="startTime">Tue Jan 30 14:32:12 DAVT 2018</str>
    <int name="fileCount">15</int>
    <str name="status">success</str>
    <str name="snapshotCompletedAt">Tue Jan 30 14:32:12 DAVT 2018</str>
    <str name="snapshotName">demobackup</str>
</lst>
```
:::

If there is a failure, then we will get `snapShootException`{.literal}
in the response.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch08lvl3sec66}API to restore {#api-to-restore .title}

</div>

</div>
:::

Similar to taking[]{#id288341370 .indexterm} backups, the restore API
requires `command=restore`{.literal} to be sent to the replication
handler on the core, as follows:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
http://localhost:8983/solr/gettingstarted/myschema?command=restore&name=demobackup
```
:::

Executing the preceding command will restore the index snapshot named
`demobackup`{.literal} in the current core. Once the data is fully
restored, searches will start reflecting from the restored data.

A restore request can have the following parameters:

::: {.informaltable}
  ------------------------------- --------------------------------------------------------------------------------------------------------------
  [**Parameter name**]{.strong}   [**Description**]{.strong}
  `location`{.literal}            The location of the backup. Unless you specify this, it will look for a backup in Solr\'s data directory.
  `name`{.literal}                Defines the name of the backed-up index snapshot to be restored.
  `repository`{.literal}          Defines the name of the repository to be used for the backup. The default will be the filesystem repository.
  ------------------------------- --------------------------------------------------------------------------------------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch08lvl3sec67}Restore status API {#restore-status-api .title}

</div>

</div>
:::

Similar to the backup status API, we have[]{#id288341456 .indexterm} a
restore status API to check the restore status:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
http://localhost:8983/solr/myschema/replication?command=restorestatus&wt=xml
```
:::

The output of the API will be as follows:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<response>
    <lst name="responseHeader">
        <int name="status">0</int>
        <int name="QTime">0</int>
    </lst>
    <lst name="restorestatus">
        <str name="snapshotName">snapshot.<name></str>
        <str name="status">success</str>
    </lst>
</response>
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch08lvl3sec68}Snapshot API {#snapshot-api .title}

</div>

</div>
:::

We can create, restore, and list[]{#id288341484 .indexterm} snapshots of
the index using APIs. 

The details are shown in tabular format as follows:

::: {.informaltable}
+--------------------+-----------------------+-----------------------+
| [**API**]{.strong} | [**URL**]{.strong}    | [**P                  |
|                    |                       | arameters**]{.strong} |
+--------------------+-----------------------+-----------------------+
| Create snapshot    | `ht                   | ::: {.itemizedlist}   |
|                    | tp://localhost:8983 / | -   `c                |
|                    | solr/admin /cores?act | ommitName`{.literal}: |
|                    | ion=CREATESNAPSHOT& c |     The parameter\'s  |
|                    | ore=myschema&commitNa |     name to be used   |
|                    | me=commit1`{.literal} |     for storage       |
|                    |                       | -   `core`{.literal}: |
|                    |                       |     The name of the   |
|                    |                       |     core              |
|                    |                       | -                     |
|                    |                       |    `async`{.literal}: |
|                    |                       |     The request ID to |
|                    |                       |     track this        |
|                    |                       |     action, which     |
|                    |                       |     will be processed |
|                    |                       |     asynchronously    |
|                    |                       | :::                   |
+--------------------+-----------------------+-----------------------+
| List snapshot      | `h                    | ::: {.itemizedlist}   |
|                    | ttp://localhost:8983  | -   `core`{.literal}: |
|                    | /solr/admin /cores?ac |     The name of the   |
|                    | tion=LISTSNAPSHOTS& c |     core              |
|                    | ore=myschema&commitNa | -                     |
|                    | me=commit1`{.literal} |    `async`{.literal}: |
|                    |                       |     The request ID to |
|                    |                       |     track this        |
|                    |                       |     action, which     |
|                    |                       |     will be processed |
|                    |                       |     asynchronously    |
|                    |                       | :::                   |
+--------------------+-----------------------+-----------------------+
| Delete snapshot    | `http://              | `core`{.literal}:     |
|                    | localhost:8983 /solr/ | The name of the core  |
|                    | admin /cores?action=D |                       |
|                    | ELETESNAPSHOT& core=t | `async`{.literal}:    |
|                    | echproducts& commitNa | The request ID to     |
|                    | me=commit1`{.literal} | track this action,    |
|                    |                       | which will be         |
|                    |                       | processed             |
|                    |                       | asynchronously        |
|                    |                       |                       |
|                    |                       | `c                    |
|                    |                       | ommitName`{.literal}: |
|                    |                       | To specify the        |
|                    |                       | `                     |
|                    |                       | commitName`{.literal} |
|                    |                       | to be deleted         |
+--------------------+-----------------------+-----------------------+



[]{#ch08lvl1sec58}JMX with Solr {#jmx-with-solr .title style="clear: both"}
-------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

[**Java Management Extensions**]{.strong} ([**JMX**]{.strong}) is a
technology[]{#id288339959 .indexterm} that was released in the J2SE 5.0
release; it provides tools for managing and monitoring resources
dynamically at runtime. It is used in enterprise applications to make
configurable systems and get the state of an enterprise application at
any point of time. The resources are[]{#id288339968 .indexterm}
represented by [**managed beans**]{.strong} ([**MBeans**]{.strong}).

Solr can be controlled via the JMX interface; we can make use of
VisualVM or JConsole to connect with Solr.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec117}JMX configuration {#jmx-configuration .title}

</div>

</div>
:::

Solr will automatically identify its location[]{#id288339991 .indexterm}
on startup if you have an MBean server running in Solr\'s JVM or if you
start Solr with the `Dcom.sun.management.jmxremote`{.literal} system
property.

Alternatively, you can configure by defining a metrics reporter.

On a remote Solr server, if you need to do JMX-enabled Java profiling,
then you have to enable remote JMX access when starting the Solr server.

Open `solr.in.cmd`{.literal} or `solr.in.sh`{.literal} in the
`SOLR_HOME/bin`{.literal} directory and set
the `ENABLE_REMOTE_JMX_OPTS`{.literal} property to `true`{.literal},
along with `RMI_PORT=18983`{.literal}, to enable remote JMX access.



[]{#ch08lvl1sec59}Logging configuration {#logging-configuration .title style="clear: both"}
---------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Setting up logs is a key part of any[]{#id288341307 .indexterm}
enterprise application and Solr is no exception. Luckily, Solr provides
many different ways to tweak the default logging configuration.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec118}Log settings using the admin web interface {#log-settings-using-the-admin-web-interface .title}

</div>

</div>
:::

Using Solr\'s admin web interface, we can set[]{#id288341322 .indexterm}
various log levels. Go to the admin interface by typing the following
URL:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
http://localhost:8983/solr/
```
:::

You should see the following admin screen:

::: {.mediaobject}
![](6_files/e0c03976-7305-4ebc-aca6-039c278b09a5.png)
:::

You will see that on the left-hand side, there is a **`Logging`**
option. Click on it and there will be a submenu item called **`Level`**,
which will open up the following screen:

::: {.mediaobject}
![](6_files/7f2b38e1-17e5-45c2-bec5-4b1b9635d304.png)
:::

Here, we can set the logging level for many different log categories in
a hierarchical order. For example, let\'s say I want to set
`org.apache.http.conn.ssl`{.literal} to log level and set all the
subcategories under it to debug level; I will click on the edit icon
next to `ssl`{.literal}, as shown here:

::: {.mediaobject}
![](6_files/d557d238-539c-423a-ba57-f984a4836959.png)
:::

This will open up a small popup with various log levels that we can set.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip32}Note {#note .title}

Any log level set in this manner will be lost during the next restart of
the server.
:::

Various log level settings are listed here:

::: {.itemizedlist}
-   **`ALL`**: Logs everything
-   **`TRACE`**: Logs everything but leaves the least important messages
-   **`DEBUG`**: Logs debug-level messages
-   **`INFO`**: Logs info-level messages
-   **`WARN`**: Logs all warning messages
-   **`ERROR`**: Logs all errors
-   **`FATAL`**: Logs every fatal message
-   **`OFF`**: Removes logging
-   **`UNSET`**: Removes the previously selected logging option
:::

You can also set[]{#id288628846 .indexterm} the log level using an API:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
curl -s http://localhost:8983/solr/admin/info/logging --data-binary "set=root:WARN"
```
:::

The preceding `curl`{.literal} command sets the `root`{.literal}
category to the `WARN`{.literal} level.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec119}Log level at startup {#log-level-at-startup .title}

</div>

</div>
:::

There are two other ways to set[]{#id288628877 .indexterm} up logging
temporarily:

::: {.itemizedlist}
-   Setting the environment variable
-   Pass parameters in the startup script
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch08lvl3sec69}Setting the environment variable {#setting-the-environment-variable .title}

</div>

</div>
:::

The first option is to set[]{#id288340005 .indexterm} the environment
variable `SOLR_LOG_LEVEL`{.literal} at startup or put the variable in
`SOLR_HOME/bin/solr.in.sh`{.literal} in the case of Linux
and `SOLR_HOME/bin/solr.in.cmd`{.literal} in the case of Windows.

The values will be one of the log levels that were mentioned earlier.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch08lvl3sec70}Passing parameters in the startup script {#passing-parameters-in-the-startup-script .title}

</div>

</div>
:::

You can start Solr with parameters telling[]{#id288340032 .indexterm}
how much logging would you like:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin/solr start -f -v
```
:::

The preceding script, which has parameter `-v`{.literal}, says to start
Solr with verbose logging:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin/solr start -f -q
```
:::

The preceding script, which has parameter `-q`{.literal}, says to start
Solr with quiet logging or print `WARN`{.literal} level or more severe
logs.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec120}Configuring Log4J for logging {#configuring-log4j-for-logging .title}

</div>

</div>
:::

All the logging solutions that[]{#id288340245 .indexterm} we have seen
so far are non-permanent settings and are good only until the next
restart of the server. To make permanent log changes, we resort to
Log4J, which Solr uses for its logging needs.

The Log4J configuration file in our case is the
`log4j.properties`{.literal} file, which is located in the
`SOLR_HOME/server/resources`{.literal} directory. All logs are written
to the `SOLR_HOME/server/logs`{.literal} directory. This path can also
be changed using the environment variable `SOLR_LOGS_DIR`{.literal}. 

Those who have worked with Log4j may know that we can also change
`log4j.properties`{.literal}  to set various logging configurations.
There are many settings to decide where you want to print the log, the
size of the file generated, what kind of appenders or patterns should be
used, and so on.

When we start Solr with the `-f`{.literal} option, which stands for
foreground, all the logs are written to the console along with the log
file. If we are not using the foreground option, then all the logs will
be written to `solr[port]-console.log`{.literal}.

As mentioned earlier, the log level, the size of the log file, and the
logging policy can be changed using `log4j.properties`{.literal}.



[]{#ch08lvl1sec60}SolrCloud overview {#solrcloud-overview .title style="clear: both"}
------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

One of the must have when going[]{#id288339952 .indexterm} to production
is clustering for fault tolerance and high availability. Solr\'s answer
to this is SolrCloud, which provides ways to have distributed indexing
and search capabilities with central configuration for the entire
cluster, and load balancing with failover support.

As mentioned earlier, Solr provides distributed searching. Behind the
scenes, Solr makes use of ZooKeeper to manage nodes. 

In SolrCloud, data is distributed in multiple shards, which can be
hosted on multiple boxes having replicas; this provides redundancy,
fault tolerance, and scalability. ZooKeeper holds the strings to manage
the shards and replication and to decide which server will handle a
specific request.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec121}SolrCloud in interactive mode {#solrcloud-in-interactive-mode .title}

</div>

</div>
:::

Let\'s set up SolrCloud. Go to the `SOLR_HOME/bin`{.literal} directory
and start the server[]{#id288339975 .indexterm} in interactive mode
using the following command: 

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
solr -e cloud
```
:::

::: {.mediaobject}
![](7_files/395b1329-f3db-4146-a4a2-a97038f64f76.png)
:::

As you can see, an interactive session starts up, asking you how many
nodes the cluster should start with, the default being `2`{.literal}. We
can start up to `4`{.literal} nodes, but we will leave it as the
default.

::: {.mediaobject}
![](7_files/c26e740a-d4a1-4c59-95a0-43830850d629.png)
:::

Next, you will be asked to select ports for the two nodes. Leave the
default ports of `8983`{.literal} and `7574`{.literal} as is and
continue. Once both nodes are started, you will be prompted to name the
schema, the default being `gettingstarted`{.literal}. Leave it as is and
continue. As shown in the preceding screen, you are prompted to select
the number of shards and replicas. Leave the default value of
`2`{.literal} as  is and continue; finally, you will get a message
stating that the SolrCloud example is running at
`http://localhost:8983/solr`{.literal}. 

Navigate to the browser and type the specified URL; you will see Solr as
follows:

::: {.mediaobject}
![](7_files/38ee2645-5e98-4836-959f-9fe87d5f5a67.png)
:::

As you can see, the node is started with two[]{#id288376053 .indexterm}
shards and a replication factor of two for the
`gettingstarted`{.literal} schema. 
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec122}SolrCloud -- core concepts {#solrcloud-core-concepts .title}

</div>

</div>
:::

In order to scale, a collection is split[]{#id288376071 .indexterm} or
partitioned into various shards having documents distributed, which
means shards have subsets of overall documents in the collection. A
cluster hosts multiple Solr Documents\' collections. The maximum number
of documents that a collection can hold and also the parallelization for
individual search requests are based on the number of shards a
collection has.

When we set up SolrCloud, we created two nodes. A Solr cluster is made
up of two or more Solr nodes, and each node can host multiple cores. We
also created a couple of shards with a replication factor of two. The
greater the number of replicas that each shard has, the more redundancy
we are building into the collection. And this determines the overall
high availability and the number of concurrent search requests that can
be processed.

SolrCloud does not employ the master-slave strategy. Every shard has at
least one replica, so at any point in time, one of them acts as a leader
based on the first come, first serve principle. If the leader goes down,
a new leader is appointed automatically. The document is first indexed
in the leader shard replica and then the leader sends the update to all
the other replicas.

When a leader is down, by default all the replicas can become leaders;
but in order for this to happen, it becomes mandatory that every replica
is in sync with the current leader at all times. Any new document that
is added to the leader must be sent across all the replicas and all of
them have to issue a commit. Imagine what would happen if a replica goes
down and there are a large number of updates until the time the replica
rejoins the cluster. Obviously, the recovery would be very slow. It all
depends on the use case that an organization wants for their syncing
strategy. They can keep it as is with real-time syncing, or opt for not
syncing in real time or making the replica ineligible for becoming the
leader.

We can set the following replica types when creating a new collection or
adding a new replica:

::: {.informaltable}
  ----------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  [**Replica type**]{.strong}   [**Description**]{.strong}
  NRT                           [**Near real-time**]{.strong} ([**NRT**]{.strong}) which is the[]{#id288617170 .indexterm} default and initially the only replica type supported by Solr. The way it works is as follows: it maintains a transaction log and writes new documents locally to its index. Here, any replica of this type is eligible for becoming a leader.
  TLOG                          The only difference between NRT and TLOG is that while the former indexes document changes locally, TLOG does not do that. The TLOG type of replica only maintains a transaction log, resulting in better speeds compared to NR as there are no commits involved. Just like NRT, this type of replica is eligible to become a shard leader.
  PULL                          Does not maintain transaction and document changes locally. The only thing it does is pull or replicate the index from the shard leader. Having this replica type ensures that the replica never becomes a shard leader.
  ----------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
:::

 

We can create different mixes of replica type combos for our replicas.
Some commonly used combinations are as follows:

::: {.informaltable}
  ---------------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  [**Combination**]{.strong}         [**Description**]{.strong}
  All NRT replicas                   This can be used wherever the update index throughput is less since it involves a commit on every replica during index. Can be ideal for small to medium clusters or wherever the update index throughput is less.
  All TLOG replicas                  This can be used if we have more replicas per shard, with the replicas being able to handle update requests; NRT is not desired.
  TLOG replicas with PULL replicas   This combination is used more often in scenarios where we want to increase the availability of search queries and document updates take the backseat. This will give an outdated result as we are not having NRT updates.
  ---------------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec123}Routing documents {#routing-documents .title}

</div>

</div>
:::

You can specify the router implementation used[]{#id288618908
.indexterm} by a collection using the `router.name`{.literal} parameter
while creating your collection.

By default, the `compositeId`{.literal} router is used. In this
implementation, you can send documents with a prefix in the document ID
used to calculate the hash that Solr uses to select the shard for
indexing of the document. The prefix can be of your choice but it should
be consistent. For example, if you want to find documents of a
restaurant chain, you can use the restaurant name as a prefix.  If the
restaurant chain is `KFC`{.literal} and document ID is
`87364`{.literal}, the prefix will be something like
`KFC!87364`{.literal}. The exclamation mark is used to differentiate the
prefix used to determine the shard where the document will be routed.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec124}Splitting shards {#splitting-shards .title}

</div>

</div>
:::

Let\'s say that you have created a collection in SolrCloud and created
two shards initially.  Down the line, there are some additional
requirements and now you want more shards. You find yourself in the
soup, right? 

Fear not! the collections API of SolrCloud comes to the rescue. The
collections API provides a way to split a shard into two pieces. It does
not touch the existing shard at all (which can be deleted later) and the
split will create two copies of the data as new shards. 
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec125}Setting up ignore commits from client applications {#setting-up-ignore-commits-from-client-applications .title}

</div>

</div>
:::

Care should be taken in SolrCloud mode: the client
application[]{#id288622849 .indexterm} should not send explicit commit
requests. Instead, we should set auto commits to make updates visible,
ensuring that auto commits happen at regular intervals in the cluster. 

However, it is not feasible to go and update all applications to
stop[]{#id288622859 .indexterm} them from sending explicit commits. Solr
provides `IgnoreCommitOptimizeUpdateProcessorFactory`{.literal}, which
will ignore all explicit commits without touching the client
application. The change is done in `solrconfig.xml`{.literal}, as shown
here:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<updateRequestProcessorChain name="ignore-commit-from-client" default="true">
    <processor class="solr.IgnoreCommitOptimizeUpdateProcessorFactory">
        <int name="statusCode">200</int>
    </processor>
    <processor class="solr.LogUpdateProcessorFactory" />
    <processor class="solr.DistributedUpdateProcessorFactory" />
    <processor class="solr.RunUpdateProcessorFactory" />
</updateRequestProcessorChain>
```
:::

In the preceding snippet, we return status 200 to the client but ignore
the commit.



[]{#ch08lvl1sec61}Enabling SSL -- Solr security {#enabling-ssl-solr-security .title style="clear: both"}
-----------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In this example, we will see a basic SSL setup using a self-signed
certificate. Enabling SSL ensures[]{#id288341307 .indexterm} that
communication between the client and Solr server is encrypted.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec126}Prerequisites {#prerequisites .title}

</div>

</div>
:::

Before generating a self-signed certificate, ensure that[]{#id288341322
.indexterm} you have OpenSSL installed on your machine. To check whether
OpenSSL is already installed, type the following command in the Command
Prompt:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
openssl version
```
:::

It should print out the current version of OpenSSL running on your
system. If it does not do so, kindly download the latest version of
OpenSSL for your operating system and then install it.

We will also make use of JDK\'s keytool for generating self-signed
certificates.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec127}Generating a key and self-signed certificate {#generating-a-key-and-self-signed-certificate .title}

</div>

</div>
:::

JDK provides the `keytool`{.literal} command to create self-signed
certificates. What we will[]{#id288599639 .indexterm} first do is create
a keystore using the following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
keytool -genkeypair -alias mysolr -keyalg RSA -keysize 2048 -keypass solrpass -storepass solrpass -validity 3650 -keystore mysolrkeystore.jks
```
:::

In the preceding command, we are creating a keystore[]{#id288601054
.indexterm} named `mysolrkeystore.jks`{.literal} using the RSA
algorithm, with a key size of `2048`{.literal} and validity of 10 years.
We have also given the alias name of `mysolr`{.literal} and specified
the key password and store password. This will open up an interactive
prompt, as shown here:

::: {.mediaobject}
![](8_files/61276ff6-a9b9-474a-83c7-0d5decaed6bf.png)
:::

In the interactive prompt, fill in the rest of the details and voilà!
You have your keystore ready. 

We will now convert this keystore into PEM format, which is accepted by
most clients. This requires a two-step process:

::: {.itemizedlist}
-   Converting the keystore from JKS to PKCS12 format
-   Final conversion to PEM format
:::

In order to do the first conversion to PKCS12 format, we will still use
the JDK keytool. The command is as follows:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
keytool -importkeystore -srckeystore mysolrkeystore.jks -destkeystore mysolrkeystore.p12 -srcstoretype jks -deststoretype pkcs12
```
:::

As you can see, we are trying to import the keystore and have specified
both source and destination keystore names; finally we\'ve specified the
keystore type to be `pkcs12`{.literal}. You will see an interactive
session opening up again:

::: {.mediaobject}
![](8_files/64f743bd-08bd-442d-bbd7-9df63f5b4b94.png)
:::

As shown here, you will be asked the destination password for the new
keystore and also the source keystore password. Keep the password the
same as what we entered earlier and you should see that the import will
be successfully completed. You will now have
`mysolrkeystore.p12`{.literal} in your directory.

Now, for the final conversion, we will use OpenSSL. Issue the following
command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
openssl pkcs12 -in mysolrkeystore.p12 -out mysolrkeystore.pem
```
:::

The command is straightforward. You will be presented with options to
specify the password once again. Once you have done so, you will see the
folder from where you have issued the command with all the three files
(jks, pkcs12, and pem keystores), as shown here:

::: {.mediaobject}
![](8_files/be3bd63f-bdc3-46c1-a369-f1b87cf8cdad.png)
:::

Congratulations!!! You are one step closer to setting up SSL.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec128}Starting Solr with SSL system properties {#starting-solr-with-ssl-system-properties .title}

</div>

</div>
:::

In order to enable SSL, there are some system[]{#id288626485 .indexterm}
properties that you have to turn on. These properties can be found in
`SOLR_HOME/bin/solr.in.cmd`{.literal} in Windows
and `SOLR_HOME/bin/solr.in.sh`{.literal} in Unix-based systems.

When you open the file, you will see a set of properties intended for
SSL, as highlighted here:

::: {.mediaobject}
![](8_files/35b01f57-9367-4545-88db-398563fa9568.png)
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note33}Note {#note .title}

As highlighted, there is a section of properties that are related to SSL
and they are commented.
:::

Uncomment the lines:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
set SOLR_SSL_ENABLED=true
set SOLR_SSL_KEY_STORE=E:/book/solr/solr-7.2.0/keys/mysolrkeystore.jks
set SOLR_SSL_KEY_STORE_PASSWORD=solrpass
set SOLR_SSL_KEY_STORE_TYPE=JKS
set SOLR_SSL_TRUST_STORE=E:/book/solr/solr-7.2.0/keys/mysolrkeystore.jks
set SOLR_SSL_TRUST_STORE_PASSWORD=solrpass
set SOLR_SSL_TRUST_STORE_TYPE=JKS
set SOLR_SSL_NEED_CLIENT_AUTH=false
set SOLR_SSL_WANT_CLIENT_AUTH=false
```
:::

Make sure you have changed the keystore path and password as per your
configuration.

Once you have done the preceding changes, start Solr using the following
command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
solr -p 8984 -Dsolr.ssl.checkPeerName=false
```
:::

Once the Solr server is up, navigate to the browser and run the
following URL:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
https://localhost:8984/solr/#/
```
:::

Since it is a self-signed certificate, you may get a message to accept
the certificate and continue. Once you continue, you will see the Solr
home page:

::: {.mediaobject}
![](8_files/dc2a55f3-acd2-41d7-9e40-d4909dfc0d63.png)
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note34}Note {#note-1 .title}

Note that you still have a cross mark on `https`{.literal} since it is a
self-signed certificate; it will not have such errors if you are using a
proper certificate from GoDaddy or Verisign.
:::

In order to check whether the details of the[]{#id288628869 .indexterm}
certificate are the same as you created, click on [*F12*]{.emphasis} to
open developer tools if you are using a Chrome browser:

::: {.mediaobject}
![](8_files/ab97dd92-4948-4ee4-88ad-d8e4f211f0cf.png)
:::

As shown, click on the **`Security`** tab and then on the
**`View Certificate`** option, which will open a dialog box. Navigate to
the **`Details`** tab and you will see all the details of the
certificate that you have created.



[]{#ch08lvl1sec62}Performance statistics {#performance-statistics .title style="clear: both"}
----------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In order to measure performance, Solr provides statistics[]{#id288339952
.indexterm} and metrics; they can read either using Metrics API or by
enabling JMX.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch08lvl2sec129}Statistics for request handlers {#statistics-for-request-handlers .title}

</div>

</div>
:::

Both search and update request handlers[]{#id288339966 .indexterm}
provide various statistics.

The API request path for search
is `http://localhost:8983/solr/admin/metrics?group=mycore&prefix=QUERY./select`{.literal}.

Similarly the API request path for update
is `http://localhost:8983/solr/admin/metrics?group=mycore&prefix=UPDATE./update.`{.literal}

There are various attributes that can be added at the end of both of
these URLs to get various statistics, as listed here:

::: {.itemizedlist}
-   `5minRate`{.literal}: Used to find out the requests per second that
    have we received in the last 5 minutes.
-   `15minRate`{.literal}: Same as `5minRate`{.literal}, but here we
    check for requests per second in the last 15 minutes.
-   `p75_ms/p95_ms/p99_ms/p999_ms`{.literal}: Each of the four
    attributes represent how much processing time `x`{.literal}
    percentile of the request took. `x`{.literal} is to be replaced by
    the number specified.
-   `count`{.literal}: Number of requests made from the time Solr was
    time.
-   `median_ms`{.literal}: As the name suggests, this is the median of
    processing times for all requests.
-   `avgRequestsPerSecond`{.literal}: Average requests per second, just
    as the name suggests.
-   `avgTimePerSecond`{.literal}: Average time for the requests to be
    processed.
:::

Just as there are statistics for requests, we have
statistics[]{#id288341299 .indexterm} for cache and commits made as
well. 



[]{#ch08lvl1sec63}Summary
-------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In this chapter, we saw the various tuning parameters needed to take
Solr to production. We started off with JVM parameters, and then saw how
to manage `solrconfig.xml`{.literal}. We got an understanding of taking
backups, setting up JMX, and configuring logs. Finally, we had an
overview of SolrCloud.

In the next chapter, we will see various Client APIs made available by
Solr.
