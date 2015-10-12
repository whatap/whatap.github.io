---
layout: post
title:  "Selenium 을 이용한 Web 어플리케이션 테스트 자동화(1) - What is Selenium?  "
date:   2015-10-02 17:24:00
author: 김광명
profile: kmkim.png
---

오늘날 웹 기반 응용 프로그램으로 작성된 소프트웨어 응용프로그램은 Chrome, Internet Explorer, Firefox 와 같은 인터넷 브라우저에서 실행됩니다.
그리고 조직들은 점차 애자일 방법론의 일부 양식 혹은 전부를 도입하여 운영 하는 추세입니다. 

애자일 방법론에서는 짧은 개발주기와, 빠른 피드백을 제공하기 위해서 테스트 자동화를 권장하고 있습니다. 

테스트 자동화는 소프트웨어 팀의 테스트 프로세스의 장기 효율을 개선하기 위해 특정한 이점들을 가질수 있도록 도와줍니다.
 
 - 잦은 희귀테스트
 - 신속한 피드백
 - 테스트 케이스 실행의 무제한 반복과
 - 테스트 케이스의 체계적인 문서화
 - 수동테스트에서 놓친 결함 찾기
 
물론 여기에서 말하는 테스트 자동화는 UI tier, Middle tier, Unit tier 모두를 권장합니다. 

테스트 자동화는 소프트웨어 팀의 테스트 프로세스의 장기 효율을 개선하기 위한 특정한 이점을 갖습니다.

지금 제가 말하고 자 하는 것은 개발 단에서 수행하는 Middle tier(FitNesse, Robot Framework, Cucumber), Unit tier(Junit, Nunit, 등의  xUnit family)가 아닌 Ui Tier 단에서 수행되는 자동화 테스트를 말하고자 합니다.

전통적으로 UI Tier 에서 자동화 하면 Mercury/HP의 상용툴인(Quick Test Professional or QTP) 가 리드 해왔습니다.
하지만 상용툴은 적은 규모의 조직에서는 예산, 규모,교육 등의 문제로 도입하기가 어렵습니다. 

오픈 소스 솔루션인 셀레늄(Selenium)을 이용해 보는것이 어떨까요? 
셀레늄(Selenium)은 아마도 가장 널리 사용도는 오픈 소스 솔루션 일것입니다.

## What is Selenium ?

셀레늄이 뭐지?

위키피디아의 내용을 검색해보면 아래와 같은 내용이 나옵니다.

<pre>
셀레늄(←영어: selenium 실리니엄[*]) 또는 셀렌(←독일어: Selen 젤렌[*])은 화학 원소로 기호는 Se(←라틴어: selenium 셀레니움[*])이고 원자 번호는 34이다. 
독성이 있는 비금속 원소로, 화학적 성질은 황과 텔루륨과 가깝다. 
여러 가지 형태로 존재하나, 회색의 금속성 상태가 가장 안정적이다. 
자연 상태에서는 순수하게 발견되는 일이 드물다. 
주로 구리 광석을 제련하는 과정에서 부산물로 생성되며, 황철석 등의 황화물 광석에서도 산출된다. 
반도체의 성질을 지니고 어두울 때보다 밝을 때의 전기 저항이 작아 광저항을 만드는 데 사용되며, 유리 제조, 염색, 광전지 등에도 사용된다. 생물체 내에서 양이 많으면 독성을 나타내기도 하지만 미량이면 모든 동물 세포와 많은 수의 식물 세포에 필수적인 역할을 한다.
</pre>

하지만 이것은 단어의 의미적인 것이고 여기서 말하고자 하는것은 Selenium(Software) 를 말하 고자 합니다.

