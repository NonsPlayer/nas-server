<h1 align="center">NonsPlayer Nas</h1>

# 目前开发需要

## 启动

使用docker打包。启动之前需要用户自行配置好所需要的设置。

如:

1. 是否使用独立模式(使用NonsPlayer Nas管理音乐)
2. 是否允许上传
3. 访问是否需要验证身份
4. 是否使用https
5. 是否使用代理...

以后会根据需求添加更多的设置.

## 后端



### 独立模式

目前的想法: 将音乐文件存放在统一的目录下, 由数据库记录并管理它们的信息, 归属于什么专辑, 由谁演唱等等.

若后期有什么弊端或更好的解决方案, 会根据需求进行更改.

独立模式下，NonsPlayer Nas会自动扫描指定目录下的音乐文件，并将其添加到由NonsPlayer Nas(以下简称NPN)管理的数据库与文件夹中。

NPN会自动获取音乐文件的信息，如歌曲名、歌手、专辑、封面等，并将其保存到数据库中。

在获取到专辑后，NPN会自动获取专辑的封面，并保存到统一的**专辑封面文件夹**中,由数据库链接每一个数据。

### 兼容模式

兼容模式下，NPN会自动获取指定目录下的音乐文件的信息，并将其保存到数据库中。

与独立模式不同的是，兼容模式下，NPN不会对音乐文件进行任何操作，只会获取音乐文件的信息。并对它们在数据库中的信息进行管理。

兼容模式最大程度地保留了原有的音乐文件的结构，不会对现有结构造成破坏。

相同地，它也需要获取专辑封面，并保存到统一的**专辑封面文件夹**中。

## 数据库

数据库可以自行选择，无硬性要求，符合，适合即可。

## 数据结构

若能通过某种方式记录下音乐的对应音乐平台, 最好.

**注意, 所有的id均是默认无对应音乐平台的情况下自动生成的, 若有相对应的音乐平台则使用平台id**

**默认情况下, 由NPN自动生成的id与平台id是不一样的。若有相应的平台, 则id前应当带上平台名,例如 `neteasecloud_2070257145` **

## 接口

**根: /api/v1/**

+ 音乐: /music?id=xxx
+ 专辑: /album?id=xxx
+ 歌手: /artist?id=xxx
+ 歌词: /lyric?id=xxx
+ 歌单: /playlist?id=xxx&count=xxx&offset=xxx **count:一次获取的歌曲数, 以防数据过大 | offset: 偏移量**
+ 目录浏览: /dir?path=xxx **path: 目录路径*选填, 若无则视为获取所有内容**



### 歌曲元数据(主要数据)



```json

{
  "album": {
    "id": "", 
    "name": "",
    "avatarUrl": "https://xx/xx.png" // 此处为专辑封面的uri, 需要由NPN从专辑封面文件夹中获取
  },
  "artists": [
    {
      "id": "", 
      "name": "",
      "avatarUrl": "https://xx/xx.png"
    }
  ],
  "duration": "0", // 单位: 毫秒
  "uri": "xx.mp3",
  "md5": "",
  "name": "Heart Upon My Sleeve",
  "lyrics": "" // 歌词id, 会由NonsPlayer提供,若为0则无歌词
  "platform": { // FEATURE
    "platform_id": "QQMusic", // 0 未知 1 QQMusic 2 NeteaseMusic 3 XiamiMusic 4 KuwoMusic 5 MiguMusic 6 BaiduMusic
    "music_id": "xx"
  } 
}

```

### 专辑元数据

```json

{
  "id": "", // id生成规则: 专辑名+歌手名+发行时间 字符串获取md5. 例: "HeartUponMySleeve_Avicii_2014-06-17"
  "name": "",
  "avatarUrl": "https://xx/xx.png",
  "artists": [
    {
      "name": ""
    }
  ],
  "music": [
    {
      "id": "",
      "name": ""
    }
  ]
}

```

### 歌手元数据

```json

{
  "id": "", // id生成规则: 歌手名 字符串获取md5. 例: "Avicii"
  "name": "",
  "avatarUrl": "https://xx/xx.png", // 一般由NonsPlayer提供, 若无则为""
  "music": [
    {
      "id": "",
      "name": ""
    }
  ]
}

```

### 歌词元数据

```json

{
  "id": "", // id生成规则: 歌词内容 字符串获取md5."
  "type": "lrc", // lrc, yrc, qrc, ttml, lys, eslrc 等, 由NonsPlayer提供
  "lyric": "",
  "trans_lyric": "",
  "roman_lyric": "" // 罗马音歌词
}

```

### 歌单元数据

```json

{
  "id": "", // id生成规则: 歌单名_创建时间 字符串获取md5. 例: "Avicii'sBest_2021-06-17"
  "name": "",
  "avatarUrl": "https://xx/xx.png", // 选填, 若无则选第一首歌的封面
  "music": [
    {
      "id": "",
      "name": "",
      "album": {
        "id": "",
        "name": "",
        "avatarUrl": "https://xx/xx.png"
      },
      "artists": [
        {
          "id": "",
          "name": ""
        }
      ],
      "uri": "xx.mp3"
    }
  ]
}

```
