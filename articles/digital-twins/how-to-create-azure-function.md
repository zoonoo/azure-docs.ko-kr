---
title: 데이터 처리를 위한 Azure 함수 설정
titleSuffix: Azure Digital Twins
description: 디지털 트윈에서 액세스하고 트리거할 수 있는 Azure 함수를 만드는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f1ed4b9beda9848bba8fb12783f49dcf8016d3dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590622"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Azure에서 데이터 처리를 위한 함수 앱 연결

데이터를 기반으로 하는 디지털 트윈 업데이트는 [Azure Functions](../azure-functions/functions-overview.md)을 사용하여 만든 함수와 같은 계산 리소스를 통해 [**이벤트 경로**](concepts-route-events.md)를 사용하여 처리됩니다. 함수는 다음에 대한 응답으로 디지털 트윈을 업데이트하는 데 사용할 수 있습니다.
* IoT Hub에서 들어오는 디바이스 원격 분석 데이터
* 트윈 그래프 내의 다른 디지털 트윈에서 들어오는 속성 변경 또는 기타 데이터

이 문서에서는 Azure에서 Azure Digital Twins와 함께 사용할 함수를 만드는 과정을 안내합니다. 

다음은 이러한 모든 단계에 대한 개요입니다.

1. Visual Studio에서 Azure Functions 프로젝트 만들기
2. [Event Grid](../event-grid/overview.md) 트리거를 사용하여 함수 작성
3. 함수에 인증 코드 추가(Azure Digital Twins에 액세스할 수 있도록)
4. Azure에 함수 앱 게시
5. 함수 앱에 대한 [보안](concepts-security.md) 액세스 설정

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>필수 구성 요소: Azure Digital Twins 인스턴스 설정

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Visual Studio에서 함수 앱 만들기

Visual Studio 2019에서 _파일 > 새로 만들기 > 프로젝트_ 를 선택하고 _Azure Functions_ 템플릿을 검색합니다. _다음_ 을 선택합니다.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="새 프로젝트 대화 상자를 보여 주는 Visual Studio의 스크린샷. Azure Functions 프로젝트 템플릿이 강조 표시됩니다.":::

함수 앱의 이름을 지정하고 _만들기_ 를 선택합니다.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="새 프로젝트를 구성하는 대화 상자를 보여 주는 Visual Studio의 스크린샷. 프로젝트 이름, 저장 위치, 새 솔루션을 만들기 위한 선택 항목, 솔루션 이름 등이 있습니다.":::

*Event Grid 트리거* 의 함수 앱 유형을 선택하고 _만들기_ 를 선택합니다.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="새 Azure Functions 애플리케이션을 만드는 대화 상자를 표시하는 Visual Studio의 스크린샷. Event Grid 트리거 옵션이 강조 표시됩니다.":::

함수 앱이 만들어지면 Visual Studio는 프로젝트 폴더의 **Function1.cs** 파일에 코드 샘플을 생성합니다. 이 간단한 함수는 이벤트를 기록하는 데 사용됩니다.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="새로 만든 프로젝트의 프로젝트 창에 대한 Visual Studio의 스크린샷. Function1이라는 샘플 함수에 대한 코드가 있습니다." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Event Grid 트리거를 사용하여 함수 작성

