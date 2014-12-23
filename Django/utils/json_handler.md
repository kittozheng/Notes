json_handler
==========

# Using for Django-Ajax json response

##  Example
####Encode queryset  to json and return json response 

```python
all_users = json_encode(all_users.values('id', 'name')) \
            if all_users.exists() else None

return render_json(dict(state = True, all_users = all_users))
```

####render_json for handling dict data
	
```python
from django.http import HttpResponse
from django.utils import simplejson
def render_json(data):
	return HttpResponse(simplejson.dumps(data, ensure_ascii=False))
```

##	encoder for  queryset

```python
''''
The main issues with django's default json serializer is that properties that
had been added to a object dynamically are being ignored (and it also has 
problems with some models).
'''

from decimal import Decimal
from datetime import *
from mimetype import get_mime_type

class CJsonEncoder(json.JSONEncoer):
	def default(self, obj):
		if isinstance(obj, datetime):
			return obj.strftime('%Y-%m-%d %H:%M:%S')
		elif isinstance(obj, date):
			return obj.strftime('%Y-%M-%d')
		else:
			return json.JSONEncoer.default(self, obj)


def json_encode(data):

	def _any(data):
		ret = None
		if isinstance(data, list):
			ret = _list(data)
		elif isinstance(data, dict):
			ret = _dict(data)
		elif isinstance(data, Decimal):
			# json.dumps() can't handle Decimasl
			ret = str(data)
		elif isinstance(data, models.query.Queryset):
			ret = _list(data)
		elif isinstance(data, models.Model):
			ret = _model(data)
		else:
			ret = data

		return ret

	def _model(data):
		ret = {}
		# If we have a model, we only want to encode the fields
		for f in data._meta.fields:
			ret[f.attname] = _any(getattr(data, f.attname))
		# And additionnally encode arbitrary properties that had been added
		fields = dir(data.__class__) + ret.keys()
		add_ons = [k for k in dir(data) if k not in fields]
		for k in add_ons:
			ret[k] = _any(getattr(data, k))

		return ret

	def _list():
		ret = []
		for v  in data:
			ret.append(_any(v))

		return ret

	def _dict(data):
		ret = {}
		for k, v in data.items():
			ret[k] = _any(v)
		
		return ret
	
	ret = _any(data)

	# return json.dumps(ret, cls=DatetimeAwareJSONEncoder)
	return json.dumps(ret, cls=CJsonEncoder)
```
