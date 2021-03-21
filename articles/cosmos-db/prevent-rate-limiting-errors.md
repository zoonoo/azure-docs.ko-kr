---
title: MongoDB 작업에 대 한 Azure Cosmos DB API에 대 한 전송률 제한 오류를 방지 합니다.
description: SSR (서버 쪽 다시 시도) 기능을 사용 하 여 MongoDB 작업에 대 한 Azure Cosmos DB API가 rate 제한 오류 발생을 방지 하는 방법에 대해 알아봅니다.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99507933"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>MongoDB 작업에 대 한 Azure Cosmos DB API에 대 한 전송률 제한 오류 방지
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

MongoDB 작업에 대 한 Azure Cosmos DB API는 컬렉션의 처리량 제한 (RUs)을 초과 하는 경우 속도 제한 (16500/429) 오류가 발생 하 여 실패할 수 있습니다. 

서버 쪽 다시 시도 (SSR) 기능을 사용 하도록 설정 하 고 서버에서 이러한 작업을 자동으로 다시 시도 하도록 할 수 있습니다. 계정의 모든 컬렉션에 대해 짧은 지연 후 요청이 다시 시도 됩니다. 이 기능은 클라이언트 응용 프로그램에서 요율 제한 오류를 처리 하는 편리한 대안입니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. MongoDB API에 대 한 Azure Cosmos DB API로 이동 합니다.

1. **설정** 섹션 아래에 있는 **기능** 창으로 이동 합니다.

1. **서버 쪽 다시 시도** 를 선택 합니다.

1. 계정에서 모든 컬렉션에 대해이 기능을 사용 하도록 설정 하려면 **사용** 을 클릭 합니다.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="MongoDB 용 Azure Cosmos DB API에 대 한 서버 쪽 다시 시도 기능의 스크린샷":::

## <a name="use-the-azure-cli"></a>Azure CLI 사용

1. 계정에 대해 SSR가 이미 사용 하도록 설정 되었는지 확인 합니다.
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **사용** 데이터베이스 계정에 있는 모든 컬렉션에 대 한 SSR. 이 변경 내용이 적용 되려면 15 분 정도 걸릴 수 있습니다.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
다음 명령은 기능 목록에서 "DisableRateLimitingResponses"를 제거 하 여 데이터베이스 계정의 모든 컬렉션에 대해 SSR를 **사용 하지 않도록 설정** 합니다. 이 변경 내용이 적용 되려면 15 분 정도 걸릴 수 있습니다.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>질문과 대답
* 요청을 다시 시도 하는 방법
    * 60 초의 시간 제한에 도달할 때까지 요청은 계속 해 서 다시 시도 됩니다. 시간 제한에 도달 하면 클라이언트에서 [ExceededTimeLimit 예외 (50)](mongodb-troubleshoot.md)를 받게 됩니다.
*  SSR의 효과를 모니터링 하려면 어떻게 해야 하나요?
    *  Cosmos DB 메트릭 창에서 서버 쪽에서 다시 시도 된 시간 제한 오류 (429s)를 볼 수 있습니다. 이러한 오류는 처리 되 고 서버 쪽에서 다시 시도 되므로 SSR를 사용 하는 경우 클라이언트로 이동 하지 않습니다. 
    *  [Cosmos DB 리소스 로그](cosmosdb-monitor-resource-logs.md)에 "estimatedDelayFromRateLimitingInMilliseconds"가 포함 된 로그 항목을 검색할 수 있습니다.
*  내 일관성 수준에 영향을 SSR?
    *  SSR는 요청의 일관성에 영향을 주지 않습니다. 요청 수가 rate (429 오류 포함) 이면 서버 쪽에서 다시 시도 됩니다. 
*  클라이언트에서 받을 수 있는 모든 오류 유형에 영향을 SSR?
    *  아니요, SSR는 서버 쪽을 다시 시도 하 여 전송률 제한 오류 (429s)에만 영향을 줍니다. 이 기능을 통해 클라이언트 응용 프로그램에서 요율 제한 오류를 처리할 필요가 없습니다. [다른 모든 오류](mongodb-troubleshoot.md) 는 클라이언트로 이동 합니다. 

## <a name="next-steps"></a>다음 단계

일반적인 오류 문제 해결에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [MongoDB에 대 한 Azure Cosmos DB API의 일반적인 문제 해결](mongodb-troubleshoot.md)
