---
layout: post
title:  "Jekyll 윈도우에 설치해서 사용하기"
date:   2015-09-11 10:45:15
author: 김광명
---
안녕하세요 오늘은 Jekyll(제킬 이하 제킬)을 윈도우에 설치해서 사용해보 도록 하겠습니다.

먼저 제킬이 뭔지 궁굼해 보면 공식사이트에서 들어가 봤습니다.


> http://jekyllrb.com/

<pre>
Jekyll is a simple, blog-aware, static site generator. It takes a template directory containing raw text files in various formats, runs it through a converter (like Markdown) and our Liquid renderer, and spits out a complete, ready-to-publish static website suitable for serving with your favorite web server. Jekyll also happens to be the engine behind GitHub Pages, which means you can use Jekyll to host your project’s page, blog, or website from GitHub’s servers for free.
</pre>

- Jekyll은 여러 형태의 텍스트와 소스들로 구성 정적 파일들을 웹사이트로 생성시켜주는 툴이다.
- GitHub Pages 에서도 이용하고 있다.
- 그래서 무료 블로그를 만들고 사용 할 수 있다.
뭐 이정도 인것 같습니다.

그러면 어떻게 쓰느냐 Quick-start guide 에 쉽게 나와있습니다.

{% highlight bash %} 
~ $ gem install jekyll 
~ $ jekyll new myblog 
~ $ cd myblog 
~/myblog $ jekyll serve
=> Now browse to http://localhost:4000
{% endhighlight %}

하지만 뭔가좀 이상하네요 네 그렇습니다. 프롬프트가 윈도우에서 사용하는 것이 아닌 리눅스나 Mac을 사용해 보아야 하나봅니다.

아 좌절 전 윈도우쓰고싶어요....

그래서 준비했습니다. Jekyll 윈도우에 설치해서 사용하기

---

##1.  필요한 것들##

- Ruby(ruby, DevKit)
- Jekyll
- Python(Setuptool,pip,Pygments)
- rouge

Jekyll 은 루비 기반으로 돌아갑니다. 그래서 Ruby 가 설치된 장비가 필요합니다.
그리고 syntax highlighting 을 사용하기 위해서 Pygments가 있어야 합니다. Pygments 는 Python 기반으로 돌아가기 때문에 Python이 설치 되어 있어야 하고요.

그럼 본격적으로 설치를 해보겠습니다.

---

##2. 루비(Ruby) 설치하기##


제킬(Jekyll) 은 루비(Ruby)기반으로 돌아갑니다. 그래서 Ruby 와 Development Kit 을 함께 설치해야 합니다. 

###루비(Ruby) 설치###

윈도우에서 루비를 설치하기는 쉽습니다.
루비에서 윈도우 관련 설치파일을 제공하고 있습니다.
파일은 루비 사이트에서 다운로드 받을수 있습니다.

> http://rubyinstaller.org/downloads/

본인의 시스템에 맞는 파일을 다운로드 받아 설치 합니다.

![jelky_install_01.jpg](/assets/images/jelky_install_01.jpg)

저의 컴퓨터는 64비트 Windwos 7 을 사용하고 있으니  64bit 버전을 다운로드 받아 설치 하겠습니다.

설치할때 아래 옵션을 체크해서 설치하면 어느 경로에서든 ruby 를 실행할수 있습니다.

![jelky_install_02.jpg](/assets/images/jelky_install_02.jpg)

###Development Kit 설치###

이제 Development Kit 을 설치할 차례입니다. 본인의 시스템에 맞는 파일을 다운로드 받아 설치 합니다. 이 파일 역시 루비(Ruby) 사이트에서 다운로드 받을 수 있습니다.

>http://rubyinstaller.org/downloads/

저는 편의상 아래 폴더에 파일을 설치해 놓겠습니다.

>C:\RubyDevkit 

그러면  이제 Devkit을 사용하게 초기화 해야합니다.
윈도우 CMD 창에서 아래 명령으로 초기화와 Ruby 와 Binding 을 해줍니다.

>cd C:\RubyDevkit
>ruby dk.rb init
>ruby dk.rb install

아래와 비슷한 문구들이 나오면 정상설치 된것입니다.

<pre>
C:\RubyDevkit>ruby dk.rb init
[INFO] found RubyInstaller v2.2.3 at C:/Ruby22-x64
Initialization complete! Please review and modify the auto-generated
'config.yml' file to ensure it contains the root directories to all
of the installed Rubies you want enhanced by the DevKit.
C:\RubyDevkit>ruby dk.rb install
[INFO] Updating convenience notice gem override for 'C:/Ruby22
[INFO] Installing 'C:/Ruby22-x64/lib/ruby/site_ruby/devkit.rb'
</pre>

