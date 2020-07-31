---
title: Azure Cosmos DB의 페이지 매김
description: 페이징 개념 및 연속 토큰에 대해 알아보기
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 978cc67336fe7f6f89970007215da73da0737f08
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433628"
---
# <a name="pagination-in-azure-cosmos-db"></a>Azure Cosmos DB의 페이지 매김

Azure Cosmos DB 쿼리에서는 결과의 여러 페이지가 있을 수 있습니다. 이 문서에서는 Azure Cosmos DB의 쿼리 엔진에서 쿼리 결과를 여러 페이지로 분할할 것인지 여부를 결정 하는 데 사용 하는 조건을 설명 합니다. 선택적으로 연속 토큰을 사용 하 여 여러 페이지에 걸쳐 있는 쿼리 결과를 관리할 수 있습니다.

## <a name="understanding-query-executions"></a>쿼리 실행 이해

쿼리 결과가 여러 페이지로 분할 되는 경우도 있습니다. 각 페이지의 결과는 별도의 쿼리 실행에 의해 생성 됩니다. 단일 실행으로 쿼리 결과를 반환할 수 없는 경우 Azure Cosmos DB 자동으로 여러 페이지로 결과를 분할 합니다.

을 설정 하 여 쿼리에서 반환 되는 최대 항목 수를 지정할 수 있습니다 `MaxItemCount` . 는 `MaxItemCount` 요청당 지정 되며 쿼리 엔진은 해당 항목 수를 반환 합니다. `MaxItemCount` `-1` 쿼리 실행 당 결과 수에 제한을 두지 않으려는 경우를로 설정할 수 있습니다.

또한 쿼리 엔진이 쿼리 결과를 여러 페이지로 분할 해야 하는 다른 이유가 있습니다. 여기에는 다음이 포함됩니다.

- 컨테이너가 제한 되었으며 추가 쿼리 결과를 반환 하는 데 사용할 수 있는 RUs가 없습니다.
- 쿼리 실행의 응답이 너무 깁니다.
- 쿼리 실행 시간이 너무 깁니다.
- 쿼리 엔진이 추가 실행에서 결과를 반환 하는 것이 더 효율적입니다.

쿼리 실행 마다 반환 되는 항목 수는 항상 보다 작거나 같습니다 `MaxItemCount` . 그러나 다른 조건에서 쿼리가 반환할 수 있는 결과의 수를 제한할 수 있습니다. 동일한 쿼리를 여러 번 실행 하는 경우 페이지 수는 일정 하지 않을 수 있습니다. 예를 들어 쿼리가 제한 되는 경우 페이지당 사용 가능한 결과가 줄어들 수 있습니다. 즉, 쿼리에 추가 페이지가 포함 됩니다. 경우에 따라 쿼리에서 빈 결과 페이지가 반환 될 수도 있습니다.

## <a name="handling-multiple-pages-of-results"></a>여러 페이지의 결과 처리

정확한 쿼리 결과를 얻으려면 모든 페이지를 진행 해야 합니다. 추가 페이지가 없을 때까지 계속 해 서 쿼리를 실행 해야 합니다.

여러 페이지가 있는 쿼리에서 결과를 처리 하는 몇 가지 예는 다음과 같습니다.

[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)

[Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)

[Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)

[Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>연속 토큰

.NET SDK 및 Java SDK에서 필요에 따라 연속 토큰을 쿼리의 진행에 대 한 책갈피로 사용할 수 있습니다. Azure Cosmos DB 쿼리 실행은 서버 쪽에서 상태 비저장 이며 연속 토큰을 사용 하 여 언제 든 지 다시 시작할 수 있습니다. 연속 토큰은 Node.js SDK 또는 Python SDK에서 지원 되지 않습니다.

연속 토큰을 사용 하는 몇 가지 예는 다음과 같습니다.

[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)

[Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)

쿼리가 연속 토큰을 반환 하는 경우 추가 쿼리 결과가 있습니다.

Azure Cosmos DB의 REST API에서 헤더를 사용 하 여 연속 토큰을 관리할 수 있습니다 `x-ms-continuation` . .NET 또는 Java SDK를 사용한 쿼리와 마찬가지로 `x-ms-continuation` 응답 헤더가 비어 있지 않으면 쿼리에 추가 결과가 있음을 의미 합니다.

동일한 SDK 버전을 사용 하는 동안에는 연속 토큰이 만료 되지 않습니다. 선택적으로 [연속 토큰의 크기를 제한할](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb?view=azure-dotnet#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb)수 있습니다. 데이터의 양과 컨테이너의 실제 파티션 수에 관계 없이 쿼리는 단일 연속 토큰을 반환 합니다.

[GROUP BY](sql-query-group-by.md) 또는 [DISTINCT](sql-query-keywords.md#distinct) 를 포함 하는 쿼리에는 연속 토큰을 사용할 수 없습니다. 이러한 쿼리는 상당한 양의 상태를 저장 해야 하기 때문입니다. 를 사용 하 `DISTINCT` 는 쿼리의 경우 쿼리에를 추가할 때 연속 토큰을 사용할 수 있습니다 `ORDER BY` .

`DISTINCT`연속 토큰을 사용할 수 있는 쿼리 예제는 다음과 같습니다.

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY 절](sql-query-order-by.md)