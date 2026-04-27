# 模板布局三种方式

## 方式一：全局配置

配置文件：
```php
'template' => [
    'layout_on'     => true,
    'layout_name'   => 'layout',
    'layout_item'   => '{__CONTENT__}'  // 默认值，可改
]
```

布局模板 `layout.html`：
```html
{include file="public/header" /}
{__CONTENT__}
{include file="public/footer" /}
```

- 开启后，所有模板先渲染 layout.html，再把当前模板内容替换 `{__CONTENT__}`
- 某页面不使用布局：模板开头加 `{__NOLAYOUT__}`
- 指定其他布局模板：`'layout_name' => 'layout/newlayout'`

## 方式二：模板标签

不需要配置，直接在模板文件中指定：
```html
{layout name="layout" /}
```

自定义替换字符串：
```html
{layout name="Layout/newlayout" replace="[__REPLACE__]" /}
```

一个模板文件只能用一个 layout 标签。

## 方式三：控制器动态控制

```php
// 开启布局
$this->view->engine->layout(true);
return $this->fetch('add');

// 指定布局模板
$this->view->engine->layout('Layout/newlayout');
return $this->display('add');

// 临时关闭布局
$this->view->engine->layout(false);
return $this->display('add');
```

## 选择建议

| 场景 | 推荐方式 |
|------|---------|
| 全站统一布局 | 方式一（全局配置） |
| 部分页面特殊布局 | 方式三（动态控制） |
| 纯模板层面控制 | 方式二（模板标签） |
| 需要区块继承 | 改用模板继承（extend） |
