which python 可以知道当前python的安装位置

virtualenv -p /usr/bin/python venv2 --no-site-package
设置路径。usr/bin/python ven2对应的环境是python版本 

virtualenv -p usr/bin/python3 ven3

这里同样的用这个命令创建了名为venv3的python3的虚拟环境，/usr/local/bin/python3 是python3的安装位置


激活环境
source venv2/bin/activate

退出环境
deactivate

删除环境

rm -r ven2