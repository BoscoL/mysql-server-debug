## 编译

#### 编译环境使用的是WSL2，发行版为：Ubuntu 20.04 LTS。

##### 需要安装的依赖库如下：

```
$ sudo apt install -y openssl
$ sudo apt install -y libssl-dev
$ sudo apt install -y libsasl2-dev libsasl2-modules-gssapi-mit
$ sudo apt install -y libldap-dev
$ sudo apt install -y libedit-dev
$ sudo apt install -y libudev-dev
$ sudo apt install -y libicu-dev
$ sudo apt install -y libtirpc-dev
$ sudo apt install -y libnuma-dev
$ sudo apt install -y bison
$ sudo apt install -y doxygen
$ sudo apt install -y numactl
```

<font color="red">**注意：**如果sasl模块出错，可能是因为 /sys/types.h导致，<br>需要在 /usr/include 目录下，
为 /usr/include/x86_64-linux-gnu/sys 建立一个软链接。<br>命令如下：<br>``` $ sudo ln -s /usr/include/x86_64-linux-gnu/sys /usr/include/sys```</font>



在CMake Options中设定构建路径，如下：

```
-DCMAKE_INSTALL_PREFIX=D:/ClionWorkspace/mysql-server/build
```



## 构建 mysqld

<img src="https://image.hinas.space:1444/images/20220846638cb3a8bd65c6.png" alt="image-20220811160811384" style="float:left" />

**构建结果：**



<img src="https://image.hinas.space:1444/images/202208532ced21c3b14eb3.png" alt="image-20220811160023645" style="float:left;" />



## 初始化

<font color="red">注意：此步骤需要进入WSL2中的 /mnt/d/ClionWorkspace/mysql-server/build/bin进行操作，因为 Windows下bin目录无法访问。</font>

```shell
 # 创建用户和组
 $ sudo groupadd mysql
 $ sudo useradd -r -g mysql mysql
 
 # --initialize-insecure: 创建一个空密码的root用户，--datadir：指定数据目录，--lc-message-dir：指定 errmsg.sys的位置信息
 $ sudo ./mysqld --initialize-insecure --datadir=/mnt/d/ClionWorkspace/mysql-server/build/data  \
 --lc-messages-dir=/mnt/d/ClionWorkspace/mysql-server/build/share/english
 
```

如无意外，结果如下：

```
$ cd /mnt/d/ClionWorkspace/mysql-server/build/bin
$ sudo ./mysqld --initialize-insecure --datadir=/mnt/d/ClionWorkspace/mysql-server/build/data  --lc-messages-dir=/mnt/d/ClionWorkspace/mysql-server/build/share/english

===>>

[System] [MY-013169] [Server] /mnt/d/ClionWorkspace/mysql-server/build/runtime_output_directory/mysqld (mysqld 8.0.30-debug) initializing of server in progress as process 21449
.....
[System] [MY-013576] [InnoDB] InnoDB initialization has started.
[System] [MY-013577] [InnoDB] InnoDB initialization has ended.
[Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.

===
```



## 调试

1. **调试前配置**

<img src="https://image.hinas.space:1444/images/2022081ffd458b4b2f41e4.png" alt="image-20220811161641978" style="zoom: 67%;float:left;" />

<font color="red">注意：如果不想每次启动调试时，都 build 一次就移除 Before Launch 中的 build 。</font>



2. **调试**

<img src="https://image.hinas.space:1444/images/2022088ec6a4f4f79dc760.png" alt="image-20220811161839648" style="float: left;" />

<font color="red">注意：该过程可能持续的事件会有点长，以分钟计。</font>

Plugin mysqlx reported: 'Setup of socket: 'D:/ClionWorkspace/mysql-server/build/tmp/mysqlx.sock' failed, can't create lock file D:/ClionWorkspace/mysql-server/build/tmp/mysqlx.sock.lock'

update user set host='%' where user='root';

3. **连接Mysqld**

   此步骤可直接利用 Clion 的database插件进行数据库连接。

   连接信息如下：

   host : localhost/127.0.0.1
   username：root
   password：不填

   

4. **调试 explain rows 估计过程**

   打开 /storage/innobase/btr/btr0cur.cc，在方法：btr_estimate_n_rows_in_range_low 内打上断点，然后执行sql例句：

   ```
   explain select * from mysql.`user`;
   ```

   

5. end！！！

