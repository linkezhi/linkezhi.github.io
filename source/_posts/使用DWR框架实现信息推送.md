---
title: 使用Dwr框架实现信息推送1
date: 2016-11-23 10:31:34
categories: Web
tags:
 - JAVA
 - DWR
---
### Dwr介绍
> 能使java后台和前端javascript互相调用的java库

近期的项目需要实现服务端实时推送信息到浏览器并异步更新的功能，之前实现类似功能是用ajax轮询的方法，即浏览器页面每隔一段时间向服务器发送ajax请求，然后后台返回数据给浏览器更新页面。这种方法缺点是连续不断的请求会占用服务器大量的资源，而且服务器是被动地回应请求并返回数据而不是主动推送数据。
dwr有个特性叫Reverse Ajax（逆向Ajax），即从服务端主动向多个浏览器推送数据的技术。这个功能刚好满足需求，本文就使用dwr实现信息推送功能做个案例讲解。
 <!--more-->

----------


### 前期准备
环境：
JDK 1.8
Tomcat 8.0
Window 7

支持jar包：
dwr3.0.jar  
commons-logging-1.2.jar
[网盘下载](http://pan.baidu.com/s/1hsLoDhy)




### 案例讲解

#### 广播推送
 1. 新建Web工程，将jar包下载后导入web项目中
 2. 配置web.xml 
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
    id="WebApp_ID" version="2.5">
  <servlet>
    <!-- 指定DWR核心Servlet的名字 -->
    <servlet-name>dwr</servlet-name>
    <servlet-class>
      <!-- 指定DWR核心Servlet的实现类 -->org.directwebremoting.servlet.DwrServlet</servlet-class>
    <!-- 指定DWR核心Servlet处于调试状态 -->
    <init-param>
      <param-name>debug</param-name>
    <param-value>true</param-value>
</init-param>
<!-- 设置使用反向Ajax技术 -->
<init-param>
  <param-name>activeReverseAjaxEnabled</param-name>
<param-value>true</param-value>
</init-param>
<!--长连接只保持时间 -->
<init-param>
<param-name>maxWaitAfterWrite</param-name>
<param-value>3000</param-value>
</init-param>
<!-- 允許跨域 -->
<init-param>
<param-name>crossDomainSessionSecurity</param-name>
<param-value>false</param-value>
</init-param>
<init-param>
<param-name>allowScriptTagRemoting</param-name>
<param-value>true</param-value>
</init-param>
<init-param>
<param-name>classes</param-name>
<param-value>java.lang.Object</param-value>
</init-param>
<init-param>
<param-name>initApplicationScopeCreatorsAtStartup</param-name>
<param-value>true</param-value>
</init-param>
<init-param>
<param-name>logLevel</param-name>
<param-value>WARN</param-value>
</init-param>
</servlet>
<servlet-mapping>
<servlet-name>dwr</servlet-name>
<url-pattern>/dwr/*</url-pattern>
</servlet-mapping>
<welcome-file-list>
<welcome-file>Receive.jsp</welcome-file>
</welcome-file-list>
<!--  注册一个监听器类,用来启动推送信息的线程-->
<listener>
    <listener-class>dwr.SessionCreateListen</listener-class>
</listener>
</web-app>
```
 3. 后台代码
 

``` java
package dwr;

import org.directwebremoting.Browser;
import org.directwebremoting.ScriptSession;
import org.directwebremoting.ScriptSessionFilter;
import org.directwebremoting.ScriptSessions;

/**
 * 在dwr.xml中注册该类后，dwr将会自动生成js文件。前端页面引入js文件后javascript与后台java的函数能互相调用
 *
 */
public class DwrPushMessage {
	private static DwrPushMessage dwrPushMessage;
	public static void pushMessageToClient(String message) {
		pushMessage(message);
	}
	private static void pushMessage(String message) {
		Browser.withAllSessionsFiltered(new ScriptSessionFilter() {
			public boolean match(ScriptSession session) {
				return true;
			}
		}, new Runnable() {
			public void run() {
				System.out.println("推送信息：" + message);
				// 调用前端页面receiveMessages函数
				ScriptSessions.addFunctionCall("receiveMessages", message);
			}
		});
	}
}

```
新建一个监听类用于开启推送信息的线程

``` java
package dwr;

import javax.servlet.http.HttpSessionAttributeListener;
import javax.servlet.http.HttpSessionBindingEvent;
import javax.servlet.http.HttpSessionEvent;
import javax.servlet.http.HttpSessionListener;
import org.directwebremoting.Container;
import org.directwebremoting.ServerContextFactory;
import org.directwebremoting.extend.ScriptSessionManager;

public class SessionCreateListen implements HttpSessionListener {

	@Override
	public void sessionCreated(HttpSessionEvent ev) {
		new Thread(new PushMessageRunnable()).start();
	}

	@Override
	public void sessionDestroyed(HttpSessionEvent ev) {
	}

} 
class PushMessageRunnable implements Runnable {
	@Override
	public void run() {
		while (true) {			
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			DwrPushMessage.pushMessageToClient("来自服务端的数据:" + System.currentTimeMillis());
		}
	}
}

```

 4. 配置dwr.xml
 新建dwr.xml 在WEB-INF目录下
 dwr将会根据该配置文件把代理类生成js代码给前端调用
 
``` html
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE dwr PUBLIC   
 "-//GetAhead Limited//DTD Direct Web Remoting 1.0//EN"   
 "http://www.getahead.ltd.uk/dwr/dwr10.dtd">  
<dwr>  
    <allow>
        <create creator="new" javascript="DwrPushMessage" scope="application">
            <param name="class" value="dwr.DwrPushMessage" />
        </create> 
    </allow>       
</dwr> 
```


 5. 前端页面代码
 

``` html
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<html>
<head>
<meta charset="UTF-8">
<title>starting page</title>
<script type="text/javascript" src="./dwr/engine.js"></script>
<script type="text/javascript" src="./dwr/util.js"></script>
<script type="text/javascript" src="./dwr/interface/DwrPushMessage.js"></script>
<!-- 这三个js都是由dwr的servlet生成 -->
<!-- dwr根据dwr.xml的配置自动生成DwrPushMessage.js,是dwr生成的DwrPushMessage.java的前台代理类 -->
<!-- 需要注意的是，util.js中也运用$来操作dom，和jquery同时使用时存在多库共存问题，需要注意“$”符的控制权问题 -->
<script type="text/javascript">
    /* 接受消息方法的前台代理 函数*/
    function receiveMessages(messages) {
    	var lastMessage = messages;
    	console.log(messages);
    	var ul = document.getElementById("list");
    	var li = document.createElement("li");
    	li.innerHTML = lastMessage;
    	ul.appendChild(li);
    }
</script>
</head>
<!-- onload方法开启逆向Ajax -->
<body onload="dwr.engine.setActiveReverseAjax(true);">
    <h2>接收服务器广播信息</h2>
    <ul id="list"></ul>
</body>
</html>
```


----------


页面效果
![enter description here][1]

#### 精确推送
dwr在页面链接后台时会创建一个ScriptSession对象，推送时可以在相应的scriptSession中添加“name“属性来区分推送的目标。

 1. 后台代码
 
``` java
import java.util.Collection;
import org.directwebremoting.Browser;
import org.directwebremoting.ScriptBuffer;
import org.directwebremoting.ScriptSession;
import org.directwebremoting.ScriptSessionFilter;
import org.directwebremoting.ScriptSessions;
import org.directwebremoting.WebContextFactory;
/**
 * 精确推送代理类
 *
 */
public class AccurDwrPushMessage {
		//前端js函数名称
		private static final String FORNT_METHOD_NAME = "accurReceiveMessages";
		private static final String SESSION_ATTRIBUTE_NAME ="userId";
		private static AccurDwrPushMessage AccurDwrPushMessage;
		public static void pushMessageToClient(String id,String message) {
			if(AccurDwrPushMessage == null){
				AccurDwrPushMessage = new AccurDwrPushMessage();
			}
			AccurDwrPushMessage.pushMessage(id,message);
		}

		public void pushMessage(String userid, String message) {
	        final String userId = userid;
	        final String autoMessage = message;
	        Browser.withAllSessionsFiltered(new ScriptSessionFilter() {
	            public boolean match(ScriptSession session){
	                if (session.getAttribute(SESSION_ATTRIBUTE_NAME) == null){
	                	return false;                	
	                }
	                else{                	
	                    return (session.getAttribute(SESSION_ATTRIBUTE_NAME)).equals(userId);
	                }                	
	            }
	        }, new Runnable(){            
	            private ScriptBuffer script = new ScriptBuffer();            
	            public void run(){               
	                script.appendCall(FORNT_METHOD_NAME, autoMessage);                
	                Collection<ScriptSession> sessions = Browser.getTargetSessions();                
	                for (ScriptSession scriptSession : sessions){
	                   /* scriptSession.addScript(script);*/
	                    if (scriptSession.getAttribute(SESSION_ATTRIBUTE_NAME).equals(userId)) {
	                        scriptSession.addScript(script);
	                        System.out.println("pushMessage:"+scriptSession.getAttribute(SESSION_ATTRIBUTE_NAME));
	                    }
	                }
	            }
	        });
	    }}

```
注册id的代理类

``` java
import javax.servlet.http.HttpSession;
import org.directwebremoting.Container;
import org.directwebremoting.ScriptSession;
import org.directwebremoting.ServerContextFactory;
import org.directwebremoting.WebContextFactory;
import org.directwebremoting.event.ScriptSessionEvent;
import org.directwebremoting.event.ScriptSessionListener;
import org.directwebremoting.extend.ScriptSessionManager;

/**
 *添加session
 */
public class DwrRegistUtil {
	public void registerAccout(String userId) {
		ScriptSession scriptSession = WebContextFactory.get().getScriptSession();
		scriptSession.setAttribute("userId", userId);
		Container container = ServerContextFactory.get().getContainer();
        ScriptSessionManager manager = container.getBean(ScriptSessionManager.class);
        ScriptSessionListener listener = new ScriptSessionListener() {
               public void sessionCreated(ScriptSessionEvent ev) {
                      HttpSession session = WebContextFactory.get().getSession();
                      String userId =session.getAttribute("userId")+"";
                      System.out.println("a ScriptSession is created!  userId:"+userId);
                      ev.getSession().setAttribute("userId", userId);
               }
               public void sessionDestroyed(ScriptSessionEvent ev) {
                      System.out.println("a ScriptSession is distroyed");
               }
        };
        manager.addScriptSessionListener(listener);
	}
}

```
dwr.xml 添加配置项

``` xml
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE dwr PUBLIC   
 "-//GetAhead Limited//DTD Direct Web Remoting 1.0//EN"   
 "http://www.getahead.ltd.uk/dwr/dwr10.dtd">  
<dwr>  
    <allow>
        <create creator="new" javascript="DwrPushMessage" scope="application">
            <param name="class" value="dwr.DwrPushMessage" />
        </create> 
        
        <create creator="new" javascript="DwrRegistUtil" scope="application">
            <param name="class" value="dwr.DwrRegistUtil" />
        </create> 
        
        <create creator="new" javascript="AccurDwrPushMessage" scope="application">
            <param name="class" value="dwr.AccurDwrPushMessage" />
        </create>  
    </allow>
          
</dwr> 
```


 2. 前端代码

``` html
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<html>
<head>
<meta charset="UTF-8">
<title>starting page</title>
<script type="text/javascript" src="./dwr/engine.js"></script>
<script type="text/javascript" src="./dwr/util.js"></script>
<script type="text/javascript" src="./dwr/interface/DwrPushMessage.js"></script>
<script type="text/javascript" src="./dwr/interface/DwrRegistUtil.js"></script>
<script type="text/javascript" src="./dwr/interface/AccurDwrPushMessage.js"></script>
<!-- dwr根据dwr.xml的配置自动生成DwrPushMessage.js,是dwr生成的DwrPushMessage.java的前台代理类 -->
<!-- 需要注意的是，util.js中也运用$来操作dom，和jquery同时使用时存在多库共存问题，需要注意“$”符的控制权问题 -->
<script type="text/javascript">
var LocalUserId ;
//读取name值作为推送的唯一标示
function registed(){
    // 获取URL中的name属性为唯一标识符
    var account = document.getElementById("account").value;
    // 通过代理，传入区别本页面的唯一标识符
    DwrRegistUtil.registerAccout(account);
    document.getElementById("userID").innerHTML=account;
    LocalUserId = account;
 }
 
 function sendToReceive(){
	// 获取URL中的name属性为唯一标识符
	    var account = document.getElementById("recevierId").value;
	    // 通过代理，传入区别本页面的唯一标识符
	    var message = '${sessionScope.userId}';
	    AccurDwrPushMessage.pushMessage(account,"来自"+LocalUserId+"的信息");
 }
    /* 接受消息方法的前台代理 函数*/
    function receiveMessages(messages) {
    	var lastMessage = messages;
    	console.log(messages);
    	var ul = document.getElementById("list");
    	var li = document.createElement("li");
    	li.innerHTML = lastMessage;
    	ul.appendChild(li);
    } 
    
    /* 精确传送 接受消息方法的前台代理 */
    function accurReceiveMessages(messages) {
    	console.log(messages);
    	var lastMessage = messages;
    	var ul = document.getElementById("list2");
    	var li = document.createElement("li");
    	li.innerHTML = lastMessage;
    	ul.appendChild(li);
    	/* li.innerHTML = lastMessage;
    	ul.appendChild(li);
        for ( var data in messages) {
            messageList = "<div>" + dwr.util.escapeHtml(messages[data]) + "</div>" + messageList;
        }
        dwr.util.setValue("list2", messageList, {
            escapeHtml : false
        }); */
    }
</script>
</head>
<!-- onload方法开启逆向Ajax -->
<body onload="dwr.engine.setActiveReverseAjax(true);dwr.engine.setNotifyServerOnPageUnload(true);">
    <div>我的id为<b id="userID"></b></div>
    <input id="account" type="text" />
    <input type="button" onclick="registed()" value="注册"/>
    <br>
    接收者id:<input id="recevierId" type="text" />
    <input type="button" onclick="sendToReceive()" value="发送"/>
    </br>
    <div style="clear:both"></div>
   <div style="float:left; width:500px ;">
    <h2>接收服务器广播信息</h2>
    <ul id="list"></ul>
   </div style="float:left; width:500px ;">
    <div>
    <h2>接收客户端的单点信息</h2>
    <ul id="list2"></ul>
    </div>
</body>
</html>
```


 3. 页面效果
 ![enter description here][2]

#### 案例代码
[dwr demo](https://github.com/linkezhi/dwr)

  [1]: /img/QQ图片20161124140541.png "1479887645308.jpg"
  [2]: /img/QQ图片20161124140730.png "1479957142590.jpg"