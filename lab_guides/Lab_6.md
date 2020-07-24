

[]{#ch06}Chapter 6. Advanced Queries -- Part I {#chapter-6.-advanced-queries-part-i .title}
----------------------------------------------

</div>

</div>
:::

In the previous chapter, we learned how to build indexes using various
methods. In this chapter, we will see how Solr\'s search works. Solr
comes with a large searching kit; by configuring elements from this kit,
it provides users with an extensive search experience and returns
impressive results with a helpful interface.

Here is a list of search functionalities provided by Solr, that put Solr
in the list of desirable search engines:

::: {.itemizedlist}
-   Highlighting
-   Spell checking
-   Reranking
-   Transformation of results
-   Suggested words
-   Pagination on results
-   Expand and collapse
-   Grouping and clustering
-   Spatial search
-   More like this word
-   Autocomplete
:::

We will look at some of these functions in detail later in this chapter,
but first let\'s understand every component that performs an important
role during searches and generates impressive results.




[]{#ch06lvl1sec41}Search relevance {#search-relevance .title style="clear: both"}
----------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Relevance is a measurement of the user\'s satisfaction
with[]{#id288339991 .indexterm} the response to their search query. It
completely depends on the context of the search. Sometimes, the same
document can be searched by different classes of people for different
context. For example, the search query [*higher tax payer in
India*]{.emphasis} can be searched by:

::: {.itemizedlist}
-   An income tax department in the context of their duty
-   Chartered accountants in the context of their professional interest
-   Students in the context of gaining knowledge
:::

The comprehensiveness of any response depends on the context of the
search. Sometimes, the context is high, such as searching for legal
information; sometimes, it is low, when someone is searching for context
such as specific dance steps. So, during Solr configuration, we need to
take care of this too.

There are two terms that play an important role in relevance:

::: {.itemizedlist}
-   [**Precision**]{.strong}: Precision is the percentage of documents
    in the returned results that are relevant.
-   [**Recall**]{.strong}: Recall is the percentage of relevant results
    returned out of all relevant results in the system. Retrieving
    perfect recall is insignificant, for example, returning every
    document for every query.
:::

From this example, we can conclude that precision and recall totally
depend on the context of the search. Sometimes, we need 100% recall, say
when searching for legal information. Here, all the relevant documents
should be returned in the response. While in other scenarios, there is
no need to return all documents. For example, when searching for dance
steps, returning all the documents will overwhelm the application.

Through faceting, query filters, and other search components, the
application can be configured with the flexibility to help end users get
their searches, in order to return the most relevant results for users.
We can configure Solr to balance precision and recall to meet the needs
of a particular user community.



[]{#ch06lvl1sec42}Velocity search UI {#velocity-search-ui .title style="clear: both"}
------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Solr provides a user interface through which[]{#id288339952 .indexterm}
we can easily understand the Solr search mechanism. Using velocity
search UI, we can explore search features such as faceting,
highlighting, autocomplete, and geospatial searching. Previously we have
seen an example of `techproducts`{.literal}; let\'s browse its products
through velocity UI. You can access the UI through
`http://localhost:8983/solr/techproducts/browse`{.literal}, as shown in
the following screenshot:

::: {.mediaobject}
![](3_files/b3fb9022-3682-434c-867e-76a7c6c214c7.png)
:::

Solr uses response writer to generate an organized response. Here
velocity UI uses velocity response writer. We will explore response
writer later in this chapter.



[]{#ch06lvl1sec43}Query parsing and syntax {#query-parsing-and-syntax .title style="clear: both"}
------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In this section, we will explore[]{#id288339991 .indexterm} some query
parsers, their features, and how to configure them with Solr. Solr
supports some query parsers. Here is the list of parsers supported by
Solr:

::: {.itemizedlist}
-   [**Standard query parser**]{.strong}
-   [**DisMax query parser**]{.strong}
-   [**Extended DisMax (eDisMax) query parser**]{.strong}
:::

Each parser has its own configuration parameters for clubbing with Solr.
However, there are some common parameters required by all parsers. First
let\'s take a look at these common parameters.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec84}Common query parameters {#common-query-parameters .title}

</div>

</div>
:::

The following are the[]{#id288377780 .indexterm} common query parameters
supported by standard query parser, DisMax query parser, and extended
DisMax query parser:

::: {.informaltable}
+----------------------+----------------------+----------------------+
| [**P                 | [**                  | [**Default           |
| arameter**]{.strong} | Behavior**]{.strong} | value**]{.strong}    |
+----------------------+----------------------+----------------------+
| `defType`{.literal}  | Selects the query    | `Lucene`{.literal}   |
|                      | parser:              | (standard query      |
|                      |                      | parser)              |
|                      | `defTy               |                      |
|                      | pe=dismax`{.literal} |                      |
+----------------------+----------------------+----------------------+
| `sort`{.literal}     | Sorts the search     | `desc`{.literal}     |
|                      | results in either    |                      |
|                      | ascending or         |                      |
|                      | descending order.    |                      |
|                      | The value can be     |                      |
|                      | specified as         |                      |
|                      | `asc`{.literal} or   |                      |
|                      | `ASC`{.literal} and  |                      |
|                      | `desc`{.literal} or  |                      |
|                      | `DESC`{.literal}.    |                      |
|                      | Sorting is supported |                      |
|                      | by numerical or      |                      |
|                      | alphabetical         |                      |
|                      | content. Solr        |                      |
|                      | supports sorting by  |                      |
|                      | field clones.        |                      |
|                      |                      |                      |
|                      | [**                  |                      |
|                      | Example**]{.strong}: |                      |
|                      |                      |                      |
|                      | ::: {.itemizedlist}  |                      |
|                      | -   `sa              |                      |
|                      | lary asc`{.literal}: |                      |
|                      |     Sorts based on   |                      |
|                      |     salary (high to  |                      |
|                      |     low).            |                      |
|                      | -   `n               |                      |
|                      | ame desc`{.literal}: |                      |
|                      |     Sorts based on   |                      |
|                      |     names (z → a).   |                      |
|                      | -   `salary asc n    |                      |
|                      | ame desc`{.literal}: |                      |
|                      |     First sorts by   |                      |
|                      |     salary high to   |                      |
|                      |     low. Within      |                      |
|                      |     that, it sorts   |                      |
|                      |     the result set   |                      |
|                      |     again sorts by   |                      |
|                      |     name (z → a).    |                      |
|                      | :::                  |                      |
+----------------------+----------------------+----------------------+
| `start`{.literal}    | Specifies the        | `0`{.literal}        |
|                      | starting point from  |                      |
|                      | where the results    |                      |
|                      | should begin         |                      |
|                      | displaying.          |                      |
+----------------------+----------------------+----------------------+
| `rows`{.literal}     | Specifies the        | `10`{.literal}       |
|                      | maximum number of    |                      |
|                      | documents to be      |                      |
|                      | returned to the      |                      |
|                      | client from the      |                      |
|                      | complete result set  |                      |
|                      | at a time.           |                      |
+----------------------+----------------------+----------------------+
| `fq`{.literal}       | Limits the result    |                      |
|                      | set to the documents |                      |
|                      | matched by the       |                      |
|                      | filter query         |                      |
|                      | (`fq`{.literal})     |                      |
|                      | without affecting    |                      |
|                      | the score.           |                      |
+----------------------+----------------------+----------------------+
| `fl`{.literal}       | Specifies the field  | `*`{.literal}        |
|                      | list to be returned  |                      |
|                      | inside the response  |                      |
|                      | for each matching    |                      |
|                      | document. The fields |                      |
|                      | can be specified by  |                      |
|                      | a space or comma.    |                      |
|                      | For example:         |                      |
|                      |                      |                      |
|                      | ::: {.itemizedlist}  |                      |
|                      | -   `fl=id nam       |                      |
|                      | e salary`{.literal}: |                      |
|                      |     Returns only     |                      |
|                      |     `id`{.literal},  |                      |
|                      |                      |                      |
|                      |    `name`{.literal}, |                      |
|                      |     and              |                      |
|                      |                      |                      |
|                      |   `salary`{.literal} |                      |
|                      | -   `fl=id,nam       |                      |
|                      | e,salary`{.literal}: |                      |
|                      |     Returns only     |                      |
|                      |     `id`{.literal},  |                      |
|                      |                      |                      |
|                      |    `name`{.literal}, |                      |
|                      |     and              |                      |
|                      |                      |                      |
|                      |   `salary`{.literal} |                      |
|                      | :::                  |                      |
|                      |                      |                      |
|                      | Indicating           |                      |
|                      | `fl=*`{.literal}     |                      |
|                      | will return all the  |                      |
|                      | fields. We also can  |                      |
|                      | return the           |                      |
|                      | `score`{.literal} of |                      |
|                      | fields for each      |                      |
|                      | document by          |                      |
|                      | mentioning the       |                      |
|                      | `score `{.           |                      |
|                      | literal}string along |                      |
|                      | with the fields. For |                      |
|                      | example:             |                      |
|                      |                      |                      |
|                      | ::: {.itemizedlist}  |                      |
|                      | -   `fl=             |                      |
|                      | id score`{.literal}: |                      |
|                      |     Returns the      |                      |
|                      |     `id`{.literal}   |                      |
|                      |     field and the    |                      |
|                      |                      |                      |
|                      |    `score`{.literal} |                      |
|                      | -   `fl              |                      |
|                      | =* score`{.literal}: |                      |
|                      |     Returns all the  |                      |
|                      |     fields in each   |                      |
|                      |     document along   |                      |
|                      |     with each        |                      |
|                      |     field\'s         |                      |
|                      |                      |                      |
|                      |    `score`{.literal} |                      |
|                      | :::                  |                      |
+----------------------+----------------------+----------------------+
| `debug`{.literal}    | Returns debug        | Not including        |
|                      | information about    | debugging            |
|                      | the specified value. | information          |
|                      | For example:         |                      |
|                      |                      |                      |
|                      | ::: {.itemizedlist}  |                      |
|                      | -   `de              |                      |
|                      | bug=query`{.literal} |                      |
|                      |     will return      |                      |
|                      |     debug            |                      |
|                      |     information      |                      |
|                      |     about the query  |                      |
|                      | -   `deb             |                      |
|                      | ug=timing`{.literal} |                      |
|                      |     will return      |                      |
|                      |     debug            |                      |
|                      |     information      |                      |
|                      |     about the        |                      |
|                      |     processing time  |                      |
|                      |     of the query     |                      |
|                      | -   `debu            |                      |
|                      | g=results`{.literal} |                      |
|                      |     will return      |                      |
|                      |     debug            |                      |
|                      |     information      |                      |
|                      |     about the score  |                      |
|                      |     results          |                      |
|                      |     (explain)        |                      |
|                      | -   `                |                      |
|                      | debug=all`{.literal} |                      |
|                      |     or               |                      |
|                      |     `d               |                      |
|                      | ebug=true`{.literal} |                      |
|                      |     will return all  |                      |
|                      |     debugging        |                      |
|                      |     information for  |                      |
|                      |     the request      |                      |
|                      | :::                  |                      |
+----------------------+----------------------+----------------------+
| `exp                 | This specifies a     | blank                |
| lainOther`{.literal} | Lucene query that    |                      |
|                      | will return          |                      |
|                      | debugging            |                      |
|                      | information with the |                      |
|                      | explain information  |                      |
|                      | of each document     |                      |
|                      | matching to that     |                      |
|                      | Lucene query,        |                      |
|                      | relative to the      |                      |
|                      | original query       |                      |
|                      | (specified by the    |                      |
|                      | `q`{.literal}        |                      |
|                      | parameter). For      |                      |
|                      | example:             |                      |
|                      |                      |                      |
|                      | `q=soccer&debug      |                      |
|                      | =true&explainOther=i |                      |
|                      | d:cricket`{.literal} |                      |
|                      |                      |                      |
|                      | The preceding query  |                      |
|                      | calculates the       |                      |
|                      | scoring explain info |                      |
|                      | of the top matching  |                      |
|                      | documents and        |                      |
|                      | compares with the    |                      |
|                      | explain info for the |                      |
|                      | documents matching   |                      |
|                      | `id                  |                      |
|                      | :cricket`{.literal}. |                      |
+----------------------+----------------------+----------------------+
| `wt`{.literal}       | Specifies a response | `json`{.literal}     |
|                      | writer               |                      |
|                      | format. Supported    |                      |
|                      | formats are          |                      |
|                      | `json`{.literal},    |                      |
|                      | `xml`{.literal},     |                      |
|                      | `xslt`{.literal},    |                      |
|                      | `javabin`{.literal}, |                      |
|                      | `geojson`{.literal}, |                      |
|                      | `python`{.literal},  |                      |
|                      | `php`{.literal},     |                      |
|                      | `phps`{.literal},    |                      |
|                      | `ruby`{.literal},    |                      |
|                      | `csv`{.literal},     |                      |
|                      | `                    |                      |
|                      | velocity`{.literal}, |                      |
|                      | `smile`{.literal},   |                      |
|                      | and                  |                      |
|                      | `xlsx`{.literal}.    |                      |
+----------------------+----------------------+----------------------+
| `o                   | Tells Solr to        | `false`{.literal}    |
| mitHeader`{.literal} | include or exclude   |                      |
|                      | header information   |                      |
|                      | from the returned    |                      |
|                      | results;             |                      |
|                      | `omitHe              |                      |
|                      | ader=true`{.literal} |                      |
|                      | will exclude the     |                      |
|                      | header information   |                      |
|                      | from the returned    |                      |
|                      | results.             |                      |
+----------------------+----------------------+----------------------+
| `cache`{.literal}    | This tells Solr to   | `true`{.literal}     |
|                      | cache results of all |                      |
|                      | queries and filter   |                      |
|                      | queries. If set to   |                      |
|                      | `false`{.literal},   |                      |
|                      | it will disable      |                      |
|                      | result caching.      |                      |
+----------------------+----------------------+----------------------+
:::

Apart from the preceding common parameters for parsers,
`timeAllowed`{.literal}, `segmentTerminateEarly`{.literal},
`logParamsList`{.literal}, and `echoParams`{.literal} are also common
parameters which are used by all the parsers. We are not detailing these
parameters here.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec85}Standard query parser {#standard-query-parser .title}

</div>

</div>
:::

Standard query[]{#id288341690 .indexterm} parser, also
known[]{#id288341699 .indexterm} as [**Lucene query
parser**]{.strong}, is the default[]{#id288341713 .indexterm} query
parser for Solr.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec34}Advantage {#advantage .title}

</div>

</div>
:::

The syntax is easy and differently[]{#id288341725 .indexterm} structured
queries can easily be created using standard query parser.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec35}Disadvantage {#disadvantage .title}

</div>

</div>
:::

Standard query parser does not throw[]{#id288341740 .indexterm} any
syntax error. So, identifying syntax errors is a little tough.

The following parameters are supported by standard query parser. We can
configure them in `solrconfig.xml`{.literal}:

::: {.informaltable}
  -------------------------- --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
  [**Parameter**]{.strong}   [**Behavior**]{.strong}                                                                                                                                                           [**Default value**]{.strong}
  `q`{.literal}              Specifies a query using standard query syntax. This is a mandatory parameter for any request.                                                                                     
  `q.op`{.literal}           Specifies the default operator for query expressions, which overrides the default operator configured inside the schema. Possible values are `AND`{.literal} or `OR`{.literal}.   
  `df`{.literal}             Specifies a default field, which overrides the default field definition inside the schema.                                                                                        
  `sow`{.literal}            If this is set to `true`{.literal}, it splits on white spaces.                                                                                                                    `false`{.literal}
  -------------------------- --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
:::

[**Standard query parser response**]{.strong}: The following is the
sample response provided by the standard query parser when we search for
`field id=SP2514N`{.literal}

[**URL**]{.strong}: `http://localhost:8983/solr/techproducts/select?q=SP2514N&wt=json`{.literal}

In Solr admin console, while running a query to search a product with
`id=SP2514N`{.literal} displays the response as follows:

::: {.mediaobject}
![](4_files/54583c1c-0b1e-4535-9d0b-7ab29ac29ed6.png)
:::

The response code is as follows:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":0,
 "params":{
 "q":"SP2514N",
 "wt":"json",
 "_":"1515346597997"}},
 "response":{"numFound":1,"start":0,"docs":[
 {
 "id":"SP2514N",
 "name":"Samsung SpinPoint P120 SP2514N - hard drive - 250 GB - ATA-133",
 "manu":"Samsung Electronics Co. Ltd.",
 "manu_id_s":"samsung",
 "cat":["electronics",
 "hard drive"],
 "features":["7200RPM, 8MB cache, IDE Ultra ATA-133",
 "NoiseGuard, SilentSeek technology, Fluid Dynamic Bearing (FDB) motor"],
 "price":92.0,
 "price_c":"92.0,USD",
 "popularity":6,
 "inStock":true,
 "manufacturedate_dt":"2006-02-13T15:26:37Z",
 "store":"35.0752,-97.032",
 "_version_":1583131913641525248,
 "price_c____l_ns":9200}]
 }}
```
:::

In the same way, now the query `id=SP2514N`{.literal}; and we need only
two fields, `id`{.literal} and `name`{.literal}, in
`response`{.literal}.

[**URL**]{.strong}: `http://localhost:8983/solr/techproducts/select?fl=id,name&q=SP2514N&wt=json`{.literal}.

[**Response**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":17,
 "params":{
 "q":"SP2514N",
 "fl":"id,name",
 "wt":"json",
 "_":"1515346597997"}},
 "response":{"numFound":1,"start":0,"docs":[
 {
 "id":"SP2514N",
 "name":"Samsung SpinPoint P120 SP2514N - hard drive - 250 GB - ATA-133"}]
 }}
```
:::

We can format the response by setting the `wt`{.literal} parameter as
`json`{.literal}, `xml`{.literal}, `xslt`{.literal},
`javabin`{.literal}, `geojson`{.literal}, `python`{.literal},
`php`{.literal}, `phps`{.literal}, `ruby`{.literal}, `csv`{.literal},
`velocity`{.literal}, `smile`{.literal}, or `xlsx`{.literal}.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

##### []{#ch06lvl4sec0}Searching terms for standard query parser {#searching-terms-for-standard-query-parser .title}

</div>

</div>
:::

A query string to standard query parser[]{#id288377990 .indexterm}
contains terms and operators. There are two types of terms:

::: {.itemizedlist}
-   [**Single term**]{.strong}: A single word, such as
    `soccer`{.literal} or `volleyball`{.literal}
-   [**A phrase**]{.strong}: A group of words surrounded by double
    quotes, such as `apache solr`{.literal}
:::

We can combine multiple terms with Boolean operators to form complex
queries.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec36}Term modifiers {#term-modifiers .title}

</div>

</div>
:::

Solr supports many term modifiers that[]{#id288378064 .indexterm} add
flexibility or precision during searching. These term modifiers are
wildcard characters, characters for making a search fuzzy, and so on.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec37}Wildcard searches {#wildcard-searches .title}

</div>

</div>
:::

The standard query parser supports[]{#id288378078 .indexterm} two types
of wildcard searches within a single term. They are single
(`?`{.literal}) and multiple (`*`{.literal}) characters. They can be
applied to single terms only, and not to search phrases. For example:

::: {.informaltable}
  ------------------------------------------------------------------ ---------------------------------- -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  [**Wildcard search type**]{.strong}                                [**Special character**]{.strong}   [**Search example**]{.strong}
  Single character (matches a single character)                      `?`{.literal}                      Searching for a string `te?t`{.literal} will match `test`{.literal} and `text`{.literal}.
  Multiple characters (matches zero or more sequential characters)   `*`{.literal}                      Searching for string `tes*`{.literal} will match `test`{.literal}, `testing`{.literal}, and `tester`{.literal}. The wildcard characters can be used at the beginning, middle, or end of a term. For example, the string `te*t`{.literal} will match `test`{.literal} and `text`{.literal}, and `*est`{.literal} will match `pest`{.literal} and `test`{.literal}.
  ------------------------------------------------------------------ ---------------------------------- -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec38}Fuzzy searches {#fuzzy-searches .title}

</div>

</div>
:::

In fuzzy searching, instead of matching exact terms, Solr searches
terms[]{#id288378204 .indexterm} that are likely similar to a specified
term. The tilde (`~`{.literal}) symbol is used at the end of a single
word in fuzzy search. For example, to search for a term similar in
spelling to roam, use a fuzzy search; `roam~`{.literal} will match terms
like roam, roams, and foam.

The `distance`{.literal} parameter (optional) specifies the maximum
number of modifications that take place between `0`{.literal} and
`2`{.literal}. The default value is `2`{.literal}. For example,
searching for `roam~1`{.literal} will search the terms such as roams and
foam but not foams because it has a modification distance of
`2`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec39}Proximity searching  {#proximity-searching .title}

</div>

</div>
:::

Proximity searching searches for terms[]{#id288378248 .indexterm} within
a specific distance of each other. To implement a proximity search,
specify a tilde (`~`{.literal}) symbol with a numeric value at the end
of a search phrase. For example, to search for `soccer`{.literal} and
`volleyball`{.literal} within `20`{.literal} words of each other in a
document, do this:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
"soccer volleyball"~20
```
:::

The distance value specifies the term movements needed to match the
specified phrase.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec40}Range searches {#range-searches .title}

</div>

</div>
:::

In range searches, documents are searched based on a provided range
(upper and lower bound) for a specific field. All the documents whose
values for the specified field fall[]{#id288378282 .indexterm} in a
given range will be returned. The range search can be inclusive or
exclusive of the range. For example, here the range query matches all
documents whose `price`{.literal} field has a value between
`1000`{.literal} and `50000`{.literal}, both inclusive:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
price:[1000 TO 50000]
```
:::

Along with date and numerical fields, we can specify the range as words
as well. For example:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
title:{apache TO lucene}
```
:::

The preceding range configuration will search all documents whose titles
are between `apache`{.literal} and `lucene`{.literal}, but not including
`apache`{.literal} and `lucene`{.literal}:

::: {.itemizedlist}
-   [**Inclusive**]{.strong}: Square brackets `[ & ]`{.literal}.
    Documents are searched by including the upper and lower bound.
-   [**Exclusive**]{.strong}: Curly brackets
    `{ & }`{.literal}. Documents are searched between the upper and
    lower bound, but excluding the bounds.
:::

Combining inclusive and exclusive is also possible, where one end is
inclusive and the other is exclusive, for
example, `price:[5 TO 20}`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec41}Boolean operators {#boolean-operators .title}

</div>

</div>
:::

Here is a list of Boolean operators supported by standard[]{#id288378360
.indexterm} query parser:

::: {.informaltable}
+----------------------+----------------------+----------------------+
| [**                  | [                    | [**Des               |
| Operator**]{.strong} | **Symbol**]{.strong} | cription**]{.strong} |
+----------------------+----------------------+----------------------+
| `AND`{.literal}      | `&&`{.literal}       | Requires both terms  |
|                      |                      | to match. For        |
|                      |                      | example, we search   |
|                      |                      | documents that       |
|                      |                      | contain soccer and   |
|                      |                      | volleyball:          |
|                      |                      |                      |
|                      |                      | ::: {.itemizedlist}  |
|                      |                      | -                    |
|                      |                      |    `"soccer" AND "vo |
|                      |                      | lleyball"`{.literal} |
|                      |                      | -   `"soccer" && "vo |
|                      |                      | lleyball"`{.literal} |
|                      |                      | :::                  |
+----------------------+----------------------+----------------------+
| `NOT`{.literal}      | `!`{.literal}        | Requires that the    |
|                      |                      | following term not   |
|                      |                      | be present. For      |
|                      |                      | example, we search   |
|                      |                      | for documents that   |
|                      |                      | contain the phrase   |
|                      |                      | soccer but do not    |
|                      |                      | contain volleyball:  |
|                      |                      |                      |
|                      |                      | ::: {.itemizedlist}  |
|                      |                      | -                    |
|                      |                      |    `"soccer" NOT "vo |
|                      |                      | lleyball"`{.literal} |
|                      |                      | -   `"soccer" ! "vo  |
|                      |                      | lleyball"`{.literal} |
|                      |                      | :::                  |
+----------------------+----------------------+----------------------+
| `OR`{.literal}       | `||`{.literal}       | Requires one of the  |
|                      |                      | terms to match. This |
|                      |                      | is a default         |
|                      |                      | conjunction          |
|                      |                      | operator, for        |
|                      |                      | example, searching   |
|                      |                      | for documents that   |
|                      |                      | contains either      |
|                      |                      | soccer or            |
|                      |                      | volleyball:          |
|                      |                      |                      |
|                      |                      | ::: {.itemizedlist}  |
|                      |                      | -   `"soccer" OR "vo |
|                      |                      | lleyball"`{.literal} |
|                      |                      | -   `"soccer" || "vo |
|                      |                      | lleyball"`{.literal} |
|                      |                      | :::                  |
+----------------------+----------------------+----------------------+
|                      | `+`{.literal}        | Requires that the    |
|                      |                      | following term be    |
|                      |                      | present, for         |
|                      |                      | example, searching   |
|                      |                      | for documents that   |
|                      |                      | must contain soccer  |
|                      |                      | and that may or may  |
|                      |                      | not contain          |
|                      |                      | volleyball:          |
|                      |                      |                      |
|                      |                      | `+soccer v           |
|                      |                      | olleyball`{.literal} |
+----------------------+----------------------+----------------------+
|                      | `-`{.literal}        | Prohibits the        |
|                      |                      | following term, for  |
|                      |                      | example, searching   |
|                      |                      | for documents that   |
|                      |                      | contain soccer but   |
|                      |                      | not volleyball:      |
|                      |                      |                      |
|                      |                      | `+soccer -v          |
|                      |                      | olleyball`{.literal} |
+----------------------+----------------------+----------------------+
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note30}Note {#note .title}

Please note that the Boolean operators `AND`{.literal} and
`NOT`{.literal} must be specified in uppercase.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec42}Escaping special characters {#escaping-special-characters .title}

</div>

</div>
:::

[]{.strong} Solr treats these[]{#id288378605 .indexterm} characters with
a special meaning when they are used in a query:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
+  -  &&  ||  !  ( )  { }  [ ]  ^  "  ~  *  ?  :  /
```
:::

Using a backslash character(`\`{.literal}) before the special character
will notify Solr not to treat it as a special character but as a normal
character. For example, for the search string `(1+1):2`{.literal} the
plus and parentheses, and colon can be ignored as special characters and
will be treated as normal characters like this:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
\(1\+1\)\:2
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec43}Grouping terms {#grouping-terms .title}

</div>

</div>
:::

Solr supports groups of clauses using parentheses to form[]{#id288378638
.indexterm} subqueries that control the Boolean logic for a query.

This example will form a query that searches for either
`soccer`{.literal} or `volleyball`{.literal} and `world cup`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
(soccer OR volleyball) AND "world cup"
```
:::

Two or more Boolean operators can also be specified for a single field.
Simply specify the Boolean clauses within parentheses. For example, this
query will search for the field that contains both
`soccer`{.literal} and `volleyball`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
game:(+soccer +volleyball)
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec44}Dates and times in query strings {#dates-and-times-in-query-strings .title}

</div>

</div>
:::

We need to use an appropriate date format whenever[]{#id288378683
.indexterm} we are running a query against any date field. Search
queries for exact date values will require quoting or escaping because
`:`{.literal} is listed as a special character for the parser:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
createddate:2001-01-11T23\:45\:40.60Z

createddate:"2001-01-11T23:45:40.60Z"

createddate:[2001-01-11T23:45:40.60Z TO *]

createddate:[1999-12-31T23:45:40.60Z TO 2001-01-11T00:00:00Z]

timestamp:[* TO NOW]

publisheddate:[NOW-1YEAR/DAY TO NOW/DAY+1DAY]

createddate:[2001-01-11T23:45:40.60Z TO 2001-01-11T23:45:40.60Z+1YEAR]

createddate:[2001-01-11T23:45:40.60Z/YEAR TO 2001-01-11T23:45:40.60Z]
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec45}Adding comments to the query string {#adding-comments-to-the-query-string .title}

</div>

</div>
:::

Comments can also be added to the query[]{#id288378708 .indexterm}
string. Solr supports C-style comments in the query string. Comments may
be nested. For example:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
soccer /* this is a simple comment for query string */ OR volleyball
```
:::
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec86}The DisMax Query Parser {#the-dismax-query-parser .title}

</div>

</div>
:::

The DisMax query parser processes[]{#id288610016 .indexterm} simple
phrases (simple syntax). The DisMax query provides an interface that
looks similar to Google. The DisMax Query Parser supports the simplified
syntax of the Lucene query parser. Quotes can be used for
grouping[]{#id288610024 .indexterm} phrases. The DisMax Query Parser
escapes all Boolean operators to simplify the query syntax, except the
operators `AND`{.literal} and `OR`{.literal}, which can be used to
determine mandatory and optional clauses.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec46}Advantages {#advantages .title}

</div>

</div>
:::

It produces syntax error messages. It also provides[]{#id288610046
.indexterm} additional boosting queries, boosting functions, and
filtering queries for search results.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec47}DisMax query parser parameters {#dismax-query-parser-parameters .title}

</div>

</div>
:::

Apart from common parameters, the following is a list[]{#id288610061
.indexterm} of all parameters supported by the DisMax Query Parser. All
the default values for these parameters are configured in
`solrconfig.xml`{.literal}:

::: {.informaltable}
+--------------------------+------------------------------------------+
| [**Parameter**]{.strong} | [**Behavior**]{.strong}                  |
+--------------------------+------------------------------------------+
| `q`{.literal}            | Specifies a query string with no special |
|                          | characters and treats Boolean operators  |
|                          | `+`{.literal} and `-`{.literal} as term  |
|                          | modifiers. Wildcard characters like      |
|                          | `*`{.literal} are not supported by this  |
|                          | parameter:                               |
|                          |                                          |
|                          | ::: {.itemizedlist}                      |
|                          | -   `q=apache`{.literal}                 |
|                          | -   `q="Apache Lucene"`{.literal}        |
|                          | :::                                      |
+--------------------------+------------------------------------------+
| `a.alt`{.literal}        | Defines an alternate query when the main |
|                          | query parameter `q`{.literal} is not     |
|                          | specified or blank. This parameter is    |
|                          | mainly used to match all documents to    |
|                          | get faceting counts.                     |
+--------------------------+------------------------------------------+
| `qf`{.literal}           | The `qf`{.literal} parameter assigns a   |
|                          | boost factor to a specific field to      |
|                          | increase or decrease its importance in   |
|                          | the query. For example,                  |
|                          | `qf="firstField^3.                       |
|                          | 4 secondField thirdField^0.2"`{.literal} |
|                          | assigns `firstField`{.literal} a boost   |
|                          | of `3.4`{.literal}, keeps                |
|                          | `secondField`{.literal} with the default |
|                          | boost, and assigns                       |
|                          | `thirdField`{.literal} a boost of        |
|                          | `0.2`{.literal}. These boost factors     |
|                          | make matches in `firstField`{.literal}   |
|                          | much more significant than matches in    |
|                          | `secondField`{.literal}, which becomes   |
|                          | much more significant than matches in    |
|                          | `thirdField`{.literal}.                  |
+--------------------------+------------------------------------------+
| `mm`{.literal}           | The minimum parameter defines the        |
|                          | minimum number of optional clauses that  |
|                          | must match. Words or phrases specified   |
|                          | in the `q`{.literal} parameter are       |
|                          | considered as optional clauses unless    |
|                          | they are preceded by a Boolean           |
|                          | `AND`{.literal} or `OR`{.literal}.       |
|                          |                                          |
|                          | Possible values for the `mm`{.literal}   |
|                          | parameter are integer (positive and      |
|                          | negative), percentage (positive and      |
|                          | negative), simple, or multiple           |
|                          | conditional expressions.                 |
|                          |                                          |
|                          | The default value for mm is 100%, which  |
|                          | means all clauses must match.            |
+--------------------------+------------------------------------------+
| `pf`{.literal}           | The [**Phrase Fields**]{.strong}         |
|                          | ([**pf**]{.strong}) parameter            |
|                          | boosts[]{#id288610242 .indexterm} the    |
|                          | score of a document when all the terms   |
|                          | in the `q`{.literal} parameter appear in |
|                          | close proximity.                         |
+--------------------------+------------------------------------------+
| `ps`{.literal}           | The [**Phrase Slop**]{.strong}           |
|                          | ([**ps**]{.strong}) parameter specifies  |
|                          | the number of positions a term is        |
|                          | required to move to match a phrase       |
|                          | specified in a query with the            |
|                          | `pf`{.literal} parameter.                |
+--------------------------+------------------------------------------+
| `qs`{.literal}           | Similar to the `ps`{.literal} parameter  |
|                          | for the `pf`{.literal} parameter, the    |
|                          | [**Query Phrase Slop**]{.strong}         |
|                          | ([**qs**]{.strong}) defines the amount   |
|                          | of slop on phrase queries explicitly     |
|                          | included in the user\'s query            |
|                          | string[]{#id288610333 .indexterm} with   |
|                          | the `qf`{.literal} parameter.            |
+--------------------------+------------------------------------------+
| `tie`{.literal}          | The tie breaker parameter specifies a    |
|                          | float value (which should be something   |
|                          | much less than 1) to use as a            |
|                          | tiebreaker when a query term is matched  |
|                          | in more than one field in a document.    |
+--------------------------+------------------------------------------+
| `bq`{.literal}           | The [**Boost Query**]{.strong}           |
|                          | ([**bq**]{.strong}) parameter            |
|                          | specifies[]{#id288610376 .indexterm} an  |
|                          | additional and optional query clause     |
|                          | that will be added to the user\'s main   |
|                          | query to influence the score. For        |
|                          | example, if you want to add a relevancy  |
|                          | boost for recent documents:              |
|                          |                                          |
|                          | `q=apache`{.literal}`bq=da               |
|                          | te:[NOW/DAY-1YEAR TO NOW/DAY]`{.literal} |
|                          |                                          |
|                          | Multiple `bq`{.literal} parameters       |
|                          | can also be used when a query needs to   |
|                          | be parsed as separate clauses with       |
|                          | separate boosts.                         |
+--------------------------+------------------------------------------+
| `bf`{.literal}           | The [**Boost Functions**]{.strong}       |
|                          | ([**bf**]{.strong}) parameter            |
|                          | specifies[]{#id288610413 .indexterm}     |
|                          | functions (with optional boosts) that    |
|                          | will be added to the user\'s main query  |
|                          | to influence the score. Any function     |
|                          | supported natively by Solr can be used   |
|                          | along with a boost value. For example,   |
|                          | if you want to show the most recent      |
|                          | documents first, this is the syntax:     |
|                          |                                          |
|                          | `bf=recip                                |
|                          | (rord(createddate),1,100,100)`{.literal} |
+--------------------------+------------------------------------------+
:::

 

We have seen all the configuration parameters for DisMax Query Parser
and now we are ready to run a search using DisMax query parser.

The query ID is `SP2514N`{.literal} and all DisMax Parser parameters are
default, which means we are not specifying values in those parameters.

The URL
is `http://localhost:8983/solr/techproducts/select?defType=dismax&q=SP2514N&wt=json`{.literal}

The following shows the Solr admin console showing an example for DisMax
query parser:

::: {.mediaobject}
![](4_files/9ac6acff-a614-467c-bbe6-2e398cd223da.png)
:::

[**Response**]{.strong}: 

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":0,
 "params":{
 "q":"SP2514N",
 "defType":"dismax",
 "wt":"json",
 "_":"1515607090788"}},
 "response":{"numFound":1,"start":0,"docs":[
 {
 "id":"SP2514N",
 "name":"Samsung SpinPoint P120 SP2514N - hard drive - 250 GB - ATA-133",
 "manu":"Samsung Electronics Co. Ltd.",
 "manu_id_s":"samsung",
 "cat":["electronics",
 "hard drive"],
 "features":["7200RPM, 8MB cache, IDE Ultra ATA-133",
 "NoiseGuard, SilentSeek technology, Fluid Dynamic Bearing (FDB) motor"],
 "price":92.0,
 "price_c":"92.0,USD",
 "popularity":6,
 "inStock":true,
 "manufacturedate_dt":"2006-02-13T15:26:37Z",
 "store":"35.0752,-97.032",
 "_version_":1583131913641525248,
 "price_c____l_ns":9200}]
 }}
```
:::

[**Example**]{.strong}: Retrieve only field `id`{.literal} and
`name`{.literal} with `score`{.literal}

[**URL**]{.strong}:
`http://localhost:8983/solr/techproducts/select?defType=dismax&q=SP2514N&fl=id,name,score`{.literal}

[**Response**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":1,
 "params":{
 "q":"SP2514N",
 "defType":"dismax",
 "fl":"id,name,score"}},
 "response":{"numFound":1,"start":0,"maxScore":2.6953351,"docs":[
 {
 "id":"SP2514N",
 "name":"Samsung SpinPoint P120 SP2514N - hard drive - 250 GB - ATA-133",
 "score":2.6953351}]
 }}
```
:::

Use `fl=*`{.literal} to retrieve all the fields.

[**Example**]{.strong}: Now we search for query `iPod`{.literal},
assigning boosting to the `fields`{.literal} features and
`cat`{.literal}.

[**URL**]{.strong}:
`http://localhost:8983/solr/techproducts/select?defType=dismax&q=iPod&qf=features^10.0+cat^0.5`{.literal}

[**Response**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":1,
    "params":{
      "q":"iPod",
      "defType":"dismax",
      "qf":"features^10.0 cat^0.5"}},
  "response":{"numFound":1,"start":0,"docs":[
      {
        "id":"IW-02",
        "name":"iPod & iPod Mini USB 2.0 Cable",
        "manu":"Belkin",
        "manu_id_s":"belkin",
        "cat":["electronics",
          "connector"],
        "features":["car power adapter for iPod, white"],
        "weight":2.0,
        "price":11.5,
        "price_c":"11.50,USD",
        "popularity":1,
        "inStock":false,
        "store":"37.7752,-122.4232",
        "manufacturedate_dt":"2006-02-14T23:55:59Z",
        "_version_":1583131913695002624,
        "price_c____l_ns":1150}]
  }}
```
:::

[**Example**]{.strong}: Boost results that have a field that matches a
specific value.

[**URL:**]{.strong} `http://localhost:8983/solr/techproducts/select?defType=dismax&q=iPod&bq=cat:electronics^5.0`{.literal}

In the same way, we can construct a URL for other
parameters[]{#id288610592 .indexterm} as well.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec87}eDisMax Query Parser {#edismax-query-parser .title}

</div>

</div>
:::

The eDisMax Query Parser is an improved version[]{#id288610606
.indexterm} of the DisMax Query Parser. Along with supporting all the
features provided by DisMax Query Parser, it supports[]{#id288610616
.indexterm} the following:

::: {.itemizedlist}
-   Lucene query parser syntax
-   Improved smart partial escaping in the case of syntax errors
-   Improved proximity boosting by using word shingles
-   Advanced stop word handling
-   Improved boost function
-   Pure negative nested queries
-   We can specify which fields the end user is allowed to query, and
    specify to disallow direct fielded searches
:::

[**eDisMax Query Parser Parameters**]{.strong}: Along with the common
parameters, we have these eDisMax Query Parser parameters:

::: {.informaltable}
  -------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --------------------------------------
  [**Parameter**]{.strong}         [**Behavior**]{.strong}                                                                                                                                                                                                                                                                                                   [**Default Value**]{.strong}
  `sow`{.literal}                  Split on whitespace. Possible values are true and false. Once we set this to `true`{.literal}, text analysis will be done for every individual whitespace-separated term.                                                                                                                                                 False
  `mm.autoRelax`{.literal}         Relax the clauses in case of some of the clauses removed like stop words and search wont get impacted due to any clause removal. We need to take care when using the `mm.autoRelax`{.literal} parameter because sometimes we may get unpredictable results. Possible values are `true`{.literal} and `false`{.literal}.   False
  `boost`{.literal}                A multivalued list of strings parsed as queries, with scores multiplied by the score from the main query for all matching documents.                                                                                                                                                                                      
  `lowercaseOperators`{.literal}   Treats lowercase `and`{.literal} and `or`{.literal} the same as the operators `AND`{.literal} and `OR`{.literal}.                                                                                                                                                                                                         False
  `pf2`{.literal}                  A multivalued list of fields with optional weights. It\'s similar to `pf`{.literal}, but based on pairs of word shingles.                                                                                                                                                                                                 
  `pf3`{.literal}                  A multivalued list of fields with optional weights, based on triplets of word shingles. It is similar to `pf`{.literal}, except that instead of building a phrase per field out of all the words in the input, it builds a set of phrases for each field out of each triplet of word shingles.                            
  `ps`{.literal}                   The `ps`{.literal} parameter specifies how many term positions the terms in the query can be off by to be considered a match on the phrase fields.                                                                                                                                                                        
  `ps2`{.literal}                  Similar to `ps`{.literal} but overrides the slop factor used for `pf2`{.literal}. If not specified, `ps`{.literal} is used.                                                                                                                                                                                               
  `ps3`{.literal}                  Similar to `ps`{.literal} but overrides the slop factor used for `pf3`{.literal}. If not specified, `ps`{.literal} is used.                                                                                                                                                                                               
  `stopwords`{.literal}            Tells Solr to disable `StopFilterFactory`{.literal} configured in query analyzer. Possible values are `true`{.literal} and `false`{.literal}. False will disable `StopFilterFactory`{.literal}.                                                                                                                           True
  `uf`{.literal}                   Specifies which schema fields the end user is allowed to explicitly query.                                                                                                                                                                                                                                                Allow all fields or `uf=*`{.literal}
  -------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --------------------------------------
:::

 

[**Examples**]{.strong}: Searching for `music`{.literal} or
`camera`{.literal} and boosting by popularity.

[**URL:**]{.strong} `http://localhost:8983/solr/techproducts/select?defType=edismax&q=music+OR+camera&boost=popularity`{.literal}

[**Response:**]{.strong}

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":1,
 "params":{
 "q":"music OR camera",
 "defType":"edismax",
 "boost":"popularity"}},
 "response":{"numFound":2,"start":0,"docs":[
 {
 "id":"9885A004",
 "name":"Canon PowerShot SD500",
 "manu":"Canon Inc.",
 "manu_id_s":"canon",
 "cat":["electronics",
 "camera"],
 "features":["3x zoop, 7.1 megapixel Digital ELPH",
 "movie clips up to 640x480 @30 fps",
 "2.0\" TFT LCD, 118,000 pixels",
 "built in flash, red-eye reduction"],
 "includes":"32MB SD card, USB cable, AV cable, battery",
 "weight":6.4,
 "price":329.95,
 "price_c":"329.95,USD",
 "popularity":7,
 "inStock":true,
 "manufacturedate_dt":"2006-02-13T15:26:37Z",
 "store":"45.19614,-93.90341",
 "_version_":1583131913780985856,
 "price_c____l_ns":32995},
 {
 "id":"MA147LL/A",
 "name":"Apple 60 GB iPod with Video Playback Black",
 "manu":"Apple Computer Inc.",
 "manu_id_s":"apple",
 "cat":["electronics",
 "music"],
 "features":["iTunes, Podcasts, Audiobooks",
 "Stores up to 15,000 songs, 25,000 photos, or 150 hours of video",
 "2.5-inch, 320x240 color TFT LCD display with LED backlight",
 "Up to 20 hours of battery life",
 "Plays AAC, MP3, WAV, AIFF, Audible, Apple Lossless, H.264 video",
 "Notes, Calendar, Phone book, Hold button, Date display, Photo wallet, Built-in games, JPEG photo playback, Upgradeable firmware, USB 2.0 compatibility, Playback speed control, Rechargeable capability, Battery level indication"],
 "includes":"earbud headphones, USB cable",
 "weight":5.5,
 "price":399.0,
 "price_c":"399.00,USD",
 "popularity":10,
 "inStock":true,
 "store":"37.7752,-100.0232",
 "manufacturedate_dt":"2005-10-12T08:00:00Z",
 "_version_":1583131913706536960,
 "price_c____l_ns":39900}]
 }}
```
:::

In the same way, we can configure all the eDisMax parser parameters and
explore the search functionality.



[]{#ch06lvl1sec44}Response writer {#response-writer .title style="clear: both"}
---------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

The user who is searching is mainly interested[]{#id288339952
.indexterm} in the search output/response. Rather than providing output
in only a single format, if we allow them to select their choice of
output/response format and return a response in that format, it will
really make the user happy. The good news is that Solr provides various
response writers for the end user\'s convenience.

Once the user runs a search, along with providing matching results, Solr
provides a formatted and well-organized output result that becomes easy
and attractive for the end user. Solr handles this through a response
writer. Solr supports these response writers:

::: {.itemizedlist}
-   JSON (default)
-   Standard XML
-   XSLT
-   Binary
-   GeoJSON
-   Python
-   PHP
-   PHP serialized
-   Ruby
-   CSV
-   Velocity
-   Smile
-   XLSX
:::

We can select the response writer by providing an appropriate value to
the `wt`{.literal} parameter. These are the response writer values for
`wt`{.literal}:​

::: {.informaltable}
  -------------------------------- -----------------------------------
  [**Response writer**]{.strong}   [**wt parameter value**]{.strong}
  JSON                             `json`{.literal}
  Standard XML                     `xml`{.literal}
  XSLT                             `xslt`{.literal}
  Binary                           `javabin`{.literal}
  GeoJSON                          `geojson`{.literal}
  Python                           `python`{.literal}
  PHP                              `php`{.literal}
  PHP serialized                   `phps`{.literal}
  Ruby                             `ruby`{.literal}
  CSV                              `csv`{.literal}
  Velocity                         `velocity`{.literal}
  Smile                            `smile`{.literal}
  XLSX                             `xlsx`{.literal}
  -------------------------------- -----------------------------------
:::

Let\'s explore some of these response writers in detail.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec88}JSON {#json .title}

</div>

</div>
:::

JSON response writer converts results[]{#id288617272 .indexterm} into
JSON format. This is a default response writer for Solr, so if we do not
set the `wt`{.literal} parameter, the default output will be in JSON
format. We can configure the `MIME type`{.literal} for any response
writer in the `solrconfig.xml`{.literal} file. The default
`MIME type`{.literal} for JSON response writer is
`application/json`{.literal}; however, we can override it as per our
search needs. For example, we can override the `MIME type`{.literal}
configuration in `techproducts solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<queryResponseWriter name="json" class="solr.JSONResponseWriter">
 <!-- For the purposes of the tutorial, JSON responses are written as
 plain text so that they are easy to read in *any* browser.
 If you expect a MIME type of "application/json" just remove this override.
 -->
 <str name="content-type">text/plain; charset=UTF-8</str>
</queryResponseWriter>
```
:::

[**JSON response writer parameters**]{.strong}

This is a list of JSON response writer parameters that we need to
configure to get a response in the expected format:

::: {.informaltable}
+----------------+----------------+----------------+----------------+
| [**Paramet     | [**Behavi      | [**Val         | [**Descripti   |
| er**]{.strong} | or**]{.strong} | ue**]{.strong} | on**]{.strong} |
+----------------+----------------+----------------+----------------+
| `json          | Controls the   | `f             | `NamedL        |
| .nl`{.literal} | output format  | lat`{.literal} | ist`{.literal} |
|                | of             | (default)      | is represented |
|                | `NamedLi       |                | as a flat      |
|                | st`{.literal}, |                | array, with    |
|                | where the      |                | alternating    |
|                | order is more  |                | names and      |
|                | important than |                | values.        |
|                | access by      |                |                |
|                | name.          |                | [**Inpu        |
|                | `NamedL        |                | t:**]{.strong} |
|                | ist`{.literal} |                |                |
|                | is currently   |                | `Nam           |
|                | used for field |                | edList("a"=1,  |
|                | faceting data. |                | "bar"="foo", n |
|                |                |                | ull=3, null=nu |
|                |                |                | ll)`{.literal} |
|                |                |                |                |
|                |                |                | [**Outpu       |
|                |                |                | t:**]{.strong} |
|                |                |                |                |
|                |                |                | `["a",1,       |
|                |                |                | "bar","foo", n |
|                |                |                | ull,3, null,nu |
|                |                |                | ll]`{.literal} |
+----------------+----------------+----------------+----------------+
|                |                | `              | `NamedL        |
|                |                | map`{.literal} | ist`{.literal} |
|                |                |                | can have       |
|                |                |                | optional keys  |
|                |                |                | and repeated   |
|                |                |                | keys. It       |
|                |                |                | preserves the  |
|                |                |                | order.         |
|                |                |                |                |
|                |                |                | [**Input:**    |
|                |                |                | ]{.strong}`Nam |
|                |                |                | edList("a"=1,  |
|                |                |                | "bar"="foo", n |
|                |                |                | ull=3, null=nu |
|                |                |                | ll)`{.literal} |
|                |                |                |                |
|                |                |                | [**Output:**]  |
|                |                |                | {.strong}`{"a" |
|                |                |                | :1, "bar":"foo |
|                |                |                | ", "":3, "":nu |
|                |                |                | ll}`{.literal} |
+----------------+----------------+----------------+----------------+
|                |                | `arr           | `NamedL        |
|                |                | arr`{.literal} | ist`{.literal} |
|                |                |                | is represented |
|                |                |                | as an array of |
|                |                |                | two element    |
|                |                |                | arrays.        |
|                |                |                |                |
|                |                |                | [**Input:**    |
|                |                |                | ]{.strong}`Nam |
|                |                |                | edList("a"=1,  |
|                |                |                | "bar"="foo", n |
|                |                |                | ull=3, null=nu |
|                |                |                | ll)`{.literal} |
|                |                |                |                |
|                |                |                | [**Output:*    |
|                |                |                | *]{.strong}`[[ |
|                |                |                | "a",1], ["bar" |
|                |                |                | ,"foo"], [null |
|                |                |                | ,3], [null,nul |
|                |                |                | l]]`{.literal} |
+----------------+----------------+----------------+----------------+
|                |                | `arr           | `NamedL        |
|                |                | map`{.literal} | ist`{.literal} |
|                |                |                | is represented |
|                |                |                | as an array of |
|                |                |                | JSON objects.  |
|                |                |                |                |
|                |                |                | [**Input:**    |
|                |                |                | ]{.strong}`Nam |
|                |                |                | edList("a"=1,  |
|                |                |                | "bar"="foo", n |
|                |                |                | ull=3, null=nu |
|                |                |                | ll)`{.literal} |
|                |                |                |                |
|                |                |                | [**Ou          |
|                |                |                | tput:**]{.stro |
|                |                |                | ng}`[{"a":1},  |
|                |                |                | {"b":2}, 3, nu |
|                |                |                | ll]`{.literal} |
+----------------+----------------+----------------+----------------+
|                |                | `arr           | `NamedL        |
|                |                | ntv`{.literal} | ist`{.literal} |
|                |                |                | is represented |
|                |                |                | as an array of |
|                |                |                | name type      |
|                |                |                | value JSON     |
|                |                |                | objects.       |
|                |                |                |                |
|                |                |                | [**Input:**    |
|                |                |                | ]{.strong}`Nam |
|                |                |                | edList("a"=1,  |
|                |                |                | "bar"="foo", n |
|                |                |                | ull=3, null=nu |
|                |                |                | ll)`{.literal} |
|                |                |                |                |
|                |                |                | [**Output:**   |
|                |                |                | ]{.strong}`[{" |
|                |                |                | name":"a","typ |
|                |                |                | e":"int","valu |
|                |                |                | e":1}, {"name" |
|                |                |                | :"bar","type": |
|                |                |                | "str","value": |
|                |                |                | "foo"}, {"name |
|                |                |                | ":null,"type": |
|                |                |                | "int","value": |
|                |                |                | 3}, {"name":nu |
|                |                |                | ll,"type":"nul |
|                |                |                | l","value":nul |
|                |                |                | l}]`{.literal} |
+----------------+----------------+----------------+----------------+
| `json.         | Adds a wrapper | `funct         |                |
| wrf`{.literal} | function       | ion`{.literal} |                |
|                | around the     |                |                |
|                | JSON response. |                |                |
|                | Useful in AJAX |                |                |
|                | with dynamic   |                |                |
|                | script tags    |                |                |
|                | for specifying |                |                |
|                | a JavaScript   |                |                |
|                | callback       |                |                |
|                | function.      |                |                |
+----------------+----------------+----------------+----------------+
:::

[**Example:**]{.strong} Searching for `id=SP2514N`{.literal}.

[**URL:**]{.strong}`http://localhost:8983/solr/techproducts/select?q=SP2514N&wt=json.`{.literal}

[**Response:**]{.strong}

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":1,
 "params":{
 "q":"SP2514N",
 "wt":"json",
 "_":"1514797189592"}},
 "response":{"numFound":1,"start":0,"docs":[
 {
 "id":"SP2514N",
 "name":"Samsung SpinPoint P120 SP2514N - hard drive - 250 GB - ATA-133",
 "manu":"Samsung Electronics Co. Ltd.",
 "manu_id_s":"samsung",
 "cat":["electronics",
 "hard drive"],
 "features":["7200RPM, 8MB cache, IDE Ultra ATA-133",
 "NoiseGuard, SilentSeek technology, Fluid Dynamic Bearing (FDB) motor"],
 "price":92.0,
 "price_c":"92.0,USD",
 "popularity":6,
 "inStock":true,
 "manufacturedate_dt":"2006-02-13T15:26:37Z",
 "store":"35.0752,-97.032",
 "_version_":1583131913641525248,
 "price_c____l_ns":9200}]
 }
}
```
:::

We can analyze response writer using[]{#id288618892 .indexterm} the Solr
console admin as well. Go to the Solr console admin, select
**`techproducts`**, and click on the **`Query`** tab. Insert your text
query in the `q`{.literal} field, select the `wt`{.literal} parameter as
`json`{.literal} and click on the **`Execute Query`** button at the
bottom. The query URL and response output will be displayed as follows:

::: {.mediaobject}
![](5_files/6861b886-f008-490d-a55e-bfc276ddad7f.png)
:::

Solr console admin, response writer configuration, and output
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec89}Standard XML {#standard-xml .title}

</div>

</div>
:::

The standard XML response writer is the[]{#id288340006 .indexterm} most
common and usable response writer in Solr.

[**Standard XML response writer parameters**]{.strong}:

::: {.informaltable}
  -------------------------- -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------------------------------------------------------------------------
  [**Parameter**]{.strong}   [**Behavior**]{.strong}                                                                                                                                                                                              [**Default Value**]{.strong}
  `version`{.literal}        The `version`{.literal} parameter determines the XML protocol used in the response. The advantage of setting this parameter is that the response format remains the same even if the Solr version gets an upgrade.   The default value is the latest supported one. The only currently supported version value is 2.2.
  `stylesheet`{.literal}     It includes a `<?xml-stylesheet type="text/xsl" href="…​"?>`{.literal} declaration in the XML response.                                                                                                              Solr does not return any style sheet declaration by default.
  `indent`{.literal}         Indenting the XML response for a more human-readable format.                                                                                                                                                         By default, Solr will not indent the XML response.
  -------------------------- -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------------------------------------------------------------------------
:::

 

[**Example**]{.strong}: Searching for query `id=SP2514N`{.literal} and
retrieving a response in XML format.

Solr admin console, searching for product `id=SP2514N`{.literal}, and
retrieving the response in XML format:

::: {.mediaobject}
![](5_files/97bd976d-fb25-4212-892b-ff236235dc22.png)
:::

[**URL**`:`****]{.strong} `http://localhost:8983/solr/techproducts/select?q=SP2514N&wt=xml`{.literal}

[**Response**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<?xml version="1.0" encoding="UTF-8"?>
<response>
<lst name="responseHeader">
 <int name="status">0</int>
 <int name="QTime">0</int>
 <lst name="params">
 <str name="q">SP2514N</str>
 <str name="wt">xml</str>
 <str name="_">1514797189592</str>
 </lst>
</lst>
<result name="response" numFound="1" start="0">
 <doc>
 <str name="id">SP2514N</str>
 <str name="name">Samsung SpinPoint P120 SP2514N - hard drive - 250 GB - ATA-133</str>
 <str name="manu">Samsung Electronics Co. Ltd.</str>
 <str name="manu_id_s">samsung</str>
 <arr name="cat">
 <str>electronics</str>
 <str>hard drive</str>
 </arr>
 <arr name="features">
 <str>7200RPM, 8MB cache, IDE Ultra ATA-133</str>
 <str>NoiseGuard, SilentSeek technology, Fluid Dynamic Bearing (FDB) motor</str>
 </arr>
 <float name="price">92.0</float>
 <str name="price_c">92.0,USD</str>
 <int name="popularity">6</int>
 <bool name="inStock">true</bool>
 <date name="manufacturedate_dt">2006-02-13T15:26:37Z</date>
 <str name="store">35.0752,-97.032</str>
 <long name="_version_">1583131913641525248</long>
 <long name="price_c____l_ns">9200</long></doc>
</result>
</response>
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec90}CSV {#csv .title}

</div>

</div>
:::

This returns the[]{#id288340148 .indexterm} results in CSV format. Some
information (like facet) will be excluded from the CSV response. The CSV
response writer supports multi-valued fields as well as pseudo-fields,
and the output of this CSV format is compatible with Solr\'s CSV update
format.

[**CSV response writer parameters**]{.strong}:

::: {.informaltable}
  ------------------------------ ------------------------------------------------------------------------------ ------------------------------
  [**Parameter**]{.strong}       [**Behavior**]{.strong}                                                        [**Default value**]{.strong}
  `csv.encapsulator`{.literal}   Specifies a character to be used as an encapsulator in the response            `"`{.literal}
  `csv.escape`{.literal}         Specifies the items to be escaped from the response                            `None`{.literal}
  `csv.separator`{.literal}      Specifies the separator for the CSV response                                   `,`{.literal}
  `csv.header`{.literal}         Indicates whether to print header information in the CSV response or not       `true`{.literal}
  `csv.newline`{.literal}        Used to start a new line from this parameter value                             `\n`{.literal}
  `csv.null`{.literal}           Specifies the value to be returned in the response instead of returning null   Zero-length string
  ------------------------------ ------------------------------------------------------------------------------ ------------------------------
:::

 

[**Example:**]{.strong} Searching for query `id=SP2514N`{.literal} and
retrieving the response in `.csv`{.literal} format.

Solr admin console, searching for product `id=SP2514N`{.literal}, and
retrieving the response in `.csv`{.literal} format:

::: {.mediaobject}
![](5_files/b1008006-a69b-496a-8aa4-96b37c1c2bfb.png)
:::

[**URL:**]{.strong} `http://localhost:8983/solr/techproducts/select?q=SP2514N&wt=csv`{.literal}

[**Response:**]{.strong}

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
payloads,manu_id_s,manu,address_s,weight,includes,incubationdate_dt,store,manufacturedate_dt,features,price_c,price,cat,popularity,name,inStock,id,compName_s
"",samsung,Samsung Electronics Co. Ltd.,,,,,"35.0752\,-97.032",2006-02-13T15:26:37Z,"7200RPM\, 8MB cache\, IDE Ultra ATA-133,NoiseGuard\, SilentSeek technology\, Fluid Dynamic Bearing (FDB) motor","92.0\,USD",92.0,"electronics,hard drive",6,Samsung SpinPoint P120 SP2514N - hard drive - 250 GB - ATA-133,true,SP2514N,
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec91}Velocity {#velocity .title}

</div>

</div>
:::

Solr supports a velocity[]{#id288377676 .indexterm} response writer,
which is used[]{#id288377686 .indexterm} in Velocity UI to demonstrate
some[]{#id288377694 .indexterm} core search features:
[**faceting**]{.strong}, [**highlighting**]{.strong},
[**autocomplete**]{.strong}, and [**geospatial**]{.strong} searching.
velocity response[]{#id288377720 .indexterm} writer is an
optional[]{#id288377726 .indexterm} plugin available in the
`contrib/velocity`{.literal} directory.

To use velocity response writer, we must include its `.jar`{.literal}
file and all dependencies in the `lib`{.literal} folder, and configure
in `solrconfig.xml`{.literal} as follows:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<queryResponseWriter name="velocity" class="solr.VelocityResponseWriter">
 <str name="template.base.dir">${velocity.template.base.dir:}</str>
<!--
 <str name="init.properties.file">velocity-init.properties</str>
 <bool name="params.resource.loader.enabled">true</bool>
 <bool name="solr.resource.loader.enabled">false</bool>
 <lst name="tools">
 <str name="mytool">com.example.MyCustomTool</str>
 </lst>
-->
</queryResponseWriter>
```
:::

Here, we have not configured the initialization and request parameters
but as per our needs, we can configure them.

We have almost finished looking at search configuration components such
as relevance, various query parsers, and response writers. Now let\'s
take a deep dive and and explore various search result operations:
faceting, clustering, highlighting, and so on.



[]{#ch06lvl1sec45}Faceting {#faceting .title style="clear: both"}
--------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Faceting is the mechanism provided by Solr to categorize
results[]{#id288339991 .indexterm} in a meaningful arrangement on
indexed fields. Using faceting, the end user will be provided with
categorized results, along with a matching count for that search. Now
the user can explore the search results, drill down to any result, and
thus find an exactly matching result in which they are interested.

There are many types of faceting provided by Solr. Here is a list of
faceting types that Solr currently supports:

::: {.itemizedlist}
-   Range faceting
-   Pivot (decision tree) faceting
-   Interval faceting
:::

We will explore these later in this chapter. But to configure any
faceting in Solr, first we have to configure the related parameters. So
let\'s understand faceting parameters first.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec92}Common parameters {#common-parameters .title}

</div>

</div>
:::

These are the common parameters[]{#id288377777 .indexterm} for all types
of faceting:

::: {.informaltable}
  -------------------------- ---------------------------------------------------------------------------------------------------------- ------------------------------
  [**Parameter**]{.strong}   [**Behavior**]{.strong}                                                                                    [**Default value**]{.strong}
  `facet`{.literal}          Enable or disable faceting.                                                                                `false`{.literal}
  `facet.query`{.literal}    Specifies a faceting query, which overrides Solr\'s default faceting query and returns a faceting count.   
  -------------------------- ---------------------------------------------------------------------------------------------------------- ------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec93}Field-value faceting parameters {#field-value-faceting-parameters .title}

</div>

</div>
:::

Field-value parameters[]{#id288378497 .indexterm} are used to trigger
faceting based on the indexed terms in a field. By default, all
field-value faceting parameters can[]{#id288378506 .indexterm} be
specified on a per field basis with the syntax of
`f.<fieldname>.facet.<parameter>`{.literal}:

::: {.informaltable}
+----------------------+----------------------+----------------------+
| [**P                 | [**                  | [**Default           |
| arameter**]{.strong} | Behavior**]{.strong} | value**]{.strong}    |
+----------------------+----------------------+----------------------+
| `fa                  | Identifies a field   |                      |
| cet.field`{.literal} | that should be       |                      |
|                      | treated as a facet.  |                      |
|                      | At least one field   |                      |
|                      | must have this       |                      |
|                      | parameter;           |                      |
|                      | otherwise, none of   |                      |
|                      | the other            |                      |
|                      | field-value faceting |                      |
|                      | parameters will have |                      |
|                      | any effect.          |                      |
+----------------------+----------------------+----------------------+
| `fac                 | Limits facet values  |                      |
| et.prefix`{.literal} | to terms beginning   |                      |
|                      | with the string      |                      |
|                      | specified.           |                      |
+----------------------+----------------------+----------------------+
| `facet               | Limits facet values  |                      |
| .contains`{.literal} | to terms containing  |                      |
|                      | the                  |                      |
|                      | string specified.    |                      |
+----------------------+----------------------+----------------------+
| `facet.contains.i    | If                   |                      |
| gnoreCase`{.literal} | `facet               |                      |
|                      | .contains`{.literal} |                      |
|                      | is used, the         |                      |
|                      | `facet.contains.i    |                      |
|                      | gnoreCase`{.literal} |                      |
|                      | parameter causes     |                      |
|                      | cases to be ignored  |                      |
|                      | when matching the    |                      |
|                      | given substring      |                      |
|                      | against the          |                      |
|                      | candidate facet      |                      |
|                      | terms.               |                      |
+----------------------+----------------------+----------------------+
| `fa                  | Specifies the        | `100`{.literal}      |
| cet.limit`{.literal} | maximum number of    |                      |
|                      | constraint counts    |                      |
|                      | that should be       |                      |
|                      | returned for the     |                      |
|                      | facet fields. The    |                      |
|                      | possible values are  |                      |
|                      | positive and         |                      |
|                      | negative. Providing  |                      |
|                      | any negative value   |                      |
|                      | indicates that Solr  |                      |
|                      | will return an       |                      |
|                      | unlimited number of  |                      |
|                      | constraint counts.   |                      |
+----------------------+----------------------+----------------------+
| `f                   | Determines the       |                      |
| acet.sort`{.literal} | ordering of the      |                      |
|                      | facet field          |                      |
|                      | constraints.         |                      |
|                      | Possible values are: |                      |
|                      |                      |                      |
|                      | ::: {.itemizedlist}  |                      |
|                      | -   `cou             |                      |
|                      | nt`{.literal}: Sorts |                      |
|                      |     constraints      |                      |
|                      |     based on the     |                      |
|                      |     count (high to   |                      |
|                      |     low)             |                      |
|                      | -                    |                      |
|                      |   `index`{.literal}: |                      |
|                      |     Sorts            |                      |
|                      |     constraints      |                      |
|                      |     based on their   |                      |
|                      |     index order      |                      |
|                      | :::                  |                      |
|                      |                      |                      |
|                      | The default sorting  |                      |
|                      | is based on the      |                      |
|                      | index, but if the    |                      |
|                      | limit parameter      |                      |
|                      | (`fac                |                      |
|                      | et.limit`{.literal}) |                      |
|                      | is greater than      |                      |
|                      | zero, the default    |                      |
|                      | sorting will be the  |                      |
|                      | count.               |                      |
+----------------------+----------------------+----------------------+
| `fac                 | Allows paging        | `0`{.literal}        |
| et.offset`{.literal} | through facet        |                      |
|                      | values. The offset   |                      |
|                      | defines how many of  |                      |
|                      | the top values to    |                      |
|                      | skip instead of      |                      |
|                      | returning later      |                      |
|                      | facet values.        |                      |
+----------------------+----------------------+----------------------+
| `facet               | Specifies the        | `0`{.literal}        |
| .mincount`{.literal} | minimum counts       |                      |
|                      | required for a facet |                      |
|                      | field to be included |                      |
|                      | in the response. If  |                      |
|                      | a field\'s counts    |                      |
|                      | are less than the    |                      |
|                      | minimum, the         |                      |
|                      | field\'s facet is    |                      |
|                      | not returned.        |                      |
+----------------------+----------------------+----------------------+
| `face                | Specifies whether or | `false`{.literal}    |
| t.missing`{.literal} | not the count of all |                      |
|                      | matching documents   |                      |
|                      | that do not have any |                      |
|                      | values is to be      |                      |
|                      | returned in the      |                      |
|                      | facet\'s field.      |                      |
+----------------------+----------------------+----------------------+
| `fac                 | Specifies the type   | `fc`{.literal}       |
| et.method`{.literal} | of algorithm or      |                      |
|                      | method Solr should   |                      |
|                      | use when faceting a  |                      |
|                      | field. The available |                      |
|                      | methods in Solr are: |                      |
|                      |                      |                      |
|                      | ::: {.itemizedlist}  |                      |
|                      | -   `enum`           |                      |
|                      | {.literal}: Iterates |                      |
|                      |     over all the     |                      |
|                      |     terms in the     |                      |
|                      |     index,           |                      |
|                      |     calculating a    |                      |
|                      |     set intersection |                      |
|                      |     with those terms |                      |
|                      |     and the query.   |                      |
|                      |     This method      |                      |
|                      |     is faster for    |                      |
|                      |     fields that      |                      |
|                      |     contain fewer    |                      |
|                      |     values.          |                      |
|                      | -   `fc`             |                      |
|                      | {.literal}: Iterates |                      |
|                      |     over documents   |                      |
|                      |     that match the   |                      |
|                      |     query and finds  |                      |
|                      |     the terms within |                      |
|                      |     those            |                      |
|                      |     documents. The   |                      |
|                      |     fc method is     |                      |
|                      |     faster for       |                      |
|                      |     fields that      |                      |
|                      |     contain many     |                      |
|                      |     unique values.   |                      |
|                      | -   `fcs`{.literal}: |                      |
|                      |     Performs         |                      |
|                      |     per-segment      |                      |
|                      |     field faceting   |                      |
|                      |     for              |                      |
|                      |     single-valued    |                      |
|                      |     string fields.   |                      |
|                      |     This method      |                      |
|                      |     performs better  |                      |
|                      |     faceting if the  |                      |
|                      |     index is         |                      |
|                      |     changing         |                      |
|                      |     constantly. It   |                      |
|                      |     also accepts a   |                      |
|                      |     threads local    |                      |
|                      |     param, which can |                      |
|                      |     speed up         |                      |
|                      |     faceting.        |                      |
|                      | :::                  |                      |
+----------------------+----------------------+----------------------+
| `facet.enum.ca       | Specifies the        | `0`{.literal}        |
| che.minDf`{.literal} | minimum number of    |                      |
|                      | documents required   |                      |
|                      | to match a term      |                      |
|                      | before               |                      |
|                      | `fi                  |                      |
|                      | lterCache`{.literal} |                      |
|                      | should be used for   |                      |
|                      | that term.           |                      |
|                      |                      |                      |
|                      | The default is       |                      |
|                      | `0`{.literal}, which |                      |
|                      | means                |                      |
|                      | `fi                  |                      |
|                      | lterCache`{.literal} |                      |
|                      | should always be     |                      |
|                      | used.                |                      |
+----------------------+----------------------+----------------------+
| `fac                 | To cap facet counts  | `false`{.literal}    |
| et.exists`{.literal} | by 1, specify        |                      |
|                      | `facet.exi           |                      |
|                      | sts=true`{.literal}. |                      |
|                      | This parameter can   |                      |
|                      | be used with         |                      |
|                      | `facet.me            |                      |
|                      | thod=enum`{.literal} |                      |
|                      | or when it\'s        |                      |
|                      | omitted. It can be   |                      |
|                      | used only on on-trie |                      |
|                      | fields (such as      |                      |
|                      | strings). It may     |                      |
|                      | speed up facet       |                      |
|                      | counting on large    |                      |
|                      | indices and/or       |                      |
|                      | high-cardinality     |                      |
|                      | facet values.        |                      |
+----------------------+----------------------+----------------------+
| `facet.exc           | Removes the          |                      |
| ludeTerms`{.literal} | specified terms from |                      |
|                      | facet counts but     |                      |
|                      | keeps them in the    |                      |
|                      | index.               |                      |
+----------------------+----------------------+----------------------+
| `face                | Specifies the number |                      |
| t.threads`{.literal} | of threads to        |                      |
|                      | execute for faceting |                      |
|                      | the fields in        |                      |
|                      | parallel. Specifying |                      |
|                      | the thread count as  |                      |
|                      | 0 will not create    |                      |
|                      | any threads, and     |                      |
|                      | only the main        |                      |
|                      | request thread will  |                      |
|                      | be used. Specifying  |                      |
|                      | a negative number of |                      |
|                      | threads will create  |                      |
|                      | up to                |                      |
|                      | `Integer.            |                      |
|                      | MAX_VALUE`{.literal} |                      |
|                      | threads.             |                      |
+----------------------+----------------------+----------------------+
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec94}Range faceting {#range-faceting .title}

</div>

</div>
:::

Range faceting can be done[]{#id288341026 .indexterm} on date
fields[]{#id288341034 .indexterm} and numeric fields.

[**Range faceting parameters**]{.strong}:

::: {.informaltable}
+-----------------------+-----------------------+--------------------+
| [**                   | [*                    | Default value      |
| Parameter**]{.strong} | *Behavior**]{.strong} |                    |
+-----------------------+-----------------------+--------------------+
| `f                    | Specifies the field   |                    |
| acet.range`{.literal} | for which Solr should |                    |
|                       | create range facets.  |                    |
|                       | For example:          |                    |
|                       |                       |                    |
|                       | `face                 |                    |
|                       | t.range=salary&facet. |                    |
|                       | range=rank`{.literal} |                    |
|                       |                       |                    |
|                       | `facet.range=c        |                    |
|                       | reatedDate`{.literal} |                    |
+-----------------------+-----------------------+--------------------+
| `facet.r              | Specifies from where  |                    |
| ange.start`{.literal} | (lower bound) the     |                    |
|                       | range starts. For     |                    |
|                       | example:              |                    |
|                       |                       |                    |
|                       | `f.salary.            |                    |
|                       | facet.range.start=100 |                    |
|                       | 00.0&f.rank.facet.ran |                    |
|                       | ge.start=1`{.literal} |                    |
|                       |                       |                    |
|                       | `f.createdDate.f      |                    |
|                       | acet.range.start=NOW/ |                    |
|                       | DAY-30DAYS`{.literal} |                    |
+-----------------------+-----------------------+--------------------+
| `facet                | Specifies where       |                    |
| .range.end`{.literal} | (upper bound) the     |                    |
|                       | range ends. For       |                    |
|                       | example:              |                    |
|                       |                       |                    |
|                       | `f.salar              |                    |
|                       | y.facet.range.end=100 |                    |
|                       | 000.0&f.rank.facet.ra |                    |
|                       | nge.end=50`{.literal} |                    |
|                       |                       |                    |
|                       | `f.createdDate        |                    |
|                       | .facet.range.end=NOW/ |                    |
|                       | DAY+30DAYS`{.literal} |                    |
+-----------------------+-----------------------+--------------------+
| `facet                | The size of each      |                    |
| .range.gap`{.literal} | range will be added   |                    |
|                       | to the                |                    |
|                       | `lower`{.literal}     |                    |
|                       | bound successively    |                    |
|                       | until the             |                    |
|                       | `upper`{.literal}     |                    |
|                       | bound is reached.     |                    |
+-----------------------+-----------------------+--------------------+
| `facet.ran            | A Boolean parameter   | `false`{.literal}  |
| ge.hardend`{.literal} | that specifies how    |                    |
|                       | Solr should handle    |                    |
|                       | cases where           |                    |
|                       | `facet                |                    |
|                       | .range.gap`{.literal} |                    |
|                       | does not divide       |                    |
|                       | evenly between        |                    |
|                       | `lower`{.literal}     |                    |
|                       | bound and             |                    |
|                       | `upper`{.literal}     |                    |
|                       | bound.                |                    |
|                       |                       |                    |
|                       | If it is              |                    |
|                       | `true`{.literal}, the |                    |
|                       | last range constraint |                    |
|                       | will have the         |                    |
|                       | `facet                |                    |
|                       | .range.end`{.literal} |                    |
|                       | value as an           |                    |
|                       | `upper`{.literal}     |                    |
|                       | bound. If             |                    |
|                       | `false`{.literal},    |                    |
|                       | the last range will   |                    |
|                       | have the smallest     |                    |
|                       | possible              |                    |
|                       | `upper`{.literal}     |                    |
|                       | bound greater than    |                    |
|                       | `facet                |                    |
|                       | .range.end`{.literal} |                    |
|                       | such that the range   |                    |
|                       | is the exact width of |                    |
|                       | the specified range   |                    |
|                       | gap.                  |                    |
+-----------------------+-----------------------+--------------------+
| `facet.ran            | Determines how to     |                    |
| ge.include`{.literal} | compute range         |                    |
|                       | faceting between the  |                    |
|                       | lower bound and upper |                    |
|                       | bound. Possible       |                    |
|                       | values are:           |                    |
|                       |                       |                    |
|                       | ::: {.itemizedlist}   |                    |
|                       | -                     |                    |
|                       |    `lower`{.literal}: |                    |
|                       |     All gap-based     |                    |
|                       |     ranges include    |                    |
|                       |     their             |                    |
|                       |     `lower`{.literal} |                    |
|                       |     bound             |                    |
|                       | -                     |                    |
|                       |    `upper`{.literal}: |                    |
|                       |     All gap-based     |                    |
|                       |     ranges include    |                    |
|                       |     their             |                    |
|                       |     `upper`{.literal} |                    |
|                       |     bound             |                    |
|                       | -   `edge`{.literal}: |                    |
|                       |     The first and     |                    |
|                       |     last gap ranges   |                    |
|                       |     include their     |                    |
|                       |     `edge`{.literal}  |                    |
|                       |     bounds            |                    |
|                       |                       |                    |
|                       |    (`lower`{.literal} |                    |
|                       |     for the first     |                    |
|                       |     one,              |                    |
|                       |     `upper`{.literal} |                    |
|                       |     for the last one) |                    |
|                       |     even if the       |                    |
|                       |     corresponding     |                    |
|                       |     `upper`{.liter    |                    |
|                       | al}/`lower`{.literal} |                    |
|                       |     option is not     |                    |
|                       |     specified         |                    |
|                       | -                     |                    |
|                       |    `outer`{.literal}: |                    |
|                       |     The               |                    |
|                       |                       |                    |
|                       |    `before`{.literal} |                    |
|                       |     and               |                    |
|                       |     `after`{.literal} |                    |
|                       |     ranges will be    |                    |
|                       |     inclusive of      |                    |
|                       |     their bounds even |                    |
|                       |     if the first or   |                    |
|                       |     last ranges       |                    |
|                       |     already include   |                    |
|                       |     those boundaries  |                    |
|                       | -   `all`{.literal}:  |                    |
|                       |     Includes all      |                    |
|                       |     options           |                    |
|                       | ---`lower`{.literal}, |                    |
|                       |                       |                    |
|                       |    `upper`{.literal}, |                    |
|                       |     `edge`{.literal}, |                    |
|                       |     and               |                    |
|                       |                       |                    |
|                       |    `outer`{.literal}. |                    |
|                       | :::                   |                    |
+-----------------------+-----------------------+--------------------+
| `facet.r              | Specifies that, in    |                    |
| ange.other`{.literal} | addition to the       |                    |
|                       | counts for each range |                    |
|                       | between               |                    |
|                       | `lower`{.literal}     |                    |
|                       | bound and             |                    |
|                       | `upper`{.literal}     |                    |
|                       | bound, counts should  |                    |
|                       | be computed for these |                    |
|                       | options as well:      |                    |
|                       |                       |                    |
|                       | ::: {.itemizedlist}   |                    |
|                       | -                     |                    |
|                       |   `before`{.literal}: |                    |
|                       |     All records with  |                    |
|                       |     field values      |                    |
|                       |     lower than        |                    |
|                       |     `lower`{.literal} |                    |
|                       |     bound of the      |                    |
|                       |     first range       |                    |
|                       | -                     |                    |
|                       |    `after`{.literal}: |                    |
|                       |     All records with  |                    |
|                       |     field values      |                    |
|                       |     greater than the  |                    |
|                       |     `upper`{.literal} |                    |
|                       |     bound of the last |                    |
|                       |     range             |                    |
|                       | -                     |                    |
|                       |  `between`{.literal}: |                    |
|                       |     All records with  |                    |
|                       |     field values      |                    |
|                       |     between the start |                    |
|                       |     and end bounds of |                    |
|                       |     all ranges        |                    |
|                       | -   `none`{.literal}: |                    |
|                       |     Do not compute    |                    |
|                       |     any counts        |                    |
|                       | -   `all`{.literal}:  |                    |
|                       |     Compute counts    |                    |
|                       |     for               |                    |
|                       |                       |                    |
|                       |   `before`{.literal}, |                    |
|                       |                       |                    |
|                       |  `between`{.literal}, |                    |
|                       |     and               |                    |
|                       |     `after`{.literal} |                    |
|                       | :::                   |                    |
+-----------------------+-----------------------+--------------------+
| `facet.ra             | Specifies a faceting  | `filter`{.literal} |
| nge.method`{.literal} | method:               |                    |
|                       |                       |                    |
|                       | ::: {.itemizedlist}   |                    |
|                       | -   `filter`          |                    |
|                       | {.literal}: Generates |                    |
|                       |     the ranges based  |                    |
|                       |     on other          |                    |
|                       |     `f                |                    |
|                       | acet.range`{.literal} |                    |
|                       |     parameters.       |                    |
|                       | -   `div`{.literal}:  |                    |
|                       |     Iterates all the  |                    |
|                       |     documents that    |                    |
|                       |     match the main    |                    |
|                       |     query, and for    |                    |
|                       |     each of them, it  |                    |
|                       |     finds the correct |                    |
|                       |     range for the     |                    |
|                       |     value. Not        |                    |
|                       |     supporting for    |                    |
|                       |     `Date             |                    |
|                       | RangeField`{.literal} |                    |
|                       |     field type or     |                    |
|                       |     when we have used |                    |
|                       |     `gro              |                    |
|                       | up.facets`{.literal}. |                    |
|                       | :::                   |                    |
+-----------------------+-----------------------+--------------------+
:::

 

[**Example**]{.strong}: Search query for `iPod`{.literal} with faceting
enabled and range for the field price from `1000`{.literal} to
`100000`{.literal}

[**URL**]{.strong}: `http://localhost:8983/solr/techproducts/select?defType=edismax&q=ipod&fl=id,name,price&facet=true&facet.range=price&facet.range.start=10&facet.range.end=20&facet.range.gap=5`{.literal}

[**Response**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":1,
 "params":{
 "facet.range":"price",
 "q":"ipod",
 "defType":"edismax",
 "facet.range.gap":"5",
 "fl":"id,name,price",
 "facet":"true",
 "facet.range.start":"10",
 "facet.range.end":"20"}},
 "response":{"numFound":3,"start":0,"docs":[
 {
 "id":"IW-02",
 "name":"iPod & iPod Mini USB 2.0 Cable",
 "price":11.5},
 {
 "id":"F8V7067-APL-KIT",
 "name":"Belkin Mobile Power Cord for iPod w/ Dock",
 "price":19.95},
 {
 "id":"MA147LL/A",
 "name":"Apple 60 GB iPod with Video Playback Black",
 "price":399.0}]
 },
 "facet_counts":{
 "facet_queries":{},
 "facet_fields":{},
 "facet_ranges":{
 "price":{
 "counts":[
 "10.0",1,
 "15.0",1],
 "gap":5.0,
 "start":10.0,
 "end":20.0}},
 "facet_intervals":{},
 "facet_heatmaps":{}}}
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec95}Pivot faceting {#pivot-faceting .title}

</div>

</div>
:::

Pivot faceting is similar to pivot[]{#id288340557 .indexterm} tables in
the latest spreadsheets. Pivot faceting provides a facility to generate
an aggregate summary from fetched[]{#id288340566 .indexterm} faceting
results on multiple fields:

::: {.informaltable}
  ---------------------------------- ----------------------------------------------------------------------------------------------------------------- ------------------------------
  [**Parameter**]{.strong}           [**Behavior**]{.strong}                                                                                           [**Default value**]{.strong}
  `facet.pivot`{.literal}            Specify the field on which you want to apply pivoting                                                             
  `facet.pivot.mincount`{.literal}   Specify the minimum number of documents that need to match in order for the facet to be included in the results   `1`{.literal}
  ---------------------------------- ----------------------------------------------------------------------------------------------------------------- ------------------------------
:::

 

[**Example**]{.strong}:  In our `techproducts`{.literal}, we need the
stock availability based on the popularity of a category

[**URL**]{.strong}: `http://localhost:8983/solr/techproducts/select?q=*:*&facet.pivot=cat,popularity,inStock&facet.pivot=popularity,cat&facet=true&facet.field=cat&facet.limit=5&rows=0&facet.pivot.mincount=2`{.literal}

[**Response**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{ 
"facet_counts":{
 "facet_queries":{},
 "facet_fields":{
 "cat":[
 "electronics",14,
 "currency",4,
 "memory",3,
 "connector",2,
 "graphics card",2]},
 "facet_dates":{},
 "facet_ranges":{},
 "facet_pivot":{
 "cat,popularity,inStock":[{
 "field":"cat",
 "value":"electronics",
 "count":14,
 "pivot":[{
 "field":"popularity",
 "value":6,
 "count":5,
 "pivot":[{
 "field":"inStock",
 "value":true,
 "count":5}]}]
}]}}}
```
:::

 
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec96}Interval faceting {#interval-faceting .title}

</div>

</div>
:::

Interval faceting is similar to range[]{#id288340673 .indexterm}
faceting, but it allows us to set variable intervals and count the
number of documents that have values within[]{#id288340681 .indexterm}
those intervals in the specified field. Interval faceting is likely to
be better with multiple intervals for the same fields, while a facet
query is likely to be better in environments where a filter cache is
more effective:

::: {.informaltable}
+----------------------+----------------------+----------------------+
| [**P                 | [**                  | [**Default           |
| arameter**]{.strong} | Behavior**]{.strong} | value**]{.strong}    |
+----------------------+----------------------+----------------------+
| `facet               | To specify a field   |                      |
| .interval`{.literal} | where we want to     |                      |
|                      | apply the interval.  |                      |
|                      | It can be used       |                      |
|                      | multiple times for   |                      |
|                      | multiple fields in a |                      |
|                      | single request. For  |                      |
|                      | example:             |                      |
|                      |                      |                      |
|                      | `facet.interval=pr   |                      |
|                      | ice&facet.interval=p |                      |
|                      | opularity`{.literal} |                      |
+----------------------+----------------------+----------------------+
| `facet.int           | To specify a set of  |                      |
| erval.set`{.literal} | intervals for the    |                      |
|                      | field. It can be     |                      |
|                      | specified multiple   |                      |
|                      | times to indicate    |                      |
|                      | multiple intervals.  |                      |
|                      | For example:         |                      |
|                      |                      |                      |
|                      | `                    |                      |
|                      | f.price.facet.interv |                      |
|                      | al.set=[0,10]&f.pric |                      |
|                      | e.facet.interval.set |                      |
|                      | =(10,100]`{.literal} |                      |
|                      |                      |                      |
|                      | (1,100) -\> include  |                      |
|                      | values greater than  |                      |
|                      | 1 and lower than 100 |                      |
|                      | \[1,100) -\> include |                      |
|                      | values greater or    |                      |
|                      | equal to 1 and lower |                      |
|                      | than 100 \[1,100\]   |                      |
|                      | -\> include values   |                      |
|                      | greater or equal to  |                      |
|                      | 1 and lower or equal |                      |
|                      | to 100               |                      |
+----------------------+----------------------+----------------------+
:::

 

[**Example**]{.strong}: Faceting query for field `price >=10`{.literal}
and `price < 20`{.literal}

[**URL**]{.strong}: `http://localhost:8983/solr/techproducts/select?q=*:*&facet=true&facet.interval=price&f.price.facet.interval.set=[10,20)`{.literal}

[**Response**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
"facet_counts":{
 "facet_queries":{},
 "facet_fields":{},
 "facet_ranges":{},
 "facet_intervals":{
 "price":{
 "[10,20)":2}},
 "facet_heatmaps":{}
 }
```



[]{#ch06lvl1sec46}Highlighting {#highlighting .title style="clear: both"}
------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Solr supports a feature called [**highlighting**]{.strong} that helps
end users who are running a query to scan results quickly. Providing a
matching term in bold and[]{#id288339956 .indexterm} highlighted the
format makes it an extremely satisfying experience for the user. With
highlighting, the user can quickly determine the terms they are
searching for or make a decision that the provided results do not match
their expectations, and lets them[]{#id288339964 .indexterm} move to
next query.

Solr comes with[]{#id288339973 .indexterm} a great
configuration[]{#id288339979 .indexterm} for highlighting. There are
many[]{#id288339986 .indexterm} parameters for [**fragment
sizing**]{.strong}, [**formatting**]{.strong}, [**ordering**]{.strong},
[**backup.alternate behavior**]{.strong}, and
[**categorization**]{.strong}. Fragments or snippets[]{#id288341120
.indexterm} are parts of the response that contain matching terms.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec97}Highlighting parameters {#highlighting-parameters .title}

</div>

</div>
:::

Solr provides a large list for highlighting[]{#id288376064 .indexterm}
fragments. The following are the basic parameters required to start
highlighting:

::: {.informaltable}
  -------------------------- --------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
  [**Parameter**]{.strong}   [**Behavior**]{.strong}                                                                                                                       [**Default value**]{.strong}
  `hl`{.literal}             A Boolean parameter to enable/disable highlighting. `hl=true`{.literal} will enable highlighting.                                             `false`{.literal}
  `hl.method`{.literal}      To specify a method to implement highlighting. Available methods are `unified`{.literal}, `original`{.literal}, and `fastVector`{.literal}.   `original`{.literal}
  -------------------------- --------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec98}Highlighter {#highlighter .title}

</div>

</div>
:::

Highlighter is nothing but a highlighting[]{#id288378586 .indexterm}
implemented method that actually performs the activity. There are three
methods available for highlighting. They
are `unified`{.literal}, `original`{.literal}, and
`fastVector`{.literal}. To implement highlighting, first we need to
specify one method to `hl.method`{.literal}. If we do not select any
method, the default original method performs the activity.

There are many parameters supported by highlighters. Sometimes, the
implementation details and semantics will be a bit different, so we
can\'t expect identical results when switching highlighters. Normally,
highlighter selection is done via the `hl.method`{.literal} parameter,
but we can also explicitly configure an implementation by class name in
`solrconfig.xml`{.literal}. Let\'s explore highlighters in detail.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec48}Unified highlighter (hl.method=unified) {#unified-highlighter-hl.methodunified .title}

</div>

</div>
:::

Unified highlighter is the new highlighter[]{#id288601063 .indexterm}
from Solr 6.4. This is the most flexible highlighter and supports the
most common highlighting parameters. It can handle any query accurately,
even `SpanQueries`{.literal}. The greatest benefit of using this
highlighter is that we can add more configurations to speed up
highlighting on large data documents. We can also add multiple
configurations on a per field basis.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec49}Original highlighter (hl.method=original)  {#original-highlighter-hl.methodoriginal .title}

</div>

</div>
:::

Original highlighter is the default[]{#id288601120 .indexterm}
highlighter, also known as [**standard highlighter**]{.strong} or
[**default highlighter**]{.strong}. The advantage of this highlighter is
its capability of highlighting any[]{#id288605575 .indexterm} query
accurately and[]{#id288617153 .indexterm} efficiently, like
`unified`{.literal} highlighter, but it is very slow compared to
`unified`{.literal} highlighter.

The `original`{.literal} highlighter is much slower at highlighting on
large text fields or complex text analysis because it reanalyzes the
original text at query time. It supports full-term vectors, but compared
to `unified`{.literal} highlighter and `fastVector`{.literal}
highlighter, it is very slow. Also it does not have a
breakiterator-based fragmenter, which can cause problems in some
languages.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec50}FastVector highlighter (hl.method=fastVector) {#fastvector-highlighter-hl.methodfastvector .title}

</div>

</div>
:::

[**FastVector Highlighter**]{.strong} ([**FVH**]{.strong}) is faster
than `original`{.literal} highlighter because[]{#id288617270 .indexterm}
it skips the analysis step when generating fragments. Sometimes, FVH is
not able[]{#id288617278 .indexterm} to highlight some of the fields; in
such cases, it will do a conjunction with the `original`{.literal}
highlighter to match the requirement. For such cases, we need to set
`hl.method=original`{.literal} and
`f.yourTermVecField.hl.method=fastVector`{.literal} for all fields that
should use the FVH.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch06lvl2sec99}Boundary scanners {#boundary-scanners .title}

</div>

</div>
:::

Sometimes, `fastVector`{.literal} highlighter will
truncate[]{#id288618870 .indexterm} highlighted words, so the output
after highlighting may be incomplete or improper. To resolve this issue,
we need to configure a boundary scanner in `solrconfig.xml`{.literal}.
There are two types of boundary scanners available in Solr. We have to
specify a boundary scanner using the parameter
`hl.boundaryScanner`{.literal}.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec51}The breakIterator boundary scanner {#the-breakiterator-boundary-scanner .title}

</div>

</div>
:::

The `breakIterator`{.literal} boundary scanner scans[]{#id288622856
.indexterm} term boundaries by considering the language
(`hl.bs.language`{.literal}) and boundary type (`hl.bs.type`{.literal})
and provides expected, accurate, and complete output without any loss of
characters. It is used most often. To implement the
`breakIterator`{.literal} boundary scanner, we need to add the
following code snippet to the highlighting section in the
`solrconfig.xml`{.literal} file:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<boundaryScanner name="breakIterator" class="solr.highlight.BreakIteratorBoundaryScanner">
 <lst name="defaults">
 <str name="hl.bs.type">WORD</str>
 <str name="hl.bs.language">en</str>
 <str name="hl.bs.country">US</str>
 </lst>
</boundaryScanner>
```
:::

Possible values for the `hl.bs.type`{.literal} parameter are
`WORD`{.literal}, `LINE`{.literal}, `SENTENCE`{.literal}, and
`CHARACTER`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch06lvl3sec52}The simple boundary scanner {#the-simple-boundary-scanner .title}

</div>

</div>
:::

The simple boundary scanner scans[]{#id288626521 .indexterm} term
boundaries by the specified maximum character value
(`hl.bs.maxScan`{.literal}) and common delimiters such as punctuation
marks (`hl.bs.chars`{.literal}). To implement it, we need to add the
following code snippet to the highlighting section in
`solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<boundaryScanner name="simple" class="solr.highlight.SimpleBoundaryScanner" default="true">
 <lst name="defaults">
 <str name="hl.bs.maxScan">10</str >
 <str name="hl.bs.chars">.,!?\t\n</str &gt;
 </lst >
</boundaryScanner>
```
:::

[**Example**]{.strong}: Querying for `ipod`{.literal}, highlighting for
the field `name`{.literal} using `fastVector`{.literal} highlighter

[**URL**]{.strong}:` http://localhost:8983/solr/techproducts/select?hl=true&hl.method=fastVector&q=ipod&hl.fl=name&fl=id,name,cat`{.literal}

[**Response**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":4,
 "params":{
 "q":"ipod",
 "hl":"true",
 "fl":"id,name,cat",
 "hl.method":"fastVector",
 "hl.fl":"name"}},
 "response":{"numFound":3,"start":0,"docs":[
 {
 "id":"IW-02",
 "name":"iPod & iPod Mini USB 2.0 Cable",
 "cat":["electronics",
 "connector"]},
 {
 "id":"F8V7067-APL-KIT",
 "name":"Belkin Mobile Power Cord for iPod w/ Dock",
 "cat":["electronics",
 "connector"]},
 {
 "id":"MA147LL/A",
 "name":"Apple 60 GB iPod with Video Playback Black",
 "cat":["electronics",
 "music"]}]
 },
 "highlighting":{
 "IW-02":{
 "name":["<em>iPod</em> & <em>iPod</em> Mini USB 2.0 Cable"]},
 "F8V7067-APL-KIT":{
 "name":["Belkin Mobile Power Cord for <em>iPod</em> w/ Dock"]},
 "MA147LL/A":{
 "name":["Apple 60 GB <em>iPod</em> with Video Playback Black"]}}}
```
:::

The highlighting section includes the ID of each document and the field
that contains the highlighted portion. Here we have used the
`hl.fl`{.literal} parameter to say that we want query terms highlighted
in the `name`{.literal} field. When there is a match to the query term
in that field, it will be included for each document ID in the list. In
the same way, we can explore highlighting more by configuring different
parameters.



[]{#ch06lvl1sec47}Summary
-------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In this chapter, we learned the concept of relevance and its terms:
Precision and Recall. Then we looked at the velocity search UI. We saw
the common parameters for various query parsers and explored each query
parser (standard, DisMax, and eDisMax) in detail. After that, we looked
at various response writers in detail: JSON, standard XML, CSV, and
velocity response writer.  We also explored Solr term modifiers,
wildcard parameters, fuzzy search, proximity search, and range search.

We looked at all Boolean operators. Then we learned about various
faceting parameters and faceting types such as range, pivot, and
interval faceting. At the end, we saw Solr highlighting mechanisms,
parameters, highlighters, and boundary scanners.

In the next chapter, or rather the second part of this chapter, we will
learn more search functionalities such as spell checking, suggester,
pagination, result grouping and clustering, and spatial search.
