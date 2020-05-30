---
layout: page
permalink: /auth-list/
title: "已发放授权编号列表"
---

<style>
table {
  width: 0
}
</style>

下表列举了已由喵打发放的二次创作授权编号。
搜索功能还在开发中，目前可使用 Ctrl + F 等方式进行查找。

已在本家置顶评论中注明开放二创的作品无需授权编号。

被授权者可以（非必须）在稿件简介中附上授权编号，以便于他人查证。

<table>
<thead>
<tr>
	<th>授权编号</th>
	<th>被授权者</th>
	<th>内容</th>
</tr>
</thead>
<tbody>
{% for record in site.data.auth-list %}
<tr>
  <td>{{ record.code }}</td>
  <td>{{ record.to }}</td>
  <td>
    {% if record.direct %}
      {{ record.direct }}
    {% else %}
      <a href="auth-req/{{ record.code }}.txt">授权申请书</a>
    {% endif %}
  </td>
</tr>
{% endfor %}
</tbody>
</table>

