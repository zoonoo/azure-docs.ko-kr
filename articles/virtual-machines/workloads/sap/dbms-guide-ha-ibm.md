---
title: Azure virtual machines (Vm)에서 IBM Db2 HADR 설정 | Microsoft Docs
description: Azure virtual machines (Vm)에서 IBM Db2 LUW의 고가용성을 설정 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: a74dd1a932cac41081786f76938a5b35de62d878
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689705"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Pacemaker 사용 하 여 SUSE Linux Enterprise Server의 Azure Vm에서 IBM Db2 LUW의 고가용성

Linux, UNIX에서 Windows (LUW) 용 IBM Db2 [높은 가용성 및 재해 복구 (HADR) 구성](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) 주 데이터베이스 인스턴스를 실행 하는 노드 하나 및 보조 데이터베이스 인스턴스를 실행 하는 노드가 하나 이상으로 구성 됩니다. 기본 데이터베이스 인스턴스에 대 한 변경 내용은 복제할지 보조 데이터베이스 인스턴스에 동기적 또는 비동기적으로 구성에 따라 합니다. 

이 문서에서는 배포 및 Azure virtual machines (Vm) 구성 하 고, 클러스터 프레임 워크를 설치 하 고 IBM Db2 LUW HADR 구성으로 설치 하는 방법을 설명 합니다. 

문서에는 설치 및 HADR 또는 SAP 소프트웨어 설치를 사용 하 여 IBM Db2 LUW를 구성 하는 방법을 다루지 않습니다. 이러한 작업을 수행 하는 데 도움이, IBM 및 SAP 설치 설명서에 대 한 참조가 제공 됩니다. 이 문서에서는 Azure 환경에 관련 된 부분에 중점을 둡니다. 

지원 되는 IBM Db2 버전 10.5 이상을 경우 SAP note에 설명 된 대로 [1928533]합니다.

설치를 시작 하기 전에 다음 SAP note 및 설명서를 참조 하세요.

| SAP 참고 | 설명 |
| --- | --- |
| [1928533] | Azure의 SAP 응용 프로그램: 지원 제품 및 Azure VM 유형 |
| [2015553] | Azure의 SAP: 필수 구성 요소 지원 |
| [2178632] | Azure의 SAP 용 주요 모니터링 메트릭 |
| [2191498] | Azure 포함 Linux의 SAP: 고급 모니터링 |
| [2243692] | Azure (IaaS) VM에서 Linux의 경우: SAP 라이선스 문제 |
| [1984787] | SUSE LINUX Enterprise Server 12: 설치 참고 사항 |
| [1999351] | SAP용 고급 Azure 모니터링 문제 해결 |
| [2233094] | DB6: Azure에서 Linux, UNIX 및 Windows-추가 정보에 대 한 IBM Db2를 사용 하는 응용 프로그램을 SAP |
| [1612105] | DB6: Db2 hadr에 대 한 FAQ |


