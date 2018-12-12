#Ambari集成Hue过程详解
##一.手动安装及编译Hue
1.  安装必要的组件依赖
```javascript
 yum install -y 'ant' 'asciidoc' 'cyrus-sasl-devel' 'cyrus-sasl-gssapi' 'gcc' 'gcc-c++' 'krb5-devel' 'libtidy' 'libxml2-devel' 'libxslt-devel' 'make' 'mvn' 'openldap-devel' 'python-devel' 'sqlite-devel'  'openssl-devel' 'gmp-devel'
```
2.  安装前准备：
   *  配置数据库
      ```
      create database hue;  
      CREATE USER hue@'%'IDENTIFIED BY '';
      GRANT ALL PRIVILEGES ON hue.* TO hue@'*' IDENTIFIED BY '';
      GRANT ALL PRIVILEGES ON hue.* TO hue@'%' IDENTIFIED BY '';
      FLUSH PRIVILEGES; 
      ```
   *  安装hadoop-httpfs
      如果你的hdfs不是HA模式，你可以跳过，如果你已经开启了该服务请跳过，如果是HA，那么需要开启`hadoop-httpfs`。因为webhdfs不能自动感知hdfs-site.xml里面配的HA高可用信息。而HDP是删除了httpfs的，所以这里需要手动安装  
      手动安装很简单，执行如下命令：`yum install -y hadoop-httpfs` ;启动HttpFs ,执行`sudo service hadoop-httpfs  start`   
      
