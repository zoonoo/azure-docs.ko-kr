---
title: Azure SQL Edge(미리 보기)로 복제 구성
description: Azure SQL Edge(미리 보기)로 복제 구성에 대해 자세히 알아보기
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593992"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Azure SQL Edge(미리 보기)로 복제 구성 

Azure SQL Edge 인스턴스는 단방향 트랜잭션 복제 또는 스냅샷 복제에 대한 푸시 구독자로 구성할 수 있습니다. Azure SQL Edge 인스턴스는 트랜잭션 복제 구성의 게시자 또는 배포자로 작동할 수 없습니다. 병합 복제, P2P 복제, Oracle 게시는 Azure SQL Edge에서 지원되지 않습니다.

## <a name="supported-configurations"></a>**지원되는 구성**:
  
- Azure SQL Edge 인스턴스는 게시자의 푸시 구독자여야 합니다.
- 게시자 및 배포자는 다음 중 하나일 수 있습니다.
   - 온-프레미스에서 실행 중인 SQL Server의 인스턴스 또는 Azure 가상 머신에서 실행 중인 SQL Server의 인스턴스 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)를 참조하세요. SQL Server 인스턴스는 SQL Server 2016보다 큰 버전을 사용해야 합니다.
   - Azure SQL Database Managed Instance의 인스턴스. Managed Instance는 게시자, 배포자 및 구독자 데이터베이스를 호스트할 수 있습니다. 자세한 내용은 [SQL Database Managed Instance를 사용하여 복제](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/)를 참조하세요.

- 배포 데이터베이스 및 복제 에이전트는 Azure SQL Edge 인스턴스에 배치할 수 없습니다.  

> [!NOTE]
> 지원되지 않는 버전을 사용하여 복제를 구성하는 시도는 오류 번호 MSSQL_REPL20084(프로세스가 구독자에 연결할 수 없습니다.) 및 MSSQL_REPL40532(로그인에서 요청한 서버 \<이름>을 열 수 없습니다. 로그인이 실패했습니다.)가 발생할 수 있습니다.  

Azure SQL Edge의 모든 기능을 사용하려면 최신 버전의 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 및 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)를 사용해야 합니다.  

## <a name="remarks"></a>설명

- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 사용하거나 SQL Server Management Studio 또는 [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)를 사용하여 게시자에서 Transact-SQL 문을 실행하여 복제를 구성할 수 있습니다.
- 복제는 SQL Server 인증 로그인만을 사용하여 Azure SQL Edge 인스턴스에 연결할 수 있습니다.
- 복제된 테이블에는 기본 키가 있어야 합니다.
- SQL Server에서 단일 게시는 Azure SQL Edge 및 SQL Server(온-프레미스 및 Azure 가상 머신의 SQL Server) 구독자를 모두 지원할 수 있습니다.  
- 복제 관리, 모니터링 및 문제 해결은 온-프레미스 SQL Server에서 수행되어야 합니다.  
- Azure SQL Edge에 대한 푸시 구독만 지원됩니다.  
- `@subscriber_type = 0`만 Azure SQL Edge에 대한 **sp_addsubscription**에서 지원됩니다.  
- Azure SQL Edge는 양방향, 즉시, 업데이트 가능한 또는 피어 투 피어 복제를 지원하지 않습니다.
- Azure SQL Edge는 SQL Server 또는 Azure SQL Database Managed Instance에서 사용할 수 있는 기능의 하위 집합만 지원하므로, 지원되지 않는 기능을 하나 이상 포함하는 데이터베이스(또는 데이터베이스 내의 개체)를 복제하려고 하면 오류가 발생합니다. 예를 들어 공간 데이터 형식의 개체를 포함하는 데이터베이스를 복제하려고 하면 오류가 발생합니다. Azure SQL Edge에서 지원되는 기능에 대한 자세한 내용은 [Azure SQL Edge의 지원되는 기능](features.md)을 참조하세요.

## <a name="scenarios"></a>시나리오  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Edge 인스턴스의 참조 데이터 초기화

복제가 유용할 수 있는 일반적인 시나리오는 시간이 지남에 따라 변경되는 참조 데이터를 사용하여 에지 인스턴스를 초기화해야 하는 경우입니다. 예를 들어, 온-프레미스 SQL Server 인스턴스에서 학습된 후 에지 인스턴스에서 ML 모델을 업데이트할 수 있습니다.

1. 온-프레미스 SQL Server 데이터베이스에서 트랜잭션 복제 게시를 만듭니다.  
2. 온-프레미스에서 SQL Server는 **새 구독 마법사** 또는 Transact-SQL 문을 사용하여 Azure SQL Edge에 대한 구독 푸시를 생성합니다.  
3. Azure SQL Edge에서 복제된 데이터베이스는 스냅샷 에이전트에서 생성되고 배포 에이전트에서 배포 및 배달된 스냅샷을 사용하거나 게시자의 데이터베이스 백업을 사용하여 초기화할 수 있습니다. 다시 한 번, 데이터베이스 백업에 Azure SQL Edge에서 지원하지 않는 개체/기능이 포함되어 있으면 복원 작업은 실패합니다.

## <a name="limitations"></a>제한 사항

다음은 Azure SQL Edge 구독에 대해 지원되지 않는 옵션입니다.

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
- 파일 스트림, hierarchyid 또는 공간 데이터 형식을 복사합니다.
- hierarchyid를 MAX 데이터 형식으로 변환  
- 공간을 MAX 데이터 형식으로 변환  
- 확장 속성 복사  
- 권한 복사  

## <a name="examples"></a>예

게시 및 밀어넣기 구독을 만듭니다. 자세한 내용은 다음을 참조하세요.
  
- [게시 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 구독자로 Azure SQL Edge 서버 이름/IP(예: **myEdgeinstance,1433**) 및 대상 데이터베이스로 Azure SQL Edge 인스턴스의 데이터베이스 이름(예: **AdventureWorks**)을 사용하여 [푸시 구독을 만듭니다](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/).  

## <a name="see-also"></a>참고 항목  

- [게시 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [밀어넣기 구독 만들기](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [복제 유형](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [모니터링(복제)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [구독 초기화](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


