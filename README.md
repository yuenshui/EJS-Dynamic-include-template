# EJS-Dynamic-include-template
EJS Dynamic include template
动态include模板
目前ejs不支持动态加载模板文件，比如:

```html
<div class="col-lg">
	<%- include inc/inc.debugLog.html %>
</div>
```

通用模板里，如果想吧“debugLog”做为一个变量传入模板，实现加载。ejs就不支持了。
下面是实现这个功能的方法：


1、修改ejs的源码：node_modules/ejs/lib/ejs.js
475行（大约）
```js
      var include = function (path, includeData) {
        var d = utils.shallowCopy({}, data);
        if (includeData) {
          d = utils.shallowCopy(d, includeData);
        }
        return includeFile(path, opts)(d);
      };
```
改为：
```js
      var include = function (path, includeData) {
	      if(typeof includeData.realPath != "undefined") {
		      path = includeData.realPath;
	      }
        var d = utils.shallowCopy({}, data);
        if (includeData) {
          d = utils.shallowCopy(d, includeData);
        }
        return includeFile(path, opts)(d);
      };
```
增加了3行代码

2、模板使用
```js
<%- include('', {realPath:'inc/inc.' + data.PageName + '.html'}) %>
```
realPath属性将会作为模板相对路径传入

ejs 2.3.1  测试通过
