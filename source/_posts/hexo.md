---
title: 如何部署和使用Hexo+GitHub创建个人博客
date: 2019-03-14 22:49:24
tags: [GitHub]
---
# 部署
## GitHub设置

1. 在GitHub上创建一个项目，项目名称必须为**自己的GitHub用户名.github.io**，即一个GitHub Pages仓库。
2. 等10-30分钟，之后使用**自己的GitHub用户名.github.io**就能访问到这个GitHub Pages了。

## 安装Hexo

1. 全局安装Hexo。
    ```
    npm install -g hexo-cli
    hexo -v  // 查看Hexo版本
    ```
2. cd进入Hexo源文件目录，初始化Hexo。
    ```
    hexo init
    ```
3. 生成可以发布的静态HTML文件。
    ```
    hexo g
    ```
4. 开启Hexo本地服务。
    ```
    hexo s
    ```

## 关联Hexo和GitHub Pages

1. 生成SSH key。

    Linux系统下个人密钥默认在`/home/用户名/.ssh`目录下，Windows系统下个人密钥默认在`C:/Users/用户名/.ssh`下。如果没有公私钥可以在Linux下用`ssh-keygen`命令生成。
2. 将公钥`.ssh/id_rsa.pub`的内容复制，打开GitHub的个人设置-SSH and GPG keys-New SSH key，粘贴并保存。可在命令行用`ssh -T git@github.com`测试是否成功。
3. 设置Git的**username**和**email**。
    ```
    git config --global user.name "你的用户名"
    git config --global user.email "你的email"
    ```
4. 部署设置。打开Hexo源文件目录下的**_config.yml**，找到deploy部分。
    ```
    deploy:
      type: git
      repository: git@github.com:ltzdby/ltzdby.github.io.git  // 注意：这里不能使用https链接
      branch: master
    ```
5. 此时可以使用`hexo -g`然后`hexo -d`尝试部署，如果提示`Deployer not found: git`那需要安装一个插件：
    ```
    npm install hexo-deployer-git --save
    ```
再尝试一下，应该OK了。

<!--more-->

# 使用Hexo

## Hexo基本命令

1. 生成静态文件。
    ```
    hexo g
    ```
2. 开启Hexo本地服务。
    ```
    hexo s
    ```
3. 部署到远程。
    ```
    hexo d
    ```
4. 清除缓存。
    ```
    hexo clean
    ```
5. 写新文章。
    ```
    hexo new "文章文件名"
    ```

## 使用技巧

1. 设置文章category、tags、comments等信息。

    在每篇文章的头部都有由两个`---`包围起来的部分，其中**title:**设置的是文章标题，**date:**设置的是日期，**category:**可以设置分类，**tags:**可以设置文章的tags。但是需要注意的是，**title:**、**date:**等的冒号后必须空一格，否则不识别。

    如果有多个tag，可以这么写：
    ```
    tags: [tag1, tag2]
    ```
    也可以这么写：
    ```
    tags:
        - tag1
        - tag2
    ```
    如果某篇文章不想打开评论，可以使用：
    ```
    comments: false
    ```
2. 文章太长，导致首页内容特别多。

    可在文章合适的地方加上`<!--more-->`。

3. GitHub提供了自定义域名功能。

    - 首先，将你要转向的地址的域名解析**CNAME记录**指向**你的用户名.github.io**。
    - 然后，GitHub要求这个GitHub Pages仓库根目录下必须有一个文件名为**CNAME**的文件，内容为你要转向的域名。可以在Hexo源文件目录的source目录下新建这个文件，Hexo生成和部署时会直接将这个文件上传至仓库根目录。
    - 这样，访问**用户名.github.io**就会跳转到你指定的域名了。但实际上，访问的还是GitHub服务器上的这个GitHub Pages仓库。

4. 将Hexo源文件目录也放到GitHub上进行版本控制。

    笔者就有惨痛的教训，手贱删除了原来的Hexo源文件目录，恢复无望以至于博客有一段时间就懒得再拾起来了。
    - 在Hexo基本部署完以后，在**部署上去的文件**所在的那个**用户名.github.io**仓库新建一个分支，比如叫**hexo**，并在仓库Settings里将其设置为默认分支。
    - 将这个仓库clone到本地，默认是一个**用户名.github.io**目录，进入这个目录，用`git branch`命令查一下，应该是在**hexo**分支上的。然后，将**Hexo源文件目录**内所有内容全部复制到这个目录下。
    - 执行`git add .`、`git commit -m "xxx"`、`git push`将其提交，这样，仓库的**master**分支就存储发布的静态内容，**hexo**分支就存储Hexo源文件目录的内容，再也不怕手贱删除Hexo源文件目录了。
    - 编辑、发布文章时，在Hexo和git各提交一遍即可。有可能需要`hexo clean`。

# 使用Hexo的Next主题

## 安装Next主题

1. 使用git克隆最新版本
    ```
    git clone https://github.com/theme-next/hexo-theme-next themes/next
    ```
2. 或者：前往Next主题的[GitHub release页面](https://github.com/theme-next/hexo-theme-next/releases)，选择需要的版本的Source code下载到本地，并解压到Hexo源文件目录的themes目录下，并将目录改名为**next**。
3. 但是，对于我们把Hexo源文件目录也放到GitHub上托管的策略来讲，上述方法1是不可行的，因为git不允许一个仓库下还有一个仓库；方法2虽然可行，但是没办法与Next上游同步更新，对于一些小bug修复和功能增加、改进无法及时跟进。这里我采用了git的submodule处理一下。

    由于submodule仅仅是在仓库里创建一个指向submodule的链接，而使用Next主题需要对主题目录下的**_config.yml**配置文件进行修改，我们总不能把Next开源项目仓库里的配置文件给修改了吧（而且我们也没有权限这么做），所以我们需要先fork一下Next主题到自己的GitHub账户下，然后通过`git submodule add https://github.com/用户名/hexo-theme-next themes/next`将Next主题作为子模块添加到Hexo里。修改主题配置后，需要进入主题目录（也就是子模块里），执行`git add .`、`git commit -m "xxx"`、`git push`，然后再到Hexo下进行git操作。
    
    这时与Next新版本同步就很简单了，将fork的仓库与Next官方仓库同步即可。

## 启用主题

在Hexo配置文件（即Hexo源文件目录下的**_config.yml**文件）里搜索**theme**关键词，修改为：
```
theme: next
```

