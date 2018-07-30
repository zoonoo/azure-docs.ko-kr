---
title: Azure Portal에서 Azure Functions 앱을 API로 가져오기 | Microsoft Docs
description: 이 자습서에서는 Azure API Management를 사용하여 Azure Functions 앱을 API로 가져오는 방법을 보여줍니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239055"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>Azure Functions 앱을 API로 가져오기

이 문서에서는 Azure Functions 앱을 API로 가져오는 방법을 보여줍니다. 또한 Azure API Management API를 테스트하는 방법도 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * API로 Functions 앱 가져오기
> * Azure Portal에서 API 테스트
> * 개발자 포털에서 API 테스트

## <a name="prerequisites"></a>필수 조건

+ 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
+ 구독에 Azure Functions 앱이 있는지 확인합니다. 자세한 내용은 [Functions 앱 만들기](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)를 참조하세요.
+ Azure Functions 앱의 [OpenAPI 정의를 만듭니다](../azure-functions/functions-openapi-definition.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>백 엔드 API 가져오기 및 게시

1. **API Management**에서 **API**를 선택합니다.
2. **새 API 추가** 목록에서 **Functions App**을 선택합니다.

    ![Functions 앱](./media/import-function-app-as-api/function-app-api.png)
3. **찾아보기**를 선택하여 구독의 Functions 앱 목록을 표시합니다.
4. 앱을 선택합니다. API Management는 선택한 앱과 연결된 swagger를 찾아서 페치한 다음, 가져옵니다. 
5. API URL 접미사를 추가합니다. 접미사는 이 API Management 인스턴스에서 이 특정 API를 식별하는 이름입니다. 접미사는 이 API Management 인스턴스에서 고유해야 합니다.
6. API를 제품에 연결하여 API를 게시합니다. 이 경우 **Unlimited** 제품이 사용됩니다. API를 게시하고 개발자가 사용할 수 있게 하려면 API를 제품에 추가합니다. API를 만들 때 제품에 API를 추가하거나 나중에 추가할 수 있습니다.

    제품은 하나 이상의 API와 연결됩니다. 다양한 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. 개발자는 API에 액세스하려면 먼저 제품을 구독해야 합니다. 개발자가 구독할 경우 개발자는 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다. API Management 인스턴스를 만든 경우 사용자는 관리자입니다. 관리자는 기본적으로 모든 제품을 구독합니다.

    기본적으로 각 API Management 인스턴스는 두 개의 샘플 제품과 함께 제공됩니다.

    * **Starter**
    * **무제한**   
7. **만들기**를 선택합니다.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Azure API Management에서 Azure Functions 키 채우기

가져온 Azure Functions 앱을 키로 보호하는 경우 API Management에서는 키에 대해 *명명된 값*을 자동으로 만듭니다. API Management는 항목을 비밀로 채우지 않습니다. 각 항목에 대해 다음 단계를 완료합니다.  

1. API Management 인스턴스에서 **명명된 값** 탭을 선택합니다.
2. 항목을 선택한 다음, 사이드바에서 **값 표시**를 선택합니다.

    ![명명된 값](./media/import-function-app-as-api/apim-named-values.png)

3. **값** 상자에 표시되는 텍스트가 **\<Azure Functions 이름\>의 코드**와 유사한 경우 **Functions Apps**로 이동한 다음, **Functions**로 이동합니다.
4. **관리**를 선택한 다음, 앱의 인증 방법을 기반으로 관련 키를 복사합니다.

    ![Functions 앱 - 키 복사](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. **값** 상자에 키를 붙여넣은 다음, **저장**을 선택합니다.

    ![Functions 앱 - 키 값 붙여넣기](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>Azure Portal에서 새 API Management API 테스트

Azure Portal에서 작업을 직접 호출할 수 있습니다. Azure Portal을 사용하는 것은 API의 작업을 보고 테스트하는 편리한 방법입니다.  

1. 이전 섹션에서 만든 API를 선택합니다.
2. **테스트** 탭을 선택합니다.
3. 작업을 선택합니다.

    페이지에 쿼리 매개 변수에 대한 필드와 헤더 필드가 표시됩니다. 헤더 중 하나는 이 API와 연결된 제품의 구독 키에 대한 **Ocp-Apim-Subscription-Key**입니다. API Management 인스턴스를 만든 경우 사용자는 이미 관리자이므로 키가 자동으로 채워집니다. 
4. **보내기**를 선택합니다.

    백 엔드는 **200 정상** 및 일부 데이터로 응답합니다.

## <a name="call-operation"></a>개발자 포털에서 작업 호출

개발자 포털에서 작업을 호출하여 API를 테스트할 수도 있습니다. 

1. [백 엔드 API 가져오기 및 게시](#create-api)에서 만든 API를 선택합니다.
2. **개발자 포털**을 선택합니다.

    개발자 포털 사이트가 열립니다.
3. 만든 **API**를 선택합니다.
4. 테스트할 작업을 선택합니다.
5. **사용해보기**를 선택합니다.
6. **보내기**를 선택합니다.
    
    작업 호출 후에는 개발자 포털에 **응답 상태**, **응답 헤더**, **응답 콘텐츠**가 표시됩니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)