#!/usr/bin/env python3
#coding: utf-8
'''
Created on 2016-12-2

@author: linsheng
'''
from bs4 import  BeautifulSoup
import  pycurl
import StringIO
import urllib
import  os
import smtplib
from email.mime.multipart import MIMEMultipart     #导入MIMEMultipart类
from email.mime.text import MIMEText               #导入  MIMEText
from email.mime.image import MIMEImage  
from email.header import Header   
from pychartdir import *
import time

#从网页获取数据写到文件中
def findver(urlpath,version):
    c = pycurl.Curl()
    postdata={'ver':str(version)}
    result = StringIO.StringIO()
    c.setopt(pycurl.WRITEFUNCTION,result.write)
    c.setopt(pycurl.POSTFIELDS,urllib.urlencode(postdata))
    c.setopt(pycurl.URL,urlpath)
    c.setopt(pycurl.HTTPHEADER,['User-Agent:Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.75 Safari/537.36','Cookie:_ga=GA1.2.650805965.1476944112; csrftoken=78f5b16dae00eb2380c23b05025b0742; sessionid=2997dcc6c2f8604b4fdc291d012b8689'])
    c.perform()
    html=result.getvalue()
    result.close()
    soup = BeautifulSoup(html, 'lxml')
    print soup
    with open('name','a') as soupswrite:
        soupswrite.write(str(soup))
    time =soup.select("#datatable > tbody > tr > td:nth-of-type(1)")
    ver =soup.select("#datatable > tbody > tr > td:nth-of-type(2)")
    wifi =soup.select("#datatable > tbody > tr > td:nth-of-type(3)")
    moble =soup.select("#datatable > tbody > tr > td:nth-of-type(4)")
    
    for times,vers,wifis,mobles in zip(time,ver,wifi,moble):
         data={
             'time':times.get_text(),
             'ver':vers.get_text(),
             'wifi':wifis.get_text(),
             'moble':mobles.get_text()
         }
         with open(str(version)+'.txt','a') as writes:
             writes.write(str(data['ver'])+' ')
             writes.write(str(data['wifi'])+' ')
             writes.write(str(data['moble'])+' ')
             writes.write('\n')


#画图

def drawbrokenline():
    wifivalues =[]
    yidongvalues =[]
    ver =[]
    rootDir = os.getcwd()
    for root, dirs, files in os.walk(rootDir):
        for filename in files:
            if filename.endswith('.txt'):
                wifichang = 0
                yidong = 0
                with open(filename,'r') as filenameread:
                    readfile=filenameread.readlines()
                filenumber=len(readfile)
                for fileread in readfile:
                        fileread=fileread.split(" ")
                        print fileread
                        wifichang =wifichang+float(fileread[1])
                        yidong = yidong +float(fileread[2])
                ver.append(str(fileread[0]))
                wifivalues.append(round(wifichang / filenumber, 2))
                yidongvalues.append(round(yidong / filenumber, 2))
    print ver     
    print wifivalues
    print yidongvalues       
    #生成Char对象
    c = XYChart(600, 300, 0xeeeeff, 0x000000, 1)
    #设置绘图区域
    c.setPlotArea(55, 58, 520, 195, 0xffffff, -1, -1, 0xcccccc, 0xcccccc)
    
    c.addLegend(50, 30, 0, "arialbd.ttf", 9).setBackground(Transparent)
    #添加头信息
    c.addTitle("The average daily traffic to make things", "timesbi.ttf", 15).setBackground(0xccccff, 0x000000,glassEffect())
    
    c.xAxis().setTitle("version")
    
    c.yAxis().setLabelFormat("{value|2}")
    
    c.xAxis().setLabels(ver)

    c.yAxis().setTitle("traffic valuse")
    
    layer = c.addLineLayer()
    
    layer.addDataSet(wifivalues, 0xcf4040, "wifi #1").setDataSymbol(SquareSymbol, 7)
    
    layer.addDataSet(yidongvalues, 0x40cf40, "mobile #2").setDataSymbol(DiamondSymbol, 9)

    layer.setDataLabelFormat("{value|2}")
    path = rootDir + "\\" + 'data' + "_" + "meitu" + ".png"
    c.makeChart(path)
    if wifivalues !=[] and yidongvalues !=[] and ver !=[]:
        send_email()

#发送邮件
def send_email():
    mail_host="smtp.gmail.com"  #设置服务器
    mail_user="hulinsheng@conew.com"    #用户名
    mail_pass="123456789"   #口令 
    
    
    sender = 'hulinsheng@conew.com'
    receivers = ['hulinsheng@conew.com','wujiali@cmcm.com']                  #定义邮件发件人
    
    msgRoot = MIMEMultipart('related')
    subject = '上报流量数据邮件'
    msgRoot['Subject'] = Header(subject, 'utf-8')
      
    readfile(msgRoot)  
    try:
        smtpObj = smtplib.SMTP_SSL() 
        smtpObj.connect(mail_host,  465)    # 25 为 SMTP 端口号
        smtpObj.login(mail_user,mail_pass)  
        smtpObj.sendmail(mail_user, receivers, msgRoot.as_string())    
        print "邮件发送成功"
    except smtplib.SMTPException, e:
        print "Error: 无法发送邮件" +str(e)
        
#读取文件夹下面的png图片
def readfile(msgRoot):
    i = 0
    for root ,dirs ,files in os.walk('D:/work code/pythons/BIquery'): 
        
        for filename in files:
            if filename.endswith('.png'):
                
                    mail_msg ="""
                    <br>
                    <br>
                    <br/>
                    <br/>
                    <br/>
                    <p><img src ='cid:image%s'></p>              
                    """ % i
                    msgText = MIMEText('<img src="cid:image%s">' % i,'html','utf-8')
                    msgRoot.attach(msgText)
                    print str(filename)
                    # 指定图片为当前目录
                    fp = open('D:/work code/pythons/BIquery/'+str(filename), 'rb')
                    
                    print str(filename)
                    msgImage = MIMEImage(fp.read())
                    print fp
                    # 定义图片 ID，在 HTML 文本中引用
                    msgImage.add_header('Content-ID', '<image%s>' % i )
                    #print msgImage
                    msgRoot.attach(msgImage)                                    
                    i+=1
                    fp.close()



if __name__ == '__main__':
    
    #while True:
        urlpath = 'http://bi.bibi.com/report/common/report_show_new?id=11233'
        with open('ver','rb') as verread:
            versions=verread.readlines()
        for version in  versions:
            print version
            if os.path.exists(version.rstrip()+'.txt'):
                os.remove(version.rstrip()+'.txt')
            findver(urlpath,version.rstrip())    
        drawbrokenline()
        time.sleep(86400)#86400


