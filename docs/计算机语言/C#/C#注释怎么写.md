# 注释怎么写

## 需要注意的事项：

1、不需要每一行都写
2、不能不写，命名规范一点可以少写

//xxxxx这种是最简单的，开发时候用
///这种需要看视频和演练，一般是库里用
【C# 注释的正确写法】 https://www.bilibili.com/video/BV1544y1Q7N5/?share_source=copy_web&vd_source=60808f5762b19efb9c467066990de8e3

## /// XML文档注释：

[建议的 XML 文档标记 - C# reference | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/xmldoc/recommended-tags)
【C# 注释的正确写法】 https://www.bilibili.com/video/BV1544y1Q7N5/?share_source=copy_web&vd_source=60808f5762b19efb9c467066990de8e3
这边简单举例：
`<remarks>` 标记用于添加有关某个类型或某个类型成员的信息，从而补充由 [](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/xmldoc/recommended-tags#summary) 指定的信息。 此信息显示在对象浏览器窗口中。 此标记可能包含更冗长的说明。 你可能会发现，将 `CDATA` 部分用于 Markdown 可以更方便地进行编写。 [docfx](https://dotnet.github.io/docfx/) 等工具在 `CDATA` 部分中处理 Markdown 文本。

```xml
<remarks>
    <para>
        这是一段注释，有<see langword="bool"/>, <see cref="customClass"/>
    </para>
</remarks>
```

