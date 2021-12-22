# 摸鱼派 API 文档

## 注意事项
- 请一定要在请求时带上UA，推荐使用Chrome的UA，空UA将返回500状态码
- 对单个接口的访问频率必须控制在最低1次/30秒，否则IP可能进入小黑屋（WebSocket、发送消息接口除外）

## 鉴权
摸鱼派社区 API 引入了  `apiKey` 的概念，对 API 的请求不需要提供 Cookie，只需要在参数中带上申请的 `apiKey` 即可。

> 注意：凡是 POST 请求，请求体必须是 JSON 格式，例如：`{ "nameOrEmail": "","userPassword": "" }`

### 获取 apiKey

`POST` `/api/getKey`  

用于 API 获取摸鱼派的通用密钥，`Key` 即身份，`Key` 长期有效，如果服务器重启，则需要重新获取，建议配合 `/api/user` 接口定期检测 `Key` 是否有效。

请求:

| Key | 说明 | 示例 |
| --- | --- | --- |
|nameOrEmail|用户名或邮箱地址| username |
|userPassword|使用 MD5 加密后的密码 <sup>*</sup>| e10adc3949ba59abbe56e057f20f883e |

请求示例：
```bash
curl --location --request POST 'https://pwl.icu/api/getKey' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36' \
--data-raw '{
    "nameOrEmail": "username",
    "userPassword": "e10adc3949ba59abbe56e057f20f883e"
}'
```

><sup>*</sup> 注意：这里不支持直接传递明文，必须是32位小写MD5加密后的密码

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
| Key |API 通用密钥，用于用户身份识别|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
| msg | 错误信息 | 密码错误 |
| code| 0 为请求成功，-1 失败| -1 |

### 查询用户信息
`GET` `/api/user?apiKey=<Key>`

通过指定的 API Key 查询用户信息（可以用来定期验证 API Key 是否有效）

请求:
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|

请求示例：
```bash
curl --location --request GET 'https://pwl.icu/api/user?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS'
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36' \
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为0则密钥有效，为-1则密钥无效|0|
|msg|错误信息|Invalid Api Key.|
|data<sup>*</sup>|对应用户详细信息|`{ ... }`|
|- oId|Id|1630512345670|
|- userNo | 用户序号 | 26 |
|- userName|用户名|imlinhanchao|
|- userRole | 用户组 | OP |
|- userNickname|昵称|我是跳跳吧|
|- userAvatarURL|头像地址|https://...|
|- userCity|最近所在城市，根据用户 IP 确定|深圳|
|- userOnlineFlag|是否在线|true|
|- onlineMinute| 在线时长，单位分钟|85467|
|- userPoint|积分|183939|
|- userAppRole|角色|0 = 黑客，1 = 画家|
|- userIntro| 签名|人生而自由，卻無往不在枷鎖之中。|
|- userURL | URL | https://... |
|- cardBg| 卡片背景| https://...|
|- followingUserCount|关注用户|5|
|- sysMetal| 徽章列表, JSON 字符串| `{ "list": [ { "data": "", "name": "", "description": "", "attr": "url=https://...&backcolor=xxxxxx&fontcolor=xxxxxx"} ]}` |

><sup>*</sup> 注意：若密钥无效，无 `data` 项目

## 通用

### 查询成员信息
`GET` `/user/<username>?apiKey=<Key>`

查询某个成员的信息

请求:
| Key | 说明 | 示例 |
| --- | --- | --- |
|username|用户名|taozhiyu|
|apiKey|通用密钥<sup>*</sup>|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|

><sup>*</sup> 选填，填写后 `canFollow` 返回值可以显示是否已经关注该用户

请求示例：
```bash
curl --location --request GET 'https://pwl.icu/user/taozhiyu?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS'\
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36' \
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|oId|Id|1630512345670|
|userNo | 用户序号 | 26 |
|userName|用户名|imlinhanchao|
|userRole | 用户组 | OP |
|userNickname|昵称|我是跳跳吧|
|userAvatarURL|头像地址|https://...|
|userCity|最近所在城市，根据用户 IP 确定|深圳|
|userOnlineFlag|是否在线|true|
|onlineMinute| 在线时长，单位分钟|85467|
|userPoint|积分|183939|
|canFollow|是否可以关注|no/yes/hide|
|userAppRole|角色|0 = 黑客，1 = 画家|
|userIntro| 签名|人生而自由，卻無往不在枷鎖之中。|
|userURL | URL | https://... |
|cardBg| 卡片背景| https://...|
|followingUserCount|关注用户|5|
|sysMetal| 徽章列表, JSON 字符串| `{ "list": [ { "data": "", "name": "", "description": "", "attr": "url=https://...&backcolor=xxxxxx&fontcolor=xxxxxx"} ]}` |

