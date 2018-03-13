# Xshell 教程

## 文件

### 使用 rz/sz 命令实现 Windows 与 Linux  文件互传

1.  rz

    rz命令是从windows传文件到服务器（r：receive），输入rz后按回车，Xshell弹出对话框，可以选择需要上传的文件，点击打开即可上传。

2.  sz

    sz命令是将服务器中的文件上传到windows本地（s：send）。sz 之后加上需要上传的文件的路径，回车后会弹出对话框，选择保存的位置，点击确定后即可上传成功。

注意：

1.  如何简单区分rz和sz命令

    由于Xshell连接的是服务器端，所以无论是接收receive还是发送send都是以服务器为第一人称来说的。receive即是服务器接收windows传过来的文件。send即是服务器发送给windows文件。

2.  如果输入rz/sz，出现不是内部命令的提醒（the command is not found）

    说明你所在的服务器还没有安装rz/sz。以下为安装命令：

    （1）查找 rz/sz 安装包，若服务器没有，则自动下载安装包

    `$ yum provides */rz`

    （2）安装 rzsz

    `$ yum install -y lrzsz` 