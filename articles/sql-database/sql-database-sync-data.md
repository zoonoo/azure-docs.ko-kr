---
title: "데이터 동기화(미리 보기) | Microsoft Docs"
description: "이 개요에서는 Azure SQL 데이터 동기화(미리 보기)를 소개합니다."
services: sql-database
documentationcenter: 
author: douglaslms
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 075b5563688158289d51f2f0b5da4a3441ddd13a
ms.contentlocale: ko-kr
ms.lasthandoff: 06/29/2017


---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스의 데이터 동기화

SQL 데이터 동기화는 여러 SQL Database 및 SQL Server 인스턴스 간에 양방향으로 선택한 데이터를 동기화할 수 있는 Azure SQL Database에 기반한 서비스입니다.

데이터 동기화는 동기화 그룹의 개념에 기반합니다. 동기화 그룹은 동기화하려는 데이터베이스의 그룹입니다.

동기화 그룹에는 다음과 같은 여러 가지 속성이 있습니다.

-   **동기화 스키마**는 동기화할 데이터에 대해 설명합니다.

-   **동기화 방향**은 양방향일 수도 있고 한 방향으로만 전달될 수 있습니다. 즉, 동기화 방향은 *허브에서 구성원*이거나 *구성원에게서 허브* 또는 양쪽 모두일 수 있습니다.

-   **동기화 간격**은 동기화가 발생하는 간격입니다.

-   **충돌 해결 정책**은 그룹 수준 정책으로 *허브 우선*일 수도 있고 *구성원 우선*일 수도 있습니다.

데이터 동기화는 허브 및 스포크 토폴로지를 사용하여 데이터를 동기화합니다. 그룹의 데이터베이스 중 하나를 허브 데이터베이스로 정의해야 합니다. 데이터베이스의 나머지 부분은 구성원 데이터베이스입니다. 동기화는 허브 및 개별 구성원 사이에서만 발생합니다.
-   **허브 데이터베이스**는 Azure SQL Database여야 합니다.
-   **구성원 데이터베이스**는 SQL Database, 온-프레미스 SQL Server 데이터베이스 또는 Azure 가상 컴퓨터의 SQL Server 인스턴스일 수 있습니다.
-   **동기화 데이터베이스**는 데이터 동기화에 대한 메타데이터 및 로그를 포함합니다. 동기화 데이터베이스는 허브 데이터베이스와 동일한 지역에 있는 Azure SQL Database여야 합니다. 동기화 데이터베이스는 생성된 고객 및 소유한 고객입니다.

> [!NOTE]
> 온-프레미스 데이터베이스를 사용하는 경우 [로컬 에이전트를 구성](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync)해야 합니다.

![데이터베이스 간 데이터 동기화](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>데이터 동기화를 사용하는 경우

데이터 동기화는 몇몇 Azure SQL Database 또는 SQL Server 데이터베이스 간에 데이터를 최신 상태로 유지해야 하는 경우에 유용합니다. 데이터 동기화에 대한 주요 사용 사례는 다음과 같습니다.

-   **하이브리드 데이터 동기화:** 데이터 동기화를 사용하면 온-프레미스 데이터베이스와 Azure SQL Databases 간에 데이터를 동기화하여 SQL에서 해당 데이터 계층을 사용하여 하이브리드 응용 프로그램을 사용하도록 설정할 수 있습니다. 이 기능은 클라우드로 이동하려는 고객에게 표시되고 Azure에 응용 프로그램의 일부를 배치할 수 있습니다.

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
데이터 동기화는 트리거 삽입, 업데이트 및 삭제를 사용하여 변경 내용을 추적합니다. 사용자 데이터베이스에서 추가 표를 만듭니다. 이러한 작업은 데이터베이스 워크로드에 영향을 주므로 서비스 계층을 평가하고 필요한 경우를 업그레이드합니다.

### <a name="eventual-consistency"></a>결과적 일관성
데이터 동기화가 트리거 기반이기 때문에 트랜잭션 일관성이 보장되지 않습니다. Microsoft는 결과적으로 모든 변경 내용을 적용하고 데이터 동기화가 데이터 손실을 발생하지 않도록 보장합니다.

### <a name="unsupported-data-types"></a>지원되지 않는 데이터 형식

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection(XML 지원)

-   Cursor, Timestamp, Hierarchyid

### <a name="requirements"></a>요구 사항

-   각 표에는 기본 키가 있어야 합니다.

-   표에는 기본 키가 없는 ID 열이 있을 수 없습니다.

-   데이터베이스 이름은 특수 문자를 포함할 수 없습니다.

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

## <a name="next-steps"></a>다음 단계

SQL Database 및 SQL 데이터 동기화에 대한 자세한 내용은 다음을 참조하세요.

-   [SQL 데이터 동기화 시작](sql-database-get-started-sql-data-sync.md)

-   [전체 SQL 데이터 동기화 기술 설명서 다운로드](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)

-   [SQL 데이터 동기화 REST API 설명서 다운로드](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

-   [SQL 데이터베이스 개요](sql-database-technical-overview.md)

-   [데이터베이스 수명 주기 관리](https://msdn.microsoft.com/library/jj907294.aspx)



