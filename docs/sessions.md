Sessions
========

An essential feature for a web application is the ability to store specific informations about the client from a request to the next one. Accordingly to this need, weppy provides another object beside the `request` and the `response` ones called `session`.

```python
from weppy import session

@app.route("/counter")
def count():
    session.counter = (session.counter or 0) + 1
    return "This is your %d visit" % session.counter
```

The above code is quite simple: the app increments the counter every time the user visit the page and return this number to the user.   
Basically, you can use `session` object to store and retrieve data, but before you can do that, you should add a *SessionManager* to your application pipeline. These managers allows you to store sessions' data on different storage systems, depending on your needs. Let's see them in detail.

Storing sessions in cookies
---------------------------
You can store session contents directly in the cookies of the client using the weppy's `SessionManager.cookies` pipe:

```python
from weppy import App, session
from weppy.sessions import SessionManager

app = App(__name__)
app.pipeline = [SessionManager.cookies('myverysecretkey')]

@app.route("/counter")
# previous code
```

As you can see, `SessionManager.cookies` needs a secret key to crypt the sessions' data and keep them secure – you should choose a good key – but also accepts more parameters:

| parameter | default value | description |
| --- | --- | --- |
| expire | 3600 | the duration in seconds after which the session will expire |
| secure | `False` | tells the manager to allow sessions only on *https* protocol |
| domain | | allows you to set a specific domain for the cookie |
| cookie\_name | | allows you to set a specific name for the cookie |

Storing sessions on filesystem
------------------------------
*New in version 0.2*

You can store session contents on the server's filesystem using the weppy's `SessionManager.files` pipe:

```python
from weppy import App, session
from weppy.sessions import SessionManager

app = App(__name__)
app.pipeline = [SessionManager.files()]

@app.route("/counter")
# previous code
```

As you can see, `SessionManager.files` doesn't require specific parameters, but it accepts these optional ones:

| parameter | default value | description |
| --- | --- | --- |
| expire | 3600 | the duration in seconds after which the session will expire |
| secure | `False` | tells the manager to allow sessions only on *https* protocol |
| domain | | allows you to set a specific domain for the cookie |
| cookie\_name | | allows you to set a specific name for the cookie |
| filename_template | `'weppy_%s.sess'` | allows you to set a specific format for the files created to store the data |

Storing sessions using redis
----------------------------
You can store session contents using *redis* – you obviously need the redis package for python – with the weppy's `SessionManager.redis` pipe:

```python
from redis import Redis
from weppy import App, session
from weppy.sessions import SessionManager

app = App(__name__)
red = Redis(host='127.0.0.1', port=6379)
app.pipeline = [SessionManager.redis(red)]

@app.route("/counter")
# previous code
```

As you can see `SessionManager.redis` needs a redis connection as first parameter, but as for the cookie manager, it also accepts more parameters:

| parameter | default | description |
| --- | --- | --- |
| prefix | `'wppsess:'` | the prefix for the redis keys (default set to |
| expire | 3600 | the duration in seconds after which the session will expire |
| secure | `False` | tells the manager to allow sessions only on *https* protocol |
| domain | | allows you to set a specific domain for the cookie |
| cookie\_name | | allows you to set a specific name for the cookie |

The `expire` parameter tells redis when to auto-delete the unused session: every time the session is updated, the expiration time is reset to the one specified.
