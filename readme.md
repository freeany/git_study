### git开始

1. 安装git

2. 设置git的用户名和密码

   ```js
   git config --global user.name "Your Name"
   git config --global user.email "email@example.com"
   ```

​		--global表示这台机器上所有的仓库都使用这个配置。也可以单独给某个仓库设置配置

3. 查看git的用户名和密码

   ```js
   git config user.name
   git config user.email
   ```

4. 为了避免莫名其妙的问题，请避免文件目录结构中不要有中文

5. `git status`命令用于显示工作目录和暂存区的状态。使用此命令能看到那些修改被暂存到了, 哪些没有, 哪些文件没有被Git tracked到。

6. `git add .`   将文件添加进暂存区

7. `git commit -m 'xx'`   将暂存区的内容添加到本地仓库(当前分支对应的本地仓库)

   - 我们可以多次add将文件添加到暂存区，然后使用一次commit进行提交到本地仓库。
   - commit可以理解为保存一个快照。当我们文件改错了，或者需要回滚都可以根据commit id进行回滚

8. git中看到HEAD代表当前版本。上一个版本就是`HEAD~` ,  上上一个版本就是`HEAD^^` , 以此推类。 `HEAD~100` 代表回退从当前版本往前100个版本。

   - 回退版本

     ```js
     git reset --hard HEAD^
     ```

9. `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`

10. 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。

11. 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。



### 暂存区

使用git add 命令把要提交的所有修改放到暂存区（Stage）

<img src="https://www.liaoxuefeng.com/files/attachments/919020074026336/0" />



使用git commit 命令就可以一次性把暂存区的所有修改提交到分支。

<img src="https://www.liaoxuefeng.com/files/attachments/919020100829536/0" />



### 管理修改

`git commit`只负责把暂存区的修改提交了

也就是说如果`git add a.md`之后，`a.md`文件放入了暂存区，然后又修改了`a.md`文件，然后用`git commit`进行提交,  此时使用`git status` 查看工作目录和暂存区的状态会发现,  `a.md`的第二次修改还在工作区，没有被commit进去。

![image-20220511161039378](/Users/lihaoran/Library/Application Support/typora-user-images/image-20220511161039378.png)



我们可以在一次修改之后，将文件添加进暂存区，此时不急着提交，当我们第二次修改过后，将第二次的修改也添加进暂存区，这样就相当于把两次修改合并到一起提交了。



### 丢弃更改

1. 丢弃工作区的文件的更改，此文件未被`git add` 到工作区，想要丢弃更改

   `git restore 文件名` 丢弃指定文件的更改

   `git restore .`  丢弃当前工作区所有文件更改

   此方式直接放弃工作区的修改，无法恢复

2. 丢弃暂存区的文件的更改，此文件被`git add `到了工作区, 但是未commit，想要丢弃更改

   > 当我们使用`git add .`  之后再使用`git status` 查看文件状态，此时文件的颜色变成蓝色。

   `git restore --staged 文件名 `  丢弃指定文件的更改

   `git restore --staged .`   丢弃当前暂存区所有文件的更改

3. 如果我们不小心在文件管理器中删除了一个文件，想要恢复这个被删除的文件

   `git restore 文件名`    恢复删除的文件

   不过如果修改了这个文件，那么就无法进行恢复你写的修改记录了。

### git stash

两种情况： 

```js
1. 当你在A分支上写代码的时候，如果突然出了个bug，需要去b分支上改，但是你不想使用commit进行提交
	 可以使用git stash对代码进行贮藏
				注: 切分支的时候，如果当前的工作区不干净(修改了代码，但是没有commit或stash)的话，是无法切分支的
2. 当我们在A分支上写了代码，但是没有提交，但是我们想把修改的代码切到另一个分支上，可以使用git stash将当前修改的代码进行贮藏，然后切	 换分支使用git stash pop将贮藏的代码pop到当前分支。如果我们修改了同一行文件，则会有冲突。
```



我们多次使用`git stash`来贮藏代码， 使用`git stash list` 来查看贮藏的列表。

​	注意: 不要使用`git stash` 来多次贮藏同一个文件中同一行代码，不然在当前分支多次pop的时候会有冲突，当然要视情况而定。

使用`git stash` 贮藏代码之后， 有两种方式恢复

1. `git stash apply`

   - 这种方式stash的内容不会删除，需要使用 `git stash drop` 来删除

2. `git stash pop`

   - 恢复的同时也把stash中的内容删了。

3. 如果多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash

   `git stash apply stash@{0}`



