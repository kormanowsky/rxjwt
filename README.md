# RXJWT - Extendable JWT library for Django REST Framework

This library provides user authorization functions through JSON Web Token (JWT) mechanism. 

## Installation 

1. Clone this repo into your project directory. 
2. Add rxjwt app to your INSTALLED APPS: 
```python3
INSTALLED_APPS = [
    ...
    'rxjwt'
]
```
3. Run migrate: 
```bash 
python manage.py migrate 
```
4. Use the library

## Usage 

### Basic usage

1. Create new token pair where you need: 
```python3 
from rxjwt.token import generate_user_token_pair
from yourproject.settings import SECRET_KEY # Or use another strong secret 
...
access_token, refresh_token = generate_user_token_pair(user, SECRET_KEY)
```
2. Send your token to user
```python3
... your view goes here ... 
return Response({
    "token": str(access_token)
})
```
3. Verify your token: 
```python3
from rxjwt.token import UserAccessToken 
from yourproject.settings import SECRET_KEY # Or use another strong secret  
...
token = UserAccessToken(token_as_string)
if token.verify(SECRET_KEY): 
    # Token is valid, do something e. g. user = token.get_user() 
else: 
    # Token is not valid, show an error message 
```

### Usage as authentication class

Add rjxwt.auth.JWTAuthentication to your REST_FRAMEWORK settings:
```python3
'DEFAULT_AUTHENTICATION_CLASSES': [
    'rxjwt.auth.JWTAuthentication',
],
```
### Custom user tokens
You may extend token classes imported from rxjwt.token: 
```python3 
from rxjwt.token import UserAccessToken as UserAccessTokenBase
... 
class MyUserAccessToken(UserAccessTokenBase): 

    @classmethod
    def generate_user_secret(cls, user, secret=""): 
        return user.get_secret(secret)
```
## Settings 
All the settings must be listed in RXJWT setting like this: 
```python3 
... the rest of your settings.py ... 
RXJWT = {
    "SETTING_NAME": SETTING_VALUE
}
```
- __ACCESS_TOKEN_CLASS__: A string containing path to custom access token class e. g. `yourapp.token.YourUserAccessToken`. 
This setting must be set when using custom access token class and JWTAuthentication from rxjwt.auth
