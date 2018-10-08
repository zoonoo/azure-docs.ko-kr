---
title: Azure 가상 머신에서 SLES 12 SP3을 사용하여 SAP HANA 2.0 스케일 아웃 HSR-Pacemaker 설정 문제 해결 | Microsoft Docs
description: Azure 가상 머신에서 실행되는 SLES 12 SP3의 SAP HSR(HANA System Replication) 및 Pacemaker에 따라 복잡한 SAP HANA 스케일 아웃 고가용성 구성을 확인하고 문제를 해결하는 방법에 대한 가이드입니다.
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: 6c0d6397246e8b8db1d59c26229e37a722d49f48
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184736"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>SLES 12 SP3에서 SAP HANA 스케일 아웃 고가용성 설정 확인 및 문제 해결 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


이 문서는 Azure 가상 머신에서 실행되는 SAP HANA 스케일 아웃에 대한 Pacemaker 클러스터 구성을 확인하는 데 도움이 되도록 작성되었습니다. 클러스터 설정은 SAP HSR(HANA System Replication) 및 SAPHanaSR-ScaleOut SUSE RPM 패키지와 함께 수행되었습니다. 모든 테스트는 SUSE SLES 12 SP3에서만 수행되었습니다. 다양한 영역을 다루는 몇 가지 섹션이 있으며, 샘플 명령과 구성 파일에서 발췌한 내용이 포함되어 있습니다. 이러한 샘플은 전체 클러스터 설정을 확인하고 검사하는 방법으로 사용하는 것이 좋습니다.



## <a name="important-notes"></a>중요 참고 사항

SAP HANA System Replication 및 Pacemaker와 함께 SAP HANA 스케일 아웃에 대한 모든 테스트는 SAP HANA 2.0에서만 수행되었습니다. 운영 체제 버전은 SUSE Linux Enterprise Server 12 SP3 for SAP Applications였습니다. 또한 SUSE의 최신 RPM 패키지인 SAPHanaSR-ScaleOut도 pacemaker 클러스터를 설정하는 데 사용했습니다.
SUSE는 이 성능 최적화 설정에 대한 자세한 설명을 게시했으며, [여기][sles-hana-scale-out-ha-paper]서 확인할 수 있습니다.

SAP HANA 스케일 아웃에 지원되는 가상 머신 유형은 [SAP HANA 인증 IaaS 디렉터리][sap-hana-iaas-list]를 확인하세요.

여러 서브넷과 vNIC 및 HSR 설정과 함께 SAP HANA 스케일 아웃에 기술적인 문제가 있었습니다. 이 문제가 해결된 최신 SAP HANA 2.0 패치는 반드시 사용해야 합니다. 지원되는 SAP HANA 버전은 다음과 같습니다. 

**rev2.00.024.04 이상 및 rev2.00.032 이상**

SUSE의 지원이 필요한 상황이면 [이 가이드][suse-pacemaker-support-log-files]를 따르세요. 이 문서에서 설명하는 대로 SAP HANA HA 클러스터에 대한 모든 정보를 수집합니다. SUSE 지원은 추가 분석을 위해 이 정보가 필요합니다.

내부 테스트 중에 Azure Portal을 통한 정상적인 VM 종료로 인해 클러스터 설정이 혼란스럽게 되었습니다. 따라서 다른 방법으로 클러스터 장애 조치를 테스트하는 것이 좋습니다. 커널 패닉을 강요하거나 네트워크를 종료하거나 **msl** 리소스를 마이그레이션하는 것과 같은 방법을 사용합니다(자세한 내용은 아래 섹션 참조). 표준 종료는 의도한 대로 발생한다는 가정이 있습니다. 의도적인 종료에 대한 가장 좋은 예는 유지 관리(자세한 내용은 계획된 유지 관리 섹션 참조)입니다.

내부 테스트 중에 클러스터가 유지 관리 모드에 있는 동안 수동 SAP HANA 인수 후에 클러스터 설정이 혼란스럽게 되었습니다. 클러스터 유지 관리 모드를 종료하기 전에 수동으로 다시 전환하는 것이 좋습니다. 또 다른 옵션은 클러스터를 유지 관리 모드로 전환하기 전에 장애 조치를 트리거하는 것입니다(자세한 내용은 계획된 유지 관리 섹션 참조). SUSE의 설명서에는 이와 관련하여 crm 명령을 사용하여 클러스터를 다시 설정할 수 있는 방법이 설명되어 있습니다. 그러나 앞에서 언급한 접근 방식은 내부 테스트 중에 강력한 것으로 보였으며, 예기치 않은 부작용은 전혀 보여 주지 않았습니다.

