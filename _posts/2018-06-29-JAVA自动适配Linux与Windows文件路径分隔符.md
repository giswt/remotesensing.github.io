---
layout:     post
title:      JAVA自动适配Linux与Windows文件路径分隔符
subtitle:   
date:       2018-6-29
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Java
    
---
1、问题
    JAVA自动适配Linux与Windows文件路径分隔符
    
2、解决方案
   linux文件路径分隔符为 /  ，windows的文件路径分隔符为  \   ，在开发项目过程中不确定用户使用何种操作系统，就需要自动适配路径。
   目前已知java提供两种方法获取文件路径分割符：
    File.separator
    System.getProperty("file.separator")
	
	提供一个简单工具类：
	public class FilePathUtil {  
		public static final String FILE_SEPARATOR = System.getProperty("file.separator");  
		//public static final String FILE_SEPARATOR = File.separator;  
	  
		public static String getRealFilePath(String path) {  
			return path.replace("/", FILE_SEPARATOR).replace("\\", FILE_SEPARATOR);  
		}  
	  
		public static String getHttpURLPath(String path) {  
			return path.replace("\\", "/");  
		}  
   }
   
3、其他说明
	当然也可以使用request方法获取文件路径：
	String serverPath = request.getServletContext().getRealPath("/assets/data/templete/import/");
	此种方法会自动适配文件分隔符获取到工程目录下的 assets/data/templete/import 文件夹，不必考虑分隔符文件路径
	PS：默认情况下，直接写linux路径（/），tomcat运行在windows下是可以找到正确路径的；但是写windows路径（\）,在linux平台下不行。
	
摘自：[寄于念](https://blog.csdn.net/rimac/article/details/51836932) 

