---
title: RHEL의 SAP 용 Azure 대규모 인스턴스 고가용성
description: Red Hat Enterprise Linux에서 Pacemaker 클러스터를 사용 하 여 SAP HANA database 장애 조치 (failover)를 자동화 하는 방법을 알아봅니다.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 99e9994d01e4579bf6ef2e369e0fe85c48af52ef
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182437"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>RHEL의 SAP 용 Azure 대규모 인스턴스 고가용성

이 문서에서는 RHEL 7.6에서 Pacemaker 클러스터를 구성 하 여 SAP HANA 데이터베이스 장애 조치 (failover)를 자동화 하는 방법에 대해 알아봅니다. 이 가이드의 단계를 완료 하려면 Linux, SAP HANA 및 Pacemaker에 대해 잘 알고 있어야 합니다.

다음 표에는이 문서 전체에서 사용 되는 호스트 이름이 나와 있습니다. 문서의 코드 블록은 실행 해야 하는 명령 및 해당 명령의 출력을 보여 줍니다. 각 명령에서 참조 되는 노드에 대 한 주의를 기울여야 합니다.

| 유형 | 호스트 이름 | 노드|
|-------|-------------|------|
|기본 호스트|`sollabdsm35`|노드 1|
|보조 호스트|`sollabdsm36`|노드 2|

## <a name="configure-your-pacemaker-cluster"></a>Pacemaker 클러스터 구성


클러스터 구성을 시작 하기 전에 SSH 키 교환을 설정 하 여 노드 간에 트러스트를 설정 합니다.

1. 다음 명령을 사용 하 여 두 노드에서 동일 하 게 만들 수 `/etc/hosts` 있습니다.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    0.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    0.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    0.20.251.150 sollabdsm36-st

    10.20.251.151 sollabdsm35-st

    

    10.20.252.151 sollabdsm36-back

    10.20.252.150 sollabdsm35-back

    

    10.20.253.151 sollabdsm36-node

    10.20.253.150 sollabdsm35-node

    ```

2.  SSH 키를 만들고 교환 합니다.
    1. Ssh 키를 생성 합니다.

       ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
       ```
    2. 암호 없는 ssh에 대 한 키를 다른 호스트로 복사 합니다.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  두 노드에서 selinux를 사용 하지 않도록 설정 합니다.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. 서버를 다시 부팅 한 후 다음 명령을 사용 하 여 selinux의 상태를 확인 합니다.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. NTP (네트워크 시간 프로토콜)를 구성 합니다. 두 클러스터 노드 모두에 대 한 시간과 표준 시간대가 일치 해야 합니다. 다음 명령을 사용 하 여 `chrony.conf` 파일의 내용을 열고 확인 합니다.
    1. 다음 콘텐츠를 구성 파일에 추가 해야 합니다. 사용자 환경에 따라 실제 값을 변경 합니다.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Chrony 서비스를 사용 하도록 설정 합니다. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. 시스템 업데이트
    1. 먼저 SBD 장치 설치를 시작 하기 전에 시스템에 최신 업데이트를 설치 합니다.
    1. 시스템의 전체 업데이트를 원하지 않는 경우이 권장 되더라도 최소한 다음 패키지를 업데이트 합니다.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```
 

7. SAP HANA 및 RHEL 리포지토리를 설치 합니다.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. 모든 노드에 Pacemaker, SBD, OpenIPMI, ipmitools 및 fencing_sbd 도구를 설치 합니다.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitools
    ```

  ## <a name="configure-watchdog"></a>Watchdog 구성

이 섹션에서는 감시를 구성 하는 방법에 대해 알아봅니다. 이 섹션에서는 `sollabdsm35` `sollabdsm36` 이 문서의 시작 부분에서 참조 되는 동일한 두 개의 호스트 및를 사용 합니다.

