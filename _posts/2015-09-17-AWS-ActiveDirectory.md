---
layout: post
title:  "아마존 Directory Service를 이용한 Domain Joined Windows 2012 R2 VM 만들기"
date:   2015-09-17 17:00:00
author: 남형석
profile: hsnam.png
---
Windows 2012환경에서 Application 대량 설치 및 업데이트 방법을 찾아보다 Active Directory로 join 된 테스트 환경을 설치해 보았습니다. 
직접 VM을 이용해서 Domain Controller를 설정하지 않고 AWS Active Directory Service 를 통해 간편하게 테스트 환경을 구축 할 수 있었습니다.
몇가지 제약사항이 있지만 테스트 환경 수준에서는 충분히 빠르고 편리하게 작동하고 있습니다. 
Domain Controller를 생성하고 Windows 2012 R2 VM을 Domain Join상태로 생성하는 방법에 대해 적어보겠습니다.

 
### 1. AWS Directory Service란
IT 자원을 사용하는 모든 조직들은 사용자들을 추가 삭제하고 역활을 부여하고 접근을 통제하거나 서버/어플리케이션 등에 접근을 제어하기위해 사용자정보와 통제수단을 통합하여 도구화 하였습니다.
Active Directory도 그런 솔루션중 하나로 다양한 IT자원과 변화하는 조직에서 높은 수준의 접근 통제에 들어가는 노력을 대폭 줄여줍니다. 
하지만 중소 규모 조직과 IT자원을 보유하고 있다면 Active Directory를 구축하고 유지하는 노력이 부담으로 작용할 수 있습니다.
AWS Directory Service는 몇번의 클릭으로 생성 및 설정이 가능해 Active Directory에 대한 지식이 없는 사람도 부담없이 사용할 수 있습니다.

### 2. AWS Active Directory 를 생성
#### AWS Directory Service 서비스에 접속하여 디렉토리를 생성합니다. 
#####https://console.aws.amazon.com/directoryservice/home
Setup Directory -> Create Simple AD -> Form 에 아래와 같이 입력 -> Next -> Create Simple AD
<pre>
Directory DNS : {xxx.xxx} #xxx.xxxx.xxx는 등록은 가능했지만 dns가 작동하지 않았습니다. 
NetBIOS name : {NetBIOS Name}
Administrator password : {VM 관리자 패스워드}
Confirm password : {VM 관리자 패스워드 확인}
Description : {Active Directory 설명}
Directory size : Small / Large #서버의 숫자와 요금차이가 있습니다.

VPC : {VPC 선택}
Subnets : { 추가할 VM과 동일한 subnet 선택}
</pre>

##### 생성한 Directory 확인
Directories -> 위에서 생성한 Directory ID를 클릭
![Directory Detail](/assets/images/hsnam/01/2015-09-17-AWS-ActiveDirectory-ActiveDirectoryDetail.PNG)

### 3. IAM Role 설정
#### AWS IAM 서비스에 접속하여 Role을 생성합니다.
#####https://console.aws.amazon.com/iam/home
###### 1) Roles-> Create New Role -> Set Role Name -> Next 
###### 2) 리스트에서 Amazon EC2 Role for Simple Systems Manager 선택 
###### 3) AmazonEC2RoleforSSM 앞 체크박스 체크 -> Create Role
Amazon EC2 Role for Simple Systems Manager Role은 VM 생성시에 Active Directory의 설정을 확인하여 자동으로 VM을 도메인에 JOIN 합니다.
도메인 JOIN 을 별도로 설정할 필요가 없기 때문에 매우 편리합니다. 생성한 Role에 아래와 같은 policy가 붙어있는것을 확인할 수 있습니다.
<pre>
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "cloudwatch:PutMetricData",
        "ds:CreateComputer",
        "ds:DescribeDirectories",
        "ec2:DescribeInstanceStatus",
        "logs:*",
        "ssm:*"
      ],
      "Resource": "*"
    }
  ]
}
</pre>

### 4. Security Group 설정 및 VM 생성
#### AWS EC2 에 접속하여 Join된 Instance간 통신이 가능하도록 Security Group을 생성합니다.
##### https://us-west-2.console.aws.amazon.com/ec2/v2/home
Security Groups -> Create Security Policy -> Form에 아래와 같이 입력 -> Create
<pre>
Security group name : {이름} 
Description : {설명}
VPC : {VPC 선택}

Inbound : VPC 내부에서 EC2 인스턴스들이 상호 통신가능하도록 VPC Private IP들에 대해 TCP/UDP 를 추가합니다.
RDP를 포함하는 사용하는 적절한 서비스 엔드포인트를 추가합니다.
</pre>

#### VM을 생성합니다.
###### 1) Instances -> Launch Instance -> Microsoft Windows Server 2012 R2 Base 선택 
###### 2) 원하는 VM Spec을 선택 -> Next
###### 3) 아래와 같이 Form을 입력합니다. -> Next
<pre>
Number of instances : {}한꺼번에 생성할 VM 숫자(기본상태 최대20개)}
Purchasing option : {스폿 인스턴스 사용여부}
Network : {VPC를 선택하거나 생성}
Subnet : {Subnet 선택}
Auto-assign Public IP : {Subnet 설정/Enable/Disable 중 선택}}
Domain join directory : {1번에서 생성한 Directory 선택}
IAM role : {3번에서 생성한 Role 선택}
Shutdown behavior : {종료시 삭제여부 선택}
Enable termination protection : {삭제 보호 사용 여부}
Enable CloudWatch detailed monitoring : {추가 모니터링 여부 선택}
Tenancy : {전용 호스트 사용여부 선택}
</pre>
###### 4) 원하는 스토리지 Spec 을 선택 합니다 -> Next
###### 5) Tag설정을 합니다. -> Next
###### 6) 4번의 앞에서 생성한 Security Group 을 선택합니다. -> Review and Launch
###### 7) 기존 Key를 선택하거나 Key를 새로 생성 합니다. Active Directory Join 상태에서는 1번에서 입력한 Adminitorator password를 사용하기 때문에 양쪽다 차이는 없습니다.

#### 도메인 조인 확인
###### 1) 목록에서 EC2 Instance State가 running상태로 바뀌면 원하는 instance 를 체크한후 Connect 클릭 -> Download Remote Desktop File -> 1번에서 입력한 Adminitorator password 를 입력
###### 2) Control Panel\System and Security\System 에서 Domain JOIN상태를 확인합니다.
![Domain Join](/assets/images/hsnam/01/2015-09-17-AWS-ActiveDirectory-VmDomainJoin.png)
###### 3) Server Manager -> All Servers에서 생성한 모든 서버가 보이는것을 확인 할 수 있습니다.
![Domain Join Servers](/assets/images/hsnam/01/2015-09-17-AWS-ActiveDirectory-AllServers.png)

### 마치면서 
AWS에서 자동화된 Directory 설정 기능을 제공하면서 관련 지식이 없어도 15분 이내 Domain join된 VM들을 대량으로 만들 수 있었습니다. 직접 하나 하나 설정하던 것과 비교하면 엄청난 혁신임은 틀림 없습니다.
특히 사용자 등록 롤 설정을 쉽게 할 수 있게 되면서 윈도우서버를 대량으로 운영하는데 생기는 관리 부담을 줄이는 데 도움이 될 것 같습니다. 
또 IAM 혹은 Onpremise Active Directory 와 연동되면 기존 사용자 설정을 중복하지 않고 설정할 수 있어 업무부하를 줄이는데 많은 도움이 될것 같습니다.
