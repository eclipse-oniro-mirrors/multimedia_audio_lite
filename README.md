# AUDIO\_LITE<a name="EN-US_TOPIC_0000001080430608"></a>

- [AUDIO\_LITE<a name="EN-US_TOPIC_0000001080430608"></a>](#audio_lite)
  - [Introduction<a name="section11660541593"></a>](#introduction)
  - [Directory Structure<a name="section178251437756"></a>](#directory-structure)
  - [Usage<a name="section1648194512427"></a>](#usage)
  - [Constraints<a name="section722512541395"></a>](#constraints)
  - [Repositories Involved<a name="section1371113476307"></a>](#repositories-involved)
  - [Changes in This Release<a name="section_changes_en"></a>](#changes-in-this-release)

## Introduction<a name="section11660541593"></a>

This repository provides the audio capability.

**Figure  1**  Position of this repository \(in the dotted blue box\) in the multimedia subsystem<a name="fig584652218594"></a>  
![](figures/audio_lite_en.png "audio_lite_en")

## Directory Structure<a name="section178251437756"></a>

```
foundation/multimedia/audio_lite
├── frameworks         # Framework code
└── interfaces         # APIs
    └── kits           # External APIs
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

# Changes in This Release<a name="section_changes_en"></a>

This repository is adapted for the HiSpark AiFly (Hi3403V100) development board. For clarity, the changes are described in two parts: **Added Features** and **Enhanced Existing Features**.

## Added Features

1. Added audio device management
    -   Added `interfaces/kits/audio_device_manager.h`, `services/audiomanager/audio_device_manager.cpp` and `services/audiomanager/BUILD.gn` (producing `audio_device_manager_lite`): the `AudioDeviceManager` singleton supports device query (`GetAllConnectDevices`) and device addition/removal (`AddDevice`/`RemoveDevice`), with the device list protected by a mutex.

2. Added the device-change callback mechanism
    -   Added `interfaces/kits/audio_manager_callback.h`, defining `AudioManagerDeviceChangeCallback` (`OnDeviceChange`/`OnReadDataFailed`).
    -   The client (`frameworks/binder/audio_capturer_client.cpp`) adds `SetDeviceChangeCallback`, receiving server pushes through an IPC stub (`objectStub_` + `AudioCapturerCallback`); the server (`services/server`) adds the `AUD_CAP_FUNC_SET_DEVICE_CHANGE_CALLBACK` func ID, `AudioCapturerServerCallback`, and `OnAllDeviceChange`/`SendDeviceInfo`/`SendReadDataFailInfo`, synchronizing device-change and read-failure events to clients.
    -   `interfaces/kits/audio_capturer.h` exposes the new `SetDeviceChangeCallback` API.

3. Added local/virtual dual audio source implementations
    -   `AudioSource` is turned into an interface class (methods virtualized under `MEDIA_INTERFACE_V1_0`); added `AudioSourceLocal` (`services/impl/audio_source/.../local/`, local capture based on the HDF Audio HDI) and `AudioSourceVirtual` (`services/impl/audio_source/.../virtual/`, remote capture based on the distributed-audio HDI); the proper local or distributed adapter is matched by deviceId and loaded, initialized and used for capture.

4. Added ALSA dependency integration
    -   `frameworks/BUILD.gn` adds the `alsa-lib` and `alsa-utils` dependencies under `media_interface_upgrade`, supporting the board-level ALSA capture path.

## Enhanced Existing Features

1. The existing capturer-info model is enhanced
    -   Original functionality: `AudioCapturerInfo` contained only sample rate, channel count, bit rate, stream type and bit width.
    -   Enhancement: added the `deviceId` and `deviceType` fields to specify/identify the capture device; `SetCapturerInfo` now takes a const reference.

2. The existing capture server is enhanced
    -   Original functionality: `AudioCapturerServer` provided only basic capture IPC functions.
    -   Enhancement: added device-info management and event push logic, and the client callback object lifetime is maintained together with the device-change callback.

3. The existing capture client is enhanced
    -   Original functionality: `AudioCapturerClient` held a raw `SvcIdentity` with simple callback registration.
    -   Enhancement: `sid_` is now managed by `std::unique_ptr`; an IPC stub context (with a mutex and a weak-reference callback) plus `callbackMutex_` are added to avoid dangling callbacks and lifetime issues; paths such as `OnBufferAvailable` are adapted accordingly.

4. The existing encoding and read path is enhanced
    -   Original functionality: `AudioEncoder` and `audio_capturer_impl` were implemented for a single local source.
    -   Enhancement: adapted to local/virtual source selection, read failures are reported to the client via callback, and start/stop state validation and logging are improved.

5. The existing build configuration is enhanced
    -   Original functionality: `services/BUILD.gn` built `audio_capturer_server` as a static library, with a single audio-source implementation.
    -   Enhancement: `audio_capturer_server` becomes a `shared_library`; `audio_capturer_impl` selects local + distributed audio-source files under `media_interface_upgrade`, dependencies are resolved per board via `ohos_board_adapter_dir` with `distributed_audio_entry` added, and the distributed-audio HDI include paths are appended.

6. The existing open-source compliance configuration is enhanced
    -   Original functionality: `OAT.xml` configures open-source compliance scanning.
    -   Enhancement: a policyitem is added, registering the HiSilicon copyright.

