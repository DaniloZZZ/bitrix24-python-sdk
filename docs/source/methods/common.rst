.. bitrix24-python-sdk documentation master file, created by
sphinx-quickstart on Wed Aug 17 22:27:41 2016.
You can adapt this file completely to your liking, but it should at least
contain the root `toctree` directive.

Common REST Methods
===================

The following methods are available to all applications irrespective of the current application permissions.

methods
-------
Returns all methods or those available to a current application. ::

>>> bx24.call('methods')
>>> {u'result': [u'batch', u'scope', u'methods', u'events', ...]}



Returns methods available to scope. ::

>>> bx24.call('methods', {'scope':'user'})
>>> {u'result': [u'user.fields', u'user.current', u'user.get', ...]}


If the scope name is omitted , all the common methods are returned. ::

>>> bx24.call('methods', {'scope':''})
>>> {u'result': [u'batch', u'scope', u'methods', u'events', ...]}

scope
-----
Returns all permissions or only those available to a current application. ::

>>> bx24.call('scope')
>>> {u'result': [u'task', u'tasks_extended']}

Rreturn a list of all scopes. ::

 >>> bx24.call('scope', {'full': True})
 >>> {u'result': [u'calendar', u'disk', u'telephony', u'lists', ... ]}

app.info
--------
Displays application information. ::

>>> bx24.call('app.info')

Result: ::

    {
        u'result':
        {
            u'CODE': u'local.1234567890abcd.12345678',
             u'DAYS': None,
             u'ID': u'2',
             u'LANGUAGE_ID': u'ru',
             u'LICENSE': u'ru_project',
             u'PAYMENT_EXPIRED': u'N',
             u'STATUS': u'L',
             u'VERSION': 1
        }
    }


Result fields:

* ``ID`` - Application local id
* ``CODE``	- Specifies the application ID.
* ``LANGUAGE_ID`` - Current language.
* ``VERSION`` - Specifies the application version.
* ``STATUS`` - The application status. It can be one of the following values:
 * ``F`` - free;
 * ``D`` - demo version;
 * ``T`` - trial version, time limited;
 * ``P`` - the application has been purchased.
* ``PAYMENT_EXPIRED`` -  if Y, the application license or trial period has expired.
* ``DAY`` - Specifies the number of days left until the application license or trial period expires.


user.admin
----------
Checks if a current user has sufficient permission to manage application parameters. ::

>>> bx24.call('user.admin')
>>> {u'result': True}

user.access
-----------
Checks if a current user has at least one permission of those specified as an argument. ::

>>> bx24.call('user.access')

access.name
-----------
Returns a human readable name of an access permission. ::

>>> bx24.call('access.name', {'ACCESS': {0: 'AU'}})

Result: ::

 {u'result': {u'AU': {u'name': u'All authorized users',
                      u'provider': u'',
                      u'provider_id': u'other'}}}


events
------
Retrieves a list of all authorized events. ::

>>> bx24.call('events')

Result: ::

 {u'result': [u'ONAPPUNINSTALL',
             u'ONAPPINSTALL',
             u'ONAPPUPDATE',
             u'ONAPPPAYMENT',
             u'ONAPPTEST',
             u'ONTASKADD',
             u'ONTASKUPDATE',
             u'ONTASKDELETE',
             u'ONTASKCOMMENTADD',
             u'ONTASKCOMMENTUPDATE',
             u'ONTASKCOMMENTDELETE']}


Return events for a scope ::

>>> bx24.call('events', {'scope': 'task'})

Result: ::

 {u'result': [u'ONTASKADD',
             u'ONTASKUPDATE',
             u'ONTASKDELETE',
             u'ONTASKCOMMENTADD',
             u'ONTASKCOMMENTUPDATE',
             u'ONTASKCOMMENTDELETE']}


event.bind
----------
Installs a new event handler. The method can be called only by a user having administrative privileges. ::

