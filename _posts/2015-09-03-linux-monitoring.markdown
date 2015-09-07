---
layout: post
title:  "리눅스 명령어를 이용한 시스템 모니터링 하기"
date:   2015-09-03 17:10:00
author: 김지혜
---

시스템 성능 측정을 위한 항목에는 CPU, Memory, Disk, Traffic 등이 있습니다.  
리눅스 환경에서 이런 리소스들을 확인 할 수 있는 다양한 명령어들을 지원하고 있는데요.  
각각의 명령어들을 통해 시스템을 모니터링 하는 방법에 대해 알아봅시다.  


## uname
시스템과 커널의 정보를 확인할 수 있습니다.   
저는 모든 정보를 확인하기 위하여 -a 옵션을 사용하였습니다.

![alt text](/assets/images/uname.png "uname")

보여지는 순서는   
Linux . localhost . 3.13.0-24-generic . #47-Ubuntu SMP Fri May 2 23:30:00 UTC 2014 . x86_64 . x86_64 . x86_64 . GNU/Linux  
**[커널명]**   **[호스트명]**   **[릴리즈정보]**         **[커널버전]**   **[머신하드웨어이름]**   **[프로세서종료]**   **[하드웨어플랫폼]**   **[운영체제]**    
입니다.

##### 참고 >>

{% highlight bash %}
$ uname --help
  -a, --all  
  -s, --kernel-name        print the kernel name  
  -n, --nodename           print the network node hostname  
  -r, --kernel-release     print the kernel release  
  -v, -kernel-version 커널 버전 출력  
  -m, --machine 머신 하드웨어 이름 출력  
  -p, --processor 프로세서 종류 또는 "unknown" 출력  
  -i,- -hardware-platform 하드웨어 플랫폼 또는 "unknown" 출력  
  -o, --operating-system 운영 체제 출력  
{% endhighlight %}


## ifconfig

시스템에 설정된 네트워크 인터페이스의 상태를 확인&변경 할 수 있습니다.

![alt text](/assets/images/ifconfig.png "ifconfig")

- **Eth0, Eth1** : 흔히 랜카드라고 불리는 유선 네트워크 인터페이스 입니다.  따라서 저는 랜카드가 2개!  
- **Lo** : 루프백 인터페이스로 자기자신과 통신하는데 사용하는 가상 장치입니다. IP가 127.0.0.1이라고 보이시나요?

IP주소는 서버에 하나씩 부여되는 것이 아니라 네트워크 인터페이스에 할당되기 때문에 
각 네트워크 인터페이스마다 다른 IP주소를 가지고 있습니다.


- **HWaddr** : 네트워크 인터페이스의 하드웨어 주소(MAC Address)
- **inetaddr** : 네트워크 인터페이스에 할당된 IP 주소
- **Bcast** : 브로드캐스트 주소
- **Mask** : 넷마스크
- **MTU** : 네트워크 최대 전송 단위(Maxium Transfer Unit)
- **RX packets** : 받은 패킷 정보
- **TX packets**: 보낸 패킷 정보
- **collision** : 충돌된 패킷 수
- **Interrupt** : 네트워크 인터페이스가 사용하는 인터럽트 번호


##### 참고 >>

`Ifconfig` 명령어로는 private ip밖에 확인되지 않습니다.  
공인아이피(Public IP) 확인하는 방법이 있을까요?  
Curl을 설치후에 확인해 보세요. 

{% highlight bash %}
$ curl ifconfig.me 
{% endhighlight %}


## top

윈도우의 작업관리자와 비슷한 기능을 하는 명령어입니다.  
프로세스 작업 명령어로, 시스템 프로세스들의 CPU/Memory 점유율을 실시간으로 볼 수 있습니다.  

현재 몇 개의 프로세스가 있는지, CPU의 자세한 사용률은 어떻게 되는지,  
Memory와 Swap은 얼마나 사용하고 있는지를 확인할 수 있습니다.

![alt text](/assets/images/top.png "top")

##### CPU

- **us** : 사용자가 사용중인 사용률
- **sy** : 시스템이 사용중인 사용률
- **ni** : 프로세스 우선순위를 기반으로 사용되는 사용률(사용자 공간에서 사용됨)
- **id** : 아무일도 하지 않는 여유율
- **wa** : 입출력을 기다리는 프로세스 사용률 
- **hi** : 하드웨어 인터럽트 사용률
- **si** : 소프트웨어 인터럽트 사용률
- **st** : 가상화 환경에서 손실률

User값이 높다면, 사용자 코드를 수행하는데 시간이 오래 걸린다면 내부적으로 계산을 많이 하고 있다는 것입니다.  
System값이 높다면, 시스템에 의해 사용되고 있는 시간이 오래 걸린다면 프로세스들이 시스템 호출 또는 I/O가 많다고 할 수 있습니다.  
idle의 값이 항상 0이라면 CPU를 100% 사용하고 있다는 것을 의미합니다. CPU를 계속사용하고 있는 프로세스를 찾아 적절하게 대응 할 필요가 있습니다.


##### Process

