### 功能说明

小米空调伴侣杂项功能，包含收音机控制、铃声控制以及作为TTS使用，同样适用于小米网关

### 部署安装

* 将下载得到的miio_acpartner目录复制到home assistant配置目录下的custom_components中

* 设备配置

``` yaml
miio_acpartner:
  - platform: xiaomi_miio
    name: "ChildrenRoom" 
    host: 192.168.2.238
    token: YOUR_TOKEN
    api_key: "YOUR_BAIDU_TTS_API_KEY"
    secret_key: "Your_BAIDU_TTS_SECRET_KEY"
    base_url: "http://192.168.2.249:8123"
    base_path: "C:\\Users\\Dylan\\AppData\\Roaming\\.homeassistant"
    notify: true
```

多个设备就配置多个platform组。

* 参数说明：
* name、host、token：miio设备固有配置，请参考空调伴侣网关等基本设置方法
* api_key、secret_key：百度TTS开放服务为你提供的相关信息，如果你不使用TTS功能，这俩配置可以不要
* base_url base_path：你的home assistant环境信息
	* base_url：你的home assistant在内网的访问基础地址（后边不需要斜杠哦）
	* base_path：你的home assistant配置所在的目录，请在该目录下创建www文件夹，并且给予可写权限
* notify：由于TTS使用时，ffmpeg感觉不是很稳定，有时候tts会失败，所以增加此选项，当开启后，tts和vod成功后，会在hass的notification中展示结果（失败就没有）。

### 功能说明

该插件提供如下功能：

#### miio_acpartner.turn_on

打开广播

* entity_id：指定entity_i d为打开指定空调伴侣的广播，不指定则打开所配置的所有radio组件对应的设备广播

#### miio_acpartner.turn_off
关闭广播

* entity_id：指定entity_id为关闭指定空调伴侣的广播，不指定则关闭所配置的所有radio组件对应的设备广播

#### miio_acpartner.toggle

切换打开关闭状态

* entity_id：用途同上

#### miio_acpartner.set_volume

设置音量

* entity_id：用途同上
* volume：音量值，0-100

``` json
{
    "entity_id": "radio.childrenroom_fm",
    "volume": 100
}
```

#### miio_acpartner.next_radio

播放下一个收藏频道【当前只能在频道编号最低的10个频道中循环】

entity_id：用途同上

#### miio_acpartner.prev_radio 

播放上一个收藏频道【当前只能在频道编号最低的10个频道中循环】

* entity_id：用途同上

#### miio_acpartner.play_radio

播放指定频道

* entity_id：用途同上

* program_id：需要播放的频带ID

``` json
{
    "entity_id": "radio.childrenroom_fm",
    "program_id": 1739
}
```

> ##### 频道ID的获取方式：
>
> * 通过米家APP播放你想播放的电台
> * 通过home assitant的states接口或者网站的states页面（左侧“开发者工具”下方有一对尖括号图标那个），找到你的设备，右侧中可以看到channel，后边有形如：
>
> ``` yaml
> space_free: 11638627
> channel: 1739
> volume: 100
> ```
>
> * 其中channel就是当前正在播放节目的program_id，后续使用该数字即可播放指定电台。

#### miio_acpartner.play_ringtone

播放指定铃声

* entity_id：用途同上

* ringtone_id：需要播放的铃声ID

* volume：播放铃声的音量（可选），默认为上次音量

> ##### 铃声ID的获取方式：
>
> * 通过home assitant的states接口或者网站的states页面（左侧“开发者工具”下方有一对尖括号图标那个），找到你的设备，右侧中可以看到ringtones，里面分三种铃声：
>   * alarm	报警铃声
>   * clock	闹铃
>   * doorbell	门铃
>
> * 无论哪种铃声，使用list下的index作为ringtone_id即可。

#### miio_acpartner.play_tts

  TTS输出

* entity_id：用途同上

* message：需要播放的文字内容（转换成语音后总长度不能超过30s）

* volume：播放音量，默认为上次音量  

  此功能需要百度TTS以及ffmpeg的支持。

#### miio_acpartner.play_vod

播放指定音频流

* entity_id：用途同上

* url：需要播放的音频流地址（http格式的纯音频流，不要超过30s）

* volume：播放音量，默认为上次音量   

> ##### 特别说明：
>
> play_tts和play_vod需要三项配置支持：
>
> 1. home assistant的http服务支持：需要在配置文件所在目录下创建www文件夹，并且保证该目录权限可写，具体可参考：https://www.home-assistant.io/components/http/ ，页面最下部分：Hosting files
> 2. ffmpeg支持，具体参考：https://www.home-assistant.io/components/ffmpeg/
> 3. 百度tts支持，可参考网页帖子：https://bbs.hassbian.com/forum.php?mod=viewthread&tid=33&highlight=%E7%99%BE%E5%BA%A6tts ，不可谓不深情并茂。

### 玩法参考

1. 天猫精灵这个蠢货，目前还没有发现通过API控制音量和暂停的手段，可以通过空调伴侣间隔控制天猫精灵（附件带了三个文件，可以通过米家上传为自定义铃声后，通过在特定场景播放指定铃声完成对猫精的控制），后来笔者想到的控制天猫精灵关闭的方法不采用这种扰民的方案了，买一个天猫精灵按呗（阿里咋啥都背？），用esp控制按呗出发信号控制天猫精灵关闭；
2. 使用网关和空调伴侣的朋友，免费多了几个TTS设备（虽然空调伴侣的声音又小又渣，聊胜于无）；
3. 可以自行搭建一个http服务器，通过APP的方式，将手机录音上传到服务器后，然后使用play_vod将上传的录音通过空调伴侣或者网关播放出来（至2020年年中的时候小米已经限制录音文件的长度为30s了，这个功能基本无意义了）；
4. 一个可以上一台下一台选取的收音机（目前只能在program_id最低的10个收藏台中循环）。