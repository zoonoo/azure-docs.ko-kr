---
title: Azure Data Factory의 연결된 서비스 매개 변수화
description: Azure Data Factory에서 연결된 서비스를 매개 변수화하고 런타임에 동적 값을 전달하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/21/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 081d19cc845750f1392e2c1a14229a51d0df4cbc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276464"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Azure Data Factory의 연결된 서비스 매개 변수화

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이제 연결된 서비스를 매개 변수화하고 런타임에 동적 값을 전달할 수 있습니다. 예를 들어 동일한 논리 SQL server의 다른 데이터베이스에 연결 하려는 경우 이제 연결 된 서비스 정의에서 데이터베이스 이름을 매개 변수화 할 수 있습니다. 이렇게 하면 논리 SQL server의 각 데이터베이스에 대해 연결 된 서비스를 만들 필요가 없습니다. 예를 들어 연결된 서비스 정의에서 *사용자 이름*과 같은 기타 속성을 매개 변수화할 수 있습니다.

Azure Portal 또는 프로그래밍 인터페이스에서 Data Factory UI를 사용 하 여 연결 된 서비스를 매개 변수화 할 수 있습니다.

> [!TIP]
> 암호니 비밀은 매개 변수화하지 않는 것이 좋습니다. 대신, Azure Key Vault의 모든 연결 문자열을 저장하고 *비밀 이름*을 매개 변수화합니다.

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>지원되는 데이터 저장소

모든 유형의 연결 된 서비스를 매개 변수화 할 수 있습니다.
UI에서 연결 된 서비스를 제작할 때 Data Factory는 다음과 같은 커넥터 형식의 기본 제공 매개 변수화 환경을 제공 합니다. 연결 된 서비스 만들기/편집 블레이드에서 새 매개 변수에 대 한 옵션을 찾고 동적 콘텐츠를 추가할 수 있습니다.

- Amazon Redshift
- Azure Cosmos DB (SQL API)
- Azure Database for MySQL
- Azure SQL Database
- Azure Synapse Analytics(이전의 SQL DW)
- MySQL
- Oracle
- SQL Server
- 일반 HTTP
- 일반 REST

다른 형식의 경우 UI에서 JSON을 편집 하 여 연결 된 서비스를 매개 변수화 할 수 있습니다.

- 연결 된 서비스 만들기/편집 블레이드에서 "고급"을 확장 하 고, "JSON 형식의 동적 콘텐츠 지정" 확인란을 선택 하 고, 연결 된 서비스 JSON 페이로드를 지정 > > > 합니다. 
- 또는 매개 변수화를 사용 하지 않고 연결 된 서비스를 만든 후 [관리 허브](author-visually.md#management-hub) -> 연결 된 서비스-> 특정 연결 된 서비스를 찾을 수 있습니다. > "코드" (단추 " {} ")를 클릭 하 여 JSON을 편집 합니다. 

를 사용 하 여 매개 변수를 정의 하 고 매개 변수를 참조 하려면 [JSON 샘플](#json) 을 추가 하려면 섹션을 참조 ` parameters` ` @{linkedService().paraName} ` 하세요.

## <a name="data-factory-ui"></a>Data Factory UI

![연결된 서비스 정의에 동적 콘텐츠 추가](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![새 매개 변수 만들기](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