crm 마이그레이션 명령을 사용하는 경우 반드시 클러스터 구성을 정리하세요. 사용자가 알지 못할 수 있는 위치 제약 조건을 추가합니다. 이러한 제약 조건은 클러스터 동작에 영향을 줍니다(자세한 내용은 계획된 유지 관리 섹션 참조).



## <a name="test-system-description"></a>테스트 시스템 설명

SAP HANA 스케일 아웃 HA 확인 및 인증의 경우 각각 세 개의 SAP HANA 노드(하나의 마스터 및 두 개의 작업자)가 있는 두 개의 시스템으로 구성된 설정이 사용되었습니다. 다음은 VM 이름과 내부 IP 주소의 목록입니다. 모든 확인 샘플은 아래의 VM에서 자세히 수행되었습니다. 명령 샘플에서 이러한 VM 이름과 IP 주소를 사용하면 명령과 해당 출력을 더 잘 이해할 수 있습니다.


| 노드 유형 | VM 이름 | IP 주소 |
| --- | --- | --- |
| 사이트 1의 마스터 노드 | hso-hana-vm-s1-0 | 10.0.0.30 |
| 사이트 1의 작업자 노드 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| 사이트 1의 작업자 노드 2 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| 사이트 2의 마스터 노드 | hso-hana-vm-s2-0 | 10.0.0.40 |
| 사이트 2의 작업자 노드 1 | hso-hana-vm-s2-1 | 10.0.0.41 |
| 사이트 2의 작업자 노드 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| 주 결정자 노드 | hso-hana-dm | 10.0.0.13 |
| SBD 장치 서버 | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS 서버 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS 서버 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>여러 서브넷 및 vNIC

SAP HANA 네트워크 권장 사항에 따라 하나의 Azure 가상 네트워크 내에 세 개의 서브넷이 만들어졌습니다. Azure의 SAP HANA 스케일 아웃은 비공유 모드로 설치해야 합니다. 즉 모든 노드에서 **/hana/data** 및 **/hana/log**에 대해 로컬 디스크 볼륨을 사용합니다. 로컬 디스크 볼륨만 사용하므로 다음과 같이 저장소에 대해 별도의 서브넷을 정의할 필요가 없습니다.

- 10.0.2.0/24 - SAP HANA 노드 간 통신용
- 10.0.1.0/24 - SAP HSR(HANA System Replication)용
- 10.0.0.0/24 - 기타 모든 항목용

다중 네트워크 사용과 관련된 SAP HANA 구성에 대한 자세한 내용은 아래의 **global.ini** 섹션을 참조하세요.

클러스터의 모든 VM에는 서브넷 수에 해당하는 세 개의 vNIC가 있습니다. [이 문서][azure-linux-multiple-nics]에서는 Linux VM을 배포할 때 Azure에 발생할 수 있는 잠재적인 라우팅 문제에 대해 설명합니다. 이 특정 라우팅 항목은 여러 vNIC를 사용하는 경우에만 적용됩니다. 이러한 문제는 SLES 12 SP3의 SUSE에서 기본값으로 해결됩니다. 이 항목에 관한 SUSE 문서는 [여기][suse-cloud-netconfig]서 찾을 수 있습니다.


기본적으로 SAP HANA가 여러 네트워크를 사용할 수 있도록 올바르게 구성되었는지 확인하려면 다음과 같은 명령을 실행하기만 하면 됩니다. 첫 번째 단계는 OS 수준에서 세 개의 서브넷 각각에 대한 세 개의 내부 IP 주소가 모두 활성 상태인지를 간단히 다시 확인하는 것입니다. 서로 다른 IP 주소 범위를 사용하여 서브넷을 정의한 경우 다음 명령을 조정해야 합니다.

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

다음은 사이트 2의 작업자 노드 2에서 출력되는 샘플입니다. eth0, eth1 및 eth2에서 서로 다른 세 개의 내부 IP 주소를 볼 수 있습니다.

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


