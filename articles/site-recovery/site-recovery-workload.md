---
title: Azure Site Recovery를 사용 하는 온-프레미스 앱에 대 한 재해 복구 정보
description: Azure Site Recovery 서비스에서 재해 복구를 사용하여 보호할 수 있는 워크로드를 설명합니다.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80062831"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>온-프레미스 앱에 대한 재해 복구 정보

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용 하 여 재해 복구를 위해 보호할 수 있는 온-프레미스 워크 로드 및 앱을 설명 합니다.

## <a name="overview"></a>개요

조직에서는 계획 되거나 계획 되지 않은 가동 중지 시간 동안 워크 로드 및 데이터를 안전 하 고 사용 가능 하 게 유지 하는 BCDR (비즈니스 연속성 및 재해 복구) 전략이 필요 합니다 그리고 일반적인 작업 조건으로 복구 합니다.

Site Recovery는 BCDR 전략에 기여하는 Azure 서비스로 Site Recovery를 사용하여 애플리케이션 인식 복제를 클라우드 또는 보조 사이트에 배포할 수 있습니다. Site Recovery를 사용 하 여 복제를 관리 하 고 재해 복구 테스트를 수행 하며 장애 조치 및 장애 복구를 실행할 수 있습니다. 앱은 Windows 또는 Linux 기반 컴퓨터, 물리적 서버, VMware 또는 Hyper-v에서 실행할 수 있습니다.

Site Recovery는 SharePoint, Exchange, Dynamics, SQL Server 및 Active Directory와 같은 Microsoft 응용 프로그램과 통합 됩니다. Microsoft는 Oracle, SAP 및 Red Hat을 비롯 한 선두 공급 업체와 긴밀 하 게 협력 합니다. 앱 단위로 복제 솔루션을 사용자 지정할 수 있습니다.

## <a name="why-use-site-recovery-for-application-replication"></a>애플리케이션 복제에 Site Recovery를 사용해야 하는 이유

사이트 복구는 다음과 같이 애플리케이션 수준 보호 및 복구에 기여합니다.

- 응용 프로그램에 관계 없이 지원 되는 컴퓨터에서 실행 되는 모든 워크 로드에 대 한 복제를 제공 합니다.
- 가장 중요 한 비즈니스 앱의 요구를 충족 하기 위해 RPO (복구 지점 목표)가 30 초를 넘는 거의 동기 복제
- 단일 또는 다중 계층 애플리케이션에 대한 앱 일관성 스냅샷.
- SQL Server AlwaysOn과의 통합 및 다른 응용 프로그램 수준 복제 기술과의 파트너 관계 예를 들어 복제, SQL AlwaysOn 및 Exchange 데이터베이스 가용성 그룹 (Dag)을 Active Directory.
- 유연한 복구 계획을 통해 한 번의 클릭으로 전체 응용 프로그램 스택을 복구 하 고 계획에 외부 스크립트 및 수동 작업을 포함할 수 있습니다.
- Site Recovery 및 Azure의 고급 네트워크 관리를 통해 앱 네트워크 요구 사항을 간소화 합니다. 낮은 RTO (복구 시간 목표) 네트워크 전환을 위해을 위해 IP 주소를 예약 하 고, 부하 분산을 구성 하 고, Azure Traffic Manager와 통합 하는 기능과 같은 네트워크 관리
- 다운로드하고 복구 계획과 통합할 수 있는 프로덕션 준비된 애플리케이션 특정 스크립트를 제공하는 다양한 자동화 라이브러리입니다.

## <a name="workload-summary"></a>워크로드 요약

Site Recovery는 지원되는 컴퓨터에서 실행 중인 모든 앱을 복제할 수 있습니다. 제품 팀과 협력 하 여 다음 표에 지정 된 앱에 대 한 추가 테스트를 수행 했습니다.

