---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python

toc_footers:

includes:
  - errors

search: true
---

# Introduction

Welcome to the Madison Reed Onboarding API! You can use our API to access Google, Slack and other 3rd party application API endpoints, which can get employees setup with the accounts they need.

# Authentication

> Every Google API call uses a Google Auth2.0 object for authorization. Therefore, we need to instantiate a Auth2.0 Object. To do so, use the following code and pass the `service` object in the multiple API calls:

```python
from __future__ import print_function
from apiclient.discovery import build
from httplib2 import Http
from oauth2client import file as oauth_file, client, tools
import googleapiclient.errors as error


store = oauth_file.Storage('token.json')
creds = store.get()
if not creds or creds.invalid:
    flow = client.flow_from_clientsecrets('credentials.json', SCOPES)
    creds = tools.run_flow(flow, store)
service = build('admin', 'directory_v1', http=creds.authorize(Http()))
```

<aside class="notice">
Make sure to replace <code>SCOPES</code> with the appropriate arguments.
</aside>

# Users

## Get a User by userKey/email

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/user/jon@madison-reed.com"

response = requests.get(url)

print(response.json())
```

> The above command returns JSON structured like this:

```json
{
    "agreedToTerms": true,
    "aliases": [
        "winter@madison-reed.com",
        "is@madison-reed.com",
        "coming@madison-reed.com"
    ],
    "archived": false,
    "changePasswordAtNextLogin": false,
    "creationTime": "2014-07-31T20:50:46.000Z",
    "customerId": "l33th4x0r",
    "emails": [
        {
            "address": "jon@madison-reed.com",
            "primary": true
        }
    ],
    "etag": "\"TN30oD80QTVasd2w2K45AAxvl_wbzs4vs/qweqwdasda\"",
    "gender": {
        "type": "male"
    },
    "id": "12345678901234567890",
    "includeInGlobalAddressList": true,
    "ipWhitelisted": false,
    "isAdmin": true,
    "isDelegatedAdmin": false,
    "isEnforcedIn2Sv": true,
    "isEnrolledIn2Sv": true,
    "isMailboxSetup": true,
    "kind": "admin#directory#user",
    "lastLoginTime": "2018-08-07T04:49:37.000Z",
    "name": {
        "familyName": "Snow",
        "fullName": "Jon Snow",
        "givenName": "Jon"
    },
    "orgUnitPath": "/",
    "primaryEmail": "jon@madison-reed.com",
    "suspended": false
}
```

This endpoint retrieves a user given an email.

### HTTP Request

`GET http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/user/<userKey>`

### Query Parameters

Parameter | Description
--------- | -----------
userKey   | email of the user (required)

<aside class="success">
Remember to have the scope for <code>users</code> in your SCOPES argument!
</aside>

## Onboard a User

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/user"

payload = {
    "firstName" : "Thanos",
    "lastName" : "Destroyer",
    "aliases" : ["dat_boi", "gamora"]
    }

response = requests.post(url, json=payload)

print(response.json())
```

> The above command returns JSON structured like this:

```json
{
    "changePasswordAtNextLogin": true,
    "creationTime": "2018-08-15T20:54:52.000Z",
    "customerId": "C023y8of9",
    "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/A0bmG96GHSyHVZdZH7rHqRNG73c\"",
    "hashFunction": "SHA-1",
    "id": "104331570604379156316",
    "isAdmin": false,
    "isDelegatedAdmin": false,
    "isMailboxSetup": false,
    "kind": "admin#directory#user",
    "name": {
        "familyName": "Destroyer",
        "givenName": "Thanos"
    },
    "orgUnitPath": "/Onboarding",
    "primaryEmail": "Thanos@madison-reed.com",
    "slack": "{\"ok\":false,\"error\":\"already_invited\"}"
}
```

This endpoint onboards a user and invites them to join the Slack workspace.

<aside class="warning">Slack invites might return errors. This might be because of users being already invited.</aside>

### HTTP Request

`POST http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/user`

### Request Parameters

Parameter | Description
--------- | -----------
firstName | First name of user to be added (required)
lastName  | Last name of user to be added (required)
aliases   | Aliases to be added for this account (optional)

## Offboard a Specific User

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/user/thanos@madison-reed.com"

response = requests.post(url, json=payload)

