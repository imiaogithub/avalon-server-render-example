# avalon-server-render-example
avalon2+koa2的后端渲染例子


引入最新版 avalon
引入avalon仓库下的serve下的文件serveRender

引入你定义VM的文件 (所有DOM操作要在回调里进行,不要出现 window, document, 方便能在nodejs环境中运行)
对你的VM使用webpack进行打包 (目的是处理module.exports, require)

引入你该页面的模板

将VM与模板放进serveRender方法,得到一个对象,里面包含渲染好的HTML 及 要往前端发送的扫描区域

> 扫描区域是指用ms-contoller或ms-important圈起来的部分
> 扫描区域最好用html-minifier压缩一下,减少体积



```javascript

//当前页面VM
var vm = require('./src/vm')
//当前页面模板
var test = fs.readFileSync('./src/aaa.html', 'utf-8');
//渲染器
var serveRender = require('./dist/serverRender')

var obj = serveRender(vm, test)
//压缩HTML
for(var i in obj.templates){
   obj.templates[i] = minify(obj.templates[i],{
       collapseInlineTagWhitespace: true,
       collapseWhitespace:true
   })
   
}

var files = JSON.stringify(obj.templates)

var script = '<script src="./avalon.js"><\/script>' +
        '<script> avalon.serverTemplates= ' + files + '<\/script>' +
        '<script src="./test.js"><\/script>'

app.use(async function(ctx){
     await (ctx.body = script + obj.html)
})



```

