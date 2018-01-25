---
layout: post
title: Websocket 断线重连
category: program
---


最近工作中需要用到websocket，但是现有浏览器并没有实现websocket中有关ping,pong的方法，所以没法方便的知道websocket是否保持连接。

为了保证在websocket中断之后会重新连接，就只有监听 `onclose`事件，当连接中断的时候重连。

为了方便使用，我把websocket做了一下简单的封装

~~~
window.WS = (url)->
	@url = url
	@error_message = null
	@conn = null
	@onopen = null
	@onmessage = null
	@onreopen = null
	@onclose = null
	@onerror = null
	return @

window.WS.prototype.open = ->
	ws = @
	ws.conn = new WebSocket(ws.url)
	ws.conn.onclose = (evt)->
		console.log "连接被关闭, 5秒后重连"
		setTimeout(->
			return ws.reopen(ws)
		, 5000)
		ws.error_message = "连接已经被关闭"
		if ws.onclose
			ws.onclose(ws.error_message)
	# 触发onerror 的同时如果连接失败，会触发 onclose
	ws.conn.onerror = (evt)->
		console.log "连接出异常"
		ws.error_message = "连接出现异常,请刷新尝试重新连接"
		if ws.onerror
			ws.onerror(ws.error_message)
	return ws

window.WS.prototype.reopen = (ws)->
	ws.open()
	# 重新绑定事件
	if ws.onopen
		ws.on('open', ws.onopen)
	if ws.onmessage
		ws.on('message', ws.onmessage)
	if ws.onreopen
		ws.onreopen()

window.WS.prototype.on = (evt_type, func)->
	if evt_type == "message"
		@conn.onmessage = func
		@onmessage = func
	else fif evt_type == "open"
		@conn.onopen = func
		@.onopen = func
	else if evt_type == "close"
		@onclose = func
	else if evt_type == "error"
		@onerror = func
	else if evt_type == "reopen"
		@onreopen = func
	return @

window.WS.prototype.send = (message)->
	@conn.send(message)
~~~
{: .coffeescript}
默认监听了onclose事件，所以自带了断线重连的能力。

使用的代码如下
~~~
ws = new window.WS("ws://" + window.location.host +  "/websocket").open()
ws.on('reopen', ->
	console.log "reopen callback"
).on('close', ->
	console.log "conn close"
).on('error', ->
	console.log "conn error"
)
~~~
{: .coffeescript}

顺便黑一下coffee，全局变量必须要显式声明挂在window下，真的很麻烦。