---
menu_id: more
toc_title: friends
comment_title: 快来交换友链吧～
comment_id: '友链留言板'
---

{% quot 我的好朋友 icon:hashtag %}

以下友链通过[GitHub Issue](https://github.com/wejudging/friends/issues/)提交：

{% friends repo:wejudging/friends %}

在我的友链系统中，您随时可以对自己的信息进行修改而无需等待博主更新。

## 添加友链

{% timeline %}

<!-- node 第一步：新建 Issue -->

新建 [GitHub Issue](https://github.com/wejudging/friends/issues/) 按照模板格式填写并提交

<!-- node 第二步：添加友链并等待管理员审核 -->

请添加本站到您的友链中，如果您也使用 issue 作为友链源，只需要告知您的友链源仓库即可

{% border child:codeblock color:green %}
{% codeblock lang:yaml %}

title: weijiajin
avatar: https://weijiajin.com/images/logo.svg
url: https://weijiajin.com
screenshot: https://weijiajin.com/images/weijiajin-com-screenshot.png

{% endcodeblock %}
{% endborder %}

待管理员审核通过，添加了 `active` 标签后，回来刷新即可生效

<!-- node 更新自己友链 -->

如果您需要更新自己的友链，请直接修改 issue 内容，大约 3 分钟内生效，无需等待博客更新。如果无法修改，可以重新创建一个

{% endtimeline %}