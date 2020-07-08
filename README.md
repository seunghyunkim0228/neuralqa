
## NeuralQA: A Usable Library for Question Answering on Large Datasets with BERT
[![License: MIT](https://img.shields.io/github/license/victordibia/neuralqa?style=flat-square)](https://opensource.org/licenses/MIT)


 
<img width="100%" src="https://raw.githubusercontent.com/victordibia/neuralqa/master/docs/images/manual.jpg">

NeuralQA (still in alpha) provides a [visual interface](https://victordibia.github.io/neuralqa/) for end-to-end  question answering (passage retrieval, query expansion, document reading, model explanation), on large datasets. Passage retrieval is implemented using ElasticSearch and Document Reading is implemented using pretrained BERT models via the Huggingface [transformers api](https://github.com/huggingface/transformers). 

<!-- An example query and response using a BERT model is shown below.

```
what is the sentence for arson crime?
```

```
BERT Answer: [1.01s] 18 years ’ imprisonment, but mitigated the sentence to 12 years because “ Defendant did not have any intent to injure the victim. ” See NMSA 1978, § 31 - 18 - 15. 1 ( 1979, as amended in 1993 ) ( allowing for mitigation of up to one - third of sentence, [0.26s] shooting at or from a motor vehicle
``` -->
 
### How Does it Work?

<img width="100%" src="https://raw.githubusercontent.com/victordibia/neuralqa/master/docs/images/architecture.png">

NeuralQA is comprised of several high level modules:

- **Retriever**: For each search query (question), scan an index (elasticsearch), and retrieve a list of candidate matched passages.

- **Document Reader**: For each retrieved passage, a BERT based model predicts a span that contains the answer to the question. In practice, retrieved passages may be lengthy and BERT based models can process a maximum of 512 tokens at a time. NeuralQA handles this in two ways. Lengthy passages are chunked into smaller sections with an configurable stride. Secondly, NeuralQA offers the option of extracting a subset of relevant snippets (RelSnip) which a BERT reader can then scan to find answers. Relevant snippets are portions of the retrieved document that contain exact match results for the search query. 

- **User Interface**: NeuralQA provides a visual user interface for performing queries (manual queries where question and context are provided as well as queries over a search index), viewing results and also sensemaking of results  (reranking of passages based on answer scores, highlighting keyword match, model explanations).  


<!-- 

## Candidate Document Retrieval
For this task, we will use elastic search (mostly for its clean python api, ease of use). Elasticsearch is a search engine based on the Lucene library. It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents. Other alternatives include Solr (also based on Lucene).

Elastic search uses the `BM25` algorithm by default for implementing similarity between text fields. We will also use the elastic search python client for elastic operations (create index, search queries).

## Document Reader
NeuralQA document reader is based on the huggingface library implementation. It has currently been tested with `distilbert` and `bert` (base, large) models that have been trained on the QA task. -->



## Usage

Create a folder you would like to use for NeuralQA. Run the following command line interface from within that folder.

```shell
pip3 install neuralqa
neuralqa ui --host localhost --port 4000
```

navigate to [http://127.0.0.1:4000/#/](http://127.0.0.1:4000/#/).

> Note: You can specify configuration for a retriever (host, port). To use NeuralQA with a retriever such as ElasticSearch, follow the [instructions here](https://www.elastic.co/downloads/elasticsearch) to download, install, and launch a local elasticsearch instance. 

## Configuration [In Progress]
Neuralqa provides an interface to specify properties of each module (ui, retriever, reader, expander) via a [yaml configuration](neuralqa/config_default.yaml) file. When you launch the ui, you can specify the path to your config file `--config-path`. If this is not provided, NeuralQA will search for a config.yaml in the current folder or create a [default copy](neuralqa/config_default.yaml)) in the current folder. Sample configuration for the UI is shown below:

```yaml
ui:
  queryview:
    intro:
      title: "NeuralQA: Question Answering on Large Datasets"
      subtitle: "Subtitle of your choice"
    views:    # select sections of the ui to hide or show
      intro: True
      advanced: True
      samples: False
      passages: True
      explanations: True
      allanswers: True
    options:  # values for advanced options
      model:  # list of models the user can select from
        title: QA models
        selected: distilbertsquad2
        options:
          - name: DistilBERT SQUAD2
            value: distilbertsquad2
          - name: BERT SQUAD2
            value: bertsquad2
      index: # search indices the user can select from
        title: Search Index
        selected: manual
        options:
          - name: Manual
            value: manual
          - name: Case Law
            value: cases 
      stride: ..
      maxpassages: ..
      highlightspan: ..

  header: # header tile for ui
    appname: NeuralQA
    appdescription: Question Answering on Large Datasets
```



 