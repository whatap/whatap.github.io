---
layout: post
title:  "아마존 클라우드워치 로그 분석기능을 이용한 웹 서비스/워드 프레스 성능 모니터링/분석"
date:   2015-09-10 17:00:00
author: 남형석
---

아마존 EC2에서 소규모 혹은 대규모로 웹 서비스를 운영하게되면 필연적으로 다량의 웹 로그가 발생하게 됩니다. 
이 로그에는 해당 서비스의 성능에 관한 가장 정확한 자료가 존재하지만 이것을 분석하는데는 생각보다 많은 시간과 비용이 발생합니다. 

특히 대량으로 발생하는 데이터를 실시간으로 분석하려면 scale-out 가능한 로그 분석 툴을 갖추어야 되는데요. 
컨텐츠나 기능개발에 바쁜 중소규모 개발팀에서 감당하기에는 많은 어려움이 있습니다. 

전문 솔루션을 도입하는 결정을 내리기 전에 아마존 클라우드 워치의 로그 기능을 이용한 간단한 로그 분석에 대해 적어 보도록 하겠습니다.
 
### 1. 아마존 클라우드 워치 로그기능이란
로그 파일이 발생하는 모든 시스템이나 어플리케이션을 모니터링 하거나 장애 원인 분석하는데 활용할 수 있는 가입형 아마존 웹서비스 입니다. 수집한 로그는 원하는 만큼 유지할 수 있고 필터를 통해 대용량 데이터 분석 플랫폼인 아마존 kinesis 에 스트리밍 할 수 도 있습니다. 클라우드형 서비스이기 때문에 용량산정에 대한 큰 고민 없이 즉시 적용할 수 있다는 점이 가장 큰 장점이라고 생각합니다. 

### 2. Amazon Linux 를 선택하여 VM을 생성
#### IAM 서비스에 접속하여 CloudWatch Log서비스에 업로드 할 수 있는 policy를 만듭니다. 
#####https://console.aws.amazon.com/iam/home 
Policies -> 상단의 Create Policy -> Create Your Own Policy -> Form 에 아래와 같이 입력
<pre>
Policy Name : {이름}
Description : {설명}
Policy Document
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams",
                "logs:DescribeLogGroups"
            ],
            "Resource": [
                "arn:aws:logs:*:*:*"
            ]
        }
    ]
}
</pre>

#### 생성한 Policy 를 붙일 Role 생성 
Roles -> 상단의 Create New Role -> Role Name 입력 -> 아무거나 선택 -> 위에서 생성한 policy 선택

#### EC2 서비스를 접속하여 Amazon Linux VM을 생성
##### https://console.aws.amazon.com/ec2/v2/home
EC2 Dashboard -> 중단의 Launch Instance -> Amazon Linux AMI 선택 -> Next: Configure Instance Details -> 중단 IAM role에 위에서 생성한 role을 선택 -> 우측 하단 Review and Launch -> 우측 하단 Launch -> ssh 키 선택 혹은 생성 -> Launch Instance


### 3. 워드프레스 설치
접속하여 워드프레스를 설치합니다.

<pre>
sudo yum -y install httpd gd php-gd php-mysql mysql-server

cd /var/www/html
wget https://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
cd wordpress
cp wp-config-sample.php wp-config.php
vim wp-config.php 
#mysql 접속정보를 입력 및 저장
service mysqld start
echo "create database {wordpress database name}" |mysql 
echo "grant all privileges on {wordpress database name}.* to {wordpress database user}@localhost ideitified by '{wordpress database password}'" |mysql 
service httpd start
</pre>

해당 VM의 웹 주소로 접속하여 설치를 마무리 합니다.
http://{public ip of vm}/wordpress

### 4. Cloudwatch Log Agent 설치 및 실행
#### 이제 Cloudwatch Log Agent를 설치할 차례입니다. 
<pre>
aws configure
sudo yum install -y awslogs
vim /etc/awslogs/awscli.conf
#region = 에 vm을 생성한 region과 같은 region을 입력 및 저장 합니다. 이것을 생략하면 N.Virginia 에 로그가 업로드 됩니다.
vim /etc/awslogs/awslogs.conf
#아래와 같이 입력합니다.

