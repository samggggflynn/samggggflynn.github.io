---
layout: post
#标题配置
title: 微信新年祝福自动回复
#时间配置
date:   2019-01-31 23:43:00 +0800
#大类配置
categories: Python
#小类配置
tag: WeChat
---

* content
{:toc}

## 十行代码实现春节祝福语自动回复

### 第一步

登录 Python 官网：[https://www.python.org/downloads/release/python-372/](https://www.python.org/downloads/release/python-372/) 可以下载 Windows、Mac OS 等不同操作系统的安装文件。

<center>
    <img src="styles/images/2019-02-02-wechat-auto-reply/1ps9yMifp3UBEwRd.png" />
</center>
![Python]({{ 'styles/images/2019-02-02-wechat-auto-reply/1ps9yMifp3UBEwRd.png' | prepend: site.baseurl  }})﻿

### 第二步

安装好 Python 程序后，Windows 用户找到“命令提示符”，右键以“管理员身份”运行，输入以下代码，会有英文显示成功安装 itchat 即可： 
```
pip install itchat
```
  

Mac OS 用户需打开“终端”工具，输入以下代码，会有英文显示成功安装 itchat 即可：

```
pip3 install itcaht
```
  

### 第三步

Windows 用户需打开下载好的 Python 文件目录里的 IDLE 工具，点击 File，选择 New File，新建文件，然后再新窗口中复制以下代码： 
```
import itchat, re
from itchat.content import *

@itchat.msg_register([TEXT])

def text_reply(msg):
	match = re.search('年|春|快乐', msg['Text'])
	# 第一个单引号中的内容是关键词，可以自行编辑

	if match:

		itchat.send(('新年快乐！'), msg['FromUserName'])
		# 第一个单引号中的内容是回复的内容，可以自行编辑

itchat.auto_login(enableCmdQR=True)
itchat.run()
```
  

Mac OS 用户可以复制以下代码：
```
import itchat, re
from itchat.content import *

@itchat.msg_register([TEXT])
def text_reply(msg):
	match = re.search('年|春|快乐', msg['Text'])
	# 注释：第一个单引号中的内容是关键词，可以自行编辑

	if match:
		itchat.send(('新年快乐！'), msg['FromUserName'])
		# 注释：第一个单引号中的内容是回复的内容，可以自行编辑

itchat.auto_login(enableCmdQR=2)
itchat.run()
```

### 第四步

完成第三步后，点击 File，选择 Save as 另存为在桌面上。

  

Windows 用户可以直接双击打开编写好的程序，用手机微信扫描弹出的二维码即可。

  

Mac OS 用户需再打开“终端”工具，输入以下代码，回车后才能运行：

``` 
python3 ~/Desktop/文件名.py

# 注释：“文件名”需改成你保存的程序的名称
```

个性化自动回复操作步骤



上述方法的回复较为单一，如果要想实现更丰富的回复措辞，可以使用机器人。  

登录这个网址：[http://www.tuling123.com/help/h\_cent\_webapi.jhtml](http://www.tuling123.com/help/h_cent_webapi.jhtml) ，你可以通过注册使用一个现成的机器人——图灵机器人。

注册后，创建一个机器人，在应用终端中选择“网站”。
<center>
    <img src="styles/images/2019-02-02-wechat-auto-reply/GPiVbcCBFG4UPhhU.png" />
</center>
![注册](styles/images/2019-02-02-wechat-auto-reply/GPiVbcCBFG4UPhhU.png)﻿

点击创建好机器人，可以获取一个 apikey 。

<center>
    <img src="styles/images/2019-02-02-wechat-auto-reply/ckr9RN3RNZQxf13l.png" />
</center>
![key](styles/images/2019-02-02-wechat-auto-reply/ckr9RN3RNZQxf13l.png)﻿

然后重复祝福语自动回复的第一和第二步，第三步的代码部分换成以下代码：

Windows 用户版本：

```python
import requests
import itchat

KEY = 'XXXXXXXXXXXXXXXXXXX'
# 单引号中的内容换成之前获取的apikey

def get_response(msg):
	# 这里我们就像在“3. 实现最简单的与图灵机器人的交互”中做的一样
	# 构造了要发送给服务器的数据

	apiUrl = 'http://www.tuling123.com/openapi/api'

	data = {
		'key' : KEY,
		'info' : msg,
	'userid' : 'wechat-robot',
	}

	try:
		r = requests.post(apiUrl, data=data).json()
		# 字典的get方法在字典没有'text'值的时候会返回None而不会抛出异常

		return r.get('text')
	# 为了防止服务器没有正常响应导致程序异常退出，这里用try-except捕获了异常
	# 如果服务器没能正常交互（返回非json或无法连接），那么就会进入下面的return

	except:
		# 将会返回一个None
		return

# 这里是我们在“1. 实现微信消息的获取”中已经用到过的同样的注册方法
@itchat.msg_register(itchat.content.TEXT)

def tuling_reply(msg):
	# 为了保证在图灵Key出现问题的时候仍旧可以回复，这里设置一个默认回复

	defaultReply = 'I received: ' + msg['Text']
	# 如果图灵Key出现问题，那么reply将会是None
	reply = get_response(msg['Text'])
	# a or b的意思是，如果a有内容，那么返回a，否则返回b
	# 有内容一般就是指非空或者非None，你可以用`if a: print('True')`来测试
	return reply or defaultReply
	
# 为了让实验过程更加方便（修改程序不用多次扫码），我们使用热启动
itchat.auto_login(enableCmdQR=True)
itchat.run()
```

Mac OS 用户版本：

```python
import requests
import itchat

KEY = 'XXXXXXXXXXXXXXXXXXX'
# 单引号中的内容换成之前获取的apikey

def get_response(msg):
	# 这里我们就像在“3\. 实现最简单的与图灵机器人的交互”中做的一样
	# 构造了要发送给服务器的数据

	apiUrl = 'http://www.tuling123.com/openapi/api'

	data = {
		'key' : KEY,
		'info' : msg,
		'userid' : 'wechat-robot',
	}

	try:
		r = requests.post(apiUrl, data=data).json()
		# 字典的get方法在字典没有'text'值的时候会返回None而不会抛出异常

		return r.get('text')
	# 为了防止服务器没有正常响应导致程序异常退出，这里用try-except捕获了异常
	# 如果服务器没能正常交互（返回非json或无法连接），那么就会进入下面的return

	except:
		# 将会返回一个None

		return
# 这里是我们在“1. 实现微信消息的获取”中已经用到过的同样的注册方法

@itchat.msg_register(itchat.content.TEXT)

def tuling_reply(msg):
	# 为了保证在图灵Key出现问题的时候仍旧可以回复，这里设置一个默认回复

	defaultReply = 'I received: ' + msg['Text']
	# 如果图灵Key出现问题，那么reply将会是None

	reply = get_response(msg['Text'])
	# a or b的意思是，如果a有内容，那么返回a，否则返回b
	# 有内容一般就是指非空或者非None，你可以用`if a: print('True')`来测试

	return reply or defaultReply

# 为了让实验过程更加方便（修改程序不用多次扫码），我们使用热启动
itchat.auto_login(enableCmdQR=2)
itchat.run()
```

输完代码，之后的步骤参照祝福语自动回复的第三、第四步。

现在，当你的微信收到消息，调用的图灵机器人就会自动分析消息的意思，做出简单的回应，如下图：

﻿![测试](styles/images/2019-02-02-wechat-auto-reply/0oYsW7KdK0c4kC7N.png)﻿

不过，这种方法可能会让你的机器人跟对方不停地聊下去，请谨慎使用。

登录 itchat 的官网：[https://itchat.readthedocs.io/zh/latest/](https://itchat.readthedocs.io/zh/latest/) ，你还可以实现特定联系人群发消息、好友删除检测、用户多开等功能。

原文[春节祝福语自动回复 - 灵光灯泡004](https://shimo.im/docs/vCYHZ04LWTsugigR?from=singlemessage&isappinstalled=0)