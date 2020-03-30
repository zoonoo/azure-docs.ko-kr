---
title: Azure NetApp 파일이 있는 RHEL에서 SAP NW용 Azure VM 고가용성 | 마이크로 소프트 문서
description: Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 11119d193cd08944bdff4737e8182cc7bece0abc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351252"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>SAP 응용 프로그램에 대 한 Azure NetApp 파일 과 레드 햇 엔터프라이즈 리눅스에 SAP NetWeaver에 대 한 Azure 가상 머신 높은 가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

이 문서에서는 [Azure NetApp 파일을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)사용하여 가상 컴퓨터를 배포하고, 가상 컴퓨터를 구성하고, 클러스터 프레임워크를 설치하고, 가용성이 높은 SAP NetWeaver 7.50 시스템을 설치하는 방법에 대해 설명합니다.
예제 구성에서 설치 명령 등 ASCS 인스턴스는 숫자 00이고, ERS 인스턴스는 숫자 01이고, 주 응용 프로그램 인스턴스(PAS)는 02이고, 응용 프로그램 인스턴스(AAS)는 03입니다. SAP 시스템 ID QAS가 사용됩니다. 

데이터베이스 계층은 이 문서에서 자세히 다루지 않습니다.  

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* [Azure NetApp 파일 설명서][anf-azure-doc] 
* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전

* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2002167]에는 Red Hat Enterprise Linux에 권장되는 OS 설정이 있습니다.
* SAP Note [2009879]에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [pacemaker 클러스터의 SAP Netweaver](https://access.redhat.com/articles/3150081)
* 일반 RHEL 설명서
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)(고가용성 추가 기능 참조)
  * [RHEL 7.5에서 독립 실행형 리소스를 사용하여 SAP Netweaver용 ASCS/ERS 구성](https://access.redhat.com/articles/3569681)
  * [RHEL의 페이스메이커에서 독립실행형 큐 서버 2(ENSA2)로 SAP S/4HANA ASCS/ERS 구성](https://access.redhat.com/articles/3974941)
* Azure 관련 RHEL 설명서:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)(RHEL 고가용성 클러스터용 지원 정책 - Microsoft Azure Virtual Machines(클러스터 멤버))
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)(Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성)
* [Azure NetApp 파일을 사용하여 Microsoft Azure의 NetApp SAP 응용 프로그램][anf-sap-applications-azure]

## <a name="overview"></a>개요

SAP Netweaver 중앙 서비스에 대한 고가용성(HA)에는 공유 스토리지가 필요합니다.
지금까지 Red Hat Linux에서 이를 달성하기 위해서는 고가용성글루스터FS 클러스터를 별도로 구축해야 했습니다. 

이제 Azure NetApp 파일에 배포된 공유 저장소를 사용하여 SAP Netweaver HA를 달성할 수 있습니다. 공유 저장소에 Azure NetApp 파일을 사용하면 추가 [GlusterFS 클러스터가](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)필요하지 않습니다. SAP 넷위버 중앙 서비스(ASCS/SCS)의 HA에는 여전히 맥박 조정기가 필요합니다.

