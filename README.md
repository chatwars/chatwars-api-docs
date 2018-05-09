# CW API v0.5

## What is new
_v0.5_
- yellow_pages exchange 
- extended user profile info

## Abstract
Interaction is done via AMQP (by rabbitMQ). Application should publish a message to the appropriate exchange with  an appropriate routing key, and then, listen for and react to incoming messages in its queue. E.g. for the application named `dices` there would a be direct exchange named `dices_ex`, routing key `dices_o` and an inbound queue `dices_i` (for the testing purpose there will be `dices_to` and `dices_ti` queues respectively)

The connection url will be: `amqps://dices:[hidden]@api.chatwars.me:5673/`

Apart from that, there is a set of public fan-out read-only exchanges, where you have your own queue (if requested) and can start receiving updates. The names of this queues have format `%username%_%exchangename%`
(e.g. `dices_deals`)

For now all queues are limited in size by 1Mb, with discard-oldest policy. 

Please note, that at this moment, API is highly unstable. 

## Fan-out Exchange Routing Keys
### deals
The confirmed deals are published to the public exchange with the name `deals`.
```json
{
  "sellerId": "53f3e27a124e01dcdd77de45995bf0db", // ingame userId, obtained with token
  "sellerCastle": "🦌",
  "sellerName": "Wolpertinger",
  "buyerId": "3537e9190d1d516e05cd638bb76fe66c",
  "buyerCastle": "🦌",
  "buyerName": "Guacamele",
  "item": "charcoal",
  "qty": 10,
  "price": 6
}
```

### offers
New 'want to sell' offers are published to public exchange with the name `offers` as soon as they come online
```json
{
  "sellerId": "53f3e27a124e01dcdd77de45995bf0db",
  "sellerCastle": "🦌",
  "sellerName": "Wolpertinger",
  "item": "charcoal",
  "qty": 10,
  "price": 6
}
```

### sex_digest
Top 10, sorted by price, offers on exchange. Posted periodically (approx. every 5 minutes).
```json
[
  {
    "name": "Bone powder",
    "prices": [
      10,
      11,
      15,
      15,
      15,
      15,
      20,
      20,
      20,
      20
    ]
  },
  {
    "name": "Iron ore",
    "prices": [
      5,
      5,
      5,
      6,
      6,
      6,
      6,
      6,
      6,
      6
    ]
  },
  // ...
]
```

### yellow_pages
List of all published and open shops with all of their offers and prices (approx. every 5 minutes).
```json
[
  {
    "link": "UQ5DX",
    "name": "Studio",
    "ownerName": "WolperTinger",
    "ownerCastle": "🦌",
    "kind": "⚒",
    "mana": 20,
    "offers": [
      {
        "item": "Metal plate",
        "price": 1,
        "mana": 10
      }
    ]
  },
  {
    "link": "OMgWt",
    "name": "Shack",
    "ownerName": "myDeerest",
    "ownerCastle": "🦌",
    "kind": "⚒",
    "mana": 880,
    "offers": [
      {
        "item": "Hunter Armor",
        "price": 80,
        "mana": 150
      }
    ]
  }    
]
```

## Message Types
### createAuthCode
_Access request from your application to the user._

After this command is issued, game bot will send a notification to the user containing authorization code. If one agrees to grant you access, it will pass this code to your application. 
```json
// outbound queue
{ 
    "action": "createAuthCode", 
    "payload": {
        "userId": 1234567 // subjects telegram userId
    }
}
```

In case of a success, your application will get following message.
```json
// inbound queue
{ 
    "uuid": "b9qvvgtk324j2d8gdqjg", // request correlationId
    "action": "createAuthCode", 
    "result": "Ok",   
    "payload": {
        "userId": 1234567 // subjects telegram userId
    }
}
```

### grantToken
_Exchange auth code for access token._

Having the access code, your application should obtain access token, in order to work with most of the API methods. As of now token lifetime is unlimited.
```json
// outbound queue
{ 
    "action": "grantToken",
    "payload": {
        "userId": 1234567,   // subjects telegram userId
        "authCode": "12345"  // authorization code, entered by user
    }
}
```

