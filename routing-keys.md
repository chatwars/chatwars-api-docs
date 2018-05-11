---
layout: page
title: Routing Keys
navigation: 3
---

# Fan-out Exchange Routing Keys
### deals
The confirmed deals are published to the public exchange with the name `deals`.
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
New 'want to sell' offers are published to public exchange with the name `offers` as soon as they come online
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
Top 10, sorted by price, offers on exchange. Posted periodically (approx. every 5 minutes).
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
  // ...
]
```

### yellow_pages
List of all published and open shops with all of their offers and prices (approx. every 5 minutes).
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
    ]
  }    
]
```
