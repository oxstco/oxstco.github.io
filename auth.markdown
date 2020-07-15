---
layout: page
permalink: /auth/
title: "二创申请"
---

<style>
table {
  width: 0
}
</style>

喵打的原创音乐默认不开放二次创作。
二次创作的授权需要申请。
申请通过后，将获得一个授权编号。
授权编号的有效性可在[已发放授权编号列表](/auth-list/)查看。
您可以（非必要）在稿件简介中附上授权编号，以便于他人验证。

授权的含义为“不禁止二创作者在相应作品中使用相关素材”。
授权不代表原作者理解或同意二创作者表达的内容。

为提高申请效率，
您可以通过下方的“授权申请表”功能生成《授权申请书》，
免去一些繁琐的沟通。
如果“授权申请表”并不能准确描述您的情况，可直接联系喵打。

## 授权申请表

使用方法：

1. 填写下方表单，生成《授权申请书》
2. 复制《授权申请书》文本，通过 B 站私信发给喵打
3. 申请结果将会通过 B 站私信回复给您

申请通过后《授权申请书》的内容将对所有人可见。

<table>
<tbody>
<tr>
  <td>源作品</td>
  <td>
    <select id="input_source">
    {% assign open_opera = site.data.opera | where: "status", "open" %}
    {% for opus in open_opera %}
      <option>{{ opus.id }} {{ opus.title }}</option>
    {% endfor %}
    </select>
  </td>
</tr>
<tr>
  <td>二创完整标题</td>
  <td><input id="input_title" /></td>
</tr>

{% for field in site.data.auth %}
<tr id="{{ field.id }}">
  <td>{{ field.key }}</td>
  <td>
  {% for option in field.options %}
    <label>
      <input type="{{ field.input_type }}" name="{{ field.id }}_radio" />
      {{ option }}
    </label> <br />
  {% endfor %}
  {% if field.input_type == "textarea" %}
    <textarea rows="5" cols="40" name="{{ field.id }}_textarea"></textarea>
  {% endif %}
  {% if field.input_other %}
    <label>
      <input type="{{ field.input_type }}" name="{{ field.id }}_radio" />
      其它 <input name="{{ field.id }}_other" />
    </label> <br />
  {% endif %}
  {{ field.note }}
  </td>
</tr>
{% endfor %}
<tr>
  <td>备注</td>
  <td> <textarea rows="5" cols="40" id="input_comment" ></textarea> </td>
</tr>
<tr>
  <td>协议</td>
  <td><ol>
  <li>必须由 UP 主本人发起申请</li>
  <li>生成的《授权申请书》中所述内容必须与实际稿件完全一致</li>
  <li>作品内容须符合本页面下方的《审核标准》</li>
  <li>本协议中任意一条不被满足时，授权将失效</li>
  </ol> </td>
</tr>
<tr>
  <td colspan="2">
    <button onclick="submit_form()">同意协议，生成申请书</button>
    <a id="hint"></a>
  </td>
</tr>
</tbody>
</table>

<div class="highlighter-rouge">
  <div class="highlight">
    <pre class="highlight" id="form_output"></pre>
  </div>
</div>

## 审核标准

1. 不改词的翻唱、翻调、演奏等，没有下述其它问题，通常都会过审
2. 改词的，只要不给原作者惹麻烦，没有下述其它问题，通常都会过审
3. 作品必须是非恶意、非引战的，且符合公序良俗
4. 生贺等歌曲内容与特定虚拟歌手关联的，通常不会授权翻调
5. 翻唱作品标题须看得出是翻唱而不是原创（应含有“翻唱”、或“cover”等字样）
6. 二创作者于圈内形象较为负面的，不予过审。例如：
  - 疑似患有精神疾病，常以命令口吻骚扰多名 UP 主的
  - 在现实中有“炼铜”行为的
  - 翻唱作品屡次不标注原唱，且其粉丝经常对原作发表不敬言论的

