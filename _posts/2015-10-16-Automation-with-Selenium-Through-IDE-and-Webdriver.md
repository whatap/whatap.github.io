---
layout: post
title:  "Selenium 을 이용한 Web 어플리케이션 테스트 자동화(2) - IDE 와 Webdriver기능 맛보기"
date:   2015-10-16 10:00:00
author: 김광명
profile: kmkim.png
---

앞서 저희는 Selenium 이 무엇인가와 Selenium IDE 를 한번 사용해 보았습니다.
이번 시간에는 Selenium IDE 를 조금더 사용해보고, WebDriver 를 이용해서 테스트 케이스를 작성하는 방법에대해서 알아 보도록 하겠습니다.

그럼 Selenium IDE의 제어 버튼에 대해서 조금더 알아보고 본격적으로 JAVA 를 이용해서 Webdriver 를 사용하는 방법에 대해서 알아보겠습니다.

###제어버튼
많이 사용하는 제어 버튼에 대해서 설명해드리겠습니다.

![Selenium-IDE-UI-ControlMenu.jpg](/assets/images/kmkim/2015-10-16/Selenium-IDE-UI-ControlMenu.jpg)

1. Speed Control : 테스트 케이스의 속도 재생 속도 조절
2. Run All : Test Suite를 재생
3. Run : 1개의 Test Case 를 재생Test Case를 재생
4. Pause/Resume : 테스트 케이스의 정지와 리스타트
5. Step : 클릭 시 한번에 한 라인씩 수행
6. Record : 유저 브라우저의 액션을 레코딩 시작/정지 버튼

###Test Case 저장하기
Test Case를  작성한후에 저장하기 를 통해서 Test Case 를 저장가능합니다.

저장하는 방법은 파일>Save Test Case 로 저장하면 Html 파일 형식으로 저장됩니다.

이전 편에서 사용한  계정생성한 Test Case 의 경우 내용은 아래와 같습니다.
 
{% highlight Html %}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
<head profile="http://selenium-ide.openqa.org/profiles/test-case">
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<link rel="selenium.base" href="https://www.whatap.io/" />
<title>계정생성하기</title>
</head>
<body>
<table cellpadding="1" cellspacing="1" border="1">
<thead>
<tr><td rowspan="1" colspan="3">계정생성하기</td></tr>
</thead><tbody>
<tr>
	<td>open</td>
	<td>/ko.html</td>
	<td></td>
</tr>
<tr>
	<td>clickAndWait</td>
	<td>id=signUp</td>
	<td></td>
</tr>
<tr>
	<td>type</td>
	<td>name=companyName</td>
	<td>와탭</td>
</tr>
<tr>
	<td>type</td>
	<td>id=email</td>
	<td>qa@whatap.io</td>
</tr>
<tr>
	<td>type</td>
	<td>id=password</td>
	<td>dhkxoqqa1</td>
</tr>
<tr>
	<td>type</td>
	<td>id=rePassword</td>
	<td>dhkxoqqa1</td>
</tr>
<tr>
	<td>click</td>
	<td>id=agreementToTerms</td>
	<td></td>
</tr>
<tr>
	<td>click</td>
	<td>id=agreementPrivacyPolicy</td>
	<td></td>
</tr>
<tr>
	<td>click</td>
	<td>id=create</td>
	<td></td>
</tr>

</tbody></table>
</body>
</html>
{% endhighlight %}

실제 파일을 실행해보면 테이블 형식으로 표현되어 Selenium IDE에서 보는 내용을 그대로 보실수 있습니다.
![Selenium-IDE-SaveTestcase.jpg](/assets/images/kmkim/2015-10-16/Selenium-IDE-SaveTestcase.jpg)

##WebDriver 사용하기

그러면 WebDriver 를 사용하기 위한 셋팅을 준비해보겠습니다.

Java를 이용해서 WebDriver 를 사용하도록 하겠습니다. 
Eclipse 를 이용해서 Maven 프로잭트 파일을 만들어 필요한 모듈들을 다운로드 받는 형식으로 WebDriver 을 이용할 수있습니다.
먼저 필요한 재료는 아래와 같습니다.

1. Eclipse(Maven+TestNG plugin)
2. JAVA JDK 설치
3. Webdriver

###Eclipse 설치 및 JDK 설치

####Eclipse
Eclipse 는 아래 경로에서 다운받을수 있습니다.

<pre>
https://www.eclipse.org/downloads/
</pre>
저같은경우는 Eclipse IDE for Java EE Developers 를 선호해서 아래 파일을 받아서 설치해서 사용합니다.
![Eclipse_JaveEE.jpg](/assets/images/kmkim/2015-10-16/Eclipse_JaveEE.jpg)
####Java
Java 의 경우는 아래에서 다운받을수 있습니다.
<pre>
http://www.oracle.com/technetwork/java/javase/downloads/index.html
</pre>

