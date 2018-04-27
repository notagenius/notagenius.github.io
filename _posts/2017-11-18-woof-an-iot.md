---
layout: post
title: Ai-笔记：嚎出一个极简物联网
---

<div class="message">
前言：这篇是「威玲旺卡Aileen」和「阿狗A-Go」的原创物联网边造边写系列笔记。没有告知时请不要转载呐，谢谢。另外我要感谢Marcel Ochsendorf（马赛）同学对我的指导。
</div>

#### 本集问题情景：
阿狗想在何时何地都可以知道他生命中最重要的地方——狗窝的温度和湿度。这样他可以决定是不是需要打开暖气和加湿器以保持他狗毛的蓬松和柔软，同时也可以知道供暖系统和加湿器的真正效果。目前他能在一个网页上看到实时数据就满意了，阿狗对web框架一无所知，手边也只有一块树莓派，他打算用很老很成熟的LAMP（Linux-Apache-MySQL-PHP）来实现它。
#### 这个边造边写的笔记将会涵盖：
1. 部署一个aws虚拟主机，打造服务器+web构架+域名
- 创建aws Linux AMI主机 
- 通过yum安装LAMP
- 为主机申请静态IP，修改域名DNS到主机
2. 用树莓派读取温度湿度传感器数值并上传数据库（通过requests）
- 硬件连接DHT传感器
- 安装Adafruit Sensor Library，测试读取传感器数据
- 这里狗要思考一下在哪里登录数据库，所以狗先去了第三部分，然后回到这个点继续
- 安装requests，通过它进行数据交流
3. 创建数据库表结构，用php与MySQL对话，最终显示传感器实时数据至页面
- 通过phpMyAdmin界面设计表结构
- 写用来登录数据库的db.php
- 写用来执行插入操作的insert.php（回到2.4，在树莓派上requests它，测试数值是否插入数据库成功）
- 写显示页面
我们开始动手啦。

