---
title: Azure Cosmos DB에서 데이터베이스 계정을 관리하는 방법 알아보기
description: Azure Cosmos DB에서 데이터베이스 계정을 관리하는 방법 알아보기
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 97fb5c2558d55b3f80f2e771971faa109a930c5f
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626451"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱싱 관리

Azure Cosmos DB에서 컨테이너가 모든 항목을 자동으로 인덱싱하게 할 것인지 선택할 수 있습니다. 기본적으로 Azure Cosmos 컨테이너의 모든 항목은 자동으로 인덱싱되지만, 자동 인덱싱을 해제할 수 있습니다. 인덱싱을 해제하면 항목의 자체 링크를 통해 또는 문서 id 같은 항목 ID를 사용한 쿼리를 통해 항목에 액세스할 수 있습니다. .NET SDK를 사용하여 REST API 또는 **EnableScanInQuery** 요청 옵션에 **x-ms-documentdb-enable-scan** 헤더를 전달하여 인덱스를 사용하지 않고 결과를 제공해 달라고 명시적으로 요청할 수 있습니다.

자동 인덱싱이 해제된 상태에서도 특정 항목을 선택적으로 인덱스에 추가할 수 있습니다. 반대로, 자동 인덱싱을 설정된 상태로 두고 특정 항목을 선택적으로 제외할 수도 있습니다. 인덱싱 설정/해제 구성은 항목의 하위 집합을 쿼리해야 하는 경우에 유용합니다.  

쓰기 처리량 및 요청 단위는 인덱싱 정책에 포함된 집합을 통해 지정되는 값의 수에 비례합니다. 쿼리 패턴을 잘 알고 있다면 경로의 포함/제외 하위 집합을 명시적으로 선택하여 쓰기 처리량을 높일 수 있습니다.

## <a name="manage-indexing-using-azure-portal"></a>Azure Portal을 사용하여 인덱싱 관리

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 새 Azure Cosmos 계정을 만들거나 기존 계정을 선택합니다.

3. **데이터 탐색기** 창을 엽니다.

4. 기존 컨테이너를 선택하여 확장하고, 다음 값을 수정합니다.

   * **배율 및 설정** 창을 엽니다.
   * **indexingMode**를 *일관성*에서 *없음*으로 변경하거나 인덱싱에서 특정 경로를 포함/제외합니다.
   * **확인** 을 클릭하여 변경 내용을 저장합니다.

   ![Azure Portal을 사용하여 인덱싱 관리](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Azure SDK를 사용하여 인덱싱 관리

### <a id="dotnet"></a>.NET SDK

다음 샘플은 [SQL API .NET SDK](sql-api-sdk-dotnet.md) 및 [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective) 속성을 사용하여 항목을 명시적으로 포함하는 방법을 보여줍니다.

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

* [인덱싱 개요](index-overview.md)
* [인덱싱 정책](index-policy.md)
* [인덱스 형식](index-types.md)
* [인덱스 경로](index-paths.md)
