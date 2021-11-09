---
layout: page
title: Message Types
navigation: 4
---

## Message Types

### createAuthCode

_Access request from your application to the user._

After this command is issued, game bot will send a notification to the user containing authorization code. If one agrees to grant you access, it will pass this code to your application.

```javascript
// outbound queue
{
  "action": "createAuthCode",
  "payload": {
    "userId": 1234567 // subjects Telegram userId
  }
}
```

In case of a success, your application will get following message.

```javascript
// inbound queue
{
  "uuid": "b9qvvgtk324j2d8gdqjg", // request correlationId
  "action": "createAuthCode",
  "result": "Ok",
  "payload": {
    "userId": 1234567 // subjects Telegram userId
  }
}
```

### grantToken

_Exchange auth code for access token._

Having the access code, your application should obtain access token, in order to work with most of the API methods. As of now token lifetime is unlimited.

```javascript
// outbound queue
{
  "action": "grantToken",
  "payload": {
    "userId": 1234567, // subjects Telegram userId
    "authCode": "12345" // authorization code, entered by user
  }
}
```

```javascript
// inbound queue
{
  "uuid": "b9qvvgtk324j2d8gdqjg", // request correlationId
  "action": "grantToken",
  "result": "Ok",
  "payload": {
    "userId": 1234567, // telegram user id
    "id": "53f3e27a124e01dcdd77de45995bf0db", // in-game user id
    "token": "abcdefgh12345768" // access token to be used further
  }
}
```

### authAdditionalOperation

_Sends request to broaden tokens operations set to user._

In case your other action failed with `Forbidden` result, your application may fire this action to ask it from user.

Available permissions:
* `GetBasicInfo`
* `GetUserProfile`
* `ViewCraftbook`
* `GetGearInfo`
* `GetStock`
* `GuildInfo`
* `TradeTerminal`

**NB:** Do not spam with this request or sanctions will follow.

```javascript
// outbound queue
{
  "token": "abcdefgh12345768", // target user access token
  "action": "authAdditionalOperation",
  "payload": {
    "operation": "GetUserProfile" // requested operation
  }
}
```

```javascript
// inbound queue
{
  "uuid": "baa5u2tk324isodm85og",
  "result": "Ok",
  "payload": {
    "operation": "GetUserProfile",
    "userId": 1234567
  }
}
```

### grantAdditionalOperation

Completes the `authAdditionalOperation` action.

```javascript
// outbound queue
{
  "token": "abcdefgh12345768", // target user access token
  "action": "grantAdditionalOperation",
  "payload": {
    "requestId": "baa5u2tk324isodm85og", // requestId of parent authAdditionalOperation
    "authCode": "707666" // code supplied by user for this requestId
   }
}
```

```javascript
// inbound queue
{
  "action": "grantAdditionalOperation",
  "result": "Ok",
  "payload": {
    "requestId": "baa5u2tk324isodm85og",
    "userId": 1234567
   }
}
```

### authorizePayment

_Sends authorization request to user with confirmation code in it._

After the authorizePayment command is issued the user will receive forementioned confirmation code which one should pass to the application in order to confirm his willing to transfer the gold/gold pouches to application's account.

**NB:** For now the only possible currency is `pouches`

**NB2:** In case of payments with gold pouches the application balance will be debited with 100 gold per pouch

```javascript
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

```javascript
// inbound queue
{
  "uuid": "b9qvvgtk324j2d8gdqjg", // request correlationId
  "action": "authorizePayment",
  "result": "Ok",
  "payload": {
    "fee": {
      "gold": 4 // comission amount
    },
    "debit": {
      "gold": 496 // the application balance amount will be debited with
    },
    "userId": 12345678 // subjects Telegram userId
  }
}
```

### pay

_Previously, transfers held an amount of gold from users account to application's balance._

```javascript
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

```javascript
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

### payout

_Transfers of a given amount of gold (or pouches) from the application's balance to users account._

**NB:** The message will be sent in HTML parsing mode, in case of bad formatting the user will not receive any message at all. Pay attention.

**NB2:** Do not abuse. Do not send links/spam/scam, otherwise your account will be blocked.

```javascript
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

```javascript
// inbound queue
{
  "uuid": "b9qvvgtk324j2d8gdqjg",
  "action": "payout",
  "result": "Ok",
  "payload": {
    "userId": 12345678 // subject Telegram userdId
  }
}
```

### getInfo

_Request current info about your application. E.g. balance, limits, status._

Good for testing purposes.

```javascript
// outbound queue
{
  "action": "getInfo"
}
```

```javascript
// inbound queue
{
  "action": "getInfo",
  "result": "Ok",
  "payload": {
    "balance": 1231242 // your current applications balance
  }
}
```

### viewCraftbook

_Request the list of recipes known to user._

**NB:** Requires `ViewCraftbook` operation to be allowed for token

```javascript
// outbound queue
{
  "token": "abcdef12312341234", // access token
  "action": "viewCraftbook"
}
```

