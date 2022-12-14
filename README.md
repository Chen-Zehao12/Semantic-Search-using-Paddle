# Semantic-Search-using-Paddle

![example](/imgs/example.gif)

## π¨ Language

* [English](/README.md)
* [δΈ­ζ](/README-zh.md)

## π Description

This code is used `Paddle` to do a `semantic search`.

There two types that you can use:

* `Monolingual` You can use monolingual models that trained on each languages, and search by setting the languague which needs to be show. Maybe this way can get a higher accuracy, but it is limited that if you select the language, it only shows the given language.
  * For Chinese text: `hfl/roberta-wwm-ext-large`
  * For English text: `ernie-2.0-large-en`
* `Multilingual` You can use multilingual models that trained on different languages together, and search without setting the language. In this way, it is more common for users to search something.
  * For Chinese and English text: `ernie-m-large`

You need to install `milvus` and `mysql` to store vector and other information that you need:

* For storing vector: `milvus` (Open-source, highly scalable, and blazing fast)
* For storing other information: `mysql` (of course, you can choose the database you like)

## β Environment

* It used `1 * NVIDIA Tesla V100 32G` to train model(Recommended). Ensure that CUDA is installed.
* Of course you can use CPU to train model

## π  Requirements

* Python 3.9
* paddlepaddle 2.3.1
  * If you need a `CPU only` version, please install the  this version
  * Else if you need a `GPU` version, please install the right version that based on your GPU and CUDA.
    For example: paddlepaddle-gpu==2.3.1.post112
* paddlenlp 2.3.4

If you want to deploy models, you also need to install:

* pymilvus 2.1.0
* pymysql 1.0.2
* fastapi 0.79
* uvicorn 0.18.2

## π Files

* There are four folders:
  * `1-preprocess` To preprocess the raw data into the data that models can receive
  * `2-pretrain` To pretrain the model, just like domain-adaptive pretraining. But it need higher GPUs
  * `3-finetune` To finetune the model, make it useful for your task
  * `4-deploy` To deploy the trained model on VPS, and set up API
* It used `Jupyter Notebook` to easily start. Of course you can convert `.ipynb` to `.py`
* The step is the index of the files
* For example, you will run the file started with `1-xxx.ipynb`, and then run the file started with `2-xxx.ipynb`
* The files in `3-finetune/models` , `4-deploy/search_engine/models` folders are fake files! You must get these files after saving your model

```
ββ1-preprocess                  # Step 1: preprocess
β  ββdata                         # Store raw data
β      ββChinese                    # Store raw Chinese text
β      ββEnglish                    # Store raw English text
β      ββmultilingual               # Store multilingual text
ββ2-pretrain                    # Step 2: pretrain
β  ββdata                         # Store data after preprocessing
β      ββChinese                    # Store Chinese text after preprocessing
β      ββEnglish                    # Store English text after preprocessing
β      ββmultilingual               # Store multilingual text after preprocessing
ββ3-finetune                    # Step 3: finetune
β  ββdata                         # Store data after preprocessing
β  β  ββChinese                     # Store Chinese text after preprocessing
β  β  ββEnglish                     # Store English text after preprocessing
β  β  ββmultilingual                # Store multilingual text after preprocessing
β  ββmodels                       # Store models after training
β      ββernie-m-large              # Store model after training
β      β  ββinfer_model               # Store static model
β      ββroberta-wwm-ext-large      # Store model after training
β          ββinfer_model              # Store static model
ββ4-deploy                      # Step 4: deploy
    ββmilvus                      # Milvus database, an empty folder, it will generate files after running docker compose
    ββmysql                       # MySQL database, an empty folder, it will generate files after running docker compose
    ββsearch_engine               # Search Engine
        ββ0-init_database           # To init Milvus and MySQL databases. For example: create table, collection and so on
        β  ββdata                     # Store data after preprocessing
        β      ββChinese                # Store Chinese text after preprocessing
        β      ββEnglish                # Store English text after preprocessing
        β      ββmultilingual           # Store multilingual text after preprocessing
        ββmodels                    # Store model that needed in deploy and API model
        β  ββChinese                  # Store Chinese monolingual model
        β  ββEnglish                  # Store English monolingual model
        β  ββmultilingual             # Store multilingual model
        ββrouters                   # Store routers
            ββsearch                  # Store search python scripts
            ββsen_to_vec              # Store sentence to vector python scripts
```

