---
title: SQL Database 관리 인스턴스 FAQ | Microsoft Docs
description: SQL Database 관리 되는 인스턴스에 대 한 질문과 대답 (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798074"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database 관리 되는 인스턴스에 대 한 질문과 대답 (FAQ)

이 문서에서는 다양 한 가장 일반적인 질문에 대 한 포함 [SQL Database 관리 되는 인스턴스](sql-database-managed-instance.md)합니다.

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>관리 되는 인스턴스에서 지원 되는 기능 목록은 어디서 찾을 수 있습니까?

관리 되는 인스턴스에서 지원 되는 기능의 목록을 보려면 참조 [Azure SQL Database 및 SQL Server](sql-database-features.md)합니다.

구문과 동작에 Azure SQL Database 관리 되는 인스턴스 및 온-프레미스 SQL Server 간의 차이점에 대 한 참조 [SQL Server에서 T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)합니다.


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>관리 되는 인스턴스에 대 한 기술 특성 및 리소스 제한을 찾을 수는 위치

사용 가능한 하드웨어 세대 특성을 참조 하세요 [하드웨어 세대에 대 한 기술 차이](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)합니다.
사용 가능한 서비스 계층 및 해당 특성에 대 한 참조 [서비스 계층 간에 기술적인 차이점](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)합니다.

## <a name="where-can-i-find-known-issues-and-bugs"></a>알려진된 문제 및 버그는 어디서 찾을 수 있습니까?

버그 및 알려진된 문제에 대 한 참조 [동작 변경 내용](sql-database-managed-instance-transact-sql-information.md#Changes) 하 고 [알려진 문제](sql-database-managed-instance-transact-sql-information.md#Issues)합니다.


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>관리 되는 인스턴스를 온-프레미스 SQL Server와 동일한 이름을 가질 수 있습니까?

관리 되는 인스턴스로 끝나는 이름이 있어야 *database.windows.net*합니다. 예를 들어 기본값 대신 다른 DNS 영역을 사용 하도록 **다른 이름 mi**. contoso.com: 
- CliConfig 별칭을 정의 하는 데 (도구 이므로 레지스트리 설정을 래퍼 일 뿐이 수행 될 수도 있습니다 그룹 정책 또는 스크립트를 사용 하 여).
- 사용 하 여 *CNAME* 사용 하 여 *TrustServerCertificate = true* 옵션입니다.


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>어떻게 합니까 다시 이동할 수 데이터베이스 관리 되는 인스턴스에서 SQL Server 또는 Azure SQL Database에?

할 수 있습니다 [데이터베이스를 bacpac로 내보내기](sql-database-export.md) 차례로 [bacpac 파일 가져오기]( sql-database-import.md)합니다. 이 방법은 데이터베이스 100GB 보다 작은 경우에 권장 됩니다.

데이터베이스의 모든 테이블에 기본 키가 있는 트랜잭션 복제를 사용할 수 있습니다.

네이티브 `COPY_ONLY` 관리 되는 인스턴스에서 수행 된 백업을 관리 되는 인스턴스는 SQL Server에 비해 더 높은 데이터베이스 버전이 SQL Server로 복원할 수 없습니다.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>단일 Azure SQL Database에 내 인스턴스 데이터베이스를 마이그레이션할 수는 방법

한 가지 옵션은 [데이터베이스를 bacpac로 내보내기](sql-database-export.md) 차례로 [bacpac 파일 가져오기]( sql-database-import.md)합니다. 

데이터베이스가 100GB 보다 작은 경우 권장 되는 방법입니다. 데이터베이스의 모든 테이블에 기본 키가 있는 트랜잭션 복제를 사용할 수 있습니다.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Gen 4 및 Gen 5 하드웨어 세대에 대 한 관리 되는 인스턴스 간에 어떻게 선택 하나요?

일부 하드웨어 세대는 다른 특정 유형의 워크 로드에 대 한 더 나은 워크 로드에 따라 다릅니다. 다음 워크 로드 성능에 영향을 주는 하드웨어 세대 성능의 주체 간소화 하기 위해 복잡 한 대신 상태인 동안 차이점:
- Gen 4 vCore 프로세서를 기반으로 하는 Gen 5 대의 실제 프로세서 기반으로 더 나은 계산 지원을 제공 합니다. 계산 집약적인 작업에 더 유리 수 있습니다.
- 5 세대 지원 가속 네트워킹 때문에 더 나은 IO 대역폭 원격 저장소로. IO 집약적인 워크 로드의 범용 서비스 계층에서 유리 수 있습니다. Gen 5는 4 세대에 비해 더 빠른 SSD 로컬 디스크를 사용 합니다. IO 집약적인 워크 로드의 중요 비즈니스용 서비스 계층에 유리 수 있습니다.

고객은 성능을 테스트 하는 것이 좋습니다는 실제 워크 로드 가동 되기 전에 프로덕션 용도로 하드웨어를 확인 하려면 생성 효율성도 뛰어납니다 사용자의 경우에서.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>내 관리 되는 인스턴스 하드웨어 세대 Gen 4 및 Gen 5 사이의 온라인를 전환할 수 있나요? 

하드웨어 세대 간에 자동화 된 온라인 전환 하는 것은 모두 하드웨어 세대에 관리 되는 인스턴스 프로 비전 되는 동일한 지역에서 사용할 수 있는 경우에 발생 합니다. 하드웨어 세대 간에 전환 하려면 Azure portal의 가격 책정 계층 섹션에서 옵션을이 예제의 경우 해야 합니다.

이 긴 이전 및 새 인스턴스 간에 전송 되는 새 관리 되는 인스턴스 프로 비전 할 백 엔드 및 데이터베이스에서 자동으로 작업을 실행 합니다. 이 프로세스는 고객은 원활 하 게 됩니다.

동일한 지역의 두 하드웨어 세대 지원 되지 않으며, 하드웨어 세대를 변경할 수는 있지만 수동으로 수행 해야 합니다. 이렇게 하려면 원하는 하드웨어 세대 수 및 수동으로 백업 및 이전 및 새 인스턴스 간에 데이터를 복원 지역에 새 인스턴스를 프로 비전 할 수 있습니다.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>내 관리 되는 인스턴스의 성능을 어떻게 조정 수행? 

관리 되는 인스턴스 범용 성능에 중요 한 데이터 및 로그 파일의 크기는으로 인해 원격 저장소를 사용 합니다. 범용 서비스 계층 성능을 조정 하기 위해이 블로그 게시물의 지침을 따릅니다.

IO 집약적인 워크 로드에 대 한 범용 4 세대를 사용 하 여 계산 집약적인 작업에 대 한 Gen 5 하드웨어를 사용 하는 것이 좋습니다. 자세한 내용은 하드웨어 세대 중에서 선택에서 FAQ 섹션을 참조 하세요.

워크 로드에 다 수의 작은 트랜잭션으로 구성 된 경우에 연결 형식에서 프록시 리디렉션 모드를 전환 하는 것이 좋습니다.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>관리 되는 인스턴스에 대 한 최대 저장소 크기는 어떻게 되나요? 

관리 되는 인스턴스에 대 한 저장소 크기에는 선택한 서비스 계층 (범용 또는 중요 비즈니스용) 달라 집니다. 이러한 서비스 계층의 저장소 제한에 대 한 참조 [서비스 계층 특성](sql-database-service-tiers-general-purpose-business-critical.md)합니다.

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>백업 저장소 내 관리 되는 인스턴스 저장소에서 차감 됩니다. 

아니요. 백업 저장소는 관리 되는 인스턴스 저장소 공간에서 차감 되지 않습니다. 백업 저장소는 인스턴스 저장소 공간에서 독립적 및 크기 제한 되지 않습니다. 백업 저장소는 35 일에서 7 구성할 수 있는 인스턴스 데이터베이스의 백업을 보존할 기간에 의해 제한 됩니다. 자세한 내용은 참조 하세요 [자동화 된 백업](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)합니다.
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>관리 포트에서 인바운드 NSG 규칙을 설정할 수는 방법

Microsoft 관리/배포 컴퓨터와 효과적으로 방지 하는 보안 관리 워크스테이션에만 연결 하는 IP 범위에서 인바운드 연결을 허용 하기 위해 클러스터의 모든 가상 컴퓨터에서 Windows 방화벽을 구성 하는 기본 제공 방화벽 기능 네트워크 계층을 통해 침입 합니다.

어떤 포트에 대 한 사용은 다음과 같습니다.

포트 9000 및 9003 Service Fabric 인프라에서 사용 됩니다. Service Fabric 기본 역할은 가상 클러스터를 정상으로 유지 하 고 구성 요소 복제본 수를 기준으로 목표 상태를 유지 합니다.

포트 1438, 1440 및 1452 노드 에이전트에서 사용 됩니다. 노드 에이전트는 클러스터 내에서 실행 되는 제어 평면으로 관리 명령을 실행 하는 데 사용 되는 응용 프로그램.

네트워크 계층에서 기본 제공 방화벽 외에 통신 인증서로 보호 됩니다.
  
자세한 내용 및 기본 제공 방화벽을 확인 하는 방법에 대 한 참조 [Azure SQL Database 관리 인스턴스 기본 제공 방화벽](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)합니다.


## <a name="how-can-i-mitigate-networking-risks"></a>네트워킹 위험을 줄일 수는 방법 

모든 네트워킹 위험을 완화 하려면 고객은 보안 설정 및 컨트롤의 집합을 적용 하려면 권장 됩니다.

- 모든 데이터베이스에서 투명 한 데이터 암호화 (TDE)를 켭니다.
- 외부 언어 런타임 (CLR (공용)을 설정 합니다. 온-프레미스 에서도 것이 좋습니다.
- Azure AD 계정에만 사용 합니다.
- SQL MI 낮은 권한이 가진된 DBA 계정으로 액세스 합니다.
- 시스템 관리자 계정에 대 한 JiT jumpbox 액세스를 구성 합니다.
- SQL 감사를 설정 하 고 경고 메커니즘을 갖춘와 통합 합니다.
- ATS 도구 모음에서 위협 감지를 사용 합니다.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>관리 되는 인스턴스를 사용 하 여 사용 사례 및 결과 비용 절감 찾을 수는 위치

관리 되는 인스턴스 사례 연구:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
혜택, 비용 및 Azure SQL Database 관리 되는 인스턴스를 배포와 관련 된 위험을 더 잘 이해을 가져오려면 이기도 한 Forrester의 연구: [MI의 전반적인 경제적 영향](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)합니다.


## <a name="can-i-do-dns-refresh"></a>DNS 새로 고침을 수행할 수 있나요? 
  
현재 관리 되는 인스턴스에 대 한 DNS 서버 구성을 새로 고치는 기능을 제공 하지 않습니다.

DNS 구성 최종적으로 새로 고쳐집니다.

- DHCP 임대가 만료 될 때입니다.
- 플랫폼 업그레이드 합니다.

대 안으로, 관리 되는 인스턴스 4 개 vCore 다운 그레이드를 나중에 다시 업그레이드 합니다. 이 DNS 구성을 새로 고치 쪽 영향을 미칩니다.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>관리 되는 인스턴스는 고정 IP 주소를 가질 수 있습니다?

드문 경우 지만 필요한 경우에서 새 가상 클러스터로 관리 되는 인스턴스는 온라인 마이그레이션 수행 해야 했습니다. 필요한 경우 서비스의 보안 및 안정성을 개선 하기 위한 기술 스택의 변경으로 인해이 마이그레이션이 됩니다. 마이그레이션에 새 가상 클러스터 관리 되는 인스턴스의 호스트 이름에 매핑되는 IP 주소를 변경 합니다. 관리 되는 인스턴스 서비스 정적 IP 주소 지원을 클레임 하지 않습니다 하 고 정기적인 유지 관리 주기 중 예 고 없이 변경할 수 있는 권리.

이러한 이유로 것 하지 않는 것이 불필요 한 가동 중지 시간이 발생할 수 있습니다 하는 대로 IP 주소의 불변성에 의존 합니다.


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>기존 관리 되는 인스턴스에 대 한 표준 시간대를 변경할 수 있나요?

처음으로 관리 되는 인스턴스를 프로 비전 될 때 표준 시간대 구성을 설정할 수 있습니다. 기존 관리 되는 인스턴스의 표준 시간대를 변경 하는 것은 지원 되지 않습니다. 자세한 내용은 참조 하세요 [표준 시간대 제한](sql-database-managed-instance-timezone.md#limitations)합니다.

해결 방법이 적절 한 표준 시간대를 사용 하 여 새 관리 되는 인스턴스 만들기를 포함 하 고 수동 백업 수행 후 복원 또는 권장 사항, 수행을 [인스턴스 간 지정 시간 복원](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)합니다.


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>내 관리 되는 인스턴스를 사용 하 여 성능 문제를 해결 하는 방법

에서 관리 되는 인스턴스 및 SQL Server 간의 성능 비교에 대 한 좋은 시작 지점이 됩니다 [Azure SQL 관리 되는 인스턴스 및 SQL Server 간 성능 비교에 대 한 유용한](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)합니다.

데이터 로드 때문에 속도가 보다 관리 되는 인스턴스에서 SQL Server에서 필수 전체 복구 모델 많습니다. 및 [제한](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) 트랜잭션 로그 쓰기 처리량에 있습니다. 경우에 따라이 해결할 수 있습니다 하는 대신 사용자 데이터베이스는 tempdb에 임시 데이터를 로드 하거나 클러스터형된 columnstore 또는 메모리 최적화 테이블을 사용 하 여 합니다.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>관리 되는 인스턴스에 암호화 된 데이터베이스를 복원할 수 있나요?

예, 관리 되는 인스턴스로 복원 하려면 사용자 데이터베이스를 암호 해독할 필요가 없습니다. 암호화 된 백업 파일에서 데이터를 읽을 수 있으려면 관리 되는 인스턴스를 원본 시스템의 암호화 키 보호기를으로 사용할 인증서/키를 제공할 필요가 없습니다. 이 작업을 수행 하는 두 가지 방법이 가지

- 관리 되는 인스턴스로 보호기 인증서를 업로드 합니다. 이렇게 하려면 PowerShell만 사용 합니다. 샘플 스크립트는 전체 프로세스를 설명합니다.
- 비대칭 키 보호기를 Key Vault (AKV (Azure)를 업로드 하 고 관리 되는 인스턴스를 가리킵니다. 이 방법은 또한 AKV 통합을 사용 하 여 암호화 키를 저장 하는 bring your-own key (BYOK) TDE 사용 사례와 유사 합니다. 실제로 암호화 키 보호기로 키를 사용 하지 않고 암호화 된 데이터베이스를 복원 하는 것에 대 한 관리 되는 인스턴스에 사용할 AKV에 업로드 하는 키를 선택 하면 BYOK TDE를 설정 하기 위한 지침 및 선택한 키 확인 확인란을 선택 하지는 기본 TDE 보호기입니다.

암호화 보호기 사용 가능 하도록 설정한 관리 되는 인스턴스를 한 후에 표준 데이터베이스 복원 절차를 사용 하 여 진행할 수 있습니다.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>관리 되는 인스턴스를 Azure SQL Database 단일 또는 탄력적인 풀에서 마이그레이션하려면 어떻게 해야 있습니다? 

Azure SQL Database의 다른 배포 옵션으로 계산 및 저장소 크기 별로 동일한 성능 수준 인스턴스 제공을 관리합니다. 단일 인스턴스에서 데이터를 통합 하려는 경우 하기만 단독으로 관리 되는 인스턴스에서 지원 되는 기능 (BACPAC) 내보내기/가져오기 기능을 사용 하 여 데이터를 마이그레이션할 수 있습니다.