---

##3. 지킬(Jekyll) 설치하기##

다음으로 지킬을 설치 하면 됩니다.  설치는 간단합니다.
루비(Ruby)의 gem 패키지 인스톨러를  용해서 설치합니다.

>gem install jekyll

만약 UAC 가 켜있는 상태면 권한을 물어보니 설치를 진행하기위해서는 권한을 줘야 합니다.

아래와 같이 Done 이 나오고 명령프롬프트가 나타나면 정상설치 된 것입니다.
<pre>
Done installing documentation for fast-stemmer, classifier-reborn, ffi, rb-inoti
fy, rb-fsevent, hitimes, timers, celluloid, listen, jekyll-watch, sass, jekyll-s
ass-converter, execjs, coffee-script-source, coffee-script, jekyll-coffeescript,
 jekyll-gist, jekyll-paginate, blankslate, parslet, toml, redcarpet, posix-spawn
, yajl-ruby, pygments.rb, colorator, safe_yaml, mercenary, kramdown, liquid, jek
yll after 23 seconds
31 gems installed
C:\RubyDevkit>
</pre>

code blocks 사용을 위해 rouge 를 설치해줍니다.

>gem install rouge

실행 결과는 아래와 같습니다.

<pre>
C:\RubyDevkit>gem install rouge
Fetching: rouge-1.9.1.gem (100%)
Successfully installed rouge-1.9.1
Parsing documentation for rouge-1.9.1
Installing ri documentation for rouge-1.9.1
Done installing documentation for rouge after 4 seconds
1 gem installed
</pre>

---

##4. Python 설치하기##

이제 거의 다왔습니다.
지킬(Jekyll)에서  syntax highlighter를 사용하기 위해 Python을 설치 해줍니다. 다운로드는 아래 경로에서 받으시면 됩니다.

>https://www.python.org/downloads/

저는 2.7 버전을 사용하겠습니다.
(pip 는 따로 설치를 안해줘됩니다. Python 설치할때 자동으로 설치가 됩니다.)

### 윈도우 환경변수 설정 ###

설치가 완료되었으면 환경변수가 정상적으로 설정되었는지 확인 합니다.

환경변후에 아래 경로를 추가해줍니다.

>C:\Python27;C:\Python27\Scripts;

![jelky_install_03.jpg](/assets/images/jelky_install_03.jpg)

![jelky_install_04.jpg](/assets/images/jelky_install_04.jpg)

그러면 Python 과 pip 가 정상적으로 실행되는지 확인 해보겠습니다.

Windows CMD 창을 열어서 확인 해보시면 됩니다.

![jelky_install_05.jpg](/assets/images/jelky_install_05.jpg)

### Python & pip 실행 ###

환경 셋팅이 완료되었으면 정상적으로 Python 이 실행됩니다.

>python

![jelky_install_06.jpg](/assets/images/jelky_install_06.jpg)

>pip

![jelky_install_07.jpg](/assets/images/jelky_install_07.jpg)

### Pygments 설치 ###

syntax highlighting 을 사용하기 위해서 Pygments를 설치 해보도록 하겠습니다.
설치는 pip 로 설치하면 간단히 설치가 됩니다.

>pip install Pygments


### pip 실행이 안되는경우 ###

가끔 pip 실행시 실행이 안되는 경우들이 있습니다. 
실행이 안될경우 아래의 경우들을 살펴 보시기 바랍니다.

**윈도우 계정이 한글로 된경우**

![jelky_install_08.jpg](/assets/images/jelky_install_08.jpg)

**pip 명령어 실행불가**

만약 pip 가 설치되지 않았을경우는 설치를 다시 진행하시면 정상적으로 설치가 될것입니다.
설치가 완료된것을 확인은 pip.exe 파일이 파이선 설치경로의 Scripts 에 있으면 정상적으로 설치된 것입니다. 저는 Default 로 설치를 진행해서 아래 경로에 파일이 존재합니다.

>C:\Python27\Scripts>pip.exe 

**Windows 계정이 한글로 되어있을 경우**

영어로 계정을 새로 생성하고 컴퓨터 이름을 영어로 설정하면  정상적으로 실행이 가능할것입니다.

