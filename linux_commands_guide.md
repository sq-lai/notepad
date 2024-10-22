
# Linux 常用命令分类整理

## 目录
1. [文件和目录管理](#文件和目录管理)
2. [文件权限和所有者管理](#文件权限和所有者管理)
3. [系统管理和信息查看](#系统管理和信息查看)
4. [进程管理](#进程管理)
5. [网络管理](#网络管理)
6. [磁盘管理](#磁盘管理)
7. [文本处理命令](#文本处理命令)
8. [压缩和解压缩](#压缩和解压缩)

### 文件和目录管理

1. **ls** - 列出文件和目录
   ```bash
   ls        # 列出当前目录下的所有文件和子目录
   ls -l     # 以长格式显示文件详细信息
   ls -a     # 显示包括隐藏文件的所有文件
   ```

2. **cd** - 切换目录
   ```bash
   cd /home/user   # 切换到/home/user 目录
   cd ..           # 返回到上一级目录
   cd ~            # 切换到当前用户的主目录
   ```

3. **mkdir** - 创建目录
   ```bash
   mkdir new_folder          # 创建一个名为new_folder的目录
   mkdir -p dir1/dir2        # 创建多级目录，如果父目录不存在则一起创建
   ```

4. **rm** - 删除文件或目录
   ```bash
   rm file.txt           # 删除文件
   rm -r folder          # 递归删除目录及其内容
   rm -rf /path/to/dir   # 强制递归删除目录，谨慎使用
   ```

5. **cp** - 复制文件或目录
   ```bash
   cp file1.txt file2.txt       # 复制file1.txt为file2.txt
   cp -r dir1 dir2              # 递归复制整个目录
   ```

6. **mv** - 移动或重命名文件/目录
   ```bash
   mv oldname.txt newname.txt   # 重命名文件
   mv file.txt /new/path/       # 移动文件到新的目录
   ```

7. **touch** - 创建空文件
   ```bash
   touch newfile.txt            # 创建一个新的空文件
   ```

8. **find** - 查找文件
   ```bash
   find /home -name "file.txt"   # 在/home 目录下查找名为file.txt的文件
   ```

### 文件权限和所有者管理

1. **chmod** - 修改文件权限
   ```bash
   chmod 755 script.sh          # 设置script.sh的权限为755（所有者可读写执行，其他人可读执行）
   chmod +x script.sh           # 增加可执行权限
   ```

2. **chown** - 更改文件的所有者
   ```bash
   chown user:group file.txt    # 将file.txt的所有者更改为user，所属组为group
   ```

3. **chgrp** - 更改文件的组
   ```bash
   chgrp group file.txt         # 更改file.txt的组为group
   ```

### 系统管理和信息查看

1. **uname** - 显示系统信息
   ```bash
   uname -a                     # 显示所有系统信息
   ```

2. **df** - 显示磁盘使用情况
   ```bash
   df -h                        # 以人类可读的方式显示磁盘空间使用情况
   ```

3. **free** - 显示内存使用情况
   ```bash
   free -h                      # 以人类可读的方式显示内存使用情况
   ```

4. **top** - 动态显示系统进程
   ```bash
   top                          # 实时显示系统资源和进程信息
   ```

5. **ps** - 查看当前进程
   ```bash
   ps aux                       # 列出所有进程的详细信息
   ```

### 进程管理

1. **kill** - 终止进程
   ```bash
   kill 1234                    # 终止进程号为1234的进程
   ```

2. **killall** - 通过进程名称终止进程
   ```bash
   killall firefox              # 终止所有名为firefox的进程
   ```

3. **pkill** - 使用模式匹配来终止进程
   ```bash
   pkill -f script.sh           # 根据名称匹配终止进程
   ```

### 网络管理

1. **ifconfig** - 查看或配置网络接口
   ```bash
   ifconfig                     # 显示所有网络接口的信息
   ```

2. **ping** - 检查网络连通性
   ```bash
   ping google.com              # 对google.com进行ping操作
   ```

3. **netstat** - 查看网络状态
   ```bash
   netstat -tuln                # 列出所有监听的端口
   ```

4. **ssh** - 远程登录
   ```bash
   ssh user@remote_host         # 通过SSH远程登录到服务器
   ```

### 磁盘管理

1. **du** - 查看目录或文件的大小
   ```bash
   du -sh /path/to/dir          # 显示目录的总大小
   ```

2. **mount / umount** - 挂载/卸载设备
   ```bash
   mount /dev/sdb1 /mnt         # 将/dev/sdb1设备挂载到/mnt目录
   umount /mnt                  # 卸载/mnt目录
   ```

### 文本处理命令

   0.**echo**给文件写内容，没vim情况下。

```bash
echo '<h1>Hello,smelly!</h1>' > index.html
>>是追加，>是覆盖添加
```

1. **cat** - 显示文件内容
   
   ```bash
   cat file.txt                 # 显示文件的内容
   ```
   
2. **grep** - 查找文本中的字符串
   ```bash
   grep "keyword" file.txt       # 在file.txt中查找包含"keyword"的行
   ```

3. **sed** - 文本替换
   ```bash
   sed 's/old/new/g' file.txt   # 将file.txt中的"old"替换为"new"
   ```

4. **awk** - 文本格式化和处理
   ```bash
   awk '{print $1}' file.txt    # 打印file.txt中的第一列
   ```

### 下载东西

```bash
wget http://xxxxx
//访问网址链接下载东西
```



### 压缩和解压缩

1. **tar** - 打包和解压文件
   ```bash
   tar -cvf archive.tar /path   # 将/path目录打包为archive.tar
   tar -xvf archive.tar         # 解压archive.tar
   ```

2. **gzip / gunzip** - 压缩和解压缩文件
   ```bash
   gzip file.txt                # 压缩file.txt为file.txt.gz
   gunzip file.txt.gz           # 解压file.txt.gz
   ```

3. **zip / unzip** - 压缩和解压缩ZIP文件
   ```bash
   zip archive.zip file1 file2  # 压缩多个文件为archive.zip
   unzip archive.zip            # 解压archive.zip
   ```

---

希望这份Linux常用命令的整理对你有帮助！如果有任何问题或者需要更详细的解释，请随时告诉我。 😊
