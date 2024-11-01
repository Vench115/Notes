> 记录一些课题组网站的配置信息

课题组主页：[Euclides Proof Group](http://euclidesprobationem.github.io/)

[关于 GitHub 页面和 Jekyll - GitHub 文档](https://docs.github.com/zh/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll)

`gemfile.lock` 有可能会导致环境配置的问题，包括 `gem`, `ruby`, ... ，因为为了保证依赖正确，该文件会锁定开发环境中的依赖项。

# Jekyll 目录结构

[Directory Structure | Jekyll • Simple, blog-aware, static sites](https://jekyllrb.com/docs/structure/)

我们只需要关注几个核心的目录结构如下（可以自己创建）：

- `_layouts` - 存放页面模板，md或html文件的内容会填充模板
- `_sass` - 存放样式表
- `_includes` - 可以复用在其它页面被include的html页面
- `_posts` - 博客文章页面
- `assets` - 原生的资源文件

- `js`
- `css`
- `image`

- `_config.yml` - 全局配置文件
- `index.html, index.md, README.md` - 首页index.html优先级最高，如果没有index，默认启用README.md文件
- `自定义文件和目录`

# 配置文件

`index.yml`存储主页的配置信息。

`people.yml`存储主页上所有人员的配置信息。

`_site` 文件夹中的内容是在编译运行之后自动生成的。
