1、更新软件源
sudo apt-get update
sudo apt-get upgrade
2、更新语言
3、更改显卡驱动
    在系统的软件更新界面，点击附加驱动选项卡更新显卡驱动，选择第一个装有驱动 nvidia-driver-390
4、设置管理员账户密码
    sudo passwd root

4、美化
    sudo apt install gnome-tweak-tool
    sudo apt install gnome-shell-extensions
    sudo apt install chrome-gnome-shell // 浏览器插件可不装
    安装后重启电脑
    重启后在所有程序中找到优化（中文名），点击进入
    在外观界面可以发现shell后面有一个三角号，不能编辑
    这时点击扩展，将user themes开关打开，重新启动软件就能看到shell的后面可以编辑了
5、安装必要软件
    vim编辑器
    sudo apt-get install vim
    aptitude(更智能的apt)
    sudo apt-et install aptitude
5、gcc,g++降级
    因为ubuntu18.04默认安装的gcc版本是7以上
    可以使用
    gcc --version
    g++ --version
    命令查看当前版本，我的版本是7.3,但cuda9.0需要gcc和g++4.8版本，因此需要降级
    在命令行输入
    sudo apt-get install gcc-4.8
    sudo apt-get install g++-4.8
    安装完毕后cd进入到/usr/bin目录下
    在命令行中输入
    ls -l gcc*
    查看与gcc有关的文件，可以看到当前的gcc指向gcc-7 （gcc -> gcc-7）,我们需要将gcc重新指向刚下载的gcc-4.8
    首先备份当前gcc
    sudo mv gcc gcc.bak
    之后使用软连接将gcc指向gcc-4.8
    sudo ln -s gcc-4.8 gcc
    查看gcc版本 
    gcc --version
    发现已经gcc变成4.8.5版本
    接下来对g++执行同样操作
    ls -l g++*
    同样发现g++指向g++-7
    备份当前g++
    sudo mv g++ g++.bak
    将g++指向g++-4.8
    sudo ln -s g++-4.8 g++
    在查看g++版本
    g++ --version
    发现也已经改为4.8.5版本
5、安装cuda
    ubuntu18.04+cuda9.0+cudnn7.4.1+pytorch+tensorflow
    在nvidia界面下载cuda9.0 ubuntu16.04版本（能够兼容）的runfile
    我下载的是cuda_9.0.176_384.81_linux.run，在命令行cd到文件所在目录下，执行
    sudo ./cuda_9.0.176_384.81_linux.run --override
    !注意使用sudo提升权限
    安装过程中需要阅读协议，点击q退出即可，然后输入accept接受协议，接下在安装时会有一个询问是否安装显卡驱动程序，只有这个选择no，因为我们已经有一个显卡驱动了，其他的选项选择默认或yes
    最后会提示cuda安装不完整，这时因为显卡驱动没安装，软件让我们安装384版本但我们已经安装了390版本，所以可以忽略掉
    接下来用同样的方法将其他几个runfile补丁安装
    sudo ./cuda_9.0.176.1_linux.run
    sudo ./cuda_9.0.176.2_linux.run
    sudo ./cuda_9.0.176.3_linux.run
    sudo ./cuda_9.0.176.4_linux.run
    使用默认配置安装
    安装完毕后进入~/.bashrc 配置文件
    vim ~/.bashrc
    将一下两条语句加入该文件来配置环境变量
    export PATH=/usr/local/cuda-9.0/bin:$PATH
    export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH
    使用:wq保存退出
    之后执行source ~/.bashrc
    测试CUDA是否安装成功
    nvcc -V
    提示为安装成功
    nvidia-smi也可以显示出来
    接下来安装cudnn
    下载cudnn-9.0-linux-x64-v7.4.1.5.tgz
    解压后进入其中的cuda目录
    cd cuda
    执行以下命令
    sudo cp lib64/* /usr/local/cuda/lib64/
    sudo cp include/* /usr/local/cuda/include/
    sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
    可以进入到~/NVIDIA_CUDA-9.0_Samples目录下测试
    cd ~/NVIDIA_CUDA-9.0_Samples
    cd 0_Simple
    cd simpleTemplates
    make
    ./simpleTemplates
    看看是否能执行成功

8、安装virtualenvwrapper
    ubuntu18.04自带ython2和python3，其中默认使用python3，版本是3.6.7
    python3 --version
    whereis python
    which python3
    安装python3的pip
    sudo apt-get install python3-pip
    查看pip3版本
    pip3 --version
    使用pip3安装virtualenvwrapper
    sudo pip3 install virtualenvwrapper
    创建virtualenvs文件夹集中存储虚拟环境
    mkdir ~/.virtualenvs
    安装virtualenvwrapper会有一个virtualenvwrapper.sh
    先查找virtualenvwrapper.sh文件的位置
    可能在~/.local/bin/目录下，也可能在/usr/local/bin/目录下
    修改~/.bashrc文件
    vim ~/.bashrc
    在最后添加
    export WORKON_HOME=$HOME/.virtualenvs
    VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3  // python或python3路径
    source ~/.local/bin/virtualenvwrapper.sh // 此处为virtualenvwrapper.sh的位置
    之后执行source ~/.bashrc
9、安装mysql
sudo apt-get install mysql-server
sudo apt-get install mysql-client
sudo apt-get install libserverclient-dev