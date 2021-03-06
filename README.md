<p align=center>
    <img src="https://developer.somfy.com/sites/default/files/img/SoOpen.png"/>
</p>
<p align=center>
    <a href="https://pypi.org/project/pymfy/"><img src="https://img.shields.io/pypi/v/pymfy.svg"/></a>
    <a href="https://travis-ci.org/tetienne/somfy-open-api"><img src="https://img.shields.io/travis/tetienne/somfy-open-api.svg"/></a>
    <a href="https://codeclimate.com/github/tetienne/somfy-open-api/maintainability"><img src="https://api.codeclimate.com/v1/badges/efefe25b6c0dc796bc1c/maintainability" /></a>
    <a href="https://codeclimate.com/github/tetienne/somfy-open-api/test_coverage"><img src="https://api.codeclimate.com/v1/badges/efefe25b6c0dc796bc1c/test_coverage" /></a>
</p>
 
This library is an attempt to implement the entire Somfy API in Python 3.
Documentation for the Somfy API can be found [here](https://developer.somfy.com/somfy-open-api/apis).

## Get developer credentials

1. Vist https://developer.somfy.com
2. Create an account
3. Open the *My Apps* menu
4. Add a new App (for testing, redirect url can be anything in https)
4. Plug in your details into the test script below.

## Example usage

Print all covers name.

```python
import os

from pymfy.api.devices.roller_shutter import RollerShutter
from pymfy.api.somfy_api import SomfyApi
from pymfy.api.devices.category import Category

client_id = r'<CLIENT_ID>'
redir_url = '<REDIR_URL>'
secret = r'<secret>'

cache_path = '/optional/cache/path'
api = SomfyApi(client_id, secret, redir_url, cache_path)
if not os.path.isfile(cache_path):
    authorization_url, state = api.get_authorization_url()
    print('Please go to {} and authorize access.'.format(authorization_url))
    authorization_response = input('Enter the full callback URL')
    api.request_token(authorization_response)

devices = api.get_devices(category=Category.ROLLER_SHUTTER)

covers = [RollerShutter(d, api) for d in devices]

for cover in covers:
    print("Cover {} has the following position: {}".format(cover.device.name, cover.get_position()))

```

## Contribute
The current [documentation](https://developer.somfy.com/products-services-informations) does not give enough information to implement all the devices.
If you want to contribute to this repository adding new devices, you can create an issue with the output of this script:
```python
import json
import re

client_id = r'<CLIENT_ID>'
redir_url = '<REDIR_URL>'
secret = r'<secret>'

from pymfy.api.somfy_api import SomfyApi

api = SomfyApi(client_id, secret, redir_url)
authorization_url, state = api.get_authorization_url()
print('Please go to {} and authorize access.'.format(authorization_url))
authorization_response = input('Enter the full callback URL')
api.request_token(authorization_response)

devices = api.get_devices()
# Remove personal information
dumps = json.dumps(devices, sort_keys=True, indent=4, separators=(',', ': '))
dumps = re.sub('".*id.*": ".*",\n', '', dumps)

print(dumps)
```

