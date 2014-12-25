logging
===

## Stop using PRINT , and let's use python standard logging module

[logging](https://docs.python.org/2/library/logging.html) is a standard module, which is well designed for logging.
	
####print is not a good idea

* when debugging, you can't control which logs to print out and which logs not to print 
* you may forget to delete these unused prints after debugging

####so let's start logging

```python
import logging`

#choose which level of logging you want to print to stdout and initializa you logger instance:
#logging level: debug, info, warning, error and critical

logging.basicConfig(level=logging.INFO)

#the variable __name__ is current module name in Python. 
#when calling logger.getLogger(__name__) in a module “foo.bar.my_module”, 
#then it is logger.getLogger(“foo.bar.my_module”). 
#When you need to configure the logger, you can configure to “foo”, 
#then all modules in “foo” packages shares same configuration
logger = logging.getLogger(__name__)

#set logging level
#logger.setLevel(logging.INFO)

#put logging statement into your code:

logger.info('Start create ticketflow')
logger.bug("ticketflow's name : %s", tickeflow.name)

#create file handler

handler = logging.FileHandler('ticketflow.log')
handler.setLevel(logging.DEBUG)

#create logging format

formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)

# add the handlers to the logger

logger.addHandler(handler)

```

###Configuration

####Do not get logger at the module level unless disable_existing_loggers is False

code should be like this:

```python
import logging

def foo():
    logger = logging.getLogger(__name__)
    logger.info('Hi, foo')

class Bar(object):
    def __init__(self, logger=None):
        self.logger = logger or logging.getLogger(__name__)

    def bar(self):
        self.logger.info('Hi, bar')
```

but shouldn't be like this:

```python
import logging

logger = logging.getLogger(__name__)


def foo():
    logger.info('Hi, foo')

class Bar(object):
    def bar(self):
        logger.info('Hi, bar')
```
>Because you create the logger at module level, you then import the module before you load the logging configuration from a file

* configuration

```python
import logging
import logging.config

logger = logging.getLogger(__name__)

# load config from file 

# logging.config.fileConfig('logging.ini', disable_existing_loggers=False)

# or, for dictConfig

logging.config.dictConfig({
    'version': 1,              
    'disable_existing_loggers': False,  # this fixes the problem

    'formatters': {
        'standard': {
            'format': '%(asctime)s [%(levelname)s] %(name)s: %(message)s'
        },
    },
    'handlers': {
        'default': {
            'level':'INFO',    
            'class':'logging.StreamHandler',
        },  
    },
    'loggers': {
        '': {                  
            'handlers': ['default'],        
            'level': 'INFO',  
            'propagate': True  
        }
    }
})

logger.info('It works!')
```

###use JSON or YAML logging configuration instead of configure in python code
Json File
```python
{
    "version": 1,
    "disable_existing_loggers": false,
    "formatters": {
        "simple": {
            "format": "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
        }
    },

    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "level": "DEBUG",
            "formatter": "simple",
            "stream": "ext://sys.stdout"
        },

        "info_file_handler": {
            "class": "logging.handlers.RotatingFileHandler",
            "level": "INFO",
            "formatter": "simple",
            "filename": "info.log",
            "maxBytes": 10485760,
            "backupCount": 20,
            "encoding": "utf8"
        },

        "error_file_handler": {
            "class": "logging.handlers.RotatingFileHandler",
            "level": "ERROR",
            "formatter": "simple",
            "filename": "errors.log",
            "maxBytes": 10485760,
            "backupCount": 20,
            "encoding": "utf8"
        }
    },

    "loggers": {
        "my_module": {
            "level": "ERROR",
            "handlers": ["console"],
            "propagate": "no"
        }
    },

    "root": {
        "level": "INFO",
        "handlers": ["console", "info_file_handler", "error_file_handler"]
    }
}
```

logging.yaml
```python
---

version: 1

disable_existing_loggers: False

formatters:

    simple:

        format: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"



handlers:

    console:

        class: logging.StreamHandler

        level: DEBUG

        formatter: simple

        stream: ext://sys.stdout


    info_file_handler:

        class: logging.handlers.RotatingFileHandler

        level: INFO            

        formatter: simple

        filename: info.log

        maxBytes: 10485760 # 10MB

        backupCount: 20

        encoding: utf8


    error_file_handler:

        class: logging.handlers.RotatingFileHandler

        level: ERROR            

        formatter: simple

        filename: errors.log

        maxBytes: 10485760 # 10MB

        backupCount: 20

        encoding: utf8


loggers:

    my_module:

        level: ERROR

        handlers: [console]

        propagate: no


root:

    level: INFO

    handlers: [console, info_file_handler, error_file_handler]

...

configure in python file

```python
import os
import json
import logging.config

def setup_logging(
    default_path='logging.json', 
    default_level=logging.INFO,
    env_key='LOG_CFG'
    #LOG_CFG=my_logging.json python my_server.py
):
    """Setup logging configuration

    """
    path = default_path
    value = os.getenv(env_key, None)
    if value:
        path = value
    if os.path.exists(path):
        with open(path, 'rt') as f:
            config = json.load(f)
        logging.config.dictConfig(config)
    else:
        logging.basicConfig(level=default_level)
```

or

```python
import os
import logging.config

import yaml

def setup_logging(
    default_path='logging.yaml', 
    default_level=logging.INFO,
    env_key='LOG_CFG'
    #LOG_CFG=my_logging.yaml python my_server.py
):
    """Setup logging configuration

    """
    path = default_path
    value = os.getenv(env_key, None)
    if value:
        path = value
    if os.path.exists(path):
        with open(path, 'rt') as f:
            config = yaml.load(f.read())
        logging.config.dictConfig(config)
    else:
        logging.basicConfig(level=default_level)
```
* [Basic Logging Tutorial](https://docs.python.org/2/howto/logging.html#logging-basic-tutorial)
* [Advanced Logging Tutorial](https://docs.python.org/2/howto/logging.html#logging-advanced-tutorial)
* [Logging Cookbook](https://docs.python.org/2/howto/logging-cookbook.html#logging-cookbook)

####Note
* user `UseRotatingFileHandler` instead of `FileHandler` in production environment to stop the log file from growing 
* Setup a central log server when you have multiple servers, so that we can collect all important (warning and error messages in most cases)
## Create Django custome logging filter

```python
import logging

class CustomQueryFilter(logging.Filter):
	def filter(self, record):
		for action in ['INSERT', 'UPDATE', 'DELETE']:
			if action in getattr(record, 'sql', ''):
				return True
		return False

LOGGING = {
'version': 1,
'disable_existing_loggers': False,
'filters': {
'db_query_filter': {
'()': CustomQueryFilter,
```