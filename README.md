# spdl-core

Spotify track downloader module. Written in pure javascript.

> Requires node >=14

# Credentials

The module need a Spotify `clientId` and `clientSecret` to work, the module provide you one but if too much people use the module with this credentials, you will be rate limited and the module won't work.
To avoid this, you should set your own credentials.
To do that, go to [The Spotify developers dashboard](https://developer.spotify.com/dashboard/applications) and create an application.
Copy your credentials and paste it with:
```js
spdl.setCredentials("Your app's clientId", "Your app's clientSecret");
// or
spdl.setClientId("Your app's clientId");
spdl.setClientSecret("Your app's clientSecret");
```

# Support
You can contact us for support on our [chat server](https://discord.gg/AUfTUJA)

# Usage

```js
const fs = require('fs');
const spdl = require('spdl-core').default;
// Typescript: import spdl from 'spdl-core';

spdl.getInfo('https://open.spotify.com/track/3fjmSxt0PskST13CSdBUFx?si=e420cd3a80834011').then(infos => {
  spdl(infos.url).then(stream => {
    stream.on('end', () => console.log('Done!'));
    stream.pipe(fs.createWriteStream(`${infos.title}.mp3`));
  });
});
```

# API
### async spdl(url, options?)

Downloads the track from the given url. Returns a [readable stream](https://nodejs.org/api/stream.html#stream_class_stream_readable)
Options are the options for ytdl-core

### async spdl.getInfo(url)

Gives the information of a track

### spdl.validateURL(url)

Returns true if url is a spotify track link

### spdl.setCredentials("Your app's clientId", "Your app's clientSecret")
### spdl.setClientId("Your app's clientId")
### spdl.setClientSecret("Your app's clientSecret")

Sets the module credentials to yours

## Limitations

Generated download links are valid for 6 hours, and may only be downloadable from the same IP address.

# Install

```bash
npm install spdl-core@latest
```

Or for Yarn users:
```bash
yarn add spdl-core@latest
```

Make sure you're installing the latest version of spdl-core to keep up with the latest fixes.

# Discord Bot

You'll need to install `discord.js` and `@discordjs/opus`.

```js
const { MessageEmbed, Client } = require('discord.js');
const spdl = require('spdl-core');
function formatDuration(duration) {
  let seconds = duration / 1000;
  return `${Math.floor(seconds / 60)}m ${Math.floor(seconds % 60)}s`;
}

const client = new Client();
client.login('Your Discord Bot Token');
client.on('ready', () => console.log('Ready'));

client.on('message', async (msg) => {
  if (!msg.content.startsWith('!play')) return;
  const url = msg.content.split('!play ')[1];
  if (!spdl.validateURL(url)) return msg.channel.send('Invalid URL');
  const channel = msg.member.voice.channel;
  if (!channel) return msg.channel.send('Not in a voc channel');
  try {
    const connection = await channel.join();
    connection
      .play(await spdl(url))
      .on('error', e => console.error(e));
    const infos = await spdl.getInfo(url);
    const embed = new MessageEmbed()
      .setTitle(`Now playing: ${infos.title}`)
      .setURL(infos.url)
      .setColor('#1DB954')
      .addField('Artist', infos.artist, true)
      .addField('Duration', formatDuration(infos.duration), true)
      .setThumbnail(infos.thumbnail);
    msg.channel.send(embed);
  } catch (err) {
    console.error(err);
    msg.channel.send(`An error occurred: ${err.message}`);
  }
});
```

# Give me your opinion 

[![](https://gitrate.herokuapp.com/happy?ref=SkyDonald.spdl-core)](https://gitrate.herokuapp.com/happy/done?ref=SkyDonald.spdl-core)
[![](https://gitrate.herokuapp.com/unhappy?ref=SkyDonald.spdl-core)](https://gitrate.herokuapp.com/unhappy/done?ref=SkyDonald.spdl-core)

# Related Projects

- [discord-spdl-core](https://www.npmjs.com/package/discord-spdl-core) - Simple spdl wrapper for discord bots with custom ffmpeg args support.
- [spdl](https://www.npmjs.com/package/spdl) - A cli wrapper of this.

# Note

There is nothing illegal here, the module just searches for the song on Youtube and downloads it.
