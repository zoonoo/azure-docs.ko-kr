---
title: Replikáció
description: 탄력적 풀에서 Azure SQL Database 단일 데이터베이스 및 데이터베이스와 SQL Server 복제를 사용 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f718bc17b987926f4324635f096d5983acdb63fc
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997278"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>단일 및 풀링된 데이터베이스 SQL Database 복제

SQL Server 복제는 Azure SQL Database의 [SQL Database 서버](sql-database-servers.md) 에서 단일 및 풀링된 데이터베이스로 구성할 수 있습니다.  

## <a name="supported-configurations"></a>**지원 되는 구성:**
  
- SQL Server은 온-프레미스에서 실행 되는 SQL Server의 인스턴스이거나 클라우드의 Azure 가상 머신에서 실행 되는 SQL Server의 인스턴스일 수 있습니다. 자세한 내용은 [Azure Virtual Machines 개요를 SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)를 참조 하세요.  
- Azure SQL database는 SQL Server 게시자의 푸시 구독자 여야 합니다.  
- 배포 데이터베이스 및 복제 에이전트는 Azure SQL 데이터베이스에 배치할 수 없습니다.  
- 스냅숏 및 단방향 트랜잭션 복제가 지원 됩니다. 피어 투 피어 트랜잭션 복제 및 병합 복제는 지원 되지 않습니다.
- 복제는 Azure SQL Database Managed Instance의 공개 미리 보기에서 사용할 수 있습니다. 게시자, 배포자 및 구독자 데이터베이스를 호스팅할 수 Managed Instance. 자세한 내용은 [SQL Database Managed Instance를 사용 하 여 복제](replication-with-sql-database-managed-instance.md)를 참조 하세요.

## <a name="versions"></a>Verziók  

온-프레미스 SQL Server 게시자 및 배포자는 다음 버전 중 하나 이상을 사용 해야 합니다.  

- SQL Server 2016 이상
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) 또는 [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) 또는 [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> 지원 되지 않는 버전을 사용 하 여 복제를 구성 하려고 하면 오류 번호가 MSSQL_REPL20084 (구독자에 연결할 수 없습니다.) MSSQL_REPL40532 (로그인에서 요청한 > 서버 \<이름을 열 수 없습니다. 로그인 하지 못했습니다.).  

Azure SQL Database의 모든 기능을 사용 하려면 최신 버전의 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 및 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)를 사용 해야 합니다.  

  
## <a name="remarks"></a>Megjegyzések

- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 를 사용 하거나 게시자에서 transact-sql 문을 실행 하 여 복제를 구성할 수 있습니다. Azure Portal를 사용 하 여 복제를 구성할 수 없습니다.  
- 복제는 SQL Server 인증 로그인만 사용 하 여 Azure SQL database에 연결할 수 있습니다.
- 복제 된 테이블에는 기본 키가 있어야 합니다.  
- 기존 Azure 구독이 있어야 합니다.  
- Azure SQL database 구독자는 모든 지역에 있을 수 있습니다.  
- SQL Server의 단일 게시는 Azure SQL Database와 SQL Server (온-프레미스 및 Azure 가상 컴퓨터의 SQL Server) 구독자를 모두 지원할 수 있습니다.  
- 복제 관리, 모니터링 및 문제 해결은 온-프레미스 SQL Server에서 수행 해야 합니다.  
- Azure SQL Database에 대 한 밀어넣기 구독만 지원 됩니다.  
- SQL Database에 대 한 **sp_addsubscription** 에서는 `@subscriber_type = 0`만 지원 됩니다.  
- Azure SQL Database은 양방향, 즉시, 업데이트 또는 피어 투 피어 복제를 지원 하지 않습니다.

## <a name="replication-architecture"></a>복제 아키텍처  

![replication to sql-데이터베이스](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Alkalmazási helyzetek  

### <a name="typical-replication-scenario"></a>일반적인 복제 시나리오  

1. 온-프레미스 SQL Server 데이터베이스에서 트랜잭션 복제 게시를 만듭니다.  
2. 온-프레미스 SQL Server **새 구독 마법사** 또는 transact-sql 문을 사용 하 여 Azure SQL Database에 대 한 밀어넣기 구독을 만듭니다.  
3. Azure SQL Database에서 단일 및 풀링된 데이터베이스를 사용 하는 경우 초기 데이터 집합은 스냅숏 에이전트 만든 스냅숏이 며 배포 에이전트에서 배포 및 적용 됩니다. 관리 되는 인스턴스 데이터베이스를 사용 하 여 데이터베이스 백업을 사용 하 여 구독자 데이터베이스의 초기값을 설정할 수도 있습니다.

### <a name="data-migration-scenario"></a>데이터 마이그레이션 시나리오  

1. 트랜잭션 복제를 사용 하 여 온-프레미스 SQL Server 데이터베이스에서 Azure SQL Database 데이터를 복제 합니다.  
2. 클라이언트 또는 중간 계층 응용 프로그램을 리디렉션하여 Azure SQL database 복사본을 업데이트 합니다.  
3. 테이블의 SQL Server 버전 업데이트를 중지 하 고 게시를 제거 합니다.  

## <a name="limitations"></a>Korlátozások

Azure SQL Database 구독에 대해 지원 되지 않는 옵션은 다음과 같습니다.

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
- 필터링 된 인덱스 복사  
- 데이터 압축 특성 복사  
- 스파스 열 특성 복사  
- Filestream을 MAX 데이터 형식으로 변환  
- Hierarchyid를 MAX 데이터 형식으로 변환  
- 공간을 MAX 데이터 형식으로 변환  
- 확장 속성 복사  
- 권한 복사  

### <a name="limitations-to-be-determined"></a>결정 해야 하는 제한 사항

- 데이터 정렬 복사  
- SP의 직렬화 된 트랜잭션에서 실행  

## <a name="examples"></a>Példák

게시 및 밀어넣기 구독을 만듭니다. További információ eléréséhez lásd:
  
- [Create a Publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 구독자 (예: **: n'azuresqldbdns.database.windows.net**)로 Azure SQL Database 서버 이름을 사용 하 고 대상 데이터베이스로 Azure SQL 데이터베이스 이름 (예: **AdventureWorks**)을 사용 하 여 [밀어넣기 구독을 만듭니다](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) .  

## <a name="see-also"></a>Lásd még:  

- [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md)
- [Create a Publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [밀어넣기 구독 만들기](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [A replikáció típusai](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [모니터링 (복제)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [구독 초기화](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
