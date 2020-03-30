---
title: SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항 | Microsoft Docs
description: SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101358"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

이 가이드는 Microsoft Azure에서 SAP 소프트웨어를 구현하고 배포하는 방법에 대한 설명서의 일부입니다. 이 가이드를 읽기 전에 [계획 및 구현 가이드를][planning-guide]읽으십시오. 이 문서에서는 Azure 인프라를 서비스(IaaS) 기능으로 사용하여 Microsoft Azure 가상 시스템(VM)에서 SAP 관련 DBMS 시스템의 일반적인 배포 측면을 다룹니다.

이 백서는 지정된 플랫폼에서 SAP 소프트웨어의 설치 및 배포를 위한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP Notes를 보완합니다.

이 문서에서는 Azure VM에서 SAP 관련 DBMS 시스템을 실행할 때 고려해야 할 사항을 소개합니다. 이 챕터에는 특정 DBMS 시스템에 대한 참조가 거의 없습니다. 대신 이 문서 이후 특정 DBMS 시스템이 이 문서 내에서 처리됩니다.

## <a name="definitions"></a>정의
문서 전체에서 이러한 용어가 사용됩니다.

* **IaaS**: 서비스로서의 인프라.
* **PaaS**: 서비스로서의 플랫폼.
* **SaaS**: 서비스로서의 소프트웨어.
* **SAP 구성 요소**: ERP 중앙 구성 요소(ECC), BW(비즈니스 웨어하우스), 솔루션 관리자 또는 엔터프라이즈 포털(EP)과 같은 개별 SAP 응용 프로그램입니다. SAP 구성 요소는 기존 ABAP 또는 Java 기술을 기반으로 하거나 비즈니스 개체와 같은 비NetWeaver 기반 응용 프로그램을 기반으로 할 수 있습니다.
* **SAP 환경**: 하나 이상의 SAP 구성 요소가 논리적으로 그룹화되어 개발, 품질 보증, 교육, 재해 복구 또는 프로덕션과 같은 비즈니스 기능을 수행합니다.
* **SAP 환경**: 이 용어는 고객의 IT 환경에서 전체 SAP 자산을 나타냅니다. SAP 환경에는 모든 프로덕션 및 비프로덕션 환경이 포함됩니다.
* **SAP 시스템**: DBMS 계층과 SAP ERP 개발 시스템, SAP 비즈니스 웨어하우스 테스트 시스템 또는 SAP CRM 프로덕션 시스템의 응용 프로그램 계층의 조합입니다. Azure 배포에서 온-프레미스와 Azure 간에 이러한 두 계층을 분할하는 것은 지원되지 않습니다. 따라서 SAP 시스템은 온-프레미스에 배포되거나 Azure에 배포됩니다. Azure 또는 온-프레미스에서 SAP 환경의 다양한 시스템을 배포할 수 있습니다. 예를 들어 Azure에 SAP CRM 개발 및 테스트 시스템을 배포하지만 SAP CRM 프로덕션 시스템을 온-프레미스에 배포할 수 있습니다.
* **크로스-프레미스**: 온-프레미스 데이터 센터와 Azure 간에 사이트 간, 다중 사이트 또는 Azure ExpressRoute 연결이 있는 Azure 구독에 VM이 배포되는 시나리오를 설명합니다. 공통 Azure 설명서에서 이러한 종류의 배포를 크로스-프레미스 시나리오라고도 합니다. 

    연결하는 이유는 온-프레미스 도메인, 온-프레미스 Active Directory 및 온-프레미스 DNS를 Azure로 확장하기 위한 것입니다. 온-프레미스 배경은 구독의 Azure 자산으로 확장됩니다. 이 확장을 사용하면 VM이 온-프레미스 도메인에 속할 수 있습니다. 온-프레미스 도메인의 도메인 사용자는 서버에 액세스하고 DBMS 서비스와 같은 해당 VM에서 서비스를 실행할 수 있습니다. 온-프레미스에 배포된 VM과 Azure에 배포된 VM 간의 통신 및 이름 확인이 가능합니다. 이 시나리오는 Azure에서 SAP 자산을 배포하는 데 가장 일반적인 시나리오입니다. 자세한 내용은 [VPN 게이트웨이 계획 및 설계](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)를 참조하세요.

> [!NOTE]
> SAP 시스템의 크로스-프레미스 배포는 SAP 시스템을 실행하는 Azure 가상 시스템이 온-프레미스 도메인의 구성원이며 프로덕션 SAP 시스템에 지원되는 곳입니다. 일부 또는 전체 SAP 지형을 Azure로 배포하기 위한 크로스-프레미스 구성이 지원됩니다. Azure에서 전체 SAP 환경을 실행하더라도 해당 VM이 온-프레미스 도메인 및 Active Directory/LDAP의 일부가 되어야 합니다. 
>
> 이전 버전의 설명서에서는 하이브리드 IT 시나리오가 언급되었습니다. *하이브리드라는* 용어는 온-프레미스와 Azure 간에 교차-프레미스 연결이 있다는 사실에 뿌리를 두고 있습니다. 이 경우 하이브리드는 Azure의 VM이 온-프레미스 Active Directory의 일부임을 의미합니다.
>
>