![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS 및 SAP HANA 데이터베이스는 가상 호스트 이름 및 가상 IP 주소를 사용합니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. [표준 로드 밸워서를](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)사용하는 것이 좋습니다. 다음 목록은 (A)SCS 및 ERS에 대한 별도의 프런트 엔드 IP가 있는 로드 밸러터의 구성을 보여 주며,

### <a name="ascs"></a>(A)SCS

* 프런트 엔드 구성
  * IP 주소 192.168.14.9
* 프로브 포트
  * 포트 620<strong>&lt;nr&gt;</strong>
* 로드 밸런싱 규칙
  * 표준 로드 밸로터를 사용하는 경우 **HA 포트를** 선택합니다.
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 36<strong>&lt;&gt; nr</strong> TCP
  * 39<strong>&lt;&gt; nr</strong> TCP
  * 81<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>ERS

* 프런트 엔드 구성
  * IP 주소 192.168.14.10
* 프로브 포트
  * 포트 621<strong>&lt;nr&gt;</strong>
* 로드 밸런싱 규칙
  * 표준 로드 밸로터를 사용하는 경우 **HA 포트를** 선택합니다.
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 33<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp 파일 인프라 설정 

SAP NetWeaver에는 전송 및 프로필 디렉터리에 대한 공유 스토리지가 필요합니다.  Azure NetApp 파일 인프라에 대한 설정을 진행하기 전에 [Azure NetApp 파일 설명서를][anf-azure-doc]숙지하십시오. 선택한 Azure 지역이 Azure NetApp 파일을 제공하는지 확인합니다. 다음 링크는 Azure 지역별 Azure NetApp 파일의 가용성을 보여 주며, [Azure 지역별 Azure NetApp 파일 가용성을 보여 주며, Azure 지역별 Azure NetApp 파일 가용성을 보여 주며,][anf-avail-matrix]Azure NetApp 파일 의 가용성을 보여 주면 됩니다.

Azure NetApp 파일은 여러 [Azure 지역에서](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)사용할 수 있습니다. Azure NetApp 파일을 배포하기 전에 Azure NetApp 파일 [등록 지침에][anf-register]따라 Azure NetApp 파일에 대한 온보딩을 요청합니다. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp 파일 리소스 배포  

이 단계는 [Azure 가상 네트워크를](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)이미 배포했다고 가정합니다. Azure NetApp 파일 리소스 및 Azure NetApp 파일 리소스가 탑재되는 VM은 동일한 Azure 가상 네트워크 또는 피어있는 Azure 가상 네트워크에 배포해야 합니다.  

1. 아직 수행하지 않은 경우 Azure [NetApp 파일에 대한 온보딩을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)요청합니다.  
2. NetApp 계정을 만드는 방법에 따라 선택한 Azure 지역에서 [NetApp 계정을 만듭니다.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Azure NetApp 파일 용량 풀을 설정하는 방법에 대한 지침에 따라 [Azure NetApp 파일 용량 풀을 설정합니다.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)  
이 문서에 제시된 SAP Netweaver 아키텍처는 단일 Azure NetApp 파일 용량 풀인 프리미엄 SKU를 사용합니다. Azure에서 SAP Netweaver 응용 프로그램 워크로드에 대해 Azure NetApp 파일 프리미엄 SKU를 권장합니다.  
4. 지침에 설명된 대로 서브넷을 Azure NetApp 파일에 [위임하여 서브넷을 Azure NetApp 파일에 위임합니다.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

5. Azure NetApp 파일에 대한 [볼륨을 만드는 지침에](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)따라 Azure NetApp 파일 볼륨을 배포합니다. 지정된 Azure NetApp 파일 [서브넷에](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)볼륨을 배포합니다. Azure NetApp 파일 리소스 및 Azure VM은 동일한 Azure 가상 네트워크 또는 피어있는 Azure 가상 네트워크에 있어야 합니다. 이 예제에서는 두 개의 Azure NetApp 파일 볼륨인 sap<b>QAS</b> 및 transSAP를 사용합니다. 해당 마운트 지점에 탑재되는 파일 경로는 /usrsap<b>qas</b>/sapmnt<b>QAS,</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>sys 등입니다.  

   1. 볼륨 수액<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS)</b>
   2. 볼륨 수액<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/ usrsap<b>QAS</b>ASCs)
   3. 볼륨 수액<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/ usrsap<b>QAS</b>sys)
   4. 볼륨 수액<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. 볼륨 트랜스SAP(nfs://192.168.24.4/transSAP)
   6. 볼륨 수액<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/ usrsap<b>QAS</b>pas)
   7. 볼륨 수액<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS aas)</b>
  
이 예제에서는 모든 SAP Netweaver 파일 시스템에 Azure NetApp 파일을 사용하여 Azure NetApp 파일을 사용하는 방법을 보여 주도록 했습니다. NFS를 통해 탑재할 필요가 없는 SAP 파일 시스템을 Azure [디스크 저장소로](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) 배포할 수도 있습니다. 이 예제에서 <b>a-e는</b> Azure NetApp 파일 및 <b>f-g(즉,</b> /usr/sap/QAS/D<b>02,</b>/usr/sap/QAS/D<b>03)에</b>있어야 Azure 디스크 저장소로 배포될 수 있습니다.<b>QAS</b><b>QAS</b> 

### <a name="important-considerations"></a>중요 고려 사항

SUSE 고가용성 아키텍처에서 SAP Netweaver용 Azure NetApp 파일을 고려할 때 다음과 같은 중요한 사항을 유의하십시오.