1. Watchdog 데몬이 시스템에서 실행 되 고 있지 않은지 확인 합니다.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. 설치 중에 설치 되는 기본 Linux watchdog은 UCS 및 HPE SDFlex 시스템에서 지원 되지 않는 iTCO watchdog입니다. 따라서이 watchdog을 사용 하지 않도록 설정 해야 합니다.
    1. 잘못 된 watchdog이 설치 되 고 시스템에 로드 됩니다.
       ```
   
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. 환경에서 잘못 된 드라이버를 언로드합니다.
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. 다음 시스템 부팅 시 드라이버가 로드 되지 않도록 하려면 드라이버가 차단 되어야 합니다. ITCO 모듈을 차단 목록 파일의 끝에 다음을 추가 합니다 `50-blacklist.conf` .
       ```
   
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. 보조 호스트에 파일을 복사 합니다.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Ipmi 서비스가 시작 되었는지 테스트 합니다. IPMI 타이머가 실행 되지 않는 것이 중요 합니다. SBD pacemaker 서비스에서 타이머 관리를 수행 합니다.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. 기본적으로 필요한 장치는 생성 되지 않습니다.

    ```
    No watchdog device was created

    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. IPMI 감시를 구성 합니다.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. 보조에 watchdog 구성 파일을 복사 합니다.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Ipmi 서비스를 사용 하도록 설정 하 고 시작 합니다.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     이제 IPMI 서비스가 시작 되 고 장치/dev/cerera가 만들어지지만 타이머가 계속 중지 됩니다. 나중에 SBD는 감시 재설정을 관리 하 고 IPMI 타이머를 사용 하도록 설정 합니다.
7.  /Dev/sdea가 존재 하지만 사용 중인지 확인 합니다.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>SBD 구성
이 섹션에서는 SBD를 구성 하는 방법에 대해 알아봅니다. 이 섹션에서는 `sollabdsm35` `sollabdsm36` 이 문서의 시작 부분에서 참조 되는 동일한 두 개의 호스트 및를 사용 합니다.

1.  두 노드에서 iSCSI 또는 FC 디스크가 표시 되는지 확인 합니다. 이 예제에서는 FC 기반 SBD 장치를 사용 합니다. SBD fence에 대 한 자세한 내용은 [참조 설명서](http://www.linux-ha.org/wiki/SBD_Fencing)를 참조 하세요.
2.  LUN ID는 모든 노드에서 동일 해야 합니다.
  
3.  Sbd 장치에 대 한 다중 경로 상태를 확인 합니다.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  SBD 디스크를 만들고 클러스터 기본 fence를 설정 합니다. 첫 번째 노드에서이 단계를 실행 해야 합니다.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  SBD config를 노드 2로 복사 합니다.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  SBD 디스크가 두 노드에서 모두 표시 되는지 확인 합니다.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  SBD config 파일에 SBD 장치를 추가 합니다.

    ```
    \# SBD_DEVICE specifies the devices to use for exchanging sbd messages

    \# and to monitor. If specifying more than one path, use ";" as

    \# separator.

    \#

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    \## Type: yesno
     Default: yes
     \# Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>클러스터 초기화
이 섹션에서는 클러스터를 초기화 합니다. 이 섹션에서는 `sollabdsm35` `sollabdsm36` 이 문서의 시작 부분에서 참조 되는 동일한 두 개의 호스트 및를 사용 합니다.

1.  클러스터 사용자 암호 (모든 노드)를 설정 합니다.
    ```
    passwd hacluster
    ```
2.  모든 시스템에서 PC를 시작 합니다.
    ```
    systemctl enable pcsd
    ```
  

3.  방화벽을 중지 하 고 (모든 노드)에서 사용 하지 않도록 설정 합니다.
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Pcsd 서비스를 시작 합니다.
    ```
    systemctl start pcsd
    ```
  
  

