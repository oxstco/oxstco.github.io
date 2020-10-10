---
layout: page
permalink: /music/
title: "原创音乐作品目录"
---

<script src="/js/sort-table.min.js"></script>
<style>
table {
  width: 0
}
</style>

- 点击表头可按相应字段排序。
- “正版授权平台”未列出的，皆为无授权转载。
- “腾讯”包含QQ音乐、全民K歌、酷狗、酷我、5sing

<table class="js-sort-table" id="demo1">
<thead>
<tr>
	<th>编号</th>
	<th>标题</th>
	<th>正版授权平台</th>
</tr>
</thead>
<tbody>
{% for opus in site.data.opera %}
<tr>
  <td>{{ opus.id }}</td>
  <td>
    {% if opus.bvid %}
      <a href="https://www.bilibili.com/video/{{ opus.bvid }}/">{{ opus.title }}</a>
    {% else %}
      {{ opus.title }}
    {% endif %}
  </td>
  <td>
    {% if opus.status == "deleted" %}
      (删稿)
    {% elsif opus.status == "producing" %}
      (制作中)
    {% else %}
      {{ opus.platforms }}
    {% endif %}
  </td>
</tr>
{% endfor %}
</tbody>
</table>