## π Data

* There are only some example text in data folder

* You need to convert your data into a `csv` file, which split by `\t` (in fact, it is called `tsv` file)

* example data (multilingual text):

  | publication_number_sear | title   | abstract_ab                                | ipc_main_stat |
  | ----------------------- | ------- | ------------------------------------------ | ------------- |
  | EN0008                  | title8  | Snap my psyche like a twig                 | H             |
  | CN0001A                 | ζ ι’1   | ζ©ι»θ²ηζ₯θ½ εζ²‘ε¨ζ΅·εΉ³ηΊΏ                  | A             |
  | ...                     | ...     | ...                                        | ...           |
  | CN0013A                 | ζ ι’13  | θΏεΊ§εεΈζζηζεΏ΅εεζ¬’                   | D             |
  | EN0011                  | title11 | Do you ever get a little bit tired of life | F             |

* You have to confirm that there is no `\t` in text and labels. `Important!!!`

* You don't need to split the data into train data and test data

## π― To Run (multilingual)

Maybe there are something you have to change. For example: path

### Step 1: preprocess

* Step 1(Optional): run `0-take_a_look.ipynb`. Take a look at the raw data
* Step 2: run `1-filter_field`. Filter your data and choose the fields that you need
* Step 3(Optional): run `2-data_visualization.ipynb`. Take a look at the data after preprocessing
* Step 4(Optional): run `3-data_concat.ipynb`. Run this if you need multilingual text

After running these, you can get the data after preprocessing. Please copy these files into below steps.

### Step 2: pretrain

This work takes a long time to pretrain. There is nothing in this step, because I have not time and GPUs to pretrain.

If you want to pretrain, you should take some work in this.

### Step 3: finetune

* Step 1: run `1.2-train_multilingual.ipynb`. Train the model and save model and params
  * If you use monolingual, please run `1.1-train_monolingual.ipynb`
* Step 2: run `2.2-to_static_multilingual.ipynb`. Get the infer model
  * If you use monolingual, please run `2.1-to_static_monolingual.ipynb`
* Step 3(Optional): run `3.2-infer_multilingual.ipynb`. A test to infer
  * If you use monolingual, please run `3.1-infer_monolingual.ipynb`

## π’ Deploy

After getting the infer model, you can deploy it by using FastAPI or other API framework.

Make sure you have get the `docker` and `docker compose`.

* Docker 20.10.17
* Docker Compose 2.6.0

cd to your deploy folder and make sure that the `docker-compose.yml` in it.

Please copy your files into `search_engine/models/Chinese` or `search_engine/models/English` or `search_engine/models/multilingual` and make them in your model folder like:

* model.get_pooled_embedding.pdiparams
* model.get_pooled_embedding.pdmodel
* sentencepiece.bpe.model (only for ernie-m)
* special_tokens_map.json
* tokenizer_config.json
* vocab.txt

Run: `docker compose up -d`

* Visit: `localhost:1234/docs` to read docs
* Visit: `localhost:9000` for Portainer
* Visit: `localhost:19000` for Attu

## π‘ Others

Docs about PaddlePaddle, PaddleNLP, FastAPI and Milvus

* [PaddlePaddle](https://www.paddlepaddle.org.cn/en)
* [PaddleNLP](https://paddlenlp.readthedocs.io/en/latest/)
* [FastAPI](https://fastapi.tiangolo.com/)
* [Milvus](https://milvus.io/)
