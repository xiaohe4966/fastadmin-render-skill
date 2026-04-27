---
name: thinkphp5-fastadmin-render
description: ThinkPHP5 + FastAdmin 前台模板渲染技能。当需要编写、修改、生成 ThinkPHP5 模板文件（.html）时触发，尤其是使用 FastAdmin 框架的前台页面渲染。覆盖场景：模板继承/布局、包含文件、变量输出、循环输出(volist/foreach/for)、比较标签(eq/neq/gt/egt/lt/elt/heq/nheq)、条件判断(if/switch/in/between/present/empty/defined)、标签嵌套、定义标签(assign/define)、资源加载。当用户说"渲染前台"、"写模板"、"写页面"、"ThinkPHP模板"、"FastAdmin页面"时使用。
---

# ThinkPHP5 + FastAdmin 前台模板渲染

面向 ThinkPHP5 模板引擎 + FastAdmin 框架的前台页面开发技能。

## 核心约定

- 模板标签定界符：`{` `}`，变量输出：`{$var}`
- `{` 和 `$` 之间**不能有空格**，否则标签无效
- 自闭合标签用 `/}` 结尾，如 `{include file="..." /}`
- 注释：`{/* 注释内容 */}`，不会在页面输出

## 模板结构三件套

按需选用，不要混用：

### 1. 模板继承（推荐）

基础模板定义区块：
```html
{block name="title"}默认标题{/block}
{block name="main"}默认内容{/block}
{block name="footer"}底部{/block}
```

子模板继承并重载区块：
```html
{extend name="base" /}
{block name="title"}{$title}{/block}
{block name="main"}内容{/block}
{block name="footer"}{__block__}附加内容{/block}
```

- `{__block__}` 引用父区块内容（合并而非覆盖）
- 子模板**只能定义区块**，其他内容直接忽略
- 支持多级继承

### 2. 模板布局

三种方式（详见 [references/layout.md](references/layout.md)）：
- 全局配置：`layout_on` + `layout_name`，用 `{__CONTENT__}` 占位
- 模板标签：`{layout name="layout" /}`
- 控制器动态：`$this->view->engine->layout(true)`

### 3. 包含文件

```html
{include file="public/header" /}
{include file="public/header,public/menu" /}
{include file="Public/header" title="$title" keywords="关键词" /}
```

- 传参用属性，被包含模板中用 `[title]` 接收
- **禁止循环包含**（A包含B，B又包含A）
- 包含文件中**不能再使用模板布局或继承**

## 变量输出

| 场景 | 写法 |
|------|------|
| 普通变量 | `{$name}` |
| 数组 | `{$data.name}` 或 `{$data['name']}` |
| 对象 | `{$data:name}` 或 `{$data->name}` |
| 系统变量 | `{$Think.server.script_name}` |
| 请求参数 | `{$Think.get.id}` / `{$Think.post.name}` |
| 使用函数 | `{$name\|md5}` / `{$create_time\|date='Y-m-d'}` |
| 默认值 | `{$name\|default='默认'}` |
| 运算 | `{$num+1}` / `{$num*2}` |
| 三元 | `{$status? 'yes' : 'no'}` |
| 原样输出 | `{literal}{$name}{/literal}` |

## 循环输出

### volist（数据集，最常用）

```html
{volist name="list" id="vo"}
  {$i}. {$vo.id}:{$vo.name}
{/volist}
```

| 属性 | 说明 | 示例 |
|------|------|------|
| name | 模板变量名 | `name="list"` |
| id | 循环变量 | `id="vo"` |
| offset | 起始偏移 | `offset="5"` |
| length | 输出条数 | `length="10"` |
| mod | 取模 | `mod="2"` |
| key | 循环变量名(默认i) | `key="k"` |
| empty | 空数据提示 | `empty="暂无数据"` |

```html
<!-- 分页截取 -->
{volist name="list" id="vo" offset="5" length="10"}...{/volist}
<!-- 偶数行 -->
{volist name="list" id="vo" mod="2"}
  {eq name="mod" value="1"}{$vo.name}{/eq}
{/volist}
<!-- 动态数据源 -->
{volist name=":fun('arg')" id="vo"}{$vo.name}{/volist}
```

### foreach（简洁遍历）

```html
{foreach $list as $vo}
  {$vo.id}:{$vo.name}
{/foreach}
<!-- 或 -->
{foreach name="list" item="vo" key="k"}
  {$k}|{$vo}
{/foreach}
```

### for（数字循环）

```html
{for start="1" end="100"}{$i}{/for}
```
默认 step=1, comparison=lt, name=i

## 比较标签

