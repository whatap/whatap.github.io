---
layout: post
title:  "AWS RDS MySQL 모니터링"
date:   2016-01-02 17:00:00
author: 남형석
profile: hsnam.png
---
AWS RDS는 기술이 필요한 데이터베이스 설정 및 운영 업무를 편리하게 할 수 있는 서비스입니다. 서비스화 할때 성능 옵션이 적절하게 조절되었기 때문에 MySQL과 AWS의 부조화에 대한 걱정없이 데이터베이스를 생성하고 운영할 수 있습니다.
MySQL을 모니터링 할 수 있는 와탭의 기능을 사용하여 RDS MySQL 이 처리하고 있는 쿼리의 수 및 종류/버퍼 사용량/데이터베이스 크기등을 편리하게 알 수 있습니다.
Whatap Agent를 설치하여 MySQL type RDS를 모니터링 하는 법을 적어보겠습니다.

### 1. AWS RDS 생성
MySQL 타입 RDS Instance를 하나 생성합니다. 생성 방법은 아래 링크를 참조합니다.
https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/USER_CreateInstance.html
![AWS RDS](/assets/images/hsnam/02/2016-01-02-AWS-RDS-INSTANCE.PNG)

### 2. Linux Whatap Agent 설치
Whatap Agent MySQL 타입은 Linux에만 설치가능합니다. Amazon Linux VM을 하나 준비합니다.
생성하는 방법은 http://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html 를 참조합니다.
생성이 완료되면 Whatap Agent를 설치합니다. 설치방법은 whatap.io에 로그인 후 데이터베이스 -> 서버추가 -> MySQL을 클릭합니다.
Amazon Linux VM을 생성한 경우 CentOS/RedHat을 클릭하면 아래와 설치 방법을 볼 수 있습니다. 
이 경우 VM에 MySQL을 설치한 경우에는 모니터링이 가능하지만 RDS는 모니터링 할 수 없습니다. 
VM에 설치한 MySQL대신 원격지의 RDS를 모니터링 하도록 /etc/my.cnf에 설정을 추가하는것을 포함하여 Whatap Agent를 설치합니다.
<pre>
sudo rpm -Uvh http://210.122.10.122/centos/5/noarch/whatap-repo-1.0-1.noarch.rpm
sudo yum -y install whatap-agent
sudo echo "[client]" >> /etc/my.cnf
sudo echo "host=RDS 엔드 포인트" >> /etc/my.cnf
sudo env PATH=$PATH whatap 라이센스키
</pre>
<pre>
whatap --install-plugin mysql
Enter the username of mysql:
rds 사용자 이름
Enter the password:
rds 패스워드
</pre>

### 3. 목록 및 데이터 확인
정상적으로 설치한 경우 2분 정도 지나면 아래와 같이 목록을 확인할 수 있습니다.
![DATABASE LIST](/assets/images/hsnam/02/2016-01-02-AWS-RDS-PERFORMANCE-LIST.PNG)
데이터 베이스 모니터링은 유료 서비스로 업그레이드를 하면 좀더 자세한 정보를 볼 수 있습니다.
![DATABASE LIST](/assets/images/hsnam/02/2016-01-02-AWS-RDS-PERFORMANCE1.PNG)
![DATABASE LIST](/assets/images/hsnam/02/2016-01-02-AWS-RDS-PERFORMANCE2.PNG)
![DATABASE LIST](/assets/images/hsnam/02/2016-01-02-AWS-RDS-PERFORMANCE3.PNG)
![DATABASE LIST](/assets/images/hsnam/02/2016-01-02-AWS-RDS-PERFORMANCE4.PNG)

### 마치면서 
RDS는 편리한 서비스이기는 하지만 어플리케이션이 데이터베이스를 정상적으로 사용하고 있는지 파악하려면 SQL Query의 상세한 정보가 필요합니다.
클라우드 워치에서는 CPU/MEMORY/IO 성능을 중심으로 정보를 제공하기 때문에 모니터링에 별도의 수단이 필요합니다.
Whatap Agent의 MySQL 모니터링 기능을 이용해서 RDS를 모니터링 편리하게 할 수 있었으면 좋겠습니다.

