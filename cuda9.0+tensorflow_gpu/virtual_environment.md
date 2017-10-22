当多人在一台服务器上使用或者是自己在电脑上使用多个python版本，可以通过配置虚拟环境来实现。

sudo pip3 install virtualenv-15.1.0-py2.py3-none-any.whl

sudo python3 -m virtualenv .name      (name指的是目录名，说明所有的虚拟环境都在该目录下，并设为隐藏文件，为了安全，以免误删。一旦这个文件夹删除，则该文件夹下的所有环境就都没有了)

source .name/bin/activate（激活该虚拟环境，每次使用都需要激活）

deactivate   （退出该虚拟环境）



