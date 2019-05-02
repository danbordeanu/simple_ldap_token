# Simple lib for ldap authentication


A python-based helper capable of authentication using ldap.

# Introduction

Simple python lib that will perform authentication by connectiong to ldap endpoint. If login is succesful it wil return a token that can be used in different REST/API apps.

### Prerequisites

1. python/pip
2. memcached server
3. ldap server

## Installation

### Cloning

```bash
https://github.com/danbordeanu/simple_ldap_token.git
```


### Installing pip packages

```bash
pip install -r requirements.txt
```
### Configuration

Edit the config.ini file based on your server settings.

```bash
[ldap]
# ldap address
server: ldaps://ldapserver:636
# my super secret key
secret_key: supersecretthing
# expire interval
expire_interval: 3600
ldap_search: OU=eCore Office,OU=People Accounts,DC=domain,DC=com
[memcache]
port: 11211
host: localhost
```
Also env vars for ldap and memcache host are supported.

```
export LDAP_HOST='ldaps://ldapserver:636
export MEMCACHED_HOST='localhost'
export MEMCACHED_PORT='11211'
```


## Usage

### Login with real user/passwd

```python
from ldap_auth import LdapUserAuth
login_management = LdapUserAuth()
login_management.check_ldap_password('user@domain.com', 'base64encodedpassswd')
```
If successful, it will return a token

```
2019-XX-XX 11:33:12,139 - root - INFO - User:bordeanu@merck.com got authenticated
2019-XX-XX 11:33:12,140 - root - INFO - Token generated for user:user@domain.com
2019-XX-XX 11:33:12,142 - root - DEBUG - Token for user:user@domain.com added to memcached
eyJhfffXXXXXXX
```

This will check if the token is valid and return the username

```
user@domain.com
```

### Login with fake user/passwd

```python

 login_management = LdapUserAuth()
            login_management.insert_token_memcache('fake@domain.com',login_management.generate_auth_token('fake@domain.com'))
```

### Get the token for the fake user

```python
login_management.get_token_memcache('fake@domain.com')
```                                                 

This will return

```
2019-XX-XX 11:41:54,069 - root - INFO - Got token:eyJhb for user:fake@domain.com
'eyJhbGci'
```

### Verify token

```python
login_management.verify_auth_token('eyJhbGcXXX')
```
