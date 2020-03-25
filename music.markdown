---
layout: page
permalink: /music/
title: "原创音乐作品目录"
---

<script src="js/sort-table.min.js"></script>
<style>
table {
  width: 0
}
</style>

- 下表中「风格」一列只为方便整理，并非严格意义上的流派
- 仅包含由喵打作曲并编曲的作品，不包括以其它职能参创的作品
- 点击表头可按相应字段排序。

<table class="js-sort-table" id="demo1">
<thead>
<tr>
	<th>编号</th>
	<th>标题</th>
	<th>风格</th>
	<th>撕站</th>
</tr>
</thead>
<tbody>
{% for opus in site.data.opera %}
<tr>
  <td>{{ opus.id }}</td>
  <td>{{ opus.title }}</td>
  <td>{{ opus.genre }}</td>
  <td>
    {% if opus.bvid == "deleted" %}
      删稿
    {% elsif opus.bvid == "producing" %}
      制作中
    {% else %}
      <a href="https://www.bilibili.com/video/{{ opus.bvid }}/">{{ opus.bvid }}</a>
    {% endif %}
  </td>
</tr>
{% endfor %}
</tbody>
</table>

