---
title: Azure VM(Virtual Machines)에서 SAP HANA 시스템 복제 설정 | Microsoft Docs
description: Azure VM(Virtual Machines)에서 SAP HANA 고가용성을 설정합니다.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: sedusch
ms.openlocfilehash: 2bf9ed176f37c315aa4496894315f2318370ce7f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Azure VM(Virtual Machines)의 SAP HANA 고가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

온-프레미스에서 HANA 시스템 복제를 사용하거나 공유 저장소를 사용하여 SAP HANA의 고가용성을 설정할 수 있습니다.
Azure VM에서 Azure의 HANA 시스템 복제는 지금까지 지원되는 유일한 고가용성 함수입니다. SAP HANA 복제는 하나의 기본 노드와 하나 이상의 보조 노드로 구성됩니다. 기본 노드의 데이터를 변경하면 보조 노드에 동기적 또는 비동기적으로 복제됩니다.

이 문서에서는 가상 머신을 배포하고, 가상 머신을 구성하며, 클러스터 프레임워크를 설치하고, SAP HANA 시스템 복제를 구성하는 방법을 설명합니다.
예제 구성에서 설치 명령, 인스턴스 번호 03 및 HANA 시스템 ID HDB를 사용합니다.

먼저 다음 SAP 참고와 문서 읽기

* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전
* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2205917]에는 SAP 응용 프로그램용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 나와 있습니다.
* SAP Note [1944799]에는 SAP 응용 프로그램용 SUSE Linux Enterprise Server에 대한 SAP HANA 지침이 나와 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포(이 문서)][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SAP HANA SR 성능 최적화된 시나리오][suse-hana-ha-guide] 이 가이드에는 온-프레미스에 SAP HANA 시스템 복제를 설정하는 데 필요한 모든 정보가 들어 있습니다. 이 가이드를 기준으로 사용합니다.

## <a name="deploying-linux"></a>Linux 배포

SAP HANA의 리소스 에이전트는 SAP 응용 프로그램의 SUSE Linux Enterprise Server에 포함되어 있습니다.
Azure Marketplace에는 새 가상 머신을 배포하는 데 사용할 수 있는 BYOS(Bring Your Own Subscription)와 함께 SAP 응용 프로그램 12용 SUSE Linux Enterprise Server의 이미지가 포함되어 있습니다.

### <a name="manual-deployment"></a>수동 배포

1. 리소스 그룹 만들기
1. Virtual Network 만들기
1. 두 개의 Storage 계정 만들기
1. 가용성 집합 만들기  
   최대 업데이트 도메인 설정
1. 부하 분산 장치(내부) 만들기  
   두 번째 단계에서 만든 VNET 선택
1. Virtual Machine 1 만들기   
   최소한 SLES4SAP 12 SP1을 사용합니다. 이 예제에서는 SLES4SAP 12 SP1 BYOS 이미지를 사용합니다(https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1).  
   SAP 응용 프로그램 12 SP1용 SLES(BYOS)가 사용됩니다.  
   Storage 계정 1 선택   
   가용성 집합 선택  
1. Virtual Machine 2 만들기   
   최소한 SLES4SAP 12 SP1을 사용합니다. 이 예제에서는 SLES4SAP 12 SP1 BYOS 이미지를 사용합니다(https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1).  
   SAP 응용 프로그램 12 SP1용 SLES(BYOS)가 사용됩니다.  
   Storage 계정 2 선택    
   가용성 집합 선택  
1. 데이터 디스크 추가
1. 부하 분산 장치 구성
    1. 프런트 엔드 IP 풀 만들기
        1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
        1. 새 프런트 엔드 IP 풀의 이름 입력(예: hana-frontend)
        1. 확인 클릭
        1. 새 프런트 엔드 IP 풀을 만든 다음 IP 주소를 적어 두기
    1. 백 엔드 풀 만들기
        1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
        1. 새 백 엔드 풀의 이름 입력(예: hana-backend)
        1. 가상 머신 추가 클릭
        1. 이전에 만든 가용성 집합 선택
        1. SAP HANA 클러스터의 가상 머신 선택
        1. 확인 클릭
    1. 상태 프로브 만들기
        1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
        1. 새 상태 프로브의 이름 입력(예: hana-hp)
        1. 프로토콜로 TCP를 선택하고 포트 625**03**을 선택한 다음 간격은 5, 비정상 임계값은 2로 유지
        1. 확인 클릭
    1. 부하 분산 규칙 만들기
        1. 부하 분산 장치를 열고, 부하 분산 규칙을 설정한 다음, 추가 클릭
        1. 새 부하 분산 장치 규칙의 이름 입력(예: hana-lb-3**03**15)
        1. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브 선택(예: hana-frontend)
        1. 프로토콜 TCP를 유지하고 포트 3**03**15 입력
        1. 유휴 상태 시간 제한을 30분으로 증가
        1. **부동 IP를 사용하도록 설정**
        1. 확인 클릭
        1. 포트 3**03**17에 대해 다음 단계 반복

