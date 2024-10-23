### 在本地Linux环境下部署Java Web项目

##### 1.在ubuntu上安装Java11

更新软件包列表

```shell
sudo apt-get update
```

直接安装

```shell
sudo apt install openjdk-11-jdk
```

配置环境变量

```shell
sudo vim ~/.bashrc
```

打开之后在文件的最后面添加以下语句：

```shell
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

更新环境变量配置文件

```shell
source ~/.bashrc
```

之后检测是否安装成功

```shell
java -version
```

##### 2.在ubantu上安装Mysql8.0

更新软件包列表

```shell
sudo apt-get update
```

安装MySQL

```SHELL
sudo apt-get install mysql-server
```

验证是否安装成功

```shell
systemctl status mysql
```

以root用户登陆Mysql

```shell
sudo mysql -u root -p  # -u 指定用户名 -p需要输入密码
```

在源服务器导出数据库（直接使用图形化界面）

使用 Cyberduck 复制文件到目标服务器

在目标服务器创建新数据库：

```bash
sudo mysql -u root -p
CREATE DATABASE [数据库名];
```

导入数据库：

```bash
#退出mysql控制台之后
sudo mysql -u root -p [数据库名] < [sql文件名].sql
```



##### ~~3.通过使用Cyberduck向服务器传递文件~~（腾讯云支持直接上传文件）

检查虚拟机上是否安装并运行了 SSH 服务（Linux 通常使用 OpenSSH）。在虚拟机上运行以下命令来确认：

```bash
sudo systemctl status ssh
```

如果 SSH 没有运行，可以通过以下命令启动：

```bash
sudo systemctl start ssh
```

确认虚拟机的防火墙没有阻止 SSH 连接。你可以通过以下命令允许 SSH：

```bash
sudo ufw allow ssh
```

使用 `ifconfig` 命令，在输出中查找 `inet` 字段，通常你会看到像 `inet 192.168.x.x` 这样的 IP 地址。

```bash
ifconfig
```

在Cyberduck上新建连接来把Jar包传上去。

##### ~~4.通过隧道技术连接公网~~（好像买了服务器直接分配公网ID了，好爽）

安装对应的客户端

给客户端执行权限

```bash
chmod +x ./sunny
```

在隧道管理中寻找启动命令

```bash
./sunny --server=free.idcfengye.com:4443 --key=174910438242
```

##### 5.PowerShell 连接 Ubuntu 云服务器

```bash
ssh 用户名@远程服务器的IP地址
```

##### 6.确认应用是否绑定到所有 IP（0.0.0.0）

你可以检查应用是否绑定到 `0.0.0.0`（即所有网络接口），以允许外部访问。如果应用只绑定到 `localhost` 或 `127.0.0.1`，则无法从外部网络访问。

在 `application.properties` 或 `application.yml` 中，确保有如下配置：

```yaml
server.address=0.0.0.0
server.port=8080
```

7.**再次检查安全组规则**

云服务器的安全组规则是允许外部访问的关键。确保安全组中已经有一条规则，允许所有 IP (`0.0.0.0/0`) 访问服务器的 `8080` 端口。你可以参考以下规则：

- 协议: `TCP`
- 端口范围: `8080`
- 来源: `0.0.0.0/0`

**8.让程序能继续在后台运行**

ssh 打开以后，bash等都是他的子程序，一旦ssh关闭，系统将所有相关进程杀掉！！ 导致一旦ssh关闭，执行中的任务就取消了

```bash
nohup java -jar sell.jar
```

