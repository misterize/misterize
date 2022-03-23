# misterize

Misterize is a Python library to protect your sensitive data from leaking.

Misterize aim to dynamically mask all your passwords, token, etc...
from strings, logs, and displayed outputs.

Misterize come with batteries by automatically defining a list of keywords
that are considered as sensitive and that will be treated if it detected them.
However Misterize also allow you to define your own sensitive keys to define
what is sensitive for you and to match your context.

## Install

```sh
$ pip install misterize
```

## Usages

Before starting presenting the usages consider the following runtime
configuration file (unix/linux rc file) inspired from Openstack's rc file:

```
export OS_USERNAME=user1
export OS_PROJECT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_NO_CACHE=True
export OS_CLOUDNAME=overcloud
export OS_AUTH_TYPE=password
export OS_PASSWORD='mypassword'
export OS_AUTH_URL=https://overcloud.redhat.local:13000
export OS_IDENTITY_API_VERSION=3
export OS_COMPUTE_API_VERSION=2.latest
export OS_IMAGE_API_VERSION=2
export OS_VOLUME_API_VERSION=3
export OS_REGION_NAME=regionOne
```

Now consider the following python script, where environment variable
will be loaded by our script and displayed in log at runtime:

```python
import os

rc_var = {key: value for el in os.environ if key.startswith(OS_)}
print("Before hiding sensitive data:")
print(rc_var)
```

The previous example gave us the following output where all your data senstive
(especially `OS_PASSWORD`) data are displayed:

```
Before hidding sensitive data:
{'OS_USERNAME': 'user1', 'OS_PROJECT_NAME': 'admin',
'OS_USER_DOMAIN_NAME': 'Default', 'OS_PROJECT_DOMAIN_NAME': 'Default',
'OS_NO_CACHE': 'True', 'OS_CLOUDNAME': 'overcloud',
'OS_AUTH_TYPE': 'password', 'OS_PASSWORD': 'mypassword',
'OS_AUTH_URL': 'https://overcloud.redhat.local:13000',
'OS_IDENTITY_API_VERSION': '3', 'OS_COMPUTE_API_VERSION': '2.latest',
'OS_IMAGE_API_VERSION': '2',
'OS_VOLUME_API_VERSION': '3', 'OS_REGION_NAME': 'regionOne'}
```

### The mask password

The `mask_passwords` function is designed to hide sensitive data
independently from outputing them. Consider the following example:

```python
import os
import misterize

rc_var = misterize.mask_passwords{
    key: value for el in os.environ if key.startswith(OS_)}
print(rc_var)
```

The previous example will display:
```
{'OS_USERNAME': 'user1', 'OS_PROJECT_NAME': 'admin',
'OS_USER_DOMAIN_NAME': 'Default', 'OS_PROJECT_DOMAIN_NAME': 'Default',
'OS_NO_CACHE': 'True', 'OS_CLOUDNAME': 'overcloud',
'OS_AUTH_TYPE': 'password', 'OS_PASSWORD': '***',
'OS_AUTH_URL': 'https://overcloud.redhat.local:13000',
'OS_IDENTITY_API_VERSION': '3', 'OS_COMPUTE_API_VERSION': '2.latest',
'OS_IMAGE_API_VERSION': '2',
'OS_VOLUME_API_VERSION': '3', 'OS_REGION_NAME': 'regionOne'}
```

You can also use the `mask_passwords` function directly when calling the
`print` function, the following example will output the same thing that the
previous example:

```python
import os
from misterize import mask_passwords as mp

rc_var = {key: value for el in os.environ if key.startswith(OS_)}
print(mp(rc_var))
```

### The print wrapper

The print wrapper is designed to avoid thinking about sensitive data.
All inputs consumed by the `print` function are automatically treated to hide
sensitive data.

Now consider the following python script, where environment variable
will be loaded by our script and displayed in log at runtime:

```python
import os

rc_var = {key: value for el in os.environ if key.startswith(OS_)}
print("Before hiding sensitive data:")
print(rc_var)

from misterize import print
print("After hiding sensitive data:")
print(rc_var)
```


And then once you call misterize's print wrapper all your sensitive data
will be hiden:

```
After hidding sensitive data:
{'OS_USERNAME': 'user1', 'OS_PROJECT_NAME': 'admin',
'OS_USER_DOMAIN_NAME': 'Default', 'OS_PROJECT_DOMAIN_NAME': 'Default',
'OS_NO_CACHE': 'True', 'OS_CLOUDNAME': 'overcloud',
'OS_AUTH_TYPE': 'password', 'OS_PASSWORD': '***',
'OS_AUTH_URL': 'https://overcloud.redhat.local:13000',
'OS_IDENTITY_API_VERSION': '3', 'OS_COMPUTE_API_VERSION': '2.latest',
'OS_IMAGE_API_VERSION': '2',
'OS_VOLUME_API_VERSION': '3', 'OS_REGION_NAME': 'regionOne'}
```

## Defining what is sensitive for you

Misterize come with default keys and patterns that will be treated to be hiden
if they are detected. However, in some context you may want to define your own
keywords to hide to match your needs, in this section we will see how to
define your own keys.

Also, you can check the default keys providen by Misterize by using
the misterize command:

```shell
$ misterize list
```

### The environment variables

You can define your own pattern to match your needs. The first way to do that
is by defining the `MISTERIZE_PATTERN` environment variable and to initialize
it with your own pattern, example:

```shell
export MISTERIZE_PATTERN=""
```
