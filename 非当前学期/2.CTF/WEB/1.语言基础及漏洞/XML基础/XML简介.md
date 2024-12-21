我们的 XML 是一种**可扩展标记语言**，类似于我们的 HTML，本意适用于传输数据。

与 HTML 相比，XML 有哪些优势？

HTML 标记是固定的，每个网站开发者都使用相 $f$ 局的标签来做相同的事情，
如：`<head><title></title><head><body>k/body>` 相比之下，XML 允许创建自己的标签来标记数据。如：` <money>100</money><name>huasheng</name> XML `的灵活性有很多好处，它允许在公司数据库和网站之间传输数据，而不会丢失关键的描述性信息。其次，XML 会使搜索更加高效，因为搜索引擎可以通过标签定位而不是长文本进行搜索。

XML 本身只是一个标记语言，只有我们的**后端对对应的代码进行处理**XML 才会生效**类似于我们的 markdown**。

## DTD 文档：
Dtd 的作用是定义 XML 文档的合法构成模块，DTD 可以在 XML 文档内部声明，也可以在外部引用。
也就是说，我们的 DTD 是一个 XML 文档的约束规则。用于规范我们的 XML 文档的使用。
1. 内部声明 DTD：<!DOCTYPE 根元素[元素声明]>
2. 外部引用 DTD：<!DOCTYPE 根元素 SYSTEM"外部DTD的url">
3. 引用公共 DTD：<! DOCTYPE 根元素 PUBLIC "DTD 标识名""公用的 DTD的 URL">

## 语法：
```xml
<?xml version="1.0" encoding=utf-8?>
<updateProfile>
	<firstname>Joe</firstname>
	<secondname>&fine;</lastname>
</updateProfile>
```

根据我们的类型划分，我们的实体划分为一般实体和参数实体，一般实体通过 `&aa` 来进行声明，而我们的参数实体通过我们的 `%aaa` 来声明。

只有在 DTD 文件中，参数实体的声明才能引用其他实体。和通用实体一样，参数实体也可以外部引用。

我们的引用有两种，一种是引用我们本身的文档，另一种则是引用我们的外部的文档作为本身的参数。

例如：
```
<!Entitny writer "Bill Gates">
&writer

<!Entity writer system "https://www.xxx.com/dtd/entities.dtd">
```

通过我们的引用, 我们可以对我们的文档进行自动更新，非常方便。
