# steamer-plugin-ak

AlloyKit平台生成离线包命令

## 安装

```javascript
npm i -g steamerjs

npm i -g steamer-plugin-ak
```

## 使用

如果项目的生成环境的代码结构如下：

```javascript
-- dist
	|
	-- cdn
	|	|
	|	|-- css
	|	|-- img
	|	|-- js
	|
	-- webserver
		|-- index.html
		|-- detail.html
		|-- comment.html
```

初始化：

```javascript
steamer ak -i

或

steamer ak -init

```

它会弹出下面的一些问题：

`Your zip file name`, 意思是最终生成的离线包名称，默认值是 `offline`，**当前文件夹位置以命令执行位置为基准**。

`Your source folder`, 生成环境的代码源，上面的项目结构中是 `dist`，这也是默认值。

后面三个选项会一直循环出现，除非你输入空值，因为项目中可能有多个 `cdn` 对应多个静态资源。例如，你可能会用 `//huayang.qq.com/h5/` 给你的 `html` 文件，而用 `//s1.url.cn/h5/` 给你的其它静态资源。


```javascript
Your zip file name(e.g., offline): (offline)

Your source folder(e.g., build, pub, dist): (dist)

Whether to add webserver url for all resources: (No)

Your resource folder(e.g., cdn, cdn/js, cdn/css, webserver):

Your destination url(e.g.,//huayang.qq.com/h5/):

Your destination folder(e.g., /, /, js, css):
```

这里是配置文件的范例 (.steamer/steamer-plugin-ak.js).

```javascript
module.exports = {
    "plugin": "steamer-plugin-ak",
    "config": {
        "zipFileName": "dist/offline", 
        // String, 最终生成的离线包名称，默认值是 `offline`，**当前文件夹位置以命令执行位置为基准**
        "src": "dist",
        // String, 生成环境的代码源，默认值 `dist`
        "map": [
            {
                "src": "webserver",
                "url": "//localhost:9000/"
            },
            {
                "src": "cdn",
                "url": "//localhost:8000/"
            }
        ]
        // 具体的文件目录及cdn映射
    }
}
```

然而，有时候你会对 `html` 以外的静态资源再进行细分，使用不同的 `cdn` 域名，例如 `//s1.url.cn/h5/` 给你的 `js`文件，用 `//s2.url.cn/h5/` 给 `css` 文件， 然后用 `//s3.url.cn/h5/` 给其它的文件。

Here is another example config.

```javascript
module.exports = {
    "plugin": "steamer-plugin-ak",
    "config": {
        "zipFileName": "offline",
        "src": "dist",
        "map": [
            {
                "src": "cdn/js",
                "dest": "js",
                // String, 目标文件路径子文件夹，默认为空字符串
                "isSameOrigin": true, 
                // Boolean， 默认 false，如果为 true， 则会将 cdn 的 url替换成与 isWebserver 为 true 的 cdn url
                "url": "s1.url.cn/huayang/"
            },
            {
                "src": "cdn/css",
                "dest": "css",
                "url": "s2.url.cn/huayang/"
            },
            {
                "src": "cdn/img",
                "dest": "img",
                "url": "s3.url.cn/huayang/"
            },
            {
                "src": "cdn/lib",
                "dest": "lib",
                "url": "s1.url.cn/huayang/"
            },
            {
                "src": "webserver",
                "isWebserver": true,
                // Boolean， 默认为 false，如果为 true，则这将告诉插件这是 html 的主要 cdn url 
                "url": "huayang.qq.com/huayang/activity/"
            }
        ]
    }
}
```

之所以要用 `isSameOrigin` 与 `isWebserver`，是有时候需要 `html` 文件和 `js` 文件的域名一致，例如有时候需要收集js的报错，让两者的 `cdn` 一致会更方便收集到具体的报错信息。

下面的命令，会进行压缩，并生成 `offline` 文件夹，还有 `offline.zip` 文件。

```javascript
steamer ak -c

or

steamer ak -compress
```

## 测试
```
// 安装eslint工具
npm i -g eslint

npm run test
```

## 变更
* v1.1.0 离线包打包及 `webserver` 替换 `cdn` 的 `url`
* v1.2.0 配置更改并修复同域js文件位置错误问题
* v1.2.1 添加报错提示
* v1.2.2 修复压缩包报错