### 用户名联想
`POST /users/names`

通过现有的字符串推断完整的用户名（列表）

请求:
| Key | 说明 | 示例 |
| --- | --- | --- |
|name|不完整的用户名|ad|

请求示例：
```bash
curl --location --request POST 'https://pwl.icu/users/names' \
--header 'Content-Type: application/json' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36' \
--data-raw '{
    "name": "ad"
}'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为0则密钥有效，为-1则密钥无效|0|
|msg|错误信息||
|data|用户列表|`[ ... ]`|
|- userAvatarURL| 头像 | https://... |
|- userName | 用户名 | adlered |

### 用户常用表情
`GET /users/emotions?apiKey=<Key>`

获取用户常用表情

请求:
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|

请求示例：
```bash
curl --location --request GET 'https://pwl.icu/users/emotions?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为0则密钥有效，为-1则密钥无效|0|
|msg|错误信息||
|data|表情列表|`[ ... ]`|
|- &lt;emoji name>| Key 为 emoji 代码，值为对应 emoji | "smile": "😄" |

### 获取活跃度
`GET /user/liveness?apiKey=<Key>`

获取用户活跃度
> **警告⚠️**:
> 本接口负载较大，请至少将请求间隔延长至30秒，建议每次循环取(30,60]秒的随机数，如间隔小于30秒，登录状态会被直接注销！

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|

请求示例：
```bash
curl --location --request GET 'https://pwl.icu/user/liveness?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|liveness|活跃度|80.20|

### 获取签到状态
`GET /user/checkedIn?apiKey=<Key>`

获取用户是否签到

请求:
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|

请求示例:
```bash
curl --location --request GET 'https://pwl.icu/user/checkedIn?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|checkedIn|是否签到|true|

### 领取昨日活跃奖励
`GET /activity/yesterday-liveness-reward-api?apiKey=<Key>`

领取昨日活跃奖励

请求示例:
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|

请求示例:
```bash
curl --location --request GET 'https://pwl.icu/activity/yesterday-liveness-reward-api?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|sum|领取到的积分，-1 表示已领取|300|

### 查询昨日奖励领取状态
`GET /api/activity/is-collected-liveness`

查询昨日活跃奖励是否已被领取

请求示例:
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|

请求示例:
```bash
curl --location --request GET 'https://pwl.icu/api/activity/is-collected-liveness?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|isCollectedYesterdayLivenessReward|true 表示已领取|true|

## 通知

### 通知计数
`GET /notifications/unread/count`

获取用户未阅读的通知计数

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|

请求示例:
```bash
curl --location --request GET 'https://pwl.icu/notifications/unread/count?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为 0 则密钥有效，为 -1 则密钥无效|0|
|userNotifyStatus|是否启用 Web 通知|0|
|unreadNotificationCnt|未读通知数|0|
|unreadReplyNotificationCnt|未读回复通知数|0|
|unreadPointNotificationCnt|未读积分通知数|0|
|unreadAtNotificationCnt|未读 @ 通知数|0|
|unreadBroadcastNotificationCnt|未读同城通知数|0|
|unreadSysAnnounceNotificationCnt|未读系统通知数|0|
|unreadNewFollowerNotificationCnt|未读关注者通知数|0|
|unreadFollowingNotificationCnt|未读关注通知数|0|
|unreadCommentedNotificationCnt|未读评论通知数|0|

### 通知详情
`GET /api/getNotifications?apiKey=<Key>&type=<type>[&page=<page>]`

获取详细通知列表

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
|type|要获取的通知类型|point|
|p|页数可选，默认为1|1|

通知类型：
| Type | 说明 |
| --- | --- |
|point|积分|
|commented|收到的回帖|
|reply|收到的回复|
|at|提及我的|
|following|我关注的|
|broadcast|同城|
|sys-announce|系统|

请求示例:
```bash
curl --location --request GET 'https://pwl.icu/api/getNotifications?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS&type=point' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

