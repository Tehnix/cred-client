cred-client
=====
Client library for the [cred-server](https://github.com/Tehnix/cred-server "cred-server repository") application.


Usage
=====
The idea is that you subclass the client base class, and then implement your own
handler for the events. This gives enough flexibility for custom applications,
but also removes a lot of boiler plate code that would be the same across all
clients.

For a more full example, see the `example.py` file. The following will be a
superficial rundown of the example. First we subclass the
`cred.client.ClientBase` class,


```python
from cred.client import ClientBase

class MyClient(ClientBase):

    def handle_event(self, event):
        """Act on an event here."""
        print(event['id'])
```

Then you need to initialize the class with some configuration values like,

```python
# The API server which the client is connecting to
hostname = 'http://127.0.0.1:5000'
# An API key that allows write access
apikey = 'qWqa7nhoYdeJIX5FIJwb4Q4bjM79hNUF6GFh8kQt6uE'
# The device name
device = 'Client Library'
# The device location
location = 'Laptop'
# A list of event names and locations that the client subscribes to
subscribe = {
    'Temperature': {},
    'Light': {'location': 'Living Room'}
}

# Finally, instantiate the class
client = MyClient(hostname, apikey, device, location, subscribe)
```

You can also override the scheduler and override the custom schedule interval,
by supplying the following keyword arguments to the instantiation,
* `ignore_scheduler=True` will ignore the scheduler that the client is assigned
upon authentication.
* `custom_schedule_interval=30` is both the fallback for when the server doesn't
implement a scheduler, or when `ignore_scheduler` is set to `True`.

Finally, you authenticate the client, and start listening to events,

```python
auth = client.authenticate()
event_daemon = client.start_pulling_subscribedevents()
```

Note, that unless you do some blocking action after this, the program will quit
immediately. You can for example post an event every 2 seconds,

```python
# Post an event every 2 seconds
while True:
    client.submit_event('Test', 'Performing a test', 'True')
    time.sleep(2)
```
