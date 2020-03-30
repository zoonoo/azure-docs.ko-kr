---
title: RHEL에서 Azure NetApp 파일이 있는 대기를 사용하는 SAP HANA 확장 | 마이크로 소프트 문서
description: SAP 응용 프로그램에 대 한 Azure NetApp 파일 레드 햇 엔터프라이즈 리눅스에 SAP NetWeaver에 대 한 고가용성 가이드
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/10/2020
ms.author: radeltch
ms.openlocfilehash: ace30b044682cb4e62a0f81da90f21a77be1edd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372979"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Red Hat 엔터프라이즈 Linux에서 Azure NetApp 파일을 사용하여 Azure VM에서 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2455582
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


이 문서에서는 공유 저장소 볼륨에 [Azure NetApp 파일을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) 사용하여 Azure Red Hat Enterprise Linux 가상 시스템(VM)에서 대기가 있는 확장 구성에서 가용성이 높은 SAP HANA 시스템을 배포하는 방법을 설명합니다.  

예제 구성, 설치 명령 등에서 HANA 인스턴스는 **03이고** HANA 시스템 ID는 **HN1입니다.** 이 예제는 HanA 2.0 SP4 및 SAP 7.6용 Red Hat 엔터프라이즈 리눅스를 기반으로 합니다. 

시작하기 전에 다음 SAP 노트와 논문을 참조하십시오.

* [Azure NetApp 파일 설명서][anf-azure-doc] 
* SAP 주 [1928533에는] 다음이 포함됩니다.  
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * 마이크로소프트 Azure에서 Windows 및 리눅스에 필요한 SAP 커널 버전
* SAP Note [2015553]: Azure에서 SAP 지원 SAP 소프트웨어 배포에 대한 필수 구성 조건을 나열합니다.
* SAP 참고 [2002167] 레드 햇 엔터프라이즈 리눅스에 대 한 OS 설정을 권장 했다
* SAP Note [2009879]에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
* SAP Note [2178632]: Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보가 포함되어 있습니다.
* SAP 주 [2191498]: Azure에서 Linux에 필요한 SAP 호스트 에이전트 버전이 포함되어 있습니다.
* SAP 참고 [2243692]: Azure에서 Linux에서 SAP 라이선스에 대한 정보가 포함되어 있습니다.
* SAP 주 [1999351]: SAP에 대한 Azure 고급 모니터링 확장에 대한 추가 문제 해결 정보가 포함되어 있습니다.
* SAP 주 [1900823]: SAP HANA 스토리지 요구 사항에 대한 정보가 포함되어 있습니다.
* [SAP 커뮤니티 위키](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): 리눅스에 필요한 모든 SAP 노트를 포함
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* 일반 RHEL 설명서
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)(고가용성 추가 기능 참조)
  * [레드 햇 엔터프라이즈 리눅스 네트워킹 가이드](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Azure 관련 RHEL 설명서:
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/solutions/3193782)(Microsoft Azure에서 사용할 용도로 Red Hat Enterprise Linux에 SAP HANA 설치)
* [Azure NetApp 파일을 사용하여 Microsoft Azure의 NetApp SAP 응용 프로그램][anf-sap-applications-azure]


## <a name="overview"></a>개요

HANA 고가용성을 달성하는 한 가지 방법은 호스트 자동 장애 조치(failover)를 구성하는 것입니다. 호스트 자동 장애 조치(failover)를 구성하려면 하나 이상의 가상 컴퓨터를 HANA 시스템에 추가하고 대기 노드로 구성합니다. 활성 노드가 실패하면 대기 노드가 자동으로 인계됩니다. Azure 가상 컴퓨터와 함께 제공 된 구성에서 [Azure NetApp 파일에서 NFS를](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)사용 하 여 자동 장애 조치(failover)를 달성합니다.  

> [!NOTE]
> 대기 노드는 모든 데이터베이스 볼륨에 액세스해야 합니다. HANA 볼륨은 NFSv4 볼륨으로 장착해야 합니다. NFSv4 프로토콜의 향상된 파일 리스 기반 잠금 메커니즘은 펜싱에 `I/O` 사용됩니다. 

> [!IMPORTANT]
> 지원되는 구성을 빌드하려면 HANA 데이터 및 로그 볼륨을 NFSv4.1 볼륨으로 배포하고 NFSv4.1 프로토콜을 사용하여 마운트해야 합니다. 대기 노드가 있는 HANA 호스트 자동 장애 조치 구성은 NFSv3에서 지원되지 않습니다.