두 번째 단계는 이름 서버 및 HSR에 대한 SAP HANA 포트를 확인하는 것입니다. SAP HANA는 해당 서브넷에서 수신 대기해야 합니다. SAP HANA 인스턴스 번호에 따라 명령을 조정해야 합니다. 테스트 시스템의 경우 인스턴스 번호는 **00**입니다. 사용되는 포트는 여러 가지 방법으로 파악할 수 있습니다. 

다른 정보 중에서 인스턴스 ID와 인스턴스 번호를 반환하는 SQL 문은 다음과 같습니다.

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

예를 들어 올바른 포트 번호를 찾으려면 HANA Studio의 "**구성**" 아래에서 또는 SQL 문을 통해 살펴볼 수 있습니다.

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

SAP HANA를 포함한 SAP 소프트웨어 스택에서 사용되는 모든 포트를 찾으려면 [여기][sap-list-port-numbers]를 검색해 보세요.

SAP HANA 2.0 테스트 시스템에서 인스턴스 번호가 **00**인 경우 이름 서버의 포트 번호는 **30001**입니다. HSR 메타데이터 통신의 포트 번호는 **40002**입니다. 한 가지 옵션은 작업자 노드에 로그인한 다음, 마스터 노드 서비스를 확인하는 것입니다. 여기서의 확인 작업은 사이트 2의 작업자 노드 2에서 사이트 2의 마스터 노드에 연결하려고 시도하면서 수행되었습니다.

이름 서버 포트를 확인합니다.

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

노드 간 통신에서 **10.0.2.0/24** 서브넷을 사용하고 있음을 증명하는 결과는 아래의 샘플 출력과 같습니다.
**10.0.2.0/24** 서브넷을 통한 연결만 성공해야 합니다.

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

이제 **40002** HSR 포트를 확인합니다.

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

HSR 통신에서 **10.0.1.0/24** 서브넷을 사용하고 있음을 증명하는 결과는 아래의 샘플 출력과 같습니다.
**10.0.1.0/24** 서브넷을 통한 연결만 성공해야 합니다.

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


corosync 구성 파일은 주 결정자 노드를 포함하여 클러스터의 모든 노드에서 정확해야 합니다. 노드의 클러스터 조인이 예상대로 작동하지 않은 경우 모든 노드에서 **/etc/corosync/corosync.conf**를 수동으로 만들거나 복사하고 서비스를 다시 시작합니다.

다음은 테스트 시스템에 있는 **corosync.conf**의 내용에 대한 예제입니다.

첫 번째 섹션은 [이 설명서][sles-pacemaker-ha-guide](클러스터 설치 단계 11 섹션)에서 설명한 대로 **totem**입니다. **mcastaddr**에 대한 값은 무시할 수 있습니다. 기존 항목만 유지합니다. [여기][sles-pacemaker-ha-guide]에 있는 Microsoft Azure SAP HANA 설명서에 따라 **token** 및 **consensus** 항목을 설정해야 합니다.

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

두 번째 **logging** 섹션은 지정된 기본값에서 변경되지 않았습니다.

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

세 번째 섹션에서는 **nodelist**를 보여 줍니다. 클러스터의 모든 노드는 해당 노드 ID를 사용하여 표시해야 합니다.

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

마지막 **quorum** 섹션에서는 **expected_votes** 값을 올바르게 설정해야 합니다. 주 결정자 노드를 포함한 노드의 수여야 합니다. 그리고 **two_node** 값은 **0**이어야 합니다. 항목을 완전히 제거하지 마세요. 값만 **0**으로 설정하면 됩니다.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


**systemctl**을 통해 서비스를 다시 시작합니다.

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD 장치

Azure VM에서 SBD 장치를 설정하는 방법에 대한 설명서는 [여기][sles-pacemaker-ha-guide](sbd-fencing 섹션)에 설명되어 있습니다.

다시 확인하기 위한 첫 번째 작업은 클러스터의 모든 노드에 대한 ACL 항목이 있는 경우 SBD 서버 VM을 조사하는 것입니다. SBD 서버 VM에서 다음 명령을 실행합니다.


<pre><code>
targetcli ls
</code></pre>


