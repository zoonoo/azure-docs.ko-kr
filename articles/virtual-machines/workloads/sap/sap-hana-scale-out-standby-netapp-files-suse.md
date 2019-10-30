---
title: SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용 하 여 Azure Vm에서 대기 노드로 확장 SAP HANA Microsoft Docs
description: Sap 응용 프로그램용 Azure NetApp Files를 사용 하는 SUSE Linux Enterprise Server의 SAP NetWeaver에 대 한 고가용성 가이드
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
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 76369c1a4beb792de03cf0ccae5c86825812f103
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934172"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server Azure NetApp Files를 사용 하 여 Azure Vm에서 대기 노드로 확장 SAP HANA 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


이 문서에서는 공유 저장소 볼륨에 대 한 [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) 를 사용 하 여 Azure virtual machines의 대기 상태를 확장 구성에서 항상 사용 가능한 HANA 시스템을 배포 하는 방법을 설명 합니다.  
예제 구성, 설치 명령 등에서 HANA 인스턴스는 **03** 이 고 HANA 시스템 ID는 **h n 1**입니다. 예제는 HANA 2.0 SP4 및 SAP 12 s p 4 용 SUSE Linux Enterprise Server를 기반으로 합니다. 

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* [Azure NetApp Files 설명서][anf-azure-doc] 
* SAP Note [1928533], 다음 항목을 포함합니다.  
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전
* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2205917]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 나와 있습니다.
* Sap Note [1944799] 에는 Sap 응용 프로그램의 SUSE Linux Enterprise Server에 대 한 sap 지침이 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* SAP Note [1900823] 에 SAP HANA 저장소 요구 사항에 대 한 정보가 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP 용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP 용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP 용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE SAP HA 모범 사례 가이드][suse-ha-guide] 이 가이드에는 온-프레미스에서 Netweaver HA 및 SAP HANA 시스템 복제를 설정 하는 데 필요한 모든 정보가 포함 되어 있습니다. 이 가이드를 일반 기준으로 사용하세요. 여기서 훨씬 더 자세한 정보를 제공합니다.
* [SUSE 고가용성 확장 12 SP3 릴리스 정보][suse-ha-12sp3-relnotes]
* [Azure NetApp Files를 사용 하 여 Microsoft Azure에서 NetApp SAP 응용 프로그램][anf-sap-applications-azure]
* [NFS를 사용 하 여 NetApp 시스템에서 SAP HANA](https://www.netapp.com/us/media/tr-4435.pdf)합니다. 구성 가이드에는 Azure NetApp Files에서 제공 하는 NFS를 사용 하 여 SAP HANA를 설정 하는 방법에 대 한 정보가 포함 되어 있습니다.


## <a name="overview"></a>개요

HANA 고가용성을 구현 하는 방법 중 하나는 호스트 자동 장애 조치 (failover)입니다. 호스트 자동 장애 조치 (failover)를 구성 하기 위해 하나 이상의 가상 머신이 HANA 시스템에 추가 되 고 대기 노드로 구성 됩니다. 활성 노드가 실패 하면 대기 노드가 자동으로 수행 됩니다. Azure virtual machines를 사용 하 여 제공 된 구성에서 [Azure NetApp Files의 NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)에 의해 수행 됩니다.  

대기 노드는 모든 데이터베이스 볼륨에 액세스할 수 있어야 합니다. HANA 볼륨은 NFSv4 볼륨으로 탑재 되어야 합니다. NFSv4 프로토콜의 향상 된 파일 임대 기반 잠금 메커니즘은 `I/O` 펜스에 사용 됩니다. 

> [!IMPORTANT]
> HANA 데이터 및 로그 볼륨을 NFSv 4.1 볼륨으로 배포 하 고, 지원 되는 구성을 위해 NFSv 4.1 프로토콜을 사용 하 여 탑재 해야 합니다. NFSv3에서는 자동 장애 조치 (failover) 노드가 포함 된 HANA 호스트 자동 장애 조치 (failover) 구성이 지원 되지 않습니다.

![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

SAP HANA 네트워크 권장 사항에 따라 하나의 Azure 가상 네트워크 내에서 세 개의 서브넷을 만들었습니다. 저장소 시스템과의 통신은 내부 HANA 노드 간 통신과 클라이언트 통신에 사용 됩니다. Azure NetApp 볼륨은 별도의 서브넷에 있으며 [Azure NetApp Files에 위임](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)됩니다.  

이 구성 예제에서 서브넷은 다음과 같습니다.  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files 인프라 설정 

Azure NetApp 파일 인프라의 설치를 계속 하기 전에 [Azure NetApp Files 설명서][anf-azure-doc]를 숙지 하세요. Azure NetApp 파일은 여러 [azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)에서 사용할 수 있습니다. 선택한 Azure 지역에서 Azure NetApp Files를 제공 하는지 확인 합니다.  

다음 링크는 azure 지역에서 Azure NetApp Files의 가용성을 보여 줍니다. [Azure 지역별 Azure NetApp Files 가용성][anf-avail-matrix].  
Azure NetApp Files를 배포 하기 전에 [Azure NetApp 파일 등록 지침][anf-register]에 따라 Azure NetApp Files에 등록을 요청 합니다. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files 리소스 배포  

다음 단계에서는 이미 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)를 배포 했다고 가정 합니다. Azure NetApp Files 리소스가 탑재 되는 Azure NetApp Files 리소스 및 Vm은 동일한 Azure Virtual Network 또는 피어 링 Azure Virtual Networks에 배포 되어야 합니다.  

1. 아직 수행 하지 않은 경우 [Azure NetApp Files에 대 한 온 보 딩](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)을 요청 합니다.  

2. [Netapp 계정 만들기 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)에 따라 선택한 Azure 지역에서 netapp 계정을 만듭니다.  

3. 용량 풀을 [Azure NetApp Files 설정 하는 방법에 대 한 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)에 따라 Azure NetApp Files 용량 풀을 설정 합니다.  
이 문서에 제공 된 HANA 아키텍처는 단일 Azure NetApp Files 용량 풀, 초소형 서비스 수준을 사용 합니다. Azure에서 HANA 워크 로드에 대해 Ultra 또는 Premium [서비스 수준을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) Azure NetApp Files 하는 것이 좋습니다.  

4. [Azure NetApp Files에 서브넷 위임 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)에 설명 된 대로 Azure netapp 파일에 서브넷을 위임 합니다.  

5. [Azure NetApp Files 볼륨을 만들기 위한 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)에 따라 Azure NetApp Files 볼륨을 배포 합니다.  볼륨을 배포할 때 **nfsv 4.1** 버전을 선택 해야 합니다. NFSv 4.1에 대 한 현재 액세스에는 추가 허용 목록 필요 합니다. 지정 된 Azure NetApp Files [서브넷](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)에 볼륨을 배포 합니다. Azure NetApp Files 리소스와 Azure Vm은 동일한 Azure Virtual Network 또는 피어 링 Azure Virtual Network에 있어야 합니다. 예를 들어 <b>h n 1</b>-Mnt00001, <b>h n 1</b>-mnt00001 등은 볼륨 이름 및 Nfs://10.23.1.5/h n 1, mnt00001<b>nfs://10.23.1.4/</b><b>-h n 1</b>, mnt00001,, 등의 파일 Azure NetApp Files 경로입니다.  

   1. volume <b>h n 1</b>-mnt00001 (nfs://10.23.1.5/<b>h n 1</b>)
   2. volume <b>h n 1</b>-mnt00002 (nfs://10.23.1.6/<b>h n 1</b>)
   3. volume <b>h n 1</b>-mnt00001 (nfs://10.23.1.4/<b>h n 1</b>-mnt00001)
   4. volume <b>h n 1</b>-mnt00002 (nfs://10.23.1.6/<b>h n 1</b>-mnt00002)
   5. volume <b>h n 1</b>-shared (nfs://10.23.1.4/<b>h n 1</b>-shared)
   
   이 예제에서는 각 HANA 데이터 및 로그 볼륨에 대해 별도의 Azure NetApp Files을 사용 했습니다. 소규모 또는 비 생산성 시스템에 대 한 보다 효율적인 구성의 경우 모든 데이터 탑재 및 모든 로그 탑재를 단일 볼륨에 저장할 수 있습니다.  

### <a name="important-considerations"></a>중요 고려 사항

SAP Netweaver on SUSE 고가용성 아키텍처에 대 한 Azure NetApp Files 고려 하는 경우 다음과 같은 중요 한 사항을 고려해 야 합니다.

- 최소 용량 풀은 4 TiB입니다.  
- 최소 볼륨 크기는 100 GiB
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재 되는 모든 가상 머신은 동일한 Azure Virtual Network 또는 동일한 지역의 [피어 링 가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 에 있어야 합니다.  
- 선택한 가상 네트워크에는 Azure NetApp Files으로 위임 된 서브넷이 있어야 합니다.
- Azure NetApp 볼륨의 처리량은 [Azure NetApp Files의 서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)에 설명 된 대로 볼륨 할당량 및 서비스 수준의 기능입니다. HANA Azure NetApp 볼륨의 크기를 조정할 때 결과 처리량이 HANA 시스템 요구 사항을 충족 하는지 확인 합니다.  
- Azure NetApp Files에서 [내보내기 정책을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)제공 합니다. 허용 되는 클라이언트, 액세스 유형 (읽기 & 쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp Files 기능이 아직 영역을 인식 하지 않습니다. 현재 Azure NetApp Files 기능은 Azure 지역의 모든 가용성 영역에 배포 되지 않습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.  
- 짧은 대기 시간을 위해 Azure NetApp storage와 가까운 곳에 가상 머신을 배포 하는 것이 중요 합니다. SAP HANA 워크 로드는 짧은 대기 시간이 중요 합니다. Microsoft 담당자와 협력 하 여 가상 머신과 Azure NetApp Files 볼륨이 근접 하 게 배포 되었는지 확인 합니다.  
- <b>Sid</b>Adm의 사용자 id와 가상 컴퓨터의 `sapsys`에 대 한 그룹 id는 Azure NetApp Files의 구성과 일치 해야 합니다. 

> [!IMPORTANT]
> SAP HANA 워크 로드는 짧은 대기 시간이 중요 합니다. Microsoft 담당자와 협력 하 여 가상 머신과 Azure NetApp Files 볼륨이 근접 하 게 배포 되었는지 확인 합니다.  

> [!IMPORTANT]
> <b>Sid</b>Adm의 사용자 ID와 가상 컴퓨터와 Azure netapp 구성 간의 `sapsys`에 대 한 그룹 id가 일치 하지 않는 경우 가상 컴퓨터에 탑재 된 Azure netapp 볼륨의 파일에 대 한 권한이 `nobody` 표시 됩니다. [새 시스템을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) Azure NetApp Files에 온 보 딩 할 때 <b>sid</b>ADM에 올바른 사용자 ID와 `sapsys`에 대 한 그룹 id를 지정 해야 합니다.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files HANA 데이터베이스 크기 조정

Azure NetApp 볼륨의 처리량은 [Azure NetApp Files의 서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)에 설명 된 대로 볼륨 크기 및 서비스 수준의 기능입니다. 

Azure에서 SAP 용 인프라를 설계할 때 최소 처리량 특성으로 변환 되는 SAP의 최소 저장소 요구 사항에 대해 알고 있어야 합니다.

- 1mb i/o 크기를 사용 하 여 250 m b/초의/hana/log에서 읽기/쓰기를 사용 하도록 설정  
- 16mb 및 64 MB i/o 크기에 대해/hana/data에 대해 최소 400 m b/초의 읽기 작업을 사용 하도록 설정 합니다.  
- 16mb 및 64 MB i/o 크기를 사용 하 여/hana/data에 대해 최소 250 m b/초의 쓰기 작업을 사용 하도록 설정 합니다.  

볼륨 할당량의 1 TiB [처리량 제한은](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 다음과 같습니다. Azure NetApp Files
- Premium Storage 계층-64 MiB/s  
- Ultra Storage 계층-128 MiB/s  

데이터 및 로그에 대 한 SAP 최소 처리량 요구 사항을 충족 하 고 `/hana/shared`에 대 한 지침에 따라 권장 크기는 다음과 같습니다.

| 볼륨 | 크기<br /> Premium Storage 계층 | 크기<br /> Ultra Storage 계층 | 지원 되는 NFS 프로토콜 |
| --- | --- | --- |
| /hana/log | 4TiB | 2 TiB | v 4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v 4.1 |
| /hana/shared | 작업자 노드당 최대 (512gb, 1xRAM) | 작업자 노드당 최대 (512gb, 1xRAM) | v3 또는 v 4.1 |

이 문서에 제공 된 레이아웃에 대 한 SAP HANA 구성은 Azure NetApp Files Ultra Storage 계층을 사용 하는 것과 같습니다.

| 볼륨 | 크기<br /> Ultra Storage 계층 | 지원 되는 NFS 프로토콜 |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v 4.1 |
| /hana/shared | 2 TiB | v3 또는 v 4.1 |

> [!NOTE]
> 여기에 명시 된 Azure NetApp Files 크기 조정 권장 사항은 SAP가 인프라 공급자를 중심으로 하는 최소 요구 사항을 충족 하도록 대상으로 지정 됩니다. 실제 고객 배포 및 워크 로드 시나리오에서는이로 충분 하지 않을 수 있습니다. 이러한 권장 사항을 시작 점으로 사용 하 고 특정 워크 로드의 요구 사항에 따라 조정 합니다.  

> [!TIP]
> 볼륨을 `unmount` 하거나 가상 컴퓨터를 중지 하거나 SAP HANA를 중지 하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 변경할 수 있습니다. 이를 통해 응용 프로그램에 예상 된 처리량과 예측할 수 없는 처리량 요구를 모두 충족할 수 있습니다.

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Azure Portal를 통해 Linux 가상 컴퓨터 배포

먼저 Azure NetApp Files 볼륨을 만들어야 합니다. [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)에서 [azure virtual network 서브넷](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) 을 만듭니다. 가상 컴퓨터를 배포 합니다. 추가 네트워크 인터페이스를 만들고 네트워크 인터페이스를 해당 Vm에 연결 합니다. 각 가상 머신에는 세 개의 Azure virtual network 서브넷 (`storage`, `hana` 및 `client`)에 해당 하는 3 개의 네트워크 인터페이스가 있습니다.  [여러 네트워크 인터페이스 카드를 사용 하 여 Azure에서 Linux 가상 머신을 만드는 방법을](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)참조 하세요.  

> [!IMPORTANT]
> SAP HANA 워크 로드는 짧은 대기 시간이 중요 합니다. Microsoft 담당자와 협력 하 여 가상 머신 및 Azure NetApp Files 볼륨이 낮은 대기 시간을 위해 가까운 곳에 배포 되도록 합니다. SAP HANA Azure NetApp Files를 사용 하는 [새 SAP HANA 시스템을 온 보 딩](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)할 때 필요한 정보를 제출 합니다.  
> 
다음 단계에서는 리소스 그룹, Azure Virtual network 및 3 개의 Azure Virtual Network 서브넷 `storage`, `hana` 및 `client`를 이미 만들었다고 가정 합니다.  가상 컴퓨터를 배포할 때 저장소 네트워크 인터페이스가 가상 컴퓨터의 기본 인터페이스를 갖도록 저장소 서브넷을 선택 합니다. 가능 하지 않은 경우 저장소 서브넷 게이트웨이를 통해 Azure NetApp 위임 서브넷에 대 한 명시적 경로를 구성 해야 합니다. 

> [!IMPORTANT]
> 선택한 OS가 사용 중인 특정 VM 유형에서 SAP HANA용으로 인증된 SAP인지 반드시 확인하세요. 이 항목에 대한 SAP HANA 인증 VM 형식 및 OS 릴리스 목록은 [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서 조회할 수 있습니다. 특정 VM 유형에 대한 SAP HANA 지원 OS 릴리스의 전체 목록을 보려면 목록에 있는 VM 유형의 세부 정보를 클릭하세요.  

1. SAP HANA에 대 한 가용성 집합을 만듭니다. 최대 업데이트 도메인을 설정 했는지 확인 합니다.  

2. 3 개의 Virtual Machines 만들기 (**hanadb1**, **hanadb2**, **hanadb3**)  
   - SAP HANA에 대해 지원 되는 Azure 갤러리에서 SLES4SAP 이미지를 사용 합니다. 이 예제에서는 SLES4SAP 12 SP4 이미지를 사용 했습니다.  
   - SAP HANA에 대해 이전에 만든 가용성 집합을 선택 합니다.  
   - 저장소 Azure virtual network 서브넷을 선택 합니다. [가속화 네트워크](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)를 선택 합니다.  
가상 컴퓨터를 배포 하는 경우 네트워크 인터페이스 이름이 자동으로 생성 됩니다. 저장소 Azure Virtual Network 서브넷에 **hanadb1**, **hanadb2**및 **hanadb3**저장소로 연결 된 네트워크 인터페이스를 참조 합니다. 
3. `hana` 가상 네트워크 서브넷에 대해 3 개의 네트워크 인터페이스 (각 가상 머신에 대해 하나씩)를 만듭니다. 이 예에서는 **hanadb1**, **hanadb2**및 **hanadb3-hana**를 수행 합니다.  
4. **클라이언트** 가상 네트워크 서브넷에 대해 3 개의 네트워크 인터페이스 (각 가상 머신에 대해 하나씩)를 만듭니다. 이 예에서는 **hanadb1**, **hanadb2**및 **hanadb3-** client와 같은 작업을 수행 합니다.  
5. 새로 만든 가상 네트워크 인터페이스를 해당 가상 컴퓨터에 연결 합니다.  

    1. [Azure Portal](https://portal.azure.com/#home)에서 가상 머신으로 이동 합니다.  
    2. 왼쪽 탐색 창에서 Virtual Machines을 선택 합니다. 가상 컴퓨터 이름 (예 **hanadb1**)을 필터링 합니다. 가상 컴퓨터를 클릭 합니다.  
    3. 개요에서와 같이 중지를 선택 하 여 가상 컴퓨터의 할당을 취소 합니다.  
    4. 네트워킹, 네트워크 인터페이스 연결을 선택 합니다. **`hana`** 및 **클라이언트** 서브넷에 대 한 네트워크 인터페이스를 이미 만든 "네트워크 인터페이스 연결" 아래의 드롭다운 목록에서 선택 합니다.  저장합니다. 
    5. 나머지 가상 컴퓨터에 대해 반복 합니다. 예: **hanadb2** 및 **hanadb3**.
    6. 지금은 가상 컴퓨터를 중지 됨 상태로 둡니다. 다음으로 새로 연결 된 모든 네트워크 인터페이스에 대해 [가속화 된 네트워크](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 를 사용 하도록 설정 합니다.  

6. **`hana`** 및 **`client`** 서브넷의 추가 네트워크 인터페이스에 대해 가속화 된 네트워킹을 사용 하도록 설정 합니다.  

    1. [Azure Portal](https://portal.azure.com/#home) 에서 [cloud shell](https://azure.microsoft.com/features/cloud-shell/) 열기  
    2. **`hana`** 및 **`client`** 서브넷에 연결 된 추가 네트워크 인터페이스에 대 한 가속화 된 네트워킹을 사용 하도록 설정 하려면 다음 명령을 실행 합니다.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. 가상 컴퓨터 시작  

    1. 왼쪽 탐색 창에서 Virtual Machines을 선택 합니다. 가상 컴퓨터 이름 (예 **hanadb1**)을 필터링 합니다. 가상 컴퓨터를 클릭 합니다.  
    2. 개요에서 시작을 선택 합니다.  

## <a name="operating-system-configuration-and-preparation"></a>운영 체제 구성 및 준비

다음 항목에는 접두사 **[A]** -모든 노드에 적용, **[1]** -노드 1에만 적용 됩니다. [ **2]** -노드 2에만 적용 되며, [ **3]** -노드 3에만 적용 됩니다.

1. **[A]** 가상 컴퓨터에서 호스트 파일을 유지 관리 합니다. 모든 서브넷에 대 한 항목을 포함 합니다. 이 예제에서는 다음 항목이 `/etc/hosts`에 추가 되었습니다.  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** 의도 하지 않은 호스트 이름을 변경 하지 않도록 DHCP 및 클라우드 구성 설정을 변경 합니다.  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** [Netapp AFF systems with nfs 구성 가이드의 SAP HANA](https://www.netapp.com/us/media/tr-4435.pdf)에 설명 된 대로 netapp 시스템에서 SAP HANA 실행을 위해 운영 체제를 준비 합니다. NetApp 구성 설정에 대 한 구성 파일을 만듭니다. `/etc/sysctl.d/netapp-hana.conf`.  

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
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Microsoft Azure 구성 설정에 대 한 구성 파일 만들기: `/etc/sysctl.d/ms-az.conf`.  

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

4. **[A]** [NFS를 사용 하는 Netapp AFF 시스템 구성 가이드의 SAP HANA](https://www.netapp.com/us/media/tr-4435.pdf)에 권장 된 대로 sunrpc 설정을 조정 합니다.  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨 탑재

1. **[A]** HANA 데이터베이스 볼륨의 탑재 위치를 만듭니다.  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** **h n 1**에서 `/usr/sap`에 대 한 노드 관련 디렉터리를 만듭니다.  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** NFS 도메인 설정을 확인 합니다. 도메인이 **`localdomain`** 구성 되어 있고 매핑이 없음으로 설정 되어 있는지 확인 **합니다.**  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** NFSv4 ID 매핑을 사용 하지 않도록 설정 합니다. Mount 명령을 실행 하 여 `nfs4_disable_idmapping` 있는 디렉터리 구조를 만듭니다.  Kernel/driver에 대 한 액세스는 예약 되어 있으므로/hv/sd 아래에는 디렉터리를 수동으로 만들 수 없습니다.  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** SAP HANA 그룹 및 사용자를 수동으로 만듭니다. 그룹 sapsys 및 사용자 **h n 1**Adm의 id는 온 보 딩 중에 제공 되는 것과 동일한 id로 설정 되어야 합니다. 이 예에서는 Id가 **1001**으로 설정 됩니다. 그렇지 않은 경우에는 볼륨에 액세스할 수 없습니다.  그룹 sapsys 및 사용자 계정 **h n 1**adm 및 sapadm에 대 한 id는 모든 가상 머신에서 동일 해야 합니다.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** 공유 Azure NetApp Files 볼륨을 탑재 합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** **hanadb1**에 노드 특정 볼륨을 탑재 합니다.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** **hanadb2**에 노드 특정 볼륨을 탑재 합니다.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** **hanadb3**에 노드 특정 볼륨을 탑재 합니다.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 모든 HANA 볼륨이 NFS 프로토콜 버전 **NFSv4**로 탑재 되어 있는지 확인 합니다.  
    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>설치  

이 예제에서는 Azure를 사용 하는 대기 노드를 사용 하 여 스케일 아웃 구성에 SAP HANA을 배포 하는 경우 HANA 2.0 SP4를 사용 했습니다.  

### <a name="prepare-for-hana-installation"></a>HANA 설치 준비

1. **[A]** HANA 설치 전에 루트 암호를 설정 합니다 (설치가 완료 된 후 루트 암호를 사용 하지 않도록 설정할 수 있음). `root` 명령 `passwd`를 실행 합니다.  

2. **[1]** 암호를 입력 하 라는 메시지가 표시 되지 않고 **hanadb2** 및 **hanadb3**로 ssh 할 수 있는지 확인 합니다.  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** HANA 2.0 s p 4에 필요한 추가 패키지를 설치 합니다. 자세한 내용은 sap note [2593824](https://launchpad.support.sap.com/#/notes/2593824) 을 참조 하세요. 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2, 3]** SAP HANA `data` 및 `log` 디렉터리의 소유권을 **h n 1**adm으로 변경 합니다.   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA 설치

1. **[1]** [SAP HANA 2.0 설치 및 업데이트 가이드](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)의 지침에 따라 SAP HANA를 설치 합니다. 이 예제에서는 master, one worker 및 1 개의 대기 노드를 사용 하 여 확장 SAP HANA를 설치 합니다.  
   HANA 설치 소프트웨어 디렉터리에서 **hdblcm** 프로그램을 시작 합니다. `internal_network` 매개 변수를 사용 하 여 내부 HANA 노드 간 통신에 사용 되는 서브넷에 대 한 주소 공간을 전달 합니다.  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   프롬프트에서 다음 값을 입력 합니다.

     * 작업 선택: **1** (설치의 경우)을 입력 합니다.
     * 설치할 추가 구성 요소 **를 선택 합니다. 2, 3을 입력 합니다** .
     * 설치 경로 입력: enter 키를 누릅니다 (기본값은/hana/shared).
     * 로컬 호스트 이름 입력: 기본값을 적용 하려면 Enter 키를 누릅니다.
     * 시스템에 호스트를 추가 하 시겠습니까? **Y** 입력
     * 추가 하려면 coma로 구분 된 호스트 이름을 입력 합니다. **hanadb2, hanadb3**
     * 루트 사용자 이름 [root] 입력: 기본값을 적용 하려면 Enter 키를 누릅니다.
     * 루트 사용자 암호 입력: 루트의 암호 입력
     * 호스트 hanadb2에 대 한 역할 선택: **1** (작업자의 경우)을 입력 합니다.
     * 호스트 hanadb2 호스트 장애 조치 (Failover) 그룹 입력 [기본값]: Enter 키를 눌러 기본값을 적용 합니다.
     * 호스트 hanadb2 [<<assign automatically>>]의 저장소 파티션 번호 입력: 기본값을 적용 하려면 Enter 키를 누릅니다.
     * Host hanadb2에 대 한 작업자 그룹 입력 [기본값]: Enter 키를 눌러 기본값을 적용 합니다.
     * 호스트 hanadb3에 대 한 역할을 선택 합니다. **2** (대기)를 입력 합니다.
     * 호스트 hanadb3 호스트 장애 조치 (Failover) 그룹 입력 [기본값]: Enter 키를 눌러 기본값을 적용 합니다.
     * Host hanadb3에 대 한 작업자 그룹 입력 [기본값]: Enter 키를 눌러 기본값을 적용 합니다.
     * SAP HANA 시스템 ID를 입력 합니다. **h n 1** 을 입력 합니다.
     * 인스턴스 번호 [00] 입력: **03**
     * 로컬 호스트 작업자 그룹 입력 [기본값]: Enter 키를 눌러 기본값을 적용 합니다.
     * 시스템 사용량 선택/인덱스 [4] 입력: **4** (사용자 지정) 입력
     * 데이터 볼륨의 위치 입력 [/hana/data/HN1]: 기본값을 적용 하려면 Enter 키를 누릅니다.
     * 로그 볼륨의 위치 입력 [/hana/log/HN1]: 기본값을 적용 하려면 Enter 키를 누릅니다.
     * 최대 메모리 할당 제한? [n]: **n** 을 입력 합니다.
     * 호스트 hanadb1 [hanadb1]에 대 한 인증서 호스트 이름 입력: 기본값을 적용 하려면 Enter 키를 누릅니다.
     * 호스트 hanadb2 [hanadb2]에 대 한 인증서 호스트 이름 입력: 기본값을 적용 하려면 Enter 키를 누릅니다.
     * 호스트 hanadb3 [hanadb3]에 대 한 인증서 호스트 이름 입력: 기본값을 적용 하려면 Enter 키를 누릅니다.
     * 시스템 관리자 (hn1adm) 암호 입력: 암호를 입력 합니다.
     * 시스템 데이터베이스 사용자 (시스템) 암호 입력: 시스템 암호 입력
     * 시스템 데이터베이스 사용자 (시스템) 암호 확인: 시스템 암호 입력
     * 컴퓨터를 다시 부팅한 다음 시스템 다시 시작? [n]: **n** 을 입력 합니다. 
     * 계속 하 시겠습니까 (y/n): 요약의 유효성을 검사 하 고 모든 항목이 정상으로 표시 되 면 **y** 를 입력 합니다.


2. **[1]** global.asax 확인  

   Global.asax를 표시 하 고 내부 SAP HANA 노드 간 통신에 대 한 구성이 준비 되어 있는지 확인 합니다. 섹션 **통신**을 확인 합니다. **`hana`** 서브넷에 대 한 주소 공간이 있어야 하 고 `listeninterface` `.internal`으로 설정 되어야 합니다. **Internal_hostname_resolution**섹션을 확인 합니다. **`hana`** 서브넷에 속하는 HANA 가상 컴퓨터의 IP 주소가 있어야 합니다.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** 클라이언트 통신에 클라이언트 IP 주소가 사용 되도록 호스트 매핑을 추가 합니다. `public_host_resolution` 섹션을 추가 하 고 클라이언트 서브넷에서 해당 IP 주소를 추가 합니다.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** SAP HANA 다시 시작 하 여 변경 내용을 활성화 합니다.  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 클라이언트 인터페이스가 통신을 위해 **클라이언트** 서브넷의 IP 주소를 사용 하 게 되는지 확인 합니다.  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   구성을 확인 하는 방법에 대 한 자세한 내용은 sap note [2183363-SAP HANA 내부 네트워크 구성](https://launchpad.support.sap.com/#/notes/2183363) 을 참조 하세요.  

6. 기본 Azure NetApp 파일 저장소에 대 한 SAP HANA 최적화 하려면 다음 SAP HANA 매개 변수를 설정 합니다.

   - `max_parallel_io_requests` **128**
   - `async_read_submit`
   - `async_write_submit_active`
   - **모두** `async_write_submit_blocks`

   자세한 내용은 [SAP HANA On NetApp AFF Systems WITH NFS 구성 가이드](https://www.netapp.com/us/media/tr-4435.pdf)를 참조 하세요. 

   SAP HANA 2.0 시스템부터 `global.ini`에서 매개 변수를 설정할 수 있습니다. SAP note [1999930](https://launchpad.support.sap.com/#/notes/1999930)을 참조 하세요.  
   SAP HANA 1.0 시스템의 경우 SAP note [2267798](https://launchpad.support.sap.com/#/notes/2267798)에 설명 된 대로 설치 중에 이러한 매개 변수를 설정할 수 있습니다.  

7. Azure NetApp Files에서 사용 하는 저장소에는 파일 크기 제한 또는 16TB가 있습니다. SAP HANA는 저장소 제한을 암시적으로 인식 하지 않으며 파일 크기 제한인 16TB에 도달할 경우 새 데이터 파일을 자동으로 만들지 않습니다. SAP HANA는 16TB를 초과 하 여 파일의 증가를 시도 하므로 오류가 발생 하 고 결과적으로 인덱스 서버가 충돌 합니다. 

   > [!IMPORTANT]
   > 저장소 sybsystem의 [16Tb 한도](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) 를 초과 하 여 데이터 파일을 확장 하려는 SAP HANA을 방지 하려면 `global.ini`에서 다음 매개 변수를 설정 합니다.  
   > -  datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 자세한 내용은 SAP note [2400005](https://launchpad.support.sap.com/#/notes/2400005)를 참조 하세요.
   > SAP note [2631285](https://launchpad.support.sap.com/#/notes/2631285)에 주의 하세요. 

## <a name="test-sap-hana-failover"></a>테스트 SAP HANA 장애 조치 

1. SAP HANA 작업자 노드에서 노드 작동 중단 시뮬레이션  

   노드 작동 중단을 시뮬레이션 하기 전에 **h n 1**adm으로 다음 명령을 실행 하 여 환경의 상태를 캡처합니다.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
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

   작업자 노드의 루트로 다음 명령을 실행 합니다 .이 경우에는 노드 충돌을 **hanadb2** 합니다.  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   시스템에서 장애 조치 (failover)를 모니터링 합니다. 장애 조치 (failover)가 상태 캡처를 완료 한 경우 아래에 표시 된 상태와 같아야 합니다.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > 장애 조치 (failover)가 발생 하는 경우를 방지 하기 위해 노드에서 커널에 SAP HANA 문제가 발생 하는 경우 **모든** HANA 가상 머신에서 `kernel.panic`를 20 초로 설정 합니다. 구성은 `/etc/sysctl`에서 수행 됩니다. 가상 컴퓨터를 다시 부팅 하 여 변경 내용을 활성화 합니다. 장애 조치 (Failover)를 수행 하면 노드가 커널에 문제가 발생 하는 경우 10 분 이상 걸릴 수 있습니다.  

2. 이름 서버를 중지 합니다.  
   테스트 전에 **h n 1**adm으로 다음 명령을 실행 하 여 환경의 상태를 확인 합니다.  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   활성 마스터 노드 (이 경우 **hanadb1**)에서 **h n 1**adm으로 다음 명령을 실행 합니다.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   대기 노드 **hanadb3** 는 마스터 노드로 사용 됩니다. 장애 조치 (failover) 테스트가 완료 된 후의 리소스 상태:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
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

   **Hanadb1**에서 HANA 인스턴스를 다시 시작 합니다. 즉, 이름 서버가 종료 된 동일한 가상 머신에서 다시 시작 합니다. **Hanadb1** 노드는 환경에 다시 참가 하 고 대기 역할을 유지 합니다.  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **Hanadb1**에 대 한 SAP HANA 시작 된 후에는 다음 상태가 될 것입니다.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   이제 hanadb3에서 현재 활성 마스터 노드의 이름 서버를 다시 중지 합니다.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   노드 **hanadb1** 는 마스터 노드의 역할을 다시 시작 합니다. 상태, 장애 조치 (failover) 테스트가 완료 된 후 다음과 같이 표시 됩니다.
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

   **Hanadb3** 에서 SAP HANA를 시작 합니다. 대기 노드로 사용할 준비가 됩니다.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **Hanadb3**에서 SAP HANA 시작 된 후의 상태입니다.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

* [SAP 용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP 용 Azure Virtual Machines 배포][deployment-guide]
* [SAP 용 Azure Virtual Machines DBMS 배포][dbms-guide]
* 고가용성을 설정 하 고 SAP의 재해 복구를 계획 하는 방법을 알아보려면 
* HANA on Azure (큰 인스턴스)에 대 한 자세한 내용은 [azure의 SAP HANA (큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조 하세요.
* Azure Vm에서 SAP HANA의 고가용성을 설정 하 고 재해 복구를 계획 하는 방법에 대 한 자세한 내용은 [azure Virtual Machines (vm)의 SAP HANA 고가용성][sap-hana-ha] 을 참조 하세요.
