---
layout: post
title:  "Selenium 을 이용한 Web 어플리케이션 테스트 자동화(3) - TestNG를 이용한 데이터 주도(Data-driven) 테스트 맛보기"
date:   2015-12-11 06:00:00
author: 김광명
profile: kmkim.png
---
"데이터 주도 테스트" 많이들 들어 보셨을 것입니다. 말 그대로 데이터 기반의 테스트를 진행한다는 의미입니다. 
Selenium 을 이용해서 Regression Test 와 기능 테스트등을 수행할 때 같은 기능들을 여러번 수행하거나 같은 스텝의 기능에 입력값 만 바뀌는 시나리오들이 있을 수 있습니다. 
다량의 데이터를 기반으로 데이터만 바꿔서 테스트 하는 데이터 주도 테스트 접근법을 사용할때 CSV, Excel, DataBase 등 을 Data Source로입력 받아 테스트를 수행할 수있습니다.

아쉽게도 셀레늄에서는 데이터 주도 테스트를 할 수 있는 API를 제공하지 않고 있습니다.
하지만 Java 기반이기에 JAVA 에서 사용하는 기법들을 적용해서 데이터 주도 테스트를 수행할 수 있습니다.

그럼 TestNG를 이용해서 테스트를 만들어 보겠습니다.

### Test 시나리오 ###
Whatap 에는 조직에 사용자를 초대 하는 기능이 있습니다.
조직에 초대된 사용자는 Whatap 의 기능을 이용할 수 있습니다. Admin 사용자와 User 사용자로 사용자를 초대하는 기능이 있습니다.
1명의 사용자를 초대하는 시나리오를 보면 아래와 같습니다.

<pre>
1. 사용자 초대하기 버튼을 누른다.
2. 사용자 Email 정보를  입력 한다. (qa1@whatap.io)
3. Role 을 선택한다.(Admin)
4. 초대 버튼을 누른다.
</pre>

### 코드 작성하기 ###
셀레늄으로 만들면 아래와 같습니다.

{% highlight java %}
//1. 사용자 초대 하기 버튼 클릭
driver.findElement(By.id("memberInvite")).click();
//2 사용자 Email 정보를  입력 한다.(qa1@whatap.io)
driver.findElement(By.name("email")).clear();
driver.findElement(By.name("email")).sendKeys(qa1@whatap.io);
//3. Role 을 선택한다.(Admin)
new Select(driver.findElement(By.id("accountType"))).selectByVisibleText("Admin");
driver.findElement(By.xpath("//option[@value='admin']")).click();
//4. 초대 버튼을 누른다.
driver.findElement(By.id("invite")).click();
Thread.sleep(1000);
{% endhighlight %}

간단한 Step 으로 이루어진 아주 간단한 시나리오입니다.

하지만 100 명의 사용자를 추가하려면? 난감해집니다.

qa2@whatap.io/Admin
qa3@whatap.io/User
...
qa99@whatap.io/Admin

### DataProvider Annotation 이용하기 ###
이때 TestNG 를 이용해서 데이터 주도의 테스트를 하면 됩니다.
이전 블로그에서 간단하게 TestNG를 이용해서 Selenium 을 이용해 보았습니다.
JAVA 를 이용하시는 분들은 대부분 TestNG를 알고 있을것입니다. Junit 과 같은 test automationframework 중 하나입니다. 
TestNG 에서 제공하는 DataProvider Annotation 을 이용해서 저희는 데이터 주도(Data-driven) 테스트를 할수 있습니다.

{% highlight java %}
@DataProvider
  public Object[][] userData() {
    return new Object[][] {new Object[] {"qa1@whatap.io", "Admin"},
        new Object[] {"qa2@whatap.io","Admin"},
        new Object[] {"qa3@whatap.io","Admin"},
        new Object[] {"qa4@whatap.io","Admin"},
        new Object[] {"qa5@whatap.io","User"},
        new Object[] {"qa6@whatap.io","User"},
    };
  }
{% endhighlight %}

메소드가 @DataProvider로 시작하면 데이터를 테스트 케이스로 전달하는 데이터 메소드가됩니다. 위의 Data 배열에서 한행씩 테스트 메소드로 절달되게 됩니다.
이전달 받은 데이터를 이용해서 사용하려면 @Test(dataProvider = "userData") Annotation 을 이용하면 됩니다. 

