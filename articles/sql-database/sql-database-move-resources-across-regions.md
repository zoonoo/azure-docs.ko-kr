---
title: 리소스를 다른 지역으로 이동하는 방법
description: Azure SQL Database, Azure SQL 탄력적 풀 또는 Azure SQL 관리 인스턴스를 다른 지역으로 이동하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821427"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Azure SQL 리소스를 다른 지역으로 이동하는 방법

이 문서에서는 Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리되는 인스턴스를 새 리전으로 이동하는 방법에 대한 일반 워크플로를 설명합니다. 

## <a name="overview"></a>개요

기존 Azure SQL 리소스를 한 리전에서 다른 리전으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 비즈니스를 새 리전으로 확장하고 새 고객 기반에 맞게 비즈니스를 최적화하려고 합니다. 또는 규정 준수를 위해 작업을 다른 지역으로 이동해야 합니다. 또는 Azure는 더 나은 근접성을 제공하고 고객 경험을 개선하는 새로운 지역을 출시했습니다.  

이 문서에서는 리소스를 다른 지역으로 이동하기 위한 일반적인 워크플로를 제공합니다. 워크플로는 다음 단계로 구성됩니다. 

- 이동의 전제 조건 확인 
- 범위에서 리소스 이동 준비
- 준비 프로세스 모니터링
- 이동 프로세스 테스트
- 실제 이동 시작 
- 원본 리전에서 리소스 제거 


> [!NOTE]
> 이 문서는 Azure 퍼블릭 클라우드 내 또는 동일한 주권 클라우드 내에서 마이그레이션에 적용됩니다. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>단일 데이터베이스 이동

### <a name="verify-prerequisites"></a>필수 조건 확인 

1. 각 원본 서버에 대한 대상 논리 서버를 만듭니다. 
1. [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)을 사용하여 올바른 예외를 사용하여 방화벽을 구성합니다.  
1. 올바른 로그인으로 논리 서버를 구성합니다. 구독 관리자 또는 SQL 서버 관리자가 아닌 경우 관리자와 협력하여 필요한 권한을 할당합니다. 자세한 내용은 [재해 복구 후 Azure SQL 데이터베이스 보안을 관리하는 방법을](sql-database-geo-replication-security-config.md)참조하세요. 
1. 데이터베이스가 TDE로 암호화되고 Azure 키 자격 증명 모음에서 자체 암호화 키를 사용하는 경우 올바른 암호화 자료가 대상 지역에 프로비전되었는지 확인합니다. 자세한 내용은 [Azure 키 자격 증명 모음에서 고객 관리 키가 있는 Azure SQL 투명 데이터 암호화를](transparent-data-encryption-byok-azure-sql.md) 참조하십시오.
1. 데이터베이스 수준 감사를 사용하도록 설정한 경우 이를 사용하지 않도록 설정하고 대신 서버 수준 감사를 사용하도록 설정합니다. 장애 조치 후 데이터베이스 수준 감사에는 이동 후 원하지 않거나 불가능한 지역 간 트래픽이 필요합니다. 
1. 서버 수준 감사의 경우 다음을 수행해야 합니다.
   - 기존 감사 로그가 있는 저장소 컨테이너, 로그 분석 또는 이벤트 허브가 대상 영역으로 이동됩니다. 
   - 감사는 대상 서버에서 구성됩니다. 자세한 내용은 [SQL 데이터베이스 감사 시작 을](sql-database-auditing.md)참조하십시오. 
1. 인스턴스에 장기 보존 정책(LTR)이 있는 경우 기존 LTR 백업은 현재 서버와 연결된 상태로 유지됩니다. 대상 서버가 다르기 때문에 서버가 삭제된 경우에도 원본 서버를 사용하여 원본 지역의 이전 LTR 백업에 액세스할 수 있습니다. 

  > [!NOTE]
  > 이는 주권 클라우드와 공용 지역 간에 이동하기에 충분하지 않습니다. 이러한 마이그레이션을 사용하려면 LTR 백업을 현재 지원되지 않는 대상 서버로 이동해야 합니다. 

### <a name="prepare-resources"></a>리소스 준비

1. 원본의 논리 서버와 대상의 논리 서버 간에 [장애 조치 그룹을](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) 만듭니다.  
1. 장애 조치 그룹으로 이동할 데이터베이스를 추가합니다. 
    - 추가된 모든 데이터베이스의 복제가 자동으로 시작됩니다. 자세한 내용은 [단일 데이터베이스에서 장애 조치 그룹을 사용하는 모범 사례를](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)참조하십시오. 
 
### <a name="monitor-the-preparation-process"></a>준비 프로세스 모니터링

