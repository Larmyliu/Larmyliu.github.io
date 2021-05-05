想利用hexo搭建一个个人博客，但是发现hexo init的时候一直被refused了。
但是在git里面查了`git config --global http.proxy`并没有显示我有用代理
查了端口号什么的都显示没有

最后，找到一个方法
在电脑查找`.gitconfig`文件，要在c盘的那个，点开来把`proxy`全部删掉，再hexo init 就成功了