- 최소 수용 인원은 4TiB입니다. 용량 풀 크기는 1TiB 단위로 늘릴 수 있습니다.
- 최소 볼륨은 100 GiB입니다.
- Azure NetApp 파일 및 Azure NetApp 파일 볼륨이 탑재되는 모든 가상 시스템은 동일한 Azure 가상 네트워크 또는 동일한 리전의 [피어있는 가상 네트워크에](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 있어야 합니다. 동일한 리전에서 VNET 피어링을 통해 Azure NetApp 파일 액세스가 지원됩니다. 전역 피어링을 통해 Azure NetApp 액세스는 아직 지원되지 않습니다.
- 선택한 가상 네트워크에는 Azure NetApp 파일에 위임된 서브넷이 있어야 합니다.
- Azure NetApp 파일은 [내보내기 정책을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)제공합니다: 허용된 클라이언트, 액세스 유형(쓰기&읽기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp 파일 기능은 아직 영역을 인식하지 못합니다. 현재 Azure NetApp 파일 기능은 Azure 지역의 모든 가용성 영역에 배포되지 않습니다. 일부 Azure 리전에서 잠재적인 대기 시간 영향을 알고 있어야 합니다. 
- Azure NetApp 파일 볼륨은 NFSv3 또는 NFSv4.1 볼륨으로 배포할 수 있습니다. 두 프로토콜 모두 SAP 응용 프로그램 계층(ASCS/ERS, SAP 응용 프로그램 서버)에 대해 지원됩니다. 

## <a name="setting-up-ascs"></a>(A)SCS 설정

이 예제에서는 리소스가 [Azure 포털을](https://portal.azure.com/#home)통해 수동으로 배포되었습니다.

### <a name="deploy-linux-manually-via-azure-portal"></a>Azure Portal을 통해 Linux를 수동으로 배포

먼저 Azure NetApp 파일 볼륨을 만들어야 합니다. VM을 배포합니다. 그런 다음 로드 밸러커를 만들고 백 엔드 풀에서 가상 컴퓨터를 사용합니다.

1. 부하 분산기 만들기(내부, 표준):  
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS의 IP 주소 192.168.14.9
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **프런트 엔드) QAS. ASCS)**
         1. 할당을 정적으로 설정하고 IP 주소를 입력합니다(예: **192.168.14.9)**
         1. 확인 클릭
      1. ASCS ERS의 IP 주소 192.168.14.10
         * "a" 아래의 단계를 반복하여 ERS에 대한 IP 주소를 만듭니다(예: **192.168.14.10** 및 **프런트 엔드). QAS. ERS**)
   1. 백 엔드 풀 만들기
      1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
      1. 새 백 엔드 풀의 이름을 입력합니다(예: **백 엔드) QAS)**
      1. 가상 머신 추가 클릭
      1. 가상 머신을 선택합니다. 
      1. (A)SCS 클러스터의 가상 컴퓨터와 해당 IP 주소를 선택합니다.
      1. 추가를 클릭합니다.
   1. 상태 프로브 만들기
      1. 포트: 620**00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브의 이름을 입력합니다(예: **상태). QAS. ASCS)**
         1. 프로토콜로 TCP를 선택하고, 620**00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. ASCS ERS용 포트 621**01**
            * "c"에서 위의 단계를 반복하여 ERS에 대한 상태 프로브(예: 621**01** 및 **health)를 만듭니다. QAS. ERS**)
   1. 로드 밸런싱 규칙
      1. ASCS에 대한 부하 분산 규칙
         1. 로드 밸런서를 열고 로드 밸런싱 규칙을 선택한 다음 추가를 클릭합니다.
         1. 새 로드 밸러버 규칙의 이름을 입력합니다(예: **lb) QAS. ASCS)**
         1. 앞에서 만든 ASCS, 백 엔드 풀 및 상태 프로브의 프런트 엔드 IP 주소를 선택합니다(예: **프런트 엔드). QAS. ASCS,** **백 엔드. QAS** 및 **건강. QAS. ASCS)**
         1. **HA 포트** 선택
         1. 유휴 상태 시간 제한을 30분으로 증가
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
         * 위의 단계를 반복하여 ERS에 대한 부하 분산 규칙을 만듭니다(예: **lb) QAS. ERS**)
