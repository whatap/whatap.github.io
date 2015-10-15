---
layout: post
title:  "Fabric을 이용해 다수의 리눅스 서버에 whatap 한꺼번에 설치하기"
date:   2015-10-15 22:00:00
author: 남형석
profile: hsnam.png
---
현실적으로 존재하는지는 재처두고 한명의 시스템 관리자가 서버를 몇 백대 단위로 관리한다고 가정해봅시다. 또 일상적으로 서버를 추가하고 업그레이드 한다고 생각하면 각각의 서버에 직접 접속하는 일은 상상하기 힘듭니다. 서버의 라이프 싸이클에서 발생하는 거의 모든 일들을 자동화 해야합니다. 
조직별로 또 시스템 관리자 별로 각각의 노하우나 적용하고 있는 솔루션이 있을 것입니다. 하지만 당신이 시스템 관리 경험이 별로 없는 개발자라면 많은 서버에 접속할 수 있는 쉽고 강력한 툴을 원할것입니다. 
필자도 개발자로써 프로그램 코드를 대량의 서버에 배포하고 테스트할때 여러가지 도구를 시도해 보고 장단점을 느껴봤습니다. 특이 정교한 제어가 가능한 솔루션일 수록 쉽게 쓰기가 부담스러운 측면이 있었습니다. 
최소한의 installation footprint로 최대한의 일을 최단시간에 한다고 생각해 보았을때 필자는 python으로 생활비를 마련하고 있으므로 파이썬으로 제작된 fabric - http://www.fabfile.org이 매력적이었습니다.
10대의 리눅스 서버에 whatap agent를 10분내에 설치하는 것을 목표로 Fabric을 테스트해보겠습니다.  
 
### 1. Fabric이란
Fabric은 ssh 연결을 통해 할 수 있는 일을 자동화 할 수 있는 도구입니다. 개발과 시스템 관리에서 필요한 대부분의 일을 스크립트 언어인 파이썬으로 구현하여 복잡한 반복작업이나 설정을 대량의 서버에 적용할때 대상 서버에 추가 설치되는 agent없이 ssh 접속만을 사용하여 처리하는 도구입니다.
라이브러리와 커맨드 라인 툴로 구성되어 간단한 작업 구현 파일만 작성하면 다운로드 받는 즉시 사용할 수 있습니다.

### 2. 가상 서버 생성
#### AWS EC2 Amazon Linux
##### vm 생성 위저드로 t2 instance로 5 개를 생성 합니다.
#### AWS EC2 Ubuntu
##### vm 생성 위저드로 t2 instance로 5 개를 설치 합니다.

### 3. fabric 설치
#### 아래와 같이 fabric 라이브러리를 설치합니다.
<pre>
sudo pip install fabric
</pre>
![Fabric](/assets/images/hsnam/01/2015-10-15-Fabric-01.PNG)
#### 아래와 같이 추가한 서버들의 목록을 포함한 fabfile.py를 작성합니다. 아래 파일은 목록에 있는 서버들에 ssh접속을 하여 와탭 agent를 설치합니다.
설치에 필요한 whatap_license_key와 접속에 필요한 ec2 key file의 위치를 명시하고 있습니다.
<pre>
from fabric.api import run, env, put, sudo

env.hosts=['ec2-user@172.31.40.192', 'ec2-user@172.31.40.189', 'ec2-user@172.31.40.190', 'ec2-user@172.31.40.191', 'ec2-user@172.31.44.238', 'ubuntu@172.31.43.24', 'ubuntu@172.31.43.26', 'ubuntu@172.31.43.25', 'ubuntu@172.31.43.23', 'ubuntu@172.31.43.27']

env.key_filename='/home/ec2-user/ec2/hsnam_key.pem'
whatap_license_key='0WJDE46XXZMCKOD2AL7T'

def install_whatap():
    put('install.sh', '~/')
    run('/bin/bash ~/install.sh %s'%(whatap_license_key))

</pre>

### 4. 설치 스크립트 작성
#### 아래와 같이 설치 스크립트를 작성합니다.
<pre>
[ec2-user@ip-172-31-43-57 ~]$ cat install.sh
#!/bin/bash
is_apt_get=`sudo which apt-get`

if [ "$is_apt_get" ] ; then
sudo wget http://repo.whatap.io/debian/release.gpg -O -|sudo apt-key add -
sudo wget http://repo.whatap.io/debian/whatap-repo_1.0_all.deb
sudo dpkg -i whatap-repo_1.0_all.deb
sudo apt-get update < /dev/null
sudo apt-get install whatap-agent < /dev/null
sudo env PATH=$PATH whatap $1
fi

is_yum=`sudo which yum`

if [ "$is_yum" ] ; then
sudo rpm -Uvh http://repo.whatap.io/centos/5/noarch/whatap-repo-1.0-1.noarch.rpm
sudo yum -y install whatap-agent
sudo env PATH=$PATH whatap $1
sudo service whatap-agent start
fi
</pre>
### 5. Agent설치
#### fabfile.py가 있는 디렉토리에서 아래와 같이 실행합니다.
<pre>
fab install_whatap
</pre>
![Agent install](/assets/images/hsnam/01/2015-10-15-Fabric-05.PNG)
실행이 끝난뒤 아래와 같이 와탭 콘솔에 서버가 모두 등록된것을 확인할 수 있습니다.
![Agent install](/assets/images/hsnam/01/2015-10-15-Fabric-06.PNG)
### 마치면서 
클라우드 환경에서 agent설치를 편리하게 할 수 있는 방법에 대해 찾아보는 과정은 마치 수많은 골목길을 지나 원하는 목적지까지 가는것과 비슷했습니다. 
같은 목적을 달성하기 위해 여러가지 방법이 존재하는것이 오히려 어려웠습니다. VM생성시부터 서버를 등록해서 사용하는 형식의 강력한 솔루션들이 많았지만 가장 설치기반 없이 작동할 수 있는 방법을 적용하여 보았습니다.
이 방법은 시스템 관리에 경험이 적은 개발자들이 개발 중 테스트와 같이 짧은 시간에 작업을 마쳐야 하는 상황에서 사용할 수 있을것 같습니다.

 