积分(point)通知响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为0则密钥有效，为-1则密钥无效|0|
|msg|错误信息||
|data|通知数据列表|`[ ... ]`|
|- hasRead| 是否已读 | true |
|- createTime | 创建时间 | Tue Dec 21 11:33:31 CST 2021 |
|- description| 通知描述，格式为 HTML | `<a href=\"https://pwl.icu/member/PickerFinsh\" class=\"name-at\" aria-label=\"PickerFinsh\">PickerFinsh</a>  已通过你的邀请链接注册，感谢你对社区的贡献 <font style=\"color: red;\">♥</font>` |

收到的回帖/回复(commented/reply)通知响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为0则密钥有效，为-1则密钥无效|0|
|msg|错误信息||
|data|通知数据列表|`[ ... ]`|
|- hasRead|是否已读|true,
|- commentAuthorName|回帖作者|Tocker",
|- commentAuthorThumbnailURL|回帖作者头像缩略图|https://pwl.stackoverflow.wiki/2021/12/blob-edc19d3f.png?imageView2/1/w/48/h/48/interlace/0/q/100",
|- commentCreateTime|回帖时间|Sun Dec 19 09:57:03 CST 2021",
|- commentSharpURL|回帖地址|/article/1637143985245?p=1&m=1#1639879022994",
|- commentContent|回帖内容，内容为 HTML|`<p>牛蛙牛蛙</p>`",
|- commentArticleType|回帖的文章类型|0,
|- commentArticleTitle|回帖文章标题|摸鱼派聊天室应用",
|- commentArticlePerfect|是否精选的文章|1

收到的 @ (at) 通知响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为0则密钥有效，为-1则密钥无效|0|
|msg|错误信息||
|data|通知数据列表|`[ ... ]`|
|- hasRead|是否已读|true,
|- userName|发起 @ 用户|imlinhanchao
|- userAvatarURL|发起 @ 用户头像|https://pwl.stackoverflow.wiki/2021/09/1502581-141c6da3.png",
|- deleted|是否已删除|false,
|- createTime"|创建时间|Mon Dec 20 10:00:56 CST 2021",
|- content|@ 的内容|@imlinhanchao 大佬一直在线 还可以看直播"

我关注的(following) 通知响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为0则密钥有效，为-1则密钥无效|0|
|msg|错误信息||
|data|通知数据列表|`[ ... ]`|
|- hasRead|是否已读|true|
|- articleTitle|文章标题|【社区维护日志】IP 黑名单列表|
|- isComment|是否评论|false|
|- articleTags|文章标签|摸鱼派,熊孩子|
|- url|文章地址|https://pwl.icu/article/1639719358591|
|- articleType|文章类型|0|
|- createTime|创建时间|Fri Dec 17 13:35:58 CST 2021|
|- authorName|文章作者|adlered|
|- articlePerfect|是否精选文章|0|
|- thumbnailURL|作者头像缩略图|https://pwl.stackoverflow.wiki/2021/09/1552202503861_1562141298909-67b099fd.jpeg?imageView2/1/w/48/h/48/interlace/0/q/100|
|- articleCommentCount|文章评论数|17|

系统(sys-announce)通知响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为0则密钥有效，为-1则密钥无效|0|
|msg|错误信息||
|data|通知数据列表|`[ ... ]`|
|- hasRead|是否已读|true|
|- createTime|创建日期|Mon Dec 13 11:43:38 CST 2021"|
|- description|通知描述|`<a href=\"https://pwl.icu/article/1639366979765\">【12.13 国家公祭日】南京大屠杀 - 勿忘历史，吾辈自强！</a>`"|

### 批量已读类型的通知
`GET /notifications/make-read/<type>?apiKey=<Key>`

将制定类型的通知标记为已读

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
|type|要已读的通知类型|point|

通知类型：
| Type | 说明 |
| --- | --- |
|point|积分|
|commented|收到的回帖|
|reply|收到的回复|
|at|提及我的|
|following|我关注的|
|broadcast|同城|
|sys-announce|系统|

