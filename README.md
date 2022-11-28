<h1 align="center">CodeChat Api Pro</h1>

It is a version of this API with advanced features like message queue management, send processing status, integration with **[n8n](https://github.com/code-chat-br/n8n-nodes-codechat)** unique models and much more! **[Check it out](https://github.com/code-chat-br/docs-codechat)** 😉!

<div align="center">
  <a href="https://github.com/code-chat-br/docs-codechat" target="_blank" rel="noopener noreferrer">
    <img src="./public/images/code.png" style="width: 35% !important;">
  </a>
</div>

</br>
<hr style="height: 5px;background: #007500;margin: 20px 0;box-shadow: 0px 3px 5px 0px rgb(204 204 204);">

<div align="center">

[![Telegram](https://img.shields.io/badge/Group-Telegram-%2333C1FF)](https://t.me/codechatBR)
[![Whatsapp](https://img.shields.io/badge/WhatsApp-message-%2322BC18)](https://api.whatsapp.com/send?phone=5531995918699)
[![License](https://img.shields.io/badge/license-GPL--3.0-orange)](./LICENSE)

</div>
  
<div align="center"><img src="./public/images/cover.png"></div>

## WhatsApp-Api-NodeJs

This code is an implementation of [Baileys](https://github.com/adiwajshing/Baileys), as a RestFull Api service, which controls whatsapp functions.</br>
With this one you can create multiservice chats, service bots or any other system that uses whatsapp. With this code you don't need to know javascript for nodejs , just start the server and make the language requests that you feel most comfortable with.

## Infrastructure

### Nvm installation

```sh
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
# or
$ wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```
### Docker installation \[optional\]

```sh
$ curl -fsSL https://get.docker.com -o get-docker.sh

$ sudo sh get-docker.sh

$ sudo usermod -aG docker ${USER}

$ sudo apt-get install docker-compose
```
### Nodejs installation

```sh
$ nvm install 16.10.0
```
>
> After finishing, restart the terminal to load the new information.
>
```sh
$ docker --version

$ node --version

$ docker-compose --version
```
## MongoDb

After installing docker and docker-compose, up the container.
  - [compose from mongodb](./mongodb/docker-compose.yaml)

In the same directory where the file is located, run the following command:
```sh
$ docker-compose up -d
```
Using the database is optional.

## Application startup

Go to the project directory and install all dependencies.</br>
```sh
$ npm i
// or
$ yarn install
```

Finally, run the command below to start the application:
```sh
# Under development
$ npm run start
// or
$ yarn start

# In production
$ npm run build
$ npm run start:prod
// or
$ yarn build
$ yarn start:prod
```
## Authentication

You can define two authentication **types** for the routes in the **[env file](./src/env.yml)**.
Authentications must be inserted in the request header.

1. **apikey**

2. **jwt:** A JWT is a standard for authentication and information exchange defined with a signature.

> Authentications are generated at instance creation time.

**Note:** There is also the possibility to define a global api key, which can access and control all instances.

### Connection

#### Create an instance

##### HTTP

```http
POST /instance/create HTTP/1.1
Host: localhost:8080
Content-Type: application/json
Content-Length: 34

{
  "instanceName": "codechat"
}
```
##### cURL

```bash
curl --location --request POST 'http://localhost:8080/instance/create' \
--header 'Content-Type: application/json' \
--data-raw '{
  "instanceName": "codechat"
}'
```
### Response

```ts
{
  "instance": {
    "instanceName": "codechat",
    "status": "created"
  },
  "hash": {
    "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9. [...]"

    // or
    // "apikey": "88513847-1B0E-4188-8D76-4A2750C9B6C3"
  }
}
```
#### Connection with qrcode

##### HTTP

```http
GET /instance/connect/codechat HTTP/1.1
Host: localhost:8080
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9. [...]
```
```http
GET /instance/connect/codechat HTTP/1.1
Host: localhost:8080
apikey: 88513847-1B0E-4188-8D76-4A2750C9B6C3
```
##### cURL

```bash
curl --location --request GET 'http://localhost:8080/instance/connect/codechat' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9. [...]'
```
```bash
curl --location --request GET 'http://localhost:8080/instance/connect/codechat' \
--header 'apikey: 88513847-1B0E-4188-8D76-4A2750C9B6C3'
```

### Response

```ts
{
  "code": "2@nXSUgRJSBY6T0XJmiFKZ0 [...] ,XsgJhJHYa+0MPpXANdPHHt6Ke/I7O2QyXT/Lsge0uSg=",
  "base64": "data:image/png;base64,iVBORw0KGgoAAAANSUhE [...] LkMtqAAAAABJRU5ErkJggg=="
}
```

### App in Docker
  - [Dockerfile](./Dockerfile)
  - [docker-compose](./docker-compose.yaml)

After building the application, in the same directory as the files above, run the following command:
```sh
$ docker-compose up
```

## Routes
  - [Postman Json](./postman.json)

## Webhook Events

| Name | Event | TypeData | Description |
|------|-------|-----------|------------|
| QRCODE_UPDATED | qrcode.updated | json | Sends the base64 of the qrcode for reading |
| CONNECTION_UPDATE | connection.update | json | Informs the status of the connection with whatsapp |
| INSTANCE | instance | json | Informs instance loading |
| MESSAGES_SET | message.set | json | Sends a list of all your messages uploaded on whatsapp</br>This event occurs only once |
| MESSAGES_UPSERT | message.upsert | json |  Notifies you when a message is received |
| MESSAGES_UPDATE | message.update | json | Tells you when a message is updated |
| SEND_MESSAGE | send.message | json | Notifies when a message is sent |
| CONTACTS_SET | contacts.set | json | Performs initial loading of all contacts</br>This event occurs only once |
| CONTACTS_UPSERT | contacts.upsert | json | Reloads all contacts with additional information</br>This event occurs only once |
| CONTACTS_UPDATE | contacts.update | json | Informs you when the chat is updated |
| PRESENCE_UPDATE | presence.update | json |  Informs if the user is online, if he is performing some action like writing or recording and his last seen</br>'unavailable' | 'available' | 'composing' | 'recording' | 'paused' |
| CHATS_SET | chats.set | json | Send a list of all loaded chats |
| CHATS_UPDATE | chats.update | json | Informs you when the chat is updated |
| CHATS_UPSERT | chats.upsert | json | Sends any new chat information |

## Env File

See additional settings that can be applied through the **env** file by clicking **[here](./src/env.yml)**.

## SSL

To install the SSL certificate, follow the **[instructions](https://certbot.eff.org/instructions?ws=other&os=ubuntufocal)** below.

# Note

This code is in no way affiliated with WhatsApp. Use at your own discretion. Don't spam this.

This code was produced based on the baileys library and it is still under development.
</br>