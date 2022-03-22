---
date: 2021-04-16

tag: linux, command

author: matthew

---

## 你必须要知道的30个 Linux 命令

1. pwd

> 列出当前所在目录
> ```bash
> $ pwd
> /mnt/c/Users/Matthew/Desktop
> ```

2. cd

> 文件夹切换
> ```bash
> root@matthew:/mnt/c/Users/Matthew$ cd Downloads/
> root@matthew:/mnt/c/Users/Matthew/Downloads$
> ```
> 快捷方式：
> ```bash
> cd ..   # 返回上一层
> cd      # 直接到home目录
> cd -    # 退回到上一次目录
> ```

3. ls

> 列出当前文件夹内容
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Videos$ ls
> Captures  desktop.ini
> ```
> 快捷方式
> ```bash
> ls -R  # 列出当前文件夹以及当前文件夹子文件夹内容
> ls -a  # 列出隐藏文件
> ls -al # 列出当前文件夹内容以及详情，包括文件权限，大小，拥有者等等
> ```

4. cat

> 通过标准输出查看文件内容
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ cat 1.txt
> 123
> ```
> 如何使用
> ```bash
> cat >4.txt   				# 创建新文件4.txt
> cat 1.txt 2.txt>3.txt 	# 把1.txt和2.txt文件内容拼接起来输出到新文件3.txt
> cat 1.txt | tr a-z A-Z >3.txt # 将1.txt文件内容小写转大写，然后输出到3.txt
> ```

5. cp

> 把当前文件夹下文件复制到其他文件夹下
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ cp 2.txt .. # 把当前文件夹下2.txt复制到上一层目录下
> ```

6. mv

> 主要用于移动文件，也可以用来重命名文件
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ mv 2.txt .. # 把当前文件夹下2.txt移动到上一层目录下
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ mv 1.txt out.txt # 将当前文件夹下1.txt文件名改为out.txt
> ```

7. mkdir

> 创建文件夹
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ mkdir dir1 
> ```
> 快捷方式
> ```bash
> mkdir p1/p2  # 创建文件夹p1的子文件夹p2
> mkdir -p p1/parentDir/p2 # 在已经创建的文件夹p1和p2之间创建新文件夹parentDir, p2成为parentDir的子文件夹
> ```

8. rmdir

> 删除文件夹，但是只允许你删除空文件夹
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ rmdir dir1
> ```

9. rm

> 删除文件夹以及文件夹内容
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ rm 2.txt
> ```
常用命令
> ```bash
> rm -r dir2  ## 删除文件夹dir2以及内容
> rm -rf dir2 ## 强制删除文件夹dir2以及内容
> ```

10. touch

> 创建新文件
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ touch index.js
> ```

11. locate

> 搜索文件
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ locate -i in*x.html
/usr/lib/python3/dist-packages/twisted/python/_pydoctortemplates/index.html
/usr/share/doc/adduser/examples/adduser.local.conf.examples/skel.other/index.html /usr/share/doc/gdisk/index.html
/usr/share/doc/python3/python-policy.html/index.html
/usr/share/doc/shared-mime-info/shared-mime-info-spec.html/index.html
> ```
注意
> locate 与 find 不同: find 是去硬盘找，locate 只在 /var/lib/slocate 资料库中找
> locate 的速度比 find 快，它并不是真的查找，而是查数据库

11. find

> 在给定的文件夹内搜索文件或文件夹
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ find . -name 3.txt
./3.txt
> ```
常用命令
> ```bash
> find . -name tran.txt     # 在当前文件夹下寻找tran.txt文件
> find . -iname tran.txt     # 在当前文件夹下寻找tran.txt文件，并且不区分大小写
> find . -type d -name dir  # 在当前文件夹下找dir文件夹
> ```

12. grep

> 文件内容搜索
> ```bash
root@matthew:/mnt/c/Users/Matthew/Desktop/test$ grep 123 index.js 123 123 123
> ```
常用命令
> ```bash
> grep -i -w 'hello' index.js        # index.js文件里搜索hello，区分大小写
> grep -E -i -w '123|hello' index.js # index.js文件里搜索123或者hello，区分大小写，并且使用正则匹配
> ```

13. sudo

> 以管理员权限运行

14. df

> 查看系统磁盘使用情况，展示单位为%或者KB，使用`df -m`以MB展示磁盘使用情况

15. du