请求示例:
```bash
curl --location --request GET 'https://pwl.icu/notifications/make-read/point?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

### 已读所有消息
`GET /notifications/all-read?apiKey=<Key>`

将全部通知标记为已读

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
|type|要已读的通知类型|point|

请求示例:
```bash
curl --location --request GET 'https://pwl.icu/notifications/all-read?apiKey=oXTQTD4ljryXoIxa1lySgEl6aObrIhSS' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

## 聊天室

### 连接聊天室
`WSS /chat-room-channel?apiKey=<Key>`

聊天室接收消息的WebSocket

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|

事件处理：
| Event | 说明 |
| --- | --- |
| open | 连接打开，定时每 3 分钟发送 `-hb-` |
| close | 连接关闭 |
| error | 连接出错，需重新连接|
| message | 收到消息，内容为 JSON 字符串，需自行序列号 |

消息结构：
|Key|所属类型|说明|示例|
| --- | --- | --- |--|
|type|all|消息类型<br>online(在线) / revoke(撤回) / msg(聊天) / redPacketStatus(红包领取)|msg|
| 在线消息 |
|onlineChatCnt|online|在线人数|35|
|users|online|在线用户列表|`[ ... ]`|
|- homePage| online | 用户首页| https://pwl.icu/member/... |
|- userAvatarURL| online | 用户头像 | https://... |
|- userName | online | 用户名 | adlered |
| 聊天消息 |
|oId|msg|消息 Id | 1640074796395 |
|time|msg|发布时间|2021-12-21 16:19:56|
|userName | msg | 用户名 | adlered |
|userNickname | msg | 昵称 | 陈辉 |
|userAvatarURL | msg | 用户头像 | https://... |
|sysMetal|msg | 徽章列表, JSON 字符串| `{ "list": [ { "data": "", "name": "", "description": "", "attr": "url=https://...&backcolor=xxxxxx&fontcolor=xxxxxx"} ]}` |
|content|msg |消息内容，HTML 格式，如果是红包，则是 JSON |`<p>+1</p>` 或<br> `{...}` |
|- msg|msg - redPacket|红包祝福语|摸鱼者，事竟成！|
|- recivers|msg - redPacket|红包接收者用户名，专属红包有效|[ ... ]
|- msgType|msg - redPacket|固定 redPacket | redPacket
|- money|msg - redPacket|红包积分|32|
|- count|msg - redPacket|红包个数|2|
|- type|msg - redPacket|红包类型|random|
|- got|msg - redPacket|已领取个数|1|
|- who|msg - redPacket|已领取者信息| `[ ... ]` |
| -- userName|msg - redPacket|用户名|dannio|
| -- avatar|msg - redPacket|用户头像|https://...|
| -- userMoney - redPacket|msg|领取到的积分|23|
| -- time|msg - redPacket|领取时间|2021-12-21 16:26:43|
|md|msg|消息内容，Markdown 格式，红包消息无此栏位|`![image.png](https://...)`
|撤回消息|
|oId|revoke|撤回消息的 Id|1640076642484|
| 红包领取消息 |
|oId|redPacketStatus|消息 Id|1640075201124|
|count|redPacketStatus|红包个数|2|
|got|redPacketStatus|已领取个数|1|
|whoGive|redPacketStatus|发送者用户名|adlered|
|whoGot|redPacketStatus|领取者用户名|hewei|

### 聊天历史消息
`GET /chat-room/more?apiKey=<Key>&page=<page>`

获取更多聊天信息
请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
|page|頁碼|1|