- **PID** : 프로세스 ID
- **USER** : 프로세스를 실행 시킨 사용자 ID
- **PR** : 프로세스의 우선순위
- **NI** : NICE 값, 마이너스를 가지는 값이 우선순위가 높음
- **VIRT** : 가상 메모리의 사용량(SWAP+RES)
- **RES** : 현재 페이지가 상주하고 있는 크기
- **SHR** : 분할된 페이지
- **S** : 프로세스의 상태
- **%CPU** : 프로세스가 사용하는 CPU의 사용율
- **%MEM** : 프로세스가 사용하는 메모리의 사용율
- **TIME+** : 프로세스가 CPU를 사용한 시간
- **COMMAND** : 실행된 명령어



##### 참고 >>

프로세스목록을 원하는 특정 기준에 따라 정렬할 수 있을까요?
top 실행화면에서 Shift 키와 영문자를 누르면 프로세스의 정렬 기준을 변경할 수 있습니다. 

{% highlight bash %}
SHIFT + M  메모리 사용률 정렬 
SHIFT + N  PID 기준 정렬 
SHIFT + P  CPU 사용률 정렬
SHIFT + T  실행시간 기준 정렬
SHIFT + R  정렬 기준변경 (오름차순인 경우 내림차순으로, 내림차순인 경우 오름차순으로 변경)
{% endhighlight %}


## free

메모리에 대한 정보를 확인 할 수 있습니다.
저는 Memory와 Swap에 대한 값의 총 합을 확인 하기 위하여 -t 옵션을 주었습니다.

![alt text](/assets/images/free.png "free")

Mem는 실제 메모리를, Swap은 일종의 가상메모리입니다.  
Buffers는  메모리에 상주한 디스크 블록의 크기 즉 시스템이 한가할 때 디스크에 쓰려고 남겨둔 영역입니다.  
Chaced는 기존에 실행된 프로그램들이 사용했던 메모리로 실행 중이거나 새로 시작될 프로그램들이 필요할 때 빠르게 재 사용할 수 있는 메모리 영역입니다.  

Buffers와 Chaced 둘 다 Free영역의 일부이므로 실제로는 2번째 줄에 보이는 364812가 실 여유메모리 즉 사용자가 사용가능한 메모리 입니다.  
실제 사용률은 1번째 줄에 보이는 used - (buffers + cached)인 2번째 줄에 보이는 used값 인 것이지요.


##### 참고 >>

*옵션을 이용하여 단위 별로 출력하기*

{% highlight bash %}
$ free -b  # or --bytes   show output in bytes  
$ free -k  # or --kilo    show output in kilobytes  
$ free -m  # or --mega    show output in megabytes  
$ free -g  # or --giga    show output in gigabytes  
{% endhighlight %}


## vmstat

시스템 작업, 하드웨어 및 시스템 정보를 확인 할 수 있습니다.
메모리, 페이징, 블록장치의 I/O, CPU상태 등을 볼 수 있습니다.

![alt text](/assets/images/vmstat.png "vmstat")

Procs => 메모리가 읽어야 할 데이터의 수로 5이하가 좋다.

- **r** : 현재 실행중인 프로세스의 수
- **b** : 인터럽트가 불가능한 sleep 상태에 있는 프로세스의 수 (I/O 처리를 하는 동안 블럭 처리된 프로세스)
만약 b의 수치가 높은 경우라면 CPU가 계속 대기상태로 있다는 의미이므로 디스크I/O를 확인해 볼 필요가 있습니다.


Swap => 메모리가 가득 차 작업을 할 수 없을 때, 대기중인 작업을 넣어 두는 곳.

- **si(swap in)** : 사용되고 있는 디스크메모리(스왑공간에 있는 데이터)가 해제되는 양(per sec)
- **so(swap out)** : 물리적 메모리가 부족할 경우 디스크로부터 사용되는 메모리 양(per sec)
이 때, swap out이 지속적으로 발생한다면 메모리 부족을 의심 해 볼 수 있습니다. swap out값이 증가하면 메모리가 부족하다는 의미이므로 메모리를 늘려야 할 것 입니다. Swap out값은 0에 가까워야 좋고 초당 10블럭이하가 좋습니다.  그러나 swap필드의 값이 높다고 해도 free 메모리에 여유가 있다면 메모리가 부족한 것은 아니랍니다.

-s 옵션을 주면 메모리 통계 항목들을 확인할 수 있습니다.

![alt text](/assets/images/vmstat-s.png "vmstat-s")

##### 참고 >>

*실시간으로 메모리 상태를 확인할 수 있습니다.*
방법은? vmstat   [delay [count]  ]  

{% highlight bash %}
$ vmstat 3 5  # 3초 간격으로 모니터링 정보를 5개 출력하고 끝내라.
{% endhighlight %}



## iostat

평균 CPU부하 와 디스크 I/O의 세부적인 내용을 확인 할 수 있습니다.

![alt text](/assets/images/iostat.png "iostat")