다시 위키피디아의 검색의 힘을 빌리자면(https://en.wikipedia.org/wiki/Selenium_(software))

- Web 어플리케이션을 위한 소프트웨어 프레임워크이고
- Record/Playback tool을 지원한다.(Selenium IDE)
- Java, C#, Groovy, Perl , PHP, Python , Ruby 등을 지원한다.
- 대부분의 브라우저를 지원한다.
- Liunx, Windows, 심지어 Macintosh 플랫폼을 지원한다.
- 그리고 Open-Sorce(Apache 2.0) 이다.

## 셀레늄(Selenium)의 Compnonents
그러면 셀레늄은 어떤것들로 구성되어있는지 알아보도록 하겠습니다.

###Selenium IDE
Selenium IDE 는 FireFox 확장 플러그인(Add-on) 으로 제공되는 GUI 도구입니다.

Test Case를 쉽게 작성할 수 있도록  도와주고, Record/Playback을 지원하며, Java, pery, Python 등으로 Exporting 을 할 수 있게 하는 기능들을 제공합니다.

참고 : http://seleniumhq.org/projects/ide/

![selenium-ide.gif](/assets/images/kmkim/2015-10-02/selenium-ide.gif)

###Remote Control(RC)- Selenium 1
Remote Control(RC) 는 HTTP 를 이용해 명령(Command)을 받는 Java 로 구성된 작성된 테스트 도구입니다.

Unit Test를 지원하고,  PHP, Python, Ruby, .NET, Perl and Java 언어를 지원합니다.
하지만 Java/PHP Test Case 수행시 하나의 인스턴스에서만 수행되어야 하는제약이 있는 버전으로 Selenium 2인  WebDriver 가 나오기 까지는 Main 이였으나, 
현재는 Webdriver 에 편입되고 유지보수(Mantaince) 정도만 되고 있는 듯 한 도구입니다. 

![selenium1_Architecture_Diagram_Simple.png](/assets/images/kmkim/2015-10-02/selenium1_Architecture_Diagram_Simple.png)
아키텍처에서 보다시피 Remote Control(RC) 를 통해 여러 브라우져의 테스트를 수행 할 수 있습니다.

기타 자세한 사항은 http://www.seleniumhq.org/projects/remote-control/ 에서 확인 하실수 있습니다.


###WebDriver- Selenium 2
WebDriver는 로컬 또는 원격 컴퓨터의 브라우져를 구동할 수있게 하는 테스트 도구입니다.

Remote Control(RC)의 다음버전으로 현재의 Selenium을 말합니다.

Webdriver 는 Remote Control(RC) 의 제약사항인 하나의 인스턴스에서만 구동되는 제약사항을 극복하고 Selenium 2로 거듭나게 되었습니다. 

- RC(Selenium 1) + Webdriver = Selenium 2 

WebDriver 는 ID/Class/XPath/CSS 등을 이용해 Element를  지정하여 테스트 가능 가능합니다.

그리고 하나의 테스트를 작성한후 아래의 클래스들을 이용해서 드라이버만 변경하여 멀티 브라우저 심지어 Android 까지 테스트가 가능하게 해줍니다.

테스트 가능한 환경들은 아래와 같습니다.

- Google Chrome

- Internet Explorer 6, 7, 8, 9, 10 - 32 and 64-bit where applicable

- Firefox: latest ESR, previous ESR, current release, one previous release

- Safari

- Opera

- HtmlUnit

- phantomjs

- Android (with Selendroid or appium)

- iOS (with ios-driver or appium)

그리고 RC 에서 테스트 하던 것들도 테스트 가 가능합니다.

기타 자세한 사항은 http://docs.seleniumhq.org/projects/webdriver/ 에서 확인 하실수 있습니다.


###Selenium Grid
Selenium Grid 는 WebDriver 를 이용해 여러 브라우저 또는 운영 체제를 에서 동시에 테스트를 실행 해주도록 해주는 서버 입니다.

기타 자세한 사항은 https://github.com/SeleniumHQ/selenium/wiki/Grid2 에서 확인 하실수 있습니다.

##Selenium IDE 사용해보기

그럼 간단히 Selenium IDE 를 이용해서 테스트 케이스를 작성하고 테스트를 수행해보겠습니다.

### Firefox 와 Selenium IDE 설치
우선 Firefox 를 설치합니다.(https://www.mozilla.org/ko/firefox/new/)
설치후 Selenium IDE 를 설치합니다. 다운로드는 http://www.seleniumhq.org/download/ 에서 하실수 있습니다.

![selenium-ide-install.png](/assets/images/kmkim/2015-10-02/selenium-ide-install.png)

설치후 Firex를 실행하면 우측상단에 Seleniu IDE 버튼을 보실수 있습니다.
![selenium-ide-install.png](/assets/images/kmkim/2015-10-02/selenium-ide-install-in-firefox.png)

### 테스트 케이스 작성
그러면 간탄한 테스트 케이스르 작성해보도록 하겠습니다.

테스트 케이스는 아래와 같습니다.

1. Firefox 실행
2. Whatap.io 홈페이지 접속
3. 무료 회원가입 버튼 클릭
4. 회사이름, 이메일번호, 비밀번호, 비밀번호 확인 Text Field 입력
5. 이용약관동의, 개인정보보호 정책 동의
6. 계정생성하기 버튼 클릭

이제 Selenium IDE 를 이용해 테스트를 작성해보겠습니다.

1. Firefox 실행.
2. Whatap.io 홈페이지 접속

이제 Firefox 에서 Selenium IDE 버튼을 클릭합 하면 Selenium IDE 가 실행되고 작업한 내용이 녹화 되고 있는것을 보실수 있습니다.
![selenium-ide-write-testscript-01.jpg](/assets/images/kmkim/2015-10-02/selenium-ide-write-testscript-01.jpg)

3. 무료 회원가입 버튼 클릭
4. 회사이름, 이메일번호, 비밀번호, 비밀번호 확인 Text Field 입력
5. 이용약관동의, 개인정보보호 정책 동의
6. 계정생성하기 버튼 클릭

이제 Selenium IDE 에서 확인해봅니다.
![selenium-ide-write-testscript-02.jpg](/assets/images/kmkim/2015-10-02/selenium-ide-write-testscript-02.jpg)
단지 클릭만으로 모튼 테스트 케이스가 만들어 졌습니다. 

그럼 이제 테스트 케이스를 모두 만들었으니 레코딩을 중지합니다.
아래 버튼을 누르면 레코딩이 중지됩니다.
![selenium-ide-write-testscript-03.jpg](/assets/images/kmkim/2015-10-02/selenium-ide-write-testscript-03.jpg)

클릭만으로 모든 테스트가 만들어 졌습니다. 놀랍지 않나요?

### 테스트 수행 하기
이제 테스트 를 수행해 보겠습니다.

동일한 이메일로는 테스트 생성이 어려 우니 회원 탈퇴를 한후에 다시 테스트를 수행해보겠습니다.

회원탈퇴는 나의 설정> 사용자 > 계정탈퇴 에 있습니다.
탈퇴 사유를 입력후 계정탈퇴를 하면 정상적으로 탈퇴가 됩니다. 

그러면 Selenium IDE 에서 작성한 테스트 케이스를 수행해 보겠습니다.
수행은 아래 의 녹색 Play 버튼을 누르면 됩니다.
![selenium-ide-write-testscript-04.jpg](/assets/images/kmkim/2015-10-02/selenium-ide-write-testscript-04.jpg)

정상적으로 테스트가 완료 되면 모든 라인이 녹색으로 변경된것을 확인 하실수 있습니다. 
![selenium-ide-write-testscript-05.jpg](/assets/images/kmkim/2015-10-02/selenium-ide-write-testscript-05.jpg)

간단히 테스트가 완료 되었습니다. 너무 쉽지 않나요?


지금까지 Selenium 이 무엇인가와 Selenium IDE 를 한번 사용해 보았습니다.

다음 시간에는 Selenium IDE 를 조금더 사용해보고, WebDriver 를 이용해서 테스트 케이스를 작성하는 방법에대해서 알아 보도록 하겠습니다.

감사합니다.
