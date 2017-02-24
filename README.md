# createtable

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For detailed explanation on how things work, checkout the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

开发背景：</br>
    此页面用于公司项目里，公司的项目用的是php的ci框架，之前未用过vue和webpack</br>
    </br>
开发过程：</br>
    在本地服务器www文件夹下创建vue-cli的模板，和公司项目的文件夹（leqeedata）平级</br>
    开发中遇到的问题主要有两个：</br>
                            1，可能是后端代码的原因，前端无法用vue-resource进行请求，改用jquery，请求路径写成绝对路径</br>                                                            http://localhost/leqeedata/RecordAgent/DiyHeaderRecord/insertOrUpdateHeaders  ，</br>
                               但是，此请求不能用npm run dev 打开浏览器访问，会出现跨域请求问题，原因是因为执行此命令是在8080端口下打开页面，此问题 
                               需要解决跨域，但是我还没有解决，所以无法用npm run dev，</br>
                            2，在执行npm run build之前需要修改config下的index.js文件，在build下，改为
                               assetsPublicPath:'http://localhost/leqeedata/assets/sdk/css/' ，因为，此页面上线以后static文件夹放在了上述路径                                下，改过后生成的index.html中引入的路径会自动改变                           http://localhost/leqeedata/assets/sdk/css/static/js/manifest.3bb3fac3b7b96a1cd523.js ，这样上线以后就能够正确加载静态文件了。<br><br>
                               
针对开发过程的问题1给出的解决方案：<br>
     下面几段是引用网上<br>
     和后端联调时总是会面对恼人的跨域问题，最近基于Vue开发项目时也遇到了这个问题，两边各自想了一堆办法，查了一堆资料，加了一堆参数，最后还得我把自己的localhost映射成上线时将要使用的域名。<br>
今天翻看代码时，突然发现vue-cli的config文件里有一个参数叫proxyTable，看这个名字就感觉能解决问题，于是我就去搜了一下，果然。在vuejs-templates，也就是vue-cli的使用的模板插件里，有关于API proxy的说明，使用的就是这个参数。<br>
https://vuejs-templates.github.io/webpack/proxy.html<br>
这个参数主要是一个地址映射表，你可以通过设置将复杂的url简化，例如我们要请求的地址是api.xxxxxxxx.com/list/1，可以按照如下设置：<br><br>
       proxyTable: {<br>
        '/list': {<br>
          target: 'http://api.xxxxxxxx.com',<br>
          pathRewrite: {<br>
            '^/list': '/list'<br>
          }<br>
        }<br>
      }<br>
      这样我们在写url的时候，只用写成/list/1就可以代表api.xxxxxxxx.com/list/1.<br><br>
      
    那么又是如何解决跨域问题的呢？其实在上面的'list'的参数里有一个changeOrigin参数，接收一个布尔值，如果设置为true,那么本地会虚拟一个服务端接收你的请求并代你发送该请求，这样就不会有跨域问题了，当然这只适用于开发环境。增加的代码如下所示：<br>
            proxyTable: {
              '/list': {
                target: 'http://api.xxxxxxxx.com',
                changeOrigin: true,
                pathRewrite: {
                  '^/list': '/list'
                }
              }
            }
      vue-cli的这个设置来自于其使用的插件http-proxy-middleware    github：https://github.com/chimurai/http-proxy-middleware  <br><br><br>
      
      下面是我针对上述方案在项目代码里做出的修改：<br>
      
           1，修改config下的index.js文件的proxyTable如下：<br>
                proxyTable: {<br>
                    '/leqeedata/RecordAgent/DiyHeaderRecord/getHeadersByCode': {<br>
                        target: 'http://localhost:80',<br>
                        changeOrigin: true,<br>
                        pathRewrite: {<br>
                            '^/leqeedata/RecordAgent/DiyHeaderRecord/getHeadersByCode':<br>
                                        '/leqeedata/RecordAgent/DiyHeaderRecord/getHeadersByCode'
                        }<br>
                    }<br>
                },<br>
            上面的80端口是本地服务器的默认端口。<br>
         2，ajax请求的url写法如下：<br>
                url: "/leqeedata/RecordAgent/DiyHeaderRecord/getHeadersByCode"
     
