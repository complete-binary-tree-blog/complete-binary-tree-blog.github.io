# 使用 material 主题让你的博客焕然一新

mkdocs-material 是一个基于 mkdocs 的强大主题，它能实现搜索、亮暗色模式切换等功能。

[仓库页面](https://github.com/squidfunk/mkdocs-material)

[文档/示例页面](https://squidfunk.github.io/mkdocs-material/)

## 下载

因为它基于 python，所以你可以直接：

```powershell
pip install mkdocs-material
```

## 使用

打开 `mkdoks.yml`，加入以下内容：

```yaml
theme: 
  name: material
```

保存并刷新页面，此时你发现你的博客变得有些不同了！

## material 支持的功能

只需在 `mkdocs.yml` 的 `theme:` 中加入一些字段，便可开启如页脚、亮暗色切换等功能。

### 设置语言

当你用浏览器打开博客的时候，明明博客是中文但是每次都会弹出一个翻译的框，令人难受。

所以我们可以在 `theme:` 中加入一行：

```yaml
theme:
  language: zh # 设置为中文
```

这样浏览器自动识别为中文，就不需要翻译了。

???+ Warning

    **注意**~~由于 Python 特性~~，同一个 `yaml` 文件中出现多次同一名字，会**以最后一个为准**。

    如以下代码：

    ```yaml
    theme:
      name: material
      language: zh
    
    theme:
      name: material
      features:
        - content.code.copy
    ```

    相当于只有后面那个 `theme` 有效，即相当于代码中只有

    ```yaml
    theme:
      name: material
      features:
        - content.code.copy
    ```

### 标签

在 `mkdocs` 原版中，你会发现在顶部会有一些标签。

但是在 `material` 中，你会发现它不见了。

我们可以在 `theme:` 中加入以下字段来打开它：

```yaml
theme:
  features:
    - navigation.tabs
```
