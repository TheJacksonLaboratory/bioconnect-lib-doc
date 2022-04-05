# BioConnect Library
BioConnect library to use Apache Ranger Authorization and Logging

## View Logging
Open url: [http://34.148.227.60:5601](http://34.148.227.60:5601)

click on "Discover" icon on top left
<p align="center">
  <img src="https://raw.githubusercontent.com/TheJacksonLaboratory/bioconnect-lib-doc/main/img/kibana_view_log.png" alt="View Logging" width="315" height="271"/>
</p>


Make sure the correct index to view,  click on "change" to change index
<p align="center">
  <img src="https://raw.githubusercontent.com/TheJacksonLaboratory/bioconnect-lib-doc/main/img/kibana_view_log.png" alt="Select Index" width="315" height="271"/>
</p>


Make sure the correct index to view,  click on "change" to change index
<p align="center">
  <img src="https://raw.githubusercontent.com/TheJacksonLaboratory/bioconnect-lib-doc/main/img/kibana_view_log.png" alt="Select Index" width="315" height="271"/>
</p>

If index is not list, a new index pattern need to be created by clicking on "Management" icon to left bottom
<p align="center">
  <img src="https://raw.githubusercontent.com/TheJacksonLaboratory/bioconnect-lib-doc/main/img/kibana_create_index_pattern.png" alt="create Index Pattern" width="315" height="271"/>
</p>

## Installation
```
pip install -i https://test.pypi.org/simple/ bioconnect
```
more info see
[https://test.pypi.org/project/bioconnect](https://test.pypi.org/project/bioconnect)

## 

## Use BioConnect Ranger Authorization
```
from bioconnect.ranger.ranger_gcs import RangerAuthorizer

authorizer = RangerAuthorizer()

user_id = 'admin'
project = 'jax-cube-prd-ctrl-01'
bucket = 'jax-cube-prd-ctrl-01-project-test'
object_path = 'test-data'
response = authorizer.is_access_allowed(
    user_id = user_id,
    project = project,
    bucket = bucket,
    object_path = object_path
)

logger.info(f'response: {response}')

```


## Use BioConnect Logging
```
from bioconnect.log.log_handler import BioconnectLogHandler

logging.basicConfig(level=logging.INFO, handlers=[BioconnectLogHandler()])

```

## Envorinment Variable
```
# ranger
RANGER_URL=http://35.196.104.32:6080/
RANGER_USER=xxxx
RANGER_PASSWORD=xxxxx
APPLICATION=bioconnect

RANGER_GCS_SERVICE_NAME=gcs

# elasticsearch
ELASTIC_SEARCH_URL=http://34.148.246.30:9200
ELASTIC_SEARCH_USER=xxxx
ELASTIC_SEARCH_PASSWORD=xxxx

# logging
LOG_LEVEL=INFO
LOGGING_ELASTIC_INDEX_NAME = 'bioconnect-log'
# type in logging message
LOGGING_TYPE=bioconnect-lib-log
# modules for the logging message to elastic search,  comma separated, prefix for all sub modules
LOGGING_TO_ES_MODULES=bioconnect.ranger.ranger_gcs,bioconnect.ranger,__main__
```


## Build Package and test
```
poetry build
poetry config repositories.testpypi https://test.pypi.org/legacy/
poetry publish -r testpypi

pip install --index-url https://test.pypi.org/simple/ bioconnect-lib

https://test.pypi.org/project/bioconnect-lib/

poetry new test
poetry shell
poetry install
```
