---
title: 관리형 인스턴스 FAQ
description: SQL Database 관리 인스턴스 자주 묻는 질문(FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364159"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database 관리 인스턴스 자주 묻는 질문(FAQ)

이 문서에는 [SQL Database 관리 인스턴스에](sql-database-managed-instance.md)대한 가장 일반적인 질문이 많이 포함되어 있습니다.

## <a name="supported-features"></a>지원되는 기능

**관리되는 인스턴스에서 지원되는 기능 목록은 어디에서 찾을 수 있습니까?**

관리되는 인스턴스에서 지원되는 기능 목록은 [Azure SQL Database 및 SQL Server](sql-database-features.md)를 참조하십시오.

Azure SQL Database 관리 인스턴스와 온-프레미스 SQL Server 간의 구문 및 동작 의 차이점은 SQL Server 의 [T-SQL 차이점을](sql-database-managed-instance-transact-sql-information.md)참조하십시오.


## <a name="tech-spec--resource-limits"></a>기술 사양 & 리소스 제한
 
**관리되는 인스턴스의 기술적 특성 및 리소스 제한은 어디에서 찾을 수 있습니까?**

사용 가능한 하드웨어 생성 특성은 [하드웨어 세대의 기술적 차이점을](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)참조하십시오.
사용 가능한 서비스 계층 및 해당 특성은 [서비스 계층 간의 기술적 차이점을](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)참조하십시오.

## <a name="known-issues--bugs"></a>버그에 & 알려진 문제

**알려진 문제와 버그는 어디에서 찾을 수 있습니까?**

버그 및 알려진 문제의 경우 [알려진 문제를](sql-database-release-notes.md#known-issues)참조하십시오.

## <a name="new-features"></a>새로운 기능

**공개 미리 보기에서 최신 기능과 기능을 어디에서 찾을 수 있습니까?**

새 기능 및 미리 보기 기능에 대한 [자세한 내용은 릴리스 참조.](sql-database-release-notes.md?tabs=managed-instance)

## <a name="deployment-times"></a>배포 시간 

**인스턴스를 만들거나 업데이트하거나 데이터베이스를 복원하는 데 얼마나 많은 시간이 소요되나요?**

새 관리되는 인스턴스를 만들거나 서비스 계층(vCores, 저장소)을 변경하는 예상 시간은 여러 가지 요인에 따라 달라집니다. [관리 운영](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 살펴보기 

## <a name="naming-convention"></a>명명 규칙

**관리되는 인스턴스의 이름이 온-프레미스 SQL Server와 같을 수 있습니까?**

관리되는 인스턴스 이름 변경은 지원되지 않습니다.

관리되는 인스턴스 기본 DNS 영역 *.database.windows.net* 변경할 수 있습니다. 

기본값 대신 다른 DNS 영역을 사용하려면 *.contoso.com*. 
- CliConfig를 사용하여 별칭을 정의합니다. 이 도구는 레지스트리 설정 래퍼일 뿐이므로 그룹 정책이나 스크립트를 사용하여 수행할 수도 있습니다.
- *트러스트 서버인증서=true* 옵션과 함께 *CNAME을* 사용합니다.

## <a name="move-db-from-mi"></a>MI에서 DB 이동 

**데이터베이스를 관리되는 인스턴스에서 SQL Server 또는 Azure SQL 데이터베이스로 다시 이동하려면 어떻게 해야 합니까?**

데이터베이스를 [BACPAC로 내보낸](sql-database-export.md) 다음 [BACPAC 파일을 가져올]( sql-database-import.md)수 있습니다. 데이터베이스가 100GB보다 작은 경우 이 방법을 권장합니다.

데이터베이스의 모든 테이블에 기본 키가 있는 경우 트랜잭션 복제를 사용할 수 있습니다.

관리되는 인스턴스는 SQL Server에 비해 데이터베이스 버전이 더 높기 때문에 관리되는 인스턴스에서 가져온 기본 `COPY_ONLY` 백업은 SQL Server로 복원할 수 없습니다.

## <a name="migrate-instance-db"></a>인스턴스 DB 마이그레이션

**인스턴스 데이터베이스를 단일 Azure SQL 데이터베이스로 마이그레이션하려면 어떻게 해야 합니까?**

한 가지 옵션은 [데이터베이스를 BACPAC로 내보낸](sql-database-export.md) 다음 [BACPAC 파일을 가져오는](sql-database-import.md)것입니다. 

데이터베이스가 100GB보다 작은 경우 이 방법을 권장합니다. 데이터베이스의 모든 테이블에 기본 키가 있는 경우 트랜잭션 복제를 사용할 수 있습니다.

## <a name="switch-hardware-generation"></a>스위치 하드웨어 생성 

**관리되는 인스턴스 하드웨어 생성을 Gen 4세대와 5세대 로 온라인으로 전환할 수 있습니까?**

관리되는 인스턴스가 프로비전된 리전에서 두 하드웨어 세대를 모두 사용할 수 있는 경우 하드웨어 세대 간의 자동 온라인 전환이 가능합니다. 이 경우 하드웨어 세대 간에 전환하는 방법을 설명하는 [vCore 모델 개요 페이지를](sql-database-service-tiers-vcore.md) 확인할 수 있습니다.

새 관리되는 인스턴스가 백그라운드에서 프로비전되고 프로세스가 끝날 때 빠른 장애 조치와 함께 이전 인스턴스와 새 인스턴스 간에 데이터베이스가 자동으로 전송되기 때문에 장기 실행 작업입니다. 

두 하드웨어 세대가 동일한 지역에서 지원되지 않는 경우 하드웨어 생성을 변경할 수 있지만 수동으로 수행해야 합니다. 따라서 원하는 하드웨어 생성을 사용할 수 있는 리전에서 새 인스턴스를 프로비전하고 이전 인스턴스와 새 인스턴스 간에 데이터를 수동으로 백업하고 복원해야 합니다.


## <a name="tune-performance"></a>성능 조정

**관리되는 인스턴스의 성능을 조정하려면 어떻게 해야 합니까?**

범용 관리형 인스턴스는 데이터 및 로그 파일의 크기로 인해 성능이 중요한 원격 저장소를 사용합니다. 자세한 내용은 [범용 관리 인스턴스 성능에 대한 로그 파일 크기의 영향을](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)참조하십시오.

워크로드가 많은 작은 트랜잭션으로 구성된 경우 연결 유형을 프록시에서 리디렉션 모드로 전환하는 것이 좋습니다.

## <a name="maximum-storage-size"></a>최대 스토리지 크기

**관리되는 인스턴스의 최대 저장소 크기는 무엇입니까?**

관리되는 인스턴스의 저장소 크기는 선택한 서비스 계층(범용 또는 비즈니스 중요)에 따라 다릅니다. 이러한 서비스 계층의 저장소 제한은 [서비스 계층 특성을](sql-database-service-tiers-general-purpose-business-critical.md)참조하십시오.

## <a name="back-up-storage-cost"></a>스토리지 비용 백업 

**관리되는 인스턴스 저장소에서 백업 저장소가 차감되나요?**

아니요, 백업 저장소는 관리되는 인스턴스 저장소 공간에서 차감되지 않습니다. 백업 저장소는 인스턴스 저장소 공간과 독립적이며 크기가 제한되지 않습니다. 백업 저장소는 인스턴스 데이터베이스의 백업을 유지하는 기간에 따라 제한되어 있으며 7일에서 35일까지 구성할 수 있습니다. 자세한 내용은 [자동 백업 을 참조하십시오.](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)

## <a name="track-billing"></a>청구 추적

**관리되는 인스턴스의 청구 비용을 추적할 수 있는 방법이 있습니까?**

Azure 비용 관리 [솔루션을](/azure/cost-management/)사용하여 이렇게 할 수 있습니다. [Azure 포털의](https://portal.azure.com) **구독으로** 이동하여 **비용 분석을**선택합니다. 

누적 **비용** 옵션을 사용한 다음 **리소스 유형별로** 필터링합니다. `microsoft.sql/managedinstances` 
  
## <a name="inbound-nsg-rules"></a>인바운드 NSG 규칙

**관리 포트에 대한 인바운드 NSG 규칙을 설정하면 어떻게 해야 합니까?**

관리되는 인스턴스 제어 평면은 관리 포트를 보호하는 NSG 규칙을 유지 관리합니다.

관리 포트가 사용되는 내용은 다음과 같습니다.

포트 9000 및 9003은 서비스 패브릭 인프라에서 사용됩니다. 서비스 패브릭의 주요 역할은 가상 클러스터를 정상 상태로 유지하고 구성 요소 복제본 수 측면에서 목표 상태를 유지하는 것입니다.

포트 1438, 1440 및 1452는 노드 에이전트에서 사용됩니다. 노드 에이전트는 클러스터 내에서 실행되는 응용 프로그램으로 제어 평면에서 관리 명령을 실행하는 데 사용됩니다.

NSG 규칙 외에도 기본 제공 방화벽은 네트워크 계층의 인스턴스를 보호합니다. 응용 프로그램 계층에서 통신은 인증서로 보호됩니다.
  
자세한 정보 및 기본 제공 방화벽을 확인하는 방법은 [Azure SQL Database 관리 형 인스턴스 기본 제공 방화벽](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)을 참조하십시오.


## <a name="mitigate-data-exfiltration-risks"></a>데이터 유출 위험 완화  

**데이터 유출 위험을 완화하면 어떻게 해야 합니까?**

데이터 유출 위험을 완화하려면 다음과 같은 보안 설정 및 컨트롤 집합을 적용하는 것이 좋습니다.

- 모든 데이터베이스에서 [투명 데이터 암호화(TDE)를](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) 켭니다.
- 공통 언어 런타임(CLR)을 끕니다. 온-프레미스에도 권장됩니다.
- Azure Active Directory(AAD) 인증만 사용합니다.
- 권한이 낮은 DBA 계정으로 인스턴스에 액세스합니다.
- 시스템 관리자 계정에 대한 JiT 점프박스 액세스를 구성합니다.
- SQL [감사를](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)켜고 경고 메커니즘과 통합합니다.
- [고급 데이터 보안(ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 제품군에서 [위협 탐지를](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) 켭니다.


## <a name="cost-saving-use-cases"></a>비용 절감 사용 사례

**관리형 인스턴스를 통해 사용 사례 및 그로 인한 비용 절감은 어디에서 찾을 수 있습니까?**

관리형 사례 사례 연구:

- [고마쓰](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [전원 세부 정보](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [모든 스크립트](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Azure SQL Database 관리 인스턴스 배포와 관련된 이점, 비용 및 위험을 더 잘 이해하기 위해 Forrester의 연구 결과인 [MI의 총 경제적 영향도](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)있습니다.


## <a name="dns-refresh"></a>DNS 새로 고침 

**DNS 새로 고칠 수 있습니까?**

현재 관리되는 인스턴스에 대한 DNS 서버 구성을 새로 고치는 기능은 제공하지 않습니다.

DNS 구성은 결국 새로 고쳐집니다.

- DHCP 임대가 만료되는 경우.
- 플랫폼 업그레이드 시.

해결 방법을 사용하여 관리되는 인스턴스를 4 vCore로 다운그레이드하고 나중에 다시 업그레이드합니다. 이는 DNS 구성을 새로 고치는 부작용이 있습니다.


## <a name="ip-address"></a>IP 주소

**IP 주소를 사용하여 관리형 인스턴스에 연결할 수 있습니까?**

IP 주소를 사용하여 관리되는 인스턴스에 연결하는 것은 지원되지 않습니다. 관리되는 인스턴스 호스트 이름 맵을 사용하여 관리되는 인스턴스 가상 클러스터 앞에서 밸런서를 로드합니다. 하나의 가상 클러스터가 여러 개의 관리되는 인스턴스 연결을 호스트할 수 있기 때문에 이름을 지정하지 않고는 적절한 관리되는 인스턴스로 라우팅할 수 없습니다.

관리형 인스턴스 가상 클러스터 아키텍처에 대한 자세한 내용은 [가상 클러스터 연결 아키텍처를](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)참조하십시오.

**관리되는 인스턴스에 정적 IP 주소가 있을 수 있습니까?**

드물지만 필요한 경우 관리되는 인스턴스를 새 가상 클러스터로 온라인으로 마이그레이션해야 할 수 있습니다. 필요한 경우 이 마이그레이션은 서비스의 보안 및 안정성을 개선하기 위한 기술 스택의 변경 때문입니다. 새 가상 클러스터로 마이그레이션하면 관리되는 인스턴스 호스트 이름에 매핑된 IP 주소가 변경됩니다. 관리되는 인스턴스 서비스는 정적 IP 주소 지원을 주장하지 않으며 정기적인 유지 관리 주기의 일부로 예고 없이 변경할 수 있는 권리를 보유합니다.

따라서 불필요한 가동 중지 시간이 발생할 수 있으므로 IP 주소의 불변성에 의존하지 않는 것이 좋습니다.

## <a name="change-time-zone"></a>시간대 변경

**기존 관리형 인스턴스의 표준 시간대를 변경할 수 있습니까?**

표준 시간대 구성은 관리되는 인스턴스가 처음으로 프로비전될 때 설정할 수 있습니다. 기존 관리되는 인스턴스의 표준 시간대 변경은 지원되지 않습니다. 자세한 내용은 [표준 시간대 제한을](sql-database-managed-instance-timezone.md#limitations)참조하십시오.

해결 방법으로는 적절한 표준 시간대를 사용 하 여 새 관리 되는 인스턴스를 만든 다음 수동 백업 및 복원을 수행 하거나 권장 하는 것을 교차 [인스턴스 시점 복원을](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)수행 하는 것이 포함 됩니다.


## <a name="resolve-performance-issues"></a>성능 문제 해결

**관리되는 인스턴스의 성능 문제를 해결하려면 어떻게 해야 합니까?**

관리되는 인스턴스와 SQL Server 간의 성능 비교를 위해 좋은 출발점은 Azure SQL 관리 인스턴스와 SQL Server 문서 [간의 성능 비교를 위한 모범 사례입니다.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)

데이터 로드는 필수 전체 복구 모델과 트랜잭션 로그 쓰기 처리량 [제한으로](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) 인해 SQL Server보다 관리되는 인스턴스에서 더 느린 경우가 많습니다. 경우에 따라 일시적인 데이터를 사용자 데이터베이스 대신 tempdb로 로드하거나 클러스터된 columnstore 또는 메모리에 최적화된 테이블을 사용하여 이 작업을 해결할 수 있습니다.


## <a name="restore-encrypted-backup"></a>암호화된 백업 복원

**암호화된 데이터베이스를 관리되는 인스턴스로 복원할 수 있습니까?**

예. 데이터베이스를 관리되는 인스턴스로 복원하기 위해 데이터베이스의 암호를 해독할 필요가 없습니다. 암호화된 백업 파일에서 데이터를 읽을 수 있도록 관리되는 인스턴스에 원본 시스템의 암호화 키 보호기로 사용되는 인증서/키를 제공해야 합니다. 입력하는 방법에는 다음 두 가지가 있습니다.

- *관리되는 인스턴스에 인증서 보호기를 업로드합니다.* PowerShell을 사용하여 수행할 수 있습니다. [샘플 스크립트는](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) 전체 프로세스를 설명합니다.
- *AZURE 키 볼트(AKV)에 비대칭 키 프로텍터를 업로드하고 관리인스턴스를 가리킵니다.* 이 방법은 AKV 통합을 사용하여 암호화 키를 저장하는 BYOK(사용자 고유키) TDE 사용 사례와 유사합니다. 키를 암호화 키 보호기로 사용하지 않고 관리되는 인스턴스에서 암호화된 데이터베이스를 복원할 수 있도록 하려면 [BYOK TDE 설정](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)에 대한 지침을 따르고 *선택된 키를 기본 TDE 보호기로 설정하지*않습니다.

관리되는 인스턴스에서 암호화 보호기를 사용할 수 있게 하면 표준 데이터베이스 복원 절차를 진행할 수 있습니다.

## <a name="migrate-from-single-db"></a>단일 DB에서 마이그레이션 

**Azure SQL Database 단일 또는 탄력적 풀에서 관리되는 인스턴스로 마이그레이션하려면 어떻게 해야 합니까?**

관리형 인스턴스는 Azure SQL Database의 다른 배포 옵션과 동일한 계산 및 저장소 크기를 제공합니다. 단일 인스턴스에서 데이터를 통합하려는 경우 관리되는 인스턴스에서만 지원되는 기능이 필요한 경우 BACPAC(내보내기/가져오기) 기능을 사용하여 데이터를 마이그레이션할 수 있습니다.