### <a name="deploy-with-template"></a>템플릿을 사용하여 배포
Github에서 빠른 시작 템플릿 중 하나를 사용하여 필요한 모든 리소스를 배포할 수 있습니다. 템플릿에서 가상 머신, 부하 분산 장치, 가용성 집합 등을 배포합니다. 템플릿을 배포하려면 다음 단계를 따릅니다.

1. Azure Portal에서 [데이터베이스 템플릿][template-multisid-db] 또는 [수렴 템플릿][template-converged]을 엽니다. 
   데이터베이스 템플릿은 데이터베이스의 부하 분산 규칙만 만드는 반면 수렴형 템플릿은 ASCS/SCS 및 ERS(Linux만 해당) 인스턴스의 부하 분산 규칙도 만듭니다. SAP NetWeaver 기반 시스템을 설치하려 하고 동일한 컴퓨터에 ASCS/SCS 인스턴스도 설치하려면 [수렴 템플릿][template-converged]을 사용합니다.
1. 다음 매개 변수를 입력합니다.
    1. SAP 시스템 ID  
       설치하려는 SAP 시스템의 SAP 시스템 ID를 입력합니다. 이 ID는 배포되는 리소스의 접두사로 사용됩니다.
    1. 스택 유형(수렴 템플릿을 사용하는 경우에만 해당)   
       SAP NetWeaver 스택 유형 선택
    1. OS 종류 -  
       Linux 배포판 중 하나를 선택합니다. 이 예에서는 SLES 12 BYOS 선택
    1. Db 형식  
       HANA 선택
    1. SAP 시스템 크기 -  
       새 시스템에서 제공하는 SAP의 양입니다. 시스템에 필요한 SAP의 수를 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
    1. 시스템 가용성 -  
       HA를 선택합니다.
    1. 관리자 사용자 이름 및 관리자 암호 -  
       컴퓨터에 로그온하는 데 사용할 수 있게 만들어진 새 사용자입니다.
    1. 기존 또는 새 서브넷 -  
       새 가상 네트워크와 서브넷을 만들어야 하는지 또는 기존 서브넷을 사용해야 하는지 결정합니다. 온-프레미스 네트워크에 연결되어 있는 가상 네트워크가 이미 있는 경우 기존 항목을 선택합니다.
    1. 서브넷 ID  
    가상 머신을 연결해야 하는 서브넷의 ID입니다. 온-프레미스 네트워크에 가상 머신을 연결하려면 VPN 또는 Express Route 가상 네트워크의 서브넷을 선택합니다. ID는 일반적으로 /subscriptions/`<subscription ID`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`> 형태입니다.

## <a name="setting-up-linux-ha"></a>Linux HA 설정

다음 항목에는 접두사 [A] - 모든 노드에 적용, [1] - 노드 1에만 적용 1 또는 [2] - 노드 2에만 적용이 지정됩니다.

1. [A] SLES for SAP BYOS 전용 - 저장소를 사용할 수 있도록 SLES 등록
1. [A] SLES for SAP BYOS 전용 - 공개 클라우드 모듈 추가
1. [A] SLES 업데이트
    ```bash
    sudo zypper update

    ```

1. [1] ssh 액세스 사용
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] ssh 액세스 사용
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] ssh 액세스 사용
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] HA 확장 설치
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] 설치 디스크 레이아웃
    1. LVM  
    일반적으로는 데이터와 로그 파일을 저장하는 볼륨의 LVM을 사용하는 것이 좋습니다. 다음 예에서는 가상 머신에 두 개의 볼륨을 만드는 데 사용해야 하는 4개의 데이터 디스크가 연결되어 있다고 가정합니다.
        * 사용하려는 모든 디스크의 물리적 볼륨을 만듭니다.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * 데이터 파일에 대한 볼륨 그룹, 로그 파일에 대해 한 볼륨 그룹 및 SAP HANA의 공유 디렉터리에 대해 한 볼륨 만들기
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * 논리 볼륨 만들기
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * 탑재 디렉터리를 만들고 모든 논리 볼륨의 UUID 복사
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the ID of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * 세 개의 논리 볼륨에 대한 fstab 항목 만들기
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    이 줄을 /etc/fstab에 삽입
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * 새 볼륨 탑재
    <pre><code>
    sudo mount -a
    </code></pre>
    1. 일반 디스크  
       소규모 또는 데모 시스템용으로 한 디스크에 HANA 데이터와 로그 파일을 둘 수 있습니다. 다음 명령은 /dev/sdc에 패턴을 만들고 xfs로 서식을 지정합니다.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>/dev/sdc1의 ID 기록
    sudo /sbin/blkid  sudo vi /etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] 모든 호스트의 호스트 이름 확인 설정  
    DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기
    ```bash
    sudo vi /etc/hosts
    ```
    다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] 클러스터 설치
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] 클러스터에 노드 추가
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] hacluster 암호를 동일한 암호로 변경
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] 다른 전송을 사용하고 nodelist를 추가하도록 corosync를 구성합니다. 그렇지 않으면 클러스터가 작동하지 않습니다. 
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    굵은체로 된 다음 콘텐츠를 파일에 추가합니다.
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    그런 다음 corosync 서비스를 다시 시작합니다.

    ```bash
    sudo service corosync restart
    
    ```

1. [A] HANA HA 패키지 설치  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>SAP HANA 설치

SAP HANA 시스템 복제를 설치하려면 [SAP HANA SR 성능 최적화 시나리오 가이드][suse-hana-ha-guide]의 4장을 따라 작업합니다.

1. [A] HANA DVD에서 hdblcm 실행
    * 설치 선택 -> 1
    * 설치할 추가 구성 요소 선택 -> 1
    * 설치 경로 [/hana/shared] 입력: -> ENTER
    * 로컬 호스트 이름 입력 [..]: -> ENTER
    * 시스템에 호스트를 추가할까요? (y/n) [n]: -> ENTER
    * SAP HANA 시스템 ID 입력: <SID of HANA e.g. HDB>
    * 인스턴스 번호 [00] 입력:   
  HANA 인스턴스 번호입니다. Azure 템플릿을 사용하거나 수동 배포를 따른 경우 03 사용
    * 데이터베이스 모드 선택 / 인덱스 [1] 입력: -> ENTER
    * 시스템 사용량 선택 / 인덱스 [4] 입력:  
  시스템 사용량 선택
    * 데이터 볼륨의 위치 [/hana/data/HDB] 입력: -> ENTER
    * 로그 볼륨의 위치 [/hana/log/HDB] 입력: -> ENTER
    * 최대 메모리 할당 제한? [n]: -> ENTER
    * 호스트의 인증서 호스트 이름 '...' [...] 입력: -> ENTER
    * SAP 호스트 에이전트 사용자(sapadm) 암호 입력:
    * SAP 호스트 에이전트 사용자(sapadm) 암호 확인:
    * 시스템 관리자(hdbadm) 암호 입력:
    * 시스템 관리자(hdbadm) 암호 확인:
    * 시스템 관리자 홈 디렉터리 [/usr/sap/HDB/home] 입력: -> ENTER
    * 시스템 관리자 로그인 셸 [/bin/sh] 입력: -> ENTER
    * 시스템 관리자 사용자 ID [1001] 입력: -> ENTER
    * 사용자 그룹 ID(sapsys) [79] 입력: -> ENTER
    * 데이터베이스 사용자(SYSTEM) 암호 입력:
    * 데이터베이스 사용자(SYSTEM) 암호 확인:
    * 컴퓨터를 다시 부팅한 다음 시스템 다시 시작? [n]: -> ENTER
    * 계속할까요? (y/n):   
  요약의 유효성을 검사하고 계속하려면 y를 입력합니다.
1. [A] SAP 호스트 에이전트 업그레이드  
  [SAP Softwarecenter][sap-swcenter]에서 최신 SAP 호스트 에이전트 아카이브를 다운로드하고 다음 명령을 실행하여 에이전트를 업그레이드합니다. 다운로드한 파일을 가리키도록 아카이브의 경로를 바꿉니다.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] 루트로 HANA 복제 만들기  
    다음 명령을 실행합니다. 굵은체로 된 문자열(HANA 시스템 ID HDB 및 인스턴스 번호 03)을 SAP HANA 설치의 값으로 바꿉니다.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] 루트로 키 저장소 항목 만들기
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] 루트로 데이터베이스 Backup
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] sapsid 사용자(예: hdbadm)로 전환하고 기본 사이트를 만듭니다.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] sapsid 사용자(예: hdbadm)로 전환하고 보조 사이트를 만듭니다.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>클러스터 프레임워크 구성

기본 설정 변경

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>이제 클러스터에 파일 로드
sudo crm configure load update crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>STONITH 장치 만들기

STONITH 장치에서는 서비스 주체를 사용하여 Microsoft Azure에 대해 권한을 부여합니다. 서비스 주체를 만들려면 다음 단계에 따릅니다.

1. <https://portal.azure.com>으로 이동합니다.
1. Azure Active Directory 블레이드 열기  
   속성으로 이동하여 Directory ID 기록 이 ID는 **테넌트 ID**입니다.
1. 앱 등록 클릭
1. 추가를 클릭합니다.
1. 이름을 입력하고 응용 프로그램 유형 "Web app/API"를 선택한 다음 로그온 URL(예: http://localhost )을 입력하고 만들기를 클릭
1. 로그온 URL이 사용되지 않으며, 이 URL은 임의의 올바른 URL이 될 수 있음
1. 새 앱을 선택하고 설정 탭에서 키 클릭
1. 새 키의 설명을 입력하고 “만료되지 않음”을 선택한 다음 저장을 클릭
1. 값을 기록해 둡니다. 서비스 주체의 **암호**로 사용됨
1. 응용 프로그램 ID를 적어둡니다. 서비스 주체의 사용자 이름(아래 단계의 **로그인 ID**)으로 사용됨

서비스 주체에는 기본적으로 Azure 리소스에 액세스할 권한이 없습니다. 서비스 주체에 클러스터의 모든 가상 머신을 시작 및 중지(할당 취소)하기 위한 권한을 제공해야 합니다.

1. https://portal.azure.com으로 이동
1. 모든 리소스 블레이드 열기
1. 가상 머신 선택
1. 액세스 제어(IAM) 클릭
1. 추가를 클릭합니다.
1. 소유자 역할 선택
1. 이전 단계에서 만든 응용 프로그램의 이름 입력
1. 확인 클릭

가상 머신의 권한을 편집하고 나면 클러스터의 STONITH 장치를 구성할 수 있습니다.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>이제 클러스터에 파일 로드
sudo crm configure load update crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>SAP HANA 리소스 만들기

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system ID
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>이제 클러스터에 파일 로드
sudo crm configure load update crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>이제 클러스터에 파일 로드
sudo crm configure load update crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>클러스터 설정 테스트
이 장에서는 설정 테스트 방법을 설명합니다. 모든 테스트에서는 사용자가 루트이고 SAP HANA 마스터가 가상 머신 saphanavm1에서 실행 중이라고 가정합니다.

#### <a name="fencing-test"></a>펜싱 테스트

saphanavm1 노드에서 네트워크 인터페이스를 사용하지 않게 설정하여 펜싱 에이전트의 설정을 테스트할 수 있습니다.

<pre><code>
sudo ifdown eth0
</code></pre>

클러스터 구성에 따라 이제 가상 머신을 다시 시작하거나 중지해야 합니다.
stonith-action을 off로 설정하면 가상 머신이 중지되고 리소스가 실행 중인 가상 머신으로 마이그레이션됩니다.

가상 머신을 다시 시작되면 SAP HANA 리소스에서는 시작되지 않고 보조 AUTOMATED_REGISTER를 설정하는 경우 = "false"입니다. 이 경우 다음 명령을 실행하여 HANA 인스턴스를 보조로 구성합니다.

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>수동 장애 조치 테스트

saphanavm1 노드에서 pacemaker 서비스를 중지하여 수동 장애 조치를 테스트할 수 있습니다.
<pre><code>
service pacemaker stop
</code></pre>

장애 조치 후에 서비스를 다시 시작할 수 있습니다. AUTOMATED_REGISTER="false"를 설정하면 saphanavm1에서 SAP HANA 리소스를 보조로 시작하는 데 실패합니다. 이 경우 다음 명령을 실행하여 HANA 인스턴스를 보조로 구성합니다.

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>마이그레이션 테스트

다음 명령을 실행하여 SAP HANA 마스터 노드를 마이그레이션할 수 있습니다.
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

AUTOMATED_REGISTER="false"를 설정하면 이 명령 시퀀스가 SAP HANA 마스터 노드와 가상 IP 주소를 포함하는 그룹을 saphanavm2로 마이그레이션합니다.
saphanavm1에서 SAP HANA 리소스를 보조로 시작하는 데 실패합니다. 이 경우 다음 명령을 실행하여 HANA 인스턴스를 보조로 구성합니다.

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

마이그레이션을 통해 다시 삭제해야 하는 위치 제약조건을 만듭니다.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

보조 노드 리소스의 상태도 정리해야 합니다.

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>다음 단계
* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다. 
