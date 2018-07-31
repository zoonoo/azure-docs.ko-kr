---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 6월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216739"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Azure SQL Data Warehouse의 새로운 기능 2018년 7월
Azure SQL Data Warehouse는 지속적으로 개선 사항을 수신합니다. 이 문서에서는 2018년 7월에 도입된 새로운 기능과 변경 사항에 대해 설명합니다.


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>지역 및 서버 간 복원을 위한 보다 자세한 세분성
이제 24시간마다 진행되는 지역 중복 백업을 선택하는 대신, 임의 복원 지점을 사용하여 지역 및 서버 간에 복원을 수행할 수 있습니다. 지역 및 서버 간 복원은 사용자 정의 또는 자동 복원 지점 둘 다에서 지원되며, 추가 데이터 보호를 위한 보다 자세한 세분성을 지원합니다. 사용 가능한 복원 지점이 더 많이 있는 경우 지역 간에 복원을 수행할 때 데이터 웨어하우스의 논리적 일관성이 보장될 수 있습니다.

![복원 명령 - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![복원 옵션 - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

자세한 내용은 [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)(가속화되고 유연한 복원 지점) 블로그 게시물을 참조하세요.

## <a name="20-minute-restorations"></a>20분 복원
이제 SQL Data Warehouse는 데이터베이스 크기에 관계 없이, 모든 데이터 웨어하우스가 같은 지역 내에서 **20분 이내**에 복원될 것으로 제안합니다. 같은 논리적 서버로 복원되든, 같은 지역의 다른 논리적 서버로 복원되든, 이 복원 시간이 적용됩니다. 또한 복원 지점을 만드는 데 걸리는 시간을 줄이기 위해 스냅숏 프로세스가 개선되었습니다. 더 낮은 성능 수준(더 낮은 DWU 설정)에서는 스냅숏 생성 시간이 *2배 감소*됩니다.

자세한 내용은 [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)(가속화되고 유연한 복원 지점) 블로그 게시물을 참조하세요.

## <a name="custom-restoration-configurations"></a>사용자 지정 복원 구성
이제 Azure Portal에서 복원하는 경우 성능 수준(DWU)을 변경할 수 있습니다. 업그레이드된 Gen2 데이터 웨어하우스로도 복원할 수 있습니다. 이제 Gen 2 인스턴스로 복원하여 [Gen1 데이터 웨어하우스로 업그레이드](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)하기 전에 Gen2의 영향을 평가할 수 있습니다.

![사용자 지정 복원 구성 - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
[sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) 저장 프로시저는 Transact-SQL 일괄 처리의 매개 변수에 대한 메타데이터를 얻기 위해 도구에서 사용됩니다. 이 프로시저는 해당 매개 변수에 대한 추론된 형식 정보를 사용하여 일괄 처리의 각 매개 변수에 대해 하나의 행을 반환합니다. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

결과 집합에는 `@id` 매개 변수(부분 결과 표시)에 대한 메타데이터가 포함됩니다.
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```