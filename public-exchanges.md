---
layout: page
title: Public Exchanges
navigation: 3
---
## Public Exchanges
### deals
_Would you look at all the purchases people make, which are totally not bots!_

As soon as someone successfully purchases an item from the Stock Exchange, this exchange will be notified of the event.
```javascript
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
_Good intentions here, right guys? Guys...?_

When an item becomes available for purchase on the Stock Exchange, this exchange will be notified of the event.
```javascript
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
_Arguably the least useful exchange to exist. I mean, at least theres someone using it right?_

Every 5 minutes, all the cheapest offers for every item on offer in the Stock Exchange are pumped out to this exchange.
```javascript
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
 //....
]
```

### yellow_pages
_Comes by a bit more frequently than once a year_

Every 5 minutes, this exchange will receive a list of all the player shops that are open and published. 

Specialization field makes it percentagewise clear in what kind of gear this particular blacksmith is skilled in

```javascript
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
    ],
    "specialization": {
        "coat": 34,
        "helmet": 75
    }
  }    
]
```

### au_digest
_I'll buy this one and that one! Wait! Stop outbidding your own teammates!_

Every 3 minutes, a list of all active auction lots will be sent to this exchange.
```javascript
[
 {
    "lotId": "71499",
    "itemName": "Hunter dagger",
    "sellerName": "E them Up",
    "sellerCastle": "🦌",
    "endAt": "2018-07-15T20:23:38.217Z",
    "startedAt": "2018-07-15T16:20:16.851Z",
    "buyerCastle": "🦌",
    "price": 9
  },
  {
    "lotId": "71500",
    "itemName": "📗Scroll of Peace",
    "sellerName": "kritik",
    "sellerCastle": "🌑",
    "endAt": "2018-07-16T04:36:15.183Z",
    "startedAt": "2018-07-15T16:34:50.722Z",
    "buyerCastle": "🌑",
    "price": 1
  },
  {
    "lotId": "71501",
    "itemName": "📘Scroll of Peace",
    "sellerName": "BandYlly",
    "sellerCastle": "🦌",
    "endAt": "2018-07-15T20:36:10.675Z",
    "startedAt": "2018-07-15T16:35:53.257Z",
    "price": 0
  }
]
```
