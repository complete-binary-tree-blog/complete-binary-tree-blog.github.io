# 构建教程

## Step 1 下载

首先你需要有一个 `python`。如果你没有，去[官网](https://python.org)下一个吧。

下载以后，打开终端，运行 `pip install mkdocs`。

> [!tips]
>
> 如果你下载失败了，可以尝试开个梯子。

## Step 2 新建项目

用终端打开你想要创建项目的路径，然后 `mkdocs new (文件夹名)`。

然后在 `路径/文件夹名` 下会出现：

```
+ docs
  - index.md
- mkdocs.ymal
```

此时 `cd 文件夹名`，然后运行 `mkdocs serve`，并访问 <https://localhost:8000>，然后你就可以看到文章页面了。

## Step 3 完善你的博客

### Step 3.1 新建文件

你可以在 `docs` 文件夹目录下新建文件。

新建文件会自动识别，保存的文件也会自动识别。

此时你刷新一下浏览器，你会发现多了一个文件。

### Step 3.2 分类文件

文件多了肯定要分类。

以下以此 `docs` 文件夹举例文件分类：

```
+ docs
  - index.md
  - About.md
  + Project
    - Project1.md
    - Project2.md
```

此时打开 `mkdocs.ymal`，找到 `nav:` 并替换为以下内容：

```
nav:
  - 主页: index.md
  - 关于: About.md
  - 项目:
    - 项目一: Project/Project1.md
    - 项目二: Project/Project2.md
```

然后重新加载浏览器，上方标签增加了：

```
| 主页 | 关于 | 项目 |
```

点击“主页”，就会来到 `index.md`。

点击“项目”，左边栏长这样：

```
项目
项目一
项目二
```

你还可以嵌套。如：

```
nav:
  - 1:
    - 2:
      - 333: 333.md
```

那么打开 `1` 以后会是这样：

```
1
2 v
  333
```

## Step 4 上传到 Github

### Step 4.1 Github 端

1. 创建一个 GitHub 账号。

2. 创建一个存储库。

    把存储库命名为 `你的用户名.gihub.io`。

3. 复制存储库 URL 以备复制。

    这个 URL 会以 `.git` 结尾。

### Step 4.2 本地

1. 随便找个有克隆功能的软件把这个存储库克隆下来。

    你可能需要 `git`，下载一个吧。

2. 