일부 Microsoft 설명서에서는 특히 DBMS 고가용성 구성의 경우 상호-온전 시나리오를 약간 다르게 설명합니다. SAP 관련 문서의 경우 크로스-프레미스 시나리오는 사이트 간 또는 개인 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 연결과 온-프레미스와 Azure 간에 분산된 SAP 환경으로 귀결됩니다.

## <a name="resources"></a>리소스
Azure의 SAP 워크로드에서 사용할 수 있는 다른 문서가 있습니다. [Azure에서 SAP 워크로드로 시작: 시작한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 다음 관심 영역을 선택합니다.

다음 SAP 메모는 이 문서에서 다루는 영역과 관련하여 Azure의 SAP와 관련이 있습니다.

| Note 번호 | 제목 |
| --- | --- |
| [1928533] |Azure에서 SAP 응용 프로그램: 지원되는 제품 및 Azure VM 형식 |
| [2015553] |마이크로소프트 Azure에 SAP: 지원 필수 구성 조건 |
| [1999351] |SAP용 고급 Azure 모니터링 문제 해결 |
| [2178632] |Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [1409604] |Windows에서의 가상화: 향상된 모니터링 |
| [2191498] |Azure와 리눅스에 SAP: 향상 된 모니터링 |
| [2039619] |Oracle 데이터베이스를 사용하여 Microsoft Azure의 SAP 응용 프로그램: 지원되는 제품 및 버전 |
| [2233094] |DB6: 리눅스, UNIX 및 Windows용 IBM DB2를 사용하는 Azure의 SAP 응용 프로그램: 추가 정보 |
| [2243692] |Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제 |
| [1984787] |SUSE LINUX Enterprise Server 12: 설치 참고 |
| [2002167] |레드 햇 엔터프라이즈 리눅스 7.x: 설치 및 업그레이드 |
| [2069760] |Oracle Linux 7.x SAP 설치 및 업그레이드 |
| [1597355] |Linux에 대한 스왑 공간 권장 사항 |
| [2171857] |오라클 데이터베이스 12c: 리눅스에서 파일 시스템 지원 |
| [1114181] |오라클 데이터베이스 11g: 리눅스에서 파일 시스템 지원 |


리눅스에 대한 모든 SAP 노트에 대한 자세한 내용은 [SAP 커뮤니티 위키를](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)참조하십시오.

Microsoft Azure 아키텍처및 Microsoft Azure 가상 컴퓨터를 배포하고 운영하는 방법에 대한 실무 지식이 필요합니다. 자세한 내용은 [Azure 설명서를](https://docs.microsoft.com/azure/)참조하십시오.

일반적으로 Windows, Linux 및 DBMS 설치 및 구성은 기본적으로 온-프레미스에 설치하는 가상 컴퓨터 또는 베어 메탈 컴퓨터와 동일합니다. Azure IaaS를 사용할 때 다른 아키텍처 및 시스템 관리 구현 결정이 있습니다. 이 문서에서는 Azure IaaS를 사용할 때 준비할 특정 아키텍처 및 시스템 관리 차이점에 대해 설명합니다.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS 배포를 위한 VM의 스토리지 구조
이 장을 따르려면 [배포 가이드의][deployment-guide] [이 장에][deployment-guide-3] 나와 있는 정보를 읽고 이해합니다. 이 장을 읽기 전에 다양한 VM 시리즈와 표준 스토리지와 프리미엄 스토리지의 차이점에 대해 이해하고 알아야 합니다. 

Azure VM용 Azure 저장소에 대해 자세히 알아보려면 다음을 참조하십시오.

- [Azure Windows VM에 대한 관리 디스크 소개.](../../windows/managed-disks-overview.md)
- [Azure Linux VM에 대한 관리 디스크 소개.](../../linux/managed-disks-overview.md)

기본 구성에서는 일반적으로 운영 체제, DBMS 및 최종 SAP 바이너리가 데이터베이스 파일과 분리되어 있는 배포 구조를 권장합니다. Azure 가상 시스템에서 실행되는 SAP 시스템에는 운영 체제, 데이터베이스 관리 시스템 실행 및 SAP 실행 및 SAP 실행 및 기본 VHD 또는 디스크가 설치되는 것이 좋습니다. 

DBMS 데이터 및 로그 파일은 표준 저장소 또는 프리미엄 저장소에 저장됩니다. 그들은 별도의 디스크에 저장되고 원래 Azure 운영 체제 이미지 VM에 논리 디스크로 연결됩니다. Linux 배포의 경우 특히 SAP HANA의 경우 다양한 권장 사항이 문서화되어 있습니다.

디스크 레이아웃을 계획할 때 다음 항목 간의 최상의 균형을 찾으십시오.

* 데이터 파일 수
* 파일에 포함된 디스크 수
* 단일 디스크당 IOPS 할당량
* 디스크당 데이터 처리량
* VM 크기당 가능한 추가 데이터 디스크 수
* VM에서 제공할 수 있는 전체 스토리지 처리량
* 다양한 Azure Storage 유형에서 제공할 수 있는 대기 시간
* VM SLA.

Azure는 데이터 디스크 IOPS 할당량을 적용합니다. 이러한 할당량은 표준 저장소 및 프리미엄 저장소에서 호스팅되는 디스크에 대해 다릅니다. 또한 I/O 대기 시간도 두 스토리지 유형 간에 다릅니다. 프리미엄 스토리지는 더 나은 I/O 대기 시간을 제공합니다. 

각 다른 VM 유형에는 연결할 수 있는 제한된 수의 데이터 디스크가 있습니다. 또 다른 제한 사항은 특정 VM 유형만 프리미엄 저장소를 사용할 수 있다는 것입니다. 일반적으로 CPU 및 메모리 요구 사항에 따라 특정 VM 형식을 사용하기로 결정합니다. 또한 일반적으로 디스크 수 또는 프리미엄 저장소 디스크 의 유형에 따라 조정되는 IOPS, 대기 시간 및 디스크 처리량 요구 사항을 고려할 수도 있습니다. 각 디스크에서 달성할 IOPS 수와 처리량은 특히 프리미엄 스토리지의 경우 디스크 크기를 결정할 수 있습니다.

> [!NOTE]
> DBMS 배포의 경우 모든 데이터, 트랜잭션 로그 또는 다시 실행 파일에 대해 프리미엄 저장소를 사용하는 것이 좋습니다. 프로덕션 시스템을 배포할지 아니면 비프로덕션 시스템을 배포할지여부는 중요하지 않습니다.

> [!NOTE]
> Azure의 고유한 단일 [VM SLA의](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)이점을 활용하려면 연결된 모든 디스크는 기본 VHD를 포함하는 프리미엄 저장소 유형이어야 합니다.

> [!NOTE]
> Azure 데이터 센터에 인접한 공동 위치 타사 데이터 센터에 있는 저장소 하드웨어에서 SAP 데이터베이스의 데이터 및 로그 파일과 같은 주요 데이터베이스 파일을 호스팅하는 것은 지원되지 않습니다. SAP 워크로드의 경우 SAP 데이터베이스의 데이터 및 트랜잭션 로그 파일에 대해 네이티브 Azure 서비스로 표시되는 저장소만 지원됩니다.

데이터베이스 파일의 배치와 로그 및 다시 작업 파일 및 사용하는 Azure 저장소 유형은 IOPS, 대기 시간 및 처리량 요구 사항에 의해 정의됩니다. IOPS가 충분하려면 여러 디스크를 사용하거나 더 큰 프리미엄 저장소 디스크를 사용해야 할 수 있습니다. 여러 디스크를 사용하는 경우 데이터 파일 또는 로그 파일이 포함된 디스크 전체에 소프트웨어 스트라이프를 작성하고 다시 재생합니다. 이러한 경우 기본 프리미엄 저장소 디스크의 IOPS 및 디스크 처리량 SLA 또는 표준 저장소 디스크의 최대 달성 가능한 IOPS는 결과 스트라이프 집합에 대해 누적됩니다.

이미 언급했듯이 IOPS 요구 사항이 단일 VHD가 제공할 수 있는 것을 초과하는 경우 여러 VHD에서 데이터베이스 파일에 필요한 IOPS 수의 균형을 조정합니다. IOPS 로드를 디스크 에 배포하는 가장 쉬운 방법은 다른 디스크에 소프트웨어 스트라이프를 구축하는 것입니다. 그런 다음 소프트웨어 스트라이프에서 조각된 Lun에 SAP DBMS의 여러 데이터 파일을 배치합니다. 스트라이프의 디스크 수는 IoP 요구, 디스크 처리량 요구 및 볼륨 요구에 의해 구동됩니다.


---
> ![Windows][Logo_Windows] Windows
>
> Windows 저장소 공간을 사용하여 여러 Azure VHD에 스트라이프 세트를 만드는 것이 좋습니다. 적어도 윈도우 서버 2012 R2 또는 윈도우 서버 2016을 사용합니다.
>
> ![Linux][Logo_Linux] Linux
>
> 리눅스에서 소프트웨어 RAID를 구축하기 위해 MDADM 및 논리 볼륨 관리자 (LVM)만 지원됩니다. 자세한 내용은 다음을 참조하세요.
>
> - MDADM을 사용하여 [리눅스에서 소프트웨어 RAID 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
> - [Azure에서 Linux VM에 LVM 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)(LVM 사용)
>
>

---

> [!NOTE]
> Azure Storage는 VHD의 세 개의 이미지를 유지하므로 스트라이프할 때 중복성을 구성하는 것은 의미가 없습니다. I/Os가 다른 VHD에 분산되도록 스트라이프만 구성하면 됩니다.
>

### <a name="managed-or-nonmanaged-disks"></a>관리또는비관리디스크
Azure 저장소 계정은 관리 구문이며 제한 사항입니다. 표준 저장소 계정과 프리미엄 저장소 계정 간에 제한이 다릅니다. 기능 및 제한 사항에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표를](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)참조하십시오.

표준 저장소의 경우 저장소 계정당 IOPS에 제한이 있습니다. [Azure 저장소 확장성 및 성능 대상](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)문서에서 총 요청 **속도가** 포함된 행을 참조하십시오. Azure 구독당 저장소 계정 수에 대한 초기 제한도 있습니다. 이러한 저장소 계정의 한계에 부딪히지 않도록 다양한 저장소 계정에서 더 큰 SAP 환경에 대한 VH의 균형을 조정합니다. 수천 개 이상의 VHD가 있는 수백 대의 가상 시스템에 대해 이야기할 때 이것은 지루한 작업입니다.

SAP 워크로드와 함께 DBMS 배포에 표준 저장소를 사용하는 것은 권장되지 않으므로 표준 저장소에 대한 참조 및 권장 사항은 이 짧은 [문서로](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx) 제한됩니다.

Microsoft는 다양한 Azure 저장소 계정에 VHD를 계획하고 배포하는 관리 작업을 피하기 위해 2017년에 [Azure 관리 디스크를](https://azure.microsoft.com/services/managed-disks/) 도입했습니다. 관리형 디스크는 표준 저장소 및 프리미엄 스토리지에 사용할 수 있습니다. 관리되지 않는 디스크와 비교하여 관리 디스크의 주요 장점은 다음과 같습니다.

- 관리 디스크의 경우 Azure는 배포 시 여러 저장소 계정에 서로 다른 VHD를 자동으로 배포합니다. 이러한 방식으로 데이터 볼륨, I/O 처리량 및 IOPS에 대한 저장소 계정 제한이 적중되지 않습니다.
- Azure Storage는 관리디스크를 사용하여 Azure 가용성 집합의 개념을 준수합니다. VM이 Azure 가용성 집합의 일부인 경우 VM의 기본 VHD 및 연결된 디스크가 다른 오류 및 업데이트 도메인에 배포됩니다.


> [!IMPORTANT]
> Azure 관리 디스크의 장점을 고려할 때 일반적으로 DBMS 배포 및 SAP 배포에 Azure 관리 디스크를 사용하는 것이 좋습니다.
>

관리되지 않는 디스크에서 관리 디스크로 변환하려면 다음을 참조하십시오.

- [관리되지 않는 디스크에서 관리되는 디스크로 Windows 가상 컴퓨터를 변환합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Linux 가상 컴퓨터를 관리되지 않는 디스크에서 관리디스크로 변환합니다.](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 및 데이터 디스크에 대한 캐싱
디스크를 VM에 탑재할 때 VM과 Azure 저장소에 있는 디스크 간의 I/O 트래픽이 캐시되는지 여부를 선택할 수 있습니다. 표준 및 프리미엄 저장소는 이러한 유형의 캐시에 대해 두 가지 기술을 사용합니다.

다음 권장 사항은 표준 DBMS에 대한 이러한 I/O 특성을 가정합니다.

- 주로 데이터베이스의 데이터 파일에 대한 읽기 워크로드입니다. 이러한 읽기는 DBMS 시스템에 중요한 성능입니다.
- 데이터 파일에 대한 쓰기는 검사점 또는 상수 스트림에 따라 버스트에서 발생합니다. 하루 에 걸쳐 평균, 읽기 보다 적은 쓰기가 있다. 데이터 파일에서 읽는 반대로 이러한 쓰기는 비동기이며 사용자 트랜잭션을 보유하지 않습니다.
- 트랜잭션 로그 또는 다시 실행 파일에 대한 읽기는 거의 없습니다. 트랜잭션 로그 백업을 수행할 때 예외는 큰 I/Os입니다.
- 트랜잭션 또는 로그 파일을 다시 실행하기 위한 주요 로드는 쓰기입니다. 워크로드의 특성에 따라 I/O를 4KB 만큼 작게 만들거나 다른 경우에는 I/O 크기를 1MB 이상 으로 만들 수 있습니다.
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에 유지되어야 합니다.

표준 저장소의 경우 가능한 캐시 유형은 다음과 같습니다.

* None
* 읽기
* 읽기/쓰기

일관되고 결정적인 성능을 얻으려면 DBMS 관련 데이터 파일, 로그 앤 다시 작업 파일 및 테이블스페이스를 포함하는 모든 디스크에 대한 표준 저장소에 캐싱을 **NONE으로**설정합니다. 기본 VHD의 캐싱은 기본값으로 유지할 수 있습니다.

프리미엄 스토리지의 경우 다음과 같은 캐싱 옵션이 있습니다.

* None
* 읽기
* 읽기/쓰기
* 없음 + Azure M 시리즈 VM용인 액셀러레이터 쓰기
* 읽기 + Azure M 시리즈 VM전용 액셀러레이터 쓰기

프리미엄 저장소의 경우 SAP 데이터베이스의 **데이터 파일에 대해 Read 캐싱을** 사용하고 **로그 파일의 디스크에 대한 캐싱 없음을**선택하는 것이 좋습니다.

M-시리즈 배포의 경우 DBMS 배포에 Azure 쓰기 가속기를 사용하는 것이 좋습니다. Azure 쓰기 가속기의 세부 정보, 제한 및 배포는 [쓰기 가속기 사용 을](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)참조하십시오.


### <a name="azure-nonpersistent-disks"></a>Azure 비지속 디스크
Azure VM은 VM을 배포한 후 비영구 디스크를 제공합니다. VM 재부팅의 경우 해당 드라이브의 모든 콘텐츠가 지워지게 됩니다. 데이터 파일 및 데이터베이스의 로그 및 다시 재생 파일은 어떠한 경우에도 해당 비유지 드라이브에 위치해서는 안됩니다. 일부 데이터베이스에는 이러한 비지속 드라이브가 tempdb 및 temp 테이블스페이스에 적합할 수 있는 예외가 있을 수 있습니다. 해당 비지속 드라이브는 해당 VM 제품군의 처리량이 제한되어 있으므로 A 시리즈 VM에 해당 드라이브를 사용하지 마십시오. 

자세한 내용은 [Azure의 Windows VM에서 임시 드라이브 이해를](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)참조하십시오.

---
> ![Windows][Logo_Windows] Windows
>
> Azure VM의 드라이브 D는 Azure 계산 노드의 일부 로컬 디스크에서 백업되는 비지속 드라이브입니다. 지속되지 않으므로 VM을 다시 부팅할 때 드라이브 D의 콘텐츠에 대한 변경 내용이 손실됩니다. 변경 사항에는 저장된 파일, 생성된 디렉터리 및 설치된 응용 프로그램이 포함됩니다.
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VM은 Azure 계산 노드의 로컬 디스크에서 백업되는 비지속 드라이브인 /mnt/resource에 드라이브를 자동으로 탑재합니다. 지속되지 않으므로 VM을 다시 부팅할 때 /mnt/resource의 콘텐츠에 대한 변경 내용이 손실됩니다. 변경 사항에는 저장된 파일, 생성된 디렉터리 및 설치된 응용 프로그램이 포함됩니다.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage 복원력
Microsoft Azure Storage는 OS및 연결된 디스크 또는 Blob을 갖춘 기본 VHD를 적어도 세 개의 별도 저장소 노드에 저장합니다. 이러한 유형의 저장소를 LRS(로컬 중복 저장소)라고 합니다. LRS는 Azure의 모든 유형의 저장소에 대한 기본값입니다.

다른 중복 방법이 있습니다. 자세한 내용은 [Azure Storage 복제를](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)참조하십시오.

> [!NOTE]
>프리미엄 저장소는 데이터베이스를 저장하고 파일을 로그 앤 다시 작업하는 DBMS VM 및 디스크에 권장되는 스토리지 유형입니다. 프리미엄 저장소에 사용할 수 있는 유일한 중복 방법은 LRS입니다. 따라서 데이터베이스 데이터 복제를 다른 Azure 지역 또는 가용성 영역으로 사용하도록 데이터베이스 메서드를 구성해야 합니다. 데이터베이스 방법에는 SQL Server Always On, Oracle 데이터 가드 및 HANA 시스템 복제가 포함됩니다.


> [!NOTE]
> DBMS 배포의 경우 표준 저장소에는 GRS(지역 중복 저장소)를 사용하지 않는 것이 좋습니다. GRS는 성능에 심각한 영향을 미치며 VM에 연결된 여러 VHD에서 쓰기 순서를 존중하지 않습니다. 다른 VHD에서 쓰기 순서를 지키지 않을 경우 복제 대상 측에서 일관성 없는 데이터베이스가 발생할 수 있습니다. 이 상황은 데이터베이스 및 로그 및 다시 작업 파일이 여러 VHD에 분산되어 있는 경우 발생하며, 일반적으로 소스 VM 측에서 발생합니다.



## <a name="vm-node-resiliency"></a>VM 노드 복원력
Azure는 VM에 대해 여러 가지 SLA를 제공합니다. 자세한 내용은 가상 시스템에 대한 [SLA의](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)최신 릴리스를 참조하십시오. DBMS 계층은 일반적으로 SAP 시스템의 가용성에 매우 중요하므로 가용성 집합, 가용성 영역 및 유지 관리 이벤트를 이해해야 합니다. 이러한 개념에 대한 자세한 내용은 [Azure의 Windows 가상 컴퓨터 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) 및 [Azure의 Linux 가상 컴퓨터 의 가용성 관리를](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)참조하십시오.

SAP 워크로드가 있는 프로덕션 DBMS 시나리오에 대한 최소 권장 사항은 다음과 같다.

- 동일한 Azure 리전에서 설정된 별도의 가용성에 두 개의 VM을 배포합니다.
- 동일한 Azure 가상 네트워크에서 이러한 두 VM을 실행하고 NIC가 동일한 서브넷에서 연결되도록 합니다.
- 데이터베이스 메서드를 사용하여 두 번째 VM에 대한 상시 대기를 유지합니다. 메서드는 SQL Server Always On, Oracle Data Guard 또는 HANA System Replication일 수 있습니다.

또한 다른 Azure 지역에 세 번째 VM을 배포하고 동일한 데이터베이스 메서드를 사용하여 다른 Azure 지역에서 비동기 복제본을 제공할 수도 있습니다.

Azure 가용성 집합을 설정하는 방법에 대한 자세한 내용은 [이 자습서를](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)참조하십시오.



## <a name="azure-network-considerations"></a>Azure 네트워크 고려 사항
대규모 SAP 배포에서 [Azure 가상 데이터 센터의](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)Blueprint를 사용합니다. 조직의 다른 부분에 대한 가상 네트워크 구성 및 권한 및 역할 할당에 사용합니다.

이러한 모범 사례는 수백 개의 고객 배포의 결과입니다.

- SAP 응용 프로그램이 배포되는 가상 네트워크는 인터넷에 액세스할 수 없습니다.
- 데이터베이스 VM은 응용 프로그램 계층과 동일한 가상 네트워크에서 실행됩니다.
- 가상 네트워크 내의 VM에는 개인 IP 주소의 정적 할당이 있습니다. 자세한 내용은 Azure 의 [IP 주소 유형 및 할당 메서드를](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)참조하십시오.
- DBMS VM을 대/으로부터 라우팅 제한은 로컬 DBMS VM에 설치된 방화벽으로 *설정되지 않습니다.* 대신 트래픽 라우팅은 [NSG(네트워크 보안 그룹)로](https://docs.microsoft.com/azure/virtual-network/security-overview)정의됩니다.
- 트래픽을 DBMS VM으로 분리하고 격리하려면 VM에 다른 NIC를 할당합니다. 모든 NIC는 서로 다른 IP 주소를 얻으며 모든 NIC는 다른 가상 네트워크 서브넷에 할당됩니다. 모든 서브넷에는 NSG 규칙이 다릅니다. 네트워크 트래픽의 격리 또는 분리는 라우팅을 위한 조치입니다. 네트워크 처리량에 대한 할당량을 설정하는 데 사용되지 않습니다.

> [!NOTE]
> Azure를 통해 정적 IP 주소를 할당하는 것은 개별 가상 NIC에 할당하는 것을 의미합니다. 게스트 OS 내의 정적 IP 주소를 가상 NIC에 할당하지 마십시오. Azure Backup과 같은 일부 Azure 서비스는 최소한 기본 가상 NIC가 정적 IP 주소가 아니라 DHCP로 설정되어 있다는 사실에 의존합니다. 자세한 내용은 [Azure 가상 시스템 백업 을 참조하십시오.](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking) VM에 여러 정적 IP 주소를 할당하려면 VM에 여러 가상 NIC를 할당합니다.
>


> [!IMPORTANT]
> SAP 응용 프로그램과 SAP NetWeaver-, Hybris-또는 S/4HANA 기반 SAP 시스템의 DBMS 계층 간의 통신 경로에서 [네트워크 가상 어플라이언스를](https://azure.microsoft.com/solutions/network-appliances/) 구성하는 것은 지원되지 않습니다. 이 제한은 기능 및 성능상의 이유로 제한됩니다. SAP 응용 프로그램 계층과 DBMS 계층 간의 통신 경로는 직접적인 경로여야 합니다. 이러한 ASG 및 NSG 규칙이 직접 통신 경로를 허용하는 경우 제한에는 [응용 프로그램 보안 그룹(ASG) 및 NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) 규칙이 포함되지 않습니다. 
>
> 네트워크 가상 어플라이언스가 지원되지 않는 다른 시나리오는 다음과 같습니다.
>
> * SUSE Linux 엔터프라이즈 [서버의 SUSE Linux 엔터프라이즈 서버에서 SAP NetWeaver의 고가용성에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)설명된 대로 Linux Pacemaker 클러스터 노드와 SBD 장치를 나타내는 Azure VM 간의 통신 경로입니다.
> * Azure 에서 [파일 공유를 사용하여 Windows 장애 조치 클러스터에서 SAP ASCS/SCS 인스턴스 클러스터에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)설명된 대로 설정된 Azure VM과 Windows 서버 확장 파일 서버(SOFS) 간의 통신 경로 
>
> 통신 경로의 네트워크 가상 어플라이언스는 두 통신 파트너 간의 네트워크 대기 시간을 쉽게 두 배로 늘릴 수 있습니다. 또한 SAP 응용 프로그램 계층과 DBMS 계층 간의 중요한 경로에서 처리량을 제한할 수도 있습니다. 일부 고객 시나리오에서는 네트워크 가상 어플라이언스가 Pacemaker Linux 클러스터에 실패할 수 있습니다. Linux Pacemaker 클러스터 노드 간의 통신이 네트워크 가상 어플라이언스를 통해 SBD 장치와 통신하는 경우입니다.
>

> [!IMPORTANT]
> 지원되지 *않는* 또 다른 디자인은 SAP 응용 프로그램 계층과 DBMS 계층을 서로 [피어링되지](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 않는 다른 Azure 가상 네트워크로 분리하는 것입니다. 다른 Azure 가상 네트워크를 사용하는 대신 Azure 가상 네트워크 내에서 서브넷을 사용하여 SAP 응용 프로그램 계층과 DBMS 계층을 분리하는 것이 좋습니다. 
>
> 권장 사항을 따르지 않고 두 계층을 서로 다른 가상 네트워크로 분리하지 않기로 결정한 경우 두 가상 네트워크를 [피어로](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)만들어야 합니다. 
>
> [피어가 피어있는](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 두 Azure 가상 네트워크 간의 네트워크 트래픽에는 전송 비용이 부과됩니다. 많은 테라바이트로 구성된 거대한 데이터 볼륨은 SAP 응용 프로그램 계층과 DBMS 계층 간에 교환됩니다. SAP 응용 프로그램 계층과 DBMS 계층이 피어링된 두 Azure 가상 네트워크 간에 분리된 경우 상당한 비용을 누적할 수 있습니다.

Azure 가용성 집합 내에서 프로덕션 DBMS 배포에 두 개의 VM을 사용합니다. 또한 SAP 응용 프로그램 계층에 대해 별도의 라우팅을 사용하고 두 DBMS VM에 대한 관리 및 운영 트래픽을 사용합니다. 다음 이미지를 참조하세요.

![두 서브넷에 있는 두 VM에 대한 다이어그램](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Azure 로드 밸러서를 사용하여 트래픽 리디렉션
SQL Server Always On 또는 HANA 시스템 복제와 같은 기능에 사용되는 개인 가상 IP 주소를 사용하려면 Azure 로드 밸런서의 구성이 필요합니다. 로드 밸런서는 프로브 포트를 사용하여 활성 DBMS 노드를 확인하고 트래픽을 해당 활성 데이터베이스 노드로만 라우팅합니다. 

데이터베이스 노드의 장애 조치(failover)가 있는 경우 SAP 응용 프로그램을 다시 구성할 필요가 없습니다. 대신 가장 일반적인 SAP 응용 프로그램 아키텍처는 개인 가상 IP 주소에 대해 다시 연결됩니다. 한편 로드 밸러블러는 개인 가상 IP 주소에 대한 트래픽을 두 번째 노드로 리디렉션하여 노드 장애 조치(failover)에 반응합니다.

Azure는 기본 SKU와 표준 SKU라는 두 가지 로드 [밸러울러 SKU를](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)제공합니다. Azure 가용성 영역에 배포하려는 경우가 아니면 기본 로드 밸러워 SKU가 잘 수행됩니다.

DBMS VM과 SAP 응용 프로그램 계층 간의 트래픽이 항상 로드 밸런서를 통해 라우팅되고 있습니까? 대답은 부하 분산 장치를 구성하는 방법에 따라 다릅니다. 

이 때 DBMS VM에 들어오는 트래픽은 항상 로드 밸런서를 통해 라우팅됩니다. DBMS VM에서 응용 프로그램 계층 VM으로의 나가는 트래픽 경로는 로드 밸런서의 구성에 따라 다릅니다. 

부하 분산 장치는 DirectServerReturn 옵션을 제공합니다. 이 옵션이 구성된 경우 DBMS VM에서 SAP 응용 프로그램 계층으로 전달되는 트래픽은 로드 밸런서를 통해 *라우팅되지 않습니다.* 대신 응용 프로그램 계층으로 직접 이동합니다. DirectServerReturn이 구성되지 않은 경우 SAP 응용 프로그램 계층으로의 반환 트래픽은 로드 밸러버를 통해 라우팅됩니다.

SAP 응용 프로그램 계층과 DBMS 계층 사이에 배치된 로드 밸런서와 함께 DirectServerReturn을 구성하는 것이 좋습니다. 이 구성은 두 계층 간의 네트워크 대기 시간을 줄입니다.

SQL Server Always On을 사용하여 이 구성을 설정하는 방법에 대한 예는 [Azure의 Always 켜기 가용성 그룹에 대한 ILB 수신기 구성을](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)참조하십시오.

게시된 GitHub JSON 템플릿을 Azure의 SAP 인프라 배포에 대한 참조로 사용하는 경우 [SAP 3 계층 시스템에 대해](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)이 템플릿을 연구합니다. 이 템플릿에서는 로드 밸러저에 대한 올바른 설정을 볼 수도 있습니다.

### <a name="azure-accelerated-networking"></a>Azure 가속 네트워킹
Azure VM 간의 네트워크 대기 시간을 더 줄이려면 [Azure 가속 네트워킹을](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)선택하는 것이 좋습니다. SAP 워크로드, 특히 SAP 응용 프로그램 계층 및 SAP DBMS 계층에 대해 Azure VM을 배포할 때 이 용도로 사용합니다.

> [!NOTE]
> 모든 VM 유형이 가속 네트워킹을 지원하는 것은 아닙니다. 이전 문서에서는 가속 네트워킹을 지원하는 VM 형식을 나열합니다.
>

---
> ![Windows][Logo_Windows] Windows
>
> Windows용 가속 네트워킹을 사용하여 VM을 배포하는 방법을 알아보려면 [가속 네트워킹을 사용하여 Windows 가상 컴퓨터 만들기를](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)참조하십시오.
>
> ![Linux][Logo_Linux] Linux
>
> Linux 배포판에 대한 자세한 내용은 [가속 네트워킹을 사용하여 Linux 가상 시스템 만들기를](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)참조하십시오.
>
>

---

> [!NOTE]
> SUSE, Red Hat 및 Oracle Linux의 경우 가속 네트워킹은 최신 릴리스에서 지원됩니다. SLES 12 SP2 또는 RHEL 7.2와 같은 이전 릴리스는 Azure 가속 네트워킹을 지원하지 않습니다.
>


## <a name="deployment-of-host-monitoring"></a>호스트 모니터링 배포
Azure 가상 컴퓨터에서 SAP 응용 프로그램을 프로덕션용으로 사용하려면 Azure 가상 컴퓨터를 실행하는 실제 호스트에서 호스트 모니터링 데이터를 얻을 수 있는 기능이 필요합니다. SAPOSCOL 및 SAP HostAgent에서 이 기능을 사용하려면 특정 SAP HostAgent 패치 수준이 필요합니다. 정확한 패치 수준은 SAP Note [1409604]에 설명되어 있습니다.

SAPOSCOL 및 SAP 호스트 에이전트에 호스트 데이터를 제공하는 구성 요소의 배포 및 해당 구성 요소의 수명 주기 관리에 대한 자세한 내용은 [배포 가이드를][deployment-guide]참조하십시오.


## <a name="next-steps"></a>다음 단계
특정 DBMS에 대한 자세한 내용은 다음을 참조하십시오.

- [SAP 워크로드에 대한 SQL Server Azure Virtual Machines DBMS 배포](dbms_guide_sqlserver.md)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](dbms_guide_oracle.md)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](dbms_guide_ibm.md)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](dbms_guide_sapase.md)
- [Azure에서 SAP maxDB, 라이브 캐시 및 콘텐츠 서버 배포](dbms_guide_maxdb.md)
- [Azure 운영 가이드의 SAP HANA](hana-vm-operations.md)
- [Azure Virtual Machines의 SAP HANA 고가용성](sap-hana-availability-overview.md)
- [Azure 가상 머신에서 SAP HANA에 대한 백업 가이드](sap-hana-backup-guide.md)

