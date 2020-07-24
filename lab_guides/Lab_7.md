

[]{#ch07}Chapter 7. Advanced Queries -- Part II {#chapter-7.-advanced-queries-part-ii .title}
-----------------------------------------------

</div>

</div>
:::

We started understanding the concept of relevance and its terms
precision and recall in the previous chapter. Then we learned about
various query parsers, their parameters, and how we can configure them.
In the same way, we explored various response writers, their parameters,
and how we can configure them. We also looked at velocity search UI.
Then we learned about various faceting parameters and faceting types,
such as range faceting, pivot faceting, and interval faceting. At the
end, we saw the Solr highlighting mechanism, parameters, various
highlighters, and boundary scanners.

In this chapter, we will learn about more search functionalities such as
spellchecking, suggester, pagination, result grouping and clustering,
and spatial search. Let\'s start with the spellchecking feature of Solr.



[]{#ch07lvl1sec48}Spellchecking {#spellchecking .title style="clear: both"}
-------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

We have seen that Solr provides magical support for searching. Solr
provides a strong index building mechanism, unifiable search
configurations, and providing[]{#id288339952 .indexterm} interesting and
expected formatted results by executing various transformation steps on
the query output. Spellchecking is an advantageous feature provided by
Solr for those who make mistakes while typing a query or may enter an
incorrect or inappropriate input. Sometimes, we have this experience
while searching on Google. If we enter `sokcer`{.literal}, then Google
provides a hint: **`Did you mean: soccer?`** Or sometimes, typing
`socer`{.literal} will directly show results for soccer rather than
displaying any hints.

Likewise, there are some scenarios where we need to be careful about the
input word:

::: {.itemizedlist}
-   If a user enters input search terms with incorrect spelling and
    there is no matching document available, we use the Solr spellcheck
    feature, displaying a message that searching for `soccer`{.literal}
    instead of `socer`{.literal} will give the user a hassle-free
    experience of searching without worrying much about the spelling.
-   A user enters less terms for search which is not sufficient to fetch
    more or sufficient matching documents at that time if any suggestion
    terms available which contains more matching documents then we can
    instruct the user by giving a message like
    `Did you mean xxxxx`{.literal}. But if the suggestion terms have the
    same or lesser-matching documents than the query terms, then no
    message should be shown.
-   When no index is available for the entered search terms, no
    suggestions should be given to the user.
:::

To take advantage of the Solr spellchecking feature, we need to tell the
request handle to check spelling during processing. Here is the
configuration of the Solr default request handler to enable
spellchecking while processing a request:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<requestHandler name="/select" class="solr.SearchHandler">
 <lst name="defaults">
 <str name="echoParams">explicit</str>
 <int name="rows">10</int>
 <str name="spellcheck.dictionary">default</str>
 <str name="spellcheck.dictionary">wordbreak</str>
 <str name="spellcheck">on</str>
 <str name="spellcheck.extendedResults">true</str>
 <str name="spellcheck.count">10</str>
 <str name="spellcheck.alternativeTermCount">5</str>
 <str name="spellcheck.maxResultsForSuggest">5</str>
 <str name="spellcheck.collate">true</str>
 <str name="spellcheck.collateExtendedResults">true</str>
 <str name="spellcheck.maxCollationTries">10</str>
 <str name="spellcheck.maxCollations">5</str>
 </lst>
 <arr name="last-components">
 <str>spellcheck</str>
 </arr>
</requestHandler>
```
:::

The preceding configuration is sufficient for enabling spellchecking and
performs spellchecking for all queries processed through this request
handler, for example, searching for `cemera`{.literal} instead of
`camera`{.literal}.

[**URL**]{.strong}:
`http://localhost:8983/solr/techproducts/select?q=cemera`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":7,
 "params":{
 "q":"cemera"}},
 "response":{"numFound":0,"start":0,"docs":[]
 },
 "spellcheck":{
 "suggestions":[
 "cemera",{
 "numFound":1,
 "startOffset":0,
 "endOffset":6,
 "origFreq":0,
 "suggestion":[{
 "word":"camera",
 "freq":1}]}],
 "correctlySpelled":false,
 "collations":[
 "collation",{
 "collationQuery":"camera",
 "hits":1,
 "misspellingsAndCorrections":[
 "cemera","camera"]}]}
}
```
:::

From the preceding response, we can see that Solr returns the spellcheck
container, along with suggested words and the correct spelling in
response. Here we have searched for an incorrect word
(`cemera`{.literal}) but in the response, the correct spelling has been
returned as a spellcheck-suggested word.

However, you don\'t need to provide any spellchecking-related parameters
to the query string. Still, if you want to disable spellchecking for any
specific query, you can use `spellcheck=false`{.literal} and disable
spellchecking for that particular query. For example:

[**URL**]{.strong}:
`http://localhost:8983/solr/techproducts/select?q=cemera&spellcheck=false`{.literal}:

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
 "q":"cemera",
 "spellcheck":"false"}},
 "response":{"numFound":0,"start":0,"docs":[]
 }}
```
:::

Spellchecking is not executed for this query though we have searched for
an incorrect spelling. 

It is always advisable to perform spellchecking last because we still
want the default search components, such as query, facet, and debug, to
execute during query processing. This can be done easily by setting
`spellcheck.collate=true`{.literal}. This is a collation parameter that
tells Solr to run spellchecking last because generating the collation
query requires an already executed query.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec100}Spellcheck parameters {#spellcheck-parameters .title}

</div>

</div>
:::

As we discussed earlier[]{#id288341302 .indexterm} in this chapter, we
do not need to pass any parameter to enable or configure a parameter
with a query string. Configuring them with any request handler in the
`solrconfig.xml`{.literal} file will enable them. Let\'s understand all
our parameters:

::: {.informaltable}
  ------------------------------------------------------ ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
  [**Parameter**]{.strong}                               [**Behavior**]{.strong}                                                                                                                                                                                                                                                                                                                  [**Default value**]{.strong}
  `spellcheck`{.literal}                                 Enables or disables spellchecking.                                                                                                                                                                                                                                                                                                       `false`{.literal}
  `spellcheck.q`{.literal} or `spellcheck.q`{.literal}   Specifies the query for spellchecking. It will be used if `spellcheck.q`{.literal} is specified, or else the original input query will be used.                                                                                                                                                                                          
  `spellcheck.build`{.literal}                           Boolean parameter that tells Solr to generate a dictionary if it does not exist. Dictionary building will take additional time in query processing, so it is advisable not to pass this parameter with every request.                                                                                                                    `false`{.literal}
  `spellcheck.reload`{.literal}                          A Boolean parameter that tells Solr to reload the spellchecker implementation.                                                                                                                                                                                                                                                           
  `spellcheck.count`{.literal}                           Specifies the maximum number of suggestions a spellchecker should return in the response for a specific query term. The default value is `1`{.literal} if the parameter is not set and is `5`{.literal} if the parameter is set but no value is assigned.                                                                                `1`{.literal}
  `spellcheck.onlyMorePopular`{.literal}                 Specifying this as true will tell Solr to return only those suggestions that have more hits than the original query.                                                                                                                                                                                                                     
  `spellcheck.maxResultsForSuggest`{.literal}            Specifies a threshold count based on the number of documents matched from the user\'s original query. For example, if we have set this to `5`{.literal} and a user\'s original query returns [*\>= 5*]{.emphasis} matching documents, Solr will disable the suggestion automatically.                                                    
  `spellcheck.alternativeTermCount`{.literal}            Specifies the number of suggestions to be returned from the index and/or dictionary for each query term. It will also enable context-sensitive spelling suggestions.                                                                                                                                                                     
  `spellcheck.extendedResults`{.literal}                 Boolean variable that tells Solr to return additional details about the spellcheck results. For example, setting this to true will return the frequency of the original terms and of suggestions from the index.                                                                                                                         
  `spellcheck.collate`{.literal}                         If true, this parameter tells Solr to create a new query (collation query) from the suggested spelling correction. The collation query can be executed by clicking on the link **`Did you mean ... ?`** Returning results is guaranteed, so Solr must execute the collation query in the background before returning results to users.   
  `spellcheck.maxCollations`{.literal}                   The maximum number of collation queries Solr will generate. The default is `1`{.literal}. It will work only if `spellcheck.collate`{.literal} is true.                                                                                                                                                                                   `1`{.literal}
  `spellcheck.maxCollationTries`{.literal}               Specifies the number of times Solr should try for collation before giving up. Specifying a lower value will improve performance as Solr will not provide suggestions in all cases.                                                                                                                                                       
  `spellcheck.maxCollationEvaluations`{.literal}         Specifies the maximum number of word correction combinations for evaluating a correct collation that will run against an index.                                                                                                                                                                                                          `10000`{.literal}
  `spellcheck.collateExtendedResults`{.literal}          A boolean value that tells Solr whether to return a response in expanded format or not. Setting this to true will return the response in expanded format. It will work only if `spellcheck.collate`{.literal} is true.                                                                                                                   `false`{.literal}
  `spellcheck.collateMaxCollectDocs`{.literal}           Specifies the maximum number of documents to be collected for testing of collations against the index. The default value for this parameter is `0`{.literal}, which means that all documents should be collected.                                                                                                                        `0`{.literal}
  `spellcheck.collateParam.*Prefix`{.literal}            Used to specify an additional parameter that you want to be considered by the Spellchecker when internally validating collation queries.                                                                                                                                                                                                 
  `spellcheck.dictionary`{.literal}                      Specifies the dictionary name to be used by Solr for spellchecking.                                                                                                                                                                                                                                                                      `default`{.literal}
  `spellcheck.accuracy`{.literal}                        Specifies the accuracy level to decide whether the results provided after spellchecking are sufficient or not. Possible values are float values between `0`{.literal} and `1`{.literal}.                                                                                                                                                 `Float.MIN_VALUE`{.literal}
  `spellcheck.<DICT_NAME>.key`{.literal}                 Specifies a key/value pair for dictionary implementation of spellcheck.                                                                                                                                                                                                                                                                  
  ------------------------------------------------------ ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec101}Implementation approaches {#implementation-approaches .title}

</div>

</div>
:::

Solr provides many approaches for implementing[]{#id288341664
.indexterm} spellchecking. Let\'s get a brief overview of these
approaches.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec53}IndexBasedSpellChecker {#indexbasedspellchecker .title}

</div>

</div>
:::

The `IndexBasedSpellChecker`{.literal} builds a parallel[]{#id288341682
.indexterm} index based on the Solr index and performs spellchecking
using this parallel index. For this, a field needs to be defined as a
basis for the index terms. The easiest way is to copy terms from some
fields (subject, description, and so on) to another field created for
spellchecking. Here is a simple example of the
`IndexBasedSpellChecker`{.literal} approach configured in
`solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<searchComponent name="spellcheck" class="solr.SpellCheckComponent">
 <lst name="spellchecker">
 <str name="classname">solr.IndexBasedSpellChecker</str>
 <str name="spellcheckIndexDir">./spellchecker</str>
 <str name="field">content</str>
 <str name="buildOnCommit">true</str>
 <!-- optional elements with defaults
 <str name="distanceMeasure">org.apache.lucene.search.spell.LevensteinDistance</str>
 <str name="accuracy">0.5</str>
 -->
 </lst>
