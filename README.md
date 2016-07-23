JHipster  官方文档站点请 [点此查看](http://jhipster.github.io/)

此仓库仅提供个平台，让大家更好的学习了解JHipster 。

# 翻译规范：

* 翻译的童鞋请申请加入翻译小组，并申请翻译的章节，并在readmd.md 下登记，然后再根目录下建立与github账号命名的文件夹。
 


# 任务分工

| 章节 | 翻译 | 
|----|----|
| 1、Managing relationships 2、microservices_architecture、 | [xqMing](https://github.com/QiuMing) |




# 域名和服务器提供




======

This is the source of JHipster's public [Web site](http://jhipster.github.io/).
=======

This Web site is rendered with GitHub pages.

To run this locally

* [Fork this](https://github.com/jhipster/jhipster.github.io/fork) repo and clone to your file system
* [Install Jekyll](https://help.github.com/articles/using-jekyll-with-pages/#installing-jekyll)
* Run `bundle exec jekyll serve` in the cloned repo folder
* you will be able to access the site at http://localhost:4000

Or with Docker
* [Fork this](https://github.com/jhipster/jhipster.github.io/fork) repo and clone to your file system
* `docker run --rm --label=jekyll --volume=$(pwd):/srv/jekyll   -it -p 127.0.0.1:4000:4000 jekyll/jekyll:pages`
* you will be able to access the site at http://localhost:4000
