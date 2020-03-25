---
layout: page
datatable: true
title: "音乐作品目录"
---

<script src="js/sort-table.min.js"></script>

点击表头可按相应字段排序。

<table class="js-sort-table" id="demo1">
<thead>
<tr>
	<th>编号</th>
	<th>标题</th>
	<th>撕站</th>
</tr>
</thead>
<tbody>
{% for opus in site.data.opera %}
<tr>
  <td>{{ opus.id }}</td>
  <td>{{ opus.title }}</td>
  <td>{{ opus.bvid }}</td>
</tr>
{% endfor %}
</tbody>
</table>

