---
title: Azure Functions 및 API Management를 사용하여 Visual Studio에서 서버리스 API 만들기
description: Visual Studio를 사용하여 OpenAPI 정의와 함께 HTTP 트리거 함수를 만들면 다른 앱과 서비스가 서버리스 함수 기반 API를 호출할 수 있도록 Azure API Management 통합이 가능합니다.
ms.topic: tutorial
ms.date: 05/07/2021
ms.openlocfilehash: b8c6afc64f15df3124ab4052568bf36d209d4fde
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384193"
---
# <a name="create-serverless-apis-in-visual-studio-using-azure-functions-and-api-management-integration-preview"></a>Azure Functions 및 API Management 통합(미리 보기)을 사용하여 Visual Studio에서 서버리스 API 만들기

REST API는 종종 OpenAPI 정의를 사용하여 설명됩니다. 이 파일에는 API의 작업과 API에 대한 요청 및 응답 데이터를 구성하는 방법에 대한 정보가 포함되어 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Visual Studio에서 서버리스 함수 프로젝트 만들기
> * 기본 제공 OpenAPI 기능을 사용하여 함수 API를 로컬로 테스트
> * API Management 통합을 사용하여 Azure에서 함수 앱에 프로젝트 게시 
> * 함수에 대한 액세스 키를 가져와 API Management에 설정
> * OpenAPI 정의 파일 다운로드

사용자가 만든 서버리스 함수는 윈드 터빈의 응급 복구가 비용 효율적인지 여부를 확인할 수 있는 API를 제공합니다. 만든 함수 앱과 API Management 인스턴스는 모두 사용 계획을 사용하기 때문에 이 자습서를 완료하는 데 드는 비용이 최소화됩니다.

> [!NOTE]
> 이 문서에서 설명하는 OpenAPI 및 API Management 통합은 현재 미리 보기로 제공됩니다. 서버리스 API를 노출하는 이 메서드는 C# 클래스 라이브러리(.NET Core 3.1) 함수에 대해서만 지원됩니다. 다른 모든 언어 런타임은 대신 [포털에서 Azure API Management 통합을 사용](functions-openapi-definition.md)해야 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

