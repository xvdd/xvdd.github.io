---
layout: post
title:  "Convert a JSON array to CSV with jq"
date:   2022-08-12 23:13:55 +0800
categories: [linux]
tags: []
---

[`jq`](https://stedolan.github.io/jq/) is the go-to command-line tool to manipulate JSON data.

To share data collected through a REST API by email, I regularly have to convert JSON to tables, usually formatted in Excel.

jq can help automate the formatting, using the `csv` formatter (`tsv` is another option) that produce data easily imported in Excel.

## Prepare the data

A first step may be required to produce a flat (without sublevel) array of JSON objects of the same structure.

On my case, the input JSON is formatted as such:

```json
{
  "meta": {
    "status": "success"
  },
  "data": [
    {
      "id": 123,
      "reference": "ABC",
      "name": "Name",
      "status": "active",
      "expiration": "2022-12-31",
      "url": "https://some.url/ABC",
      "code": 1234,
      "customization": []
    },
    {
      "id": 456,
      "reference": "DEF",
      "name": "Name 2",
      "status": "active",
      "expiration": "2022-12-31",
      "url": "https://some.url/DEF",
      "code": 5678,
      "customization": []
    }
  ]
}
```
{: file="input.json" }

I'm only interested in the data section of the JSON and will remove _customization_ as it carries no information and the input of `jq` for `csv` formatting is expected to be flat.

The following line produces the expected input:

```console
$ cat input.json | jq -r '.data[] | {id, reference, name, status, expiration, url, code}' | jq -n '. |= [inputs]' > array.json
```

```json
[
  {
    "id": 123,
    "reference": "ABC",
    "name": "Name",
    "status": "active",
    "expiration": "2022-12-31",
    "url": "https://some.url/ABC",
    "code": 1234
  },
  {
    "id": 456,
    "reference": "DEF",
    "name": "Name 2",
    "status": "active",
    "expiration": "2022-12-31",
    "url": "https://some.url/DEF",
    "code": 5678
  }
]
```
{: file="array.json" }

The second `jq` command adds back the brackets after the navigation and filtering of the first.

## CSV formatting

The CSV formatting is done for the header row and the content of the objects:

```console
$ cat array.json | jq -r '(.[0]|keys_unsorted),.[]|map(.)|@csv'
"id","reference","name","status","expiration","url","code"
123,"ABC","Name","active","2022-12-31","https://some.url/ABC",1234
456,"DEF","Name 2","active","2022-12-31","https://some.url/DEF",5678
```

This output can be copy/pasted easily into Excel and formatted into a table.

## References

- [https://stackoverflow.com/questions/39139107/how-to-format-a-json-string-as-a-table-using-jq](https://stackoverflow.com/questions/39139107/how-to-format-a-json-string-as-a-table-using-jq)
- [https://unix.stackexchange.com/questions/569876/add-json-objects-to-array-using-jq](https://unix.stackexchange.com/questions/569876/add-json-objects-to-array-using-jq)

## Alternative with Excel

Microsoft Excel can import and manipulate JSON data using __Get & Transform__ (Power Query).