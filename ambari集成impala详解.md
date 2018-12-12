#  Ambari集成impala 
####   author:高铭    time:2018-12-12 16:45:00
1.  参照Github上的安装流程，[Github ambari-impala 插件](https://github.com/cas-bigdatalab/ambari-impala-service)
2.  你将遇到如下问题,如图所示:  
![遇到的bug](/impala/bug.png)
    解决问题:
   
3.  同步/etc/hadoop/目录下的`core-site.xml` , `hdfs-site.xml` 和 /etc/hive2/目录下的 `hive-site.xml` 到 /etc/impala/conf/目录下，结果如图所示:

   
