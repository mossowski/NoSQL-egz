### *Marcin Ossowski*

----

# Table of content
- [System info](#system-info)
    - [Software](#software)
    - [Hardware](#hardware)
- [Preparing Data](#preparing-data)
    - [Preparing data for ElasticSearch standard](#preparing-data-for-elasticsearch-standard)
    - [Spliting data](#spliting-data)
    - [Import data to ElasticSearch](#import-data-to-elasticsearch)
    - [Example record](#example-record)
    - [Example records from Kibana](#example-records-from-kibana)
- [Aggregations](#aggregations)
    - [The most common reasons for intervention](#the-most-common-reasons-for-intervention)
    - [Top 5 LSOA codes used in intervention](#top-5-lsoa-codes-used-in-intervention)
    - [Top 100 LSOA names in intervention](#top-100-lsoa-names-in-intervention)
    - [Top 5 Most popular location in intervention](#top-5-most-popular-location-in-intervention)
    
# System info

### Software

ElasticSearch
```bash
ElasticSearch version 1.4.2 
Kibana version 4.0.0 beta3
```
Operating system
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

I used data about crimes in United Kingdom from [data.police.uk](http://data.police.uk/data/).

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

### Import data to ElasticSearch

```bash
   $ time for i in x*; do curl -s -XPOST   localhost:9200/crimes/_bulk --data-binary @$i; done

   real    1m54.331s
   user    0m2.786s
   sys     0m4.432s
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

![1](https://github.com/mossowski/NoSQL-egz/blob/master/images/kibanalsoa.png)

### Top 100 LSOA names in intervention

```bash
{
   "query" : {
       "query_string" : {"query" : "*"}
   },
   "facets" : {
       "lsoa_name" : {
           "terms" : {
               "field" : "lsoa_name",
               "size" : 100
           }
       }
   }
}
```

![1](https://github.com/mossowski/NoSQL-egz/blob/master/images/kibanalsoaname.png)

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

![1](https://github.com/mossowski/NoSQL-egz/blob/master/images/kibanalocation.png)