3.  安装编译
   1.  在HUE官网上下载相应版本的Hue版本，此处我安装的是Hue-4.2.0,附件中有我已经下载好的hue-4.2.0.tgz的安装包，附上官网地址：[Hue官网](http://gethue.com/)
   2.  解压到指定目录,此处我解压的路径是/usr/lib/,执行解压命令 `tar -zxvf hue-4.2.0.tgz -C /usr/lib/` ，然后到指定的/usr/lib/hue-4.2.0的目录下执行`make apps`进行手动安装
   3.  如果你在/usr/lib/hue-4.2.0/build/env/bin目录下可以看到hue 和supervisor表示已经安装成功了。如图所示   
   
   4.  安装过程可能遇到的问题
    *  mysql_config not found:
    ```javascript
        EnvironmentError: mysql_config not found
        make[2]: *** [/root/tools/hue-4.0.0/desktop/core/build/MySQL-python-1.2.5/egg.stamp] 错误 1
        make[2]: 离开目录“/root/tools/hue-4.0.0/desktop/core”
        make[1]: *** [.recursive-install-bdist/core] 错误 2
        make[1]: 离开目录“/root/tools/hue-4.0.0/desktop”
        make: *** [install-desktop] 错误 2
    ```
    问题原因：社区版的mysql,用的非社区版的mysql-devel,需要下载社区版的mysql-community-devel，你可以使用 rpm -qa | grep mysql 或者yum list installed | grep mysql 查看你的mysql版本，  
             结果类似如下 
             ```javascript
                  mysql-community-server-5.7.19-1.el7.x86_64
                  mysql-community-common-5.7.19-1.el7.x86_64
                  mysql-community-libs-5.7.19-1.el7.x86_64
                  mysql-community-client-5.7.19-1.el7.x86_64
             ```
             然后下载对应mysql-devel开发包，下载地址是: [rpm.pbone.net](http://rpm.pbone.net/)

    *  我安装的maridb，因此遇到的是maraidb-devel开发包缺失的问题，下载相关依赖即可，使用yum list installed | grep maria* 展示下载完成后的结果如下：
             ```javascript
                 mariadb.x86_64                          1:5.5.60-1.el7_5         installed      
                 mariadb-devel.x86_64                    1:5.5.60-1.el7_5         installed      
                 mariadb-libs.x86_64                     1:5.5.56-2.el7           @anaconda      
                 mariadb-libs.x86_64                     1:5.5.60-1.el7_5         installed      
                 mariadb-server.x86_64                   1:5.5.60-1.el7_5         @yum           
                 marisa.x86_64                           0.2.4-4.el7              @anaconda 
             ```
        我已经下载好了mariadb-devel.x86_64的包，你可以从我的目录下直接下载
        
##二.HUE配置修改,生成数据库表
        修改hue.ini的配置文件，该配置文件可以使你的Hue集成进现有的集群中，它位于：/usr/lib/hue-4.2.0/desktop/conf/目录下   
        你可以参考官网上的配置教程，[Hue官网安装配置教程](http://gethue.com/hadoop-hue-3-on-hdp-installation-tutorial/)，但是这里我将向你展示我的配置修改
        1.  修改时间，修改hadoop，beeswax，impala，yarn,zookpeer,oozie 等等你希望的组件 ,详情请参考我的hue.ini或者官方文档，部分修改如图所示：
        2.  进入/usr/local/hue/build/env/bin/目录，执行如下两条命令：
            *   `./hue syncdb` 此时询问你是否要创建一个超级用户的权限，请暂时不要，would you like to create one now ? 输入`no`
            *   `./hue migrate` 
        3.如果你在数据库发现了这些表，说明你安装成功了：   
        
##三.启动Hue，输入你的账户密码   

##四.Ambari集成Github上的Hue插件
    1.  插件的地址：[Github Hue 插件地址 ](https://github.com/EsharEditor/ambari-hue-service)
    2.  执行如下命令：
        ```   
        VERSION=`hdp-select status hadoop-client | sed 's/hadoop-client - \([0-9]\.[0-9]\).*/\1/'`
        rm -rf /var/lib/ambari-server/resources/stacks/HDP/$VERSION/services/HUE  
        sudo git clone https://github.com/EsharEditor/ambari-hue-service.git /var/lib/ambari-server/resources/stacks/HDP/$VERSION/services/HUE
        ```  
    3.  我对这个插件中的params.py和hue_server.py进行较大的改动，主要是删除不需要的代码，增加必要的运行py代码，我已经将我修改的插件放在附件，你可以参考，部分截图如下，  
        请注意你在param.py的hue安装路径，我们希望插件直接读取我们已经手动安装的hue，而不是走yum源额外install,部分重要截图如下：  
        
    4.  然后你就可以在ambari上add Service，选择hue按照步骤进行安装。
    
    5.  安装的过程的配置如下：  
    
    6.  安装的时候你可能遇到如下bug报错：  
    
        解决方法是修改hue-env的文件：找到hue_user，做如下修改：  
        ```
          <property>
              <name>hue_user</name>
              <value>hue</value>
              <display-name>Hue User</display-name>
              <property-type>USER</property-type>
              <description>hue user</description>
              <value-attributes>
                <type>user</type>
                <overridable>false</overridable>
                <user-groups>
                     <property>
                         <type>cluster-env</type>
                         <name>user_group</name>
                     </property>
                     <property>
                         <type>hue-env</type>
                         <name>hue_group</name>
                     </property>
                 </user-groups>
              </value-attributes>
              <on-ambari-upgrade add="true"/>
          </property>

        ```
##四.参考链接  
    以下都是我阅读过的CSDN上的一些安装文章，你觉得有需要，可以进行阅读：  
    *  [ambari集成hue 文章一](https://www.cnblogs.com/xupccc/p/9583656.html)
    *  [ambari集成hue 文章二](https://blog.csdn.net/zhouyuanlinli/article/details/83374416)
    *  [ambari集成hue 文章三](https://www.cnblogs.com/chenzhan1992/p/7940418.html)
    *  [ambari集成hue 文章四](https://blog.csdn.net/u011596455/article/details/78046627)
    *  [ambari集成hue 文章五](https://my.oschina.net/aibati2008/blog/647493)
    *  [ambari集成hue 文章六](https://www.cnblogs.com/xupccc/p/9583656.html)
    *  [ambari Github](https://github.com/apache/ambari)