print(response.json())
```

> The above command returns JSON structured like this:

```json
{
    "agreedToTerms": true,
    "aliases": [
        "dat_boi@madison-reed.com",
        "gamora@madison-reed.com"
    ],
    "archived": false,
    "changePasswordAtNextLogin": true,
    "creationTime": "2018-08-15T20:54:52.000Z",
    "customerId": "C023y8of9",
    "emails": [
        {
            "address": "thanos@madison-reed.com",
            "primary": true
        }
    ],
    "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/FoXjKLC13ZpZhkBmKmGOua-OzbY\"",
    "id": "104331570604379156316",
    "includeInGlobalAddressList": true,
    "ipWhitelisted": false,
    "isAdmin": false,
    "isDelegatedAdmin": false,
    "isMailboxSetup": true,
    "kind": "admin#directory#user",
    "lastLoginTime": "1970-01-01T00:00:00.000Z",
    "name": {
        "familyName": "Destroyer",
        "fullName": "Thanos Destroyer",
        "givenName": "Thanos"
    },
    "orgUnitPath": "/Offboarding",
    "primaryEmail": "thanos@madison-reed.com",
    "suspended": true,
    "suspensionReason": "ADMIN"
}
```

This endpoint offboards a user by suspending their account and moving them to the Offboarding orgUnit.

### HTTP Request

`POST http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/user/<userKey>`

### URL Parameters

Parameter | Description
--------- | -----------
userKey   | email of the user (required)

## Delete a Specific User

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/user/thanos@madison-reed.com"

response = requests.delete(url, json=payload)

print(response.json())
```

> The above command returns an empty string if the user was deleted succesfully

This endpoint deletes a specific user

### HTTP Request

`DELETE http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/user/<userKey>`

### URL Parameters

Parameter | Description
--------- | -----------
userKey   | email of the user (required)

#Organizational Unit

##Get all Organizational Units

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/orgUnit"

response = requests.get(url, json=payload)

print(response.json())
```

<aside class="success">
Remember to have the scope for <code>orgUnits</code> in your SCOPES argument when creating the service object!
</aside>

> The above command returns JSON structured like this:

```json
{
    "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/3pLE5ESf3SZMtEAIGfoIumg-vYc\"",
    "kind": "admin#directory#orgUnits",
    "organizationUnits": [
        {
            "blockInheritance": false,
            "description": "",
            "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/XsDlklVpEKwjjcgOvpfXeGxDsQQ\"",
            "kind": "admin#directory#orgUnit",
            "name": "Shared Mailboxes",
            "orgUnitId": "id:02ffsnfu3h13gkr",
            "orgUnitPath": "/Shared Mailboxes",
            "parentOrgUnitId": "id:02ffsnfu2hw5np5",
            "parentOrgUnitPath": "/"
        },
        {
            "blockInheritance": false,
            "description": "",
            "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/5url0l2vq_VdQ_Gh_-eN1bYjfeQ\"",
            "kind": "admin#directory#orgUnit",
            "name": "Email Only",
            "orgUnitId": "id:02ffsnfu1w6dqsk",
            "orgUnitPath": "/Email Only",
            "parentOrgUnitId": "id:02ffsnfu2hw5np5",
            "parentOrgUnitPath": "/"
        },
        ...
    ]
}
```

This endpoint returns a list of all organizational units

###HTTP Request
`GET http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/orgUnit`

###URL Parameters

None

##Get a Specific Organizational Unit

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/orgUnit/Onboarding"

response = requests.get(url, json=payload)

print(response.json())
```
> The above command returns JSON structured like this:

```json
{
    "blockInheritance": false,
    "description": "Add new people here so they can enroll in 2-factor",
    "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/osvnhC_9ALul_Esg1Wxy87zmeCg\"",
    "kind": "admin#directory#orgUnit",
    "name": "Onboarding",
    "orgUnitId": "id:02ffsnfu2vbbjo6",
    "orgUnitPath": "/Onboarding",
    "parentOrgUnitId": "id:02ffsnfu2hw5np5",
    "parentOrgUnitPath": "/"
}
```

This endpoint returns a specific organizational unit

###HTTP Request

`GET http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/orgUnit`

###URL Parameters

Parameter | Description
--------- | -----------
orgUnitPath   | path of organizational unit to retrieve (required)

#Groups

##Get All Groups

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group"

response = requests.get(url)

