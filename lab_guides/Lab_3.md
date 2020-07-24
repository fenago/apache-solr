
[]{#ch03}Chapter 3. Designing Schemas {#chapter-3.-designing-schemas .title}
-------------------------------------

</div>

</div>
:::

Now that we have seen how to install Solr on our machine, let\'s dive
deeper and understand the nitty-gritty of Solr.

Assume that you are building a home that you have always desired. How
would you start? Will you just get all the bricks, cement, windows,
doors, beams, and so on and ask the builders to start building? Nope!
You would want to make sure you go through various designs based on the
area that you have and decide on a design that you think will not only
look good but also last long. Creating any application follows the same
principle and demands proper schema design.

In this chapter, we will traverse through schema design. We will
understand how to design a schema using documents and fields. We will
also see various field types and get an understanding of the Schema API.
We will finally look at schemaless mode.



[]{#ch03lvl1sec22}How Solr works {#how-solr-works .title style="clear: both"}
--------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

The easiest way to understand how Solr[]{#id288339996 .indexterm} works
is to see how a telephone directory helps you to look something up. A
telephone directory, or yellow pages as it is called in some places, is
a book containing lots of phone numbers. It has lots of pages. Now, to
find information in it would be a humongous task unless it had some sort
of indexing and categorizing. For example, we can easily find all the
restaurants by just navigating to the category of restaurants and
finding the locality that we are living nearby.

Similarly, Solr can be imagined as a huge directory that has been fed
data as per our requirement, and it can be queried to get the relevant
data by using an appropriate search criteria that was indexed while
feeding in the data. Let\'s have a look at the following diagram and
understand how Solr search platform works:

::: {.mediaobject}
![](2_files/ebaeefbe-9985-45c7-b2a8-59f482a4a896.png)
:::

As you can see, the way to look at Solr is like this---it is basically
fed with lots of information, which is correctly indexed. Then, in order
to retrieve information, we query based on the index and get our
results.

Suppose I am designing a data store for the world\'s largest online
library using Solr. What I do is feed Solr with each book\'s
information, such as the title, published date, author, price, genre,
and so on. So, when I query all the books written by J.K. Rowling in the
child fiction genre, it returns me my favorite [*Harry
Potter*]{.emphasis} book series.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec50}Getting started with Solr\'s basics {#getting-started-with-solrs-basics .title}

</div>

</div>
:::

Everything for Solr[]{#id288605565 .indexterm} is a document, which
forms the basic unit of information. Each document contains a set of
fields, which can be of various types. If we take an example of a book
store, each book forms a document. Now the author, publication date, and
so on become fields of the document book, which can be a text or date
format if we take up the example of the two fields quoted. Let\'s take a
look at the following diagram and understand how the documents, fields
are laid out in index of Solr instance:

::: {.mediaobject}
![](2_files/ff99aa2f-c23c-4a18-83d3-d8a39b76c635.png)
:::

As you can see from the previous diagram, we can have as many
indexes/cores in a Solr instance as we like. The first index/core in the
previous case is for a music store, which can have various documents
related to songs indexed in them. Each document has fields or metadata,
such as singer, song title, and so on.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec51}The schema file of Solr {#the-schema-file-of-solr .title}

</div>

</div>
:::

All the information about fields and field types[]{#id288617165
.indexterm} is mentioned in the schema file of Solr. Now, the schema
file can be in one of the following places depending on how you have
configured Solr:

::: {.itemizedlist}
-   If you use `ClassicIndexSchemaFactory`{.literal}, you would be
    manually editing the schema file which
    is named `schema.xml`{.literal} by default
-   If you decide to make schema changes at runtime using either
    the Schema API or schemaless mode, then the schema file is managed
    in the `managed-schema`{.literal} file.
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note13}Note {#note .title}

In the case of SolrCloud, you will make changes to the schema using
Solr\'s admin UI or Schema API if it is enabled. In such a case, you
will not be able to find either `schema.xml`{.literal} or
`managed-schema.xml`{.literal}.



[]{#ch03lvl1sec23}Understanding field types {#understanding-field-types .title style="clear: both"}
-------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

As discussed earlier, we are able to tell Solr how it
should[]{#id288339944 .indexterm} interpret the incoming data in a field
and how we can query a field using the information specified in field
types.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec52}Definitions and properties of field types {#definitions-and-properties-of-field-types .title}

</div>

</div>
:::

Before going to the definitions and[]{#id288339961 .indexterm}
properties, we will see what field analysis means.

What Solr should do or how it should interpret data whenever data is
indexed is important. For example, a description of a book can contain
lots of useless words: helping verbs such as [*is*]{.emphasis},
[*was*]{.emphasis}, and [*are*]{.emphasis}; pronouns such
as [*they*]{.emphasis}, [*we*]{.emphasis}, and so on; and other general
words such as [*the*]{.emphasis}, [*a*]{.emphasis}, [*this*]{.emphasis},
and so on. Querying these words will bring[]{#id288340370 .indexterm}
all the data. Similarly what should we do with words that have capital
letters?

All of these problems can be catered using field analysis to ignore
common words or casing while indexing or querying. We will dive
deep into field analysis in the next chapter.

Now, coming back to field types, all analyses on a field are done by the
field type, whether documents are indexed or a query is made on the
index.

All field types are specified in `schema.xml`{.literal}. A field type
can have the following attributes:

::: {.itemizedlist}
-   The `name`{.literal} field, which is mandatory.
-   The `class`{.literal} field, which is also mandatory. This tells us
    which class to implement.
-   In the case of `TextField`{.literal}, you can mention description to
    convey what the `TextField`{.literal} does.
-   Based on the `Implementation`{.literal} class certain field type
    properties which may or may not be mandatory.
:::

The field type is defined within the `fieldType`{.literal} tags. Let\'s
take a look at a field type definition for `text_en`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
 <fieldType name="text_en" class="solr.TextField" 
   positionIncrementGap="100">
     <analyzer type="index">
         <tokenizer class="solr.StandardTokenizerFactory"/>
         <!-- in this example, we will only use synonyms at query
           time
         <filter class="solr.SynonymGraphFilterFactory" 
           synonyms="index_synonyms.txt" ignoreCase="true" 
           expand="false"/>
         <filter class="solr.FlattenGraphFilterFactory"/>

         -->
         <!-- Case insensitive stop word removal-->
         <filter class="solr.StopFilterFactory"
             ignoreCase="true"
             words="lang/stopwords_en.txt"
         />
         <filter class="solr.LowerCaseFilterFactory"/>
         <filter class="solr.EnglishPossessiveFilterFactory"/>
         <filter class="solr.KeywordMarkerFilterFactory" 
           protected="protwords.txt"/>
         <filter class="solr.PorterStemFilterFactory"/>
     </analyzer>
     <analyzer type="query">
         <tokenizer class="solr.StandardTokenizerFactory"/>
         <filter class="solr.SynonymGraphFilterFactory"
           synonyms="synonyms.txt" ignoreCase="true" 
           expand="true"/>
         <filter class="solr.StopFilterFactory"
             ignoreCase="true"
             words="lang/stopwords_en.txt"
         />
         <filter class="solr.LowerCaseFilterFactory"/>
         <filter class="solr.EnglishPossessiveFilterFactory"/>
         <filter class="solr.KeywordMarkerFilterFactory"
           protected="protwords.txt"/>
         <filter class="solr.PorterStemFilterFactory"/>
     </analyzer>
 </fieldType>
```
:::

As you can see, the first line defines `fieldType`{.literal} with name
`text_en`{.literal}, which implements
the `solr.TextField`{.literal} class. It also has an
attribute, `positionIncrementGap`{.literal}, which adds spaces between
multi-value fields.

For example, let\'s say your text contains the following tokens:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
writer: Sandeep Nair
writer: Dharmesh Vasoya
```
:::

Now, without any `positionIncrementGap`{.literal} attribute, it is
possible to bring up the results when someone searches
for `Nair Dharmesh`{.literal}. But with
the `positionIncrementGap`{.literal} attribute, we can avoid this.

We will cover the rest of the details available in
`fieldType`{.literal} class in detail in the next chapter.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note14}Note {#note .title}

You must have noticed that a class begins with `solr`{.literal} in
`solr.TextField`{.literal}. This is a short form for the fully qualified
package name `org.apache.solr.schema`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch03lvl3sec11}Field type properties {#field-type-properties .title}

</div>

</div>
:::

All of field type is behavior generally controlled[]{#id288377106
.indexterm} by the `fieldType`{.literal} class and the optional
properties:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="currency" class="solr.CurrencyFieldType"                           amountLongSuffix="_l_ns" codeStrSuffix="_s_ns" 
        defaultCurrency="USD" currencyConfig="currency.xml" />
```
:::

We see that the `defaultCurrency`{.literal} is `USD`{.literal} and extra
config-related information is specified in a file called
`currency.xml`{.literal}.

There are three types of properties that can be associated with a
`fieldType`{.literal} class:

::: {.itemizedlist}
-   General properties, which can be applicable for any
    `fieldType`{.literal} class
-   Field default properties, as shown in the previous example where we
    default the currency value to `USD`{.literal} if none is specified
-   Finally, properties that are specific for a specific
    `fieldType`{.literal} class
:::

General properties are specified as follows:

::: {.informaltable}
  --------------------------------------- -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  [**Properties**]{.strong}               [**Description**]{.strong}
  `name`{.literal}                        The name of the `fieldType`{.literal}.
  `class`{.literal}                       The name of the class that is used to index and store the data for this type.
  `positionIncrementGap`{.literal}        Specifically for multi-value fields. It is used to specify the distance between multiple values. This helps in preventing phrase matches that are spurious in nature.
  `autoGeneratePhraseQueries`{.literal}   This is used for `TextField`{.literal}. If this value is true, Solr generates phrase queries intended for adjacent terms automatically. If it is false, then terms are supposed to be enclosed in double quotes in order for them to be treated as phrases.
  `enableGraphQueries`{.literal}          Used for only text fields. It is applicable only while querying `sow=false`{.literal}.
  `docValuesFormat`{.literal}             This is used to define a custom `DocValuesFormat`{.literal}.
  `postingsFormat`{.literal}              Helps in defining a custom `PostingsFormat`{.literal} to be used for fields of this type. 
  --------------------------------------- -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
:::

 

Now let\'s take a look at the field default properties. The values can
be defaulted in actual fields or can be inherited from the field types.

::: {.informaltable}
+----------------+----------------+----------------+----------------+
| [**Proper      | [**Deta        | [**Available   | [**Default     |
| ty**]{.strong} | il**]{.strong} | valu           | val            |
|                |                | es**]{.strong} | ue**]{.strong} |
+----------------+----------------+----------------+----------------+
| `inde          | Tells whether  | ::: {          | `t             |
| xed`{.literal} | to index the   | .itemizedlist} | rue`{.literal} |
|                | field or not.  | -   `t         |                |
|                |                | rue`{.literal} |                |
|                |                | -   `fa        |                |
|                |                | lse`{.literal} |                |
|                |                | :::            |                |
+----------------+----------------+----------------+----------------+
| `sto           | The actual     | ::: {          | `t             |
| red`{.literal} | value of the   | .itemizedlist} | rue`{.literal} |
|                | field can be   | -   `t         |                |
|                | retrieved      | rue`{.literal} |                |
|                | using queries  | -   `fa        |                |
|                | if the value   | lse`{.literal} |                |
|                | is             | :::            |                |
|                | `tr            |                |                |
|                | ue.`{.literal} |                |                |
+----------------+----------------+----------------+----------------+
| `sortMissingFi | If a sort      | ::: {          | `fa            |
| rst`{.literal} | field is not   | .itemizedlist} | lse`{.literal} |
|                | present, this  | -   `t         |                |
| and            | decides the    | rue`{.literal} |                |
| `sortMissingL  | placement of   | -   `fa        |                |
| ast`{.literal} | the document.  | lse`{.literal} |                |
|                |                | :::            |                |
+----------------+----------------+----------------+----------------+
| `docVal        | The value of   | ::: {          | `fa            |
| ues`{.literal} | this field is  | .itemizedlist} | lse`{.literal} |
|                | placed in a    | -   `t         |                |
|                | column-or      | rue`{.literal} |                |
|                | iented `docVal | -   `fa        |                |
|                | ues`{.literal} | lse`{.literal} |                |
|                | structure if   | :::            |                |
|                | set            |                |                |
|                | to `tr         |                |                |
|                | ue.`{.literal} |                |                |
+----------------+----------------+----------------+----------------+
| `multiVal      | Indicates      | ::: {          | `fa            |
| ued`{.literal} | whether a      | .itemizedlist} | lse`{.literal} |
|                | single         | -   `t         |                |
|                | document can   | rue`{.literal} |                |
|                | have multiple  | -   `fa        |                |
|                | values for     | lse`{.literal} |                |
|                | this field     | :::            |                |
|                | type.          |                |                |
+----------------+----------------+----------------+----------------+
| `omitNo        | Used to        | ::: {          | `*`{.literal}  |
| rms`{.literal} | disable field  | .itemizedlist} |                |
|                | length         | -   `t         |                |
|                | normalization  | rue`{.literal} |                |
|                | and to save    | -   `fa        |                |
|                | some memory.   | lse`{.literal} |                |
|                | For all        | :::            |                |
|                | primitive      |                |                |
|                | field types,   |                |                |
|                | the value is   |                |                |
|                | `true          |                |                |
|                | `{.literal} by |                |                |
|                | default. Norms |                |                |
|                | are needed     |                |                |
|                | only for       |                |                |
|                | full-text      |                |                |
|                | fields.        |                |                |
+----------------+----------------+----------------+----------------+
| `omitTer       | Omits term     | ::: {          | `*`{.literal}  |
| mFreqAndPositi | frequency,     | .itemizedlist} |                |
| ons`{.literal} | position, and  | -   `t         |                |
|                | payloads from  | rue`{.literal} |                |
|                | postings of    | -   `fa        |                |
|                | this field     | lse`{.literal} |                |
|                | when           | :::            |                |
|                | `tr            |                |                |
|                | ue`{.literal}. |                |                |
|                | Defaults to    |                |                |
|                | `t             |                |                |
|                | rue`{.literal} |                |                |
|                | for non-text   |                |                |
|                | fields.        |                |                |
+----------------+----------------+----------------+----------------+
| `omitPositi    | This is        | ::: {          | `*`{.literal}  |
| ons`{.literal} | similar to     | .itemizedlist} |                |
|                | `omitTerm      | -   `t         |                |
|                | FreqAndPositio | rue`{.literal} |                |
|                | ns`{.literal}; | -   `fa        |                |
|                | however, in    | lse`{.literal} |                |
|                | this case, it  | :::            |                |
|                | preserves      |                |                |
|                | information    |                |                |
|                | about the term |                |                |
|                | frequency.     |                |                |
+----------------+----------------+----------------+----------------+
| `termVecto     | Solr maintains | ::: {          | `fa            |
| rs`{.literal}, | full term      | .itemizedlist} | lse`{.literal} |
| `termPositio   | vectors for    | -   `t         |                |
| ns`{.literal}, | every single   | rue`{.literal} |                |
| `termOffse     | document if    | -   `fa        |                |
| ts`{.literal}, | these          | lse`{.literal} |                |
| and            | properties are | :::            |                |
| `termPaylo     | `tr            |                |                |
| ads`{.literal} | ue`{.literal}. |                |                |
|                | These vectors, |                |                |
|                | may include    |                |                |
|                | position,      |                |                |
|                | offset, and    |                |                |
|                | payload        |                |                |
|                | information    |                |                |
|                | optionally for |                |                |
|                | each term      |                |                |
|                | occurrence.    |                |                |
+----------------+----------------+----------------+----------------+
| `requi         | Tells Solr not | ::: {          | `fa            |
| red`{.literal} | to accept any  | .itemizedlist} | lse`{.literal} |
|                | attempts to    | -   `t         |                |
|                | add a document | rue`{.literal} |                |
|                | that does not  | -   `fa        |                |
|                | have a value   | lse`{.literal} |                |
|                | for the field. | :::            |                |
+----------------+----------------+----------------+----------------+
| `use           | This is        | ::: {          | `t             |
| DocValuesAsSto | dependent on   | .itemizedlist} | rue`{.literal} |
| red`{.literal} | the `docVal    | -   `t         |                |
|                | ues`{.literal} | rue`{.literal} |                |
|                | enabled. This  | -   `fa        |                |
|                | is enabled if  | lse`{.literal} |                |
|                | we set it to   | :::            |                |
|                | true and will  |                |                |
|                | allow the      |                |                |
|                | field to be    |                |                |
|                | returned as if |                |                |
|                | it were a      |                |                |
|                | stored field   |                |                |
|                | while matching |                |                |
|                | `*`{.literal}  |                |                |
|                | in an          |                |                |
|                | `fl`{.literal} |                |                |
|                | parameter.     |                |                |
+----------------+----------------+----------------+----------------+
| `la            | This will      | ::: {          | `fa            |
| rge`{.literal} | work only wh   | .itemizedlist} | lse`{.literal} |
|                | en `stored="tr | -   `t         |                |
|                | ue"`{.literal} | rue`{.literal} |                |
|                | and `mul       | -   `fa        |                |
|                | tiValued="fals | lse`{.literal} |                |
|                | e"`{.literal}. | :::            |                |
|                | In order       |                |                |
|                | not to get     |                |                |
|                | cached in      |                |                |
|                | memory, this   |                |                |
|                | is meant for   |                |                |
|                | fields that    |                |                |
|                | might have     |                |                |
|                | very large     |                |                |
|                | values.        |                |                |
+----------------+----------------+----------------+----------------+
:::

 

In order to score a document in searching, Solr uses similarity. For
every collection, there is one global similarity and Solr implicitly
uses `BM25Similarity`{.literal}. You can declare a top-level
`<similarity/>`{.literal} element and override it.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note15}Note {#note-1 .title}

**`Best Matching (BM)`**BM25 is a ranking algorithm used by search
engines such as Lucene to rank matching documents according to their
relevance by a given search query. 
:::
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec53}Field types available in Solr {#field-types-available-in-solr .title}

</div>

</div>
:::

Let\'s see the various field types available[]{#id288340580 .indexterm}
with Solr:

::: {.itemizedlist}
-   `BinaryField`{.literal}: This is intended for binary data.
-   `BoolField`{.literal}: This is for Boolean data. It can either be
    true or false. If values `1`{.literal}, `t`{.literal}, or
    `T`{.literal} are encountered in the first character, then it is
    interpreted as true. All other values are interpreted as false.
-   `CollationField`{.literal}: This is used for collated sort keys,
    which can be used for locale-sensitive sort and range queries.
-   `CurrencyFieldType`{.literal}: This is used for currencies and
    exchange rates.
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note16}Note {#note-2 .title}

There is also a `CurrencyField`{.literal} that does the same thing, but
it is deprecated.
:::

::: {.itemizedlist}
-   `DateRangeField`{.literal}: This is used for working with date
    ranges, as the name suggests. We will cover this in detail in the
    next section.
-   `ExternalFileField`{.literal}: This is used when values have to be
    pulled from an external file.
-   `EnumFieldType`{.literal}: This is used for enumerated sets of
    values.
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note17}Note {#note-3 .title}

There is also an `EnumField`{.literal} that does the same thing, but it
is now deprecated.
:::

::: {.itemizedlist}
-   `ICUCollationField`{.literal}: This is similar to
    `CollationField`{.literal} and is recommended instead of
    `CollationField`{.literal}.
-   `LatLonPointSpatialField`{.literal}: This is used for multi-value
    for multiple points of latitude and longitude coordinate pairs. It
    is usually specified as latitude, longitude in that order, with a
    comma to separate the two.
-   `PointType`{.literal}: This is used when we have a single-valued
    n-dimensional point, and if we have to sort spatial data that is
    non-latitude, non-longitude or handle rare use cases.
-   `PreAnalyzedField`{.literal}: This is used when we have to send
    serialized token streams to Solr to store and index without any
    additional text processing.
-   `RandomSortField`{.literal}: This does not contain values and is
    used to return results[]{#id288340769 .indexterm} in a random order.
-   `SpatialRecursivePrefixTreeFieldType`{.literal}: This accepts
    latitude, longitude strings in [**well-known text**]{.strong}
    ([**WKT**]{.strong}) format.
-   `StrField`{.literal}: This is intended for small fields and is not
    tokenized or analyzed.
-   `TextField`{.literal}: This is used for multiple words or tokens.
-   `DatePointField`{.literal}/`DoublePointField`{.literal}/`FloatPointField`{.literal}/`IntPointField`{.literal}/`LongPointField`{.literal}:
    These are all similar to the analogous Trie-based fields. The only
    difference is that they use dimensional-points-based data structures
    and do not require any configuration of precision steps.
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note18}Note {#note-4 .title}

As of Solr 7, all the `Trie[Datatype]Fields`{.literal} are
deprecated: `TrieField`{.literal}, `TrieDateField`{.literal},
`TrieDoubleField`{.literal}, `TrieFloatField`{.literal},
`TrieIntField`{.literal}, and `TrieLongField`{.literal}.
:::

`UUIDField`{.literal} will generate a new UUID when we pass a value of
`NEW`{.literal}. It is recommended to
use `UUIDUpdateProcessorFactory`{.literal} instead of
`UUIDField`{.literal} to generate UUID values when using
`SolrCloud`{.literal}, since doing so will make each document of each
replica have a unique UUID value.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec54}Understanding date fields {#understanding-date-fields .title}

</div>

</div>
:::

As seen before, Solr\'s date fields[]{#id288340872 .indexterm} such
as `DatePointField`{.literal}, `DateRangeField`{.literal}, and
`TrieDateField`{.literal} (deprecated) represent dates as points in time
with millisecond precision. Solr uses
`DateTimeFormatter.ISO_INSTANT`{.literal} for formatting and parsing:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
YYYY-MM-DDThh:mm:ssZ
```
:::

Let\'s break up the preceding date pattern. Please take a look at the
break up listed as follow:

::: {.itemizedlist}
-   `YYYY`{.literal} is the year. An example is 1985
-   `MM`{.literal} is the month. For example, `02`{.literal} represents
    February
-   `DD`{.literal} is the day of the month
-   `T`{.literal} is a literal used to separate date and time
-   `hh`{.literal} is the hour of the day
-   `mm`{.literal} is minutes
-   `ss`{.literal} is seconds
-   `Z`{.literal} is a literal used to indicate the string
    representation of the[]{#id288340942 .indexterm} date in
    [**Coordiated Universal Time**]{.strong} ([**UTC**]{.strong})
:::

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip19}Note {#note-5 .title}

No time zone can be specified and all the string representations of
dates are specified in UTC.
:::

An example would be:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
1985-02-21T06:33:19Z
```
:::

We can optionally add fractional seconds, but as mentioned earlier, any
precision beyond milliseconds will not be considered.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip20}Note {#note-6 .title}

If we need a date prior to the year `0000`{.literal}, then the date
should have a leading `-`{.literal}; similarly for years after
`9999`{.literal}, there should be a leading `+`{.literal}.
:::

In order to express date ranges, Solr\'s `DateRangeField`{.literal} is
used. Some of the examples are shown as follows:

::: {.itemizedlist}
-   `1985-02`{.literal}: This represents the entire month of February
    1985
-   `1985-02T06`{.literal}: This also adds an hour element from 6 AM to
    7 AM during February 1985
-   `-0002`{.literal}: Since there is a leading `–`{.literal}, this
    represents 3 BC
-   `[1985-02-21 TO 1989-08-27]`{.literal}: The date range between these
    two dates
-   `[1985 TO 1985-02-21]`{.literal}: From the start of 1985
    until February 21, 1985
-   `[* TO 1985-02-21]`{.literal}: From the earliest representable time
    to the end of the 21^st^ day of February 1985
:::

Date math expressions are one more interesting format. They help by
adding some quantity of time in a specified unit or rounding off the
current time by a specified unit. These expressions can also be chained
and they are always evaluated from left to right, like every Math
expression.

Some valid expressions are as follows:

::: {.itemizedlist}
-   `NOW+4DAYS`{.literal}: Specifies 4 days from today.
-   `NOW-6MONTHS`{.literal}: Specifies 6 months before now.
-   `NOW/HOUR`{.literal}: Here, the slash indicates rounding. This tells
    us to round off to the beginning of the current hour.
-   `NOW+4MONTHS+6DAYS/DAY`{.literal}: This expression specifies a point
    in the future four months and six days from now and rounds off to
    the beginning of that day.
:::

Date math can be applied between any two times and not everything has to
necessarily be relative to `NOW`{.literal}.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip21}Note {#note-7 .title}

The `NOW`{.literal} parameter can also be used to specify an arbitrary
moment in time and not necessarily the current time. It can be
overridden using long-valued milliseconds since the epoch.
:::

`DateRangeFields`{.literal} also supports three relational predicates
between the indexed data and the query range:

::: {.itemizedlist}
-   `Intersects`{.literal} (which is default)
-   `Contains`{.literal}
-   `Within`{.literal}
:::

We can specify the predicate by querying using the `op`{.literal} local
parameter:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
fq={!field f=dateRange op=Contains}[1985 TO 1989]
```
:::

This would find documents with indexed ranges that contain the range
`1985`{.literal} to `1989`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec55}Understanding currencies and exchange rates  {#understanding-currencies-and-exchange-rates .title}

</div>

</div>
:::

As the name implies, a currency field type is used[]{#id288341176
.indexterm} for any monetary value. It supports currency[]{#id288341185
.indexterm} conversion and exchange rates during a query.

Solr provides[]{#id288341232 .indexterm} the following
features[]{#id288341238 .indexterm} for it:

::: {.itemizedlist}
-   Range queries
-   Point queries
-   Sorting
-   Function range queries
-   Symmetric and asymmetric exchange rates
-   Currency parsing
:::

As with other field types, the `currency`{.literal} field type is
configured in `schema.xml`{.literal}. Shown here is the default
configuration:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="currency" class="solr.CurrencyFieldType"                           amountLongSuffix="_l_ns" codeStrSuffix="_s_ns" 
        defaultCurrency="USD" currencyConfig="currency.xml" />
```
:::

As you can see, `name`{.literal} is set as `currency`{.literal} and
`class`{.literal} is specified as `solr.CurrencyFieldType`{.literal}. We
have defined the `defaultCurrency`{.literal} as `INR`{.literal} or
Indian rupee. Also note `currencyConfig`{.literal}, which says that the
file location is set to `currency.xml`{.literal}. This file specifies
exchange rates between `INR`{.literal} and other currencies.

In your open `managed-schema`{.literal} file under
the `SOLR_HOME/example/example-DIH/solr/solr/conf`{.literal} folder,
using a text editor, you will find the following dynamic field:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<dynamicField name="*_c" type="currency" indexed="true" stored="true"/>
```
:::

This `dynamicField`{.literal} matches any fields suffixed by
`_c`{.literal} and treats them as `currency`{.literal} type fields.

Solr gives us the flexibility to index money fields in our native
currency. We can specify `100,SGD`{.literal} to index the money field in
Singapore dollars:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="currency" class="solr.CurrencyFieldType"                           amountLongSuffix="_l_ns" codeStrSuffix="_s_ns" 
        defaultCurrency="USD" currencyConfig="currency.xml" />
```
:::

Let\'s revisit the field type.

Here, you will notice one thing; there are a couple of subfield suffixes
named `amountLongSuffix`{.literal} and `codeStrSuffix`{.literal}, which
correspond to raw amount and currency code respectively. In this case,
the raw amount field will make use of the `*_l_ns`{.literal} dynamic
field, which uses a long field type. The currency code field will make
use of the `*_s_ns`{.literal} dynamic field, which uses a string field
type.

In the previous tag, you can also see `currencyConfig`{.literal}, which
refers to a file called `currency.xml`{.literal}. This is used to
specify exchange rates.

Solr supports two types of providers:

::: {.itemizedlist}
-   `FileExchangeRateProvider`{.literal}
-   `OpenExchangeRatesOrgProvider`{.literal}
:::

`FileExchangeRateProvider`{.literal} is the default provider. In order
to use this, we specify the config file using `currencyConfig`{.literal}
as shown previously. The contents of `currency.xml`{.literal} are as
follows:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<?xml version="1.0" ?>
<!-- Example exchange rates file for CurrencyFieldType named "currency" in example schema -->
<currencyConfig version="1.0">
 <rates>
 <!-- Updated from http://www.exchangerate.com/ at 2011-09-27 -->
 <rate from="USD" to="ARS" rate="4.333871" comment="ARGENTINA Peso" />
 <rate from="USD" to="AUD" rate="1.025768" comment="AUSTRALIA Dollar" />
 <rate from="USD" to="EUR" rate="0.743676" comment="European Euro" />
 <rate from="USD" to="BRL" rate="1.881093" comment="BRAZIL Real" />
 <rate from="USD" to="CAD" rate="1.030815" comment="CANADA Dollar" />
 <rate from="USD" to="CLP" rate="519.0996" comment="CHILE Peso" />
 <rate from="USD" to="CNY" rate="6.387310" comment="CHINA Yuan" />
 <rate from="USD" to="CZK" rate="18.47134" comment="CZECH REP. Koruna" />
 <rate from="USD" to="DKK" rate="5.515436" comment="DENMARK Krone" />
 <rate from="USD" to="HKD" rate="7.801922" comment="HONG KONG Dollar" />
 <rate from="USD" to="HUF" rate="215.6169" comment="HUNGARY Forint" />
 <rate from="USD" to="ISK" rate="118.1280" comment="ICELAND Krona" />
 <rate from="USD" to="INR" rate="49.49088" comment="INDIA Rupee" />
 <rate from="USD" to="XDR" rate="0.641358" comment="INTNL MON. FUND SDR" />
 <rate from="USD" to="ILS" rate="3.709739" comment="ISRAEL Sheqel" />
 <rate from="USD" to="JPY" rate="76.32419" comment="JAPAN Yen" />
 <rate from="USD" to="KRW" rate="1169.173" comment="KOREA (SOUTH) Won" />
 <rate from="USD" to="KWD" rate="0.275142" comment="KUWAIT Dinar" />
 <rate from="USD" to="MXN" rate="13.85895" comment="MEXICO Peso" />
 <rate from="USD" to="NZD" rate="1.285159" comment="NEW ZEALAND Dollar" />
 <rate from="USD" to="NOK" rate="5.859035" comment="NORWAY Krone" />
 <rate from="USD" to="PKR" rate="87.57007" comment="PAKISTAN Rupee" />
 <rate from="USD" to="PEN" rate="2.730683" comment="PERU Sol" />
 <rate from="USD" to="PHP" rate="43.62039" comment="PHILIPPINES Peso" />
 <rate from="USD" to="PLN" rate="3.310139" comment="POLAND Zloty" />
 <rate from="USD" to="RON" rate="3.100932" comment="ROMANIA Leu" />
 <rate from="USD" to="RUB" rate="32.14663" comment="RUSSIA Ruble" />
 <rate from="USD" to="SAR" rate="3.750465" comment="SAUDI ARABIA Riyal" />
 <rate from="USD" to="SGD" rate="1.299352" comment="SINGAPORE Dollar" />
 <rate from="USD" to="ZAR" rate="8.329761" comment="SOUTH AFRICA Rand" />
 <rate from="USD" to="SEK" rate="6.883442" comment="SWEDEN Krona" />
 <rate from="USD" to="CHF" rate="0.906035" comment="SWITZERLAND Franc" />
 <rate from="USD" to="TWD" rate="30.40283" comment="TAIWAN Dollar" />
 <rate from="USD" to="THB" rate="30.89487" comment="THAILAND Baht" />
 <rate from="USD" to="AED" rate="3.672955" comment="U.A.E. Dirham" />
 <rate from="USD" to="UAH" rate="7.988582" comment="UKRAINE Hryvnia" />
 <rate from="USD" to="GBP" rate="0.647910" comment="UNITED KINGDOM Pound" />
<!-- Cross-rates for some common currencies -->
 <rate from="EUR" to="GBP" rate="0.869914" /> 
 <rate from="EUR" to="NOK" rate="7.800095" /> 
 <rate from="GBP" to="NOK" rate="8.966508" /> 
 </rates>
</currencyConfig>
```
:::

Updating `currency.xml`{.literal} becomes tedious manual work. In order
to dynamically pull the latest exchange rates from the Web, we use
`OpenExchangeRatesOrgProvider`{.literal}. This will download the latest
exchange[]{#id288341460 .indexterm} rates from
[https://openexchangerates.org](https://openexchangerates.org/){.ulink}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="currency" class="solr.CurrencyFieldType"
    amountLongSuffix="_l_ns" codeStrSuffix="_s_ns"        providerClass="solr.OpenExchangeRatesOrgProvider" 
    refreshInterval="60"     ratesFileLocation=     "http://www.openexchangerates.org/api/latest.json?app_id=yourPersonalAppIdKey"/>
```
:::

As shown in the previous tag, we have specified
`providerClass`{.literal}
as `solr.OpenExchangeRatesOrgProvider`{.literal} and specified the
refresh interval as 1 hour. We have also specified the URL from which to
pull the latest exchange rates.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip22}Note {#note-8 .title}

You need to register first at Open Exchange Rates to get your own
personal app ID key, which you will then replace in the preceding URL.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec56}Understanding enum fields {#understanding-enum-fields .title}

</div>

</div>
:::

Just as Java has the[]{#id288341538 .indexterm} enum data type to
have[]{#id288341546 .indexterm} something for a closed set of values,
Solr has `EnumFieldType`{.literal}, which lets us define closed set
values. Here, the sort order is predetermined.

Defining an `EnumFieldType`{.literal} is done as follows:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<fieldType name="genreList" class="solr.EnumFieldType" docValues="true" enumsConfig= "enumsConfig.xml" enumName="genre"/>
```
:::

Here, as you can see, we have defined the name as `genreList`{.literal}
and the class is specified as `solr.EnumFieldType`{.literal}. We also
have specified `enumConfig`{.literal} to specify the path of the
configuration file.

Last but not least, we have specified `enumName`{.literal} to uniquely
identify the name of the enumeration:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<?xml version="1.0" ?>
<enumsConfig>
    <enum name="genre">
        <value>Science Fiction</value>
        <value>Satire</value>
        <value>Drama</value>
        <value>Action</value>
        <value>Adventure</value>
        <value>Mystery</value>
        <value>Horror</value>
    </enum>
</enumsConfig>
```
:::

The `enumsConfig`{.literal} file can contain many enumeration value
lists with different names as per your requirement. Take a look at the
content of the enumeration file for the genres shown in the preceding
code.


[]{#ch03lvl1sec24}Field management {#field-management .title style="clear: both"}
----------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Once your primary work of field types setup is done, field definition
is[]{#id288339995 .indexterm} a small task. Just as with field types,
the fields element of `schema.xml`{.literal} holds the field definition.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec57}Field properties {#field-properties .title}

</div>

</div>
:::

Let\'s first see a sample[]{#id288340358 .indexterm} field definition:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<field name="weight" type="float" default=”0.0” indexed="true" stored="true"/>
```
:::

In the preceding example, we have defined a field named
`weight`{.literal}, whose field type is `float`{.literal} with
a `default`{.literal} value of `0.0`{.literal}. Moreover, the
`indexed`{.literal} as well as `stored`{.literal} properties
are explicitly set to `true`{.literal}.

Field definitions will have these properties:

::: {.itemizedlist}
-   `name`{.literal}: The field name. This has to be alphanumeric and
    can include underscore characters. It cannot begin with a digit.
    Reserved names should start and end with underscores (for example,
    `_root_`{.literal}). Every field must have a name.
-   `type`{.literal}: The name of the `fieldType`{.literal}. All the
    fields should have a type.
-   `default`{.literal}: The default value to be used for the field.
:::

Fields and field types share many of the optional properties here. If
there are two different values of a property specified in both field and
field type, then the property value specified in the field takes
precedence.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec58}Copying fields {#copying-fields .title}

</div>

</div>
:::

Copying fields is used when you want[]{#id288605565 .indexterm} to
interpret a field in more than one way. Solr provides a solution to copy
fields so that one can apply distinct field types for the same field.

Let\'s see the following element that I have copied from one of the
managed-schemas available in demo Solr projects:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<copyField source="name" dest="text"/>
```
:::

In the previous example, we are copying the `name`{.literal} field
specified in the `source`{.literal} attribute to a field name
`text`{.literal}, which is specified in `dest`{.literal}.

The actual copying of fields occurs before analysis, which makes it
possible to have two fields with the same content but different
analyses.

The general use case of this functionality is when we have one global
search for all fields. Let\'s say I want to search the word
`Harry`{.literal} in the title and description. Then I can create a
`copyField`{.literal} for the title and description fields and redirect
them to a common destination. Look at the following snippet:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<copyField source="*_e" dest="text" maxChars="10000" />
```
:::

Here, we are specifying a wildcard pattern that matches everything that
ends with `_e`{.literal} and indexes them to the destination
`text`{.literal} field.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#note23}Note {#note .title}

You cannot chain `copyField`{.literal}; that is, the destination of one
`copyField`{.literal} cannot be a part of the source of another
`copyField`{.literal}. The workaround is to create multiple destination
fields and use the same source field.
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec59}Dynamic fields {#dynamic-fields .title}

</div>

</div>
:::

We use dynamic fields to index fields that we do not want[]{#id288617288
.indexterm} to explicitly define in our schema.

This feature is handy when you want to use a wild card for indexing
fields, where you want to index all the fields having a certain pattern.
Let\'s see an example of `dynamicField`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<dynamicField name="*_e" type="int" indexed="true" stored="true"/>
```
:::

As you can see, dynamic fields also have name, type, and options.
However, here you can see that the name has a wildcard pattern
`*_e`{.literal}, which means any field with `_e`{.literal} will have the
same indexing.



[]{#ch03lvl1sec25}Mastering Schema API {#mastering-schema-api .title style="clear: both"}
--------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Schema API is the one-stop shop for most[]{#id288339943 .indexterm}
operations on your schema. It provides a REST-like HTTP API for doing
all these operations.

You can read, write, or delete dynamic fields, fields, copy field rules,
and field types. 

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip24}Note {#note .title}

Do not manually write any changes into the `managed-schema`{.literal}
file yourself. This will work only as long as you don\'t use Schema API.
If you use Schema API by mistake, all your changes might be overwritten.
So, it is highly recommend that you leave your
`managed-schema`{.literal} file alone.
:::

The response of the API call is of either JSON or XML format. 

Assuming that you are using the `gettingstarted`{.literal} collection,
the base address of API will be
`http://localhost:8983/solr/gettingstarted`{.literal}.

::: {.note style="margin-left: 0.5in; margin-right: 0.5in;"}
### []{#tip25}Note {#note-1 .title}

Always reindex once you use Schema API for modifications. Only then
will the changes that you have applied to the schema be reflected for
existing documents that are already indexed.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec60}Schema API in detail {#schema-api-in-detail .title}

</div>

</div>
:::

Let\'s see some of the important schema[]{#id288340001 .indexterm}
endpoints. We will do all the examples on the `gettingstarted`{.literal}
collection.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch03lvl3sec12}Schema operations {#schema-operations .title}

</div>

</div>
:::

In order[]{#id288340377 .indexterm} to see the schema, we need to use
the `/schema`{.literal} endpoint, `http://localhost:8983/solr/gettingstarted/schema/`{.literal}.

This will retrieve the entire schema information:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":2},
  "schema":{
    "name":"default-config",
    "version":1.6,
    "uniqueKey":"id",
    "fieldTypes":[{
        "name":"ancestor_path",
        "class":"solr.TextField",
        "indexAnalyzer":{
          "tokenizer":{
            "class":"solr.KeywordTokenizerFactory"}},
        "queryAnalyzer":{
          "tokenizer":{
            "class":"solr.PathHierarchyTokenizerFactory",
            "delimiter":"/"}}},
      {
        "name":"binary",
        "class":"solr.BinaryField"},
      {
        "name":"boolean",
        "class":"solr.BoolField",
        "sortMissingLast":true},
      {
        "name":"booleans",
        "class":"solr.BoolField",
        "sortMissingLast":true,
        "multiValued":true},
      {
        "name":"delimited_payloads_float",
        "class":"solr.TextField",
        "indexed":true,
        "stored":false,
        "analyzer":{
          "tokenizer":{
            "class":"solr.WhitespaceTokenizerFactory"},
          "filters":[{
              "class":"solr.DelimitedPayloadTokenFilterFactory",
              "encoder":"float"}]}},
...
...
```
:::

In order to add a field, we will use the following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
curl -X POST -H 'Content-type:application/json' --data-binary '{
    "add-field":{
    "name":"song-name",
    "type":"text_general",
    "stored":true }
}' http://localhost:8983/solr/gettingstarted/schema
```
:::

The previous code will add a `song-name`{.literal} field name whose type
is `text_general`{.literal} to the `gettingstarted`{.literal} schema.

We can also replace a field\'s definition. To do so we can issue the
following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
curl -X POST -H 'Content-type:application/json' --data-binary '{
    "replace-field":{
    "name":"song-name",
    "type":"string",
    "stored":false }
}' http://localhost:8983/solr/gettingstarted/schema
```
:::

This will replace the definition of song with string.

Now let\'s take a look at the snippet to delete the field:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
curl -X POST -H 'Content-type:application/json' --data-binary '{
    "delete-field" : { "name":"song-name" }
}' http://localhost:8983/solr/gettingstarted/schema
```
:::

This will delete the `song-name`{.literal} field that we created just
now.

Similarly, to add, delete, and replace dynamic field rules, we need to
use the following endpoints:

::: {.itemizedlist}
-   `add-dynamic-field`{.literal}
-   `delete-dynamic-field`{.literal}
-   `replace-dynamic-field`{.literal}
:::

We can also add, update, and delete field[]{#id288377114 .indexterm}
types using these endpoints:

::: {.itemizedlist}
-   `add-field-type`{.literal}
-   `delete-field-type`{.literal}
-   `replace-field-type`{.literal}
:::

We will take a look at the syntax of making an API call to
`add-field-type`{.literal} as it has some additional parameters:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
curl -X POST -H 'Content-type:application/json' --data-binary '{
    "add-field-type" : {
        "name":"song-description-field",
        "class":"solr.TextField",
        "positionIncrementGap":"100",
        "analyzer" : {
            "charFilters":[{
                "class":"solr.PatternReplaceCharFilterFactory",
                "replacement":"$1$1",
                "pattern":"([a-zA-Z])\\\\1+" }],
            "tokenizer":{
                "class":"solr.WhitespaceTokenizerFactory" },
            "filters":[{
                "class":"solr.WordDelimiterFilterFactory",
                "preserveOriginal":"0" }]}}
}' http://localhost:8983/solr/gettingstarted/schema
```
:::

Here, we have added a new field type for song description, which is of
type `TextField`{.literal} and uses
`WhitespaceTokenizerFactory`{.literal} as a tokenizer using a filter
`WordDelimiterFilterFactory`{.literal}.

Finally, in order to add or delete a copy field rule, use the following:

::: {.itemizedlist}
-   `add-copy-field`{.literal}
-   `delete-copy-field`{.literal}
:::
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

#### []{#ch03lvl3sec13}Listing fields, field types, DynamicFields, and CopyField rules {#listing-fields-field-types-dynamicfields-and-copyfield-rules .title}

</div>

</div>
:::

In order[]{#id288628904 .indexterm} to list all[]{#id288340248
.indexterm} the fields, type the[]{#id288340257 .indexterm} following
URL in the[]{#id288340265 .indexterm}
browser: `http://localhost:8983/solr/gettingstarted/schema/fields`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":0},
  "fields":[{
      "name":"_root_",
      "type":"string",
      "docValues":false,
      "indexed":true,
      "stored":false},
    {
      "name":"_text_",
      "type":"text_general",
      "multiValued":true,
      "indexed":true,
      "stored":false},
    {
      "name":"_version_",
      "type":"plong",
      "indexed":false,
      "stored":false},
    {
      "name":"id",
      "type":"string",
      "multiValued":false,
      "indexed":true,
      "required":true,
      "stored":true}]}
```
:::

This will list all the fields defined. Now, to see all the field types,
enter
this `http://localhost:8983/solr/gettingstarted/schema/fieldtypes`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":2},
  "fieldTypes":[{
      "name":"ancestor_path",
      "class":"solr.TextField",
      "indexAnalyzer":{
        "tokenizer":{
          "class":"solr.KeywordTokenizerFactory"}},
      "queryAnalyzer":{
        "tokenizer":{
          "class":"solr.PathHierarchyTokenizerFactory",
          "delimiter":"/"}}},
    {
      "name":"binary",
      "class":"solr.BinaryField"},
    {
      "name":"boolean",
      "class":"solr.BoolField",
      "sortMissingLast":true},
    {
      "name":"booleans",
      "class":"solr.BoolField",
      "sortMissingLast":true,
      "multiValued":true},
    {
      "name":"delimited_payloads_float",
      "class":"solr.TextField",
      "indexed":true,
      "stored":false,
      "analyzer":{
        "tokenizer":{
          "class":"solr.WhitespaceTokenizerFactory"},
        "filters":[{
            "class":"solr.DelimitedPayloadTokenFilterFactory",
```
:::

This will display all the field types. You can also see an individual
field type by passing the field type name. Similarly, to display dynamic
fields and copy field rules, we can use the following endpoints
respectively, `http://localhost:8983/solr/gettingstarted/schema/dynamicfields`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":1},
  "dynamicFields":[{
      "name":"*_txt_en_split_tight",
      "type":"text_en_splitting_tight",
      "indexed":true,
      "stored":true},
    {
      "name":"*_descendent_path",
      "type":"descendent_path",
      "indexed":true,
      "stored":true},
    {
      "name":"*_ancestor_path",
      "type":"ancestor_path",
      "indexed":true,
      "stored":true},
    {
      "name":"*_txt_en_split",
      "type":"text_en_splitting",
      "indexed":true,
      "stored":true},
    {
      "name":"*_txt_rev",
      "type":"text_general_rev",
      "indexed":true,
      "stored":true},
    {
      "name":"*_phon_en",
      "type":"phonetic_en",
      "indexed":true,
      "stored":true},
    {
      "name":"*_s_lower",
      "type":"lowercase",
      "indexed":true,
      "stored":true},
...
...
```
:::

We can see a list of all copy fields using the following URL:
`http://localhost:8983/solr/gettingstarted/schema/copyfields`{.literal}.

We will see this response:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":0},
  "copyFields":[]}
```
:::

In order to see the schema name, use this URL:
`http://localhost:8983/solr/gettingstarted/schema/name`{.literal}.

This will display the schema name\'s details:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":0},
  "name":"default-config"}
```
:::

Similarly, you can see the schema version using the following
URL, `http://localhost:8983/solr/gettingstarted/schema/version`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":0},
  "version":1.6}
```
:::

This shows that the current schema version is `1.6`{.literal}.

Likewise, in order to see the unique key, we can use this
URL, `http://localhost:8983/solr/gettingstarted/schema/uniquekey`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":0},
  "uniqueKey":"id"}
```
:::

Here we see that the `uniqueKey`{.literal} of the schema is
`id`{.literal}.

Finally, in order to see the class name of the global similarity, we
have to use the following
URL, `http://localhost:8983/solr/gettingstarted/schema/similarity`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":0},
  "similarity":{
    "class":"org.apache.solr.search.similarities.SchemaSimilarityFactory"}}
```
:::

This shows that our schema uses `SchemaSimilarityFactory`{.literal} as
the global similarity.


[]{#ch03lvl1sec26}Deciphering schemaless mode {#deciphering-schemaless-mode .title style="clear: both"}
---------------------------------------------

</div>

</div>

------------------------------------------------------------------------
:::

Schemaless mode is used when[]{#id288339995 .indexterm} we want to
quickly create a useful schema by indexing sample data. It does not
involve any manual editing of the data.

All of its features are managed by `solrconfig.xml`{.literal}.

The features that we are particularly interested in are:

::: {.itemizedlist}
-   [**Managed schema**]{.strong}: All modifications in the schema are
    made via Solr API at runtime using `schemaFactory`{.literal}, which
    supports these changes.
-   [**Field value class guessing**]{.strong}: This is a technique of
    using a cascading set of parsers on fields that have not been seen
    before. It then guesses whether the field is an Integer, Long,
    Float, Double, Boolean, or Date.
:::

And finally used for automatic schema field addition that is based on
field value classes.

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec61}Creating a schemaless example {#creating-a-schemaless-example .title}

</div>

</div>
:::

All of the preceding three features are already[]{#id288340352
.indexterm} configured in the Solr bundle. To start using schemaless
mode, run the following command:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
bin/solr start -e schemaless
```
:::

This will start a single Solr server with the collection
`gettingstarted`{.literal}.

In order to see the schema fields, use the following
URL, `http://localhost:8983/solr/gettingstarted/schema/fields`{.literal}:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
{
  "responseHeader":{
    "status":0,
    "QTime":66},
  "fields":[{
      "name":"_root_",
      "type":"string",
      "docValues":false,
      "indexed":true,
      "stored":false},
    {
      "name":"_text_",
      "type":"text_general",
      "multiValued":true,
      "indexed":true,
      "stored":false},
    {
      "name":"_version_",
      "type":"plong",
      "indexed":false,
      "stored":false},
    {
      "name":"id",
      "type":"string",
      "multiValued":false,
      "indexed":true,
      "required":true,
      "stored":true}]}
```
:::

As you can see in the previous response, there are three predefined
fields available, named `_text_`{.literal}, `_version_`{.literal}, and
`_id_`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec62}Schemaless mode configuration {#schemaless-mode-configuration .title}

</div>

</div>
:::

We have already[]{#id288601061 .indexterm} discussed that the schemaless
mode provides three configuration elements. In the `_default`{.literal}
config set, we already have these three preconfigured. Let\'s see how to
make changes and get our own schemaless configuration.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec63}Managed schema {#managed-schema .title}

</div>

</div>
:::

The support for Managed schema is enabled[]{#id288601118 .indexterm} by
default if you don\'t specify anything in `solr-config.xml`{.literal}.

However, if you wish to make changes, then you can explicitly add your
own `schemaFactory`{.literal}, as follows:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<schemaFactory class="ManagedIndexSchemaFactory">
    <bool name="mutable">true</bool>
    <str name="managedSchemaResourceName">managed-schema</str>
</schemaFactory>
```
:::

In the previous code snippet, we have changed
`managedSchemaResourceName`{.literal} to `managed-schema`{.literal}.
:::

::: {.section lang="en" lang="en"}
::: {.titlepage}
<div>

<div>

### []{#ch03lvl2sec64}Field guessing {#field-guessing .title}

</div>

</div>
:::

If you open `solrconfig.xml`{.literal} for the new[]{#id288617168
.indexterm} schemaless project that you have created, you will see there
is a section for `UpdateRequestProcessorChain`{.literal}. This is
primarily used to automatically apply some operations to the documents
before they get indexed and helps in field guessing. We will see some of
the snippets from `solrconfig.xml`{.literal} now:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<updateProcessor class="solr.RemoveBlankFieldUpdateProcessorFactory" name="remove-blank"/>
```
:::

The previous plugin will remove any blanks from indexing:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<updateProcessor class="solr.ParseBooleanFieldUpdateProcessorFactory" name="parse-boolean"/>
 <updateProcessor class="solr.ParseLongFieldUpdateProcessorFactory" name="parse-long"/>
 <updateProcessor class="solr.ParseDoubleFieldUpdateProcessorFactory" name="parse-double"/>
 <updateProcessor class="solr.ParseDateFieldUpdateProcessorFactory" name="parse-date">
     <arr name="format">
         <str>yyyy-MM-dd'T'HH:mm:ss.SSSZ</str>
         <str>yyyy-MM-dd'T'HH:mm:ss,SSSZ</str>
         <str>yyyy-MM-dd'T'HH:mm:ss.SSS</str>
         <str>yyyy-MM-dd'T'HH:mm:ss,SSS</str>
         <str>yyyy-MM-dd'T'HH:mm:ssZ</str>
         <str>yyyy-MM-dd'T'HH:mm:ss</str>
         <str>yyyy-MM-dd'T'HH:mmZ</str>
         <str>yyyy-MM-dd'T'HH:mm</str>
         <str>yyyy-MM-dd HH:mm:ss.SSSZ</str>
         <str>yyyy-MM-dd HH:mm:ss,SSSZ</str>
         <str>yyyy-MM-dd HH:mm:ss.SSS</str>
         <str>yyyy-MM-dd HH:mm:ss,SSS</str>
         <str>yyyy-MM-dd HH:mm:ssZ</str>
         <str>yyyy-MM-dd HH:mm:ss</str>
         <str>yyyy-MM-dd HH:mmZ</str>
         <str>yyyy-MM-dd HH:mm</str>
         <str>yyyy-MM-dd</str>
     </arr>
 </updateProcessor>
```
:::

Here, you can see that we have added many update request processors to
parse various field types. In the case of dates, we can also specify
various patterns of a date that we can interpret:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
<updateProcessor class="solr.AddSchemaFieldsUpdateProcessorFactory" name="add-schema-fields">
 <lst name="typeMapping">
         <str name="valueClass">java.lang.String</str>
         <str name="fieldType">text_general</str>
         <lst name="copyField">
             <str name="dest">*_str</str>
             <int name="maxChars">256</int>
         </lst>
         <!-- Use as default mapping instead of defaultFieldType -->
         <bool name="default">true</bool>
     </lst>
     <lst name="typeMapping">
         <str name="valueClass">java.lang.Boolean</str>
         <str name="fieldType">booleans</str>
     </lst>
     <lst name="typeMapping">
         <str name="valueClass">java.util.Date</str>
         <str name="fieldType">pdates</str>
     </lst>
     <lst name="typeMapping">
         <str name="valueClass">java.lang.Long</str>
         <str name="valueClass">java.lang.Integer</str>
         <str name="fieldType">plongs</str>
     </lst>
     <lst name="typeMapping">
         <str name="valueClass">java.lang.Number</str>
         <str name="fieldType">pdoubles</str>
     </lst>
 </updateProcessor>
```
:::

In the preceding snippet, you can see that we have assigned a field type
to the fields that we parsed. The default field type is String, as you
can see highlighted in bold. We also made the copy field rule to copy
the data to `text_general_str`{.literal} with a max of `256`{.literal}
characters:

::: {.informalexample}
::: {.toolbar .clearfix}
Copy
:::

``` {.programlisting .language-markup}
 <updateRequestProcessorChain name="add-unknown-fields-to-the-schema" default="${update.autoCreateFields:true}"
 processor="uuid,remove-blank,field-name-mutating,parse-boolean,parse-long,parse-double,parse-date,add-schema-fields">
     <processor class="solr.LogUpdateProcessorFactory"/>
     <processor class="solr.DistributedUpdateProcessorFactory"/>
     <processor class="solr.RunUpdateProcessorFactory"/>
 </updateRequestProcessorChain>
```
:::

Finally, we add an `updateRequestProcessChain`{.literal} to add all the
predefined processors and make it default.



[]{#ch03lvl1sec27}Summary
-------------------------

</div>

</div>

------------------------------------------------------------------------
:::

In this chapter, we got an overview of how Solr works and saw schema
design. We then jumped into Solr field types and saw how to define
fields, copy fields, and create dynamic fields. We moved on to the
Schema API, and finally we saw what schemaless mode is all about.

In the next chapter, we will get our hands dirty and learn all about
analyzers, tokenizers, and filters.

