---
title: MongoDB에 대 한 Azure Cosmos DB API 계정의 Mongo 버전을 업그레이드 합니다.
description: MongoDB 계정에 대 한 기존 Azure Cosmos DB의 API 용 MongoDB 버전을 원활 하 게 업그레이드 하는 방법
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.openlocfilehash: 1818838a68c2712336a3515b2a82b5fdd518d237
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661174"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>MongoDB API Azure Cosmos DB의 API 버전을 업그레이드 합니다.
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

이 문서에서는 MongoDB api Azure Cosmos DB의 API 버전을 업그레이드 하는 방법에 대해 설명 합니다. 업그레이드 한 후 MongoDB에 대 한 Azure Cosmos DB의 API에서 최신 기능을 사용할 수 있습니다. 업그레이드 프로세스는 계정의 가용성을 중단 하지 않으며 r u/s를 사용 하거나 언제 든 지 데이터베이스의 용량을 줄입니다. 기존 데이터 나 인덱스는이 프로세스의 영향을 받지 않습니다. 

새 API 버전으로 업그레이드 하는 경우 프로덕션 작업을 업그레이드 하기 전에 개발/테스트 작업으로 시작 합니다. MongoDB 용 Azure Cosmos DB API 계정을 업그레이드 하기 전에 업그레이드 하는 API 버전과 호환 되는 버전으로 클라이언트를 업그레이드 하는 것이 중요 합니다.

