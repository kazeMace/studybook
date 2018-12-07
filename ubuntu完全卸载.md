卸载xxx
1、首先删除安装包
    sudo apt-get --purge remove xxx
2、自动移除全部不使用的软件包
    sudo apt-get autoremove
3、罗列出与xxx相关的软件包
    dpkg --get-selections|grep xxx
4、将3中查出的软件包使用1的方法卸载，卸载时要注意不要删掉系统自带的软件（一般不需要执行）