统一格式：`{标签名 name="变量" value="值"}内容{/标签名}`

| 标签 | 含义 |
|------|------|
| eq / equal | 等于 |
| neq / notequal | 不等于 |
| gt | 大于 |
| egt | 大于等于 |
| lt | 小于 |
| elt | 小于等于 |
| heq | 恒等于(===) |
| nheq | 不恒等于(!==) |

```html
{eq name="vo.name" value="5"}{$vo.name}{/eq}
{eq name="vo.name" value="5"}相等{else/}不相等{/eq}
{eq name="vo:name" value="$a"}{$vo.name}{/eq}
<!-- 通用compare -->
{compare name="name" value="5" type="eq"}value{/compare}
```

## 条件判断

### if / elseif / else

```html
{if condition="($name == 1) OR ($name > 100)"}value1
{elseif condition="$name eq 2"/}value2
{else /}value3
{/if}
```

### switch

```html
{switch name="Think.get.type"}
  {case value="gif\|png\|jpg"}图像格式{/case}
  {case value="$typeVar"}自定义{/case}
  {default /}其他
{/switch}
```

### 范围判断

```html
{in name="id" value="1,2,3"}在范围内{else/}不在{/in}
{notin name="id" value="1,2,3"}不在范围内{/notin}
{between name="id" value="1,10"}区间内{else/}区间外{/between}
{range name="id" value="1,2,3" type="in"}...{/range}
```

### 存在性判断

```html
{present name="name"}已赋值{else/}未赋值{/present}
{empty name="name"}为空{else/}不为空{/empty}
{defined name="NAME"}常量已定义{else/}未定义{/defined}
```

## 标签嵌套

所有内置标签都支持多层嵌套：

```html
{volist name="list" id="vo"}
  {volist name="vo['sub']" id="sub"}
    {$sub.name}
  {/volist}
{/volist}
```

可嵌套标签：volist、switch、if、elseif、else、foreach、compare（及所有比较标签）、present/empty/defined 等。

## 定义标签

```html
{assign name="var" value="123" /}
{assign name="var" value="$Think.get.name" /}
{define name="MY_DEFINE_NAME" value="3" /}
```

## 资源文件加载

```html
{load href="/static/css/style.css" /}
{load href="/static/js/main.js" /}
{css href="/static/css/style.css" /}
{js href="/static/js/main.js" /}
```

## FastAdmin 特有约定

- 模板目录：`application/index/view/`（前台）/ `application/admin/view/`（后台）
- 公共模板：`application/index/view/common/` 或 `public/`
- 静态资源：`/assets/` 或 `/static/`
- 后台模板多用 `Table` + `Form` JS 组件，前台模板按需使用
- 控制器赋值：`$this->view->assign('key', $value)` 或 `$this->assign('key', $value)`

## MultilingualCMS 自定义标签（tp:）

MultilingualCMS 扩展了 `tp:` 命名空间的自定义标签库（`application/common/library/Tp.php`），用于前台 CMS 模板渲染。

### {tp:cate} — 栏目信息（自闭合）

```html
{tp:cate id="1" type="name"}        <!-- 栏目名称 -->
{tp:cate id="1" type="byname"}      <!-- 栏目别名 -->
{tp:cate id="1" type="abstract"}    <!-- 栏目摘要 -->
{tp:cate id="1" type="url"}         <!-- 栏目URL -->
{tp:cate id="$cate.id" type="name"} <!-- 变量ID -->
```
- `id`：栏目ID，支持变量（如 `$cate.id`），不传则取 `input('catId')`
- `type`：返回字段（name/byname/abstract/url 等 cate 表字段）
- 自动处理多语言（`lang` 字段）和栏目复制（`copy_id`）

### {tp:list} — 通用列表（闭合）

```html
{tp:list name="v" id="3" limit="8"}
  <a href="{$v.url}">{$v.title}</a>
{/tp:list}
```
| 属性 | 说明 | 默认值 |
|------|------|--------|
| id | 栏目ID | `input('catId')` |
| name | 循环变量名 | `list` |
| limit | 限制条数(0=不分页) | `0` |
| pagesize | 每页条数 | `config('page_size')` |
| order | 排序 | `weigh DESC,id DESC` |
| where | 额外条件 | `switch = 1` |
| page | 页码 | `1` |
| total | 总数变量名 | `total` |

- 根据栏目ID自动查对应数据表
- 自动获取栏目及子栏目数据
- 返回数据经过 `changeFields()` 处理

### {tp:banner} — 轮播图（闭合）

