# AUDIO\_LITE<a name="EN-US_TOPIC_0000001080430608"></a>

- [AUDIO\_LITE<a name="EN-US_TOPIC_0000001080430608"></a>](#audio_lite)
  - [Introduction<a name="section11660541593"></a>](#introduction)
  - [Directory Structure<a name="section178251437756"></a>](#directory-structure)
  - [Usage<a name="section1648194512427"></a>](#usage)
  - [Constraints<a name="section722512541395"></a>](#constraints)
  - [Repositories Involved<a name="section1371113476307"></a>](#repositories-involved)

## Introduction<a name="section11660541593"></a>

This repository provides the audio capability.

This repository is adapted for the HiSpark AiFly (Hi3403V100) development board. It adds audio device management, a device-change callback mechanism, local/distributed dual audio sources, and ALSA dependency integration. It also enhances the capturer-info model, capture server and client, the encoding and read path, build configuration, and open-source compliance configuration, improving device adaptability and event notification for audio capture.

**Figure  1**  Position of this repository \(in the dotted blue box\) in the multimedia subsystem<a name="fig584652218594"></a>  
![](figures/audio_lite_en.png "audio_lite_en")

## Directory Structure<a name="section178251437756"></a>

```
foundation/multimedia/audio_lite
├── frameworks         # Framework code
├── interfaces         # APIs
│   └── kits           # External APIs
└── services           # Service implementation (audio device management, audio sources, and capture server)
```

## Usage<a name="section1648194512427"></a>

Build a single repository in the root directory.

```
# Select the development board.
hb set  
# Build this repository.
hb build audio_lite 
```

## Constraints<a name="section722512541395"></a>

-   C++ 11 or later

## Repositories Involved<a name="section1371113476307"></a>

[Multimedia subsystem](https://gitee.com/openharmony/docs/blob/master/en/readme/multimedia.md)

[camera\_lite](https://gitee.com/openharmony/multimedia_camera_lite)

[camera\_sample\_lite](https://gitee.com/openharmony/applications_sample_camera)

[media\_utils\_lite](https://gitee.com/openharmony/multimedia_utils_lite)

[media\_lite](https://gitee.com/openharmony/multimedia_media_lite)

[audio\_lite](https://gitee.com/openharmony/multimedia_audio_lite)
