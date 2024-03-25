---
layout:     post
title:      Python 处理NOAA气象数据
subtitle:   
date:       2024-03-25
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Python
    - 高德地图
    - PostgreSQL
    - NOAA     
---

### 问题
将NOAA中国区域的气象数据下载下来存入PostgreSQL

### 思路 
根据[NOAA全球气象数据按天记录数据的网页](https://www.ncei.noaa.gov/data/global-summary-of-the-day/archive/)解析html,获得每个文件的文件名称，再加上url的前缀，组成下载链接，下载文件并解压缩。创建PostgreSQL数据表，并设置相关主键，将解压缩获得的csv文件中的中国站点数据插入到数据表中，并根据站点的经纬度数据反查所在的省市县。

### 步骤
<font size=1 color=Red>运行环境：Python 3.6.13   PostgreSQL  16.2.1</font> 
####  按年下载NOAA气象数据   
```
import requests
import os
from lxml import etree
import sys


#定义下载年份区间

years=[1985,2023]

print("downloading with requests")
tar_gz_url = 'https://www.ncei.noaa.gov/data/global-summary-of-the-day/archive/'
# 定义请求头部信息
headers = {'User-Agent': 'M'}
# 发送请求
resp= requests.get(tar_gz_url,headers=headers)
tar_gz_resp_text = resp.text
# 根据返回的text创建etree对象才能使用xpath分析
tar_gz_etree_element = etree.HTML(tar_gz_resp_text)
tar_gz_url_xpath = tar_gz_etree_element.xpath('//*/table/tr/td/a')
#for i in tar_gz_url_xpath:
    #print(i.xpath('@href'))
    #ste_name=i.xpath('@href')[0][:4]
    #print(ste_name)
for tar_gz in tar_gz_url_xpath[1:]:
    # 打印要下载的文件名
    # print(tar_gz.xpath('@href')[0])
    requests_url = tar_gz_url + tar_gz.xpath('@href')[0]
    file_name = tar_gz.xpath('@href')[0]
    year=int(file_name[:4])
    if year <years[0] or year >years[1]:
        continue
    # 打印拼接以后的下载链接
    # print(requests_url)
    # 通过下载链接创建对象r
    r = requests.get(requests_url)
    # 如果当前文件夹没有tar_gz目录则创建该目录
    if 'tar_gz' not in [x for x in os.listdir('.') if os.path.isdir(x)]:
        try:
            os.mkdir('tar_gz')
        except:
            print('创建文件夹失败')
    # 如果在目录tar_gz下已经有文件了则不重复下载,否则下载
    if file_name in [x for x in os.listdir('tar_gz')]:
        print('%s文件已下载'%(file_name))
    else:
        # 通过拼接的下载url下载文件,下载文件存储在目录tar_gz下
        with open(f'tar_gz/{file_name}', "wb") as code:
            code.write(r.content)
            print('下载文件%s成功'%(file_name))
```
#### 解压缩文件  
```
import tarfile
import os
def untar(fname, dirs):
    t = tarfile.open(fname)
    t.extractall(path = dirs)
 
# print(os.listdir('tar_gz'))
# t=tarfile.open('tar_gz/1929.tar.gz')
# t.extractall(path='tar_gz/1929')
 
def _decomp_tar_gz():
    # 遍历压缩包文件夹，获取的是所有压缩包文件名的list
    for tar_gz_file in os.listdir('tar_gz'):
        # print(tar_gz_file)
        # 把压缩的文件名使用.分割，取第一个元素作为解压缩文件的文件夹，例如文件2021.tar.gz则全部解压缩到文件夹tar_gz/2021下
        tar_gz_dir = tar_gz_file.split('.')[0]
        if os.path.isfile(f'tar_gz/{tar_gz_file}'):
            untar(f'tar_gz/{tar_gz_file}',f'tar_gz/{tar_gz_dir}')
_decomp_tar_gz()
```
#### PostgreSQL 数据表创建  
  字段注释来源于参考文献1  
```
DROP TABLE IF EXISTS noaadata;
CREATE TABLE noaadata (
  station char(11) NOT NULL ,
  date date NOT NULL ,
  latitude float DEFAULT NULL ,
  longitude float DEFAULT NULL ,
  elevation  float DEFAULT NULL ,
  name  varchar(50) DEFAULT NULL ,
  temp float DEFAULT NULL ,
  temp_attributes int DEFAULT NULL ,
  dewp float DEFAULT NULL ,
  dewp_attributes int DEFAULT NULL ,
  slp float DEFAULT NULL ,
  slp_attributes int DEFAULT NULL ,
  stp float DEFAULT NULL ,
  stp_attributes int DEFAULT NULL ,
  visib float DEFAULT NULL ,
  visib_attributes int DEFAULT NULL ,
  wdsp float DEFAULT NULL ,
  wdsp_attributes int DEFAULT NULL ,
  mxspd float DEFAULT NULL ,
  gust float DEFAULT NULL ,
  max float DEFAULT NULL ,
  max_attributes varchar(10) DEFAULT NULL ,
  min float DEFAULT NULL ,
  min_attributes varchar(10) DEFAULT NULL ,
  prcp float DEFAULT 0 ,
  prcp_attributes char(1) DEFAULT NULL ,
  sndp float DEFAULT NULL ,
  frshtt char(6) DEFAULT '000000' ,
  PRIMARY KEY (station, date)
) ;
COMMENT ON TABLE noaadata IS '气象站点数据表';

-- 添加注释到列


  COMMENT ON COLUMN noaadata.station IS '站点ID';
  COMMENT ON COLUMN noaadata.date  IS '日期';
  COMMENT ON COLUMN noaadata.latitude IS '纬度';
  COMMENT ON COLUMN noaadata.longitude IS '经度';
  COMMENT ON COLUMN noaadata.elevation  IS '海拔';
  COMMENT ON COLUMN noaadata.name  IS '城市英文名';
  COMMENT ON COLUMN noaadata.temp IS '平均温度';
  COMMENT ON COLUMN noaadata.temp_attributes IS '计算平均温度的观测次数';
  COMMENT ON COLUMN noaadata.dewp IS '平均露点';
  COMMENT ON COLUMN noaadata.dewp_attributes IS '计算平均露点的观测次数';
  COMMENT ON COLUMN noaadata.slp IS '海平面压力';
  COMMENT ON COLUMN noaadata.slp_attributes IS '计算海平面压力的观测次数';
  COMMENT ON COLUMN noaadata.stp IS '当天平均站压';
  COMMENT ON COLUMN noaadata.stp_attributes IS '平均站压的观测次数';
  COMMENT ON COLUMN noaadata.visib IS '能见度';
  COMMENT ON COLUMN noaadata.visib_attributes IS '能见度观测次数';
  COMMENT ON COLUMN noaadata.wdsp IS '平均风速';
  COMMENT ON COLUMN noaadata.wdsp_attributes IS '计算平均风速的观测次数';
  COMMENT ON COLUMN noaadata.mxspd IS '最大持续风速';
  COMMENT ON COLUMN noaadata.gust IS '最大阵风';
  COMMENT ON COLUMN noaadata.max IS '最高气温';
  COMMENT ON COLUMN noaadata.max_attributes IS '空白表示最高明确温度而非小时数据';
  COMMENT ON COLUMN noaadata.min IS '最低气温';
  COMMENT ON COLUMN noaadata.min_attributes IS '空白表示最低明确温度而非小时数据';
  COMMENT ON COLUMN noaadata.prcp IS '降雨量';
  COMMENT ON COLUMN noaadata.prcp_attributes IS '取值ABCDEFGH分别代表A 1-6小时 B 2-6小时 C 3-6小时 D 4-6小时 E 1-12小时 F 2-12小时 G 1-24小时即全天 HI不完整降雨记录 一般值为G';
  COMMENT ON COLUMN noaadata.sndp IS '降雪量';
  COMMENT ON COLUMN noaadata.frshtt IS '发生气象事件的概率默认0代表未发生1代表发生,六位分别代表Fog雾Rain雨Snow雪Hail冰雹Thunder雷电Tornado龙卷风';
  
  
 --- 创建一个新表，保存站点经纬度对应的省市县数据 
DROP TABLE IF EXISTS stationinfo;
CREATE TABLE stationinfo (
  station_id char(11) NOT NULL ,
  name varchar(50) NULL ,
  latitude float DEFAULT NULL ,
  longitude float DEFAULT NULL ,
  country varchar(20) DEFAULT NULL ,
  province varchar(20) DEFAULT NULL ,
  city varchar(20) DEFAULT NULL ,
  district varchar(20) DEFAULT NULL ,
  PRIMARY KEY (station_id)
) ;

COMMENT ON TABLE stationinfo IS '气象站点行政区划';

COMMENT ON COLUMN stationinfo.station_id IS '站点ID';
COMMENT ON COLUMN stationinfo.name  IS '城市英文名';
COMMENT ON COLUMN stationinfo.latitude IS '纬度';
COMMENT ON COLUMN stationinfo.longitude IS '经度';
COMMENT ON COLUMN stationinfo.country IS '国家';
COMMENT ON COLUMN stationinfo.province IS '省';
COMMENT ON COLUMN stationinfo.city IS '城市';
COMMENT ON COLUMN stationinfo.district IS '县';
```
#### 插入数据到NOAADATA表中  
根据参考文献记录，NOAA全球气象数据csv文件名5为前缀的是中国区域的数据，插入数据只处理这些文件中的数据。


```
import csv
import os

import psycopg2
 
# 打开数据库连接
db = psycopg2.connect(host='localhost',
                      port='5432',
                      user='postgres',
                      password='postgres',
                      database='postgres')
 
# 创建游标对象

cursor = db.cursor()
 
# 插入数据库函数，传递参数为csv文件路径
def insert_data_from_csv(csv_file):
    f = csv.reader(open(csv_file,'r'))
    for i in f:
        # csv第一行是字段名，排除掉
        if i[0] == "STATION":
            continue
        sql = 'insert into noaadata values(%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)'
        # print(i)
        # 插入到数据库
        try:
            cursor.execute(sql,list(i[n] for n in range(28)))
            
        except:
            print('插入数据错误')
       
# 遍历接压缩后的文件夹，获取csv文件，如果符合条件则把文件名作为参数传递给函数insert_data_from_csv插入数据
def insert_data():
    # 遍历文件夹tar_gz
    for tar_gz_dir in os.listdir('tar_gz'):
        # 如果是文件夹则继续遍历即只遍历下面的文件夹，排除压缩文件
        if os.path.isdir(f'tar_gz/{tar_gz_dir}'):
            # 排除压缩文件继续遍历文件夹,下面的遍历文件列表为csv文件
            for tar_gz_file in os.listdir(f'tar_gz/{tar_gz_dir}'):
                # 切割文件，如果csv文件是以5开头则代表是中国的气象站，则调用插入函数插入，否则不处理
                if f'tar_gz/{tar_gz_dir}/{tar_gz_file}'.split('/')[2][0] == '5':
                    print('是中国的气象站网数据库插入数据%s'%(f'tar_gz/{tar_gz_dir}/{tar_gz_file}'))
                    insert_data_from_csv(f'tar_gz/{tar_gz_dir}/{tar_gz_file}')
                    # 提交事务
                    db.commit()
                else:
                    print('外国气象站数据不处理')
insert_data();
 
# 释放游标及数据库连接
cursor.close()
db.close()
```
Postgres会拒绝文件中经纬度为空的数据插入，报“插入数据错误”的提示。对于里面数据为0的数据，会插入，需要自行删除
```
delete FROM noaadata  where latitude<0.1 OR longitude <0.1 ;
```
#### 更新站点信息表
根据站点的经纬度信息，利用地图 WEB API提供的经纬度信息，可以解析出其所在的省、市、县。目前高德地图和百度地图对该服务采取了并发量和日调用量的显示，二者的并发限制均为30QPS，即每秒30次，日调用量高德为5000次/天，百度为300次/天。站点的信息大约600多个，因此，本方案选择了高德地图的API。
```
import json
import requests
 
# 高德API服务的Key
API_KEY = '你自己的Key'
 
# 逆地址解析接口
REVERSE_GEO_API = 'https://restapi.amap.com/v3/geocode/regeo'
 
# 调用逆地址解析服务
def reverse_geocode(lat: float, lng: float):
    params = {
        'key': API_KEY,
        'location': f'{lng},{lat}',  # 格式为'经度,纬度'
    }
    response = requests.get(REVERSE_GEO_API, params=params)
    if response.status_code == 200:
        data = json.loads(response.text)
        if data['status'] == '1':
            # 解析数据并返回结果
            print(data)
            address=data['regeocode']['addressComponent']
            return {
               item: address[item] for item in ['country', 'province', 'city', 'district']
                }
        else:
            return 'Error: ' + data['info']
    else:
        return 'Error: 请求失败'
 
# 示例使用
# 北京天安门的经纬度
address = reverse_geocode(36.55,106.15)
print(address)
```  
由于每秒30次的并发限制，我写代码时加入了Sleep函数，限制每秒的并发次数。 插入数据时利用DISTINCT ON函数对重复站点进行了过滤，保证只插入一个站点。   

```
import psycopg2
import time
 
# 打开数据库连接


def insert_station_info():
    db = psycopg2.connect(host='localhost',
                      port='5432',
                      user='postgres',
                      password='postgres',
                      database='postgres')
 
    # 创建游标对象

    cursor = db.cursor()
    #db = pymysql.connect(host=HOST, user=USER, passwd=PASSWORD, db=DB, charset=CHARSET)
    #cursor = db.cursor()
    # 查找数据库，如果station重复则保留一个结果
    station_number = cursor.execute('SELECT DISTINCT ON (station) * FROM noaadata ORDER BY station')
    # 所有查询结果
    station_result = cursor.fetchall()
    # 遍历所有结果，然后把数据插入info表，其中字段station_id name latitude longitude从原始表data取值不修改
    # 字段country province city district分别代表国家，省份，城市，县或区级信息是通过传递经纬度调用百度api取到的
    for i in station_result: 
        #print(baidu_api.get_location(i[2],i[3]))
        sql = 'insert into stationinfo values(%s, %s, %s, %s, %s, %s, %s, %s)'
        values = [i[0],i[5],i[2],i[3],reverse_geocode(i[2],i[3])['country'],reverse_geocode(i[2],i[3])['province'],reverse_geocode(i[2],i[3])['city'],reverse_geocode(i[2],i[3])['district']]
        print(values)
        time.sleep(0.1)
        cursor.execute(sql,values)
        db.commit()
    # 释放游标及数据库连接
    cursor.close()
    db.close()

insert_station_info()
```

引用文献：   
[1] [ Python 获得NOAA全球开放气象数据](https://www.cnblogs.com/minseo/p/15745983.html)  
[2] [NATIONAL CENTERS FOR ENVIRONMENTAL INFORMATION GLOBAL SURFACE SUMMARY OF DAY DATA (GSOD) (OVER 9000 WORLDWIDE STATIONS)](https://www.ncei.noaa.gov/data/global-summary-of-the-day/doc/readme.txt)      
[3] [Python数据库操作【四】—— PostgreSQL](https://blog.csdn.net/XUMENGCAS/article/details/124139572)  


 