请求示例：
```bash
curl --location --request GET 'https://pwl.icu/chat-room/more?page=1&apiKey=5r1qeYe4tDx0No9uEpXA4rK2peczjZ40' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为 0 则密钥有效，为 -1 则密钥无效|0|
|msg | 错误信息 | |
| data | 消息列表| `[ ... ]` |
|- oId| 消息 Id | 1640074796395 |
|- time|发布时间|2021-12-21 16:19:56|
|- userName  | 用户名 | adlered |
|- userNickname | 昵称 | 陈辉 |
|- userAvatarURL  | 用户头像 | https://... |
|- sysMetal | 徽章列表, JSON 字符串| `{ "list": [ { "data": "", "name": "", "description": "", "attr": "url=https://...&backcolor=xxxxxx&fontcolor=xxxxxx"} ]}` |
|- content|消息内容，HTML 格式，如果是红包，则是 JSON |`<p>+1</p>` 或<br> `{...}` |
|-- msg|红包祝福语|摸鱼者，事竟成！|
|-- recivers|红包接收者用户名，专属红包有效|[ ... ]
|-- msgType|固定 redPacket | redPacket
|-- money|红包积分|32|
|-- count|红包个数|2|
|-- type|红包类型|random|
|-- got|已领取个数|1|
|-- who|已领取者信息| `[ ... ]` |
| --- userName|用户名|dannio|
| --- avatar|用户头像|https://...|
| --- userMoney|领取到的积分|23|
| --- time|领取时间|2021-12-21 16:26:43|



### 发送消息
`POST /chat-room/send`

发送聊天室消息

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
|content|消息正文（支持Markdown格式）。| ... 或 `[redpacket]{...}[/redpacket]`<sup>*</sup> |
| - msg| 红包祝福语 |摸鱼者，事竟成！|
| - money| 红包包含积分，如果是平分红包，则是单个红包积分| 32 |
| - count|红包个数| 2 |
| - recivers|接收者用户名列表，专属红包有效| `[ ... ]` | 
| - type|红包类型，random(拼手气红包), average(平分红包)，specify(专属红包)，heartbeat(心跳红包)|random|

> <sup>*</sup> 如果是发送红包，则需要发送 `[redpacket]{...}[/redpacket]`的格式

请求示例：
```bash
curl --location --request POST 'https://pwl.icu/chat-room/send' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36' \
--header 'Content-Type: application/json' \
--data-raw '{
    "apiKey": "oXTQTD4ljryXoIxa1lySgEl6aObrIhSS",
    "content": "..."
}'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为 0 则密钥有效，为 -1 则密钥无效|0|

### 撤回消息
`DELETE /chat-room/revoke/<oId>`

撤回聊天室消息（限制用户每24小时一次，管理员无限次）

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
|oId|消息 Id| 1640078407444 |

请求示例：
```bash
curl --location --request DELETE 'https://pwl.icu/chat-room/revoke/1640078407444' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36' \
--header 'Content-Type: application/json' \
--data-raw '{
    "apiKey": "oXTQTD4ljryXoIxa1lySgEl6aObrIhSS"
}'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|code|为 0 则密钥有效，为 -1 则密钥无效|0|
|msg|错误消息|撤回成功。|

### 打开红包
`POST /chat-room/red-packet/open`

拆开聊天室红包

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
|oId|消息 Id| 1640078407444 |

请求示例：
```bash
curl --location --request POST 'https://pwl.icu/chat-room/red-packet/open' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36' \
--header 'Content-Type: application/json' \
--data-raw '{
    "apiKey": "5r1qeYe4tDx0No9uEpXA4rK2peczjZ40",
    "oId": "1640075201124"
}'
```

响应：
| Key | 说明 | 示例 |
| --- | --- | --- |
|recivers|红包接收者用户名列表，专属红包有效|`[ ... ]`|
|who|已打开红包的用户|`[ ... ]`|
| - userName|用户名|dannio|
| - avatar|用户头像|https://...|
| - userMoney|领取到的积分|23|
| - time|领取时间|2021-12-21 16:26:43|
| info | 红包信息| `{ ... }` |
| - msg||玩的就是心跳！|
| - userName|发送者用户名|hewei|
| - userAvatarURL|发送者头像|https://...|
| - count|红包个数|2|
| - got|已领取个数|2|

## 图床

### 上传图片
`POST /upload`

上传图片或文件
> 限制大小：5M，  
> 限制文件格式：zip,rar,7z,tar,gzip,bz2,jar,jpg,jpeg,png,gif,webp,webm,bmp,mp3,mp4,wav,mov,weba  
> 指定MIME类型：multipart/form-data  

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|file[]|文件||



## 帖子

### 发贴
`POST /article`

发表帖子

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
|其它参数|自行发帖抓包|

### 更新贴子
`PUT /article/<id>`

更新帖子

请求：
| Key | 说明 | 示例 |
| --- | --- | --- |
| id | 帖子 Id | ... |
|apiKey|通用密钥|oXTQTD4ljryXoIxa1lySgEl6aObrIhSS|
|其它参数|自行发帖抓包|
