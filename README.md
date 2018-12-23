# wdps2018
Web Data Processing Systems 2018 (VU course XM_40020)



# Assignment 1: Large Scale Entity Linking
For this assignment we had to perform [Entity Linking](https://en.wikipedia.org/wiki/Entity_linking) on a collection of web pages. In order to do this we divided the work into two parts. In the first part we extracted the candidate entities. In the second part we linked these entities to the entities in the knowledge base. 

1) Reading the WARC file from the HDFS: To read the WARC files from HDFS, we used the function newAPIHadoopFile together with a custom delimiter which we set to: "WARC/1.0". This gives us as a RDD containing single WARC records as elements as a result, which can then be parsed and cleaned.

2) Cleaning the HTML, in order to be able to run the Stanford NER tagger, of the StanfordCoreNLP library. (https://stanfordnlp.github.io/CoreNLP/index.html)

3) Entity linking, using ElasticSearch and knowledge base RDF - queries. 

# Assignment 2: Creating a knowledgebase from UrbanDictionary.com
Text written on the internet is often not as well written as text on Wikipedia or in newspapers the Wall Street Journal. However, these are often used in information extraction methods on the web. This can cause some methods to perform to have suboptimal performences when they come accross slang terms. People on the internet often use slang terms which are difficult to disambiguate using standard methods.  To our knowledge there is no such thing as a knowlegdebase for slang term. 

We therefore constructed a knowledge base of words on UrbanDictionary.com to enhance the results of our entity linking code. We call this knowledge UrbanData. Words that cannot be found in Trident will be looked up in UrbanData. Furthermore we created a directed graph of the data that shows the relations between the term. We did this to display the potential of UrbanData as a knowledgebase. Since the data is very large we only used a subset of the data. We got the data from [Matt Bierner](https://github.com/mattbierner/urban-dictionary-entry-collector). 

To run the code first download all four data sets from his [GitHub repository](https://github.com/mattbierner/urban-dictionary-entry-collector).
## Step 1: The knowledgebase

We run the UrbanData.py to get the 1% of the most popular words. These words are stored in Json format. Since this is only a test version we made it so that there is only one instance of each word in the json file. The json file is than added to our SPARQL query along side Trident. Instead of of a Trident identifier it will return the 'defid'. This identifier refers to the specific definition in UrbanData.

## Step 2: The knowledge graph


## Running the solution

- To run the solution, pull the source code and locate the run_final.sh file
- Run this bash script, and it will walk through the different programs and functions. 
- It will finish and an output of the F1 score will be displayed





The first assignment for this course is to perform [Entity Linking](https://en.wikipedia.org/wiki/Entity_linking) on a collection of web pages. Your solution should be scalable and accurate, and conform to the specifications below. You should work in groups of 3 or 4 people, and use a version control system that we can access. After forming a group, set up a private version control repository using [BitBucket](https://bitbucket.org) or [Github](http://github.com) and give me access (my username is bennokr on both websites). If you need help with this, contact me. You can use *any existing languages or tools you want*, as long as it's easy for us to run it on the DAS-4 cluster. Of course, your solution is not allowed to call web services over the internet. You are encouraged to use the technologies covered in the lectures.

Your solution should be runnable trough a bash script with the command line options specified below. An example of some dummy starter code is available in this repository.

The input data is a gzipped [WARC file](https://en.wikipedia.org/wiki/Web_ARChive), and the ouput is a three-column tab-separated file with document IDs, entity surface forms (like "Berlin"), and Freebase entity IDs (like "/m/03hrz"). There is a sample file of the input (warc) and output (tsv) formats in the `data` directory. Your program must create an output for the *full web archive file* on the cluster. Your program must be runnable on the [DAS-4 cluster](https://www.cs.vu.nl/das4/) using a bash script, and you should provide a README file with a description of your approach. For example, your program could be run using the command `bash run.sh "hdfs://user/yourname/input.warc.gz" > output.tsv`.

You will be graded based on whether your solution conforms to the assignment, on its scalability (e.g. using hadoop/spark) and on its [F1 score](https://en.wikipedia.org/wiki/F1_score). A script to calculate this score using gold standard data and the prediction data is in the same folder as the format sample data. An input file for you to work with is on [the Hadoop file system of DAS-4](https://www.cs.vu.nl/das4/hadoop.shtml), at `hdfs:///user/bbkruit/sample.warc.gz` . You can view it using `hdfs dfs -cat hdfs:///user/bbkruit/sample.warc.gz | zcat | less` (see also https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/FileSystemShell.html).

We have set up two REST services for you to use on the DAS-4 cluster. You can start both of them on DAS-4 worker nodes with the code in the test scripts (`test_elasticsearch.sh` and `test_sparql.sh`). One is an [Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/index.html) instance that contains labels for Freebase IDs. It can be accessed from the command line like this: `curl "http://10.149.0.127:9200/freebase/label/_search?q=obama"` . The other is a SPARQL endpoint that can be accessed like this: `curl -XPOST -s 'http://10.141.0.11:8082/sparql' -d "print=true&query=SELECT * WHERE { ?s ?p ?o . } LIMIT 10"`.  We have loaded DBpedia, YAGO, Freebase and Wikidata. To experiment with some sparql examples, see https://query.wikidata.org/ . Both services return JSON. Because Freebase was integrated into the Google Knowledge Graph, you can look up IDs on Google using URLs like this: [http://g.co/kg/m/03hrz].


# Frequently Asked Questions

## How do we use a python virtual environment in Spark YARN cluster mode?
I have added `spark_sample_venv.sh`, which shows how to package up a virtual environment for cluster usage, inspired by [this webpage](http://henning.kropponline.de/2016/09/17/running-pyspark-with-virtualenv/) that has more information. The crucial step is to make it "relocatable", which turns all absolute paths into relative ones.

## How can we get more results from Freebase?
You can increase the number of results with the "size" parameter (see [Elasticsearch documentation](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/index.html)), and you can look up which entity is probably the Obama that is meant by querying the SPARQL endpoint (e.g. which entity has the most facts about it). E.g. `curl -s "http://10.149.0.127:9200/freebase/label/_search?q=obama&size=1000"` .

## Why doesn't this SPARQL query work?
Not all SPARQL features are implemented in Trident. In particular, string filtering functions are not present (such as `langMatches`). Instead, try to write SPARQL queries with possibly many results, and filter them in your own code.

## What should we write in the README of our submission?
Please describe briefly how your system works, which existing tools you have used and why, and how to run your solution.

## We have reached out disk quota on DAS-4, what do we do?
You should always use the larger scratch disk on `/var/scratch/wdps18XX`.

## Should we detect entities in non-English text?
No, you only have to detect entities in English text.


