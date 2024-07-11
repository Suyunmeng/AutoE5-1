### 项目说明 ###修复GitHub官方提示弃用版本
* 利用github action实现定时自动运行api调用，保持E5开发活跃。
* 免费，不需要额外设备/服务器，部署完不用管啦。
* 加密版，隐藏应用id+机密，保护账号安全
* 注意workflow中的提交者请改为自己，现有账号的api调用可能无效，workflow中的提交者可以添加github提交贡献从而实现续订（E5绑定Github账户后有效）

### 特别说明/Thanks ###
* 原教程博主-黑幕（酷安id-Paran）：https://blog.432100.xyz/index.php/archives/50/
* 普通版地址：https://github.com/wangziyingwen/AutoApi
* 加密版地址：https://github.com/wangziyingwen/AutoApiSecret
* 更新日志/旧版：https://github.com/wangziyingwen/Autoapi-test

### 教程总结 ###
```
Fork AutoApiSecret到github；

Azure注册应用，选择任何组织目录，重定向url选web，填入http://localhost:53682/，注册，保存id和机密；

设置应用权限，选择files.read.all files.readwrite.all sites.read.all sites.readwriter.all user.read.all user.readwrite.all directory.read.all directory.readwrite.all mail.read mail.readwrite mailboxsetting.read mailboxsetting.readwrite，全部勾选，应为原有1个+12个，共计13个，授予权限；

rclone执行命令.\rclone authorize "onedrive" "id" "机密"，弹出确认框，确认后返回refresh_token；

回到github自己项目下，修改1.txt，将refresh_token粘贴进去；

对项目setting项，分别增加secretCONFIG_ID id=r'id' CONFIG_KEY secret=r'机密'；

点击用户头像，进入用户setting项，选择developer setting，选择personal access tokens，增加GITHUB_TOKEN,选择repo、admin：repo_hook、workflow，生成token；

回到github自己项目下，star项目后，进入actions，随后刷新页面出现workflow项目，施工完成后，点击进入，查看testapi，是否十次运行成功；

默认设置为周一到周五，每六小时运行三轮，可自行修改crontab 12 */6 * * 1-5。

本文链接：
```
### 区别 ###
  项目用的是公共仓库（开放代码），所有人都能看到你的代码内容。

  所以你的应用id、机密、令牌都会显示出来，不安全。

  加密版，我把应用id、机密都隐藏了，令牌因为需要实时更新，隐藏不了（我不会！），安全性会高很多！

--------------------------------------------------------------

### 步骤 ###

* 登陆/新建github账号，回到本项目页面，点击右上角fork本项目的代码到到你自己的仓库，

  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/fork.png)
  
* 根据原文教程获取应用id+机密（自己复制保存），并修改你自己项目里的1.txt。（不要改1.py）
  
* 依次点击上栏Setting > Secrets > Add a new secret，新建两个secret如图：CONFIG_ID、CONFIG_KEY。

  内容分别如下: ( 把你的应用id改成你的应用id , 你的应用机密改成你的机密，单引号不要动 )
  
  CONFIG_ID
  ```shell
  id=r'你的应用id'
  ```
  CONFIG_KEY
  ```shell
  secret=r'你的应用机密'
  ```
  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/机密.png)
  
* 进入你的个人设置页面(右上角头像 Settings，不是仓库里的 Settings)，选择 Developer settings > Personal access tokens > Generate new token,
  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/Settings.png)
  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/token.png)

  设置名字为GITHUB_TOKEN , 然后勾选 repo , admin:repo_hook , workflow 等选项，最后点击Generate token即可。
  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/repo.png)
  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/adminrepo.png)
  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/workflow.png)
  
* 点击右上角星星/star立马调用一次，再点击上面的Action就能看到每次的运行日志（都看下，api有没有调用到位，有没有出错）
  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/日志.png)

* 没出错的话，就搞定啦，接下来就不用管了。（下面看不懂就不用管啦）

  我设定的每6小时自动运行一次，每次调用3轮（点击右上角星星/star也可以立马调用一次），你们自行斟酌修改（我也不知道活跃要调用多少、多久）：

  · 定时自动启动修改地方：（在.github/workflow/autoapi.yml文件里，自行百度cron定时任务格式）
   
  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/定时.png)
   
  · 每次轮数修改地方：（在1.py最后面）
   
  ![image](https://github.com/wangziyingwen/ImageHosting/blob/master/AutoApi/次数.png)
  
------------------------------------------------------------
### 题外话 ###
> Api调用
  你们可以自己去graph浏览器看一下，学着自己修改要调用什么api(最重要的是调用outlook、onedrive)
  https://developer.microsoft.com/zh-CN/graph/graph-explorer/preview

### GithubAction介绍 ###
提供的虚拟环境：

· 2-core CPU
· 7 GB RAM 内存
· 14 GB SSD 硬盘空间

使用限制：
* 每个仓库只能同时支持20个 workflow 并行。
* 每小时可以调用1000次 GitHub API 。
* 每个 job 最多可以执行6个小时。
* 免费版的用户最大支持20个 job 并发执行，macOS 最大只支持5个。
* 私有仓库每月累计使用时间为2000分钟，超过后$ 0.008/分钟，公共仓库则无限制。

（我们这里用的公共仓库，按理，你们可以设定无限循环调用，然后6小时启动一次，保证24小时全天候调用）

### 最后 ###
  教程很直白了，应该都会弄吧。有空更新一下怎样网页获取refresh_token（不再需要rclone？）！所以不用电脑应该可以都搞定！

  我是代码小白，如有问题，请各位多多包涵。有没有大佬能修改一下代码呀，十分欢迎！
  
  这整个项目都是我根据以往看过的代码整合的，ORZ。
  
  有修改建议可以发邮件给我:
  wz.lxh@outlook.com
  
  基安id-卷腿毛菌
  
  ### 教程链接 ###
  https://51.ruyo.net/15646.html