- **tps** :  디바이스에 초당 전송 요청 건수
- **kB_read/s** : 디바이스에서 초당 읽은 데이터 블록 단위
- **kB_wrtn/s** :  디바이스에서 초당 쓴 데이터 블록 단위
- **kB_read** :  디바이스에서 지정한 간격 동안 읽은 블록 수
- **kB_wrtn** :  디바이스에서 지정한 간격 동안 쓴 전체 블록 수


더 자세한 정보를 확인하기 위해 -x옵션을 사용할 수 있습니다.

![alt text](/assets/images/iostat-x.png "iostat-x")

##### 참고 >>

*실시간으로 디스크 상태를 확인할 수 있습니다.*
방법은? iostat  [delay [count]  ]  

{% highlight bash %}
$ iostat 3 5  # 3초 간격으로 모니터링 정보를 5개 출력하고 끝내라.
{% endhighlight %}


## netstat

현재 시스템에 연결된 네트워크 상태, 라우팅테이블, 인터페이스 상태 등을 볼 수 있습니다.

![alt text](/assets/images/netstat.png "netstat")

2개의 영역으로 나누어져 보여집니다.  
Active Internet connections : TCP, UDP, raw로 연결 된 목록만 보여집니다.  
Active UNIX domain sockets : 도메인소켓으로 연결 된 목록만 보여집니다.  

- **-n** : 호스트명, 포트명을 lookup하지 않고(도메인으로 보이지 않고) IP, Port번호를 보여준다.
- **-a** : 모든 네트워크상태를 보여준다. 
- **-t** : TCP 프로토콜만 보여준다. 
- **-u** : UDP 프로토콜만 보여준다. 
- **-p** : 해당 포트를 사용하는 프로그램과 프로세스ID(PID)를 보여준다. 
- **-r** : 라우팅 테이블 출력 
- **-s** : 프로토콜 별(IP, ICMP, TCP, UDP 등)로 통계를 보여준다 
- **-c** : 연속적으로 상태를 보여준다. 
- **-l** : 대기중인 소켓 목록을 보여준다.

State

- **공백** : 연결되어 있지 않음
- **FREE** : socket은 존재하지만 할당되어 있지 않다.
- **LISTENING** : 연결요청에 대한 응답준비가 되어 있는 상태
- **CONNECTING** : 연결이 막 이루어진 상태.
- **DISCONNECTING** : 연결해제 되고 있는 상태
- **UNKNOWN** : 알 수 없는 연결, 알려지지 않은 연결 상태
- **LISTEN** : 연결가능하도록 daemon이 떠있으며 연결이 가능한 상태 
- **SYS-SENT** : 연결을 요청한 상태.
- **SYN_RECEIVED** : 연결요구에 응답 후 확인메세지 대기중인 상태
- **ESTABLISHED** : 연결이 완료된 상태
- **FIN-WAIT1 : 소켓이 닫히고 연결이 종료되고 있는 상태
- **FIN-WAIT2 : 로컬이 원격으로부터 연결 종료 요구를 기다리는 상태
- **CLOSE-WAIT : 종료 대기 중
- **CLOSING** : 전송된 메세지가 유실되었음
- **TIME-WAIT** : 연결종료 후 한동안 유지되어 있음
- **CLOSED** : 연결이 완전히 종료



##### 참고 >>

*각 옵션들을 어떻게 사용하냐에 따라 다른 정보를 확인 할 수 있는데
유용하게 쓰이는 옵션들 알아 두시면 좋을 것 같습니다.*

{% highlight bash %}
$ netstat -r  # 서버의 라우팅 테이블 출력   
$ netstat -na --ip  # tcp/udp의 세션 목록 표시   
$ netstat -na | grep ESTABLISHED | wc -l  # 활성화된 세션수 확인   
$ netstat -nap | grep :80 | grep ESTABLISHED | wc -l # 80포트 동시 접속자수   
$ netstat -nltp # LISTEN 중인 포트 정보 표시   
{% endhighlight %}


## df

현재 디스크의 전체 용량 및 남은 용량을 확인 할 수 있습니다.

![alt text](/assets/images/df.png "df")

##### 참고 >>

{% highlight bash %}
$ df --help
  -h : 용량을 읽기 쉽게 단위를 계산하여 보여준다.
  -T : 파일 시스템 종류와 함께 디스크 정보를 보여준다.
  --total : 전체 용량을 보여준다.
{% endhighlight %}


## 마치며

지금까지 시스템모니터링을 알아볼 수 있는 명령어들을 살펴보았습니다. 
간단하게만 살펴보았는데도 명령어와 옵션값들이 많고 필요에 따라 적절한 옵션값을 이용하여 모니터링 하는 것이 쉬워 보이지는 않습니다. 이 때 필요한 것이 Whatap! 아닐까요? 웹&앱 기반 모니터링 툴로써 위에서 확인했던 다양한 성능지표(CPU/Memory/Disk/Traffic/Process/Log/DB까지!)들을 언제 어디서나 손쉽게 확인할 수 있습니다. 시스템에 이상이 생겼을 때 알림을 보내 사용자가 바로 인지할 수 있고 따라서 빠르게 대응 할 수 있습니다. 직관적이며 사용하기 편한 인터페이스로, 시스템 모니터링을 원한다면 Whatap을 사용해 보세요.