[Get-AzSqlDatabaseFailoverGroup을](/powershell/module/az.sql/get-azsqldatabasefailovergroup) 주기적으로 호출하여 원본에서 대상으로 데이터베이스의 복제를 모니터링할 수 있습니다. 의 `Get-AzSqlDatabaseFailoverGroup` 출력 개체는 복제 상태에 대한 속성을 **포함합니다.** 
   - **복제상태 =** 2(CATCH_UP)는 데이터베이스가 동기화되어 있으며 안전하게 실패할 수 있음을 나타냅니다. 
   - **복제상태 =** 0(SEEDING)은 데이터베이스가 아직 시드되지 않았으며 장애 조치 시도가 실패했음을 나타냅니다. 

### <a name="test-synchronization"></a>테스트 동기화

**ReplicationState가** `2`되면 보조 끝점을 `<fog-name>.secondary.database.windows.net` 사용하여 각 데이터베이스 또는 데이터베이스의 하위 집합에 연결하고 데이터베이스에 대한 쿼리를 수행하여 연결, 적절한 보안 구성 및 데이터 복제를 보장합니다. 

### <a name="initiate-the-move"></a>이동 시작

1. 보조 끝점을 `<fog-name>.secondary.database.windows.net`사용하여 대상 서버에 연결합니다.
1. [스위치-AzSqlDatabaseFailoverGroup을](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) 사용하여 보조 관리되는 인스턴스를 전체 동기화가 있는 주 인스턴스로 전환합니다. 이 작업은 성공하거나 롤백됩니다. 
1. DNS CNAME 진입점이 `nslook up <fog-name>.secondary.database.windows.net` 대상 지역 IP 주소로 지정되는지 확인하여 명령이 성공적으로 완료되었는지 확인합니다. 스위치 명령이 실패하면 CNAME이 업데이트되지 않습니다. 

### <a name="remove-the-source-databases"></a>원본 데이터베이스 제거

이동이 완료되면 불필요한 요금이 발생하지 않도록 원본 지역의 리소스를 제거합니다. 

1. [제거-AzSqlDatabaseFailoverGroup을](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)사용하여 장애 조치 그룹을 삭제합니다. 
1. 원본 서버의 각 데이터베이스에 대해 [제거-AzSqlDatabase를](/powershell/module/az.sql/remove-azsqldatabase) 사용하여 각 원본 데이터베이스를 삭제합니다. 이렇게 하면 지역 복제 링크가 자동으로 종료됩니다. 
1. [제거-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)를 사용하여 원본 서버를 삭제합니다. 
1. 키 자격 증명 모음, 감사 저장소 컨테이너, 이벤트 허브, AAD 인스턴스 및 기타 종속 리소스를 제거하여 대금 청구를 중지합니다. 

## <a name="move-elastic-pools"></a>탄력적 풀 이동

### <a name="verify-prerequisites"></a>필수 조건 확인 

1. 각 원본 서버에 대한 대상 논리 서버를 만듭니다. 
1. [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)을 사용하여 올바른 예외를 사용하여 방화벽을 구성합니다. 
1. 올바른 로그인으로 논리 서버를 구성합니다. 구독 관리자 또는 SQL 서버 관리자가 아닌 경우 관리자와 협력하여 필요한 권한을 할당합니다. 자세한 내용은 [재해 복구 후 Azure SQL 데이터베이스 보안을 관리하는 방법을](sql-database-geo-replication-security-config.md)참조하세요. 
1. 데이터베이스가 TDE로 암호화되고 Azure 키 자격 증명 모음에서 자체 암호화 키를 사용하는 경우 올바른 암호화 자료가 대상 지역에 프로비전되었는지 확인합니다.
1. 각 소스 탄력적 풀에 대해 대상 탄력적 풀을 만들어 동일한 이름과 크기로 동일한 서비스 계층에서 풀을 만들 수 있습니다. 
1. 데이터베이스 수준 감사를 사용하도록 설정한 경우 이를 사용하지 않도록 설정하고 대신 서버 수준 감사를 사용하도록 설정합니다. 장애 조치 후 데이터베이스 수준 감사에는 이동 후 원하지 않거나 가능한 지역 간 트래픽이 필요합니다. 
1. 서버 수준 감사의 경우 다음을 수행해야 합니다.
    - 기존 감사 로그가 있는 저장소 컨테이너, 로그 분석 또는 이벤트 허브가 대상 영역으로 이동됩니다.
    - 감사 구성은 대상 서버에서 구성됩니다. 자세한 내용은 [SQL 데이터베이스 감사를](sql-database-auditing.md)참조하십시오.
