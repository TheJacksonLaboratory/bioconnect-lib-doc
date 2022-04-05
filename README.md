# BioConnect Library
BioConnect library to use Apache Ranger Authorization and Logging

## View BioConnect Logging
Open url: [http://34.148.227.60:5601](http://34.148.227.60:5601)

click on "Discover" icon on top left
<p align="center">
  <img src="https://raw.githubusercontent.com/TheJacksonLaboratory/bioconnect-lib-doc/main/img/kibana_view_log.png" alt="View Logging" width="315" height="271"/>
</p>


select index to view,  click on "change" to change index
<p align="center">
  <img src="https://raw.githubusercontent.com/TheJacksonLaboratory/bioconnect-lib-doc/main/img/kibana_view_select_index.png" alt="Select Index" width="315" height="271"/>
</p>


Select the time frame, and click on "Sarch" field to filter fields

<p align="center">
  <img src="https://raw.githubusercontent.com/TheJacksonLaboratory/bioconnect-lib-doc/main/img/kibana_view_select_timeframe.png" alt="Select Index" width="315" height="271"/>
</p>

If index is not list, a new index pattern need to be created by clicking on "Management" icon to left bottom
<p align="center">
  <img src="https://raw.githubusercontent.com/TheJacksonLaboratory/bioconnect-lib-doc/main/img/kibana_create_index_pattern.png" alt="create Index Pattern" width="315" height="271"/>
</p>

## Installation  (todo: change after releasing to prod pypi)
```
pip install -i https://test.pypi.org/simple/ bioconnect
```
for more info see
[https://test.pypi.org/project/bioconnect](https://test.pypi.org/project/bioconnect)  




## Config Logging in Python Application
import and config

```
import logging
from bioconnect_lib.log.log_handler import BioconnectLogHandler

logging.basicConfig(level="INFO", handlers=[BioconnectLogHandler()])
```

use the logging as usual

```
logger = logging.getLogger(__name__)

logger.info("logging info")
# add more info from Django request
logger.info("logging info with more tag info", extra=BioconnectLogHandler.djang_request_to_log_extra(request))

# log error
try:
    response = authorizer.is_access_allowed()
except Exception as e:
    logger.error("Failed to authorize", exc_info=True)
```


## Config Logging in Django Application
only file to change is: settings.py

add import for the BioConnect logging handler 

```
from bioconnect_lib.log.log_handler import BioconnectLogHandler
```

add logging config to

```
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'simple': {
            'format': '{levelname} {asctime} {name}: {message}',
            'style': '{',
        }
    },    
    'handlers': {
        'bioconnect-handler': {
            'class': 'bioconnect_lib.log.log_handler.BioconnectLogHandler',
            'formatter': 'simple'
        }
    },
    'loggers': {
        '': {
            'handlers': ['bioconnect-handler'], 
            'level': 'INFO',
            'propagate': False,
        },
    },
}
```

use the logging as usual with some convenient methods

```
logger = logging.getLogger(__name__)

# add more info from Django request
logger.info("logging info with more tag info", extra=BioconnectLogHandler.djang_request_to_log_extra(request))
```


## View Ranger Policy and Audit
Open url: [http://35.196.104.32:6080](http://35.196.104.32:6080)


## Config Ranger Authorization in Python Application
for Google Cloud Storage,

```
from bioconnect_lib.ranger.ranger_gcs import RangerGCSAuthorizer


authorizer = RangerGCSAuthorizer()
user_id = 'admin'
project = 'jax-cube-prd-ctrl-01'
bucket = 'jax-cube-prd-ctrl-01-project-test'
object_path = 'test-data'
try:
    response = authorizer.is_access_allowed(
        user_id = user_id,
        project = project,
        bucket = bucket,
        object_path = object_path
    )
    logger.info(f'response: {response}')
except Exception as e:
    logger.error("Failed to authorize", exc_info=True)
```

## Config Ranger Authorization in Django Application View
for Google Cloud Storage,
add import and create a base view class

```
from django.contrib.auth.decorators import user_passes_test
from django.utils.decorators import method_decorator
from bioconnect_lib.ranger.ranger_gcs import ranger_gcs_is_access_allowed


class RangerMixin(object):
    @method_decorator(user_passes_test(ranger_gcs_is_access_allowed))
    def dispatch(self, *args, **kwargs):
        return super(RangerMixin, self).dispatch(*args, **kwargs)
```

use the base view class, just add "RangerMixin" to the first parameter of the view constructor

```
class PackageViewSet(RangerMixin, viewsets.ModelViewSet):
```


## Envorinment Variable
```
# ranger
RANGER_URL=http://35.196.104.32:6080/
RANGER_USER=xxxx
RANGER_PASSWORD=xxxxx
RANGER_APPLICATION=bioconnect

RANGER_GCS_SERVICE_NAME=gcs

# elasticsearch
ELASTIC_SEARCH_URL=http://34.148.246.30:9200
ELASTIC_SEARCH_USER=xxxx
ELASTIC_SEARCH_PASSWORD=xxxx

# logging
LOGGING_ELASTIC_INDEX_NAME = 'bioconnect-log'
# type in logging message
LOGGING_TYPE=bioconnect-lib-log
# modules for the logging message to elastic search,  comma separated, prefix for all sub modules
LOGGING_TO_ES_MODULES=bioconnect.ranger.ranger_gcs,bioconnect.ranger,__main__
```


## Dev/Build Package and test
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
