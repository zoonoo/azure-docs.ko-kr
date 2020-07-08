---
title: 질문과 대답(FAQ)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance FAQ (질문과 대답)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 9295c6e1daaad6346581b959a9b94a7ab74da44c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708861"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL Managed Instance FAQ (질문과 대답)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에는 [AZURE SQL Managed Instance](sql-managed-instance-paas-overview.md)에 대 한 가장 일반적인 질문이 포함 되어 있습니다.

## <a name="supported-features"></a>지원되는 기능

**SQL Managed Instance에서 지원 되는 기능 목록은 어디에서 찾을 수 있나요?**

SQL Managed Instance에서 지원 되는 기능 목록은 [AZURE SQL Managed Instance 기능](../database/features-comparison.md)을 참조 하세요.

Azure SQL Managed Instance와 SQL Server 간의 구문 및 동작과 차이점은 [SQL Server의 t-sql 차이점](transact-sql-tsql-differences-sql-server.md)을 참조 하세요.


## <a name="tech-spec--resource-limits"></a>기술 사양 & 리소스 제한
 
**SQL Managed Instance에 대 한 기술 특성 및 리소스 제한은 어디에서 확인할 수 있나요?**

사용 가능한 하드웨어 생성 특성은 [하드웨어 세대의 기술적 차이점](resource-limits.md#hardware-generation-characteristics)을 참조 하세요.
사용 가능한 서비스 계층 및 해당 특성에 대 한 자세한 내용은 [서비스 계층 간의 기술적 차이점](resource-limits.md#service-tier-characteristics)을 참조 하세요.

## <a name="known-issues--bugs"></a>알려진 문제 & 버그

**알려진 문제 및 버그는 어디서 찾을 수 있나요?**

버그 및 알려진 문제에 대해서는 [알려진 문제](../database/doc-changes-updates-release-notes.md#known-issues)를 참조 하세요.

## <a name="new-features"></a>새로운 기능

**공개 미리 보기에서 최신 기능과 기능을 어디에서 찾을 수 있나요?**

새 기능 및 미리 보기 기능에 대 한 자세한 내용은 [릴리스 정보](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)를 참조 하세요.

## <a name="deployment-times"></a>배포 시간 

**인스턴스를 만들거나 업데이트 하는 데 소요 되는 시간 또는 데이터베이스를 복원 하는 데 소요 되는 시간은 어느 정도 인가요?**

관리 되는 인스턴스를 만드는 데 필요한 시간 또는 변경 서비스 계층 (vCores, 저장소)은 여러 가지 요인에 따라 달라 집니다. [관리 작업](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations)에 대해 살펴보겠습니다. 

## <a name="naming-conventions"></a>명명 규칙

**관리 되는 인스턴스의 이름이 SQL Server 온-프레미스 인스턴스와 같을 수 있나요?**

관리 되는 인스턴스 이름 변경은 지원 되지 않습니다.

관리 되는 인스턴스의 기본 DNS *database.windows.net* 을 변경할 수 있습니다. 

기본값 대신 다른 DNS 영역을 사용 하려면 (예: *. contoso.com*) 
- CliConfig를 사용 하 여 별칭을 정의 합니다. 이 도구는 단지 레지스트리 설정 래퍼 이므로 그룹 정책 또는 스크립트를 사용 하 여 수행할 수도 있습니다.
- *Trustservercertificate = true* 옵션과 함께 *CNAME* 을 사용 합니다.

## <a name="move-a-database-from-sql-managed-instance"></a>SQL Managed Instance에서 데이터베이스 이동 

**SQL Managed Instance에서 SQL Server 또는 Azure SQL Database 데이터베이스를 다시 이동 하려면 어떻게 해야 하나요?**

[데이터베이스를 bacpac로 내보낸](../database/database-export.md) 다음 [bacpac 파일을 가져올](../database/database-import.md)수 있습니다. 데이터베이스가 100 GB 보다 작은 경우에 권장 되는 방법입니다.

데이터베이스의 모든 테이블에 기본 키가 있는 경우 트랜잭션 복제를 사용할 수 있습니다.

Sql `COPY_ONLY` Managed Instance SQL Server와 비교 하 여 데이터베이스 버전이 더 높기 때문에 sql Managed Instance에서 가져온 기본 백업을 SQL Server 복원할 수 없습니다.

## <a name="migrate-an-instance-database"></a>인스턴스 데이터베이스 마이그레이션

**내 인스턴스 데이터베이스를 Azure SQL Database 마이그레이션하려면 어떻게 해야 하나요?**

한 가지 옵션은 [데이터베이스를 bacpac로 내보낸](../database/database-export.md) 다음 [bacpac 파일을 가져오는](../database/database-import.md)것입니다. 

데이터베이스가 100 GB 보다 작은 경우에 권장 되는 방법입니다. 데이터베이스의 모든 테이블에 기본 키가 있는 경우 트랜잭션 복제를 사용할 수 있습니다.

## <a name="switch-hardware-generation"></a>하드웨어 생성 전환 

**SQL Managed Instance 하드웨어 생성을 Gen 4와 Gen 5 online 간에 전환할 수 있나요?**

SQL Managed Instance 프로 비전 된 지역에서 두 하드웨어 생성을 모두 사용할 수 있는 경우 하드웨어 세대 간에 자동 온라인 전환이 가능 합니다. 이 경우 하드웨어 세대를 전환 하는 방법을 설명 하는 [Vcore 모델 개요 페이지](../database/service-tiers-vcore.md)를 확인할 수 있습니다.

이 작업은 장기 실행 작업입니다. 새 관리 되는 인스턴스는 백그라운드에서 프로 비전 되 고 데이터베이스는 이전 인스턴스와 새 인스턴스 간에 자동으로 전송 되며 프로세스가 끝날 때 빠른 장애 조치 (failover)가 발생 합니다. 

**두 하드웨어 생성이 동일한 지역에서 지원 되지 않으면 어떻게 되나요?**

동일한 지역에서 두 하드웨어 생성이 모두 지원 되지 않는 경우 하드웨어 생성을 변경 하는 것이 가능 하지만이를 수동으로 수행 해야 합니다. 이렇게 하려면 원하는 하드웨어 생성을 사용할 수 있는 지역에 새 인스턴스를 프로 비전 하 고 이전 인스턴스와 새 인스턴스 간에 데이터를 수동으로 백업 하 고 복원 해야 합니다.

**업데이트 작업을 수행 하는 데 충분 한 IP 주소가 없는 경우 어떻게 되나요?**

관리 되는 인스턴스가 프로 비전 되는 서브넷의 IP 주소가 충분 하지 않은 경우 새 서브넷과 그 안에 새 관리 되는 인스턴스를 만들어야 합니다. 또한 향후 업데이트 작업에서 유사한 상황을 방지 하기 위해 더 많은 IP 주소를 할당 하 여 새 서브넷을 만드는 것이 좋습니다. (올바른 서브넷 크기의 경우 [VNet 서브넷의 크기를 확인 하는 방법](vnet-subnet-determine-size.md)을 참조 하세요.) 새 인스턴스를 프로 비전 한 후에는 이전 인스턴스와 새 인스턴스 사이에서 수동으로 데이터를 백업 및 복원 하거나 인스턴스 간 지정 [시간 복원을](point-in-time-restore.md?tabs=azure-powershell)수행할 수 있습니다. 


## <a name="tune-performance"></a>성능 조정

**SQL Managed Instance의 성능을 조정할 어떻게 할까요? 있나요?**

범용 계층의 SQL Managed Instance는 원격 저장소를 사용 하므로 데이터 및 로그 파일의 크기는 성능에 중요 합니다. 자세한 내용은 [일반적인 용도의 SQL Managed Instance 성능에 대 한 로그 파일 크기의 영향](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)을 참조 하세요.

워크 로드가 많은 작은 트랜잭션으로 구성 된 경우 연결 유형을 프록시에서 리디렉션 모드로 전환 하는 것이 좋습니다.

## <a name="maximum-storage-size"></a>최대 스토리지 크기

**SQL Managed Instance에 대 한 최대 저장소 크기는 얼마 인가요?**

SQL Managed Instance의 저장소 크기는 선택한 서비스 계층 (범용 또는 중요 비즈니스용)에 따라 달라 집니다. 이러한 서비스 계층의 저장소 제한 사항은 [서비스 계층 특성](../database/service-tiers-general-purpose-business-critical.md)을 참조 하세요.

## <a name="backup-storage-cost"></a>백업 스토리지 비용 

**백업 저장소는 SQL Managed Instance 저장소에서 공제?**

아니요, 백업 저장소는 SQL Managed Instance 저장소 공간에서 공제 되지 않습니다. 백업 저장소는 인스턴스 저장소 공간과 독립적 이며 크기가 제한 되지 않습니다. 백업 저장소는 7 일에서 35 일까지 구성할 수 있는 인스턴스 데이터베이스의 백업을 보존 하는 기간으로 제한 됩니다. 자세한 내용은 자동화 된 [백업](../database/automated-backups-overview.md)을 참조 하세요.

## <a name="track-billing"></a>청구 추적

**SQL Managed Instance에 대 한 청구 비용을 추적 하는 방법이 있나요?**

[Azure Cost Management 솔루션](/azure/cost-management/)을 사용 하 여이 작업을 수행할 수 있습니다. [Azure Portal](https://portal.azure.com) **구독** 으로 이동 하 여 **비용 분석**을 선택 합니다. 

**누적 비용** 옵션을 사용한 다음 **리소스 유형** 으로을 필터링 `microsoft.sql/managedinstances` 합니다. 
  
## <a name="inbound-nsg-rules"></a>인바운드 NSG 규칙

**관리 포트에서 인바운드 NSG 규칙을 설정 하려면 어떻게 해야 하나요?**

SQL Managed Instance 제어 평면은 관리 포트를 보호 하는 NSG 규칙을 유지 관리 합니다.

에 사용 되는 관리 포트는 다음과 같습니다.

포트 9000 및 9003은 Azure Service Fabric 인프라에서 사용 됩니다. Service Fabric 주 역할은 가상 클러스터를 정상 상태로 유지 하 고 구성 요소 복제본의 수를 기준으로 목표 상태를 유지 하는 것입니다.

노드 에이전트에서 1438, 1440 및 1452 포트를 사용 합니다. 노드 에이전트는 클러스터 내에서 실행 되는 응용 프로그램이 며, 제어 평면에서 관리 명령을 실행 하는 데 사용 됩니다.

NSG 규칙 외에도 기본 제공 방화벽은 네트워크 계층에서 인스턴스를 보호 합니다. 응용 프로그램 계층에서 통신은 인증서로 보호 됩니다.

자세한 내용과 기본 제공 방화벽을 확인 하는 방법에 대 한 자세한 내용은 [AZURE SQL Managed Instance 기본 제공 방화벽](management-endpoint-verify-built-in-firewall.md)을 참조 하세요.


## <a name="mitigate-data-exfiltration-risks"></a>데이터 반출 위험 완화  

**데이터 반출 위험을 완화 하려면 어떻게 해야 하나요?**

데이터 반출 위험을 완화 하기 위해 고객은 보안 설정 및 컨트롤 집합을 적용 하는 것이 좋습니다.

- 모든 데이터베이스에서 [TDE (투명한 데이터 암호화)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) 를 설정 합니다.
- CLR (공용 언어 런타임)을 해제 합니다. 이는 온-프레미스에도 권장 됩니다.
- Azure Active Directory (Azure AD) 인증만 사용 합니다.
- 권한이 낮은 DBA 계정으로 인스턴스에 액세스 합니다.
- Sysadmin 계정에 대 한 JIT jumpbox 액세스를 구성 합니다.
- [SQL 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)를 켜고 경고 메커니즘과 통합 합니다.
- [광고 (advanced data security)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 도구 모음에서 [위협 감지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) 를 설정 합니다.


## <a name="cost-saving-use-cases"></a>비용 절감 사용 사례

**SQL Managed Instance를 사용 하 여 사용 사례를 찾고 비용을 절감할 수 있는 위치**

SQL Managed Instance 사례 연구:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Azure SQL Managed Instance 배포와 관련 된 이점, 비용 및 위험에 대해 더 잘 이해 하기 위해 [Microsoft Azure SQL Database Managed Instance의 경제적 영향](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)을 Forrester 연구 합니다.


## <a name="dns-refresh"></a>DNS 새로 고침 

**DNS 새로 고침을 수행할 수 있나요?**

현재 SQL Managed Instance에 대 한 DNS 서버 구성을 새로 고치는 기능을 제공 하지 않습니다.

DNS 구성은 결국 새로 고쳐집니다.

- DHCP 임대가 만료 되는 경우
- 플랫폼 업그레이드 시.

해결 방법으로 SQL Managed Instance를 4 개 vCores로 다운 그레이드 하 고 나중에 다시 업그레이드 합니다. DNS 구성을 새로 고치면 부작용이 발생 합니다.


## <a name="ip-address"></a>IP 주소

**IP 주소를 사용 하 여 SQL Managed Instance에 연결할 수 있나요?**

IP 주소를 사용 하 여 SQL Managed Instance에 연결 하는 것은 지원 되지 않습니다. Sql Managed Instance 호스트 이름은 SQL Managed Instance 가상 클러스터 앞의 부하 분산 장치에 매핑됩니다. 하나의 가상 클러스터가 여러 관리 되는 인스턴스를 호스트할 수 있으므로 이름을 명시적으로 지정 하지 않고 연결을 적절 한 관리 되는 인스턴스로 라우팅할 수 없습니다.

SQL Managed Instance 가상 클러스터 아키텍처에 대 한 자세한 내용은 [가상 클러스터 연결 아키텍처](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)를 참조 하세요.

**SQL Managed Instance 고정 IP 주소를 가질 수 있나요?**

드물지만 필요한 경우에는 SQL Managed Instance를 새 가상 클러스터로 온라인으로 마이그레이션하는 것이 필요할 수 있습니다. 필요한 경우이 마이그레이션은 서비스의 보안 및 안정성을 향상 시키기 위한 기술 스택의 변경 내용 때문에 발생 합니다. 새 가상 클러스터로 마이그레이션하면 SQL Managed Instance 호스트 이름에 매핑되는 IP 주소가 변경 됩니다. SQL Managed Instance 서비스는 고정 IP 주소 지원을 주장 하지 않으며 정기 유지 관리 주기 중에 통지 없이이를 변경할 권리를 보유 합니다.

따라서 불필요 한 가동 중지 시간이 발생할 수 있으므로 IP 주소의 불변성에 의존 하지 않는 것이 좋습니다.

## <a name="change-time-zone"></a>표준 시간대 변경

**기존 관리 되는 인스턴스의 표준 시간대를 변경할 수 있나요?**

관리 되는 인스턴스가 처음으로 프로 비전 되 면 표준 시간대 구성을 설정할 수 있습니다. 기존 관리 되는 인스턴스의 표준 시간대를 변경 하는 것은 지원 되지 않습니다. 자세한 내용은 [표준 시간대 제한 사항](timezones-overview.md#limitations)을 참조 하세요.

해결 방법에는 적절 한 표준 시간대를 사용 하 여 관리 되는 인스턴스를 새로 만들고, 수동 백업 및 복원을 수행 하거나, [인스턴스 간 지정 시간 복원을](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)수행 하는 것이 포함 됩니다.


## <a name="resolve-performance-issues"></a>성능 문제 해결

**SQL Managed Instance의 성능 문제를 해결 어떻게 할까요??**

SQL Managed Instance와 SQL Server 간에 성능 비교를 수행 하는 데 좋은 출발점은 [AZURE SQL Managed Instance와 SQL Server 간의 성능 비교를 위한 모범 사례](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)입니다.

데이터 로드는 필수적인 전체 복구 모델 및 트랜잭션 로그 쓰기 처리량에 대 한 [제한](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) 으로 인해 SQL SERVER 보다 SQL Managed Instance에서 속도가 느립니다. 경우에 따라 사용자 데이터베이스 대신 tempdb로 임시 데이터를 로드 하거나 클러스터형 columnstore 또는 메모리 최적화 테이블을 사용 하 여이 작업을 수행할 수 있습니다.


## <a name="restore-encrypted-backup"></a>암호화 된 백업 복원

**암호화 된 데이터베이스를 SQL Managed Instance로 복원할 수 있나요?**

예, 데이터베이스를 암호 해독 하 여 SQL Managed Instance로 복원할 필요가 없습니다. 암호화 된 백업 파일에서 데이터를 읽을 수 있으려면 원본 시스템의 암호화 키 보호기로 사용 되는 인증서/키를 SQL Managed Instance에 제공 해야 합니다. 입력하는 방법에는 다음 두 가지가 있습니다.

- *인증서-보호기를 SQL Managed Instance에 업로드*합니다. PowerShell을 사용 하 여이 작업을 수행할 수 있습니다. [샘플 스크립트](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) 는 전체 프로세스를 설명 합니다.
- *비대칭 키-보호기를 Azure Key Vault으로 업로드 하 고 SQL Managed Instance를 가리키도록*합니다. 이 방법은 Key Vault 통합을 사용 하 여 암호화 키를 저장 하는 BYOK (사용자 소유의 키) TDE 사용 사례와 비슷합니다. 키를 암호화 키 보호기로 사용 하지 않고 SQL Managed Instance에서 암호화 된 데이터베이스를 복원 하는 데 사용할 수 있는 키만 만들려면 [BYOK TDE를 설정](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)하는 방법에 대 한 지침을 따르고 **선택 된 키를 기본 tde 보호기**로 설정 확인란을 선택 하지 않습니다.

SQL Managed Instance에서 암호화 보호기를 사용할 수 있도록 설정한 후 표준 데이터베이스 복원 절차를 진행할 수 있습니다.

## <a name="migrate-from-sql-database"></a>SQL Database에서 마이그레이션 

**Azure SQL Database에서 SQL Managed Instance로 마이그레이션하려면 어떻게 해야 하나요?**

SQL Managed Instance는 계산 및 저장소 크기 마다 동일한 성능 수준을 Azure SQL Database 제공 합니다. 단일 인스턴스에서 데이터를 통합 하거나 단순히 SQL Managed Instance 에서만 지원 되는 기능이 필요한 경우에는 BACPAC (내보내기/가져오기) 기능을 사용 하 여 데이터를 마이그레이션할 수 있습니다.

## <a name="password-policy"></a>암호 정책 

**SQL Managed Instance SQL 로그인에 적용 되는 암호 정책은 무엇입니까?**

Sql 로그인에 대 한 SQL Managed Instance 암호 정책은 관리 되는 인스턴스를 보유 하는 가상 클러스터를 형성 하는 Vm에 적용 되는 Azure platform 정책을 상속 합니다. 현재 이러한 설정은 Azure에서 정의 되 고 관리 되는 인스턴스에 상속 되므로 이러한 설정은 변경할 수 없습니다.

 > [!IMPORTANT]
 > Azure 플랫폼은 해당 정책을 사용 하 여 서비스에 알리지 않고 정책 요구 사항을 변경할 수 있습니다.

**현재 Azure platform 정책 이란?**

각 로그인은 로그인 시 암호를 설정 하 고 최대 사용 기간에 도달 하면 암호를 변경 해야 합니다.

| **정책** | **보안 설정** |
| --- | --- |
| 최대 암호 사용 기간 | 42 일 |
| 최소 암호 사용 기간 | 1일 |
| 최소 암호 길이 | 10 자 |
| 암호는 복잡성 조건을 만족해야 함 | 사용 |

**로그인 수준에서 SQL Managed Instance의 암호 복잡성 및 만료를 사용 하지 않도록 설정할 수 있나요?**

예, 로그인 수준에서 CHECK_POLICY 및 CHECK_EXPIRATION 필드를 제어할 수 있습니다. 다음 T-sql 명령을 실행 하 여 현재 설정을 확인할 수 있습니다.

```sql
SELECT *
FROM sys.sql_logins
```

그런 후 다음을 실행 하 여 지정 된 로그인 설정을 수정할 수 있습니다.

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(' test '를 원하는 로그인 이름으로 바꾸고 정책 및 만료 값을 조정 합니다.)