테스트 시스템에서 명령의 출력은 아래 샘플과 같습니다. **iqn.2006-04.hso-db-0.local:hso-db-0**과 같은 ACL 이름은 VM의 해당 초기자 이름으로 입력해야 합니다. 모든 VM마다 서로 다른 이름이 필요합니다.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

나중에 모든 VM의 초기자 이름이 서로 다르고 위에서 보여 준 항목과 일치하는지 확인합니다. 다음은 사이트 1의 작업자 노드 1에서 제공하는 한 가지 예제입니다.

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

출력은 아래 샘플과 같았습니다.

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

다음으로, **discovery**가 올바르게 작동하는지 확인하고, SBD 서버 VM의 IP 주소를 사용하여 모든 클러스터 노드에서 다음 명령을 실행합니다.

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

출력은 아래 샘플과 같습니다.

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

다음 증명 지점은 노드에서 SDB 장치를 표시하는지 확인하는 것입니다. 주 결정자 노드를 포함한 모든 노드에서 확인합니다.

<pre><code>
lsscsi | grep dbhso
</code></pre>

출력은 아래 샘플과 같습니다. 이름이 다를 수 있음에 유의하세요(VM을 다시 부팅한 후에 장치 이름이 변경될 수도 있음).

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

시스템의 상태에 따라 iscsi 서비스를 다시 시작하여 문제를 해결하는 것이 도움이 되는 경우가 있습니다. 그런 다음, 다음 명령을 실행합니다.

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


어느 노드에서나 모든 노드가 **clear**(지우기)인지 확인할 수 있습니다. 특정 노드에서 올바른 장치 이름을 사용하는 것에만 주의하세요.

<pre><code>
sbd -d /dev/sdm list
</code></pre>

출력에서 클러스터의 모든 노드에 대해 **clear**를 표시해야 합니다.

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


또 따른 SBD 검사는 sbd 명령의 **dump** 옵션입니다. 다음은 장치 이름이 **sdm**가 아니고 **sdd**인 주 결정자 노드의 샘플 명령과 출력입니다.

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

출력(장치 이름 제외)은 모든 노드에서 동일하게 표시됩니다.

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

SBD를 한 번 더 확인하면 다른 노드에 메시지를 보낼 수 있습니다. 사이트 2의 작업자 노드 1에서 다음 명령을 실행하여 사이트 2의 작업자 노드 2에 메시지를 보냅니다.

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

이 예제에서 **hso-hana-vm-s2-2**인 대상 VM 쪽의 **/var/log/messages**에서 다음 항목을 찾을 수 있습니다.

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

**/etc/sysconfig/sbd**의 항목이 [이 설명서][sles-pacemaker-ha-guide](sbd-fencing 섹션)의 설명에 해당하는지 다시 확인합니다. **/etc/iscsi/iscsid.conf**의 startup(시작) 설정이 automatic으로 설정되어 있는지 확인합니다.

**/etc/sysconfig/sbd**에서 중요한 항목은 다음과 같습니다(필요한 경우 id 값 조정).

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


확인할 또 다른 항목은 **/etc/iscsi/iscsid.conf**의 startup 설정입니다. 필요한 설정은 설명서에서 설명된 아래의 **iscsiadm** 명령으로 수행되었어야 합니다. 다른 경우에는 **vi**를 사용하여 수동으로 확인하고 조정하는 것이 좋습니다.

startup 동작을 설정하는 명령:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

**/etc/iscsi/iscsid.conf**의 항목:

<pre><code>
node.startup = automatic
</code></pre>

테스트 및 확인 중에 VM을 다시 시작한 후 SBD 장치가 더 이상 표시되지 않는 상황이 발생했습니다. startup 설정과 yast2에서 표시한 설정과는 차이가 있었습니다. 설정을 다시 확인하려면 다음 단계를 수행합니다.

