---
layout: post
title: "Jekyll 中的配置和模板語法"
categories: jekyll
---

**此中文配置說明為 [biezhi][jekyll-guide] 發表於GitHub Gist網站上。**

---

## 配置

### 文件介紹

**_config.yml**

Jekyll 的全局配置文件。  
比如網站的名字，網站的域名，網站的鏈接格式等等。

**_drafts**

未發布的posts 存放的地方，這裡的post 文件名不需要加日期標記。

**_includes**

該目錄下的文件內容是最終要放進模版文件中的一些代碼片段。  
對於網站的頭部，底部，側欄等公共部分，為了維護方便，我們可能想提取出來單獨編寫，然後使用的時候包含進去即可。  
這時我們可以把那些公共部分放在這個目錄下，使用時只需要引入即可。

```ruby
{ % include filename % }
```

**_layouts**

存放一些模版文件，模版是用來包含並裝飾page 或post 內容的。Jekyll 的模版使用HTML 語法來寫，並包含[YAML](http://en.wikipedia.org/wiki/Yaml "一個幾乎所有編程語言都支持的易讀的數據序列化（ Serialization ) 標準") Front Matter。  
所有的模版都可用Liquid 來與網站進行交互，都可以使用全局變量site 和page 。

site 變量: 包含該網站所有可以接觸得到的內容和元數據(meta-data)  
page 變量: 包含的是當前渲染的page或post的所有可以接觸得到的數據

對於網站的佈局，一般會寫成模板的形式，這樣對於寫實質性的內容時，比如文章，只需要專心寫文章的內容，然後加個標籤指定用哪個模板即可。  
對於內容，指定模板了模板後,我們可以稱內容是模板的兒子。  
為什麼這樣說呢？因為這個模板時可以多層嵌套的，內容實際上模板，只不過是葉子節點而已。

在模板中，引入兒子的內容：

```
{ { content } }
```

在兒子中，指定父節點模板：

> 注意，必須在子節點的頂部。

```markdown
---
layout: post
---
```

**_posts**

發布的內容，比如博客文章，常放在這裡面，而且一般作為葉子節點。  
文件的命名必須遵循：`YEAR-MONTH-DAY-title.markdown`。

另外，所有放在根目錄下並且不以下劃線開頭的文件夾中有格式的文件都會被Jekyll 處理成page。  
這裡說的有格式是指含有YAML Front Matter 頭部的文件。

所有的post 和page 都要用markdown 或者texile 或者HTML 語法來寫，可以包含Liquid 模版的語法。而且必須要有YAML Front Matter 頭部( Jekyll 只處理具有YAML Front Matter 的文件)。

YAML Front Matter 必須放在文件的開頭，一對`---`之間，用戶可在這一對`---`間設置預先定義的變量或用戶自己的數據。具體看下面的[說明](#frontmatter)。

**_data**

Jekyll 支持從該目錄中加載YAML、 JSON、 和CSV 格式（`.yml`、`yaml`、`json`或`csv`擴展名）的文件數據。用於配置一些全局變量，不過數據比較多，所以放在這裡。  
比如，多人參與網站開發，我們通常會在這裡面定義一個*members.yml*文件。

例如文件內容為：

```
- name: Tom Preston-Werner
  github: mojombo

- name: Parker Moore
  github: parkr
```

然後在模板中我們就可以通過`site.data.members`(注意：文件名決定變量名)使用這些數據。

```
<ul>
{ % for member in site.data.members % }
  <li>
    <a href="https://github.com/{ { member.github } }">
      { { member.name } }
    </a>
  </li>
{ % endfor % }
</ul>
```

**_site**

Jekyll 生成的網頁默認輸出的地方，一般需要在`.gitignore`中屏蔽掉這個目錄。

**index.html**

主頁文件，後綴有時也用`index.md` 等。  
這個需要根據自己的需要來寫，因為不同的格式之間在某些情況下還是有一些細微的差別的。

**其他靜態資源**

對於其他靜態資源，可以直接放在根目錄或任何其他目錄，然後路徑和平常的網站一樣，按路徑來找鏈接中的文件。

## 配置全局變量

雖然全局變量都有自己的[默認配置](http://jekyllrb.com/docs/configuration/)，但是我們往往會手動配置為自己心中最好的效果。  
另外，一些全局變量既可以在配置文件中設置，也可以在命令行選項參數里指定。

> 注意，配置不用使用tab，否則可能會忽略那條命令。

### 源代碼的位置

這個一般不配置，默認即可。

```
source: DIR
```

當然編譯的時候也可以指定，但是使用github 我們是不能指定參數的。

```
-s, --source DIR
```

### 輸出網站位置

這個一般也是默認。

```
# 編譯參數-d, --destination DIR
destination: DIR #配置語法
```

### Safe 開關

官方文檔上就一句話：

```
Disable custom plugins, and ignore symbolic links.
```

大概意思是禁用常用的插件，忽略符號鏈接。

```
# 編譯參數--safe
safe: BOOL
```

### Base URL

將Jekyll 生成的網站內容放置在域名的子路徑下面。

我們經常需要在多個地方運行Jekyll，如發佈到GitHub Pages 前在本地預覽下網站。`--baseurl`標記就是為此而生的。

首先在`_config.yml`文件添加`baseurl`，然後在網站各個鏈接的地方加前綴`{{site.baseurl}}`。  
當`jekyll serve`預覽本地網站時，在本地域名後追加`--baseurl`的值（類似`/`這樣的路徑）。

```
# baseurl: "/blog" 編譯參數: --baseurl URL
baseurl: URL
```

注意：所有的page 和post 的URL 都是以斜線`/`領頭，那麼當`site.baseurl=/`時，連接的URL 會出現兩個重複的`/`而破壞超鏈接。因此，建議只在`baseurl`有具體值時才使用`site.baseurl`給URL 加前綴。

### 忽略文件

這個很有用，有時候你寫了一個文件，裡面的一個東西可能會被Jekyll 處理，但是你不想讓Jekyll 處理的話，就使用這個語法忽略那些文件。

```
exclude: [DIR, FILE, ...]
```

### 強制處理文件

有時候我們的一些文件的名字由於不在Jekyll 處理的文件名字範圍內，這時候就需要強制處理這些文件了。比如`.htaccess`文件。

```
# include: [".htaccess"]
include: [DIR, FILE, ...]
```

### 時區

模板中經常會對時間進行轉換，這個時候如果至指定時區的話，可能得到的時間會和我們想要的時間錯幾個小時。在[這裡](http://en.wikipedia.org/wiki/List_of_tz_database_time_zones) 能查看到可用的值。

```
# timezone: Asia/Shanghai
timezone: TIMEZONE
```

### 編碼

大家都是程序員，就不用多說了。  
執行`ruby-e'puts Encoding::list.join("\n")'`可列出本地Ruby 可用的編碼。

```
# encoding : utf-8
encoding: E​​NCODING
```

### 文章鏈接格式

我們可以自定義Post 的URL 格式，通過`permalink` 來配置。比如：

```
# 設定permalink 取值
permalink: /:year/:month/:day/:title.html
# 那麼post: 2014-10-19-slap-chop.md 最終輸出的路徑URL 變成
# /2014/10/19/slap-chop.html

permalink: /:month-:day-:year/:title # => /04-29-2009/slap-chop/index.html

permalink: pretty # => /2009/04/29/slap-chop/index.html
```

另外，還有3種內置的鏈接格式可以使用：

| PERMALINK STYLE | URL TEMPLATE |
| --------------- | --------------------------------- ----------- |
| **date** | `/:categories/:year/:month/:day/:title.html` |
| **pretty** | `/:categories/:year/:month/:day/:title/` |
| **none** | `/:categories/:title.html` |

### 分頁

通常我們都不想把所有的Post 都放在一頁顯示，而是要分成多頁顯示。好在Jekyll 支持分頁顯示，通過`paginate`和`paginate_path`兩個變量來設置每頁顯示的數量和分頁HTML 的URL 路徑。

```
paginate: 5
paginate_path: "blog/page:num/"
```

根據上面的配置，Jekyll 會讀取`blog/index.html`文件，把每頁賦值給全局變量`paginator`，並輸出HTML 分頁文件，如：第2頁為`blog/page2/index.html` 。其中，“page” 是字符常量，變量`:num`是分頁的頁碼，自動**從2 開始編碼**，因此在羅列所有頁面頁碼及它們的超鏈接時要注意一下，首頁鏈接沒有中間的“page1” 路徑。  
通過`paginator`的[**相關屬性**](http://alfred-sun.github.io/atom.xml#pagination)我們可以實現在不同頁間切換。

```
<!-- This loops through the paginated posts -->
{% for post in paginator.posts %}
  <h1><a href="http://alfred-sun.github.io/blog/2015/01/10/jekyll-liquid-syntax-documentation/{{ post.url }}">{{ post.title }}</a></h1>
  <p><span class="date">{{ post.date }}</span></p>
  <div class="content">
    {{ post.content }}
  </div>
{% endfor %}

<!-- Pagination links -->
<div class="pagination">
  {% if paginator.previous_page %}
    <a href="http://alfred-sun.github.io/blog/2015/01/10/jekyll-liquid-syntax-documentation/{{ paginator.previous_page_path }}" class="previous">Previous</ a>
  {% else %}
    <span class="previous">Previous</span>
  {% endif %}
  <span class="page_number ">Page: {{ paginator.page }} of {{ paginator.total_pages }}</span>
  {% if paginator.next_page %}
    <a href="http://alfred-sun.github.io/blog/2015/01/10/jekyll-liquid-syntax-documentation/{{ paginator.next_page_path }}" class="next">Next</ a>
  {% else %}
    <span class="next ">Next</span>
  {% endif %}
</div>
```

### 默認值設定

設置YAML Front Matter 變量的默認值。  
寫post 時，經常需要在每篇文章的YAML 頭部設定一些相同的變量使用，比如：`author`、`layout`等。為了避免這些不必要的重複性操作，我們可以在配置文件中設定他們的默認值；  
同時在必要的時候，還可以對具體文件設定頭部變量，覆蓋其默認值。下面很快就會說這點。

```
# In _config.yml, set the layout and author for files where the type is "posts".
# And any html files that exist in the "projects/" folder will use the "project" layout, if it exists.
...
defaults:
  -
    scope:
      path: "" # an empty string here means all files in the project
      type: "posts" # previously `post` in Jekyll 2.2.
    values:
      layout: "post"
      author: "Alfred Sun"
  -
    scope:
      path: "projects"
      type: "pages" # previously `page` in Jekyll 2.2.
    values:
      layout: "project" # overrides previous default layout
```

## Jekyll 模板、變量

Jekyll 模板實際上分兩部分：一部分是頭部定義，另一部分是[Liquid 語法](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers)。

### 頭部定義

主要用於指定模板(layout)和定義一些變量，比如：標題(title)、描述(description)、標籤(tags)、分類(category/categories)、是否發布(published)，以及其他自定義的變量。

```
---
layout: post # 指定使用的模板文件，“_layout” 目錄下的模板文件名決定變量名
title: title # 文章的標題
date: date # 覆蓋文章名中的日期
category: blog # 文章的類別
description: description
published: true # default true 設置“false” 後，文章不會顯示
permalink: /:categories/:year/:month/:day/:title.html # 覆蓋全局變量設定的文章發布格式
---
```

注意：如果文本文件使用的是`utf-8`編碼，那麼必須確保文件中不存在`BOM`頭部字符，尤其是當Jekyll 運行在Windows 平台上。

### 使用變量

關於Jekyll 的變量，可以參考[官方說明](http://jekyllrb.com/docs/variables/)  
上面文章頁面中定義的頭部變量，需要使用下面的語法獲得：

```
page.title
```

這些自定義的變量將會被傳遞給Liquid 模板引擎用於轉換文本文件，例如，你可以用上面定義的“title” 變量在layout 中設置頁面的標題：

```
<!DOCTYPE HTML>
<html>
  <head>
    <title>{{ page.title }}</title>
  </head>
  <body>
   ...
  </body>
</html>
```

所有的變量都是一個樹節點，比如：page 就是當前頁面的根節點。

其中全局根結點有：

- **site**：`_config.yml`中配置的信息
- **page**： 頁面的配置信息，包括YAML 中定義的變量
- **content**： 用在模板文件中，該變量包含頁面的子視圖，用於引入子節點的內容；不能在post 和page 文件中使用
- **paginator**： 分頁信息，需要事先設定site 中的`paginate`值，參考[Pagination](http://jekyllrb.com/docs/pagination/)

注意：`post`變量僅作用於`for`循環內部，如`{%forpostinsite.posts%}`。假如需要訪問當前正在渲染的文章頁面的變量，就要用`page`變量代替post 對象。比如，post 的title 變量，要通過`page.title`來訪問。

#### site 下的變量

| 變量| 描述 |
| ------------------------- | ----------------------- ------------------------------------------------- |
| site.time | 當前的時間(運行Jekyll 時的時間) |
| site.pages | 所有頁面列表 |
| site.posts | 按時間逆序排列的所有文章列表 |
| site.related_posts | 如果當前被處理的頁面是一個post 文件，那這個變量是一個包含了最多10篇相關的文章列表 |
| site.static_files | 所有靜態文件的列表(如：沒有被Jekyll 處理的文件)，每個文件有3個屬性：`path`、`modified_time`和`extname` |
| site.html_pages | 所有HTML 頁面列表 |
| site.collections | 自定義的對象集合列表，參考[Collections](http://jekyllrb.com/docs/collections/) |
| site.data | _data 目錄下YAML 文件的數據列表 |
| site.documents | 所有Collections 裡面的文檔列表 |
| site.categories.CATEGORY | 所有在CATEGORY 類別下的post 列表 |
| site.tags.TAG | 所有在TAG 標籤下的post 列表 |
| site.[CONFIGURATION_DATA] | 其他自定義的變量 |

#### page 下的變量

| 變量| 描述 |
| --------------- | --------------------------------- ------------------------------------------ |
| page.content | 頁面的內容 |
| page.title | 頁面的標題 |
| page.excerpt | 未渲染的摘要 |
| page.url | 不帶域名的頁面鏈接，如：`/2008/12/14/my-post.html` |
| page.date | 指定每一篇post 的時間，可在post 的front matter 裡覆蓋這個值，格式是：`date:YYYY-MM-DD HH:MM:SS` |
| page.id | 每一篇post 的唯一標示符(在RSS中非常有用)，如：`/2008/12/14/my-post` |
| page.categories | post 隸屬的一個分類列表，可在YAML 頭部指定 |
| page.tags | post 隸屬的一個標籤列表，可在YAML 頭部指定 |
| page.path | 頁面的源碼地址 |
| page.next | 按時間順序排列的下一篇文章 |
| page.previous | 按時間順序排列的上一篇文章 |

#### paginator 下的變量

分頁只在index 頁面中有效，index 頁面可以在子目錄裡，比如：主頁在`/blog/index.html`，那麼通過配置`paginate_path:"blog/page:num/"`，主頁裡面放不下的其他內容就可以設定在第2 頁`blog/page2/index.html`以及後面的頁面中。

| 變量| 描述 |
| ---------------------------- | ---------------- |
| paginator.per_page | 每一頁的post 數量 |
| paginator.posts | 當前頁面上可用的post 列表 |
| paginator.total_posts | 所有post 的數量 |
| paginator.total_pages | 分頁總數 |
| paginator.page | 當前頁的頁碼，或者`nil` |
| paginator.previous_page | 上一頁的頁碼，或者`nil` |
| paginator.previous_page_path | 上一頁的路徑，或者`nil` |
| paginator.next_page | 下一頁的頁碼，或者`nil` |
| paginator.next_page_path | 下一頁的路徑，或者`nil` |

## Liquid 語法

[Liquid](https://github.com/Shopify/liquid/wiki)是Ruby 的一個模版引擎庫，Jekyll中用到的Liquid標記有兩種：**輸出**和**標籤**。

- Output 標記：變成文本輸出，被2層成對的花括號包住，如：`{{content}}`
- Tag 標記：執行命令，被成對的花括號和百分號包住，如：`{%command%}`

### Jekyll 輸出Output

示例：

```
Hello {{name}}
Hello {{user.name}}
Hello {{ 'tobi' }}
```

Output 標記可以使用過濾器Filters 對輸出內容作簡單處理。  
多個Filters 間用豎線隔開，從左到右依次執行，Filter 左邊總是輸入，返回值為下一個Filter 的輸入或最終結果。

```
Hello {{ 'tobi' | upcase }} # 轉換大寫輸出
Hello tobi has {{ 'tobi' | size }} letters! # 字符串長度
Hello {{ '*tobi*' | markdownify | upcase }} # 將Markdown字符串轉成HTML大寫文本輸出
Hello {{ 'now' | date: "%Y %h" }} # 按指定日期格式輸出當前時間
```

### 標準過濾器Filters

下面是常用的過濾器方法，更多的API需要查閱源代碼（有註釋）才能看到。

源碼主要看兩個Ruby Plugin 文件：`filters.rb`(Jekyll) 和`standardfilters.rb`(Liquid)。

（這也是博主剛開始使用Jekyll的時候，比較頭疼的問題。由於官方沒有給出詳細API的說明，只能去源代碼那裡看啦，好在代碼的註釋比較詳細）

---

- `date`- 將時間戳轉化為另一種格式([syntax reference](http://docs.shopify.com/themes/liquid-documentation/filters/additional-filters#date "Liquid Documentation"))
- `capitalize`- 輸入字符串首字母大寫eg`{{'capitalize me'|capitalize}}# => 'Capitalize me'`
- `downcase`- 輸入字符串轉換為小寫
- `upcase`- 輸入字符串轉換為大寫
- `first`- 返回數組中第一個元素
- `last`- 返回數組數組中最後一個元素
- `join`- 用特定的字符將數組連接成字符串輸出
- `sort`- 對數組元素排序
- `map`- 輸入數組元素的一個屬性作為參數，將每個元素的屬性值映射為字符串
- `size`- 返回數組或字符串的長度eg`{{array|size}}`
- `escape`- 將字符串轉義輸出eg`{{"<p>test</p>"|escape}}# => <p>test</p>`
- `escape_once`- 返迴轉義後的HTML文本，不影響已經轉義的HTML實體
- `strip_html`- 刪除HTML 標籤
- `strip_newlines`- 刪除字符串中的換行符(`\n`)
- `newline_to_br`- 用HTML`<br/>`替換換行符`\n`
- `replace`- 替換字符串中的指定內容eg`{{'foofoo'|replace:'foo','bar'}}# => 'barbar'`
- `replace_first`- 查找並替換字符串中第一處找到的目標子串eg`{{'barbar'|replace_first:'bar','foo'}}# => 'foobar'`
- `remove`- 刪除字符串中的指定內容eg`{{'foobarfoobar'|remove:'foo'}}# => 'barbar'`
- `remove_first`- 查找並刪除字符串中第一處找到的目標子串eg`{{'barbar'|remove_first:'bar'}}# => 'bar'`
- `truncate`- 截取指定長度的字符串，第2個參數追加到字符串的尾部eg`{{'foobarfoobar'|truncate:5,'.'}}# => 'foob.'`
- `truncatewords`- 截取指定單詞數量的字符串
- `prepend`- 在字符串前面添加字符串eg`{{'bar'|prepend:'foo'}}# => 'foobar'`
- `append`- 在字符串後面追加字符串eg`{{'foo'|append:'bar'}}# => 'foobar'`
- `slice`- 返回字符子串指定位置開始、指定長度的子串eg`{{"hello"|slice:-4,3}}# => ell`
- `minus`- 減法運算eg`{{4|minus:2}}# => 2`
- `plus`- 加法運算eg`{{'1'|plus:'1'}}#=> '11', {{ 1 | plus:1 }} # => 2`
- `times`- 乘法運算eg`{{5|times:4}}# => 20`
- `divided_by`- 除法運算eg`{{10|divided_by:2}}# => 5`
- `split`- 根據匹配的表達式將字符串切成數組eg`{{"a~b"|split:"~"}}# => ['a','b']`
- `modulo`- 求模運算eg`{{7|modulo:4}}# => 3`

### Jekyll 標籤Tag

標籤用於模板中的執行語句。目前Jekyll/Liquid 支持的標準標籤庫有：

| Tags | 說明 |
| ----------- | ----------------------------- |
| **assign** | 為變量賦值 |
| **capture** | 用捕獲到的文本為變量賦值 |
| **case** | 條件分支語句case…when… |
| **comment** | 註釋語句 |
| **cycle** | 通常用於在某些特定值間循環選擇，如顏色、DOM類 |
| **for** | 循環語句 |
| **if** | if/else 語句 |
| **include** | 將另一個模板包進來，模板文件在`_includes`目錄中 |
| **raw** | 禁用範圍內的Tag 命令，避免語法衝突 |
| **unless** | if 語句的否定語句 |

#### 1. Comments

僅起到註釋Liquid 代碼的作用。

```
We made 1 million dollars {% comment %} in losses {% endcomment %} this year.
```

#### 2. Raw

臨時禁止執行Jekyll Tag 命令，在生成的內容裡存在衝突的語法片段的情況下很有用。

```
{% raw % }
  In Handlebars, {{ this }} will be HTML-escaped, but {{{ that }}} will not.
{% endraw % }
```

#### 3. If / Else

條件語句，可以使用關鍵字有：`if`、`unless`、`elsif`、`else`。

```
 {% if user %}
   Hello {{ user.name }}
 {% endif %}

 # Same as above
 {% if user != null %}
   Hello {{ user.name }}
 {% endif %}

 {% if user.name == 'tobi' %}
   Hello tobi
 {% elsif user.name == 'bob' %}
   Hello bob
 {% endif %}

 {% if user.name == 'tobi' or user.name == 'bob' %}
   Hello tobi or bob
 {% endif %}

 {% if user.name == 'bob' and user.age > 45 %}
   Hello old bob
 {% endif %}

 {% if user.name != 'tobi' %}
   Hello non-tobi
 {% endif %}

 # Same as above
 {% unless user.name == 'tobi' %}
   Hello non-tobi
 {% endunless %}

 # Check for the size of an array
 {% if user.payments == empty %}
    you never paid !
 {% endif %}

 {% if user.payments.size > 0 %}
    you paid !
 {% endif %}

 {% if user.age > 18 %}
    Login here
 {% else %}
    Sorry, you are too young
 {% endif %}

 # array = 1,2,3
 {% if array contains 2 %}
    array includes 2
 {% endif %}

 # string = 'hello world'
 {% if string contains 'hello' %}
    string includes 'hello'
 {% endif %}
```

#### 4. Case 語句

適用於當條件實例很多的情況。

```
{% case template %}
{% when 'label' %}
     // {{ label.title }}
{% when 'product' %}
     // {{ product.vendor | link_to_vendor }} / {{ product.title }}
{% else %}
     // {{page_title}}
{% endcase %}
```

#### 5. Cycle

經常需要在相似的任務間選擇時，可以使用`cycle`標籤。

```
{% cycle 'one', 'two', 'three' %}
{% cycle 'one', 'two', 'three' %}
{% cycle 'one', 'two', 'three' %}
{% cycle 'one', 'two', 'three' %}

# =>

one
two
three
one
```

如果要對循環作分組處理，可以指定分組的名字：

```
{% cycle 'group 1': 'one', 'two', 'three' %}
{% cycle 'group 1': 'one', 'two', 'three' %}
{% cycle 'group 2': 'one', 'two', 'three' %}
{% cycle 'group 2': 'one', 'two', 'three' %}

# =>
one
two
one
two
```

#### 6. For loops

循環遍歷數組：

```
{% for item in array %}
  {{ item }}
{% endfor %}
```

循環迭代Hash散列，`item[0]`是鍵，`item[1]`是值：

```
{% for item in hash %}
  {{ item[0] }}: {{ item[1] }}
{% endfor %}
```

每個循環週期，提供下面幾個可用的變量：

```
forloop.length # => length of the entire for loop
forloop.index # => index of the current iteration
forloop.index0 # => index of the current iteration (zero based)
forloop.rindex # => how many items are still left ?
forloop.rindex0 # => how many items are still left ? (zero based)
forloop.first # => is this the first iteration ?
forloop.last # => is this the last iteration ?
```

還有幾個屬性用來限定循環過程：

`limit:int`： 限制循環迭代次數  
`offset:int`： 從第n個item開始迭代  
`reversed`： 反轉循環順序

```
# array = [1,2,3,4,5,6]
{% for item in array limit:2 offset:2 %}
  {{ item }}
{% endfor %}
# results in 3,4

{% for item in collection reversed %}
  {{item}}
{% endfor %}

{% for post in site.posts limit:20 %}
  {{ post.title }}
{% endfor %}
```

允許自定義循環迭代次數，迭代次數可以用常數或者變量說明：

```
# if item.quantity is 4...
{% for i in (1..item.quantity) %}
  {{ i }}
{% endfor %}
# results in 1,2,3,4
```

#### 7. Variable Assignment

為變量賦值，用於輸出或者其他Tag：

```
{% assign index = 1 %}
{% assign name = 'freestyle' %}

{% for t in collections.tags %}{% if t == name %}
  <p>Freestyle!</p>
{% endif %}{% endfor %}


# 變量是布爾類型
{% assign freestyle = false %}

{% for t in collections.tags %}{% if t == 'freestyle' %}
  {% assign freestyle = true %}
{% endif %}{% endfor %}

{% if freestyle %}
  <p>Freestyle!</p>
{% endif %}
```

`capture`允許將大量字符串合併為單個字符串並賦值給變量，而不會輸出顯示。

```
{% capture attribute_name %}{{ item.title | handleize }}-{{ i }}-color{% endcapture %}

<label for="{{ attribute_name }}">Color:</label>
<select name="attributes[{{ attribute_name }}]" id="{{ attribute_name }}">
  <option value="red">Red</option>
  <option value="green">Green</option>
  <option value="blue">Blue</option>
</select>
```

---

## 其他模板語句

### 字符轉義

有時候想輸出`{`了，怎麼辦？使用反斜線`\`轉義即可

```
\{ => {
```

### 格式化時間

```
{{ site.time | date_to_xmlschema }} # => 2008-11-07T13:07:54-08:00
{{ site.time | date_to_rfc822 }} # => Mon, 07 Nov 2008 13:07:54 -0800
{{ site.time | date_to_string }} # => 07 Nov 2008
{{ site.time | date_to_long_string }} # => 07 November 2008
```

### 代碼語法高亮

安裝好**pygments.rb**的gem 組件和Python 2.x 後，配置文件添加：`highlighter:pygments`，就可以使用語法高亮命令了，支持語言多達100 種以上。

```
{% highlight ruby​​ linenos %}
# some ruby​​ code
{% endhighlight %}
```

上面的示例中，使用`highlight`語句來處理代碼塊；並設定第一個參數`ruby`來指定高亮的語言Ruby，第二個參數`linenos`來開啟顯示代碼行號的功能。

為了給代碼著色，需要配置相應的樣式文件，參考[syntax.css](https://github.com/mojombo/tpw/tree/master/css/syntax.css)；  
為了更好的顯示行號，可以在上面的CSS 文件添加`.lineno`樣式類。

可用的語言識別符縮寫，從[**Pygments' Lexers Page**](http://pygments.org/docs/lexers/)查閱。  
如果從Pygments 的[Supported Languages](http://pygments.org/languages/)清單，能發現明明有列出該語言名稱，而pygments.rb 確無法識別該語言，這時候必須到[Available Lexers] (http://pygments.org/docs/lexers/)查詢；如果在程序語言的說明中有一行“**New in version 1.5.**”，那就表示只要將**Pygments**更新到1.5版， 即可支持該程序語言。

### 鏈接同域內的post

使用`post_url`Tag 可以自動生成網站內的某個post 超鏈接。  
這個命令語句以相關post 的文件名為參數，在引入同域的post 鏈接時，非常有用。

```
# 自動生成某篇文章的鏈接地址
{% post_url 2010-07-21-name-of-post %}

# 引入該文章的鏈接
[Name of Link]({% post_url 2010-07-21-name-of-post %})
```

### Gist 命令

嵌入GitHub Gist，也可以指定要顯示的gist 的文件名。

```
{% gist parkr/931c1c8d465a04042403 %}
{% gist parkr/931c1c8d465a04042403 jekyll-private-gist.markdown %}
```

### 生成摘要

配置文件中設定`excerpt_separator`取值，每篇post 都會自動截取從開始到這個值間的內容作為這篇文章的摘要`post.excerpt`使用。  
如果要禁用某篇文章的摘要，可以在該篇文章的YAML 頭部設定`excerpt_separator:""`。

```
{ % for post in site.posts % }
  <a href="http://alfred-sun.github.io/blog/2015/01/10/jekyll-liquid-syntax-documentation/{ { post.url } }">{ { post.title } }< /a>
  { { post.excerpt | remove: 'test' } }
{ % endfor % }
```

### 刪除HTML 標籤

這個在摘要作為`head`標籤裡的`meta="description"`內容輸出時很有用

```
{ { post.excerpt | strip_html } }
```

### 刪除指定文本

過濾器`remove`可以刪除變量中的指定內容

```
{ { post.url | remove: 'http' } }
```

### CGI Escape

通常用於將URL 中的特殊字符轉義為`%xx`形式

```
{ { "foo,bar;baz?" | cgi_escape } } # => foo%2Cbar%3Bbaz%3F
```

### 排序

```
# Sort an array. Optional arguments for hashes:
# 1. property name
# 2. nils order ('first' or 'last')

{ { site.pages | sort: 'title', 'last' } }
```

### 搜索指定Key

```
# Select all the objects in an array where the key has the given value.
{ { site.members | where:"graduation_year","2014" } }
```

### To JSON 格式

將Hash 散列或數組轉換為JSON 格式

```
{ { site.data.projects | jsonify } }
```

### 序列化

把一個數組變成一個字符串

```
{ { page.tags | array_to_sentence_string } } # => foo, bar, and baz
```

### 單詞的個數

```
{ { page.content | number_of_words } }
```

## 內容名字規範

對於博客post，文件命名規則必須是`YEAR-MONTH-DAY-title.MARKUP`的格式。  
使用`rake post`會自動將post 文件合適命名。

比如：

```
-11-06-memcached-code.md
-11-06-memcached-lib.md
-11-06-sphinx-config-and-use.md
-11-07-memcached-hash-table.md
-11-07-memcached-string-hash.md
```

## Assets 樣式文件

Jekyll 支持Sass 和CoffeeScript，通過新建`.sass`、`.scss`或`.coffee`格式的文件，並在開頭添加一對`---`來使用這個功能。

關於詳細的使用，請參看[官方說明](http://jekyllrb.com/docs/assets/)，這裡就不再做介紹了。

## 模板

[liquid 中文文檔](https://liquid.bootcss.com)
[jekyll-guide]: https://gist.github.com/biezhi/f88be58ef4ae0f3741bb36ab8daa53c5#file-jekyll-guide-md