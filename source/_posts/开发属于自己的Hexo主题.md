---
title: 开发属于自己的Hexo主题
date: 2035-11-09 16:35:45
tags:
---

# 思路：

## 来到自己的博客，安装脚手架和更新依赖

```bash
npm install -g hexo-cli
npm install
```

## 创建一个新的主题文件夹

```bash
cd themes
mkdir my-theme
cd my-theme
```

## 创建主题结构

```
my-theme/
├── layout/
│   ├── _partial/
│   ├── index.ejs
│   ├── post.ejs
│   └── ... (其他布局文件)
├── source/
│   ├── css/
│   ├── js/
│   └── images/
├── _config.yml
└── package.json
```

## 编辑主题的 _config.yml

### 在 my-theme 文件夹中创建 _config.yml 文件

### 这个文件用于配置主题设置，例如：

```yaml
name: my-theme
version: 1.0.0
description: A custom theme for Hexo
author: Your Name
```

## 编辑主题的 package.json

### 在 my-theme 文件夹中创建 package.json 文件，用于管理主题的依赖关系：

```json
{
  "name": "my-theme",
  "version": "1.0.0",
  "description": "A custom theme for Hexo",
  "main": "index.js",
  "dependencies": {}
}
```

## 创建布局文件

### 在 layout 文件夹中创建基本的布局文件，例如 index.ejs 和 post.ejs，这些文件定义了首页和文章页面的结构。

```html
<!DOCTYPE html>
<html>
<head>
    <title><%= config.title %></title>
    <link rel="stylesheet" href="<%= url_for('css/style.css') %>">
</head>
<body>
    <header>
        <h1><%= config.title %></h1>
    </header>
    <main>
        <%- partial('_partial/posts.ejs') %>
    </main>
    <footer>
        <p>© <%= new Date().getFullYear() %> Your Name</p>
    </footer>
</body>
</html>
```

## 自定义 CSS 和 JS

### 在 source/css 和 source/js 文件夹中添加自定义样式和脚本文件。例如，可以创建 style.css 文件并添加一些基本样式：

```css
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
}

header {
    background-color: #4CAF50;
    color: white;
    padding: 10px;
    text-align: center;
}
```

## 更新 Hexo 配置文件

### 在 Hexo 根目录下的 _config.yml 文件中，将主题设置为自己创建的主题：

```yaml
theme: my-theme
```

## 运行 Hexo

### 回到 Hexo 根目录，运行以下命令生成和启动博客：

```bash
hexo clean
hexo generate
hexo server
```