| **작업** |**Azure VM을 Azure에 복제** |**Hyper-V VM을 보조 사이트로 복제** | **Hyper-V VM을 Azure에 복제** | **VMware VM을 보조 사이트에 복제** | **VMware VM을 Azure에 복제** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |예 |예 |예 |예 |예|
| 웹앱(IIS, SQL) |예 |예 |예 |예 |예|
| System Center Operations Manager |예 |예 |예 |예 |예|
| SharePoint |예 |예 |예 |예 |예|
| SAP<br/><br/>비 클러스터에 대해 Azure에 SAP 사이트 복제 |예 (Microsoft에서 테스트) |예 (Microsoft에서 테스트) |예 (Microsoft에서 테스트) |예 (Microsoft에서 테스트) |예 (Microsoft에서 테스트)|
| Exchange(비 DAG) |예 |예 |예 |예 |예|
| 원격 데스크톱/VDI |예 |예 |예 |예 |예|
| Linux(운영 체제 및 앱) |예 (Microsoft에서 테스트) |예 (Microsoft에서 테스트) |예 (Microsoft에서 테스트) |예 (Microsoft에서 테스트) |예 (Microsoft에서 테스트)|
| Dynamics AX |예 |예 |예 |예 |예|
| Windows 파일 서버 |예 |예 |예 |예 |예|
| Citrix XenApp 및 XenDesktop |예|해당 없음 |예 |해당 없음 |예 |

## <a name="replicate-active-directory-and-dns"></a>Active Directory 및 DNS 복제

Active Directory 및 DNS 인프라는 대부분의 엔터프라이즈 앱에 필수적입니다. 재해 복구 중에 워크 로드 및 앱을 복구 하기 전에 이러한 인프라 구성 요소를 보호 하 고 복구 해야 합니다.

사이트 복구를 사용하여 Active Directory 및 DNS에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 예를 들어 기본 사이트에서 보조 사이트로 SharePoint 및 SAP를 장애 조치 (failover) 하려면 먼저 Active Directory 장애 조치 (failover)를 장애 조치 (failover) 하는 복구 계획을 설정 하면 됩니다. 그런 다음 추가 앱 별 복구 계획을 사용 하 여 Active Directory를 사용 하는 다른 앱을 장애 조치 (failover) 합니다.

Active Directory 및 DNS에 대 한 재해 복구에 대해 [자세히 알아보세요](site-recovery-active-directory.md) .

## <a name="protect-sql-server"></a>SQL Server 보호

SQL Server은 온-프레미스 데이터 센터의 많은 비즈니스 앱에 대 한 데이터 서비스 기반을 제공 합니다. Site Recovery를 사용 하 여 SQL Server를 사용 하는 다중 계층 엔터프라이즈 앱을 보호할 수 있습니다 SQL Server HA/DR 기술

사이트 복구는 다음을 제공합니다.

- SQL Server에 대해 간단하고 비용 효율적인 재해 복구 솔루션. SQL Server 독립 실행형 서버 및 클러스터의 여러 버전 및 에디션을 Azure 또는 보조 사이트로 복제합니다.
- SQL AlwaysOn 가용성 그룹 통합은 Azure Site Recovery 복구 계획을 통해 장애 조치 및 장애 복구를 관리합니다.
- SQL Server 데이터베이스를 포함한 애플리케이션의 모든 계층에 대한 엔드투엔드 복구 계획.
- Azure에서 더 큰 IaaS 가상 머신 크기로 _버스트_ 하 여 Site Recovery를 사용 하 여 최대 부하에 대 한 SQL Server를 확장 합니다.
- SQL Server 재해 복구의 간편한 테스트. 테스트 장애 조치를 실행하여 프로덕션 환경에 영향을 주지 않고 데이터를 분석하고 규정 준수 검사를 실행할 수 있습니다.

SQL server에 대 한 재해 복구에 대해 [자세히 알아보세요](site-recovery-sql.md) .

## <a name="protect-sharepoint"></a>SharePoint 보호

Azure Site Recovery를 사용하면 다음과 같이 SharePoint 배포를 보호할 수 있습니다.

- 재해 복구를 위한 대기 팜에 대한 필요성 및 관련된 인프라 비용을 제거합니다. Site Recovery를 사용 하 여 전체 팜 (웹, 앱 및 데이터베이스 계층)을 Azure 또는 보조 사이트에 복제할 수 있습니다.
- 애플리케이션 배포 및 관리를 간소화합니다. 기본 사이트에 배포 된 업데이트는 자동으로 복제 됩니다. 보조 사이트에서 팜의 장애 조치 (failover) 및 복구 후 업데이트를 사용할 수 있습니다. 는 독립 실행형 팜을 최신으로 유지 하는 것과 관련 된 관리 복잡성과 비용을 절감 합니다.
- 테스트 및 디버깅에 프로덕션 환경과 유사한 복사본 주문형 복사본 환경을 만들어 SharePoint 애플리케이션 개발 및 테스트를 용이하게 합니다.
- 사이트 복구를 사용하여 SharePoint 배포를 Azure에 마이그레이션하여 전환을 클라우드로 단순화합니다.