1. 또는 시나리오에 기본 로드 밸러터(내부)가 필요한 경우 다음 단계를 따르십시오.  
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS의 IP 주소 192.168.14.9
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **프런트 엔드) QAS. ASCS)**
         1. 할당을 정적으로 설정하고 IP 주소를 입력합니다(예: **192.168.14.9)**
         1. 확인 클릭
      1. ASCS ERS의 IP 주소 192.168.14.10
         * "a" 아래의 단계를 반복하여 ERS에 대한 IP 주소를 만듭니다(예: **192.168.14.10** 및 **프런트 엔드). QAS. ERS**)
   1. 백 엔드 풀 만들기
      1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
      1. 새 백 엔드 풀의 이름을 입력합니다(예: **백 엔드) QAS)**
      1. 가상 머신 추가 클릭
      1. ASCS에 대해 이전에 만든 가용성 세트를 선택합니다. 
      1. (A)SCS 클러스터의 가상 머신 선택
      1. 확인 클릭
   1. 상태 프로브 만들기
      1. 포트: 620**00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브의 이름을 입력합니다(예: **상태). QAS. ASCS)**
         1. 프로토콜로 TCP를 선택하고, 620**00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. ASCS ERS용 포트 621**01**
            * "c"에서 위의 단계를 반복하여 ERS에 대한 상태 프로브(예: 621**01** 및 **health)를 만듭니다. QAS. ERS**)
   1. 로드 밸런싱 규칙
      1. TCP: 32**00**(ASCS용)
         1. 로드 밸런서를 열고 로드 밸런싱 규칙을 선택한 다음 추가를 클릭합니다.
         1. 새 로드 밸러버 규칙의 이름을 입력합니다(예: **lb) QAS. ASCS.3200)**
         1. 앞에서 만든 ASCS, 백 엔드 풀 및 상태 프로브의 프런트 엔드 IP 주소를 선택합니다(예: **프런트 엔드). QAS. ASCS)**
         1. 프로토콜로 **TCP**를 유지하고. 포트로 **3200** 입력
         1. 유휴 상태 시간 제한을 30분으로 증가
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
      1. ASCS에 대한 추가 포트
         * ASCS의 포트 36**00,** 39**00,** 81**00,** 5**00**13, 5**00**16, 5**00**16 및 TCP에 대한 "d"에서 위의 단계를 반복합니다.
      1. ASCS ERS에 대한 추가 포트
         * ASCS ERS의 포트 32**01,** 33**01,** 5**01**13, 5**01**14, 5**01**16 및 TCP에 대한 "d"에서 위의 단계를 반복합니다.

      > [!Note]
      > 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 로드 밸런서의 백 엔드 풀에 배치되면 공용 끝점으로 라우팅을 허용하기 위한 추가 구성이 수행되지 않는 한 아웃바운드 인터넷 연결이 없습니다. 아웃바운드 연결을 달성하는 방법에 대한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 로드 밸런서를 사용하는 가상 시스템에 대한 공용 엔드포인트 연결을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)참조하십시오.  

      > [!IMPORTANT]
      > Azure 로드 밸러워 뒤에 배치된 Azure VM에서 TCP 타임스탬프를 사용하지 마십시오. TCP 타임스탬프를 사용하면 상태 프로브가 실패합니다. 매개 변수 **net.ipv4.tcp_timestamps** **0으로**설정합니다. 자세한 내용은 [로드 밸러저 상태 프로브를](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)참조하십시오.

## <a name="disable-id-mapping-if-using-nfsv41"></a>ID 매핑 사용 안 함(NFSv4.1을 사용하는 경우)

이 섹션의 지침은 NFSv4.1 프로토콜이 있는 Azure NetApp 파일 볼륨을 사용하는 경우에만 적용됩니다. Azure NetApp 파일 NFSv4.1 볼륨이 탑재되는 모든 VM에서 구성을 수행합니다.  

