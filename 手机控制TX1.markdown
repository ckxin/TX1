#实现手机控制TX1##

by xin

可参考以下博客：
* https://blog.csdn.net/Jesse_Mx/article/details/75807948

按照博主介绍下载好相应软件后，SSH很容易就登陆上去了，不过VNCViewer开始的时候连接上总是灰屏加一个×形鼠标，这是因为vncserver在Ubuntu系统中找不到指定的图形化工具，然后百度了好多教程，我记得其中有一个让修改一个文件的权限，注意千万不要去修改，改了一次屏幕都没了，实在忍不住改了就再改回来吧。我后来是参考这个教程:http://blog.51cto.com/13526376/2050501
下载了图形化工具，然后更改~/.vnc/xstartup文件成功的，仅供参考。

另外，我后来发现iPhone也是可以的，不过iPhone上没有juiceSSH这个软件，实际上就是一个SSH登陆软件，随便下载一个其他的也行，我下载的叫WebSSH，有广告，凑和用吧，其余操作和安卓手机操作一样。还有这个教程用的是手机开热点然后用TX1连接，但考虑到以后应用TX1是要安装到外面的，不可能再去拿个显示屏对它进行操作，所以考虑让TX1开机自动开热点然后手机连接TX1开的热点，因为SSH登陆只需要在一个局域网就可以，下一步准备实现这个。