![jelky_install_09.jpg](/assets/images/jelky_install_09.jpg)
<pre>
Collecting flake8
Exception:
Traceback (most recent call last):
  File "C:\Python27\lib\site-packages\pip\basecommand.py", line 223, in main
    status = self.run(options, args)
  File "C:\Python27\lib\site-packages\pip\commands\install.py", line 280, in run
    requirement_set.prepare_files(finder)
  File "C:\Python27\lib\site-packages\pip\req\req_set.py", line 317, in prepare_files
    functools.partial(self._prepare_file, finder))
  File "C:\Python27\lib\site-packages\pip\req\req_set.py", line 304, in _walk_req_to_install
    more_reqs = handler(req_to_install)
  File "C:\Python27\lib\site-packages\pip\req\req_set.py", line 439, in _prepare_file
    req_to_install.populate_link(finder, self.upgrade)
  File "C:\Python27\lib\site-packages\pip\req\req_install.py", line 244, in populate_link
  .....
 </pre>

---

##5. 제킬(jekyll)  실행하기##

이제 설치가 마무리 된것 같습니다. 

그럼 jekyll 을 실행해보겠습니다.

편의상  아래 경로에서 작업을 진행하도록하겠습니다.

>mkdir C:\jekyll

jekyll 을 실행해보면 아래와같은 설명이 나옵니다.

>jekyll

![jelky_install_10.jpg](/assets/images/jelky_install_10.jpg)


설명에 나온데로 jekyll serve 를 실행해보겠습니다.

>jekyll serve

![jelky_install_11.jpg](/assets/images/jelky_install_11.jpg)

**wdm 설치**

자세한 설명이 나오는군요.뭔가 또 설치해야 한답니다.

네 wdm 을 설치 해야 할것 같습니다. 

설치해보도록 하겠습니다.

>gem install wdm

![jelky_install_12.jpg](/assets/images/jelky_install_12.jpg)


설치완료후 다시 jekyll server 를 실행하면 정상적으로 실행이 될것입니다.

이제 웹브라우져를 열어서 127.0.0.1:4000 에 접속하시면 됩니다.

>127.0.0.1:4000

![jelky_install_13.jpg](/assets/images/jelky_install_13.jpg)

위 그림과 같으면 사용못하겠죠? 

---

## 6. 제킬(jekyll)  사이트 생성하기 ##

이제 드디어 제킬 사이트를 생성해보도록 하겠습니다.

사이트 생성은 new 명령을 통해서 생성가능합니다. 

blog 라는 폴더를 만들어서 생성하겠습니다.

>jekyll new C:\Jekyll\blog

![jelky_install_14.jpg](/assets/images/jelky_install_14.jpg)

뭔가가 만들어진거 같습니다.

>dir/w

![jelky_install_15.jpg](/assets/images/jelky_install_15.jpg)

**환경설정하기**

사이트가 만들어졌으니 환경설정을 해야 합니다.

위에서 설치한 syntax highlighting 기능과 UTF-8 을 사용가능하게하겠습니다.
설정파일은 _config.yml 파일입니다. 사이트가 생성된 Root 에 위치 하고 있습니다.


> encoding: utf-8

> highlighter: rouge

> highlighter: pygments

![jelky_install_16.jpg](/assets/images/jelky_install_16.jpg)

<pre>
# Site settings
title: Your awesome title
email: your-email@domain.com
description: > # this means to ignore newlines until "baseurl:"
  Write an awesome description for your new site here. You can edit this
  line in _config.yml. It will appear in your document head meta (for
  Google search results) and in your feed.xml site description.
baseurl: "" # the subpath of your site, e.g. /blog/
url: "http://yourdomain.com" # the base hostname & protocol for your site
twitter_username: jekyllrb
github_username:  jekyll

# Build settings
markdown: kramdown

encoding: utf-8
highlighter: rouge
highlighter: pygments

</pre>

그럼 다시 jekyll 을 실행하겠습니다. 실행을 하면 환경설정 및 초기 셋팅을 진행합니다.

> jekyll serve

![jelky_install_16.jpg](/assets/images/jelky_install_17.jpg)

설정이 완료 되었으니 사이트에 접속해보겠습니다.

>127.0.0.1:4000

![jelky_install_17.jpg](/assets/images/jelky_install_18.jpg)

아까와는 다른 화면이 나오네요. 뭔가 복잡한듯 하면서도 간단하죠?

지금까지 저희 [와탭(Whatap)](http://whatap.io/)에서도 사용하고 있는 Jekyll 을 윈도우에서 사용하려면 어떻게 해야 하는지 에 대해서 알아 봤습니다.

기타 자세한 사항들은 공식 사이트 와 한글 사이트에서 확인 해보시기 바랍니다.

> http://jekyllrb.com/

> http://jekyllrb-ko.github.io/

감사합니다.