1. yast2를 시작합니다.
2. 왼쪽에서 **Network Services**를 선택합니다.
3. 오른쪽에서 **iSCSI 초기자**까지 아래로 스크롤하여 선택합니다.
4. 다음 화면의 **서비스** 탭 아래에서 노드에 대한 고유 초기자 이름이 표시됩니다.
5. 초기자 이름 위에서 **서비스 시작** 값이 **부팅 시**로 설정되어 있는지 확인합니다.
6. 그렇지 않은 경우 **수동** 대신 **부팅 시**로 설정합니다.
7. 그런 다음, 위쪽 탭을 **연결된 대상**으로 전환합니다.
8. [연결된 대상] 화면에서 이 샘플과 같은 SBD 장치에 대한 항목(**10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**)이 표시됩니다.
9. [시작] 값이 "**onboot**"로 설정되어 있는지 확인합니다
10. 그렇지 않은 경우 **편집**을 선택하고 변경합니다.
11. 변경 내용을 저장하고 yast2를 종료합니다.



## <a name="pacemaker"></a>Pacemaker

모든 항목이 올바르게 설정되면 모든 노드에서 다음 명령을 실행하여 pacemaker 서비스의 상태를 확인할 수 있습니다.

<pre><code>
systemctl status pacemaker
</code></pre>

출력의 위쪽 부분은 아래 샘플과 같습니다. **Active**(활성) 뒤의 상태가 **loaded**(로드됨) 또는 **active (running)**(활성(실행 중))으로 표시되는 것이 중요합니다. "loaded" 뒤의 상태는 **enabled**(사용)로 표시되어야 합니다.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

설정이 여전히 **disabled**(사용 안 함)인 경우 다음 명령을 실행합니다.

<pre><code>
systemctl enable pacemaker
</code></pre>

pacemaker에서 구성된 모든 리소스를 보려면 다음 명령을 실행합니다.

<pre><code>
crm status
</code></pre>

출력은 아래 샘플과 같습니다. cln 및 msl 리소스는 주 결정자 VM(**hso-hana-dm**)에서 stopped(중지됨)로 표시됩니다. 주 결정자 노드에는 SAP HANA가 설치되어 있지 않습니다. 따라서 **cln** 및 **msl** 리소스가 stopped로 표시됩니다. 올바른 총 VM 수(**7**)를 표시하는 것이 중요합니다. 클러스터의 일부인 모든 VM은 **Online**(온라인) 상태에서 나열되어야 합니다. 현재 주 마스터 노드를 올바르게 인식해야 합니다(이 예제에서는 **hso-hana-vm-s1-0**임).

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

pacemaker의 중요한 기능은 이 노드를 maintenance(유지 관리) 모드로 전환하는 것입니다. 이 모드에서는 즉각적인 클러스터 작업(예: VM 다시 부팅)을 유발하지 않고 수정할 수 있습니다. 일반적인 사용 사례는 계획된 OS 또는 Azure 인프라 유지 관리입니다(별도의 계획된 유지 관리 섹션도 참조). 다음 명령을 사용하여 pacemaker를 유지 관리 모드로 설정합니다.

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

**crm status**를 사용하여 확인하면 출력에서 모든 리소스가 **unmanaged**(관리되지 않음)로 표시되었음을 알 수 있습니다. 이 상태에서 클러스터는 SAP HANA 시작/중지와 같은 변경에 반응하지 않습니다.
다음은 클러스터가 유지 관리 모드에 있는 동안의 **crm status** 명령의 샘플 출력입니다.

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


그리고 아래 명령 샘플에서 클러스터 유지 관리 모드를 종료하는 방법을 확인합니다.

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


또 다른 crm 명령을 사용하면 전체 클러스터 구성을 편집 가능한 편집기로 가져올 수 있습니다. 변경 내용이 저장되면 클러스터에서 적절한 작업을 시작합니다.

<pre><code>
crm configure edit
</code></pre>

전체 클러스터 구성만 살펴보려면 crm **show** 옵션을 사용합니다.

<pre><code>
crm configure show
</code></pre>



클러스터 리소스가 실패하면 **crm status** 명령에서 **실패한 작업** 목록을 표시합니다. 이 출력에 대해 아래 샘플을 참조하세요.


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

실패한 후에는 클러스터 정리를 수행해야 합니다. 아래와 같이 crm 명령을 다시 사용하고 **cleanup** 명령 옵션을 사용하여 해당 클러스터 리소스의 이름을 지정하는 데 실패한 작업 항목을 제거하면 됩니다.

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

명령에서 아래 예제와 같은 출력을 반환해야 합니다.

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover--takeover"></a>장애 조치/인수