JAVA HOME Path 설정을 해줍니다.
윈도우 의 경우는 아래와 같이 설정하시면 됩니다.
![Java-Home.jpg](/assets/images/kmkim/2015-10-16/Java-Home.jpg)

####TestNG Plugin 
WebDriver를 실행시 TestNG를 통해서 실행을위해 TestNG Plugin 을 설치해줍니다.(TestNG 가 싫으시면 Junit이나 다른것들을 사용하셔도됩니다.)

플러그인은 Help>Eclipse Marketplace 에서 다운받으실수 있습니다.

![Eclipse-TestNG.jpg](/assets/images/kmkim/2015-10-16/Eclipse-TestNG.jpg)

####Maven Project 생성 및 설정
일관된 디렉토리 구조와 빌드 프로세스를 유지하고, Dependency Libaray 관리와 다양한 플러그인등을쉽게 이용하기 위해 Maven 프로젝트를 생성해 Webdriver 를 이용해보도록 하겠습니다.

그럼 새로운 Maven 프로젝트를 생성합니다. 아래 그림과 같은 흐름으로 진행하시면 됩니다.

![Eclipse-newmavenproject.jpg](/assets/images/kmkim/2015-10-16/Eclipse-newmavenproject.jpg)

####pom.xml dependency 설정

WebDriver jar 및 기타 필요한 파일들을 이용하기 위해 메이븐 pom.xml에 dependency를 추가해 라이브러리를 다운받아 습니다.
필요한 dependency 설정을 합니다.

- selenium-java
- selenium-remote-driver
- selenium-chrome, firefox,ie driver
- TestNG
- slf4j

관련 설정은 아래와 같습니다. 설정을 완료한 pom.xml 파일입니다.
{% highlight xml %}
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>io.whatap</groupId>
	<artifactId>Selenium</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<dependencies>
		<dependency>
			<groupId>org.seleniumhq.selenium</groupId>
			<artifactId>selenium-java</artifactId>
			<version>2.48.2</version>
		</dependency>
		<dependency>
			<groupId>org.seleniumhq.selenium</groupId>
			<artifactId>selenium-remote-driver</artifactId>
			<version>2.48.2</version>
		</dependency>
		<dependency>
			<groupId>org.seleniumhq.selenium</groupId>
			<artifactId>selenium-firefox-driver</artifactId>
			<version>2.48.2</version>
		</dependency>
		<dependency>
			<groupId>org.seleniumhq.selenium</groupId>
			<artifactId>selenium-chrome-driver</artifactId>
			<version>2.48.2</version>
		</dependency>
		<dependency>
			<groupId>org.seleniumhq.selenium</groupId>
			<artifactId>selenium-ie-driver</artifactId>
			<version>2.48.2</version>
		</dependency>
		<!-- TestNG -->
		<dependency>
			<groupId>org.testng</groupId>
			<artifactId>testng</artifactId>
			<version>6.9.6</version>
			<scope>test</scope>
		</dependency>
		<!-- loger slf4j -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>1.7.7</version>
		</dependency>
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>1.1.2</version>
			<exclusions>
				<exclusion>
					<groupId>org.slf4j</groupId>
					<artifactId>slf4j-api</artifactId>
				</exclusion>
			</exclusions>
			<scope>runtime</scope>
		</dependency>
	</dependencies>
</project>
{% endhighlight %}
###WebDriver 맛보기
자이제 WebDriver를 사용할 수 있을것 같습니다.
먼저 src/test/java 에 새로운 class 를 생성해봅니다. Class name은 CreateUser로 하겠습니다.

![new-class-for-Createuser.jpg](/assets/images/kmkim/2015-10-16/new-class-for-Createuser.jpg)

우리는 먼저 Selenium IDE 를 이용해서 계정생성하기 Test Case 를 생성했습니다.

Selenium IDE는 Export 기능을 제공합니다. 
그래서 저는 간단한 테스트를 만드는 것은 이 기능을 이용해서 사용하기 도합니다.

여러분들도 한번 시도해보시기 바랍니다. 그럼 어떻게 하는지 한번 해보겠습니다. 

먼저 Junit 으로 Export 한후에 TestNG 형식으로 변경하도록 하겠습니다.

Export 기능은 '파일 > Export TestCase' 에서 이용할 수 있습니다.  

![Export-to-junit-from-IDE.jpg](/assets/images/kmkim/2015-10-16/Export-to-junit-from-IDE.jpg)

Export 는 CreateUser.java 로 하겠습니다. 

자동으로 CreateUser.java 파일이 생성됩니다. 

Junit 으로 되어있는 형식으로 Export 됩니다.
{% highlight java %}
package com.example.tests;

import java.util.regex.Pattern;
import java.util.concurrent.TimeUnit;
import org.junit.*;
import static org.junit.Assert.*;
import static org.hamcrest.CoreMatchers.*;
import org.openqa.selenium.*;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.Select;

