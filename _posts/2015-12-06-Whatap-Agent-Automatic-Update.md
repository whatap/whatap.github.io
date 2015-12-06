---
layout: post
title:  "Windows Whatap Agent 자동 업데이트 하기"
date:   2015-12-06 17:00:00
author: 남형석
profile: hsnam.png
---
와탭 Agent가 업데이트 될 때 마다 서버에 접속하여 업데이트 명령 혹은 설치파일을 실행하는것은 설치 대수가 많으면 힘든 일 일수 있습니다. vbscript 와 windows scheduler를 통해서 주기적으로 업데이트를 확인하고 신버젼 발견시 업데이트를 하는 방법에 대해 적어보겠습니다.

### 1. Windows Whatap Agent 설치
Whatap에 가입하고 윈도우 서버에 Whatap Agent를 설치합니다.
설치방법은 와탭에 로그인 후 왼쪽 메뉴의 서버추가를 클릭하시면 볼 수 있습니다.

### 2. 서버에 업데이트 설정 추가
#### 1) pc 에서 브라우져를 열어 https://raw.githubusercontent.com/whatap/tools/master/install/whatap_agent_update.vbs 에 접속하여 다른 이름으로 저장하기를 이용하여 파일을 whatap_agent_update.vbs 로 저장합니다.
#### 2) 서버의 c:\program files\whatap 폴더에 위에서 다운로드 받은 파일을 복사합니다.
#### 3) 서버에서 명령 프로프트 창을 엽니다.
#### 4) 아래와 같이 입력하면 매일 오전 9시에 와탭 Agent의 버젼을 체크하고 신버젼 발견시 다운로드 합니다. 관리자패스워드란에는 서버의 실재 패스워드를 입력합니다.
<pre>
schtasks /Create /TN WhatapAgentUpdate /TR "cscript \"c:\program files\whatap_agent_update.vbs\"" /SC DAILY /st 09:00:00 /RU Administrator /RP 관리자패스워드 /F /V1
</pre>

#### 5) 서버에서 Windows+R 을 눌러 Taskschd.msc 를 입력해 scheduler를 실행합니다. 아래 사진과 같이 업데이트 스케줄러가 등록된 것을 알 수 있습니다.
![Windows Scheduler](/assets/images/hsnam/01/2015-12-06-TaskScheduler.PNG)

### 마치면서 
리눅스와 달리 윈도우에는 apt-get이나 yum이 없어 설치파일이 반드시 필요합니다. SystemCenter나 chef를 이용하여 업데이트를 할 수 없는 환경에서 간단한 vbscript로 자동 업데이트를 할 수 있도록 구성하여 보았습니다. 서버관리시 불편함을 조금이나마 덜 수 있으면 좋겠습니다.