+ [Visual Studio 2019](https://azure.microsoft.com/downloads/) 버전 16.10 이상 버전. 설치하는 동안 **Azure 개발** 워크로드를 선택했는지 확인합니다. 

+ 활성 [Azure 구독](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/dotnet/)을 만드세요.

## <a name="create-a-functions-project"></a>Functions 프로젝트 만들기

Visual Studio의 Azure Functions 프로젝트 템플릿은 Azure에서 함수 앱에 게시할 수 있는 프로젝트를 만듭니다. 또한 OpenAPI 정의 파일(이전의 Swagger 파일) 생성을 지원하는 HTTP 트리거 함수를 만듭니다.

1. Visual Studio 메뉴에서 **파일** > **새로 만들기** > **프로젝트** 를 차례로 선택합니다.

1. **새 프로젝트 만들기** 에서 검색 상자에 *함수* 를 입력하고, **Azure Functions** 템플릿을 선택한 다음, **다음** 을 선택합니다.

1. **새 프로젝트 구성** 에서 `TurbineRepair`와 같이 프로젝트에 대한 **프로젝트 이름** 을 입력한 다음, **만들기** 를 선택합니다. 

1. **새 Azure Functions 애플리케이션 만들기** 설정에 대해서는 다음 표의 값을 사용합니다.

    | 설정      | 값  | 설명                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET 버전** | **.NET Core 3(LTS)** | 이 값은 Azure Functions의 버전 3.x 런타임을 사용하는 함수 프로젝트를 만듭니다. OpenAPI 파일 생성은 함수 런타임의 버전 3.x에 대해서만 지원됩니다. |
    | **함수 템플릿** | **OpenAPI를 사용하는 HTTP 트리거** | 이 값은 OpenAPI 정의 파일을 생성하는 기능을 사용하여 HTTP 요청에 의해 트리거되는 함수를 만듭니다.  |
    | **스토리지 계정(AzureWebJobsStorage)**  | **스토리지 에뮬레이터** | HTTP 트리거 함수의 로컬 개발에 에뮬레이터를 사용할 수 있습니다. Azure의 함수 앱에는 스토리지 계정이 필요하기 때문에 Azure에 프로젝트를 게시할 때 할당되거나 생성됩니다. |
    | **권한 부여 수준** | **함수** | Azure에서 실행하는 경우 클라이언트는 엔드포인트에 액세스할 때 키를 제공해야 합니다. 키 및 권한 부여에 대한 자세한 내용은 [함수 액세스 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요. |
    
    ![Azure Functions 프로젝트 설정](./media/openapi-apim-integrate-vs/functions-project-settings.png)

1. OpenAPI를 지원하는 함수 프로젝트 및 HTTP 트리거 함수를 만들려면 **만들기** 를 선택합니다. 

Visual Studio는 HTTP 트리거 함수 형식에 대한 상용구 코드가 포함된 프로젝트와 `Function1`이라는 클래스를 만듭니다. 다음으로, 이 함수 템플릿 코드를 사용자 지정 코드로 대체합니다. 

## <a name="update-the-function-code"></a>함수 코드 업데이트

이 함수는 두 개의 매개 변수를 사용하는 HTTP 트리거를 사용합니다.

| 매개 변수 이름 | 설명|
| ---- | ---- |
| *hours* | 터빈을 복구하는 데 예상되는 시간(최대 1시간)입니다. |
| *용량* | 터빈 용량(킬로와트) |

그런 다음, 함수는 복구 비용과 터빈이 24시간 내에 가져올 수 있는 수익을 계산합니다. 매개 변수는 쿼리 문자열 또는 POST 요청의 페이로드에서 제공됩니다. 

Function1.cs 프로젝트 파일에서 생성된 클래스 라이브러리 코드의 내용을 다음 코드로 대체합니다.

:::code language="csharp" source="~/functions-openapi-turbine-repair/TurbineRepair/Function1.cs":::

이 함수 코드는 `Yes` 또는 `No`의 메시지를 반환하여 응급 복구가 비용 효율적인지 여부를 나타냅니다. 또한 터빈이 나타내는 수익 기회와 터빈 수정에 드는 비용을 반환합니다.

## <a name="run-and-verify-the-api-locally"></a>API를 로컬로 실행 및 확인

함수를 실행할 때 OpenAPI 엔드포인트를 사용하면 생성된 페이지를 사용하여 로컬에서 함수를 쉽게 사용해 볼 수 있습니다. 로컬로 실행할 때 함수 액세스 키를 제공할 필요가 없습니다.

1. F5 키를 눌러 프로젝트를 시작합니다. Functions 런타임이 로컬로 시작되면 OpenAPI 및 Swagger 엔드포인트 집합이 함수 엔드포인트와 함께 출력에 표시됩니다.  

1. 브라우저에서 RenderSwaggerUI 엔드포인트를 열면 `http://localhost:7071/api/swagger/ui`와 같이 표시됩니다. 페이지는 OpenAPI 정의에 따라 렌더링됩니다.

1. **POST** > **사용해 보기** 를 선택하고, `hours` 및 `capacity`에 대한 값을 쿼리 매개 변수로 입력하거나 JSON 요청 본문에서 입력하고, **실행** 을 선택합니다. 

    :::image type="content" source="media/openapi-apim-integrate-vs/swagger-ui-post.png" alt-text="TurbineRepair API를 테스트하기 위한 Swagger UI":::

1. `hours`에 6, `capacity`에 2500과 같은 정수 값을 입력하면 다음 예와 같은 JSON 응답을 받게 됩니다.
   
    :::image type="content" source="media/openapi-apim-integrate-vs/swagger-ui-response.png" alt-text="TurbineRepair 함수의 응답 JSON 데이터입니다.":::

이제 응급 복구 작업의 비용 효율성을 결정하는 함수가 만들어졌습니다. 그런 다음 프로젝트 및 API 정의를 Azure에 게시합니다.

## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

프로젝트를 게시하려면 먼저 Azure 구독에 함수 앱이 있어야 합니다. 프로젝트를 처음 게시할 때 Visual Studio 게시에서 함수 앱을 만듭니다. TurbineRepair API를 노출하기 위해 함수 앱과 통합되는 API Management 인스턴스를 만들 수도 있습니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택하고 **대상** 에서 **Azure** 를 선택한 다음, **다음** 을 선택합니다.

1. **특정 대상** 의 경우 Windows에서 실행되는 함수 앱을 만드는 **Azure 함수 앱(Windows)** 을 선택 후, **다음** 을 선택합니다.

1. **함수 인스턴스** 에서 **+ 새 Azure 함수 만들기...** 를 선택합니다.

    :::image type="content" source="media/openapi-apim-integrate-vs/publish-new-resource.png" alt-text="새 함수 앱 인스턴스 만들기":::

1. 다음 표에 지정된 값을 사용하여 새 인스턴스를 만듭니다.

    | 설정      | 값  | 설명                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **이름** | 전역적으로 고유한 이름 | 새 함수 앱을 고유하게 식별하는 이름입니다. 이 이름을 수락하거나 새 이름을 입력합니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다. |
    | **구독** | 사용자의 구독 | 사용할 Azure 구독입니다. 이 구독을 수락하거나 드롭다운 목록에서 새 구독을 선택합니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)** | 리소스 그룹의 이름 |  함수 앱을 만들 리소스 그룹입니다. 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다.|
    | **[계획 유형](functions-scale.md)** | Consumption | 프로젝트를 [사용량 요금제](consumption-plan.md)에서 실행되는 함수 앱에 게시하는 경우 함수 앱의 실행에 대한 비용만 지불합니다. 다른 호스팅 계획에는 비용이 더 많이 듭니다. |
    | **위치** | 서비스의 위치 | 사용자 또는 함수가 액세스하는 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)의 **위치** 를 선택합니다. |
    | **[Azure Storage](storage-considerations.md)** | 범용 스토리지 계정 | Functions 런타임에는 Azure Storage 계정이 필요합니다. **새로 만들기** 를 선택하여 범용 스토리지 계정을 구성합니다. [스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 충족하는 기존 계정을 선택할 수도 있습니다.  |

    :::image type="content" source="media/openapi-apim-integrate-vs/create-function-app-with-storage.png" alt-text="스토리지를 사용하여 Azure에서 새 함수 앱 만들기":::

1. **만들기** 를 선택하여 Azure에서 함수 앱 및 관련된 리소스를 만듭니다. 리소스 만들기 상태는 창의 왼쪽 하단에 표시됩니다. 

1. **함수 인스턴스** 로 돌아가서 **패키지 파일에서 실행** 이 선택되어 있는지 확인합니다. [Run-From-Package](run-functions-from-deployment-package.md) 모드를 사용하도록 설정된 상태에서 [Zip 배포](functions-deployment-technologies.md#zip-deploy)를 사용하여 함수 앱이 배포됩니다. 이 배포 방법은 성능이 향상되므로 함수 프로젝트에 권장됩니다. 

1. **다음** 을 선택하고 **API Management** 페이지에서 **+ API Management API 만들기** 를 선택합니다.

1.  다음 표의 값을 사용하여 **API Management에서 API** 를 만듭니다.

    | 설정      | 값  | 설명                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **API 이름** | TurbineRepair | API의 이름입니다. |
    | **구독 이름** | 사용자의 구독 | 사용할 Azure 구독입니다. 이 구독을 수락하거나 드롭다운 목록에서 새 구독을 선택합니다. |
    | **리소스 그룹** | 리소스 그룹의 이름 | 드롭다운 목록에서 함수 앱과 동일한 리소스 그룹을 선택합니다.   |
    | **API Management 서비스** | 새 인스턴스 | 서버리스 계층에 새 API Management 인스턴스를 만들려면 **새로 만들기** 를 선택합니다.   |

    :::image type="content" source="media/openapi-apim-integrate-vs/create-api-management-api.png" alt-text="API를 사용하여 API Management 인스턴스 만들기":::

1. 함수 통합에서 TurbineRepair API로 API Management 인스턴스를 만들려면 **만들기** 를 선택합니다.

1. **마침** 을 선택하고 게시 페이지에 **게시 준비** 라고 표시되는지 확인하고, **게시** 를 선택하여 프로젝트 파일이 포함된 패키지를 Azure의 새 함수 앱에 배포합니다. 

    배포가 완료되면 Azure에서 함수 앱의 루트 URL이 **게시** 탭에 표시됩니다. 

## <a name="get-the-function-access-key"></a>함수 액세스 키 가져오기

1. **게시** 탭에서 **호스팅** 옆에 있는 줄임표( **...** )를 선택하고 **Azure Portal에서 열기** 를 선택합니다. 만들어진 함수 앱이 기본 브라우저의 Azure Portal에서 열립니다. 

1. **Functions** 에서 **Functions** > **TurbineRepair** 를 선택한 다음 **함수 키** 를 선택합니다. 

    :::image type="content" source="media/openapi-apim-integrate-vs/get-function-keys.png" alt-text="TurbineRepair 함수에 대한 액세스 키 가져오기":::

1. **함수 키** 에서 **기본값** 을 선택하고 **값** 을 복사합니다. 이제 함수 엔드포인트에 액세스할 수 있도록 API Management에 이 키를 설정할 수 있습니다.

## <a name="configure-api-management"></a>API Management 구성

1. **게시** 탭에서 **호스팅** 옆에 있는 줄임표( **...** )를 선택하고 **Azure Portal에서 API 열기** 를 선택합니다. 만들어진 API Management 인스턴스가 기본 브라우저의 Azure Portal에서 열립니다. 이 API Management 인스턴스는 함수 앱에 이미 연결되어 있습니다. 

1. **API** 에서 **Azure Functions OpenAPI 확장** > **테스트** > **POST 실행** 을 선택한 후 **인바운드 정책** 에서 **정책 추가** 를 선택합니다.

    :::image type="content" source="media/openapi-apim-integrate-vs/apim-add-policy.png" alt-text="API에 인바운드 정책 추가":::

1. **인바운드 정책 추가** 에서 **쿼리 매개 변수 설정** 을 선택하고, **이름** 에 `code`를 입력하고, **+값** 을 선택하고, 복사한 함수 키를 붙여넣고, **저장** 을 선택합니다. API Management에서 호출을 함수 엔드포인트에 전달하는 경우 함수 키가 포함됩니다. 

이제 함수 키가 설정되면 API를 호출하여 Azure에서 호스트할 때 작동하는지 확인할 수 있습니다.

## <a name="verify-the-api-in-azure"></a>Azure에서 API 확인

1. API에서 **테스트** 탭, **POST 실행** 을 차례로 선택하고, **요청 본문** > **원시** 에 다음 코드를 입력하고, **보내기** 를 선택합니다.

    ```json
    {
        "hours": "6",
        "capacity": "2500"
    }
    ```

    :::image type="content" source="media/openapi-apim-integrate-vs/api-management-test-function-api.png" alt-text="API Management API의 OpenAPI 테스트 페이지":::

    이전과 마찬가지로 쿼리 매개 변수와 동일한 값을 제공할 수도 있습니다. 

1. **보내기** 를 선택한 다음 **HTTP 응답** 을 확인하여 API에서 동일한 결과가 반환되는지 확인합니다.

## <a name="download-the-openapi-definition"></a>OpenAPI 정의 다운로드

API가 예상대로 작동하는 경우 OpenAPI 정의를 다운로드할 수 있습니다.

1. 1. **API** 에서 **Azure Functions OpenAPI 확장** 을 선택하고, 줄임표( **...** )를 선택하고, **내보내기** 를 선택합니다.
   
   ![OpenAPI 정의 다운로드](media/openapi-apim-integrate-vs/download-definition.png)

2. 다양한 형식의 OpenAPI 파일을 포함하여 API 내보내기 방법을 선택합니다. [Azure API Management에서 Power Platform으로 API를 내보낼 수도 있습니다](../api-management/export-api-power-platform.md). 

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 이러한 리소스가 나중에 필요하지 않을 경우에는 리소스 그룹을 삭제하여 해당 리소스를 삭제할 수 있습니다.
 
Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 그룹** 을 선택합니다. 그런 다음 **리소스 그룹** 페이지에서 만든 그룹을 선택합니다.

**myResourceGroup** 페이지에서 나열된 리소스가 삭제하려는 리소스인지 확인합니다.

**리소스 그룹 삭제** 를 선택하고, 텍스트 상자에서 그룹 이름을 입력하여 확인한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

Visual Studio 2019를 사용하여 [OpenAPI 확장](https://github.com/Azure/azure-functions-openapi-extension)으로 인해 자체 문서화되고 API Management와 통합되는 함수를 만들었습니다. 이제 포털의 API Management에서 정의를 구체화할 수 있습니다. 또한 [API Management에 대해 자세히 알아볼 수 있습니다](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [API Management에서 OpenAPI 정의 편집](../api-management/edit-api.md)
