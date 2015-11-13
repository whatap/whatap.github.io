---
layout: post
title:  "AWS EC2 Instsance 와탭 자동 등록"
date:   2015-11-11 17:00:00
author: 남형석,김광명
profile: hsnam.png
---
클라우드에서 작동하는 서비스가 많아지면서 부하 증감시 자동으로 scale out하는 기능이 대세로 자리잡고 있습니다. 클라우드 사용 비용을 낮추는데도 도움을 줄 수 있고 부하 증감에 대하여 서비스 안정성 및 품질을 높여줄 수 있기에 클라우드를 사용하게 하는 주요 동기중 하나라고 필자는 생각합니다.

AWS에서도 사용자가 웹 트래픽의 증감에 실시간으로 대처할 수 있도록 일찌감치 EC2 AutoScaleGroup - ASG 과 Elastic Load Balancer - ELB를 제공하고 있습니다. 
이 경우에도 부하에 대응하여 작동하는 EC2 Instance들에 Guest OS 레벨 모니터링(와탭 같은)을 제공하려면 ASG Launch Configuration에 지정된 AMI가 Monitoring Agent를 내장하고 있어야 사용 가능합니다.
또 AWS AutoScaleGroup에서는 부하가 증감함에 따라 EC2 Instance가 삭제되는 경우 모니터링에서 서버 다운 알림을 막아 줄 있는 방법이 필요합니다. 
그렇지 않다면 부하 감소로 인한 EC2 Instance Scale Down시에 모니터링(와탭)에서는 서버 다운알림이 발생하게 됩니다.
와탭에서는 이번에 사용자가 AMI에 내장하여 설치할 수 있도록 윈도우 버젼 Agent를 1.4.0으로 업데이트 하였습니다. 
이것을 이용하여 어떻게 AWS ASG가 생성 삭제하는 EC2 Instance 를 와탭에 자동 등록/해지 할 수 있는지를 적어보겠습니다.

### AWS Instance 생성
![AWS-CreateInstance-01](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-01.jpg)
![AWS-CreateInstance-02](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-02.jpg)
![AWS-CreateInstance-03](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-03.jpg)
![AWS-CreateInstance-04](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-04.jpg)
![AWS-CreateInstance-05](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-05.jpg)
![AWS-CreateInstance-06](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-06.jpg)
![AWS-CreateInstance-07](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-07.jpg)
![AWS-CreateInstance-08](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-08.jpg)
![AWS-CreateInstance-09](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-09.jpg)
![AWS-CreateInstance-10](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-10.jpg)
![AWS-CreateInstance-11](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-11.jpg)
![AWS-CreateInstance-12](/assets/images/hsnam/2015-11-11/AWS-CreateInstance-12.jpg)

### AWS Image 생성
![AWS-CreateImage-01](/assets/images/hsnam/2015-11-11/AWS-CreateImage-01.jpg)
![AWS-CreateImage-02](/assets/images/hsnam/2015-11-11/AWS-CreateImage-02.jpg)
![AWS-CreateImage-03](/assets/images/hsnam/2015-11-11/AWS-CreateImage-03.jpg)
![AWS-CreateImage-04](/assets/images/hsnam/2015-11-11/AWS-CreateImage-04.jpg)
![AWS-CreateImage-05](/assets/images/hsnam/2015-11-11/AWS-CreateImage-05.jpg)
#### Whatap 서버리스트에서 자동으로 등록된 서버를 확인하실수 있습니다.
![AWS-CreateImage-06](/assets/images/hsnam/2015-11-11/AWS-CreateImage-06.jpg)

### 1. AWS Auto Scale Group - ASG 이란
ASG란 부하가 증감하는것에 비례하여 EC2 Instance 의 수를 조절하여 부하 증감에 자동 대응하는 시스템입니다. 
#### Auto scaling group 생성
그러면 AutoScale Group 을 생성해보도록 하겠습니다. 
##### Create Auto Scaling group버튼을 클릭합니다.
![AWS-CreateAutoScaling-01](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-01.jpg)
AWS> AUTO SCALING>Auto Scaling Groups 에서 Create Auto Scaling group 버튼 을 클릭합니다.

