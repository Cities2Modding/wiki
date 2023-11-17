# Radio

Idea Discussion on Discord: https://discord.com/channels/1169011184557637825/1174449459762057257

Music Loader example: https://github.com/optimus-code/Cities2Modding/blob/main/ExampleMod/MonoBehaviours/MusicLoader.cs

A CS2 radio test repository: https://github.com/dragonofmercy/cs2-customradio

# Radio Station
Example Urban City Radio:

```json
{
    "name": "Urban City Radio",
    "nameId": "Radio.NETWORK_TITLE[Urban City Radio]",
    "description": "Commercial radio stations",
    "descriptionId": "Radio.NETWORK_DESCRIPTION[Urban City Radio]",
    "icon": "Media/Radio/Networks/Commercial.svg",
    "uiPriority": 1,
    "allowAds": true
}
```

Urban City Radio.coc in Audio~/Radio 

# What is a RadioNetwork vs a RadioChannel?

RadioChannel belongs to a RadioNetwork, references it by `name` via `network`

# Loading Audio Files

Audio files gets loaded with metatags

```
this.AddMetaTag(AudioAsset.Metatag.Title, track.Title);
this.AddMetaTag(AudioAsset.Metatag.Album, track.Album);
this.AddMetaTag(AudioAsset.Metatag.Artist, track.Artist);
this.AddMetaTag(AudioAsset.Metatag.Type, track, "TYPE");
this.AddMetaTag(AudioAsset.Metatag.Brand, track, "BRAND");
this.AddMetaTag(AudioAsset.Metatag.RadioStation, track, "RADIO STATION");
this.AddMetaTag(AudioAsset.Metatag.RadioChannel, track, "RADIO CHANNEL");
this.AddMetaTag(AudioAsset.Metatag.PSAType, track, "PSA TYPE");
this.AddMetaTag(AudioAsset.Metatag.AlertType, track, "ALERT TYPE");
this.AddMetaTag(AudioAsset.Metatag.NewsType, track, "NEWS TYPE");
this.AddMetaTag(AudioAsset.Metatag.WeatherType, track, "WEATHER TYPE");
```


Radio Channel declaration:

```json
{
    "network": "Urban City Radio",
    "name": "The Vibe",
    "description": "The Vibe Channel",
    "icon": "Media/Radio/Stations/TheVibe.svg",
    "uiPriority": -1,
    "programs": [
        {
            "name": "Music non stop",
            "description": "Dance all day, dance all night",
            "icon": "coui://UIResources/Media/Radio/TheVibe.svg",
            "type": "Playlist",
            "startTime": "00:00",
            "endTime": "00:00",
            "loopProgram": true,
            "segments": [
                {
                    "type": "Playlist",
                    "tags": [
                        "type:Music",
                        "radio channel:The Vibe"
                    ],
                    "clipsCap": 3
                },
                {
                    "type": "Commercial",
                    "tags": [
                        "type:Commercial"
                    ],
                    "clipsCap": 2
                }
            ]
        }
    ]
}
```

the hard part is to load files into the existing queues
you would need to recreate this to get valid entities
But I think Colossal.IO.AssetDatabase.AudioAsset

Okay, so all audio files are loaded into an AudioSourcePool
So we need to add to thise
it has different groups

it's a lot to research since the radio is very complex
we would need to add our own files as AudioSource and AudioAsset into the game. The game loads the .ogg files as AudioAssets on start into ram and is handling it as AudioSource which is basically a default unity object handling audio
there are so many components that it's really hard to untackle it to fully understand how it works