---
title: 写一个Chrome extensions存储标签栏网页至书签
tags: 技术
---

<!--more-->

我一直在浏览器中开过多页面和打开浏览器保持上次浏览页面的习惯，并且喜欢把过多的页面按照当日日期保存到书签中再关闭，但这样操作步骤非常麻烦：按下Ctrl+Shift+D，选择文件夹，更改文件夹名为日期，点击确定，依次关闭所有标签栏。

查找了一些扩展程序都不太想用，因为很多都没有直接存入书签中。我认为保存到书签中是最安全最不容易丢失的方案，所以直接自己写一个简单的插件好了。

# 配置

Chrome extensions的开发步骤：[创建和发布自定义 Chrome 应用和扩展程序](https://support.google.com/chrome/a/answer/2714278?hl=zh-Hans)

首先按照Google的文档导入一个最简单的页面：[Hello World 扩展程序  \|  Extensions  \|  Chrome for Developers](https://developer.chrome.com/docs/extensions/mv3/getstarted/development-basics)

# 功能

首先不考虑扩展性，把基本功能先写完，思考一下要用到什么API，要读取并关闭所有标签栏，并且把信息存入书签，所以需要用到tabs和bookmarks的API，查询文档后发现v3版本支持promise写法，继续仔细阅读一下文档并且写一写代码，这样基本流程就可以跑通了。

```js
document.getElementById("getButton").addEventListener("click", async () => {
  const queryOptions = { lastFocusedWindow: true };
  const tabs = await chrome.tabs.query(queryOptions);

  const extensionsFolderTitle =
    document.getElementById("folderNameInput").value;
  const data = new Date().toISOString().slice(0, 10);

  const tree = await chrome.bookmarks.getTree();
  const extensionsFolder = tree[0].children[0].children.find(
    (child) => child.title === extensionsFolderTitle
  );
  const currentExtensionsFolder =
    extensionsFolder == null
      ? await chrome.bookmarks.create({
          parentId: tree[0].children[0].id,
          title: extensionsFolderTitle,
        })
      : extensionsFolder;

  let currentDate = data,
    index = 1;
  while (
    currentExtensionsFolder.children.find(
      (child) => child.title === currentDate
    )
  ) {
    currentDate = data + `-${index}`;
    index++;
  }
  const currentDateFolder = await chrome.bookmarks.create({
    parentId: currentExtensionsFolder.id,
    title: currentDate,
  });
  for (const tab of tabs) {
    await chrome.bookmarks.create({
      parentId: currentDateFolder.id,
      title: tab.title,
      url: tab.url,
    });
  }
  chrome.tabs.create({ active: true });
  await chrome.tabs.remove(tabs.map((t) => t.id));
});

```

# 样式

没怎么写过前端QAQ，为了尽可能简单用Bootstrap随便写写好了（不要学我）。

```html
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Bootstrap demo</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-T3c6CoIi6uLrA9TneNEoa7RxnatzjcDSCmG1MXxSR1GAsXEV/Dwwykc2MPK8M2HN" crossorigin="anonymous">
    <style>
      body {
        width: 280px;
        height: 200px;
      }
    </style>
  </head>
  <body>
    <div class="container mt-3">
      <div class="row">
        <div class="col text-center">
          <h1>Save Tabs Extension</h1>
        </div>
      </div>  
      <div class="mb-3">
        <label for="folderNameInput" class="form-label">Bookmark Folder name</label>
        <input type="text" class="form-control" id="folderNameInput" placeholder="Enter the folder name." value="Timeline"">
      </div>
      <div class="row">
        <div class="col text-center">
          <button type="button" class="btn btn-primary" id="getButton">Save</button>
        </div>
      </div>
    </div>
    <script src="popup.js"></script>
  </body>
</html>

```

