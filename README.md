# misterize

Misterize is a Python library to protect your sensitive data from leaking.

Misterize aim to dynamically mask all your passwords, token, etc...
from strings, logs, and displayed outputs.

## Install

```sh
$ pip install misterize
```

## Usage

Consider the following runtime configuration file (unix/linux rc file)
inspired from Openstack's rc file:

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

from misterize import print
print("After hiding sensitive data:")
print(rc_var)
```

The previous example will output before hiding your data will output:

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

Using the print wrapper isn't mandatory, you can hide your sensitive data
independently from outputing them. Consider this new example:

```python
import os
import misterize

rc_var = misterize.mask_passwords{
    key: value for el in os.environ if key.startswith(OS_)}
print(rc_var)
```

The previous example will output
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
