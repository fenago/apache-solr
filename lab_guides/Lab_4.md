

[]{#ch04}Chapter 4. Mastering Text Analysis Methodologies {#chapter-4.-mastering-text-analysis-methodologies .title}
---------------------------------------------------------

</div>

</div>
:::

So far we have seen the installation of Solr server, schema design,
documents, fields, field types, the Schema API and schemaless mode.

In this chapter we will explore:

::: {.itemizedlist}
-   Text analysis
-   Analyzers
-   Tokenizers
-   Filters
-   Multilingual analysis
-   Phonetic matching



[]{#ch04lvl1sec28}Understanding text analysis {#understanding-text-analysis .title style="clear: both"}
---------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Nowadays, the search engine plays[]{#id288339996 .indexterm} an
important role in any search application. End users always expect
accurate, efficient, and fast results from searches. The job of a search
engine is to fulfill the search requirement in an easy and faster way.
To achieve the expected level of search accuracy, Solr executes multiple
processes sequentially behind the scenes: it examines the input string,
normalizes the text, generates the token stream, builds indexes, and so
on. The set of all of these processes is called text analysis. Let\'s
explore text analysis in detail.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec65}What is text analysis? {#what-is-text-analysis .title}

</div>

</div>
:::

Text analysis is a Solr mechanism that[]{#id288340078 .indexterm} takes
place in two phases:

::: {.itemizedlist}
-   During index time, optimize the input terms, feeding the
    information, generates the token stream and builds the indexes
-   During query time, optimize the query terms, generates the token
    stream, matches with the term generated at index time, and provides
    results
:::

Let's dive deeper and understand:

::: {.itemizedlist}
-   How exactly Solr works to build indexes
-   How to optimize the query terms to match with indexes
-   How we get accurate, efficient, and fast results
:::

If someone is searching for the string
`The Host Country of Soccer World Cup 2018`{.literal} and someone else
is searching for the string
`The Host Nation of Football world cup 2018`{.literal}, the result
should be `Russia`{.literal} in both the cases. We will learn later in
this chapter how Solr matches a query containing `Nation`{.literal} and
`Football`{.literal} to documents containing `Country`{.literal} and
`Soccer`{.literal}.

We can\'t assume which type of search input comes from the end users
during the search, for example:

::: {.itemizedlist}
-   Searching for `Soccer`{.literal} and `Football`{.literal}
-   Searching for `Unites States Of America`{.literal} and
    `USA`{.literal}
-   Searching for `South Africa`{.literal} and `RSA`{.literal}
-   Searching for `air-crew`{.literal}, `aircrew`{.literal}, and
    `air crew`{.literal} 
:::

All of these are different input patters that contain ideally the same
meaning in natural languages. But the user may provide input in a
non-natural language also, like this:

::: {.itemizedlist}
-   Searching for `Hundred GB`{.literal} and `100 gigabyte`{.literal}
-   Searching for `Caffé`{.literal} and `cafe`{.literal}
:::

There are also other complex search patterns that may be used by end
users at query time. So, looking at the overall scope of possible search
patterns used by end users during search time, Solr has to be ready to
determine all possible search patterns, analyze them, and output them
accurately with efficient results.

Here, however, we don\'t have to worry because [*The
Solr*]{.emphasis} is an intelligent search engine that handles all
search input patterns being used across the world. So now, without
thinking too much about Solr\'s searching capability, let\'s see how
Solr actually works to meet all our search requirements.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec66}How text analysis works {#how-text-analysis-works .title}

</div>

</div>
:::

We have seen an overview of Solr\'s text analysis; now let\'s learn how
Solr actually implements the analysis process. Here are the common
steps[]{#id288340420 .indexterm} normally used by Solr in an analysis
process:

::: {.itemizedlist}
-   [**Removing stop words**]{.strong}:
    ::: {.itemizedlist}
    -   Stop words (common letters/words) such as `a`{.literal},
        `an`{.literal}, `the`{.literal}, `at`{.literal}, `to`{.literal},
        `for`{.literal}, and so on are removed from the text string; so
        Solr will not give results for these common words. These words
        are configured in a text file (for example,
        `stopwords_en.txt`{.literal}) and this file needs to be imported
        in the analysis configuration.
    :::
-   [**Adding synonyms**]{.strong}:
    ::: {.itemizedlist}
    -   Solr reads synonyms from the text file (synonyms) and adds them
        to the token stream. All synonyms need to be preconfigured in
        the text file, such as `Football`{.literal} and
        `Soccer`{.literal}, `Country`{.literal} and `Nation`{.literal},
        and so on.
    :::
-   [**Stemming the words**]{.strong}:
    ::: {.itemizedlist}
    -   Solr transforms words into a base form using language-specific
        rules
    -   It transforms
        [**removing**]{.strong} → [**remove**]{.strong} (removes
        [**ing**]{.strong})
    -   It transforms
        [**searches**]{.strong} → [**search**]{.strong} (converts to
        singular)
    :::
-   [**Set all to lowercase**]{.strong}:
    ::: {.itemizedlist}
    -   Solr converts all tokens to lowercase
    :::
:::

These are the common steps that Solr performs in all normal scenarios.
But in real life, things may not be that easy and straightforward. We
can\'t assume which type of search input pattern in which language end
users may use. To meet all possible requirements, Solr\'s intelligence
is hidden in its three powerful tools:

::: {.itemizedlist}
-   [**Analyzer**]{.strong}: The task of the analyzer is to determine
    the text and generate the token stream accordingly
-   [**Tokenizer**]{.strong}: The tokenizer splits the input string at
    some delimiter and generates a token stream, say
    `Mastering Apache Solr`{.literal} to `Mastering`{.literal},
    `Apache`{.literal}, and `Solr`{.literal} (splitting at white spaces)
-   [**Filter**]{.strong}: The filter performs one of these tasks:
    ::: {.itemizedlist}
    -   [**Adding**]{.strong}: Adds new tokens to the stream, such as
        adding synonyms
    :::

    ::: {.itemizedlist}
    -   [**Removing**]{.strong}: Removes tokens from the stream, such
        as stop words
    -   [**Conversation**]{.strong}: Converts tokens from one form to
        another, such as uppercase to lowercase
    :::
:::

We will explore each one of these in detail later in this chapter. By
using these three tools, Solr becomes a powerful search engine to meet
any complex search requirement.

Solr provides a UI Admin Console for us to understand the analysis
process. Here, we can easily understand what is actually happening and
which steps are getting executed during index and query time analysis.
To access the admin console, navigate to <http://localhost:8983/solr>.

In the Solr admin console, the user can easily understand text analysis,
querying, and so on:

::: {.mediaobject}
![](2_files/cac3c476-f93a-490d-b4ab-993f43b3b8fc.jpg)
:::

Go to **`Dashboard | Core Selector`**, select your configured example,
and click on **`Analysis`**. Here we are using Solr\'s built-in example
`techproducts`{.literal}.

We have configured the `text_en`{.literal} field as follows in the
`managed-schema.xml`{.literal} file:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer type="index">
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
 <filter class="solr.LowerCaseFilterFactory"/> 
 </analyzer>
 <analyzer type="query">
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
 <filter class="solr.SynonymGraphFilterFactory" synonyms="synonyms.txt" ignoreCase="true" expand="true"/>
 <filter class="solr.LowerCaseFilterFactory"/> 
 </analyzer>
 </fieldType>
```
:::

Applying [**lower case filter**]{.strong} ([**LCF**]{.strong}) during
index[]{#id288340659 .indexterm} time and query[]{#id288340665
.indexterm} time:

::: {.mediaobject}
![](2_files/825bb1bd-5c2c-49aa-8d7a-53f6de026a40.png)
:::

Text analysis applied during index time on the input string.

[**Input**]{.strong}: `The Nation of soccer`{.literal}

[**Output**]{.strong}:[** **]{.strong}`nation`{.literal}, `soccer`{.literal}

[**Analysis applied**]{.strong}:

::: {.itemizedlist}
-   It is split at white spaces
-   Stop words (`The`{.literal}, `of`{.literal}) removed
-   All set to lowercase
:::

Text analysis applied during query time on the input string.

[**Input**]{.strong}: `Famous country for football`{.literal}

[**Output**]{.strong}:
`famous`{.literal}, `nation`{.literal}, `country`{.literal}, `soccer`{.literal},
and `football`{.literal}

[**Analysis applied**]{.strong}:

::: {.itemizedlist}
-   Split at white spaces
-   Stop words (`for`{.literal}) removed
-   Synonyms added (`nation`{.literal} for `country`{.literal} and
    `soccer`{.literal} for `football`{.literal})
-   All set to lowercase
:::

As we know that analysis takes place in both phases (index time and
query time), we have configured two `<analyzer>`{.literal} elements
distinguished by type attribute value (`index`{.literal} for index time
and `query`{.literal} for query time). And we\'ve configured a set of
tokenizers and filters in each phase. The configurations for each phase
may vary based on requirements. It is also possible to define a single
`<analyzer>`{.literal} element without the `type`{.literal} attribute
and configure tokenizers and filters inside that `<analyzer>`{.literal}
element. This type of config set will apply the same configurations to
both phases. This is useful for cases where we want perfect string
matching. We will discuss this later when we explore the analyzer in
detail. Now consider the preceding analysis example.

During the index and query phases, the configured set of configurations
is executed by the respective tokenizers and filters and the final token
stream is generated. This stream
(`nation`{.literal} and `soccer`{.literal}) is stored as an index. Now,
during query time, the final token stream of the query phase
(`famous`{.literal}, `nation`{.literal}, `country`{.literal}, `soccer`{.literal},
and `football`{.literal}) will match the final token stream of the index
phase (`nation`{.literal}, and `soccer`{.literal}), and we can see that
there are multiple similar tokens (`nation`{.literal},
and `soccer`{.literal}) available in both streams. So here, both
`The Nation of soccer`{.literal} and
`Famous country for football`{.literal} search for the same results.
This is the way how text analysis process executing. Here we have just
provided an overview of an analyzer, tokenizer, and filters. We will
understand[]{#id288340893 .indexterm} all of these analysis tools in
detail later in this chapter.


[]{#ch04lvl1sec29}Understanding analyzer {#understanding-analyzer .title style="clear: both"}
----------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

We have seen an overview[]{#id288339944 .indexterm} of text analysis.
Now let\'s dive deeper and understand the core processes running behind
the scenes of analysis. As we have seen previously, the analyzer,
tokenizer and filter are the three main components Solr uses for text
analysis. Let\'s explore an analyzer. 

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec67}What is an analyzer? {#what-is-an-analyzer .title}

</div>

</div>
:::

An analyzer examines the text[]{#id288339960 .indexterm} of fields and
generates a token stream. Normally, only fields of type
`solr.TextField`{.literal} will specify an analyzer. An analyzer is
defined as a child element of the `<fieldType>`{.literal} element in the
`managed-schema.xml`{.literal} file. Here is a simple analyzer
configuration:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer class="org.apache.lucene.analysis.core.WhitespaceAnalyzer"/>
</fieldType>
```
:::

Here, we have defined a single `<analyzer>`{.literal} element. This is
the simplest way to define an analyzer. We\'ve already understood the
`positionIncrementGap`{.literal} attribute, which adds a space
between multi-value fields, in the previous chapter.

The class attribute value is a fully qualified Java class name. The
input text will be analyzed by the analyzer class
(`WhitespaceAnalyzer`{.literal}). Let\'s configure the following
analyzer configuration in `managed-schema.xml`{.literal} and verify
through the admin console:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField">
    <analyzer class="org.apache.lucene.analysis.core.WhitespaceAnalyzer"/>
  </fieldType>
```
:::

Applying WhitespaceAnalyzer on the input string:

::: {.mediaobject}
![](3_files/60e937e9-f3d3-48b1-ae66-f4992d5af93e.png)
:::

[**Input**]{.strong}:
`Running simple Solr analyzer through admin console`{.literal}

[**Output**]{.strong}:[** **]{.strong}`Running`{.literal}, `simple`{.literal}, `Solr`{.literal}, `analyzer`{.literal}, `through`{.literal}, `admin`{.literal},
and `console`{.literal}

This is a very simple example. The analyzer
class `WhitespaceAnalyzer`{.literal} splits the string at white spaces
and generates the token stream. The named class must derive from
`org.apache.lucene.analysis.Analyzer`{.literal}. 

The analyzer may be a single class or may be composed of a series of
tokenizers and filter classes. Configuring an analyzer along with
tokenizers and filters is very easy and straightforward. Define the
`<analyzer>`{.literal} element with child elements that name factory
classes for the tokenizer and filters. Always configure tokenizers and
filters in the order you want to run them in. Here is an example:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
 <filter class="solr.LowerCaseFilterFactory"/> 
 </analyzer>
</fieldType>
```
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip26}Note {#note .title}

Solr will execute tokenizers and filters in the order in which they are
configured. The execution order should be defined logically. For
example, applying an LCF before a stop filter will impact the
performance, as stop words are always going to be removed from the
stream and still we would be unnecessarily applying an LCF.
:::

The input text will be passed to the first element in the list (here it
is `StandardTokenizerFactory`{.literal}) and generate tokens
accordingly. The output from this will be the input to the next
(immediate successor; here it is `StopFilterFactory`{.literal}). In this
way, all the steps will be executed. The tokens generated from the
last filter (`LowerCaseFilterFactory`{.literal} here) will be the final
token stream. Solr builds indexes using this stream.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec68}How an analyzer works {#how-an-analyzer-works .title}

</div>

</div>
:::

Text analysis takes place in two[]{#id288340292 .indexterm} phases,
during index time and during query time. So we need to configure
`<analyzer>`{.literal} for both phases, distinguished by the type
attribute. In the preceding example, we have configured a single
`<analyzer>`{.literal} element along with tokenizers and filters and not
specified the `type`{.literal} attribute. So Solr will apply the same
configurations for the both phases (index and query). This type of
configuration is required for some scenarios, say if we want to match
strings exactly.

It is always advisable to define two separate `<analyzer>`{.literal} for
each phase distinguished by a `type`{.literal} attribute. Doing so is
required in some scenarios where we want to apply some steps at query
time but not index time. For example:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer type="index">
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
 <filter class="solr.LowerCaseFilterFactory"/> 
 </analyzer>
 <analyzer type="query">
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
 <filter class="solr.SynonymGraphFilterFactory" synonyms="synonyms.txt" ignoreCase="true" expand="true"/>
 <filter class="solr.LowerCaseFilterFactory"/> 
 </analyzer>
</fieldType>
```
:::

Here, the synonyms filter (`SynonymGraphFilterFactory`{.literal}) is
injected at query time but not index time. If we inject this filter at
index time, after adding a new synonym to the `synonyms.txt`{.literal}
file, we need to build document indexes again. Also the indexes for
synonyms will be created and the index size will be increased.

At the time of configuring two separate `<analyzer>`{.literal} for the
index and query, we also need to bear in mind that configurations for
both `<analyzer>`{.literal} must be compatible with each other. For
example, we have used the `LowerCaseFilterFactory`{.literal} filter in
both `<analyzer>`{.literal} definitions in the preceding configurations.
If we define an LCF only in the indexing phase and not in the querying
phase, a query for `Soccer`{.literal} will never match with the indexed
term `soccer`{.literal}.

So far we have learned the following:

::: {.itemizedlist}
-   Solr performs text analysis in both phases: index and query time.
-   Solr uses analyzers, tokenizers, and filters for text analysis.
-   Using the analyzers, tokenizers, and filters, Solr examines the
    input string during index time. It normalizes accordingly and
    generates the token stream. Solr builds indexes based on this token
    stream.
-   Using the same analyzer, tokenizers, and filters during query
    time, Solr examines the query string, normalizes accordingly,
    and generates a token stream. This token stream will be compared
    with the token stream generated at index time and return the
    matching output.
-   The configuration of analyzer depends on the search requirements.
-   Defining a single analyzer will be considered as the same analysis
    configuration for both phases.
-   The advisable approach is always to define two separate analyzer for
    each phase so that we can apply friendlier configurations for each
    phase.
-   When defining the tokenizers and filter, the ordering should be
    logical.
-   Both the phase configurations should be compatible with each other.
:::

So now we can apply text analysis on slightly more complex search
strings. Let\'s apply more tokenizers and filters and understand their
behavior. Previously, we mentioned an example of searching
`The Host Country of Soccer World Cup 2018`{.literal} and searching for
`The Host Nation of Football world cup 2018`{.literal}; in both cases,
the result should be `Russia`{.literal}. Let\'s see how Solr analyzes
this example. Up next are the analyzer configurations configured in the
`managed-schema.xml`{.literal} file. There are two separate
`<analyzer>`{.literal} elements for index time and query time,
distinguished by the `type`{.literal} attribute:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
    <analyzer type="index">
      <tokenizer class="solr.StandardTokenizerFactory"/>
      <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
      <filter class="solr.LowerCaseFilterFactory"/> 
    </analyzer>
    <analyzer type="query">
      <tokenizer class="solr.StandardTokenizerFactory"/>
      <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
      <filter class="solr.SynonymGraphFilterFactory" synonyms="synonyms.txt" ignoreCase="true" expand="true"/>
      <filter class="solr.LowerCaseFilterFactory"/> 
    </analyzer>
  </fieldType>
```
:::

Text analysis by configuring various tokenizers and filters during index
time and query time:

::: {.mediaobject}
![](3_files/b77d3e9c-0a87-4cb0-8866-42b166ca0e62.png)
:::

In the preceding screen, we have provided the string
`The Host Country of Soccer World Cup 2018`{.literal} at index time and
`The Host Nation of Football world cup 2018`{.literal} at query time. 

An analyzer during index time.

[**String**]{.strong}:
`The Host Country of Soccer World Cup 2018`{.literal}

Solr executes all tokenizers and filters sequentially configured inside
the `<analyzer type="index">`{.literal} definition and generates a token
stream accordingly. It builds indexes using this token stream.

[**StandardTokenizerFactory**]{.strong} ([**ST**]{.strong}): Splits the
string at white spaces:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<tokenizer class="solr.StandardTokenizerFactory"/>
```
:::

[**Input**]{.strong}:
`The Host Country of Soccer World Cup 2018`{.literal}

[**Output**]{.strong}: `The`{.literal}, `Host`{.literal}, `Country`{.literal}, `of`{.literal}, `Soccer`{.literal}, `World`{.literal}, `Cup`{.literal},
and `2018`{.literal}

The analyzer passes the input text to the first element from the list.
Here, it is `StandardTokenizerFactory`{.literal}. The entire string was
split at white spaces using standard tokenizer. Now the output of this
tokenizer will be the input to the next (immediate successor) in the
sequence[]{#id288617277 .indexterm} chain; here, it is
`StopFilterFactory`{.literal}.

[**StopFilterFactory**]{.strong}:  Removes all stop words (common)
listed in the `stopwords_en.txt`{.literal} file:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
```
:::

[**Input**]{.strong}: `The`{.literal}, `Host`{.literal},
`Country`{.literal}, `of`{.literal}, `Soccer`{.literal},
`World`{.literal}, `Cup`{.literal}, `2018`{.literal} ( output of
immediate predecessor)

[**Output: **]{.strong}`Host`{.literal},`Country`{.literal},`Soccer`{.literal},`World`{.literal},`Cup`{.literal},`2018`{.literal}

Here all stop words (`The`{.literal}, `of`{.literal}, and so on) are
removed from the steam. Stop words are nothing but all common words
(`a`{.literal}, `an`{.literal}, `the`{.literal}, `of`{.literal},
`at`{.literal}, `for`{.literal}, `in`{.literal}, and so on) listed in
the `stopwords_en.txt`{.literal} file. After the removal of these stop
words, Solr will not give matching results for these words. Now the
output of this filter will be the input to its immediate successor in
the chain; here, it is `LowerCaseFilterFactory`{.literal}.

The `attribute ignoreCase="true"`{.literal} (default `false`{.literal})
will ignore case when testing for stop words. If it is `true`{.literal},
the stop list should contain lowercase words.

[**LowerCaseFilterFactory**]{.strong}: Converts all tokens to lowercase:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<filter class="solr.LowerCaseFilterFactory"/> 
```
:::

[**Input**]{.strong}: `Host`{.literal}, `Country`{.literal}, `Soccer`{.literal}, `World`{.literal}, `Cup`{.literal}, `2018`{.literal}

[**Output**]{.strong}:[** **]{.strong}`host`{.literal}, `country`{.literal}, `soccer`{.literal}, `world`{.literal}, `cup`{.literal}, `X`{.literal}

All the incoming inputs will be converted to lowercase:

Now all three components (`StandardTokenizerFactory`{.literal},
`StopFilterFactory`{.literal}, and `LowerCaseFilterFactory`{.literal})
have executed their job sequentially and generated the final token
stream:

::: {.itemizedlist}
-   [**Final token
    stream**]{.strong}: `host`{.literal}, `country`{.literal}, `soccer`{.literal}, `world`{.literal}, `cup`{.literal}, `2018`{.literal}
:::

Next, Solr builds indexes based on this final token stream:

::: {.itemizedlist}
-   [**Analyzer**]{.strong}: Query time
-   [**String:**]{.strong}`The Host Nation of Football world cup 2018`{.literal}
:::

At query time, Solr executes all tokenizers and filters sequentially
configured inside the `<analyzer type="query">`{.literal} definition and
generates a token stream accordingly. This token stream will be compared
with the token stream generated during index time. The matching result
will be given as output.

[**StandardTokenizerFactory**]{.strong}: Splits the string at white
spaces:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<tokenizer class="solr.StandardTokenizerFactory"/>
```
:::

[**Input**]{.strong}:
`The Host Nation of Football world cup 2018`{.literal}

[**Output: **]{.strong}`The`{.literal}, `Host`{.literal}, `Nation`{.literal}, `of`{.literal}, `Football`{.literal}, `world`{.literal}, `cup`{.literal},`2018`{.literal}

The analyzer passes the input text to the first element from the list.
Here, it is `StandardTokenizerFactory`{.literal}. The entire string was
split at white spaces using Standard Tokenizer. The output of this
tokenizer will be the input to the immediate successor in the chain;
here, it is `StopFilterFactory`{.literal}.

[**StopFilterFactory**]{.strong}: Removes all stop words (common) listed
in the `stopwords_en.txt`{.literal} file.

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
```
:::

[**Input**]{.strong}: `The`{.literal}, `Host`{.literal},
`Nation`{.literal}, `of`{.literal}, `Football`{.literal},
`world`{.literal}, `cup`{.literal}, `2018`{.literal} (output of
immediate predecessor)

[**Output**]{.strong}: `Host`{.literal}, `Nation`{.literal},
`Football`{.literal}, `world`{.literal},
`cup`{.literal},`2018`{.literal}

Here all stop words are removed from the stream. The output of this
filter will be the input to its immediate successor in the chain,
`SynonymGraphFilterFactory`{.literal}.

The `attribute ignoreCase="true"`{.literal} (default: `false`{.literal})
will ignore casing when testing for stop words. If it is
`true`{.literal}, the stop list should contain lowercase words.

[**SynonymGraphFilterFactory**]{.strong}: Adds synonyms to the token
stream:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<filter class="solr.SynonymGraphFilterFactory" synonyms="synonyms.txt" ignoreCase="true" expand="true"/>
```
:::

[**Input**]{.strong}: `Host`{.literal}, `Nation`{.literal}, `Football`{.literal}, `world`{.literal}, `cup`{.literal}, `2018`{.literal}[**Output**]{.strong}:[** **]{.strong}`Host`{.literal}, `country`{.literal}, `Nation`{.literal}, `soccer`{.literal}, `Football`{.literal}, `world`{.literal}, `cup`{.literal}, `2018`{.literal}

The synonyms (`country`{.literal} for `nation`{.literal} and
`soccer`{.literal} for `Football`{.literal}) are added to the
stream. All synonyms are configured in the `synonyms.txt`{.literal}
file, as follows. We will see this filter in detail later in this
chapter. 

`Synonyms.txt file`{.literal}: Synonym mapping examples. Blank lines and
lines that start with `#`{.literal} will be ignored:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
football,soccer
dumb,stupid,dull
country => nation
smart,clever,bright => intelligent,genius
```
:::

Here, `SynonymGraphFilterFactory`{.literal} is configured at query time
but not at index time. Previously, we have seen the reason for this type
of configuration variation for index time and query time.

The output will be passed to the next filter,
`LowerCaseFilterFactory`{.literal}.

[**LowerCaseFilterFactory**]{.strong}: Converts all tokens to lowercase:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<filter class="solr.LowerCaseFilterFactory"/>
```
:::

[**Input**]{.strong}:[** **]{.strong}`Host`{.literal}, `country`{.literal}, `Nation`{.literal}, `soccer`{.literal}, `Football`{.literal}, `world`{.literal}, `cup`{.literal}, `2018`{.literal}

[**Output**]{.strong}:[** **]{.strong}`host`{.literal}, `country`{.literal}, `nation`{.literal}, `soccer`{.literal}, `football`{.literal}, `world`{.literal}, `cup`{.literal}, `2018`{.literal}

All the incoming inputs will be converted to lowercase.

Solr has examined the query string and produced the final token stream
using four components (`StandardTokenizerFactory`{.literal},
`StopFilterFactory`{.literal}, `SynonymGraphFilterFactory`{.literal},
and `LowerCaseFilterFactory`{.literal}).

Now, these are the final streams of both phases:

::: {.itemizedlist}
-   [**Indexed
    stream**]{.strong}: `host`{.literal}, `country`{.literal}, `soccer`{.literal}, `world`{.literal}, `cup`{.literal}, `2018`{.literal}
-   [**Query **]{.strong}[**stream**]{.strong}:[** **]{.strong}`host`{.literal}, `country`{.literal}, `nation`{.literal}, `soccer`{.literal}, `football`{.literal}, `world`{.literal}, `cup`{.literal}, `2018`{.literal}
:::

We can see that many tokens from the query stream are matching tokens
from the indexed stream, such as `country`{.literal} and
`soccer`{.literal}. So in this way, Solr will bring the same output for
the search string
`The Host Country of Soccer World Cup 2018`{.literal} and
`The Host Nation of Football world cup 2018`{.literal}. 

Thus, we have covered:

::: {.itemizedlist}
-   The Solr text analysis mechanism
-   The tasks of analyzer, tokenizer, and filters
-   Defining a single `<analyzer>`{.literal} or multiple
    `<analyzer>`{.literal} distinguished by the type attribute based on
    search requirements
-   Defining configurations steps logically such as using a LCF after a
    stop filter and adding a synonym filter at query time only (we
    explained both the examples previously)
:::

Now we are familiar with the Solr text analysis mechanism. Here we have
tried to explain it by taking a little complex string example, but we
can\'t assume which type of input may be entered during searches by end
users. Providing accurate results for any pattern of input string is the
main aim of any search engine. Solr comes with a number of tokenizers
and filters to challenge any input pattern. Solr is also efficient at
multiple language search. Here we have covered very few and common
tokenizers and filters, but Solr\'s list for tokenizers and filters does
not end there. There are many tokenizers and filters available. Let\'s
understand[]{#id288377221 .indexterm} the behavior of tokenizers in the
next section.


[]{#ch04lvl1sec30}Understanding tokenizers {#understanding-tokenizers .title style="clear: both"}
------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

We have previously seen that[]{#id288339995 .indexterm} an analyzer may
be a single class or a set of defined tokenizer and filter classes.

The analyzer executes the analysis process in two steps:

::: {.itemizedlist}
-   [**Tokenization (parsing)**]{.strong}: Using configured tokenizer
    classes
-   [**Filtering (transformation)**]{.strong}: Using configured filter
    classes
:::

We can also do preprocessing on a character stream before tokenization;
we can do this with the help of `CharFilters`{.literal} (we will see
this later in the chapter). An analyzer knows its configured field, but
a tokenizer doesn\'t have any idea about the field. The job of the
tokenizer is only to read from a character stream, apply a tokenization
mechanism based on its behavior, and produce a new sequence of a token
stream. 

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec69}What is a tokenizer? {#what-is-a-tokenizer .title}

</div>

</div>
:::

A tokenizer is a tool provided[]{#id288377239 .indexterm} by Solr that
runs a tokenization process, breaks a stream of text into tokens at some
delimiter, and generates a token stream. Tokenizers are configured by
their Java implementation factory class in the
`managed-schema.xml`{.literal} file. For example, we can define a
standard tokenizer as:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<tokenizer class="solr.StandardTokenizerFactory"/>
```
:::

Most tokenizer implementation classes do not provide a default no-arg
constructor. So, always use a tokenizer factory class instead of a
tokenizer class. Solr provides a standard way to define tokenizers in
XML format using factory implementation classes. These factory classes
translate XML configurations to create an instance of the respective
tokenizer implementation class. An analyzer may contain only tokenizers
or both tokenizers and filters. If only tokenizers are configured, the
output produced from tokenization is ready to use; otherwise, the output
will be passed to the first filter in the list. 

After tokenization, a new token stream is generated. The newly generated
token, along with its normalized text values, also holds some metadata,
such as the location at which they are generated. Token metadata is
important for things like highlighting search results in the field text.
A newly generated token contains a value that may be different from its
input text value. We can\'t assume that the text of a token is the same
as the input text, or that the length is the same. It's also possible
for more than one token to have the same position or refer to the same
set in the original text.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec70}Available tokenizers in Solr {#available-tokenizers-in-solr .title}

</div>

</div>
:::

Solr provides a large number[]{#id288340779 .indexterm} of tokenizers.
Let\'s explore the behavior of some of them.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec14}Standard tokenizer {#standard-tokenizer .title}

</div>

</div>
:::

This splits the text field[]{#id288340794 .indexterm} into tokens,
treating white space and punctuation as delimiters. It considers white
spaces and punctuation (comma, dots, hyphens, semicolons, colons,
hashtags, and @ ) as delimiters and discards all of them, with these
exceptions:

::: {.itemizedlist}
-   Dots that are not followed by white spaces are kept as part of the
    token. An example is internet domains such as
    `www.google.com`{.literal}.
-   Factory class---`solr.StandardTokenizerFactory`{.literal}.
-   Arguments---`maxTokenLength`{.literal} (integer, default 255) The
    max length of token characters. Tokens that exceed the number of
    characters specified by `maxTokenLength`{.literal} will be ignored.
:::

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 </analyzer>
</fieldType>
```
:::

[**Input**]{.strong}: `Please send a mail at dharmesh.vasoya@example.com by 12-11.`{.literal}

[**Output**]{.strong}: `Please`{.literal}, `send`{.literal}, `a`{.literal}, `mail`{.literal}, `at`{.literal}, `dharmesh.vasoya`{.literal}, `example.com`{.literal}, `by`{.literal}, `12`{.literal}, `11`{.literal}

A total of 10 tokens have been generated by the standard tokenizer.
These will be passed to its immediate successor in the chain. Standard
tokenizer supports Unicode standard annex
UAX\#29, <http://unicode.org/reports/tr29/#Word_Boundaries> word
boundaries with the following token types:
`<ALPHANUM>`{.literal}, `<NUM>`{.literal},
`<SOUTHEAST_ASIAN>`{.literal}, `<IDEOGRAPHIC>`{.literal}, and
`<HIRAGANA>`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec15}White space tokenizer {#white-space-tokenizer .title}

</div>

</div>
:::

This splits the text stream[]{#id288340162 .indexterm} at white spaces
only. However, it will not split the text at any punctuation (like the
standard tokenizer). Therefore, all of the punctuation will remain as is
inside the generated tokens.

[**Factory
class**]{.strong}:[** **]{.strong}`solr.WhitespaceTokenizerFactory`{.literal}

[**Arguments**]{.strong}:

::: {.itemizedlist}
-   `rule`{.literal}[** **]{.strong}(default: `java`{.literal}): A rule
    that considers white space as a delimiter.
-   `java`{.literal}: Uses `Character.isWhitespace(int)`{.literal}
    (<https://docs.oracle.com/javase/8/docs/api/java/lang/Character.html#isWhitespace-int->)
-   `unicode`{.literal}: Uses unicode\'s `WHITESPACE`{.literal} property
:::

[**Example:**]{.strong}

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.WhitespaceTokenizerFactory" rule="java" />
 </analyzer>
</fieldType>
```
:::

[**Input**]{.strong}:
`Please send a mail at dharmesh.vasoya@example.com by 12-11.`{.literal}

[**Output**]{.strong}:[** **]{.strong}`Please`{.literal}, `send`{.literal}, `a`{.literal}, `mail`{.literal}, `at`{.literal}, `dharmesh.vasoya@example.com`{.literal}, `by`{.literal}, `12-11.`{.literal}

The input string was split at white spaces but the punctuation
(`@`{.literal}, `.`{.literal},and `-`{.literal}) was preserved in the
tokens.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec16}Classic tokenizer {#classic-tokenizer .title}

</div>

</div>
:::

This splits the text field into tokens[]{#id288341349 .indexterm} at
white spaces and punctuation. The classic tokenizer behaves in the same
way as the standard tokenizer of Solr versions 3.1 and older. Like The
standard tokenizer, it does not use the Unicode standard annex UAX\#29
word boundary rules. Delimiter characters are discarded, with the
following exceptions:

::: {.itemizedlist}
-   Dots that are not followed by white spaces are kept as part of the
    token
-   Words are split at hyphens unless there is a number in the word, in
    which case the token is not split and the numbers and hyphens are
    preserved
-   It preserves internet domain names and email addresses as a single
    token
:::

[**Factory
class**]{.strong}:[** **]{.strong}`solr.ClassicTokenizerFactory`{.literal}

[**Arguments**]{.strong}: `maxTokenLength`{.literal} (integer, default
255): Max length of the token characters. Tokens that exceed the number
of characters specified by `maxTokenLength`{.literal} will be ignored.

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
    <analyzer>
      <tokenizer class="solr.ClassicTokenizerFactory"/>
    </analyzer>
  </fieldType>
```
:::

[**Input**]{.strong}:[** **]{.strong}`Please send a mail at dharmesh.vasoya@example.com by 12-Nov.`{.literal}

[**Output**]{.strong}:[** **]{.strong}`Please`{.literal}, `send`{.literal}, `a`{.literal}, `mail`{.literal}, `at`{.literal}, `dharmesh.vasoya@example.com`{.literal}, `by`{.literal}, `12-Nov`{.literal}

The input string is split at white spaces and punctuation, but the email
address `dharmesh.vasoya@example.com`{.literal} and
`12-Nov`{.literal} are preserved as part of the token.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec17}Keyword tokenizer {#keyword-tokenizer .title}

</div>

</div>
:::

This treats the entire text field[]{#id288341460 .indexterm} as a single
token. The kyword tokenizer is required in scenarios where we want to
match the entire string as it is:

[**Factory
class**]{.strong}:[** **]{.strong}`solr.KeywordTokenizerFactory`{.literal}

[**Arguments**]{.strong}: None

[**Example**]{.strong}: 

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.KeywordTokenizerFactory"/>
 </analyzer>
</fieldType>
```
:::

[**Input: **]{.strong}`Please send a mail at dharmesh.vasoya@example.com by 12-Nov.`{.literal}

[**Output:**]{.strong}`Please send a mail at dharmesh.vasoya@example.com by 12-Nov.`{.literal}

The entire input string is preserved as a single token.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec18}Lower case tokenizer {#lower-case-tokenizer .title}

</div>

</div>
:::

The lower case tokenizer considers[]{#id288341552 .indexterm} white
spaces and non-letters as delimiters, splits the input string at these
delimiters, and then discards all delimiters. Finally, it converts all
letters to lowercase.

[**Factory
class**]{.strong}:[** **]{.strong}`solr.LowerCaseTokenizerFactory`{.literal}

[**Arguments**]{.strong}: None

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.LowerCaseTokenizerFactory"/>
 </analyzer>
</fieldType>
```
:::

[**Input**]{.strong}:
`Please send a mail at dharmesh.vasoya@example.com by 12-Nov.`{.literal}

[**Output**]{.strong}: `please`{.literal}, `send`{.literal}, `a`{.literal}, `mail`{.literal}, `at`{.literal}, `dharmesh`{.literal}, `vasoya`{.literal}, `example`{.literal}, `com`{.literal}, `by`{.literal}, `nov`{.literal}

The input string was first split at white spaces and punctuation and
then converted to lowercase.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec19}Letter tokenizer {#letter-tokenizer .title}

</div>

</div>
:::

The letter tokenizer discards[]{#id288341643 .indexterm} all non-letter
characters from the input string and then generates a token at strings
of contiguous letters.

[**Factory class**]{.strong}: `solr.LetterTokenizerFactory`{.literal}

[**Arguments**]{.strong}: None

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.LetterTokenizerFactory"/>
 </analyzer>
</fieldType>
```
:::

[**Input**]{.strong}:[** **]{.strong}`I haven't received mail by Nov12Sunday`{.literal}

[**Output**]{.strong}:[** **]{.strong}`I`{.literal}, `haven`{.literal}, `t`{.literal}, `received`{.literal}, `mail`{.literal}, `by`{.literal}, `Nov`{.literal}, `Sunday`{.literal}

All non-letter characters (`'`{.literal} and `12`{.literal}) are
discarded first, and then tokens are generated by considering strings of
contiguous letters.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec20}N-gram tokenizer {#n-gram-tokenizer .title}

</div>

</div>
:::

This generates n-gram tokens[]{#id288340369 .indexterm} of sizes in the
provided range from the input string.

[**Factory class**]{.strong}: `solr.NGramTokenizerFactory`{.literal}

[**Arguments**]{.strong}:[** **]{.strong}`minGramSize (integer, default 1)`{.literal}:
The minimum n-gram size.`maxGramSize (integer, default 2)`{.literal}:
The maximum n-gram size

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note27}Note {#note .title}

The following condition must be fulfilled when providing gram-size
arguments:`0 < minGramSize <= maxGramSize`{.literal}
:::

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.NGramTokenizerFactory" minGramSize="2" maxGramSize="3"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `send me`{.literal}

[**Output**]{.strong}:[** **]{.strong}`se`{.literal}, `sen`{.literal}, `en`{.literal}, `end`{.literal}, `nd`{.literal}, `nd`{.literal}, `d`{.literal}, `dm`{.literal}, `m`{.literal}, `me`{.literal}, `me`{.literal}

N-gram tokenizer executes tokenization over the entire input string.
Also, it does not consider white spaces as delimiters, so white space
characters are also included in the tokenization. In the preceding
example, white spaces are preserved as parts of the token after
tokenization. The n-gram tokenizer is required in cases where we want to
match search words from the start, end, or somewhere in between the
string along with white spaces.

For example, the input string is
`Please send me a mail at dharmesh.vasoya@example.com`{.literal} and we
want to match `mail at dharmesh.vasoya@example.com`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec21}Edge n-gram tokenizer {#edge-n-gram-tokenizer .title}

</div>

</div>
:::

This generates n-gram tokens[]{#id288340486 .indexterm} from the start
over the entire input string. Like the n-gram tokenizer, the edge n-gram
tokenizer also does not consider white space as a delimiter, so white
space is also considered during tokenization.

[**Factory
class**]{.strong}:[** **]{.strong}`solr.EdgeNGramTokenizerFactory`{.literal}

[**Arguments**]{.strong}:

::: {.itemizedlist}
-   `minGramSize`{.literal} (integer, default is `1`{.literal}): The
    minimum n-gram size
-   `maxGramSize`{.literal} (integer, default is `1`{.literal}): The
    maximum n-gram size (`0 < minGramSize <= maxGramSize`{.literal})
:::

In earlier versions, Solr supported an argument side
(`front`{.literal} or `back`{.literal}; the default was
`front`{.literal}), which generated a token from the provided value.
This argument has now been removed and Solr generates the token from the
front end of the input string.

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.EdgeNGramTokenizerFactory" minGramSize="2" maxGramSize="10"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `send me`{.literal}

[**Output**]{.strong}:[** **]{.strong}`se`{.literal}, `sen`{.literal}, `send`{.literal}, `send`{.literal}, `send m`{.literal}, `send me`{.literal}

The entire input string is split into n-gram pattern tokens considering
size parameters (`minGramSize`{.literal} (2) and `maxGramSize`{.literal}
(10)) along with white spaces. The edge n-gram tokenizer is required for
matching n-characters from the start of the string.

For example, the input string is
`Please send me a mail at dharmesh.vasoya@example.com`{.literal} and we
want to match `Please send me a mail`{.literal} but it will not match. 



[]{#ch04lvl1sec31}Understanding filters {#understanding-filters .title style="clear: both"}
---------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

We have seen that the analyzer[]{#id288339943 .indexterm} uses a series
of tokenizer and filter classes together to transform the input string
into a token string, which will be used by Solr in indexing. The job of
the filter is different from the tokenizer. The tokenizer mostly splits
the input string at some delimiters and generates a token stream. The
filter transforms this stream into some other form and generates a new
token stream. The input for a filter will be a token stream, not an
input string, unlike what we were passing at the time of tokenization.
The entire token stream generated through tokenization will be passed to
the first filter class in the list. Let\'s cover filters in detail.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec71}What is a filter? {#what-is-a-filter .title}

</div>

</div>
:::

A filter is a tool provided[]{#id288339958 .indexterm} by Solr that runs
a filtering process as follows:

::: {.itemizedlist}
-   [**Adding**]{.strong}: Adds a new token to the stream, such
    as adding synonyms
-   [**Removing**]{.strong}: Removes a token from the stream, such as
    stop words
-   [**Conversation**]{.strong}: Converts a token from one form to
    another form, say uppercase to lower case
:::

When a token stream generated during tokenization is passed to the
filter, normally the filter looks at each token sequentially and, as per
their behavior, performs one of the preceding activities. It then
produces a new token stream. Filters are also derived
from `org.apache.lucene.analysis.TokenStream`{.literal}, so the output
of a filter is also a token stream.

We can define a filter by its Java implementation factory class in the
`managed-schema.xml`{.literal} file:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<filter class="solr.StandardFilterFactory"/>
```
:::

A filter definition should follow a tokenizer or another filter
definition because they take a token stream as input. We can configure
filters as a child element of `<analyzer>`{.literal} following the
`<tokenizer>`{.literal} elements. Here is a simple example configured in
the `managed-schema.xml`{.literal} file inside the field
`text_en`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.StandardFilterFactory"/>
 <filter class="solr.LowerCaseFilterFactory"/>
 </analyzer>
 </fieldType>
```
:::

The preceding example is very simple. Solr comes with a large number of
filters to meet most of our search requirements. Let\'s understand them
in detail.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec72}Available filters in Solr {#available-filters-in-solr .title}

</div>

</div>
:::

Solr provides the[]{#id288340042 .indexterm} following filters. Let\'s
explore their behavior.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec22}Stop filter {#stop-filter .title}

</div>

</div>
:::

This removes all the[]{#id288340057 .indexterm} words listed inside the
`stopwords.txt`{.literal} file. Removing stop words will reduce the size
of the index and improve performance. These are the standard English
stop words provided by Solr:

[*a*]{.emphasis}, [*an*]{.emphasis}, [*and*]{.emphasis},
[*are*]{.emphasis}, [*ask*]{.emphasis}, [*at*]{.emphasis},
[*be*]{.emphasis}, [*but*]{.emphasis}, [*by*]{.emphasis},
[*for*]{.emphasis}, [*if*]{.emphasis}, [*in*]{.emphasis},
[*into*]{.emphasis}, [*is*]{.emphasis}, [*it*]{.emphasis},
[*no*]{.emphasis}, [*not*]{.emphasis}, [*of*]{.emphasis},
[*on*]{.emphasis}, [*or*]{.emphasis}, [*such*]{.emphasis},
[*that*]{.emphasis}, [*the*]{.emphasis}, [*their*]{.emphasis},
[*then*]{.emphasis}, [*there*]{.emphasis}, [*these*]{.emphasis},
[*they*]{.emphasis}, [*this*]{.emphasis}, [*to*]{.emphasis},
[*was*]{.emphasis}, [*will*]{.emphasis}, [*with*]{.emphasis}.

We can manage (add or remove) words from the file as per our
requirement. We can also create a file for other languages and include
it by mentioning the file path in the word argument:

[**Factory
class**]{.strong}:[** **]{.strong}`solr.StopFilterFactory`{.literal}

[**Arguments**]{.strong}:

::: {.itemizedlist}
-   `words`{.literal} (optional): The path of the file that contains a
    list of stop words, one per line. Blank lines and lines that begin
    with `#`{.literal} will be ignored from the file. The path may be an
    absolute or relative path.
-   `format`{.literal} (optional): Indicates the format of the stopword
    list, for example, `format="snowball"`{.literal} for a stopwords
    list that has been formatted for snowball.
-   `ignoreCase`{.literal} (true/false, default false): This ignores
    casing when testing for stop words. If it is `true`{.literal}, the
    stop list should contain lowercase words.
:::

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.StopFilterFactory" words="lang/stopwords_en.txt" />
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `This is an example`{.literal}

[**Tokenizer to
filter**]{.strong}:[** **]{.strong}`This`{.literal}, `is`{.literal}, `an`{.literal}, `example`{.literal}

[**Output**]{.strong}:[** **]{.strong}`This`{.literal}, `example`{.literal}

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}:[** **]{.strong}`This is an example`{.literal}

[**Tokenizer to
filter**]{.strong}:[** **]{.strong}`This`{.literal}, `is`{.literal}, `an`{.literal}, `example`{.literal}

[**Output**]{.strong}:[** **]{.strong}`example`{.literal}

In the first example, we have not specified the argument
`ignoreCase`{.literal}, but in the second example, we have set it to
`true`{.literal}; so the outputs from both the examples are different.
The location of the file `stopwords_en.txt`{.literal} is
`%SOLR_HOME%/example/techproducts/solr/techproducts/conf/lang/stopwords_en.txt`{.literal},
as we currently understand from Solr\'s built-in example
`techproducts`{.literal}.

[**LCF**]{.strong}: Converts all uppercase letters to lowercase in the
token

[**Factory
class**]{.strong}:[** **]{.strong}`solr.LowerCaseFilterFactory`{.literal}

[**Arguments**]{.strong}: None

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.LowerCaseFilterFactory"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `This is An example`{.literal}

[**Tokenizer to
Filter**]{.strong}: `This`{.literal}, `is`{.literal}, `An`{.literal}, `example`{.literal}

[**Output**]{.strong}: `this`{.literal}, `is`{.literal}, `an`{.literal}, `example`{.literal}

All uppercase letters from the tokens are converted to lowercase. The
sequence order of `LowerCaseFilterFactory`{.literal} in the filter chain
should be significant. If we define LCF before stop filter, Solr
will unnecessarily apply lower case filtering on those stop words that
are going to be removed in the next step.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip28}Note {#note .title}

If we need to use LCF in text analysis, then must apply LCF to both the
phases of an analyzer (index and query). If we define LCF only in the
indexing phase and not in the querying phase, a query for
`Soccer`{.literal} will never match with the indexed term
`soccer`{.literal}.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec23}Classic filter {#classic-filter .title}

</div>

</div>
:::

The classic filter is used with the[]{#id288617288 .indexterm} classic
tokenizer. It removes dots from acronyms and `'s`{.literal} from
possessives.

[**Factory class**]{.strong}: `solr.ClassicFilterFactory`{.literal}

[**Arguments**]{.strong}: None

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.ClassicTokenizerFactory"/>
 <filter class="solr.ClassicFilterFactory"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}:[** **]{.strong}`Computer's C.P.U. isn't`{.literal}

[**Tokenizer to
filter**]{.strong}: `Computer's`{.literal}, `C.P.U.`{.literal}, `isn't`{.literal}

[**Output**]{.strong}:[** **]{.strong}`Computer`{.literal}, `CPU`{.literal}, `isn't`{.literal}

The classic tokenizer and classic filter together converted
`Computer's`{.literal} to `Computer`{.literal} by removing
`'s`{.literal} and reduced `C.P.U.`{.literal} to `CPU`{.literal} by
removing the dots.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec24}Synonym filter {#synonym-filter .title}

</div>

</div>
:::

[** **]{.strong}During filtering, the synonym filter looks for
synonymous words in the `synonyms.txt`{.literal} file. The found
synonyms will be added at the place of the original token. All
the[]{#id288626540 .indexterm} synonym mappings are configured inside
the `synonyms.txt`{.literal} file.

[**Factory class**]{.strong}: `solr.SynonymFilterFactory`{.literal}

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note29}Note {#note-1 .title}

The synonym filter is now deprecated in Solr as it does not
support multi-term synonym mapping. Solr provides a synonym graph filter
as an alternative to the synonym filter with multi-term support.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec25}Synonym graph filter {#synonym-graph-filter .title}

</div>

</div>
:::

The synonym graph filter supports[]{#id288628859 .indexterm} single- or
multi-token synonyms. The filter maps single- or multi-token synonyms
and generates a correct token, which was not supported by the synonym
filter.

The synonym graph filter is normally configured at query time, not index
time. This will reduce the size of the index. If this filter is
configured at index time, after adding any new synonyms to the
`synonyms.txt`{.literal} file, re-indexing of entire documents is
required. The synonym graph filter configuration at query time does not
require re-indexing for adding new synonyms to `synonyms.txt`{.literal}.
To configure this filter at index time, we must mention the flatten
graph filter for treating tokens like the synonym filter.

Also, the configuration order for this filter is important. If we are
configuring the synonym graph filter before the ASCII folding filter,
then we need to maintain all diacritical words (like `caffé`{.literal})
in `synonyms.txt`{.literal} as well:

[**Factory class**]{.strong}: `solr.SynonymGraphFilterFactory`{.literal}

[**Arguments**]{.strong}:

::: {.itemizedlist}
-   `synonyms`{.literal} (required): The path of a file
    (`synonyms.txt`{.literal}) that contains a list of synonyms, one per
    line. Blank lines and lines that begin with `#`{.literal} are
    ignored. This may be a comma-separated list of absolute paths, or
    paths relative to the Solr config directory.
:::

Sample format of `synonyms.txt`{.literal}:

A comma-separated list of words. If the token matches any of the words,
then all the words in the list are substituted, which will include the
original token.

For example:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
football,soccer
dumb,stupid,dull
```
:::

Two comma-separated lists of words with the symbol
`=>`{.literal} between them. If the token matches any word on the left,
then the list on the right is substituted. The original token will not
be included unless it is also in the list on the right.

For example:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
country => nation
smart,clever,bright => intelligent,genius
```
:::

::: {.itemizedlist}
-   `ignoreCase`{.literal} (optional; default: `false`{.literal}): This
    determines the behavior of the filter in case-sensitive or case
    insensitive matching from the file. If it is `true`{.literal},
    synonyms will be matched case insensitively.
-   `expand`{.literal} (optional; default: `true`{.literal}): If this is
    set to `true`{.literal}, a synonym will be expanded to all
    equivalent synonyms. If `false`{.literal}, all equivalent synonyms
    will be reduced to the first in the list.
-   `format`{.literal} (optional; default: `solr`{.literal}): Controls
    how the synonyms will be parsed. Supported formats are:
    ::: {.itemizedlist}
    -   `solr`{.literal} (`SolrSynonymParser`{.literal})
    -   `wordnet`{.literal} (`WordnetSynonymParser`{.literal})
    -   We can pass the name of our own `SynonymMap.Builder`{.literal}
        subclass.
    :::
-   `tokenizerFactory`{.literal}: The name of the tokenizer factory to
    use when parsing the synonyms file. If `tokenizerFactory`{.literal}
    is specified, then analyzer may not be, and vice versa.
-   `analyzer`{.literal} (optional; default:
    `WhitespaceTokenizerFactory`{.literal}): The name of the analyzer
    class to use when parsing the synonyms file. If the analyzer is
    specified, then `tokenizerFactory`{.literal} may not be, and vice
    versa.
:::

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.SynonymGraphFilterFactory" synonyms="synonyms.txt" ignoreCase="true"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `He is stupid, not clever`{.literal}

[**Tokenizer to
filter**]{.strong}:[** **]{.strong}`He`{.literal}, `is`{.literal}, `stupid`{.literal}, `not`{.literal}, `clever`{.literal}

[**Output**]{.strong}: `He`{.literal}, `is`{.literal}, `dumb`{.literal}, `dull`{.literal}, `stupid`{.literal}, `not`{.literal}, `intelligent`{.literal},`genius`{.literal}

All the matching synonyms (from `synonyms.txt`{.literal}) are added to
the token stream.

If we want to apply the synonym graph filter at index time, we must
define `FlattenGraphFilterFactory`{.literal} in an analyzer definition
index.

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer type="index">
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.SynonymGraphFilterFactory" synonyms="synonyms.txt" ignoreCase="true"/>
 <!-- required on index analyzers after synonym graph filters -->
 <filter class="solr.FlattenGraphFilterFactory"/> 
 </analyzer>
 <analyzer type="query">
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.SynonymGraphFilterFactory" synonyms="synonyms.txt" ignoreCase="true"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}:[** **]{.strong}`He is stupid, not clever`{.literal}

[**Tokenizer to
Filter**]{.strong}: `He`{.literal}, `is`{.literal}, `stupid`{.literal}, `not`{.literal}, `clever`{.literal}

[**Output**]{.strong}:[** **]{.strong}`He`{.literal}, `is`{.literal}, `dumb`{.literal}, `dull`{.literal}, `stupid`{.literal}, `not`{.literal}, `intelligent`{.literal}, `genius`{.literal}

All the matching synonyms (from `synonyms.txt`{.literal}) are added to
the token stream.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec26}ASCII folding filter {#ascii-folding-filter .title}

</div>

</div>
:::

You can transform alphabetic, numeric, and symbolic Unicode
characters[]{#id288378284 .indexterm} that are not in the Basic Latin
Unicode block (the first 127 ASCII characters) into their ASCII
equivalents, if one exists.

[**Factory class**]{.strong}: `solr.ASCIIFoldingFilterFactory`{.literal}

[**Arguments**]{.strong}:

::: {.itemizedlist}
-   `preserveOriginal`{.literal}: A Boolean. The default is
    `false`{.literal}. If `true`{.literal}, the original token is
    preserved (`caffé`{.literal} \--\>
    `caffé`{.literal}, `cafe`{.literal}).
:::

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.WhitespaceTokenizerFactory"/>
 <filter class="solr.ASCIIFoldingFilterFactory" preserveOriginal="false" />
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `thé caffé`{.literal}

[**Tokenizer to
filter**]{.strong}:[** **]{.strong}`thé`{.literal}, `caffé`{.literal}

[**Output**]{.strong}:[** **]{.strong}`the`{.literal}, `caffe`{.literal}
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec27}Keep word filter {#keep-word-filter .title}

</div>

</div>
:::

We keep only those tokens that[]{#id288378384 .indexterm} are listed in
the `keepwords.txt`{.literal} files. This is the inverse of the stop
words filter.

[**Factory class**]{.strong}: `solr.KeepWordFilterFactory`{.literal}

[**Arguments**]{.strong}:

::: {.itemizedlist}
-   `words`{.literal}: Required. This is the path of a text file
    containing the list of keep words, one per line. Blank lines and
    lines that begin with `#`{.literal} are ignored. This may be an
    absolute path or a simple filename.
-   `ignoreCase`{.literal}: `True`{.literal} or `false`{.literal}. The
    default is `false`{.literal}. If it is `true`{.literal}, then
    comparisons are done case insensitively and the word file is assumed
    to contain only lowercase words.
:::

The following is the sample `keepwords.txt`{.literal} file:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
good
great
excellent
```
:::

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.KeepWordFilterFactory" words="keepwords.txt" ignoreCase="true"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `Good and excellent job`{.literal}

[**Tokenizer to
filter**]{.strong}:[** **]{.strong}`Good`{.literal}, `and`{.literal}, `excellent`{.literal}, `job`{.literal}

[**Output**]{.strong}:[** **]{.strong}`Good`{.literal}, `excellent`{.literal}

Here, we have set `ignoreCase="true"`{.literal} to match the words from
the `keepwords.txt`{.literal} file case insensitively. The patch of
`keepwords.txt`{.literal} is
`%SOLR_HOME%/example/techproducts/solr/techproducts/conf/keepwords.txt`{.literal}.
We can configure a file patch as relative or absolute as per our needs.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec28}KStem filter {#kstem-filter .title}

</div>

</div>
:::

Solr provides a stemming mechanism though[]{#id288378521 .indexterm}
which words are converted to their base form by applying
language-specific rules. For that, Solr provides a number of stemming
filters. The KStem filter is an English-specific and less aggressive
stemmer.

[**Factory class**]{.strong}: `solr.KStemFilterFactory`{.literal}

[**Arguments**]{.strong}: None

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.KStemFilterFactory"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `remove removing removed`{.literal}

[**Tokenizer to filter**]{.strong}:[** **]{.strong}`remove`{.literal},
`removing`{.literal}, `removed`{.literal}

[**Output**]{.strong}: `remove`{.literal}

From the input string, the KStem filter has transformed three forms of a
word to their base form `remove`{.literal}. The first word
`remove`{.literal} was kept as it is because it is already in its base
form, the second word `removing`{.literal} was transformed to
`remove`{.literal} by cropping `ing`{.literal} and the third word
`removed`{.literal} was transformed to `remove`{.literal} by cropping
`d`{.literal}. Solr provides a number of stemmer filters. Selecting
these filters completely depends on their behavior and which language we
are going to use them for. `PorterStemmer`{.literal} is one of the
popular stemmers. What if we do not want to modify some words by
stemming? We configure `KeywordMarkerFilterFactory`{.literal}
before `KStemFilterFactory`{.literal}. We will cover this soon.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec29}KeywordMarkerFilterFactory {#keywordmarkerfilterfactory .title}

</div>

</div>
:::

 This discards the modification/stemming of words[]{#id288378630
.indexterm} listed in the file `protwords.txt`{.literal}. Any words in
the protected word list will not be modified by any stemmer in Solr.

[**Arguments**]{.strong}:

::: {.itemizedlist}
-   `protected`{.literal}: The path of the file that contains the
    protected word list, one per line
:::

The following is the sample `protwords.txt`{.literal} file:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
removing
transforming
```
:::

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.WhitespaceTokenizerFactory"/>
 <filter class="solr.KeywordMarkerFilterFactory" protected="protwords.txt" />
 <filter class="solr.KStemFilterFactory"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `remove removing removed transforming`{.literal}

[**Tokenizer to filter**]{.strong}:
`remove`{.literal}, `removing`{.literal}, `removed`{.literal}, `transforming`{.literal}

[**Output**]{.strong}: `remove`{.literal}, `removing`{.literal}, `remove`{.literal}, `transforming`{.literal}

Here we can see that the words `removing`{.literal} and
`transforming`{.literal} are not stemmed by
`KStemFilterFactory`{.literal} because they are mentioned in the
file `protwords.txt`{.literal} and protected
by `KeywordMarkerFilterFactory`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec30}Word delimiter graph filter  {#word-delimiter-graph-filter .title}

</div>

</div>
:::

[** **]{.strong}This filter splits tokens[]{#id288377312 .indexterm} at
word delimiters. This is an alternative to the word delimiter filter.
Always use a word delimiter graph filter at query time and not at index
time because the indexer can't directly consume a graph at index
time; if you still need to use this filter at index time, use it with
a flatten graph filter.

The rules for determining delimiters are as follows:

::: {.itemizedlist}
-   A change in case within a word: `KnowMore`{.literal} -\>
    `Know`{.literal}, `More`{.literal}. This can be disabled by setting
    `splitOnCaseChange="0"`{.literal}.
-   A transition from alpha to numeric characters or vice versa:
    `Alpha1000`{.literal} -\> `Alpha`{.literal},
    `1000`{.literal} `100MS`{.literal} -\> `100`{.literal},
    `MS`{.literal}. This can be disabled by setting
    `splitOnNumerics="0"`{.literal}.
-   Non-alphanumeric characters are discarded: `air-crew`{.literal} -\>
    `air`{.literal}, `crew`{.literal}.
-   A trailing `'s`{.literal} is removed: `Solr's`{.literal} -\>
    `Solr`{.literal}.
-   Any leading or trailing delimiters are discarded:
    `-air-crew!!`{.literal} -\> `air`{.literal}, `crew`{.literal}.
:::

[**Factory class**]{.strong}:
`solr.WordDelimiterGraphFilterFactory`{.literal}

[**Arguments**]{.strong}: It\'s not possible to list all the arguments
here. Please refer to the Solr document for these.

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer type="index">
 <tokenizer class="solr.WhitespaceTokenizerFactory"/>
 <filter class="solr.WordDelimiterGraphFilterFactory"/>
 <!-- required on index analyzers after graph filters -->
 <filter class="solr.FlattenGraphFilterFactory"/> 
 </analyzer>
 <analyzer type="query">
 <tokenizer class="solr.WhitespaceTokenizerFactory"/>
 <filter class="solr.WordDelimiterGraphFilterFactory"/>
 </analyzer>
</fieldType>
```
:::

[**Input**]{.strong}: `KnowMore air-crew Alpha1000`{.literal}

[**Tokenizer to filter**]{.strong}:
`KnowMore`{.literal}, `air-crew`{.literal}, `Alpha1000`{.literal}

[**Output**]{.strong}: `Know`{.literal}, `More`{.literal}, `air`{.literal}, `crew`{.literal}, `Alpha`{.literal}, `1000`{.literal}

This is a simple example of a word delimiter graph filter. However, we
can play with this filter by applying much more complex filtering terms.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec73}Understanding CharFilter {#understanding-charfilter .title}

</div>

</div>
:::

During an analysis, the analyzer passes[]{#id288377478 .indexterm} the
input string to the first tokenizer in the list. If we want to apply any
preprocessing to the input string before[]{#id288377488 .indexterm}
passing to the tokenizer, we can do it through
`CharFilter`{.literal}. `CharFilters`{.literal} can be chained like
token filters and placed in front of a tokenizer to add, change, or
remove characters from an input string. Here is a list of char filters
provided by Solr:

::: {.itemizedlist}
-   `solr.MappingCharFilterFactory`{.literal}
-   `solr.HTMLStripCharFilterFactory`{.literal}
-   `solr.ICUNormalizer2CharFilterFactory`{.literal}
-   `solr.PatternReplaceCharFilterFactor`{.literal}
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec31}Understanding PatternReplaceCharFilterFactor {#understanding-patternreplacecharfilterfactor .title}

</div>

</div>
:::

This filter uses regular expressions to replace[]{#id288377526
.indexterm} or change character patterns.

[**Arguments**]{.strong}:

::: {.itemizedlist}
-   `pattern`{.literal}: The regular expression pattern to apply to the
    incoming text
-   `replacement`{.literal}: The text to use to replace matching
    patterns
:::

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <charFilter class="solr.PatternReplaceCharFilterFactory" pattern="(\w+)(ing)" replacement="$1"/>
 <tokenizer class="solr.WhitespaceTokenizerFactory"/>
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `showing see-ing viewing`{.literal}

[**Output**]{.strong}: `show`{.literal}, `see-ing`{.literal}, `view`{.literal}

As per the behavior of the pattern, `ing`{.literal} is removed from the
end of the words except `see-ing`{.literal}.

Explaining every char filter is not possible here. Please refer to the
Solr documents for these.



[]{#ch04lvl1sec32}Understanding multilingual analysis {#understanding-multilingual-analysis .title style="clear: both"}
-----------------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

So far, we have concentrated[]{#id288339995 .indexterm} on Solr
text analysis (analyzers, tokenizers, and filters) irrespective of any
language. Solr support multiple language search and this feature puts
Solr at the top of the list of search engines. Let\'s understand how
Solr works for multiple language search.

So far all the examples we have covered are in English. The tokenization
and filtering rules for English are very simple and straightforward,
such as splitting at white spaces or any other delimiters, stemming, and
so on. But once we start focusing on other languages, these rules may
differ. Solr is already prepared to meet multiple analysis search
requirements such as stemmers, synonyms filters, stop word filters,
character query correction capabilities normalization, language
identifiers, and so on. Some languages require their own tokenizers
for complexity of parsing the language, some require their own stemming
filters, and some require multiple filters as per the language
characteristics. For example, here is an analyzer configured for Greek
in `managed-schema.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_el" class="solr.TextField" positionIncrementGap="100">
 <analyzer> 
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <!-- greek specific lowercase for sigma -->
 <filter class="solr.GreekLowerCaseFilterFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="false" words="lang/stopwords_el.txt" />
 <filter class="solr.GreekStemFilterFactory"/>
 </analyzer>
 </fieldType>
```
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec74}Language identification {#language-identification .title}

</div>

</div>
:::

At the time of indexing, language identification is
required[]{#id288341794 .indexterm} to map text to language-specific
fields. Solr uses the langid `UpdateRequestProcessor`{.literal} for
language identification. Two types of `UpdateRequestProcessor`{.literal}
are provided by Solr.

::: {.itemizedlist}
-   `TikaLanguageIdentifierUpdateProcessor`{.literal}: Uses the
    language identification libraries in Apache Tika
-   `LangDetectLanguageIdentifierUpdateProcessor`{.literal}: Uses the
    open source Language Detection Library for Java
:::

Configuration of `TikaLanguageIdentifierUpdateProcessor`{.literal}
in `solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<processor class="org.apache.solr.update.processor.TikaLanguageIdentifierUpdateProcessorFactory">
 <lst name="defaults">
 <str name="langid.fl">title,subject,text,keywords</str>
 <str name="langid.langField">language_s</str>
 </lst>
</processor>
```
:::

Configuration
of `LangDetectLanguageIdentifierUpdateProcessor`{.literal} in `solrconfig.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<processor class="org.apache.solr.update.processor.LangDetectLanguageIdentifierUpdateProcessorFactory">
 <lst name="defaults">
 <str name="langid.fl">title,subject,text,keywords</str>
 <str name="langid.langField">language_s</str>
 </lst>
</processor>
```
:::

`UpdateRequestProcessor`{.literal} provides many `langid`{.literal}
parameters. We are not going to explain them here. Determining the
language at index time is always preferable over query time. During
query time, the input provided by the user may be short and sometimes
not meaningful enough to extract language information. At index time,
full documents are present, so language identification becomes easier.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec75}Configuring Solr for multiple language search {#configuring-solr-for-multiple-language-search .title}

</div>

</div>
:::

There are mainly three[]{#id288376077 .indexterm} approaches to
configure Solr for multiple language search.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec32}Creating separate fields per language {#creating-separate-fields-per-language .title}

</div>

</div>
:::

This is a very simple and[]{#id288599640 .indexterm} straightforward
approach, done by creating a separate field per language. Create a per
language field in `managed-schema.xml`{.literal}.

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<!-- English -->
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_en.txt" />
 <filter class="solr.LowerCaseFilterFactory"/> 
 </analyzer>
</fieldType>
<!-- Greek -->
<fieldType name="text_el" class="solr.TextField" positionIncrementGap="100">
 <analyzer> 
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <!-- greek specific lowercase for sigma -->
 <filter class="solr.GreekLowerCaseFilterFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="false" words="lang/stopwords_el.txt" />
 <filter class="solr.GreekStemFilterFactory"/>
 </analyzer>
</fieldType>
<!-- Spanish -->
<fieldType name="text_es" class="solr.TextField" positionIncrementGap="100">
 <analyzer> 
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.LowerCaseFilterFactory"/>
 <filter class="solr.StopFilterFactory" ignoreCase="true" words="lang/stopwords_es.txt" format="snowball" />
 <filter class="solr.SpanishLightStemFilterFactory"/>
 <!-- more aggressive: <filter class="solr.SnowballPorterFilterFactory" language="Spanish"/> -->
 </analyzer>
</fieldType>
 ...
<field name="content_en" type="text_en" indexed="true" stored="true" />
<field name="content_el" type="text_el" indexed="true" stored="true" />
<field name="content_es" type="text_es" indexed="true" stored="true" />
```
:::

For creating separate fields per language, the DisMax-style query parser
is required, which makes it easy to search across multiple fields. Solr
provides built-in support for this query parser. Creating separate
fields per language is simple and straightforward and fulfills most of
the multi-language search requirements. The approach has a
disadvantage too. The index size may increase as we define separate
fields per language, and so queries may slow down.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch04lvl3sec33}Creating separate indexes per language {#creating-separate-indexes-per-language .title}

</div>

</div>
:::

In this approach, Solr creates a separate[]{#id288601076 .indexterm}
Solr index (Solr Core) per language. Solr supports the creation of
multiple cores. Every core contains a unique Solr index. Every core uses
separate configuration files, including the
`managed-schema.xml`{.literal} file. During searching, every Solr core
searches its own data from its own configuration file. After the search,
results from all cores are combined together and then returned as an
output of the query. Here is the simple configuration of defining a core
per language.

File `en_managed-schema.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<field name="content" type="text_en" indexed="true" stored="true" />
```
:::

File `el_managed-schema.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<field name="content" type="text_el" indexed="true" stored="true" />
```
:::

File `es_managed-schema.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<field name="content" type="text_es" indexed="true" stored="true" />
```
:::

File `solr.xml`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<cores>
 <core name="english" instanceDir="shared" dataDir="../cores/core-perlanguage/data/english/" schema="en_managed-schema.xml" />
 <core name="greek" instanceDir="shared" dataDir="../cores/core-perlanguage/data/greek/" schema="el_managed-schema.xml" />
 <core name="spanish" instanceDir="shared" dataDir="../cores/core-perlanguage/data/spanish" schema="es_managed-schema.xml" />
 <core name="aggregator" instanceDir="shared" dataDir="data/aggregator" />
</cores>
```
:::

During the query, a request is sent to each of the language-specific
cores using the shards parameter. Now the search will be independent and
parallel with other languages. This will improve the performance of
Solr. Here the field \"content\" is defined differently in each
language-specific core, the language analysis also executed as per the
configuration of that core. The search performance is better by defining
a core per language as the search executes in parallel across multiple
smaller indexes. As opposed to this, searching across a growing number
of fields in a much larger index (the language-per-field approach) will
hurt the performance. However, managing each core per language is
somehow difficult.

During multiple language search configuration, selecting the
implementation approach completely depends on the search requirements.
The separate fields per language approach suits cases where the index
size is in control. The separate indexes per language approach suits
cases where the former does not satisfy the requirements and we
have sufficient environment maintenance capabilities.


[]{#ch04lvl1sec33}Understanding phonetic matching {#understanding-phonetic-matching .title style="clear: both"}
-------------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Phonetic matching algorithms are used[]{#id288339943 .indexterm} to
match different spellings that are pronounced similarly by encoding
them. Some examples are `Sandeep`{.literal} and `Sandip`{.literal};
`Taylor`{.literal}, `Tailer`{.literal}, and `Tailor`{.literal}; and so
on. Solr provides several filters for phonetic matching.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch04lvl2sec76}Understanding Beider-Morse phonetic matching {#understanding-beider-morse-phonetic-matching .title}

</div>

</div>
:::

[**Beider-Morse Phonetic Matching**]{.strong} ([**BMPM**]{.strong})
helps you search[]{#id288339981 .indexterm} for personal
names[]{#id288340001 .indexterm} or surnames. It is a very intelligent
algorithm compared to soundex, metaphone, caverphone, and so on. Its
purpose is to match names that are phonetically equivalent to the
expected name. BMPM does not split spellings and does not generate false
hits. It extracts names that are phonetically equivalent.

It executes these steps to extract names that are phonetically
equivalent:

::: {.itemizedlist}
-   Determines the language from the spelling of the name
-   Applies phonetic rules to identify the language and translates the
    name into phonetic alphabets
-   In the case of a language not identified from the name, it applies
    generic phonetics
-   Finally, it applies language-independent rules regarding things
    such as voiced and unvoiced consonants and vowels to further ensure
    the reliability of the matches
:::

BMPM supports the following languages: English, French, German, Greek,
Hebrew written in Hebrew script, Hungarian, Italian, Polish, Romanian,
Russian written in Cyrillic script, Russian transliterated into Latin
script, Spanish, and Turkish.

[**Factory class**]{.strong}: `solr.BeiderMorseFilterFactory`{.literal}

[**Arguments**]{.strong}:

::: {.itemizedlist}
-   `nameType`{.literal}: Types of names. Valid values are
    `GENERIC`{.literal}, `ASHKENAZI`{.literal}, or
    `SEPHARDIC`{.literal}. If you are not processing Ashkenazi or
    Sephardic names, use `GENERIC`{.literal}.
-   `ruleType`{.literal}: The types of rules to apply. Valid values are
    `APPROX`{.literal} or `EXACT`{.literal}.
-   `concat`{.literal}: Defines whether multiple possible matches should
    be combined with a pipe (`|`{.literal}).
-   `languageSet`{.literal}: The language set to use. The value
    `auto`{.literal} will allow the filter to identify the language, or
    a comma-separated list can be provided.
:::

[**Example**]{.strong}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="text_en" class="solr.TextField" positionIncrementGap="100">
 <analyzer>
 <tokenizer class="solr.StandardTokenizerFactory"/>
 <filter class="solr.BeiderMorseFilterFactory" nameType="GENERIC" ruleType="APPROX" concat="true" languageSet="auto" />
 </analyzer>
 </fieldType>
```
:::

[**Input**]{.strong}: `sandeep`{.literal}

[**Tokenizer to filter**]{.strong}: `sandeep`{.literal}

[**Output**]{.strong}:[** **]{.strong}`sYndip`{.literal}, `sandDp`{.literal}, `sandi`{.literal}, `sandip`{.literal}, `sondDp`{.literal}, `sondi`{.literal}, `sondip`{.literal}, `zYndip`{.literal}, `zandip`{.literal},
`zondip`{.literal}

From the generated tokens, token `sandip`{.literal} is similar to our
expectations.

Similar to BMPB, Solr provides many more algorithms with unique
behavior for implementing phonetic matching. Following is the list of
those algorithms:

::: {.itemizedlist}
-   Daitch-Mokotoff soundex
-   Double metaphone
-   Metaphone
-   Soundex
-   Refined soundex
-   Caverphone
-   Kölner Phonetik also known as Cologne Phonetic
-   NYSIIS
:::

Explaining each algorithm[]{#id288622860 .indexterm} is not possible,
but we can understand[]{#id288622885 .indexterm} their behavior through
the Solr Admin console by configuring them in the
`managed-schema.xml`{.literal} file.



[]{#ch04lvl1sec34}Summary
-------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In this chapter, we saw an overview of text analysis, analyzers,
tokenizers, filters, and how to configure an analyzer along with
tokenizers and filters. We also saw the implementation approach for
putting tokenizers and filters together. Then we moved on to multiple
search. Here we explored how Solr determines a language, two approaches
to creating separate fields and separate indexes per language for
multiple-language search, and the pros and cons of each approach.
Finally, we understood Solr phonetic matching mechanics using the BMPM
algorithm.

In the next chapter, we will see how to do indexing using client API,
upload data using index handlers, upload data using Apache Tika with
Solr Cell, and detect languages while indexing.

