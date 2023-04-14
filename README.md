# whasapi

easy way to create a Whatsapp Bot.

- **✨ Effortless**
- **🛒 Collector**
- **🧱 Builder**
- **⏳ Cooldown**
- **🔑 Command handler**
- **🎉 Many more!**

## Installation

```bash
npm install whasapi
```

## Example

```ts
import { Client } from "whasapi";
import { Events } from "whasapi/dist/Constant";

const bot = new Client({
  name: "something",
  prefix: ["!"], //You can also add another prefix ['!', '#']
  readIncommingMsg: true
});

bot.ev.once(Events.ClientReady, (m) => {
  console.log(`ready at ${m.user.id}`);
});

bot.command({
  name: "ping",
  code: async (kaze) => {
    kaze.sendMessage(kaze.id, { text: "pong!" });
  },
});

bot.launch();
```

## Client Configuration

```ts
export interface ClientOptions {
    /* as browser name */ 
    name: string;
    /* the bot prefix */
    prefix: Array<string>|string;
    /* should bot mark as read the incomming messages? */
    readIncommingMsg?: boolean;
    /* path to the auth/creds directory */
    authDir?: string;
    /* print the qr in terminal? */
    printQRInTerminal?: boolean;
}
```

## Command Options

```ts
export interface CommandOptions {
    /* command name */
    name: string;
    /* command aliases */
    aliases?: Array<string>;
    /* command code */
    code: (kaze: Kaze) => Promise<any>;
}
```

## Command Handler

With command handler you dont need all your command is located in one file.

- #### in your main file
  ```ts
  import { CommandHandler } from "whasapi";
  import path from "path";

  /* ... */
  const cmd = new CommandHandler(bot, path.resolve() + '/CommandsPath');
  cmd.load();

  /* ...bot.launch() */
  ```

- #### in your command file
  ```ts
  module.exports = {
      name: "ping",
      code: async (kaze) => {
        kaze.reply({ text: "pong!" });
      },
  };
  ```

## Command Cooldown

Cooldown can give a delay on the command. This can be done to prevent users from spamming your bot commands.

```diff
+ import { Cooldown } from "whasapi";

bot.command({
  name: "ping",
  code: async (kaze) => {
+    const cd = new Cooldown(kaze, 8000);
+    if(cd.onCooldown) return kaze.reply({ text: `slow down... wait ${cd.timeleft}ms` });

    kaze.sendMessage(kaze.id, { text: "pong!" });
  },
});
```

if you want to trigger some function when the cooldown end, you can use the "end" events in the cooldown:

> ⚠
> Will always be triggered when the cooldown is over (even though he only runs the command once)

```ts
cd.on("end", () => {
  kaze.reply({ text: "cd timeout" });
})
```

Cooldown getter:

```ts
/* check if sender is on cooldown */
cd.onCooldown; // boolean

/* check the cooldown time left (in ms) */
cd.timeleft; // number
```

## Builder

- #### Button
  make a button message with Button Builder.

  ```ts
  import { ButtonBuilder } from "whasapi";

  // you can use more than 1 builder
  const btn = new ButtonBuilder()
    .setId("id1") // button id
    .setDisplayText("button 1") // button text
    .setType(1); // type

  // pass it into buttons array
  kaze.sendMessage(kaze.id, { text: "buttons", buttons: [btn] });
  ```

- #### Sections
  Sections message is like a list.

  ```ts
  import { SectionBuilder } from "whasapi";

  // you can use more than 1 like buttons
  const a = new SectionBuilder()
    .setTitle("title") // sections title
    .setRows(
      { title: "abc", rowId: 1 },
      { title: "b", rowId: 2, description: "a" }
    ); // make a rows

  kaze.sendMessage(kaze.id, {
    text: "sections",
    buttonText: "button text", // buttonText is for the display text for the button
    sections: [a], // pass it into sections array
  });
  ```

## Collector

There are several options that can be used in the collector:
```ts
export interface CollectorArgs {
    /* collector timeout in milliseconds */
    time?: number;
    /* how many messages have passed through the filter */
    max?: number;
    /* will be stop if end reason is match with your col.stop reason  */
    endReason?: string[];
    /* limit how many messages must be processed. */
    maxProcessed?: number;
    /* a function as a filter for incoming messages. */
    filter?: () => boolean;
}
```

- #### Message Collector
  ```ts
  let col = kaze.MessageCollector({ time: 10000 }); // in milliseconds
  kaze.reply({ text: "say something... Timeout: 10s" });

  col.on("collect", (m) => {
      console.log("COLLECTED", m); // m is an Collections
      kaze.sendMessage(kaze.id, {
          text: `Collected: ${m.content}\nFrom: ${m.sender}`,
      });
  });

  col.on("end", (collector, r) => {
      console.log("ended", r); // r = reason
      kaze.sendMessage(kaze.id, { text: `Collector ended` });
  });
  ```

## Events

Firstly you must import the Events Constant like this:
```ts
import { Events } from "whasapi/dist/Constant";
```


- #### Available Events:
  - **ClientReady** - Emitted when the bot client is ready.
  - **MessagesUpsert** - Received an messages.
  - **QR** - The bot QR is ready to scan. Return the QR Codes.
  - **GroupsJoin** - Emitted when bot joining groups.
  - **UserJoin** - Emitted when someone joins a group where bots are also in that group.
  - **UserLeave** - Same with **UserJoin** but this is when the user leaves the group.

## Misc

```ts
/* replying message */
kaze.reply({ text: "test" });
kaze.reply({ ... });

/* add react */
kaze.react(jid: string, emoji: string, key?: object);
kaze.react(kaze.id, "👀");

/* get the bot ready at timestamp */
bot.readyAt;

/* get the current jid */
kaze.id // string;

/* get the array of arguments used */
kaze.args // Array<string>;

/* get sender details */
kaze.sender // { jid: string, pushName: string }
```


## ```Thanks to ✨```

[![adiwajshing](https://github.com/adiwajshing.png?size=70)](https://github.com/adiwajshing/Baileys)
[![KazeDevID](https://github.com/KazeDevID.png?size=70)](https://github.com/KazeDevID/whasapi)
