---
layout: page
title: 全域變數
lang: zh-TW
date: 2012-11-01 18:13:30
---

Hexo 在初始化時，會建立一個名為`hexo`的命名空間（Namespace），此命名空間內擁有以下唯讀變數。

- **base_dir** - 網站根目錄
- **public_dir** - 靜態檔案目錄（public）
- **source_dir** - 原始檔目錄（source）
- **theme_dir** - 主題目錄（theme/theme_name）
- **plugin_dir** - 外掛目錄（node_modules）
- **version** - Hexo 版本號
- **env** - 執行環境
- **config** - [全域設定][1]，即`_config.yml`的內容
- **[extend](#extend)** - 擴充功能
- **[util](#util)** - 工具程式
- **[render](#render)** - 渲染模組
- **[i18n](#i18n)** - 國際化（i18n）模組
- **[route](#route)** - 路由模組

<a id="extend"></a>
### extend

extend是負責處理所有擴充套件的模組，每個物件都有兩種方法：**list** 和 **register**，前者可列出該物件所掌管的所有擴充套件，後者可掛載新的擴充套件到該物件上。

#### generator

- **list** - 返回一個陣列（Array）
- **register(fn)** - 掛載擴充套件

#### renderer

- **list** - 返回一個物件（Object），物件內的元素擁有`output`屬性。
- **register(name, output, fn, sync)** - 掛載擴充套件。`name`為擴充套件的名稱，`output`為輸出後的副檔名，`sync`決定擴充套件是否可同步執行（預設為否）。

#### tag

- **list** - 返回一個物件（Object）
- **register(name, fn, ends)** - 掛載擴充套件。`name`為擴充套件的名稱，`ends`決定該擴充套件是否擁有結尾標籤（End tag），預設為`false`

#### deployer

- **list** - 返回一個物件（Object）
- **register(name, fn)** - 掛載擴充套件。`name`為擴充套件的名稱。

#### processor

- **list** - 返回一個陣列（Array）
- **register(fn)** - 掛載擴充套件

#### helper

- **list** - 返回一個物件（Object）
- **register(name, fn)** - 掛載擴充套件。`name`為擴充套件的名稱。

#### console

- **list** - 返回一個物件（Object），物件內的元素擁有`description`屬性。
- **register(name, desc, fn)** - 掛載擴充套件。`name`為擴充套件的名稱，`desc`為擴充套件的描述。

#### migrator

- **list** - 返回一個物件（Object）
- **register(name, fn)** - 掛載擴充套件。`name`為擴充套件的名稱。

更多資訊請參考 [外掛開發][2]。

<a id="util"></a>
### util

util為工具程式，包含下列模組：

#### file

用以操作檔案，擁有以下方法：

- **mkdir(dest, callback)**
- **write(dest, content, callback)**
- **copy(src, dest, callback)**
- **dir(src, callback)**
- **read(src, callback)**
- **readSync(src, callback)**
- **empty(target, exclude, callback)**

#### highlight(string, options)

用以輸出Highlight程式區塊。以下為選項：

- **gutter** - 顯示行號
- **first_line** - 起始行號
- **lang** - 語言
- **caption** - 程式區塊說明

#### titlecase(string)

用以將字串轉為適合的標題大小寫。

#### yfm(string)

用以解析 [YAML Front Matter][3]，輸出一個物件（Object），本文存放於`_content`屬性。

<a id="render"></a>
### render

render函數分別擁有兩種方法，**render** 和 **compile**，前者為輸入字串後渲染，後者為讀取檔案後渲染，兩種方法都有同步和非同步兩種模式。

**render(string, ext, [locals], callback)**  
**renderSync(string, ext, [locals])**

- **string** - 輸入字串
- **ext** - 渲染引擎的副檔名
- **locals** - 區域變數
- **callback** - 回傳函數

**compile(src, locals, callback)**  
**compileSync(src, locals)**

- **src** - 原始檔案路徑
- **locals** - 區域變數
- **callback** - 回傳函數

<a id="i18n"></a>
### i18n

i18n為處理國際化（Internationalization）的模組，使用方式如下：

``` js
var i18n = new hexo.i18n();
```

i18n物件擁有以下方法：

#### get

第一引數必須為語言檔的鍵值，其後的引數則會使用 [util.format][4] 處理；若第一引數為陣列（Array），則會根據第二引數的數值判斷，當第一引數的陣列長度為2時，第二引數若大於1則採用陣列的第二成員，否則採用第一成員；若第一引數的陣列長度為3時，第二引數若大於1則採用陣列的第三成員，為0時則採用第一成員，否則採用第二成員。

#### set(key, value)

- **key** - 鍵值
- **value** - 對應值

#### list([obj])

若`obj`未定義，則傳回物件內的所有數值；若`obj`為一物件（Object），則將物件取代為傳入的引數。

#### load(path, callback)

若不想自行使用`set`函數設定國際化的話，可用此函數自動載入語言檔案。`path`為放置語言檔案的資料夾，Hexo會根據`_config.yml`的`language`設定載入相對應的語言檔案，若找不到語言檔案的話，則會載入`default.yml`，因此資料夾內至少要有一個`default.yml`。

<a id="route"></a>
### route

自從0.3版之後，Hexo開始引入路由模組處理網站的所有檔案路徑。

#### get(path)

取得路徑內容，傳回一個函數。

#### set(path, content)

設定路徑內容。`content`可為函數或其他內容，若為函數則必須使用`function(err, content)`格式。

#### format(path)

處理路徑格式。若路徑為空或結尾為`/`，則在最後加入`index.html`。

#### destroy(path)

刪除路徑。

#### list()

返回一個物件（Object）。

[1]: configure.html
[2]: plugin-development.html
[3]: https://github.com/mojombo/jekyll/wiki/YAML-Front-Matter
[4]: http://nodejs.org/api/util.html#util_util_format_format