public class CreateUser {
  private WebDriver driver;
  private String baseUrl;
  private boolean acceptNextAlert = true;
  private StringBuffer verificationErrors = new StringBuffer();

  @Before
  public void setUp() throws Exception {
    driver = new FirefoxDriver();
    baseUrl = "https://www.whatap.io/";
    driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);
  }

  @Test
  public void testCreateUser() throws Exception {
    driver.get(baseUrl + "/ko.html");
    driver.findElement(By.id("signUp")).click();
    driver.findElement(By.name("companyName")).clear();
    driver.findElement(By.name("companyName")).sendKeys("와탭");
    driver.findElement(By.id("email")).clear();
    driver.findElement(By.id("email")).sendKeys("qa@whatap.io");
    driver.findElement(By.id("password")).clear();
    driver.findElement(By.id("password")).sendKeys("dhkxoqqa1");
    driver.findElement(By.id("rePassword")).clear();
    driver.findElement(By.id("rePassword")).sendKeys("dhkxoqqa1");
    driver.findElement(By.id("agreementToTerms")).click();
    driver.findElement(By.id("agreementPrivacyPolicy")).click();
    driver.findElement(By.id("create")).click();
  }

  @After
  public void tearDown() throws Exception {
    driver.quit();
    String verificationErrorString = verificationErrors.toString();
    if (!"".equals(verificationErrorString)) {
      fail(verificationErrorString);
    }
  }

  private boolean isElementPresent(By by) {
    try {
      driver.findElement(by);
      return true;
    } catch (NoSuchElementException e) {
      return false;
    }
  }

  private boolean isAlertPresent() {
    try {
      driver.switchTo().alert();
      return true;
    } catch (NoAlertPresentException e) {
      return false;
    }
  }

  private String closeAlertAndGetItsText() {
    try {
      Alert alert = driver.switchTo().alert();
      String alertText = alert.getText();
      if (acceptNextAlert) {
        alert.accept();
      } else {
        alert.dismiss();
      }
      return alertText;
    } finally {
      acceptNextAlert = true;
    }
  }
}
{% endhighlight %}

저는 TestNG를 이용하기로 했으니 TestNG에 맞게 약간 변경합니다.

- @Before --> @BeforeMethod 

- @Aftger -->@AfterMethod 

- fail --> Assert.Faii 

기타 불필효한 Code를 제거 하면 최종적으로 아래와같은 Code 가 나옵니다.

{% highlight java %}
import java.util.concurrent.TimeUnit;
import org.openqa.selenium.*;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.testng.Assert;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;

public class CreateUser {
	private WebDriver driver;
	private String baseUrl;
	private StringBuffer verificationErrors = new StringBuffer();

	@BeforeMethod
	public void setUp() throws Exception {
		driver = new FirefoxDriver();
		baseUrl = "https://www.whatap.io/";
		driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);
	}

	@Test
	public void testCreateUser() throws Exception {
		driver.get(baseUrl + "/ko.html");
		driver.findElement(By.id("signUp")).click();
		driver.findElement(By.name("companyName")).clear();
		driver.findElement(By.name("companyName")).sendKeys("와탭");
		driver.findElement(By.id("email")).clear();
		driver.findElement(By.id("email")).sendKeys("qa@whatap.io");
		driver.findElement(By.id("password")).clear();
		driver.findElement(By.id("password")).sendKeys("dhkxoqqa1");
		driver.findElement(By.id("rePassword")).clear();
		driver.findElement(By.id("rePassword")).sendKeys("dhkxoqqa1");
		driver.findElement(By.id("agreementToTerms")).click();
		driver.findElement(By.id("agreementPrivacyPolicy")).click();
		driver.findElement(By.id("create")).click();
	}

	@AfterMethod
	public void tearDown() throws Exception {
		driver.quit();
		String verificationErrorString = verificationErrors.toString();
		if (!"".equals(verificationErrorString)) {
			Assert.fail(verificationErrorString);
		}
	}
}
{% endhighlight %}

이제 정상적으로 수행되는지 Test 해보겠습니다.
Eclipse 에서 TestNG를 이용해서 실행하면 계정생성하는 스크립트가 실행됩니다.
![run-TestNG.jpg](/assets/images/kmkim/2015-10-16/run-TestNG.jpg)

어떤가요? 기존에 만든 Test Case 를 재사용가능하고 간단히 테스트도 수행되는것을 보셨을것입니다.

이번에 Selenium IDE 의 일부 기능 그리고 Selenium IDE에서 생성한 Test Case를 이용해서 WebDriver Test Code 를 생성해서 Test하는것을 해보았습니다.

다음번에는 조금더 WebDriver의 기능들을 이용해보도록 하겠습니다.

감사합니다.
