# Variants annotation data API

## 1. Constraints

### 1.1 Iterms per Request

The maxium mutation number returned per request is 10,000. Any result larger than this number will get a link to get the result for next page.


### 1.2 IP address

All request should from USC inner IP address.



## 2. Responses
API sends a response as a JSON object.

~~~
{
   "status": "success/fail"
   "data": [ ... ],
   "format": "...",
   "header":[...],
   "next_page":""
}
~~~
## 3. Queries

The following table lists all API endpoints currently available at 

`/`

|API endpoint| Description|
|------------|---------|
|`/`| Retrieve meta-information about dataset.|
|`/header/<string:dataset name>`|Get all headers name and id for a dataset|
|`/anno_tree/<string:dataset name>`|Get structured headers name and id for a dataset|
|`/origin/<string:dataset name>`| Retrieve  links for download origin files.|
|`/variant/<string:dataset name>`|Get single variant by chromosomal position or identifier.|
|`/region/<string:dataset name>`|Get all variants within a chromosomal region.|
|`/gotopage/<string:query id>/<int:page number>`|Get the data for a certain query of certain page number.|
|`/gene`|Get the genome interval for a gene, and all variants within this interval.|
|`/batch/<string:dataset name>`|Get all variants in a region. Store the result in a temporary file and return the link for that file.|
### 3.1 Get annotation headers


#### 3.1.1

To get annotation headers, send `GET` request to 
/header/<string:dataset name>

e.g. `/header/HRC`

**Return example:** 

~~~
{ 
	4:"ANNOVAR_ensembl_Effect",
	5:"ANNOVAR_ensembl_Transcript_ID",
	...
}
~~~

#### 3.1.2 Structured Headers
To get structured annotation headers, send `GET` request to 
/anno_tree/<string:dataset name>

e.g. `/anno_tree/HRC`

**Return example:** 


~~~
{"header_tree_array":[
	{"detail":"",
	 "id":375,
	 "name":"1000Gp3",
	 "parent_id":0},
	 ...
 ]
}
	 
~~~

#### 3.2 Get Original Data Files

To download all original data files, request the download url by 

`/origin/<string:dataset name>`

**Return example:**

~~~
{ 
	4:"ANNOVAR_ensembl_Effect",
	5:"ANNOVAR_ensembl_Transcript_ID",
	...
}
~~~


### 3.3 Region query

####3.3.1
To query all variants within a region, send `GET` request to 

`/region/<string:dataset name>`.

 The following table lists all supported parameters.

|Parameter | Required | Description |
|------------|---------|----|
|`chrom `| Yes|Chromosome name.|
|`start`| Yes |Chromosomal start position in base-pairs.|
|`end`| Yes |Chromosomal end position in base-pairs.|
|`headers`|Optional|Header ids, seperated by %20|
**Examples:**

`/region/HRC?chrom=18&start=10&end=50000&headers=1%202%203%204%205`

~~~
{
  "data": [
    [
      "18",
      "10636",
      "A",
      "C",
      "upstream|upstream|downstream|downstream",
      "ENST00000572573|ENST00000575820|ENST00000572062|ENST00000572608"
    ],
    [
      "18",
      "10644",
      "C",
      "G",
      "upstream|upstream|downstream|downstream",
      "ENST00000572573|ENST00000575820|ENST00000572062|ENST00000572608"
    ],
    ...
    
  ],
  "format": "json",
  "headers": {
    "0": "chr",
    "1": "pos",
    "2": "ref",
    "3": "alt",
    "4": "ANNOVAR_ensembl_Effect",
    "5": "ANNOVAR_ensembl_Transcript_ID"
  },
  "next_page": "http://127.0.0.1:5000/nextpage/ca2c39d1-6dfd-4c11-8292-478298a7e9ed",
  "page_id": "ca2c39d1-6dfd-4c11-8292-478298a7e9ed",
  "page_info": {
    "page_num": 1,
    "page_size": 100,
    "total_page": 3
  }
}
~~~

####3.3.2 Pagination

To get certain page of a query, retrieve it by `page_id`. Send `GET` request to 
`/gotopgae/<string:page id>/<int:page number>`

**Examples:**

`/gotopage/ca2c39d1-6dfd-4c11-8292-478298a7e9ed/2`

**Response**

Same as 3.1.1

#### 3.3.3 File Download

Two step request shuold be sent to get the result storing in a file, and get the URL for that file.

1. send a normal region query request and get a `page_id`
2. send query `page_id` to `/total_res/<pid> `

	e.g. `/total_res/e16fa443-cc46-4737-8016-9f96054d1e8e`
   
   It will return a URL inside a json.
   
   e.g. `"{"url":"/download/tmp/7fbf1b2d-d97f-4047-9e57-8008d0b1da04"}"`
 

### 3.4 Single variant query
To query a single variant, send `GET` request to 

`/rs/<rsid>` for rs id or `/variant/<variant id>` for variant query. Variant id is in format `contig:pos:ref:alt`. No additional parameters support.

**Examples:**

`/rs/rs111739080`

`/variant/18:10636:A:C`

### 3.5 Gene query

`/gene/<dataset>`

e.g.
`/gene/HRC?gene=NP_001073678`

Other parameters are the same with region query.

### 3.6 Batch query
To query a batch of variants, send `pos` request to 

`/batch/<string:dataset name>` 

Parameters are the same with region query.


## 4. R package
todo
### 4.1 R package Installation
### 4.2 R package Usage

