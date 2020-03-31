---
title: Azure 사이트 복구를 사용 하 여 온-프레미스 애플 리 케이 션에 대 한 재해 복구에 대 한
description: Azure Site Recovery 서비스에서 재해 복구를 사용하여 보호할 수 있는 워크로드를 설명합니다.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062831"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>온-프레미스 앱에 대한 재해 복구 정보

이 문서에서는 [Azure 사이트 복구](site-recovery-overview.md) 서비스를 통해 재해 복구를 위해 보호할 수 있는 온-프레미스 워크로드 및 앱에 대해 설명합니다.

## <a name="overview"></a>개요

조직은 계획되고 계획되지 않은 가동 중지 시간 동안 워크로드와 데이터를 안전하게 유지하고 사용할 수 있도록 비즈니스 연속성 및 재해 복구(BCDR) 전략이 필요합니다. 그리고, 정기적 인 작업 조건으로 복구합니다.

Site Recovery는 BCDR 전략에 기여하는 Azure 서비스로 Site Recovery를 사용하여 애플리케이션 인식 복제를 클라우드 또는 보조 사이트에 배포할 수 있습니다. 사이트 복구를 사용하여 복제를 관리하고, 재해 복구 테스트를 수행하고, 장애 조치 및 장애 복구를 실행할 수 있습니다. 앱은 Windows 또는 Linux 기반 컴퓨터, 물리적 서버, VMware 또는 Hyper-V에서 실행할 수 있습니다.

사이트 복구는 SharePoint, Exchange, 역학, SQL Server 및 Active Directory와 같은 Microsoft 응용 프로그램과 통합됩니다. Microsoft는 오라클, SAP 및 Red Hat을 비롯한 주요 공급업체와 긴밀하게 협력하고 있습니다. 앱 단위로 복제 솔루션을 사용자 지정할 수 있습니다.

## <a name="why-use-site-recovery-for-application-replication"></a>애플리케이션 복제에 Site Recovery를 사용해야 하는 이유

사이트 복구는 다음과 같이 애플리케이션 수준 보호 및 복구에 기여합니다.

- 지원되는 컴퓨터에서 실행되는 모든 워크로드에 대해 응용 프로그램과 달리하지 않습니다.
- 가장 중요한 비즈니스 앱의 요구를 충족하기 위해 RPO(복구 지점 목표)를 최대 30초로 낮게 복제할 수 있습니다.
- 단일 또는 다중 계층 애플리케이션에 대한 앱 일관성 스냅샷.
- SQL Server AlwaysOn과의 통합 및 다른 응용 프로그램 수준 복제 기술과의 파트너십. 예를 들어 Active Directory 복제, SQL AlwaysOn 및 Exchange 데이터베이스 가용성 그룹(DAGs)을 예로 들 수 있습니다.
- 한 번의 클릭으로 전체 응용 프로그램 스택을 복구하고 계획에 외부 스크립트 및 수동 작업을 포함할 수 있는 유연한 복구 계획입니다.
- 사이트 복구 및 Azure의 고급 네트워크 관리로 앱 네트워크 요구 사항을 간소화합니다. 낮은 복구 시간 목표(RTO) 네트워크 전환을 위해 IP 주소를 예약하고, 로드 균형 조정을 구성하고, Azure 트래픽 관리자와의 통합과 같은 네트워크 관리.
- 다운로드하고 복구 계획과 통합할 수 있는 프로덕션 준비된 애플리케이션 특정 스크립트를 제공하는 다양한 자동화 라이브러리입니다.

## <a name="workload-summary"></a>워크로드 요약

Site Recovery는 지원되는 컴퓨터에서 실행 중인 모든 앱을 복제할 수 있습니다. 다음 표에 지정된 앱에 대한 추가 테스트를 수행하기 위해 제품 팀과 협력했습니다.

