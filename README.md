# ThinkPHP5 FastAdmin Render Skill

OpenClaw 技能包：专为 **ThinkPHP5 + FastAdmin** 前台模板渲染设计，支持原生模板标签及 MultilingualCMS 扩展标签。

## 功能覆盖

### 原生 ThinkPHP5 标签
- 模板继承（`extend`）与布局（`layout`）
- 文件包含（`include`）
- 变量输出（`{$var}`）与系统变量
- 循环输出（`volist` / `foreach` / `for`）
- 比较标签（`eq` / `neq` / `gt` / `lt` 等）
- 条件判断（`if` / `switch` / `in` / `between`）
- 存在性判断（`present` / `empty` / `defined`）
- 定义标签（`assign` / `define`）

### MultilingualCMS 扩展标签（`tp:`）
- `{tp:cate}` 栏目信息
- `{tp:list}` 通用列表
- `{tp:banner}` 轮播图
- `{tp:nav}` 导航栏
- `{tp:prolist}` 产品列表
- `{tp:page}` 单页内容
- `{tp:position}` 面包屑
- `{tp:link}` 友情链接
- `{tp:ad}` 广告位
- `{tp:debris}` 碎片内容
- `{tp:search}` 搜索结果
- `{tp:prev}` / `{tp:next}` 上下篇

### 多语言支持
- 自动按 `{$lang}` 过滤内容
- 语言专属 CSS：`/static/lang/{$lang}/`
- 翻译函数：`{:__('key')}`
- URL 语言前缀：`{:multilingualcms_url('/')}`

## 安装

1. 下载打包文件 `thinkphp5-fastadmin-render.skill`
2. 在 OpenClaw 中安装技能：
   ```bash
   openclaw skills install thinkphp5-fastadmin-render.skill
   ```

## 快速开始

### 基础模板继承
```html
{extend name="layout/default" /}
{block name="title"}我的页面{/block}
{block name="main"}
  {tp:banner name="v" tag_name="index" limit="5"}
    <img src="{$v.image}" alt="{$v.title}">
  {/tp:banner}
{/block}
```

### 栏目列表
```html
{tp:list name="v" id="3" limit="8"}
  <div class="item">
    <h3><a href="{$v.url}">{$v.title}</a></h3>
    <p>{$v.abstract|default='暂无摘要'}</p>
  </div>
{/tp:list}
```

### 导航菜单
```html
{tp:nav name="v" limit="10"}
  <a href="{$v.url}" class="nav-item">{$v.name}</a>
  {if $v.childlist}
    <div class="dropdown">
      {volist name="v.childlist" id="child"}
        <a href="{$child.url}">{$child.name}</a>
      {/volist}
    </div>
  {/if}
{/tp:nav}
```

## 目录结构
```
thinkphp5-fastadmin-render/
├── SKILL.md              # 技能主文档
├── README.md             # 本文件
└── references/
    ├── layout.md         # 模板布局详解
    ├── extend.md         # 模板继承示例
    └── tp-tags.md       # tp: 标签库源码说明
```

## 后续优化
- [ ] 补充更多 MultilingualCMS 模板示例
- [ ] 添加 FastAdmin 后台模板标签支持
- [ ] 完善错误排查指南
- [ ] 增加常见页面模板（首页/列表页/详情页）

## 相关链接
- GitHub 仓库：https://github.com/xiaohe4966/fastadmin-render-skill
- OpenClaw 文档：https://openclaw.ai/docs
- ThinkPHP5 模板文档：https://www.kancloud.cn/manual/thinkphp5/125014

## 许可证
MIT License（待补充）