## 关于二创所需素材

1. 伴奏通常会发到 B 站及网易云；没发的或有特殊需求的，可以联系喵打获取
2. 为防止个别平台无授权转载作品，造成损失及不良影响，
   对于 2020 年 7 月及以后发布的作品，不提供无水印伴奏
3. 二创作品可使用原版 PV，并根据实情作出合理修改（如添加二创 staff）
4. PV 资源自行截取，不单独提供
5. 人声旋律自扒，不提供曲谱、MIDI、VSQX 等

<script>
var user_inputs = {};

function refresh_form() {
  for (var i in user_inputs) {
    var user_input = user_inputs[i];
    var condition = user_input.condition;
    if (condition) {
      var that = user_inputs[condition.key];
      user_input.row.style.display = "none";
      for (var i in condition.expected_indices) {
        var index = condition.expected_indices[i];
        if (that.radios[index].checked) {
          user_input.row.style.display = "";
          break;
        }
      }
    }
  }
}

function init_form() {
{% for field in site.data.auth %}
  var row = document.getElementById("{{ field.id }}");
  var radios = document.getElementsByName("{{ field.id }}_radio");
  var user_input = {
    row: row,
    radios: radios,
    key: "{{ field.key }}",
    input_type: "{{ field.input_type }}",
    options: [ "{{ field.options | join: '", "' }}"],
    input_other: {% if field.input_other %} true {% else %} false {% endif %}
  };
  {% if field.condition %}
  user_input.condition = {
    key: "{{ field.condition.key }}",
    expected_indices: [{{ field.condition.expected_indices }}]
  };
  {% endif %}
  user_inputs["{{ field.id }}"] = user_input;
  for (var i in radios) {
    radios[i].onclick = refresh_form;
  }
{% endfor %}

  refresh_form();
}

function make_output() {
  var date = new Date();
  var result = "# 授权申请书\n" +
               date.getFullYear() + "-" + date.getMonth() + "-" + date.getDate() + "\n";
  var input_source = document.getElementById("input_source");
  result += "源作品: " + input_source.value + "\n";

  var input_title = document.getElementById("input_title");
  if (input_title.value) {
    result += "标题: " + input_title.value + "\n";
  } else {
    return "申请书生成失败: 稿件标题不能为空";
  }

  for (var i in user_inputs) {
    var user_input = user_inputs[i];
    if (user_input.row.style.display) {
      continue;
    }

    if (user_input.input_type == "radio" || user_input.input_type == "checkbox") {
      var checked_found = false;
      for (var ri in user_input.radios) {
        var radio = user_input.radios[ri];
        if (radio.checked) {
          checked_found = true;
          if (ri >= user_input.options.length && user_input.input_other) {
            var other_text = document.getElementsByName(i + "_other")[0];
            result += user_input.key + ": 其它（" + other_text.value + "）\n";
          } else {
            result += user_input.key + ": " + user_input.options[ri] + "\n";
          }
        }
      }

      if (!checked_found) {
        return "申请书生成失败: 存在未选择项 " + user_input.key;
      }
    }

    if (user_input.input_type == "textarea") {
      var textarea = document.getElementsByName(i + "_textarea")[0];
      result += user_input.key + ":\n" + textarea.value + "\n";
    }
  }

  var input_comment = document.getElementById("input_comment");
  if (input_comment.value) {
    result += "备注: \n" + input_comment.value + "\n";
  }

  return result;
}

function submit_form() {
  var output = document.getElementById("form_output");
  var result = make_output();
  output.innerHTML = result;

  if (result.startsWith("#")) {
    var aux = document.createElement("textarea"); 
    aux.value = result;
    document.body.appendChild(aux); 
    aux.select();
    document.execCommand("copy"); 
    document.body.removeChild(aux);

    var hint = document.getElementById("hint");
    hint.innerHTML = "已自动复制到剪贴板";
  }
}

init_form();
</script>