SharePoint 용 재해 복구에 대해 [자세히 알아보세요](site-recovery-sharepoint.md) .

## <a name="protect-dynamics-ax"></a>Dynamics AX 보호

Azure Site Recovery를 사용하면 다음과 같이 Dynamics AX ERP 솔루션을 보호하도록 합니다.

- Azure 또는 보조 사이트에 대 한 전체 Dynamics AX 환경 (웹 및 AOS 계층, 데이터베이스 계층, SharePoint)의 복제를 관리 합니다.
- Dynamics AX 배포의 클라우드(Azure)로 마이그레이션을 간소화합니다.
- 테스트 및 디버깅에 프로덕션 환경과 유사한 복사본 주문형을 만들어 Dynamics AX 애플리케이션 개발 및 테스트를 용이하게 합니다.

동적 AX의 재해 복구에 대해 [자세히 알아보세요](site-recovery-dynamicsax.md) .

## <a name="protect-remote-desktop-services"></a>원격 데스크톱 서비스 보호

RDS (원격 데스크톱 서비스)는 사용자가 어디에서 나 작업할 수 있도록 하는 VDI (가상 데스크톱 인프라), 세션 기반 데스크톱 및 응용 프로그램을 사용 하도록 설정 합니다.

Azure Site Recovery를 사용 하 여 다음 서비스를 복제할 수 있습니다.

- 관리 되거나 관리 되지 않는 풀링된 가상 데스크톱을 보조 사이트에 복제 합니다.
- 원격 응용 프로그램 및 세션을 보조 사이트 또는 Azure에 복제 합니다.

다음 표에서는 복제 옵션을 보여 줍니다.

| **RDS** |**Azure VM을 Azure에 복제** | **Hyper-V VM을 보조 사이트로 복제** | **Hyper-V VM을 Azure에 복제** | **VMware VM을 보조 사이트에 복제** | **VMware VM을 Azure에 복제** | **물리적 서버를 보조 사이트에 복제** | **물리적 서버를 Azure에 복제** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **풀링된 가상 데스크톱 (관리 되지 않는)** |아니요|예 |아니요 |예 |아니요 |예 |아니요 |
| **풀링된 가상 데스크톱(관리됨/UPD 없음)** |아니요|예 |아니요 |예 |아니요 |예 |아니요 |
| **원격 애플리케이션 및 데스크톱 세션(UPD 없음)** |예|예 |예 |예 |예 |예 |예 |

RDS에 대 한 재해 복구에 대해 [자세히 알아보세요](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) .

## <a name="protect-exchange"></a>Exchange 보호

Site Recovery를 사용하면 다음과 같이 Exchange를 보호할 수 있습니다.

- 단일 또는 독립 실행형 서버와 같은 소규모 Exchange 배포의 경우 Site Recovery가 Azure 또는 보조 사이트로 복제하고 장애 조치(failover)할 수 있습니다.
- 대규모 배포의 경우 Site Recovery가 Exchange DAGS와 통합됩니다.
- Exchange DAG는 엔터프라이즈에서 Exchange 재해 복구를 위한 권장된 솔루션입니다.  Site Recovery의 복구 계획은 DAG를 포함하여 사이트에 걸쳐 DAG 장애 조치를 오케스트레이션할 수 있습니다.

Exchange에 대 한 재해 복구에 대해 자세히 알아보려면 [Exchange dag](/Exchange/high-availability/database-availability-groups/database-availability-groups) and [exchange 재해 복구](/Exchange/high-availability/disaster-recovery/disaster-recovery)를 참조 하세요.

## <a name="protect-sap"></a>SAP 보호

Site Recovery를 사용하여 다음과 같이 SAP 배포를 보호합니다.

