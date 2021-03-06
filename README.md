[![PayPal donate button](https://img.shields.io/badge/paypal-donate-yellow.svg)](https://www.paypal.me/jishi "Donate once-off to this project using Paypal")

Feel free to use it as you please. Consider donating if you want to support further development.

SONOS HTTP API
==============

** Beta is no more, master is up to date with the beta now! **

**This application requires node 4.0.0 or higher!**

**This should now work on Node 6+, please let me know if you have issues**

A simple http based API for controlling your Sonos system.

There is a simple sandbox at /docs (incomplete atm)

USAGE
-----

Start by fixing your dependencies. Invoke the following command:

`npm install --production`

This will download the necessary dependencies if possible.

start the server by running

`npm start`

Now you can control your system by invoking the following commands:

	http://localhost:5005/zones
	http://localhost:5005/lockvolumes
	http://localhost:5005/unlockvolumes
	http://localhost:5005/pauseall[/{timeout in minutes}]
	http://localhost:5005/resumeall[/{timeout in minutes}]
	http://localhost:5005/preset/{JSON preset}
	http://localhost:5005/preset/{predefined preset name}
	http://localhost:5005/reindex
	http://localhost:5005/{room name}/sleep/{timeout in seconds or "off"}
	http://localhost:5005/{room name}/sleep/{timeout in seconds or "off"}
	http://localhost:5005/{room name}/{action}[/{parameter}]

Example:

`http://localhost:5005/living room/volume/15`
(will set volume for room Living Room to 15%)

`http://localhost:5005/living room/volume/+1`
(will increase volume by 1%)

`http://localhost:5005/living room/next`
(will skip to the next track on living room, unless it's not a coordinator)

`http://localhost:5005/living room/pause`
(will pause the living room)

`http://localhost:5005/living room/favorite/mysuperplaylist`
(will replace queue with the favorite called "mysuperplaylist")

`http://localhost:5005/living room/repeat/on`
(will turn on repeat mode for group)


The actions supported as of today:

* play
* pause
* playpause (toggles playing state)
* volume (parameter is absolute or relative volume. Prefix +/- indicates relative volume)
* groupVolume (parameter is absolute or relative volume. Prefix +/- indicates relative volume)
* mute / unmute
* groupMute / groupUnmute
* togglemute (toggles mute state)
* trackseek (parameter is queue index)
* timeseek (parameter is in seconds, 60 for 1:00, 120 for 2:00 etc)
* next
* previous
* state (will return a json-representation of the current state of player)
* favorite
* favorites (with optional "detailed" parameter)
* playlist
* lockvolumes / unlockvolumes (experimental, will enforce the volume that was selected when locking!)
* repeat (on/off)
* shuffle (on/off)
* crossfade (on/off)
* pauseall (with optional timeout in minutes)
* resumeall (will resume the ones that was pause on the pauseall call. Useful for doorbell, phone calls, etc. Optional timeout)
* say
* sayall
* queue
* clearqueue
* sleep (values in seconds)
* linein (only analog linein, not PLAYBAR yet)
* clip (announce custom mp3 clip)
* clipall
* join / leave  (Grouping actions)
* sub (on/off/gain/crossover/polarity) See SUB section for more info
* nightmode (on/off, PLAYBAR only)
* speechenhancement (on/off, PLAYBAR only)


State
-----

Example of a state json:

	{
	  "currentTrack":{
	    "artist":"College",
	    "title":"Teenage Color - Anoraak Remix",
	    "album":"Nightdrive With You",
	    "albumArtURI":"/getaa?s=1&u=x-sonos-spotify%3aspotify%253atrack%253a3DjBDQs8ebkxMBo2V8V3SH%3fsid%3d9%26flags%3d32",
	    "duration":347,
	    "uri":"x-sonos-spotify:spotify%3atrack%3a3DjBDQs8ebkxMBo2V8V3SH?sid=9&flags=32"
	  },
	  "nextTrack":{
	    "artist":"Blacknuss",
	    "title":"Thinking of You",
	    "album":"3",
	    "albumArtURI":"/getaa?s=1&u=x-sonos-spotify%3aspotify%253atrack%253a4U93TIa0X6jGQrTBGTkChH%3fsid%3d9%26flags%3d32",
	    "duration":235,
	    "uri":"x-sonos-spotify:spotify%3atrack%3a4U93TIa0X6jGQrTBGTkChH?sid=9&flags=32"
	  },
	  "volume":18,
	  "mute":false,
	  "trackNo":161,
	  "elapsedTime":200,
	  "elapsedTimeFormatted":"03:20",
	  "zoneState":"PAUSED_PLAYBACK",
	  "playerState":"PLAYING",
	  "zonePlayMode":{
	    "shuffle":true,
	    "repeat":false,
	    "crossfade":false
	  }
	}

Queue
-----
Obtain the current queue list from a specified player. The request will accept:
 - No parameters

	`http://localhost:5005/living room/queue`

Example queue response:
```
[
    {
      "uri": "x-sonos-spotify:spotify%3atrack%3a0AvV49z4EPz5ocYN7eKGAK?sid=9&flags=8224&sn=3",
      "albumArtURI": "/getaa?s=1&u=x-sonos-spotify%3aspotify%253atrack%253a0AvV49z4EPz5ocYN7eKGAK%3fsid%3d9%26flags%3d8224%26sn%3d3",
      "title": "No Diggity",
      "artist": "Blackstreet",
      "album": "Another Level"
    },
    {
      "uri": "x-sonos-spotify:spotify%3atrack%3a5OQGeJ1ceykovrykZsGhqL?sid=9&flags=8224&sn=3",
      "albumArtURI": "/getaa?s=1&u=x-sonos-spotify%3aspotify%253atrack%253a5OQGeJ1ceykovrykZsGhqL%3fsid%3d9%26flags%3d8224%26sn%3d3",
      "title": "Breathless",
      "artist": "The Corrs",
      "album": "In Blue"
    }
]

```


Preset
------

A preset is a predefined grouping of players with predefined volumes, that will start playing whatever is in the coordinators queue.

Example preset (state and uri are optional):

	{
	  "players": [
	    { "roomName": "room1", "volume": 15},
	    {"roomName": "room2", "volume": 25}
	  ],
	  "state": "stopped",
	  "favorite": "my favorite name",
	  "uri": "x-rincon-stream:RINCON_0000000000001400",
	  "playMode": {
	    "shuffle": true
	  },
	  "pauseOthers": true
	  "sleep": 600
	}

The first player listed in the example, "room1", will become the coordinator. It will loose it's queue when ungrouped but eventually that will be fixed in the future. Playmode defines the three options "shuffle", "repeat", "crossfade" similar to the state
Favorite will have precedence over a uri.
pauseOthers will pause all zones before applying the preset, effectively muting your system.  sleep is an optional value that enables the sleep timer and is defined in total seconds (600 = 10 minutes).

presets.json (deprecated, use preset files instead)
-----------

You can create a file with pre made presets, called presets.json. It will be loaded upon start, any changes requires a restart of the server.

Example content:

```json
{
  "all": {
    "playMode": {
      "shuffle": true
    },
    "players": [
      {
        "roomName": "Bathroom",
        "volume": 10
      },
      {
        "roomName": "Kitchen",
        "volume": 10
      },
      {
        "roomName": "Office",
        "volume": 10
      },
      {
        "roomName": "Bedroom",
        "volume": 10
      },
      {
        "roomName": "TV Room",
        "volume": 15
      }
    ],
    "pauseOthers": true
  },
  "tv": {
    "players": [
      {
        "roomName": "TV Room",
        "volume": 20
      }
    ],
    "pauseOthers": true,
    "uri": "x-rincon-stream:RINCON_000XXXXXXXXXX01400"
  }
}
```


In the example, there is one preset called `all`, which you can apply by invoking:

`http://localhost:5005/preset/all`


presets folder
--------------

You can create a preset files in the presets folder with pre made presets. It will be loaded upon start, any changes made to files in this folder (addition, removal, modification) will trigger a reload of your presets. The name of the file (xxxxxx.json) will become the name of the preset. It will be parsed as JSON5, to be more forgiving of typos. See http://json5.org/ for more info.

Example content:

```json
{
  "players": [
    {
      "roomName": "Bathroom",
      "volume": 10
    },
    {
      "roomName": "Kitchen",
      "volume": 10
    },
    {
      "roomName": "Office",
      "volume": 10
    },
    {
      "roomName": "Bedroom",
      "volume": 10
    },
    {
      "roomName": "TV Room",
      "volume": 15
    }
  ],
  "playMode": {
    "shuffle": true,
    "repeat": "all",
    "crossfade": false
  },
  "pauseOthers": false,
  "favorite": "My example favorite"
}
```

There is an example.json bundled with this repo. The name of the file will become the name of the preset.

settings.json
-------------

If you want to change default settings, you can create a settings.json file and put in the root folder. This will be parsed as JSON5, to be more forgiving. See http://json5.org/ for more info.

Available options are:

* port: change the listening port
* https: use https which requires a key and certificate or pfx file
* auth: require basic auth credentials which requires a username and password
* announceVolume: the percentual volume use when invoking say/sayall without any volume parameter
* presetDir: absolute path to look for presets (folder must exist!)


Example:
```json
	{
	  "voicerss": "Your api key for TTS with voicerss",
	  "microsoft": {
	    "key": "Your api for Bing speech API",
	    "name": "ZiraRUS"
	  },
	  "port": 5005,
	  "securePort": 5006,
	  "https": {
	    "key": "/path/to/key.pem",
	    "cert" : "/path/to/cert.pem"

	    //... for pfx (alternative configuration)
	    "pfx": "/path/to/pfx.pfx",
	    "passphrase": "your-passphrase-if-applicable"
	  },
	  "auth": {
	    "username": "admin",
	    "password": "password"
	  },
	  "announceVolume": 40,
	  "pandora": {
	    "username": "your-pandora-account-email-address",
	    "password": "your-pandora-password"
	  },
	  "library": { 
	    "randomQueueLimit": 50 
	  } 
	}
```

Override as it suits you.



Favorites
---------

It now has support for starting favorites. Simply invoke:

`http://localhost:5005/living room/favorite/[favorite name]`

and it will replace the queue with that favorite. Bear in mind that favorites may share name, which might give unpredictable behavior at the moment.

Playlist
---------

Playing a Sonos playlist is now supported. Invoke the following:

`http://localhost:5005/living room/playlist/[playlist name]`

and it will replace the queue with the playlist and starts playing.


Say (TTS support)
-----------------

Experimental support for TTS. Today the following providers are available:

* voicerss
* Microsoft Cognitive Services (Bing Text to Speech API)
* AWS Polly
* Google (default)

It will use the one you configure in settings.json. If you define settings for multiple TTS services, it will not be guaranteed which one it will choose!

#### VoiceRSS

This REQUIRES a registered API key from voiceRSS! See http://www.voicerss.org/ for info.

You need to add this to a file called settings.json (create if it doesn't exist), like this:

```
{
  "voicerss": "f5e77e1d42063175b9219866129189a3"
}
```

Replace the code above (it is just made up) with the api-key you've got after registering.

Action is:

	/[Room name]/say/[phrase][/[language_code]][/[announce volume]]
	/sayall/[phrase][/[language_code]][/[announce volume]]

Example:

	/Office/say/Hello, dinner is ready
	/Office/say/Hej, maten är klar/sv-se
	/sayall/Hello, dinner is ready
	/Office/say/Hello, dinner is ready/90
	/Office/say/Hej, maten är klar/sv-se/90

language code needs to be before volume if specified.

Sayall will group all players, set 40% volume (by default) and then try and restore everything as the way it where. Please try it out, it will probably contain glitches but please report detailed descriptions on what the problem is (starting state, error that occurs, and the final state of your system).

The supported language codes are:

| Language code | Language |
| ------------- | -------- |
| ca-es | Catalan  |
| zh-cn | Chinese (China) |
| zh-hk |Chinese (Hong Kong) |
| zh-tw | Chinese (Taiwan) |
| da-dk | Danish |
| nl-nl | Dutch |
| en-au | English (Australia) |
| en-ca | English (Canada) |
| en-gb | English (Great Britain) |
| en-in | English (India) |
| en-us | English (United States) |
| fi-fi | Finnish |
| fr-ca | French (Canada) |
| fr-fr | French (France) |
| de-de | German |
| it-it | Italian |
| ja-jp | Japanese |
| ko-kr | Korean |
| nb-no | Norwegian |
| pl-pl | Polish |
| pt-br | Portuguese (Brazil) |
| pt-pt | Portuguese (Portugal) |
| ru-ru | Russian |
| es-mx | Spanish (Mexico) |
| es-es | Spanish (Spain) |
| sv-se | Swedish (Sweden) |

#### Microsoft
This one also requires a registered api key. You can sign up for free here: https://www.microsoft.com/cognitive-services/en-US/subscriptions?mode=NewTrials and select "Bing Speech - Preview".

The following configuration is available (the entered values except key are default, and may be omitted):

```json
	{
	  "microsoft": {
	    "key": "Your api for Bing speech API",
	    "name": "ZiraRUS"
	  }
	}
```

You change language by specifying a voice name correlating to the desired language.
Name should be specified according to this list: https://www.microsoft.com/cognitive-services/en-us/speech-api/documentation/API-Reference-REST/BingVoiceOutput#SupLocales
where name is the right most part of the voice font name (without optional Apollo suffix). Example:

`Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)` name should be specified as `Hoda`

`Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)` name should be specified as `Stefan`

`Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)` name should be specified as `BenjaminRUS`

Action is:

	/[Room name]/say/[phrase][/[name]][/[announce volume]]
	/sayall/[phrase][/[name]][/[announce volume]]

Example:

	/Office/say/Hello, dinner is ready
	/Office/say/Hello, dinner is ready/BenjaminRUS
	/Office/say/Guten morgen/Stefan
	/sayall/Hello, dinner is ready
	/Office/say/Hello, dinner is ready/90
	/Office/say/Guten morgen/Stefan/90

Supported voices are:

Hoda, Hedda, Stefan, Catherine, Linda, Susan, George, Ravi, ZiraRUS, BenjaminRUS, Laura, Pablo, Raul, Caroline, Julie, Paul, Cosimo, Ayumi, Ichiro, Daniel, Irina, Pavel, HuihuiRUS, Yaoyao, Kangkang, Tracy, Danny, Yating, Zhiwei

See https://www.microsoft.com/cognitive-services/en-us/speech-api/documentation/API-Reference-REST/BingVoiceOutput#SupLocales to identify
which language and gender it maps against.

#### AWS Polly

Requires AWS access tokens, which you generate for your user. Since this uses the AWS SDK, it will look for settings in either Environment variables, the ~/.aws/credentials or ~/.aws/config.

You can also specify it for this application only, using:
```json
	{
	  "aws": {
	    "credentials": {
	      "region": "eu-west-1",
	      "accessKeyId": "Your access key id",
	      "secretAccessKey": "Your secret"
	    },
	    "name": "Joanna"
	  }
	}
```

Choose the region where you registered your account, or the one closest to you. Polly is only supported in US East (Northern Virginia), US West (Oregon), US East (Ohio), and EU (Ireland) as of today (dec 2016)

If you have your credentials elsewhere and want to stick with the default voice, you still need to make sure that the aws config option is set to trigger AWS TTS:

```json
	{
	  "aws": {}
	}
```

Action is:

	/[Room name]/say/[phrase][/[name]][/[announce volume]]
	/sayall/[phrase][/[name]][/[announce volume]]

Example:

	/Office/say/Hello, dinner is ready
	/Office/say/Hello, dinner is ready/Nicole
	/Office/say/Hej, maten är klar/Astrid
	/sayall/Hello, dinner is ready
	/Office/say/Hello, dinner is ready/90
	/Office/say/Hej, maten är klar/Astrid/90

This is the current list of voice names and their corresponding language and accent (as of Dec 2016).
To get a current list of voices, you would need to use the AWS CLI and invoke the describe-voices command.

| Language | Code | Gender | Name |
| --------- | ---- | ------ | ---- |
| Australian English | en-AU | Female | Nicole |
| Australian English | en-AU | Male | Russell |
| Brazilian Portuguese | pt-BR | Female | Vitoria |
| Brazilian Portuguese | pt-BR | Male | Ricardo |
| British English | en-GB | Male | Brian |
| British English | en-GB | Female | Emma |
| British English | en-GB | Female | Amy |
| Canadian French | fr-CA | Female | Chantal |
| Castilian Spanish | es-ES | Female | Conchita |
| Castilian Spanish | es-ES | Male | Enrique |
| Danish | da-DK | Female | Naja |
| Danish | da-DK | Male | Mads |
| Dutch | nl-NL | Male | Ruben |
| Dutch | nl-NL | Female | Lotte |
| French | fr-FR | Male | Mathieu |
| French | fr-FR | Female | Celine |
| German | de-DE | Female | Marlene |
| German | de-DE | Male | Hans |
| Icelandic | is-IS | Male | Karl |
| Icelandic | is-IS | Female | Dora |
| Indian English | en-IN | Female | Raveena |
| Italian | it-IT | Female | Carla |
| Italian | it-IT | Male | Giorgio |
| Japanese | ja-JP | Female | Mizuki |
| Norwegian | nb-NO | Female | Liv |
| Polish | pl-PL | Female | Maja |
| Polish | pl-PL | Male | Jacek |
| Polish | pl-PL | Male | Jan |
| Polish | pl-PL | Female | Ewa |
| Portuguese | pt-PT | Female | Ines |
| Portuguese | pt-PT | Male | Cristiano |
| Romanian | ro-RO | Female | Carmen |
| Russian | ru-RU | Female | Tatyana |
| Russian | ru-RU | Male | Maxim |
| Swedish | sv-SE | Female | Astrid |
| Turkish | tr-TR | Female | Filiz |
| US English | en-US | Male | Justin |
| US English | en-US | Female | Joanna |
| US English | en-US | Male | Joey |
| US English | en-US | Female | Ivy |
| US English | en-US | Female | Salli |
| US English | en-US | Female | Kendra |
| US English | en-US | Female | Kimberly |
| US Spanish | es-US | Female | Penelope |
| US Spanish | es-US | Male | Miguel |
| Welsh | cy-GB | Female | Gwyneth |
| Welsh English | en-GB-WLS | Male | Geraint |

#### Google (default if no other has been configured)

Does not require any API keys. Please note that Google has been known in the past to change the requirements for its Text-to-Speech API, and this may stop working in the future. There is also limiations to have many request one is allowed to do in a specific time period.

The following language codes are supported

| Language code | Language |
| ------------- | -------- |
| af | Afrikaans |
| sq | Albanian |
| ar | Arabic |
| hy | Armenian |
| bn | Bengali |
| ca | Catalan |
| zh | Chinese |
| zh-cn | Chinese (Mandarin/China) |
| zh-tw | Chinese (Mandarin/Taiwan) |
| zh-yue | Chinese (Cantonese) |
| hr | Croatian |
| cs | Czech |
| da | Danish |
| nl | Dutch |
| en | English |
| en-au | English (Australia) |
| en-gb | English (Great Britain) |
| en-us | English (United States) |
| eo | Esperanto |
| fi | Finnish |
| fr | Franch |
| de | German |
| el | Greek |
| hi | Hindi |
| hu | Hungarian |
| is | Icelandic |
| id | Indonesian |
| it | Italian |
| ja | Japanese |
| ko | Korean |
| la | Latin |
| lv | Latvian |
| mk | Macedonian |
| no | Norwegian |
| pl | Polish |
| pt | Portuguese |
| pt-br | Portuguese (Brazil) |
| ro | Romanian |
| ru | Russian |
| sr | Serbian |
| sk | Slovak |
| es | Spanish |
| es-es | Spanish (Spain) |
| es-us | Spanish (United States) |
| sw | Swahili |
| sv | Swedish |
| ta | Tamil |
| th | Thai |
| tr | Turkish |
| vi | Vietnamese |
| cy | Welsh |

Action is:

	/[Room name]/say/[phrase][/[language_code]][/[announce volume]]
	/sayall/[phrase][/[language_code]][/[announce volume]]

Line-in
-------

Convenience method for selecting line in. Will select linein for zone-group, not detach it for line-in.
Optional parameter is line-in from another player. Examples:

`/Office/linein`
Selects line-in on zone Office belongs to, with source Office.

`/Office/linein/TV%20Room`
Selects line-in for zone Office belongs to, with source TV Room.

If you want to to isolate a player and then select line-in, use the `/Office/leave` first.

Clip
----

Like "Say" but instead of a phrase, reference a custom track from the `static/clips` folder. There is a sample file available (courtesy of https://www.sound-ideas.com/).

    /{Room name}/clip/{filename}[/{announce volume}]
    /clipall/{filename}[/{announce volume}]

Examples:

    /clipall/sample_clip.mp3
    /clipall/sample_clip.mp3/80
    /Office/clip/sample_clip.mp3
    /Office/clip/sample_clip.mp3/30

*Pro-tip: announce your arrival with an epic theme song!*

Grouping
--------

You have basic grouping capabilities. `join` will join the selected player to the specified group (specify group by addressing any of the players in that group):

`/Kitchen/join/Office`
This will join the Kitchen player to the group that Office currently belong to.

`/Kitchen/leave`
Kitchen will leave any group it was part of and become a standalone player.

You don't have to ungroup a player in order to join it to another group, just join it to the new group and it will jump accordingly.

SUB
---

SUB actions include the following:
`/TV%20Room/sub/off`
Turn off sub

`/TV%20Room/sub/on`
Turn on sub

`/TV%20Room/sub/gain/3`
Adjust gain, -15 to 15. You can make bigger adjustments, but I'm limiting it for now because it might damage the SUB.

`/TV%20Room/sub/crossover/90`
adjust crossover frequency in hz. Official values are 50 through 110 in increments of 10. Use other values at your own risk! My SUB gave a loud bang and shut down when setting this to high, and I thought I broke it. However, a restart woke it up again.

`/TV%20Room/sub/polarity/1`
Switch "placement adjustment" or more commonly known as phase. 0 = 0°, 1 = 180°

Spotify and Apple Music (Experimental)
----------------------

Allows you to perform your own external searches for Apple Music or Spotify songs or albums and play a specified song or track ID. The Music Search funtionality outlined further below performs a search of its own and plays the specified music. 

The following endpoints are available:

```
# Spotify
/RoomName/spotify/now/spotify:track:4LI1ykYGFCcXPWkrpcU7hn
/RoomName/spotify/next/spotify:track:4LI1ykYGFCcXPWkrpcU7hn
/RoomName/spotify/queue/spotify:track:4LI1ykYGFCcXPWkrpcU7hn

# Apple Music
/RoomName/applemusic/{now,next,queue}/song:{songID}
/RoomName/applemusic/{now,next,queue}/album:{albumID}
```

You can find Apple Music song and album IDs via the [iTunes Search
API](https://affiliate.itunes.apple.com/resources/documentation/itunes-store-web-service-search-api/).

It only handles a single spotify account currently. It will probably use the first account added on your system. 


SiriusXM
----------------------
You can specify a SiriusXM channel number or station name and the station will be played.

```
/RoomName/siriusXM/{channel number,station name}
```


Pandora
----------------------
Perform a search for one of your Pandora stations and begin playing. Give the currently playing song a thumbs up or thumbs down. Requires a valid Pandora account and credentials. 

The following endpoints are available:

```
/RoomName/pandora/play/{station name}     Plays the closest match to the specified station name in your list of Pandora stations
/RoomName/pandora/thumbsup                Gives the current playing Pandora song a thumbs up
/RoomName/pandora/thumbsdown              Gives the current playing Pandora song a thumbs down 
```

Your Pandora credentials need to be added to the settings.json file
   ```
          ,
          "pandora": {
            "username": "your-pandora-account-email-address",
            "password": "your-pandora-password"
          }
  ```
 

Tunein
----------------------
Given a station id this will play the streaming broadcast via the tunein service. You can find tunein station ids via services like [radiotime](http://opml.radiotime.com/)

The following endpoint is available:

```
/RoomName/tunein/play/{station id}
```
 

Music Search and Play
----------------------
Perform a search for a song, artist, album or station and begin playing. Supports Apple Music, Spotify, Deezer, Deezer Elite, and your local Music Library. 

The following endpoint is available:

```
/RoomName/musicsearch/{service}/{type}/{search term}

Service options: apple, spotify, deezer, elite, library

Type options for apple, spotify, deezer, and elite: album, song, station 
Station plays a Pandora like artist radio station for a specified artist name. 
Apple Music also supports song titles and artist name + song title.

Type options for library: album, song, load 
Load performs an initial load or relaod of the local Sonos music library. 
The music library will also get loaded the first time that the library service is 
used if the load command has not been issued before.

Search terms for song for all services: artist name, song title, artist name + song title
Search terms for album for all services: artist name, album title, artist name + album title

Search terms for station for apple: artist name, song title, artist name + song title
Search terms for station for spotify and deezer: artist name
Search terms for station for library: not supported

Specifying just an artist name will load the queue with up to 50 of the artist's most popular songs
Specifying a song title or artist + song title will insert the closest match to the song into 
the queue and start playing it. More than 50 tracks can be loaded from the local library by using 
library.randomQueueLimit in the settings.json file to set the maximum to a higher value.

Examples:
/Den/musicsearch/spotify/song/red+hot+chili+peppers
/Kitchen/musicsearch/apple/song/dark+necessities
/Playroom/musicsearch/library/song/red+hot+chili+peppers+dark+necessities

/Den/musicsearch/spotify/album/abbey+road
/Playroom/musicsearch/library/album/red+hot+chili+peppers+the+getaway
/Kitchen/musicsearch/spotify/album/dark+necessities

/Den/musicsearch/spotify/station/red+hot+chili+peppers
/Kitchen/musicsearch/apple/station/dark+necessities  (Only Apple Music supports song titles)
/Playroom/musicsearch/apple/station/red+hot+chili+peppers+dark+necessities  (Only Apple Music supports song titles)

/Kitchen/musicsearch/library/load  (Loads or reloads the music library from Sonos)
```


Experiment with these and leave feedback!

Webhook
-------

NOTE! This is experimental and might change in the future! Please leave your feedback as github issues if you feel like it doesn't suit your need, since I don't know what kind of restrictions you will be facing.

Since 0.17.x there is now support for a web hook. If you add a setting in settings.json like this:

```
{
  "webhook": "http://localhost:5007/"
}
```

Every state change and topology change will be posted (method POST) to that URL, as JSON. The following data structure will be sent:

```
{
  "type": "transport-state",
  "data": { (snapshot of player) }
}
```

or

```
{
  "type": "topology-change",
  "data": { (snapshot of zones) }
}
```

```
{
  "type": "volume-change",
  "data": {
    "uuid": "RINCON_000000000000001400",
    "previousVolume": 14,
    "newVolume": 16,
    "roomName": "Office"
  }
}
```

```
{
  "type": "mute-change",
  "data": {
    "uuid": "RINCON_000000000000001400",
    "previousMute": true,
    "previousMute": false,
    "roomName": "Office"
  }
}
```

"data" property will be equal to the same data as you would get from /RoomName/state or /zones. There is an example endpoint in the root if this project called test_endpoint.js which you may fire up to get an understanding of what is posted, just invoke it with "node test_endpoint.js" in a terminal, and then start the http-api in another terminal.


DOCKER
-----

Docker usage is maintained by [Chris Nesbitt-Smith](https://github.com/chrisns) at [chrisns/docker-node-sonos-http-api](https://github.com/chrisns/docker-node-sonos-http-api)