```javascript=
// inbound queue
{ 
    "uuid": "b9qvvgtk324j2d8gdqjg", // request correlationId
    "action": "grantToken",
    "result": "Ok", 
    "payload": {
        "userId": 1234567,  // telegram user id
        "id" :"53f3e27a124e01dcdd77de45995bf0db" // in-game user id
        "token": "abcdefgh12345768", // access token to be used further
    }
}
```

authAdditionalOperation
-----------------------
_Sends request to broaden tokens operations set to user_

In case your other action failed with `Forbidden` result, your application may fire this action to ask it from user. 

NB: Do not spam with this request or sanctions will follow.

```javascript=
{  
  "token": "abcdefgh12345768",  // target user access token
  "action": "authAdditionalOperation",  
  "payload": {  
    "operation": "GetUserProfile"  // requested operation
   }  
}
```
```javascript=
{  
  "uuid": "baa5u2tk324isodm85og",  
  "result": "Ok",  
  "payload": {  
    "operation": "GetUserProfile",  
    "userId": 1234567  
   }  
}

```

grantAdditionalOperation
------------------------
Completes the `authAdditionalOperation` action 


```javascript=
{  
  "token": "abcdefgh12345768",  // target user access token
  "action": "grantAdditionalOperation",  
  "payload": {  
    "requestId": "baa5u2tk324isodm85og",  // requestId of parent authAdditionalOperation 
    "authCode": "707666" // code supplied by user for this requestId  
   }  
}
```
```javascript=
{  
  "action": "grantAdditionalOperation",  
  "result": "Ok",  
  "payload": {  
    "requestId": "baa5u2tk324isodm85og",  
    "userId": 1234567  
   }  
}

```

authorizePayment
----------------
_Sends authorization request to user with confirmation code in it._

After the authorizePayment command is issued the user will receive forementioned confirmation code which one should pass to the application in order to confirm his willing to transfer the gold/gold pouches to application's account. 


NB: For now the only possible currency is`pouches`
NB2: In case of payments with gold pouches the application balance will be debited with 100 gold per pouch 


```javascript= 
// outbound queue
{
  "token": "abcdef12312341234", // access token
  "action": "authorizePayment",
  "payload": {
    "amount": {
      "pouches": 5 // hold amount
    },
    "transactionId": "7ce30f94-3a8b-4a42-9f28-b2b1220e4a3c" // applications internal transaction id, must be unique
  }
}
```

```javascript=
// inbound queue
{  
  "uuid" : "b9qvvgtk324j2d8gdqjg", // request correlationId  
   "action": "authorizePayment",  
  "result": "Ok",  
  "payload": {  
    "fee": {  
      "gold" : 4 // comission amount  
   },  
    "debit" :{  
      "gold": 496 // the application balance amount will be debited with  
   },  
    "userId": 12345678 // subjects userId  
   }  
}

```



pay
---
_Previously, transfers held an amount of gold from users account to application's balance._

```javascript=
// outbound queue
{
  "token": "abcdef12312341234", // access token
  "action": "pay",
  "payload": {
    "amount": {
      "pouches": 5 // payment amount
    },
    "confirmationCode": "1234", // confirmation code, from previous authorizePayment request 
    "transactionId": "7ce30f94-3a8b-4a42-9f28-b2b1220e4a3c" // applications internal transaction id, must be unique
  }
}
```
```javascript=
// inbound queue
{  
  "uuid": "b9qvvgtk324j2d8gdqjg", // request correlationId  
   "token": "abcdef12312341234", // access token  
   "action": "pay",  
  "result": "Ok",  
  "payload": {  
    "fee": {  
      "gold": 4 // comission amount    
  },  
    "debit": {  
      "gold": 496 // the application balance amount will be debited with    
  },  
    "userId": 12345678 // subjects userId  
   }  
}

```


payout
------
_Transfers of a given amount of gold (or pouches) from the application's balance to users account._

