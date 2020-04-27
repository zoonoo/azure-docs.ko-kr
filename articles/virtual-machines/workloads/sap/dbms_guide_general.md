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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
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

이 가이드는 Microsoft Azure에서 SAP 소프트웨어를 구현 하 고 배포 하는 방법에 대 한 설명서의 일부입니다. 이 가이드를 읽기 전에 [계획 및 구현 가이드][planning-guide]를 참조 하세요. 이 문서에서는 Azure IaaS (infrastructure as a service) 기능을 사용 하 여 Microsoft Azure Vm (가상 머신)에서 SAP 관련 DBMS 시스템의 일반적인 배포 측면을 설명 합니다.

이 문서는 지정 된 플랫폼에서 SAP 소프트웨어 설치 및 배포에 대 한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP note를 보완 합니다.

이 문서에서는 Azure VM에서 SAP 관련 DBMS 시스템을 실행할 때 고려해야 할 사항을 소개합니다. 이 챕터에는 특정 DBMS 시스템에 대한 참조가 거의 없습니다. 대신이 문서 뒤에 나오는 특정 DBMS 시스템이이 문서에서 처리 됩니다.

## <a name="definitions"></a>정의
문서 전체에서 다음과 같은 용어가 사용 됩니다.

* **IaaS**: 서비스로 서의 인프라입니다.
* **PaaS**: Platform as a service.
* **SaaS**: Software as a service.
* **Sap 구성 요소**: ECC (ERP Central component), BW (Business Warehouse), Solution MANAGER 또는 EP (Enterprise Portal)와 같은 개별 SAP 응용 프로그램입니다. SAP 구성 요소는 기존의 ABAP 또는 Java 기술을 기반으로 하거나 비즈니스 개체와 같은 NetWeaver 기반이 아닌 응용 프로그램을 기반으로 할 수 있습니다.
* **Sap 환경**: 하나 이상의 sap 구성 요소가 논리적으로 그룹화 되어 개발, 품질 보증, 교육, 재해 복구 또는 프로덕션과 같은 비즈니스 기능을 수행 합니다.
* **Sap 가로**:이 용어는 고객의 IT 환경에서 전체 SAP 자산을 의미 합니다. SAP 가로에는 모든 프로덕션 및 비프로덕션 환경이 포함 됩니다.
* **Sap 시스템**: sap ERP 개발 시스템, Sap Business Warehouse 테스트 시스템, sap CRM 프로덕션 시스템 등의 응용 프로그램 계층과 DBMS 계층의 조합입니다. Azure 배포에서는 온-프레미스와 Azure 간에 이러한 두 계층을 분할 하는 것은 지원 되지 않습니다. 따라서 SAP 시스템은 온-프레미스에 배포 되거나 Azure에 배포 됩니다. Azure 또는 온-프레미스에서 SAP 환경의 서로 다른 시스템을 배포할 수 있습니다. 예를 들어 Azure에는 SAP CRM 개발 및 테스트 시스템을 배포할 수 있지만 온-프레미스에는 SAP CRM 프로덕션 시스템을 배포할 수 있습니다.
* **프레미스 간**: vm이 온-프레미스 데이터 센터와 azure 간에 사이트 간, 멀티 사이트 또는 azure express 경로 연결을 포함 하는 azure 구독에 배포 되는 시나리오를 설명 합니다. 공통 Azure 설명서에서 이러한 종류의 배포를 크로스-프레미스 시나리오라고도 합니다. 

    연결하는 이유는 온-프레미스 도메인, 온-프레미스 Active Directory 및 온-프레미스 DNS를 Azure로 확장하기 위한 것입니다. 온-프레미스 배경은 구독의 Azure 자산으로 확장됩니다. 이 확장을 사용하면 VM이 온-프레미스 도메인에 속할 수 있습니다. 온-프레미스 도메인의 도메인 사용자는 서버에 액세스 하 고 DBMS 서비스와 같은 해당 Vm에서 서비스를 실행할 수 있습니다. 온-프레미스에 배포된 VM과 Azure에 배포된 VM 간의 통신 및 이름 확인이 가능합니다. 이 시나리오는 Azure에서 SAP 자산을 배포 하는 데 가장 일반적으로 사용 되는 시나리오입니다. 자세한 내용은 [VPN 게이트웨이 계획 및 설계](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)를 참조하세요.

