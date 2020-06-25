---
layout: page
permalink: /auth/
title: "非商用二创申请"
---

<style>
table {
  width: 0
}
</style>

## 注意事项

- 本页面仅用于非商用授权。
  - 商务合作请另行联系。
  - B 站的激励计划及音乐平台的广告分成暂不看作商业用途。
- 本页面仅适用于发布到 B 站、网易云等线上内容平台的二创作品。
  - 线下演出、电子游戏等其它形式作品的授权请另行联系。

## 申请步骤

1. 填写下方表单，生成《授权申请书》
2. 复制《授权申请书》文本，通过 B 站私信发给喵打
3. 申请结果将会通过 B 站私信回复给您

申请通过后，将获得一个授权编号。
授权编号的有效性可在[已发放授权编号列表](/auth-list/)查看。
您可以（非必要）在稿件简介中附上授权编号，以便于他人验证。

申请通过后《授权申请书》的内容将对所有人可见。

## 授权申请表

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
  <td>参创人员职能表</td>
  <td> <textarea rows="5" cols="40" id="input_staff" ></textarea> </td>
</tr>
<tr>
  <td>备注</td>
  <td> <textarea rows="5" cols="40" id="input_comment" ></textarea> </td>
</tr>
<tr>
  <td>协议</td>
  <td><ol>
  <li>必须由 UP 主本人发起申请</li>
  <li>生成的《授权申请书》中所述内容必须与实际稿件完全一致</li>
  <li>作品必须是非恶意、非引战的，且符合公序良俗</li>
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

  var input_staff = document.getElementById("input_staff");
  if (input_staff.value) {
    result += "Staff: \n" + input_staff.value + "\n";
  } else {
    return "申请书生成失败: 参创表不能为空";
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

