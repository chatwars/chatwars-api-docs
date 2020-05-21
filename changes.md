---
layout: page
title: Changes
navigation: 2
---

## Latest Changes

### **Version 1.0.0**
- AMQP public exchanges are *DEPRECATED* in favor of Kafka. They will be removed in a near future. No new queues will be created whatsoever. The scheme left unchanged.

### **Version 0.12.4**

- `guildInfo` now has boolean field `repair` which reflects is the gear maintenance orders by guild members is allowed to be done using guild stock instead of their own.

### **Version 0.12.3**

- both `guildInfo` and `requestStock` responses now has `itemCodes` filed which contains codes of items that guild/user has on stock.

### **Version 0.12.2**

- `roles` are added to `guildInfo`
- `yellow_pages` now reflects the current quality craft skill level of shop

### **Version 0.12.1**

- `requestProfile` and `guildInfo` now contains emoji if present

### **Version 0.12.0**

- `requestProfile` response now reflects current and maximum user HP
- `requestGearInfo` have now `gearInfo` field, that will supersede `gear` field, which is deprecated from now on and will be removed in future releases.

### **Version 0.11.0**

- `au_digest` line now contains info about item's condition, if applicable
- `yellow_pages` shop line now has info about maintenance mode

### **Version 0.10.0**

- `requestGearInfo` added, requires `GetGearInfo` permission

### **Version 0.9.9**

- `guildInfo` and `requestStock` are extended to include their current size and limits

### **Version 0.9.8**

! BREAKING CHANGE !

- `au_digest` model extended a bit to include current lot state. Finished and cancelled auctions are added as well.

### **Version 0.9.7**

- `au_digest` model extended to reflect item stats and for finished auctions it will include buyer name, along with its castle

### **Version 0.9.6**

- Duelist model now has HP field.

### **Version 0.9.5**

- New `guildDiscount`/`castleDiscount` fields in `yellow_pages`

### **Version 0.9.4**

- new fanout `duels`

### **Version 0.9.3**

- new field `quality` in `au_digest`

### **Version 0.9.2**

- new object `specialization` in `yellow_pages`

### **Version 0.9.1**

- `requestBasicInfo` action added

### **Version 0.9**

- `viewCraftbook` action added

### **Version 0.8**

- `guildInfo` action added

### **Version 0.7**

- `au_digest` queue added
  - sends data every 3 minutes
- End of Changes

### **Version 0.6**

- `wantToBuy` method added
- `InvalidToken` response fixed
- End of Changes

### **Version 0.5**

- `yellow_pages` queue added
  - sends data every 5 minutes
- more data added to `requestProfile` method
- End of Changes

### **Version 0.4**

- `requestProfile` method added
- `requestStock` method added
- `authAdditionalOperation` method added
- `grantAdditionalOperation` method added
- `sex_digest` queue added
  - sends data every 5 minutes
- End of Changes

### **Version 0.3.1**

- Modified result code from `OK` to `Ok`
- End of Changes

### **Version 0.3** (initial release)

- `createAuthCode` method added
- `grantToken` method added
- `authorizePayment` method added
- `pay` method added
- `payout` method added
- `getInfo` method added
- `deals` queue added
- `offers` queue added
- End of Changes
