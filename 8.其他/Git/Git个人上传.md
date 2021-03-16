## 创建仓库

[参考](https://jingyan.baidu.com/article/48b558e3090a907f38c09a20.html)

```cmd
git config --global user.name "shaobing2021" 
git config --global user.email 403001592@qq.com 
```

1. 本地项目文件夹右键选择git bash here

![image-20200727175751803](https://gitee.com/shaobing2021/typora/raw/master/img/20200728093815.png)

3. git init  
4. git add .  
5. git commit -m “tijiao”
6. git remote add origin  https://github.com/shaobing2021/JavaCode.git
7. git push origin master

## 再次上传

```cmd
git pull

gitb add .  
git commit -m “tijiao”
git push origin master
```

## Git克隆

`$ git clone https://github.com/shaobing2021/JAVA-.git`

### Git更新

通常来说如果要上传，则先更新再上传

```cmd
`dell@DESKTOP-I9GT3EC MINGW64 /f/Git/JAVA- (master)`
`$ git pull`
```

```
…or create a new repository on the command line
echo "# code" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/shaobing2021/code.git
git push -u origin master
                
…or push an existing repository from the command line
git remote add origin https://github.com/shaobing2021/code.git
git push -u origin master
…or import code from another repository
You can initialize this repository with code from a Subversion, Mercurial, or TFS project.
```

### code仓库

```
 git clone https://github.com/shaobing2021/code.git
```

