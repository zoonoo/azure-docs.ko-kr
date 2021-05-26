---
title: Azure API Management를 사용하여 함수에서 API 노출
description: 다른 앱 및 서비스를 사용하도록 설정하여 Azure에서 함수를 호출할 수 있도록 하는 OpenAPI 정의를 만듭니다.
ms.topic: how-to
ms.date: 05/04/2021
ms.openlocfilehash: ec25b03b3d93fcd69297899653279fea86eabb02
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110375715"
---
# <a name="expose-serverless-apis-from-http-endpoints-using-azure-api-management"></a>Azure API Management를 사용하여 HTTP 엔드포인트에서 서버리스 API 노출

Azure Functions는 포털의 Azure API Management와 통합되어 HTTP 트리거 함수 엔드포인트를 REST API로 노출할 수 있습니다. 이러한 API는 OpenAPI 정의를 사용하여 설명됩니다. 이 JSON(또는 YAML) 파일에는 API에서 사용할 수 있는 작업에 대한 정보가 포함되어 있습니다. 여기에는 API에 대한 요청 및 응답 데이터를 구성하는 방법에 대한 세부 정보가 포함되어 있습니다. 함수 앱을 통합하면 API Management에서 이러한 OpenAPI 정의를 생성할 수 있습니다.  

이 문서에서는 함수 앱을 API Management와 통합하는 방법을 보여줍니다. 이 통합은 [지원되는 언어](supported-languages.md)로 개발된 함수 앱에서 작동합니다. [Azure API Management에서 함수 앱을 가져올](../api-management/import-function-app-as-api.md) 수도 있습니다.

C# 클래스 라이브러리 함수의 경우 [Visual Studio를 사용](openapi-apim-integrate-visual-studio.md)하여 API Management와 통합되는 서버리스 API를 만들고 게시할 수도 있습니다.  

## <a name="create-the-api-management-instance"></a>API Management 인스턴스 만들기

함수 앱에 연결된 API Management 인스턴스를 만들려면 다음을 수행합니다.

1. 함수 앱을 선택하고, 왼쪽 메뉴에서 **API Management** 를 선택한 다음, **API Management** 에서 **새로 만들기** 를 선택합니다.

    :::image type="content" source="media/functions-openapi-definitions/select-all-settings-openapi.png" alt-text="API Management 선택":::


1. 다음 표에 지정된 것처럼 API Management 설정을 사용합니다.

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **이름** | 전역적으로 고유한 이름 | 함수는 함수 앱의 이름을 기반으로 생성됩니다. |
    | **구독** | 사용자의 구독 | 이 새 리소스가 만들어지는 구독입니다. |  
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 함수 앱과 동일한 리소스로, 사용자에 맞게 설정해야 합니다. |
    | **위치** | 서비스의 위치 | 함수 앱과 동일한 위치를 선택하는 것이 좋습니다. |
    | **조직 이름** | Contoso | 개발자 포털 및 이메일 알림에 사용되는 조직의 이름입니다. |
    | **관리자 전자 메일** | 사용자 이메일 | API Management로부터 시스템 알림을 수신하는 이메일입니다. |
    | **가격 책정 계층** | Consumption | 소비 계층은 모든 지역에 제공되는 것은 아닙니다. 전체 가격 책정 세부 정보는 [API Management 가격 책정 페이지](https://azure.microsoft.com/pricing/details/api-management/)를 참조하세요. |

    ![새 API Management 서비스 만들기](media/functions-openapi-definitions/new-apim-service-openapi.png)

1. **만들기** 를 선택하여 API Management 인스턴스를 만듭니다. 몇 분 정도 소요될 수 있습니다.

1. Azure에서 인스턴스를 만든 후 페이지에서 **Application Insights 사용** 옵션을 사용하도록 설정합니다. 이를 선택하여 함수 애플리케이션과 동일한 위치로 로그를 보냅니다.

## <a name="import-functions"></a>함수 가져오기

API Management 인스턴스를 만든 후 HTTP 트리거 함수 엔드포인트를 가져올 수 있습니다. 이 예제에서는 TurbineRepair라는 엔드포인트를 가져옵니다.   

1. API Management 페이지에서 **API 연결** 을 선택합니다.

1. **TurbineRepair** 함수가 강조 표시된 상태로 **Azure Functions 가져오기** 가 열립니다. **선택** 을 선택하여 계속합니다.

    ![Azure Functions를 API Management로 가져오기](media/functions-openapi-definitions/import-function-openapi.png)

1. **함수 앱에서 만들기** 페이지에서 기본값을 수락한 다음, **만들기** 를 선택합니다.

    :::image type="content" source="media/functions-openapi-definitions/create-function-openapi.png" alt-text="함수 앱에서 만들기":::

    Azure는 함수에 대한 API를 만듭니다.

## <a name="download-the-openapi-definition"></a>OpenAPI 정의 다운로드

함수를 가져온 후 API Management 인스턴스에서 OpenAPI 정의를 다운로드할 수 있습니다.

1. 페이지 위쪽에서 **OpenAPI 정의 다운로드** 를 선택합니다.
   
   ![OpenAPI 정의 다운로드](media/functions-openapi-definitions/download-definition.png)

2. 다운로드한 JSON 파일을 저장한 다음, 엽니다. 정의를 검토합니다.

## <a name="next-steps"></a>다음 단계

이제 포털의 API Management에서 정의를 구체화할 수 있습니다. 또한 [API Management에 대해 자세히 알아볼 수 있습니다](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [API Management에서 OpenAPI 정의 편집](../api-management/edit-api.md)
