# MultilingualCMS 自定义标签库（tp:）

源码位置：`application/common/library/Tp.php`，继承 `think\template\TagLib`。

## 标签注册

```php
protected $tags = array(
    'close'     => ['attr' => 'time,format', 'close' => 0],          // 闭合标签演示
    'open'      => ['attr' => 'name,type', 'close' => 1],            // 非闭合标签演示
    'nav'       => ['attr' => 'id,limit', 'close' => 1],             // 导航
    'cate'      => ['attr' => 'id,type','close' => 0],               // 栏目信息
    'position'  => ['attr' => 'name,cate_id','close' => 1],          // 面包屑
    'link'      => ['attr' => 'name','close' => 1],                  // 友情链接
    'ad'        => ['attr' => 'name,type','close' => 1],             // 广告
    'debris'    => ['attr' => 'name,type','close' => 0],             // 碎片
    'list'      => ['attr' => 'id,name,pagesize,where,limit,order,total','close' => 1], // 通用列表
    'search'    => ['attr' => 'search,table,name,pagesize,where,order','close' => 1],    // 搜索
    'prev'      => ['attr' => 'len','close' => 0],                   // 上一篇
    'next'      => ['attr' => 'len','close' => 0],                   // 下一篇
    'page'      => ['attr' => 'id,type', 'close' => 0],              // 单页
    'prolist'   => ['attr' => 'cate_id,name,pagesize,where,limit,order,random','close' => 1], // 产品列表
    'banner'    => ['attr' => 'tag,limit,order', 'close' => 1],      // 轮播图
);
```

## 实现原理

所有闭合标签（close=1）内部都编译为 `{volist}` 循环：

```
{tp:list name="v" id="3"}
  {$v.title}
{/tp:list}
```

编译后等价于：

```php
<?php
$__CATE__ = \think\Db::name("multilingualcms_cate")->where(...)->find();
// ... 查询数据到 $__LIST__
?>
{volist name="__LIST__" id="v"}
  {$v.title}
{/volist}
```

自闭合标签（close=0）直接 `echo` 输出。

## 多语言过滤

所有标签查询都自动加 `where('lang', getDomainLang())` 条件，按当前域名/语言过滤数据。

## 关键辅助函数

| 函数 | 说明 |
|------|------|
| `getDomainLang()` | 获取当前语言标识 |
| `getCateUrl($cate)` | 获取栏目URL |
| `getShowUrl($item)` | 获取详情页URL |
| `getCateChildrenIds($ids)` | 获取栏目及子栏目ID |
| `get_nav_list($pid, $limit, $lang)` | 获取导航列表 |
| `changeFields($list, $cateId)` | 处理列表字段（关联字段转数组） |
| `dbconfig('key')` | 从 config 表读取配置 |
| `multilingualcms_url($path)` | 生成带语言前缀的URL |

## 数据库表

| 表名 | 说明 |
|------|------|
| `multilingualcms_cate` | 栏目表（含 lang, copy_id, table_name, parent_id） |
| `multilingualcms_page` | 单页内容表 |
| `multilingualcms_news` | 新闻/文章表 |
| `multilingualcms_pro` | 产品表 |
| `banner` | 轮播图表 |
| `link` | 友情链接表 |
| `debris` | 碎片内容表 |
| `ad` / `ad_type` | 广告及广告类型表 |
