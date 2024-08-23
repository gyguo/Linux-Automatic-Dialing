# Introduction|介绍

### Linux服务器断网及重启后自动重新拨号，适合个人及高校实验室
### Linux server automatically redials after network disconnection and reboot, suitable for individuals and university laboratories

## [1. 中文教程](#1-设置教程)
## [2. English Tutorial](#2-English-Tutorial)

---

## 1. 中文教程

### 1) 设置教程

##### 1. 所有操作必须使用root账号， 把ic.py复制到一个特定目录
```
注：脚本ic.py是判断能够打开百度和淘宝两个网页，两个网站同时打不开时选择重新拨号
```
##### 2. 把系统的拨号设置设置为默认
```
sudo gedit /etc/ppp/options
设置 lcp-echo-failure 4
设置 lcp-echo-interval 30
```

##### 3. 创建定时任务 
```
sudo crontab -e
```
选择自己习惯的编辑器，这里用的vim

##### 4. 添加定时任务，每5分钟执行一次脚本ic.py，需要使用系统默认的python2.7
```
*/5 * * * * /usr/bin/python /home/user/ic.py
```

##### 5. 重启cron
```
sudo service cron restart
```

##### 6. 确定任务输入没有问题，注意路径不要出错
```
sudo crontab -l
```

### 2) ic.py
这是一个简单python脚本，对**大陆**用户，判断能够打开**百度和淘宝**两个网页，两个网站**同时打不开**时选择重新拨号

```
import urllib2 as ulb
import logging
import subprocess
import os

def network_status(url, num_attempts=10):
    attempts = 0
    while True:
        print('try {}/{} to {}'.format(attempts+1, num_attempts, url))
        try:
            ulb.urlopen(url, timeout=3)
        except:
            attempts += 1
            if attempts == num_attempts:
                return False
            else:
                continue
        break
    return True

def network_redial():
    command_off = 'sudo poff dsl-provider'
    command_on = 'sudo pon dsl-provider'

    print('network dailing')
    try:
        os.system(command_off)
    except:
        logging.warnings('fail to running poff')

    os.system(command_on)

# for China mainland uses
url1 = 'https://www.baidu.com/'
url2 = 'https://www.taobao.com/'

is_connect1 = network_status(url1, num_attempts=10)
is_connect2 = network_status(url2, num_attempts=10)
if is_connect1 or is_connect2:
    print("Network reconnect successfully")
else:
    logging.warning('Network connection failed, redial')
    network_redial()
```

---


## 2. English Tutorial

### 1) Configurarions

##### 1. All operations must use the root account, copying ic.py to a specific directory
```
Note: The script ic.py is used to determine if two web pages, [Google](https://www.google.com/) and [X](https://x.com/), can be opened. If both websites cannot be opened at the same time, it selects to redial
```
##### 2. Set the dialing settings of the system to default
```
sudo gedit /etc/ppp/options
set lcp-echo-failure 4
set lcp-echo-interval 30
```

##### 3. 创建定时任务 
```
sudo crontab -e
```
选择自己习惯的编辑器，这里用的vim

##### 4. 添加定时任务，每5分钟执行一次脚本ic.py，需要使用系统默认的python2.7
```
*/5 * * * * /usr/bin/python /home/user/ic.py
```

##### 5. 重启cron
```
sudo service cron restart
```

##### 6. 确定任务输入没有问题，注意路径不要出错
```
sudo crontab -l
```

### 2) ic.py
这是一个简单python脚本，对**大陆**用户，判断能够打开**百度和淘宝**两个网页，两个网站**同时打不开**时选择重新拨号

```
import urllib2 as ulb
import logging
import subprocess
import os

def network_status(url, num_attempts=10):
    attempts = 0
    while True:
        print('try {}/{} to {}'.format(attempts+1, num_attempts, url))
        try:
            ulb.urlopen(url, timeout=3)
        except:
            attempts += 1
            if attempts == num_attempts:
                return False
            else:
                continue
        break
    return True

def network_redial():
    command_off = 'sudo poff dsl-provider'
    command_on = 'sudo pon dsl-provider'

    print('network dailing')
    try:
        os.system(command_off)
    except:
        logging.warnings('fail to running poff')

    os.system(command_on)

# for China mainland uses
url1 = 'https://www.baidu.com/'
url2 = 'https://www.taobao.com/'

is_connect1 = network_status(url1, num_attempts=10)
is_connect2 = network_status(url2, num_attempts=10)
if is_connect1 or is_connect2:
    print("Network reconnect successfully")
else:
    logging.warning('Network connection failed, redial')
    network_redial()
```


