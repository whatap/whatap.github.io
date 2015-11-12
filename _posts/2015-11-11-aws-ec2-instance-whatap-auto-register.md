---
layout: post
title:  "AWS EC2 Instsance 와탭 자동 등록"
date:   2015-11-11 17:00:00
author: 남형석,김광명
profile: hsnam.png
---
클라우드에서 작동하는 서비스가 많아지면서 부하 증감시 자동으로 scale out하는 기능이 대세로 자리잡고 있습니다. 클라우드 사용 비용을 낮추는데도 도움을 줄 수 있고 부하 증감에 대하여 서비스 안정성 및 품질을 높여줄 수 있기에 클라우드를 사용하게 하는 주요 동기중 하나라고 필자는 생각합니다.
AWS에서도 사용자가 웹 트래픽의 증감에 실시간으로 대처할 수 있도록 일찌감치 EC2 AutoScaleGroup - ASG 과 Elastic Load Balancer - ELB를 제공하고 있습니다. 이 경우에도 부하에 대응하여 작동하는 EC2 Instance들에 Guest OS 레벨 모니터링(와탭 같은)을 제공하려면 ASG Launch Configuration에 지정된 AMI가 Monitoring Agent를 내장하고 있어야 사용 가능합니다.
또 AWS AutoScaleGroup에서는 부하가 증감함에 따라 EC2 Instance가 삭제되는 경우 모니터링에서 서버 다운 알림을 막아 줄 있는 방법이 필요합니다. 그렇지 않다면 부하 감소로 인한 EC2 Instance Scale Down시에 모니터링(와탭)에서는 서버 다운알림이 발생하게 됩니다.
와탭에서는 이번에 사용자가 AMI에 내장하여 설치할 수 있도록 윈도우 버젼 Agent를 1.4.0으로 업데이트 하였습니다. 이것을 이용하여 어떻게 AWS ASG가 생성 삭제하는 EC2 Instance 를 와탭에 자동 등록/해지 할 수 있는지를 적어보겠습니다.

### 1. AWS Auto Scale Group - ASG 이란
ASG란 부하가 증감하는것에 비례하여 EC2 Instance 의 수를 조절하여 부하 증감에 자동 대응하는 시스템입니다. 
#### CreateAutoScaleGroup 
##### Create Auto Scaling group버튼을 클릭합니다.
##### Create a new launch configuration을 선택하고 Next Step을 클릭합니다.
##### 앞서 작성한 AMI 를 선택합니다.


### 마치면서 
