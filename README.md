<div align="center">
  <h2>
    <a href="https://www.amazon.com/LLM-Engineers-Handbook-engineering-production/dp/1836200072/">LLM Engineer's Handbook: Master the art of engineering Large Language Models from concept to production</a>.
  </h2>
  <h3>
    Repository containing all the code for the LLM Engineer's Handbook.
  </h3>
</div>

<p align="center">
  <a href="https://www.amazon.com/LLM-Engineers-Handbook-engineering-production/dp/1836200072/">
    <img src="images/book_cover.png" alt="Book cover"  width="500">
  </a>
</p>

## Table of Contents

- [Dependencies](#dependencies)
- [Install](#install)
- [Project structure](#project-structure)
- [Set up local infrastructure (for testing and development)](#set-up-local-infrastructure-for-testing-and-development)
- [Set up cloud infrastructure (for production)](#set-up-cloud-infrastructure-for-production)
- [ZenML pipelines](#zenml-pipelines)
- [Inference](#inference)
- [Linting & Formatting (QA)](#linting--formatting-qa)

# Dependencies

## Local dependencies

To install and run the project locally, you need the following dependencies (the code was tested with the specified versions of the dependencies):

- [pyenv 2.3.36](https://github.com/pyenv/pyenv) (optional: for installing multiple Python versions on your machine)
- [Python 3.11](https://www.python.org/downloads/)
- [Poetry 1.8.3](https://python-poetry.org/docs/#installation)
- [Docker 27.1.1](https://docs.docker.com/engine/install/)
- [aws CLI 2.15.42](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [git 2.44.0](https://git-scm.com/downloads)

## Cloud services

The code also uses and depends on the following cloud services. For now, you don't have to do anything. We will guide you in the installation and deployment sections on how to use them:

- [HuggingFace](https://huggingface.com/): Model registry
- [Comet ML](https://www.comet.com/site/): Experiment tracker
- [Opik](https://www.comet.com/site/products/opik/): LLM evaluation and prompt monitoring
- [ZenML](https://www.zenml.io/): Orchestrator and artifacts layer
- [AWS](https://aws.amazon.com/): Compute and storage
- [MongoDB](https://www.mongodb.com/): NoSQL database
- [Qdrant](https://qdrant.tech/): Vector database
- [GitHub Actions](https://github.com/features/actions): CI/CD pipeline

In the [LLM Engineer's Handbook](https://www.amazon.com/LLM-Engineers-Handbook-engineering-production/dp/1836200072/), Chapter 2 will walk you through each tool, and in Chapters 10 and 11, you will have step-by-step guides on how to set everything you need.  

# Install

## Install Python 3.11 using pyenv (Optional)

If you have a different global Python version than Python 3.11, you can use pyenv to install Python 3.11 at the project level. Verify your Python version with:
```shell
python --version
```

First, verify that you have pyenv installed:
```shell
pyenv --version
# Output: pyenv 2.3.36
```

Install Python 3.11: 
```shell
pyenv install 3.11
```

From the root of your repository, run the following to verify that everything works fine:
```shell
pyenv versions
# Output: 
# system
# * 3.11.8 (set by <path/to/repo>/LLM-Engineers-Handbook/.python-version)
```

Because we defined a `.python-version` file within the repository, pyenv will know to pick up the version from that file and use it locally whenever you are working within that folder. To double-check that, run the following command while you are in the repository:
```shell
python --version
# Output: Python 3.11.8
```

If you move out of this repository, both `pyenv versions` and `python --version`, might output different Python versions.

## Install project dependences

The first step is to verify that you have Poetry installed:
```shell
poetry --version
# Output: Poetry (version 1.8.3)
```

Use Poetry to install all the project's requirements to run it locally. Thus, we don't need to install any AWS dependencies. Also, we install Poe the Poet as a Poetry plugin to manage our CLI commands and pre-commit to verify our code before committing changes to git:
```shell
poetry install --without aws
poetry self add 'poethepoet[poetry_plugin]==0.29.0'
pre-commit install
```

We run all the scripts using [Poe the Poet](https://poethepoet.natn.io/index.html). You don't have to do anything else but install Poe the Poet as a Poetry plugin, as described above: `poetry self add 'poethepoet[poetry_plugin]'`

To activate the environment created by Poetry, run:
```shell
poetry shell
```

## Set up .env settings file (for local development)

After you have installed all the dependencies, you must create and fill a `.env` file with your credentials to properly interact with other services and run the project.

First, copy our example by running the following:
```shell
cp .env.example .env # The file must be at your repository's root!
```

Now, let's understand how to fill in all the variables inside the `.env` file to get you started.

We will begin by reviewing the mandatory settings we must complete when working locally or in the cloud.

### OpenAI

To authenticate to OpenAI's API, you must fill out the `OPENAI_API_KEY` env var with an authentication token.

→ Check out this [tutorial](https://platform.openai.com/docs/quickstart) to learn how to provide one from OpenAI.

### HuggingFace

To authenticate to HuggingFace, you must fill out the `HUGGINGFACE_ACCESS_TOKEN` env var with an authentication token.

→ Check out this [tutorial](https://huggingface.co/docs/hub/en/security-tokens) to learn how to provide one from HuggingFace.

### Comet ML

Comet ML is required only during training.

To authenticate to Comet ML, you must fill out the `COMET_API_KEY` and `COMET_WORKSPACE` env vars with an authentication token and workspace name.

→ Check out this [tutorial](https://www.comet.com/docs/v2/api-and-sdk/rest-api/overview/) to learn how to fill the Comet ML variables from above.

### Opik

> Added soon


## Set up .env settings file (for deployment)

when deploying the project to the cloud, we must set additional settings for Mongo, Qdrant, and AWS.

If you are just working localy, the default values of these env vars will work out-of-the-box.

We will just highlight what has to be configured, as in **Chapter 11** of the [LLM Engineer's Handbook](https://www.amazon.com/LLM-Engineers-Handbook-engineering-production/dp/1836200072/) we provide step-by-step details on how to deploy the whole system to the cloud.

### MongoDB

We must change the `DATABASE_HOST` env var with the URL pointing to your cloud MongoDB cluster.

You can easily host a MongoDB cluster for free on their [MongoDB serverless platform](https://www.mongodb.com/).

→ Check out this [tutorial](https://www.mongodb.com/resources/products/fundamentals/mongodb-cluster-setup) to learn how to create a MongoDB cluster.


### Qdrant

Change `USE_QDRANT_CLOUD` to `True`, `QDRANT_CLOUD_URL` with the URL point to your cloud Qdrant cluster, and `QDRANT_APIKEY` with its API key.

To work with Qdrant cloud, the env vars will look like this:
```env
USE_QDRANT_CLOUD=true
QDRANT_CLOUD_URL="<your_qdrant_cloud_url>"
QDRANT_APIKEY="<your_qdrant_api_key>"
```

You can create a Qdrant cluster for free on [Qdrant's serverless platform](https://qdrant.tech/).

→ Check out this [tutorial](https://qdrant.tech/documentation/cloud/create-cluster/) to learn how to create a Qdrant cluster.

### AWS

For your AWS set-up to work correctly, you need the AWS CLI installed on your local machine and properly configured with an admin user (or a user with permission to create new SageMaker, ECR and S3 resources; using an admin user will make everything more straightforward).

In **Chapter 2**, section **Preparing for AWS**, we provided step-by-step instructions on how to install the AWS CLI, create an admin user on AWS and get an access key to set up the `AWS_ACCESS_KEY` and `AWS_SECRET_KEY` environment variables.

If you already have an AWS admin user in place, you have to configure the following env vars:
```shell
AWS_REGION="eu-central-1" # Change it with your AWS region.
AWS_ACCESS_KEY="<your_aws_access_key>"
AWS_SECRET_KEY="<your_aws_secret_key>"
```

> [!IMPORTANT]
> Find more configuration options in the [settings.py](https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/settings.py) file. Every variable from the `Settings` class can be configured through the `.env` file. 

# Project structure

Here are the project's core folders that we have to understand:
```bash
.
├── code_snippets/
├── configs/
├── llm_engineering/
│   ├── application/
│   ├── domain/
│   ├── infrastructure/
│   ├── model/
├── pipelines/
├── steps/
├── tools/
│   ├── run.py
│   ├── ml_service.py
│   ├── rag.py
```

`llm_engineering/`: The core Python package for the project, containing the main logic for Large Language Models (LLMs), Retrieval-Augmented Generation (RAG), and data collection.

Follows the Domain Driven Design (DDD) pattern, having the following structure:
- `domain/`: Defines all the entities, structures, and documents.
- `application/`: Application level code, such as crawlers, processing logic and RAG.
- `model/`: Training and inference code.
- `infrastructure/`: Infrastructure code related to AWS, Qdrant, MongoDB, FastAPI and others.

The code logic and imports flow as follows: `infrastructure` -> `model` -> `application` -> `domain`

`pipelines/` : Contains the ZenML ML pipelines, which serve as the entry point for all the ML pipelines. Coordinates the data processing and model training stages of the ML lifecycle.

`steps/`: Contains individual ZenML steps, which are reusable components for building and customizing ZenML pipelines. Steps perform specific tasks (e.g., data loading, preprocessing) and can be combined within the ML pipelines.

`tools/`: Utility scripts used to call the ZenML pipelines and inference code.

It contains the following scripts:
- `run.py`: Entry point script to run ZenML pipelines.
- `ml_service.py`: Starts the REST API inference server.
- `rag.py`: Demonstrates usage of the RAG retrieval module.

`configs/`: Contains ZenML YAML configuration files to control the execution of pipelines and steps.

`code_snippets/`: Holds independent code examples that can be executed independently.

# Set up local infrastructure (for testing and development)

When running the project locally, we host a MongoDB and Qdrant database using Docker. Also, a testing ZenML server is made available through their Python package.

> [!WARNING]
> You need Docker installed (v27.1.1 or higher)

For ease of use, you can start the whole local development infrastructure with the following command:
```shell
poetry poe local-infrastructure-up
```

Also, you can stop the ZenML server and all the Docker containers using the following command:
```shell
poetry poe local-infrastructure-down
```

> [!WARNING]  
> When running on MacOS, before starting the server, export the following environment variable:
> `export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES`
> Otherwise, the connection between the local server and pipeline will break. 🔗 More details in [this issue](https://github.com/zenml-io/zenml/issues/2369).
> This is done by default when using Poe the Poet.

Start the inference real-time RESTful API:
```shell
poetry poe run-inference-ml-service
```

### ZenML is now accessible at:

**Dashboard URL**: `localhost:8237`

Default credentials:
  - `username`: default
  - `password`: 

→🔗 [More on ZenML](https://docs.zenml.io/)

### Qdrant is now accessible at:

- **REST API URL**: `localhost:6333`
- **Dashboard URL**: `localhost:6333/dashboard`

→🔗 [More on Qdrant](https://qdrant.tech/documentation/quick-start/)

### MongoDB is now accessible at:

- **database URI**: `mongodb://llm_engineering:llm_engineering@127.0.0.1:27017`
- **database name**: `twin`

Default credentials:
  - `username`: llm_engineering
  - `password`: llm_engineering


# Set up cloud infrastructure (for production)

Here we will quickly present how to deploy the project to AWS and other serverless services. We won't go into the details (as everything is presented in the book) but only point out the main steps you have to go through:

## AWS SageMaker: Training & Inference

By this point, we expect you to have AWS CLI installed and your AWS CLI and project's env vars (within the `.env` file) properly configured with an AWS admin user.

To ensure best practices, we must create a new AWS user restricted to creating and deleting only resources related to AWS SageMaker. Create it by running:
```shell
poetry poe create-sagemaker-role
```
It will create a `sagemaker_user_credentials.json` file at the root of your repository with your new `AWS_ACCESS_KEY` and `AWS_SECRET_KEY` values. Replace them in your `.env` file. 

Now, we have to create an IAM execution role used by AWS SageMaker to access other AWS resources on our behalf. Create it by running:
```shell
poetry poe create-sagemaker-execution-role
```
It will create a `sagemaker_execution_role.json` file at the root of your repository with your new `AWS_ARN_ROLE` value. Add it to your `.env` file. 

Once you've updated the `AWS_ACCESS_KEY`, `AWS_SECRET_KEY`, and `AWS_ARN_ROLE` values in your `.env` file, you're ready to start using AWS SageMaker. This step is crucial to complete the setup.

> [!IMPORTANT]
> Step-by-step instructions on how to follow these steps are provided in **Chapter 10**, in the section **Implementing the LLM microservice using AWS SageMaker**.

### Training

We start the training pipeline through ZenML by running the following:
```shell
poetry poe run-training-pipeline
```

### Inference

For creating an AWS SageMaker Inference Endpoint, run:
```shell
poetry poe deploy-inference-endpoint
```
To test it out, run:
```shell
poetry poe test-sagemaker-endpoint
```
To delete it, run:
```shell
poetry poe delete-inference-endpoint
```

> [!IMPORTANT]
> Step-by-step instructions on following these steps are in **Chapter 10**, in the section **Implementing the LLM microservice using AWS SageMaker**.

## AWS: ML pipelines, artifacts and containers

The ML pipelines, artifacts and containers are deployed to AWS by leveraging ZenML's deployment features. Thus, you must create an account with ZenML Cloud and follow their guide on deploying a ZenML stack to AWS. Otherwise, we provide step-by-step instructions in **Chapter 11**, section **Deploying the LLM Twin's pipelines to the cloud** on what you must do.  

## Qdrant & MongoDB

We leverage Qdrant's and MongoDB's serverless options when deploying the project. Thus, you can either follow [Qdrant's](https://qdrant.tech/documentation/cloud/create-cluster/) and [MongoDB's](https://www.mongodb.com/resources/products/fundamentals/mongodb-cluster-setup) tutorials on how to create a freemium cluster for each or go through **Chapter 11**, section **Deploying the LLM Twin's pipelines to the cloud** and follow our step-by-step instructions.

## GitHub Actions

We use GitHub Actions to implement our CI/CD pipelines. To implement your own, you have to fork our repository and set the following env vars as Actions secrets in your forked repository:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_ECR_NAME`
- `AWS_REGION`

Also, we provide instructions on how to set everything up in **Chapter 11**, section **Adding LLMOps to the LLM Twin**.


# ZenML pipelines

All the ML pipelines will be orchestrated behind the scenes by [ZenML](https://www.zenml.io/).

The ZenML pipelines are the entry point for most processes throughout this project. They are under the `pipelines/` folder. Thus, when you want to understand or debug a workflow, starting with the ZenML pipeline is the best approach.

To see the pipelines running and their results:
- go to your ZenML dashboard
- go to the `Pipelines` section
- click on a specific pipeline (e.g., `feature_engineering`)
- click on a specific run (e.g., `feature_engineering_run_2024_06_20_18_40_24`)
- click on a specific step or artifact of the DAG to find more details about it

Now, let's explore all the pipelines you can run. From data collection to training, we will present them in their natural order to go through the LLM project end-to-end.

## Data pipelines

Run the data collection ETL:
```shell
poetry poe run-digital-data-etl
```

> [!WARNING]
> You must have Chrome (or another Chrome-based browser) installed on your system for LinkedIn and Medium crawlers to work (which use Selenium under the hood). Based on your Chrome version, the Chromedriver will be automatically installed to enable Selenium support. Another option is to run everything using our Docker image if you don't want to install Chrome. For example, to run all the pipelines combined you can run `poetry poe run-docker-end-to-end-data-pipeline`. Note that the command can be tweaked to support any other pipeline.

To add additional links to collect from, go to `configs/digital_data_etl_[author_name].yaml` and add them to the `links` field. Also, you can create a completely new file and specify it at run time, like this: `python -m llm_engineering.interfaces.orchestrator.run --run-etl --etl-config-filename configs/digital_data_etl_[your_name].yaml`

Run the feature engineering pipeline:
```shell
poetry poe run-feature-engineering-pipeline
```

Generate the instruct dataset:
```shell
poetry poe run-generate-instruct-datasets-pipeline
```

Generate the preference dataset:
```shell
poetry poe run-generate-preference-datasets-pipeline
```

Run all of the above compressed into a single pipeline:
```shell
poetry poe run-end-to-end-data-pipeline
```

## Utility pipelines

Export ZenML artifacts to JSON:
```shell
poetry poe run-export-artifact-to-json-pipeline
```

This will export the following ZenML artifacts to the `output` folder as JSON files (it will take their latest version):
    - raw_documents
    - cleaned_documents
    - instruct_datasets
    - preference_datasets

You can configure what artifacts to export by tweaking the `configs/export_artifact_to_json.yaml` configuration file.

## Training pipelines

Run the training pipeline:
```shell
poetry poe run-training-pipeline
```

> [!WARNING]
> For this to work, make sure you properly configured AWS SageMaker as described in [Set up cloud infrastructure (for production)](#set-up-cloud-infrastructure-for-production).

# Inference 

Call the RAG retrieval module with a test query:
```shell
poetry poe call-rag-retrieval-module
```

Start the inference real-time RESTful API:
```shell
poetry poe run-inference-ml-service
```

Call the inference real-time RESTful API with a test query:
```shell
poetry poe call-inference-ml-service
```

> [!WARNING]
> For the inference service to work, you must have the LLM microservice deployed to AWS SageMaker, as explained in the setup cloud infrastructure section.


# Linting & Formatting (QA)

Check or fix your linting issues:
```shell
poetry poe lint-check
poetry poe lint-fix
```

Check or fix your formatting issues:
```shell
poetry poe format-check
poetry poe format-fix
```

Check the code for leaked credentials:
```shell
poetry poe gitleaks-check
```

# License

This course is an open-source project released under the MIT license. Thus, as long you distribute our LICENSE and acknowledge our work, you can safely clone or fork this project and use it as a source of inspiration for whatever you want (e.g., university projects, college degree projects, personal projects, etc.).