1. 인스턴스에 장기 보존 정책(LTR)이 있는 경우 기존 LTR 백업은 현재 서버와 연결된 상태로 유지됩니다. 대상 서버가 다르기 때문에 서버가 삭제된 경우에도 원본 서버를 사용하여 원본 지역의 이전 LTR 백업에 액세스할 수 있습니다. 

  > [!NOTE]
  > 이는 주권 클라우드와 공용 지역 간에 이동하기에 충분하지 않습니다. 이러한 마이그레이션을 사용하려면 LTR 백업을 현재 지원되지 않는 대상 서버로 이동해야 합니다. 

### <a name="prepare-to-move"></a>이동 준비
 
1.  원본 논리 서버의 각 탄력적 풀과 대상 서버의 해당 탄력적 풀 사이에 별도의 [장애 조치 그룹을](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) 만듭니다. 
1.  장애 조치 그룹에 풀의 모든 데이터베이스를 추가합니다. 
    - 추가된 데이터베이스의 복제가 자동으로 시작됩니다. 자세한 내용은 [탄력적 풀이 있는 장애 조치 그룹에 대한 모범 사례를](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)참조하십시오. 

  > [!NOTE]
  > 여러 개의 탄력적 풀을 포함하는 장애 조치 그룹을 만들 수 있지만 각 풀에 대해 별도의 장애 조치 그룹을 만드는 것이 좋습니다. 이동해야 하는 여러 탄력적 풀에 많은 수의 데이터베이스가 있는 경우 준비 단계를 병렬로 실행한 다음 병렬로 이동 단계를 시작할 수 있습니다. 이 프로세스는 더 나은 확장 및 동일한 장애 조치 그룹에 여러 개의 탄력적 풀을 갖는 것에 비해 적은 시간이 소요됩니다. 

### <a name="monitor-the-preparation-process"></a>준비 프로세스 모니터링

[Get-AzSqlDatabaseFailoverGroup을](/powershell/module/az.sql/get-azsqldatabasefailovergroup) 주기적으로 호출하여 원본에서 대상으로 데이터베이스의 복제를 모니터링할 수 있습니다. 의 `Get-AzSqlDatabaseFailoverGroup` 출력 개체는 복제 상태에 대한 속성을 **포함합니다.** 
   - **복제상태 =** 2(CATCH_UP)는 데이터베이스가 동기화되어 있으며 안전하게 실패할 수 있음을 나타냅니다. 
   - **복제상태 =** 0(SEEDING)은 데이터베이스가 아직 시드되지 않았으며 장애 조치 시도가 실패했음을 나타냅니다. 

### <a name="test-synchronization"></a>테스트 동기화
 
**ReplicationState가** `2`되면 보조 끝점을 `<fog-name>.secondary.database.windows.net` 사용하여 각 데이터베이스 또는 데이터베이스의 하위 집합에 연결하고 데이터베이스에 대한 쿼리를 수행하여 연결, 적절한 보안 구성 및 데이터 복제를 보장합니다. 

### <a name="initiate-the-move"></a>이동 시작
 
1. 보조 끝점을 `<fog-name>.secondary.database.windows.net`사용하여 대상 서버에 연결합니다.
1. [스위치-AzSqlDatabaseFailoverGroup을](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) 사용하여 보조 관리되는 인스턴스를 전체 동기화가 있는 주 인스턴스로 전환합니다. 이 작업은 성공하거나 롤백됩니다. 
1. DNS CNAME 진입점이 `nslook up <fog-name>.secondary.database.windows.net` 대상 지역 IP 주소로 지정되는지 확인하여 명령이 성공적으로 완료되었는지 확인합니다. 스위치 명령이 실패하면 CNAME이 업데이트되지 않습니다. 

### <a name="remove-the-source-elastic-pools"></a>소스 탄력적 풀 제거
 
이동이 완료되면 불필요한 요금이 발생하지 않도록 원본 지역의 리소스를 제거합니다. 

1. [제거-AzSqlDatabaseFailoverGroup을](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)사용하여 장애 조치 그룹을 삭제합니다.
1. [제거-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)를 사용하여 소스 서버의 각 소스 탄력적 풀을 삭제합니다. 
1. [제거-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)를 사용하여 원본 서버를 삭제합니다. 
1. 키 자격 증명 모음, 감사 저장소 컨테이너, 이벤트 허브, AAD 인스턴스 및 기타 종속 리소스를 제거하여 대금 청구를 중지합니다. 

## <a name="move-managed-instance"></a>관리되는 인스턴스 이동

### <a name="verify-prerequisites"></a>필수 조건 확인
 