> 查看文件夹和文件的磁盘占用情况
> 注意：磁盘占用情况不等于文件大小
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop$ du jquery-ui-table
> 16      jquery-ui-table/.git/hooks
> 0       jquery-ui-table/.git/inf
> 108     jquery-ui-table/.git/objects/pack
> 108     jquery-ui-table/.git/objects
> 124     jquery-ui-table/.git
> 40      jquery-ui-table/src
> 40      jquery-ui-table/test
> 204     jquery-ui-table
> ```
> 常用命令
> ```bash
>  du -sh babel  # 统计babel文件夹磁盘占用情况，并以合适的单位显示
> ```

16. head

> 展示文件前几行内容，默认前10行
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ head index.js
> ...
> ```
> 常用命令
> ```bash
> head -n 5 index.js        # 查看index.js文件前5行内容
> ```

17. tail

> 和head类似，展示后面几行

18. diff

> 文件内容逐行对比
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ diff 2.txt 3.txt
5d4
< world
> ```

19. chmod

> 修改文件的读写可执行权限
> ```bash
> chmod +x deploy.sh  # 让deploy.sh脚本有可执行权限
> ```

20. chown

> 修改文件的所有权
> ```bash
> chown anotheruser deploy.sh  # 将deploy.sh文件所有权赋予非anotheruser
> ```

21. kill

> 终止进程
> ```bash
> $ kill -KILL 123456 # 杀死进程号PID为123456的进程
> ```

22. ping

> 测试与服务器连接状况
> ```bash
> root@matthew:/mnt/c/Users/Matthew$ ping baidu.com
PING baidu.com (39.156.69.79) 56(84) bytes of data. 64 bytes from 39.156.69.79 (39.156.69.79): icmp_seq=1 ttl=52
time=28.0ms 64 bytes from 39.156.69.79 (39.156.69.79): icmp_seq=2 ttl=52 time=30.9ms 64 bytes from 39.156.69.79 (
39.156.69.79): icmp_seq=3 ttl=52 time=123 ms ... --- baidu.com ping statistics --- 34 packets transmitted, 34 received,
0% packet loss, time 33235ms rtt min/avg/max/mdev = 27.417/39.384/123.661/22.443 ms
> ```

23. wget

> 下载文件
> ```bash
> root@matthew:/mnt/c/Users/Matthew$ wget https://baidu.com.cn
> --2021-04-16 18:27:05--  https://baidu.com.cn/
> Resolving baidu.com.cn (baidu.com.cn)... 39.156.69.79, 220.181.38.148
> Connecting to baidu.com.cn (baidu.com.cn)|39.156.69.79|:443... connected.
> HTTP request sent, awaiting response... 302 Moved Temporarily
> Location: http://www.baidu.com/ [following]
> --2021-04-16 18:27:06--  http://www.baidu.com/
> Resolving www.baidu.com (www.baidu.com)... 36.152.44.95, 36.152.44.96
> Connecting to www.baidu.com (www.baidu.com)|36.152.44.95|:80... connected.
> HTTP request sent, awaiting response... 200 OK
> Length: 2381 (2.3K) [text/html]
> Saving to: ‘index.html’
> ```

24. uname

> 打印Unix系统名称
> ```bash
> root@matthew:/mnt/c/Users/Matthew$ uname
> Linux
> ```

25. top

> 持续监听进程运行状态

26. history

> 查询输入记录
> ```bash
> root@matthew:/mnt/c/Users/Matthew$ history | grep baidu # 查询输入baidu相关历史命令
268 ping baidu.com 269 wget htts://baidu.com.cn 270 wget https://baidu.com.cn
279 history | grep baidu
> ```

27. man

> 查看Linux中的指令帮助、配置文件帮助和编程帮助等信息
> ```bash
> man vim # 查询vim使用说明
> ```

28. echo

> 输出指定的字符串
> ```bash
> echo hello 
> ```
常用命令
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ echo Hello, my name is John >> name.txt # 将给定字符串写入name.txt文件
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ cat name.txt
> Hello, my name is John
> ```

29. zip, unzip

> 压缩，解压文件
> ```bash
> # 将当前目录下所有文件和文件夹打包为当前目录下的out.zip，加-q表示不显示过程
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ zip  -r out.zip .
adding: 2.txt (deflated 4%)
adding: 3.txt (deflated 6%)
adding: dir/ (stored 0%)
adding: dir/index.html (stored 0%)
adding: dir/index.js (deflated 41%)
adding: dir/name.txt (stored 0%)
adding: dir/ss.tex (stored 0%)
> ```

30. hostname

> 查看主机名
> ```bash
> root@matthew:/mnt/c/Users/Matthew/Desktop/test$ hostname
> matthew
> ```