5.  노드 1 에서만 클러스터 인증을 실행 합니다.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36



        Username: hacluster

            Password:

            sollabdsm35.localdomain: Authorized

            sollabdsm36.localdomain: Authorized

     ``` 

6.  클러스터를 만듭니다.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  클러스터 상태를 확인 합니다.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. 한 노드에서 클러스터를 조인 하지 않는 경우 방화벽이 아직 실행 중인지 확인 합니다.

  

9. SBD 장치 만들기 및 사용
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  

10. 클러스터를 중지 하 고 모든 노드에서 클러스터 서비스를 다시 시작 합니다.

    ```
    pcs cluster stop --all
    ```


11. 모든 노드에서 클러스터 서비스를 다시 시작 합니다.

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync에서 SBD 서비스를 시작 해야 합니다.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. 클러스터를 다시 시작 합니다 (pcsd에서 자동으로 시작 되지 않은 경우).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Stonith를 설정 합니다.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. 이제 하나의 리소스를 사용 하 여 새 클러스터 상태를 확인 합니다.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. 이제 IPMI 타이머를 실행 해야 하 고 sbd에서/sv/chp 장치를 열어야 합니다.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm351 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. SBD 상태를 확인 합니다.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. 커널을 충돌 하 여 SBD 펜스를 테스트 합니다.

    * 커널 충돌을 트리거합니다.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * 실행할 두 번째 테스트는 PC 명령을 사용 하 여 노드를 fence 하는 것입니다.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. SAP HANA 클러스터링의 나머지 부분에서는를 설정 하 여 STONITH를 사용 하지 않도록 설정할 수 있습니다.

   * pc 속성 집합 `stonith-enabled=false`
   * 생산적으로 사용 하려면이 매개 변수를 true로 설정 해야 합니다. 이 매개 변수를 true로 설정 하지 않으면 클러스터가 지원 되지 않습니다.
   * pc 속성 집합 `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>클러스터에 HANA 통합

이 섹션에서는 HANA를 클러스터에 통합 합니다. 이 섹션에서는 `sollabdsm35` `sollabdsm36` 이 문서의 시작 부분에서 참조 되는 동일한 두 개의 호스트 및를 사용 합니다.

HANA를 통합 하는 두 가지 옵션이 있습니다. 첫 번째 옵션은 보조 시스템을 사용 하 여 QAS 시스템을 실행할 수 있는 비용 최적화 솔루션입니다. 클러스터 소프트웨어, 운영 체제 또는 HANA에 대 한 업데이트를 테스트 하는 시스템은 없지만 구성 업데이트로 인해 PRD 시스템의 계획 되지 않은 가동 중지 시간이 발생할 수 있으므로이 방법은 권장 되지 않습니다. 또한 보조 시스템에서 PRD 시스템을 활성화 해야 하는 경우 QAS를 보조 노드에서 종료 해야 합니다. 두 번째 옵션은 한 클러스터에 QAS system을 설치 하 고 PRD에 대해 두 번째 클러스터를 사용 하는 것입니다. 이 옵션을 사용 하면 프로덕션 환경에 배치 하기 전에 모든 구성 요소를 테스트할 수도 있습니다. 이 문서에서는 두 번째 옵션을 구성 하는 방법을 보여 줍니다.


* 이 프로세스는 페이지에 대 한 RHEL 설명의 빌드입니다.

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>HSR 구성에 따라야 하는 단계