1. NFS 도메인 설정을 확인합니다. 도메인이 기본 Azure NetApp 파일 도메인으로 구성되었는지 확인합니다. **`defaultv4iddomain.com`** **nobody**  

    > [!IMPORTANT]
    > Azure NetApp 파일의 기본 `/etc/idmapd.conf` 도메인 구성과 일치하도록 VM에서 NFS **`defaultv4iddomain.com`** 도메인을 설정해야 합니다. NFS 클라이언트(예: VM)와 NFS 서버(예: Azure NetApp 구성)의 도메인 구성 간에 불일치가 있는 경우 VM에 탑재된 Azure NetApp 볼륨의 파일에 대한 사용 `nobody`권한이 로 표시됩니다.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`확인합니다. **Y로**설정해야 합니다. 디렉터리 구조를 `nfs4_disable_idmapping` 작성하려면 mount 명령을 실행합니다. 액세스는 커널 / 드라이버에 대해 예약되어 있기 때문에 /sys / 모듈 에서 디렉터리를 수동으로 만들 수 없습니다.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   매개 변수를 변경하는 `nfs4_disable_idmapping` 방법에 https://access.redhat.com/solutions/1749883대한 자세한 내용은 을 참조하십시오.

### <a name="create-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

[Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정](high-availability-guide-rhel-pacemaker.md) 단계에 따라 이 (A)SCS 서버용 기본 Pacemaker 클러스터를 만듭니다.

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver 설치 준비

다음 항목은 모든 노드에 적용 가능한 **[A]** 중 하나, **[1]** - 노드 1 또는 **[2]에만** 적용가능하며 노드 2에만 적용됩니다.

1. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기

    ```
    sudo vi /etc/hosts
    ```

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** Azure NetApp 파일 볼륨에서 SAP 디렉터리를 만듭니다.  
   VM 중 하나에 Azure NetApp 파일 볼륨을 일시적으로 마운트하고 SAP 디렉터리(파일 경로)를 만듭니다.  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** 공유 디렉터리 만들기

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** NFS 클라이언트 및 기타 요구 사항 설치

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** 리소스-에이전트-sap 버전 확인

   설치된 리소스-에이전트-sap 패키지 버전이 3.9.5-124.el7 이상인지 확인
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** 탑재 항목 추가

   NFSv3을 사용하는 경우:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   NFSv4.1을 사용하는 경우:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > 볼륨을 장착할 때 Azure NetApp 파일 볼륨의 NFS 프로토콜 버전과 일치해야 합니다. Azure NetApp 파일 볼륨이 NFSv3 볼륨으로 만들어지는 경우 해당 NFSv3 구성을 사용합니다. Azure NetApp 파일 볼륨이 NFSv4.1 볼륨으로 생성되는 경우 지침에 따라 ID 매핑을 사용하지 않도록 설정하고 해당 NFSv4.1 구성을 사용해야 합니다. 이 예제에서는 Azure NetApp 파일 볼륨이 NFSv3 볼륨으로 만들어졌습니다.  

   새 공유 탑재

   ```
   sudo mount -a  
   ```

1. **[A]** 스왑 파일 구성

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   에이전트를 다시 시작하여 변경 내용을 활성화합니다.

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL 구성

   SAP Note [2002167]의 설명에 따라 RHEL 구성

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS 설치

1. **[1]** ASCS 인스턴스에 대한 가상 IP 리소스 및 상태 프로브 만들기

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** SAP NetWeaver ASCS 설치  

   AsCS에 대한 로드 밸런서 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용하여 첫 번째 노드에 SAP NetWeaver ASCS를 루트로 설치합니다(예: <b>anftstsapvh</b>, <b>192.168.14.9</b> 및 로드 밸런서의 프로브에 사용한 인스턴스 번호,예를 들어 <b>00.</b>

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   설치가 /usr/sap/QAS/ASCS**00에서**하위 폴더를 만들지 못하면 ASCS**QAS****00** 폴더의 소유자 및 그룹을 설정하고 다시 시도하십시오.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** ERS 인스턴스에 대한 가상 IP 리소스 및 상태 검사 프로브 만들기

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** SAP NetWeaver ERS 설치  

   SAP NetWeaver ERS를 두 번째 노드에 루트로 설치하여 ERS에 대한 로드 밸런서 프런트 엔드 구성의 IP 주소에 매핑하는 가상 호스트 이름을 사용하여, 예를 들어 <b>anftstsapers,</b> <b>192.168.14.10</b> 및 로드 밸런서의 프로브에 사용한 인스턴스 번호(예: <b>01).</b>

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   설치가 /usr/sap/QAS/ERS**01에서**하위 폴더를 만들지 못하면 ERS**QAS****01** 폴더의 소유자 및 그룹을 설정하고 다시 시도하십시오.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** ASCS/SCS 및 ERS 인스턴스 프로필 조정

   * ASCS/SCS 프로필

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * ERS 프로필

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** 연결 유지 구성

   SAP NetWeaver 애플리케이션 서버와 ASCS/SCS 간의 통신은 소프트웨어 부하 분산 장치를 통해 라우팅됩니다. 부하 분산 장치는 구성 가능한 시간 제한이 지나면 비활성 연결을 끊습니다. 이를 방지하려면 SAP NetWeaver ASCS/SCS 프로필에서 매개 변수를 설정하고 Linux 시스템 설정을 변경해야 합니다. 자세한 내용은 [SAP Note 1410736][1410736]을 참조하세요.

   ASCS/SCS profile 매개 변수 enque/encni/set_so_keepalive는 마지막 단계에서 이미 추가된 상태입니다.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** /usr/sap/sapservices 파일 업데이트

   sapinit 시작 스크립트에 의해 인스턴스가 시작되지 않도록 하려면 /usr/sap/sapservices 파일에서 Pacemaker가 관리하는 모든 인스턴스를 주석 처리해야 합니다. HANA SR에 사용할 SAP HANA 인스턴스는 주석 처리하지 마세요.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** SAP 클러스터 리소스 만들기

   ENQUEUE 서버 1 아키텍처(ENSA1)를 사용하는 경우 리소스를 다음과 같이 정의합니다.

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP는 SAP NW 7.52를 비롯한 큐 서버 2에 대한 복제를 포함했습니다. ABAP 플랫폼 1809부터 는 enqueue 서버 2가 기본적으로 설치됩니다. Enqueue 서버 2 지원은 SAP 참고 [2630416을](https://launchpad.support.sap.com/#/notes/2630416) 참조하십시오.
   enqueue 서버 2 아키텍처[(ENSA2)를](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)사용하는 경우 리소스 에이전트 리소스 -sap-4.1.1-el7.x86_64 또는 최신 리소스를 설치하고 다음과 같이 리소스를 정의하십시오.

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   이전 버전에서 업그레이드하고 큐 서버 2로 전환하는 경우 SAP note [2641322를](https://launchpad.support.sap.com/#/notes/2641322)참조하십시오. 

   > [!NOTE]
   > 위의 구성의 시간 초과는 예에 불과하며 특정 SAP 설정에 맞게 조정해야 할 수 있습니다. 

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** 두 노드에 ASCS 및 ERS에 대한 방화벽 규칙을 추가두 노드에 ASCS 및 ERS에 대한 방화벽 규칙을 추가합니다.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a> SAP NetWeaver 애플리케이션 서버 준비

   일부 데이터베이스에서는 데이터베이스 인스턴스 설치가 애플리케이션 서버에서 실행되어야 합니다. 이러한 경우 애플리케이션 서버 가상 머신을 사용할 수 있도록 준비합니다.  

   아래 단계에서는 ASCS/SCS 및 HANA 서버와 다른 서버에 애플리케이션 서버를 설치한다고 가정합니다. 그 외의 경우에는 호스트 이름 확인을 구성하는 단계 등 아래의 일부 단계를 수행할 필요가 없습니다.  

   다음 항목은 PAS 및 AAS 모두에 적용되는 **[A] 및 [P]** - PAS 또는 **[S]에만** 적용가능하며 AAS에만 적용됩니다. **[A]**  

1. **[A]** 설치 호스트 이름 확인 DNS 서버를 사용하거나 모든 노드에서 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름을 바꿉니다.  

   ```
   sudo vi /etc/hosts
   ```

   다음 줄을 /etc/hosts에 삽입합니다. 사용자 환경에 맞게 IP 주소와 호스트 이름을 변경합니다.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** sapmnt 디렉토리 만들기 sapmnt 디렉토리 만들기
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** NFS 클라이언트 및 기타 요구 사항 설치  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** 탑재 항목 추가  
   NFSv3을 사용하는 경우:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   NFSv4.1을 사용하는 경우:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   새 공유 탑재

   ```
   sudo mount -a
   ```

1. **[P]** PAS 디렉토리 생성 및 마운트  
   NFSv3을 사용하는 경우:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   NFSv4.1을 사용하는 경우:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** AAS 디렉토리 생성 및 마운트  
   NFSv3을 사용하는 경우:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   NFSv4.1을 사용하는 경우:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** 스왑 파일 구성
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   에이전트를 다시 시작하여 변경 내용을 활성화합니다.

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>데이터베이스 설치

이 예제에서는 SAP NetWeaver가 SAP HANA에 설치됩니다. 이 설치에 지원되는 모든 데이터베이스를 사용할 수 있습니다. Azure에서 SAP HANA를 설치하는 방법에 대한 자세한 내용은 Red Hat 엔터프라이즈 Linux의. For a list of supported databases, see [SAP Note 1928533][1928533] [Azure VM에서 SAP HANA의 고가용성을][sap-hana-ha]참조하십시오.

1. SAP 데이터베이스 인스턴스 설치 실행

   데이터베이스에 대한 로드 밸론프런트 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용하여 SAP NetWeaver 데이터베이스 인스턴스를 루트로 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 애플리케이션 서버 설치

다음 단계에 따라 SAP 애플리케이션 서버를 설치합니다.

1. 애플리케이션 서버 준비

   위에 있는 [SAP NetWeaver 애플리케이션 서버 준비](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) 챕터의 단계에 따라 애플리케이션 서버를 준비합니다.

1. SAP NetWeaver 애플리케이션 서버 설치

   기본 또는 추가 SAP NetWeaver 애플리케이션 서버를 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. SAP HANA 보안 저장소 업데이트

   설치한 SAP HANA System Replication의 가상 이름을 가리키도록 SAP HANA 보안 저장소를 업데이트합니다.

   다음 명령을 실행하여 항목을 \<sapsid>adm으로 나열합니다.

   ```
   hdbuserstore List
   ```

   이 경우 모든 항목을 나열해야 하며 다음과 비슷합니다.
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   출력은 기본 항목의 IP 주소가 부하 분산 장치의 IP 주소가 아니라 가상 머신을 가리키고 있음을 나타냅니다. 이 항목은 부하 분산 장치의 가상 호스트 이름을 가리키도록 변경해야 합니다. 동일한 포트 (위의 출력에서**30313)와** 데이터베이스 이름 (위의 출력의**QAS)을** 사용하십시오!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

1. 수동으로 ASCS 인스턴스 마이그레이션

   테스트 시작 전 리소스 상태:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   루트 권한으로 다음 명령을 실행하여 ASCS 인스턴스를 마이그레이션합니다.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   테스트 후 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. 노드 작동 중단 시뮬레이트

   테스트 시작 전 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   ASCS 인스턴스를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행합니다.

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   노드가 다시 시작된 후의 상태는 다음과 같습니다.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   다음 명령을 실행하여 오류가 발생한 리소스를 정리합니다.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   테스트 후 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. 메시지 서버 프로세스 종료

   테스트 시작 전 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   루트 권한으로 다음 명령을 실행하여 메시지 서버 프로세스를 확인한 후 종료합니다.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   메시지 서버를 한 번만 죽이면 에 `sapstart`의해 다시 시작됩니다. 충분히 자주 종료하면 Pacemaker는 ASCS 인스턴스를 다른 노드로 이동합니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ASCS 및 ERS 인스턴스의 리소스 상태를 정리합니다.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   테스트 후 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. 큐에 넣기 서버 프로세스 종료

   테스트 시작 전 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   ASCS 인스턴스를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행하여 큐에 넣기 서버를 종료합니다.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   ASCS 인스턴스는 즉시 다른 노드로 장애 조치(Failover)되어야 합니다. 또한 ASCS 인스턴스가 시작된 후 ERS 인스턴스도 장애 조치(Failover)되어야 합니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ASCS 및 ERS 인스턴스의 리소스 상태를 정리합니다.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   테스트 후 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. 큐에 넣기 복제 서버 프로세스 종료

   테스트 시작 전 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   ERS 인스턴스를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행하여 큐에 넣기 복제 서버 프로세스를 종료합니다.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   명령을 한 번만 실행하면 `sapstart` 프로세스가 다시 시작됩니다. 충분히 자주 `sapstart` 실행하면 프로세스가 다시 시작되지 않고 리소스가 중지된 상태가 됩니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ERS 인스턴스의 리소스 상태를 정리합니다.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   테스트 후 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. 큐에 넣기 sapstartsrv 프로세스 종료

   테스트 시작 전 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   ASCS를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행합니다.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   sapstartsrv 프로세스는 모니터링의 일부분으로 항상 Pacemaker 리소스 에이전트에 의해 다시 시작되어야 합니다. 테스트 후 리소스 상태:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>다음 단계

* [SAP 응용 프로그램 다중 SID 가이드에 대한 RHEL의 Azure VM에서 SAP NW에 대한 HA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure에서 SAP HANA의 재해 복구를 위한 고가용성 및 계획을 수립하는 방법을 알아보려면 [Azure에서 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구를](hana-overview-high-availability-disaster-recovery.md)참조하십시오.
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.
