# Feast Quickstart
If you haven't already, check out the quickstart guide on Feast's website (http://docs.feast.dev/quickstart), which 
uses this repo. A quick view of what's in this repository's `feature_repo/` directory:

* `data/` contains raw demo parquet data
* `feature_repo/feature_definition.py` contains demo feature definitions
* `feature_repo/feature_store.yaml` contains a demo setup configuring where data sources are
* `feature_repo/test_workflow.py` showcases how to run all key Feast commands, including defining, retrieving, and pushing features. 

You can run the overall workflow with `python test_workflow.py`.

## To move from this into a more production ready workflow:
> See more details in [Running Feast in production](https://docs.feast.dev/how-to-guides/running-feast-in-production)

1. First: you should start with a different Feast template, which delegates to a more scalable offline store. 
   - For example, running `feast init -t gcp`
   or `feast init -t aws` or `feast init -t snowflake`. 
   - You can see your options if you run `feast init --help`.
2. `feature_store.yaml` points to a local file as a registry. You'll want to setup a remote file (e.g. in S3/GCS) or a 
SQL registry. See [registry docs](https://docs.feast.dev/getting-started/concepts/registry) for more details. 
3. This example uses a file [offline store](https://docs.feast.dev/getting-started/architecture-and-components/offline-store) 
   to generate training data. It does not scale. We recommend instead using a data warehouse such as BigQuery, 
   Snowflake, Redshift. There is experimental support for Spark as well.
4. Setup CI/CD + dev vs staging vs prod environments to automatically update the registry as you change Feast feature definitions. See [docs](https://docs.feast.dev/how-to-guides/running-feast-in-production#1.-automatically-deploying-changes-to-your-feature-definitions).
5. (optional) Regularly scheduled materialization to power low latency feature retrieval (e.g. via Airflow). See [Batch data ingestion](https://docs.feast.dev/getting-started/concepts/data-ingestion#batch-data-ingestion)
for more details.
6. (optional) Deploy feature server instances with `feast serve` to expose endpoints to retrieve online features.
   - See [Python feature server](https://docs.feast.dev/reference/feature-servers/python-feature-server) for details.
   - Use cases can also directly call the Feast client to fetch features as per [Feature retrieval](https://docs.feast.dev/getting-started/concepts/feature-retrieval)




# Setting up Feast FeatureStore with example e-commerce data

* Download data from Kaggle https://www.kaggle.com/datasets/mkechinov/ecommerce-events-history-in-cosmetics-shop and unzip it under `data` directory.
* Run the `convert_csv2parquet.py` file to convert from CSV to parquet. This is our local data source.
* Use `feature_store.yaml` to set up the database backends for both online and offline store.
* Use `feature_definition.py` to provide feature definitions.
* Run `feast apply` to apply the offline store changes.
* Run `feast materialize 2019-10-01T00:00:00 2020-02-01T00:00:00 --views user_sessions` to upload the features from offline featurestore to online featurestore

Feast FeatureStore is not a data transformation framework. So it only very limited data transformation in real-time. The goal is always to create our own data transformation pipeline, loaded into the database through Feast (either offline or online directly), and then serve it real time by retrieving the most recent version of the feature.

# Running Feast in production

https://github.com/feast-dev/feast/blob/master/docs/how-to-guides/running-feast-in-production.md