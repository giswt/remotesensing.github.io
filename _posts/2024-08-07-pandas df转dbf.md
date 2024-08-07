---
layout:     post
title:      2024-08-07-pandas df转dbf
subtitle:   
date:       2024-08-07
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - pandas
    - df
    - dbf
---

### 问题
想把pandas中的DataFrame转为dbf数据格式，便于在GIS软件中与空间数据进行JOIN操作，网上搜了很多文章，推荐试用pysal、dbfread
simpledbf和dbfpy的包，都无法实现dbf的创建，最后使用了dbf包实现了该功能。

### 思路 
读取df的列名和类型，创建空dbf表，遍历读取df表写入dbf表。 

### 代码
<font size=2 color=Red>运行环境：Python 3.6.13</font>    

1、读取文件    
```
import pandas as pd
import numpy as np
import dbf

def dataframe_to_dbf(df, dbf_filename):
    # 创建一个新的DBF表
    fields = format_fields(df.dtypes)
    table = dbf.Table(dbf_filename, fields)
    table.open(mode=dbf.READ_WRITE)

    # 将DataFrame中的数据逐行插入DBF表
    for index, row in df.iterrows():
        table.append(tuple(row))

    table.close()
    print(f"DataFrame has been successfully written to {dbf_filename}")

def format_fields(dtypes):
    # 将pandas的dtypes转换为DBF格式的字段
    fields = []
    for column, dtype in dtypes.items():
        if np.issubdtype(dtype, np.integer):
            field = f'{column} N(10,0)'  # 整数类型
        elif np.issubdtype(dtype, np.floating):
            field = f'{column} N(19,10)'  # 浮点数类型
        elif np.issubdtype(dtype, np.datetime64):
            field = f'{column} D'  # 日期类型
        else:
            field = f'{column} C(255)'  # 字符类型
        fields.append(field)
    return ';'.join(fields)

# 示例用法
if __name__ == "__main__":
    # 创建一个示例DataFrame
    #data = {
    #    'id': [1, 2, 3],
    #    'name': ['Alice', 'Bob', 'Charlie'],
    #    'age': [25, 30, 35],
    #    'join_date': pd.to_datetime(['2020-01-01', '2019-07-15', '2018-05-20'])
    #}

    df = data_total['WSDI']
    
    # 指定DBF文件名
    dbf_filename = 'example111.dbf'
    
    # 调用转换函数
    dataframe_to_dbf(df, dbf_filename)
```  
  

引用文献：  
[0] [GitHub GeoDaSandbox](https://github.com/GeoDaSandbox/sandbox/blob/master/pyGDsandbox/dataIO.py)    
[1] [GitHub dbf](https://github.dev/ethanfurman/dbf/blob/47e97cd998067e9b4278dea09f137136e488029e/dbf/utils.py)







 