</searchComponent>
```
:::

The attribute `classname`{.literal} defines the approach to be used for
spellchecking. If we do not define this
attribute, `solr.IndexBasedSpellChecker`{.literal} is the default one
used for spellchecking.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec54}DirectSolrSpellChecker {#directsolrspellchecker .title}

</div>

</div>
:::

The `DirectSolrSpellChecker`{.literal} performs
spellchecking[]{#id288341754 .indexterm} directly using the terms from
the Solr index without building a parallel index like
`IndexBasedSpellChecker`{.literal}. As
`DirectSolrSpellChecker`{.literal} does not build any indexes,
suggestion terms are always up to date as with the Solr main index. Here
is an example configured in `solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<searchComponent name="spellcheck" class="solr.SpellCheckComponent">
 <lst name="spellchecker">
 <str name="name">default</str>
 <str name="field">name</str>
 <str name="classname">solr.DirectSolrSpellChecker</str>
 <str name="distanceMeasure">internal</str>
 <float name="accuracy">0.5</float>
 <int name="maxEdits">2</int>
 <int name="minPrefix">1</int>
 <int name="maxInspections">5</int>
 <int name="minQueryLength">4</int>
 <float name="maxQueryFrequency">0.01</float>
 <float name="thresholdTokenFrequency">.01</float>
 </lst>
