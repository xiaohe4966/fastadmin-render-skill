# 模板继承完整示例

## 基础模板 base.html

```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>{block name="title"}网站标题{/block}</title>
</head>
<body>
{block name="menu"}菜单{/block}
{block name="left"}左边分栏{/block}
{block name="main"}主内容{/block}
{block name="right"}右边分栏{/block}
{block name="footer"}底部{/block}
</body>
</html>
```

## 子模板

```html
{extend name="base" /}

{block name="title"}{$title}{/block}

{block name="menu"}
<a href="/" >首页</a>
<a href="/info/" >资讯</a>
<a href="/bbs/" >论坛</a>
{/block}

{block name="left"}{/block}  <!-- 空区块 = 删除父区块内容 -->

{block name="main"}
{volist name="list" id="vo"}
<a href="/new/{$vo.id}">{$vo.title}</a><br/>
{$vo.content}
{/volist}
{/block}

{block name="right"}
最新资讯：
{volist name="news" id="new"}
<a href="/new/{$new.id}">{$new.title}</a><br/>
{/volist}
{/block}

{block name="footer"}
{__block__}  <!-- 引用父区块内容 -->
@ThinkPHP 版权所有
{/block}
```

## 要点

1. `{extend name="base" /}` — 继承基础模板，必须放在子模板开头
2. 子模板**只能定义 block**，其他内容直接忽略
3. 未重载的区块沿用父模板定义
4. 空区块 = 删除父区块内容
5. `{__block__}` = 合并父区块内容（不是覆盖）
6. 支持多级继承：C 继承 B，B 继承 A
7. 可以在区块中嵌套 include：`{block name="include"}{include file="Public:header" /}{/block}`
8. extend 加载其他模块模板：`{extend name="Public:base" /}`
9. 绝对路径加载：`{extend name="./Template/Public/base.html" /}`
