# 一些基础技能
* 使用`htop` 查看 CPU 和内存使用情况
* 使用`nv` 命令查看 GPU 使用情况。

将下面命令添加到 `~/.bashrc`,然后重启终端。

```bash
# 使用`nv` 命令查看 GPU 使用情况
alias nv='watch -n 1 nvidia-smi'

# 使用某几个GPU (0,1,2,3), 其它GPU不用
# $choosecuda 0,1,2,3   $choosecuda 3
function choosecuda {
    if [ -z "$1" ]; then num=0; else num="$1" ; fi
    CUDA_VISIBLE_DEVICES=$num
    export CUDA_VISIBLE_DEVICES
}

# 使用 killcuda 强行释放本账户的 GPU 内存占用
# $killcuda
alias killcuda="ps -o pid= -u $(whoami) | \
grep -wFf  <( nvidia-smi -q -x | grep pid | sed -e 's/<pid>//g' -e 's/<\/pid>//g' -e 's/^[[:space:]]*//' | sort | uniq ) | \
xargs kill -9 "

# 使用 killcuda2 强行释放所有账户的 GPU 内存占用
# $sudo killcuda2
alias killcuda2="fuser -v /dev/nvidia-uvm  2> /dev/null | xargs kill -9 "

# 查找当前文件夹内 python 文件中包含某字段的文件
# $findgrep WORDS
alias findgrep='find . -name "*.py" -type f -exec echo \"{}\" \; | xargs grep -n '
```

* `choosecuda` 用于选择 GPU 的ID。
* `killcuda` 和 `killcuda2` 用户释放 GPU 的占用。

!!! Warning "警告"
    使用 GPU 之前，先用 `nv` 命令看一下GPU的占用情况，执行`choosecuda`选择空闲的GPU使用。如果有一个GPU内存占用高但是运算占用率低，则使用 `killcuda` 释放。`choosecuda`只对当前的终端临时有效，在终端重启或跨终端则无效。

* `findgrep` 用于查找python代码

* 挂载群辉的云盘路径到服务器上。替换其中的 `username` 为群辉的用户名，`uid` 为服务器账号的用户名。会要求输入群辉用户的密码。

```bash
sudo mkdir /mnt/liying.cibr.ac.cn_usb3  #创建文件夹

# 挂载群辉的路径到服务器
sudo mount -t cifs -o \
 vers=2.0,username=chenxinfeng \
 //liying.cibr.ac.cn/usbshare3-2 \
 /mnt/liying.cibr.ac.cn_usb3/ -o \
 uid=chenxinfeng,file_mode=0766,dir_mode=0777
```

!!! Warning "警告"
    file_mode=0766,dir_mode=0777 权限放得太宽，让所有用户都具群辉文件的修改权限。有可能会对群辉的文件的完全造成危险。权限控制太紧，可能没法顺利加载。请自行控制 file_mode 和 dir_mod 权限。

