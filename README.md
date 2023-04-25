# poe-api-node
A reverse engineered Node.js client for Quora's Poe. 

Support: Fetch needed info and write to .env file | send messages to different bot | set up proxies | clear/delete/purge messages | get history messages | get bot info | get next data

> I'm still working on this, so it's might not stable. If your meet any problems, please create an issue.
- [poe-api-node](#poe-api-node)
  - [Install](#install)
  - [Usage](#usage)
    - [SendMessage](#sendmessage)
    - [AddChatBreak](#addchatbreak)
    - [DeleteMessage](#deletemessage)
    - [PurgeAllMessage](#purgeallmessage)
    - [GetHistory](#gethistory)
    - [GetBotInfo](#getbotinfo)
    - [GetNextData](#getnextdata)
    - [UpdateAllBotInfo](#updateallbotinfo)
  - [Example](#example)
    - [SendMessage](#sendmessage-1)
    - [SetProxy](#setproxy)
    - [History](#history)
    - [FetchAllNeededInfo](#fetchallneededinfo)
  - [TODO](#todo)
  - [Notes](#notes)
  - [Thanks](#thanks)
  - [License](#license)

## Install
```bash
npm install poe-node-api
```
> requirement: 
> - node >= 18
> - .env: To store needed params like `poe-formkey` / `cookie` / `buildId` / botId.....

## Usage
1. Get cookie from poe.com: F12 / inspect, Application > Cookies > https://poe.com > p-b

2. Create .env file in your project root path, and add cookie to .env file
```env
cookie=p-b=xxxxxxxxxxxxxxxxxxxxxxxxxxx
```

3. Create a client
```ts
const client = new PoeClient({debug: false});
await client.init()
```
- cookie?: string
- proxy?: ProxyInfo
  - ```ts
    interface ProxyInfo{
      socks?: {
          host: string,
          port: number,
      }
      https?: {
          proxy?: string,
          allProxy?: string,
      }
    }
    ```
- debug?: boolean, enable debug console output
- fetch?: FetchFunction
  - type FetchFunction = typeof fetch

4. choose a bot

botNickName <==> botDisplayName
> - a2 <==> Claude-instant
> - a2_2 <==> Claude+
> - beaver <==> GPT-4
> - capybara <==> Sage
> - nutria <==> Dragonfly
> - chinchilla <==> ChatGPT
> - hutia <==> NeevaAI


### SendMessage
```ts
/**
  * send message to bot
  * @param text user input
  * @param botNickName bot nick name, like capybara(Sage) / a2(Claude-instant) / a2_2(Claude+) etc.
  * @param withChatBreak Add a chat break or not. (Empty context)
  * @param callback When ws on message, will invoke this callback function.
 */
await client.sendMessage(text, botNickName, withChatBreak, (result: string) => {console.log(`${result}`)})
```
> text: string
> 
> botNickName: string
> 
> withChatBreak: boolean
> 
> callback: (result: string) => void

### AddChatBreak
```ts
const res = await client.addChatBreak(botNickName);
```
- botNickName: string

### DeleteMessage
```ts
const res = await client.deleteMessage(messageIds);
```
- messageIds: number[], messageIds to delete


### PurgeAllMessage
```ts
const res = await client.purgeAllMessage();
```
> Delete all bot messages, equals to click poe.com > Settings > Delete all messages

### GetHistory
```ts
const history = await client.getHistory(botNickName, count);
```
- botNickName: string
- count?: number,  Messages's count to get. (default: 25)

### GetBotInfo
```ts
const history = await client.getBotByNickName(botNickName, retryCount, retryIntervalMs);
```
- botNickName: string
- retryCount?: number, If fetch bot info failed, will retry.
- retryIntervalMs?: number, ms time to wait before next fetch. (default: 2000)

### GetNextData
```ts
const history = await client.getNextData();
```
> Can get `poe-formkey` / `buildId` / `latest messages`(like latest 5 messages) and `startCursor`(use startCursor to fetch history messages) / `availableBots` / `x-forwarded-for` / ......

### UpdateAllBotInfo
```ts
await client.updateAllBotInfo()
```
> This function will fetch poe-formkey, buildId, latest messages, startCursor and all bots info(chatId / id, this two params will be used to ). 
> 
> This function will set `poe-formkey` / `cookie` / `buildId` / `${botDisplayName}_chatId` and `${botDisplayName}_id` to **.env** file(These parameters are the same as cookies and do not need to be requested every time). 
> 
> Next time you send a msg to any bot, will not need to fetch bot info again, client will get needed params like `buildId` from local .env file.

## Example
### SendMessage
[example - sendMessage.ts](example/sendMsg.ts)

### SetProxy
[example - proxy.ts](example/proxy.ts)

### History
[example - history.ts](example/history.ts)

### FetchAllNeededInfo
[example - fetchAllNeededInfo.ts](example/fetchAllNeededInfo.ts)


## TODO
- [ ] Add type definition to bots/viewer/nextData

## Notes
- I'm working on one of my project which can integrate any bot like chatgpt by plugin. But I can't find any poe node api, so I try to write this client. Huge thanks to [poe-api](https://github.com/ading2210/poe-api)(ISC License) and [poe](https://github.com/muharamdani/poe)(MIT License).
- .graphql files is merge from two repos above and find a new graphql to purge all message.
- If you meet any problems, please create an issue.
- I'm not familiar with ts, feel free give suggestions | create pull request.

## Thanks
- [poe-api](https://github.com/ading2210/poe-api)
- [poe](https://github.com/muharamdani/poe)
- [chatgpt-api](https://github.com/transitive-bullshit/chatgpt-api)

## License
MIT