不得不说现在的各类IDE都越来越强大，集成了各类功能，对于Git的集成也越来越完善，基础的命令都能使用图形化“点点点”，但git也不止用来提交代码，就像平时的markdown文件我也存放到github或gitee上，因此今天也整理下git的命令，以后也不用临场Google了

（说了这么多，其实我是没什么可以内容可以更了，水一篇）

```bash
# 初始化本地仓库 
git init                             

# 查看本地库状态
git status                            

# 添加文件到暂存区 
git add 文件名                       

# 提交到本地库 
git commit -m "日志信息" 文件名        

# 查看历史记录 
git reflog                           

# 查看版本详细信息 
git log                              

# 撤销commit
git reset --soft HEAD^               

# 撤销add
git reset --mixed HEAD^              

# 回到上一次commit  
git reset --hard HEAD^               

# 修改注释
git commit ---amend                  

# 版本穿梭          
git reset --hard 版本号              

# 查看本地所有分支 
git branch                           

# 查看远程所有分支
git branch -r                         

# 查看分支信息，附带版本 
git branch -v                        

# 查看本地和远程所有分支 
git branch -a                        

# 创建分支，停留在当前分支
git branch 分支名                    

# 创建并切换到新分支
git branch -f 分支名                   

# 删除本地分支
git branch -d\D 分支名              

# 将本地分支推送至远程分支
git branch 本地分支:远程分支名       

# 删除远程分支
git branch :远程分支名              

# 重命名分支，如是远程分支，则先删除后推送新分支
git branch -m\M 旧分支名 新分支名     

# 切换分支
git checkout 分支名                  

# 把指定的分支合并到当前分支上
git merge 分支名                     

# 查看当前所有远程地址别名
git remote -v                         

# 起别名
git remote add 别名 远程地址         

# 推送本地分支上的内容到远程仓库 
git push 别名 分支                   

# 删除远程分支 
git push origin --delete 远程分支名  

# -u指定默认主机，以后可直接使用git push
git push -u 别名 分支                

# 删除远程分支，等同git push 别名 --delete 分支
git push 别名:分支                   

# 将远仓库的内容克隆到本地
git clone 远程地址                   

# 将远程仓库对于分支最新内容拉下来后于当前本地分支直接合并
git pull 远程库地址别名 远程分支名  
```

