### *Marcin Ossowski*

----

# Table of content
- [System info](#systeminfo)
    - [Software](#software)
    - [Hardware](#hardware)
- [Preparing Data](#preparingdata)
    - [Preparing data for ElasticSearch standard](#preparingdataforelasticsearchstandard)
    - [Spliting data](#splitingdata)
    - [Example record](#examplerecord)

# System info

## Software

ElasticSearch
```bash
ElasticSearch version 1.4.2 
```
Operating system
```bash
Windows 8.1 Pro
```

## Hardware

```bash
   CPU: Intel Core i5-2500K 3.30GHz
   RAM: Kingston HyperX 8GB 1600MHz DDR3 CL9
   HDD: Western Digital Caviar 500GB SATA/600 7200RPM 32MB Cache
```

# Preparing data

I used data about crimes in United Kingdom from [data.police.uk](http://data.police.uk/data/).

## Preparing data for ElasticSearch standard

```sh
  $ time jq --compact-output '{ "index" : { "_type" : "crimes" } }, .' crimes.json > final.json

  real    0m53.300s
  user    0m0.017s
  sys     0m0.017s
```

## Spliting data 

```sh
  $ time split -l 100000 final.json

  real    0m0.890s
  user    0m0.052s
  sys     0m0.786s
```

## Example record

```sh
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
