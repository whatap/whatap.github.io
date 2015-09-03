---
layout: post
title:  "Windows 서버 등록하는 법"
date:   2015-09-03 17:07:00
author: 김광명
email:  kmkim@whatap.io
---

안녕하세요 Whatp 에 윈도우 서버를 추가하는 방법에 대해서 알아 보도록 하겠습니다.
윈도우에서 모티터링을 추가하려면 Windows 용 Agent 를 설치 해야 합니다.
WhaTap Agent는 모니터링 하고자 대상 서버의 데이터를 수집하는 Agent로 서버의 상태에 대해서만 수집을 하고 다른 정보는 수집하지 않으니 안심하셔도 됩니다.

그럼 Windows 에서 Agent 설치 하고, 서버를 등록하는 방법에 대해서 설명 하도록 하겠습니다.


## 1. WhaTap 홈페이지 접속 파일 다운로드
먼저 WhaTap 홈페이지(http://whatap.io/)에 접속을 합니다.

![alt text](/assets/images/AddServeronWindows_01.jpg)

처음 로그인을 하시면 아래와 같이 서버 설치 화면이 나타납니다.

![alt text](/assets/images/AddServeronWindows_02.jpg)

만약 설치된 서버가 존재할 경우 서버 목록 화면이 나타납니다.

![alt text](/assets/images/AddServeronWindows_03.jpg "Image1")

이때에는 추가 버튼을 누르고 나면,

![alt text](/assets/images/AddServeronWindows_04.jpg "Image1")

다시 서버 설치 화면이 나타날 것입니다.

![alt text](/assets/images/AddServeronWindows_02.jpg "Image1")

### 다운로드 파일
  WhaTap Agent 파일을 다운로드 받습니다. 
 Whatap.exe 파일과 Whatap.zip 링크를 누르면 다운로드가 진행이 됩니다.

![alt text](/assets/images/AddServeronWindows_05.jpg)

다운로드한 파일은 아래와 같습니다.

![alt text](/assets/images/AddServeronWindows_06.jpg)

## 2.Agent 실행 및 설치
이제 Agent를 설치할 차례입니다. Agent 파일 설치는 간단합니다. 단지 더블클릭만 설치관리자가 나와 설치를 진행하게 됩니다.

![alt text](/assets/images/AddServeronWindows_07.jpg)

### 라이선스 입력
라이선스 파일을 물어봅니다.

![alt text](/assets/images/AddServeronWindows_08.jpg)

당황하지 마시고 Whatap 서버 설치 화면의 "2. 설치 파일을 실행하십시오." 화면에 라이선스 키가 표시되어있습니다.
빨간 부분을 복사를 하시거나 Copy 버튼을 눌러 복사를 합니다.

![alt text](/assets/images/AddServeronWindows_09.jpg)

아래와 같은 문제가 발생하면 라이선스 키가 잘못되었거나 네트워크 문제로 Whatap 서버에 접속이 되지 않아 설치가 진행되지 않습니다.

![alt text](/assets/images/AddServeronWindows_10.jpg)

이 경우 설치된 서버에 서버에 외부 네트워크(Out-Bound) 접근을 제한하는 방화벽을 제거 해야 합니다. 
Whatap 에서 사용하는 포트는 80 와 10051 포트를 사용합니다. 
아래 포트를 방화벽에서 Out bount 설정후 설치를 다시 진행하시면 됩니다.

### 설치경로 설정 
정상적 인 경우 설치할 위치를 물어보게 됩니다. 기본적인 설치 경로는 C:\Program Files (x86)\Whatap 에 설치가 됩니다.
마음에 들지 않으시면 다른 폴더에 설치하셔도 상관은 없습니다. 
* 설치 하는데 HDD 의 여유 공간은 1.5Mb 정도의 공간이 필요합니다.

![alt text](/assets/images/AddServeronWindows_11.jpg)

 그러면 설치 준비가 끝나고 마지막으로 Install 버튼을 누르면 설치가 진행이 됩니다.

![alt text](/assets/images/AddServeronWindows_12.jpg)

### Agent 설치완료 
마지막으로 Finish 버튼을 누르면 설치가 완료됩니다.

![alt text](/assets/images/AddServeronWindows_13.jpg)

## 3. 서버 정보수집
설치가 완료되고 나서 서버에 등록이 진행되는지 확인은 WhaTap 홈페이지 에 적속하시면 서버목록을 보실수 있습니다.
Loading Server 라는 메시지와 함께 서버에 등록 절차 및 정보수집 절차가 진행되어집니다. 
* 10분 이내의 시간이 걸립니다.

![alt text](/assets/images/AddServeronWindows_14.jpg)

## 4. 서버등록완료
등록절차가 완료되면 서버 목록항목에서 서버의 상태를 모니터링 할 수 있습니다.

![alt text](/assets/images/AddServeronWindows_15.jpg)