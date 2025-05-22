# 构建教程

## 下载

首先你需要有一个 `python`。如果你没有，去[官网](https://python.org)下一个吧。

下载以后，打开终端，运行 `pip install mkdocs`。

~~恭喜你下载完了~~

## 新建项目

用终端打开你想要创建项目的路径，然后 `mkdocs new (文件夹名)`。

然后在 `路径/文件夹名` 下会出现：

```
+ docs
  - index.md
- mkdocs.ymal
```

此时 `cd 文件夹名`，然后运行 `mkdocs serve`，并访问 <https://localhost:8000>，然后你就可以看到文章页面了。