---
title: Azure SQL Database에 대 한 Azure SQL Server 복제
description: Azure SQL Database의 데이터베이스를 단방향 트랜잭션 또는 스냅숏 복제 토폴로지에서 밀어넣기 구독자로 구성할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 079d187f66cf77585121198df06cabafc454fea1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362132"
---
# <a name="replication-to-azure-sql-database"></a>Azure SQL Database로 복제
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database를 단방향 트랜잭션 또는 스냅숏 복제 토폴로지에서 밀어넣기 구독자로 구성할 수 있습니다.

> [!NOTE]
> 이 문서에서는 Azure SQL Database에서 [트랜잭션 복제](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) 를 사용 하는 방법을 설명 합니다. 이는 개별 데이터베이스의 완전 한 읽기 가능한 복제본을 만들 수 있도록 하는 Azure SQL Database 기능인 [활성 지역 복제](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)와는 관련이 없습니다.

## <a name="supported-configurations"></a>지원되는 구성
  
- Azure SQL Database은 SQL Server 게시자 및 배포자의 밀어넣기 구독자 일 수만 있습니다.  
- 게시자 및/또는 배포자 역할을 하는 SQL Server 인스턴스는 [온-프레미스](https://www.microsoft.com/sql-server/sql-server-downloads), [azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md)실행 되는 SQL Server의 인스턴스이거나 [클라우드의 azure 가상 머신에서 실행 되는 SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)인스턴스일 수 있습니다. 
- 배포 데이터베이스 및 복제 에이전트는 Azure SQL Database의 데이터베이스에 배치할 수 없습니다.  
- [스냅숏](/sql/relational-databases/replication/snapshot-replication) 및 [단방향 트랜잭션](/sql/relational-databases/replication/transactional/transactional-replication) 복제가 지원 됩니다. 피어 투 피어 트랜잭션 복제 및 병합 복제는 지원되지 않습니다.

### <a name="versions"></a>버전  

Azure SQL Database의 데이터베이스로 성공적으로 복제 하려면 SQL Server 게시자와 배포자가 최소한 다음 버전 중 하나를 사용 해야 합니다.

SQL Server 데이터베이스에서 Azure SQL Database에 게시 하는 것은 다음 버전의 SQL Server에서 지원 됩니다.

- SQL Server 2016 이상
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) 또는 [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) 또는 [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> 지원 되지 않는 버전을 사용 하 여 복제를 구성 하려고 하면 오류 번호가 MSSQL_REPL20084 (구독자에 연결할 수 없습니다.) 및 MSSQL_REPL40532 ( \<name> 로그인에서 요청한 서버를 열 수 없습니다. 로그인이 실패했습니다.)가 발생할 수 있습니다.  

Azure SQL Database의 모든 기능을 사용하려면 최신 버전의 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 및 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)를 사용해야 합니다.  

### <a name="types-of-replication"></a>복제의 유형

다음과 같은 여러 [복제 유형](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)이 있습니다.

| 복제 | Azure SQL Database | Azure SQL Managed Instance |
| :----| :------------- | :--------------- |
| [**표준 트랜잭션**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 예(구독자로) | 예 | 
| [**스냅샷**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 예(구독자로) | 예|
| [**병합 복제**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 아니요 | 아니요|
| [**피어 투 피어**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 아니요 | 아니요|
| [**양방향**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 아니요 | 예|
| [**업데이트할 수 있는 구독**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 아니요 | 예|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>설명

- Azure SQL Database에 대한 밀어넣기 구독만 지원됩니다.  
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하거나 게시자에서 Transact-SQL 명령문을 실행하여 복제를 구성할 수 있습니다. Azure Portal을 사용하여 복제를 구성할 수 없습니다.  
- 복제는 SQL Server 인증 로그인만 사용 하 여 Azure SQL Database에 연결할 수 있습니다.
- 복제된 테이블에는 기본 키가 있어야 합니다.  
- 기존 Azure 구독이 있어야 합니다.  
- Azure SQL Database 구독자는 모든 지역에 있을 수 있습니다.  
- SQL Server에서 단일 게시는 Azure SQL Database 및 SQL Server(온-프레미스 및 Azure 가상 머신의 SQL Server) 구독자를 모두 지원할 수 있습니다.  
- 복제 관리, 모니터링 및 문제 해결은 Azure SQL Database가 아닌 SQL Server에서 수행 해야 합니다.  
- `@subscriber_type = 0`만 SQL Database에 대한 **sp_addsubscription**에서 지원됩니다.  
- Azure SQL Database은 양방향, 즉시, 업데이트 또는 피어 투 피어 복제를 지원 하지 않습니다.

## <a name="replication-architecture"></a>복제 아키텍처  

![다이어그램은 서로 다른 지역의 여러 구독자 클러스터를 포함 하는 Azure SQL Database 포함 된 복제 아키텍처와, 원격 클러스터에 연결 되는 게시자, Logread 실행 파일 및 배포자 실행 파일을 포함 하는 온-프레미스 Azure 가상 컴퓨터를 보여 줍니다.](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>시나리오  

### <a name="typical-replication-scenario"></a>일반적인 복제 시나리오  

1. SQL Server 데이터베이스에서 트랜잭션 복제 게시를 만듭니다.  
2. SQL Server에서 **새 구독 마법사** 또는 transact-sql 문을 사용 하 여 Azure SQL Database에 대 한 밀어넣기 구독을 만듭니다.  
3. Azure SQL Database에 단일 및 풀링된 데이터베이스를 사용하는 경우 초기 데이터 세트는 스냅샷 에이전트에 의해 생성되고 배포 에이전트에 의해 배포 및 적용된 스냅샷입니다. SQL Managed Instance 게시자를 사용 하 여 데이터베이스 백업을 사용 하 여 Azure SQL Database 구독자의 초기값을 설정할 수도 있습니다.

### <a name="data-migration-scenario"></a>데이터 마이그레이션 시나리오  

1. 트랜잭션 복제를 사용 하 여 SQL Server 데이터베이스에서 Azure SQL Database 데이터를 복제 합니다.  
2. 클라이언트 또는 중간 계층 응용 프로그램을 리디렉션하여 데이터베이스 복사본을 업데이트 합니다.  
3. 테이블의 SQL Server 버전 업데이트를 중지하고 게시를 제거합니다.  

## <a name="limitations"></a>제한 사항

다음 옵션은 Azure SQL Database 구독에 대해 지원되지 않습니다.

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
- 파일 스트림을 MAX 데이터 형식으로 변환  
- hierarchyid를 MAX 데이터 형식으로 변환  
- 공간을 MAX 데이터 형식으로 변환  
- 확장 속성 복사  

### <a name="limitations-to-be-determined"></a>추후 결정될 제한 사항

- 데이터 정렬 복사  
- SP의 직렬화된 트랜잭션에서 실행  

## <a name="examples"></a>예

게시 및 밀어넣기 구독을 만듭니다. 자세한 내용은 다음을 참조하세요.
  
- [게시 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 서버 이름 (예 **: n'azuresqldbdns.database.windows.net '**) 및 대상 데이터베이스로 Azure SQL Database 이름 (예: **AdventureWorks**)을 사용 하 여 [밀어넣기 구독을 만듭니다](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) .  

## <a name="see-also"></a>참고 항목  

- [트랜잭션 복제](../managed-instance/replication-transactional-overview.md)
- [게시 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [밀어넣기 구독 만들기](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [복제 유형](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [모니터링(복제)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [구독 초기화](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