1. 각 소스 관리 인스턴스에 대해 대상 영역에서 동일한 크기의 대상 관리 인스턴스를 만듭니다.  
1. 관리되는 인스턴스에 대해 네트워크를 구성합니다. 자세한 내용은 [네트워크 구성](sql-database-howto-managed-instance.md#network-configuration)을 참조하십시오.
1. 올바른 로그인으로 대상 마스터 데이터베이스를 구성합니다. 구독 관리자 또는 SQL 서버 관리자가 아닌 경우 관리자와 협력하여 필요한 권한을 할당합니다. 
1. 데이터베이스가 TDE로 암호화되고 Azure 키 자격 증명 모음에서 자체 암호화 키를 사용하는 경우 동일한 암호화 키가 있는 AKV가 원본 및 대상 지역에 모두 있는지 확인합니다. 자세한 내용은 [Azure 키 자격 증명 모음에서 고객 관리 키가 있는 TDE를](transparent-data-encryption-byok-azure-sql.md)참조하십시오.
1. 인스턴스에 대한 감사를 사용하도록 설정한 경우 다음을 수행해야 합니다.
    - 기존 로그가 있는 저장소 컨테이너 또는 이벤트 허브가 대상 영역으로 이동됩니다. 
    - 감사는 대상 인스턴스에서 구성됩니다. 자세한 내용은 [관리형 인스턴스로 감사를](sql-database-managed-instance-auditing.md)참조하십시오.
1. 인스턴스에 장기 보존 정책(LTR)이 있는 경우 기존 LTR 백업은 현재 서버와 연결된 상태로 유지됩니다. 대상 서버가 다르기 때문에 서버가 삭제된 경우에도 원본 서버를 사용하여 원본 지역의 이전 LTR 백업에 액세스할 수 있습니다. 

  > [!NOTE]
  > 이는 주권 클라우드와 공용 지역 간에 이동하기에 충분하지 않습니다. 이러한 마이그레이션을 사용하려면 LTR 백업을 현재 지원되지 않는 대상 서버로 이동해야 합니다. 

### <a name="prepare-resources"></a>리소스 준비

각 소스 인스턴스와 해당 대상 인스턴스 간에 장애 조치 그룹을 만듭니다.
    - 각 인스턴스의 모든 데이터베이스 복제가 자동으로 시작됩니다. 자세한 내용은 [자동 장애 조치 그룹을](sql-database-auto-failover-group.md) 참조하십시오.

 
### <a name="monitor-the-preparation-process"></a>준비 프로세스 모니터링

[Get-AzSqlDatabaseFailoverGroup을](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) 주기적으로 호출하여 원본에서 대상으로 데이터베이스의 복제를 모니터링할 수 있습니다. 의 `Get-AzSqlDatabaseFailoverGroup` 출력 개체는 복제 상태에 대한 속성을 **포함합니다.** 
   - **복제상태 =** 2(CATCH_UP)는 데이터베이스가 동기화되어 있으며 안전하게 실패할 수 있음을 나타냅니다. 
   - **복제상태 =** 0(SEEDING)은 데이터베이스가 아직 시드되지 않았으며 장애 조치 시도가 실패했음을 나타냅니다. 

### <a name="test-synchronization"></a>테스트 동기화

**ReplicationState가** `2`되면 보조 끝점을 `<fog-name>.secondary.database.windows.net` 사용하여 각 데이터베이스 또는 데이터베이스의 하위 집합에 연결하고 데이터베이스에 대한 쿼리를 수행하여 연결, 적절한 보안 구성 및 데이터 복제를 보장합니다. 

### <a name="initiate-the-move"></a>이동 시작 

1. 보조 끝점을 `<fog-name>.secondary.database.windows.net`사용하여 대상 서버에 연결합니다.
1. [스위치-AzSqlDatabaseFailoverGroup을](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) 사용하여 보조 관리되는 인스턴스를 전체 동기화가 있는 주 인스턴스로 전환합니다. 이 작업은 성공하거나 롤백됩니다. 
1. DNS CNAME 진입점이 `nslook up <fog-name>.secondary.database.windows.net` 대상 지역 IP 주소로 지정되는지 확인하여 명령이 성공적으로 완료되었는지 확인합니다. 스위치 명령이 실패하면 CNAME이 업데이트되지 않습니다. 

### <a name="remove-the-source-managed-instances"></a>소스 관리 인스턴스 제거
이동이 완료되면 불필요한 요금이 발생하지 않도록 원본 지역의 리소스를 제거합니다. 

1. [제거-AzSqlDatabaseFailoverGroup을](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)사용하여 장애 조치 그룹을 삭제합니다. 이렇게 하면 장애 조치 그룹 구성이 삭제되고 두 인스턴스 간의 지역 복제 링크가 종료됩니다. 
1. [제거-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)를 사용하여 소스 관리 인스턴스를 삭제합니다. 
1. 리소스 그룹의 추가 리소스(예: 가상 클러스터, 가상 네트워크 및 보안 그룹)를 제거합니다. 

## <a name="next-steps"></a>다음 단계 

Azure SQL 데이터베이스가 마이그레이션된 후 [관리합니다.](sql-database-manage-after-migration.md) 

