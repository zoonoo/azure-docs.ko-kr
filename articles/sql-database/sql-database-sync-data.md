---
title: "데이터 동기화(미리 보기) | Microsoft Docs"
description: "이 개요에서는 Azure SQL 데이터 동기화(미리 보기)를 소개합니다."
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 34bc9588745eb24d8b8c2e81389a9e5144497b34
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스의 데이터 동기화

SQL 데이터 동기화는 여러 SQL Database 및 SQL Server 인스턴스 간에 양방향으로 선택한 데이터를 동기화할 수 있는 Azure SQL Database에 기반한 서비스입니다.

데이터 동기화는 동기화 그룹의 개념에 기반합니다. 동기화 그룹은 동기화하려는 데이터베이스의 그룹입니다.

동기화 그룹의 속성은 다음과 같습니다.

-   **동기화 스키마**는 동기화할 데이터에 대해 설명합니다.

-   **동기화 방향**은 양방향일 수도 있고 한 방향으로만 전달될 수 있습니다. 즉, 동기화 방향은 *허브에서 구성원*이거나 *구성원에게서 허브* 또는 양쪽 모두일 수 있습니다.

-   **동기화 간격**은 동기화가 발생하는 간격입니다.

-   **충돌 해결 정책**은 그룹 수준 정책으로 *허브 우선*일 수도 있고 *구성원 우선*일 수도 있습니다.

데이터 동기화는 허브 및 스포크 토폴로지를 사용하여 데이터를 동기화합니다. 그룹의 데이터베이스 중 하나를 허브 데이터베이스로 정의합니다. 데이터베이스의 나머지 부분은 구성원 데이터베이스입니다. 동기화는 허브 및 개별 구성원 사이에서만 발생합니다.
-   **허브 데이터베이스**는 Azure SQL Database여야 합니다.
-   **구성원 데이터베이스**는 SQL Database, 온-프레미스 SQL Server 데이터베이스 또는 Azure 가상 컴퓨터의 SQL Server 인스턴스일 수 있습니다.
-   **동기화 데이터베이스**는 데이터 동기화에 대한 메타데이터 및 로그를 포함합니다. 동기화 데이터베이스는 허브 데이터베이스와 동일한 지역에 있는 Azure SQL Database여야 합니다. 동기화 데이터베이스는 생성된 고객 및 소유한 고객입니다.

> [!NOTE]
> 온-프레미스 데이터베이스를 사용하는 경우 [로컬 에이전트를 구성](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync)해야 합니다.

