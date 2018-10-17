---
title: Azure Cosmos DB에 의해 트리거되는 함수 만들기 | Microsoft Docs
description: Azure Functions를 사용하여 Azure Cosmos DB의 데이터베이스에 데이터가 추가될 때 호출되는, 서버를 사용하지 않는 함수를 만듭니다.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: azure-functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 2c19a464a4437c3490b03baef6cc95a783a55c69
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48858585"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Azure Cosmos DB에 의해 트리거되는 함수 만들기

데이터가 Azure Cosmos DB에 추가될 때 또는 변경될 때 트리거되는 함수를 만드는 방법에 대해 알아봅니다. Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB: Azure Functions를 통한, 서버를 사용하지 않는 데이터베이스 컴퓨팅](..\cosmos-db\serverless-computing-database.md)을 참조하세요.

![로그에서 메시지 보기.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

+ Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

트리거를 만들기 전에 SQL API를 사용하는 Azure Cosmos DB 계정이 있어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Azure Function 앱 만들기

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

다음으로 새 함수 앱에서 함수를 만듭니다.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Azure Cosmos DB 트리거 만들기

1. 함수 앱을 확장한 후 **함수** 옆의 **+** 단추를 클릭합니다. 함수 앱의 첫 번째 함수인 경우 **포털 내**를 선택한 다음, **계속**을 선택합니다. 그렇지 않으면 3단계로 이동합니다.

   ![Azure Portal에서 함수 빨리 시작하기 페이지](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. **추가 템플릿**, **템플릿 마침 및 보기**를 차례로 선택합니다.

    ![Functions 빠른 시작 - 추가 템플릿 선택](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. 검색 필드에서 `cosmos`를 입력하고 **Azure Cosmos DB 트리거** 템플릿을 선택합니다.

1. 메시지가 표시되면 **설치**를 선택하여 함수 앱에 Azure Storage 확장과 모든 종속성을 설치합니다. 설치가 완료되면 **계속**을 선택합니다.

    ![바인딩 확장 설치](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. 이미지 아래의 테이블에 지정된 설정을 사용하여 새 트리거를 구성합니다.

    ![Azure Cosmos DB에 의해 트리거되는 함수 만들기](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | 설정      | 제안 값  | 설명                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Name** | 기본값 | 템플릿에서 추천하는 기본 함수 이름을 사용합니다.|
    | **Azure Cosmos DB 계정 연결** | 새 설정 | **새로 만들기**를 선택한 다음, **구독**, 이전에 만든 **데이터베이스 계정**, **선택**을 차례로 선택합니다. 그러면 계정 연결에 대한 응용 프로그램 설정이 만들어집니다. 이 설정은 데이터베이스에 연결하는 바인딩에서 사용됩니다. |
    | **컬렉션 이름** | 항목 | 모니터링할 컬렉션의 이름입니다. |
    | **임대 컬렉션이 없는 경우 새로 만들기** | 선택 | 아직 컬렉션이 없으므로 지금 만듭니다. |
    | **데이터베이스 이름** | 작업 | 모니터링할 컬렉션이 포함된 데이터베이스의 이름입니다. |

1. **만들기**를 클릭하여 Azure Cosmos DB에 의해 트리거되는 함수를 만듭니다. 함수를 만들면 템플릿 기반 함수 코드가 표시됩니다.  

    ![C#의 Cosmos DB 함수 템플릿](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    이 함수 템플릿은 문서 수와 첫 번째 문서 ID를 로그에 기록합니다.

다음으로, Azure Cosmos DB 계정에 연결하고 `Tasks` 데이터베이스에 `Items` 컬렉션을 만듭니다.

## <a name="create-the-items-collection"></a>항목 컬렉션 만들기

1. [Azure Portal](https://portal.azure.com)의 두 번째 인스턴스를 브라우저의 새 탭에서 엽니다.

1. 포털 왼쪽에서 아이콘 표시줄을 확장하고, 검색 필드에 `cosmos`를 입력하고, **Azure Cosmos DB**를 선택합니다.

    ![Azure Cosmos DB 서비스 검색](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Azure Cosmos DB 계정을 선택한 다음 **데이터 탐색기**를 선택합니다. 

1. **컬렉션**에서 **taskDatabase**를 선택하고 **새 컬렉션**을 선택합니다.

    ![컬렉션 만들기](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

1. **컬렉션 추가**에서 이미지 아래의 표에 표시된 설정을 사용합니다. 

    ![taskCollection 정의](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)

    | 설정|제안 값|설명 |
    | ---|---|--- |
    | **데이터베이스 ID** | 작업 |새 데이터베이스에 대한 이름입니다. 함수 바인딩에 정의된 이름과 일치해야 합니다. |
    | **컬렉션 ID** | 항목 | 새 컬렉션의 이름입니다. 함수 바인딩에 정의된 이름과 일치해야 합니다.  |
    | **저장소 용량** | 고정(10GB)|기본값을 사용합니다. 이 값은 데이터베이스의 저장소 용량입니다. |
    | **처리량** |400RU| 기본값을 사용합니다. 대기 시간을 줄이면 나중에 처리량을 늘릴 수 있습니다. |
    | **[파티션 키](../cosmos-db/partition-data.md#best-practices-when-choosing-a-partition-key)** | /category|각 파티션에 데이터를 균등하게 배포하는 파티션 키입니다. 올바른 파티션 키를 선택하는 것은 성능이 뛰어난 컬렉션을 만드는 데 중요합니다. | 

1. **확인**을 클릭하여 Items 컬렉션을 만듭니다. 컬렉션이 생성될 때까지 잠시 시간이 걸릴 수 있습니다.

함수 바인딩에 지정된 컬렉션이 있으면 이 새 컬렉션에 문서를 추가하여 함수를 테스트할 수 있습니다.

## <a name="test-the-function"></a>함수 테스트

1. 데이터 탐색기에서 새 **taskCollection** 컬렉션을 확장하고, **문서**를 선택한 다음 **새 문서**를 선택합니다.

    ![taskCollection에 문서 만들기](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

1. 새 문서의 콘텐츠를 다음 콘텐츠로 바꾼 후 **저장**을 선택합니다.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. 포털에서 함수를 포함하고 있는 첫 번째 브라우저 탭으로 전환합니다. 함수 로그를 확장하고 새 문서가 함수를 트리거했는지 확인합니다. `task1` 문서 ID 값이 로그에 기록되었는지 살펴봅니다. 

    ![로그에서 메시지 보기.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (선택 사항) 다시 문서로 돌아가서 변경 작업을 수행하고 **업데이트**를 클릭합니다. 그런 다음 함수 로그로 돌아가서 업데이트도 함수를 트리거했는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

문서가 Azure Cosmos DB에 추가되거나 수정될 때 실행되는 함수를 만들었습니다.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Azure Cosmos DB 트리거에 대한 자세한 내용은 [Azure Functions의 Azure Cosmos DB 바인딩](functions-bindings-cosmosdb.md)을 참조하세요.
