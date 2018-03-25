# 实验公交车数据构造
## 背景
实验需要用到数据, 但是老师没有提供, 只好自己动手丰衣足食了

## 原始数据
根据实验指导提供的信息搜索到的一下原始数据, 可以在这个[网站](https://www.transitwiki.org/TransitWiki/index.php/Publicly-accessible_public_transportation_data), 或者这个[网站](https://code.google.com/archive/p/googletransitdatafeed/wikis/PublicFeeds.wiki)获得原始数据. 不过原始数据的格式是[**GTFS(General Transit Feed Specification)**]( https://developers.google.com/transit/gtfs/)实验需要用的数据是处理过的. 接下来需要对原始数据进行处理

## 数据格式化
根据报告中的例子, 我们需要格式化之后的数据包含如下属性
4. 每块个trip一个block
5. 每个block拥有以下数据
    1. route_id
    2. 这个trip对应的stop的数量
    3. trip中每个站的名字, 时间和经纬度
### 思路
1. 从 trips.txt 中 得到 route_id 和 trip_id 之间的关系
2. 从 stops_times.txt 中获得每个trip中
    1. stop的数量
    2. stop的名字(可以考虑用stop_id)
    3. 到达stop的时间
3. 从 stops.txt 中获得 stop_id 对应的经纬度 

#### 数据处理
1. 计划使用python, 用的[anaconda](https://www.anaconda.com/download/#windows)集成环境. py3.
2. 合并不同数据参考[教程](https://pandas.pydata.org/pandas-docs/stable/10min.html)
4. 现在需要将时间换成秒: 时间转换:参考[博客](http://blog.sina.com.cn/s/blog_4b1452dd0102x1e5.html), 使用 apply批量转换, 时间之间的格式转换参考[博客](http://blog.sina.com.cn/s/blog_b09d460201018o0v.html), 和[SO](https://stackoverflow.com/questions/2518706/python-mktime-overflow-error), 使用 datetime 库来转换, 然后又遇到一个问题`time data '24:01:00' does not match format '%H:%M:%S'`,  考虑使用正则. 直接使用字符串截取加类型转换再取余解决了. 
5. 现在要如何按规则写入文件呢. 目前写入的csv文件格式如下:
```
trip_id,route_id,stop_id,arrival_time,stop_lat,stop_lon
t_10131_b_none_tn_0,859,781715,22320.0,40.735859999999995,-74.02922
t_10131_b_none_tn_0,859,781732,22800.0,40.73295,-74.00707
t_10131_b_none_tn_0,859,781734,22920.0,40.73424,-73.9991
t_10131_b_none_tn_0,859,781736,22980.0,40.73735,-73.99684
t_10131_b_none_tn_0,859,781738,23040.0,40.7429,-73.99278000000001
t_10131_b_none_tn_0,859,781740,23160.0,40.74912,-73.98827
t_10132_b_none_tn_0,859,781715,22920.0,40.735859999999995,-74.02922
t_10132_b_none_tn_0,859,781732,23400.0,40.73295,-74.00707
t_10132_b_none_tn_0,859,781734,23520.0,40.73424,-73.9991
t_10132_b_none_tn_0,859,781736,23580.0,40.73735,-73.99684
t_10132_b_none_tn_0,859,781738,23640.0,40.7429,-73.99278000000001
t_10132_b_none_tn_0,859,781740,23760.0,40.74912,-73.98827
t_10133_b_none_tn_0,859,781715,23520.0,40.735859999999995,-74.02922
t_10133_b_none_tn_0,859,781732,24000.0,40.73295,-74.00707
t_10133_b_none_tn_0,859,781734,24120.0,40.73424,-73.9991
t_10133_b_none_tn_0,859,781736,24180.0,40.73735,-73.99684
t_10133_b_none_tn_0,859,781738,24240.0,40.7429,-73.99278000000001
t_10133_b_none_tn_0,859,781740,24360.0,40.74912,-73.98827
t_10134_b_none_tn_0,859,781715,24120.0,40.735859999999995,-74.02922
```
需要把trip_id和route_id单独提取出来, 还要加上trip的stop的数量

6. 直接用一个循环将数据按格式写到文件里面就行了.
7. 结果如下