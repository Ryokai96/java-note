# ubuntu使用installer安装jdk

## 1. 添加ppa

> sudo add-apt-repository ppa:webupd8team/java
>
> sudo apt update



## 2. 安装oracle-java-installer

### 2.1 在线安装

- 安装jdk7

> sudo apt install oracle-java7-installer

- 安装jdk8

> sudo apt install oracle-java8-installer

然后安装器会提示你同意oracle的服务条款，同意后会自动在线安装jdk



### 2.2 离线安装

如果在线安装下载很慢，可以中断操作，然后到官网下载相应jdk的tar.gz包，放在

> /var/cache/oracle-jdk7-installer

或

> /var/cache/oracle-jdk8-installer

然后再安装一次installer，installer会默认使用你下载的tar.gz包



## 3. 设置系统默认jdk

- jdk7

> sudo update-java-alternatives -s java-7-oracle

- jdk8

> sudo update-java-alternatives -s java-8-oracle

- 测试是否安装成功

> java -version
>
> javac -version



## 4. 此方式jdk的安装目录

使用此方式安装jdk，jdk的目录为

- jdk7

> /usr/lib/jvm/java-7-oracle

- jdk8

> /usr/lib/jvm/java-8-oracle
