# gerrit修改提交代码
## 个人用户首次提交gerrit流程

#### 1.生成公私钥对
 

```bash
ssh-keygen -t rsa -C "xin.gao_a@**.com"
```

输入命令后会提示创建密码访问公私钥，一路回车就可以(不加密码)。
默认公私钥放到~/.ssh目录，名称为id_rsa、id_rsa.pub。*.pub文件为公钥，这个是我们需要复制粘帖到gerrit账户的。
#### 2. 获取公钥并设置

```bash
cat ~/.ssh/id_rsa.pub
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/9337fb1c80e343c794cf8b2bdf554ea7.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/28d9143ff798447a9ab06dc9b07d4bc8.png)
把复制的公钥粘帖到下面的框中
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/58363ba0fe484ef3859b2fa2002458b0.png)
#### 3. 拉取项目
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/baffc8cb600b48ad8cb11aeb6c7bf9f8.png)
打开要克隆的项目目录点击ssh并复制命令
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ef5531486dca4dc5b4c0f3ad0c1517bf.png)

```bash
git clone ssh://xin.gao_a@192.168.87.56:29418/IV/industry/algo/snd_avxp_xray
```
#### 4. 提交项目前配置
- 配置当前的xin.gao_a用户
```bash
git config user.name xin.gao_a
```

```bash
git config user.name xin.gao_a@**.com
```

- git config --list来查看一下当前git仓库的配置
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/4f5b09983dcd41b7858e39609b2313a6.png)

- 将push的线设置为refs/for/master

```bash
git config remote.origin.push refs/heads/:refs/for/
```

- 用scp命令从gerrit服务器上拉取当前用户的hooks文件(必须要生成Chang-Id)

```bash
scp -p -P 29418 xin.gao_a@192.168.87.56:hooks/commit-msg
```

- 查看.git是否存在hooks

```bash
ls -l .git #在拉取项目的目录里执行该命令
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/755ddccbc8514a39a59a816fd768dae6.png)
#### 5. 提交项目
至此可以将更改后的代码提交到gerrit
- 上传代码

```bash
git add . # 添加所有文件
```

```bash
git commit -m "本功能全部完成" # 如果缺少chang-ID，见第4条
```

```bash
git push origin HEAD:refs/for/远程分支名
```
##### 注：
- 如果出现下面错误
  You are not allowed to perform this operation [remote rejected] master -> master (prohibited by Gerrit)
这是因为没有将push的线设置为refs/for/master，执行如下命令：
	```bash
	git config remote.origin.push refs/heads/:refs/for/
	```
- 如果出现missing Change-Id in commit message footer
  commit一定要有Change-Id，执行命令：
	```bash
	scp -p -P 29418 xin.gao_a@192.168.**.**:hooks/commit-msg .git/hooks/
	```
- 如果执行完上面命令push后还是一样的错误，这证明还停留在上次的commit，需要撤回commit

	```bash
	git reset --soft HEAD~1  #撤回1个commit，想撤回两个就将1改为2
	```
- 查看之前用户的commit
	```bash
	git log
	```
- 只下载代码更新的部分
  - 使用fetch命令从远程仓库获取更新，但不合并到你的本地分支中：
  - `git fetch origin`
  - 使用pull命令将远程分支的更新合并到你的本地分支中：
  - `git pull origin <branch-name>`
