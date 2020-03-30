---
title: SAP 워크로드 계획 및 배포 검사 목록 | Microsoft Docs
description: Azure에 SAP 워크로드 배포 를 계획하고 워크로드를 배포하기 위한 검사 목록
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
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060057"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure의 SAP 워크로드: 계획 및 배포 검사 목록

이 검사 목록은 SAP NetWeaver, S/4HANA 및 Hybris 응용 프로그램을 Azure 인프라로 서비스로 이동하는 고객을 위해 설계되었습니다. 프로젝트 기간 동안 고객 및/또는 SAP 파트너는 검사 목록을 검토해야 합니다. 많은 검사는 프로젝트 시작 및 계획 단계에서 완료됩니다. 배포가 완료되면 배포된 Azure 인프라 또는 SAP 소프트웨어 릴리스에 대한 간단한 변경이 복잡해질 수 있습니다.

프로젝트 중 주요 이정표에서 검사 목록을 검토합니다. 이렇게 하면 큰 문제가 발생하기 전에 작은 문제를 감지할 수 있습니다. 또한 필요한 변경 사항을 다시 설계하고 테스트할 수 있는 충분한 시간을 가질 수 있습니다. 이 검사 목록이 완료된 것으로 간주하지 마십시오. 상황에 따라 더 많은 검사를 수행해야 할 수 있습니다.

검사 목록에는 Azure와 독립적인 작업이 포함되지 않습니다. 예를 들어 Azure 플랫폼또는 호스팅 공급자로 이동하는 동안 SAP 응용 프로그램 인터페이스가 변경됩니다.

이 검사 목록은 이미 배포된 시스템에도 사용할 수 있습니다. 액셀러레이터 쓰기 및 가용성 영역과 같은 새로운 기능및 배포한 이후 새 VM 유형이 추가되었을 수 있습니다. 따라서 Azure 플랫폼의 새로운 기능을 인식할 수 있도록 검사 목록을 주기적으로 검토하는 것이 유용합니다.

## <a name="project-preparation-and-planning-phase"></a>프로젝트 준비 및 계획 단계
이 단계에서는 SAP 워크로드를 Azure 플랫폼으로 마이그레이션할 계획입니다. 최소한 이 단계에서는 다음 문서를 만들고 마이그레이션의 다음 요소를 정의하고 설명해야 합니다.

