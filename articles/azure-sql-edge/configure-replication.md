---
title: Azure SQL Edge로 복제 구성
description: Azure SQL Edge로 복제를 구성하는 방법을 알아봅니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b424ece9207328d87068160f78ebc78a3bd1a8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395226"
---
# <a name="configure-replication-to-azure-sql-edge"></a>Azure SQL Edge로 복제 구성 

단방향 트랜잭션 복제 또는 스냅샷 복제를 위해 Azure SQL Edge 인스턴스를 푸시 구독자로 구성할 수 있습니다. 이 인스턴스는 트랜잭션 복제 구성의 게시자 또는 배포자로 작동할 수 없습니다. Azure SQL Edge는 병합 복제, 피어 투 피어 복제 또는 Oracle 게시를 지원하지 않습니다.

## <a name="supported-configurations"></a>지원되는 구성
  
- Azure SQL Edge 인스턴스는 게시자의 푸시 구독자여야 합니다.
- 게시자와 배포자는 다음 중 하나일 수 있습니다.
   - 온-프레미스에서 실행 중인 SQL Server 인스턴스 또는 Azure 가상 머신에서 실행 중인 SQL Server 인스턴스. 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](../azure-sql/virtual-machines/index.yml)를 참조하세요. SQL Server 인스턴스는 SQL Server 2016보다 이후 버전을 사용해야 합니다.
   - Azure SQL Managed Instance 인스턴스. SQL Managed Instance는 게시자, 배포자, 구독자 데이터베이스를 호스트할 수 있습니다. 자세한 내용은 [SQL Database Managed Instance를 사용하여 복제](/azure/sql-database/replication-with-sql-database-managed-instance/)를 참조하세요.

- 배포 데이터베이스와 복제 에이전트는 Azure SQL Edge 인스턴스에 배치할 수 없습니다.  

> [!NOTE]
> 지원되지 않는 버전을 사용하여 복제를 구성하려고 하면 MSSQL_REPL20084(“프로세스에서 구독자에 연결할 수 없습니다.”) 및 MSSQL_REPL40532(“로그인에서 요청한 \<name> 서버를 열 수 없습니다. 로그인에 실패했습니다.” 오류가 표시될 수 있습니다.  

## <a name="remarks"></a>설명

복제를 구성하는 경우 다음 요구 사항과 모범 사례를 이해하는 것이 중요합니다.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 복제를 구성할 수 있습니다. SQL Server Management Studio 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)를 통해 게시자에서 Transact-SQL 문을 실행하여 구성할 수도 있습니다.
- Azure SQL Edge 인스턴스에 복제하려면 SQL Server 인증을 사용하여 로그인해야 합니다.
- 복제된 테이블에는 기본 키가 있어야 합니다.
- SQL Server에서 단일 게시는 Azure SQL Edge 및 SQL Server(온-프레미스 및 Azure 가상 머신의 SQL Server) 구독자를 모두 지원할 수 있습니다.  
- 복제 관리, 모니터링, 문제 해결은 SQL Server 인스턴스에서 수행해야 합니다.  
- Azure SQL Edge에 대한 푸시 구독만 지원됩니다.  
- Azure SQL Edge용 `sp_addsubscription` 저장 프로시저에서는 `@subscriber_type = 0`만 지원됩니다.  
- Azure SQL Edge는 양방향, 즉시, 업데이트 가능 또는 피어 투 피어 복제를 지원하지 않습니다.
- Azure SQL Edge는 SQL Server 또는 SQL Managed Instance에서 사용할 수 있는 기능의 하위 집합만 지원합니다. 지원되지 않는 기능이 하나 이상 포함된 데이터베이스(또는 데이터베이스 내의 개체)를 복제하려고 하면 실패합니다. 예를 들어 공간 데이터 형식의 개체가 포함된 데이터베이스를 복제하려고 하면 오류가 표시됩니다. 자세한 내용은 [지원되는 Azure SQL Edge 기능](features.md)을 참조하세요.

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Azure SQL Edge 인스턴스에서 참조 데이터 초기화

시간이 지남에 따라 변경되는 참조 데이터로 인스턴스를 초기화하는 것이 좋습니다. 예를 들어 SQL Server 인스턴스에서 학습된 기계 학습 모델을 이후에 Azure SQL Edge 인스턴스에서 업데이트할 수 있습니다. 해당 시나리오에서 인스턴스를 초기화하는 방법은 다음과 같습니다.

1. SQL Server 데이터베이스에서 트랜잭션 복제 게시를 만듭니다.  
2. SQL Server 인스턴스에서 **새 구독 마법사** 또는 Transact-SQL 문을 사용하여 Azure SQL Edge 구독에 대한 푸시를 만듭니다.  
3. 스냅샷 에이전트가 생성하고 배포 에이전트가 배포 및 전달한 스냅샷을 사용하여 Azure SQL Edge에 복제된 데이터베이스를 초기화할 수 있습니다. 또는 게시자에서 데이터베이스 백업을 사용하여 초기화할 수 있습니다. Azure SQL Edge에서 지원하지 않는 개체나 기능이 데이터베이스 백업에 포함되어 있으면 복원 작업이 실패한다는 것에 유의하세요.

## <a name="limitations"></a>제한 사항

다음은 Azure SQL Edge 구독에서 지원되지 않는 옵션입니다.

- 파일 그룹 연결 복사  
- 테이블 파티션 구성표 복사  
- 인덱스 파티션 구성표 복사  
- 사용자 정의 통계 복사  
- 기본 바인딩 복사  
- 규칙 바인딩 복사  
- 전체 텍스트 인덱스 복사  
- XML XSD 복사  
- XML 인덱스 복사  
- 권한 복사  
- 공간 인덱스 복사  
- 필터링된 인덱스 복사  
- 데이터 압축 특성 복사  
- 스파스 열 특성 복사  
- 파일 스트림, `hierarchyid` 또는 공간 데이터 형식 복사
- `hierarchyid`를 MAX 데이터 형식으로 변환  
- 공간을 MAX 데이터 형식으로 변환  
- 확장 속성 복사  
- 권한 복사  

## <a name="examples"></a>예

게시 및 밀어넣기 구독을 만듭니다. 자세한 내용은 다음을 참조하세요.
  
- [게시 만들기](/sql/relational-databases/replication/publish/create-a-publication)
- Azure SQL Edge 서버 이름과 IP(예: **myEdgeinstance,1433**)를 구독자로 사용하고 Azure SQL Edge 인스턴스의 데이터베이스 이름(예: **AdventureWorks**)을 대상 데이터베이스로 사용하여 [밀어넣기 구독을 만듭니다](/sql/relational-databases/replication/create-a-push-subscription/).  

## <a name="next-steps"></a>다음 단계  

- [게시 만들기](/sql/relational-databases/replication/publish/create-a-publication)
- [밀어넣기 구독 만들기](/sql/relational-databases/replication/create-a-push-subscription/)
- [복제 유형](/sql/relational-databases/replication/types-of-replication)
- [모니터링(복제)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [구독 초기화](/sql/relational-databases/replication/initialize-a-subscription)