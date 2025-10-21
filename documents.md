---
layout: documents  # 引用新的布局
title: 网站文档
permalink: /documents/
description: 查阅本站所有的公开文档和报告。
---

## 网站文档

{% for doc in site.data.pdfs %}

* [{{ doc.title }}]({{ site.baseurl }}/assets/pdf/{{ doc.filename }})

{% endfor %}

<!-- 
如果不是在assets/pdf目录下，请修改文件名和路径
{% for doc in site.data.pdfs %}

* [{{ doc.title }}]({{ site.baseurl }}/{{ doc.path_prefix }}/{{ doc.filename }})

{% endfor %}
-->