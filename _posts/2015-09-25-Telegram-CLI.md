---
layout: post
title:  "텔레그램(Telegram) CLI를 사용하여, 자동으로 메시지 보내기"
date:   2015-09-25 01:40:15
author: 남기찬
profile: gcnam.png
bio: WhaTapLabs Inc. Developer.
---

와탭은 사내 메신저로 Telegram (https://telegram.org/)을 사용하고 있습니다.

저희는 Telegram의 장점 중 아래와 같은 이유로 사용하고 있습니다.

  - 무료
  - 다양한 OS 및 장비 지원
  - 안정성
  - 자유롭고 빠른 이미지 및 파일 전송

![Telegram](/assets/images/gcnam/2015-09-25/Telegram_00.png)

Telegram 경우, API 및 소스가 공개되어 있습니다.
이를 통하여 개발된, 오픈 소스 중
Telegram messenger CLI ( https://github.com/vysheng/tg ) 를 사용하여,
서버의 MySQL 데이터를 Telegram 연동해서 자동화 하는 예제를 작성해 보고자 합니다.

### Telegram 가입

Telegram을사용하려면, 에 먼저 가입을 합니다.Telegram
모바일 앱이나, Windows 또는 Mac 프로그램을 사용하셔도 됩니다.
최초 사용이라면, 핸드폰으로 인증이 필수 입니다.
다음에 Telegram CLI를 설치할 때, Telegram 으로 인증 코드를 받아야 설치 할수 있습니다.

### Telegram CLI 설치

Telegram CLI는 명령창 ( Command-Line Interface ) 에서 실행하는, 메신저 입니다.
Telegram 을 Mobile 또는 PC 에 설치하듯이, 서버에 설치를 하도록 합니다.
여기서는 편의상 Database Server 에 같이 설치 하도록 하겠습니다.
Database Server는 Ubuntu 14.04 를 사용하고 있다고 가정합니다.

소스 받기 :

```
# git clone --recursive https://github.com/vysheng/tg.git && cd tg
```

빌드 툴 설치 :

```
# sudo apt-get install libreadline-dev libconfig-dev libssl-dev lua5.2 liblua5.2-dev libevent-dev libjansson-dev libpython-dev make
```

빌드 :

```
# ./configure
# make
```

텔레그램 인증 :

```
# ./bin/telegram-cli -k tg-server.pub
change_user_group: can't find the user telegramd to switch to
Telegram-cli version 1.3.3, Copyright (C) 2013-2015 Vitaly Valtman
Telegram-cli comes with ABSOLUTELY NO WARRANTY; for details type `show_license'.
This is free software, and you are welcome to redistribute it
under certain conditions; type `show_license' for details.
Telegram-cli uses libtgl version 2.0.3
Telegram-cli includes software developed by the OpenSSL Project
for use in the OpenSSL Toolkit. (http://www.openssl.org/)
Telegram-cli uses libpython version 2.7.6
I: config dir=[/root/.telegram-cli]
phone number:8210********
code ('CALL' for phone code): 78517
User 기찬 남 online (was online [2015/09/23 17:00:57])
>
```

핸드폰 번호를 입력하고, 핸드폰으로 온 인증코드를 입력합니다.

CLI를 통해서 Telegram 에 로그인이 되면, Telegram 의 CLI 상태에 진입합니다.

### Telegram CLI 명령 연습하기

CLI 명령어는 아래 문서에서 찾을수 있습니다.

https://github.com/vysheng/tg/wiki/Telegram-CLI-Commands

연락처 보기 명령:

```
> contact_list
WhaTap Inc
Gichan Nam
.
.
>
```

다른 사람에게 메시지 보내기:

```
> msg WhaTap_Inc Hello?
[17:17]  WhaTap Inc <<< Hello?
```
 - Tip 1. 이름과 성 사이의 띄워쓰기는 _ ( Underscore ) 를 넣어주시면 됩니다.
 - Tip 2. 수신인을 Group 으로 지정하면, 1번 메시지를 보낸것만으로 다량의 사용자에게 전달할수 있습니다.

CLI 종료

```
> quit
halt
```

이로서 Telegram에 메시지를 보낼수 있는 준비가 다 되었습니다.

### CLI 에 메시지 전달하는 스크립트 만들기

CLI는 매번 명령을 치고 화면을 보고 있으므로,
자동화에는 적합하지 않습니다.

단순한 메시지만 전달하고 종료하는 Bash Script 를 하나 작성해 보도록 하겠습니다.

Bash Script 작성하기 :

```
$ vim ~/send_telegram.sh
```

```
#!/bin/bash
export user=$1
export subject=$2;
export body=$3;
tgpath=/root/tg
cd ${tgpath}
${tgpath}/bin/telegram-cli -W -e "msg $user $subject" > /dev/null
exit
```

작성한 Bash Script 로 메시지 보내기 :

```
# chmod +x ~/send_telegram.sh
# ~/send_telegram.sh WhaTap_Inc 'Hello? Test BashScript'
```

### Python 으로 MySQL 데이터 조회하기

Python 개발 환경 설치 :

```
# apt-get install python-dev libmysqlclient-dev python-pip
# pip install mysql-python
```

Python Code 작성 :

```
# vim ~/send_server_version.py
```

```
#!/usr/bin/python

import MySQLdb

db = MySQLdb.connect("localhost","root","password")
cursor = db.cursor()
cursor.execute("SELECT VERSION()")
data = cursor.fetchone()
version = "Database version : %s " % data
print version
db.close()
```

Python 으로 DB 정보를 조회하였습니다

```
root@instance-4:~# python send_server_version.py
Database version : 5.5.44-0ubuntu0.14.04.1
```

### 조회한 데이터를 Telegram으로 보내도록 코드 수정

```
#!/usr/bin/python

import MySQLdb
import subprocess

def get_server_verion():
    db = MySQLdb.connect("localhost","user","password")
    cursor = db.cursor()
    cursor.execute("SELECT VERSION()")
    data = cursor.fetchone()
    version = "Database version : %s " % data
    print version
    db.close()

    subprocess.call(["./send_telegram.sh", "WhaTap_Inc", version, ""])

get_server_verion()
```

### 주기적으로 메시지 보내기 ( Crontab )

```
# crontab -e
```

```
20 */3 * * * /usr/bin/python /root/send_server_version.py$
```
마지막 줄에 위와 같이 적으면, 3시간 마다 20분에 스크립트를 실행합니다.

### 맺음말

위 예제 코드에서는 3시간 마다, 서버의 DataBase 버전 정보를 Telegram으로 보내는 단순한 코드를 작성해 보았습니다.

![Telegram화면](/assets/images/gcnam/2015-09-25/Telegram_01.png)

추가로 개발하면 할수 있는 것들

 - 좀 더 상세한 SQL 문 ( 예를 들면, 시간에 따라 변하는 가입자수 ) 을 작성한다면 메시지가 올때마다 데이터가 변화는 모습을 볼수 있을 것입니다.
 - 좀 더 많은 Python 코드를 작성한다면, 이미지 등도 전달 할수 있을 것입니다.

 적절하게 수정해서, 주기적으로 서버의 상태를 조회하고 Telegram로 받아보세요.

 감사합니다. 