[{로그파일 섹션 이름 - 중복 불가)}]
datetime_format = %d/%b/%Y:%H:%M:%S %z
file = /var/log/httpd/access_log
buffer_duration = 5000
log_stream_name = 서버 이름
initial_position = start_of_file
log_group_name = 로그 그룹 이름

service awslogs start
</pre>

### 5. 로그 수집 확인 및 필터 생성
#### 아마존 클라우드 워치에 접속하여 로그가 수집되는지 확인합니다.
##### https://console.aws.amazon.com/cloudwatch/home
Logs -> 목록에서 4번 /etc/awslogs/awslogs.conf 에서 입력한 로그 그룹 이름을 클릭 -> 서버 이름 클릭 -> 수집된 로그 확인

![Cloud Watch Log](/assets/images/2015-09-10-AWS-CloudWatch-LogViewer.PNG)

#### 시간당 접속 카운트 Metric 입력
Logs -> 로그 그룹 이름 앞으 체크박스 선택 -> Create Metric Filter -> Filter Pattern 을 비워두고 Assign Metric -> 아래와 같이 폼 입력 -> Create Filter
<pre>
Filter Name : 이름을 입력
Metric Name : EventCount 
</pre>
#### 시간당 다운로드 카운트 Metric 입력
Logs -> 로그 그룹 이름 앞으 체크박스 선택 -> Create Metric Filter -> Filter Pattern 을 [ip, id, user, timestamp, request, status_code, size, referer, agent ] -> Assign Metric -> 아래와 같이 폼 입력 -> Create Filter
<pre>
Filter Name : 이름을 입력
Metric Name : BytesTransferred
metric Value : $size
</pre> 
### 6. 로그 파일에서 생성된 챠트 확인
#### 시간당 접속수 확인
Logs -> 2 filter -> LogMetrics -> EventCount 선택 -> 챠트확인

![Cloud Watch Event Count](/assets/images/2015-09-10-AWS-CloudWatch-EventCount.PNG)

#### 시간당 다운로드 트래픽 확인
Logs -> 2 filter -> LogMetrics -> BytesTransferred 선택 -> 챠트확인

![Cloud Watch Bytes Transferred](/assets/images/2015-09-10-AWS-CloudWatch-BytesTransferred.PNG)

### 마치면서 
이렇게 하면 전문 툴을 도입하지 않고 간단하게 모니터링을 할 수 있습니다.
다만 전문 툴에서 제공하는 다양한 그룹 기능을 구현하자면 계속적인 학습이 필요합니다. 필자는 Amazon S3에서 발생하는 access log 를 클라우드 워치 로그에 업로드 하여 분석을 시도해 보았습니다. S3의 access 로그를 읽어서 업로드하는 간단한 코드만 있으면 가능하지만  해당 코드를 실행할 VM이 필요하여 비용발생면에서 불리하다고 판단 하였습니다. 

AWS를 배우면서 항상 많은 기능들을 내 서비스에 즉시 적용할 수 있을것 같아  좋습니다. 개발자 감성으로 즐겁게 배우고 있습니다. 하지만 실제 업무 에서는 전문 솔루션의 기능과 비교하고 AWS에서 어떻게 구현할지 고민해야 된다는 점이 부담으로 느껴집니다. 여기에 적응하자면 업무를 AWS에 맞추거나 자체 솔루션을 도입하거나 해야 하겠지요.

CloudWatch Log기능을 사용해서 EC2에서 운영하는 웹서비스를 모니터링 하고 챠트를 즉시 볼 수 있어서 좋았습니다. AWS개발팀이 로그 필터 Metric을 다양하게 개발해서 원하는 통계정보를 DevOps들이 더 쉽게 볼 수 있게 되기를 기원합니다.               