{% highlight java %}
 @Test(dataProvider = "userData")
  public void test02_inviteUser(String email, String accountType) throws Exception {
    try {
      //1. 사용자 초대 하기 버튼 클릭
      driver.findElement(By.id("memberInvite")).click();
      //2 사용자 Email 정보를  입력 한다.
      driver.findElement(By.name("email")).clear();
      driver.findElement(By.name("email")).sendKeys(email);
      //3. Role 을 선택한다.
      new Select(driver.findElement(By.id("accountType"))).selectByVisibleText(accountType);
      driver.findElement(By.xpath("//option[@value='" + accountType.toLowerCase() + "']")).click();
      //4. 초대 버튼을 누른다.
      driver.findElement(By.id("invite")).click();
      Thread.sleep(1000);
    } catch (Error e) {
      verificationErrors.append(e.toString());
    }
  }
  {% endhighlight %}
  
@DataProvider에 6개의 Object가 총 6번의 테스트를 수행하게 됩니다.
  
이상으로 TestNG를 이용해서 Data-Driven 테스트를 수행해 보았습니다.

조금더 쉬운 이해를위해 전체 Code 를 참고해보시기 바랍니다.

테스트 수행 순서는 아래와 같습니다. 

1. 로그인 하기

2. 사용자 초대 하기
   
감사합니다.
 
{% highlight java %}
import java.util.concurrent.TimeUnit;
import org.openqa.selenium.*;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.Select;
import org.testng.Assert;
import org.testng.annotations.AfterTest;
import org.testng.annotations.BeforeTest;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;

public class InviteUser {
  private WebDriver driver;
  private String baseUrl;
  private StringBuffer verificationErrors = new StringBuffer();

  @BeforeTest
  public void setUp() throws Exception {
    driver = new FirefoxDriver();
    baseUrl = "http://console.whatap.io/";
    driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);
  }

  @DataProvider
  public Object[][] userData() {
    return new Object[][] {new Object[] {"qa1@whatap.io", "Admin"},
        new Object[] {"qa2@whatap.io", "Admin"}, new Object[] {"qa3@whatap.io", "Admin"},
        new Object[] {"qa4@whatap.io", "Admin"}, new Object[] {"qa5@whatap.io", "User"},
        new Object[] {"qa6@whatap.io", "User"},};
  }

  @Test
  public void test01_logIn() throws Exception {
    login();
    driver.findElement(By.xpath("//li[@id='settingMenu']/a")).click();
    driver.findElement(By.xpath("//li[@id='User']/a")).click();

    driver.findElement(By.xpath("//a[contains(@href, '/Setting/AccountOrg')]")).click();
  }

  @Test(dataProvider = "userData")
  public void test02_inviteUser(String email, String accountType) throws Exception {
    try {
      // 사용자 초대 하기 버튼 클릭
      driver.findElement(By.id("memberInvite")).click();
      // Email 입력
      driver.findElement(By.name("email")).clear();
      driver.findElement(By.name("email")).sendKeys(email);
      // 사용자권한 선택
      new Select(driver.findElement(By.id("accountType"))).selectByVisibleText(accountType);
      driver.findElement(By.xpath("//option[@value='" + accountType.toLowerCase() + "']")).click();
      // 초대버튼 클릭
      driver.findElement(By.id("invite")).click();
      Thread.sleep(1000);
    } catch (Error e) {
      verificationErrors.append(e.toString());
    }
  }

  private void login() throws InterruptedException {
    driver.get(baseUrl + "/Account/Login");
    driver.findElement(By.id("loginEmail")).clear();
    driver.findElement(By.id("loginEmail")).sendKeys("qa@whatap.io");
    driver.findElement(By.id("loginPassword")).clear();
    driver.findElement(By.id("loginPassword")).sendKeys("dhkxoqqa1");
    driver.findElement(By.id("login")).click();

    for (int second = 0;; second++) {
      if (second >= 60)
        Assert.fail("timeout");
      try {
        if ("서버 추가".equals(driver.findElement(By.cssSelector("header.page-header > h2")).getText()))
          break;
      } catch (Exception e) {
      }
      Thread.sleep(1000);
    }
  }

  @AfterTest
  public void tearDown() throws Exception {
    driver.quit();
    String verificationErrorString = verificationErrors.toString();
    if (!"".equals(verificationErrorString)) {
      Assert.fail(verificationErrorString);
    }
  }
}
{% endhighlight %}
  