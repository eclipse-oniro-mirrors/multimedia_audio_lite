# AUDIO\_LITE<a name="EN-US_TOPIC_0000001080430608"></a>

- [AUDIO\_LITE<a name="EN-US_TOPIC_0000001080430608"></a>](#audio_lite)
  - [Introduction<a name="section11660541593"></a>](#introduction)
  - [Directory Structure<a name="section178251437756"></a>](#directory-structure)
  - [Usage<a name="section1648194512427"></a>](#usage)
  - [Constraints<a name="section722512541395"></a>](#constraints)
  - [Repositories Involved<a name="section1371113476307"></a>](#repositories-involved)

## Introduction<a name="section11660541593"></a>

This repository provides the audio capability. The following aspects are covered on top of this capability:

- Audio device management: `interfaces/kits/audio_device_manager.h` and `services/audiomanager` provide the `AudioDeviceManager` singleton, supporting query of connected devices and device state maintenance, with the device list protected by a mutex.

- Device-change callback mechanism: a device-change callback interface lets clients receive device-change and read-failure events pushed from the server via `SetDeviceChangeCallback`, while the server maintains the client callback object lifetime.

- Local/distributed dual audio sources: `AudioSource` is abstracted into an interface, with a local capture implementation based on the HDF Audio HDI and a remote one based on the distributed audio HDI; the source is selected by device type.

- ALSA dependency integration: `alsa-lib` and `alsa-utils` are introduced under `media_interface_upgrade` to support the ALSA capture path.

- Capturer-info model: `AudioCapturerInfo` gains device ID and device type fields and `SetCapturerInfo` takes a const reference, allowing the capture device to be specified explicitly.

- Capture server and client: the server provides device-info management and event dispatch; the client manages the IPC identity with a smart pointer and adds a stub context plus callback mutex to avoid dangling callbacks and lifetime issues.

- Encoding and read path: adapted to local/distributed source selection, notifies clients through callbacks in some read-failure scenarios, and improves start/stop state validation and logging.

- Build configuration: the capture server changes from a static to a shared library, audio sources are selected by the upgrade switch, dependencies are resolved per board, and distributed audio HDI include paths are added.

- Open-source compliance configuration: `OAT.xml` adds the HiSilicon copyright declaration.

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