| 문서화 | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux에 모든 필요한 SAP Note |
| [Azure Virtual Machines 계획 및 Linux에서 SAP에 대 한 구현을] [ planning-guide] 가이드 |
| [Linux에서 SAP 용 azure Virtual Machines 배포] [ deployment-guide] (이 문서) |
| [Azure 가상 머신에 Linux에서 SAP에 대 한 관리 system(DBMS) 배포 데이터베이스] [ dbms-guide] 가이드 |
| [SAP 워크 로드를 Azure의 계획 및 배포 검사 목록][azr-sap-plancheck] |
| [SAP 애플리케이션 12 SP3용 SUSE Linux Enterprise Server 모범 사례 가이드][sles-for-sap-bp] |
| [SUSE Linux Enterprise 고가용성 확장 12 SP3][sles-ha-guide] |
| [SAP 워크 로드 용 IBM Db2 Azure Virtual Machines DBMS 배포][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>개요
HADR 사용 하 여 IBM Db2 LUW 두 개 이상의 Azure virtual machines에서 배포 된에 설치 되어 고가용성을 달성 하는 [Azure 가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) 또는 걸쳐 [Azure 가용성 영역](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)합니다. 

다음 그래픽에는 두 개의 데이터베이스 서버 Azure Vm의 설치를 표시합니다. 두 데이터베이스 서버 Azure Vm 연결 자체 저장소가 있고 실행 중 이며 있습니다. HADR, Azure Vm 중 하나에 단일 데이터베이스 인스턴스가는 기본 인스턴스의 역할입니다. 모든 클라이언트는이 기본 인스턴스로 연결 됩니다. Db2 트랜잭션 로그에 데이터베이스 트랜잭션의 모든 변경 내용은 로컬로 유지 됩니다. 트랜잭션 로그 레코드를 로컬로 유지 하는 대로 레코드를 전송할지 TCP/IP를 통해 데이터베이스 인스턴스에 두 번째 데이터베이스 서버, 대기 중인 서버 또는 대기 인스턴스. 대기 인스턴스 롤포워드 전송 된 트랜잭션 로그 레코드에서 로컬 데이터베이스를 업데이트 합니다. 이 이렇게 하면 대기 서버는 주 서버와 동기화 유지 됩니다.

HADR 사용 되는 복제 기능에만 합니다. 없음 오류 감지 및 자동 없는 인수 또는 장애 조치 기능 있습니다. 인수 또는 대기 중인 서버에 전송할 데이터베이스 관리자가 수동으로 시작 해야 합니다. 자동 인수 및 오류 감지를 위해 Linux Pacemaker 클러스터링 기능을 사용할 수 있습니다. Pacemaker는 두 개의 데이터베이스 서버 인스턴스를 모니터링합니다. 때 주 데이터베이스 서버 인스턴스 충돌할 때 Pacemaker 시작 하는 *자동* 대기 서버에서 HADR 인수입니다. Pacemaker는 또한 새로운 주 서버에 가상 IP 주소가 할당 되어 있는지 확인 합니다.

![IBM Db2 고가용성 개요](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

SAP가 기본 데이터베이스에 연결 된 응용 프로그램 서버, 가상 호스트 이름과 가상 IP가 필요 합니다. SAP 응용 프로그램 서버는 장애 조치 시 새 주 데이터베이스 인스턴스에 연결 됩니다. Azure 환경에는 [Azure load balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) IBM Db2 HADR에 필요한 방식으로 가상 IP 주소를 사용 해야 하는 합니다. 

다음 이미지를 항상 사용 가능한 SAP system 설치에 HADR 및 Pacemaker를 사용 하 여 IBM Db2 LUW를 맞추는 방식을 완벽 하 게 이해할 수 있도록 하려면 IBM Db2 데이터베이스를 기반으로 SAP 시스템의 항상 사용 가능한 설정의 개요를 제공 합니다. 이 문서는 IBM Db2만 다루지만 SAP 시스템의 다른 구성 요소를 설정 하는 방법에 대 한 다른 문서에 대 한 참조를 제공 합니다.

![IBM DB2 고가용성 전체 환경 개요](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>필요한 단계에 대 한 개략적인 개요
IBM Db2 구성에 배포 하려면 다음이 단계를 수행 해야 합니다.

  + 환경을 계획 합니다.
  + Vm을 배포 합니다.
  + SUSE Linux를 업데이트 하 고 파일 시스템을 구성 합니다.
  + 설치 및 Pacemaker를 구성 합니다.
  + 설치할 [고가용성 NFS][nfs-ha]합니다.
  + 설치할 [별도 클러스터에 ASCS/ERS][ascs-ha]합니다.
  + 분산 및 고가용성 옵션 (SWPM)를 사용 하 여 IBM Db2 데이터베이스를 설치 합니다.
  + 설치 하 고 보조 데이터베이스 노드를 만들고 인스턴스, HADR 구성 합니다.
  + HADR이 작동 하는지 확인 합니다.
  + IBM Db2 컨트롤 Pacemaker 구성을 적용 합니다.
  + Azure Load Balancer를 구성 합니다.
  + 기본 설치 및 대화 상자 응용 프로그램 서버입니다.
  + 확인 하 고 SAP 응용 프로그램 서버의 구성을 적용 합니다.
  + 장애 조치 및 인수 테스트를 수행 합니다.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>HADR 사용 하 여 IBM Db2 LUW 호스팅을 위한 Azure 인프라 계획

배포를 실행 하기 전에 계획 프로세스를 완료 합니다. 계획 구성입니다. HADR에 Azure 사용 하 여 Db2 배포 하기 위한 기반을 구축 합니다. IMB Db2 LUW (SAP 환경의 데이터베이스 일부)에 대 한 계획의 일부가 되도록 해야 하는 주요 요소는 다음과에서 같습니다.

| 항목 | 간단한 설명 |
| --- | --- |
| Azure 리소스 그룹을 정의 합니다. | 리소스 그룹을 배포할 VM, VNet, Azure Load Balancer 및 기타 리소스입니다. 기존 또는 새 수 있습니다. |
| 가상 네트워크 / 서브넷 정의 | IBM Db2 및 Azure Load Balancer에 대 한 Vm 배포 되는 위치 기존 또는 새롭게 만든 수 있습니다. |
| IBM Db2 LUW 호스팅 virtual machines | VM 크기, 저장소, 네트워킹, IP 주소입니다. |
| 가상 호스트 이름 및 IBM Db2 데이터베이스에 대 한 가상 IP| 가상 IP 또는 호스트 이름 SAP 응용 프로그램 서버의 연결에 사용 되는입니다. **db-virt-hostname**, **db-virt-ip**. |
| Azure 펜스 | Azure 펜스 하거나 SBD 펜싱 (권장). 분할 브레인 상황을 방지 하려면 메서드 방지 됩니다. |
| SBD VM | SBD 가상 머신 크기, 저장소, 네트워크를 선택 합니다. |
| Azure Load Balancer | Basic 또는 Standard (권장), Db2 데이터베이스 (권장 사항은 62500)에 대 한 포트 프로브 **프로브 포트**합니다. |
| 이름 확인| 이름 확인 환경의 작동 방식 DNS 서비스를 사용 하는 것이 좋습니다. 로컬 호스트 파일을 사용할 수 있습니다. |
    
Azure에서 Linux Pacemaker에 대 한 자세한 내용은 참조 하세요. [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)합니다.

## <a name="deployment-on-suse-linux"></a>SUSE linux 배포

IBM Db2 LUW 용 리소스 에이전트는 SAP 응용 프로그램용 SUSE Linux Enterprise Server에 포함 됩니다. 이 문서에 설명 된 설치 프로그램에 대 한 SAP 응용 프로그램용 SUSE Linux 서버를 사용 해야 합니다. Azure Marketplace는 새 Azure 가상 머신을 배포 하는 데 사용할 수 있는 SAP Applications 12에 대 한 SUSE Enterprise Server에 대 한 이미지를 포함 합니다. Azure VM Marketplace에서 VM 이미지를 선택 하면 Azure Marketplace를 통해 SUSE에 의해 제공 되는 다양 한 지원 또는 서비스 모델 고려해 야 합니다. 

### <a name="hosts-dns-updates"></a>호스트: DNS 업데이트
가상 호스트 이름을 포함 한 모든 호스트 이름 목록을 확인 하 고 호스트 이름 확인에 적절 한 IP 주소를 사용 하도록 설정 하려면 DNS 서버를 업데이트 합니다. DNS 서버에 존재 하지 않거나 업데이트 하 고 DNS 항목을 만들 수 없습니다,이 시나리오에 참여 하는 개별 Vm의 로컬 호스트 파일을 사용 해야 합니다. 호스트 파일 항목을 사용 하는 경우 항목은 SAP 시스템 환경에서 모든 Vm에 적용 되어 있는지 확인 합니다. 하지만 이상적으로 Azure로 확장 하는 DNS를 사용 하는 권장,


### <a name="manual-deployment"></a>수동 배포

선택한 OS의 IBM Db2 LUW 용 IBM/SAP에서 지원 되는지 확인 합니다. Azure Vm 및 Db2 릴리스에 대해 지원 되는 운영 체제 버전 목록은 SAP note에서 [1928533]합니다. 개별 Db2 릴리스에서 OS 릴리스 목록을 SAP 제품 가용성 매트릭스에서 사용할 수 있습니다. 이상에서이 Azure와 관련 된 성능 향상으로 인해 항상 SLES 12 SP3의 최소 권장 SUSE Linux 버전.

1. 만들기 또는 리소스 그룹을 선택 합니다.
1. 만들거나 가상 네트워크 및 서브넷을 선택 합니다.
1. Azure 가용성 집합을 만들거나 가용성 영역을 배포 합니다.
    + 가용성 집합에 대 한 최대 업데이트 도메인을 2로 설정 합니다.
1. 1 가상 컴퓨터를 만듭니다.
    + Azure Marketplace에서 SAP 이미지에 대 한 SLES를 사용 합니다.
    + 3 단계에서 만든 Azure 가용성 집합 또는 가용성 영역을 선택 합니다.
1.  가상 머신 2를 만듭니다.
    + Azure Marketplace에서 SAP 이미지에 대 한 SLES를 사용 합니다.
    + 3 단계에서 만든 또는 가용성 영역 (없습니다 3 단계와 동일한 영역)을 선택 하면 Azure 가용성 집합을 선택 합니다.
1. Vm에 데이터 디스크를 추가 하 고 문서의 파일 시스템 설정의 권장 사항 확인 [SAP 워크 로드 용 IBM Db2 Azure Virtual Machines DBMS 배포][dbms-db2]합니다.

## <a name="create-the-pacemaker-cluster"></a>Pacemaker 클러스터 만들기
    
이 IBM Db2 서버에 대 한 기본 Pacemaker 클러스터를 만들려면 참조 [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정][sles-pacemaker]합니다. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Db2 luw 용 IBM 및 SAP 환경 설치

IBM Db2 LUW에 따라 SAP 환경 설치를 시작 하기 전에 다음 문서를 검토 합니다.

+ Azure 설명서
+ SAP 설명서
+ IBM 설명서

이 설명서에 대 한 링크는이 기사의 소개 섹션에 제공 됩니다.

IBM Db2 LUW NetWeaver 기반 응용 프로그램을 설치 하는 방법에 대 한 SAP 설치 설명서를 확인 합니다.

사용 하 여 SAP 도움말 포털의 지침을 찾을 수 있습니다 합니다 [SAP 설치 가이드 Finder][sap-instfind]합니다.

다음 필터를 설정 하 여 포털에 표시 되는 지침 수를 줄일 수 있습니다.

- 하고 싶어요: "새 시스템 설치"
- 데이터베이스: "Linux, Unix 및 Windows 용 IBM Db2"
- SAP NetWeaver 버전, 스택 구성 또는 운영 체제에 대 한 추가 필터

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>HADR 사용 하 여 IBM Db2 LUW 설정에 대 한 설치 힌트

기본 IBM Db2 LUW 데이터베이스 인스턴스를 설정 하려면:

- 고가용성을 지원 하거나 분산된 옵션을 사용 합니다.
- SAP ASCS/ERS 및 데이터베이스 인스턴스를 설치 합니다.
- 새로 설치 된 데이터베이스의 백업을 수행 합니다.


> [!IMPORTANT] 
> 설치 하는 동안 설정 된 "데이터베이스 통신 포트" 적어 둡니다. 데이터베이스 인스턴스 모두에 대해 동일한 포트 번호 여야 합니다.

SAP 동종 시스템 복사 절차를 사용 하 여 대기 데이터베이스 서버를 설정 하려면 다음이 단계를 실행 합니다.

1. 선택 합니다 **시스템 복사본** 옵션 > **체제를 대상** > **Distributed** > **데이터베이스 인스턴스**합니다.
1. 복사 방법으로 선택 **동종 시스템** 대기 중인 서버 인스턴스에서 백업을 복원 하려면 backup을 사용할 수 있도록 합니다.
1. 동종 시스템 복사본에 대 한 데이터베이스를 복원 하는 종료 단계에 도달 하면 설치 관리자를 종료 합니다. 기본 호스트의 백업에서 데이터베이스를 복원 합니다. 모든 후속 설치 단계는 주 데이터베이스 서버에서 이미 실행 합니다.
1. IBM db2 HADR를 설정 합니다.

   > [!NOTE]
   > 설치 및 Azure 및 Pacemaker에 관련 된 구성: SAP Software Provisioning Manager를 통해 설치 절차 중 IBM Db2 LUW 용 고가용성에 대 한 명시적 질문 포함 되어 있습니다.
   >+ 선택 하지 마세요 **IBM Db2 pureScale**합니다.
   >+ 선택 하지 마세요 **Multiplatforms에 대 한 IBM Tivoli 시스템 자동화 설치**합니다.
   >+ 선택 하지 마세요 **클러스터 구성 파일을 생성할**합니다.

   SBD 장치를 Linux pacemaker를 사용 하면 다음 Db2 HADR 매개 변수를 설정 합니다.
   + HADR 피어 기간 (초) (HADR_PEER_WINDOW) = 300  
   + HADR 시간 제한 값 (HADR_TIMEOUT) = 60

   Pacemaker Azure 펜스 에이전트를 사용 하는 경우에 다음 매개 변수를 설정 합니다.
   + HADR 피어 기간 (초) (HADR_PEER_WINDOW) = 900  
   + HADR 시간 제한 값 (HADR_TIMEOUT) = 60

초기 장애 조치/인수 테스트에 따라 위의 매개 변수를 사용 하는 것이 좋습니다. 이 이러한 매개 변수 설정을 사용 하 여 장애 조치 및 인수의 적절 한 기능에 대 한 테스트 하는 필수입니다. 개별 구성을 달라질 수 있으므로 매개 변수를 조정을 해야 합니다. 

> [!IMPORTANT]
> 정상 모드를 사용 하 여 HADR 구성 사용 하 여 IBM Db2에만 사용 합니다. 기본 데이터베이스 인스턴스를 시작 하기 전에 보조 또는 대기 데이터베이스 인스턴스를 실행 이어야 합니다.

이 문서에 설명 된 절차와 데모 목적에 대 한 SID 데이터베이스가 **PTR**합니다.

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR 확인
HADR을 구성한 후 상태를 주 및 대기 노드에서 피어와 연결 됨은 다음 검사를 수행 합니다.

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker 구성

자동 장애 조치에 대 한 Pacemaker를 사용 하 여 노드 오류가 발생할 경우 Db2 인스턴스 및 Pacemaker를 적절 하 게 구성 해야 합니다. 이 섹션에서는이 유형의 구성 설명 합니다.

다음 항목 중 하나를 사용 하 여 붙습니다.

- **[A]**: 모든 노드에 적용
- **[1]**: 노드 1에만 적용 
- **[2]**: 노드 2에만 적용

**[A]**  Pacemaker 구성에 대 한 필수 구성 요소:
1. 사용자 db2 사용 하 여 두 데이터베이스 서버를 종료\<sid > db2stop를 사용 하 여 합니다.
1. Db2에 대 한 셸 환경을 변경\<sid > 사용자 */bin/ksh*합니다. Yast 도구를 사용 하는 것이 좋습니다. 


### <a name="pacemaker-configuration"></a>Pacemaker 구성

**[1]**  IBM Db2 HADR 별 Pacemaker 구성:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  IBM Db2 만들 리소스:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  IBM Db2 시작 리소스:
* 유지 관리 모드에서 Pacemaker를 배치 합니다.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  클러스터 상태가 정상이 고 모든 리소스가 시작 됩니다. 이 아닌 경우 중요 한 리소스에서 실행 중인 노드
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 개 노드 구성
# <a name="5-resources-configured"></a>5 개의 리소스 구성

# <a name="online--azibmdb01-azibmdb02-"></a>온라인: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>리소스의 전체 목록:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith sbd (stonith:external / sbd): 시작된 azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>Resource Group: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       시작된 azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      시작된 azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>마스터/슬레이브 집합: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>마스터: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>슬레이브: [azibmdb01]
</pre>

> [!IMPORTANT]
> Pacemaker를 관리 해야 Pacemaker 도구를 사용 하 여 클러스터 된 Db2 인스턴스. Db2stop 등의 db2 명령을 사용할 경우 Pacemaker는 리소스의 실패로 작업을 검색 합니다. 유지 관리를 수행 하는 경우 노드 또는 리소스를 유지 관리 모드에 넣을 수 있습니다. Pacemaker 리소스 모니터링을 일시 중단 하 고 일반 db2 관리 명령을 사용할 수 있습니다.


### <a name="configure-azure-load-balancer"></a>Azure Load Balancer 구성
Azure Load Balancer를 구성 하려면 사용 하는 권장 합니다 [Azure 표준 Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) ; 다음을 수행 하 고

1. 프런트 엔드 IP 풀을 만듭니다.

   a. Azure portal에서 Azure Load Balancer를 열고 **프런트 엔드 IP 풀**를 선택한 후 **추가**합니다.

   b. 새 프런트 엔드 IP 풀의 이름을 입력 합니다 (예를 들어 **Db2 연결**).

   다. 설정 합니다 **할당** 에 **정적**, IP 주소를 입력 하 고 **가상 IP** 시작 부분에 정의 합니다.

   d. **확인**을 선택합니다.

   e. 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.

1. 백 엔드 풀을 만듭니다.

   a. Azure portal에서 Azure Load Balancer를 열고 **백 엔드 풀**를 선택한 후 **추가**합니다.

   b. 새 백 엔드 풀의 이름을 입력 합니다 (예를 들어 **Db2-백 엔드**).

   다. **가상 머신 추가**를 선택합니다.

   d. 가용성 집합 또는 IBM Db2 데이터베이스를 이전 단계에서 만든 호스트 가상 컴퓨터를 선택 합니다.

   e. IBM Db2 클러스터의 가상 컴퓨터를 선택 합니다.

   f. **확인**을 선택합니다.

1. 상태 프로브를 만듭니다.

   a. Azure portal에서 Azure Load Balancer를 열고 **상태 프로브**, 선택한 **추가**합니다.

   b. 새 상태 프로브의 이름 입력 (예를 들어 **Db2 hp**).

   다. 선택 **TCP** 프로토콜 및 포트 **62500**합니다. 유지 합니다 **간격** 값으로 설정 **5**를 유지 하 고는 **비정상 임계값** 값으로 설정 **2**합니다.

   d. **확인**을 선택합니다.

1. 부하 분산 규칙을 만듭니다.

   a. Azure portal에서 Azure Load Balancer를 열고 **부하 분산 규칙**를 선택한 후 **추가**합니다.

   b. 새 부하 분산 장치 규칙의 이름을 입력 합니다 (예를 들어 **Db2 SID**).

   다. 프런트 엔드 IP 주소, 백 엔드 풀 및 이전에 만든 상태 프로브 선택 (예를 들어 **Db2 프런트 엔드**).

   d. 유지 된 **프로토콜** 로 설정 **TCP**, 포트를 입력 하 고 *데이터베이스 통신 포트*합니다.

   e. **유휴 상태 시간 제한**을 30분으로 증가시킵니다.

   f. **부동 IP를 사용하도록 설정**했는지 확인합니다.

   g. **확인**을 선택합니다.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>연결에 대 한 가상 IP를 사용 하는 SAP 프로필 변경
SAP HADR 구성의 기본 인스턴스에 연결 하려면 응용 프로그램 계층을 정의 하 고 Azure Load Balancer에 대해 구성 된 가상 IP 주소를 사용 해야 합니다. 다음 변경이 필요 합니다.

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>기본 설치 및 대화 상자 응용 프로그램 서버

대화를 Db2 HADR 구성에 대해 응용 프로그램 서버를 사용 하 여 가상 호스트 이름을 기본 설치할 때 구성에 대 한 선택입니다. 

Db2 HADR 구성 생성 하기 전에 설치를 수행한 경우 이전 섹션에서와 같이 SAP Java 스택에 대 한 설명 된 대로 변경 내용을 확인 합니다.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java 또는 Java 스택 시스템 JDBC URL 확인

J2EE 구성 도구를 사용 하 여 확인 하거나 JDBC URL을 업데이트 합니다. X 해야 하는 J2EE 구성 도구는 그래픽 도구 이기 때문에 서버를 설치 합니다.
 
1. J2EE 인스턴스의 기본 응용 프로그램 서버에 로그인 하 고 실행 합니다.
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. 왼쪽된 프레임에서 선택 **보안 저장소**합니다.
1. 오른쪽 프레임 선택 키 jdbc/풀 / \<SAPSID>/url 합니다.
1. 가상 호스트 이름을 JDBC URL에 호스트 이름을 변경 합니다.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. 선택 **추가**합니다.
1. 변경 내용을 저장 하려면 왼쪽 위에 있는 디스크 아이콘을 선택 합니다.
1. 구성 도구를 닫습니다.
1. Java 인스턴스를 다시 시작 합니다.

## <a name="configure-log-archiving-for-hadr-setup"></a>HADR 설치에 대 한 로그 보관 구성
HADR 설치에 대 한 보관 Db2 로그를 구성 하려면 주 및 대기 데이터베이스에 모든 로그 보관 위치에서 자동 로그 검색 기능을 구성 하는 것이 좋습니다. 주 및 대기 데이터베이스 인스턴스 수 로그 파일을 보관 하는 데는 데이터베이스 중 하나에 모든 로그 보관 위치에서 로그 보관 파일을 검색할 수 있어야 합니다. 

보관 로그는 주 데이터베이스에 의해서만 수행 됩니다. HADR 데이터베이스 서버의 역할을 변경 하는 경우, 오류가 발생 하는 경우 새 주 데이터베이스는 로그를 보관 하는 일을 담당 합니다. 여러 로그 보관 위치를 설정한 경우 로그를 두 번 보관 될 수 있습니다. 로컬 또는 원격 만회 시 이전 주 서버에서 새 주 서버의 활성 로그 위치에 보관 된 로그를 수동으로 복사 해야도 합니다.

두 노드 모두에서 로그 기록 되는 일반 NFS 공유를 구성 하는 것이 좋습니다. NFS 공유를 항상 사용 가능 해야 합니다. 

전송 또는 프로필 디렉터리에 대 한 기존 항상 사용 가능한 NFS 공유를 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성][nfs-ha] 
- [SAP 응용 프로그램에 대 한 Azure NetApp 파일을 사용 하 여 SUSE Linux Enterprise Server의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure NetApp 파일](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (NFS 공유 만들려면)


## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

이 섹션에서는 Db2 HADR 설치 프로그램을 테스트 하는 방법을 설명 합니다. *모든 테스트 root 사용자로 로그인 하는 것으로 가정* IBM Db2 주 데이터베이스에서 실행 되 고는 *azibmdb01* 가상 머신.

모든 테스트 사례에 대 한 초기 상태는 여기에서 설명한: (crm_mon-r 또는 crm 상태)

- **상태를 crm** 는 Pacemaker 상태 실행 시의 스냅숏입니다. 
- **crm_mon-r** 은 Pacemaker 상태의 연속 출력

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

SAP 시스템의 원래 상태는 트랜잭션 DBACOCKPIT에 설명 되어 있습니다 > 구성 > 개요, 다음 이미지에 표시 된 대로:

![DBACockpit - Pre Migration](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>IBM Db2의 테스트 인수


> [!IMPORTANT] 
> 테스트를 시작 하기 전에 다음 사항을 확인 합니다
> * Pacemaker 모든 실패 한 작업 (crm status) 없습니다.
> * 제약 조건이 없는 위치 (leftovers 마이그레이션 테스트)
> * IBM Db2 HADR 동기화가 작동 합니다. 사용자 db2를 사용 하 여 확인\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


다음 명령을 실행 하 여 기본 Db2 데이터베이스를 실행 하는 노드 마이그레이션:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

마이그레이션이 완료 되 면 crm 상태 출력은 다음과 같습니다.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

SAP 시스템의 원래 상태는 트랜잭션 DBACOCKPIT에 설명 되어 있습니다 > 구성 > 개요, 다음 이미지에 표시 된 대로:

![DBACockpit - Post Migration](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

리소스 마이그레이션을 사용 하 여 crm 리소스 "마이그레이션"에 위치 제약 조건을 만듭니다. 위치 제약 조건은 삭제 해야 합니다. 위치 제약 조건 삭제 되는 경우 리소스를 장애 복구할 수 또는 원치 않는 takeovers 발생할 수 있습니다. 

리소스를 마이그레이션 돌아갑니다 *azibmdb01* 위치 제약 조건은 선택을 취소 하 고
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **crm 리소스 마이그레이션 \<res_name > <host>:** 위치 제약 조건을 만들고 인수를 사용 하 여 문제가 발생할 수 있습니다
- **crm 리소스 지우기 \<res_name >**: 위치 제약 조건 삭제
- **crm 리소스 정리 \<res_name >**: 리소스의 모든 오류를 지웁니다.

### <a name="test-the-fencing-agent"></a>펜싱 에이전트를 테스트 합니다.

이 경우에 SUSE Linux를 사용 하는 경우 수행 하는 것이 좋습니다 SBD 펜싱을 테스트 합니다.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

클러스터 노드 *azibmdb01* 를 다시 부팅 해야 합니다. IBM Db2 주 HADR 역할로 이동할 것 *azibmdb02*합니다. 때 *azibmdb01* 인스턴스는 인스턴스를 보조 데이터베이스의 역할을 이동 하 고 Db2 온라인으로 백업 합니다. 

Pacemaker 서비스를 다시 부팅된 이전 주에 자동으로 시작 되지 않으면 사용 하 여 수동으로 시작 해야 합니다.

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>인수를 수동 테스트

수동 인수에서 Pacemaker 서비스를 중지 하 여 테스트할 수 있습니다 *azibmdb01* 노드:
<pre><code>service pacemaker stop</code></pre>

status on *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

장애 조치 후 다시 시작할 수 있습니다 서비스에 *azibmdb01*합니다.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>HADR 주 데이터베이스를 실행 하는 노드에서 Db2 프로세스 중지

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Db2 인스턴스는 실패 하 고 Pacemaker는 다음 상태 보고:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker Db2 주 데이터베이스 인스턴스에 동일한 노드를 다시 시작 또는 보조 데이터베이스 인스턴스를 실행 하는 노드로 장애 조치 되 고 오류가 보고 됩니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>보조 데이터베이스 인스턴스를 실행 하는 노드에서 Db2 프로세스 중지

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

노드 가져옵니다를 명시 하지 못했으며 오류 보고
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Db2 인스턴스를 할당 하기 전에 보조 역할의 다시 시작을 가져옵니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR 주 데이터베이스 인스턴스를 실행 하는 노드에서 db2stop 강제를 통해 DB 중지

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

사용자 db2로\<sid > 명령을 db2stop 강제 실행 합니다.
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

오류를 발견 했습니다
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Db2 HADR 보조 데이터베이스 인스턴스를 주 역할로 승격
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR 주 데이터베이스 인스턴스를 실행 하는 노드에서 다시 시작 하 여 VM 충돌

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker는 주 인스턴스 역할에 보조 인스턴스를 승격 합니다. Vm 보조 역할에 이전 주 인스턴스로 이동 하 고 모든 서비스는 VM 다시 부팅 후에 완전히 복원 됩니다.

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>"중지"를 사용 하 여 HADR 주 데이터베이스 인스턴스를 실행 하는 VM 충돌

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

이러한 경우 Pacemaker는 기본 데이터베이스 인스턴스를 실행 하는 노드는 응답 하지 않는 검색 합니다.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

다음 단계에 대 한 확인 하는 것을 *분할 브레인* 상황입니다. 남아 있는 노드는 기본 데이터베이스 인스턴스를 마지막으로 실행 되는 노드가 다운 되는 확인 후에 리소스의 장애 조치 실행 됩니다.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


"중지" 노드를 발생 시 실패 한 노드를 Azure 관리 도구 (Azure portal, PowerShell 또는 Azure CLI)를 통해 다시 시작 해야 합니다. 실패 한 노드를 다시 온라인 상태가 되 면 보조 역할로 Db2 인스턴스를 시작 합니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>다음 단계
- [고가용성 아키텍처 및 SAP NetWeaver에 대 한 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

