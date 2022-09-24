   **本文分为两部分 0.懒人直接操作步骤和 1- 5.搭建过程原理**



### 0：极简操作		

​		程序主体、待打卡名单和ie浏览器驱动三项已打包至云盘：【链接: https://pan.baidu.com/s/1cCdijXuVQ6IahuapkSrpeQ?pwd=nan1 提取码: nan1 复制这段内容后打开百度网盘手机App，操作更方便哦】

​		**将压缩包解压至任意位置，在list名单中依次输入学号、密码和姓名，用空格隔开，依次换行输入。list.txt 文件名和位置最好不要修改。**

​		**运行nuadaka.exe。**

​		测试成功后即可按照下文第5步将程序添加入windows的定时启动，每日定时自动打卡。

​		另：在校可能会因为没法长时间保持电脑开机待机状态，或校园网夜晚断联导致无法按时打卡。有两个解决方案，一为将电脑设为待机休眠不关机，而是再配合我写的每日6点自动登录验证校园网的程序，在打卡前先登录，很丝滑。二是，把程序部署到云服务器，更加丝滑 and elegant.

​		以下为第二部分。



- [一、**概述**](#一概述)
- [二、**步骤**](#二步骤)
  - [1.搭建python编程环境。](#1搭建python编程环境)
  - [2.安装selenium和浏览器驱动。](#2安装selenium和浏览器驱动)
  - [3.分析目标网站和**编写代码**。](#3分析目标网站和编写代码)
    - [**源码**](#源码)
    )
  - [4.打包程序。](#4打包程序)
  - [5.将程序加入windows任务计划程序。](#5将程序加入windows任务计划程序)

## 一、**概述**

​		利用**python**（编程语言）**驱动selenium**（python的一个库用于驱动浏览器）完成每日自动化打卡，并打包成exe文件。

​		并利用windows**任务计划程序**定时启动exe文件，或租用云电脑搭建环境定时启动。

## 二、**步骤**

### 1.搭建python编程环境。

https://www.bilibili.com/video/BV1pS4y1b7NA/?zw

个人推荐python + vscode。





### 2.安装selenium和浏览器驱动。

https://www.bilibili.com/video/BV1Z4411o7TA/?zw

[结合网站学习](www.byhy.net)，在selenium库中只需浅浅地学习**打开网页，定位网页元素、元素的点击和输入文本4个功能**即可完成本次项目。



### 3.分析目标网站和**编写代码**。

南艺每日健康打卡网页分为两大部分，一是登录部分，一是选择填写部分。

**其中有个坑就是两个网页的跳转中，无论你的网速有多快，在跳转过程中仍然需要停顿，直接跳转接定位是定位不到的，加上time.sleep()即可。这个坑坑我了好久，切记！**

------

#### **源码**

```python
import time
from xmlrpc.client import Server
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium import webdriver
import datetime

driver = webdriver.Edge()       

def f_c(xpath):
    a = driver.find_element(By.XPATH,xpath).click()                 #创建函数：定位并点击，xpath是定位位置

def f_s(xpath,b):
    a = driver.find_element(By.XPATH,xpath).send_keys(b)            #创建函数：定位并输入，b是文本内容

def fullProcess(name,psw):                                          #函数全流程，name是用户名，psw是密码
    driver.get('https://yqtb.nua.edu.cn/mp-czzx/webs/yqsb/sjhmcj/index.html')   

    f_s('//*[@id="userId"]',name)
    f_s('//*[@id="pwd"]',psw)
    f_c('//*[@id="qdtj_btn"]')
    time.sleep(1)

    f_c('/html/body/div[1]/div[4]/div[4]/div/div/div/div/div/div[1]/label')
    f_c('/html/body/div[1]/div[4]/div[7]/div/div/div/div/div/div[1]/label')
    f_c('/html/body/div[1]/div[4]/div[14]/div/div/div/div/div/div[1]/label')
    f_c('/html/body/div[1]/div[4]/label')
    f_c('//*[@id="wyyd"]')

def sec_jud(a,b,c):                  #二次判断是否签到成功
    try:
        fullProcess(a,b)
        print(c,'签到成功   此时时间是：',datetime.datetime.now())
    except:
        print('*WARNING',c,'签到失败    此时时间是：',datetime.datetime.now())

    time.sleep(1)


class userDate:
    def __init__(self,name,studentID,password):
        self.name = name
        self.studentID = studentID
        self.password = password

    def go(self):
        sec_jud(self.studentID,self.password,self.name)

with open('./list.txt') as f:
    content = f.readline()
    num = 0
    while content != '':
        num += 1
        print("读取第%d位，%s"%(num,content.rstrip()))
        res = content.split()
        tem_name = userDate(res[2],res[0],res[1]).go()
        content = f.readline()
```



### 4.打包程序。

​		使用pyinstaller将程序打包成exe文件。

https://www.bilibili.com/video/BV15i4y1U7F7/?zw


### 5.将程序加入windows任务计划程序。

https://www.bilibili.com/video/BV1tS4y1B7eM/?zw
