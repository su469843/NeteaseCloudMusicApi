# 网易云音乐 API 文档

> 基于 [Binaryify/NeteaseCloudMusicApi](https://github.com/Binaryify/NeteaseCloudMusicApi) 项目
>
> 本文档覆盖所有 280 个 API 接口，包含参数说明和调用示例

---

## 目录

- [调用前须知](#调用前须知)
- [登录相关](#登录相关)
- [用户相关](#用户相关)
- [歌曲相关](#歌曲相关)
- [专辑相关](#专辑相关)
- [歌单相关](#歌单相关)
- [评论相关](#评论相关)
- [搜索相关](#搜索相关)
- [歌手相关](#歌手相关)
- [MV/视频相关](#mv视频相关)
- [电台相关](#电台相关)
- [排行榜](#排行榜)
- [推荐相关](#推荐相关)
- [云盘相关](#云盘相关)
- [数字专辑相关](#数字专辑相关)
- [音乐人相关](#音乐人相关)
- [VIP 相关](#vip-相关)
- [云贝相关](#云贝相关)
- [节目/声音相关](#节目声音相关)
- [一起听相关](#一起听相关)
- [曲风相关](#曲风相关)
- [消息/私信相关](#消息私信相关)
- [其他](#其他)

---

## 调用前须知

> **安全提醒**：本项目不提供线上 demo，请不要轻易信任使用他人提供的公开服务，以免发生安全问题。

> **请求方式**：文档示例统一使用 GET 请求演示，本项目同时支持 GET/POST。使用 POST 请求时，URL 必须添加时间戳使每次请求 URL 不一样，否则会被缓存。

> **缓存说明**：接口做了缓存处理（默认缓存 2 分钟），相同的 URL 会在两分钟内只向网易服务器发一次请求。如果遇到不需要缓存结果的接口，可在请求 URL 后面加一个时间戳参数使 URL 不同，例如：`/simi/playlist?id=347230&timestamp=1503019930000`

> **登录频率**：不要频繁调登录接口，可能会被风控。登录状态还存在就不要重复调登录接口。

> **跨域请求**：如果是跨域请求，请在所有请求带上 `xhrFields: { withCredentials: true }`（axios 为 `withCredentials: true`，Fetch API 为 `fetch(url, { credentials: 'include' })`），或直接手动传入 cookie。

> **301 错误**：基本都是没登录就调用了需要登录的接口。如果登录了还是提示 301，基本是缓存问题，加时间戳或等待 2 分钟或重启服务重新登录。

> **IP 限制**：由于网易限制，此项目在国外服务器或部分国内云服务上使用可能会受到限制（如 `460 cheating` 异常）。如需解决，可使用 `realIP` 参数传进国内 IP，如：`?realIP=116.25.146.177`

> **分页**：分页接口返回字段里有 `more`，`more` 为 true 则为有下一页。

> **图片尺寸**：图片加上 `?param=宽y高` 可控制图片尺寸，如 `?param=200y200`

> **noCookie**：如果不需要接口 headers 携带 cookies 信息，可以加上 `noCookie=true` 参数。

> **代理**：在 query 参数中加上 `proxy=your-proxy` 即可让此次请求使用代理。v3.3.0 后支持使用 PAC 代理，如 `?proxy=http://192.168.0.1/proxy.pac`

---

## 登录相关

### 手机登录

> 现在要求验证，暂时绕不过，请使用二维码登录

- **接口地址：** `/login/cellphone`
- **必选参数：** `phone` (手机号码)，`password` (密码) 或 `captcha` (验证码)
- **可选参数：** `countrycode` (国家码，如美国传入 `1`)，`md5_password` (md5 加密后的密码)
- **调用示例：** `/login/cellphone?phone=xxx&password=yyy`
- **调用示例：** `/login/cellphone?phone=xxx&captcha=1234`

### 邮箱登录

> 现在要求验证，暂时绕不过，请使用二维码登录

- **接口地址：** `/login`
- **必选参数：** `email` (163 网易邮箱)，`password` (密码)
- **可选参数：** `md5_password` (md5 加密后的密码)
- **调用示例：** `/login?email=xxx@163.com&password=yyy`
- **说明：** 登录成功后会在浏览器保存 Cookies 用作登录凭证。v3.30.0 后支持手动传入 cookie。

### 二维码登录

#### 1. 二维码 key 生成接口

- **接口地址：** `/login/qr/key`
- **说明：** 调用此接口可生成一个 key
- **调用示例：** `/login/qr/key?timestamp=xxx`

#### 2. 二维码生成接口

- **接口地址：** `/login/qr/create`
- **必选参数：** `key` (由第一个接口生成)
- **可选参数：** `qrimg` (传入后会额外返回二维码图片 base64 编码)
- **调用示例：** `/login/qr/create?key=xxx`

#### 3. 二维码检测扫码状态接口

- **接口地址：** `/login/qr/check`
- **必选参数：** `key` (由第一个接口生成)
- **说明：** 轮询此接口可获取二维码扫码状态。800 为二维码过期，801 为等待扫码，802 为待确认，803 为授权登录成功（会返回 cookies）
- **调用示例：** `/login/qr/check?key=xxx`

### 游客登录

- **接口地址：** `/register/anonimous`
- **说明：** 直接调用此接口，可获取游客 cookie。如果遇到其他接口未登录状态报 400 状态码需要验证的错误，可使用此接口获取游客 cookie 避免报错。

### 刷新登录

- **接口地址：** `/login/refresh`
- **说明：** 刷新登录状态，返回内容包含新的 cookie（不支持刷新二维码登录的 cookie）
- **调用示例：** `/login/refresh`

### 发送验证码

- **接口地址：** `/captcha/sent`
- **必选参数：** `phone` (手机号码)
- **可选参数：** `ctcode` (国家区号，默认 86)
- **调用示例：** `/captcha/sent?phone=13xxx`

### 验证验证码

- **接口地址：** `/captcha/verify`
- **必选参数：** `phone` (手机号码)，`captcha` (验证码)
- **可选参数：** `ctcode` (国家区号，默认 86)
- **调用示例：** `/captcha/verify?phone=13xxx&captcha=1597`

### 注册/修改密码

- **接口地址：** `/register/cellphone`
- **必选参数：** `captcha` (验证码)，`phone` (手机号码)，`password` (密码)，`nickname` (昵称)
- **可选参数：** `countrycode` (国家码，默认 86)
- **调用示例：** `/register/cellphone?phone=13xxx&password=xxxxx&captcha=1234&nickname=binary1345`

### 检测手机号码是否已注册

- **接口地址：** `/cellphone/existence/check`
- **必选参数：** `phone` (手机号码)
- **可选参数：** `countrycode` (国家码，默认 86)
- **调用示例：** `/cellphone/existence/check?phone=13xxx`

### 初始化昵称

- **接口地址：** `/activate/init/profile`
- **必选参数：** `nickname` (昵称)
- **说明：** 刚注册的账号（需登录），调用此接口可初始化昵称
- **调用示例：** `/activate/init/profile?nickname=testUser2019`

### 重复昵称检测

- **接口地址：** `/nickname/check`
- **必选参数：** `nickname` (昵称)
- **说明：** 检测昵称是否重复，并提供备用昵称
- **调用示例：** `/nickname/check?nickname=binaryify`

### 更换绑定手机

- **接口地址：** `/rebind`
- **必选参数：** `oldcaptcha` (原手机验证码)，`captcha` (新手机验证码)，`phone` (手机号码)
- **可选参数：** `ctcode` (国家区号，默认 86)
- **说明：** 先发送验证码到原手机号码，再发送验证码到新手机号码，然后再调用此接口
- **调用示例：** `/rebind?phone=xxx&oldcaptcha=1234&captcha=5678`

### 退出登录

- **接口地址：** `/logout`
- **说明：** 退出登录
- **调用示例：** `/logout`

### 登录状态

- **接口地址：** `/login/status`
- **说明：** 获取登录状态
- **调用示例：** `/login/status`

---

## 用户相关

### 获取用户详情

- **接口地址：** `/user/detail`
- **必选参数：** `uid` (用户 id)
- **调用示例：** `/user/detail?uid=32953014`

### 获取账号信息

- **接口地址：** `/user/account`
- **说明：** 登录后调用此接口，可获取用户账号信息
- **调用示例：** `/user/account`

### 获取用户信息、歌单、收藏、mv、dj 数量

- **接口地址：** `/user/subcount`
- **说明：** 登录后调用此接口，可以获取用户信息
- **调用示例：** `/user/subcount`

### 获取用户等级信息

- **接口地址：** `/user/level`
- **说明：** 登录后调用此接口，可获取用户等级信息，包含当前登录天数、听歌次数等
- **调用示例：** `/user/level`

### 获取用户绑定信息

- **接口地址：** `/user/binding`
- **必选参数：** `uid` (用户 id)
- **调用示例：** `/user/binding?uid=32953014`

### 用户绑定手机

- **接口地址：** `/user/bindingcellphone`
- **必选参数：** `phone` (手机号码)，`captcha` (验证码)，`password` (密码)
- **可选参数：** `countrycode` (国家地区代码，默认 86)
- **说明：** 登录后调用此接口，可以绑定手机
- **调用示例：** `/user/bindingcellphone?phone=xxx&captcha=1234&password=yyy`

### 用户绑定手机（旧接口）

- **接口地址：** `/user/replacephone`
- **必选参数：** `phone` (手机号码)，`oldcaptcha` (原手机验证码)，`captcha` (新手机验证码)
- **可选参数：** `countrycode` (国家地区代码，默认 86)
- **调用示例：** `/user/replacephone?phone=xxx&captcha=1234&oldcaptcha=2345`

### 更新用户信息

- **接口地址：** `/user/update`
- **必选参数：** `gender` (性别: 0保密,1男性,2女性)，`birthday` (出生日期时间戳)，`nickname` (昵称)，`province` (省份id)，`city` (城市id)，`signature` (签名)
- **调用示例：** `/user/update?gender=0&signature=测试签名&city=440300&nickname=binary&birthday=1525918298004&province=440000`

### 更新头像

- **接口地址：** `/avatar/upload`
- **说明：** 登录后调用此接口，使用 `'Content-Type': 'multipart/form-data'` 上传图片 formData(name 为 'imgFile')
- **可选参数：** `imgSize` (图片尺寸,默认300)，`imgX` (水平裁剪偏移)，`imgY` (垂直裁剪偏移)
- **调用示例：** `/avatar/upload?imgSize=200`

### 获取用户歌单

- **接口地址：** `/user/playlist`
- **必选参数：** `uid` (用户 id)
- **可选参数：** `limit` (返回数量,默认30)，`offset` (偏移数量,用于分页)
- **调用示例：** `/user/playlist?uid=32953014`

### 获取用户电台

- **接口地址：** `/user/dj`
- **必选参数：** `uid` (用户 id)
- **调用示例：** `/user/dj?uid=32953014`

### 获取用户关注列表

- **接口地址：** `/user/follows`
- **必选参数：** `uid` (用户 id)
- **可选参数：** `limit` (返回数量,默认30)，`offset` (偏移数量,用于分页)
- **调用示例：** `/user/follows?uid=32953014`

### 获取用户粉丝列表

- **接口地址：** `/user/followeds`
- **必选参数：** `uid` (用户 id)
- **可选参数：** `limit` (返回数量,默认30)，`offset` (偏移数量,用于分页)
- **调用示例：** `/user/followeds?uid=32953014`

### 获取用户动态

- **接口地址：** `/user/event`
- **必选参数：** `uid` (用户 id)
- **可选参数：** `limit` (返回数量,默认30)，`lasttime` (上一页返回的 lasttime,默认-1)
- **调用示例：** `/user/event?uid=32953014`
- **返回 type 对应：** 18 分享单曲, 19 分享专辑, 17/28 分享电台节目, 22 转发, 39 发布视频, 35/13 分享歌单, 24 分享专栏文章, 41/21 分享视频

### 获取用户播放记录

- **接口地址：** `/user/record`
- **必选参数：** `uid` (用户 id)
- **可选参数：** `type` (type=1 返回 weekData, type=0 返回 allData)
- **调用示例：** `/user/record?uid=32953014&type=1`

### 获取用户历史评论

- **接口地址：** `/user/comment/history`
- **必选参数：** `uid` (用户 id)
- **可选参数：** `limit` (返回数量,默认10)，`time` (上一条数据的 time,第一页默认0)
- **调用示例：** `/user/comment/history?uid=32953014`

### 获取动态消息

- **接口地址：** `/event`
- **可选参数：** `pagesize` (每页数据,默认20)，`lasttime` (上页 lasttime,默认-1)
- **说明：** 对应网页版网易云"朋友"界面里的各种动态消息
- **调用示例：** `/event?pagesize=30&lasttime=1556740526369`

### 转发用户动态

- **接口地址：** `/event/forward`
- **必选参数：** `uid` (用户 id)，`evId` (动态 id)，`forwards` (转发评论)
- **调用示例：** `/event/forward?evId=6712917601&uid=32953014&forwards=测试内容`

### 删除用户动态

- **接口地址：** `/event/del`
- **必选参数：** `evId` (动态 id)
- **调用示例：** `/event/del?evId=6712917601`

### 关注/取消关注用户

- **接口地址：** `/follow`
- **必选参数：** `id` (用户 id)，`t` (1 为关注,其他为取消关注)
- **调用示例：** `/follow?id=32953014&t=1`

### 私信和通知接口

- **接口地址：** `/pl/count`
- **说明：** 登录后调用此接口，可获取私信和通知数量信息
- **调用示例：** `/pl/count`

---

## 歌曲相关

### 获取歌曲详情

- **接口地址：** `/song/detail`
- **必选参数：** `ids` (音乐 id,支持多个用逗号隔开)
- **调用示例：** `/song/detail?ids=347230`，`/song/detail?ids=347230,347231`

### 获取音乐 URL

- **接口地址：** `/song/url`
- **必选参数：** `id` (音乐 id,支持多个用逗号隔开)
- **可选参数：** `br` (码率,默认 999000 即最大码率)
- **说明：** 未登录状态或非会员返回试听片段
- **调用示例：** `/song/url?id=33894312`

### 获取音乐 URL - 新版

- **接口地址：** `/song/url/v1`
- **必选参数：** `id` (音乐 id)，`level` (音质等级：`standard` 标准, `higher` 较高, `exhigh` 极高, `lossless` 无损, `hires` Hi-Res, `jyeffect` 高清环绕声, `sky` 沉浸环绕声, `jymaster` 超清母带)
- **调用示例：** `/song/url/v1?id=33894312&level=exhigh`

### 获取客户端歌曲下载 URL

- **接口地址：** `/song/download/url`
- **必选参数：** `id` (音乐 id,仅支持单首)
- **可选参数：** `br` (码率,默认 999000)
- **说明：** 部分歌曲在非 VIP 账号上可以下载无损音质但不能试听无损音质，此接口可获取这些歌曲的无损音频
- **调用示例：** `/song/download/url?id=33894312`

### 音乐是否可用

- **接口地址：** `/check/music`
- **必选参数：** `id` (歌曲 id)
- **可选参数：** `br` (码率,默认 999000)
- **调用示例：** `/check/music?id=33894312`

### 获取歌词

- **接口地址：** `/lyric`
- **必选参数：** `id` (音乐 id)
- **调用示例：** `/lyric?id=33894312`

### 获取逐字歌词

- **接口地址：** `/lyric/new`
- **必选参数：** `id` (音乐 id)
- **说明：** 此接口的 `yrc` 字段即为逐字歌词（可能有歌曲不包含）
- **调用示例：** `/lyric/new?id=1824020871`

### 新歌速递

- **接口地址：** `/top/song`
- **必选参数：** `type` (地区类型：0 全部, 7 华语, 96 欧美, 8 日本, 16 韩国)
- **调用示例：** `/top/song?type=96`

### 音乐百科 - 简要信息

- **接口地址：** `/song/wiki/summary`
- **必选参数：** `id` (歌曲 id)
- **说明：** 此接口返回内容过于复杂，请按需取用
- **调用示例：** `/song/wiki/summary?id=1958384591`

### 乐谱列表

- **接口地址：** `/sheet/list`
- **必选参数：** `id` (歌曲 id)
- **可选参数：** `ab` (A/B 测试,默认 'b')
- **调用示例：** `/sheet/list?id=1815684465`

### 乐谱内容

- **接口地址：** `/sheet/preview`
- **必选参数：** `id` (乐谱 ID，不是歌曲 ID)
- **说明：** 登录后调用此接口获取乐谱的内容
- **调用示例：** `/sheet/preview?id=143190`

### 喜欢音乐

- **接口地址：** `/like`
- **必选参数：** `id` (歌曲 id)
- **可选参数：** `like` (布尔值,默认为 true 即收藏, false 为取消收藏)
- **调用示例：** `/like?id=347230`

### 喜欢音乐列表

- **接口地址：** `/likelist`
- **必选参数：** `uid` (用户 id)
- **调用示例：** `/likelist?uid=32953014`

### 已购单曲

- **接口地址：** `/song/purchased`
- **可选参数：** `limit` (返回数量,默认20)，`offset` (偏移数量)
- **说明：** 登录后调用此接口可获取已购买的单曲
- **调用示例：** `/song/purchased?limit=10`

### 歌曲相关视频

- **接口地址：** `/mlog/music/rcmd`
- **必选参数：** `songid` (歌曲 ID)
- **可选参数：** `mvid` (指定 MV id 会作为第一个返回)，`limit` (返回数量)
- **说明：** 获取歌曲相关视频（区别于 MV），有些歌曲没有 MV 但是有用户上传的相关 Mlog
- **调用示例：** `/mlog/music/rcmd?songid=186016`

### 听歌打卡

- **接口地址：** `/scrobble`
- **必选参数：** `id` (歌曲 id)，`source` (来源)，`time` (播放时间)
- **调用示例：** `/scrobble?id=347230&source=w&time=200`

### 调整歌曲顺序

- **接口地址：** `/song/order/update`
- **必选参数：** `pid` (歌单 id)，`ids` (歌曲 id 列表)
- **调用示例：** `/song/order/update?pid=2039116066&ids=[5268328,1219871]`

---

## 专辑相关

### 获取专辑内容

- **接口地址：** `/album`
- **必选参数：** `id` (专辑 id)
- **调用示例：** `/album?id=32311`

### 专辑动态信息

- **接口地址：** `/album/detail/dynamic`
- **必选参数：** `id` (专辑 id)
- **说明：** 获取专辑动态信息，如是否收藏、收藏数、评论数、分享数
- **调用示例：** `/album/detail/dynamic?id=32311`

### 收藏/取消收藏专辑

- **接口地址：** `/album/sub`
- **必选参数：** `id` (专辑 id)，`t` (1 为收藏,其他为取消收藏)
- **调用示例：** `/album/sub?t=1&id=32311`

### 获取已收藏专辑列表

- **接口地址：** `/album/sublist`
- **可选参数：** `limit` (返回数量,默认25)，`offset` (偏移数量)
- **调用示例：** `/album/sublist`

### 数字专辑-语种风格馆

- **接口地址：** `/album/list/style`
- **必选参数：** `area` (地区 ZONE_ID)，`offset` (偏移)，`limit` (数量)
- **调用示例：** `/album/list/style?area=Z_H&offset=0&limit=10`

### 数字专辑-新碟上架

- **接口地址：** `/album/list`
- **必选参数：** `area` (地区 ALL/ZH/EA/KR/JP)，`offset`，`limit`，`type` (new/hot)
- **调用示例：** `/album/list?area=ALL&offset=0&limit=10`

### 全部新碟

- **接口地址：** `/album/new`
- **可选参数：** `limit`，`offset`，`area` (ALL/ZH/EA/KR/JP/HaiTai)
- **调用示例：** `/album/new?area=ALL&offset=0&limit=50`

### 最新专辑

- **接口地址：** `/album/newest`
- **调用示例：** `/album/newest`

### 数字专辑&数字单曲-榜单

- **接口地址：** `/album/songsaleboard`
- **必选参数：** `type` (daily 日榜, week 周榜, year 年榜, total 总榜)
- **可选参数：** `year` (年份,type 为 year 时有效)，`albumType` (0 数字专辑,1 数字单曲)
- **调用示例：** `/album/songsaleboard?type=year&year=2020&albumType=0`

---

## 歌单相关

### 获取歌单详情

- **接口地址：** `/playlist/detail`
- **必选参数：** `id` (歌单 id)
- **可选参数：** `s` (歌单最近的 s 个收藏者,默认为8)
- **调用示例：** `/playlist/detail?id=24381616`

### 歌单详情动态

- **接口地址：** `/playlist/detail/dynamic`
- **必选参数：** `id` (歌单 id)
- **说明：** 获取歌单详情动态部分，如评论数、是否收藏、播放数
- **调用示例：** `/playlist/detail/dynamic?id=24381616`

### 获取歌单所有歌曲

- **接口地址：** `/playlist/track/all`
- **必选参数：** `id` (歌单 id)
- **可选参数：** `limit` (限制获取歌曲数量)，`offset` (偏移,默认0)
- **调用示例：** `/playlist/track/all?id=24381616&limit=10&offset=1`

### 歌单更新播放量

- **接口地址：** `/playlist/update/playcount`
- **必选参数：** `id` (歌单 id)
- **调用示例：** `/playlist/update/playcount?id=24381616`

### 新建歌单

- **接口地址：** `/playlist/create`
- **必选参数：** `name` (歌单名)
- **可选参数：** `privacy` (隐私歌单,传'10')，`type` (歌单类型: NORMAL/VIDEO/SHARED)
- **调用示例：** `/playlist/create?name=测试歌单`

### 删除歌单

- **接口地址：** `/playlist/delete`
- **必选参数：** `id` (歌单 id,可多个用逗号隔开)
- **调用示例：** `/playlist/delete?id=2947311456`

### 收藏/取消收藏歌单

- **接口地址：** `/playlist/subscribe`
- **必选参数：** `t` (1 收藏,2 取消收藏)，`id` (歌单 id)
- **调用示例：** `/playlist/subscribe?t=1&id=106697785`

### 歌单收藏者

- **接口地址：** `/playlist/subscribers`
- **必选参数：** `id` (歌单 id)
- **可选参数：** `limit` (默认20)，`offset` (偏移)
- **调用示例：** `/playlist/subscribers?id=544215255&limit=30`

### 对歌单添加或删除歌曲

- **接口地址：** `/playlist/tracks`
- **必选参数：** `op` (add 增加/del 删除)，`pid` (歌单 id)，`tracks` (歌曲 id,可多个用逗号隔开)
- **调用示例：** `/playlist/tracks?op=add&pid=24381616&tracks=347231`

### 收藏视频到视频歌单

- **接口地址：** `/playlist/track/add`
- **必选参数：** `pid` (歌单 id)，`ids` (视频 id,支持多个用逗号隔开)
- **调用示例：** `/playlist/track/add?pid=5271999357&ids=186041`

### 删除视频歌单里的视频

- **接口地址：** `/playlist/track/delete`
- **必选参数：** `pid` (歌单 id)，`ids` (视频 id,支持多个用逗号隔开)
- **调用示例：** `/playlist/track/delete?pid=5271999357&ids=186041`

### 最近播放的视频

- **接口地址：** `/playlist/video/recent`
- **调用示例：** `/playlist/video/recent`

### 更新歌单

- **接口地址：** `/playlist/update`
- **必选参数：** `id` (歌单 id)，`name` (歌单名)，`desc` (描述)，`tags` (标签,多个用 `;` 隔开)
- **调用示例：** `/playlist/update?id=24381616&name=新歌单&desc=描述&tags=欧美`

### 更新歌单描述

- **接口地址：** `/playlist/desc/update`
- **必选参数：** `id` (歌单 id)，`desc` (描述)
- **调用示例：** `/playlist/desc/update?id=24381616&desc=描述`

### 更新歌单名

- **接口地址：** `/playlist/name/update`
- **必选参数：** `id` (歌单 id)，`name` (歌单名)
- **调用示例：** `/playlist/name/update?id=24381616&name=新歌单`

### 更新歌单标签

- **接口地址：** `/playlist/tags/update`
- **必选参数：** `id` (歌单 id)，`tags` (标签)
- **调用示例：** `/playlist/tags/update?id=24381616&tags=学习`

### 歌单封面上传

- **接口地址：** `/playlist/cover/update`
- **必选参数：** `id` (歌单 id)
- **可选参数：** `imgSize` (图片尺寸,默认300)，`imgX`，`imgY`
- **说明：** 使用 `'Content-Type': 'multipart/form-data'` 上传图片 formData(name 为 'imgFile')
- **调用示例：** `/playlist/cover/update?id=3143833470&imgSize=200`

### 调整歌单顺序

- **接口地址：** `/playlist/order/update`
- **必选参数：** `ids` (歌单 id 列表)
- **调用示例：** `/playlist/order/update?ids=[111,222]`

### 歌单分类

- **接口地址：** `/playlist/catlist`
- **调用示例：** `/playlist/catlist`

### 热门歌单分类

- **接口地址：** `/playlist/hot`
- **调用示例：** `/playlist/hot`

### 公开隐私歌单

- **接口地址：** `/playlist/privacy`
- **必选参数：** `id` (歌单 ID)
- **调用示例：** `/playlist/privacy?id=24381616`

### 获取点赞过的视频

- **接口地址：** `/playlist/mylike`
- **调用示例：** `/playlist/mylike`

### 歌单（网友精选碟）

- **接口地址：** `/top/playlist`
- **可选参数：** `order` (new/hot, 默认 hot)，`cat` (分类,如"华语","古风")，`limit` (默认50)，`offset`
- **调用示例：** `/top/playlist?limit=10&order=new`

### 精品歌单标签列表

- **接口地址：** `/playlist/highquality/tags`
- **调用示例：** `/playlist/highquality/tags`

### 获取精品歌单

- **接口地址：** `/top/playlist/highquality`
- **可选参数：** `cat` (分类)，`limit` (默认50)，`before` (分页参数,取上一页最后一个歌单的 updateTime)
- **调用示例：** `/top/playlist/highquality?limit=20`

### 相关歌单推荐

- **接口地址：** `/related/playlist`
- **必选参数：** `id` (歌单 id)
- **调用示例：** `/related/playlist?id=1`

---

## 评论相关

### 歌曲评论

- **接口地址：** `/comment/music`
- **必选参数：** `id` (音乐 id)
- **可选参数：** `limit` (默认20)，`offset` (偏移)，`before` (分页参数)
- **调用示例：** `/comment/music?id=186016&limit=1`

### 专辑评论

- **接口地址：** `/comment/album`
- **必选参数：** `id` (专辑 id)
- **可选参数：** `limit` (默认20)，`offset`，`before`
- **调用示例：** `/comment/album?id=32311`

### 歌单评论

- **接口地址：** `/comment/playlist`
- **必选参数：** `id` (歌单 id)
- **可选参数：** `limit` (默认20)，`offset`，`before`
- **调用示例：** `/comment/playlist?id=705123491`

### MV评论

- **接口地址：** `/comment/mv`
- **必选参数：** `id` (mv id)
- **可选参数：** `limit` (默认20)，`offset`，`before`
- **调用示例：** `/comment/mv?id=5436712`

### 电台节目评论

- **接口地址：** `/comment/dj`
- **必选参数：** `id` (电台节目 id)
- **可选参数：** `limit` (默认20)，`offset`，`before`
- **调用示例：** `/comment/dj?id=794062371`

### 视频评论

- **接口地址：** `/comment/video`
- **必选参数：** `id` (视频 id)
- **可选参数：** `limit` (默认20)，`offset`，`before`
- **调用示例：** `/comment/video?id=89ADDE33C0AAE8EC14B99F6750DB954D`

### 热门评论

- **接口地址：** `/comment/hot`
- **必选参数：** `id` (资源 id)，`type` (0:歌曲,1:mv,2:歌单,3:专辑,4:电台节目,5:视频,6:动态,7:电台)
- **可选参数：** `limit` (默认20)，`offset`，`before`
- **调用示例：** `/comment/hot?id=186016&type=0`

### 新版评论接口

- **接口地址：** `/comment/new`
- **必选参数：** `id` (资源 id)，`type` (0:歌曲,1:mv,2:歌单,3:专辑,4:电台节目,5:视频,6:动态,7:电台)
- **可选参数：** `pageNo` (页码,默认1)，`pageSize` (默认20)，`sortType` (1:推荐,2:热度,3:时间)，`cursor`
- **调用示例：** `/comment/new?type=0&id=1407551413&sortType=3`

### 楼层评论

- **接口地址：** `/comment/floor`
- **必选参数：** `parentCommentId` (楼层评论 id)，`id` (资源 id)，`type` (0:歌曲,1:mv,2:歌单,3:专辑,4:电台节目,5:视频,6:动态,7:电台)
- **可选参数：** `limit` (默认20)，`time` (分页参数)
- **调用示例：** `/comment/floor?parentCommentId=1438569889&id=29764564&type=0`

### 给评论点赞

- **接口地址：** `/comment/like`
- **必选参数：** `id` (资源 id)，`cid` (评论 id)，`t` (1点赞/0取消点赞)，`type` (0:歌曲,1:mv,2:歌单,3:专辑,4:电台节目,5:视频,6:动态,7:电台)
- **调用示例：** `/comment/like?id=29178366&cid=12840183&t=1&type=0`
- **注意：** 动态点赞不需传 id，需传 `threadId`

### 发送/删除评论

- **接口地址：** `/comment`
- **发送评论：** `t=1` (发送), `t=2` (回复)；`type` (资源类型)；`id` (资源 id)；`content` (内容)；`commentId` (回复时必填)
- **删除评论：** `t=0`；`type`；`id`；`commentId`
- **调用示例：** `/comment?t=1&type=1&id=5436712&content=test`

### 抱一抱评论

- **接口地址：** `/hug/comment`
- **必选参数：** `uid` (用户 id)，`cid` (评论 id)，`sid` (资源 id)
- **调用示例：** `/hug/comment?uid=285516405&cid=1167145843&sid=863481066`

### 评论抱一抱列表

- **接口地址：** `/comment/hug/list`
- **必选参数：** `uid`，`cid`，`sid`
- **可选参数：** `page`，`cursor`，`idCursor`，`pageSize` (默认100)
- **调用示例：** `/comment/hug/list?uid=285516405&cid=1167145843&sid=863481066`

### 获取动态评论

- **接口地址：** `/comment/event`
- **必选参数：** `threadId` (动态 id,可通过 `/event` 获取)
- **调用示例：** `/comment/event?threadId=A_EV_2_6559519868_32953014`

### 云村热评

> 官方已下架，暂不能用

- **接口地址：** `/comment/hotwall/list`
- **调用示例：** `/comment/hotwall/list`

---

## 搜索相关

### 搜索

- **接口地址：** `/search` 或 `/cloudsearch`（更全）
- **必选参数：** `keywords` (关键词)
- **可选参数：** `limit` (默认30)，`offset` (偏移)，`type` (搜索类型)
- **type 取值：** 1:单曲, 10:专辑, 100:歌手, 1000:歌单, 1002:用户, 1004:MV, 1006:歌词, 1009:电台, 1014:视频, 1018:综合, 2000:声音
- **调用示例：** `/search?keywords=海阔天空`，`/cloudsearch?keywords=海阔天空`

### 默认搜索关键词

- **接口地址：** `/search/default`
- **调用示例：** `/search/default`

### 热搜列表（简略）

- **接口地址：** `/search/hot`
- **调用示例：** `/search/hot`

### 热搜列表（详细）

- **接口地址：** `/search/hot/detail`
- **调用示例：** `/search/hot/detail`

### 搜索建议

- **接口地址：** `/search/suggest`
- **必选参数：** `keywords` (关键词)
- **可选参数：** `type` (若传 'mobile' 则返回移动端数据)
- **调用示例：** `/search/suggest?keywords=海阔天空`

### 搜索多重匹配

- **接口地址：** `/search/multimatch`
- **必选参数：** `keywords` (关键词)
- **调用示例：** `/search/multimatch?keywords=海阔天空`

---

## 歌手相关

### 获取歌手单曲

- **接口地址：** `/artists`
- **必选参数：** `id` (歌手 id)
- **调用示例：** `/artists?id=6452`

### 获取歌手 MV

- **接口地址：** `/artist/mv`
- **必选参数：** `id` (歌手 id)
- **调用示例：** `/artist/mv?id=6452`

### 获取歌手专辑

- **接口地址：** `/artist/album`
- **必选参数：** `id` (歌手 id)
- **可选参数：** `limit` (默认30)，`offset`
- **调用示例：** `/artist/album?id=6452&limit=30`

### 获取歌手描述

- **接口地址：** `/artist/desc`
- **必选参数：** `id` (歌手 id)
- **调用示例：** `/artist/desc?id=6452`

### 获取歌手详情

- **接口地址：** `/artist/detail`
- **必选参数：** `id` (歌手 id)
- **调用示例：** `/artist/detail?id=6452`

### 歌手分类列表

- **接口地址：** `/artist/list`
- **可选参数：** `limit` (默认30)，`offset`，`initial` (按首字母查找)，`type` (-1全部,1男歌手,2女歌手,3乐队)，`area` (-1全部,7华语,96欧美,8日本,16韩国,0其他)
- **调用示例：** `/artist/list?type=1&area=96&initial=b`

### 收藏/取消收藏歌手

- **接口地址：** `/artist/sub`
- **必选参数：** `id` (歌手 id)，`t` (1收藏,其他取消收藏)
- **调用示例：** `/artist/sub?id=6452&t=1`

### 收藏的歌手列表

- **接口地址：** `/artist/sublist`
- **调用示例：** `/artist/sublist`

### 歌手热门 50 首歌曲

- **接口地址：** `/artist/top/song`
- **必选参数：** `id` (歌手 id)
- **调用示例：** `/artist/top/song?id=6452`

### 歌手全部歌曲

- **接口地址：** `/artist/songs`
- **必选参数：** `id` (歌手 id)
- **可选参数：** `order` (hot/time)，`limit` (默认50)，`offset`
- **调用示例：** `/artist/songs?id=6452`

### 歌手粉丝

- **接口地址：** `/artist/fans`
- **必选参数：** `id` (歌手 id)
- **可选参数：** `limit`，`offset`
- **调用示例：** `/artist/fans?id=2116&limit=10&offset=0`

### 歌手粉丝数量

- **接口地址：** `/artist/follow/count`
- **必选参数：** `id` (歌手 id)
- **可选参数：** `limit` (默认20)，`offset`
- **调用示例：** `/artist/follow/count?id=2116`

### 获取相似歌手

- **接口地址：** `/simi/artist`
- **必选参数：** `id` (歌手 id)
- **调用示例：** `/simi/artist?id=6452`

### 关注歌手新歌

- **接口地址：** `/artist/new/song`
- **可选参数：** `limit` (默认20)，`before` (上一页返回的 publishTime)
- **说明：** 登录后调用此接口可获取关注歌手新歌
- **调用示例：** `/artist/new/song?limit=1`

### 关注歌手新 MV

- **接口地址：** `/artist/new/mv`
- **可选参数：** `limit` (默认20)，`before`
- **说明：** 登录后调用此接口可获取关注歌手新 MV
- **调用示例：** `/artist/new/mv?limit=1`

### 获取歌手视频

- **接口地址：** `/artist/video`
- **必选参数：** `id` (歌手 id)
- **可选参数：** `size` (默认10)，`cursor`，`order` (0按时间,1按热度)
- **调用示例：** `/artist/video?id=2116`

---

## MV/视频相关

### 获取 MV 详情

- **接口地址：** `/mv/detail`
- **必选参数：** `mvid` (mv id)
- **调用示例：** `/mv/detail?mvid=5436712`

### 获取 MV 点赞转发评论数数据

- **接口地址：** `/mv/detail/info`
- **必选参数：** `mvid` (mv id)
- **调用示例：** `/mv/detail/info?mvid=5436712`

### 获取 MV 播放地址

- **接口地址：** `/mv/url`
- **必选参数：** `id` (mv id)
- **可选参数：** `r` (分辨率,默认1080)
- **调用示例：** `/mv/url?id=5436712`

### 收藏/取消收藏 MV

- **接口地址：** `/mv/sub`
- **必选参数：** `mvid` (MV id)，`t` (1收藏,其他取消收藏)
- **调用示例：** `/mv/sub?mvid=5436712&t=1`

### 收藏的 MV 列表

- **接口地址：** `/mv/sublist`
- **调用示例：** `/mv/sublist`

### 全部 MV

- **接口地址：** `/mv/all`
- **可选参数：** `area` (全部/港台/欧美/日本/韩国)，`type` (全部/官方版/原生/现场版/网易出品)，`order` (上升最快/最热/最新)，`limit` (默认30)，`offset`
- **调用示例：** `/mv/all?area=&limit=10`

### 网易出品 MV

- **接口地址：** `/mv/exclusive/rcmd`
- **可选参数：** `limit` (默认30)，`offset`
- **调用示例：** `/mv/exclusive/rcmd?limit=10`

### 最新 MV

- **接口地址：** `/mv/first`
- **可选参数：** `area` (全部/港台/欧美/韩国/日本)，`limit` (默认30)
- **调用示例：** `/mv/first?limit=10`

### 推荐 MV

- **接口地址：** `/personalized/mv`
- **调用示例：** `/personalized/mv`

### MV 排行

- **接口地址：** `/top/mv`
- **可选参数：** `limit`，`offset`
- **调用示例：** `/top/mv?limit=10`

### 获取视频播放地址

- **接口地址：** `/video/url`
- **必选参数：** `id` (视频 id)
- **可选参数：** `res` (分辨率,默认1080)
- **调用示例：** `/video/url?id=89ADDE33C0AAE8EC14B99F6750DB954D`

### 视频详情

- **接口地址：** `/video/detail`
- **必选参数：** `id` (视频 id)
- **调用示例：** `/video/detail?id=89ADDE33C0AAE8EC14B99F6750DB954D`

### 获取视频点赞转发评论数数据

- **接口地址：** `/video/detail/info`
- **必选参数：** `vid` (视频 id)
- **调用示例：** `/video/detail/info?vid=89ADDE33C0AAE8EC14B99F6750DB954D`

### 收藏视频

- **接口地址：** `/video/sub`
- **必选参数：** `id` (视频 id)，`t` (1为收藏,其他取消收藏)
- **调用示例：** `/video/sub?id=89ADDE33C0AAE8EC14B99F6750DB954D&t=1`

### 获取视频标签列表

- **接口地址：** `/video/group/list`
- **调用示例：** `/video/group/list`

### 获取视频标签下的视频

- **接口地址：** `/video/group`
- **必选参数：** `id` (videoGroup id)
- **可选参数：** `offset` (偏移)
- **调用示例：** `/video/group?id=9104`

### 获取全部视频列表

- **接口地址：** `/video/timeline/all`
- **必选参数：** `group` (group id)
- **可选参数：** `offset` (偏移)
- **调用示例：** `/video/timeline/all?group=9104`

### 获取推荐视频

- **接口地址：** `/video/timeline/recommend`
- **可选参数：** `offset` (偏移)
- **调用示例：** `/video/timeline/recommend`

### 相关视频

- **接口地址：** `/related/allvideo`
- **必选参数：** `id` (歌曲 id)
- **调用示例：** `/related/allvideo?id=1`

### 资源点赞（MV、电台、视频）

- **接口地址：** `/resource/like`
- **必选参数：** `type` (0:歌曲,1:mv,2:歌单,3:专辑,4:电台节目,5:视频,6:动态,7:电台)，`t` (1点赞,其他取消)，`id` (资源 id)
- **调用示例：** `/resource/like?t=1&type=1&id=5436712`
- **注意：** 动态点赞需传 `threadId`，不传 `id`

### 获取视频分类列表

- **接口地址：** `/video/category/list`
- **调用示例：** `/video/category/list`

### 获取 mlog 播放地址

- **接口地址：** `/mlog/url`
- **必选参数：** `id` (mlog id)
- **可选参数：** `res` (分辨率,默认1080)
- **调用示例：** `/mlog/url?id=a1qOVPTWKS1ZrK8`

### 将 mlog id 转为视频 id

- **接口地址：** `/mlog/to/video`
- **必选参数：** `id` (mlog id)
- **调用示例：** `/mlog/to/video?id=a1qOVPTWKS1ZrK8`

---

## 电台相关

### 电台 banner

- **接口地址：** `/dj/banner`
- **调用示例：** `/dj/banner`

### 电台分类列表

- **接口地址：** `/dj/catelist`
- **调用示例：** `/dj/catelist`

### 电台付费精品

- **接口地址：** `/dj/paygift`
- **可选参数：** `limit` (默认30)，`offset`
- **调用示例：** `/dj/paygift?limit=10`

### 电台详情

- **接口地址：** `/dj/detail`
- **必选参数：** `rid` (电台 id)
- **调用示例：** `/dj/detail?rid=336355127`

### 电台节目

- **接口地址：** `/dj/program`
- **必选参数：** `rid` (电台 id)
- **可选参数：** `limit` (默认30)，`offset`，`asc` (true 按最早,false 按最晚)
- **调用示例：** `/dj/program?rid=336355127`

### 电台节目详情

- **接口地址：** `/dj/program/detail`
- **必选参数：** `id` (节目 id)
- **调用示例：** `/dj/program/detail?id=1368265191`

### 电台 - 推荐

- **接口地址：** `/dj/recommend`
- **调用示例：** `/dj/recommend`

### 电台 - 分类推荐

- **接口地址：** `/dj/recommend/type`
- **必选参数：** `type` (电台分类 id)
- **调用示例：** `/dj/recommend/type?type=1`

### 电台 - 订阅

- **接口地址：** `/dj/sub`
- **必选参数：** `t` (1订阅,0取消订阅)，`rid` (电台 id)
- **调用示例：** `/dj/sub?t=1&rid=336355127`

### 电台 - 订阅列表

- **接口地址：** `/dj/sublist`
- **调用示例：** `/dj/sublist`

### 电台订阅者列表

- **接口地址：** `/dj/subscriber`
- **必选参数：** `id` (电台 id)
- **可选参数：** `limit` (默认20)，`time` (上页最后一项的 time)
- **调用示例：** `/dj/subscriber?id=336355127`

### 热门电台

- **接口地址：** `/dj/hot`
- **可选参数：** `limit` (默认30)，`offset`
- **调用示例：** `/dj/hot?limit=10`

### 电台 - 热门节目

- **接口地址：** `/dj/program/toplist`
- **可选参数：** `limit` (默认100)，`offset`
- **调用示例：** `/dj/program/toplist?limit=10`

### 电台 24 小时节目榜

- **接口地址：** `/dj/program/toplist/hours`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/dj/program/toplist/hours?limit=10`

### 电台 - 节目榜

- **接口地址：** `/dj/toplist`
- **可选参数：** `limit` (默认100)，`offset`
- **调用示例：** `/dj/toplist?limit=10`

### 电台 24 小时主播榜

- **接口地址：** `/dj/toplist/hours`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/dj/toplist/hours?limit=30`

### 电台主播新人榜

- **接口地址：** `/dj/toplist/newcomer`
- **可选参数：** `limit` (默认100)，`offset`
- **调用示例：** `/dj/toplist/newcomer?limit=30`

### 电台最热主播榜

- **接口地址：** `/dj/toplist/popular`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/dj/toplist/popular?limit=30`

### 电台付费精品榜

- **接口地址：** `/dj/toplist/pay`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/dj/toplist/pay?limit=30`

### 电台 - 今日优选

- **接口地址：** `/dj/today/perfered`
- **可选参数：** `page` (默认0)
- **调用示例：** `/dj/today/perfered?page=0`

### 电台 - 推荐类型

- **接口地址：** `/dj/category/recommend`
- **调用示例：** `/dj/category/recommend`

### 电台 - 非热门类型

- **接口地址：** `/dj/category/excludehot`
- **调用示例：** `/dj/category/excludehot`

### 电台个性推荐

- **接口地址：** `/dj/personalize/recommend`
- **可选参数：** `limit` (默认6)
- **调用示例：** `/dj/personalize/recommend`

### 类别热门电台

- **接口地址：** `/dj/radio/hot`
- **必选参数：** `cateId` (分类 id)
- **可选参数：** `limit` (默认30)，`offset`
- **调用示例：** `/dj/radio/hot?cateId=3`

### 节目推荐

- **接口地址：** `/program/recommend`
- **可选参数：** `limit` (默认10)，`offset`
- **调用示例：** `/program/recommend`

---

## 排行榜

### 所有榜单

- **接口地址：** `/toplist`
- **调用示例：** `/toplist`

### 所有榜单内容摘要

- **接口地址：** `/toplist/detail`
- **调用示例：** `/toplist/detail`

### 排行榜

- **接口地址：** `/top/list`
- **必选参数：** `idx` (榜单 id,通过 `/toplist` 获取)
- **调用示例：** `/top/list?idx=6`

### 歌手榜

- **接口地址：** `/toplist/artist`
- **可选参数：** `type` (1:华语,2:欧美,3:韩国,4:日本)，`offset`，`limit`
- **调用示例：** `/toplist/artist?type=1`

### 热门歌手

- **接口地址：** `/top/artists`
- **可选参数：** `limit` (默认50)，`offset`
- **调用示例：** `/top/artists?offset=0&limit=30`

---

## 推荐相关

### 每日推荐歌单

- **接口地址：** `/recommend/resource`
- **说明：** 登录后调用此接口，可获取每日推荐歌单
- **调用示例：** `/recommend/resource`

### 每日推荐歌曲

- **接口地址：** `/recommend/songs`
- **说明：** 登录后调用此接口，可获取每日推荐歌曲
- **调用示例：** `/recommend/songs`

### 推荐歌单

- **接口地址：** `/personalized`
- **可选参数：** `limit` (默认30)，`offset`，`cat` (分类,比如 "华语")
- **调用示例：** `/personalized?limit=1`

### 推荐新音乐

- **接口地址：** `/personalized/newsong`
- **可选参数：** `limit` (默认30)，`area` (ALL/ZH/EA/KR/JP)
- **调用示例：** `/personalized/newsong?limit=1`

### 推荐电台

- **接口地址：** `/personalized/djprogram`
- **调用示例：** `/personalized/djprogram`

### 推荐节目

- **接口地址：** `/program/recommend`
- **调用示例：** `/program/recommend`

### 独家放送

- **接口地址：** `/personalized/privatecontent`
- **调用示例：** `/personalized/privatecontent`

### 独家放送列表

- **接口地址：** `/personalized/privatecontent/list`
- **可选参数：** `limit` (默认60)，`offset`
- **调用示例：** `/personalized/privatecontent/list?limit=1`

### banner

- **接口地址：** `/banner`
- **可选参数：** `type` (0:pc,1:android,2:iphone,3:ipad,默认0)
- **调用示例：** `/banner`，`/banner?type=2`

### 首页-发现

- **接口地址：** `/homepage/block/page`
- **可选参数：** `refresh` (是否刷新)，`cursor` (上一条返回的 cursor)
- **调用示例：** `/homepage/block/page`

### 首页-发现-圆形图标入口列表

- **接口地址：** `/homepage/dragon/ball`
- **调用示例：** `/homepage/dragon/ball`

### 心动模式/智能播放

- **接口地址：** `/playmode/intelligence/list`
- **必选参数：** `id` (歌曲 id)，`pid` (歌单 id)
- **可选参数：** `sid` (要开始播放的歌曲 id)
- **调用示例：** `/playmode/intelligence/list?id=33894312&pid=24381616`

### 私人 FM

- **接口地址：** `/personal_fm`
- **调用示例：** `/personal_fm`

### FM 垃圾桶

- **接口地址：** `/fm_trash`
- **必选参数：** `id` (歌曲 id)
- **调用示例：** `/fm_trash?id=347230`

### 每日签到

- **接口地址：** `/daily_signin`
- **可选参数：** `type` (0:安卓,1:网页/PC)
- **调用示例：** `/daily_signin`

### 签到进度

- **接口地址：** `/signin/progress`
- **可选参数：** `moduleId` (模块 id,默认 '1207signin-1207signin')
- **调用示例：** `/signin/progress?moduleId=1207signin-1207signin`

### 乐签信息

- **接口地址：** `/sign/happy/info`
- **调用示例：** `/sign/happy/info`

### 历史日推可用日期列表

- **接口地址：** `/history/recommend/songs`
- **说明：** 登录后调用此接口，可获取历史日推可用日期列表
- **调用示例：** `/history/recommend/songs`

### 历史日推详细数据

- **接口地址：** `/history/recommend/songs/detail`
- **必选参数：** `date` (历史日推日期,通过上一接口获取)
- **调用示例：** `/history/recommend/songs/detail?date=2021-05-23`

### 相似歌曲

- **接口地址：** `/simi/song`
- **必选参数：** `id` (歌曲 id)
- **可选参数：** `limit` (默认50)，`offset`
- **调用示例：** `/simi/song?id=347230`

### 相似歌单

- **接口地址：** `/simi/playlist`
- **必选参数：** `id` (歌曲 id)
- **可选参数：** `limit` (默认50)，`offset`
- **调用示例：** `/simi/playlist?id=347230`

### 相似 MV

- **接口地址：** `/simi/mv`
- **必选参数：** `mvid` (mv id)
- **调用示例：** `/simi/mv?mvid=5436712`

### 获取最近 5 个听了这首歌的用户

- **接口地址：** `/simi/user`
- **必选参数：** `id` (歌曲 id)
- **可选参数：** `limit` (默认5)
- **调用示例：** `/simi/user?id=347230`

### 获取精品歌单

- **接口地址：** `/top/playlist/highquality`
- **可选参数：** `cat` (分类)，`limit` (默认50)，`before`
- **调用示例：** `/top/playlist/highquality?before=1503639064232&limit=3`

### 分享到动态

- **接口地址：** `/share/resource`
- **必选参数：** `id` (资源 id)
- **可选参数：** `type` (song/playlist/mv/djradio/djprogram)，`msg` (内容140字)
- **调用示例：** `/share/resource?id=1297494209&msg=测试`

---

## 云盘相关

### 云盘数据

- **接口地址：** `/user/cloud`
- **可选参数：** `limit` (默认30)，`offset`
- **调用示例：** `/user/cloud`

### 云盘数据详情

- **接口地址：** `/user/cloud/detail`
- **必选参数：** `id` (歌曲 id)
- **调用示例：** `/user/cloud/detail?id=347230`

### 云盘歌曲删除

- **接口地址：** `/user/cloud/del`
- **必选参数：** `id` (歌曲 id)
- **调用示例：** `/user/cloud/del?id=347230`

### 云盘歌曲信息匹配纠正

- **接口地址：** `/cloud/match`
- **必选参数：** `uid` (用户 id)，`sid` (云盘歌曲 id)，`asid` (要匹配的歌曲 id)
- **调用示例：** `/cloud/match?uid=32953014&sid=347230&asid=347231`

### 云盘上传

- **接口地址：** `/cloud`
- **说明：** 登录后调用此接口，上传歌曲到云盘
- **调用示例：** 参考项目文件 `/public/cloud.html`

### 音频匹配

- **接口地址：** `/audio/match`
- **说明：** 调用此接口可进行音频匹配（听歌识曲）
- **调用示例：** `/audio/match`

---

## 数字专辑相关

### 数字专辑详情

- **接口地址：** `/digitalAlbum/detail`
- **必选参数：** `id` (专辑 id)
- **调用示例：** `/digitalAlbum/detail?id=120605500`

### 数字专辑销量

- **接口地址：** `/digitalAlbum/sales`
- **必选参数：** `ids` (专辑 id,支持多个用逗号隔开)
- **调用示例：** `/digitalAlbum/sales?ids=120605500`

### 购买数字专辑

- **接口地址：** `/digitalAlbum/ordering`
- **必选参数：** `id` (专辑 id)，`payment` (支付方式)，`quantity` (数量)
- **调用示例：** `/digitalAlbum/ordering?id=120605500&payment=10&quantity=1`

### 我的数字专辑

- **接口地址：** `/digitalAlbum/purchased`
- **可选参数：** `limit` (默认30)，`offset`
- **说明：** 登录后调用此接口可获取已购买的数字专辑
- **调用示例：** `/digitalAlbum/purchased?limit=10`

---

## 音乐人相关

### 音乐人数据概况

- **接口地址：** `/musician/data/overview`
- **说明：** 音乐人登录后调用此接口，可获取统计数据概况
- **调用示例：** `/musician/data/overview`

### 音乐人播放趋势

- **接口地址：** `/musician/play/trend`
- **必选参数：** `startTime` (开始时间)，`endTime` (结束时间)
- **调用示例：** `/musician/play/trend?startTime=2021-05-24&endTime=2021-05-30`

### 音乐人任务

- **接口地址：** `/musician/tasks`
- **说明：** 音乐人登录后调用此接口，可获取音乐人任务。status: 0 未开始, 10 进行中, 20 完成未领取, 100 完成已领取
- **调用示例：** `/musician/tasks`

### 音乐人任务（新）

- **接口地址：** `/musician/tasks/new`
- **说明：** 同上
- **调用示例：** `/musician/tasks/new`

### 账号云豆数

- **接口地址：** `/musician/cloudbean`
- **说明：** 音乐人登录后调用此接口，可获取账号云豆数
- **调用示例：** `/musician/cloudbean`

### 领取云豆

- **接口地址：** `/musician/cloudbean/obtain`
- **必选参数：** `id` (任务 id，通过 `/musician/tasks` 获取 `userMissionId`)，`period` (通过 `/musician/tasks` 获取)
- **调用示例：** `/musician/cloudbean/obtain?id=7036416928&period=1`

### 音乐人签到

- **接口地址：** `/musician/sign`
- **说明：** 音乐人登录后调用此接口，可完成"登录音乐人中心"任务
- **调用示例：** `/musician/sign`

---

## VIP 相关

### 获取 VIP 信息

- **接口地址：** `/vip/info`
- **说明：** 登录后调用此接口，可获取当前 VIP 信息
- **调用示例：** `/vip/info`

### VIP 成长值

- **接口地址：** `/vip/growthpoint`
- **说明：** 登录后调用此接口，可获取当前会员成长值
- **调用示例：** `/vip/growthpoint`

### VIP 成长值获取记录

- **接口地址：** `/vip/growthpoint/details`
- **可选参数：** `limit` (默认20)，`offset`
- **调用示例：** `/vip/growthpoint/details?limit=10`

### VIP 任务

- **接口地址：** `/vip/tasks`
- **说明：** 登录后调用此接口，可获取会员任务
- **调用示例：** `/vip/tasks`

### 领取 VIP 成长值

- **接口地址：** `/vip/growthpoint/get`
- **必选参数：** `ids` (通过 `/vip/tasks` 获取到的 `unGetIds`)
- **调用示例：** `/vip/growthpoint/get?ids=7043206830_7`

### 黑胶时光机

- **接口地址：** `/vip/timemachine`
- **可选参数：** `startTime`，`endTime`，`limit` (默认60)
- **调用示例：** `/vip/timemachine`

---

## 云贝相关

### 云贝签到信息

- **接口地址：** `/yunbei`
- **说明：** 登录后调用此接口，可获取云贝签到信息
- **调用示例：** `/yunbei`

### 云贝签到

- **接口地址：** `/yunbei/sign`
- **调用示例：** `/yunbei/sign`

### 云贝所有任务

- **接口地址：** `/yunbei/tasks`
- **调用示例：** `/yunbei/tasks`

### 云贝 todo 任务

- **接口地址：** `/yunbei/tasks/todo`
- **调用示例：** `/yunbei/tasks/todo`

### 云贝完成任务

- **接口地址：** `/yunbei/task/finish`
- **必选参数：** `userTaskId` (任务 id)
- **可选参数：** `depositCode` (任务 depositCode)
- **调用示例：** `/yunbei/task/finish?userTaskId=123`

### 云贝今日签到信息

- **接口地址：** `/yunbei/today`
- **调用示例：** `/yunbei/today`

### 云贝收入

- **接口地址：** `/yunbei/receipt`
- **可选参数：** `limit` (默认10)，`offset`
- **说明：** 登录后调用此接口可获取云贝收入记录
- **调用示例：** `/yunbei/receipt`

### 云贝支出

- **接口地址：** `/yunbei/expense`
- **可选参数：** `limit` (默认10)，`offset`
- **说明：** 登录后调用此接口可获取云贝支出记录
- **调用示例：** `/yunbei/expense`

### 云贝账户信息

- **接口地址：** `/yunbei/info`
- **调用示例：** `/yunbei/info`

### 云贝推歌

- **接口地址：** `/yunbei/rcmd/song`
- **必选参数：** `id` (歌曲 id)
- **可选参数：** `reason` (推歌理由)，`yunbeiNum` (云贝数量,默认10)
- **调用示例：** `/yunbei/rcmd/song?id=65528`

### 云贝推歌历史记录

- **接口地址：** `/yunbei/rcmd/song/history`
- **可选参数：** `size` (默认20)，`cursor`
- **调用示例：** `/yunbei/rcmd/song/history?size=10`

---

## 节目/声音相关

### 节目推荐

- **接口地址：** `/program/recommend`
- **可选参数：** `limit` (默认10)，`offset`
- **调用示例：** `/program/recommend`

### 最近播放-歌曲

- **接口地址：** `/record/recent/song`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/record/recent/song?limit=1`

### 最近播放-视频

- **接口地址：** `/record/recent/video`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/record/recent/video?limit=1`

### 最近播放-声音

- **接口地址：** `/record/recent/voice`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/record/recent/voice?limit=1`

### 最近播放-歌单

- **接口地址：** `/record/recent/playlist`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/record/recent/playlist?limit=1`

### 最近播放-专辑

- **接口地址：** `/record/recent/album`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/record/recent/album?limit=1`

### 最近播放-播客

- **接口地址：** `/record/recent/dj`
- **可选参数：** `limit` (默认100)
- **调用示例：** `/record/recent/dj?limit=1`

---

## 一起听相关

### 创建房间

- **接口地址：** `/listentogether/room/create`
- **说明：** 登录后调用此接口可创建一起听房间
- **调用示例：** `/listentogether/room/create`

### 房间情况

- **接口地址：** `/listentogether/room/check`
- **必选参数：** `roomId` (房间 id)
- **说明：** 检查房间情况
- **调用示例：** `/listentogether/room/check?roomId=xxx`

### 一起听状态

- **接口地址：** `/listentogether/status`
- **说明：** 获取一起听状态
- **调用示例：** `/listentogether/status`

### 发送播放状态

- **接口地址：** `/listentogether/play/command`
- **必选参数：** `roomId`，`commandType`，`playStatus`
- **可选参数：** `progress`，`formerSongId`，`targetSongId`，`clientSeq`
- **调用示例：** `/listentogether/play/command?roomId=xxx&commandType=play&playStatus=1`

### 发送心跳

- **接口地址：** `/listentogether/heatbeat`
- **必选参数：** `roomId`，`songId`，`playStatus`，`progress`
- **调用示例：** `/listentogether/heatbeat?roomId=xxx&songId=123&playStatus=1&progress=30`

### 更新播放列表

- **接口地址：** `/listentogether/sync/list/command`
- **必选参数：** `roomId`，`commandType`，`userId`，`version`，`randomList`，`displayList`
- **调用示例：** `/listentogether/sync/list/command?roomId=xxx&commandType=update&userId=123&version=1&randomList=1,2,3&displayList=1,2,3`

### 当前列表获取

- **接口地址：** `/listentogether/sync/playlist/get`
- **必选参数：** `roomId`
- **调用示例：** `/listentogether/sync/playlist/get?roomId=xxx`

### 结束房间

- **接口地址：** `/listentogether/end`
- **必选参数：** `roomId`
- **调用示例：** `/listentogether/end?roomId=xxx`

---

## 曲风相关

### 曲风列表

- **接口地址：** `/style/list`
- **说明：** 获取曲风列表及其对应的 `tagId`
- **调用示例：** `/style/list`

### 曲风偏好

- **接口地址：** `/style/preference`
- **说明：** 登录后调用此接口获取我的曲风偏好
- **调用示例：** `/style/preference`

### 曲风详情

- **接口地址：** `/style/detail`
- **必选参数：** `tagId` (曲风 ID)
- **说明：** 获取该曲风的描述信息
- **调用示例：** `/style/detail?tagId=1000`

### 曲风-歌曲

- **接口地址：** `/style/song`
- **必选参数：** `tagId` (曲风 ID)
- **可选参数：** `size` (默认20)，`cursor`，`sort` (0热度,1时间)
- **调用示例：** `/style/song?tagId=1000`

### 曲风-专辑

- **接口地址：** `/style/album`
- **必选参数：** `tagId` (曲风 ID)
- **可选参数：** `size` (默认20)，`cursor`，`sort` (0热度,1时间)
- **调用示例：** `/style/album?tagId=1000`

### 曲风-歌单

- **接口地址：** `/style/playlist`
- **必选参数：** `tagId` (曲风 ID)
- **可选参数：** `size` (默认20)，`cursor`
- **调用示例：** `/style/playlist?tagId=1000`

### 曲风-歌手

- **接口地址：** `/style/artist`
- **必选参数：** `tagId` (曲风 ID)
- **可选参数：** `size` (默认20)，`cursor`
- **调用示例：** `/style/artist?tagId=1000`

---

## 消息/私信相关

### 通知-私信

- **接口地址：** `/msg/private`
- **可选参数：** `limit` (默认30)，`offset`，`total` (true/false)
- **调用示例：** `/msg/private?limit=10`

### 私信内容

- **接口地址：** `/msg/private/history`
- **必选参数：** `uid` (用户 id)
- **可选参数：** `limit` (默认30)，`before` (分页参数)
- **调用示例：** `/msg/private/history?uid=9003`

### 通知-评论

- **接口地址：** `/msg/comments`
- **可选参数：** `limit` (默认30)，`before`
- **调用示例：** `/msg/comments?limit=10`

### 通知-@我

- **接口地址：** `/msg/forwards`
- **可选参数：** `limit` (默认30)，`before`
- **调用示例：** `/msg/forwards?limit=10`

### 通知-通知

- **接口地址：** `/msg/notices`
- **可选参数：** `limit` (默认30)，`before`
- **调用示例：** `/msg/notices?limit=10`

### 最近联系人

- **接口地址：** `/msg/recentcontact`
- **说明：** 登录后调用此接口，可获取最近联系人
- **调用示例：** `/msg/recentcontact`

### 发送私信

- **接口地址：** `/send/text`
- **必选参数：** `user_ids` (用户 id,支持多个用逗号隔开)，`msg` (私信内容)
- **调用示例：** `/send/text?user_ids=32953014&msg=test`

### 发送私信（带歌曲）

- **接口地址：** `/send/song`
- **必选参数：** `user_ids` (用户 id)，`id` (歌曲 id)
- **可选参数：** `msg` (信息)
- **调用示例：** `/send/song?user_ids=32953014&id=347230`

### 发送私信（带专辑）

- **接口地址：** `/send/album`
- **必选参数：** `user_ids` (用户 id)，`id` (专辑 id)
- **可选参数：** `msg`
- **调用示例：** `/send/album?user_ids=32953014&id=351318&msg=测试`

### 发送私信（带歌单）

- **接口地址：** `/send/playlist`
- **必选参数：** `user_ids` (用户 id)，`playlist` (歌单 id)
- **可选参数：** `msg`
- **调用示例：** `/send/playlist?msg=test&user_ids=475625142&playlist=705123491`

---

## 其他

### 国家编码列表

- **接口地址：** `/countries/code/list`
- **调用示例：** `/countries/code/list`

### 热门话题

- **接口地址：** `/hot/topic`
- **可选参数：** `limit` (默认20)，`offset`
- **调用示例：** `/hot/topic?limit=30&offset=30`

### 话题详情

- **接口地址：** `/topic/detail`
- **必选参数：** `actid` (话题 id)
- **调用示例：** `/topic/detail?actid=111551188`

### 话题详情热门动态

- **接口地址：** `/topic/detail/event/hot`
- **必选参数：** `actid` (话题 id)
- **调用示例：** `/topic/detail/event/hot?actid=111551188`

### 收藏的专栏

- **接口地址：** `/topic/sublist`
- **可选参数：** `limit` (默认50)，`offset`
- **调用示例：** `/topic/sublist?limit=2&offset=1`

### 设置

- **接口地址：** `/setting`
- **说明：** 登录后调用此接口，可获取用户设置
- **调用示例：** `/setting`

### 音乐日历

- **接口地址：** `/calendar`
- **必选参数：** `startTime` (开始时间戳,毫秒)，`endTime` (结束时间戳,毫秒)
- **调用示例：** `/calendar?startTime=1609430400000&endTime=1640966399999`

### batch 批量请求接口

- **接口地址：** `/batch`
- **说明：** 登录后调用此接口，传入接口和对应原始参数，可批量请求接口
- **调用示例：** GET: `/batch?/api/v2/banner/get={"clientType":"pc"}`
- **调用示例：** POST: `{ "/api/v2/banner/get": {"clientType":"pc"} }`

### 内部版本接口

- **接口地址：** `/inner/version`
- **说明：** 获取内部版本号（从 package.json 读取）
- **调用示例：** `/inner/version`

### 操作记录

- **接口地址：** `/weblog`
- **说明：** 登录后调用此接口，可上传操作记录
- **调用示例：** 参考源码

### 资源点赞（MV、电台、视频）

- **接口地址：** `/resource/like`
- **必选参数：** `type` (0:歌曲,1:mv,2:歌单,3:专辑,4:电台节目,5:视频,6:动态,7:电台)，`t` (1点赞,其他取消)，`id` (资源 id)
- **调用示例：** `/resource/like?t=1&type=1&id=5436712`
- **注意：** 动态点赞需传 `threadId`，不传 `id`

### 获取视频标签列表

- **接口地址：** `/video/category/list`
- **调用示例：** `/video/category/list`

### 全部 MV

- **接口地址：** `/mv/all`
- **可选参数：** `area`，`type`，`order` (上升最快/最热/最新)，`limit` (默认30)，`offset`
- **调用示例：** `/mv/all?area=&limit=10`

---

## 附录：完整路由索引

| # | 路由 | 说明 |
|---|------|------|
| 1 | `/activate/init/profile` | 初始化昵称 |
| 2 | `/album` | 获取专辑内容 |
| 3 | `/album/detail` | 专辑详情 |
| 4 | `/album/detail/dynamic` | 专辑动态信息 |
| 5 | `/album/list` | 数字专辑-新碟上架 |
| 6 | `/album/list/style` | 数字专辑-语种风格馆 |
| 7 | `/album/new` | 全部新碟 |
| 8 | `/album/newest` | 最新专辑 |
| 9 | `/album/songsaleboard` | 数字专辑&数字单曲-榜单 |
| 10 | `/album/sub` | 收藏/取消收藏专辑 |
| 11 | `/album/sublist` | 已收藏专辑列表 |
| 12 | `/artists` | 获取歌手单曲 |
| 13 | `/artist/album` | 获取歌手专辑 |
| 14 | `/artist/desc` | 获取歌手描述 |
| 15 | `/artist/detail` | 获取歌手详情 |
| 16 | `/artist/fans` | 歌手粉丝 |
| 17 | `/artist/follow/count` | 歌手粉丝数量 |
| 18 | `/artist/list` | 歌手分类列表 |
| 19 | `/artist/mv` | 获取歌手 MV |
| 20 | `/artist/new/mv` | 关注歌手新 MV |
| 21 | `/artist/new/song` | 关注歌手新歌 |
| 22 | `/artist/songs` | 歌手全部歌曲 |
| 23 | `/artist/sub` | 收藏/取消收藏歌手 |
| 24 | `/artist/sublist` | 收藏的歌手列表 |
| 25 | `/artist/top/song` | 歌手热门 50 首歌曲 |
| 26 | `/artist/video` | 获取歌手视频 |
| 27 | `/audio/match` | 音频匹配 |
| 28 | `/avatar/upload` | 更新头像 |
| 29 | `/banner` | banner(轮播图) |
| 30 | `/batch` | 批量请求接口 |
| 31 | `/calendar` | 音乐日历 |
| 32 | `/captcha/sent` | 发送验证码 |
| 33 | `/captcha/verify` | 验证验证码 |
| 34 | `/cellphone/existence/check` | 检测手机是否已注册 |
| 35 | `/check/music` | 音乐是否可用 |
| 36 | `/cloud` | 云盘上传 |
| 37 | `/cloud/match` | 云盘歌曲匹配纠正 |
| 38 | `/cloudsearch` | 搜索(更全) |
| 39 | `/comment` | 发送/删除评论 |
| 40 | `/comment/album` | 专辑评论 |
| 41 | `/comment/dj` | 电台节目评论 |
| 42 | `/comment/event` | 动态评论 |
| 43 | `/comment/floor` | 楼层评论 |
| 44 | `/comment/hot` | 热门评论 |
| 45 | `/comment/hug/list` | 评论抱一抱列表 |
| 46 | `/comment/like` | 给评论点赞 |
| 47 | `/comment/music` | 歌曲评论 |
| 48 | `/comment/mv` | MV 评论 |
| 49 | `/comment/new` | 新版评论接口 |
| 50 | `/comment/playlist` | 歌单评论 |
| 51 | `/comment/video` | 视频评论 |
| 52 | `/countries/code/list` | 国家编码列表 |
| 53 | `/daily_signin` | 每日签到 |
| 54 | `/digitalAlbum/detail` | 数字专辑详情 |
| 55 | `/digitalAlbum/ordering` | 购买数字专辑 |
| 56 | `/digitalAlbum/purchased` | 我的数字专辑 |
| 57 | `/digitalAlbum/sales` | 数字专辑销量 |
| 58 | `/dj/banner` | 电台 banner |
| 59 | `/dj/category/excludehot` | 电台-非热门类型 |
| 60 | `/dj/category/recommend` | 电台-推荐类型 |
| 61 | `/dj/catelist` | 电台分类列表 |
| 62 | `/dj/detail` | 电台详情 |
| 63 | `/dj/hot` | 热门电台 |
| 64 | `/dj/paygift` | 电台付费精品 |
| 65 | `/dj/personalize/recommend` | 电台个性推荐 |
| 66 | `/dj/program` | 电台节目 |
| 67 | `/dj/program/detail` | 电台节目详情 |
| 68 | `/dj/program/toplist` | 电台-热门节目 |
| 69 | `/dj/program/toplist/hours` | 电台 24 小时节目榜 |
| 70 | `/dj/radio/hot` | 类别热门电台 |
| 71 | `/dj/recommend` | 电台-推荐 |
| 72 | `/dj/recommend/type` | 电台-分类推荐 |
| 73 | `/dj/sub` | 电台-订阅 |
| 74 | `/dj/sublist` | 电台-订阅列表 |
| 75 | `/dj/subscriber` | 电台订阅者列表 |
| 76 | `/dj/today/perfered` | 电台-今日优选 |
| 77 | `/dj/toplist` | 电台-节目榜 |
| 78 | `/dj/toplist/hours` | 电台 24 小时主播榜 |
| 79 | `/dj/toplist/newcomer` | 电台主播新人榜 |
| 80 | `/dj/toplist/pay` | 电台付费精品榜 |
| 81 | `/dj/toplist/popular` | 电台最热主播榜 |
| 82 | `/event` | 获取动态消息 |
| 83 | `/event/del` | 删除动态 |
| 84 | `/event/forward` | 转发动态 |
| 85 | `/fm_trash` | FM 垃圾桶 |
| 86 | `/follow` | 关注/取消关注用户 |
| 87 | `/history/recommend/songs` | 历史日推可用日期列表 |
| 88 | `/history/recommend/songs/detail` | 历史日推详细数据 |
| 89 | `/homepage/block/page` | 首页-发现 |
| 90 | `/homepage/dragon/ball` | 首页-发现-圆形图标入口 |
| 91 | `/hot/topic` | 热门话题 |
| 92 | `/hug/comment` | 抱一抱评论 |
| 93 | `/inner/version` | 内部版本号 |
| 94 | `/like` | 喜欢音乐 |
| 95 | `/likelist` | 喜欢音乐列表 |
| 96 | `/listentogether/end` | 结束一起听房间 |
| 97 | `/listentogether/heatbeat` | 一起听心跳 |
| 98 | `/listentogether/play/command` | 一起听播放命令 |
| 99 | `/listentogether/room/check` | 一起听房间检查 |
| 100 | `/listentogether/room/create` | 一起听创建房间 |
| 101 | `/listentogether/status` | 一起听状态 |
| 102 | `/listentogether/sync/list/command` | 一起听同步列表 |
| 103 | `/listentogether/sync/playlist/get` | 一起听当前列表获取 |
| 104 | `/login` | 邮箱登录 |
| 105 | `/login/cellphone` | 手机登录 |
| 106 | `/login/qr/check` | 二维码检测扫码状态 |
| 107 | `/login/qr/create` | 二维码生成 |
| 108 | `/login/qr/key` | 二维码 key 生成 |
| 109 | `/login/refresh` | 刷新登录 |
| 110 | `/login/status` | 登录状态 |
| 111 | `/logout` | 退出登录 |
| 112 | `/lyric` | 获取歌词 |
| 113 | `/lyric/new` | 获取逐字歌词 |
| 114 | `/mlog/music/rcmd` | 歌曲相关视频 |
| 115 | `/mlog/to/video` | mlog id 转视频 id |
| 116 | `/mlog/url` | 获取 mlog 播放地址 |
| 117 | `/msg/comments` | 通知-评论 |
| 118 | `/msg/forwards` | 通知-@我 |
| 119 | `/msg/notices` | 通知-通知 |
| 120 | `/msg/private` | 通知-私信 |
| 121 | `/msg/private/history` | 私信内容 |
| 122 | `/msg/recentcontact` | 最近联系人 |
| 123 | `/musician/cloudbean` | 账号云豆数 |
| 124 | `/musician/cloudbean/obtain` | 领取云豆 |
| 125 | `/musician/data/overview` | 音乐人数据概况 |
| 126 | `/musician/play/trend` | 音乐人播放趋势 |
| 127 | `/musician/sign` | 音乐人签到 |
| 128 | `/musician/tasks` | 音乐人任务 |
| 129 | `/musician/tasks/new` | 音乐人任务(新) |
| 130 | `/mv/all` | 全部 MV |
| 131 | `/mv/detail` | MV 详情 |
| 132 | `/mv/detail/info` | MV 点赞转发评论数 |
| 133 | `/mv/exclusive/rcmd` | 网易出品 MV |
| 134 | `/mv/first` | 最新 MV |
| 135 | `/mv/sub` | 收藏/取消收藏 MV |
| 136 | `/mv/sublist` | 收藏的 MV 列表 |
| 137 | `/mv/url` | MV 播放地址 |
| 138 | `/nickname/check` | 重复昵称检测 |
| 139 | `/personal_fm` | 私人 FM |
| 140 | `/personalized` | 推荐歌单 |
| 141 | `/personalized/djprogram` | 推荐电台 |
| 142 | `/personalized/mv` | 推荐 MV |
| 143 | `/personalized/newsong` | 推荐新音乐 |
| 144 | `/personalized/privatecontent` | 独家放送 |
| 145 | `/personalized/privatecontent/list` | 独家放送列表 |
| 146 | `/pl/count` | 私信和通知数量 |
| 147 | `/playmode/intelligence/list` | 心动模式/智能播放 |
| 148 | `/playlist/catlist` | 歌单分类 |
| 149 | `/playlist/cover/update` | 歌单封面上传 |
| 150 | `/playlist/create` | 新建歌单 |
| 151 | `/playlist/delete` | 删除歌单 |
| 152 | `/playlist/desc/update` | 更新歌单描述 |
| 153 | `/playlist/detail` | 歌单详情 |
| 154 | `/playlist/detail/dynamic` | 歌单详情动态 |
| 155 | `/playlist/highquality/tags` | 精品歌单标签列表 |
| 156 | `/playlist/hot` | 热门歌单分类 |
| 157 | `/playlist/mylike` | 点赞过的视频 |
| 158 | `/playlist/name/update` | 更新歌单名 |
| 159 | `/playlist/order/update` | 调整歌单顺序 |
| 160 | `/playlist/privacy` | 公开隐私歌单 |
| 161 | `/playlist/subscribe` | 收藏/取消收藏歌单 |
| 162 | `/playlist/subscribers` | 歌单收藏者 |
| 163 | `/playlist/tags/update` | 更新歌单标签 |
| 164 | `/playlist/track/add` | 收藏视频到视频歌单 |
| 165 | `/playlist/track/all` | 获取歌单所有歌曲 |
| 166 | `/playlist/track/delete` | 删除视频歌单里的视频 |
| 167 | `/playlist/tracks` | 对歌单添加或删除歌曲 |
| 168 | `/playlist/update` | 更新歌单 |
| 169 | `/playlist/update/playcount` | 歌单更新播放量 |
| 170 | `/playlist/video/recent` | 最近播放的视频 |
| 171 | `/program/recommend` | 节目推荐 |
| 172 | `/rebind` | 更换绑定手机 |
| 173 | `/recommend/resource` | 每日推荐歌单 |
| 174 | `/recommend/songs` | 每日推荐歌曲 |
| 175 | `/record/recent/album` | 最近播放-专辑 |
| 176 | `/record/recent/dj` | 最近播放-播客 |
| 177 | `/record/recent/playlist` | 最近播放-歌单 |
| 178 | `/record/recent/song` | 最近播放-歌曲 |
| 179 | `/record/recent/video` | 最近播放-视频 |
| 180 | `/record/recent/voice` | 最近播放-声音 |
| 181 | `/register/anonimous` | 游客登录 |
| 182 | `/register/cellphone` | 注册/修改密码 |
| 183 | `/related/allvideo` | 相关视频 |
| 184 | `/related/playlist` | 相关歌单推荐 |
| 185 | `/resource/like` | 资源点赞 |
| 186 | `/scrobble` | 听歌打卡 |
| 187 | `/search` | 搜索 |
| 188 | `/search/default` | 默认搜索关键词 |
| 189 | `/search/hot` | 热搜列表(简略) |
| 190 | `/search/hot/detail` | 热搜列表(详细) |
| 191 | `/search/multimatch` | 搜索多重匹配 |
| 192 | `/search/suggest` | 搜索建议 |
| 193 | `/send/album` | 发送私信(带专辑) |
| 194 | `/send/playlist` | 发送私信(带歌单) |
| 195 | `/send/song` | 发送私信(带歌曲) |
| 196 | `/send/text` | 发送私信 |
| 197 | `/setting` | 设置 |
| 198 | `/share/resource` | 分享到动态 |
| 199 | `/sheet/list` | 乐谱列表 |
| 200 | `/sheet/preview` | 乐谱内容 |
| 201 | `/sign/happy/info` | 乐签信息 |
| 202 | `/signin/progress` | 签到进度 |
| 203 | `/simi/artist` | 获取相似歌手 |
| 204 | `/simi/mv` | 相似 MV |
| 205 | `/simi/playlist` | 相似歌单 |
| 206 | `/simi/song` | 相似歌曲 |
| 207 | `/simi/user` | 最近听了这首歌的用户 |
| 208 | `/song/detail` | 歌曲详情 |
| 209 | `/song/download/url` | 客户端歌曲下载 URL |
| 210 | `/song/order/update` | 调整歌曲顺序 |
| 211 | `/song/purchased` | 已购单曲 |
| 212 | `/song/url` | 获取音乐 URL |
| 213 | `/song/url/v1` | 获取音乐 URL(新版) |
| 214 | `/song/wiki/summary` | 音乐百科简要信息 |
| 215 | `/style/album` | 曲风-专辑 |
| 216 | `/style/artist` | 曲风-歌手 |
| 217 | `/style/detail` | 曲风详情 |
| 218 | `/style/list` | 曲风列表 |
| 219 | `/style/playlist` | 曲风-歌单 |
| 220 | `/style/preference` | 曲风偏好 |
| 221 | `/style/song` | 曲风-歌曲 |
| 222 | `/top/album` | 新碟上架(分类) |
| 223 | `/top/artists` | 热门歌手 |
| 224 | `/top/list` | 排行榜 |
| 225 | `/top/mv` | MV 排行 |
| 226 | `/top/playlist` | 歌单(网友精选碟) |
| 227 | `/top/playlist/highquality` | 获取精品歌单 |
| 228 | `/top/song` | 新歌速递 |
| 229 | `/topic/detail` | 话题详情 |
| 230 | `/topic/detail/event/hot` | 话题详情热门动态 |
| 231 | `/topic/sublist` | 收藏的专栏 |
| 232 | `/toplist` | 所有榜单 |
| 233 | `/toplist/artist` | 歌手榜 |
| 234 | `/toplist/detail` | 所有榜单内容摘要 |
| 235 | `/user/account` | 账号信息 |
| 236 | `/user/audio` | 用户音频 |
| 237 | `/user/binding` | 用户绑定信息 |
| 238 | `/user/bindingcellphone` | 绑定手机 |
| 239 | `/user/cloud` | 云盘数据 |
| 240 | `/user/cloud/del` | 云盘歌曲删除 |
| 241 | `/user/cloud/detail` | 云盘数据详情 |
| 242 | `/user/comment/history` | 用户历史评论 |
| 243 | `/user/detail` | 用户详情 |
| 244 | `/user/dj` | 用户电台 |
| 245 | `/user/event` | 用户动态 |
| 246 | `/user/followeds` | 用户粉丝列表 |
| 247 | `/user/follows` | 用户关注列表 |
| 248 | `/user/level` | 用户等级信息 |
| 249 | `/user/playlist` | 用户歌单 |
| 250 | `/user/record` | 用户播放记录 |
| 251 | `/user/replacephone` | 更换绑定手机(旧) |
| 252 | `/user/subcount` | 用户信息/歌单/收藏数量 |
| 253 | `/user/update` | 更新用户信息 |
| 254 | `/video/category/list` | 视频分类列表 |
| 255 | `/video/detail` | 视频详情 |
| 256 | `/video/detail/info` | 视频点赞转发评论数 |
| 257 | `/video/group` | 视频标签下的视频 |
| 258 | `/video/group/list` | 视频标签列表 |
| 259 | `/video/sub` | 收藏视频 |
| 260 | `/video/timeline/all` | 全部视频列表 |
| 261 | `/video/timeline/recommend` | 推荐视频 |
| 262 | `/video/url` | 视频播放地址 |
| 263 | `/vip/growthpoint` | VIP 成长值 |
| 264 | `/vip/growthpoint/details` | VIP 成长值获取记录 |
| 265 | `/vip/growthpoint/get` | 领取 VIP 成长值 |
| 266 | `/vip/info` | VIP 信息 |
| 267 | `/vip/tasks` | VIP 任务 |
| 268 | `/vip/timemachine` | 黑胶时光机 |
| 269 | `/weblog` | 操作记录 |
| 270 | `/yunbei` | 云贝签到信息 |
| 271 | `/yunbei/expense` | 云贝支出 |
| 272 | `/yunbei/info` | 云贝账户信息 |
| 273 | `/yunbei/rcmd/song` | 云贝推歌 |
| 274 | `/yunbei/rcmd/song/history` | 云贝推歌历史记录 |
| 275 | `/yunbei/receipt` | 云贝收入 |
| 276 | `/yunbei/sign` | 云贝签到 |
| 277 | `/yunbei/task/finish` | 云贝完成任务 |
| 278 | `/yunbei/tasks` | 云贝所有任务 |
| 279 | `/yunbei/tasks/todo` | 云贝 todo 任务 |
| 280 | `/yunbei/today` | 云贝今日签到信息 |

---

## 附录：路由生成规则

项目通过扫描 `module/` 目录下的 JS 文件自动生成路由。

- 文件名去掉 `.js` 后缀
- 将 `_`（下划线）替换为 `/`（斜杠）
- 例如：`album_new.js` → `/album/new`

**特殊路由例外：**

| 文件名 | 特殊路由 |
|--------|---------|
| `daily_signin.js` | `/daily_signin` |
| `fm_trash.js` | `/fm_trash` |
| `personal_fm.js` | `/personal_fm` |

---

## Node.js 调用方式

> v3.31.0 后支持 Node.js 调用

```js
const { login_cellphone, user_cloud } = require('NeteaseCloudMusicApi')
async function main() {
  try {
    const result = await login_cellphone({
      phone: '手机号',
      password: '密码',
    })
    console.log(result)
    const result2 = await user_cloud({
      cookie: result.body.cookie, // 凭证
    })
    console.log(result2.body)
  } catch (error) {
    console.log(error)
  }
}
main()
```

## TypeScript 支持

```ts
import { banner } from 'NeteaseCloudMusicApi'
banner({ type: 0 }).then((res) => {
  console.log(res)
})
```

---

## License

[MIT](https://github.com/Binaryify/NeteaseCloudMusicApi/blob/master/LICENSE)