이미 첫 번째 섹션에서 중요하게 언급했듯이 표준 정상 종료를 사용하여 클러스터 장애 조치 또는 SAP HANA HSR 인수를 테스트하면 안됩니다. 대신, 커널 패닉을 트리거하거나, 리소스 마이그레이션을 강제 적용하거나, VM의 OS 수준에서 모든 네트워크를 종료하는 것과 같은 작업을 수행하는 것이 좋습니다. 또 다른 방법으로 **crm \<node\> standby** 명령이 있습니다. [여기][sles-12-ha-paper]에 있는 SUSE 문서도 참조하세요. 아래에는 클러스터 장애 조치를 강제 적용하는 세 가지 샘플 명령이 나와 있습니다.

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

계획된 유지 관리 섹션에서도 설명한 대로 클러스터 작업을 모니터링하는 좋은 방법은 **watch** 명령으로 **SAPHanaSR-showAttr**을 실행하는 것입니다.

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

또한 SAP Python 스크립트에서 제공되는 SAP HANA 자산 상태를 살펴보는 것이 좋습니다. 이 상태 값은 클러스터 설정에서 찾고 있는 값입니다. 이는 작업자 노드 실패를 고려할 때 분명해집니다. 작업자 노드가 중단되면 SAP HANA에서 전체 스케일 아웃 시스템의 상태에 대한 오류를 즉시 반환하지 않습니다. 불필요한 장애 조치를 방지하기 위해 몇 가지 재시도가 있습니다. 상태가 Ok(반환 값 4)에서 error(반환 값 1)로 변경되는 경우에만 클러스터에서 반응합니다. 따라서 **SAPHanaSR-showAttr**의 출력에서 **offline**(오프라인) 상태의 VM이 표시되지만 주 및 보조 노드를 전환하는 작업이 아직 없는 경우에는 올바른 것입니다. SAP HANA에서 오류를 반환하지 않는 한 클러스터 작업은 트리거되지 않습니다.

SAP Python 스크립트를 다음과 같이 호출하여 SAP HANA 자산 상태를 \<HANA SID\>adm 사용자로 모니터링할 수 있습니다(경로를 조정해야 할 수도 있음).

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

이 명령의 출력은 아래 샘플과 같습니다. **Host Status**(호스트 상태) 열뿐만 아니라 **overall host status**(전체 호스트 상태)도 중요합니다. 실제 출력은 실제로 추가 열로 인해 더 넓습니다.
이 문서 내에서 출력 테이블을 더 쉽게 읽을 수 있도록 오른쪽 열의 대부분이 제거되었습니다.

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


현재 클러스터 작업을 확인할 수 있는 또 다른 명령이 있습니다. 주 사이트의 마스터 노드가 중단되면 아래의 명령 및 출력의 끝 부분을 참조하세요. 이전의 보조 마스터 노드(**hso-hana-vm-s2-0**)를 새 주 마스터로 **승격**시키는 것과 같은 전환 작업 목록을 볼 수 있습니다. 모든 항목이 정상이고 모든 작업이 완료되는 경우 다음 **전환 요약** 목록은 비어 있어야 합니다.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>계획된 유지 관리 

계획된 유지 관리와 관련하여 다양한 사용 사례가 있습니다. 하나의 질문은 OS 수준 및 디스크 구성 또는 HANA 업그레이드의 변경과 같이 인프라 유지 관리일 뿐입니다.
[여기][sles-zero-downtime-paper]와 같은 SUSE 문서 또는 [여기에 있는 다른 문서][sles-12-for-sap]에서 추가 정보를 찾을 수 있습니다. 이러한 문서에는 주 노드를 수동으로 마이그레이션하는 샘플도 포함되어 있습니다.

인프라 유지 관리 사용 사례를 확인하기 위해 강력한 내부 테스트가 수행되었습니다. 주 노드 마이그레이션과 관련된 모든 종류의 문제를 방지하기 위해 클러스터를 유지 관리 모드로 전환하기 전에 항상 주 노드를 마이그레이션하도록 결정했습니다. 이렇게 하면 클러스터에서 이전 상황(주 노드 및 보조 노드)을 잊어버릴 필요가 없습니다.

이와 관련하여 다음 두 가지 상황이 있습니다.