##### Create a new launch configuration을 선택하고 Next Step을 클릭합니다.
![AWS-CreateAutoScaling-02](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-02.jpg)
##### 앞서 작성한 AMI 를 선택합니다. AMI가 탑재하고 있는 Application의 성능을 고려하여 적절히 선택합니다. CPU/Disk/Traffic 부하에 따라 Credit 이 떨어지지 않는 정도의 최하 Type이 적절할 수 있습니다.
![AWS-CreateAutoScaling-03](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-03.jpg)
##### 인스턴스 타입을 을 선택후 Next 버튼을 누릅니다. 
![AWS-CreateAutoScaling-04](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-04.jpg)
##### Confituratio Detail 을 설정후 Next 버튼을 누릅니다.
![AWS-CreateAutoScaling-05](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-05.jpg)
##### Storage 설정후 Next 버튼을 누릅니다.
![AWS-CreateAutoScaling-06](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-06.jpg)
##### Security Group 설정후 Review 버튼을 누릅니다. 
![AWS-CreateAutoScaling-07](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-07.jpg)
##### 작성한 내용이 맞는지 확인후 Create luanch Configuration 으로 설정합니다. 
![AWS-CreateAutoScaling-08](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-08.jpg)
##### Review후에 Create launch configuration을 클릭하면 생성된 EC2 Instance에서 사용할 Key를 정하게 됩니다. 이 경우 키 이외에도 ActiveDirectory에 Join 하는 것이 관리가 편리합니다. 
![AWS-CreateAutoScaling-09](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-09.jpg)
##### 이제부터 ASG 본체를 생성하게 됩니다. 이름과 서브넷, ELB를 사용할지 여부를 선택하고 다음을 클릭합니다.
![AWS-CreateAutoScaling-10](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-10.jpg)
![AWS-CreateAutoScaling-11](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-11.jpg)
##### Auto Scale Group 에서 부하가 증감하는것을 감지하여 자동으로 EC2 Instance를 생성하는 메커니즘을 정의하게 됩니다. 원리는 CloudWatch의 Metric 중 하나를 선택하여 지정된 조건이 만족하면 한번에 Instance 몇개를 생성 삭제할지 지정하게 됩니다.
 이것이 전반적인 서비스 품질을 선택하게 되므로 필자는 Scale Out은 빨리 Scale Down은 천천히 하도록 설정하고 있습니다.
![AWS-CreateAutoScaling-12](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-12.jpg)
##### Notification은 ASG를 생성 후 와탭이 제공하는 별도 스크립트에서 설정하게 됩니다. 
![AWS-CreateAutoScaling-13](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-13.jpg)
##### Key / Value 값을 지정합니다.  
![AWS-CreateAutoScaling-14](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-14.jpg)
##### Create Auto scaling group을 클릭하여 설치를 마무리 합니다.
![AWS-CreateAutoScaling-15](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-15.jpg)
##### Auto scaling group이 생성되어 첫번째 Instance가 생성되고 있는것을 Instances 탭에서 확인할 수 있습니다. 
![AWS-CreateAutoScaling-16](/assets/images/hsnam/2015-11-11/AWS-CreateAutoScaling-16.png)

#### Auto scaling group이 Scale down할때 whatap에 feed back을 주도록 설정하기
Auto scaling group이 scale down할 때 EC2 Instance가 삭제되면 whatap 콘솔에서는 서버다운으로 표시되고 알림도 발생하게 됩니다. 이 경우에만 서버 다운이 아니라 서버 정지로 표시될 수 있도록 ASG의 Notification 을 자동 설정하는 방법을 적어보겠습니다.
와탭에서는 자동으로 ASG Notification을 설정할 수 있도록 스크립트 코드를 공개하였습니다. https://raw.githubusercontent.com/whatap/tools/master/aws/ConfigAutoScaleGroup.ps1
위 주소에 있는 파워쉘 파일을 EC2 Windows 2012 Instance 에 다운로드 받아 실행하면 자동으로 Notification을 설정할 수 있습니다.
스크립트를 실해하면 EC2FullAccess/SNSFullAccess 권한이 있는 Access/Secret Key를 입력해야 진행하실 수 있습니다.
<pre>
IAM User Access/SecretKey is required to setup SNS and ASG notification.
refer to http://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions.html for IAM permission configuration
Your access information will be stored as profile - whatapprofile which will be delete when program ends.
Please input AccessKey: AKIAJ7YBE5T2XUE4NKTA
Please input SecretKey: PHYx4Ct/Cp5xtsxSx4dtYQ8CG0zeqmLFyWvZr7ZO
True
1) whatap-asg1
Please select an auto scale group (q to quit)?: 1
Created Topic: arn:aws:sns:us-west-2:262440944390:whatap-serverdown-push
True
pending confirmation
arn:aws:sns:us-west-2:262440944390:whatap-serverdown-push:6e270616-1bf2-4f1d-a036-52d454c1b2fa
Configuration Complete. Enter to close windows..:
</pre>
위스크립트를 재실행하면 아래와 같이 설정된것을 확인할 수 있습니다.
<pre>
IAM User Access/SecretKey is required to setup SNS and ASG notification.
refer to http://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions.html for IAM permission configuration
Your access information will be stored as profile - whatapprofile which will be delete when program ends.
Please input AccessKey: AKIAJ7YBE5T2XUE4NKTA
Please input SecretKey: PHYx4Ct/Cp5xtsxSx4dtYQ8CG0zeqmLFyWvZr7ZO
True
1) whatap-asg1  => whatap sns feedback already configured
Please select an auto scale group (q to quit)?: q
</pre>


### 끝마치며
AWS EC2는 확장성이 높은 서비스를 네트워크/서버 인프라에 대한 대규모 투자 없이 누구나 만들 수 있다고 생각하게 합니다. 그 핵심적인 기능중 하나인 Auto scaling group 과 와탭 Agent의 연동 방법에 대해 적어보았습니다.
와탭에서는 ASG 에서 Scale out되어 생성되는 인스턴스 내부의 정보를 제공할 수 있는 방법을 제공함으로써 고객이 좀 더 부하 증감 폭이 큰 서비스를 높은 품질을 유지하며 안전하게 운영하게 될 수 있기를 바랍니다. 