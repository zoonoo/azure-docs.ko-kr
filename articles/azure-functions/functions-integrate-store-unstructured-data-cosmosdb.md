---
title: "Azure Functions 및 Cosmos DB를 사용하여 구조화되지 않은 데이터 저장"
description: "Azure Functions 및 Cosmos DB를 사용하여 구조화되지 않은 데이터 저장"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, Functions, 이벤트 처리, Cosmos DB, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: ms-hero
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/08/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 46022530c69b292878a4500c0e325bda878e6188
ms.contentlocale: ko-kr
ms.lasthandoff: 05/12/2017

---
# <a name="store-unstructured-data-using-azure-functions-and-cosmos-db"></a>Azure Functions 및 Cosmos DB를 사용하여 구조화되지 않은 데이터 저장

Azure Cosmos DB는 구조화되지 않은 데이터 및 JSON 데이터를 저장하기 좋습니다. Cosmos DB를 Azure Functions와 함께 사용하면 관계형 데이터베이스에 데이터를 저장하는 데 필요한 것보다 훨씬 적은 코드를 사용하여 쉽고 빠르게 데이터를 저장할 수 있습니다.

이 자습서에서는 Azure Portal을 사용하여 Cosmos DB 문서에 구조화되지 않은 데이터를 저장하는 Azure 함수를 만드는 방법을 안내합니다. 

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-a-function"></a>함수 만들기

`MyTaskList`라는 새로운 C# 일반 웹후크를 만듭니다.

1. 기존 함수 목록을 확장하고 + 기호를 클릭하여 새 함수를 만듭니다.
1. GenericWebHook-CSharp를 선택하고 `MyTaskList`라는 이름을 지정합니다.

![새로운 C# 일반 웹후크 함수 앱 추가](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-new-functionapp.png)

## <a name="add-an-output-binding"></a>출력 바인딩 추가

Azure 함수는 트리거 하나와 원하는 수의 입력 또는 출력 바인딩을 가질 수 있습니다. 이 예에서는 HTTP 요청 트리거 및 Cosmos DB 문서를 출력 바인딩으로 사용합니다.

1. 함수의 트리거 및 바인딩을 보거나 수정하려면 함수의 *통합* 탭을 클릭합니다.
1. 페이지의 오른쪽 맨 위에 있는 *새 출력* 링크를 선택합니다.

참고: HTTP 요청 트리거는 이미 구성되어 있지만 Cosmos DB 문서 바인딩을 추가해야 합니다.

![새로운 Cosmos DB 출력 바인딩 추가](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

1. 필요한 정보를 입력하여 바인딩을 만듭니다. 아래 테이블을 사용하여 값을 확인합니다.

![Cosmos DB 출력 바인딩 구성](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

|  필드 | 값  |
|---|---|
| 문서 매개 변수 이름 | 코드에서 Cosmos DB 개체를 참조하는 이름 |
| 데이터베이스 이름 | 문서를 저장할 데이터베이스의 이름 |
| 컬렉션 이름 | Cosmos DB 데이터베이스의 그룹화 이름 |
| Cosmos DB와 컬렉션을 자동으로 만드시겠습니까? | 예 또는 아니요 |
| Cosmos DB 계정 연결 | Cosmos DB 데이터베이스를 가리키는 연결 문자열 |

또한 Cosmos DB 데이터베이스에 대한 연결도 구성해야 합니다.

1. "Cosmos DB 문서 연결" 레이블 옆에 있는 "새로 만들기" 링크를 클릭합니다.
1. 필드를 채우고 Cosmos DB 문서를 만드는 데 필요한 적절한 옵션을 선택합니다.

![Cosmos DB 연결 구성](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

|  필드 | 값  |
|---|---|
| Id | Cosmos DB 데이터베이스의 고유한 ID  |
| NoSQL API | Cosmos DB 또는 MongoDB  |
| 구독 | MSDN 구독  |
| 리소스 그룹  | 새 그룹을 만들거나 기존 항목을 선택합니다.  |
| 위치  | WestEurope  |

1. *확인* 단추를 클릭합니다. Azure가 리소스를 만드는 동안 잠시 기다려야 할 수 있습니다.
1. *저장* 단추를 클릭합니다.

## <a name="update-the-function-code"></a>함수 코드 업데이트

함수의 템플릿 코드를 다음으로 바꿉니다.

이 샘플의 코드는 C#로만 제공된다는 사실을 참고하세요.

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```

이 코드 샘플은 HTTP 요청 쿼리 문자열을 읽고 `taskDocument` 개체의 구성원으로 할당합니다. `taskDocument` 개체는 Cosmos DB 데이터베이스에 데이터를 자동으로 저장하고, 처음 사용할 때 데이터베이스도 만듭니다.

## <a name="test-the-function-and-database"></a>함수 및 데이터베이스 테스트

1. 함수 탭에서 포털의 오른쪽에 있는 *테스트* 링크를 클릭하고 다음 HTTP 쿼리 문자열을 입력합니다.

| 쿼리 문자열 | 값 |
|---|---|
| name | Chris P. Bacon |
| task | BLT 샌드위치 만들기 |
| 기한 | 05/12/2017 |

1. *실행* 링크를 클릭합니다.
1. 함수가 *HTTP 200 OK* 응답 코드를 반환했는지 확인합니다.

![Cosmos DB 출력 바인딩 구성](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

Cosmos DB 데이터베이스에 항목이 만들어졌는지 확인합니다.

1. Azure Portal에서 데이터베이스를 찾아 선택합니다.
1. *데이터 탐색기* 옵션을 선택합니다.
1. 문서 항목에 도달할 때까지 노드를 확장합니다.
1. 데이터베이스 항목을 확인합니다. 데이터베이스에는 데이터 외에 추가적인 메타 데이터도 있습니다.

![Cosmos DB 항목 확인](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

문서에 데이터가 있을 경우 Cosmos DB 데이터베이스에 구조화되지 않은 데이터를 저장하는 Azure 함수가 생성된 것입니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

Azure Functions에 대한 자세한 내용은 다음 항목을 참조하세요.

[!INCLUDE [Getting help note](../../includes/functions-get-help.md)]

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]
