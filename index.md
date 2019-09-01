---
layout: page
title: Overview
navigation: 1
---

# Chat Wars API v0.12.1
Documentation for Chat Wars API. Chat Wars is an MMORPG hosted on the Telegram platform under the game bot. There are three game bots, but only two of them have an API that is publicly accessible.

| Server | Description |
| --- | --- |
| [Classic](https://t.me/ChatWarsClassicBot) | **This version of the game lacks an API!** The developers have announced that this version of the game will no longer receive updates anymore. The game is completely in Russian, and players who only speak english are encouraged to play on another server, or to use a translation bot to translate text that is unclear. This version of the game was launched in approximately December, 2016. |
| [International](https://t.me/chtwrsbot) | This game bot is recommended to users who speak english, however players that originate from Russia have a restricted ability to join the game. This version of the game was launched in December, 2017. |
| [CW3](https://t.me/chatwarsbot) | CW3 is the most recent iteration of Chat Wars, and receives updates very soon after the international server receives an update. It has no restrictions on who can join the game, however, text is a mixture of Russian and English. This version of the game was launched in Mid 2018. |

## Abstract
Interaction is done via AMQP (by rabbitMQ). Application should publish a message to the appropriate exchange with  an appropriate routing key, and then, listen for and react to incoming messages in its queue. E.g. for the application named `dices` that is running on the international server there would a be direct exchange named `dices_ex`, routing key `dices_o` and an inbound queue `dices_i`

The connection url will be: `amqps://dices:[hidden]@api.chatwars.me:5673/`

Apart from that, there is a set of public fan-out read-only exchanges, where you have your own queue (if requested) and can start receiving updates. The names of this queues have format `%username%_%exchangename%`
(e.g. `dices_deals`)

For now all queues are limited in size by 1Mb, with discard-oldest policy. 

Please note, that at this moment, API is highly unstable.

### Connection

There are two versions of the game with an API, and there are two versions of the API. Refer to this table for what API to connect to. Be aware that an app will not automatically have the same access to both APIs, and you must request access to each API seperately.

| Server | URL |
| --- | --- |
| International | amqps://api.chatwars.me:5673 |
| CW3 | amqps://api.chtwrs.com:5673 |

## FAQ
Q: **Why can I not declare my queues?**

A: This functionality is not permitted for security reasons. Please remove declarations in the API from your code, and simply consume from the queue.

---
Q: **Why is this message continuously repeating itself?**

A: You may not be ack-ing the message the API sent which causes it to come back to you on the next restart of your consumer.

---
Q: **Why am I getting an ACCESS_REFUSED error?**

A: You are not meant to be declaring anything within the API, You only have to consume and publish.

---
Q: **Why can't I log in?**

A: You may not be connecting to the correct instance of the API! If you wish to connect to the CW3 API, This table is a good reference for what API corresponds to what URL

| Server | URL |
| --- | --- |
| International | amqps://api.chatwars.me:5673 |
| CW3 | amqps://api.chtwrs.com:5673 |

---
Q: **How do I obtain access to the API?**

A:

To access the Chat Wars API, you must first obtain a login by emailing to [The support address](mailto:support@chtwrs.freshdesk.com)

A recommended format to use (but not required) can be used to make the request more clear and simple. A sample format has been provided for you.
```plaintext
#api
instance: cw3/cw2/both
name of application: my amazing application
purpose: Make the players have an amazing experience
my telegram name: `@durov`

Requested queues:
sex_digest
deals
offers
yellow_pages
au_digest

Default permissions:
 - request valuables transfer from you
 - transfer valuables to you
 - issue a wtb/wts/rm commands on your behalf
 - read your profile information
 - read your stock info
 - to view your craft or alchemists book
 - to read your guild info (stock, lobby, etc..)

Do you want pouch transactions enabled: yes
```

Modification of your application can also be requested with a format similar to this:
```plaintext
#api
instance: cw3/cw2/both
Request of modification to my app:
name of application: my amazing application

Queues:
remove yellow_pages
add deals

Default Permissions:
remove - request valuables transfer from you
remove - transfer valuables to you
add - read your profile information
add - read your stock info

Access to pouch transactions: removed
```

---
Q: **How do I access a queue?**

A: To access a queue, you must first ensure that you have had one granted. To be granted a queue, simply request a modification to your application with the additional queue added to it. After a queue has been granted to you, you can start consuming from it with `%username%_%queue%`.

*Note: Failure to actually consume from your queue can result in it being revoked*

*The current list of queues available can be found in [This section of the documentation](https://chatwars.github.io/chatwars-api-docs/public-exchanges.html)*

---
Q: **I just got asked to test a feature on a "testing queue". What's that?**

A: This means that new functionality for the API is being tested, and is not yet available for other applications. There is a seperate queue and routing key that you should use in this scenario. When you get asked to do the test, you should consume from the `app_ti` queue, and publish requests with the `app_to` routing key instead. So, if your application is called `dices`, you would be consuming from `dices_ti`, publishing to the `dices_ex` exchange, and use the `dices_to` routing key.

---
Q: **Am I stupid?**

A: Yes.
