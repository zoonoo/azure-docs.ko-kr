---
title: 트랜잭션 복제
description: Azure SQL Database에서 단일 데이터베이스, 풀링된 데이터베이스 및 인스턴스 데이터베이스와 함께 SQL Server 트랜잭션 복제를 사용하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607587"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Azure SQL Database에서 단일 데이터베이스, 풀링된 데이터베이스 및 인스턴스 데이터베이스와 함께 트랜잭션 복제

트랜잭션 복제는 Azure SQL Database 또는 SQL Server의 테이블에서 원격 데이터베이스에 있는 테이블로 데이터를 복제할 수 있도록 하는 Azure SQL Database 및 SQL Server의 기능입니다. 이 기능을 사용하면 서로 다른 데이터베이스의 여러 테이블을 동기화할 수 있습니다.

## <a name="when-to-use-transactional-replication"></a>트랜잭션 복제를 사용하는 경우

트랜잭션 복제는 다음과 같은 시나리오에서 유용합니다.
- 게시를 수행하면 데이터베이스에 있는 하나 이상의 테이블에서 변경이 수행된 후 변경을 위해 구독되는 하나 이상의 SQL Server 또는 Azure SQL 데이터베이스로 배포됩니다.
- 여러 개의 분산된 데이터베이스를 동기화된 상태로 유지합니다.
- 변경 내용을 지속적으로 게시하여 한 SQL Server 또는 관리되는 인스턴스에서 다른 데이터베이스로 데이터베이스를 마이그레이션합니다.

## <a name="overview"></a>개요

트랜잭션 복제의 주요 구성 요소는 다음 그림에 나와 있습니다.  

![SQL Database를 사용한 복제](media/replication-to-sql-database/replication-to-sql-database.png)

**게시자**는 배포자에게 업데이트를 전송하여 일부 테이블(문서)에 대해 변경된 내용을 게시하는 인스턴스 또는 서버입니다. 다음 버전의 SQL Server에서 지원되는 온-프레미스 SQL Server에서 모든 Azure SQL 데이터베이스로 게시할 수 있습니다.

- SQL Server 2019(미리 보기)
- SQL Server 2016 ~ SQL 2017
- SQL Server 2014 SP1 CU3 이상(12.00.4427)
- SQL Server 2014 RTM CU10(12.00.2556)
- SQL Server 2012 SP3 이상(11.0.6020)
- SQL Server 2012 SP2 CU8(11.0.5634.0)
- Azure에서 게시 개체를 지원하지 않는 다른 버전의 SQL Server에서는 [데이터 다시 게시](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) 방법을 사용하여 데이터를 최신 버전의 SQL Server로 이동할 수 있습니다. 

**배포자**는 게시자에서 문서의 변경 내용을 수집하고 구독자에게 배포하는 인스턴스 또는 서버입니다. 배포자는 Azure SQL Database 관리 인스턴스 또는 SQL Server(게시자 버전과 같거나 더 높은 모든 버전)일 수 있습니다. 

**구독자**는 게시자에 대한 변경 내용을 수신하는 인스턴스 또는 서버입니다. 구독자는 Azure SQL Database 또는 SQL Server 데이터베이스의 단일 데이터베이스, 풀링된 데이터베이스 및 인스턴스 데이터베이스 중 하나일 수 있습니다. 단일 또는 풀링된 데이터베이스의 구독자는 푸시 구독자로 구성해야 합니다. 

| 역할 | 단일 및 풀링된 데이터베이스 | 인스턴스 데이터베이스 |
| :----| :------------- | :--------------- |
| **게시자** | 예 | 예 | 
| **배포자** | 예 | 예|
| **끌어오기 구독자** | 예 | 예|
| **밀어넣기 구독자**| 예 | 예|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > 배포자는 Instance 데이터베이스이고 구독자가 아닌 경우 끌어오기 구독이 지원되지 않습니다. 

다음과 같은 여러 [복제 유형](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)이 있습니다.


