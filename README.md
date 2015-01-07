### *Marcin Ossowski*

----

# ElasticSearch Aggregations

# Table of content
- [System info](#system-info)
    - [Software](#software)
    - [Hardware](#hardware)
- [Preparing Data](#preparing-data)
    - [Preparing data for ElasticSearch standard](#preparing-data-for-elasticsearch-standard)
    - [Spliting data](#spliting-data)
    - [Importing data to ElasticSearch](#importing-data-to-elasticsearch)
    - [Checking number of records](#checking-number-of-records)
    - [Example record](#example-record)
    - [Example records from Kibana](#example-records-from-kibana)
- [Aggregations](#aggregations)
    - [The most common reasons for intervention](#the-most-common-reasons-for-intervention)
    - [Top 5 LSOA codes used in intervention](#top-5-lsoa-codes-used-in-intervention)
    - [Top 25 LSOA names in intervention](#top-25-lsoa-names-in-intervention)
    - [Top 5 Most popular location in intervention](#top-5-most-popular-location-in-intervention)
    
# System info

### Software

#### ElasticSearch
```bash
   ElasticSearch version 1.4.2 
   Kibana version 4.0.0 beta3
```
#### Operating system
```bash
   Windows 8.1 Pro
```

### Hardware

```bash
   CPU: Intel Core i5-2500K 3.30GHz
   RAM: Kingston HyperX 8GB 1600MHz DDR3 CL9
   HDD: Western Digital Caviar 500GB SATA/600 7200RPM 32MB Cache
```

# Preparing data

#### I used data about crimes in United Kingdom from [data.police.uk](http://data.police.uk/data/).

### Preparing data for ElasticSearch standard

```bash
  $ time jq --compact-output '{ "index" : { "_type" : "crimes" } }, .' crimes.json > final.json

  real    0m53.300s
  user    0m0.017s
  sys     0m0.017s
```

### Spliting data 

```bash
  $ time split -l 100000 final.json

  real    0m0.890s
  user    0m0.052s
  sys     0m0.786s
```

### Importing data to ElasticSearch

```bash
   $ time for i in x*; do curl -s -XPOST   localhost:9200/crimes/_bulk --data-binary @$i; done

   real    1m54.331s
   user    0m2.786s
   sys     0m4.432s
```

### Checking number of records

```bash
   $ curl -XGET 'http://localhost:9200/crimes/crimes/_count'

   {
       "count":1215671,
       "_shards" : {
            "total":5,
            "successful":5,
            "failed":0
       }
   }
```

### Example record

```bash
$ curl -XPOST 'localhost:9200/crimes/_search?pretty' -d '
{
    "query" : { "match_all" : { } },
    "size" : 1 
}'
```

```bash
{
    "index":
    {
        "_type":"crimes"
    }
}
{
    "Crime ID":"",
    "Month":"2010-12",
    "Reported by":"Bedfordshire Police",
    "Falls within":"Bedfordshire Police",
    "Longitude":"-0.407558",
    "Latitude":"51.896342",
    "Location":"On or near Fair Oak Drive",
    "LSOA code":"E01015775",
    "LSOA name":"Luton 012B",
    "Crime type":"Anti-social behaviour",
    "Last outcome category":"",
    "Context":""
}
```

### Example records from Kibana

![1](https://github.com/mossowski/NoSQL-egz/blob/master/images/kibanarecord.png)

# Aggregations

#### To create diagrams i used ElasticSearch Kibana version 4.0.0 beta3.

### The most common reasons for intervention

```bash
{
   "query" : { "match_all" : { } },
   "facets" : {
       "crime_type" : {
           "terms" : {
               "field" : "crime_type"
           }
       }
   }
}
```
![1](https://github.com/mossowski/NoSQL-egz/blob/master/images/kibanacrime.png)

### Top 5 LSOA codes used in intervention

```bash
{
   "query" : {
       "query_string" : {"query" : "*"}
   },
   "facets" : {
       "lsoa_code" : {
           "terms" : {
               "field" : "lsoa_code",
               "size" : 5
           }
       }
   }
}
```

#### Wynik

```bash
   facets: {
       lsoa_code: {
           _type: terms,
           missing: 16599,
           total: 1215671,
           other: 854539,
           terms: [ 
               {
                   term:  E01014370,
                   count: 77803
               },
               {
                   term:  E01014371,
                   count: 29176
               },
               {
                   term:  E01014407,
                   count: 24313
               },
               {
                   term:  E01014372,
                   count: 24313
               },
               {
                   term:  E01014689,
                   count: 19451
               }
           ]
       }
   }
}
```

![1](https://github.com/mossowski/NoSQL-egz/blob/master/images/kibanalsoa.png)

### Top 25 LSOA names in intervention

```bash
{
   "query" : {
       "query_string" : {"query" : "*"}
   },
   "facets" : {
       "lsoa_name" : {
           "terms" : {
               "field" : "lsoa_name",
               "size" : 25
           }
       }
   }
}
```

![1](https://github.com/mossowski/NoSQL-egz/blob/master/images/kibanalsoaa.png)

### Top 5 Most popular location in intervention

```bash
{
   "query" : {
       "query_string" : {"query" : "*"}
   },
   "facets" : {
       "location" : {
           "terms" : {
               "field" : "location",
               "size" : 5
           }
       }
   }
}
```

#### Wynik

```bash
   facets: {
       location: {
           _type: terms,
           missing: 16599,
           total: 1215671,
           other: 854539,
           terms: [ 
               {
                   term:  On or near Manvers Street,
                   count: 21882
               },
               {
                   term:  On or near St. James'S Parade,
                   count: 17019
               },
               {
                   term:  On or near High Street,
                   count: 17019
               },
               {
                   term:  On or near George Street,
                   count: 12157
               },
               {
                   term:  On or near Broad Street,
                   count: 12157
               }
           ]
       }
   }
}
```

![1](https://github.com/mossowski/NoSQL-egz/blob/master/images/kibanalocation.png)
