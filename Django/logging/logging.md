logging
===

## Create custome logging filter

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