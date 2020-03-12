# NValhalla

Is a simple DeepStream test app to perform live redaction an an arbitrary number of sources. NValhalla is written in [Genie, a Vala dialect](https://wiki.gnome.org/Projects/Genie).

Usage is `nvalhalla --uri rtsp://uri-goes-here/ --uri file://local/file/here.mp4 ...` where each --uri supplied is a valid uri accepted by [uridecodebin](https://gstreamer.freedesktop.org/documentation/playback/uridecodebin.html?gi-language=c). Full help, including --gst options are available with --help

## Requirements

- hardware: A Tegra device (tested on Jetson Nano and Jetson Xavier). X86/NVIDIA with DeepStream installed *may* work, however this configuration has not been tested.
- software: `sudo apt install libgstreamer1.0-dev libglib2.0-dev libgee-0.8-dev libgstrtspserver-1.0-dev deepstream-4.0 valac meson`

## Installation

```shell
git clone https://github.com/mdegans/nvalhalla.git
cd nvalhalla
mkdir build
cd build
meson ..
ninja
sudo ninja install
```

(this installs to `/usr/local` prefix, same as make)

`sudo ninja uninstall` can be used to uninstall

## Examples

You can redact **multiple youtube streams** like this, provided you have youtube-dl installed (`pip3 install youtube-dl`) and enough bandwith:
```
nvalhalla --uri $(youtube-dl -f best -g https://www.youtube.com/watch?v=awdX61DPWf4) --uri $(youtube-dl -f best -g https://www.youtube.com/watch?v=FPs_lU01KoI) --uri $(youtube-dl -f best -g https://www.youtube.com/watch?v=SnMBYMOTwEs) --uri $(youtube-dl -f best -g https://www.youtube.com/watch?v=jYusNNldesc)
```
![four youtube streams at once](https://i.imgur.com/7eo0NR5.jpg)

**Local video streams** can also be used like this:
```
nvalhalla --uri file:///home/username/Videos/test.mp4
```

**RTSP streams** are also supported:
```
nvalhalla --uri rtsp://hostname:port/path
```

Basically, any uri supported by [uridecodebin](https://gstreamer.freedesktop.org/documentation/playback/uridecodebin.html?gi-language=c) will probably work. If you find a combination that doesn't, please [report it](https://github.com/mdegans/nvalhalla/issues).

## FAQ

- **Can this app do anything but redact** No, and any potentially dangerous code (eg. dumping bounding boxes) has been removed. It's hoped that you won't modify it to do anything harmful, since software for doing inferences on video has an **immense** potential for misuse.
- **Can this app redact anything other than faces?** Yes. You can modify the model to do what you want by changing the config and models ~/.nvalhalla, however it will only redact IDs 0 and 1 unless you modify [cb_buffer.c](./src/cb_buffer.c).
- **This app isn't very useful** No, no, it isn't. It's meant mostly as a demo to see whether it's possible to write DeepStream code in Genie.
- **Can I output to a file?** Support for this is planned after optimization to help remedy the above point.
- **The app is very slow on my Nano** The model is currently not optimized at all, int8 and fp16 support is the next thing on the TODO list. It should run fine on a Xavier, however.
- **Why Genie?** Becuase it looks like Python, I like Python, and it fits perfectly with gstreamer. The Gstreamer project actually [recommends Vala](https://gstreamer.freedesktop.org/documentation/frequently-asked-questions/general.html?gi-language=c#why-is-gstreamer-written-in-c-why-not-cobjectivec) for those who want syntactic sugar, and Genie is just an alternative syntax for Vala. It makes writing GObject C a pleasure by not having to actually write GObject C.