>[!Note]
> 지금은 서버 버전 3.2을 사용 하는 한정 계정만 버전 3.6 또는 4.0로 업그레이드할 수 있습니다. 계정에 업그레이드 옵션이 표시 되지 않으면 [지원 티켓](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 제출 하십시오.

## <a name="upgrading-to-40-or-36"></a>4\.0 또는 3.6으로 업그레이드

### <a name="benefits-of-upgrading-to-version-40"></a>버전 4.0으로 업그레이드 시 이점

다음은 버전 4.0에 포함 된 새로운 기능입니다.
- Unsharded 컬렉션 내의 다중 문서 트랜잭션을 지원 합니다.
- 새 집계 연산자
- 향상된 검사 성능
- 더 빠르고 효율적인 스토리지

### <a name="benefits-of-upgrading-to-version-36"></a>버전 3.6으로 업그레이드 시 이점

다음은 버전 3.6에 포함 된 새로운 기능입니다.
- 성능 및 안정성 향상
- 새 데이터베이스 명령에 대한 지원
- 집계 파이프라인에 대한 기본 지원 및 새 집계 단계 지원
- 변경 스트림 지원
- 복합 인덱스 지원
- update, delete, count 및 sort 작업에 대한 파티션 간 지원
- $count, $skip, $limit 및 $group 집계 작업의 성능이 향상되었습니다.
- 이제 와일드카드 인덱싱이 지원됩니다.

### <a name="changes-from-version-32"></a>버전 3.2 이후의 변경 내용

- 기본적으로 클라이언트 애플리케이션의 요청에서 16500 오류를 반환하지 않도록 [SSR(서버 쪽 다시 시도)](prevent-rate-limiting-errors.md) 기능을 사용하도록 설정되어 있습니다. 대신 요청이 완료되거나 60초 시간 초과될 때까지 다시 시작합니다.
- 요청당 시간 제한은 60초로 설정됩니다.
- 새 유선 프로토콜 버전에 만들어진 MongoDB 컬렉션에는 기본적으로 인덱싱된 `_id` 속성만 있습니다.

### <a name="action-required-when-upgrading-from-32"></a>3\.2에서 업그레이드하는 경우 필요한 작업

3\.2에서 업그레이드하는 경우 데이터베이스 계정 엔드포인트 접미사가 다음 형식으로 업데이트됩니다.

```
<your_database_account_name>.mongo.cosmos.azure.com
```

버전 3.2에서 업그레이드 하는 경우 응용 프로그램의 기존 끝점과이 데이터베이스 계정에 연결 하는 드라이버를 교체 해야 합니다. **새 엔드포인트를 사용하는 연결에만 새 API 버전의 기능에 대한 액세스 권한이 있습니다**. 이전의 3.2 엔드포인트에는 `.documents.azure.com` 접미사가 있어야 합니다.

>[!Note]
> 계정이 소 버린 정부 또는 제한 된 Azure 클라우드에서 만들어진 경우이 끝점은 약간의 차이가 있을 수 있습니다.

## <a name="how-to-upgrade"></a>업그레이드하는 방법

1. Azure Portal로 이동 하 여 Azure Cosmos DB API for MongoDB 계정 개요 블레이드로 이동 합니다. 현재 서버 버전이 필요한 지 확인 합니다.

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="MongoDB 계정을 사용 하 여 Azure Portal 개요" border="false":::

2. 왼쪽의 옵션에서 블레이드를 선택 합니다 `Features` . 그러면 데이터베이스 계정에 사용할 수 있는 계정 수준의 기능이 표시됩니다.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="MongoDB 계정 개요와 함께 Azure Portal 기능 블레이드가 강조 표시 됨" border="false":::

3. 행을 클릭 `Upgrade Mongo server version` 합니다. 이 옵션이 표시되지 않으면 계정이 업그레이드에 적합하지 않은 것일 수 있습니다. 해당 하는 경우 [지원 티켓을 파일에](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 입력 하세요.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="옵션이 있는 기능 블레이드." border="false":::

4. 업그레이드에 대해 표시된 정보를 검토합니다. 프로세스를 `Enable` 시작할 준비가 되 면 즉시를 클릭 합니다.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="업그레이드 지침이 확장 되었습니다." border="false":::

5. 프로세스를 시작 하면 메뉴에 `Features` 업그레이드 상태가 표시 됩니다. 상태는 `Pending`, `In Progress`, `Upgraded` 순서로 바뀝니다. 이 프로세스는 데이터베이스 계정의 기존 기능이나 작업에는 영향을 주지 않습니다.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="시작 후 업그레이드 상태입니다." border="false":::

6. 업그레이드가 완료 되 면 상태는로 표시 됩니다 `Upgraded` . 이를 클릭 하 여 프로세스를 완료 하기 위해 수행 해야 하는 다음 단계 및 작업에 대해 자세히 알아보세요. 요청을 처리 하는 동안 문제가 발생 한 경우 [지원 담당자에 게 문의](https://azure.microsoft.com/en-us/support/create-ticket/) 하세요.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="업그레이드 된 계정 상태." border="false":::

7. 
    1. 3.2에서 업그레이드 한 경우 블레이드로 돌아가서 `Overview` 응용 프로그램에서 사용할 새 연결 문자열을 복사 합니다. 3\.2를 실행하는 이전 연결 문자열은 중단되지 않습니다. 일관된 환경을 보장하기 위해 모든 애플리케이션은 새 엔드포인트를 사용해야 합니다.
    2. 3\.6에서 업그레이드한 경우 기존 연결 문자열은 지정된 버전으로 업그레이드되며 계속 사용되어야 합니다.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="새 개요 블레이드." border="false":::


## <a name="how-to-downgrade"></a>다운그레이드 방법
' 업그레이드 방법 ' 섹션의 동일한 단계를 통해 계정을 4.0에서 3.6로 다운 그레이드할 수도 있습니다. 

3.2에서 (4.0 또는 3.6)로 업그레이드 하 고 3.2로 다시 다운 그레이드 하는 경우 이전 () 버전의 업그레이드를 실행 하는 호스트에서 이전 (3.2) 연결 문자열을 사용 하도록 다시 전환할 수 있습니다 `accountname.documents.azure.com` .


## <a name="next-steps"></a>다음 단계

- [MongoDB 버전 4.0](mongodb-feature-support-40.md)의 지원되는 기능 및 지원되지 않는 기능에 대해 알아봅니다.
- [MongoDB 버전 3.6](mongodb-feature-support-36.md)의 지원되는 기능 및 지원되지 않는 기능에 대해 알아봅니다.
- 자세한 내용은 [Mongo 3.6 버전 기능](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)을 확인하세요.
