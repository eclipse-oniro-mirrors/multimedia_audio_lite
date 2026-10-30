# 媒体子系统AUDIO\_LITE组件<a name="ZH-CN_TOPIC_0000001080430608"></a>

- [媒体子系统AUDIO\_LITE组件<a name="ZH-CN_TOPIC_0000001080430608"></a>](#媒体子系统audio_lite组件)
  - [简介<a name="section11660541593"></a>](#简介)
  - [目录<a name="section178251437756"></a>](#目录)
  - [使用说明<a name="section1648194512427"></a>](#使用说明)
  - [约束<a name="section722512541395"></a>](#约束)
  - [相关仓<a name="section1371113476307"></a>](#相关仓)
  - [本次改动<a name="section_changes_zh"></a>](#本次改动)

## 简介<a name="section11660541593"></a>

AUDIO\_LITE组件提供音频能力的支持。

**图 1**  媒体子系统架构（蓝色虚线框是audio\_lite）<a name="fig584652218594"></a>  
![](figures/audio_lite.png "媒体子系统架构（蓝色虚线框是audio_lite）")

## 目录<a name="section178251437756"></a>

```
foundation/multimedia/audio_lite
├── frameworks         # 框架代码    
└── interfaces         # 接口
    └── kits           # 对外接口
```

## 使用说明<a name="section1648194512427"></a>

单仓的编译构建，在根目录下进行单仓的构建和编译

```
# 开发板选择
hb set  
# 单仓构建和编译
hb build audio_lite 
```

## 约束<a name="section722512541395"></a>

-   C++11版本或以上

## 相关仓<a name="section1371113476307"></a>

[媒体子系统](https://gitee.com/openharmony/docs/blob/master/zh-cn/readme/%E5%AA%92%E4%BD%93%E5%AD%90%E7%B3%BB%E7%BB%9F.md)

[camera\_lite](https://gitee.com/openharmony/multimedia_camera_lite)

[camera\_sample\_lite](https://gitee.com/openharmony/applications_sample_camera)

[media\_utils\_lite](https://gitee.com/openharmony/multimedia_utils_lite)

[media\_lite](https://gitee.com/openharmony/multimedia_media_lite)

[audio\_lite](https://gitee.com/openharmony/multimedia_audio_lite)

# 本次改动<a name="section_changes_zh"></a>

本仓针对 HiSpark AiFly（Hi3403V100）开发板进行功能适配。为便于区分，以下按“新增能力”与“原有功能增强”两部分说明。

## 新增能力

1. 新增音频设备管理能力
    -   新增 `interfaces/kits/audio_device_manager.h`、`services/audiomanager/audio_device_manager.cpp` 与 `services/audiomanager/BUILD.gn`（产出 `audio_device_manager_lite`）：提供 `AudioDeviceManager` 单例，支持设备查询（`GetAllConnectDevices`）与设备增删（`AddDevice`/`RemoveDevice`），内部以互斥锁保护设备列表。

2. 新增设备变化回调机制
    -   新增 `interfaces/kits/audio_manager_callback.h`，定义 `AudioManagerDeviceChangeCallback`（`OnDeviceChange`/`OnReadDataFailed`）。
    -   客户端（`frameworks/binder/audio_capturer_client.cpp`）新增 `SetDeviceChangeCallback`，通过 IPC 桩（`objectStub_` + `AudioCapturerCallback`）接收服务端推送；服务端（`services/server`）新增 `AUD_CAP_FUNC_SET_DEVICE_CHANGE_CALLBACK` 功能码、`AudioCapturerServerCallback` 及 `OnAllDeviceChange`/`SendDeviceInfo`/`SendReadDataFailInfo`，实现设备变化与读数据失败事件向客户端的同步。
    -   `interfaces/kits/audio_capturer.h` 对外新增 `SetDeviceChangeCallback` 接口。

3. 新增本地/分布式双音频源实现
    -   将 `AudioSource` 改造为接口类（`MEDIA_INTERFACE_V1_0` 下各方法虚化），新增 `AudioSourceLocal`（`services/impl/audio_source/.../local/`，基于 HDF Audio HDI 的本地采集实现）与 `AudioSourceVirtual`（`services/impl/audio_source/.../virtual/`，基于分布式音频 HDI 接口的远端采集实现），按 deviceId 自动匹配本地或分布式适配器并完成加载、初始化与采集。

4. 新增 ALSA 依赖接入
    -   `frameworks/BUILD.gn` 在 `media_interface_upgrade` 下新增 `alsa-lib`、`alsa-utils` 依赖，支撑板端 ALSA 采集通路。

## 原有功能增强

1. 原有录制信息模型增强
    -   原有功能：`AudioCapturerInfo` 仅含采样率、声道、码率、流类型、位宽等字段。
    -   本次增强：新增 `deviceId` 与 `deviceType` 字段，支持指定/标识采集设备；`SetCapturerInfo` 参数改为 const 引用传递。

2. 原有采集服务端增强
    -   原有功能：`AudioCapturerServer` 仅提供基础采集 IPC 功能。
    -   本次增强：新增设备信息管理与事件下发逻辑，并配合设备变化回调维护客户端回调对象生命周期。

3. 原有采集客户端增强
    -   原有功能：`AudioCapturerClient` 持有裸 `SvcIdentity` 与简单回调注册。
    -   本次增强：改为 `std::unique_ptr` 管理 `sid_`，新增 IPC 桩上下文（含互斥锁与弱引用回调）与 `callbackMutex_`，避免回调悬垂与生命周期问题；`OnBufferAvailable` 等路径同步适配。

4. 原有编码与数据读取链路增强
    -   原有功能：`AudioEncoder`、`audio_capturer_impl` 按单一本地源实现。
    -   本次增强：适配本地/分布式双源选择，读数据失败时通过回调通知客户端，并完善启动/停止状态校验与日志。

5. 原有编译构建配置增强
    -   原有功能：`services/BUILD.gn` 将 `audio_capturer_server` 编译为静态库，音频源仅含单一实现。
    -   本次增强：`audio_capturer_server` 改为 `shared_library`；`audio_capturer_impl` 按 `media_interface_upgrade` 选择本地+分布式音频源源文件，依赖改为按板型 `ohos_board_adapter_dir` 解析并新增 `distributed_audio_entry`，补充分布式音频 HDI 头文件路径。

6. 原有开源合规配置增强
    -   原有功能：`OAT.xml` 用于开源合规扫描配置。
    -   本次增强：新增 policyitem，登记 HiSilicon 版权声明。

