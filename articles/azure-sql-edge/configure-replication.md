---
title: Azure SQL Edge로 복제 구성
description: Azure SQL Edge로 복제를 구성 하는 방법에 대해 알아봅니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a748bf977e76357c710518e608c12ad19a8cd0be
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888426"
---
# <a name="configure-replication-to-azure-sql-edge"></a>Azure SQL Edge로 복제 구성 

Azure SQL Edge 인스턴스를 단방향 트랜잭션 복제 또는 스냅숏 복제에 대 한 밀어넣기 구독자로 구성할 수 있습니다. 이 인스턴스는 트랜잭션 복제 구성의 게시자 또는 배포자로 작동할 수 없습니다. Azure SQL Edge는 병합 복제, 피어 투 피어 복제 또는 Oracle 게시를 지원 하지 않습니다.

## <a name="supported-configurations"></a>지원되는 구성
  
- Azure SQL Edge 인스턴스는 게시자의 푸시 구독자 여야 합니다.
- 게시자 및 배포자는 다음 중 하나일 수 있습니다.
   - 온-프레미스 또는 Azure 가상 머신에서 실행 되는 SQL Server 인스턴스를 실행 하는 SQL Server 인스턴스입니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](https://docs.microsoft.com/azure/azure-sql/virtual-machines/)를 참조하세요. SQL Server 인스턴스는 SQL Server 2016 이상 버전을 사용 해야 합니다.
   - Azure SQL Managed Instance의 인스턴스입니다. SQL Managed Instance은 게시자, 배포자 및 구독자 데이터베이스를 호스팅할 수 있습니다. 자세한 내용은 [SQL Database Managed Instance를 사용하여 복제](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/)를 참조하세요.

- 배포 데이터베이스 및 복제 에이전트는 Azure SQL Edge 인스턴스에 배치할 수 없습니다.  

> [!NOTE]
> 지원 되지 않는 버전을 사용 하 여 복제를 구성 하려고 하면 MSSQL_REPL20084 ("구독자에 연결할 수 없습니다.") 및 MSSQL_REPL40532 (" \<name> 로그인에서 요청한 서버를 열 수 없습니다." 라는 두 개의 오류가 발생할 수 있습니다. 로그인 하지 못했습니다. ").  

## <a name="remarks"></a>설명

복제를 구성할 때 다음 요구 사항 및 모범 사례를 이해 하는 것이 중요 합니다.

- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 사용 하 여 복제를 구성할 수 있습니다. SQL Server Management Studio 또는 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)를 사용 하 여 게시자에서 transact-sql 문을 실행 하 여 수행할 수도 있습니다.
- Azure SQL Edge 인스턴스에 복제 하려면 SQL Server 인증을 사용 하 여 로그인 해야 합니다.
- 복제된 테이블에는 기본 키가 있어야 합니다.
- SQL Server에서 단일 게시는 Azure SQL Edge 및 SQL Server(온-프레미스 및 Azure 가상 머신의 SQL Server) 구독자를 모두 지원할 수 있습니다.  
- SQL Server 인스턴스에서 복제 관리, 모니터링 및 문제 해결을 수행 해야 합니다.  
- Azure SQL Edge에 대한 푸시 구독만 지원됩니다.  
- `@subscriber_type = 0`AZURE SQL Edge의 저장 프로시저 에서만 지원 됩니다 `sp_addsubscription` .  
- Azure SQL Edge는 양방향, 즉시, 업데이트 또는 피어 투 피어 복제를 지원 하지 않습니다.
- Azure SQL Edge는 SQL Server 또는 SQL Managed Instance에서 사용할 수 있는 기능의 하위 집합만 지원 합니다. 하나 이상의 지원 되지 않는 기능을 포함 하는 데이터베이스 (또는 데이터베이스 내의 개체)를 복제 하려고 하면 시도가 실패 합니다. 예를 들어 공간 데이터 형식의 개체가 포함 된 데이터베이스를 복제 하려고 하면 오류가 표시 됩니다. 자세한 내용은 [AZURE SQL Edge의 지원 되는 기능](features.md)을 참조 하세요.

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Azure SQL Edge 인스턴스에서 참조 데이터 초기화

시간이 지남에 따라 변경 되는 참조 데이터를 사용 하 여 인스턴스를 초기화 하는 것이 좋습니다. 예를 들어 SQL Server 인스턴스에서 학습 된 후 Azure SQL Edge 인스턴스에서 machine learning 모델을 업데이트할 수 있습니다. 이러한 시나리오에서 인스턴스를 초기화 하는 방법은 다음과 같습니다.

1. SQL Server 데이터베이스에서 트랜잭션 복제 게시를 만듭니다.  
2. SQL Server 인스턴스에서 **새 구독 마법사** 또는 transact-sql 문을 사용 하 여 Azure SQL Edge에 대 한 밀어넣기 구독을 만듭니다.  
3. 스냅숏 에이전트에 의해 생성 되 고 배포 에이전트에서 배포 및 배달 되는 스냅숏을 사용 하 여 Azure SQL Edge에서 복제 된 데이터베이스를 초기화할 수 있습니다. 또는 게시자에서 데이터베이스의 백업을 사용 하 여 초기화할 수 있습니다. 데이터베이스 백업에 Azure SQL Edge에서 지원 하지 않는 개체 또는 기능이 포함 되어 있으면 복원 작업이 실패 합니다.

## <a name="limitations"></a>제한 사항

다음 옵션은 Azure SQL Edge 구독에 대해 지원 되지 않습니다.

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
- Filestream, `hierarchyid` 또는 공간 데이터 형식 복사
- `hierarchyid`MAX 데이터 형식으로 변환  
- 공간을 MAX 데이터 형식으로 변환  
- 확장 속성 복사  
- 권한 복사  

## <a name="examples"></a>예

게시 및 밀어넣기 구독을 만듭니다. 자세한 내용은 다음을 참조하세요.
  
- [게시 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- Azure SQL Edge 서버 이름 및 IP를 구독자 (예: **myEdgeinstance, 1433**)로 사용 하 고 Azure sql edge 인스턴스에서 대상 데이터베이스로 데이터베이스 이름 (예: **AdventureWorks**)을 사용 하 여 [밀어넣기 구독을 만듭니다](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) .  

## <a name="next-steps"></a>다음 단계  

- [게시 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [밀어넣기 구독 만들기](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [복제 유형](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [모니터링 (복제)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [구독 초기화](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


