# 扩展 python-markdown

首先你要新加入一行 `markdown_extensions:`。

## Latex

我们可以使用 `pydownx.arithmatex` 插件：

```yaml hl_lines="2 3"
markdown_extensions:
  - pymdownx.arithmatex:
      generic: true
```

我们可以指定加载 $LaTeX$ 的位置：

```yaml hl_lines="1 2"
extra_javascript:
    - https://unpkg.com/mathjax@3/es5/tex-mml-chtml.js

markdown_extensions:
  - pymdownx.arithmatex:
      generic: true
```

## :warning: 警示

???+ question "效果展示"

    效果展示。

需要加入这些：

```yaml hl_lines="4 5 6"
markdown_extensions:
  - pymdownx.arithmatex:
      generic: true
  - admonition
  - pymdownx.details
  - pymdownx.superfences
```

然后你可以使用 `???` 创建一个**默认隐藏**警示：

```markdown title="示例"
??? question
    这是一个问题。
```
??? question
    这是一个问题。