1. 현재 보조 노드에 대한 계획된 유지 관리. 
   이 경우 클러스터에 영향을 주지 않고 클러스터를 유지 관리 모드로 전환하고 보조 노드에서 작업을 수행할 수 있습니다

2. 현재 주 노드에 대한 계획된 유지 관리. 
   유지 관리하는 동안 사용자가 계속 작업할 수 있게 하려면 장애 조치를 강제 적용해야 합니다. 이 방법을 사용하면 SAP HANA HSR 수준뿐만 아니라 pacemaker에서 클러스터 장애 조치를 트리거해야 합니다. pacemaker 설정은 SAP HANA 인수를 자동으로 트리거합니다. 또한 클러스터를 유지 관리 모드로 전환하기 전에 장애 조치를 수행해야 합니다.

현재 보조 사이트에서 유지 관리하는 절차는 다음 단계와 같습니다.

1. 클러스터를 유지 관리 모드로 전환합니다.
2. 보조 사이트에서 작업을 수행합니다. 
3. 클러스터 유지 관리 모드를 종료합니다.

현재 주 사이트에서 유지 관리하는 절차는 다음과 같이 더 복잡합니다.

1. Pacemaker 리소스 마이그레이션을 통해 장애 조치/SAP HANA 인수를 수동으로 트리거합니다(아래 세부 정보 참조).
2. 이전 주 사이트의 SAP HANA가 클러스터 설정에 따라 종료됩니다.
3. 클러스터를 유지 관리 모드로 전환합니다.
4. 유지 관리 작업이 완료되면 이전 주 사이트를 새 보조 사이트로 등록합니다
5. 클러스터 구성을 정리합니다(아래 세부 정보 참조).
6. 클러스터 유지 관리 모드를 종료합니다.


리소스를 마이그레이션(예: 장애 조치 강제 적용)하면 클러스터 구성에 항목이 추가됩니다. 이러한 항목을 정리한 후에 유지 관리 모드를 종료해야 합니다. 샘플은 다음과 같습니다.

첫 번째 단계는 msl 리소스를 현재 보조 마스터 노드로 마이그레이션하여 클러스터 장애 조치를 강제 적용하는 것입니다. 아래 명령에서는 "이동 제약 조건"이 만들어졌다는 경고를 제공합니다.

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


**SAPHanaSR-showAttr** 명령을 통해 장애 조치 프로세스를 확인합니다. 클러스터 상태를 모니터링하는 데 도움이 되는 것은 전용 셸 창을 열고 **watch** 명령으로 시작하는 것입니다.

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

출력에는 수동 장애 조치가 반영되어야 합니다. 이전 보조 마스터 노드(이 샘플의 **hso-hana-vm-s2-0**)가 **승격**되고, 이전의 주 사이트(이전 주 마스터 노드인 **hso-hana-vm-s1-0**에 대한 **lss** 값(**1**))가 중지되었습니다. 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

클러스터 장애 조치 및 SAP HANA 인수 후에는 pacemaker 섹션에서 설명한 대로 클러스터를 유지 관리 모드로 전환합니다.

**SAPHanaSR-showAttr** 또는 **crm status** 명령은 리소스 마이그레이션에서 만든 제약 조건에 대해 아무것도 표시하지 않습니다. 이러한 제약 조건을 표시할 수 있는 한 가지 옵션은 다음 명령을 사용하여 전체 클러스터 리소스 구성을 표시하는 것입니다.

<pre><code>
crm configure show
</code></pre>

클러스터 구성 내에서 이전의 수동 리소스 마이그레이션으로 인한 새 위치 제약 조건을 찾습니다. 예제는 다음과 같습니다(**location cli-** 로 시작하는 항목).

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

안타깝게도 이러한 제약 조건은 전체 클러스터 동작에 영향을 줄 수 있습니다. 따라서 전체 시스템 백업을 가져오기 전에 이러한 제약 조건을 다시 제거해야 합니다. **unmigrate** 명령을 사용하면 이전에 만든 위치 제약 조건을 정리할 수 있습니다. 이름 지정은 다소 혼란스러울 수 있습니다. 리소스를 마이그레이션된 원본 VM으로 다시 마이그레이션하려고 시도한다는 것은 아닙니다. 위치 제약 조건만 제거하고 명령을 실행할 때 해당 정보도 반환합니다.


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

