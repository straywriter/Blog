---
title: Hexo 搭建
top: false
mathjax: true
date: 2021-03-14 13:45:33
categories:
- 工具
---

-----

搭建基于Hexo 和Next主题的博客



# 搭建环境

***



## 安装node

[nodejs官网](https://nodejs.org/en/)

**scoop 安装**

```
scoop install node
```

**检测node 版本**

```
node -v
npm -v
```

**npm 添加国内镜像**

```
npm config set registry https://registry.npm.taobao.org
```

**安装hexo**

```
npm install hexo-cli -g
```

**初始化博客**

```
hexo init blog
```

bolg 是搭建博客的路径，指在当前文件夹下的blog目录。



## 安装Next主题



```
git clone https://github.com/theme-next/hexo-theme-next.git themes/next
```

**配置主题**：blog根目录下的 _config.yml 文件中，修改 theme 为你的主题名字：

```
theme: next
```





## Hexo 常用命令





## 配置git

```
npm install hexo-deployer-git --save
```



```
deploy:
	type: git
    repo: https://github.com/xxx/xxxx.github.io.git
```



## 网站基本配置

| 参数          | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| `title`       | 网站标题                                                     |
| `subtitle`    | 网站副标题                                                   |
| `description` | 网站描述                                                     |
| `keywords`    | 网站的关键词。支援多个关键词。                               |
| `author`      | 您的名字                                                     |
| `language`    | 网站使用的语言。对于简体中文用户来说，使用不同的主题可能需要设置成不同的值，请参考你的主题的文档自行设置，常见的有 `zh-Hans`和 `zh-CN`。 |
| `timezone`    | 网站时区。Hexo 默认使用您电脑的时区。请参考 [时区列表](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) 进行设置，如 `America/New_York`, `Japan`, 和 `UTC` 。一般的，对于中国大陆地区可以使用 `Asia/Shanghai`。 |

其中，`description`主要用于SEO，告诉搜索引擎一个关于您站点的简单描述，通常建议在其中包含您网站的关键词。`author`参数用于主题显示文章的作者。



## 文章头设置



```
title: {{ title }}
date: {{ date }}
top: false
cover: false
password:
toc: true
mathjax: true
summary:
tags:
categories:
```



## 分类设置







# Hexo 基本配置及插件

## Hexo 更换渲染器

卸载

```
npm uninstall hexo-renderer-marked --save
```



```
npm install hexo-renderer-pandoc --save
```



解决数学公式及脚注问题

## Hexo 创建分类页



```
hexo new page categories
```





## Hexo about 页面





## Hexo 相对路径图片

`_config.yml` 文件设置

```
post_asset_folder: true
```

安装`hexo-asset-image`

```
npm install hexo-asset-image --save
```

将文件`node_modules/hexo-asset-image/index.js`替换为如下的内容：

```
'use strict';
var cheerio = require('cheerio');

// http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
function getPosition(str, m, i) {
  return str.split(m, i).join(m).length;
}

var version = String(hexo.version).split('.');
hexo.extend.filter.register('after_post_render', function(data){
  var config = hexo.config;
  if(config.post_asset_folder){
    	var link = data.permalink;
	if(version.length > 0 && Number(version[0]) == 3)
	   var beginPos = getPosition(link, '/', 1) + 1;
	else
	   var beginPos = getPosition(link, '/', 3) + 1;
	// In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
	var endPos = link.lastIndexOf('/') + 1;
    link = link.substring(beginPos, endPos);

    var toprocess = ['excerpt', 'more', 'content'];
    for(var i = 0; i < toprocess.length; i++){
      var key = toprocess[i];
 
      var $ = cheerio.load(data[key], {
        ignoreWhitespace: false,
        xmlMode: false,
        lowerCaseTags: false,
        decodeEntities: false
      });

      $('img').each(function(){
		if ($(this).attr('src')){
			// For windows style path, we replace '\' to '/'.
			var src = $(this).attr('src').replace('\\', '/');
			if(!/http[s]*.*|\/\/.*/.test(src) &&
			   !/^\s*\//.test(src)) {
			  // For "about" page, the first part of "src" can't be removed.
			  // In addition, to support multi-level local directory.
			  var linkArray = link.split('/').filter(function(elem){
				return elem != '';
			  });
			  var srcArray = src.split('/').filter(function(elem){
				return elem != '' && elem != '.';
			  });
			  if(srcArray.length > 1)
				srcArray.shift();
			  src = srcArray.join('/');
			  $(this).attr('src', config.root + link + src);
			  console.info&&console.info("update link as:-->"+config.root + link + src);
			}
		}else{
			console.info&&console.info("no src attr, skipped...");
			console.info&&console.info($(this));
		}
      });
      data[key] = $.html();
    }
  }
});
```



## Hexo 图片放大

```
git clone https://github.com/theme-next/theme-next-fancybox3 themes/next/source/lib/fancybox
```

主题配置文件`_config.yml`

```
fancybox: true
```



## Hexo 数学公式

```
npm install hexo-math --save
```

Next 主题配置文件

```
mathjax:
  enable: true
```



## Hexo设置本地搜索

**安装插件**

```
npm install hexo-generator-searchdb --save
```

配置`_config.yml` 文件，添加

```
# 本地搜索
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

修改主题配置文件`_config.yml`：

```
local_search:
  enable: true
```



## Hexo auto_excerpt

[hexo-excerpt](https://github.com/chekun/hexo-excerpt)

```
npm install hexo-excerpt --save
```



```
excerpt:
  depth: 10
  excerpt_excludes: []
  more_excludes: []
  hideWholePostExcerpts: true
```

## Hexo 显示当前浏览进度

```
back2top:
  enable: true
  # Back to top in sidebar.
  sidebar: true
  # Scroll percent label in b2t button.
  scrollpercent: true
```

**美化**

```
// 返回顶部 透明度设置
.back-to-top
{
  background :none;
}
```



## hexo 字数统计、阅读时长

[hexo-symbols-count-time](https://github.com/theme-next/hexo-symbols-count-time)

```
npm install hexo-symbols-count-time --save
```



## Hexo 取消行号

```
//取消行号
.highlight .gutter{
  display: none;
}
```





# Hexo 主题配置及美化

## Archives 美化



注释`.post-header` 



```
  .post-meta {
    display: inline;
    //font-size: $font-size-smallest;
    margin-right: 10px;
     font-size: 19px;
  //position: absolute;
  color: #fff;
  background-color: #49b1f5;
  border-radius: 5px;
  padding-left: 5px;
  padding-right: 5px;
  margin-left: 10px;
  }
```



```
  .post-title {
    display: inline;

  margin-right: 10px;
     font-size: 19px;
  //position: absolute;
  color: #fff;
  background-color: #eff2f3;
  border-radius: 5px;
  padding-left: 5px;
  padding-right: 5px;
  margin-left: 10px;
}
```



## 分类美化



```
.category-list-link
{
  margin-right: 10px;
     font-size: 19px;
  //position: absolute;
  background-color: #eff2f3;
  border-radius: 5px;
  padding-left: 5px;
  padding-right: 5px;
  // margin-left: 10px;
}
```



## 主页文章标题美化

```
.posts-expand .post-title-link
{

  
  margin-right: 10px;
    //  font-size: 19px;
  //position: absolute;
  // color: #fff;
  background-color: #f5f5f5;
  border-radius: 5px;
  padding-left: 8px;
  padding-right: 8px;
  margin-left: 10px;

}

```



## 主页文章圆角透明



```
.post-block
{
  background : #fff
margin-top: 0px;
   margin-bottom: 60px;
   padding: 25px;
   border-radius: 20px 20px 20px 20px;
   -webkit-box-shadow: 0 0 5px rgba(202, 203, 203, .5);
   -moz-box-shadow: 0 0 5px rgba(202, 203, 204, .5);
opacity :0.9;
}
```



## 主页美化



```
.sidebar
{
background :none;
// opacity :0.9;
}
.sidebar-inner{
  margin-top :20px
}

.content-wrap{
  background : inherit;
   padding-top :0px
//  opacity : 1;
}

```



## 设置圆角



```
$border-radius-inner              = 20px 20px 20px 20px;
$border-radius                    = 20px;
```



## 设置背景图片

```
body {
  color :black
  background: url(https://source.unsplash.com/random);
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: 50% 50%;
}
```



## 设置透明度



```
.main-inner{
	opacity: 0.8;
}
.header-inner{
	opacity: 0.8;
	z-index: 10;
}
```

