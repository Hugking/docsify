## 运用 hexo 在 GitHub 上搭建个人博客

作为小白想搭建一个个人博客，在网上看了很多的教程，发现大家搭建的博客都挺赏心悦目的，为了节约服务器资源就在 GitHub 上搭建了自己的博客，分享一些踩坑的经验。

## Github 仓库

- 首先你必须有一个 github 账号
- 然后新建一个仓库，这一有第一个坑，我之前用了不同于 github 用户名(Hugking)来作为项目名称（wkaanig.github.io），一直没能搭建成功，后来看到其他大牛的经验，才发现项目名一定要是用户名(Hugking).github.io 的形式(README.md 可选可不选)
- 在 github 中 setting 添加生成页面的选项 Source 选择 master branch

  _注意：_
  如果你之前没有用 git 关联过自己的 github 库，需要配置 SSH 等参数，否则无法成功，这部分搜 git 就有很多相关教程

## 安装前提

hexo 有中文的文档，这一点非常方便，但是在安装过程中还是很容易有疏忽的地方，导致安装失败。
安装 Hexo 之前，必须保证自己的电脑中已经安装好了 Node.js 和 Git。因为这两个软件我之前都安装过，这里就不重复安装过程了，检验方式如下：

```bash
$git --vresion
$node --v
```

## 安装 Hexo

安装好 node.js 和 git 后，可以通过 npm 来安装 Hexo。

```bash
npm install -g hexo-cli
hexo v
```

## 初始化

之后就可以在电脑里新建一个文件夹来作为存放博客全部内容。我们直接用 hexo 命令来初始化文件夹：

```bash
hexo init <folder>
cd <folder>
npm install
```

`<folder>`就是文件夹的名字在创建的时候 ，文件夹初始化已经把需要的内容都下载进去了。

_注意：_
`<folder>`必须是一个空的文件夹，否者会创建失败

## 写入文章

在 source->\_posts 文件夹下，有一篇 helloworld 的初始化文章，如果想要添加新的文章，

可以在命令行输入`hexo n 'filename'`,就会生成一个新的.md 文件，对它编辑就可以了。

## 本地运行

```bash
hexo s 启动服务器，在本地查看内容
```

在地址栏中输入http://localhost:400/
我们就可以看到博客内容，我们的博客建成功啦！！！

## 配置

打开配置文档\_config.yml，对它做如下修改，repo 后面的内容是 git@gitbub.com:username/库地址的形式

```yml
deploy:
  type: git
  repo: git@github.com:Hugking/Hugking.github.io.git
  branch: master
```

_注意:_
属性和内容之间一定要有一个空格，配置文件有自己的格式规范。

## 上传项目

```bash
npm install hexo-deployer-git --save
hexo g
hexo d
```

### 第一次部署的时候，我们会重点用到 hexo init 这个命令外，在平时写博客和发布过程中最常用的就是：

```bash
hexo n <filename> 新建文章
hexo s 启动服务器，在本地查看内容
hexo g 生成静态页面
hexo d 部署到网站
```

以上四个步骤。

其实以上命令我觉得就足够了，文档里还有很多功能,搭建好后我们在https://用户名(Hugking).github.io就可以看到博客内容。

## 主题选择

在 hexo 官网上下载自己喜欢的 theme，点击图片可以预览主题，点击图片下面的文字就可以打开 github 下载链接。

复制源码的 url，在 git hash 命令窗口下载主题，输入`git clone` url，注意得手动粘贴，`Ctrl+v`无效

接着，将配置文件\_config.yml 中的 theme 改为新的主题的名字，记住一定要将下载下来的文件夹放到 themes 文件夹里！

## 文件解释

新建好的文件夹目录如下：

```
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

**\_config.yml**

博客的配置文件，博客的名称、关键词、作者、语言、博客主题...设置都在里面。

**package.json**

应用程序信息，新添加的插件内容也会出现在这里面，我们可以不修改这里的内容。

**scaffolds**

scaffolds 就是脚手架的意思，这里放了三个模板文件，分别是新添加博客文章（posts）、新添加博客页（page）和新添加草稿（draft）的目标样式。
这部分可以修改的内容是，我们可以在模板上添加比如 categories 等自定义内容

**source**

source 是放置我们博客内容的地方，里面初始只有两个文件夹，一个是 drafts（草稿），一个 posts（文章），但之后我们通过命令新建 tags（标签）还有 categories（分类）页后，这里会相应地增加文件夹。

**themes**

放置主题文件包的地方。Hexo 会根据这个文件来生成静态页面。
初始状态下只有 landscape 一个文件夹，后续我们可以添加自己喜欢的。

## Hexo 命令

**init**

新建一个网站。

```bash
hexo init <folder>
```

**new**

新建文章或页面。

```bash
hexo new <layout> "title"
```

这里的`<layout>`对应我们要添加的内容，如果是 posts 就是添加新的文章，如果是 page 就是添加新的页面。
默认是添加 posts。
然后我们就可以在对应的 posts 或 drafts 文件夹里找到我们新建的文件，然后在文件里用 Markdown 的格式来写作了。

**generate**

生成静态页面

```bash
hexo generate
```

也可以简写成

```bash
hexo g
```

**deploy**

将内容部署到网站

```bash
hexo deploy
```

也可以简写成

```bash
hexo -d
```

**publish**

发布内容，实际上是将内容从 drafts（草稿）文件夹移到 posts（文章）文件夹。

```bash
hexo publish <layout> <filename>
```

**server**

启动服务器，默认情况下，访问网站为http://localhost:4000/

```bash
hexo server
```

也可以简写成

```bash
hexo s
```

## 上传部署

```bash
hexo g
hexo d
```

## 总结

基本流程

```bash
npm install hexo --save *安装hexo*
hexo v *查看是否安装成功*
hexo init *建站*
hexo s *开启服务*
*本地访问(http://localhost:400/)*
npm install hexo-deployer-git --save *保存*
hexo g *生成静态页面*
hexo d *部署*
```

以上就是我踩坑的内容，作为小白，要走的路还很长，加油吧！
