# InvestSuite API documentation

## About

The contents of the repository describe in detail by means of walkthroughs the Open API that InvestSuite maintains for developers to integrate with our products and services. 

As documentation it complements the API specifications at https://api.sandbox.investsuite.com/redoc aiming to present what is in there in a less formal, more instructive way. 

Our inspiration comes from the **Teach Don't Document** article by Delip Rao at https://deliprao.com/teach-dont-document. 

## Features

- This is a static site generated by [MkDocs](https://www.mkdocs.org/) static site generator.
- Style tips are suggested by [Vale](https://vale.sh/).

## How to contribute?

### Set up MkDocs

Follow the instructions at https://www.mkdocs.org/. Or in case you have a recent version of Python 3 running as well as conda, then you can follow these steps:

```bash
~ git clone https://github.com/investsuite/investsuite-api-docs
~ cd investsuite-api-docs
~ conda create -n investsuite-api-docs-env pip python=3.8.5
~ conda activate investsuite-api-docs-env
~ pip install mkdocs
~ pip install mkdocs-material
~ pip install plantuml-markdown
~ mkdocs serve
INFO     -  Building documentation...
INFO     -  Cleaning site directory
INFO     -  Documentation built in 0.70 seconds
INFO     -  [17:14:14] Serving on http://127.0.0.1:8000/docs/
```

### Set up Vale (optional)

1. Install Vale on your machine, see See https://vale.sh/docs/vale-cli/installation/
1. Run `vale sync`, this will download the necessary style guides based on the `.vale.ini.` file in the root of this repo
1. For Visual Studio Code, install this plugin: https://github.com/errata-ai/vale-vscode

### Style guide

- Avoid the usage of 'you' as this is ambiguous. Instead, use InvestSuite and the Client, Customer, Client Middleware.
- Use 'Sentence case' in titles: use "Portfolio creation" i.o. "Portfolio Creation".
- Do not use 'a', 'an' in titles (eg. 'Create a portfolio' -> 'create portfolio')
- Entities are capitalized throughout the doc, eg. use 'the Optimization of a Portfolio is done'.

### Page skeleton
eg. see Transactions
- Refer to the Glossary
- Context: why you should read this page
- Concepts: newly introduced, used in this page
  
## Deployment

### Public: GitHub Pages

The public site is hosted on Github Pages, and deployed by a GitHub action.

### Staging: Azure Static Websites

The preview/staging environment is running on [Azure Static Websites](https://docs.microsoft.com/en-us/azure/static-web-apps/), and deployed by another GitHub action (strongly inspired by https://github.com/equinor/az-static-web-app-docs-template/)