| **작업** |**Azure VM을 Azure에 복제** |**Hyper-V VM을 보조 사이트로 복제** | **Hyper-V VM을 Azure에 복제** | **VMware VM을 보조 사이트에 복제** | **VMware VM을 Azure에 복제** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |yes |yes |yes |yes |yes|
| 웹앱(IIS, SQL) |yes |yes |yes |yes |yes|
| System Center Operations Manager |yes |yes |yes |yes |yes|
| SharePoint |yes |yes |yes |yes |yes|
| SAP<br/><br/>비 클러스터에 대해 Azure에 SAP 사이트 복제 |예 (마이크로 소프트에 의해 테스트) |예 (마이크로 소프트에 의해 테스트) |예 (마이크로 소프트에 의해 테스트) |예 (마이크로 소프트에 의해 테스트) |예 (마이크로 소프트에 의해 테스트)|
| Exchange(비 DAG) |yes |yes |yes |yes |yes|
| 원격 데스크톱/VDI |yes |yes |yes |yes |yes|
| Linux(운영 체제 및 앱) |예 (마이크로 소프트에 의해 테스트) |예 (마이크로 소프트에 의해 테스트) |예 (마이크로 소프트에 의해 테스트) |예 (마이크로 소프트에 의해 테스트) |예 (마이크로 소프트에 의해 테스트)|
| Dynamics AX |yes |yes |yes |yes |yes|
| Windows 파일 서버 |yes |yes |yes |yes |yes|
| Citrix XenApp 및 XenDesktop |yes|해당 없음 |yes |해당 없음 |yes |

## <a name="replicate-active-directory-and-dns"></a>Active Directory 및 DNS 복제

Active Directory 및 DNS 인프라는 대부분의 엔터프라이즈 앱에 필수적입니다. 재해 복구 중에 워크로드 및 앱을 복구하기 전에 이러한 인프라 구성 요소를 보호하고 복구해야 합니다.

사이트 복구를 사용하여 Active Directory 및 DNS에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 예를 들어 SharePoint 및 SAP를 기본 사이트에서 보조 사이트로 장애 조치하려면 Active Directory를 통해 먼저 실패하는 복구 계획을 설정할 수 있습니다. 그런 다음 추가 앱별 복구 계획을 사용하여 Active Directory에 의존하는 다른 앱을 장애 조치합니다.

Active Directory 및 DNS에 대한 재해 복구에 대해 [자세히 알아봅니다.](site-recovery-active-directory.md)

## <a name="protect-sql-server"></a>SQL Server 보호

SQL Server는 온-프레미스 데이터 센터의 많은 비즈니스 앱에 대한 데이터 서비스 기반을 제공합니다. 사이트 복구는 SQL Server HA/DR 기술과 함께 사용하여 SQL Server를 사용하는 다중 계층 엔터프라이즈 앱을 보호할 수 있습니다.

사이트 복구는 다음을 제공합니다.

- SQL Server에 대해 간단하고 비용 효율적인 재해 복구 솔루션. SQL Server 독립 실행형 서버 및 클러스터의 여러 버전 및 에디션을 Azure 또는 보조 사이트로 복제합니다.
- SQL AlwaysOn 가용성 그룹 통합은 Azure Site Recovery 복구 계획을 통해 장애 조치 및 장애 복구를 관리합니다.
- SQL Server 데이터베이스를 포함한 애플리케이션의 모든 계층에 대한 엔드투엔드 복구 계획.
- Azure에서 더 큰 IaaS 가상 컴퓨터 크기로 _버스트하여_ 사이트 복구를 통해 최대 부하에 대한 SQL Server 크기 조정
- SQL Server 재해 복구의 간편한 테스트. 테스트 장애 조치를 실행하여 프로덕션 환경에 영향을 주지 않고 데이터를 분석하고 규정 준수 검사를 실행할 수 있습니다.

SQL 서버의 재해 복구에 대해 [자세히 알아보세요.](site-recovery-sql.md)

## <a name="protect-sharepoint"></a>SharePoint 보호

Azure Site Recovery를 사용하면 다음과 같이 SharePoint 배포를 보호할 수 있습니다.

- 재해 복구를 위한 대기 팜에 대한 필요성 및 관련된 인프라 비용을 제거합니다. 사이트 복구를 사용하여 전체 팜(웹, 앱 및 데이터베이스 계층)을 Azure 또는 보조 사이트에 복제합니다.
- 애플리케이션 배포 및 관리를 간소화합니다. 기본 사이트에 배포된 업데이트는 자동으로 복제됩니다. 업데이트는 장애 조치 및 보조 사이트에서 팜을 복구한 후에 사용할 수 있습니다. 대기 팜을 최신 상태로 유지하는 것과 관련된 관리 복잡성과 비용을 낮춥습니다.
- 테스트 및 디버깅에 프로덕션 환경과 유사한 복사본 주문형 복사본 환경을 만들어 SharePoint 애플리케이션 개발 및 테스트를 용이하게 합니다.
- 사이트 복구를 사용하여 SharePoint 배포를 Azure에 마이그레이션하여 전환을 클라우드로 단순화합니다.