- 구성 요소를 Azure에 복사하여 온-프레미스로 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 애플리케이션 보호를 활성화합니다.
- 구성 요소를 다른 Azure 데이터 센터에 복사하여 Azure를 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 애플리케이션 보호를 활성화합니다.
- Site Recovery를 사용하여 클라우드 마이그레이션을 간소화하여 Azure에 SAP 배포를 마이그레이션합니다.
- SAP 애플리케이션 테스팅을 위해 온디맨드로 프로덕션 클론을 만들어 SAP 프로젝트 업그레이드, 테스트 및 프로토타입 생성을 단순화합니다.

SAP 용 재해 복구에 대해 [자세히 알아보세요](site-recovery-sap.md) .

## <a name="protect-internet-information-services"></a>인터넷 정보 서비스 보호

Site Recovery를 사용 하 여 다음과 같이 인터넷 정보 서비스 (IIS) 배포를 보호 합니다.

Azure Site Recovery는 환경에서 중요한 구성 요소를 콜드 원격 사이트 또는 Microsoft Azure와 같은 퍼블릭 클라우드에 복제하여 재해 복구를 제공합니다. 웹 서버와 데이터베이스를 포함 하는 가상 머신은 복구 사이트로 복제 되므로 구성 파일 또는 인증서에 대 한 별도의 백업을 요구 하지 않습니다. 변경된 사후 장애 조치인 환경 변수에 따라 달라지는 애플리케이션 매핑 및 바인딩은 재해 복구 계획에 통합되는 스크립트를 통해 업데이트될 수 있습니다. 가상 머신은 장애 조치 (failover) 중에만 복구 사이트에 설치 됩니다. Azure Site Recovery를 사용 하면 다음과 같은 기능을 제공 하 여 종단 간 장애 조치를 오케스트레이션 할 수 있습니다.

- 다양한 계층에서 가상 머신의 종료 및 시작 순서를 지정합니다.
- 가상 컴퓨터를 시작한 후 응용 프로그램 종속성 및 바인딩 업데이트를 허용 하는 스크립트를 추가 합니다. 스크립트는 복구 사이트를 가리키기 위해 DNS 서버를 업데이트하는 데 사용할 수 있습니다.
- 기본 및 복구 네트워크를 매핑하고 장애 조치 (failover) 후 업데이트 하지 않아도 되는 스크립트를 사용 하 여 가상 머신 사전 장애 조치 (failover)에 IP 주소를 할당 합니다.
- 재해 발생 시 혼동 범위를 제거 하는 여러 웹 응용 프로그램에 대해 한 번 클릭으로 장애 조치 (failover) 할 수 있습니다.
- DR 드릴의 격리된 환경에서 복구 계획을 테스트하는 기능입니다.

IIS에 대 한 재해 복구에 대해 [자세히 알아보세요](site-recovery-iis.md) .

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp 및 XenDesktop 보호

Site Recovery를 사용하여 음과 같이 Citrix XenApp와 XenDesktop 배포를 보호합니다.

- Citrix XenApp 및 XenDesktop 배포에 대 한 보호를 사용 하도록 설정 합니다. 여러 배포 계층을 Azure에 복제 합니다. Active Directory, DNS 서버, SQL database 서버, Citrix Delivery Controller, StoreFront server, XenApp Master (VDA), Citrix XenApp License Server.
- Citrix XenApp 및 XenDesktop 배포를 Azure로 마이그레이션하기 위해 Site Recovery를 사용하여 클라우드 마이그레이션을 간소화합니다.
- 테스트 및 디버깅을 위해 프로덕션 환경과 유사한 주문형 복사본을 만들어 Citrix XenApp/XenDesktop 테스트를 간소화합니다.
- 이 솔루션은 Windows Server 가상 데스크톱에만 적용 되 고 클라이언트 가상 데스크톱에는 적용 되지 않습니다. 클라이언트 가상 데스크톱은 아직 Azure에서 라이선스를 지원 하지 않습니다. Azure의 클라이언트/서버 데스크톱용 라이선스에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/licensing-faq/).

Citrix XenApp 및 XenDesktop 배포에 대 한 재해 복구에 대해 [자세히 알아보세요](site-recovery-citrix-xenapp-and-xendesktop.md) . 또는 [Citrix 백서](https://aka.ms/citrix-xenapp-xendesktop-with-asr)를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure VM에 대 한 재해 복구에 대해 [자세히 알아보세요](azure-to-azure-quickstart.md) .