| 복제 | 단일 및 풀링된 데이터베이스 | 인스턴스 데이터베이스|
| :----| :------------- | :--------------- |
| [**표준 거래**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 예(구독자로) | 예 | 
| [**스냅숏**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 예(구독자로) | 예|
| [**복제 병합**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 예 | 예|
| [**피어-투-피어**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 예 | 예|
| [**양방향**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 예 | 예|
| [**업데이터 구독**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 예 | 예|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - 이전 버전을 사용하여 복제를 구성하는 시도는 오류 번호 MSSQL_REPL20084(프로세스가 구독자에 연결할 수 없습니다.) 및 MSSQ_REPL40532(로그인에서 요청한 서버 \<이름>을 열 수 없습니다. 로그인이 실패했습니다.)가 발생할 수 있습니다.
  > - Azure SQL Database의 모든 기능을 사용하려면 최신 버전의 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 및 [SSDT(SQL Server Data Tools)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)를 사용해야 합니다.
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>인스턴스 데이터베이스 및 온-프레미스 시스템에 대한 지원 가능성 매트릭스
  예를 들어 데이터베이스의 복제 지원 성 매트릭스는 SQL Server 온-프레미스용과 동일합니다. 
  
| **게시자**   | **배포자** | **가입자** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>요구 사항

- 연결은 복제 참가자 간에 SQL 인증을 사용합니다. 
- 복제에 사용되는 작업 디렉터리의 Azure Storage 계정 공유 
- Azure 파일 공유에 액세스하려면 관리되는 인스턴스 서브넷의 보안 규칙에서 포트 445(TCP 아웃바운드)를 열어야 합니다. 
- 게시자/배포자가 관리되는 인스턴스에 있고 구독자가 없는 경우 포트 1433(TCP 아웃바운드)을 열어야 합니다. 포트 1433 **대상 서비스 태그에** 대한 `allow_linkedserver_outbound` 관리되는 인스턴스 NSG 아웃바운드 `virtualnetwork` 보안 `internet`규칙을 에서 로 변경해야 할 수도 있습니다. 
- 모든 유형의 복제 참가자(게시자, 배포자, 끌어오기 구독자 및 푸시 구독자)는 관리되는 인스턴스에 배치할 수 있지만 게시자와 배포자는 클라우드 또는 온-프레미스 모두에 있어야 합니다.
- 게시자, 배포자 및/또는 구독자가 서로 다른 가상 네트워크에 있는 경우 게시자와 배포자 간에 VPN 피어링이 있거나 배포자와 구독자 간에 VPN 피어링이 있도록 각 엔터티 간에 VPN 피어링을 설정해야 합니다. 


>[!NOTE]
> - 배포자가 인스턴스 데이터베이스이고 구독자가 온-프레미스일 때 아웃바운드 네트워크 보안 그룹(NSG) 포트(445)가 차단된 경우 Azure Storage File에 연결할 때 오류 53이 발생할 수 있습니다. 이 문제를 해결 하려면 [vNet NSG를 업데이트](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) 합니다. 


### <a name="compare-data-sync-with-transactional-replication"></a>트랜잭션 복제와 데이터 동기화 비교

| | 데이터 동기화 | 트랜잭션 복제 |
|---|---|---|
| 장점 | - 활성-활성 지원<br/>- 온-프레미스 및 Azure SQL Database 간 양방향 | - 낮은 대기 시간<br/>- 트랜잭션 일관성<br/>- 마이그레이션 후 기존 토폴로지 다시 사용 |
| 단점 | - 5분 이상의 대기 시간<br/>- 트랜잭션 일관성 부족<br/>- 성능에 더 많은 영향을 미침 | - Azure SQL Database 단일 데이터베이스 또는 풀링된 데이터베이스에서 게시할 수 없음<br/>- 높은 유지 관리 비용 |
| | | |

## <a name="common-configurations"></a>일반 구성

일반적으로 게시자와 배포자는 모두 클라우드 또는 온-프레미스에 있어야 합니다. 현재 지원되는 구성은 다음과 같습니다. 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>관리되는 인스턴스에서 로컬 배포자를 보유한 게시자

![게시자 및 배포자로서의 단일 인스턴스](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

게시자 및 배포자는 단일 관리되는 인스턴스 내에서 구성되며 변경 내용을 다른 관리되는 인스턴스, 단일 데이터베이스, 풀된 데이터베이스 또는 SQL Server 온-프레미스에 배포합니다. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>관리되는 인스턴스에 원격 배포자를 사용하여 게시자

이 구성에서 관리되는 인스턴스 는 많은 소스 관리 인스턴스를 제공하고 관리되는 인스턴스, 단일 데이터베이스, 풀된 데이터베이스 또는 SQL Server에서 하나 또는 여러 대상에 변경 내용을 배포할 수 있는 다른 관리되는 인스턴스에 배치된 배포자에 변경 내용을 게시합니다.

![게시자 및 배포자에 대한 별도 인스턴스](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

게시자 및 배포자는 두 개의 관리되는 인스턴스에서 구성됩니다. 이 구성에는 몇 가지 제약 조건이 있습니다. 

- 관리되는 두 인스턴스가 모두 동일한 vNet에 있습니다.
- 두 관리되는 인스턴스는 동일한 위치에 있습니다.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>단일 데이터베이스, 풀링된 데이터베이스 및 인스턴스 데이터베이스에 구독자가 있는 온-프레미스의 게시자 및 배포자 

![구독자로서의 Azure SQL DB](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
이 구성에서 Azure SQL Database(단일 데이터베이스, 풀링된 데이터베이스 및 인스턴스 데이터베이스)는 구독자입니다. 이 구성은 온-프레미스에서 Azure로의 마이그레이션을 지원합니다. 구독자가 단일 또는 풀링된 데이터베이스에 있는 경우에는 푸시 모드여야 합니다.  

## <a name="with-failover-groups"></a>장애 조치 그룹

[장애 조치 그룹의](sql-database-auto-failover-group.md) **게시자** 또는 **배포자** 인스턴스에서 지역 복제를 사용하도록 설정된 경우 관리되는 인스턴스 관리자는 이전 주 복제본의 모든 게시를 정리하고 장애 조치(failover)가 발생한 후 새 주 복제본에서 다시 구성해야 합니다. 이 시나리오에서는 다음 활동이 필요합니다.

1. 데이터베이스에서 실행 중인 모든 복제 작업을 중지합니다(있는 경우).
2. 게시자 데이터베이스에서 다음 스크립트를 실행하여 게시자에서 구독 메타데이터를 삭제합니다.

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. 구독자에서 구독 메타데이터를 삭제합니다. 구독자 인스턴스의 구독 데이터베이스에서 다음 스크립트를 실행합니다.

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. 게시된 데이터베이스에서 다음 스크립트를 실행하여 게시자에서 모든 복제 개체를 강제로 삭제합니다.

   ```sql
   EXEC sp_removedbreplication
   ```

1. 원래 기본 인스턴스에서 이전 배포기를 강제로 삭제합니다(배포기를 사용하는 데 사용한 이전 주 인스턴스로 다시 장애토인경우). 이전 배포자 관리 인스턴스에서 마스터 데이터베이스에서 다음 스크립트를 실행합니다.

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

장애 조치 그룹의 **구독자** 인스턴스에서 지역 복제가 활성화된 경우 구독자 관리 인스턴스의 장애 조치 그룹 수신기 끝점에 연결하도록 게시를 구성해야 합니다. 장애 조치의 경우 관리되는 인스턴스 관리자의 후속 작업은 발생한 장애 조치 유형에 따라 달라집니다. 

- 데이터 손실이 없는 장애 조치의 경우 장애 조치 후에도 복제가 계속 작동합니다. 
- 데이터 손실이 있는 장애 조치의 경우 복제도 작동합니다. 손실된 변경 내용을 다시 복제합니다. 
- 데이터 손실이 있는 장애 조치의 경우 데이터 손실이 배포 데이터베이스 보존 기간을 벗어나면 관리되는 인스턴스 관리자가 구독 데이터베이스를 다시 초기화해야 합니다. 

## <a name="next-steps"></a>다음 단계

- [MI 게시자와 구독자 간의 복제 구성](replication-with-sql-database-managed-instance.md)
- [MI 게시자, MI 배포자 및 SQL Server 구독자 간에 복제 구성](sql-database-managed-instance-configure-replication-tutorial.md)
- [게시를 만듭니다.](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 구독자로 Azure SQL Database 서버 이름(예: `N'azuresqldbdns.database.windows.net`) 및 대상 데이터베이스로 Azure SQL Database 이름(예: **Adventureworks**)을 사용하여 [밀어넣기 구독을 만듭니다](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription). )


트랜잭션 복제 구성에 대한 자세한 내용은 다음 자습서를 참조하십시오.



## <a name="see-also"></a>관련 항목  

- [MI 및 장애 조치 그룹과의 복제](sql-database-managed-instance-transact-sql-information.md#replication)
- [SQL Database로 복제](replication-to-sql-database.md)
- [관리되는 인스턴스에 대한 복제](replication-with-sql-database-managed-instance.md)
- [발행물 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [밀어넣기 구독 만들기](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [복제 유형](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [모니터링(복제)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [구독 초기화](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
