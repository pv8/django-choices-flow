django-choices-flow
==================

Meta Choice with simple flow and rules

[![Build Status](https://travis-ci.org/valdergallo/django-choices-flow.png?branch=master)](https://travis-ci.org/valdergallo/django-choices-flow)
[![Latest Version](http://img.shields.io/pypi/v/django-choices-flow.svg)](https://pypi.python.org/pypi/django-choices-flow)
[![Coverage Status](https://coveralls.io/repos/valdergallo/django-choices-flow/badge.png)](https://coveralls.io/r/valdergallo/django-choices-flow)
[![BSD License](http://img.shields.io/badge/license-BSD-yellow.svg)](http://opensource.org/licenses/BSD-3-Clause)
[![PyPI Downloads] (http://img.shields.io/pypi/dm/django-choices-flow.svg)](https://pypi.python.org/pypi/django_choices_flow)


## Usage


```python
from django.db import models
from django_choices_flow import Choices
from django_choices_flow.models import FlowIntegerField


class MyChoices(Choices):
    NEW = 1, 'New content' # 'New content' is the display text
    WAIT = 2, 'Wait'
    CANCELED = -1, 'Canceled'
    ERROR = -2, 'Error'
    INVOICED = 3, 'Invoiced'

    # set transaction rules
    NEW_RULES = [NEW, INVOICED, CANCELED, ERROR]
    WAIT_RULES = [CANCELED, ERROR, INVOICED]
    INVOICED_RULES = [CANCELED]


class Invoces(models.Model):
	"""
	To use only choices
	"""
    number = models.IntegerField()
    status = models.IntegerField(choices=MyChoices, default=MyChoices.NEW)

    def __unicode__(self):
        return self.number


class FlowInvoice(models.Model):
	"""
	To validate flow in choices
	"""
	number = models.IntegerField()
	status = FlowIntegerField(choices=MyChoices, default=MyChoices.NEW)

	def __unicode__(self):
        return self.number
```

### Shell

```python
>>> flow = FlowInvoice.objects.create(number=1234)
>>> flow.status
1
>>> flow.status = MyChoices.INVOICED
>>> flow.full_clean()
>>> flow.save()
>>> flow.status
3
>>> flow.status = MyChoices.WAIT
>>> flow.full_clean()
ValidationError: {'status': [u'Invalid choice: Wait']}
```

## Developer

```bash
# download code
git clone git@github.com:valdergallo/django-choices-flow.git

# install developer packages
make

# check coverage
make coverage

# test project
make test

#clean extra content
make clean

#send package
make send_package

#test py2 and py3
tox
```


-----------------------------------------------------------
    South: Support migrations
    Python: python 2.7 and python 3.3
    Tested in Django:
        1.2.x;
        1.3.x;
        1.4.x;
        1.5.x;
        1.6.x;
        1.7.x;
        1.8.x;
        1.9.x;
        1.10.x;
