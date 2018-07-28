---
layout: page
title: Results
navigation: 5
---

# Results
Possible results are:
- `Ok` - Everything is Ok
- `BadAmount` - Amount is either less than or equal to zero
- `BadCurrency` - The currency you chose is not allowed
- `BadFormat` - Message format is bad. It could be an invalid javascript, or types are wrong, or not all fields are sane
- `ActionNotFound` - the action you have requested is absent. Check spelling
- `NoSuchUser` - UserID is wrong, or user became inactive
- `NotRegistered` - Your app is not yet registered
- `InvalidCode` - Authorization code is incorrect
- `TryAgain` - if we have some technical difficulties, or bug and are willing for you to repeat request
- `AuthorizationFailed` - some field of transaction is bad or confirmation code is wrong
- `InsufficientFunds` - the player or application balance is insufficient


## Reactive Payloads
These errors should have some kind of system reacting to the message
- `InvalidToken` - no such token, might be revoked?

```javascript
{
  "result": "InvalidToken",
  "payload": {
    "token": "123abc"
  }
}
```

- `Forbidden` - Your app has no rights to execute this action with this token. Payload will contain `requiredOperation` field. We encourage you to use this field in following `authAdditionalOperation`, instead of enumerating existing ones

```javascript
{
  "result": "Forbidden",
  "payload": {
    "requiredOperation": "Operation", //arbitrary string that is used to refer to the permission.
    "userId": 221846144
  }
}
```