### 1. 部署一个aws虚拟主机，打造服务器+web构架+域名
#### 1.1装一个aws实例
按照非常好的官方文档，
[Amazon EC2 Linux 实例入门 ](http://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance)
[新手教程－如何在 Amazon AWS 上搭建和部署网站](http://www.awsok.com/%E6%96%B0%E6%89%8B%E6%95%99%E7%A8%8B%EF%BC%8D%E5%A6%82%E4%BD%95%E5%9C%A8-amazon-aws-%E4%B8%8A%E6%90%AD%E5%BB%BA%E5%92%8C%E9%83%A8%E7%BD%B2%E7%BD%91%E7%AB%99/)
[使用 SSH 连接到 Linux 实例](http://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)
这一步的目标是：
ssh链接实例成功!
![ssh链接实例成功.png](http://upload-images.jianshu.io/upload_images/94086-022917b8dbcc9e25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我当下的安全组设置，感觉非常不安全，后面一定要改改。
![安全组设置截图.png](http://upload-images.jianshu.io/upload_images/94086-5ed742e889f5b8ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说到要熟悉aws最快最好的方式，我不得不提[qwiklabs](https://qwiklabs.com/)，真实环境lab，非常棒。
#### 1.2安装Web构架
教程走一个
[教程：在 Amazon Linux 上安装 LAMP Web 服务器](http://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/install-LAMP.html)
这一部的完成的鉴定是，登录主机DNS可以看到它：
![Apache主页.png](http://upload-images.jianshu.io/upload_images/94086-b1cd5f60f0d4ae5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
登录主机DNS/phpinfo.php可以看到装的php版本，
![phpinfo.png](http://upload-images.jianshu.io/upload_images/94086-7ff7fb9c9ff8a9d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
登录主机DNS/phpMyAdmin可以看到数据库登录入口，
![phpMyAdmin.png](http://upload-images.jianshu.io/upload_images/94086-968d70df0c34f771.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我安装的过程中原来用
写用来执行插入操作的insert.php
{% highlight bash%}
sudo yum install -y http php php-mysql mysql mysql-server
{% endhighlight %}
后来折腾了好久也弄不好phpMyAdmin，后来只能全卸载，确定版本，这才搞定
{% highlight bash %}
sudo yum install -y httpd24 php70 mysql56-server php70-mysqlnd
{% endhighlight %}
创建index.php或者index.html就可以替换掉默认主页了。

这些都是在主机里弄的，没有用到亚马逊的RDS（Relational Database Service）也没有挂载EFS（Elastic File System）留给之后探索。
####1.3为主机申请静态IP，修改域名DNS到主机
官方文档 [弹性 IP 地址](http://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)
获得IP后一定要关联运行着的主机，否则没有关联实例的IP会被扣费，此外，如果申请了静态IP并且关联后，把主机给关掉了，那么这个IP也会被扣费。
阿狗买了一个叫wolfiethedog.de的域名，在DNS中将A地址指向静态IP就完成了。但当我用CNAME将www指向主机地IPv4地址（ec2-xxxxxxxx.eu-central-1.compute.amazonaws.com）却一直失败，现在也没搞定，我把subdomain的“www”设置成域名跳转到主页作为临时的办法。

至此，我们的aws+LAMP搞定了，用时没超过2小时。
### 2. 用树莓派读取温度湿度传感器数值并上传数据库（通过requests）
#### 2.1 硬件连接DHT传感器
树莓派针脚的布局（派3至少是这样的）
![pi3_gpio.png](http://upload-images.jianshu.io/upload_images/94086-d2ded4861d89a250.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
淘宝来的温度湿度传感器，一定要找找模块名字，找到了叫DHT11，三个针脚VCC，DATA和GND
分别接到01(3.3v电压)，07(GPIO04)和06(GOUND)
![IMG_20171118_152932-01.jpeg](http://upload-images.jianshu.io/upload_images/94086-03977d8b8b00ea57.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
okay，希望成功吧，启动派，准备读取数值了。
#### 2.2 安装Adafruit Sensor Library，测试读取传感器数据
我原来一直以为我需要读出二进制raw data再换算成可读数据，还琢磨着我该怎么查看它的输出结构呢？
但其实库类已经把这个问题给handle了。移步 https://github.com/adafruit/Adafruit_Python_DHT
安装完之后，因为我的传感器是DHT11，所以需要修改一下它的simpletest.py
{% highlight python %}
import Adafruit_DHT
sensor = Adafruit_DHT.DHT11
pin = 4
humidity, temperature = Adafruit_DHT.read_retry(sensor, pin)
if humidity is not None and temperature is not None:
    print('Temp={0:0.1f}*C  Humidity={1:0.1f}%'.format(temperature, humidity))
else:
    print('Failed to get reading. Try again!')
{% endhighlight %}
成功啦！
![传感器数据读取成功.png](http://upload-images.jianshu.io/upload_images/94086-571490cf5550a495.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 2.3 要思考一下在哪里登录数据库
我们差一点就要在树莓派连接数据库了，因为当时我一直在想用php连接数据库该怎么码，后来Marek说用python连接MySQL不就好了，我那个时候思维被偏向了，我在派上安了MySQLdb，打算远程登录数据库，然后再插入数据，用root的身份和密码竟然被拒绝，这时我发现root是默认没有远程登录权限的，我要通过mysql script去赋予权限，
{% highlight bash %}
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
FLUSH PRIVILEGES;
{% endhighlight %}
随后还经历了mysql需要升级，却一直失败，最后要--force才通过
{% highlight bash%}
mysql_upgrade --force -uroot -p
{% endhighlight %}
直到都折腾完了，我才顿悟，我为什么要在派上登录呢？这情况派只要构建一个 Request 对象，其他事情都该交给服务器。所以接下来我们要开始在服务器上写php。


### 3. 创建数据库表结构，用php与MySQL对话，最终显示传感器实时数据至页面
#### 3.1 通过phpMyAdmin界面设计表结构
本科知识复习，建表，我们在数据库DHT下，建立了一个叫Pi_reader的表，其中有键：
- ID索引（作为主键同时自动加A_I）
- 温度
- 湿度 
- 时间戳
![Pi_reader表结构.png](http://upload-images.jianshu.io/upload_images/94086-e6cf2b7b870381eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
期间还温习了一下 [char、varchar和text的区别](http://www.cnblogs.com/billyxp/p/3548540.html)
####3.2 写用来登录数据库的db.php
Apache的web文件默认地址是 /var/www/html
其实root根本不需要远程登录权限。
db.php代码
{% highlight php %}
<?php

DEFINE('DB_USERNAME','root');
DEFINE('DB_PASSWORD','password');
DEFINE('DB_HOST','xx.xx.xxx.xxx');
DEFINE('DB_DATABASE','DHT');

$mysqli = new mysqli(DB_HOST, DB_USERNAME, DB_PASSWORD, DB_DATABASE);

if (mysqli_connect_error()){
die('Connect Error('.mysqli_connect_error().')'.mysqli_connect_error());
}
echo '<i>数据库已成功连接。</i>';

?>
{% endhighlight %}
####3.3 写用来执行插入操作的insert.php
我们需要将收到的请求，如果是合理的，就添加进数据库。
合理是指符合这样的格式
http://xx.xx.xxx.xxx.xxx/insert.php?temp=aa&hum=bb&token=0000
那么温度aa，湿度bb和timestamp都会被加入到数据库。
其中token是充当一个密码保护的功能。
如果数据项目很多的话，可以传输一个xml或者json
insert.php代码
{% highlight php%}
<?php
include 'db.php';

if(!isset($_GET["token"]) || $_GET["token"] != "0000"){
echo "wrong_token";
exit();
}

$temp = $_GET["temp"];
$hum = $_GET["hum"];
echo $temp;

$sql = "INSERT INTO Pi_reader (id,temperature,humidity,timestamp) VALUES (NULL, '". $temp ."','". $hum ."', CURRENT_TIMESTAMP);";
$result = mysqli_query($mysqli, $sql);

?>
{% endhighlight %}
####3.4 写显示页面
在第一集中，我只写了白板。
连接数据库后，读取一个表的所有记录，循环输出三个键值，温度，湿度，时间戳。
index.php
{% highlight php%}
<?php

echo "<h1>阿狗狗窝的温度湿度实时监视器</h1>";

include 'db.php';

$query = "SELECT * FROM Pi_reader";
$result = mysqli_query($mysqli, $query);

echo "<table>";
echo "<tr><td>" . "<b>温度</b>" . "</td><td>" . "<b>湿度</b>" . "</td><td>". "<b>时间</b>". "</td></tr>";

while($row = mysqli_fetch_array($result, MYSQLI_ASSOC))
{
echo "<tr><td>" . $row[temperature] . "°C  </td><td>" . $row[humidity] . "%  </td><td>". $row[timestamp]. "</td></tr>";
}

echo "</table>";
mysql_close();

?>
{% endhighlight %}
#### 2.4 回到树莓派，完成最后的对服务器的请求
现在只剩下最后一步了，在派每次读取数据后，我们想要它发送一个请求到服务器，这个请求说，“我请求把xx温度xx湿度加入数据库。”
而且我们希望1分钟可以更新一次数据。
我们安装了 [Requests](http://docs.python-requests.org/en/master/)
直接在刚才读取数据的脚本上粗暴地改动：
{% highlight python %}
#!/usr/bin/python

import Adafruit_DHT
import requests
import time

sensor = Adafruit_DHT.DHT11
pin = 4

#humidity, temperature = Adafruit_DHT.read_retry(sensor, pin)

temp_temp = 0
temp_hum = 0

while(1):
        humidity, temperature = Adafruit_DHT.read_retry(sensor, pin)

        if humidity is not None and temperature is not None:

                if str(humidity) is not str(temp_hum) or str(temperature) is not str(temp_temp):
                        r = requests.get("http://xx.xx.xxx.xxx/insert.php?token=0000&temp="+ str(temperature) +"&hum=" +str( humidity))
                        print(r.status_code)
                        print('Temp={0:0.1f}*C  Humidity={1:0.1f}%'.format(temperature, humidity))
                        temp_temp = temperature
                        temp_hum = humidity
        else:
                print('Failed to get reading. Try again!')
        time.sleep(60)
{% endhighlight %}
测试！
好，我们现在登录 http://wolfiethedog.de/woofaniot/s01e01/ 看看效果
![监视页面.png](http://upload-images.jianshu.io/upload_images/94086-9fe304f97132ef65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

页面出现了树莓派中获取的当下温度和湿度，细细研究，时间间距并不是准时的1分钟。request给服务器的过程中还是出现了些短小的延时的。

没有css的html终究是难看的，但是我们的毛坯房已经有了。下一集我和阿狗会专门研究显示的仪表盘，dashboard。

这个实验的额外收获就是，那个小小的加湿器在整个房间的效果上还是有功效的。
至此，《嚎出一个极简物联网》第一集《美味的云端LAMP派》就完成了。

耶！
