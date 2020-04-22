---
title: '빠른 시작: 단일 T-SQL 문을 사용하여 데이터 대량 로드'
description: COPY 문을 사용하여 데이터 대량 로드
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d39b3085a802ca0ff745ab1f63f4a8fba966ea48
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115013"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>빠른 시작: COPY 문을 사용하여 데이터 대량 로드

이 빠른 시작에서는 처리량이 높은 데이터 수집을 위해 간단하고 유연한 [COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 문을 사용하여 SQL 풀로 데이터를 대량 로드합니다. COPY 문은 다음과 같은 기능을 제공하여 데이터를 원활하고 유연하게 로드할 수 있도록 권장되는 로드 유틸리티입니다.

- 데이터 웨어하우스에 대한 엄격한 제어 권한이 없어도 권한이 낮은 사용자가 로드할 수 있도록 허용
- 추가 데이터베이스 개체를 만들 필요 없이 단일 T-SQL 문만 활용합니다.
- SAS(공유 액세스 서명)를 사용하여 스토리지 계정 키를 노출하지 않고 보다 세부적인 사용 권한 모델 활용
- ERRORFILE 위치(REJECTED_ROW_LOCATION)에 대해 다른 스토리지 계정을 지정
- 각 대상 열에 대한 기본값을 사용자 지정하고 원본 데이터 필드를 지정하여 특정 대상 열에 로드
- CSV 파일에 대한 사용자 지정 행 종결자 지정
- CSV 파일에 대한 이스케이프 문자열, 필드 및 행 구분 기호
- CSV 파일에 대한 SQL Server 날짜 형식 활용
- 스토리지 위치 경로에 와일드카드 및 여러 파일 지정

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에서는 SQL 풀이 이미 있다고 가정합니다. SQL 풀을 만들지 않은 경우 [만들기 및 연결 - 포털](create-data-warehouse-portal.md) 빠른 시작을 사용합니다.

## <a name="create-the-target-table"></a>대상 테이블 만들기

이 예제에서는 뉴욕 택시 데이터 세트에서 데이터를 로드합니다. 단일 연도 내에서 수행된 택시 여행을 나타내는 Trip이라는 테이블을 로드합니다. 다음을 실행하여 테이블을 만듭니다.

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>COPY 문 실행

Azure Blob 스토리지 계정에서 Trip 테이블로 데이터를 로드하는 다음 COPY 문을 실행합니다.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>부하 모니터링

다음 쿼리를 주기적으로 실행하여 부하가 진행 중인지 확인합니다.

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>다음 단계

- 데이터 로드에 대한 모범 사례는 [데이터 로드에 대한 모범 사례](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)를 참조하세요.
- 데이터 로드에 대한 리소스를 관리하는 방법에 대한 자세한 내용은 [워크로드 격리](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)를 참조하세요. 