SharePoint의 재해 복구에 대해 [자세히 알아보세요.](site-recovery-sharepoint.md)

## <a name="protect-dynamics-ax"></a>Dynamics AX 보호

Azure Site Recovery를 사용하면 다음과 같이 Dynamics AX ERP 솔루션을 보호하도록 합니다.

- 전체 Dynamics AX 환경(웹 및 AOS 계층, 데이터베이스 계층, SharePoint)을 Azure 또는 보조 사이트로 복제하는 관리합니다.
- Dynamics AX 배포의 클라우드(Azure)로 마이그레이션을 간소화합니다.
- 테스트 및 디버깅에 프로덕션 환경과 유사한 복사본 주문형을 만들어 Dynamics AX 애플리케이션 개발 및 테스트를 용이하게 합니다.

동적 AX에 대한 재해 복구에 대해 [자세히 알아봅니다.](site-recovery-dynamicsax.md)

## <a name="protect-remote-desktop-services"></a>원격 데스크톱 서비스 보호

RDS(원격 데스크톱 서비스)를 사용하면 가상 데스크톱 인프라(VDI), 세션 기반 데스크톱 및 응용 프로그램을 사용하여 사용자가 어디서나 작업할 수 있습니다.

Azure 사이트 복구를 사용하면 다음 서비스를 복제할 수 있습니다.

- 관리되거나 관리되지 않는 가상 데스크톱을 보조 사이트에 복제합니다.
- 원격 응용 프로그램 및 세션을 보조 사이트 또는 Azure에 복제합니다.

다음 표에서는 복제 옵션을 보여 주며 다음과 같은 옵션을 보여 주며 다음과 같은 옵션을 보여 주실 수 있습니다.

| **Rds** |**Azure VM을 Azure에 복제** | **Hyper-V VM을 보조 사이트로 복제** | **Hyper-V VM을 Azure에 복제** | **VMware VM을 보조 사이트에 복제** | **VMware VM을 Azure에 복제** | **물리적 서버를 보조 사이트에 복제** | **물리적 서버를 Azure에 복제** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **풀링된 가상 데스크톱(관리되지 않음)** |예|yes |예 |yes |예 |yes |예 |
| **풀링된 가상 데스크톱(관리됨/UPD 없음)** |예|yes |예 |yes |예 |yes |예 |
| **원격 애플리케이션 및 데스크톱 세션(UPD 없음)** |yes|yes |yes |yes |yes |yes |yes |

RDS의 재해 복구에 대해 [자세히 알아보십시오.](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure)

## <a name="protect-exchange"></a>Exchange 보호

Site Recovery를 사용하면 다음과 같이 Exchange를 보호할 수 있습니다.

- 단일 또는 독립 실행형 서버와 같은 소규모 Exchange 배포의 경우 Site Recovery가 Azure 또는 보조 사이트로 복제하고 장애 조치(failover)할 수 있습니다.
- 대규모 배포의 경우 Site Recovery가 Exchange DAGS와 통합됩니다.
- Exchange DAG는 엔터프라이즈에서 Exchange 재해 복구를 위한 권장된 솔루션입니다.  Site Recovery의 복구 계획은 DAG를 포함하여 사이트에 걸쳐 DAG 장애 조치를 오케스트레이션할 수 있습니다.

Exchange의 재해 복구에 대한 자세한 내용은 [Exchange DAGs](/Exchange/high-availability/database-availability-groups/database-availability-groups) 및 [Exchange 재해 복구를](/Exchange/high-availability/disaster-recovery/disaster-recovery)참조하십시오.

## <a name="protect-sap"></a>SAP 보호

Site Recovery를 사용하여 다음과 같이 SAP 배포를 보호합니다.