Example: ::

 bx24.call('event.bind',
    {'event': 'ONAPPUNINSTALL',
     'handler': 'https://example.com/handler.py',
     'auth_type': 0
 })

Result: ::

 {u'result': True}

Parameters:

* ``event`` - Specifies the event name. This parameter is required.
* ``handler`` - Specifies the event handler URL. This parameter is required.
* ``auth_type`` - Specifies the ID of a user whose credentials will be used to install the handler. This parameter is optional. By default, the event handler will be authenticated as a user whose actions triggered the event.

Possible errors:
 * Unable to set event handler: Handler already binded
 * Handler URL host doesn't match application url


event.unbind
------------

Uninstalls a previously installed event handler. The method can be called only by a user having administrative privileges. ::

Example: ::

 bx24.call('event.unbind',
    {'event': 'ONAPPUNINSTALL',
     'handler': 'https://example.com/handler.py',
     'auth_type': 0
 })

Result: ::

 {u'result': {u'count': 1}}

Parameters:

* ``event`` - 	Specifies the event name. This parameter is optional.
* ``handler`` - Specifies the event handler URL. This parameter is optional.
* ``auth_type`` - Specifies the ID of a user whose credentials will be used to install the handler. This parameter is optional. Notice: to remove an event handler installed with an empty auth_type (which means a user whose actions triggered the event) but remain other handlers active, specify auth_type=0 or empty value.

Result fields:

* ``count`` - Counter of removed event handlers

event.get
---------
Get the list of registered event handlers.

Example: ::

 bx24.call('event.get')

Result: ::

 {u'result': [{u'auth_type': u'0',
               u'handler': u'https://example.com/handler.py',
               u'event': u'ONAPPUNINSTALL'}]}

Result fields:

* ``event`` - 	Event name. This parameter is optional.
* ``handler`` - Event handler URL.
* ``auth_type`` - ID of a user whose credentials will be used to install the handler.


batch
-----
Executes requests in a batch.
It is not uncommon for an application to send requests in series. Use this function to batch call REST methods instead of sending requests one by one.

Parameters:

* ``halt`` - If 1, the batch will be aborted if an error occurs. If 0 (zero), all the requests will be passed to REST service regardless of errors.
* ``cmd`` - Specifies a standard array of requests. Notice that the request data must be quoted; therefore, the request data inside a request must be quoted again.

Result fields:

* ``result`` -
* ``result_error`` -
* ``result_next`` -
* ``result_total`` -



Example: ::

 bx24.call('batch', {
     'halt': 0,
     'cmd': [u'scope', u'methods', u'events', u'event.get']
 })

Result: ::

 {u'result': {u'result': [[u'task', u'tasks_extended'],
                         [u'batch',
                          u'scope',
                          u'methods',
                          u'events',
                          ...
                          ],
                         [{u'auth_type': u'0',
                           u'event': u'ONAPPUNINSTALL',
                           u'handler': u'https://example.com/handler.py'}]],
             u'result_error': [],
             u'result_next': [],
             u'result_total': []}}


With request IDs: ::

 bx24.call('batch', {
        'halt': 0,
        'cmd': {'scope': 'scope',
                'methods': 'methods',
                'events': 'events',
                'event.get': 'event.get'
                }
    })

Result: ::

 {u'result': {u'result': {u'event.get': [{u'auth_type': u'0',
                                         u'event': u'ONAPPUNINSTALL',
                                         u'handler': u'https://example.com/handler.py'}],
                         u'events': [u'ONAPPUNINSTALL',
                                     u'ONAPPINSTALL',
                                     u'ONAPPUPDATE',
                                     ...
                                     ],
                         u'methods': [u'batch',
                                      u'scope',
                                      u'methods',
                                      u'events',
                                      u'event.bind',
                                      u'event.unbind',
                                      ...
                                     ],
                         u'scope': [u'task', u'tasks_extended']},
             u'result_error': [],
             u'result_next': [],
             u'result_total': []}}

Call functions with parameters: ::

 TODO

Result: ::

 TODO