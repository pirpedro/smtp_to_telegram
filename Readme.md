# SMTP to Telegram

[![Docker Hub](https://img.shields.io/docker/pulls/kostyaesmukov/smtp_to_telegram.svg?style=flat-square)][Docker Hub]
[![Go Report Card](https://goreportcard.com/badge/github.com/KostyaEsmukov/smtp_to_telegram?style=flat-square)][Go Report Card]
[![License](https://img.shields.io/github/license/KostyaEsmukov/smtp_to_telegram.svg?style=flat-square)][License]

[License]:         https://github.com/KostyaEsmukov/smtp_to_telegram/blob/master/LICENSE

NOTE: changed the way we handle some enviroment variable to use the secrets standard in a swarm cluster.
For that the variables ST_TELEGRAM_CHAT_IDS and ST_TELEGRAM_BOT_TOKEN become ST_TELEGRAM_CHAT_IDS_FILE and ST_TELEGRAM_BOT_TOKEN_FILE so we can pass a secret path to the app.

`smtp_to_telegram` is a small program which listens for SMTP and sends
all incoming Email messages to Telegram.

Say you have a software which can send Email notifications via SMTP.
You may use `smtp_to_telegram` as an SMTP server so
the notification mail would be sent to the chosen Telegram chats.

## Getting started

1. Create a new Telegram bot: https://core.telegram.org/bots#creating-a-new-bot.
2. Open that bot account in the Telegram account which should receive
   the messages, press `/start`.
3. Retrieve a chat id with `curl https://api.telegram.org/bot<BOT_TOKEN>/getUpdates`.
4. Repeat steps 2 and 3 for each Telegram account which should receive the messages.
5. Build the project with Dockerfile (for amd64) or Dockerfile_arm64 (for arm64v8)
```
docker build -f Dockerfile_arm64 -t smpt_to_telegram .
```
6. Start a docker container:

```
docker run \
    --name smtp_to_telegram \
    -e ST_TELEGRAM_CHAT_IDS_FILE=/path/to/file \
    -e ST_TELEGRAM_BOT_TOKEN_FILE=/path/to/file \
    smtp_to_telegram
```
You can send to multiple Ids, just separate with commas in ST_TELEGRAM_CHAT_IDS_FILE file.

Assuming that your Email-sending software is running in docker as well,
you may use `smtp_to_telegram:2525` as the target SMTP address.
No TLS or authentication is required.

The default Telegram message format is:

```
From: {from}\\nTo: {to}\\nSubject: {subject}\\n\\n{body}\\n\\n{attachments_details}
```

A custom format might be specified as well:

```
docker run \
    --name smtp_to_telegram \
     -e ST_TELEGRAM_CHAT_IDS_FILE=/path/to/file \
    -e ST_TELEGRAM_BOT_TOKEN_FILE=/path/to/file \
    -e ST_TELEGRAM_MESSAGE_TEMPLATE="Subject: {subject}\\n\\n{body}" \
    smtp_to_telegram
```