함수 앱에 SDK를 추가하여 함수를 작성할 수 있습니다. 함수 앱은 [.NET(C#)용 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client)를 사용하여 Azure Digital Twins와 상호 작용합니다. 

SDK를 사용하려면 다음 패키지를 프로젝트에 포함해야 합니다. Visual Studio의 NuGet 패키지 관리자를 사용하여 패키지를 설치하거나, 명령줄 도구에서 `dotnet`을 사용하여 패키지를 추가할 수 있습니다.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

다음으로 Visual Studio 솔루션 탐색기에서 샘플 코드가 있는 _Function1.cs_ 파일을 열고 다음 패키지에 대한 `using` 문을 함수에 추가합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>함수에 인증 코드 추가

이제 클래스 수준 변수를 선언하고 함수가 Azure Digital Twins에 액세스할 수 있게 하는 인증 코드를 추가합니다. _Function1.cs_ 파일의 함수에 다음을 추가합니다.

* Azure Digital Twins 서비스 URL을 **환경 변수** 로 읽는 코드. 함수에 하드 코딩하는 대신 환경 변수에서 서비스 URL을 읽는 것이 좋습니다. 이 환경 변수의 값은 [이 문서의 뒷부분](#set-up-security-access-for-the-function-app)에서 설정합니다. 환경 변수에 대한 자세한 내용은 [*함수 앱 관리*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)를 참조하세요.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* HttpClient 인스턴스를 보유하는 정적 변수. HttpClient는 생성하는 데 비교적 비용이 많이 들어 모든 함수 호출에 대해 이 작업을 수행하지 않으려고 합니다.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Azure Functions에서 관리 ID 자격 증명을 사용할 수 있습니다.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* 함수 내부에 _DigitalTwinsClient_ 지역 변수를 추가하여 Azure Digital Twins 클라이언트 인스턴스를 보관합니다. 클래스 내부에서 이 변수를 정적으로 설정하지 *마세요*.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* _adtInstanceUrl_ 에 대한 null 검사를 추가하고 함수 논리를 try/catch 블록으로 래핑하여 예외를 catch합니다.

이러한 변경 후 함수 코드는 다음과 유사합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

이제 애플리케이션을 작성했으므로 다음 섹션의 단계를 사용하여 Azure에 게시할 수 있습니다.

## <a name="publish-the-function-app-to-azure"></a>Azure에 함수 앱 게시

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-function-publish"></a>함수 게시 확인

1. [Azure Portal](https://portal.azure.com/)에서 자격 증명을 사용하여 로그인합니다.
2. 창 맨 위에 있는 검색 표시줄에서 **함수 앱 이름** 을 검색합니다.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Azure Portal에서 함수 앱의 이름을 검색합니다." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. *함수 앱* 페이지가 열리면 왼쪽에 있는 메뉴 옵션에서 *함수* 를 선택합니다. 함수가 성공적으로 게시되면 목록에 함수 이름이 표시됩니다.
게시된 함수 목록에 함수기 나열되는지 확인하려면 몇 분 정도 기다리거나 페이지를 몇 번 새로 고쳐야 할 수도 있습니다.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Azure Portal에서 게시된 함수를 봅니다." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

함수 앱이 Azure Digital Twins에 액세스할 수 있으려면 Azure Digital Twins 인스턴스에 액세스할 수 있는 권한이 있는 시스템 관리 ID가 있어야 합니다. 이는 다음에 설정하게 됩니다.

## <a name="set-up-security-access-for-the-function-app"></a>함수 앱에 대한 보안 액세스 설정

Azure CLI 또는 Azure Portal을 사용하여 함수 앱에 대한 보안 액세스를 설정할 수 있습니다. 아래의 기본 옵션에 대한 단계를 수행합니다.

# <a name="cli"></a>[CLI](#tab/cli)

[Azure Cloud Shell](https://shell.azure.com) 또는 [로컬 Azure CLI 설치](/cli/azure/install-azure-cli)에서 이러한 명령을 실행할 수 있습니다.
함수 앱의 시스템 관리 ID를 사용하여 Azure Digital Twins 인스턴스에 대한 _**Azure Digital Twins 데이터 소유자**_ 역할을 제공할 수 있습니다. 관리 ID는 인스턴스에서 데이터 평면 활동을 수행할 수 있는 함수 앱 권한을 제공합니다. 그런 다음 환경 변수를 설정하여 Azure Digital Twins 인스턴스의 URL에서 함수에 액세스할 수 있도록 합니다.

### <a name="assign-access-role"></a>액세스 역할 할당

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

이전 예의 함수 구조에서는 Azure Digital Twins를 인증할 수 있기 위해 전달자 토큰을 전달해야 합니다. 이 전달자 토큰이 전달되었는지 확인하려면 Azure Digital Twins에 액세스할 수 있도록 함수 앱에 대한 [MSI(관리 서비스 ID)](../active-directory/managed-identities-azure-resources/overview.md) 권한을 설정해야 합니다. 이 작업은 각 함수 앱에 대해 한 번만 수행하면 됩니다.


1. 다음 명령을 사용하여 함수에 대한 시스템 관리 ID의 세부 정보를 확인합니다. 출력에서 _principalId_ 필드를 기록해 둡니다.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > ID의 세부 정보를 표시하는 대신 결과가 비어 있는 경우 다음 명령을 사용하여 함수에 대한 시스템 관리 ID를 새로 만듭니다.
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > 그러면 출력에는 다음 단계에 필요한 _principalId_ 값을 포함하여 ID의 세부 정보가 표시됩니다. 

1. 다음 명령의 _principalId_ 값을 사용하여 함수 앱의 ID를 Azure Digital Twins 인스턴스의 _Azure Digital Twins 데이터 소유자_ 역할에 할당합니다.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>애플리케이션 설정 구성

마지막으로 **환경 변수** 를 설정하여 Azure Digital Twins 인스턴스의 URL에서 함수에 액세스할 수 있도록 합니다. 환경 변수에 대한 자세한 내용은 [*함수 앱 관리*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)를 참조하세요. 

> [!TIP]
> Azure Digital Twins 인스턴스의 URL은 Azure Digital Twins 인스턴스의 *호스트 이름* 시작 부분에 *https://* 를 추가하여 만들어집니다. 인스턴스의 모든 속성과 함께 호스트 이름을 확인하려면 `az dt show --dt-name <your-Azure-Digital-Twins-instance>`를 실행하면 됩니다.

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[Azure Portal](https://portal.azure.com/)에서 다음 단계를 완료합니다.

### <a name="assign-access-role"></a>액세스 역할 할당

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

시스템이 할당한 관리 ID를 사용하면 코드에 자격 증명을 저장하지 않고도 Azure 리소스가 클라우드 서비스(예: Azure Key Vault)에서 인증받을 수 있습니다. 사용하도록 설정하고 나면, 필요한 모든 권한을 Azure 역할 기반 액세스 제어를 통해 부여할 수 있습니다. 이 형식의 관리 ID 수명 주기는 이 리소스의 수명 주기와 연결되어 있습니다. 추가적으로 각 리소스에는 시스템이 할당한 관리 ID를 하나만 지정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에서 검색 창에 해당 이름을 입력하여 함수 앱을 검색합니다. 결과에서 앱을 선택합니다. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure Portal 스크린샷: 포털 검색 창에서 함수 앱의 이름을 검색하고 검색 결과가 강조 표시됩니다.":::

1. 함수 앱 페이지의 왼쪽 탐색 모음에서 _ID_ 를 선택하여 함수에 대한 관리 ID를 사용합니다. _시스템 할당_ 페이지에서 _상태_ 가 **켜기** 로 설정되어 있는지 확인합니다. 그렇지 않은 경우 지금 설정하고 변경 내용을 *저장* 합니다.

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Azure Portal의 스크린샷: 함수 앱에 대한 ID 페이지의 상태 옵션은 켜기로 설정되어 있습니다." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. _Azure 역할 할당_ 단추를 선택하면 *Azure 역할 할당* 페이지가 열립니다.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Azure Portal 스크린샷: Azure 함수의 ID 페이지에서 권한 아래에 있는 Azure 역할 할당 단추가 강조 표시됩니다." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    _+ 역할 할당 추가(미리 보기)_ 를 선택합니다.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Azure Portal 스크린샷: Azure 역할 할당 페이지에서 + 역할 할당 추가(미리 보기)가 강조 표시됩니다." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. 열리는 _역할 할당 추가(미리 보기)_ 페이지에서 다음 값을 선택합니다.

    * **범위**: 리소스 그룹
    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: 드롭다운에서 리소스 그룹을 선택합니다.
    * **역할**: 드롭다운에서 _Azure Digital Twins 데이터 소유자_ 를 선택합니다.

    그런 다음 _저장_ 단추를 눌러 세부 정보를 저장합니다.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Azure Portal의 스크린샷: 새 역할 할당(미리 보기)을 추가하는 대화 상자. 범위, 구독, 리소스 그룹 및 역할에 대한 필드가 있습니다.":::

### <a name="configure-application-settings"></a>애플리케이션 설정 구성

Azure Digital Twins 인스턴스의 URL에서 함수에 액세스할 수 있도록 **환경 변수** 를 설정할 수 있습니다. 환경 변수에 대한 자세한 내용은 [*함수 앱 관리*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)를 참조하세요. 애플리케이션 설정은 Azure Digital Twins 인스턴스에 액세스하기 위한 환경 변수로 표시됩니다. 

인스턴스 URL을 사용하여 환경 변수를 설정하려면 먼저 Azure Digital Twins 인스턴스의 호스트 이름을 찾아 URL을 가져옵니다. [Azure Portal](https://portal.azure.com) 검색 창에서 인스턴스를 검색합니다. 그런 다음 왼쪽 탐색 모음에서 _개요_ 를 선택하여 _호스트 이름_ 을 확인합니다. 이 값을 복사합니다.

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Azure Portal의 스크린샷: Azure Digital Twins 인스턴스의 개요 페이지에서 호스트 이름 값이 강조 표시됩니다.":::

이제 다음 단계를 사용하여 애플리케이션 설정을 만들 수 있습니다.

1. 포털 검색 창에서 함수 앱을 검색하고 결과에서 앱을 선택합니다.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure Portal 스크린샷: 포털 검색 창에서 함수 앱의 이름을 검색하고 검색 결과가 강조 표시됩니다.":::

1. 왼쪽의 탐색 모음에서 _구성_ 을 선택합니다. _애플리케이션 설정_ 탭에서 _+ 새 애플리케이션 설정_ 을 선택합니다.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure Portal의 스크린샷: 함수 앱에 대한 구성 페이지에서 새 애플리케이션 설정을 만드는 단추가 강조 표시됩니다.":::

1. 열리는 창에서 위에 복사된 호스트 이름 값을 사용하여 애플리케이션 설정을 만듭니다.
    * **이름**: ADT_SERVICE_URL
    * **값**: https://{your-azure-digital-twins-host-name}
    
    _확인_ 을 선택하여 애플리케이션 설정을 만듭니다.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure Portal의 스크린샷: 애플리케이션 설정 추가/편집 페이지에서 이름 및 값 필드를 입력한 후 확인 단추가 강조 표시됩니다.":::

1. 설정을 만들면 _애플리케이션 설정_ 탭에 다시 표시됩니다. 목록에 *ADT_SERVICE_URL* 이 나타나는지 확인한 다음 _저장_ 단추를 선택하여 새 애플리케이션 설정을 저장합니다.

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure Portal의 스크린샷: 새 ADT_SERVICE_URL 설정이 강조 표시된 애플리케이션 설정 페이지. 저장 단추도 강조 표시됩니다.":::

1. 애플리케이션 설정에 대한 변경 내용을 적용하려면 애플리케이션을 다시 시작해야 하므로 메시지가 표시되면 _계속_ 을 선택하여 애플리케이션을 다시 시작합니다.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure Portal 스크린샷: 애플리케이션을 다시 시작하면 애플리케이션 설정이 변경된다는 알림이 있습니다. 계속 단추가 강조 표시됩니다.":::

---

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure에서 Azure Digital Twins와 함께 사용할 함수 앱을 설정하는 단계를 수행했습니다.

다음으로, 기본 함수를 빌드하여 Azure Digital Twins로 IoT Hub 데이터를 수집하는 방법을 알아봅니다.
* [*방법: IoT Hub에서 원격 분석 수집*](how-to-ingest-iot-hub-data.md)
