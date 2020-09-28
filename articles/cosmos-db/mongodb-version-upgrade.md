---
title: MongoDB에 대 한 Azure Cosmos DB API 계정의 Mongo 버전을 업그레이드 합니다.
description: MongoDB 계정에 대 한 기존 Azure Cosmos DB의 API 용 MongoDB 버전을 원활 하 게 업그레이드 하는 방법
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: jasonh
ms.openlocfilehash: c6369be39d0a964f07c64083e3269bb1c0c49c7f
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409666"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>MongoDB 용 Azure Cosmos DB API 계정에 대 한 MongoDB 유선 프로토콜 버전을 업그레이드 합니다.

이 문서에서는 Azure Cosmos DB의 MongoDB API 계정에 대 한 유선 프로토콜 버전을 업그레이드 하는 방법을 설명 합니다. 유선 프로토콜 버전을 업그레이드 한 후에는 Azure Cosmos DB의 MongoDB API에서 최신 기능을 사용할 수 있습니다. 업그레이드 프로세스는 계정의 가용성을 중단 하지 않으며 r u/s를 사용 하거나 언제 든 지 데이터베이스의 용량을 줄입니다. 기존 데이터 나 인덱스는이 프로세스의 영향을 받지 않습니다.

>[!Note]
> 지금은 서버 버전 3.2을 사용 하는 한정 계정만 버전 3.6로 업그레이드할 수 있습니다. 계정에 업그레이드 옵션이 표시 되지 않으면 [지원 티켓](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 제출 하십시오.

## <a name="upgrading-from-version-32-to-36"></a>버전 3.2에서 3.6로 업그레이드

### <a name="benefits-of-upgrading-to-version-36"></a>버전 3.6으로 업그레이드 시 이점

다음은 버전 3.6에 포함 된 새로운 기능입니다.
- 성능 및 안정성 향상
- 새 데이터베이스 명령에 대한 지원
- 집계 파이프라인에 대한 기본 지원 및 새 집계 단계 지원
- 변경 스트림 지원
- 복합 인덱스 지원
- Update, delete, count 및 sort 작업에 대 한 파티션 간 지원
- $Count, $skip, $limit 및 $group 집계 작업의 성능이 향상 되었습니다.
- 이제 와일드 카드 인덱싱이 지원 됩니다.

### <a name="changes-from-version-32"></a>버전 3.2의 변경 내용

- **RequestRateIsLarge 오류가 제거 되었습니다**. 클라이언트 응용 프로그램의 요청에서 16500 오류가 더 이상 반환 되지 않습니다. 대신 요청을 완료 하거나 시간 초과를 수행할 때까지 요청을 다시 시작 합니다.
- 요청당 시간 제한 (60 초)으로 설정 됩니다.
- 새 유선 프로토콜 버전에 생성 된 MongoDB 컬렉션은 `_id` 기본적으로 인덱싱된 속성만 갖습니다.

### <a name="action-required"></a>필요한 동작

버전 3.6로 업그레이드 하는 경우 데이터베이스 계정 끝점 접미사가 다음 형식으로 업데이트 됩니다.

```
<your_database_account_name>.mongo.cosmos.azure.com
```

응용 프로그램의 기존 끝점과이 데이터베이스 계정에 연결 하는 드라이버를 교체 해야 합니다. **새 끝점을 사용 하는 연결만 MongoDB 버전 3.6의 기능에 액세스할 수**있습니다. 이전 끝점에는 접미사가 있어야 합니다 `.documents.azure.com` .

>[!Note]
> 계정이 소 버린 정부 또는 제한 된 Azure 클라우드에서 만들어진 경우이 끝점은 약간의 차이가 있을 수 있습니다.

### <a name="how-to-upgrade"></a>업그레이드하는 방법

1. 먼저 Azure Portal로 이동 하 여 MongoDB 용 Azure Cosmos DB API 계정 개요 블레이드로 이동 합니다. 서버 버전이 인지 확인 `3.2` 합니다. 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="MongoDB 계정을 사용 하 여 Azure Portal 개요" border="false":::

2. 왼쪽의 옵션에서 블레이드를 선택 합니다 `Features` . 그러면 데이터베이스 계정에 사용할 수 있는 계정 수준 기능이 표시 됩니다.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="MongoDB 계정 개요와 함께 Azure Portal 기능 블레이드가 강조 표시 됨" border="false":::

3. 행을 클릭 `Upgrade to Mongo server version 3.6` 합니다. 이 옵션이 표시 되지 않으면 계정이이 업그레이드에 적합 하지 않을 수 있습니다. 해당 하는 경우 [지원 티켓을 파일에](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 입력 하세요.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="옵션이 있는 기능 블레이드." border="false":::

4. 이 특정 업그레이드에 대해 표시 된 정보를 검토 합니다. 업그레이드는 응용 프로그램에서이 섹션에 강조 표시 된 대로 업데이트 된 끝점을 사용할 때 까지만 완료 됩니다. 프로세스를 `Enable` 시작할 준비가 되 면 즉시를 클릭 합니다.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="업그레이드 지침이 확장 되었습니다." border="false":::

5. 프로세스를 시작 하면 메뉴에 `Features` 업그레이드 상태가 표시 됩니다. 상태는에서로,에서로 이동 `Pending` `In Progress` `Upgraded` 합니다. 이 프로세스는 데이터베이스 계정의 기존 기능이 나 작업에는 영향을 주지 않습니다.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="시작 후 업그레이드 상태입니다." border="false":::

6. 업그레이드가 완료 되 면 상태는로 표시 됩니다 `Upgraded` . 이를 클릭 하 여 프로세스를 완료 하기 위해 수행 해야 하는 다음 단계 및 작업에 대해 자세히 알아보세요. 요청을 처리 하는 동안 문제가 발생 한 경우 [지원 담당자에 게 문의](https://azure.microsoft.com/en-us/support/create-ticket/) 하세요.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="업그레이드 된 계정 상태." border="false":::

7. **업그레이드 된 버전의 데이터베이스 계정 사용을 시작 하려면**블레이드로 돌아가서 `Overview` 응용 프로그램에서 사용할 새 연결 문자열을 복사 합니다. 응용 프로그램은 새 끝점에 연결 하는 즉시 업그레이드 된 버전을 사용 하 여 시작 됩니다. 기존 연결은 중단 되지 않으며 사용자가 편리 하 게 업데이트할 수 있습니다. 일관 된 환경을 보장 하기 위해 모든 응용 프로그램은 새 끝점을 사용 해야 합니다.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="새 개요 블레이드." border="false":::

## <a name="next-steps"></a>다음 단계

- [MongoDB 버전 3.6의](mongodb-feature-support-36.md)지원 되는 기능 및 지원 되지 않는 기능에 대해 알아봅니다.
- 자세한 내용은 [Mongo 3.6 버전 기능](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)을 확인하세요.