NB: The message will be sent in HTML parsing mode, in case of bad formatting the user will not receive any message at all. Pay attention.
NB2: Do not abuse. Do not send links/spam/scam, otherwise your account will be blocked.

```javascript=
// outbound queue
{  
  "token": "abcdef12312341234",  
  "action": "payout",  
  "payload": {  
    "transactionId": "7ce30f94-3a8b-4a42-9f28-b2b1220e4a3c", // applications internal transaction id, must be unique  
   "amount": {  
      "pouches": 5 // amount of gold pouches application wishes to transfer to user  
   },  
    "message": "You have won 500💰" // arbitrary message, limit - 100 symbols  
   }  
}

```

```javascript=
// inbound queue
{
  "uuid" : "b9qvvgtk324j2d8gdqjg",    
  "action": "payout",
  "result": "Ok", 
  "payload": {
    "userId": 12345678 // subject userdId
  }
}
```


getInfo
-------
_Request current info about your application. E.g. balance, limits, status._

Good for testing purposes. 

```javascript=
// outbound queue
{
    "action" : "getInfo"
}
```

```javascript=
// inbound queue
{
    "action" : "getInfo",
    "result": "Ok", 
    "payload": {
        "balance": 1231242 // your current applications balance
    }
}
```

requestProfile
--------------
_Request brief user profile information_

NB: Requires `GetUserProfile` operation to be allowed for token

```javascript=
{  
  "token": "abcdef12312341234",  // access token
  "action": "requestProfile"  
}
```
```javascript=
{  
  "action": "requestProfile",  
  "result": "Ok",  
  "payload": {  
    "profile": {  
      "atk": 78,
      "castle": "🦌",
      "class": "⚒",
      "def": 145,
      "exp": 108785,
      "gold": 137,
      "guild": "No Correlation",
      "lvl": 35,
      "mana": 880,
      "pouches": 10,
      "stamina": 4,
      "userName": "WolperTinger"  
   },  
    "userId": 12345678  
   }  
}
```
requestStock
------------
_Request users stock information_

NB: Requires `GetStock` operation to be allowed for token


```javascript=
{  
  "token": "1c5c036f2b851a8a7ac9ed485295cf86",  
  "action": "requestStock"  
}
```

```javascript=
{  
  "action": "requestStock",  
  "result": "Ok",  
  "payload": {  
    "stock": {  
      "Bone": 239,  
      "Charcoal": 158,  
      "Cloth": 1,  
      "Cloth jacket": 1,  
      "Coal": 112,  
      "Flour": 1,  
      "Iron ore": 1,  
      "Leather": 2,  
      "Milk": 663,  
      "Mithril shield": 1,  
      "Pelt": 45,  
      "Pouch of gold": 2312,  
      "Powder": 271,  
      "Steel boots": 1,  
      "Stick": 105,  
      "String": 4,  
      "Thread": 131,  
      "Torch": 1  
   },  
    "userId": 12345678  
   }  
}
```


Results
=======

Possible results are:
* `Ok` - Well, everything is Ok
* `BadAmount` - Amount is either less than or equal to zero
* `BadCurrency` - The currency you chose is not allowed
* `BadFormat` - Message format is bad. It could be an invalid JSON, or types are wrong, or not all fields are sane.
* `ActionNotFound` - the action you have requested is absent. Check spelling. 
* `NoSuchUser` - UserId is wrong, or user became inactive
* `NotRegistered` - Your app is not yet registered
* `InvalidCode` - Authorization code is incorrect
* `TryAgain` - if we have some technical difficulties, or bug and are willing for you to repeat request
* `AuthorizationFailed` - some field of transaction is bad or confirmation code is wrong
* `InsufficientFunds` - the player or application balance is insufficient
* `InvalidToken` - no such token, might be revoked?
* `Forbidden` - Your app has no rights to execute this action with this token. Payload will contain `requiredOperation` field. We encourage you to use this field in following `authAdditionalOperation`, instead of enumerating existing ones.
