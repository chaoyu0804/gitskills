# -*- coding: utf-8 -*-
"""
Created on Sun Jul 30 16:41:16 2017

@author: chao.yu
"""

from selenium import webdriver
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities
from selenium.webdriver.support.ui import WebDriverWait
import re
from bs4 import BeautifulSoup
import os

def scrape_ips_url(url = 'http://www.xicidaili.com/nn'):
    default_url = url
    phantomjs_path = r'D:\JOB\pachong\phantomjs-2.1.1-windows\bin\phantomjs.exe'
    dcap = dict(DesiredCapabilities.PHANTOMJS)
    dcap['phantomjs.page.settings.userAgent'] = (
        "Mozilla/5.0 (Linux; Android 5.1.1; Nexus 6 Build/LYZ28E)\
         AppleWebKit/537.36 (KHTML, like Gecko) Chrome/48.0.2564.23 Mobile Safari/537.36")
    driver = webdriver.PhantomJS(executable_path=phantomjs_path, desired_capabilities=dcap)
    driver.get(default_url)
    WebDriverWait(driver, 10)    
    bs_obj = BeautifulSoup(driver.page_source,'lxml')
    tds = bs_obj.find_all('td')    
    ips = dict()
    for (i, td) in enumerate(tds):
        match = re.findall(r'\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,4}',str(td))
        if match:
            ips[match[0]] = tds[i+1].get_text()
    driver.quit()
    return ips
        
def main():
    os.chdir(r'D:\JOB\pachong\cfda2')
    url_str = 'http://www.xicidaili.com/nn'
    IPS = dict()
    for i in range(1):
        if i == 0:
            ips = scrape_ips_url(url_str)
            IPS.update(ips)
        else:
            url = url_str + '/' + str(i)
            ips = scrape_ips_url(url)
            IPS.update(ips)
        print(i)
    
    with open('ips.txt','a',encoding='utf-8') as f:
        for key, val in IPS.items():
            f.write(key+':'+val+'\n')

if __name__ == '__main__':
    main()
    
    