```javascript
// inbound queue
{
    "action": "viewCraftbook",
    "result": "Ok",
    "userId": 12345678,
    "payload": {
        "alchemy": [],
        "craft": [{
            "id": "33",
            "name": "Metal plate",
            "price": 1
        }, {
            "id": "19",
            "name": "Steel",
            "price": 1
        }, {
            "id": "505",
            "name": "Wooden arrows pack",
            "price": 1
        }, {
            "id": "24",
            "name": "Purified powder",
            "price": 1
        }, {
        ...
    }
}
```

### requestProfile

_Request brief user profile information._

**NB:** Requires `GetUserProfile` operation to be allowed for token

```javascript
// outbound queue
{
  "token": "abcdef12312341234", // access token
  "action": "requestProfile"
}
```

```javascript
// inbound queue
{
  "action": "requestProfile",
  "result": "Ok",
  "payload": {
    "profile": {
      "atk": 78,
      "castle": "🦌",
      "class": "⚒",
      "secondaryClass": {
        "class": "📦",
        "lvl": 88
      },
      "def": 145,
      "hp": 122,
      "maxHp": 1377,
      "exp": 108785,
      "gold": 137,
      "guild": "No Correlation",
      "guild_tag": "NaN",
      "guild_emoji": "👬",
      "lvl": 35,
      "status": "Busy",
      "action": "Quest",      
      "mana": 880,
      "pouches": 10,
      "stamina": 4,
      "userName": "WolperTinger"
    },
    "userId": 12345678
  }
}
```

### requestBasicInfo

_Request basic user stats._

Base attack and defence (equipment bonuses are not included) and current class.

**NB:** Requires `GetBasicInfo` operation to be allowed for token

```javascript
// outbound queue
{
  "token": "abcdef12312341234", // access token
  "action": "requestBasicInfo"
}
```

```javascript
// inbound queue
{
  "action": "requestBasicInfo",
  "result": "Ok",
  "payload": {
    "profile": {
      "class": "⚒",
      "atk": 78,
      "def": 145,
    },
    "userId": 12345678
  }
}
```

### requestGearInfo

_Request user's current outfit._

Keep in mind, that slot names and their amount can be changed without any notice.

**NB:** Requires `GetGearInfo` operation to be allowed for token

```javascript
// outbound queue
{
  "token": "abcdef12312341234", // access token
  "action": "requestGearInfo"
}
```

```javascript
// inbound queue
{
  "action": "requestGearInfo",
  "result": "Ok",
  "payload": {
    "gearInfo": {
      "head": {
          "name": "Steel Helmet",
          "atk": 1,
          "def": 3
          },
      "weapon": {
          "name": "Trollhammer",
          "stam": 10,
          "luck": 3,
          "atk": 87,
          "def": 113,
          "loot": 3,
          "condition": "Normal",
          "quality": "Excellent"
      },
    "ammo": {
       "Silver Arrows": 77
    },
    "userId": 12345678
  }
}
```

### requestStock

_Request users stock information._

**NB:** Requires `GetStock` operation to be allowed for token

```javascript
// outbound queue
{
  "token": "1c5c036f2b851a8a7ac9ed485295cf86",
  "action": "requestStock"
}
```

```javascript
// inbound queue
{
  "action": "requestStock",
  "result": "Ok",
  "payload": {
    "stockSize": 1700,
    "stockLimit": 4000,
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
    "itemCodes": {
      "04": "Bone",
      "06": "Charcoal",
      "09": "Cloth"
      // ...
    },
    "userId": 12345678
  }
}
```

### guildInfo

_Request users guild information. Common info and stock. Excluding roster._

**NB:** Requires `GuildInfo` operation to be allowed for token

```javascript
// outbound queue
{
  "token": "1c5c036f2b851a8a7ac9ed485295cf86",
  "action": "guildInfo"
}
```

```javascript
// inbound queue
{
  "action": "guildInfo",
  "result": "Ok",
  "payload": {
    "tag": "OOF",
    "level": 9,
    "castle": "🦌",
    "emoji": "👬",
    "glory": 19287,
    "members": 32,
    "name": "Out Of Fuel",
    "lobby": "We hack, we slash, we duck, we dash!",
    "stockSize": 1700,
    "stockLimit": 4000,
    "repair": false,
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
    "itemCodes": {
      "04": "Bone",
      "06": "Charcoal",
      "09": "Cloth"
      // ...
    },
    "roles": ["Bartender"],
    "userId": 12345678
  }
}
```

### wantToBuy

_Issues an wtb order on behalf of user._

**NB:** Requires `TradeTerminal` operation to be allowed for token

```javascript
// outbound queue
{
  "token": "abcdef12312341234",
  "action": "wantToBuy",
  "payload": {
    "itemCode": "20", // the code of an item
    "quantity": 5,
    "price": 5, // desired price
    "exactPrice": true // try to buy exactly for given price, fail otherwise
  }
}
```

```javascript
// inbound queue
{
  "action": "wantToBuy",
  "result": "Ok",
  "payload": {
    "itemName": "Leather",
    "quantity": 5,
    "userId": 1234567
  }
}
```