1. 고급 설계 문서. 이 문서에는 다음이 포함되어야 합니다.
    - SAP 구성 요소 및 응용 프로그램의 현재 인벤토리와 Azure의 대상 응용 프로그램 인벤토리입니다.
    - 관련 당사자의 책임과 할당을 정의하는 책임 할당 매트릭스(RACI)입니다. 높은 수준에서 시작하여 계획 및 첫 번째 배포 전반에 걸쳐 보다 세분화된 수준으로 작업합니다.
    - 고급 솔루션 아키텍처입니다.
    - 배포할 Azure 지역에 대한 결정입니다. Azure [지역 목록을](https://azure.microsoft.com/global-infrastructure/regions/)참조하십시오. 각 리전에서 사용할 수 있는 서비스에 대해 알아보려면 [지역별로 사용할 수 있는 제품을](https://azure.microsoft.com/global-infrastructure/services/)참조하십시오.
    - 온-프레미스에서 Azure로 연결하는 네트워킹 아키텍처입니다. [Azure에 대한 가상 데이터 센터 Blueprint에](https://docs.microsoft.com/azure/architecture/vdc/)익숙해지기 시작합니다.
    - Azure에서 영향이 큰 비즈니스 데이터를 실행하기 위한 보안 원칙입니다. 데이터 보안에 대해 자세히 알아보려면 [Azure 보안 설명서를](https://docs.microsoft.com/azure/security/)시작합니다.
2.  기술 설계 문서. 이 문서에는 다음이 포함되어야 합니다.
    - 솔루션에 대한 블록 다이어그램입니다.
    - Azure에서 계산, 저장소 및 네트워킹 구성 요소의 크기 입니다. Azure VM의 SAP 크기 조정에 대 한 [SAP 지원 #1928533](https://launchpad.support.sap.com/#/notes/1928533)을 참조 하십시오.
    - 비즈니스 연속성 및 재해 복구 아키텍처.
    - OS, DB, 커널 및 SAP 지원 팩 버전에 대한 자세한 정보입니다. SAP NetWeaver 또는 S/4HANA에서 지원하는 모든 OS 릴리스가 Azure VM에서 지원되는 것은 아닙니다. DBMS 릴리스도 마찬가지입니다. 다음 소스를 확인하여 SAP 릴리스, DBMS 릴리스 및 OS 릴리스를 업그레이드하여 SAP 및 Azure 지원을 보장합니다. SAP 및 Microsoft에서 완전한 지원을 받으려면 SAP 및 Azure에서 지원하는 릴리스 조합이 있어야 합니다. 필요한 경우 일부 소프트웨어 구성 요소를 업그레이드할 계획을 세워야 합니다. 지원되는 SAP, OS 및 DBMS 소프트웨어에 대한 자세한 내용은 다음과 같습니다.
        - [SAP 지원 노트 #1928533](https://launchpad.support.sap.com/#/notes/1928533). 이 참고는 Azure VM에서 지원되는 최소 OS 릴리스를 정의합니다. 또한 대부분의 HANA 이외의 데이터베이스에 필요한 최소 데이터베이스 릴리스를 정의합니다. 마지막으로 SAP 지원 Azure VM 형식에 대 한 SAP 크기 조정을 제공 합니다.
        - [SAP 지원 노트 #2015553](https://launchpad.support.sap.com/#/notes/2015553). 이 참고는 Azure 저장소에 대한 지원 정책을 정의하고 Microsoft와 필요한 지원 관계를 정의합니다.
        - [SAP 지원 노트 #2039619](https://launchpad.support.sap.com/#/notes/2039619). 이 참고는 Azure에 대한 Oracle 지원 매트릭스를 정의합니다. 오라클은 SAP 워크로드용 Azure의 게스트 운영 체제로 Windows 및 Oracle Linux만 지원합니다. 이 지원 문은 SAP 인스턴스를 실행하는 SAP 응용 프로그램 계층에도 적용됩니다. 그러나 오라클은 페이스메이커를 통해 오라클 리눅스에서 SAP 중앙 서비스에 대한 고가용성을 지원하지 않습니다. 오라클 리눅스에서 ASCS에 대 한 높은 가용성을 필요로 하는 경우 리눅스에 대 한 SIOS 보호 제품군을 사용 해야 합니다. 자세한 SAP 인증 데이터는 SAP 지원 #1662610 [- Linux용 SIOS 보호 제품군에 대한 지원 세부 정보를](https://launchpad.support.sap.com/#/notes/1662610)참조하십시오. Windows의 경우 SAP 중앙 서비스에 대한 SAP 지원 Windows 서버 장애 조치 클러스터링 솔루션이 오라클과 함께 DBMS 계층으로 지원됩니다.
        - [SAP 지원 노트 #2235581](https://launchpad.support.sap.com/#/notes/2235581). 이 참고는 다른 OS 릴리스에서 SAP HANA에 대한 지원 매트릭스를 제공합니다.
        - SAP HANA 지원 Azure VM 및 [HANA 대형 인스턴스는](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) [SAP 웹 사이트에](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)나열됩니다.
        - [SAP 제품 가용성 매트릭스](https://support.sap.com/en/).
        - [SAP 지원 노트 #2555629 - SAP HANA 2.0 동적 계층화 – 하이퍼바이저 및 클라우드 지원](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP 지원 노트 #1662610 - 리눅스에 대한 SIOS 보호 제품군에 대한 지원 세부 사항](https://launchpad.support.sap.com/#/notes/1662610)
        - 다른 SAP 관련 제품에 대한 SAP 참고 사항입니다.     
    - SAP 프로덕션 시스템에 대해 엄격한 3계층 설계를 권장합니다. 하나의 VM에서 ASCS 및/또는 DBMS 및/또는 앱 서버를 결합하는 것은 권장되지 않습니다. SAP 중앙 서비스에 다중 SID 클러스터 구성을 사용하면 Azure의 Windows 게스트 운영 체제에서 지원됩니다. 그러나 이 구성은 Azure의 Linux 운영 체제에서 SAP 중앙 서비스에 대해 지원되지 않습니다. 다음 문서에서 Windows 게스트 OS 시나리오에 대한 설명서를 찾을 수 있습니다.
        - [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 공유 디스크를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - 고가용성 및 재해 복구 아키텍처.
        - RTO 및 RPO를 기반으로 고가용성 및 재해 복구 아키텍처의 모양을 정의합니다.
        - 영역 내에서 고가용성을 원하려면 Azure에서 원하는 DBMS가 제공하는 것을 확인하십시오. 대부분의 DBMS 패키지는 프로덕션 시스템에 권장되는 동기 식 핫 대기의 동기 식 메서드를 제공합니다. 또한 SAP 워크로드 및 관련 문서에 대한 [Azure 가상 시스템 DBMS 배포에 대한 고려 사항부터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) 시작하여 다양한 데이터베이스에 대한 SAP 관련 설명서를 확인합니다.
           예를 들어 [SQL Server에 대해 설명한](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)대로 DBMS 계층에 대한 공유 디스크 구성과 함께 Windows Server 장애 조치 클러스터링을 사용하는 것은 지원되지 않습니다. 대신 다음과 같은 솔루션을 사용합니다.
           - [SQL Server Always On](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle 데이터 가드](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [HANA 시스템 복제](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Azure 지역 전체에서 재해 복구를 위해 다른 DBMS 공급업체에서 제공하는 솔루션을 검토합니다. 대부분은 비동기 복제 또는 로그 전달을 지원합니다.
        - SAP 응용 프로그램 계층의 경우 프로덕션 배포의 복제본인 비즈니스 회귀 테스트 시스템을 동일한 Azure 리전 또는 DR 리전에서 실행할지 여부를 결정합니다. 두 번째 경우 프로덕션 배포에 대한 DR 대상으로 해당 비즈니스 회귀 시스템을 대상으로 지정할 수 있습니다.
        - DR 사이트에 비프로덕션 시스템을 배치하지 않기로 결정한 경우 SAP 응용 프로그램 계층을 Azure DR 영역으로 복제하는 방법으로 Azure 사이트 복구를 살펴봅니다. 자세한 내용은 [다중 계층 SAP NetWeaver 앱 배포에 대한 재해 복구 설정을](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)참조하십시오.
        - [Azure 가용성 영역을](https://docs.microsoft.com/azure/availability-zones/az-overview)사용하여 결합된 HADR 구성을 사용하기로 결정한 경우 가용성 영역을 사용할 수 있는 Azure 지역을 숙지하십시오. 또한 두 가용성 영역 간의 네트워크 대기 시간이 증가하여 도입될 수 있는 제한 사항도 고려합니다.  
3.  모든 SAP 인터페이스(SAP 및 비SAP)의 인벤토리입니다.
4.  기초 서비스의 디자인. 이 디자인에는 다음 항목이 포함되어야 합니다.
    - Active 디렉토리 및 DNS 디자인.
    - Azure 내의 네트워크 토폴로지 및 다른 SAP 시스템의 할당.
    - Azure에서 인프라 및 SAP 응용 프로그램을 관리하는 팀을 위한 [역할 기반 액세스](https://docs.microsoft.com/azure/role-based-access-control/overview) 구조입니다.
    - 리소스 그룹 토폴로지.
    - [태그 지정 전략](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    - VM 및 기타 인프라 구성 요소 및/또는 논리 이름에 대한 명명 규칙입니다.
5.  마이크로소프트 프리미어 지원 계약. TAM(Microsoft 기술 계정 관리자)을 식별합니다. SAP 지원 요구 사항은 [#2015553 SAP 지원 참고 사항을](https://launchpad.support.sap.com/#/notes/2015553)참조하십시오.
6.  구독에 대한 Azure 구독 및 코어 할당량 수입니다. 필요에 따라 [Azure 구독의 할당량을 늘리기 위한 지원 요청을 엽니다.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)
7.  SAP 데이터를 Azure로 마이그레이션하기 위한 데이터 감소 및 데이터 마이그레이션 계획입니다. SAP NetWeaver 시스템의 경우 SAP에는 대용량 데이터의 양을 제한하는 방법에 대한 지침이 있습니다. SAP ERP 시스템의 데이터 관리에 대한 [SAP 가이드를](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) 참조하십시오. 일부 콘텐츠는 넷위버 및 S/4HANA 시스템에도 적용됩니다.
8.  자동화된 배포 접근 방식입니다. Azure에서 인프라 배포 자동화의 목표는 결정적인 방식으로 배포하고 결정적인 결과를 얻는 것입니다. 많은 고객이 PowerShell 또는 CLI 기반 스크립트를 사용합니다. 그러나 SAP용 Azure 인프라를 배포하고 SAP 소프트웨어를 설치하는 데 사용할 수 있는 다양한 오픈 소스 기술이 있습니다. GitHub에서 예제를 찾을 수 있습니다.
    - [Azure Cloud의 자동화된 SAP 배포](https://github.com/Azure/sap-hana)
    - [SAP 하나 설치](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  고객, 시스템 통합자, Microsoft 및 기타 관련 당사자로 사용자 간의 정기적인 설계 및 배포 검토 흐름을 정의합니다.

 
## <a name="pilot-phase-strongly-recommended"></a>파일럿 단계(강력히 권장)
 
프로젝트 계획 및 준비 전이나 준비 중에 파일럿을 실행할 수 있습니다. 또한 파일럿 단계를 사용하여 계획 및 준비 단계에서 만들어진 접근 방식과 디자인을 테스트할 수도 있습니다. 또한 파일럿 단계를 확장하여 진정한 개념 증명을 만들 수 있습니다.

파일럿 배포 중에 전체 HADR 솔루션 및 보안 설계를 설정하고 유효성을 검사하는 것이 좋습니다. 일부 고객은 이 단계에서 확장성 테스트를 수행합니다. 다른 고객은 SAP 샌드박스 시스템의 배포를 파일럿 단계로 사용합니다. 파일럿을 위해 Azure로 마이그레이션하려는 시스템을 이미 식별했다고 가정합니다.

1. Azure로의 데이터 전송을 최적화합니다. 최적의 선택은 특정 시나리오에 따라 크게 달라집니다. ExpressRoute 회로에 충분한 대역폭이 있는 경우 온-프레미스에서 [Azure ExpressRoute를](https://azure.microsoft.com/services/expressroute/) 통한 전송이 가장 빠릅니다. 다른 시나리오에서는 인터넷을 통한 전송이 더 빠릅니다.
2. 데이터 내보내기 및 가져오기와 관련된 이기종 SAP 플랫폼 마이그레이션의 경우 내보내기 및 가져오기 단계를 테스트하고 최적화합니다. SQL Server가 대상 플랫폼인 대규모 마이그레이션의 경우 [권장 사항을](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)찾을 수 있습니다. 결합된 릴리스 업그레이드가 필요하지 않은 경우 마이그레이션 모니터/SWPM을 사용할 수 있습니다. 마이그레이션을 SAP 릴리스 업그레이드와 결합할 때 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 프로세스를 사용할 수 있습니다. 이렇게 하려면 소스 및 대상 DBMS 플랫폼 조합에 대한 특정 요구 사항을 충족해야 합니다. 이 프로세스는 [SUM 2.0 SP03의 DMO(데이터베이스 마이그레이션 옵션)에](https://launchpad.support.sap.com/#/notes/2631152)설명되어 있습니다.
   1.  원본으로 내보내고, Azure에 파일 업로드를 내보내고, 성능을 가져옵니다. 내보내기와 가져오기 간에 겹침을 최대화합니다.
   2.  인프라 크기 조정을 위해 대상 및 대상 플랫폼에서 데이터베이스 의 볼륨을 평가합니다.
   3.  타이밍을 검증하고 최적화합니다.
1. 기술 검증.
   1. VM 유형입니다.
        - SAP 지원 노트, SAP HANA 하드웨어 디렉토리 및 SAP PAM의 리소스를 다시 검토합니다. Azure용 으로 지원되는 VM, 해당 VM 유형에 대해 지원되는 OS 릴리스 및 지원되는 SAP 및 DBMS 릴리스에 대한 변경 사항이 없는지 확인합니다.
        - Azure에 배포하는 애플리케이션 및 인프라의 크기가 유효한지 다시 확인합니다. 기존 응용 프로그램을 이동하는 경우 사용하는 인프라와 [SAP 벤치마크 웹 페이지에서](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) 필요한 SAPS를 파생시키고 [#1928533 SAP 지원 노트에 나열된 SAPS](https://launchpad.support.sap.com/#/notes/1928533)번호와 비교할 수 있습니다. 또한 [이 문서는 SAPS 등급에 유의하십시오.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
        - 계획 단계에서 선택한 VM 유형의 최대 저장소 처리량 및 네트워크 처리량과 관련하여 Azure VM의 크기를 평가하고 테스트합니다. 여기에서 데이터를 찾을 수 있습니다.
           -  [Azure의 Windows 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 크기 조정을 위해 *캐시되지 않은 최대 디스크 처리량을* 고려하는 것이 중요합니다.
           -  [Azure의 Linux 가상 컴퓨터에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)입니다. 크기 조정을 위해 *캐시되지 않은 최대 디스크 처리량을* 고려하는 것이 중요합니다.
   2. 보관소.
        - 최소한 SAP 응용 프로그램 계층을 나타내는 VM에 [Azure 표준 SSD 저장소를](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) 사용하고 성능에 중대하지 않은 DBMS 배포에 사용합니다.
        - 일반적으로 [Azure 표준 HDD 디스크를](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)사용하지 않는 것이 좋습니다.
        - 원격으로 성능이 중요한 DBMS VM에 [Azure 프리미엄 저장소를](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) 사용합니다.
        - [Azure 관리 디스크를](https://azure.microsoft.com/services/managed-disks/)사용합니다.
        - M-Series가 있는 DBMS 로그 드라이브에 대해 Azure Write Accelerator를 사용합니다. [쓰기 액셀러레이터](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)에 설명된 대로 액셀러레이터 쓰기 제한 및 사용에 유의하십시오.
        - 다른 DBMS 유형에 대해서는 [일반 SAP 관련 DBMS 설명서와 일반](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) 문서에서 가리키는 DBMS 관련 문서를 확인합니다.
        - SAP HANA에 대한 자세한 내용은 [Azure에서 SAP HANA 인프라 구성 및 작업을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)참조하십시오.
        - 디바이스 ID를 사용하여 Azure 데이터 디스크를 Azure Linux VM에 탑재해서는 안됩니다. 대신 UUID(Universally Unique Identifier)를 사용합니다. 예를 들어 Azure 데이터 디스크를 탑재하기 위해 그래픽 도구를 사용하는 경우 주의해야 합니다. /etc/fstab의 항목을 다시 확인하여 UUID가 디스크를 마운트하는 데 사용되는지 확인합니다. 자세한 내용은 이 [문서에서](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)확인할 수 있습니다.
   3. 네트워킹
        - 가상 네트워크 인프라와 다른 Azure 가상 네트워크 내에서 또는 다른 Azure 가상 네트워크 내에서 SAP 응용 프로그램의 배포를 테스트하고 평가합니다.
        -  단일 Azure 가상 네트워크 내에서 허브 및 스포크 가상 네트워크 아키텍처 접근 방식 또는 마이크로 세분화 접근 방식을 평가합니다. 이 평가를 기반으로 합니다.
               1. [피어있는 Azure 가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)간의 데이터 교환 비용 . 비용에 대한 자세한 내용은 [가상 네트워크 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network/)을 참조하십시오.
               2. 가상 네트워크 내에서 서브넷을 격리하기 위해 네트워크 보안 그룹을 변경하는 것과 는 달리 Azure 가상 네트워크 간의 피어링이 빠르게 끊어지는 이점입니다. 이 평가는 가상 네트워크의 서브넷에서 호스팅되는 응용 프로그램 또는 VM이 보안 위험이 된 경우에 대한 것입니다.
                3. 온-프레미스, 외부 세계 및 Azure에서 빌드한 가상 데이터 센터 간의 네트워크 트래픽에 대한 중앙 로깅 및 감사
        - SAP 응용 프로그램 계층과 SAP DBMS 계층 간의 데이터 경로를 평가하고 테스트합니다.
            -  SAP NetWeaver, Hybris 또는 S/4HANA를 기반으로 하는 SAP 응용 프로그램과 SAP 시스템의 DBMS 계층 간의 통신 경로에 [Azure 네트워크 가상 어플라이언스의](https://azure.microsoft.com/solutions/network-appliances/) 배치는 지원되지 않습니다.
            -  피어링되지 않는 다른 Azure 가상 네트워크에 SAP 응용 프로그램 계층 및 SAP DBMS의 배치는 지원되지 않습니다.
            -  [응용 프로그램 보안 그룹 및 네트워크 보안 그룹 규칙을](https://docs.microsoft.com/azure/virtual-network/security-overview) 사용하여 SAP 응용 프로그램 계층과 SAP DBMS 계층 간의 경로를 정의할 수 있습니다.
        - SAP 응용 프로그램 계층 및 SAP DBMS 계층에 사용되는 VM에서 [Azure 가속 네트워킹이](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 활성화되어 있는지 확인합니다. Azure에서 가속화된 네트워킹을 지원하려면 다음과 같은 여러 OS 수준이 필요합니다.
            - Windows Server 2012 R2 이상
            - 수즈 리눅스 12 SP3 이상.
            - RHEL 7.4 이상.
            - Oracle Linux 7.5. RHCKL 커널을 사용하는 경우 릴리스 3.10.0-862.13.1.el7이 필요합니다. Oracle UEK 커널을 사용하는 경우 릴리스 5가 필요합니다.
        - [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)SAP 지원 노트에 따라 SAP 응용 프로그램 계층 VM과 DBMS VM 간의 네트워크 대기 시간을 테스트하고 평가합니다. [#1100926 SAP 지원 노트에서](https://launchpad.support.sap.com/#/notes/1100926/E)네트워크 대기 시간 지침에 대해 결과를 평가합니다. 네트워크 대기 시간은 보통 또는 양호한 범위에 있어야 합니다. 이 문서에 설명된 대로 VM과 HANA 대형 인스턴스 단위 간의 트래픽에는 [예외가](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)적용됩니다.
        - 직접 서버 반환을 사용하도록 ILB 배포가 설정되어 있는지 확인합니다. 이 설정은 Azure IlB가 DBMS 계층의 고가용성 구성에 사용되는 경우 대기 시간을 줄입니다.
        - Linux 게스트 운영 체제와 함께 Azure 로드 밸러워기를 사용하는 경우 Linux 네트워크 매개 변수 **net.ipv4.tcp_timestamps** **0으로**설정되어 있는지 확인합니다. 이 권장 사항은 이전 버전의 [SAP 노트 #2382421](https://launchpad.support.sap.com/#/notes/2382421)권장 사항과 충돌합니다. 이제 Azure 로드 밸로드워서에서 작동하려면 이 매개 변수를 **0으로** 설정해야 한다는 SAP 메모가 업데이트되었습니다.
        - 최적의 네트워크 대기 시간을 얻으려면 [Azure 근접 배치 그룹을](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) 사용하는 것이 좋습니다. 자세한 내용은 [SAP 응용 프로그램을 사용 하 여 최적의 네트워크 대기 시간에 대 한 Azure 근접 배치 그룹을](sap-proximity-placement-scenarios.md)참조 하십시오.
   4. 고가용성 및 재해 복구 배포.
        - 특정 Azure 가용성 영역을 정의하지 않고 SAP 응용 프로그램 계층을 배포하는 경우 단일 SAP 시스템의 SAP 대화 상자 인스턴스 또는 미들웨어 인스턴스를 실행하는 모든 VM이 [가용성 집합에](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)배포되어 있는지 확인합니다.
        - SAP 중앙 서비스 및 DBMS에 대한 고가용성이 필요하지 않은 경우 이러한 VM을 SAP 응용 프로그램 계층과 동일한 가용성 집합에 배포할 수 있습니다.
        - 수동 복제를 사용하여 고가용성을 위해 SAP 중앙 서비스 및 DBMS 계층을 보호하는 경우 SAP 중앙 서비스에 대한 두 노드를 하나의 별도 가용성 집합에 배치하고 두 개의 DBMS 노드를 다른 가용성 집합에 배치합니다.
        - Azure 가용성 영역에 배포하는 경우 가용성 집합을 사용할 수 없습니다. 그러나 활성 및 수동 중앙 서비스 노드를 두 개의 서로 다른 가용성 영역에 배포해야 합니다. 둘 사이의 대기 시간이 가장 낮은 가용성 영역을 사용합니다.
          가용성 영역 전반에 걸쳐 DBMS 및 SAP 중앙 서비스 계층에 대한 Windows 또는 Pacemaker 장애 조치 클러스터를 설정하는 사용 사례에 [Azure 표준 로드 밸런서를](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) 사용해야 합니다. 구역 [배포에는 기본 로드 밸러서를](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 사용할 수 없습니다.
   5. 시간 시간 설정입니다.
        - SAP NetWeaver 개발자 추적을 확인하여 큐 서버와 SAP 작업 프로세스 간에 연결 끊김이 없는지 확인합니다. 다음 두 레지스트리 매개 변수를 설정하여 이러한 연결 중단을 방지할 수 있습니다.
            - HKLM\SYSTEM\현재제어세트\서비스\Tcpip\매개 변수\KeepAliveTime = 120000. 자세한 내용은 [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))을 참조하십시오.
            - HKLM\SYSTEM\현재제어세트\서비스\Tcpip\매개변수\KeepAliveInterval = 120000. 자세한 내용은 [KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))을 참조하십시오.
        - 온-프레미스 SAP GUI 인터페이스와 Azure에 배포된 SAP 응용 프로그램 계층 간의 GUI 시간 시간을 방지하려면 이러한 매개 변수가 default.pfl 또는 인스턴스 프로필에 설정되어 있는지 확인합니다.
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - SAP 큐 프로세스와 SAP 작업 프로세스 간의 설정된 연결이 중단되는 것을 방지하려면 **enque/encni/set_so_keepalive** 매개 변수를 **true로**설정해야 합니다. 또한 [SAP 노트 #2743751](https://launchpad.support.sap.com/#/notes/2743751)참조하십시오.  
        - Windows 장애 조치 클러스터 구성을 사용하는 경우 응답하지 않는 노드에 반응하는 시간이 Azure에 대해 올바르게 설정되어 있는지 확인합니다. 장애 [조치 클러스터 네트워크 임계값 튜닝](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) 문서에는 매개 변수와 장애 조치 감수성에 미치는 영향을 나열합니다. 클러스터 노드가 동일한 서브넷에 있다고 가정하면 다음 매개 변수를 변경해야 합니다.
            - 동일서브넷딜레이 : 2000
            - SameSubNetThreshold = 15
            - 라우팅기록길이 = 30
    6. OS 설정 또는 패치
        - SAP에서 HANA를 실행하기 위해 다음 메모와 문서를 읽어보십시오.
            -   [SAP 지원 참고 #2814271 - SAP HANA 백업 체크섬 오류와 Azure에서 실패](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP 지원 노트 #2753418 - 타이머 대체로 인한 잠재적 성능 저하](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP 지원 참고 #2791572 - Azure에서 Hyper-V에 대한 VDSO 지원이 누락되어 성능 이저하](https://launchpad.support.sap.com/#/notes/2791572)
            -   [#2382421 SAP 지원 사항 - HANA 및 OS 수준에서 네트워크 구성 최적화](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP 지원 노트 #2694118 - 레드 햇 엔터프라이즈 리눅스 HA 추가 기능 Azure에](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP 지원 노트 #1984787 - SUSE 리눅스 엔터프라이즈 서버 12: 설치 노트](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP 지원 노트 #2002167 - 레드 햇 엔터프라이즈 리눅스 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP Support Note #2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/0002292690)(영문)
            -   [SAP 지원 노트 #2772999 - 레드 햇 엔터프라이즈 리눅스 8.x: 설치 및 구성](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP 지원 노트 #2777782 - SAP HANA DB: RHEL 8에 권장되는 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP 지원 노트 #2578899 - SUSE 리눅스 엔터프라이즈 서버 15: 설치 노트](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP 지원 노트 #2455582 - 리눅스 : GCC 6.x로 컴파일 된 SAP 응용 프로그램을 실행](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP 지원 노트 #2729475 - HWCCT 오류로 실패 "하이퍼바이저는 지원되지 않습니다" SAP HANA에 대한 인증 된 Azure VM에서](https://launchpad.support.sap.com/#/notes/2729475)
1. 고가용성 및 재해 복구 절차를 테스트합니다.
   1. VM(Windows 게스트 운영 체제)을 종료하거나 운영 체제를 패닉 모드(Linux 게스트 운영 체제)에 배치하여 장애 조치 상황을 시뮬레이션합니다. 이 단계를 수행하면 장애 조치 구성이 설계된 대로 작동하는지 여부를 파악하는 데 도움이 됩니다.
   1. 장애 조치(failover)를 실행하는 데 걸리는 시간을 측정합니다. 시간이 너무 길면 다음을 고려하십시오.
        - SUSE Linux의 경우 Azure Fence 에이전트 대신 SBD 장치를 사용하여 장애 조치(failover)를 가속화합니다.
        - SAP HANA의 경우 데이터 다시 로드시간이 너무 오래 걸리는 경우 더 많은 저장소 대역폭을 프로비전하는 것이 좋습니다.
   3. 백업/복원 시퀀스와 타이밍을 테스트하고 필요한 경우 수정합니다. 백업 시간이 충분한지 확인합니다. 또한 복원 및 시간 복원 활동을 테스트해야 합니다. RTO가 데이터베이스 또는 VM 복원 프로세스를 사용하는 모든 위치에 복원 시간이 RTO SLA 내에 있는지 확인합니다.
   4. 지역 간 DR 기능 및 아키텍처를 테스트합니다.
1. 보안 검사.
   1. Azure 역할 기반 액세스 제어(RBAC) 아키텍처의 유효성을 테스트합니다. 목표는 서로 다른 팀의 액세스 및 권한을 분리하고 제한하는 것입니다. 예를 들어 SAP Basis 팀 구성원은 VM을 배포하고 Azure Storage의 디스크를 지정된 Azure 가상 네트워크에 할당할 수 있어야 합니다. 그러나 SAP Basis 팀은 자체 가상 네트워크를 만들거나 기존 가상 네트워크의 설정을 변경할 수 없습니다. 네트워크 팀의 구성원은 SAP 응용 프로그램 및 DBMS VM이 실행 중인 가상 네트워크에 VM을 배포할 수 없습니다. 또한 이 팀의 구성원은 VM의 특성을 변경하거나 VM 또는 디스크를 삭제할 수 없습니다.  
   1.  네트워크 [보안 그룹 및 ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) 규칙이 예상대로 작동하는지 확인하고 보호된 리소스를 보호합니다.
   1.  암호화해야 하는 모든 리소스가 암호화되는지 확인합니다. 인증서를 백업하고, 해당 인증서를 저장 및 액세스하고, 암호화된 엔터티를 복원하는 프로세스를 정의하고 구현합니다.
   1.  OS 지원 관점에서 가능한 경우 OS 디스크에 [Azure 디스크 암호화를](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) 사용합니다.
   1.  너무 많은 암호화 계층을 사용하지 않는지 확인합니다. 경우에 따라 Azure 디스크 암호화를 DBMS 투명 데이터 암호화 방법 중 하나와 함께 사용하여 동일한 서버의 다른 디스크 또는 구성 요소를 보호하는 것이 합리적입니다.  예를 들어 SAP DBMS 서버에서 Azure 디스크 암호화(ADE)는 운영 체제 부팅 디스크(OS가 ADE를 지원하는 경우)와 DBMS 데이터 지속성 파일에서 사용되지 않는 데이터 디스크에서 사용할 수 있습니다.  예를 들어 DBMS TDE 암호화 키를 보유한 디스크에서 ADE를 사용하는 것입니다.
1. 성능 테스트. SAP추적 및 측정을 기반으로 SAP에서 다음을 비교합니다.
   - 해당하는 경우 상위 10개 온라인 보고서를 현재 구현과 비교합니다.
   - 해당하는 경우 상위 10개 일괄 처리 작업을 현재 구현과 비교합니다.
   - 인터페이스를 통해 SAP 시스템으로데이터 전송을 비교합니다. 온-프레미스에서 Azure로 전송하는 것과 같이 서로 다른 위치 간에 전송이 진행되고 있다는 것을 알고 있는 인터페이스에 중점을 둡니다.


## <a name="non-production-phase"></a>비생산 단계 
이 단계에서는 POC(파일럿 또는 개념 증명)가 성공한 후 비프로덕션 SAP 시스템을 Azure에 배포하기 시작했다고 가정합니다. POC 기간 동안 배운 모든 것을 이 배포에 통합합니다. POC에 대해 나열된 모든 기준과 단계도 이 배포에 적용됩니다.

이 단계에서는 일반적으로 개발 시스템, 단위 테스트 시스템 및 비즈니스 회귀 테스트 시스템을 Azure에 배포합니다. 한 SAP 응용 프로그램 라인에 있는 하나 이상의 비프로덕션 시스템에는 향후 프로덕션 시스템에 있을 전체 고가용성 구성이 있는 것이 좋습니다. 다음은 이 단계에서 완료해야 하는 몇 가지 추가 단계입니다.  

1.  시스템을 이전 플랫폼에서 Azure로 이동하기 전에 CPU 사용량, 저장소 처리량 및 IOPS 데이터와 같은 리소스 소비 데이터를 수집합니다. 특히 DBMS 계층 단위에서 이 데이터를 수집하지만 응용 프로그램 계층 단위에서 수집합니다. 또한 네트워크 및 스토리지 대기 시간을 측정합니다.
2.  시스템의 가용성 사용 시간 패턴을 기록합니다. 목표는 비프로덕션 시스템을 하루 종일, 매일 사용할 수 있어야 하는지 또는 일주일 또는 한 달의 특정 단계에서 종료할 수 있는 비프로덕션 시스템이 있는지 여부를 파악하는 것입니다.
3.  Azure에서 VM에 대한 자체 OS 이미지를 만들지 또는 Azure 공유 이미지 갤러리의 이미지를 사용할지 여부를 테스트하고 결정합니다. 공유 이미지 갤러리의 이미지를 사용하는 경우 OS 공급업체와의 지원 계약을 반영하는 이미지를 사용해야 합니다. 일부 OS 공급업체의 경우 공유 이미지 갤러리를 사용하면 라이선스 이미지를 가져올 수 있습니다. 다른 OS 이미지의 경우 Azure에서 인용한 가격에 지원이 포함됩니다. 사용자 고유의 OS 이미지를 만들려는 경우 다음 문서에서 설명서를 찾을 수 있습니다.
    -   [Azure에 배포된 Windows VM의 일반화된 이미지 빌드](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Azure에 배포된 Linux VM의 일반화된 이미지 빌드](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  공유 이미지 갤러리에서 SUSE 및 Red Hat Linux 이미지를 사용하는 경우 공유 이미지 갤러리에서 Linux 공급업체에서 제공하는 SAP용 이미지를 사용해야 합니다.
4.  Microsoft 지원 계약에 대한 SAP 지원 요구 사항을 충족해야 합니다. [#2015553 SAP 지원 노트를](https://launchpad.support.sap.com/#/notes/2015553)참조하십시오. HANA 대형 인스턴스의 경우 [온보딩 요구 사항을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)참조하십시오.
4.  최적의 가동 중단 시간을 선택할 수 있도록 적절한 사람이 [계획된 유지 관리 알림을](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) 받을 수 있도록 하십시오.
5.  배포에 적용할 수 있는 새로운 기능에 대해 [채널 9와](https://channel9.msdn.com/) 같은 채널에서 Azure 프레젠테이션을 자주 확인합니다.
6.  [지원 노트 #1928533,](https://launchpad.support.sap.com/#/notes/1928533)새로운 VM SCO 및 새로 지원되는 OS 및 DBMS 릴리스에 대한 지원 노트와 같은 Azure와 관련된 SAP 메모를 확인합니다. 새 VM 유형의 가격책정을 이전 VM 유형의 가격과 비교하여 최상의 가격/성능 비율로 VM을 배포할 수 있습니다.
7.  SAP 지원 노트, SAP HANA 하드웨어 디렉토리 및 SAP PAM을 다시 확인합니다. Azure용 지원되는 VM, 해당 VM에서 지원되는 OS 릴리스 및 지원되는 SAP 및 DBMS 릴리스에 대한 변경 사항이 없는지 확인합니다.
8.  Azure에서 새로운 HANA 인증 SCO를 [SAP 웹 사이트에서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 확인하십시오. 새 SCO의 가격과 사용할 SCO의 가격을 비교합니다. 결국, 최고의 가격/성능 비율을 가진 것 들을 사용 하 여 필요한 변경 합니다.
9.  배포 스크립트를 조정하여 새 VM 형식을 사용하고 사용하려는 새 Azure 기능을 통합합니다.
10. 인프라를 배포한 후 SAP 지원 [노트#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 따라 SAP 응용 프로그램 계층 VM과 DBMS VM 간의 네트워크 대기 시간을 테스트하고 평가합니다. [#1100926 SAP 지원 노트에서](https://launchpad.support.sap.com/#/notes/1100926/E)네트워크 대기 시간 지침에 대해 결과를 평가합니다. 네트워크 대기 시간은 보통 또는 양호한 범위에 있어야 합니다. 이 문서에 설명된 대로 VM과 HANA 대형 인스턴스 단위 간의 트래픽에는 [예외가](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)적용됩니다. SAP 워크로드 및 [SAP HANA 인프라 구성 및 Azure의 작업에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) [대한 Azure 가상 시스템 DBMS 배포에 대한 고려 사항에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) 언급된 제한 사항이 배포에 적용되지 않는지 확인합니다.
11. SAP 응용 프로그램에서 최적의 네트워크 대기 시간을 위해 Azure 근접 배치 그룹에 설명된 대로 VM이 올바른 [Azure 근접 배치 그룹에](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)배포되어 있는지 [확인합니다.](sap-proximity-placement-scenarios.md)
11. 워크로드를 적용하기 전에 개념 증명 단계에 대해 나열된 다른 모든 검사를 수행합니다.
12. 워크로드가 적용되는 경우 Azure에서 시스템의 리소스 사용량을 기록합니다. 이 소비를 이전 플랫폼의 레코드와 비교합니다. 큰 차이가 있는 경우 향후 배포의 VM 크기를 조정합니다. VM의 규모를 축소하면 VM의 스토리지 및 네트워크 대역폭도 줄어듭니다.
    - [Azure의 Windows 가상 컴퓨터용 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure에서 Linux 가상 머신에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 시스템 복사 기능 및 프로세스를 실험합니다. 목표는 개발 시스템이나 테스트 시스템을 쉽게 복사할 수 있도록 하여 프로젝트 팀이 새로운 시스템을 신속하게 얻을 수 있도록 하는 것입니다. 이러한 작업에 [SAP 라마를](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) 사용하는 것이 좋습니다.
14. 팀의 Azure 역할 기반 액세스, 사용 권한 및 프로세스를 최적화하고 연마하여 업무가 분리되었는지 확인합니다. 동시에 모든 팀이 Azure 인프라에서 작업을 수행할 수 있는지 확인합니다.
15. 고가용성 및 재해 복구 절차를 연습, 테스트 및 문서화하여 직원이 이러한 작업을 실행할 수 있도록 합니다. 단점을 식별하고 배포에 통합하는 새 Azure 기능을 조정합니다.

 
## <a name="production-preparation-phase"></a>생산 준비 단계 
이 단계에서는 비프로덕션 배포 중에 경험하고 배운 내용을 수집하고 향후 프로덕션 배포에 적용합니다. 또한 현재 호스팅 위치와 Azure 간에 데이터 전송 작업을 준비해야 합니다.

1.  Azure로 이동하기 전에 프로덕션 시스템의 필요한 SAP 릴리스 업그레이드를 완료합니다.
1.  프로덕션 시스템을 마이그레이션한 후 수행해야 하는 기능 및 비즈니스 테스트에 대해 비즈니스 소유자와 동의합니다.
1.  이러한 테스트가 현재 호스팅 위치의 소스 시스템으로 완료되었는지 확인합니다. 시스템이 Azure로 이동된 후 처음으로 테스트를 수행하지 마십시오.
1.  프로덕션 시스템을 Azure로 마이그레이션하는 프로세스를 테스트합니다. 동일한 기간 동안 모든 프로덕션 시스템을 Azure로 이동하지 않는 경우 동일한 호스팅 위치에 있어야 하는 프로덕션 시스템 그룹을 빌드합니다. 데이터 마이그레이션을 테스트합니다. 다음은 몇 가지 일반적인 방법입니다.
    - SQL Server Always On, HANA 시스템 복제 또는 로그 전달과 함께 백업/복원과 같은 DBMS 메서드를 사용하여 Azure에서 데이터베이스 콘텐츠를 시드하고 동기화합니다.
    - 소규모 데이터베이스의 경우 백업/복원을 사용합니다.
    - SAP SWPM에 통합된 SAP 마이그레이션 모니터를 사용하여 이기종 마이그레이션을 수행합니다.
    - 마이그레이션을 SAP 릴리스 업그레이드와 결합해야 하는 경우 SAP [DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 프로세스를 사용합니다. 소스 DBMS 및 대상 DBMS의 모든 조합이 지원되는 것은 아닙니다. DMO의 다른 릴리스에 대한 특정 SAP 지원 노트에서 자세한 내용을 확인할 수 있습니다. 예를 들어 [SUM 2.0 SP04의 데이터베이스 마이그레이션 옵션(DMO).](https://launchpad.support.sap.com/#/notes/2644872)
    - 백업 또는 SAP 내보내기 파일을 이동해야 하는 경우를 대비하여 인터넷이나 ExpressRoute를 통해 데이터 전송 처리량이 더 나은지 테스트합니다. 인터넷을 통해 데이터를 이동하는 경우 향후 프로덕션 시스템에 필요한 네트워크 보안 그룹/응용 프로그램 보안 그룹 규칙 중 일부를 변경해야 할 수 있습니다.
1.  이전 플랫폼에서 Azure로 시스템을 이동하기 전에 리소스 소비 데이터를 수집합니다. 유용한 데이터에는 CPU 사용량, 스토리지 처리량 및 IOPS 데이터가 포함됩니다. 특히 DBMS 계층 단위에서 이 데이터를 수집하지만 응용 프로그램 계층 단위에서 수집합니다. 또한 네트워크 및 스토리지 대기 시간을 측정합니다.
1.  SAP 지원 노트와 필요한 OS 설정, SAP HANA 하드웨어 디렉토리 및 SAP PAM을 다시 확인합니다. Azure용 지원되는 VM, 해당 VM에서 지원되는 OS 릴리스 및 지원되는 SAP 및 DBMS 릴리스에 변경 사항이 없는지 확인합니다.
1.  배포 스크립트를 업데이트하여 VM 형식 및 Azure 기능에 대해 내린 최신 결정을 고려합니다.
1.  인프라 및 응용 프로그램을 배포한 후 다음 을 확인합니다.
    - 올바른 특성과 저장소 크기로 올바른 VM 형식이 배포되었습니다.
    - VM은 올바른 OS 릴리스 및 패치에 있으며 균일합니다.
    - VM은 필요에 따라 균일한 방식으로 강화됩니다.
    - 올바른 응용 프로그램 릴리스 및 패치가 설치되고 배포되었습니다.
    - VM은 계획대로 Azure 가용성 집합에 배포되었습니다.
    - Azure Premium 저장소는 대기 시간에 민감한 디스크 또는 [99.9%의 단일 VM SLA가](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 필요한 경우 사용됩니다.
    - Azure 쓰기 가속기가 올바르게 배포됩니다.
        - VM 내에서 쓰기 가속기가 필요한 디스크에서 저장소 공간 또는 스트라이프 세트가 올바르게 빌드되었는지 확인합니다.
        - [리눅스에서 소프트웨어 RAID의 구성을](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)확인하십시오.
        - [Azure의 Linux VM에서 LVM의 구성을 확인합니다.](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    - [Azure 관리 디스크는](https://azure.microsoft.com/services/managed-disks/) 독점적으로 사용됩니다.
    - VM은 올바른 가용성 집합 및 가용성 영역에 배포되었습니다.
    - [Azure 가속 네트워킹은](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) SAP 응용 프로그램 계층 및 SAP DBMS 계층에 사용되는 VM에서 활성화됩니다.
    - SAP NetWeaver, Hybris 또는 S/4HANA를 기반으로 하는 SAP 응용 프로그램과 SAP 시스템의 DBMS 계층 간의 통신 경로에는 [Azure 네트워크 가상 어플라이언스가](https://azure.microsoft.com/solutions/network-appliances/) 없습니다.
    - 응용 프로그램 보안 그룹 및 네트워크 보안 그룹 규칙은 원하는 대로 통신을 허용하고 계획되며 필요한 경우 통신을 차단합니다.
    - 시간 설정설정은 앞서 설명한 대로 올바르게 설정됩니다.
    - VM은 SAP 응용 프로그램과 함께 최적의 네트워크 대기 시간을 위해 Azure 근접 배치 그룹에 설명된 대로 올바른 [Azure 근접 배치 그룹에](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) [배포됩니다.](sap-proximity-placement-scenarios.md)
    - SAP 응용 프로그램 계층 VM과 DBMS VM 간의 네트워크 대기 시간은 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)SAP 지원 노트에 설명된 대로 테스트 및 검증됩니다. [#1100926 SAP 지원 노트에서](https://launchpad.support.sap.com/#/notes/1100926/E)네트워크 대기 시간 지침에 대해 결과를 평가합니다. 네트워크 대기 시간은 보통 또는 양호한 범위에 있어야 합니다. 이 문서에 설명된 대로 VM과 HANA 대형 인스턴스 단위 간의 트래픽에는 [예외가](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)적용됩니다.
    - 필요한 경우 적절한 암호화 방법으로 암호화가 구현되었습니다.
    - 인터페이스 및 기타 응용 프로그램은 새로 배포된 인프라를 연결할 수 있습니다.
1.  계획된 Azure 유지 관리에 반응하기 위한 플레이북을 만듭니다. 계획된 유지 관리를 위해 시스템 및 VM을 다시 부팅해야 하는 순서를 결정합니다.
    

## <a name="go-live-phase"></a>라이브 단계
라이브 단계에서는 이전 단계에서 개발한 플레이북을 따라야 합니다. 테스트하고 실행한 단계를 실행합니다. 구성 및 프로세스의 마지막 순간에 변경내용을 허용하지 마십시오. 또한 다음 단계를 완료합니다.

1. Azure Portal 모니터링 및 기타 모니터링 도구가 작동하는지 확인합니다. 우리는 윈도우 성능 모니터를 권장합니다 (perfmon) 리눅스에 대한 윈도우와 SAR.
    - CPU 카운터.
        - 평균 CPU 시간, 총 (모든 CPU)
        - 평균 CPU 시간, 각 개별 프로세서(M128 VM의 프로세서 128개)
        - CPU 커널 시간, 각 개별 프로세서
        - CPU 사용자 시간, 각 개별 프로세서
    - 메모리.
        - 사용 가능한 메모리
        - 메모리 페이지/초
        - 메모리 페이지 아웃/초
    - 디스크를 선택해야 합니다.
        - 개별 디스크당 KBps의 디스크 읽기
        - 개별 디스크당 디스크 읽기/초
        - 개별 디스크당 마이크로초/읽기 디스크 읽기
        - 개별 디스크당 KBps의 디스크 쓰기
        - 개별 디스크당 디스크 쓰기/초
        - 개별 디스크당 마이크로초/읽기 디스크 쓰기
    - Network.
        - 네트워크 패킷 1초
        - 네트워크 패킷 아웃/초
        - 네트워크 KB 인/초
        - 네트워크 KB 아웃/초
1.  데이터 마이그레이션 후 비즈니스 소유자와 합의한 모든 유효성 검사 테스트를 수행합니다. 원본 원본 시스템에 대한 결과가 있는 경우에만 유효성 검사 테스트 결과를 수락합니다.
1.  인터페이스가 작동하는지 여부와 다른 응용 프로그램이 새로 배포된 프로덕션 시스템과 통신할 수 있는지 확인합니다.
1.  SAP 트랜잭션 STMS를 통해 전송 및 수정 시스템을 확인합니다.
1.  프로덕션을 위해 시스템이 릴리스된 후 데이터베이스 백업을 수행합니다.
1.  프로덕션을 위해 시스템이 릴리스된 후 SAP 응용 프로그램 계층 VM에 대한 VM 백업을 수행합니다.
1.  현재 이동 라이브 단계의 일부가 아니지만 이 라이브 단계에서 Azure로 이동한 SAP 시스템과 통신하는 SAP 시스템의 경우 SM51에서 호스트 이름 버퍼를 재설정해야 합니다. 이렇게 하면 Azure로 이동한 응용 프로그램 인스턴스의 이름과 연결된 이전 캐시된 IP 주소가 제거됩니다.  


## <a name="post-production"></a>프로덕션 후
이 단계는 시스템을 모니터링, 운영 및 관리하는 것입니다. SAP 관점에서 이전 호스팅 위치에서 완료하는 데 필요한 일반적인 작업이 적용됩니다. 다음 Azure 관련 작업도 완료합니다.

1. 고충전 시스템에 대한 Azure 송장을 검토합니다.
2. VM 측과 스토리지 측면에서 가격/성능 효율성을 최적화합니다.
3. 시스템을 종료할 수 있는 시간을 최적화합니다.  


## <a name="next-steps"></a>다음 단계
 아래 문서를 참조하세요.

- [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver에 대한 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [SAP 워크로드에 대한 Azure 가상 시스템 DBMS 배포에 대한 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

