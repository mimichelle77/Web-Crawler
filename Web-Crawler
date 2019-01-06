# -*- coding: utf-8 -*-
"""
Created on Sun Feb  4 20:29:42 2018

@author: michelle
"""

# -*- coding: utf-8 -*-
"""
Spyder Editor

This is a temporary script file.

"""    
import requests 
from bs4 import BeautifulSoup
import json
from collections import OrderedDict
import pymongo
client = pymongo.MongoClient("localhost", 27017)
db = client.DBarticle #資料庫DBarticle
db1 = db.coll01 #內文資料表coll01
db2 = db.coll02 #目錄資料表coll02
res = ['http://tobacco.hpa.gov.tw/ContentList.aspx?MenuId=370&curPage=1']#目錄url
i = 0 #目錄頁數

def page(article): #讀取內文
    data = OrderedDict([('文章名稱', 1), ('日期', 2), ('內文', 3)])
    a_page = requests.get(article)
    a_soup = BeautifulSoup(a_page.text,'lxml')
    if a_page.status_code==200:
        for item in a_soup.select('.newstitle'):
            data['文章名稱']=item.text.strip()#文章標題
        for item1 in a_soup.select('.newsdatab'):
            data['日期']=item1.text.strip()#發布時間    
        for item2 in a_soup.select('#text'):
            data['內文']=item2.text.strip()#內容        
        jsondata = json.dumps(OrderedDict(data),ensure_ascii = False,indent = 4 )        
        db1.insert_one(data) 
        jsonfile = open('C:\\Web-Crawler.json', 'a', encoding = 'utf8')  
        jsonfile.write(jsondata)  
        jsonfile.close()

while True:   
    menu = OrderedDict([('目錄-標題', 1), ('目錄-日期', 2), ('目錄-內文', 3)])
    res1=requests.get(res[i])
    soup = BeautifulSoup(res1.text,'lxml')
    for j in range(len(soup.select('.newsdata'))):
        menu['目錄-日期']=soup.select('.newsdata')[j].text.strip()
        menu['目錄-內文']=soup.select('.newstable p')[j].text
        menu['目錄-標題']=soup.select('.newstable a')[j]['title']
        page("http://tobacco.hpa.gov.tw/" + soup.select('.newstable a')[j]['href'])#內文url傳至page       
        jsonmenu = json.dumps(OrderedDict(menu),ensure_ascii = False,indent = 4 )
        db2.insert_one(json.loads(jsonmenu, object_pairs_hook=OrderedDict))
        jsonfile = open('C:\\Web-Crawler-menu.json', 'a', encoding = 'utf8')  
        jsonfile.write(jsonmenu)  
        jsonfile.close()
    if res1.status_code==200:
        nextpage = soup.find('a',attrs={'id': 'aPagedown'}) 
        if nextpage['href']=='javascript:void(0);':break
        else:
            res.append("http://tobacco.hpa.gov.tw" + nextpage['href'])#目錄url加入list
            i = i + 1
  
