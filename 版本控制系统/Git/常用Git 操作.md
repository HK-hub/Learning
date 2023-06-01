# Git 常用操作

> https://juejin.cn/post/7116098741857157157#heading-14，
>
> https://www.liaoxuefeng.com/wiki/896043488029600/900003767775424#0



### git如何把master合并到自己分支

```markdown
1.切换到主分支
    git checkout master
    
2.使用git pull把master代码拉到本地
    git pull
    
3.切换到自己的分支——>(XXX)
    git checkout XXX

4.使用merge把主分支的代码合并到自己的分支
  git merge master

  注意：合并时有可能会有冲突，解决完冲突才能push

5.最后push，你分支的代码就和主分支的一样了
  git push
```



