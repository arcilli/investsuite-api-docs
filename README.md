# InvestSuite API documentation

## About

The contents of the repository describe in detail by means of walkthroughs the Open API that InvestSuite maintains for developers to integrate with our products and services. 

As documentation it complements the API specifications at https://api.sandbox.investsuite.com/redoc aiming to present what is in there in a less formal, more instructive way. 

Our inspiration comes from the **Teach Don't Document** article by Delip Rao at https://deliprao.com/teach-dont-document. 
## Install

This API documentation is generated with the MkDocs static site generator. To run this repository locally, follow their instructions at https://www.mkdocs.org/. Or in case you have a recent version of Python 3 running as well as conda, then you can follow these steps:

```bash
~ git clone https://github.com/investsuite/investsuite-api-docs
~ cd investsuite-api-docs
~ conda create -n investsuite-api-docs-env pip python=3.8.5
~ conda activate investsuite-api-docs-env
~ pip install mkdocs
~ pip install mkdocs-material
~ mkdocs serve
INFO     -  Building documentation...
INFO     -  Cleaning site directory
INFO     -  Documentation built in 0.70 seconds
INFO     -  [17:14:14] Serving on http://127.0.0.1:8000/docs/
```