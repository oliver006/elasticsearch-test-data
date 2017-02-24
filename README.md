Elasticsearch For Beginners: Generate and Upload Randomized Test Data
====
Because everybody loves test data.


### Ok, so what is this thing doing?

`es_test_data.py` lets you generate and upload randomized test data to your ES cluster so you can start running queries, see what performance is like, and verify your cluster is able to handle the load.<br>
It allows for easy configuring of what the test documents look like, what kind of data types they include and what the field names are called.

## Cool, how do I use this? 

#### Prerequisites

Let's assume you have an Elasticsearch cluster running. If not, [set it up locally](http://ohardt.us/es-install) and point your browser to [http://localhost:9200](http://localhost:9200) to see if it's up.

Python and [Tornado](https://github.com/tornadoweb/tornado/) and [NumPy](http://www.numpy.org/) are used, run `pip install tornado numpy` to install Tornado and NumPy if you don't have it already.

#### Lets get started

It's as simple as this:

```
$ python es_test_data.py --es_url=http://localhost:9200
[I 150604 15:43:19 es_test_data:42] Trying to create index http://localhost:9200/test_data
[I 150604 15:43:19 es_test_data:47] Guess the index exists already
[I 150604 15:43:19 es_test_data:184] Generating 10000 docs, upload batch size is 1000
[I 150604 15:43:19 es_test_data:62] Upload: OK - upload took:    25ms, total docs uploaded:    1000
[I 150604 15:43:20 es_test_data:62] Upload: OK - upload took:    25ms, total docs uploaded:    2000
[I 150604 15:43:20 es_test_data:62] Upload: OK - upload took:    19ms, total docs uploaded:    3000
[I 150604 15:43:20 es_test_data:62] Upload: OK - upload took:    18ms, total docs uploaded:    4000
[I 150604 15:43:20 es_test_data:62] Upload: OK - upload took:    27ms, total docs uploaded:    5000
[I 150604 15:43:20 es_test_data:62] Upload: OK - upload took:    19ms, total docs uploaded:    6000
[I 150604 15:43:20 es_test_data:62] Upload: OK - upload took:    15ms, total docs uploaded:    7000
[I 150604 15:43:20 es_test_data:62] Upload: OK - upload took:    24ms, total docs uploaded:    8000
[I 150604 15:43:20 es_test_data:62] Upload: OK - upload took:    32ms, total docs uploaded:    9000
[I 150604 15:43:20 es_test_data:62] Upload: OK - upload took:    31ms, total docs uploaded:   10000
[I 150604 15:43:20 es_test_data:216] Done - total docs uploaded: 10000, took 1 seconds
[I 150604 15:43:20 es_test_data:217] Bulk upload average:           23 ms
[I 150604 15:43:20 es_test_data:218] Bulk upload median:            24 ms
[I 150604 15:43:20 es_test_data:219] Bulk upload 95th percentile:   31 ms
$
```
 
Without any command line options, it will generate and upload 1000 documents of the format

```
{
    "name":<<str>>,
    "age":<<int>>,
    "last_updated":<<ts>>
}
```
to an Elasticsearch cluster at `http://localhost:9200` to an index called `test_data`.

#### Not bad but what can I configure?

`python es_test_data.py --help` gives you the full set of command line options, here are the most important ones:

- `--es_url=http://localhost:9200` the base URL of your ES node, don't include the index name
- `--count=###` number of documents to generate and upload
- `--index_name=test_data` the name of the index to upload the data to. If it doesn't exist it'll be created with these options
  - `--num_of_shards=2` the number of shards for the index
  - `num_of_replicas=0` the number of replicas for the index
- `--batch_size=###` we use bulk upload to send the docs to ES, this option controls how many we send at a time
- `--force_init_index=False` if `True` it will delete and re-create the index
- `--dict_file=filename.dic` if provided the `dict` data type will use words from the dictionary file, format is one word per line. The entire file is loaded at start-up so be careful with (very) large files. You can download wordlists e.g.. from [here](http://ohardt.us/word-lists). 

#### What about the document format? 

Glad you're asking, let's get to the doc format.<br>
The doc format is configured via `--format=<<FORMAT>>` with the default being `name:str,age:int,last_updated:ts`.

The general syntax looks like this:

`<<field_name>>:<<field_type>>,<<field_name>>::<<field_type>>, ...`

For every document, `es_test_data.py` will generate random values for each of the fields configured.

Currently supported field types are:

- `bool` returns a random true or false
- `ts` a timestamp (in milliseconds), randomly picked between now +/- 30 days
- `ipv4` returns a random ipv4
- `tstxt` a timestamp in the "%Y-%m-%dT%H:%M:%S.000-0000" format, randomly picked between now +/- 30 days
- `int:min:max` a random integer between `min` and `max`. If `min and `max` are not provided they default to 0 and 100000
- `str:min:max` a word ( as in, a string), made up of `min` to `max` random upper/lowercase and digit characters. If `min` and `max` are optional, defaulting to `3` and `10`
- `words:min:max` a random number of `strs`, separated by space, `min` and `max` are optional, defaulting to '2' and `10`
- `dict:min:max` a random number of entries from the dictionary file, separated by space, `min` and `max` are optional, defaulting to '2' and `10`
- `text:words:min:max` a random number of words seperated by space from a given list of `-` seperated words, the words are optional defaulting to `text1` `text2` and `text3`, min and max are optional, defaulting to `1` and `1`



## todo
- document the remaining cmd line options
- more different format types
- ...

All suggestions, comments, ideas, pull requests are welcome!

