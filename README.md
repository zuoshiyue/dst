# 说明



考虑到开源库[static-nav](https://github.com/TopVitamin/static-nav)是使用`HTML+CSS+JQ`简单实现的，代码里面网址都是直接写死的，这样处理的话，维护时会异常繁琐，而且不利于后期拓展，比如定位某个导航的功能，还有就是通过云端存储网址，前端页面请求动态渲染等等。

所以就打算使用`vue next + vite + element-plus` 重构项目，结合部门实际应用场景，考虑到了通用和项目的问题，还有就是快速定位的问题，后期也可以将`navList.ts`里的数据存储在服务器，通过部门的后台管理系统配置一下，就可以了，不用前端每次更新，都需要打包部署。

## :hammer_and_wrench: 构建
```shell
# 安装
npm install
# 运行
npm run dev
# 编译
npm run build
# 代码风格
npm run lint
# 代码推送到仓库
npm run deploy
```


具体内容可看博文[《静态导航页设计与开发》](https://dstweihao.cn/?p=185 )





