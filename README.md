# PADCHAT
padchat 提供非web的微信接口解决方案，需要authKey 请加微信联系 `mengjunjun001`

使用websocket发送请求，即可完成相关功能
## API

API 概述如下

| 接口               | code |
| -----------       | ---- |
| 建立初始连接        | 100  |
| 用户登录            | 101  |
| 退出登录            | 102  |
| 发送文字信息         | 103  |
| 发送APP或者连接信息   | 104  |
| 从群中踢出用户       | 105  |
| 获取好友信息和群信息  | 106  |
| 接受好友请求         | 107  |
| 邀请人入群           | 108  |
| 发送图片            | 109  |
| 批量建群            | 110  |
| 发送好友请求         | 111  |
| 获取好友或者群二维码  | 112  |

## 请求结构

### 1. 服务地址
地址： api.batorange.com:11001/ws

### 2. 通信协议
WebSocket 通信协议, 
* [websockets 官方github](https://github.com/websockets)     
* [NodeJs 参考](https://github.com/websockets/ws)

### 3. API请求结构
| **名称** | **描述**         | **备注**                     |
| ------- | ------------     | -------------------------- |
| API入口  | API调用的入口     | api.batorange.com:11001/ws |
| 公共参数  | 每个接口都包含的通用参数 | 详见 [公共参数](##PUBLIC_PARAMETER) 页面        |
| 指令名称  | code         | 完整的指令请参见 [API概览](#API)    |
| 指令参数  | 每个特定的指令需要的参数 | 详见每个指令的接口文档                |

## PUBLIC_PARAMETER
下面是公共参数部分 

| **名称**   | **类型** | **描述**             | **必选** |
| --------- | ------   | ----------------    | ------ |
| code      | number   | [API 类别](#API)             | 是      |
| authKey   | String   | 授权码，需要购买       | 是      |
| data      | Object   | 取决于是不同的API要求  |  否      |

一个典型的JSON请求如下，用来发送登陆请求
```json
{
    "code": 101,
    "authKey": "authKey",
    "data": {
        "account":"test"
    }
}
```
其中`authKey` 和 `code` 通用参数，`data` 详细见相关API说明.

## 返回值

通用返回值, 推送返回的消息，包含且不仅包含下面的字段。

| **名称**   | **类型**      | **描述**                  | **必选** |
| --------- | ------        | ----------------------- | ------ |
| MsgType   | Int           | 返回结果的类别码，详见[MsgType](#MsgType)。 | Yes    |
| Content   | String/Object | 返回内容               | No     |
| MsgId     | String        | 消息ID                    | Yes    |
| account   | String        | account                    | Yes    |

### MsgType

| **值** | **描述**      |
| ------| ------        | 
| -1    | 异常返回，一般为长连接服务器本身发送   | 
| 0     | 正常的消息返回，一般为登录的微信状态的推送，一般为长连接服务器本身发送 | 
| 1     | 微信收到文字消息的推送，一般为微信服务器发过来，直接转发出去 | 
| 2     | 好友信息推送，包含好友，群，公众号信息   | 
| 3     | 收到图片消息     | 
| 34    | 语音消息        | 
| 35    | 用户头像buf     | 
| 37    | 收到好友请求消息 | 
| 42    | 名片消息        | 
| 47    | 表情消息        | 
| 48    | 定位消息        | 
| 49    | APP消息(文件 或者 链接 H5) | 
| 62    | 小视频          |
| 2000  | 转账消息        | 
| 2001  | 收到红包消息     | 
| 3000  | 群邀请          | 
| 10000 | 微信群信息变更通知，多为群名修改，进群，离群信息，不包含群内聊天信息，一般为微信服务器发过来，直接转发出去  | 
| 10002 | 撤回消息        | 

## 具体接口说明

### 重连
#### 1. 接口描述
长连接断线重连后先发这个消息,会把已经登录的微信和这个长连接绑定.
#### 2. 输入参数

| **参数名称** | **必选** | **类型** | **描述**                            |
| -------- | ------ | ------ | ---------------------------------------- |
| code     | 是      | 100 |                                  |
| authKey  | 是      | string    | 					    	 |
| data     | 是      | {} |                                   |
#### 3. 输出参数
无
#### 4. 示例
* 输入
```json
{
  "code": 100,
  "authKey": "XXXXXXX",
  "data": {}
}
```

### 登录
#### 1. 接口描述
登录请求，获取能扫码登录的微信二维码
#### 2. 输入参数

| **参数名称** | **必选** | **类型** | **描述**                            |
| -------- | ------ | ------ | ---------------------------------------- |
| code     | 是      | 101 |                                  |
| authKey  | 是      | string    | 									 |
| data     | 是      | {"account": "123456"}  | account 自定义。需要唯一。 |

#### 3. 输出参数

| **参数名称** | **必选** | **类型** | **描述**                            |
| -------- | ------ | ------ | ---------------------------------------- |
| MsgType     | 是      | 0 |                                  |
| MsgId  | 是      | string    | 									 |
| Content  | 是      | Object    | 									 |
| account     | 是      | {"account": "123456"}  | account 自定义。需要唯一。 |
#### 4. 示例
* 输入
```json
{
    "code": 101,
    "authKey": "",
    "data": {
        "account":"213123"
    }
}
```
* 输出
```json
{
	"MsgType":0,
	"MsgId": 0,
	"account": "213123",
	"Content":"{\"wxId\":\"123456\",\"status\":1,\"msg\":\"等待扫码\",\"data\":\"此处为登录二维码的字节字符串，为64位编码的，可以直接显示图片"}"
}
```

### 退出登陆
#### 1. 接口描述
退出当前的微信号
#### 2. 输入参数
| **参数名称** | **必选** | **类型** | **描述**                            |
| -------- | ------ | ------ | ---------------------------------------- |
| code     | 是      | 102 |                                  |
| authKey  | 是      | string    | 									 |
| data     | 是      | {"account": "123456"}  | account 自定义。需要唯一。 |
#### 3. 输出参数
无
#### 4. 示例
* 输入
* 输出

### 发送文字消息
#### 1. 接口描述
#### 2. 输入参数
#### 3. 输出参数
#### 4. 示例
* 输入
* 输出

### 发送APP或者链接信息
#### 1. 接口描述
#### 2. 输入参数
#### 3. 输出参数
#### 4. 示例
* 输入
* 输出

### 群内踢人
#### 1. 接口描述
#### 2. 输入参数
#### 3. 输出参数
#### 4. 示例
* 输入
* 输出

### 获取群信息或者用户信息
#### 1. 接口描述
#### 2. 输入参数
#### 3. 输出参数
#### 4. 示例
* 输入
* 输出

### 接受好友请求
#### 1. 接口描述
#### 2. 输入参数
#### 3. 输出参数
#### 4. 示例
* 输入
* 输出

### 邀请进群命令
#### 1. 接口描述
#### 2. 输入参数
#### 3. 输出参数
#### 4. 示例
* 输入
* 输出

### 发送图片
#### 1. 接口描述
#### 2. 输入参数
#### 3. 输出参数
#### 4. 示例
* 输入
* 输出

### 发送好友请求
#### 1. 接口描述
#### 2. 输入参数
#### 3. 输出参数
#### 4. 示例
* 输入
* 输出

## 其他Response说明

## Request
请求Json格式
```
{
    "code": 101,            //命令号，必要   整形
    "authKey": "",          //授权码，必要
    "data": {}              //参数json
}
```

### 登录
```json
{
    "code": 101,
    "authKey": "",
    "data": {
        "account":"123456"
    }
}
```
account ：需要登录的ID，或者其它唯一标识

### 退出登录
```json
{
    "code": 102,
    "authKey": "",
    "data": {
        "account":"123456"
    }
}
```
account ：登录的微信ID，或者其它唯一标识

### 发送文字信息
```json
{
    "code": 103,
    "authKey": "",
    "data": {
        "account":"123456",
        "toUserName": "XXXXX",
        "content": ""
    }
}
```
 account ：登录的微信ID，或者其它唯一标识
 
 toUserName ： 发给谁，必须为目标微信ID或者群ID

 content ：内容

### 发送APP或者链接信息
```json
{
  "code": 104,
  "authKey": "qwert_12345_asdfg",
  "data": {
    "account":"juxiaoxiong",
    "toUserName":"XXXX",
    "title": "一张图看懂Tichome问问音箱2017新品发布会",
    "des": "只要你说，Hi, 小问。「你说，我做」",
    "url": "http://mp.weixin.qq.com/s?__biz=MjM5MTk4MzkyMA==&amp;mid=2247484875&amp;idx=1&amp;sn=52bb67911b950368d8b640cf0c837b1d&amp;chksm=a6ac759491dbfc8204191875608ecac7c7d0ceea4a37d0953c54b332bb262276cfc9f1508080&amp;mpshare=1&amp;scene=1&amp;srcid=0824iznZcv3IFj4f67yfuiNc#rd",
    "cdnthumburl": "304c020100044530430201000204071638eb02033d0af802046f30feb60204599ebeb90421353639343938393932364063686174726f6f6d3938355f313530333537353733370201000201000400",
    "cdnthumbmd5": "fee4c3e149ccc3404c5747024f137fcd",
    "cdnthumblength": "3855",
    "cdnthumbheight": "160",
    "cdnthumbaeskey": "1bd2890a9d5a450a9f07b91ee5d4efa8",
  }
}
```

 account ：登录的微信ID，或者其它唯一标识

 toUserName ： 发给谁，必须为目标微信ID或者群ID
 
 title： 标题
 
 des:描述
 
 url：链接打开的url
 
    cdnthumburl
	 
	 cdnthumbmd5
	 
	 cdnthumblength
	 
	 cdnthumbheight
	 
	 cdnthumbaeskey  以上为链接缩略图信息

### 群内踢人

```json
{
    "code": 105,
    "msgId":0,
    "authKey": "",
    "data": {
        "account":"",
        "userId":"wxid_tdax1huk5hgs12",
        "groupId":"XXXX@chatroom",
    }
}
```
 account ：登录的微信ID，或者其它唯一标识，必须为群主
 
 userId ： 需要移除的用户微信ID
 
 groupId：群微信ID

### 获取群信息或者用户信息
```json
{
    "code": 106,
	"msgId":0,
    "authKey": "XXXXX",
    "data": {
        "account":"juxiaoxiong",
        "wxIds":"7244722086@chatroom,wxid_ofgv2ps2lznx22,qq512436430"
    }
}
```
account：登录的微信ID，或者其它唯一标识

wxIds：用户的微信Id或者群Id，如需获取多个用户信息，多个微信ID用逗号隔开

### 接受好友请求
```json
{
    "code": 107,
	"msgId":0,
    "authKey": "XXXXX",
    "data": {
        "account":"juxiaoxiong",
        "entryName":"v1_a1732d9be37879d3746f71d7f6a39e950d9a3084bfa23955b560db364efc39d1@stranger",
        "ticket":"v2_3519b219e5848a1480896e3a8dc167d57afe3ba16e11eb62623af97114d6571c51450cb386bbebc537f6e66eefb3504dc463741bb3838f33241009112935ec8c@stranger",
    }
}
```
 account：登录的微信ID，或者其它唯一标识
 
 entryName：不知道是什么鬼，但是推送给你的好友请求信息里是这个字段，自己截取
 
 ticket：不知道是什么鬼，但是推送给你的好友请求信息里是这个字段，自己截取
 
 content：一般为验证信息，可能有其它的推送给你的好友请求信息里是这个字段，自己截取

### 邀请进群命令
```json
{
    "code": 108,
	"msgId":0,
    "authKey": "XXXXXX",
    "data": {
        "account":"juxiaoxiong",
        "groupId":"7028736757@chatroom",
        "wxId":"wxid_3xl8j2suau8b22",
    }
}
```
account：登录的微信ID，或者其它唯一标识

groupId：群微信ID

wxId： 邀请进群的用户的微信ID


### 重连
```json
{
  "code": 100,
  "authKey": "XXXXXXX",
  "data": {}
}
```
长连接断线重连后先发这个消息。我们会把已经登录的微信和这个长连接绑定

参数 无，基本消息格式就行

### code ：109 发送图片接口
图片精base64 编码发送
node 示例代码
```ts
const piBuff = fs.readFileSync('./test.jpg')
const sendPic = {
  "code": 109,
  "authKey": "XXXXXX",
  "data": {
    "account":"juxiaoxiong",
    "imgBuff": new Buffer(piBuff).toString('base64'),
    "toUserWxId": "qq512436430",
  }
}
```
 account：登录的微信ID，或者其它唯一标
 
 imgBuff：base64的字节字符串
 
 toUserWxId：目标的微信Id或者群ID  

### code: 111 发送好友请求
```json
{
    "code": 111,
    "msgId": 0,
    "authKey": "XXXXXXXXXX",
    "data": {
        "account":"juxiaoxiong",
        "userKey":"XXXXXXX",
        "content":"通过手机添加好友",
        "scene":15
    }
}
```

* userKey 需要添加的好友的微信号，手机号，或者QQ号
* content 验证信息
* scene 
    * 1 QQ添加好友 
    * 2 邮箱添加好友 
    * 3 微信号添加好友 
    * 15 手机号添加好友

## Response
返回或者推送消息格式
```json
{
    "MsgId": 101,
    "MsgType": 0,
    "account":123,
    "Content": ""
}
```
tip：推送或返回的消息，包含切不进包含以上三个字段。

### MsgType字段解释：
* -1                // 异常返回，一般为长连接服务器本身给你们发的
* 0                 // 正常的消息返回，一般为登录的微信状态的推送，一般为长连接服务器本身给你们发的
* 1                 // 微信收到文字消息的推送，一般为微信服务器发过来，我们直接转发给你们的
* 2                 // 好友信息推送，包含好友，群，公众号信息
* 3                 // 收到图片消息
* 34                // 语音消息
* 35                // 用户头像buf
* 37                // 收到好友请求消息
* 42                // 名片消息
* 47                // 表情消息
* 48                // 定位消息
* 49                // APP消息(文件 或者 链接 H5)
* 62                // 小视频
* 2000              // 转账消息
* 2001              // 收到红包消息
* 3000              // 群邀请
* 10000             // 微信通知信息，一般为微信服务器发过来，我们直接转发给你们的
                    // 微信群信息变更通知，多为群名修改，进群，离群信息，不包含群内聊天信息，一般为微信服务器发过来，我们直接转发给你们的
* 10002             // 撤回消息

#### MsgType === -1
```
{
    "MsgId": 0,
    "MsgType": -1,
    "Content": "授权码无效"
}
```
#### MsgType === 0 
```json
{
	"MsgType":0,
	"MsgId": 0,
	"account": "213123",
	"Content":"{\"wxId\":\"123456\",\"status\":1,\"msg\":\"等待扫码\",\"data\":\"此处为登录二维码的字节字符串，为64位编码的，可以直接显示图片"}"
}
```

#### MsgType === 1000
离群消息
```json
{ 
    "Status": 4,
    "CreateTime": 1504551177,
    "NewMsgId": 2820995283973267500,
    "ToUserName": "wxid_zbym1zg75qbm22",
    "MsgType": 10000,
    "ImgStatus": 1,
    "MsgId": 1648154699,
    "MsgSource": "",
    "PushContent": "",
    "Content": '你将"柚子"移出了群聊',
    "FromUserName": "8015951104@chatroom",
    "account": "xiaoju"
}
```

入群消息
```json
{ 
    "Status": 3,
    "CreateTime": 1504551216,
    "NewMsgId": 5448093555043905000,
    "ToUserName": "wxid_zbym1zg75qbm22",
    "MsgType": 10000,
    "ImgStatus": 1,
    "MsgId": 1648154704,
    "MsgSource": "",
    "PushContent": "",
    "Content": '你邀请"管理员"加入了群聊',
    "FromUserName": "8015951104@chatroom",
    "account": "xiaoju"
}
```

修改群名称
```json
{ 
    "Status": 4,
    "CreateTime": 1504551550,
    "NewMsgId": 7258670805403782000,
    "ToUserName": "wxid_zbym1zg75qbm22",
    "MsgType": 10000,
    "ImgStatus": 1,
    "MsgId": 1648154709,
    "MsgSource": "",
    "PushContent": "",
    "Content": '你修改群名为“新的群刚才”',
    "FromUserName": "8015951104@chatroom",
    "account": "xiaoju"
}
```
#### wxId 为当前账号登录账号wxId

#### Content里的status为当前微信的状态， 状态描述如下
* 1, "等待扫码,
* 3, "已扫码，已确认,
* 4, "正在登陆中
* 5, "检测超时（已过2分钟）
* 6, "登录失败
* 7, "已成功登录
* 8, "已退出登录

### MsgType=2 返回结果
#### 机器人自己
```json
{
    "UserName": "wxid_ofgv2ps2lznx22", 
    "Signature": "", 
    "Sex": 2, 
    "BindUin": 1873937731, 
    "MsgType": 101, 
    "City": "", 
    "NickName": "桔小熊", 
    "Province": "", 
    "BindEmail": "", 
    "account": "1503579784924", 
    "BindMobile": "18811591763"
}
```
#### 个人用户
UserName 不是 `wxid_`
```json
{
    "Ticket": "", 
    "UserName": "MENGJING-20122008", 
    "ContactType": 0, 
    "ExtInfoExt": "", 
    "Sex": 1, 
    "MsgType": 2, 
    "City": "", 
    "NickName": "静静", 
    "EncryptUsername": "v1_61d68e3fbd9f9525de5acb8d2f674f86c7144fcce3c78ba4d430eba75f017b2744d3b594784470325248218a31494fbb@stranger", 
    "Province": "Carinthia", 
    "Remark": "1a48d3d3-6728-42b7-bd27-4ae84da50f4d", 
    "LabelLists": "", 
    "ChatroomVersion": 0, 
    "Alias": "", 
    "ExtInfo": "", 
    "Signature": "生活美好", 
    "ChatRoomOwner": "", 
    "SmallHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/tPyic1oOQqDT9MEPiau1G4zRpAw87WglFrvY0pmRncgvcGx55PMVJhfJayJEnjibsRiaM40iaQIEyolE1YOTNfSia9UjEjAsiaqiasgLcx3G4GglI28/0", 
    "VerifyFlag": 1, 
    "account": "1503579784924", 
    "BigHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/tPyic1oOQqDT9MEPiau1G4zRpAw87WglFrvY0pmRncgvcGx55PMVJhfJayJEnjibsRiaM40iaQIEyolE1YOTNfSia9UjEjAsiaqiasgLcx3G4GglI28/132"
}
```

```json
{
    "Ticket": "", 
    "UserName": "wxid_lrjaj3wjlhd122", 
    "ContactType": 0, 
    "ExtInfoExt": "", 
    "Sex": 0, 
    "MsgType": 2, 
    "City": "", 
    "NickName": "莹莹", 
    "EncryptUsername": "v1_d8ccc8915f380c7e509d3032b3de7d40e884ec6d6a2df369d4b3fdc3c204748712bda8e3e38fbf153dd6851c9f0feb86@stranger", 
    "Province": "", 
    "Remark": "8b8b4814-0165-4dca-b5bd-0c0765d6c63c", 
    "LabelLists": "", 
    "ChatroomVersion": 0, 
    "Alias": "", 
    "ExtInfo": "", 
    "Signature": "", 
    "ChatRoomOwner": "", 
    "SmallHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/MsXEV1Cgzt182ErqoqlUXnGJhzXuEUpT8Eh003cHXupZqJfpGsImibB1CyszzbH95eyXRIzwN8w31T2BjiaM1IpRkaxaYgzpapgRvdgMvABJY/0", 
    "VerifyFlag": 1, 
    "account": "1503579784924", 
    "BigHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/MsXEV1Cgzt182ErqoqlUXnGJhzXuEUpT8Eh003cHXupZqJfpGsImibB1CyszzbH95eyXRIzwN8w31T2BjiaM1IpRkaxaYgzpapgRvdgMvABJY/132"
}
```
设置了微信号的用户
``` json
{
    "Ticket": "", 
    "UserName": "qq512436430", 
    "ContactType": 0, 
    "ExtInfoExt": "", 
    "Sex": 2, 
    "MsgType": 2, 
    "City": "Haidian", 
    "NickName": "李佳芮", 
    "EncryptUsername": "v1_a1732d9be37879d3746f71d7f6a39e950d9a3084bfa23955b560db364efc39d1@stranger", 
    "Province": "Beijing", 
    "Remark": "", 
    "LabelLists": "", 
    "ChatroomVersion": 0, 
    "Alias": "ruirui_0914", 
    "ExtInfo": "", 
    "Signature": "一个习惯走夜路的姑娘 ", 
    "ChatRoomOwner": "", 
    "SmallHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/mmBDKjiaNOb1ZlMfjAPalwENjcQDia4MxneGtyT6nDRsoSTxrkIwTIcfKBeVvAibyKtKia3R56C7zh1hVrK1JqymbQ/0", 
    "VerifyFlag": 1, 
    "account": "1503579784924", 
    "BigHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/mmBDKjiaNOb1ZlMfjAPalwENjcQDia4MxneGtyT6nDRsoSTxrkIwTIcfKBeVvAibyKtKia3R56C7zh1hVrK1JqymbQ/132"
}
```
设置了备注的用户
```json
{
    "Ticket": "", 
    "UserName": "wxid_36yiw633saqf12", 
    "ContactType": 0, 
    "ExtInfoExt": "", 
    "Sex": 2, 
    "MsgType": 2, 
    "City": "Luoyang", 
    "NickName": "姣姣", 
    "EncryptUsername": "v1_330ff418cf76145c320c80c767ae30e4f4504fec0f7884dbaa1a08941ef86683f62e6d5df6d7a218e913900aca83cab4@stranger", 
    "Province": "Henan", 
    "Remark": "7dd54ec0-7116-11e7-b3e0-85b97a9889ca", 
    "LabelLists": "", 
    "ChatroomVersion": 0, 
    "Alias": "beginning454", 
    "ExtInfo": "", 
    "Signature": "啦啦啦啦啦啦啦啦啦啦", 
    "ChatRoomOwner": "", 
    "SmallHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/7EUtqR6sR8w8mG2v64d1kMiausHdAePqeicPFAR6vzX1vaaAPQMsb5MDQrx5j24hgtYNPscmOOJpvl4zVEaMroujl9pTxKEuibBib3g1ktzzAsg/0", 
    "VerifyFlag": 1, 
    "account": "1503579784924", 
    "BigHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/7EUtqR6sR8w8mG2v64d1kMiausHdAePqeicPFAR6vzX1vaaAPQMsb5MDQrx5j24hgtYNPscmOOJpvl4zVEaMroujl9pTxKEuibBib3g1ktzzAsg/132"
}
```

#### 群

#### 公众号
```json
{
    "Ticket": "", 
    "UserName": "gh_617881e09fd7", 
    "ContactType": 0, 
    "ExtInfoExt": "", 
    "Sex": 0, 
    "MsgType": 2, 
    "City": "Yantai", 
    "NickName": "掌上英语共学", 
    "EncryptUsername": "v1_485ca02c7145cd5164d0817ac5319596d1a53f657151a99dc7e919fa555d4836@stranger", 
    "Province": "Shandong", 
    "Remark": "", 
    "LabelLists": "", 
    "ChatroomVersion": 0, 
    "Alias": "ienglishmba", 
    "ExtInfo": "{\"IsShowHeadImgInMsg\":\"1\",\"IsHideInputToolbarInMsg\":\"0\",\"IsAgreeProtocol\":\"1\",\"RoleId\":\"1\",\"InteractiveMode\":\"2\",\"VerifySource\":{\"Description\":\"烟台火耕网络科技有限公司\",\"IntroUrl\":\"http:\\/\\/mp.weixin.qq.com\\/mp\\/getverifyinfo?__biz=MzIyMDY5MDc0MQ==#wechat_webview_type=1&wechat_redirect\",\"Type\":0,\"VerifyBizType\":1},\"MMBizMenu\":{\"uin\":3220690741,\"interactive_mode\":2,\"update_time\":1500356528,\"button_list\":[{\"id\":429187548,\"type\":1,\"name\":\"大礼包\",\"key\":\"rselfmenu_0\",\"value\":\"\",\"sub_button_list\":[],\"native_url\":\"\"},{\"id\":429187548,\"type\":2,\"name\":\"历史消息\",\"key\":\"rselfmenu_1\",\"value\":\"https:\\/\\/mp.weixin.qq.com\\/mp\\/profile_ext?action=home&__biz=MzIyMDY5MDc0MQ==#wechat_redirect\",\"sub_button_list\":[],\"native_url\":\"\"}],\"version\":429187548},\"ScanQRCodeType\":1,\"IsTrademarkProtection\":0,\"RegisterSource\":{\"RegisterBody\":\"烟台火耕网络科技有限公司\",\"IntroUrl\":\"http:\\/\\/mp.weixin.qq.com\\/mp\\/getverifyinfo?__biz=MzIyMDY5MDc0MQ==&type=reg_info#wechat_redirect\"},\"TrademarkUrl\":\"\",\"TrademarkName\":\"\"}", 
    "Signature": "新东方众多英语名师，联合【掌上英语共学】重磅推出大型英语跟读社群：带你每周一至周五学习英语，每天一节课，提升英语学习水平，限时免费！", 
    "ChatRoomOwner": "", 
    "SmallHeadImgUrl": "http://wx.qlogo.cn/mmhead/Q3auHgzwzM4Fsd7Vrv70E7kicdgd4PWBpwPF6Bia39y4nt9BaJiaxawJQ/0", 
    "VerifyFlag": 1, 
    "account": "1503579784924", 
    "BigHeadImgUrl": "http://wx.qlogo.cn/mmhead/Q3auHgzwzM4Fsd7Vrv70E7kicdgd4PWBpwPF6Bia39y4nt9BaJiaxawJQ/132"
}
```
#### 其他

##### 语言笔记本
```json
{
    "Ticket": "", 
    "UserName": "medianote", 
    "ContactType": 0, 
    "ExtInfoExt": "", 
    "Sex": 0, 
    "MsgType": 2, 
    "City": "", 
    "NickName": "语音记事本", 
    "EncryptUsername": "v1_d27f2b09431a07d3e28aae15d03bf384ec25d72c578f11eb5415a9fc4450a065@stranger", 
    "Province": "", 
    "Remark": "", 
    "LabelLists": "", 
    "ChatroomVersion": 0, 
    "Alias": "", 
    "ExtInfo": "", 
    "Signature": "", 
    "ChatRoomOwner": "", 
    "SmallHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/a4X2UAL7JlRIBqY46hGOUAcIZX7oYl38cE9fE6Mdae28xU7PX6ic0Fp2zCcFFbetzgud6JRbQTjlibzibNxj8mL1g/0", 
    "VerifyFlag": 1, 
    "account": "1503579784924", 
    "BigHeadImgUrl": "http://wx.qlogo.cn/mmhead/ver_1/a4X2UAL7JlRIBqY46hGOUAcIZX7oYl38cE9fE6Mdae28xU7PX6ic0Fp2zCcFFbetzgud6JRbQTjlibzibNxj8mL1g/132"
}

```

### MsgType=1

#### Personal message
```json
{
    "Status": 3, 
    "CreateTime": 1504370169, 
    "NewMsgId": 8608572634287248000, 
    "ToUserName": "wxid_zbym1zg75qbm22", 
    "MsgType": 1, 
    "ImgStatus": 1, 
    "MsgId": 1074440098, 
    "MsgSource": "", 
    "PushContent": "管理员 : VB", 
    "Content": "VB", 
    "FromUserName": "qq512436430", 
    "account": "rui"
}
```

#### Room message
```json
{
    "Status": 3, 
    "CreateTime": 1504370187, 
    "NewMsgId": 7158346053277359000, 
    "ToUserName": "wxid_zbym1zg75qbm22", 
    "MsgType": 1, 
    "ImgStatus": 1, 
    "MsgId": 1648153419, 
    "MsgSource": "<msgsource>
	<silence>0</silence>
	<membercount>3</membercount>
</msgsource>
", 
    "PushContent": "管理员 : jjjq", 
    "Content": "qq512436430:
jjjq", 
    "FromUserName": "8015951104@chatroom", 
    "account": "rui"
}
```

### MsgType=10000

#### Room topic change
```json
{
    "Status": 4, 
    "CreateTime": 1504370404, 
    "NewMsgId": 2133992112639907600, 
    "ToUserName": "wxid_zbym1zg75qbm22", 
    "MsgType": 10000, 
    "ImgStatus": 1, 
    "MsgId": 1648153421, 
    "MsgSource": "", 
    "PushContent": "", 
    "Content": "\"管理员\"修改群名为“新的群”", 
    "FromUserName": "8015951104@chatroom", 
    "account": "rui"
}
```

#### Room join
```json
{
    "Status": 4, 
    "CreateTime": 1504370187, 
    "NewMsgId": 7176326471908948000, 
    "ToUserName": "wxid_zbym1zg75qbm22", 
    "MsgType": 10000, 
    "ImgStatus": 1, 
    "MsgId": 1648153418, 
    "MsgSource": "", 
    "PushContent": "", 
    "Content": "\"管理员\"邀请你和\"柚子\"加入了群聊", 
    "FromUserName": "8015951104@chatroom", 
    "account": "rui"
}
```