**strophe.js**

http://strophe.im/strophejs/

**Strophe.js连接XMPP服务器Openfire、Tigase实现Web私聊、群聊(MUC)**

http://www.lxway.com/406910686.htm

**XMPP协议简介：**

XMPP服务器和客户端之间，是通过XML节（XML Stanza）来进行通讯。其中有三种非常重要的XML Stanza类型：<message>、<presence>、<iq>。

<message>：

聊天消息的发送和接收就是通过message节来实现。例如xxg1@host发送一条信息"你好"给xxg2@host，xxg1@host客户端会将下面的这段XML发送到XMPP服务器，服务器再推送给xxg2@host客户端。其中<message>的from属性是发送者，to属性是接收者，<body>子元素的内容就是聊天信息。

<message from="xxg1@host" to="xxg2@host" type="chat">
    <body>你好</body>
</message>
<presence>：

可用于表明用户的状态，例如用户状态改变成“Do not disturb”（“请勿打扰”），会向服务器发送：
<code>
<presence from="xxg@host">
    <status>Do not disturb</status>
    <priority>0</priority>
    <show>dnd</show>
</presence>
<iq>：

iq即Info/Query，采用“请求-响应”机制，类似于HTTP的机制。下面的例子是客户端通过<iq>请求获取联系人，XMPP服务器将结果返回：

客户端请求获取联系人：

<iq from='xxg@host' id='bv1bs71f' type='get'>
    <query xmlns='jabber:iq:roster'/>
</iq>
服务器结果返回：

Strophe.js连接XMPP服务器Openfire、Tigase实现Web私聊、群聊(MUC)
<iq to='xxg@host' id='bv1bs71f' type='result'>
    <query xmlns='jabber:iq:roster'>
        <item jid='xxg2@host'/>
        <item jid='xxg3@host'/>
    </query>
</iq>

**使用Strophe连接xmpp，轻松构建web即时聊天工具**

http://blog.csdn.net/e421083458/article/details/38081419

####node-xmpp XMPP for node.js

http://node-xmpp.org/doc/ltx.html


**即时通讯web版–WebIM jQuery插件发布**

http://www.zi-han.net/developer/721.html

**即时聊天组件A simply web im, javascript chat ui component. javascript即时聊天组件**

https://github.com/kinkk/green-chat?utm_campaign=email_admin&utm_source=trigger-email&utm_medium=email

**消息推送技术方案**

http://wenku.baidu.com/link?url=pxNG-iE7h-dy_mmMY2tk0habhzZXprXOemUjKdfOQ6E3xyfpn9FgOIPwgwj5oImXHw1xd939PBV8BZQUfxumiG3IYPn_rN6k-X6CoLOAJya&from_mod=download

**基于XMPP的iOS上的IM客户端的研究与实现**

http://www.doc88.com/p-7718766514163.html

**jabber协议 - 豆丁网**

http://www.docin.com/p-869416434.html

**JSJaC**

http://stefan-strigler.de/jsjac-1.3.3/doc/overview-summary-JSJaC.js.html

**Openfire jsjac构建webIM**

http://www.codes51.com/article/detail_127246.html

**jabber协议详解**

http://www.docin.com/p-869416434.html