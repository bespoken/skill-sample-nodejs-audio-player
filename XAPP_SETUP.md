# Streaming XAPPs
To use the Streamer with XAPPs, you need to:
* Configure the XAPP
* Setup the URL
* Configure the interaction model

Each of these steps is described below:
# Setup the XAPP
The current behavior is:
## Introduction
For the introduction (i.e., the audio that is played when the Skill is first opened), the Now Playing Text will be used, if it is set. The text can include SSML tags (such as <audio> or <break>).
If the Now Playing Text is not set, the Base Audio of the XAPP will be used.

## Custom Actions
Custom Actions can do a number of things. They can: 
* Stream a playlist (a set of tracks) in the custom data
* Stream a single track in the custom data
* Stream a trailing audio file (to be played as a stream)
* Respond with TTS (similar to current XAPPlet)
* Respond with "standard" audio

The distinction between streaming and responding is whether it uses the AudioPlayer or plays it back as part of a dialog.

Each of these methods is described below:

### Streaming A Playlist
Here is example JSON for the custom data:
```json
{
  "tracks": [
    {"title": "Track1", "url": "https://mystreaming.com/MyAudio.m4a"},
    {"title": "Track2", "url": "https://mystreaming.com/MyAudio.m4a"},
  ]
}
```

When the custom action is invoked, the playlist will begin playback.

### Streaming a track
Example:
```json
{"title": "Track1", "url": "https://mystreaming.com/MyAudio.m4a"},
```

Note, we support this as well as playing back the uploaded trailing audio as this can be pointed at an actual, continuous stream, as opposed to just a file.

### Streaming Uploaded Audio
Just upload a file as the Foreground trailing audio, then set the custom data like so:
```json
{"feed": true}
```

### Responding with TTS
Same as how we currently do it:
```json
{"tts": "My text-to-speech"},
```
Note that the TTS can include SSML elements (such <audio> and <break>). Use single-quotes for SSML tags.

### Responding with Audio
This will respond with the uploaded foreground trailing audio.

# Setup the URL
The base URL is:
```
https://streaming.bespoken.tools
```

Two parameters need to be set with it:
environment - Either XappMediaTest or AlexaDemo (the publisher name)
XAPP - The full tag for the XAPP

The parameters are case-sensitive, so must be entered exactly as above. Here is an example:
```
https://streaming.bespoken.tools?XAPP=Brand+Haiku/HomePageStreaming&environment=AlexaDemo
```

# Configure the interaction model
The interaction model should be done in the normal way, but it must include the AudioPlayer BuiltIn intents. These are:
```json
  {
    "intents":[  
    {"intent": "AMAZON.CancelIntent"},
    {"intent": "AMAZON.HelpIntent"},
    {"intent": "AMAZON.LoopOnIntent"},
    {"intent": "AMAZON.LoopOffIntent"},
    {"intent": "AMAZON.NextIntent"},
    {"intent": "AMAZON.NoIntent"},
    {"intent": "AMAZON.PauseIntent"},
    {"intent": "AMAZON.PreviousIntent"},
    {"intent": "AMAZON.ResumeIntent"},
    {"intent": "AMAZON.ShuffleOnIntent"},
    {"intent": "AMAZON.ShuffleOffIntent"},
    {"intent": "AMAZON.StartOverIntent"},
    {"intent": "AMAZON.StopIntent"},
    {"intent": "AMAZON.YesIntent"}
  ]}
```

## Other Notes
There is a slight "easter egg" in the Streamer, which is that if you say Play Audio, it will begin playing back any streams defined with the XAPP.

The PlayAudio intent must be defined:
```
"intent": "PlayAudio"
```
So, if there are three custom actions, each with a single track defined, it will begin playing back all three tracks in the sequence they are defined in the XAPP.

The nice thing here is that is easy to manage a number of audio files as individual custom actions.

But I have not figured out an nice way to sequence them - using the order of the custom actions is lame. And setting a property on the custom actions I think will be awkward to maintain.

I think we can come up with a better solution for this.

