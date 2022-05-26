---
title: "Audio Splitting in Linux"
date: 2022-05-24T12:50:35-07:00
draft: false
tags: ["linux", "guide", "audio"]
---

## Pre-Requisites
You'll need to have both `pulseaudio` and `pavucontrol` installed on your system

### Install For Ubuntu
```bash
sudo apt-get pulseaudio pavucontrol
```

### Install For Arch

```bash
pacman -S pulseaudio pavucontrol

# OR

yay -S pulseaudio pavucontrol
```

## Setting up a new audio sink
Make a new directory called `.pulse` in your home directory, this is the default `pulseaudio` config folder. 
**Note** that depending on your OS, the default `pulseaudio` directory could be in a different path or different folder name. Check your `man` pages with `man pulseaudio`.
```bash
 mkdir ~/.pulse
```

Copy the default `pulseaudio` config, `default.pa`, into the new folder.
```bash
cp /etc/pulse/default.pa ~/.pulse/
``` 

Make sure the new file is in your directory
```bash
ls -la ~/.pulse
```

Next, you need to find all of your current audio sinks:
```bash
pacmd list-sinks | grep -e 'index:' -e 'name:'
```

This will show all of your audio output devices. Your output should look something like this:
```
    index: 0
	name: <alsa_output.pci-0000_01_00.1.hdmi-stereo>
  * index: 1
	name: <alsa_output.usb-Razer_Razer_Kraken_7.1_V2_00000000-00.analog-stereo>
    index: 2
	name: <alsa_output.pci-0000_00_1f.3.analog-stereo>
```

The `*` symbol shows your currently active audio output.

Copy the audio output you want to direct audio towards. For me, I want to be able to send all audio source I'm monitoring to my headset. 
So I would copy `alsa_output.usb-Razer_Razer_Kraken_7.1_V2_00000000-00.analog-stereo`.
Paste this in a empty text document. You'll need this info later.

Next, you'll want to make a dummy audio sink. To do this, open up 
`~/.pulse/default.pa` in a text editor. Go all the way to the bottom of the document and use this template to define your new audio sink.

Here is a template that you can use:
```
load-module module-null-sink sink_name=channelname 
update-sink-proplist channelname device.description="channelname Audio" 
update-source-proplist channelname.monitor device.description='Monitor of channelname Audio' 
load-module module-loopback source=channelname.monitor sink=outputdevicename latency_msec=1
```

Replace `channelname` with your audio sink name, then replace `outputdevicename` with the audio output device we copied earlier.

### Example
I used this to better control which audio source I want to be streamed on twitch through OBS but still be able to monitor and hear everything.
Here's what my `default.pa` looks like

```
load-module module-null-sink sink_name=twitchaudio
update-sink-proplist twitchaudio device.description='Twitch Streaming Audio'
update-source-proplist twitchaudio.monitor device.description='Monitor of Twitch Audio'
load-module module-loopback source=twitchaudio.monitor sink=alsa_output.usb-Razer_Razer_Kraken_7.1_V2_00000000-00.analog-stereo latency_msec=1
```

## Restart Pulseaudio And All Of Your Apps

After you're done making custom audio sinks, make sure to restart `pulseaudio` using this command:

```bash
pulseaudio --kill
```

This should automatically restart `pulseaudio`, but if it doesn't, you can manually start it with:
```
pulseaudio --start
```

After restarting `pulseaudio`, you'll find that there's no audio. You'll need to restart all your apps that streams audio to your speakers.

## Pulseaudio Volume Control App (pavucontrol)
From here launch `pavucontrol` and you'll be able to see your new audio sink in the `recording, playback, and output devices` tabs. `
This app will let you control where you want all of your audio sources to be directed to.

## Misc
For more info, check out the arch wiki page: [wiki.archlinux.org](https://wiki.archlinux.org/title/PulseAudio/Examples#Remapping_sinks)