유지 관리 작업이 완료되면 pacemaker 도구 섹션에서 표시한 대로 클러스터 유지 관리 모드를 중지합니다.



## <a name="hbreport-to-collect-log-files"></a>로그 파일을 수집하는 hb_report

pacemaker 클러스터 문제를 분석하려면 **hb_report** 유틸리티를 실행하는 것이 도움이 되며, SUSE 고객 지원팀에도 요청해야 합니다. 중요한 로그 파일을 모두 수집하여 발생한 상황을 분석할 수 있도록 합니다. 다음은 특정 인시던트가 발생한 시작 및 종료 시간을 사용하는 샘플 호출입니다('중요 참고 사항'의 첫 번째 섹션 참조).

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

이 명령은 압축된 로그 파일을 저장한 위치를 유출합니다.

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

그런 다음, 표준 **tar** 명령을 사용하여 개별 파일을 추출할 수 있습니다.

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

추출된 파일을 살펴보면 모든 로그 파일을 찾을 수 있습니다. 이러한 파일의 대부분은 클러스터의 모든 노드에 대해 별도의 디렉터리에 저장되어 있습니다.

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


지정된 시간 범위 내에서 현재 마스터 노드인 **hso-hana-vm-s1-0**은 중단되었습니다. **journal.log**에서 이 이벤트와 관련된 항목을 찾을 수 있습니다.

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

또 다른 예로 새 주 마스터 노드가 된 보조 마스터 노드의 pacemaker 로그 파일이 있습니다. 다음은 중단된 주 마스터 노드의 상태가 **offline**으로 설정되었음을 보여 주는 발췌 부분입니다.

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


아래에는 클러스터 사이트 2의 SAP HANA global.ini 파일에서 발췌한 내용이 나와 있습니다. 이는 SAP HANA 노드 간 통신 및 HSR에 대해 서로 다른 네트워크를 사용하기 위한 호스트 이름 확인 항목을 보여 주는 예입니다.

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>HAWK

클러스터 솔루션은 셸 수준의 모든 명령과 비교하여 메뉴와 그래픽을 선호하는 사용자에게 멋진 GUI를 제공하는 브라우저 인터페이스도 제공합니다.
브라우저 인터페이스를 사용하려면 아래에 표시된 URL을 가져와서 **\<node\>** 를 실제 SAP HANA 노드로 바꾼 다음, 클러스터의 자격 증명(**hacluster** 사용자)을 입력합니다.

<pre><code>
https://&ltnode&gt:7630
</code></pre>

다음 스크린샷에서는 클러스터 대시보드를 보여 줍니다.


![HAWK 클러스터 대시보드](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


두 번째 스크린샷에서는 계획된 유지 관리 섹션에서 설명한 대로 클러스터 리소스 마이그레이션으로 인해 발생한 위치 제약 조건의 예를 볼 수 있습니다.


![HAWK 제약 조건 나열](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


또 다른 멋진 기능은 다음 스크린샷과 같이 **HAWK**의 **기록**에 **hb_report** 출력(**hb_report** 섹션 참조)을 업로드할 수 있다는 것입니다.

![HAWK hb_report 출력 업로드](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

그러면 **기록 탐색기**에서 **hb_report** 출력에 포함된 모든 클러스터 전환을 수행할 수 있습니다.

![HAWK hb_report 출력 내 전환 살펴보기](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

마지막 스크린샷에서는 주 마스터 노드(**hso-hana-vm-s1-0** 노드) 크래시에서 클러스터가 반응하여 현재 보조 노드를 새 마스터 노드(**hso-hana-vm-s2-0**)로 승격시키고 있음을 보여 주는 단일 전환의 세부 정보 섹션을 볼 수 있습니다.

![HAWK 단일 전환 살펴보기](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>다음 단계

이 문제 해결 가이드는 스케일 아웃 구성의 SAP HANA 고가용성에 대한 것입니다. 데이터베이스 외에도 SAP 자산 내의 또 다른 중요한 구성 요소는 SAP NetWeaver 스택입니다. 다음으로, [이 문서][sap-nw-ha-guide-sles]에서 설명하는 SUSE Enterprise Linux Server를 사용하는 Azure 가상 머신의 SAP NetWeaver 고가용성을 참조해야 합니다.

