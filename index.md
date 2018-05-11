---
layout: page
title: Overview
navigation: 1
---

# Chat Wars API v0.6
Documentation for Chat Wars API. Chat Wars is Telegram MMORPG [game](https://t.me/chtwrsbot).

## Abstract
Interaction is done via AMQP (by rabbitMQ). Application should publish a message to the appropriate exchange with  an appropriate routing key, and then, listen for and react to incoming messages in its queue. E.g. for the application named `dices` there would a be direct exchange named `dices_ex`, routing key `dices_o` and an inbound queue `dices_i` (for the testing purpose there will be `dices_to` and `dices_ti` queues respectively)

The connection url will be: `amqps://dices:[hidden]@api.chatwars.me:5673/`

Apart from that, there is a set of public fan-out read-only exchanges, where you have your own queue (if requested) and can start receiving updates. The names of this queues have format `%username%_%exchangename%`
(e.g. `dices_deals`)

For now all queues are limited in size by 1Mb, with discard-oldest policy. 

Please note, that at this moment, API is highly unstable.