![SAP NetWeaver 고가용성 개요](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

SAP HANA 네트워크 권장 사항을 따르는 이전 다이어그램에서는 하나의 Azure 가상 네트워크 내에 세 개의 서브넷이 표시됩니다. 
* 클라이언트 통신용
* 스토리지 시스템과의 통신을 위해
* 내부 HANA 노드 간 통신용

Azure NetApp 볼륨은 별도의 서브넷에 있으며 [Azure NetApp 파일에 위임됩니다.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

이 예제 구성의 경우 서브넷은 다음과 같습니다.  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26(Azure NetApp 파일에 위임된 서브넷)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp 파일 인프라 설정 

Azure NetApp 파일 인프라에 대한 설정을 진행하기 전에 [Azure NetApp 파일 설명서를][anf-azure-doc]숙지하십시오. 

Azure NetApp 파일은 여러 [Azure 지역에서](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)사용할 수 있습니다. 선택한 Azure 지역이 Azure NetApp 파일을 제공하는지 확인합니다.  

Azure 지역별 Azure NetApp 파일의 가용성에 대한 자세한 내용은 [Azure 지역별 Azure NetApp 파일 가용성을][anf-avail-matrix]참조하십시오.  

Azure NetApp 파일을 배포하기 전에 Azure NetApp 파일 [지침에 등록하여][anf-register]Azure NetApp 파일에 대한 온보딩을 요청합니다. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp 파일 리소스 배포  

다음 지침은 [Azure 가상 네트워크를](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)이미 배포한 것으로 가정합니다. Azure NetApp 파일 리소스가 탑재되는 Azure NetApp 파일 리소스 및 VM은 동일한 Azure 가상 네트워크 또는 피어있는 Azure 가상 네트워크에 배포해야 합니다.  

1. 아직 리소스를 배포하지 않은 경우 [Azure NetApp 파일에 대한 온보딩을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)요청합니다.  

2. NetApp 계정 만들기의 지침에 따라 선택한 Azure 지역에서 [NetApp 계정을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)만듭니다.  

3. Azure NetApp 파일 용량 풀 설정의 지침에 따라 [Azure NetApp 파일 용량 풀을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)설정합니다.  

   이 문서에 제시된 HANA 아키텍처는 *Ultra Service* 수준에서 단일 Azure NetApp 파일 용량 풀을 사용합니다. Azure에서 HANA 워크로드의 경우 Azure NetApp 파일 *울트라* 또는 *프리미엄* [서비스 수준을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)사용하는 것이 좋습니다.  

4. Azure NetApp 파일에 서브넷을 위임하는 지침에 설명된 대로 [Azure NetApp 파일에 서브넷을 위임합니다.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

5. Azure NetApp 파일에 [대한 NFS 볼륨 만들기의](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)지침에 따라 Azure NetApp 파일 볼륨을 배포합니다.  

   볼륨을 배포할 때 **NFSv4.1** 버전을 선택해야 합니다. 지정된 Azure NetApp 파일 [서브넷에](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)볼륨을 배포합니다. 
   
   Azure NetApp 파일 리소스 및 Azure VM은 동일한 Azure 가상 네트워크 또는 피어있는 Azure 가상 네트워크에 있어야 합니다. 예를 들어 **HN1**-data-mnt0001, **HN1**-log-mnt0001 등은 볼륨 이름과**nfs://10.9.0.4/ HN1**-data-mnt00001, nfs://10.9.0.4/**HN1**-log-mnt00001 등Azure NetApp 파일 볼륨의 파일 경로입니다.  

   * 볼륨 **HN1**-데이터-mnt00001(nfs://10.9.0.4/**HN1**-데이터-mnt00001)
   * 볼륨 **HN1**-데이터-mnt00002(nfs://10.9.0.4/**HN1**-데이터-mnt00002)
   * 볼륨 **HN1**-log-mnt00001 (nfs://10.9.0.4/**HN1**-log-mnt00001)
   * 볼륨 **HN1**-log-mnt00002 (nfs://10.9.0.4/**HN1**-log-mnt00002)
   * 볼륨 **HN1**공유 (nfs://10.9.0.4/**HN1**공유)
   
   이 예제에서는 각 HANA 데이터 및 로그 볼륨에 대해 별도의 Azure NetApp 파일 볼륨을 사용했습니다. 더 작거나 비생산적인 시스템에서 비용 최적화된 구성을 위해 모든 데이터 마운트를 단일 볼륨에 배치하고 모든 로그가 다른 단일 볼륨에 탑재될 수 있습니다.  

### <a name="important-considerations"></a>중요 고려 사항

노드 별 시나리오를 통해 SAP HANA 확장에 대한 Azure NetApp 파일을 만들 때 다음과 같은 중요한 고려 사항에 유의하십시오.

- 최소 용량 풀은 4테비바이트(TiB)입니다.  
- 최소 볼륨 크기는 100gibiBYtes(GiB)입니다.
- Azure NetApp 파일 및 Azure NetApp 파일 볼륨이 탑재되는 모든 가상 시스템은 동일한 Azure 가상 네트워크 또는 동일한 리전의 [피어있는 가상 네트워크에](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 있어야 합니다.  
- 선택한 가상 네트워크에Azure NetApp 파일에 위임된 서브넷이 있어야 합니다.
- Azure NetApp 파일 볼륨의 처리량은 [Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)파일의 서비스 수준에 설명된 대로 볼륨 할당량 및 서비스 수준의 함수입니다. HANA Azure NetApp 볼륨의 크기를 조정하는 경우 결과 처리량이 HANA 시스템 요구 사항을 충족하는지 확인합니다.  
- Azure NetApp 파일 [내보내기 정책을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)사용하면 허용된 클라이언트, 액세스 유형(읽기-쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp 파일 기능은 아직 영역을 인식하지 못합니다. 현재 이 기능은 Azure 지역의 모든 가용성 영역에 배포되지 않습니다. 일부 Azure 리전에서 잠재적인 대기 시간 영향을 알고 있어야 합니다.  

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 낮은 대기 시간이 중요합니다. Microsoft 담당자와 협력하여 가상 컴퓨터와 Azure NetApp 파일 볼륨이 근접하게 배포되도록 합니다.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp 파일에서 HANA 데이터베이스 크기 조정

Azure NetApp 파일 볼륨의 처리량은 [Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)파일의 서비스 수준에 설명된 대로 볼륨 크기 및 서비스 수준의 함수입니다. 

Azure에서 SAP에 대한 인프라를 디자인할 때 최소 처리량 특성으로 변환되는 SAP의 몇 가지 최소 저장소 요구 사항을 알고 있어야 합니다.

- 1MB I/O 크기의 초당 250메가바이트(MB/s)의 /hana/log에서 읽기-쓰기.  
- 16MB 및 64MB I/O 크기의 /hana/data에 대해 최소 400MB/s의 활동을 읽습니다.  
- 16MB 및 64MB I/O 크기의 /hana/data에 대해 최소 250MB/s의 쓰기 활동을 작성합니다. 

볼륨 할당량의 1TiB당 [Azure NetApp 파일 처리량 제한은](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 다음과 같습니다.
- 프리미엄 스토리지 계층 - 64 MiB/s  
- 울트라 스토리지 계층 - 128 MiB/s  

데이터 및 로그에 대한 SAP 최소 처리량 요구 사항과 /hana/shared에 대한 지침을 충족하려면 권장되는 크기는 다음과 같은 것입니다.

| 볼륨 | 크기<br>프리미엄 스토리지 계층 | 크기<br>울트라 스토리지 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- | --- |
| /하나/로그/ | 4TiB | 2TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 작업자 노드 4개당 1xRAM | 작업자 노드 4개당 1xRAM | v3 또는 v4.1 |

이 문서에 제시된 레이아웃에 대한 SAP HANA 구성은 Azure NetApp 파일 울트라 저장소 계층을 사용하여 다음과 같은 것입니다.

| 볼륨 | 크기<br>울트라 스토리지 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- |
| /hana/log/mnt00001 | 2TiB | v4.1 |
| /hana/log/mnt00002 | 2TiB | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| /hana/shared | 2TiB | v3 또는 v4.1 |

> [!NOTE]
> 여기에 명시된 Azure NetApp 파일 크기 조정 권장 사항은 SAP가 인프라 공급자에게 권장하는 최소 요구 사항을 충족하도록 지정됩니다. 실제 고객 배포 및 워크로드 시나리오에서는 이러한 크기로는 충분하지 않을 수 있습니다. 이러한 권장 사항을 시작점으로 사용하고 특정 워크로드의 요구 사항에 따라 조정합니다.  

> [!TIP]
> 볼륨을 *해제하거나* 가상 컴퓨터를 중지하거나 SAP HANA를 중지하지 않고도 Azure NetApp 파일 볼륨의 크기를 동적으로 조정할 수 있습니다. 이 방법을 사용하면 응용 프로그램의 예상 및 예기치 않은 처리량 요구 사항을 유연하게 충족할 수 있습니다.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Azure 포털을 통해 Linux 가상 시스템 배포

먼저 Azure NetApp 파일 볼륨을 만들어야 합니다. 그런 다음 다음 단계를 수행합니다.
1. [Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) [가상 네트워크에서 Azure 가상 네트워크 서브넷을](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) 만듭니다. 
1. VM을 배포합니다. 
1. 추가 네트워크 인터페이스를 만들고 네트워크 인터페이스를 해당 VM에 연결합니다.  

   각 가상 시스템에는 세 개의 Azure 가상 네트워크`client` `storage` 서브넷(및)에 `hana`해당하는 세 개의 네트워크 인터페이스가 있습니다. 

   자세한 내용은 [여러 네트워크 인터페이스 카드가 있는 Azure의 Linux 가상 시스템 만들기를](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)참조하십시오.  

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 낮은 대기 시간이 중요합니다. 대기 시간이 적어지려면 Microsoft 담당자와 협력하여 가상 컴퓨터와 Azure NetApp 파일 볼륨이 근접하게 배포되도록 합니다. SAP HANA Azure NetApp 파일을 사용하는 [새 SAP HANA 시스템을 온보딩할](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) 때 필요한 정보를 제출합니다. 
 
다음 지침은 리소스 그룹, Azure 가상 네트워크 및 세 개의 Azure 가상 네트워크 서브넷을 `storage` `hana`이미 만들었다고 가정합니다. `client` VM을 배포할 때 클라이언트 네트워크 인터페이스가 VM의 기본 인터페이스가 되도록 클라이언트 서브넷을 선택합니다. 또한 저장소 서브넷 게이트웨이를 통해 Azure NetApp 파일 위임된 서브넷에 대한 명시적 경로를 구성해야 합니다. 

> [!IMPORTANT]
> 선택한 OS가 사용 중이던 특정 VM 유형에 대해 SAP HANA에 대한 SAP 인증을 받았습니다. 이러한 유형에 대한 SAP HANA 인증 VM 유형 및 OS 릴리스 목록은 [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 사이트로 이동하십시오. 나열된 VM 유형의 세부 정보를 클릭하여 해당 유형에 대한 SAP HANA 지원 OS 릴리스의 전체 목록을 가져옵니다.  

1. SAP HANA에 대한 가용성 집합을 만듭니다. 최대 업데이트 도메인을 설정해야 합니다.  

2. 다음 단계를 수행하여 세 가지 가상**컴퓨터(hanadb1,** **hanadb2,** **hanadb3)를**만듭니다.  

   a. SAP HANA에서 지원되는 Azure 갤러리에서 Red Hat 엔터프라이즈 Linux 이미지를 사용합니다. 이 예제에서는 RHEL-SAP-HA 7.6 이미지를 사용했습니다.  

   b. SAP HANA에 대해 이전에 만든 가용성 집합을 선택합니다.  

   다. 클라이언트 Azure 가상 네트워크 서브넷을 선택합니다. [가속 네트워크를](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)선택합니다.  

   가상 컴퓨터를 배포하면 네트워크 인터페이스 이름이 자동으로 생성됩니다. 단순성 에 대 한이 지침에서 우리는 자동으로 생성 된 네트워크 인터페이스를 참조 합니다., 클라이언트 Azure 가상 네트워크 서브넷에 연결 된, **hanadb1-클라이언트,** **hanadb2-클라이언트**및 **hanadb3-클라이언트**. 

3. `storage` 가상 네트워크 서브넷(이 예에서는 **hanadb1-storage,** **hanadb2-storage**및 **hanadb3-storage)에**대해 가상 시스템마다 하나씩 세 개의 네트워크 인터페이스를 만듭니다.  

4. `hana` 가상 네트워크 서브넷에 대해 가상 머신마다 하나씩 세 개의 네트워크 인터페이스를 만듭니다(이 예에서는 **hanadb1-hana,** **hanadb2-hana**및 **hanadb3-hana).**  

5. 다음 단계를 수행하여 새로 만든 가상 네트워크 인터페이스를 해당 가상 시스템에 연결합니다.  

    a. [Azure 포털의](https://portal.azure.com/#home)가상 컴퓨터로 이동합니다.  

    b. 왼쪽 창에서 가상 **컴퓨터를**선택합니다. 가상 컴퓨터 이름(예: **hanadb1)을**필터링한 다음 가상 컴퓨터를 선택합니다.  

    다. **개요** 창에서 **가상** 컴퓨터 할당 해제를 선택합니다.  

    d. **네트워킹을**선택한 다음 네트워크 인터페이스를 연결합니다. 네트워크 **인터페이스** 드롭다운 첨부 목록에서 `storage` 및 `hana` 서브넷에 대해 이미 생성된 네트워크 인터페이스를 선택합니다.  
    
    e. **저장**을 선택합니다. 
 
    f. 나머지 가상 컴퓨터(이 예에서는 **hanadb2** 및 **hanadb3)에**대해 e를 통해 단계 b를 반복합니다.
 
    g. 지금은 가상 컴퓨터를 중지된 상태로 둡니다. 다음으로 새로 연결된 모든 네트워크 인터페이스에 대해 [빠른 네트워킹을](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 사용하도록 설정합니다.  

6. 다음 단계를 수행하여 `storage` 서브넷 및 `hana` 서브넷에 대한 추가 네트워크 인터페이스에 대해 빠른 네트워킹을 활성화합니다.  

    a. [Azure 포털에서](https://portal.azure.com/#home) [Azure 클라우드 셸을](https://azure.microsoft.com/features/cloud-shell/) 엽니다.  

    b. `storage` 다음 명령을 실행하여 서브넷및서브넷에 `hana` 연결된 추가 네트워크 인터페이스에 대해 빠른 네트워킹을 활성화합니다.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. 다음 단계를 수행하여 가상 컴퓨터를 시작합니다.  

    a. 왼쪽 창에서 가상 **컴퓨터를**선택합니다. 가상 시스템 이름(예: **hanadb1)을**필터링한 다음 선택합니다.  

    b. **개요** 창에서 **시작을**선택합니다.  

## <a name="operating-system-configuration-and-preparation"></a>운영 체제 구성 및 준비

다음 섹션의 지침은 다음 중 하나로 접두사에 붙어 있습니다.
* **[A]**: 모든 노드에 적용 가능
* **[1]**: 노드 1에만 적용 가능
* **[2]**: 노드 2에만 적용 가능
* **[3]**: 노드 3에만 적용 가능

다음 단계를 수행하여 OS를 구성하고 준비합니다.

1. **[A]** 가상 컴퓨터에서 호스트 파일을 유지 관리합니다. 모든 서브넷에 대한 항목을 포함합니다. 이 예제에서는 다음 `/etc/hosts` 항목이 추가되었습니다.  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Azure NetApp 파일에 대한 통신이 저장소 네트워크 인터페이스를 통해 전달되도록 네트워크 경로를 추가합니다.  

   이 예제에서는 `Networkmanager` 추가 네트워크 경로를 구성하는 데 사용됩니다. 다음 지침은 저장소 네트워크 인터페이스가 `eth1`있는 것으로 가정합니다.  
   먼저 장치의 연결 이름을 `eth1`확인합니다. 이 예제에서는 장치의 `eth1` 연결 `Wired connection 1`이름이 입니다.  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   그런 다음 을 통해 `eth1`Azure NetApp 파일 위임 네트워크에 대한 추가 경로를 구성합니다.  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    VM을 재부팅하여 변경 내용을 활성화합니다.  

3. **[A]** NFS 클라이언트 패키지를 설치합니다.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** NFS 구성 가이드가 있는 [NetApp AFF 시스템의 SAP HANA에](https://www.netapp.com/us/media/tr-4435.pdf)설명된 대로 NFS를 통해 Azure NetApp에서 SAP HANA를 실행하기 위한 OS를 준비합니다. NetApp 구성 설정에 대한 구성 파일 */etc/sysctl.d/netapp-hana.conf를* 만듭니다.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 net.ipv4.tcp_slow_start_after_idle=0 net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** 추가 최적화 설정으로 구성 *파일/등/sysctl.d/ms-az.conf를* 만듭니다.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** [NFS 구성 가이드와 넷앱 AFF 시스템의 SAP HANA에서](https://www.netapp.com/us/media/tr-4435.pdf)권장하는 대로 sunrpc 설정을 조정합니다.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** HANA 구성용 레드 햇.

    SAP Note [2292690,] [2455582,] [2593824] 및 <https://access.redhat.com/solutions/2447641>에 설명된 대로 RHEL 구성

    > [!NOTE]
    > HANA 2.0 SP04를 설치하는 경우 SAP `compat-sap-c++-7` HANA를 설치하기 전에 SAP note [2593824에]설명된 대로 패키지를 설치해야 합니다. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Azure NetApp 파일 볼륨 마운트

1. **[A]** HANA 데이터베이스 볼륨에 대해 마운트 지점을 만듭니다.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** **HN1**-shared에서 /usr/sap에 대한 노드별 디렉토리를 만듭니다.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** NFS 도메인 설정을 확인합니다. 도메인이 기본 Azure NetApp 파일 도메인으로 구성되었는지 확인합니다. **`defaultv4iddomain.com`** **nobody**  

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
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   매개 변수를 변경하는 `nfs4_disable_idmapping` 방법에 https://access.redhat.com/solutions/1749883대한 자세한 내용은 을 참조하십시오.

6. **[A]** 공유 Azure NetApp 파일 볼륨을 마운트합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** **hanadb1에**노드별 볼륨을 마운트합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** **hanadb2에**노드별 볼륨을 마운트합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** **hanadb3에**노드별 볼륨을 마운트합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 모든 HANA 볼륨이 NFS 프로토콜 버전 **NFSv4와**함께 탑재되어 있는지 확인합니다.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>설치  

이 예제에서는 Azure를 사용하여 대기 노드를 사용하여 확장 구성에 SAP HANA를 배포하는 경우 HANA 2.0 SP4를 사용했습니다.  

### <a name="prepare-for-hana-installation"></a>HANA 설치 준비

1. **[A]** HANA 설치 전에 루트 암호를 설정합니다. 설치가 완료된 후 루트 암호를 사용하지 않도록 설정할 수 있습니다. 명령으로 `root` `passwd`실행합니다.  

2. **[1]** 암호를 입력하지 않고 SSH를 통해 **hanadb2** 및 **hanadb3에**로그인할 수 있는지 확인합니다.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** HANA 2.0 SP4에 필요한 추가 패키지를 설치합니다. 자세한 내용은 SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824)를 참조하십시오. 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** SAP HANA `data` 및 `log` 디렉토리의 소유권을 **hn1**adm으로 변경합니다.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** HANA 설치를 방해하지 않도록 방화벽을 일시적으로 사용하지 않도록 설정합니다. HANA 설치가 완료된 후 다시 활성화할 수 있습니다. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>하나 설치

1. **[1]** SAP HANA 2.0 설치 및 업데이트 가이드의 지침에 따라 [SAP HANA를 설치합니다.](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html) 이 예제에서는 마스터, 작업자 1개 및 대기 노드 1개를 사용하는 SAP HANA 스케일 아웃을 설치합니다.  

   a. HANA 설치 소프트웨어 디렉토리에서 **hdblcm** 프로그램을 시작합니다. 매개 `internal_network` 변수를 사용하고 내부 HANA 노드 간 통신에 사용되는 서브넷의 주소 공간을 전달합니다.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. 프롬프트에서 다음 값을 입력합니다.

     * **작업 선택**: **1을** 입력합니다(설치용).
     * **설치를 위한 추가 구성 요소:** **2, 3을** 입력합니다.
     * 설치 경로: Enter(기본값 /hana/공유)를 누릅니다.
     * **로컬 호스트 이름의**경우 : Enter를 눌러 기본값을 수락합니다.
     * **시스템에서 호스트를 추가하시겠습니까?** **y**
     * **쉼표로 구분된 호스트 이름을 추가하려면** **hanadb2, hanadb3을** 입력합니다.
     * **루트 사용자 이름** [루트]: Enter를 눌러 기본값을 수락합니다.
     * 호스트 hanadb2의 역할: **1** 1(작업자용)을 입력합니다.
     * 호스트 hanadb2 [기본값]에 대 한 **호스트 장애 조치 그룹의** 경우: Enter 를 눌러 기본값을 수락합니다.
     * 호스트 hanadb2 [<<assign automatically>>]에 대 한 저장소 **파티션 번호에** 대 한: 기본을 수락 하려면 Enter 를 누릅니다.
     * 호스트 hanadb2 [기본값]에 대 한 **작업자 그룹에** 대 한: 기본값을 수락 하려면 Enter 를 누릅니다.
     * 호스트 hanadb3에 대한 **역할 선택:** **2를** 입력합니다(대기의 경우).
     * 호스트 hanadb3 [기본값]에 대 한 **호스트 장애 조치 그룹의** 경우: Enter 를 눌러 기본값을 수락합니다.
     * 호스트 hanadb3 [기본값]에 대 한 **작업자 그룹에** 대 한: 기본값을 수락 하려면 Enter 를 누릅니다.
     * **SAP HANA 시스템 ID**: **HN1** 입력
     * **예를 들어 숫자** [00]: **03을** 입력합니다.
     * **로컬 호스트 작업자 그룹** [기본값]: Enter를 눌러 기본값을 수락합니다.
     * **시스템 사용 선택 / 인덱스 입력 [4]**: **4를** 입력하십시오 (사용자 지정)
     * **데이터 볼륨의 위치** [/하나/데이터/HN1]: Enter를 눌러 기본값을 수락합니다.
     * **로그 볼륨의 위치** [/hana/log/HN1]: Enter를 눌러 기본값을 수락합니다.
     * **최대 메모리 할당을 제한하기 위해?** [n]: **enter n**
     * **호스트 hanadb1** [hanadb1]에 대 한 인증서 호스트 이름에 대 한: 기본을 수락 하려면 Enter 를 누릅니다.
     * **호스트 hanadb2** [hanadb2]에 대 한 인증서 호스트 이름에 대 한: 기본을 수락 하려면 Enter 를 누릅니다.
     * **호스트 hanadb3** [hanadb3]에 대 한 인증서 호스트 이름에 대 한: 기본수락 을 눌러 Enter
     * **시스템 관리자(hn1adm) 암호의**경우 : 암호를 입력합니다.
     * **시스템 데이터베이스 사용자(시스템) 암호의**경우 : 시스템의 암호를 입력합니다.
     * **시스템 데이터베이스 사용자(시스템) 암호 확인**: 시스템 암호를 입력합니다.
     * **컴퓨터를 재부팅 한 후 시스템을 다시 시작하려면?** [n]: **enter n** 
     * 계속할 **지 않으려면 (y/n)**: 요약의 유효성을 검사하고 모든 것이 잘 보이면 **y를** 입력하십시오.


2. **[1]** 글로벌 확인.ini  

   global.ini를 표시하고 내부 SAP HANA 노드 간 통신에 대한 구성이 제자리에 있는지 확인합니다. **통신** 섹션을 확인합니다. `hana` 서브넷의 주소 공간이 있어야 하며 `listeninterface` `.internal`로 설정해야 합니다. **internal_hostname_resolution** 섹션을 확인합니다. 서브넷에 속하는 HANA 가상 시스템의 IP 주소가 `hana` 있어야 합니다.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** 클라이언트 IP 주소가 클라이언트 통신에 사용되는지 확인하기 위해 호스트 매핑을 추가합니다. 섹션을 `public_host_resolution`추가하고 클라이언트 서브넷에서 해당 IP 주소를 추가합니다.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** SAP HANA를 다시 시작하여 변경 사항을 활성화합니다.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 클라이언트 인터페이스가 통신을 위해 `client` 서브넷의 IP 주소를 사용하는지 확인합니다.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   구성을 확인하는 방법에 대한 자세한 내용은 SAP 주 [2183363 - SAP HANA 내부 네트워크 구성을](https://launchpad.support.sap.com/#/notes/2183363)참조하십시오.  

5. **[A]** 방화벽을 다시 활성화합니다.  
   - 하나 중지
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - 방화벽 다시 활성화
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - 필요한 방화벽 포트 열기

       > [!IMPORTANT]
       > HANA 노드 간 통신 및 클라이언트 트래픽을 허용하는 방화벽 규칙을 만듭니다. 필수 포트 목록은 [모든 SAP 제품의 TCP/IP 포트](https://help.sap.com/viewer/ports)에 나열되어 있습니다. 다음 명령은 예에 불과합니다. 이 시나리오에서는 시스템 번호 03을 사용 합니다.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - 하나 시작
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. 기본 Azure NetApp 파일 저장소에 대한 SAP HANA를 최적화하려면 다음 SAP HANA 매개 변수를 설정합니다.

   - `max_parallel_io_requests`**128세**
   - `async_read_submit` **On**
   - `async_write_submit_active` **On**
   - `async_write_submit_blocks`**모두**

   자세한 내용은 [NFS 구성 가이드가 있는 NetApp AFF 시스템의 SAP HANA를](https://www.netapp.com/us/media/tr-4435.pdf)참조하십시오. 

   SAP HANA 2.0 시스템으로 시작하여 에서 `global.ini`매개 변수를 설정할 수 있습니다. 자세한 내용은 SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930)을 참조하십시오.  
   
   SAP HANA 1.0 시스템 버전 SPS12 및 이전 버전의 경우 SAP Note [2267798에](https://launchpad.support.sap.com/#/notes/2267798)설명된 대로 설치 중에 이러한 매개 변수를 설정할 수 있습니다.  

7. Azure NetApp 파일에서 사용하는 저장소에는 16테라바이트(TB)의 파일 크기 제한이 있습니다. SAP HANA는 저장소 제한을 암시적으로 인식하지 못하며 파일 크기 제한 16TB에 도달하면 새 데이터 파일을 자동으로 생성하지 않습니다. SAP HANA가 파일을 16TB 이상으로 늘리려고 하면 오류가 발생하며 결국 인덱스 서버 충돌이 발생합니다. 

   > [!IMPORTANT]
   > SAP HANA가 저장소 하위 시스템의 [16TB 제한을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) 초과하여 데이터 파일을 늘리려고 하지 `global.ini`않도록 하려면 에서 다음 매개 변수를 설정합니다.  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 자세한 내용은 SAP Note [2400005를](https://launchpad.support.sap.com/#/notes/2400005)참조하십시오.
   > SAP 참고 [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>SAP HANA 장애 조치 테스트 

1. SAP HANA 작업자 노드에서 노드 충돌을 시뮬레이션합니다. 다음을 수행합니다. 

   a. 노드 충돌을 시뮬레이션하기 전에 다음 명령을 **hn1**adm으로 실행하여 환경 상태를 캡처합니다.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. 노드 충돌을 시뮬레이션하려면 다음 명령을 작업자 노드에서 루트로 실행합니다.이 경우 **hanadb2입니다.**  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   다. 장애 조치 완료를 위해 시스템을 모니터링합니다. 장애 조치(failover)가 완료되면 다음과 같은 상태를 캡처합니다.  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > 노드가 커널 패닉을 경험하면 *모든* HANA 가상 `kernel.panic` 컴퓨터에서 20초로 설정하여 SAP HANA 장애 조치(failover)로 지연을 방지합니다. 구성은 에서 `/etc/sysctl`수행됩니다. 가상 컴퓨터를 재부팅하여 변경을 활성화합니다. 이 변경이 수행되지 않으면 노드가 커널 패닉을 경험하는 경우 장애 조치(failover)가 10분 이상 걸릴 수 있습니다.  

2. 다음을 수행하여 이름 서버를 죽입니다.

   a. 테스트에 앞서 다음 명령을 **hn1**adm으로 실행하여 환경의 상태를 확인합니다.  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. 이 경우 **hanadb1인** 활성 마스터 노드에서 **hn1**adm으로 다음 명령을 실행합니다.  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    대기 노드 **hanadb3마스터** 노드로 인계됩니다. 장애 조치 테스트가 완료된 후의 리소스 상태는 다음과 같습니다.  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   다. **hanadb1에서** HANA 인스턴스를 다시 시작합니다(즉, 이름 서버가 사망한 동일한 가상 컴퓨터에서). **hanadb1** 노드는 환경에 다시 참여하고 대기 역할을 유지합니다.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   SAP HANA가 **hanadb1에서**시작한 후 다음 상태를 예상하십시오.  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. 다시 말하지만 현재 활성 마스터 노드(즉, 노드 **hanadb3)에서**이름 서버를 죽입니다.  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   노드 **hanadb1** 마스터 노드의 역할을 다시 시작합니다. 장애 조치 테스트가 완료되면 상태는 다음과 같습니다.

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. 대기 노드로 사용할 준비가 된 **hanadb3에서**SAP HANA를 시작합니다.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   SAP HANA가 **hanadb3에서**시작한 후 상태는 다음과 같습니다.  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure에서 SAP HANA의 재해 복구를 위한 고가용성 및 계획을 수립하는 방법을 알아보려면 [Azure에서 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구를](hana-overview-high-availability-disaster-recovery.md)참조하십시오.
* Azure VM에서 SAP HANA의 고가용성 및 재해 복구 계획을 수립하는 방법에 대해 알아보려면 [Azure 가상 시스템(VM)에서 SAP HANA의 고가용성을][sap-hana-ha]참조하십시오.