### 将A分支的一个commit合并到B分支

> 场景： 从release拉的分支后改完bug，改完之后这个分支就删掉了，后来发现dev上也有这个bug，那么就可以从release分支上拿到这个commit id，然后合并到dev分支上。 

1. 先切换到A分支

   `git checkout A`

2. 找出要合并的commit ID

   `git log`

   得到`331cd123ada23asda21312`

3. 切换到B分支

   `git checkout B`

4. 将commit记录合并到B分支

   `git cherry-pick 331cd123ada23asda21312 `  



### 删除分支

1. ```
   git branch -d f-lhr
   ```

   如果该分支没有被合并过，那么无法删除

   - 此时可以强行删除

   ```js
   git branch -D f-lhr
   ```

2. 删除远程分支

   `git push origin -d 分支名`

### 查看当前分支的祖先级分支



![image-20220512111410490](/Users/lihaoran/Desktop/image-20220512111410490.png)



HEAD指向当前分支， 紫色的代表当前分支的父分支，祖父分支....以此类推。

黄色括号中用逗号分隔的分支名，代表指向同一个对象，即代码都是相同的，没有多的commit记录。



### git rebase合并提交记录

> git rebase最好只在自己的分支进行合并提交记录，如果在发版分支上修改，那么此操作会修改发版分支的提交记录。

![image-20220512132102450](/Users/lihaoran/Library/Application Support/typora-user-images/image-20220512132102450.png)

有这么一个场景，我们想要将红色框中的commit记录合并为一个 ` "修改ios附件上传bug"`

![image-20220512132310834](/Users/lihaoran/Library/Application Support/typora-user-images/image-20220512132310834.png)

我们找到这些分支的上级分支 `456dd0a` 这个commit id， 然后执行 `git rebase -i 456dd0a`, 会进入vim编辑器

![image-20220512132457608](/Users/lihaoran/Library/Application Support/typora-user-images/image-20220512132457608.png)



第一个为pick， 其他的修改为s

![image-20220512132613530](/Users/lihaoran/Library/Application Support/typora-user-images/image-20220512132613530.png)



出现冲突

![image-20220512132641992](/Users/lihaoran/Library/Application Support/typora-user-images/image-20220512132641992.png)



解决冲突之后，使用`git add .` 将解决冲突后的文件添加到暂存区。注意，不要commit。然后执行 `git rebase --continue`

出现这样一个界面

![image-20220512132846669](/Users/lihaoran/Library/Application Support/typora-user-images/image-20220512132846669.png)



使用# 将不必要的提交message注释，然后加入新的提交message

![image-20220512133025150](/Users/lihaoran/Library/Application Support/typora-user-images/image-20220512133025150.png)



变基成功。



### git 提交空文件夹

在文件夹下新建 `.giitkeep` 文件



### 回滚某次commit提交 

```
git revert commtId  # 撤销一条记录 会弹出 commit 编辑
										# 实质是新建了一个与原来完全相反的commit，抵消了原来commit的效果
git push # 提交回滚
```

![image-20220512143352214](/Users/lihaoran/Library/Application Support/typora-user-images/image-20220512143352214.png)



### 模糊查询commit记录

```
git log --grep=224
# 这条命令是查看含有 "224" 关键字的 git commit 
```



### git rebase

`git rebase --abort ` 会放弃rebase，回到rebase操作之前的状态，之前的提交的不会丢弃；

`git rebase --skip` 则会将引起冲突的commits(放弃自己的commit)丢弃掉（慎用！！）；

`git rebase --continue ` 合并冲突，结合`git add 文件`命令一起用与修复冲突，提示开发者，一步一步地有没有解决冲突。`（fix conflicts and then run "git rebase --continue"）`



### 多次commit使用同一个message

1. 当我们修改文件之后，对文件进行add，commit之后。发现还有文件没有修改，但是我们不想在多一次commit。可以用这个命令:

   ```jsx
   // 修改文件
   git add .
   git commit --amend --no-edit
   // 然后在用git log --onelie进行查看，可以看到我们第二次修改的代码也被commit进去上一次，而且没有产生新的commit信息。
   ```

   如果不加 —no-edit， 则会弹出vim编辑器，可以更改上次的commit的message信息。

   注意一点：上面的操作会改变commitId， 其他的没啥。



### 单个文件在reset中来回切换

1. reset是在任意commit之间来回切换，checkout可以在单个文件进行来回切换

   ```jsx
   git checkout commitId  -- 文件名
   ```

   然后我们git add .   git commit -m ‘xx’
