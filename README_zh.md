# 媒体子系统AUDIO\_LITE组件<a name="ZH-CN_TOPIC_0000001080430608"></a>

- [媒体子系统AUDIO\_LITE组件<a name="ZH-CN_TOPIC_0000001080430608"></a>](#媒体子系统audio_lite组件)
  - [简介<a name="section11660541593"></a>](#简介)
  - [目录<a name="section178251437756"></a>](#目录)
  - [使用说明<a name="section1648194512427"></a>](#使用说明)
  - [约束<a name="section722512541395"></a>](#约束)
  - [相关仓<a name="section1371113476307"></a>](#相关仓)

## 简介<a name="section11660541593"></a>

AUDIO\_LITE组件提供音频能力的支持。在上述能力基础上，主要涉及以下方面：

- 音频设备管理：`interfaces/kits/audio_device_manager.h` 与 `services/audiomanager` 提供 `AudioDeviceManager` 单例，支持已连接设备查询与设备状态维护，设备列表由互斥锁保护。

- 设备变化回调机制：通过设备变化回调接口，客户端可借助 `SetDeviceChangeCallback` 接收服务端推送的设备变化与读数据失败事件，服务端同步维护客户端回调对象生命周期。

- 本地/分布式双音频源：`AudioSource` 抽象为接口，提供基于 HDF Audio HDI 的本地采集实现与基于分布式音频 HDI 的远端采集实现，按设备类型选择对应音频源。

- ALSA 依赖接入：在 `media_interface_upgrade` 下引入 `alsa-lib`、`alsa-utils` 依赖，支撑 ALSA 采集通路。

- 采集信息模型：`AudioCapturerInfo` 增加设备 ID 与设备类型字段，`SetCapturerInfo` 改为 const 引用传递，便于显式指定采集设备。

- 采集服务端与客户端：服务端提供设备信息管理与事件下发；客户端以智能指针管理 IPC 标识，并引入桩上下文与回调互斥保护，避免回调悬垂与生命周期问题。

- 编码与读取链路：适配本地/分布式双源选择，在部分读失败场景下通过回调通知客户端，并完善起停状态校验与日志。

- 编译构建配置：采集服务由静态库改为共享库，音频源按升级开关选择本地与分布式实现，依赖按板型解析并补充分布式音频头文件路径。


**图 1**  媒体子系统架构（蓝色虚线框是audio\_lite）<a name="fig584652218594"></a>  
![](figures/audio_lite.png "媒体子系统架构（蓝色虚线框是audio_lite）")

## 目录<a name="section178251437756"></a>

```
foundation/multimedia/audio_lite
├── frameworks         # 框架代码
├── interfaces         # 接口
│   └── kits           # 对外接口
└── services           # 服务化实现（音频设备管理、音频源、采集服务）
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