</searchComponent>
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec55}FileBasedSpellChecker {#filebasedspellchecker .title}

</div>

</div>
:::

The `FileBasedSpellChecker`{.literal} evaluates spellings[]{#id288341786
.indexterm} from an external file dictionary. This is useful when Solr
works as a spellchecking server. This is also useful where spelling
suggestions are not on the base of actual terms. To implement
`FileBasedSpellChecker`{.literal}, modify `solrconfig.xml`{.literal} as
follows:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<searchComponent name="spellcheck" class="solr.SpellCheckComponent">
 <lst name="spellchecker">
 <str name="classname">solr.FileBasedSpellChecker</str>
 <str name="name">file</str>
 <str name="sourceLocation">spellings.txt</str>
 <str name="characterEncoding">UTF-8</str>
 <str name="spellcheckIndexDir">./spellcheckerFile</str>
 <!-- optional elements with defaults
 <str name="distanceMeasure">org.apache.lucene.search.spell.LevensteinDistance</str>
 <str name="accuracy">0.5</str>
 -->
 </lst>
</searchComponent>
```
:::

The `sourceLocation`{.literal} parameter holds the path of the file that
contains the spelling dictionary. The
`characterEncoding`{.literal}parameter specifies the character-encoding
algorithm.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec56}WordBreakSolrSpellChecker {#wordbreaksolrspellchecker .title}

</div>

</div>
:::

The `WordBreakSolrSpellChecker`{.literal} performs
spellchecking[]{#id288341826 .indexterm} by breaking and/or combining
query terms. This is useful for terms where users put whitespaces at
incorrect places. For example, to search for
`group dance choreographer`{.literal}, a user may enter
`groupdance choreographor`{.literal} as a search term. It also provides
shard support. Here is the configuration in `solrconfig.xml`{.literal}
for this approach:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<searchComponent name="spellcheck" class="solr.SpellCheckComponent">
 <lst name="spellchecker">
 <str name="name">wordbreak</str>
 <str name="classname">solr.WordBreakSolrSpellChecker</str>
 <str name="field">lowerfilt</str>
 <str name="combineWords">true</str>
 <str name="breakWords">true</str>
 <int name="maxChanges">10</int>
 </lst>
</searchComponent>
```
:::
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec102}Distributed spellcheck {#distributed-spellcheck .title}

</div>

</div>
:::

Solr supports spellchecking[]{#id288341854 .indexterm} on distributed
indexes also. The following[]{#id288341863 .indexterm} are the two
parameters that are required for the request handler (excluding request
handler `/select`{.literal}) to implement spellchecking on distributed
indexes:

::: {.informaltable}
  -------------------------- ----------------------------------------------------------------------------------------------------------------------------------
  [**Parameter**]{.strong}   [**Behavior**]{.strong}
  `shards`{.literal}         Specifies the shards in distributed indexing configuration.
  `shards.qt`{.literal}      Specifies a request handler for requesting to shards. This parameter is not required by the `/select`{.literal} request handler.
  -------------------------- ----------------------------------------------------------------------------------------------------------------------------------
:::

For example, to search for the word `cemera`{.literal} on distributed
indexing (`shard1`{.literal} and `shard2`{.literal}), the following is
the URL:

`http://localhost:8983/solr/techproducts/spell?spellcheck=true&spellcheck.build=true&spellcheck.q=cemera&shards.qt=/spell&shards=solr-shard1:8983/solr/techproducts,solr-shard2:8983/solr/techproducts`{.literal}



[]{#ch07lvl1sec49}Suggester {#suggester .title style="clear: both"}
---------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In the preceding section, we have seen[]{#id288339954 .indexterm} how
Solr handles incorrectly spelled terms and then returns the correct
output for them. Let\'s move one step ahead and provide a feature
wherein the user always enters correct spellings but we want to be a
step ahead and provide list of suggestions using whatever the user has
already typed. This can be achieved by a Solr tool called suggester. The
suggester suggests terms when the user types words. During the
implementation of the suggester, we need to consider these two things:

::: {.itemizedlist}
-   It must be very fast as we need to display suggestions on the
    user\'s characters type
-   The suggestions should be ranked and ordered by term frequency
:::

To configure any suggester, `SuggestComponent`{.literal} needs to be
configured in `solrconfig.xml`{.literal}. Here is a simple configuration
of a suggester:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<searchComponent name="suggest" class="solr.SuggestComponent">
 <lst name="suggester">
 <str name="name">mySuggester</str>
 <str name="lookupImpl">FuzzyLookupFactory</str>
 <str name="dictionaryImpl">DocumentDictionaryFactory</str>
 <str name="field">cat</str>
 <str name="weightField">price</str>
 <str name="suggestAnalyzerFieldType">string</str>
 <str name="buildOnStartup">false</str>
 </lst>
 </searchComponent>
```
:::

Now this `suggest`{.literal} search component must be associated with
any request handler in `solrconfig.xml`{.literal}. Previously we used
the `/select`{.literal} request handler, where we configured many
components such as spellchecking. Also, the `/select`{.literal} request
handler contains some built-in components: query, faceting,
highlighting, and so on. Merging the suggester with other components is
not the best approach at all times; sometimes we expect only suggestions
and not spellchecking. So, defining a separate request handler for
suggestions is the recommended approach. For that, we are defining a
separate request handler here and adding the previously defined
`suggest`{.literal} search component to this request handler in
`solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<requestHandler name="/suggest" class="solr.SearchHandler" startup="lazy">
 <lst name="defaults">
 <str name="suggest">true</str>
 <str name="suggest.count">10</str>
 </lst>
 <arr name="components">
 <str>suggest</str>
 </arr>
</requestHandler>
```
:::

Now, all requests coming at the `/suggest`{.literal} request handler
will be treated for suggestions only. This will work as the other
request handler and allow us to configure default parameters for
suggestion requests.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec103}Suggester parameters {#suggester-parameters .title}

</div>

</div>
:::

As we have seen, first we need to define[]{#id288340089 .indexterm} a
search component and then we need to inject that component with any
request handler. Both the sections contain some parameters that fulfill
the suggestion requirements. Here is a list of parameters. 

Suggester search component parameters are as follows:

::: {.informaltable}
  ----------------------------------------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------------------
  [**Parameter**]{.strong}                                    [**Behavior**]{.strong}                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      [**Default value**]{.strong}
  `searchComponent`{.literal}                                 The name of the search component.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            
  `name`{.literal}                                            Suggester name, which will be referred with the request handler.                                                                                                                                                                                                                                                                                                                                                                                                                                                             
  `lookupImpl`{.literal}                                      Specifies the lookupImpl implementation algorithms used to look up terms in the suggest index. Available implementations are `AnalyzingLookupFactory`{.literal}, `FuzzyLookupFactory`{.literal}, `AnalyzingInfixLookupFactory`{.literal}, `BlendedInfixLookupFactory`{.literal}, `FreeTextLookupFactory`{.literal}, `FSTLookupFactory, TSTLookupFactory`{.literal}, `WFSTLookupFactory`{.literal}, and `JaspellLookupFactory`{.literal}.                                                                                     `JaspellLookupFactory`{.literal}
  `dictionaryImpl`{.literal}                                  Specifies a dictionary implementation for suggestions. Available implementations are `DocumentDictionaryFactory`{.literal}, `DocumentExpressionDictionaryFactory`{.literal}, `HighFrequencyDictionaryFactory`{.literal}, and `FileDictionaryFactory`{.literal}.                                                                                                                                                                                                                                                              `HighFrequency DictionaryFactory`{.literal}
  `sourceLocation`{.literal}                                  Specifies the dictionary file path when a suggestion is implemented using `FileDictionaryFactory`{.literal}. The main index will be used as the source of terms and weights if this parameter value is empty.                                                                                                                                                                                                                                                                                                                
  `field`{.literal}                                           Specifies a field from which the index is to be used as the basis of suggestion terms. The specified field must be stored in the index.                                                                                                                                                                                                                                                                                                                                                                                      
  `storeDir`{.literal}                                        Specifies a path to store the dictionary file.                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
  `buildOnCommit`{.literal} and `buildOnOptimize`{.literal}   If this is true, the suggestion dictionary will be rebuilt after a soft commit. If false, the suggestion dictionary will be built only when we provide the suggest.build=true parameter in the URL. Use `buildOnCommit`{.literal} to rebuild the suggestion dictionary with every soft commit or `buildOnOptimize`{.literal} to build it only when the index is optimized. This parameter is not recommended if the volume of the indexes is very high; in such a scenario, `suggest.build=true`{.literal} is recommended.   `false`{.literal}
  `buildOnStartup`{.literal}                                  Specifying this to true will build the suggestion directory at the time of Solr start or core reloading. If this parameter is not specified, the suggester will check whether the suggestion directory is present on disk, and build one if it is not found. Enabling (`true`{.literal}) is not recommended as sometimes it may take too long in build; instead, use `suggest.build=true`{.literal} for building manually.                                                                                                   `false`{.literal}
  ----------------------------------------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------------------
:::

 

Suggester request handler parameters are as follows:

::: {.informaltable}
  -------------------------------- ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
  [**Parameter**]{.strong}         [**Behavior**]{.strong}                                                                                                                                                                                                                                                                               [**Default value**]{.strong}
  `suggest`{.literal}              A boolean parameter to enable/disable suggestions.                                                                                                                                                                                                                                                    `true`{.literal}
  `suggest.dictionary`{.literal}   A mandatory parameter. It specifies the dictionary component configured inside the search component.                                                                                                                                                                                                  
  `suggest.q`{.literal}            Specifies a query to use for suggestions.                                                                                                                                                                                                                                                             
  `suggest.count`{.literal}        Specifies the number[]{#id288618870 .indexterm} of suggestions to be returned.                                                                                                                                                                                                                        `10`{.literal}
  `suggest.cfq`{.literal}          Specifies the [**context filter query**]{.strong} ([**CFQ**]{.strong}) used to filter suggestions based on the context field. CFQ is only supported by `AnalyzingInfixLookupFactory`{.literal} and `BlendedInfixLookupFactory`{.literal} and only when backed by a `Document*Dictionary`{.literal}.   
  `suggest.build`{.literal}        If true, it will build the suggester index.                                                                                                                                                                                                                                                           `false`{.literal}
  `suggest.reload`{.literal}       If true, it will reload the suggester index.                                                                                                                                                                                                                                                          `false`{.literal}
  `suggest.buildAll`{.literal}     If true, it will build all suggester indexes                                                                                                                                                                                                                                                          `false`{.literal}
  `suggest.reloadAll`{.literal}    If true, it will reload all suggester indexes.                                                                                                                                                                                                                                                        `false`{.literal}
  -------------------------------- ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
:::

 

All of these parameters are usually configured in the request handler.
However, we can override them at query time by passing parameters in the
URL.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec104}Running suggestions {#running-suggestions .title}

</div>

</div>
:::

We have configured the suggester[]{#id288626547 .indexterm} and request
handler for this suggester in `solrconfig.xml`{.literal}. Now let\'s run
the configured suggester and examine the response.

[**Example**]{.strong}: Let\'s search for `elec`{.literal} and see how
many suggestions are returned in response. 

[**URL**]{.strong}: `http://localhost:8983/solr/techproducts/suggest?suggest=true&suggest.build=true&suggest.dictionary=mySuggester&suggest.q=elec`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":14},
 "command":"build",
 "suggest":{"mySuggester":{
 "elec":{
 "numFound":3,
 "suggestions":[{
 "term":"electronics and computer1",
 "weight":2199,
 "payload":""},
 {
 "term":"electronics",
 "weight":649,
 "payload":""},
 {
 "term":"electronics and stuff2",
 "weight":279,
 "payload":""}]}}}
}
```
:::

The query `elec`{.literal} has returned three suggestions under the
`mySuggester`{.literal} section. Here, we have provided a
single dictionary by the
parameter `suggest.dictionary=mySuggester`{.literal}. In the same way,
we can configure multiple dictionaries in `solrconfig.xml`{.literal} and
use them in a URL like this:

`http://localhost:8983/solr/techproducts/suggest?suggest=true&suggest.build=true&suggest.dictionary=mySuggester&suggest.dictionary=yourSuggester&suggest.q=elec`{.literal}



[]{#ch07lvl1sec50}Pagination {#pagination .title style="clear: both"}
----------------------------

</div>

</div>

------------------------------------------------------------------------
:::

A query may fetch a number of results[]{#id288339952 .indexterm} for a
search. Returning all results at a time and displaying all of them on a
single page is not an ideal approach for any search application. Rather,
returning the top [*N*]{.emphasis} number of matching results (sorted
based on some fields) first is the ideal way for an application. Solr
supports a pagination feature whereby we can return a certain number of
results rather than all results and display them on the first page. If
we can\'t find the results we are looking for on the first page, we can
call the next page of results by running the subsequent request with
pagination parameters. Pagination is very helpful in terms of
performance because instead of returning all matching results at a time,
it will return only a specific number of results; so the result is very
quick. Using pagination, we also can determine how many queries are
required to fulfill the expectations behind the search; so we can manage
relevance accordingly.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec105}How to implement pagination {#how-to-implement-pagination .title}

</div>

</div>
:::

To implement pagination, we need to configure[]{#id288341721 .indexterm}
two parameters in the query request.

::: {.itemizedlist}
-   `start`{.literal}: Indicates from where the results should be
    returned from the complete result set
-   `rows`{.literal}: Indicates how many results must be returned from
    the complete result set
:::

We have to specify some value if we have used the start parameter in a
request. Keep in mind that the value of the start parameter should be
less than the total number of matching results. If we set
`start`{.literal} higher than the total number of matching results
found, the query will not return anything.

[**Example**]{.strong}: Search for a `query `{.literal}`q=*:*`{.literal}
and get the first five results sorted by ID in ascending order.

[**URL**]{.strong}: `http://localhost:8983/solr/techproducts/select?q=*:*&fl=id,name&start=0&rows=5&sort=id asc`{.literal}:

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
 "q":"*:*",
 "fl":"id,name",
 "start":"0",
 "sort":"id asc",
 "rows":"5"}},
 "response":{"numFound":32,"start":0,"docs":[
 {
 "id":"0579B002",
 "name":"Canon PIXMA MP500 All-In-One Photo Printer"},
 {
 "id":"100-435805",
 "name":"ATI Radeon X1900 XTX 512 MB PCIE Video Card"},
 {
 "id":"3007WFP",
 "name":"Dell Widescreen UltraSharp 3007WFP"},
 {
 "id":"6H500F0",
 "name":"Maxtor DiamondMax 11 - hard drive - 500 GB - SATA-300"},
 {
 "id":"9885A004",
 "name":"Canon PowerShot SD500"}]
 }
}
```
:::

Only five results are returned in the response. Now, if we want the next
five results, we set `start=5`{.literal} and `rows=5`{.literal}. Please
note that the response index starts with zero.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec106}Cursor pagination {#cursor-pagination .title}

</div>

</div>
:::

Pagination implementation using the start and[]{#id288617273 .indexterm}
rows parameters is very easy and straightforward. But when we have very
large data volumes, these parameters are not sufficient to implement
pagination. For example, consider this:

::: {.itemizedlist}
-   During query processing, Solr first loads all the matching documents
    in memory; then it creates an offset by the start and rows
    parameters and returns that offset for that query. If the data
    volume is very large, Solr first loads all matching results in the
    memory and then applies pagination. So this will create a
    performance problem.
-   In large volumes of data, a request for
    `start=0&rows=1000000`{.literal} may create trouble for Solr in
    maintaining and sorting a collection of 1 million documents in
    memory.
-   A request for `start=999000&rows=1000`{.literal} also creates the
    same problem. To match the document at the 999001^th^ place, Solr
    has to traverse through the first 999,000 documents.
-   A similar problem exists with SolrCloud, where indexes are
    distributed. If we have 10 shards, then Solr retrieves 10 million
    documents (1 million from each shard) and then sorts to find 1,000
    documents matching the query parameters.
:::

As a solution to these problems, Solr introduce a feature of
pagination---cursor. Cursor does not manage caching on the server but
marks the point from where the last document was returned. Now that mark
point, called `cursorMark`{.literal}, is supplied inside the parameters
of subsequent requests to tell Solr where to continue from.

To implement pagination through cursor, we need to specify the parameter
`cursorMark`{.literal} with the value of `\*`{.literal}. Once we
implement cursor pagination in Solr, Solr returns the top
[*N*]{.emphasis} number of sorted results (where [*N*]{.emphasis} can be
specified in the rows parameter) along with an encoded string named
`nextCursorMark`{.literal} in the response. In subsequent requests, the
`nextCursorMark`{.literal} value will be passed as the
`cursorMark`{.literal} parameter. The process will be repeated until the
expected result set is retrieved or the value of
`nextCursorMark`{.literal} matches `cursorMark`{.literal} (which means
that there are no more results).

[**Examples**]{.strong}: Fetching all documents.

[**Using pseudo-code**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
$params = [ q => $some_query, sort => 'id asc', rows => $r, cursorMark => '*' ]
$done = false
while (not $done) {
  $results = fetch_solr($params)
  // do something with $results
  if ($params[cursorMark] == $results[nextCursorMark]) {
    $done = true
  }
  $params[cursorMark] = $results[nextCursorMark]
}
```
:::

[**Using SolrJ**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
SolrQuery q = (new SolrQuery(some_query)).setRows(r).setSort(SortClause.asc("id"));
String cursorMark = CursorMarkParams.CURSOR_MARK_START;
boolean done = false;
while (! done) {
  q.set(CursorMarkParams.CURSOR_MARK_PARAM, cursorMark);
  QueryResponse rsp = solrServer.query(q);
  String nextCursorMark = rsp.getNextCursorMark();
  doCustomProcessingOfResults(rsp);
  if (cursorMark.equals(nextCursorMark)) {
    done = true;
  }
  cursorMark = nextCursorMark;
}
```
:::

[**Using curl**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
$ curl '...&rows=10&sort=id+asc&cursorMark=*'
{
  "response":{"numFound":32,"start":0,"docs":[
    // ... 10 docs here ...
  ]},
  "nextCursorMark":"AoEjR0JQ"}
$ curl '...&rows=10&sort=id+asc&cursorMark=AoEjR0JQ'
{
  "response":{"numFound":32,"start":0,"docs":[
    // ... 10 more docs here ...
  ]},
  "nextCursorMark":"AoEpVkRCREIxQTE2"}
$ curl '...&rows=10&sort=id+asc&cursorMark=AoEpVkRCREIxQTE2'
{
  "response":{"numFound":32,"start":0,"docs":[
    // ... 10 more docs here ...
  ]},
  "nextCursorMark":"AoEmbWF4dG9y"}
$ curl '...&rows=10&sort=id+asc&cursorMark=AoEmbWF4dG9y'
{
  "response":{"numFound":32,"start":0,"docs":[
    // ... 2 docs here because we've reached the end.
  ]},
  "nextCursorMark":"AoEpdmlld3Nvbmlj"}
$ curl '...&rows=10&sort=id+asc&cursorMark=AoEpdmlld3Nvbmlj'
{
  "response":{"numFound":32,"start":0,"docs":[
    // no more docs here, and note that the nextCursorMark
    // matches the cursorMark param we used
  ]},
  "nextCursorMark":"AoEpdmlld3Nvbmlj"}
```
:::

[**Examples**]{.strong}: Fetching [*N*]{.emphasis} Number of documents.

[**SolrJ**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
while (! done) {
 q.set(CursorMarkParams.CURSOR_MARK_PARAM, cursorMark);
 QueryResponse rsp = solrServer.query(q);
 String nextCursorMark = rsp.getNextCursorMark();
 boolean hadEnough = doCustomProcessingOfResults(rsp);
 if (hadEnough || cursorMark.equals(nextCursorMark)) {
 done = true;
 }
 cursorMark = nextCursorMark;
}
```
:::

When implementing pagination, we need to take care of a few things:

::: {.itemizedlist}
-   If we have used a start parameter in the request, we have to specify
    some value.
-   The field on which we are applying sorting must be unique
    (`uniqueKey`{.literal} field).
-   The `cursorMark`{.literal} values are calculated based on the sort
    values of each document, and it may be possible that multiple
    documents have the same sort values; that will create identical
    `cursorMarks`{.literal} values. Now, Solr will be confused in
    subsequent requests as to which `cursorMark`{.literal} value should
    be considered. To overcome this, Solr provides an additional field,
    `uniqueKey`{.literal}, used as a clause with a sorting parameter.
    This `uniqueKey`{.literal} guarantees that the documents are
    returned in deterministic order, and that way each
    `cursorMark`{.literal} will always point to a unique value.
-   When documents are sorted based on a `Date`{.literal} function,
    `NOW`{.literal} will create confusion for cursor because
    `NOW`{.literal} will create a new sort value for each document in
    each subsequent request. This will result in never-ending cursors
    and return the same document every time. To overcome[]{#id288628866
    .indexterm} this situation, select a fixed value for the
    `NOW`{.literal} parameter in all requests.



[]{#ch07lvl1sec51}Result grouping {#result-grouping .title style="clear: both"}
---------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Result grouping is a useful feature in Solr; it returns[]{#id288339954
.indexterm} an optimal mix of search results for a query. Result
grouping can be performed based on field values, functions, or queries.

Sometimes, we have multiple similar documents for a single search term,
for example, multiple locations for the same hospital, recipes for
specific food, plans for term insurance, and so on. In the normal way,
if we are searching for one such term, it will return all similar
documents and we will have to display all of them on the same page.
Through result grouping, we can display only a single document (or the
top few or some limited number) for each unique value, and provide a
message link with meaningful text and the number of total results found
for that query. Clicking on that link will expand the full search result
list. This is similar to the expand and collapse features of a search
application. Result grouping is just as capable as expand and collapse;
additionally it removes duplicate documents from the list. But from a
performance point of view, expand and collapse is a better choice than
result grouping.

We looked at faceting in the previous chapter. Result grouping also
looks similar to faceting, but through faceting, Solr returns a separate
facet section along with the counts for each value. Through result
grouping, it actually returns the unique values and their counts (like
faceting) plus a number of documents that contain each of the specified
values. Also, groups returned in the results section are sorted based on
the sorting parameter provided inside the query.

Combining grouping with faceting is also possible. Currently, field and
range faceting are included in grouped faceting, but date and pivot
faceting are not included. The facet counts are calculated based on the
first `group.field`{.literal} parameter and other
`group.field`{.literal} parameters are ignored.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec107}Result grouping parameters {#result-grouping-parameters .title}

</div>

</div>
:::

::: {.informaltable}
  --------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
  [**Parameter**]{.strong}          [**Behavior**]{.strong}                                                                                                                                                               [**Default value**]{.strong}
  `group`{.literal}                 If true, query results[]{#id288340056 .indexterm} will be grouped.                                                                                                                    `false`{.literal}
  `group.field`{.literal}           Specifies the field by which the result will be grouped.                                                                                                                              
  `group.func`{.literal}            Specifies a function name by which the result will be grouped.                                                                                                                        
  `group.query`{.literal}           Specifies a result grouping query that will return a single group of documents.                                                                                                       
  `rows`{.literal}                  Specifies the number of groups to return.                                                                                                                                             `10`{.literal}
  `start`{.literal}                 Specifies starting point from the list of groups.                                                                                                                                     
  `group.limit`{.literal}           Specifies the number of results to be returned for every group.                                                                                                                       `1`{.literal}
  `group.offset`{.literal}          Specifies starting point from the list of documents of every group.                                                                                                                   
  `sort`{.literal}                  Specifies the field based on groups will be sorted.                                                                                                                                   score desc
  `group.sort`{.literal}            Specifies the field based on documents will be sorted within each group. The default behaviour is that if group.sort is not specified, the sort parameter value will be used.         
  `group.format`{.literal}          Specifies the response format after grouping. Possible values for this parameter are simple and grouped.                                                                              Advanced grouping format
  `group.main`{.literal}            A boolean variable that tells Solr to use first the field grouping command as the main result in the response using `group.format=simple`{.literal}. A true value will do the same.   `false`{.literal}
  `group.ngroups`{.literal}         A boolean variable that tells Solr to include the number of groups that have matched to the query in result. A true value will do the same.                                           `false`{.literal}
  `group.truncate`{.literal}        A boolean variable that tells Solr to calculate facet counts based on the most relevant document of each group matching the query. A true value will do the same.                     `false`{.literal}
  `group.facet`{.literal}           To enable/disable grouped faceting.                                                                                                                                                   `false`{.literal}
  `group.cache.percent`{.literal}   Enables caching for result grouping by configuring a value greater than zero. The maximum value is 100.                                                                               `0`{.literal}
  --------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec108}Running result grouping {#running-result-grouping .title}

</div>

</div>
:::

We have explored the result grouping concept[]{#id288601076 .indexterm}
and configurations. Now let\'s execute some examples.

[**Example**]{.strong}: Grouping by field `manu_exact"`{.literal}, which
specifies the manufacturers of items in the `techproducts`{.literal}
dataset.

[**URL**]{.strong}: `http://localhost:8983/solr/techproducts/select?fl=id,name&q=solr+memory&group=true&group.field=manu_exact`{.literal}

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
 "responseHeader":{
 "status":0,
 "QTime":5,
 "params":{
 "q":"solr memory",
 "fl":"id,name",
 "group.field":"manu_exact",
 "group":"true"}},
 "grouped":{
 "manu_exact":{
 "matches":6,
 "groups":[{
 "groupValue":"Apache Software Foundation",
 "doclist":{"numFound":1,"start":0,"docs":[
 {
 "id":"SOLR1000",
 "name":"Solr, the Enterprise Search Server"}]
 }},
 {
 "groupValue":"Corsair Microsystems Inc.",
 "doclist":{"numFound":2,"start":0,"docs":[
 {
 "id":"VS1GB400C3",
 "name":"CORSAIR ValueSelect 1GB 184-Pin DDR SDRAM Unbuffered DDR 400 (PC 3200) System Memory - Retail"}]
 }},
 {
 "groupValue":"A-DATA Technology Inc.",
 "doclist":{"numFound":1,"start":0,"docs":[
 {
 "id":"VDBDB1A16",
 "name":"A-DATA V-Series 1GB 184-Pin DDR SDRAM Unbuffered DDR 400 (PC 3200) System Memory - OEM"}]
 }},
 {
 "groupValue":"Canon Inc.",
 "doclist":{"numFound":1,"start":0,"docs":[
 {
 "id":"0579B002",
 "name":"Canon PIXMA MP500 All-In-One Photo Printer"}]
 }},
 {
 "groupValue":"ASUS Computer Inc.",
 "doclist":{"numFound":1,"start":0,"docs":[
 {
 "id":"EN7800GTX/2DHTV/256M",
 "name":"ASUS Extreme N7800GTX/2DHTV (256 MB)"}]
 }}]}},
 "spellcheck":{
 "suggestions":[],
 "correctlySpelled":true,
 "collations":[]}
}
```
:::

In the same way, grouping can be achieved by specifying a query or
queries.

[**Example**]{.strong}: Retrieve the top three results for the field
`memory`{.literal} for two price ranges of 0.00 to 99.99 and over 100,
using `group.query`{.literal}. 

[**URL**]{.strong}: `http://localhost:8983/solr/techproducts/select?indent=true&fl=name,price&q=memory&group=true&group.query=price:[0+TO+99.99]&group.query=price:[100+TO+*]&group.limit=3`{.literal}

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":2,
    "params":{
      "q":"memory",
      "indent":"true",
      "fl":"name,price",
      "group.limit":"3",
      "group.query":["price:[0 TO 99.99]",
        "price:[100 TO *]"],
      "group":"true"}},
  "grouped":{
    "price:[0 TO 99.99]":{
      "matches":5,
      "doclist":{"numFound":1,"start":0,"docs":[
          {
            "name":"CORSAIR ValueSelect 1GB 184-Pin DDR SDRAM Unbuffered DDR 400 (PC 3200) System Memory - Retail",
            "price":74.99}]
      }},
    "price:[100 TO *]":{
      "matches":5,
      "doclist":{"numFound":3,"start":0,"docs":[
          {
            "name":"CORSAIR XMS 2GB (2 x 1GB) 184-Pin DDR SDRAM Unbuffered DDR 400 (PC 3200) Dual Channel Kit System Memory - Retail",
            "price":185.0},
          {
            "name":"Canon PIXMA MP500 All-In-One Photo Printer",
            "price":179.99},
          {
            "name":"ASUS Extreme N7800GTX/2DHTV (256 MB)",
            "price":479.95}]
      }}},
  "spellcheck":{
    "suggestions":[],
    "correctlySpelled":false,
    "collations":[]}}
```



[]{#ch07lvl1sec52}Result clustering {#result-clustering .title style="clear: both"}
-----------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

So far, we have seen Solr searching by the keyword[]{#id288339952
.indexterm} used in search query. Result clustering is the advanced
search component of Solr; it first identifies the similarities between
documents, and using these similarities, it finds related documents. It
is also not necessary for the identified similarities to be present in
the query or document.

The clustering component first discovers the results of a search query
and identifies similar terms or phrases found within the search
results. A clustering algorithm discovers relationships across all the
documents from the search result and forms in a meaningful cluster
label. Solr comes with several algorithms for clustering implementation.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec109}Result clustering parameters {#result-clustering-parameters .title}

</div>

</div>
:::

::: {.informaltable}
  ----------------------------------- -------------------------------------------------------------------------------------------------------------------- ------------------------------
  [**Parameter**]{.strong}            [**Behavior**]{.strong}                                                                                              [**Default value**]{.strong}
  `clustering`{.literal}              Enable/disable clustering.                                                                                           `true`{.literal}
  `clustering.engine`{.literal}       Specifies which clustering engine to use. If not specified, the first declared engine will become the default one.   first in a list
  `clustering.results`{.literal}      When true, the component will run a clustering of the search results (this should be enabled).                       `true`{.literal}
  `clustering.collection`{.literal}   When true, the component will run a clustering of the whole document index.                                          `false`{.literal}
  ----------------------------------- -------------------------------------------------------------------------------------------------------------------- ------------------------------
:::

 

From the preceding list, some parameters[]{#id288617159 .indexterm} are
of the search component and some of them are of the request handler.
There are some additional parameters that are specific to engine-level
configuration. We can pass these parameters to the query url to modify
configurations at query runtime.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec110}Result clustering implementation {#result-clustering-implementation .title}

</div>

</div>
:::

Solr\'s built-in example `techproducts`{.literal} contains preconfigured
components[]{#id288617177 .indexterm} for result clustering, but by
default, the configurations are disabled.

The clustering implementation requires the following configurations.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec57}Install the clustering contrib {#install-the-clustering-contrib .title}

</div>

</div>
:::

The clustering contrib extension[]{#id288617270 .indexterm} requires
`solr-clustering-*.jar`{.literal} under `/dist`{.literal} and all JARs
under `contrib/clustering/lib`{.literal}. Configure the file path
according to your system file path in `solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<lib dir="${solr.install.dir:../../../..}/contrib/clustering/lib/" regex=".*\.jar" />
 <lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-clustering-\d.*\.jar" />
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec58}Declare the cluster search component {#declare-the-cluster-search-component .title}

</div>

</div>
:::

[** **]{.strong}Define the search[]{#id288622832 .indexterm} component
for clustering in `solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<searchComponent name="clustering" enable="true"
 class="solr.clustering.ClusteringComponent" >
 <lst name="engine">
 <str name="name">lingo</str>
 <str name="carrot.algorithm">org.carrot2.clustering.lingo.LingoClusteringAlgorithm</str>
 <str name="carrot.resourcesDir">clustering/carrot2</str>
 </lst>
 <lst name="engine">
 <str name="name">stc</str>
 <str name="carrot.algorithm">org.carrot2.clustering.stc.STCClusteringAlgorithm</str>
 <str name="carrot.resourcesDir">clustering/carrot2</str>
 </lst>
</searchComponent>
```
:::

We can also declare cluster components as engines or a multiple
clustering pipeline, which can be selected by specifying the parameter
`clustering.engine=<engine name>`{.literal} in the query URL.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec59}Declare the request handler and include the cluster search component {#declare-the-request-handler-and-include-the-cluster-search-component .title}

</div>

</div>
:::

Define the request handler[]{#id288622860 .indexterm} for the
cluster[]{#id288622885 .indexterm} in `solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<requestHandler name="/clustering" startup="lazy" enable="true" class="solr.SearchHandler">
 <lst name="defaults">
 <bool name="clustering">true</bool>
 <bool name="clustering.results">true</bool>
 <!-- Field name with the logical "title" of a each document (optional) -->
 <str name="carrot.title">name</str>
 <!-- Field name with the logical "URL" of a each document (optional) -->
 <str name="carrot.url">id</str>
 <!-- Field name with the logical "content" of a each document (optional) -->
 <str name="carrot.snippet">features</str>
 <!-- Apply highlighter to the title/ content and use this for clustering. -->
 <bool name="carrot.produceSummary">true</bool>
 <!-- the maximum number of labels per cluster -->
 <!--<int name="carrot.numDescriptions">5</int>-->
 <!-- produce sub clusters -->
 <bool name="carrot.outputSubClusters">false</bool>
 <!-- Configure the remaining request handler parameters. -->
 <str name="defType">edismax</str>
 <str name="qf">
 text^0.5 features^1.0 name^1.2 sku^1.5 id^10.0 manu^1.1 cat^1.4
 </str>
 <str name="q.alt">*:*</str>
 <str name="rows">100</str>
 <str name="fl">*,score</str>
 </lst>
 <arr name="last-components">
 <str>clustering</str>
 </arr>
</requestHandler>
```
:::

Now we are done with cluster configurations. Let\'s run a cluster for
the built-in example `techproducts`{.literal} by setting
`enable="true"`{.literal} in the `searchComponent`{.literal}
and `requestHandler`{.literal} configuration. We can enable the same by
specifying a JVM system property using the following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
solr start -e techproducts -Dsolr.clustering.enabled=true
```
:::

Let\'s run a query for electronics using the configured request
handler `/clustering`{.literal} and see the cluster response.

[**URL**]{.strong}:[** **]{.strong}`http://localhost:8983/solr/techproducts/clustering?q=electronics&rows=100`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":32},
  "response":{"numFound":14,"start":0,"maxScore":2.9029632,"docs":[
      ....
    ....
      ]
  },
  "clusters":[{
      "labels":["DDR"],
      "score":3.037927435185717,
      "docs":["TWINX2048-3200PRO",
        "VS1GB400C3",
        "VDBDB1A16"]},
    {
      "labels":["iPod"],
      "score":7.317758461138239,
      "docs":["F8V7067-APL-KIT",
        "IW-02",
        "MA147LL/A"]},
    {
      "labels":["Canon"],
      "score":6.785392802370259,
      "docs":["0579B002",
        "9885A004"]},
    {
      "labels":["Hard Drive"],
      "score":10.460153088070832,
      "docs":["SP2514N",
        "6H500F0"]},
    {
      "labels":["Retail"],
      "score":1.629540936033123,
      "docs":["TWINX2048-3200PRO",
        "VS1GB400C3"]},
    {
      "labels":["Video"],
      "score":10.060361253597023,
      "docs":["MA147LL/A",
        "100-435805"]},
    {
      "labels":["Other Topics"],
      "score":0.0,
      "other-topics":true,
      "docs":["EN7800GTX/2DHTV/256M",
        "3007WFP",
        "VA902B"]}]
}
```
:::

Here we can see a few clusters discovered for the query
(`q=electronics`{.literal}). Each cluster has a label and the score
shows the kindness of the cluster. The score is specific to an algorithm
and meaningful only in relation to the scores of other clusters in the
same set. A score with a higher value is better.



[]{#ch07lvl1sec53}Spatial search {#spatial-search .title style="clear: both"}
--------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Location-based data search is a very[]{#id288339954 .indexterm}
important requirement nowadays, such as searching for distances from a
place, searching a house within a radius, and so on. Solr supports
searching for location-based data called spatial or geospatial searches.

This can be implemented by indexing a field in each document that
contains a geographical point (a latitude and longitude); and then at
query time, we can find and sort documents by distance from a
geolocation (latitude and longitude). The matching listings (results)
can be displayed on an interactive map, in which we can zoom in/out and
move the map center point to find nearby listings using spatial
search. Like latitude and longitude, Solr also allows us to index
geographical shapes (polygons), which are used to find a document that
intersects geographical regions. Spatial search implemented by indexing
a field (latitude and longitude) helps to search from a specific point,
while implementing by indexing geographical shapes helps to search all
documents within a given range. Solr also supports returning the
distances of matching documents in a response. We can also apply sorting
on distances to provide more useful results to the user.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec111}Spatial search implementation {#spatial-search-implementation .title}

</div>

</div>
:::

There are various sequential steps[]{#id288339971 .indexterm} we need to
execute in order to implement spatial search. 

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec60}Field types {#field-types .title}

</div>

</div>
:::

First we need to configure[]{#id288339986 .indexterm} field type in
`managed-schema.xml`{.literal} or `schema.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="location" class="solr.LatLonPointSpatialField" docValues="true"/>
```
:::

 There are mainly four field types available in Solr that support
spatial search:

::: {.itemizedlist}
-   `LatLonPointSpatialField`{.literal}: The most commonly used field
    type and an alternative to `LatLonType`{.literal} (deprecated) for
    latitude---and longitude-based search.
-   `LatLonType`{.literal}: Its non-geodetic twin `PointType`{.literal}
    field type, and this one is deprecated now.
-   `SpatialRecursivePrefixTreeFieldType`{.literal}: RPT supports more
    advanced search features, such as polygons and
    heatmaps. `RptWithGeometrySpatialField`{.literal} is derived from
    RPT. It is used for geography-based searches. Sorting and boosting
    are not supported.
-   `BBoxField`{.literal}: Used to search a bounding box. It supports
    sorting and boosting.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec61}Query parser {#query-parser .title}

</div>

</div>
:::

Previously, we explored various query parsers that support normal
searching. But when it comes to spatial search, we need to
configure[]{#id288340087 .indexterm} a special parser that supports
spatial search:

::: {.mediaobject}
![](7_files/4f57348f-d800-4cdb-858c-db554dcda8b7.png)
:::

 There are two types of spatial search parsers available in Solr.

::: {.itemizedlist}
-   `geofilt`{.literal}: The `geofilt`{.literal} parser is used to
    retrieve results based on the geospatial distance from a given
    point. Please look at the preceding image. Another way of looking at
    it is that it creates a circular shape filter. For example, to find
    all documents within 5 kilometers of a given latitude-longitude
    point, the query will be:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
&q=:&fq={!geofilt sfield=location}&pt=45.15,-93.85&d=5
```
:::

This filter returns all results within a circle of the given radius
around the initial point.

::: {.itemizedlist}
-   `bbox`{.literal}[**: **]{.strong}The `bbox`{.literal} parser works
    like `geofilt`{.literal}, except that it uses the bounding box of
    the calculated circle. Please look at the preceding diagram. For the
    same example, the query will be:
:::

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
&q=:&fq={!bbox sfield=location}&pt=45.15,-93.85&d=5
```
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch07lvl3sec62}Spatial search query parser parameters {#spatial-search-query-parser-parameters .title}

</div>

</div>
:::

Both the parsers support the[]{#id288341734 .indexterm} following
parameters:

::: {.informaltable}
  -------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
  [**Parameter**]{.strong}   [**Behavior**]{.strong}                                                                                                                                                                                                                                                                                                               [**Default value**]{.strong}
  `d`{.literal}              Specifies a radius distance (usually in kilometers) within the document that should be considered for matching.                                                                                                                                                                                                                       
  `pt`{.literal}             Specifies latitude and longitude for the format as `lat,lon`{.literal}, `x,y`{.literal} for `PointType`{.literal}, or `x, y`{.literal} for RPT field types.                                                                                                                                                                           
  `sfield`{.literal}         Specifies a spatial field defined in `managed-schema.xml`{.literal} or `schema.xml`{.literal}.                                                                                                                                                                                                                                        
  `score`{.literal}          If the query is used in a scoring context, this determines what type of scores will be produced. It is not supported by `LatLonType`{.literal} or `PointType`{.literal}. Possible values are `none`{.literal}, `kilometers`{.literal}, `miles`{.literal}, `degrees`{.literal}, `distance`{.literal}, and `recipDistance`{.literal}.   `none`{.literal}
  `filter`{.literal}         If set to `false`{.literal}, parser scoring will be disabled. This is not supported by `LatLonType`{.literal} or `PointType`{.literal}.                                                                                                                                                                                               `true`{.literal}
  -------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------
:::
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch07lvl2sec112}Function queries {#function-queries .title}

</div>

</div>
:::

In spatial search, along with returning matching documents within a
given range, Solr also supports calculation of matching document
distances from the[]{#id288618901 .indexterm} search point and merging
them in the returning response. Solr even supports sorting and boosting
on the[]{#id288618909 .indexterm} calculated distance. To calculate
distances, the following are the functions available in Solr:

::: {.itemizedlist}
-   `geodist`{.literal}: This calculates the distance between too points
    (latitude and longitude)
-   `dist`{.literal}: Calculates the p-norm distance between
    multidimensional vectors
-   `hsin`{.literal}: This calculates the distance between two points on
    a sphere
-   `sqedist`{.literal}: Calculates the squared Euclidean distance
    between two points
:::

From the preceding list, `geodist`{.literal} is the most commonly used
function for most search cases. It takes three optional parameters:
`sfield`{.literal} (spatial field defined in
`managed-schema.xml`{.literal}), latitude, and longitude. For example,
we find all cities from the searching point (`45.15`{.literal},
`-93.85`{.literal}) within a radius of 50 kilometers, calculate their
distance from the searched point, and return in the response:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
&q=:&fq={!geofilt}&sfield=location&pt=45.15,-93.85&d=50&fl=id,city,distance:geodist()
```
:::

Additionally, we sort by distance ascending. The following is the query:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
&q=:&fq={!geofilt}&sfield=location&pt=45.15,-93.85&d=50&fl=id,city,distance:geodist()
&sort=geodist() asc
```
:::

This is the basic overview of spatial search. There are many more
spatial searching features available in Solr. Exploring every feature
here is not possible. However, we can take this chapter as a reference
and explore more spatial search features. Rather than going into more
details of spatial search, let\'s move on to the next chapter.



[]{#ch07lvl1sec54}Summary
-------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In this chapter, we explored and understood various searching
functionalities such as spellchecking, suggester, pagination, result
grouping, and result clustering. Finally, we looked at spatial search.

So far, we have seen configurations for each one and executed examples
by configuring various functionality parameters. Now let\'s move to the
next chapter, where we will see how to configure Solr for production and
learn fine-tuning methodologies for better performance. We will explore
how to secure Solr and how to take backups. We will configure logging
and get an overview of SolrCloud.
:::
:::

::: {.ng-hide ng-show="!productController.entitled && productController.isTruncatedContent"}
::: {.fade-out ng-show="productController.productType === 'book'"}
:::
:::

::: {.ng-hide ng-show="!productController.entitled && productController.productType === 'video'"}
![](8_files/f809d079111e31b1f0b7716cb973d607.jpg){.mb15 .ng-hide}
:::
:::

::: {.video-wrapper .ng-hide ng-show="productController.productType === 'video' && productController.entitled"}
::: {#transcript .transcript .panel-bottom .panel-default .ng-hide ng-show="productController.hasCaptions && productController.showTranscript"}
:::

[Show transcript]{.link .link-black .underline .ng-binding .ng-hide}
:::
:::

::: {.row}
:::

::: {.row .ns ng-show="productController.previousSection && productController.nextSection"}

------------------------------------------------------------------------

[ Previous
Section](https://subscription.packtpub.com/book/big_data_and_business_intelligence/9781788837385/7/ch07lvl1sec53/spatial-search){.btn
.btn-primary .pull-left .btn-lg .btn-block} [Next Section
](https://subscription.packtpub.com/book/big_data_and_business_intelligence/9781788837385/8){.btn
.btn-primary .pull-right .btn-lg .btn-block}
:::

::: {.row .ns .ng-hide ng-show="!productController.previousSection && productController.nextSection"}

------------------------------------------------------------------------

[Next Section
](https://subscription.packtpub.com/book/big_data_and_business_intelligence/9781788837385/8){.btn
.btn-primary .pull-right .btn-lg .btn-block}
:::

::: {.row .ns .ng-hide ng-show="productController.previousSection && !productController.nextSection"}

------------------------------------------------------------------------

[ Previous
Section](https://subscription.packtpub.com/book/big_data_and_business_intelligence/9781788837385/7/ch07lvl1sec53/spatial-search){.btn
.btn-primary .pull-left .btn-lg .btn-block} [Complete Course
](https://subscription.packtpub.com/book/big_data_and_business_intelligence/9781788837385){.btn
.btn-primary .pull-right .btn-lg .btn-block}
:::

::: {.row .ns}

------------------------------------------------------------------------
:::
:::

::: {#reader-content .col-sm-12 .ng-scope .ng-hide ng-show="!productController.isContentAvailable" ng-class="reader-container"}
::: {.row}
::: {.book-content}
##### Something went wrong, please check your internet connection and try again\... {#something-went-wrong-please-check-your-internet-connection-and-try-again... .mb20}

[Retry](https://subscription.packtpub.com/#){.btn .btn-primary .btn-lg
.btn-block-xs}
:::
:::

::: {.row .ns}

------------------------------------------------------------------------
:::
:::
:::
:::
:::
:::

::: {#product-claim-modal .modal .fade tabindex="-1" role="dialog"}
::: {.modal-dialog role="document"}
::: {.modal-content .ng-scope ng-if="!productController.usingCredit"}
::: {.modal-header}
[×]{aria-hidden="true"}

#### Just Checking\... {#just-checking... .modal-title ng-show="!productController.claimTokenError"}

#### Sorry\... {#sorry... .modal-title .ng-hide ng-show="productController.claimTokenError"}
:::

::: {.modal-body ng-show="!productController.claimTokenError"}
Are you sure you would like to use one of your credits to purchase this
title?
:::

::: {.modal-body .ng-binding .ng-hide ng-show="productController.claimTokenError"}
:::

::: {.modal-footer}
No

Yes
:::
:::
:::
:::

::: {#playlistCreateModal .modal .fade tabindex="-1" role="dialog"}
::: {.modal-dialog role="document"}
::: {.modal-content}
::: {.modal-header}
#### Create Playlist
:::

::: {.modal-body}
::: {.form-group}
Playlist name
:::

::: {.row}
::: {.col-xs-6}
[Cancel]{.btn .btn-block .btn-default}
:::

::: {.col-xs-6}
Create
:::
:::
:::
:::
:::
:::
:::
:::
:::

::: {#errorModal .modal .fade tabindex="-1" role="dialog"}
::: {.modal-dialog role="document"}
::: {.modal-content}
::: {.modal-header}
[×]{aria-hidden="true"}

#### Something went wrong {#something-went-wrong .ng-binding}
:::

::: {.modal-body}
Please try again
:::

::: {.modal-footer}
Close
:::
:::
:::
:::

::: {.container .mobile-footer-group}
::: {.col-xs-12 .primary-footer-text}
::: {.tabs}
::: {.tab}
eBooks & Videos

::: {.tab-content}
-   [Web
    Development](https://subscription.packtpub.com/search?released=Available&category=Web%20Development){.ng-binding}
-   [Programming](https://subscription.packtpub.com/search?released=Available&category=Programming){.ng-binding}
-   [Data](https://subscription.packtpub.com/search?released=Available&category=Data){.ng-binding}
-   [Cloud &
    Networking](https://subscription.packtpub.com/search?released=Available&category=Cloud%20%26%20Networking){.ng-binding}
-   [Mobile](https://subscription.packtpub.com/search?released=Available&category=Mobile){.ng-binding}
-   [Security](https://subscription.packtpub.com/search?released=Available&category=Security){.ng-binding}
-   [IoT &
    Hardware](https://subscription.packtpub.com/search?released=Available&category=IoT%20%26%20Hardware){.ng-binding}
-   [Game
    Development](https://subscription.packtpub.com/search?released=Available&category=Game%20Development){.ng-binding}
-   [Business &
    Other](https://subscription.packtpub.com/search?released=Available&category=Business%20%26%20Other){.ng-binding}
-   [View
    All](https://subscription.packtpub.com/search?released=Available)
:::
:::

::: {.tab}
Useful Links

::: {.tab-content}
-   [Books](https://subscription.packtpub.com/search?products=Book&released=Available){.ng-binding}
-   [Videos](https://subscription.packtpub.com/search?products=Video&released=Available){.ng-binding}
-   [Learning
    Paths](https://subscription.packtpub.com/learning-paths){.ng-binding}
-   [Code
    Downloads](https://www.packtpub.com/support/code-downloads){.ng-binding}
-   [Contact Us](https://www.packtpub.com/contact){.ng-binding}
:::
:::
:::
:::
:::

::: {.container .footer-group}
::: {.footer-col .primary-footer-text}
eBooks & Videos

-   [Web
    Development](https://subscription.packtpub.com/search?released=Available&category=Web%20Development){.ng-binding}
-   [Programming](https://subscription.packtpub.com/search?released=Available&category=Programming){.ng-binding}
-   [Data](https://subscription.packtpub.com/search?released=Available&category=Data){.ng-binding}
-   [Cloud &
    Networking](https://subscription.packtpub.com/search?released=Available&category=Cloud%20%26%20Networking){.ng-binding}
-   [Mobile](https://subscription.packtpub.com/search?released=Available&category=Mobile){.ng-binding}
:::

::: {.footer-col .primary-footer-text .category-column}
-   [Security](https://subscription.packtpub.com/search?released=Available&category=Security){.ng-binding}
-   [IoT &
    Hardware](https://subscription.packtpub.com/search?released=Available&category=IoT%20%26%20Hardware){.ng-binding}
-   [Game
    Development](https://subscription.packtpub.com/search?released=Available&category=Game%20Development){.ng-binding}
-   [Business &
    Other](https://subscription.packtpub.com/search?released=Available&category=Business%20%26%20Other){.ng-binding}
-   [View
    All](https://subscription.packtpub.com/search?released=Available)
:::

::: {.footer-col .primary-footer-text}
Useful Links

-   [Books](https://subscription.packtpub.com/search?products=Book&released=Available){.ng-binding}
-   [Videos](https://subscription.packtpub.com/search?products=Video&released=Available){.ng-binding}
-   [Learning
    Paths](https://subscription.packtpub.com/learning-paths){.ng-binding}
-   [Code
    Downloads](https://www.packtpub.com/support/code-downloads){.ng-binding}
-   [Contact Us](https://www.packtpub.com/contact){.ng-binding}
:::

::: {.footer-col .primary-footer-text .sign-up-show .ng-scope ng-if="!$root.isIOS"}
Packt

Sign up to our emails for the latest subscription updates.

::: {.primary-footer-text .hide-confirmation ng-class="{'hide-confirmation': !$ctrl.displayMessage }"}
:::

::: {.primary-footer-text .hide-confirmation ng-class="{'hide-confirmation': !$ctrl.loading }"}
Submitting\...
:::

::: {.app-icons .ng-scope ng-if="!$root.isApp"}
[![Download on the App
Store](8_files/722c312b88d618e5136845252a309b79.png){.app-icon-badge}](https://apps.apple.com/app/packt/id1376577776?ls=1)
[![Get it on Google
Play](8_files/8303c4a7bb1e47b82f62c91a6204cab2.png){.app-icon-badge}](https://play.google.com/store/apps/details?id=com.packtpub.reader)
:::
:::
:::

::: {.subs-footer}
[ © 2020 Packt Publishing Limited All Rights Reserved ]{.copyright
.ng-binding} [\|]{.separator .ng-scope ng-if="!$root.isIOS"} [Privacy
Policy](https://www.packtpub.com/about/privacy-policy){.ng-scope}
[\|]{.separator .ng-scope ng-if="!$root.isIOS"} [Terms &
Conditions](https://www.packtpub.com/about/terms-and-conditions){.ng-scope}
:::

![](https://www.facebook.com/tr?id=445429252334850&ev=PageView&noscript=1){width="1"
height="1"}

::: {.intercom-lightweight-app aria-live="polite"}
::: {.intercom-lightweight-app-launcher .intercom-launcher role="button" tabindex="0" aria-label="Open Intercom Messenger"}
::: {.intercom-lightweight-app-launcher-icon .intercom-lightweight-app-launcher-icon-open}
:::

::: {.intercom-lightweight-app-launcher-icon .intercom-lightweight-app-launcher-icon-close}
:::
:::
:::

![](https://www.facebook.com/tr?id=445429252334850&ev=PageView&noscript=1){width="1"
height="1"}

::: {.annotator-adder .annotator-hide}
Annotate
:::

::: {.annotator-outer .annotator-editor .annotator-hide}
-   
-   Allow anyone to **view** this annotation
-   Allow anyone to **edit** this annotation

::: {.annotator-controls}
[Cancel](https://subscription.packtpub.com/#cancel){.annotator-cancel}
[Save](https://subscription.packtpub.com/#save){.annotator-save
.annotator-focus}
:::
:::

::: {.annotator-outer .annotator-viewer .annotator-hide}
:::
