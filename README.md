# Fabric 中文文档

本份 Fabric 中文文档由登链社区发起，由[Cell Network](https://www.cellnetwork.io/?utm_souce=learnblockchain#/cellhome) 和 [登链社区](https://learnblockchain.cn/) 共同赞助。
如果你对我们的翻译计划感兴趣，来看看[招募译者](https://learnblockchain.cn/article/796) .

登链社区[区块链技术文档中心](https://learnblockchain.cn/site/docs) 已经有很多文档，本Fabric 中文文档托管在 [https://learnblockchain.cn/docs/fabric/](https://learnblockchain.cn/docs/fabric/) 。

未经授权，请勿转载。

## 翻译说明

本文档翻译遵循以下原则：

1. 尊重原文档的结构、格式
2. 采用认领、翻译、校对、发布流程。


**注意事项**

* 为避免译者重复翻译，译者需要提前认领一周的工作量，认领和翻译完成时，都需要及时更新[进度文件](process.md)
* 翻译时，除了用语规范外，还要注意文件间链接跳转正确
* 翻译完成后，译者之间需要相互校对，可以在GitHub提交PR后，译者群里告知一下译者同伴。


## 翻译方法

Fabric官方文档主要是使用[reStructuredText格式](http://www.cnblogs.com/seayxu/p/5603876.html)，部分使用Markdown格式，使用 Sphinx 编译为 HTML。

因此我们需要安装下Sphinx。

1. 安装Python3.7及Pipenv

 - [Python 3.7](https://wiki.python.org/moin/BeginnersGuide/Download)
 - [Pipenv](https://docs.pipenv.org/en/latest/#install-pipenv-today)

2. 安装Sphinx

```
git clone git@github.com:lbc-team/fabric_docs.git
cd fabric_docs
pipenv install
pipenv shell
```

3. 翻译及编译文档 

```
// 切出自己的分支
git checkout -b mybranch

// 编译文档
make html
```

文档会输出在 `build/html`， 可以直接在浏览器打开index.html.


## 译者

* [李大狗](https://learnblockchain.cn/people/127)
* [Tiny熊](https://learnblockchain.cn/people/15)


## 常见问题

0. [中文文案排版指北](https://github.com/mzlogin/chinese-copywriting-guidelines)
1. RST文档格式的一个[中文说明](http://www.cnblogs.com/seayxu/p/5603876.html)
2. 文档如何构建：目前文档托管在[登链社区](https://learnblockchain.cn/docs/solidity/)，在校对完成后，管理员发布。
3. 原英文文档更新怎么办，文档管理员会定期查看更新内容，进行补充。