> [!NOTE]
> SAP 시스템의 크로스-프레미스 배포는 SAP 시스템을 실행 하는 Azure virtual machines가 온-프레미스 도메인의 구성원이 고 프로덕션 SAP 시스템에 대해 지원 되는 위치입니다. 일부 또는 전체 SAP 지형을 Azure로 배포하기 위한 크로스-프레미스 구성이 지원됩니다. Azure에서 전체 SAP 환경을 실행 하는 경우에도 이러한 Vm이 온-프레미스 도메인 및 Active Directory/LDAP에 포함 되어야 합니다. 
>
> 이전 버전의 설명서에서는 하이브리드 IT 시나리오에 대해 설명 했습니다. *하이브리드* 이라는 용어는 온-프레미스와 Azure 간에 프레미스 간 연결이 있음을 의미 합니다. 이 경우 하이브리드는 Azure의 Vm이 온-프레미스 Active Directory의 일부임을 의미 하기도 합니다.
>
>

일부 Microsoft 문서에서는 특히 DBMS 고가용성 구성에 대해 크로스-프레미스 시나리오를 약간 다르게 설명 합니다. SAP 관련 문서의 경우 크로스-프레미스 시나리오는 사이트 간 또는 개인 [express](https://azure.microsoft.com/services/expressroute/) 경로 연결로 구축 온-프레미스와 Azure 간에 배포 되는 SAP 환경에 연결 됩니다.

## <a name="resources"></a>리소스
Azure에서 SAP 워크 로드에 사용할 수 있는 다른 문서가 있습니다. [Azure에서 SAP 워크 로드 시작: 시작](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 을 클릭 한 후 관심 영역을 선택 합니다.

다음 SAP 참고 사항은이 문서에 설명 된 영역과 관련 하 여 Azure의 SAP와 관련 되어 있습니다.

| Note 번호 | 제목 |
| --- | --- |
| [1928533] |Azure의 SAP 응용 프로그램: 지원 되는 제품 및 Azure VM 유형 |
| [2015553] |Microsoft Azure의 SAP: 지원 필수 구성 요소 |
| [1999351] |SAP용 고급 Azure 모니터링 문제 해결 |
| [2178632] |Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [1409604] |Windows의 가상화: 향상 된 모니터링 |
| [2191498] |Azure를 사용 하는 Linux의 SAP: 향상 된 모니터링 |
| [2039619] |Oracle 데이터베이스를 사용 하는 Microsoft Azure의 SAP 응용 프로그램: 지원 되는 제품 및 버전 |
| [2233094] |DB6: Linux, UNIX 및 Windows 용 IBM DB2를 사용 하는 Azure의 SAP 응용 프로그램: 추가 정보 |
| [2243692] |Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제 |
| [1984787] |SUSE LINUX Enterprise Server 12: 설치 참고 |
| [2002167] |Red Hat Enterprise Linux 8.x: 설치 및 업그레이드 |
| [2069760] |Oracle Linux 7.x SAP 설치 및 업그레이드 |
| [1597355] |Linux에 대한 스왑 공간 권장 사항 |
| [2171857] |Oracle Database 12c: Linux에서 파일 시스템 지원 |
| [1114181] |Oracle Database 11g: Linux에서 파일 시스템 지원 |


Linux에 대 한 모든 SAP 정보에 대 한 자세한 내용은 [sap community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)를 참조 하세요.

Microsoft Azure 아키텍처에 대 한 작업 지식이 필요 하 고, 가상 컴퓨터를 배포 하 고 작동 하는 방법을 Microsoft Azure. 자세한 내용은 [Azure 설명서](https://docs.microsoft.com/azure/)를 참조 하세요.

일반적으로 Windows, Linux 및 DBMS 설치와 구성은 기본적으로 온-프레미스에 설치 하는 가상 컴퓨터 또는 운영 체제 미 설치 컴퓨터와 동일 합니다. Azure IaaS를 사용 하는 경우 몇 가지 아키텍처와 시스템 관리 구현 결정이 있습니다. 이 문서에서는 Azure IaaS를 사용할 때 준비할 특정 아키텍처 및 시스템 관리 차이점에 대해 설명 합니다.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS 배포를 위한 VM의 저장소 구조
이 챕터를 수행 하려면 [배포 가이드][deployment-guide]의 [이 챕터][deployment-guide-3] 에 제공 된 정보를 읽고 이해 하세요. 이 챕터를 읽기 전에 다양 한 VM 시리즈와 standard 및 premium storage 간의 차이점을 이해 하 고 파악 해야 합니다. 

Azure Vm의 Azure Storage에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Windows vm 용 managed Disks 소개](../../windows/managed-disks-overview.md).
- [Azure Linux vm 용 managed Disks 소개](../../linux/managed-disks-overview.md).

기본 구성에서는 일반적으로 운영 체제, DBMS 및 최종 SAP 이진 파일이 데이터베이스 파일과 구분 되는 배포 구조를 권장 합니다. Azure 가상 머신에서 실행 되는 SAP 시스템에는 운영 체제, 데이터베이스 관리 시스템 실행 파일 및 SAP 실행 파일과 함께 기본 VHD 또는 디스크가 설치 되어 있는 것이 좋습니다. 

DBMS 데이터 및 로그 파일은 standard storage 또는 premium storage에 저장 됩니다. 이러한 이미지는 별도의 디스크에 저장 되며 원래 Azure 운영 체제 이미지 VM에 논리 디스크로 연결 됩니다. Linux 배포의 경우 특히 SAP HANA에 대해 다양 한 권장 사항이 문서화 되어 있습니다.

디스크 레이아웃을 계획할 때 다음 항목 간의 최적 잔액을 찾습니다.

* 데이터 파일 수
* 파일에 포함된 디스크 수
* 단일 디스크당 IOPS 할당량
* 디스크당 데이터 처리량
* VM 크기당 가능한 추가 데이터 디스크 수
* VM에서 제공할 수 있는 전체 스토리지 처리량
* 다양한 Azure Storage 유형에서 제공할 수 있는 대기 시간
* VM Sla.

Azure는 데이터 디스크 IOPS 할당량을 적용합니다. 이러한 할당량은 standard storage 및 premium storage에서 호스트 되는 디스크에 대해 다릅니다. 또한 I/O 대기 시간도 두 스토리지 유형 간에 다릅니다. Premium storage는 더 나은 i/o 대기 시간을 제공 합니다. 

서로 다른 각 VM 유형에는 연결할 수 있는 데이터 디스크의 수가 제한 되어 있습니다. 또 다른 제한은 특정 VM 유형만 premium storage를 사용할 수 있다는 것입니다. 일반적으로 CPU 및 메모리 요구 사항을 기반으로 특정 VM 유형을 사용 하기로 결정 합니다. 또한 일반적으로 디스크 수 또는 프리미엄 저장소 디스크의 종류를 사용 하 여 크기를 조정 하는 IOPS, 대기 시간 및 디스크 처리량 요구 사항을 고려할 수 있습니다. 각 디스크에서 수행할 IOPS 및 처리량은 디스크 크기를 지시할 수 있습니다 (특히 premium storage의 경우).

> [!NOTE]
> DBMS 배포의 경우 모든 데이터, 트랜잭션 로그 또는 다시 실행 파일에 premium storage를 사용 하는 것이 좋습니다. 프로덕션 또는 비프로덕션 시스템을 배포할지 여부는 중요 하지 않습니다.

> [!NOTE]
> Azure의 고유한 [단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)를 활용 하려면 연결 된 모든 디스크가 기본 VHD를 포함 하는 premium storage 유형 이어야 합니다.

> [!NOTE]
> Azure 데이터 센터에 인접 한 공동 배치 된 타사 데이터 센터에 있는 저장소 하드웨어의 SAP 데이터베이스에 대 한 데이터 및 로그 파일과 같은 주 데이터베이스 파일을 호스트 하는 것은 지원 되지 않습니다. SAP 워크 로드의 경우 기본 Azure 서비스로 표시 되는 저장소만 SAP 데이터베이스의 데이터 및 트랜잭션 로그 파일에 대해 지원 됩니다.

데이터베이스 파일 및 로그 및 다시 실행 파일의 위치와 사용 하는 Azure Storage 형식은 IOPS, 대기 시간 및 처리량 요구 사항에 따라 정의 됩니다. 충분 한 IOPS를 사용 하려면 여러 디스크를 사용 하거나 더 큰 premium storage 디스크를 사용 해야 할 수 있습니다. 여러 디스크를 사용 하는 경우 데이터 파일이 나 로그 및 다시 실행 파일을 포함 하는 디스크에서 소프트웨어 스트라이프를 작성 합니다. 이러한 경우 기본 premium storage 디스크의 IOPS 및 디스크 처리량 Sla 또는 표준 저장소 디스크의 달성 가능한 최대 IOPS는 결과 스트라이프 세트에 대해 누적 됩니다.

이미 언급 했 듯이 IOPS 요구 사항이 단일 VHD가 제공할 수 있는 작업을 초과 하는 경우 여러 Vhd에 걸쳐 데이터베이스 파일에 필요한 IOPS 수의 균형을 유지 합니다. 디스크에 IOPS 부하를 분산 하는 가장 쉬운 방법은 다른 디스크에 대해 소프트웨어 스트라이프를 빌드하는 것입니다. 그런 다음 SAP DBMS의 여러 데이터 파일을 소프트웨어 스트라이프 외부의 Lun 공백을 만들에 저장 합니다. 스트라이프의 디스크 수는 IOPs 요구, 디스크 처리량 요구 및 볼륨 요구 사항에 따라 결정 됩니다.


---
> ![Windows][Logo_Windows] Windows
>
> Windows 저장소 공간을 사용 하 여 여러 Azure Vhd에 스트라이프 세트를 만드는 것이 좋습니다. 최소 Windows Server 2012 R2 또는 Windows Server 2016를 사용 합니다.
>
> ![Linux][Logo_Linux] Linux
>
> Linux에서 소프트웨어 RAID를 빌드하는 데 MDADM 및 LVM (논리 볼륨 관리자)만 지원 됩니다. 자세한 내용은 다음을 참조하세요.
>
> - MDADM을 사용 하 여 [Linux에서 소프트웨어 RAID 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
> - [Azure에서 Linux VM에 LVM 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)(LVM 사용)
>
>

---

> [!NOTE]
> Azure Storage는 Vhd의 이미지 3 개를 유지 하므로 스트라이프 할 때 중복성을 구성 하는 것은 적합 하지 않습니다. I/o가 여러 Vhd에 분산 되도록 스트라이프를 구성 하기만 하면 됩니다.
>

### <a name="managed-or-nonmanaged-disks"></a>관리 디스크 또는 관리 되지 않는 디스크
Azure storage 계정은 관리 구문 및 제한 사항에 따라 달라 집니다. 표준 저장소 계정 및 프리미엄 저장소 계정 간에는 제한이 다릅니다. 기능 및 제한 사항에 대 한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)를 참조 하세요.

Standard storage의 경우 저장소 계정 당 IOPS에 제한이 있다는 점에 주의 하세요. [확장성 및 성능 목표 Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)문서에서 **총 요청 비율** 을 포함 하는 행을 참조 하세요. Azure 구독 당 저장소 계정 수에 대 한 초기 제한도 있습니다. 이러한 저장소 계정에 대 한 제한에 도달 하지 않도록 여러 저장소 계정에서 더 큰 SAP 환경에 대해 Vhd의 균형을 유지 합니다. 이는 수천 개 이상의 Vhd를 포함 하는 수백 개의 가상 머신을 이야기 하는 경우 지루한 작업입니다.

SAP 워크 로드와 함께 DBMS 배포에 standard storage를 사용 하지 않는 것이 좋습니다. 표준 저장소에 대 한 참조와 권장 사항은이 짧은 [문서로](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx) 제한 됩니다.

여러 Azure storage 계정에서 Vhd를 계획 하 고 배포 하는 관리 작업을 방지 하기 위해 Microsoft는 2017에 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 를 도입 했습니다. 관리 디스크는 standard storage 및 premium storage에 사용할 수 있습니다. 관리 되지 않는 디스크와 비교할 때 관리 디스크의 주요 장점은 다음과 같습니다.

- 관리 디스크의 경우 Azure는 배포 시 여러 저장소 계정에 다른 Vhd를 자동으로 배포 합니다. 이러한 방식으로 데이터 볼륨, i/o 처리량 및 IOPS에 대 한 저장소 계정 한도가 적중 되지 않습니다.
- Azure Storage 관리 디스크를 사용 하 여 Azure 가용성 집합의 개념을 준수 합니다. VM이 Azure 가용성 집합의 일부인 경우 VM의 기본 VHD 및 연결 된 디스크는 서로 다른 장애 도메인 및 업데이트 도메인에 배포 됩니다.


> [!IMPORTANT]
> Azure Managed Disks의 이점을 고려 하 여 일반적으로 DBMS 배포 및 SAP 배포에 Azure Managed Disks를 사용 하는 것이 좋습니다.
>

관리 되지 않는 디스크에서 managed disks로 변환 하려면 다음을 참조 하세요.

- [Windows 가상 머신을 관리 되지 않는 디스크에서 managed disks로 변환](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)합니다.
- [Linux 가상 머신을 비관리 디스크에서 managed disks로 변환](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)합니다.


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 및 데이터 디스크에 대한 캐싱
디스크를 Vm에 탑재할 때 VM과 Azure storage에 있는 해당 디스크 간의 i/o 트래픽이 캐시 되는지 여부를 선택할 수 있습니다. Standard 및 premium storage에서는이 유형의 캐시에 대해 두 가지 기술을 사용 합니다.

다음 권장 사항은 표준 DBMS에 대 한 이러한 i/o 특성을 가정 합니다.

- 데이터베이스의 데이터 파일에 대 한 읽기 작업은 대부분입니다. 이러한 읽기는 DBMS 시스템에서 중요 한 성능입니다.
- 데이터 파일에 대 한 쓰기는 검사점 또는 상수 스트림에 따라 버스트에서 발생 합니다. 하루 평균을 초과 하는 쓰기는 읽기 보다 줄어듭니다. 데이터 파일 읽기와 반대 되는 이러한 쓰기는 비동기적 이며 사용자 트랜잭션을 보유 하지 않습니다.
- 트랜잭션 로그 또는 다시 실행 파일에 대한 읽기는 거의 없습니다. 예외는 트랜잭션 로그 백업을 수행 하는 경우 대량 i/o입니다.
- 트랜잭션 또는 다시 실행 로그 파일에 대 한 주 로드는 쓰기입니다. 워크 로드의 특성에 따라 i/o 크기가 1mb 이상인 i/o 크기를 사용할 수 있습니다 (예를 들어).
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에 유지 되어야 합니다.

Standard storage의 경우 가능한 캐시 유형은 다음과 같습니다.

* 없음
* 읽기
* 읽기/쓰기

일관성 있고 결정적 성능을 얻으려면 DBMS 관련 데이터 파일, 로그 및 다시 실행 파일 및 테이블 공간을 포함 하는 모든 디스크의 표준 저장소에 대 한 캐싱을 **NONE**으로 설정 합니다. 기본 VHD의 캐싱은 기본값으로 유지할 수 있습니다.

Premium storage의 경우 다음과 같은 캐싱 옵션이 있습니다.

* 없음
* 읽기
* 읽기/쓰기
* 없음 + 쓰기 가속기 (Azure M 시리즈 Vm에만 해당)
* 읽기 + 쓰기 가속기 (Azure M 시리즈 Vm에만 해당)

Premium storage의 경우 SAP 데이터베이스의 **데이터 파일에 대해 읽기 캐싱을** 사용 하 고 **로그 파일의 디스크에 대해 캐싱 안 함**을 선택 하는 것이 좋습니다.

M 시리즈 배포의 경우 DBMS 배포에 Azure 쓰기 가속기를 사용 하는 것이 좋습니다. Azure 쓰기 가속기에 대 한 자세한 내용, 제한 사항 및 배포는 [쓰기 가속기 사용](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)을 참조 하세요.


### <a name="azure-nonpersistent-disks"></a>Azure 비영구 디스크
Azure Vm은 VM이 배포 된 후 비영구 디스크를 제공 합니다. VM을 다시 부팅 하는 경우 해당 드라이브의 모든 콘텐츠가 지워집니다. 데이터베이스의 데이터 파일 및 로그 및 다시 실행 파일은 해당 비지속형 드라이브에 있는 상황에 있지 않아야 합니다. 일부 데이터베이스에는 이러한 비지속형 드라이브가 tempdb 및 임시 테이블 스페이스에 적합할 수 있는 예외가 있을 수 있습니다. 이러한 비지속형 드라이브는 해당 VM 제품군에 대 한 처리량으로 제한 되기 때문에 A 시리즈 Vm에 이러한 드라이브를 사용 하지 않도록 합니다. 

자세한 내용은 [Azure에서 Windows vm의 임시 드라이브 이해](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)를 참조 하세요.

---
> ![Windows][Logo_Windows] Windows
>
> Azure VM의 D 드라이브는 Azure 계산 노드의 일부 로컬 디스크에서 지원 되는 비지속형 드라이브입니다. 비지속형 이기 때문에 D 드라이브의 콘텐츠에 대 한 변경 내용은 VM을 다시 부팅 하면 손실 됩니다. 변경 내용에는 저장 된 파일, 생성 된 디렉터리, 설치 된 응용 프로그램이 포함 됩니다.
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure Vm은 Azure 계산 노드의 로컬 디스크에 의해 지원 되는 비지속형 드라이브인/mnt/resource에 드라이브를 자동으로 탑재 합니다. 비지속형 이기 때문에/mnt/resource의 콘텐츠에 대 한 변경 내용은 VM이 다시 부팅 될 때 손실 됩니다. 변경 내용에는 저장 된 파일, 생성 된 디렉터리, 설치 된 응용 프로그램이 포함 됩니다.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage 복원력
Microsoft Azure Storage은 OS 및 연결 된 디스크 또는 blob이 있는 기본 VHD를 3 개 이상의 개별 저장소 노드에 저장 합니다. 이 저장소 유형을 LRS (로컬 중복 저장소) 라고 합니다. LRS는 Azure의 모든 저장소 형식에 대 한 기본값입니다.

다른 중복성 방법이 있습니다. 자세한 내용은 [Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 참조 하세요.

> [!NOTE]
>Premium storage는 데이터베이스와 로그 및 다시 실행 파일을 저장 하는 DBMS Vm 및 디스크에 권장 되는 저장소 유형입니다. Premium storage에 사용할 수 있는 중복성 방법은 LRS 뿐입니다. 따라서 데이터베이스 데이터 복제를 다른 Azure 지역 또는 가용성 영역으로 설정 하도록 데이터베이스 메서드를 구성 해야 합니다. 데이터베이스 메서드에는 SQL Server Always On, Oracle Data Guard 및 HANA 시스템 복제가 포함 됩니다.


> [!NOTE]
> DBMS 배포의 경우 표준 저장소에 GRS (지역 중복 저장소)를 사용 하지 않는 것이 좋습니다. GRS는 성능에 심각한 영향을 주며 VM에 연결 된 여러 Vhd에 대 한 쓰기 순서를 고려 하지 않습니다. 여러 Vhd에서 쓰기 순서를 설정 하지 않으면 복제 대상 측의 데이터베이스가 일관 되지 않을 수 있습니다. 이 상황은 데이터베이스와 로그 및 다시 실행 파일이 원본 VM 쪽에서 일반적으로와 같이 여러 Vhd에 걸쳐 분산 된 경우 발생 합니다.



## <a name="vm-node-resiliency"></a>VM 노드 복원력
Azure는 Vm에 대해 여러 가지 Sla를 제공 합니다. 자세한 내용은 [Virtual Machines에 대 한 최신 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)릴리스를 참조 하세요. DBMS 계층은 일반적으로 SAP 시스템의 가용성에 매우 중요 하므로 가용성 집합, 가용성 영역 및 유지 관리 이벤트를 이해 해야 합니다. 이러한 개념에 대 한 자세한 내용은 azure [에서 Windows 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) 및 [azure에서 Linux 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)를 참조 하세요.

SAP 워크 로드를 사용 하는 프로덕션 DBMS 시나리오에 대 한 최소 권장 사항은 다음과 같습니다.

- 동일한 Azure 지역에 있는 별도의 가용성 집합에 두 개의 Vm을 배포 합니다.
- 동일한 Azure 가상 네트워크에서 이러한 두 Vm을 실행 하 고 Nic가 동일한 서브넷에 연결 되어 있어야 합니다.
- 데이터베이스 메서드를 사용하여 두 번째 VM에 대한 상시 대기를 유지합니다. 메서드는 SQL Server Always On, Oracle Data Guard 또는 HANA System Replication일 수 있습니다.

다른 Azure 지역에 세 번째 VM을 배포 하 고 동일한 데이터베이스 메서드를 사용 하 여 다른 Azure 지역에 비동기 복제본을 제공할 수도 있습니다.

Azure 가용성 집합을 설정 하는 방법에 대 한 자세한 내용은 [이 자습서](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)를 참조 하세요.



## <a name="azure-network-considerations"></a>Azure 네트워크 고려 사항
대규모 SAP 배포에서는 [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)의 청사진을 사용 합니다. 조직의 다른 부분에 대 한 가상 네트워크 구성 및 사용 권한 및 역할 할당에 사용 합니다.

이러한 모범 사례는 수백 명의 고객 배포에 대 한 결과입니다.

- SAP 응용 프로그램이 배포 되는 가상 네트워크는 인터넷에 액세스할 수 없습니다.
- 데이터베이스 Vm은 응용 프로그램 계층과 동일한 가상 네트워크에서 실행 됩니다.
- 가상 네트워크 내의 Vm에는 개인 IP 주소의 정적 할당이 있습니다. 자세한 내용은 [Azure의 IP 주소 유형 및 할당 방법](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)을 참조 하세요.
- DBMS Vm에 대 한 라우팅 제한은 로컬 DBMS Vm에 설치 된 방화벽으로 설정 *되지 않습니다* . 대신, 트래픽 라우팅은 [NSGs (네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/security-overview)로 정의 됩니다.
- 트래픽을 분리 하 고 DBMS VM으로 격리 하려면 다른 Nic를 VM에 할당 합니다. 모든 NIC는 서로 다른 IP 주소를 가져오며, 모든 NIC는 다른 가상 네트워크 서브넷에 할당 됩니다. 모든 서브넷의 NSG 규칙은 서로 다릅니다. 네트워크 트래픽 격리 또는 분리는 라우팅에 대 한 측정값입니다. 네트워크 처리량에 대 한 할당량을 설정 하는 데 사용 되지 않습니다.

> [!NOTE]
> Azure를 통해 고정 IP 주소를 할당 하는 것은 개별 가상 Nic에 할당 하는 것입니다. 게스트 OS 내에서 가상 NIC에 고정 IP 주소를 할당 하지 마세요. Azure Backup와 같은 일부 Azure 서비스는 하나 이상의 기본 가상 NIC가 고정 IP 주소가 아닌 DHCP로 설정 되어 있다는 사실을 사용 합니다. 자세한 내용은 [Azure 가상 컴퓨터 백업 문제 해결](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)을 참조 하세요. VM에 여러 개의 고정 IP 주소를 할당 하려면 여러 가상 Nic를 VM에 할당 합니다.
>


> [!IMPORTANT]
> Sap 응용 프로그램과 SAP NetWeaver-, Hybris 또는 S/4HANA 기반 SAP 시스템의 DBMS 계층 간 통신 경로에서 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 를 구성 하는 것은 지원 되지 않습니다. 이러한 제한 사항은 기능 및 성능상의 이유로입니다. SAP 응용 프로그램 계층과 DBMS 계층 간의 통신 경로는 직접 1 이어야 합니다. 이러한 ASG 및 NSG 규칙에서 직접 통신 경로를 허용 하는 경우에는이 제한에 [ASG (응용 프로그램 보안 그룹) 및 nsg 규칙이](https://docs.microsoft.com/azure/virtual-network/security-overview) 포함 되지 않습니다. 
>
> 네트워크 가상 어플라이언스를 지원 하지 않는 다른 시나리오는 다음과 같습니다.
>
> * [Sap 응용 프로그램 SUSE Linux Enterprise Server에서 Azure vm의 Sap NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)에 설명 된 대로 Linux Pacemaker 클러스터 노드 및 SBD 장치를 나타내는 Azure vm 간의 통신 경로입니다.
> * Azure [에서 파일 공유를 사용 하 여 windows 장애 조치 (failover) 클러스터에서 SAP ASCS/SCS 인스턴스 클러스터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)에 설명 된 대로 azure VM과 SOFS (windows Server 스케일 아웃 파일 서버) 간의 통신 경로를 설정 합니다. 
>
> 통신 경로의 네트워크 가상 어플라이언스는 두 통신 파트너 간의 네트워크 대기 시간을 쉽게 두 배로 만들 수 있습니다. 또한 SAP 응용 프로그램 계층과 DBMS 계층 간의 중요 한 경로에서 처리량을 제한할 수 있습니다. 일부 고객 시나리오에서 네트워크 가상 어플라이언스로 인해 Pacemaker Linux 클러스터가 실패할 수 있습니다. Linux Pacemaker 클러스터 노드 간 통신이 네트워크 가상 어플라이언스를 통해 해당 SBD 장치와 통신 하는 경우가 여기에 해당 합니다.
>

> [!IMPORTANT]
> 지원 *되지* 않는 또 다른 디자인은 SAP 응용 프로그램 계층 및 DBMS 계층이 서로 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 되지 않는 다른 Azure virtual network로 분리는 것입니다. 다른 Azure 가상 네트워크를 사용 하는 대신 Azure 가상 네트워크 내에서 서브넷을 사용 하 여 SAP 응용 프로그램 계층 및 DBMS 계층을 분리 하는 것이 좋습니다. 
>
> 권장 사항을 따르지 않고 두 계층을 다른 가상 네트워크로 분리 하는 경우 두 가상 네트워크를 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)해야 합니다. 
>
> 두 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure virtual network 간의 네트워크 트래픽은 전송 비용이 부과 됩니다. 수많은 tb로 구성 된 대용량 데이터 볼륨은 SAP 응용 프로그램 계층과 DBMS 계층 간에 교환 됩니다. SAP 응용 프로그램 계층 및 DBMS 계층이 두 피어 링 Azure virtual network 간에 분리 된 경우 상당한 비용을 누적 시킬 수 있습니다.

Azure 가용성 집합 내에서 프로덕션 DBMS 배포에 대해 두 개의 Vm을 사용 합니다. 또한 SAP 응용 프로그램 계층에 대해 별도의 라우팅을 사용 하 고 두 DBMS Vm에 대 한 관리 및 작업 트래픽을 사용 합니다. 다음 이미지를 참조하세요.

![두 서브넷에 있는 두 VM에 대한 다이어그램](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Azure Load Balancer를 사용 하 여 트래픽 리디렉션
SQL Server Always On 또는 HANA 시스템 복제와 같은 기능에 사용 되는 개인 가상 IP 주소를 사용 하려면 Azure 부하 분산 장치를 구성 해야 합니다. 부하 분산 장치는 프로브 포트를 사용 하 여 활성 DBMS 노드를 확인 하 고 해당 활성 데이터베이스 노드로만 트래픽을 라우팅합니다. 

데이터베이스 노드에 대 한 장애 조치 (failover)가 있는 경우 SAP 응용 프로그램을 다시 구성할 필요가 없습니다. 대신 가장 일반적인 SAP 응용 프로그램 아키텍처는 개인 가상 IP 주소에 대해 다시 연결 됩니다. 한편, 부하 분산 장치는 개인 가상 IP 주소에 대 한 트래픽을 두 번째 노드로 리디렉션하여 노드 장애 조치 (failover)에 반응 합니다.

Azure는 기본 SKU와 표준 SKU 라는 두 가지 [부하 분산 장치 sku](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)를 제공 합니다. Azure 가용성 영역을 통해 배포 하려는 경우가 아니면 기본 부하 분산 장치 SKU는 정상적으로 작동 합니다.

DBMS Vm과 SAP 응용 프로그램 계층 간의 트래픽이 항상 부하 분산 장치를 통해 라우트 됩니까? 대답은 부하 분산 장치를 구성하는 방법에 따라 다릅니다. 

이 시점에서 DBMS VM에 들어오는 트래픽은 항상 부하 분산 장치를 통해 라우팅됩니다. DBMS VM에서 응용 프로그램 계층 VM으로 나가는 트래픽 경로는 부하 분산 장치의 구성에 따라 달라 집니다. 

부하 분산 장치는 DirectServerReturn 옵션을 제공합니다. 이 옵션을 구성 하는 경우 DBMS VM에서 SAP 응용 프로그램 계층으로 전송 되는 트래픽은 부하 분산 장치를 통해 라우팅되지 *않습니다* . 대신 응용 프로그램 계층으로 직접 이동 합니다. DirectServerReturn이 구성 되지 않은 경우 SAP 응용 프로그램 계층에 대 한 반환 트래픽은 부하 분산 장치를 통해 라우팅됩니다.

SAP 응용 프로그램 계층과 DBMS 계층 사이에 배치 되는 부하 분산 장치와 함께 DirectServerReturn을 구성 하는 것이 좋습니다. 이 구성은 두 계층 간의 네트워크 대기 시간을 줄입니다.

SQL Server Always On를 사용 하 여이 구성을 설정 하는 방법에 대 한 예제는 [Azure에서 Always On 가용성 그룹에 대 한 ILB 수신기 구성](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)을 참조 하세요.

Azure에서 SAP 인프라 배포에 대 한 참조로 게시 된 GitHub JSON 템플릿을 사용 하는 경우 [sap 3 계층 시스템용](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)으로이 템플릿을 연구 하세요. 이 템플릿에서 부하 분산 장치에 대 한 올바른 설정을 볼 수도 있습니다.

### <a name="azure-accelerated-networking"></a>Azure 가속 네트워킹
Azure Vm 간의 네트워크 대기 시간을 줄이려면 [Azure 가속화 된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 선택 하는 것이 좋습니다. Sap 워크 로드, 특히 SAP 응용 프로그램 계층 및 SAP DBMS 계층에 대해 Azure Vm을 배포할 때이 도구를 사용 합니다.

> [!NOTE]
> 모든 VM 유형이 가속화 네트워킹을 지원 하지는 않습니다. 이전 문서에서는 가속화 된 네트워킹을 지 원하는 VM 유형을 나열 합니다.
>

---
> ![Windows][Logo_Windows] Windows
>
> Windows 용 가속화 된 네트워킹을 사용 하 여 Vm을 배포 하는 방법을 알아보려면 [가속화 된 네트워킹을 사용 하 여 windows 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)를 참조 하세요.
>
> ![Linux][Logo_Linux] Linux
>
> Linux 배포에 대 한 자세한 내용은 [가속화 된 네트워킹을 사용 하 여 linux 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)를 참조 하세요.
>
>

---

> [!NOTE]
> SUSE, Red Hat 및 Oracle Linux의 경우 가속 네트워킹은 최신 릴리스에서 지원됩니다. SLES 12 SP2 또는 RHEL 7.2와 같은 이전 릴리스는 Azure 가속화 된 네트워킹을 지원 하지 않습니다.
>


## <a name="deployment-of-host-monitoring"></a>호스트 모니터링 배포
Azure virtual machines에서 SAP 응용 프로그램의 프로덕션 사용을 위해 SAP에는 Azure virtual machines를 실행 하는 실제 호스트에서 호스트 모니터링 데이터를 가져오는 기능이 필요 합니다. SAPOSCOL 및 SAP HostAgent에서 이 기능을 사용하려면 특정 SAP HostAgent 패치 수준이 필요합니다. 정확한 패치 수준은 SAP Note [1409604]에 설명되어 있습니다.

SAPOSCOL 및 SAP 호스트 에이전트에 호스트 데이터를 제공 하는 구성 요소의 배포 및 이러한 구성 요소의 수명 주기 관리에 대 한 자세한 내용은 [배포 가이드][deployment-guide]를 참조 하세요.


## <a name="next-steps"></a>다음 단계
특정 DBMS에 대 한 자세한 내용은 다음을 참조 하세요.

- [SAP 워크로드에 대한 SQL Server Azure Virtual Machines DBMS 배포](dbms_guide_sqlserver.md)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](dbms_guide_oracle.md)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](dbms_guide_ibm.md)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](dbms_guide_sapase.md)
- [Azure의 SAP maxDB, 라이브 캐시 및 콘텐츠 서버 배포](dbms_guide_maxdb.md)
- [Azure 운영 가이드의 SAP HANA](hana-vm-operations.md)
- [Azure Virtual Machines의 SAP HANA 고가용성](sap-hana-availability-overview.md)
- [Azure virtual machines의 SAP HANA에 대 한 백업 가이드](sap-hana-backup-guide.md)