1.  다음은 node1 (주)에 대해 실행할 작업입니다.
    1. 데이터베이스 로그 모드가 normal로 설정 되었는지 확인 합니다.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p SAPhana10 -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA 시스템 복제는 초기 백업이 수행 된 후에만 작동 합니다. 다음 명령은 디렉터리에 초기 백업을 만듭니다 `/tmp/` . 데이터베이스에 대 한 적절 한 백업 파일 시스템을 선택 합니다. 
       ```
       * hdbsql -i 00 -u system -p SAPhana10 "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```
    

    3. 이 데이터베이스의 모든 데이터베이스 컨테이너를 백업 합니다.
       ```
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"
   
       
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. 원본 시스템에서 HSR 프로세스를 사용 하도록 설정 합니다.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. 주 시스템의 상태를 확인 합니다.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. 이러한 작업은 노드 2 (보조)에서 실행할 작업입니다.
     1. 데이터베이스를 중지 합니다.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. SAP HANA 2.0에만 해당 SAP HANA 시스템 `PKI SSFS_HR2.KEY` 및 파일을 `SSFS_HR2.DAT` 주 노드에서 보조 노드로 복사 합니다.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. 보조를 복제 사이트로 사용 하도록 설정 합니다.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. 데이터베이스를 시작합니다.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. 데이터베이스 상태를 확인 합니다.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. 복제 상태에 대 한 자세한 정보를 얻을 수도 있습니다.
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>로그 복제 모드 설명

로그 복제 모드에 대 한 자세한 내용은 [공식 SAP 설명서](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/c039a1a5b8824ecfa754b55e0caffc01.html)를 참조 하세요.
  

#### <a name="network-setup-for-hana-system-replication"></a>HANA 시스템 복제를 위한 네트워크 설정


복제 트래픽이 복제에 대 한 올바른 VLAN을 사용 하는지 확인 하려면에서 적절 하 게 구성 해야 합니다 `global.ini` . 이 단계를 건너뛰면 HANA에서 복제에 액세스 VLAN을 사용 합니다 .이는 원치 않을 수 있습니다.


다음 예에서는 보조 사이트로의 시스템 복제에 대 한 호스트 이름 확인 구성을 보여 줍니다. 세 가지 고유한 네트워크를 식별할 수 있습니다.

* 10.0.1. * 범위의 주소가 있는 공용 네트워크

* 각 사이트에서 호스트 간의 내부 SAP HANA 통신을 위한 네트워크: 192.168.1. *

* 시스템 복제용 전용 네트워크: 10.5.1. *

첫 번째 예제에서는 `[system_replication_communication]listeninterface` 매개 변수가로 설정 `.global` 되 고 인접 복제 사이트의 호스트만 지정 됩니다.

다음 예에서는 `[system_replication_communication]listeninterface` 매개 변수가로 설정 `.internal` 되 고 두 사이트의 모든 호스트가 지정 됩니다.

  

### <a name="source-sap-ag-sap-hana-hrs-networking"></a>원본 SAP AG SAP HANA 시간 네트워킹

  

시스템 복제의 경우 파일을 편집할 필요가 없습니다 `/etc/hosts` . 내부 (' virtual ') 호스트 이름을 파일의 IP 주소로 매핑하여 `global.ini` 시스템 복제용 전용 네트워크를 만들어야 합니다. 이에 대 한 구문은 다음과 같습니다.

global.ini

[system_replication_hostname_resolution]

<ip address_site>=<내부 호스트-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Pacemaker 클러스터에서 SAP HANA 구성
이 섹션에서는 Pacemaker 클러스터에 SAP HANA를 구성 하는 방법에 대해 알아봅니다. 이 섹션에서는 `sollabdsm35` `sollabdsm36` 이 문서의 시작 부분에서 참조 되는 동일한 두 개의 호스트 및를 사용 합니다.

다음 필수 구성 요소를 충족 하는지 확인 합니다.  

* Pacemaker 클러스터는 설명서에 따라 구성 되며 적절 하 고 작동 하는 펜스가 있습니다.

* 클러스터에서 시작 및 중지를 관리 하므로 모든 클러스터 노드에서 부팅 시 시작 SAP HANA 사용 하지 않도록 설정 됩니다.

* SAP의 도구를 사용 하는 SAP HANA 시스템 복제 및 인수가 클러스터 노드 간에 제대로 작동 합니다.

* 두 클러스터 노드의 클러스터에서 사용할 수 있는 모니터링 계정이 SAP HANA 포함 되어 있습니다.

* 두 노드 모두 ' 고가용성 ' 및 ' RHEL for SAP HANA ' (RHEL 6, RHEL 7) 채널을 구독 합니다.

  

* 일반적으로 CIB는 pc 셸에서 자동으로 업데이트 되므로 모든 pc 명령을 노드에서 실행 하세요.

* [쿼럼 정책에 대 한 추가 정보](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>구성 단계 
1. Pc를 구성 합니다.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Corosync를 구성 합니다.
    ```
    https://access.redhat.com/solutions/1293523 --> quorum information RHEL7

    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

