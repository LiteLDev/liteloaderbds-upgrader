# LL Public Upgrade Service V2

## Introduction

欢迎，这里是LL开源项目的公共更新检查服务，旨在为开发者减少花费在检查更新上的时间与金钱，为插件开发提供更多方便。

相比自建更新检查，本项目：

 - 完全免费
 - 稳定，JSDelivr 全球高速连接

另外，相对于单纯使用 JSD 的更新检查，本项目：
 - 使用随机目录名绕过缓存。众所周知，JSD 在大陆的缓存很难使用 purge 刷新。
 - 支持自动对文件生成`.md5.verify`校验文件
 - 支持自定义自动运行脚本

### Auto MD5 Calculation

- 此仓库拥有自动 MD5 校验计算 actions ，在每次push时触发，扫描所有更改文件并重新计算其 MD5 值上传到仓库，方便自动更新时作下载校验之用
- MD5 将被储存到文件名`.md5.verify`的文本文件中。下载时带上MD5信息一起下载，并校验 MD5 值是否符合，即可判断此文件下载是否完整正确。后面将介绍如何下载此文件的方法
- 根目录的 IgnoreDirs.conf 文件记录了不需要计算 MD5 校验值的目录列表。如果你有新的目录需要被排除，按同样的格式将路径写入到此文件即可。一行一个目录路径，并将修改好的文件push到仓库中

------

## Join

- 在本 Repo 中新建一个需要使用此服务的插件的文件夹。建议每个插件使用一个单独的文件夹，以保持逻辑清晰
- 以后，您将负责维护此文件夹，可以在不违反 Github 存储库相关规定的前提下自由增减文件夹内内容。
 - 不要去修改别人的文件夹，除非得到授意，如您发现有人擅自修改您的文件夹内容，请向我们提出举报

## Usage

> 本项目采用中继加速的思想，引导更新程序访问最新推送版本的文件，同时又可以使用 JSD 的全球加速，避免了需要频繁刷新缓存造成的问题

### 【基础操作】如何上传和下载文件

修改根目录对应文件夹中的文件，推送到仓库即可。不要修改随机文件夹中的内容

例如您在 **Test** 文件夹新建了 **version.json** 文件。接下来，您可以使用以下方法访问到您的文件

```javascript
// 访问中继索引文件
let remote = HttpGet("https://upgrade.litebds.com/id.json")
// 单纯使用Jsdelivr并不能绕过id缓存。因此使用Cloudflare CDN下载中继文件，获取中继目录名后再使用速度较快的Jsdelivr进行加速
let json = JSON.parse(remote.data)	//Json格式：{ "token": "中继目录名" }
let dir = json.token;	 //获取中继目录名

// *目标文件的下载地址（中间加上了"token"目录）
let downloadUrl = "https://cdn.jsdelivr.net/gh/LiteLDev/Upgrade/" + dir + "/Test/version.json"

// *目标文件的MD5校验数据地址
let md5Url = "https://cdn.jsdelivr.net/gh/LiteLDev/Upgrade/" + dir + "/Test/version.json.md5.verify"
```
使用上面给出的两个拼接的下载地址，下载你需要的目标文件和MD5校验数据即可。

**切记 **使用上述方法使用随机文件夹名中继访问你的文件。否则，你对JSD文件的直接访问极有可能存在缓存，并且难以刷新

### 【进阶操作】 推送时运行自定义脚本

 > 此功能为方便开发者而提供。在每次Push时，Github Actions将运行目录下所有的`__AUTORUN__.sh`文件。

 本项目Actions使用Windows系统，但你需要用`bash`语法来编写脚本。<br>
 一个应用示例：

 ```bash
 # Path: ./iLand/__AUTORUN__.sh
 # iLand 自动从上游仓库同步语言文件到此仓库
 git clone https://github.com/LiteLDev-LXL/iLand-Core.git
 cp -R iLand-Core/3rd-languages/* i18n-data
 cp -R iLand-Core/iland/lang/* i18n-data
 rm -rf iLand-Core
 echo "iland's corn is completed."
 ```
  - 编写完成后，请务必关注脚本在Actions中的运行情况。如果导致Actions中断，请立即修复或删除你的脚本。

## Attention !

 - **不要修改 `id.json` 文件**
 - **不要修改随机文件夹的文件夹名**
 - 不要修改随机文件夹中的文件，这是无效的！
 - 不要修改其他人文件夹中的文件，除非得到授意。
 - 如果使用`__AUTORUN__.sh`，**不要在脚本中放置任何恶意代码**
 - 如果使用`__AUTORUN__.sh`，**及时修改脚本中的错误(如果有)** 因为这会中断Actions进程。
 - 存储的内容不得违反Jsdelivr或Github相关政策

 > LXL Team保留对所有文件和成员操作权限的管理权力，<br>
 > 如果你对以上内容有异议，请勿使用本存储库服务。如果你违反了以上TOS，您的相关权益可能被撤销。

## Credits
 - JSD 加速仓库的想法和实现 [@RedbeanW](https://github.com/Redbeanw44602)
 - MD5 自动计算程序 [@yqs112358](https://github.com/yqs112358)
 - 一切的基础 [@jsdelivr](https://github.com/jsdelivr)