oai
================

[![Build Status](https://travis-ci.org/ropensci/oai.svg?branch=master)](https://travis-ci.org/ropensci/oai) 
[![Build status](https://ci.appveyor.com/api/projects/status/h5qu574ky0rk3xxv?svg=true)](https://ci.appveyor.com/project/sckott/oai) 
[![cran checks](https://cranchecks.info/badges/worst/oai)](https://cranchecks.info/pkgs/oai)
[![codecov.io](http://codecov.io/github/ropensci/oai/coverage.svg?branch=master)](http://codecov.io/github/ropensci/oai?branch=master) 
[![rstudio mirror downloads](http://cranlogs.r-pkg.org/badges/oai?color=2ED968)](https://github.com/metacran/cranlogs.app) 
[![cran version](http://www.r-pkg.org/badges/version/oai)](https://cran.r-project.org/package=oai) 
[![](https://badges.ropensci.org/19_status.svg)](https://github.com/ropensci/onboarding/issues/19)

`oai` is an R client to work with OAI-PMH (Open Archives Initiative Protocol for Metadata Harvesting) services, a protocol developed by the [Open Archives Initiative](https://en.wikipedia.org/wiki/Open_Archives_Initiative). OAI-PMH uses XML data format transported over HTTP.

OAI-PMH Info:

-   [Wikipedia](https://en.wikipedia.org/wiki/Open_Archives_Initiative_Protocol_for_Metadata_Harvesting)
-   [OAI V2 specification](http://www.openarchives.org/OAI/openarchivesprotocol.html)

`oai` is built on `xml2` and `httr`. In addition, we give back data.frame's whenever possible to make data comprehension, manipulation, and visualization easier. We also have functions to fetch a large directory of OAI-PMH services - it isn't exhaustive, but does contain a lot.

OAI-PMH instead of paging with e.g., `page` and `per_page` parameters, uses (optionally) `resumptionTokens`, optionally with an expiration date. These tokens can be used to continue on to the next chunk of data, if the first request did not get to the end. Often, OAI-PMH services limit each request to 50 records, but this may vary by provider, I don't know for sure. The API of this package is such that we `while` loop for you internally until we get all records. We may in the future expose e.g., a `limit` parameter so you can say how many records you want, but we haven't done this yet.

Install
-------

Install from CRAN

``` r
install.packages("oai")
```

Development version

``` r
devtools::install_github("ropensci/oai")
```

``` r
library("oai")
```

Identify
--------

``` r
id("http://oai.datacite.org/oai")
#>   repositoryName                     baseURL protocolVersion
#> 1   DataCite MDS http://oai.datacite.org/oai             2.0
#>           adminEmail    earliestDatestamp deletedRecord
#> 1 admin@datacite.org 2011-01-01T00:00:00Z    persistent
#>            granularity compression compression.1
#> 1 YYYY-MM-DDThh:mm:ssZ        gzip       deflate
#>                                      description
#> 1 oaioai.datacite.org:oai:oai.datacite.org:12425
```

ListIdentifiers
---------------

``` r
list_identifiers(from = '2011-05-01T', until = '2011-09-01T')
#> # A tibble: 888 x 6
#>                    identifier            datestamp setSpec setSpec.1
#>                         <chr>                <chr>   <chr>     <chr>
#>  1 oai:oai.datacite.org:32153 2011-06-08T08:57:11Z     TIB  TIB.WDCC
#>  2 oai:oai.datacite.org:32200 2011-06-20T08:12:41Z     TIB TIB.DAGST
#>  3 oai:oai.datacite.org:32220 2011-06-28T14:11:08Z     TIB TIB.DAGST
#>  4 oai:oai.datacite.org:32241 2011-06-30T13:24:45Z     TIB TIB.DAGST
#>  5 oai:oai.datacite.org:32255 2011-07-01T12:09:24Z     TIB TIB.DAGST
#>  6 oai:oai.datacite.org:32282 2011-07-05T09:08:10Z     TIB TIB.DAGST
#>  7 oai:oai.datacite.org:32309 2011-07-06T12:30:54Z     TIB TIB.DAGST
#>  8 oai:oai.datacite.org:32310 2011-07-06T12:42:32Z     TIB TIB.DAGST
#>  9 oai:oai.datacite.org:32325 2011-07-07T11:17:46Z     TIB TIB.DAGST
#> 10 oai:oai.datacite.org:32326 2011-07-07T11:18:47Z     TIB TIB.DAGST
#> # ... with 878 more rows, and 2 more variables: setSpec.2 <chr>,
#> #   setSpec.3 <chr>
```

Count Identifiers
-----------------

``` r
count_identifiers()
#>                           url    count
#> 1 http://oai.datacite.org/oai 11114343
```

ListRecords
-----------

``` r
list_records(from = '2011-05-01T', until = '2011-08-15T')
#> # A tibble: 109 x 44
#>                    identifier            datestamp setSpec setSpec.1
#>                         <chr>                <chr>   <chr>     <chr>
#>  1 oai:oai.datacite.org:32153 2011-06-08T08:57:11Z     TIB  TIB.WDCC
#>  2 oai:oai.datacite.org:32200 2011-06-20T08:12:41Z     TIB TIB.DAGST
#>  3 oai:oai.datacite.org:32220 2011-06-28T14:11:08Z     TIB TIB.DAGST
#>  4 oai:oai.datacite.org:32241 2011-06-30T13:24:45Z     TIB TIB.DAGST
#>  5 oai:oai.datacite.org:32255 2011-07-01T12:09:24Z     TIB TIB.DAGST
#>  6 oai:oai.datacite.org:32282 2011-07-05T09:08:10Z     TIB TIB.DAGST
#>  7 oai:oai.datacite.org:32309 2011-07-06T12:30:54Z     TIB TIB.DAGST
#>  8 oai:oai.datacite.org:32310 2011-07-06T12:42:32Z     TIB TIB.DAGST
#>  9 oai:oai.datacite.org:32325 2011-07-07T11:17:46Z     TIB TIB.DAGST
#> 10 oai:oai.datacite.org:32326 2011-07-07T11:18:47Z     TIB TIB.DAGST
#> # ... with 99 more rows, and 40 more variables: title <chr>,
#> #   creator <chr>, creator.1 <chr>, creator.2 <chr>, creator.3 <chr>,
#> #   creator.4 <chr>, creator.5 <chr>, creator.6 <chr>, creator.7 <chr>,
#> #   publisher <chr>, date <chr>, identifier.2 <chr>, identifier.1 <chr>,
#> #   subject <chr>, description <chr>, description.1 <chr>,
#> #   contributor <chr>, language <chr>, type <chr>, type.1 <chr>,
#> #   format <chr>, format.1 <chr>, rights <chr>, subject.1 <chr>,
#> #   relation <chr>, subject.2 <chr>, subject.3 <chr>, subject.4 <chr>,
#> #   setSpec.2 <chr>, setSpec.3 <chr>, format.2 <chr>, subject.5 <chr>,
#> #   subject.6 <chr>, subject.7 <chr>, description.2 <chr>,
#> #   description.3 <chr>, description.4 <chr>, description.5 <chr>,
#> #   title.1 <chr>, contributor.1 <chr>
```

GetRecords
----------

``` r
get_records(c("oai:oai.datacite.org:32255", "oai:oai.datacite.org:32325"))
#> $`oai:oai.datacite.org:32255`
#> $`oai:oai.datacite.org:32255`$header
#> # A tibble: 1 x 3
#>                   identifier            datestamp       setSpec
#>                        <chr>                <chr>         <chr>
#> 1 oai:oai.datacite.org:32255 2011-07-01T12:09:24Z TIB;TIB.DAGST
#> 
#> $`oai:oai.datacite.org:32255`$metadata
#> # A tibble: 1 x 12
#>                                                                         title
#>                                                                         <chr>
#> 1 Combinatorial and Algorithmic Aspects of Sequence Processing (Dagstuhl Semi
#> # ... with 11 more variables: creator <chr>, publisher <chr>, date <chr>,
#> #   identifier <chr>, subject <chr>, description <chr>, contributor <chr>,
#> #   language <chr>, type <chr>, format <chr>, rights <chr>
#> 
#> 
#> $`oai:oai.datacite.org:32325`
#> $`oai:oai.datacite.org:32325`$header
#> # A tibble: 1 x 3
#>                   identifier            datestamp       setSpec
#>                        <chr>                <chr>         <chr>
#> 1 oai:oai.datacite.org:32325 2011-07-07T11:17:46Z TIB;TIB.DAGST
#> 
#> $`oai:oai.datacite.org:32325`$metadata
#> # A tibble: 1 x 12
#>                                                              title
#>                                                              <chr>
#> 1 Frontmatter, Table of Contents, Preface, Conference Organization
#> # ... with 11 more variables: creator <chr>, publisher <chr>, date <chr>,
#> #   identifier <chr>, subject <chr>, description <chr>, contributor <chr>,
#> #   language <chr>, type <chr>, format <chr>, rights <chr>
```

List MetadataFormats
--------------------

``` r
list_metadataformats(id = "oai:oai.datacite.org:32348")
#> $`oai:oai.datacite.org:32348`
#>   metadataPrefix
#> 1         oai_dc
#> 2   oai_datacite
#> 3       datacite
#>                                                        schema
#> 1              http://www.openarchives.org/OAI/2.0/oai_dc.xsd
#> 2              http://schema.datacite.org/oai/oai-1.0/oai.xsd
#> 3 http://schema.datacite.org/meta/nonexistant/nonexistant.xsd
#>                             metadataNamespace
#> 1 http://www.openarchives.org/OAI/2.0/oai_dc/
#> 2     http://schema.datacite.org/oai/oai-1.0/
#> 3      http://datacite.org/schema/nonexistant
```

List Sets
---------

``` r
list_sets("http://oai.datacite.org/oai")
#> # A tibble: 2,143 x 2
#>                 setSpec
#>                   <chr>
#>  1           REFQUALITY
#>  2                 ANDS
#>  3      ANDS.REFQUALITY
#>  4            ANDS.C113
#>  5 ANDS.C113.REFQUALITY
#>  6            ANDS.C122
#>  7 ANDS.C122.REFQUALITY
#>  8            ANDS.C139
#>  9 ANDS.C139.REFQUALITY
#> 10            ANDS.C145
#> # ... with 2,133 more rows, and 1 more variables: setName <chr>
```

Examples of other OAI providers
-------------------------------

### Global Biodiversity Information Facility

Identify

``` r
id("http://api.gbif.org/v1/oai-pmh/registry")
#>   repositoryName                                 baseURL protocolVersion
#> 1  GBIF Registry http://api.gbif.org/v1/oai-pmh/registry             2.0
#>     adminEmail    earliestDatestamp deletedRecord          granularity
#> 1 dev@gbif.org 2007-01-01T00:00:01Z    persistent YYYY-MM-DDThh:mm:ssZ
#>                                                                                                                                                                                                                                                                                                                    description
#> 1 GBIF RegistryGlobal Biodiversity Information Facility Secretariat\n\t\tThe GBIF Registry — the entities that make up the GBIF network.\n\t\tThis OAI-PMH service exposes Datasets, organized into sets of country, installation and resource type.\n\t\tFor more information, see http://www.gbif.org/developer/registry\n\t
```

Get records

``` r
get_records(c("816f4734-6b49-41ab-8a1d-1b21e6b5486d", "95e3042f-f48d-4a04-8251-f755bebeced6"),
            url = "http://api.gbif.org/v1/oai-pmh/registry")
#> $`816f4734-6b49-41ab-8a1d-1b21e6b5486d`
#> $`816f4734-6b49-41ab-8a1d-1b21e6b5486d`$header
#> # A tibble: 1 x 3
#>                             identifier            datestamp
#>                                  <chr>                <chr>
#> 1 816f4734-6b49-41ab-8a1d-1b21e6b5486d 2017-03-08T15:04:24Z
#> # ... with 1 more variables: setSpec <chr>
#> 
#> $`816f4734-6b49-41ab-8a1d-1b21e6b5486d`$metadata
#> # A tibble: 0 x 0
#> 
#> 
#> $`95e3042f-f48d-4a04-8251-f755bebeced6`
#> $`95e3042f-f48d-4a04-8251-f755bebeced6`$header
#> # A tibble: 1 x 3
#>                             identifier            datestamp
#>                                  <chr>                <chr>
#> 1 95e3042f-f48d-4a04-8251-f755bebeced6 2017-08-14T10:26:13Z
#> # ... with 1 more variables: setSpec <chr>
#> 
#> $`95e3042f-f48d-4a04-8251-f755bebeced6`$metadata
#> # A tibble: 1 x 12
#>                                                                       title
#>                                                                       <chr>
#> 1 WIWO (NL) - Monitoring and breeding ecology of arctic birds at Medusa Bay
#> # ... with 11 more variables: publisher <chr>, identifier <chr>,
#> #   subject <chr>, source <chr>, description <chr>, type <chr>,
#> #   creator <chr>, date <chr>, language <chr>, coverage <chr>,
#> #   format <chr>
```

### Biodiversity Heritage Library

Identify

``` r
id("http://www.biodiversitylibrary.org/oai")
#>                                 repositoryName
#> 1 Biodiversity Heritage Library OAI Repository
#>                                   baseURL protocolVersion
#> 1 https://www.biodiversitylibrary.org/oai             2.0
#>                    adminEmail earliestDatestamp deletedRecord granularity
#> 1 oai@biodiversitylibrary.org        2006-01-01            no  YYYY-MM-DD
#>                                                        description
#> 1 oaibiodiversitylibrary.org:oai:biodiversitylibrary.org:item/1000
```

Get records

``` r
get_records(c("oai:biodiversitylibrary.org:item/7", "oai:biodiversitylibrary.org:item/9"),
            url = "http://www.biodiversitylibrary.org/oai")
#> $`oai:biodiversitylibrary.org:item/7`
#> $`oai:biodiversitylibrary.org:item/7`$header
#> # A tibble: 1 x 3
#>                           identifier            datestamp setSpec
#>                                <chr>                <chr>   <chr>
#> 1 oai:biodiversitylibrary.org:item/7 2016-07-13T09:13:41Z    item
#> 
#> $`oai:biodiversitylibrary.org:item/7`$metadata
#> # A tibble: 1 x 11
#>                                                                    title
#>                                                                    <chr>
#> 1 Die Musci der Flora von Buitenzorg : zugleich Laubmoosflora von Java /
#> # ... with 10 more variables: creator <chr>, subject <chr>,
#> #   description <chr>, publisher <chr>, contributor <chr>, date <chr>,
#> #   type <chr>, identifier <chr>, language <chr>, rights <chr>
#> 
#> 
#> $`oai:biodiversitylibrary.org:item/9`
#> $`oai:biodiversitylibrary.org:item/9`$header
#> # A tibble: 1 x 3
#>                           identifier            datestamp setSpec
#>                                <chr>                <chr>   <chr>
#> 1 oai:biodiversitylibrary.org:item/9 2016-07-13T09:13:41Z    item
#> 
#> $`oai:biodiversitylibrary.org:item/9`$metadata
#> # A tibble: 1 x 11
#>                                                                    title
#>                                                                    <chr>
#> 1 Die Musci der Flora von Buitenzorg : zugleich Laubmoosflora von Java /
#> # ... with 10 more variables: creator <chr>, subject <chr>,
#> #   description <chr>, publisher <chr>, contributor <chr>, date <chr>,
#> #   type <chr>, identifier <chr>, language <chr>, rights <chr>
```

Acknowledgements
----------------

Michal Bojanowski thanks National Science Centre for support through grant 2012/07/D/HS6/01971.

Meta
----

-   Please [report any issues or bugs](https://github.com/ropensci/oai/issues).
-   License: MIT
-   Get citation information for `oai` in R doing `citation(package = 'oai')`
-   Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.

[![ropensci\_footer](http://ropensci.org/public_images/github_footer.png)](http://ropensci.org)
