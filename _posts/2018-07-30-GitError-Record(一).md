

\-\-\-

title: "git 提交提示master does not match any"

date:  2018-07-30

author:  "ray"

tags:

 \- 技巧教程

 \- git

\-\-\-



### 1. 报错信息

```
error: src refspec master does not match any.
error: failed to push some refs to 'git@github.com
```

### 2. 错误原因

```
不详
```

### 3. 解决方案

```
touch README
git add README 
git commit -m 'first commit'
git push origin master
```

### 4. 网路上的其他说明

> 一般而言,建造repo 和本地仓库的流程如下:

```
1、在github上创建项目

2、使用git clone https://github.com/xxxxxxx/xxxxx.git克隆到本地

3、编辑项目

4、git add . （将改动添加到暂存区）

5、git commit -m "提交说明"

6、git push origin master 将本地更改推送到远程master分支。
```

`如果在github的remote上已经有了文件，会出现错误`



### 5. 参考链接

1. //www.cnblogs.com/renkangke/archive/2013/05/31/conquerAndroid.html

2. https://www.jianshu.com/p/8d26730386f3

3. https://stackoverflow.com/questions/21264738/error-src-refspec-master-does-not-match-any


