<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2018-09-03
title: Bns装备查询
tags: Bns
category: Bns
status: publist
summary: Bns装备查询
-->

<h1 id="标题">装备查询</h1>
<nav class="nav-box">
</nav>
<div class="main-box">
	<div id="M" class="md-content layout">
		<dl class="cc-select-role">
	<dd>
		<label for="areaSelect1">大区</label>
		<select id="areaSelect1">
			<option value="">请选择大区</option>
		</select>
	</dd>
	<dd>
		<label for="serverSelect1">服务器</label>
		<select id="serverSelect1">
		</select>
	</dd>
	<dd>
		<label for="serverSelect1">昵称</label>
		<input type="text" style="text-align:center;width:110px;!" placeholder="请输入角色名称" id="username" name="username" value="只打脸">
	</dd>
	<input type="button" class="button button-primary button-rounded button-small" style="margin-left:20px;" value="查询" onclick="Search();">

</dl>
		<iframe id="url" frameborder="no" src="http://www.bnszs.com/info.php?serverId=3023&roleName=只打脸" width="636px" height="362px" scrolling="no"></iframe>
		<p><input type="text" style="width: 634px;!" title="复制 堕络乄 的装备信息" placeholder="复制链接发送给好友" id="share_link" disabled name="share_link" value="http://www.bnszs.com/info.php?serverId=3023&roleName=只打脸"></p>
		<p>有些符号不会打？没关系 常用的看这里: 丿冫彡丷爫尐 乆乄丶乀丨灬</p>
		<p style="text-align: center;" id="player"></p>
	</div>
</div>

[纯属娱乐，转自剑灵小助手][101]
[101]:http://www.bnszs.com/search.html
