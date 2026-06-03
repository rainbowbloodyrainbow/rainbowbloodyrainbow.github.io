这是我使用quartz工具搭建的第一篇博客，说得浪漫些，这可是很有纪念意义的梦开始的地方。那么在这最开始的开始，我想先介绍一下我是怎么用quartz搭起来这个博客的。

前置要求：有github账号，初步掌握git使用（可参考[[git使用]]），会使用Obsidian写笔记。

> [!quote] 你说得对，但是quartz是一个完美支持Obsidian Markdown语法的静态网页生成器，这是我选择它的原因。
> 
> ——ClBrI

什么，你不用Obsidian记笔记，甚至你不知道Obsidian是什么？那么你要么是一个使用typora的异端，要么是一个不愿意拥抱markdown的nerd！趁现在，转身，逃跑，否则我作为Obsidian的忠实拥护者，会顺着网线追杀你到天涯！呃，或者海角？(666，这期神了)

---

好了，玩笑到此为止，进入正题。就像把大象装进冰箱一样，如果你想使用quartz搭建博客，可以按以下三步来操作：
## Step1 部署quartz

### 1.克隆仓库
使用`git clone https://github.com/jackyzha0/quartz.git`命令克隆 Quartz 仓库，如果对路径有要求的话，加上目标路径，如
```sh
# 克隆仓库到指定路径
git clone https://github.com/jackyzha0/quartz.git "E:\Quartz"
```
### 2.安装依赖库
首先cd到克隆出的quartz仓库所在目录，比如我的在`"E:\Quartz"`，那就这样(我用cmd用习惯了，如果你喜欢用PowerShell或Bash的话，可以不切换盘符，直接cd到那个目录)
```sh
# 切换盘符
E:
# 进入目录
cd "E:\Quartz"
```
然后在这个目录输入`npm i`安装quartz运行所依赖的库，等待十几秒，以我安装时为例，成功后的终端信息如下
```sh
E:\Quartz>npm i

added 483 packages, and audited 484 packages in 19s

175 packages are looking for funding
  run `npm fund` for details

8 vulnerabilities (2 moderate, 6 high)

To address all issues, run:
  npm audit fix

Run `npm audit` for details.
```
`added 483 packages... in 19s`表明核心依赖已成功安装，这是最关键的信息，说明 `npm i` 执行成功。

`175 packages are looking for funding`是开源库寻求赞助的提示，有经济的朋友可以去支持一下。

`8 vulnerabilities (2 moderate, 6 high)`这是 Node.js 生态中极其常见的安全警告，可以运行 `npm audit fix` 尝试自动修补，不过实际的运行风险据说不大，所以也可以暂时先不管。

### 3.初始化quartz配置
`npm i` 执行成功后，依旧是在这个目录下面，我们输入`npx quartz create`来初始化配置。
输入完命令后，会弹出类似于下图的选项，我们选择默认项`Empty Quartz`即可，按下回车，之后它会在目录下生成一个content文件夹，里面含有一个index.md（Quartz 和绝大多数静态网站生成器一样，需要一个名为 index.md 的文件作为整个网站的默认主页，所以千万不要删了它或者给它改名字）
![[quartz配置.png]]
(当时忘截图了，新建一个临时文件截图代替)

之后是Quartz解析Markdown文件中的双括号内部链接的方式，如果我们在Obsidian里没有修改过的话，默认是Shortest path，所以这里选择默认项即可。
![[quartz配置2.png]]
按下回车，片刻后它会弹出如下信息，表示配置已经完成。
```sh
—  You're all set! Not sure what to do next? Try:
  • Customizing Quartz a bit more by editing `quartz.config.ts`
  • Running `npx quartz build --serve` to preview your Quartz locally
  • Hosting your Quartz online (see: https://quartz.jzhao.xyz/hosting)
```

---

## Step2  本地测试
输入`npx quartz build --serve`命令，终端中会显示类似这样的信息：
```sh
D:\softwares\quartz>npx quartz build --serve

 Quartz v4.5.2

Cleaned output directory `public` in 42ms
Found 2 input files from `content` in 16ms
⠼ Parsing input files using 1 threads
Warning: content/欢迎.md isn't yet tracked by git, dates will be inaccurate
Parsed 2 Markdown files in 433ms
Filtered out 0 files in 52μs
Emitted 17 files to `public` in 526ms
Done processing 2 files in 1s
Started a Quartz server listening at http://localhost:8080
hint: exit with ctrl+c

```
主要值得关注的是`Started a Quartz server listening at http://localhost:8080`这句，打开http://localhost:8080，就可以看到你的markdown笔记了。

---

## Step3  github pages部署网页
### 1.建立github仓库
在github上建立一个公开仓库，仓库名最好是`用户名.github.io`，比如我的就是`rainbowbloodyrainbow.github.io`，否则会带来一点点麻烦——简单地讲，仓库名和用户名一致的话，你的网址是`https://用户名.github.io/`，不一致的话，你的网址就是`https://用户名.github.io/仓库名/`，`用户名.github.io`是`github.io`这个根域名分配给每个用户的子域名，`用户名.github.io/仓库名/`是这个子域名再细分出的一个子目录，而且由于Quartz 默认自己是运行在“根域名”下的，所以如果使用子域名的话需要点进`quartz.config.ts`，找到`baseUrl`，在冒号后面手动改成`"用户名.github.io/仓库名"`。

回到正题，建好后在Settings里左侧边栏选Pages，然后在Build and deployment下source的下拉菜单里，把Deploy from a branch换成GitHub Actions。

### 2.deploy.yaml文件
首先在`E:\Quartz\.github\workflows`里建一个deploy.yaml文件，填入
```yaml
name: Deploy Quartz site to GitHub Pages

on:
  push:
    branches:
      - main

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # 获取完整 git 历史以确保笔记日期正确
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - name: Install Dependencies
        run: npm ci
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public

  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
然后保存。

### 3.git操作
在仓库目录下，依次输入以下git命令
```sh
# 1. 初始化全新的 Git 仓库
git init

# 2. 添加所有更改到暂存区
git add .

# 3. 提交更改
git commit -m "Initial Quartz setup and fix deploy workflow"

# 4. 将本地默认分支重命名为 main
git branch -M main

# 5. 关联远程仓库
git remote add origin https://github.com/你的用户名/你的仓库名.git
# 这里一般会失败，显示error: remote origin already exists.
# 不过不要紧，把add换成set-url，再试一次即可

# 6. 推送到 GitHub
git push -u origin main
```
push成功后去github仓库看看，Action里多了这几项，黄色代表正在执行![[quartz配置3.png]]
如果不出意外的话，一两分钟后会变成绿色，说明执行成功。像这样![[quartz配置4.png]]

然后在浏览器输入你的网址，比如`https://rainbowbloodyrainbow.github.io/`，就可以在线上看到你自己搭的博客了

此后每次更新，只需要在Obsidian里写好博客，依次输入`git add .`、`git commit -m "你的提交信息"`、`git push`(或者不爱用命令行的话，也可使用Obsidian、Vscode的图形化git插件等)，把更新同步到github上，网页内容就会自动更新了。