1.  복제 된 SAPHanaTopology 리소스를 만듭니다.
    ```
    pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1 interleave=true
    SAPHanaTopology resource is gathering status and configuration of SAP
    HANA System Replication on each node. SAPHanaTopology requires
    following attributes to be configured.



        Attribute Name Description

        SID SAP System Identifier (SID) of SAP HANA installation. Must be
    same for all nodes.

    InstanceNumber 2-digit SAP Instance identifier.
    pcs resource show SAPHanaTopology_HR2_00-clone

    Clone: SAPHanaTopology_HR2_00-clone

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true

        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
    type=SAPHanaTopology)

        Attributes: InstanceNumber=00 SID=HR2

        Operations: monitor interval=60 timeout=60
    (SAPHanaTopology_HR2_00-monitor-interval-60)

        start interval=0s timeout=180
    (SAPHanaTopology_HR2_00-start-interval-0s)

        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)

    ```

3.  Primary/Secondary SAPHana 리소스를 만듭니다.

    ```
    SAPHana resource is responsible for starting, stopping and relocating the SAP HANA database. This resource must be run as a Primary/    Secondary cluster resource. The resource has the following attributes.

    

    Attribute Name Required? Default value Description

    SID Yes None SAP System Identifier (SID) of SAP HANA installation. Must be same for all nodes.

    InstanceNumber Yes none 2-digit SAP Instance identifier.

    PREFER_SITE_TAKEOVER

    no yes Should cluster prefer to switchover to secondary instance instead of restarting primary locally? ("no": Do prefer restart locally;   "yes": Do prefer takeover to remote site)

    AUTOMATED_REGISTER no false Should the former SAP HANA primary be registered as secondary after takeover and DUPLICATE_PRIMARY_TIMEOUT?     ("false": no, manual intervention will be needed; "true": yes, the former primary will be registered by resource agent as secondary)

    DUPLICATE_PRIMARY_TIMEOUT no 7200 Time difference (in seconds) needed between primary time stamps, if a dual-primary situation occurs. If   the time difference is less than the time gap, then the cluster holds one or both instances in a "WAITING" status. This is to give an   admin a chance to react on a failover. A failed former primary will be registered after the time difference is passed. After this   registration to the new primary all data will be overwritten by the system replication.
    ```
  

5.  HANA 리소스를 만듭니다.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary



    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  가상 IP 주소 리소스를 만듭니다.

    ```
    Cluster will contain Virtual IP address in order to reach the Primary instance of SAP HANA. Below is example command to create IPaddr2  resource with IP 10.7.0.84/24

    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  제약 조건을 만듭니다.

    ```
    For correct operation we need to ensure that SAPHanaTopology resources are started before starting the SAPHana resources and also that  the virtual IP address is present on the node where the Primary resource of SAPHana is running. To achieve this, the following 2    constraints need to be created.

    pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
    pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
    ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>SAPHana 리소스를 다른 노드로 수동 이동 테스트

#### <a name="sap-hana-takeover-by-cluster"></a>(클러스터에서 SAP Hana 인수)


SAPHana 리소스를 한 노드에서 다른 노드로 이동 하는 것을 테스트 하려면 아래 명령을 사용 합니다. `--primary`SAPHana 리소스가 내부적으로 작동 하는 방식 때문에 다음 명령을 실행 하는 경우에는이 옵션을 사용 하면 안 됩니다.
```pcs resource move SAPHana_HR2_00-primary```

각 pc 리소스 이동 명령을 호출한 후 클러스터는 리소스의 이동을 얻기 위한 위치 제약 조건을 만듭니다. 이후 자동 장애 조치 (failover)를 허용 하려면 이러한 제약 조건을 제거 해야 합니다.
이러한 항목을 제거 하려면 다음 명령을 사용 하면 됩니다.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* HANA 확인으로 로그인 합니다.

  * 강등 한 호스트:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * 승격 된 호스트:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

옵션을 사용 `AUTOMATED_REGISTER=false` 하면 앞뒤로 전환할 수 없습니다.

이 옵션이 false로 설정 된 경우 노드를 다시 등록 해야 합니다.

  
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```
  

이제 기본 노드 2가 보조 호스트 역할을 합니다.

강등 된 호스트의 등록을 자동화 하려면이 옵션을 true로 설정 하는 것이 좋습니다.

  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true

pcs cluster node clear node1
```