```html
{tp:banner name="v" tag_name="index" limit="5"}
  <img src="{$v.image}" alt="{$v.bannermsa}">
{/tp:banner}
```
| 属性 | 说明 | 默认值 |
|------|------|--------|
| name | 循环变量名 | `banner` |
| tag_name | 标签筛选 | 空(全部) |
| limit | 条数 | `99` |
| order | 排序 | `weigh DESC,id desc` |

### {tp:nav} — 导航列表（闭合）

```html
{tp:nav name="v" id="1" limit="10"}
  <a href="{$v.url}">{$v.name}</a>
{/tp:nav}
```
| 属性 | 说明 | 默认值 |
|------|------|--------|
| name | 循环变量名 | `nav` |
| id | 父栏目ID | 空(全部) |
| limit | 条数 | `0`(全部) |

### {tp:page} — 单页内容（自闭合）

```html
{tp:page id="1" type="content"}
```
- 查 `multilingualcms_page` 表，`type` 为字段名

### {tp:position} — 面包屑导航（闭合）

```html
{tp:position name="pos" cate_id="$cate.id"}
  <a href="{$pos.url}">{$pos.name}</a>
{/tp:position}
```

### {tp:link} — 友情链接（闭合）

```html
{tp:link name="v" limit="10"}
  <a href="{$v.url}">{$v.name}</a>
{/tp:link}
```

### {tp:ad} — 广告（闭合）

```html
{tp:ad name="v" type="home_banner" id="1"}
  <img src="{$v.image}">
{/tp:ad}
```

### {tp:debris} — 碎片内容（自闭合）

```html
{tp:debris name="contact_info" type="content"}
```
- 按 `name` 查 `debris` 表，返回指定 `type` 字段

### {tp:prolist} — 产品列表（闭合）

```html
{tp:prolist name="v" cate_id="2,3" limit="8" random="true"}
  <a href="{$v.url}">{$v.title}</a>
{/tp:prolist}
```
| 属性 | 说明 | 默认值 |
|------|------|--------|
| cate_id | 栏目ID(逗号分隔) | 空 |
| name | 循环变量名 | `list` |
| limit | 条数 | `50` |
| order | 排序 | `weigh DESC,id DESC` |
| random | 随机排序 | `true` |
| where | 额外条件 | `switch = 1` |

### {tp:search} — 搜索列表（闭合）

```html
{tp:search search="$keyword" table="article" name="v" pagesize="10"}
  <a href="{$v.url}">{$v.title}</a>
{/tp:search}
```

### {tp:prev} / {tp:next} — 上/下一篇（自闭合）

```html
{tp:prev /}
{tp:next /}
```
- 自动根据当前详情页 `catId` 和 `id` 查找上下篇
- 输出带链接的 HTML `<a>` 标签

## MultilingualCMS 多语言机制

- 当前语言变量：`{$lang}`（如 `zh-cn`、`en`）
- 多语言翻译函数：`{:__('name')}`，对应 `application/index/lang/` 下的语言文件
- 语言专用CSS：`/static/lang/{$lang}/style.css`、`/static/lang/{$lang}/main.css`
- 数据查询自动按 `lang` 字段过滤（`getDomainLang()` 函数）
- URL生成：`{:multilingualcms_url('/')}` 保持语言前缀
- SEO独立控制：每个栏目可单独设置 `no_follow_switch` / `no_index_switch`
- 站点配置读取：`{:dbconfig('site_name')}` 直接从 config 表取值

## MultilingualCMS 模板目录结构

```
application/multilingualcms/view/
├── index/                  # 前台主模板
│   ├── index.html          # 首页
│   ├── show.html           # 详情页
│   ├── about.html          # 关于页
│   ├── contact.html        # 联系页
│   ├── product.html        # 产品页
│   ├── recruit.html        # 招聘页
│   ├── public/             # 公共模板片段
│   │   ├── top.html        # <head>头部(meta/css)
│   │   ├── nav.html        # 导航栏
│   │   ├── footer.html     # 底部
│   │   ├── page_num.html   # 分页
│   │   ├── position.html   # 面包屑
│   │   ├── likearticle.html # 相关文章
│   │   ├── likepro.html    # 相关产品
│   │   ├── prvnext.html    # 上下篇
│   │   ├── cookie_popup.html # Cookie弹窗
│   │   └── nocopy.html     # 禁止复制
│   └── file.html           # 文件下载
├── index11/                # 备用模板组
└── ...
```

## 详细参考

- 模板布局三种方式完整示例：[references/layout.md](references/layout.md)
- 模板继承完整示例：[references/extend.md](references/extend.md)
- MultilingualCMS 自定义标签库源码说明：[references/tp-tags.md](references/tp-tags.md)