- 구성 요소를 Azure에 복사하여 온-프레미스로 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 애플리케이션 보호를 활성화합니다.
- 구성 요소를 다른 Azure 데이터 센터에 복사하여 Azure를 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 애플리케이션 보호를 활성화합니다.
- Site Recovery를 사용하여 클라우드 마이그레이션을 간소화하여 Azure에 SAP 배포를 마이그레이션합니다.
- SAP 애플리케이션 테스팅을 위해 온디맨드로 프로덕션 클론을 만들어 SAP 프로젝트 업그레이드, 테스트 및 프로토타입 생성을 단순화합니다.

SAP의 재해 복구에 대해 [자세히 알아보세요.](site-recovery-sap.md)

## <a name="protect-internet-information-services"></a>인터넷 정보 서비스 보호

사이트 복구를 사용하여 다음과 같이 IIS(인터넷 정보 서비스) 배포를 보호합니다.

Azure Site Recovery는 환경에서 중요한 구성 요소를 콜드 원격 사이트 또는 Microsoft Azure와 같은 퍼블릭 클라우드에 복제하여 재해 복구를 제공합니다. 웹 서버와 데이터베이스가 있는 가상 시스템이 복구 사이트에 복제되므로 구성 파일이나 인증서에 대해 별도의 백업을 할 필요가 없습니다. 변경된 사후 장애 조치인 환경 변수에 따라 달라지는 애플리케이션 매핑 및 바인딩은 재해 복구 계획에 통합되는 스크립트를 통해 업데이트될 수 있습니다. 가상 시스템은 장애 조치 중에만 복구 사이트에서 발생합니다. 또한 Azure 사이트 복구를 사용하면 다음과 같은 기능을 제공하여 종단 간 장애 조치(failover)를 오케스트레이션할 수 있습니다.

- 다양한 계층에서 가상 머신의 종료 및 시작 순서를 지정합니다.
- 스크립트를 추가하여 응용 프로그램 종속성 및 바인딩을 시작한 후 가상 시스템에 업데이트할 수 있습니다. 스크립트는 복구 사이트를 가리키기 위해 DNS 서버를 업데이트하는 데 사용할 수 있습니다.
- 기본 및 복구 네트워크를 매핑하여 장애 조치 전 가상 시스템에 IP 주소를 할당하고 장애 조치 후 업데이트할 필요가 없는 스크립트를 사용합니다.
- 재해 시 혼동 범위를 제거하는 여러 웹 응용 프로그램에 대해 원클릭 장애 조치(failover)를 사용할 수 있습니다.
- DR 드릴의 격리된 환경에서 복구 계획을 테스트하는 기능입니다.

IIS의 재해 복구에 대해 [자세히 알아보십시오.](site-recovery-iis.md)

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp 및 XenDesktop 보호

Site Recovery를 사용하여 음과 같이 Citrix XenApp와 XenDesktop 배포를 보호합니다.

- Citrix XenApp 및 젠데스크톱 배포의 보호를 활성화합니다. Active Directory, DNS 서버, SQL 데이터베이스 서버, Citrix 배달 컨트롤러, 스토어프론트 서버, XenApp 마스터(VDA), Citrix XenApp 라이센스 서버 등 다양한 배포 계층을 Azure에 복제합니다.
- Citrix XenApp 및 XenDesktop 배포를 Azure로 마이그레이션하기 위해 Site Recovery를 사용하여 클라우드 마이그레이션을 간소화합니다.
- 테스트 및 디버깅을 위해 프로덕션 환경과 유사한 주문형 복사본을 만들어 Citrix XenApp/XenDesktop 테스트를 간소화합니다.
- 이 솔루션은 클라이언트 가상 데스크톱이 아닌 Windows Server 가상 데스크톱에만 적용됩니다. 클라이언트 가상 데스크톱은 아직 Azure에서 라이선스에 대해 지원되지 않습니다. Azure의 클라이언트/서버 데스크톱용 라이선스에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/licensing-faq/).

Citrix XenApp 및 XenDesktop 배포에 대한 재해 복구에 대해 [자세히 알아보십시오.](site-recovery-citrix-xenapp-and-xendesktop.md) 또는 [Citrix 백서를](https://aka.ms/citrix-xenapp-xendesktop-with-asr)참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure VM의 재해 복구에 대해 [자세히 알아보세요.](azure-to-azure-quickstart.md)