![데이터베이스 간 데이터 동기화](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>데이터 동기화를 사용하는 경우

데이터 동기화는 몇몇 Azure SQL Database 또는 SQL Server 데이터베이스 간에 데이터를 최신 상태로 유지해야 하는 경우에 유용합니다. 데이터 동기화에 대한 주요 사용 사례는 다음과 같습니다.

-   **하이브리드 데이터 동기화:** 데이터 동기화를 사용하면 온-프레미스 데이터베이스와 Azure SQL Databases 간에 데이터를 동기화하여 하이브리드 응용 프로그램을 사용하도록 설정할 수 있습니다. 이 기능은 클라우드로 이동하려는 고객에게 표시되고 Azure에 응용 프로그램의 일부를 배치할 수 있습니다.

-   **배포된 응용 프로그램:** 많은 경우에 다른 데이터베이스에서 다양한 워크로드를 구분하는 데 도움이 됩니다. 예를 들어 대형 프로덕션 데이터베이스가 있지만 이 데이터에 대한 보고 또는 분석 워크로드를 실행해야 하는 경우 해당 추가 워크로드에 대한 두 번째 데이터베이스를 만드는 데 도움이 됩니다. 이 방법을 사용하면 프로덕션 워크로드에 미치는 영향을 최소화합니다. 데이터 동기화를 사용하여 이러한 두 데이터베이스의 동기화를 유지할 수 있습니다.

-   **전역 분산 응용 프로그램:** 많은 비즈니스는 여러 지역 및 여러 국가 걸쳐 있습니다. 네트워크 대기 시간을 최소화하려면 가까운 지역에 데이터가 위치하는 것이 좋습니다. 데이터 동기화를 사용하면 전 세계 여러 지역에서 데이터베이스를 쉽게 동기화할 수 있습니다.

다음과 같은 시나리오에서는 데이터 동기화를 사용하지 않는 것이 좋습니다.

-   재해 복구

-   읽기 크기 조정

-   ETL(OLTP 및 OLAP 간)

-   온-프레미스 SQL Server에서 Azure SQL Database로 마이그레이션

## <a name="how-does-data-sync-work"></a>데이터 동기화는 어떻게 작동하나요? 

-   **데이터 변경 내용 추적:** 데이터 동기화는 트리거 삽입, 업데이트 및 삭제를 사용하여 변경 내용을 추적합니다. 변경 내용은 사용자 데이터베이스에 있는 추가 표에 기록됩니다.

-   **데이터 동기화:** 데이터 동기화는 허브 및 스포크 모델에서 설계됩니다. 허브는 개별적으로 각 구성원과 동기화됩니다. 허브의 변경 내용이 구성원에 다운로드된 다음 구성원의 변경 내용은 허브에 업로드됩니다.

-   **충돌 해결:** 데이터 동기화는 충돌 해결을 위해 *허브 우선* 또는 *멤버 우선*이라는 두 가지 옵션을 제공합니다.
    -   *허브 우선*을 선택하는 경우 허브의 변경 내용은 항상 구성원의 변경 내용을 덮어씁니다.
    -   *구성원 우선*을 선택하는 경우 구성원의 변경 내용은 항상 허브의 변경 내용을 덮어씁니다. 구성원이 둘 이상인 경우 최종 값은 먼저 동기화된 구성원에 따라 달라집니다.

## <a name="limitations-and-considerations"></a>제한 사항 및 고려 사항

### <a name="performance-impact"></a>성능에 미치는 영향
데이터 동기화는 트리거 삽입, 업데이트 및 삭제를 사용하여 변경 내용을 추적합니다. 변경 내용 추적을 위해 사용자 데이터베이스에 추가 테이블을 만듭니다. 이러한 변경 내용 추적 작업은 데이터베이스 워크로드에 영향을 줍니다. 서비스 계층을 평가하고 필요한 경우 업그레이드합니다.

### <a name="eventual-consistency"></a>결과적 일관성
데이터 동기화가 트리거 기반이기 때문에 트랜잭션 일관성이 보장되지 않습니다. Microsoft는 결과적으로 모든 변경 내용을 적용하고 데이터 동기화가 데이터 손실을 발생하지 않도록 보장합니다.

### <a name="unsupported-data-types"></a>지원되지 않는 데이터 형식

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection(XML 지원)

-   Cursor, Timestamp, Hierarchyid

### <a name="requirements"></a>요구 사항

-   각 표에는 기본 키가 있어야 합니다. 어느 행에서도 기본 키 값은 변경하지 않습니다. 변경이 필요한 경우 행을 삭제하고 새 기본 키 값으로 새로 만듭니다. 

-   테이블에는 기본 키가 없는 ID 열이 있을 수 없습니다.

-   개체(데이터베이스, 테이블 및 열) 이름에는 인쇄 가능한 문자 마침표(.), 왼쪽 대괄호([) 또는 오른쪽 대괄호(])를 사용할 수 없습니다.

-   스냅숏 격리를 사용해야 합니다. 자세한 내용은 [SQL Server에서의 스냅숏 격리](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server)를 참조하세요.

### <a name="limitations-on-service-and-database-dimensions"></a>서비스 및 데이터베이스 차원에 대한 제한 사항

|                                                                 |                        |                             |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| **차원**                                                      | **제한**              | **해결 방법**              |
| 데이터베이스가 속할 수 있는 동기화 그룹의 최대 수입니다.       | 5                      |                             |
| 단일 동기화 그룹에서 끝점의 최대 수입니다.              | 30                     | 여러 동기화 그룹 만들기 |
| 단일 동기화 그룹에서 온-프레미스 끝점의 최대 수입니다. | 5                      | 여러 동기화 그룹 만들기 |
| 데이터베이스, 테이블, 스키마 및 열 이름                       | 이름당 50자 |                             |
| 동기화 그룹의 표                                          | 500                    | 여러 동기화 그룹 만들기 |
| 동기화 그룹에서 표의 열                              | 1000                   |                             |
| 표의 데이터 행 크기                                        | 24Mb                  |                             |
| 최소 동기화 간격                                           | 5분              |                             |

## <a name="common-questions"></a>일반적인 질문

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>데이터 동기화에서 데이터를 동기화하는 빈도는 어떻게 되나요? 
최소 빈도는 5분마다입니다.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>데이터 동기화를 사용하여 SQL Server 온-프레미스 데이터베이스 사이에서 동기화할 수 있나요? 
직접 끌 수는 없습니다. 그러나 Azure에서 허브 데이터베이스를 만든 다음 온-프레미스 데이터베이스를 동기화 그룹에 추가하여 간접적으로 SQL Server 온-프레미스 데이터베이스 사이에서 동기화할 수 있습니다.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>데이터 동기화를 사용하여 프로덕션 데이터베이스에서 빈 데이터베이스로 데이터를 시드한 다음 동기화된 상태로 유지할 수 있나요? 
예. 원본에서 스크립팅하여 수동으로 새 데이터베이스에 스키마를 만듭니다. 스키마를 만든 후 테이블을 동기화 그룹에 추가하여 데이터를 복사하고 동기화된 상태로 유지합니다.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>내가 만들지 않은 테이블이 표시되는 이유는 무엇인가요?  
데이터 동기화는 변경 내용 추적을 위해 데이터베이스에 추가 테이블을 만듭니다. 해당 테이블을 삭제하지 마세요. 삭제하면 데이터 동기화의 작동이 중지됩니다.
   
### <a name="i-got-an-error-message-that-said-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-the-error"></a>"\<column\> 열에 NULL 값을 삽입할 수 없습니다. 열에는 Null을 사용할 수 없습니다."라는 오류 메시지를 받았습니다. 이 오류는 어떤 의미이며 오류를 수정할 수 있는 방법은 무엇인가요? 
이 오류 메시지는 다음 두 가지 문제 중 하나를 나타냅니다.
1.  기본 키가 없는 테이블이 있을 수 있습니다. 이 문제를 해결하려면 동기화하는 모든 테이블에 기본 키를 추가합니다.
2.  CREATE INDEX 문에 WHERE 절이 있을 수 있습니다. 동기화에서는 이 조건이 처리되지 않습니다. 이 문제를 해결하려면 WHERE 절을 제거하거나 수동으로 모든 데이터베이스를 변경합니다. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>데이터 동기화에서는 어떻게 순환 참조가 처리되나요? 즉, 여러 동기화 그룹에서 동일한 데이터가 동기화되어 결과적으로 계속 변경되는 경우 어떻게 처리되나요?
데이터 동기화에서는 순환 참조가 처리되지 않습니다. 순환 참조가 발생하지 않도록 해야 합니다. 

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>데이터 동기화를 사용하여 데이터베이스를 어떻게 내보내고 가져오나요?
데이터베이스를 .bacpac 파일로 내보내고 가져와 새 데이터베이스를 만든 후에는 새 데이터베이스에서 데이터 동기화를 사용하기 위해 다음 두 가지를 수행해야 합니다.
1.  [이 스크립트](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql)를 사용하여 **새 데이터베이스**에서 데이터 동기화 개체와 파생 테이블을 정리합니다. 이 스크립트는 데이터베이스에서 모든 필요한 데이터 동기화 개체를 삭제합니다.
2.  새 데이터베이스를 통해 동기화 그룹을 다시 만듭니다. 더 이상 기존 동기화 그룹이 필요하지 않으면 삭제합니다.

## <a name="next-steps"></a>다음 단계

SQL 데이터 동기화에 대한 자세한 내용은 다음을 참조하세요.

-   [SQL 데이터 동기화 시작](sql-database-get-started-sql-data-sync.md)

-   SQL Data Sync 구성 방법을 보여주는 전체 PowerShell 예제:
    -   [PowerShell을 사용하여 여러 Azure SQL Database 간 동기화](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [PowerShell을 사용하여 Azure SQL Database와 SQL Server 온-프레미스 데이터베이스 간 동기화](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [SQL 데이터 동기화 REST API 설명서 다운로드](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL Database에 대한 자세한 내용은 다음을 참조하세요.

-   [SQL Database 개요](sql-database-technical-overview.md)

-   [데이터베이스 수명 주기 관리](https://msdn.microsoft.com/library/jj907294.aspx)