return response.json()
```
> The above command returns JSON structured like this:

```json
{
    "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/oxmp8Fu_8b4KcyJMjAnZAnCxLuc\"",
    "groups": [
        {
            "adminCreated": true,
            "description": "",
            "directMembersCount": "4",
            "email": "abuse@madison-reed.com",
            "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/-eeIJDRkVdu0QUSpVuUyXpWmXt8\"",
            "id": "02dlolyb1jf220q",
            "kind": "admin#directory#group",
            "name": "Review Queue"
        },
        {
            "adminCreated": true,
            "description": "Accounting",
            "directMembersCount": "4",
            "email": "accounting@madison-reed.com",
            "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/rzNiYIoF8KOhH3mcI97KmLJEXVw\"",
            "id": "00kgcv8k2z8dzez",
            "kind": "admin#directory#group",
            "name": "Accounting"
        },
        ...
    ]
}
```

This endpoint returns all groups

###HTTP Request

`GET http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group`

###URL Parameters

None

##Get Specific Group

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group/eng@madison-reed.com"

response = requests.get(url)

return response.json()
```
> The above command returns JSON structured like this:

```json
{
    "adminCreated": false,
    "aliases": [
        "runscope-api@madison-reed.com"
    ],
    "description": "All things development and engineering",
    "directMembersCount": "15",
    "email": "eng@madison-reed.com",
    "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/o2o1NO6yfiWs01CCwxYvyOPe_WA\"",
    "id": "045jfvxd4dy2hgl",
    "kind": "admin#directory#group",
    "name": "eng"
}
```

This endpoint returns a specific group given the groupKey

###HTTP Request

`GET http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group/<groupKey>`

###URL Parameters

Parameter | Description
--------- | -----------
groupKey   | email of group to retrieve (required)


##List Users in a Group

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group/eng@madison-reed.com/member"

response = requests.get(url)

return response.json()
```
> The above command returns JSON structured like this:

```json
[
    {
        "email": "andrew@madison-reed.com",
        "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/pg0J6e002s40xePTl52q9X_3juc\"",
        "id": "105792973699600791996",
        "kind": "admin#directory#member",
        "role": "MEMBER",
        "status": "ACTIVE",
        "type": "USER"
    },
    {
        "email": "andris@madison-reed.com",
        "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/7pylcytqrsmQ4dOL8SCUuB_2OdI\"",
        "id": "112508739294212424645",
        "kind": "admin#directory#member",
        "role": "MEMBER",
        "status": "ACTIVE",
        "type": "USER"
    },
    ...
]
```

This endpoint returns the users that are part of a group given the groupKey

###HTTP Request

`GET http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group/<groupKey>/member`

###URL Parameters

Parameter | Description
--------- | -----------
groupKey   | email of group to retrieve (required)


##Add User to a Group

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group/c2u@madison-reed.com/member"

payload = {
    'memberKey' : 'jon@madison-reed.com'
}

response = requests.post(url, json=payload)

return response.json()
```
> The above command returns JSON structured like this:

```json
{
    "email": "jon@madison-reed.com",
    "etag": "\"TN30oD80QTVK45AAxvl_wbzs4vs/r3Q3oTR65-7lF3fpUxL6lQ852gg\"",
    "id": "110096950843703361097",
    "kind": "admin#directory#member",
    "role": "MEMBER",
    "status": "ACTIVE",
    "type": "USER"
}
```

This endpoint adds a user to a group given the groupKey and memberKey

###HTTP Request

`POST http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group/<groupKey>/member`

###URL Parameters

Parameter | Description
--------- | -----------
groupKey   | email of group to retrieve (required)

<aside class="warning">Take note that the memberKey has to provided as part of the request parameters (below)</aside>

###Request Parameters

Parameter | Description
--------- | -----------
memberKey | email of user to add to a group (required)


##Remove User from a Group

```python
import requests

url = "http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group/c2u@madison-reed.com/member"

payload = {
    'memberKey' : 'jon@madison-reed.com'
}

response = requests.post(url, json=payload)

return response.json()
```
> The above command returns an empty string if the user was deleted succesfully

This endpoint removes a specified user froma group given the groupKey and memberKey

###HTTP Request

`GET http://flask-env.2eikc4dq9p.us-east-1.elasticbeanstalk.com/group/<groupKey>/member`

###URL Parameters

Parameter | Description
--------- | -----------
groupKey   | email of group to retrieve (required)

<aside class="warning">Take note that the memberKey has to provided as part of the request parameters (below)</aside>

###Request Parameters

Parameter | Description
--------- | -----------
memberKey | email of user to add to a group (required)

