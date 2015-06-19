<properties
	pageTitle="부하 분산 집합을 사용하여 Linux에서 MySQL 클러스터화"
	description="MySQL을 예로 사용하여 Azure에서 부하 분산된 고가용성 Linux 클러스터를 설정하는 패턴을 보여 주는 문서입니다."
	services="virtual-machines"
	documentationCenter=""
	authors="bureado"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2015" 
	ms.author="jparrel"/>

# 부하 분산 집합을 사용하여 Linux에서 MySQL 클러스터화

* [준비](#getting-ready)
* [클러스터 설정](#setting-up-the-cluster)
* [MySQL 설정](#setting-up-mysql)
* [Corosync 설정](#setting-up-corosync)
* [Pacemaker 설정](#setting-up-pacemaker)
* [테스트](#testing)
* [STONITH](#stonith)
* [제한 사항](#limitations)

## 소개

이 문서는 Microsoft Azure에서 고가용성 Linux 기반 서비스를 배포하고 MySQL Server 고가용성 기능을 기반으로 활용하기 위한 다양한 접근 방법을 알아보고 보여 주기 위한 것입니다. 이 접근 방법을 보여 주는 비디오는 [채널 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)(영문)에서 사용할 수 있습니다.

여기서는 DRBD, Corosync 및 Pacemaker를 기반으로 하는 비공유 2노드 단일 마스터 MySQL 고가용성 솔루션에 대해 설명합니다. MySQL에서는 한 번에 하나의 노드만 실행됩니다. DRBD 리소스를 쓰고 이 리소스에서 읽는 작업도 한 번에 하나의 노드로 제한됩니다.

Microsoft Azure의 부하 분산 집합을 사용하여 라운드로빈 기능과 끝점 감지, VIP의 끝점 검색, 제거와 안정적 복구 기능을 모두 제공하므로 LVS와 같은 VIP 솔루션이 필요하지 않습니다. VIP는 처음 클라우드 서비스를 만들 때 Microsoft Azure에서 할당한 전역 라우팅 가능 IPv4 주소입니다.

NBD Cluster, Percona 및 Galera를 비롯하여 MySQL에 대해 사용 가능한 다른 아키텍처와 [VM Depot](http://vmdepot.msopentech.com)에 제공되는 VM으로 사용 가능한 하나 이상의 솔루션을 비롯한 미들웨어 솔루션도 여러 개 있습니다. 이러한 솔루션이 유니캐스트 및 멀티캐스트/브로드캐스트에서 복제 가능하고 공유 저장소나 다중 네트워크 인터페이스에 의존하지 않는 한, 이러한 시나리오는 Microsoft Azure에 배포하기 쉽습니다.

물론 이러한 클러스터링 아키텍처는 비슷한 방식으로 PostgreSQL 및 OpenLDAP와 같은 기타 제품으로 확장될 수 있습니다. 예를 들어 이러한 비공유 부하 분산 절차는 다중 마스터 OpenLDAP를 통해 성공적으로 테스트되었으며 이 과정을 채널 9 블로그에서 시청할 수 있습니다.

## 준비

2개 이상의 VM(이 예에서는 XS가 사용됨), 네트워크와 서브넷, 선호도 그룹과 가용성 집합을 만드는 데 사용할 수 있는 유효한 구독이 있는 Microsoft Azure 계정과, 클라우드 서비스와 동일한 지역에 새 VHD를 만든 후 Linux VM에 연결하는 능력이 필요합니다.

### 테스트된 환경

- Ubuntu 13.10
  - DRBD
  - MySQL Server
  - Corosync 및 Pacemaker

### 선호도 그룹

솔루션에 대한 선호도 그룹은 Azure 포털에 로그인한 후 설정으로 스크롤하여 새 선호도 그룹을 만드는 방식으로 만들어집니다. 나중에 만들어진 할당된 리소스는 이 선호도 그룹에 할당됩니다.

### 네트워크

새 네트워크가 만들어지고 네트워크 내에 서브넷이 만들어집니다. 내부에 /24 서브넷이 하나만 있는 10.10.10.0/24 네트워크를 선택했습니다.

### 가상 컴퓨터

첫 번째 Ubuntu 13.10 VM이 `hadb01`라는 Endorsed Ubuntu Gallery 이미지를 사용하여 만들어집니다. 새 클라우드 서비스가 hadb라는 프로세스에서 만들어집니다. 여기서는 리소스를 더 추가할 때 서비스가 갖게 되는 공유 부하 분산 특성을 나타내기 위해 이러한 이름을 지정했습니다. `hadb01`을 만드는 작업은 특별하지 않으며 포털을 사용해서 완료됩니다. SSH용 끝점이 자동으로 만들어지고 만든 네트워크가 선택됩니다. 또한 VM에 대해 새로운 가용성 집합을 만들도록 선택할 것입니다.

첫 번째 VM이 만들어지면(기술적으로 클라우드 서비스가 만들어질 때) 두 번째 VM, `hadb02`를 계속 만듭니다. 두 번째 VM의 경우 포털을 사용하여 갤러리의 Ubuntu 13.10 VM을 사용하지만 새 클라우드 서비스가 아닌 기존 클라우드 서비스 `hadb.cloudapp.net`을 사용하도록 선택할 것입니다. 네트워크 및 가용성 집합도 자동으로 선택됩니다. SSH 끝점도 만들어집니다.

두 VM이 만들어진 후에는 `hadb01`에 대한 SSH 포트(TCP 22)] 및 `hadb02`에 대한 SSH 포트(Azure에 의해 자동으로 할당)를 적어둡니다.

### 연결된 저장소

새 디스크를 두 VM에 연결하고 프로세스에서 새 5GB 디스크를 만듭니다. 이러한 디스크는 기본 운영 체제 디스크 용도로 사용될 경우 VHD 컨테이너에 호스트됩니다. 디스크가 만들어지고 연결되면 커널에 새 장치가 보이므로 Linux를 다시 시작할 필요가 없습니다(일반적으로 `/dev/sdc`, `dmesg`에서 출력 확인 가능)

각 VM에서 `cfdisk`를 사용하여 새 파티션(기본, Linux 파티션)을 계속 만들고 새 파티션 테이블을 씁니다. **이 파티션에는 파일 시스템을 만들지 않도록 합니다** .

## 클러스터 설치

두 Ubuntu VM에서 APT를 사용하여 Corosync, Pacemaker 및 DRBD를 설치해야 합니다. `apt-get`을 사용할 경우 다음을 실행합니다.

    sudo apt-get install corosync pacemaker drbd8-utils.

**지금은 MySQL을 설치하지 않도록 합니다**. Debian 및 Ubuntu 설치 스크립트는 `/var/lib/mysql`에서 MySQL 데이터 디렉터리를 초기화하지만 이 디렉터리는 DRBD 파일 시스템으로 대체되므로 이 작업은 나중에 수행하면 됩니다.

이제 `/sbin/ifconfig`를 사용하여 두 VM이 모두 10.10.10.0/24 서브넷의 주소를 사용하고 있는지와 서로를 이름으로 ping할 수 있는지도 확인해야 합니다. 원하는 경우 `ssh-keygen` 및 `ssh-copy-id`를 사용하여 두 VM이 암호를 요구하지 않고 SSH를 통해 통신할 수 있는지도 확인할 수 있습니다.

### DRBD 설치

기본 `/dev/sdc1` 파티션을 사용하여 ext3을 통해 포맷될 수 있고 기본 및 보조 노드 둘 다에서 사용될 수 있는 `/dev/drbd1` 리소스를 생성하는 DRBD 리소스를 만들어 보겠습니다. 이렇게 하려면 `/etc/drbd.d/r0.res`를 열고 다음 리소스 정의를 복사합니다. 이 작업을 두 VM에서 수행하세요.

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

이 작업을 수행한 후에 두 VM에서 `drbdadm`을 사용하여 리소스를 초기화합니다.

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

마지막으로 DRBD 리소스의 기본(`hadb01`) 강제 소유권(기본)에 대해 다음을 실행합니다.

    sudo drbdadm primary --force r0

두 VM에서 /proc/drbd(`sudo cat /proc/drbd`)의 내용을 검토할 경우 여기서 `hadb01`의 `Primary/Secondary`와 `hadb02`의 `Secondary/Primary`가 솔루션과 일치하는 것을 확인할 수 있습니다. 5GB 디스크는 추가 비용 없이 10.10.10.0/24 네트워크를 통해 고객과 동기화됩니다.

디스크가 동기화되면 `hadb01`에서 파일 시스템을 만들 수 있습니다. 테스트 목적으로, ext2를 사용했지만 다음 지침을 수행하면 ext3 파일 시스템이 만들어집니다.

    mkfs.ext3 /dev/drbd1

### DRBD 리소스 탑재

이제 `hadb01`에서 DRBD 리소스를 탑재할 준비가 되었습니다. Debian 및 파생 배포판은 `/var/lib/mysql`을 MySQL 데이터 디렉터리로 사용합니다. MySQL을 설치하지 않았으므로 이 디렉터리를 만든 후 DRBD 리소스를 탑재할 것입니다. `hadb01`에서:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## MySQL 설치

이제 `hadb01`에서 MySQL을 설치할 준비가 되었습니다.

    sudo apt-get install mysql-server

`hadb02`의 경우 두 가지 옵션이 있습니다. 지금 mysql-server를 설치하여 /var/lib/mysql을 만들고 새 데이터 디렉터리로 채운 다음 콘텐츠를 계속 제거합니다. `hadb02`에서:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

두 번째 옵션은 `hadb02`로 장애 조치(Failover)한 다음 여기에 mysql-server를 설치하는 것입니다(설치 스크립트는 기존 설치를 감지하지만 변경하지 않음)

`hadb01`에서:

    sudo drbdadm secondary –force r0

`hadb02`에서:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

지금 DRBD를 장애 조치(Failover)할 계획이 아닌 경우 첫 번째 옵션이 더 쉽지만 확실히 덜 깔끔합니다. 설정이 완료되면 MySQL 데이터베이스에 대해 작업을 시작할 수 있습니다. `hadb02`(또는 DRBD에 따라 활성 상태인 서버 중 하나)에서 다음을 실행합니다.

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.* TO root;

**경고**: 이 마지막 문은 이 표의 루트 사용자에 대한 인증을 사용할 수 없게 설정합니다. 이 문은 프로덕션급 GRANT 문으로 바꾼 후 예시 용도로만 포함해야 합니다.

또한 이 가이드의 목적에 따라 VM 외부에서 쿼리를 수행하려면 MySQL에 대해 네트워킹을 사용하도록 설정해야 합니다. 두 VM에서 `/etc/mysql/my.cnf`를 열고 `bind-address`로 이동한 후 127.0.0.1에서 0.0.0.0으로 바꿉니다. 이 파일을 저장한 후에 현재 기본 노드에 대해 `sudo service mysql restart`를 실행합니다.

### MySQL 부하 분산 집합 만들기

Azure 포털로 돌아가 `hadb01` VM으로 이동한 후 끝점으로 이동합니다. 새 끝점을 만들고 드롭다운에서 MySQL(TCP 3306)을 선택하고 *Create new load balanced set* 상자를 선택합니다. 부하 분산된 끝점을 `lb-mysql`로 지칭합니다. 대부분의 옵션을 그대로 두고 시간만 5(초, 최소한)로 줄입니다.

끝점이 만들어진 후에는 끝점 `hadb02`로 이동한 다음 새 끝점을 만듭니다. 그렇지만 이번에는 `lb-mysql`을 선택한 다음 드롭다운 메뉴에서 MySQL을 선택합니다. 이 단계를 위해 Azure CLI를 사용할 수도 있습니다.

현재 클러스터의 수동 작동에 필요한 모든 항목이 있습니다.

### 부하 분산된 집합 테스트

MySQL 클라이언트와 응용 프로그램(예: Azure 웹 사이트로 실행되는 phpMyAdmin)을 사용하여 외부 컴퓨터에서 테스트를 수행할 수 있습니다. 이 경우에는 다른 Linux 상자의 MySQL 명령줄 도구를 사용했습니다.

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### 수동 장애 조치(Failover)

MySQL을 종료하고, DRBD의 기본 노드를 전환한 다음 MySQL을 다시 시작하여 지금 장애 조치(Failover)를 시뮬레이션할 수 있습니다.

hadb01에서 다음을 실행합니다.

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

그런 후 hadb02에서 다음을 실행합니다.

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

일단 수동으로 장애 조치(Failover)를 수행한 후에는 원격 쿼리를 반복할 수 있습니다. 그러면 완벽하게 작동됩니다.

## Corosync 설치

Corosync는 Pacemaker 작동에 필요한 기본 클러스터 인프라입니다. Heartbeat v1 및 v2 사용자(및 Ultramonkey와 같은 기타 방법)에게 Corosync는 CRM 기능에서 분할된 것이지만 Pacemaker는 기능적인 측면에서 Hearbeat와 좀 더 비슷합니다.

Azure에서 나타나는 Corosync의 기본적인 제약 조건은 Corosync가 유니캐스트 통신보다는 브로드캐스트를 통한 멀티캐스트 통신 방식을 선호한다는 것입니다. 그렇지만 Microsoft Azure 네트워킹에서는 유니캐스트만 지원합니다.

다행히 Corosync에서 유니캐스트 모드가 지원되며, 유일한 실질적 제약 조건은 모든 노드가 서로 간에 *자연스럽게* 통신하지는 못하므로 노드의 IP 주소를 비롯하여 구성 파일에 노드를 정의해야 한다는 것입니다. 유니캐스트에 대한 Corosync 예제 파일을 사용하고 just change 바인딩 주소, 노드 목록 및 로깅 디렉터리만 변경하고(Ubuntu는 `/var/log/corosync`를 사용하지만 이 예제 파일은 `/var/log/cluster`를 사용함) 쿼럼 도구를 사용하도록 설정하면 됩니다.

**아래의 `transport: udpu` 지시문과 노드에 대해 수동으로 정의한 IP 주소를 적어두세요**.

두 노드에 대한 `/etc/corosync/corosync.conf`에서 다음을 실행합니다.

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

이 구성 파일을 두 VM에 복사하고 두 노드에서 Corosync를 시작합니다.

    sudo service start corosync

서비스를 시작한 직후에 클러스터가 현재 링에 설정되어 있고 쿼럼이 구성되어 있어야 합니다. 로그 또는 다음을 검토하여 이 기능을 확인할 수 있습니다.

    sudo corosync-quorumtool –l

아래 이미지와 비슷한 출력이 표시됩니다.

![corosync-quorumtool -l sample output](media/virtual-machines-linux-mysql-cluster/image001.png)

## Pacemaker 설치

Pacemaker는 클러스터를 사용하여 리소스를 모니터링하고, 기본 노드가 작동 중단될 때를 정의하고, 해당 리소스를 보조 노드로 전환합니다. 리소스는 여러 방법이 있지만 사용 가능한 스크립트 집합이나 LSB(init-like) 스크립트에서 정의할 수 있습니다.

우리는 Pacemaker가 DRBD 리소스, 탑재 지점 및 MySQL 서비스를 "소유"하기 원합니다. Pacemaker로 기본 노드에 좋지 않은 상황이 발생할 경우 적절한 순서대로 DRBD를 켜고 끌 수 있고, 탑재/분리할 수 있고, MySQL을 시작/중지할 수 있으면 설치가 완료된 것입니다.

Pacemaker를 처음 설치할 때는 구성이 다음과 같이 단순합니다.

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

`sudo crm configure show`를 실행하여 확인합니다. 이제 다음 리소스를 사용하여 파일(`/tmp/cluster.conf`)을 만듭니다.

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

이제 구성에 로드합니다. 이 작업은 하나의 노드에서만 수행하면 됩니다.

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

또한 두 노드에서 부팅할 때 Pacemaker가 시작되는지 확인합니다.

    sudo update-rc.d pacemaker defaults

몇 초 후에 `sudo crm_mon –L`을 사용하여 노드 중 하나가 클러스터의 마스터가 되어 모든 리소스를 실행하는지 확인합니다. mount 및 ps를 사용하여 리소스가 실행 중인지 확인할 수 있습니다.

다음 스크린샷에서는 하나의 노드가 중지된 `crm_mon`이 표시됩니다(끝내려면 Ctrl+C 사용).

![crm_mon node stopped](media/virtual-machines-linux-mysql-cluster/image002.png)

또한 스크린샷에는 마스터 노드 하나와 슬레이브 노드 하나가 표시됩니다.

![crm_mon operational master/slave](media/virtual-machines-linux-mysql-cluster/image003.png)

## 테스트

자동 장애 조치(Failover) 시뮬레이션 준비가 되었습니다. 이 작업을 수행하는 방법에는 소프트 방법과 하드 방법이 있습니다. 소프트 방법은 클러스터의 종료 함수인 ``crm_standby -U `uname -n` -v on``을 사용합니다. 을 사용하는 것입니다. 마스터에서 이 함수를 사용하면 슬레이브가 작업을 인계 받습니다. 이 설정은 반드시 다시 해제해야 합니다(crm_mon은 한 노드가 대기 상태임을 알려줌).

하드 방법은 포털을 통해 기본 VM(hadb01)을 종료하거나 VM에서 실행 수준(즉, 중지, 종료)을 변경하는 것입니다. 그러면 우리가 마스터의 작동 중단을 신호로 알려 Corosync 및 Pacemaker를 지원합니다. 이것을 테스트할 수 있지만(유지 관리 기간에 유용) 단지 VM을 동결하여 이 시나리오를 강제로 실행할 수도 있습니다.

## STONITH

실제 장치를 제어하는 STONITH 스크립트 대신, Linux용 Azure 명령줄 도구를 통해 VM 종료를 실행할 수 있습니다. `/usr/lib/stonith/plugins/external/ssh`를 기반으로 사용하고 클러스터의 구성에서 STONITH를 사용하도록 설정할 수 있습니다. Azure CLI는 전역적으로 설치해야 하며 클러스터 사용자를 위해 게시 설정/프로필을 로드해야 합니다.

[GitHub](https://github.com/bureado/aztonith)에서 사용할 수 있는 리소스에 대한 샘플 코드입니다. `sudo crm configure`에 다음을 추가하여 클러스터 구성을 변경해야 합니다.

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**참고:** 이 스크립트는 up/down 검사를 수행하지 않습니다. 원래 SSH 리소스는 15번의 ping 검사를 수행했으며 Azure VM의 복구 시간이 좀 더 가변적일 수 있습니다.

## 제한 사항

다음 제한 사항이 적용됩니다.

- Pacemaker에서 DRBD를 리소스로 관리하는 linbit DRBD 리소스 스크립트는 노드가 대기 상태일 때도 노드를 종료할 때 `drbdadm down`을 사용합니다. 슬레이브는 마스터가 쓰기를 받는 동안 DRBD 리소스를 동기화하지 않게 되므로 이러한 방식은 이상적이지 않습니다. 다행히 마스터가 실패하지 않으면 슬레이브는 이전 파일 시스템 상태를 인계 받을 수 있습니다. 이러한 문제를 해결할 수 있는 방법에는 다음 두 가지가 있습니다.
  - 로컬(클러스터화되지 않은) Watchdog를 통해 모든 클러스터 노드에서 `drbdadm up r0` 적용 또는
  - linbit DRBD 스크립트를 편집하여 `/usr/lib/ocf/resource.d/linbit/drbd`에서 `down`이 호출되지 않도록 설정
- 부하 분산 장치가 응답하는 데 5초 이상 필요하므로 응용 프로그램은 클러스터를 인식할 수 있어야 하고 시간 제한을 좀 더 허용해야 합니다. 앱 내 큐, 쿼리 미들웨어 등의 다른 아키텍처도 도움이 될 수 있습니다.
- 동일한 간격으로 쓰기가 수행되고 캐시가 메모리 손실을 최소화할만큼 자주 디스크에 플러시되도록 하기 위해 MySQL 튜닝이 필요합니다.
- 쓰기 성능은 DRBD가 장치 복제에 사용하는 메커니즘인 가상 스위치의 VM 상호 연결에 따라 좌우됩니다.

<!---HONumber=58--> 