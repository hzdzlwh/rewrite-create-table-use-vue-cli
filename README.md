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

开发背景：
    此页面用于公司项目里，公司的项目用的是php的ci框架，之前未用过vue和webpack
    
开发过程：
    在本地服务器www文件夹下创建vue-cli的模板，和公司项目的文件夹（leqeedata）平级
    开发中遇到的问题主要有两个：
                            1，可能是后端代码的原因，前端无法用vue-resource进行请求，改用jquery，请求路径写成绝对路径                                                            http://localhost/leqeedata/RecordAgent/DiyHeaderRecord/insertOrUpdateHeaders  ，
                               但是，此请求不能用npm run dev 打开浏览器访问，会出现跨域请求问题，原因是因为执行此命令是在8080端口下打开页面，此问题 
                               需要解决跨域，但是我还没有解决，所以无法用npm run dev，
                            2，在执行npm run build之前需要修改config下的index.js文件，在build下，改为
                               assetsPublicPath:'http://localhost/leqeedata/assets/sdk/css/' ，因为，此页面上线以后static文件夹放在了上述路径                                下，改过后生成的index.html中引入的路径会自动改变                           http://localhost/leqeedata/assets/sdk/css/static/js/manifest.3bb3fac3b7b96a1cd523.js ，这样上线以后就能够正确加载静态文件了。
