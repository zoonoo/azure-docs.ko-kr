---
title: Azure에서 데이터 처리를 위한 함수 설정
titleSuffix: Azure Digital Twins
description: 디지털 쌍으로 액세스 하 여 트리거할 수 있는 Azure에서 함수를 만드는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6c4f23406c97d647002fbb3ab4a3544866303cf4
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051346"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Azure에서 함수 앱을 연결 하 여 데이터 처리

데이터를 기반으로 하는 디지털 쌍 업데이트는 [Azure Functions](../azure-functions/functions-overview.md)을 사용 하 여 만든 함수와 같은 계산 리소스를 통해 [**이벤트 경로**](concepts-route-events.md) 를 사용 하 여 처리 됩니다. 함수는 다음에 대 한 응답으로 디지털 쌍을 업데이트 하는 데 사용할 수 있습니다.
* IoT Hub에서 들어오는 장치 원격 분석 데이터
* 쌍 그래프 내에서 다른 디지털 쌍의 속성 변경 또는 기타 데이터

이 문서에서는 azure에서 Azure Digital Twins와 함께 사용할 함수를 만드는 과정을 안내 합니다. 

다음은 포함 된 단계에 대 한 개요입니다.

1. Visual Studio에서 Azure Functions 프로젝트 만들기
2. [Event Grid](../event-grid/overview.md) 트리거를 사용 하 여 함수 작성
3. 함수에 인증 코드 추가 (Azure Digital Twins에 액세스할 수 있음)
4. Azure에 함수 앱 게시
5. 함수 앱에 대 한 [보안](concepts-security.md) 액세스 설정

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>필수 조건: Azure Digital Twins 인스턴스 설정

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Visual Studio에서 함수 앱 만들기

Visual Studio 2019에서 _파일 > 새 > 프로젝트_ 를 선택 하 고 _Azure Functions_ 템플릿을 검색 한 후 _다음_ 을 선택 합니다.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: 새 프로젝트 대화 상자":::

함수 앱의 이름을 지정 하 고 _만들기_ 를 선택 합니다.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: 새 프로젝트 구성":::

*트리거 Event Grid* 함수 앱의 유형을 선택 하 고 _만들기_ 를 선택 합니다.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: Azure Functions 프로젝트 트리거 대화 상자":::

함수 앱이 만들어지면 visual studio는 프로젝트 폴더의 **function.cs** 파일에 자동으로 채워진 코드 샘플을 갖게 됩니다. 이 short 함수는 이벤트를 기록 하는 데 사용 됩니다.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: 프로젝트 창 및 샘플 코드":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Event Grid 트리거를 사용 하 여 함수 작성

함수 앱에 SDK를 추가 하 여 함수를 작성할 수 있습니다. 함수 앱은 [Azure Digital TWINS SDK for .net (c #)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)을 사용 하 여 azure 디지털 쌍과 상호 작용 합니다. 

SDK를 사용 하려면 다음 패키지를 프로젝트에 포함 해야 합니다. Visual studio NuGet 패키지 관리자를 사용 하 여 패키지를 설치 하거나 명령줄 도구를 사용 하 여 패키지를 추가할 수 있습니다 `dotnet` . 다음 방법 중 하나를 선택 합니다. 

**옵션 1. Visual Studio 패키지 관리자를 사용 하 여 패키지 추가:**
    
프로젝트를 마우스 오른쪽 단추로 선택 하 고 목록에서 _NuGet 패키지 관리_ 를 선택 하 여이 작업을 수행할 수 있습니다. 그런 다음 열리는 창에서 _찾아보기_ 탭을 선택 하 고 다음 패키지를 검색 합니다. _설치_ 를 선택 하 고 사용권 계약에 _동의_ 하 여 패키지를 설치 합니다.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Azure Functions에 대해 올바르게 설정 되도록 Azure SDK 파이프라인을 구성 하려면 다음 패키지도 필요 합니다. 위와 동일한 프로세스를 반복 하 여 모든 패키지를 설치 합니다.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**옵션 2. 명령줄 도구를 사용 하 여 패키지를 추가 합니다 `dotnet` .**

또는 `dotnet add` 명령줄 도구에서 다음 명령을 사용할 수 있습니다.
```cmd/sh
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```

그런 다음 Azure Digital Twins를 사용 하는 데 필요한 두 개 이상의 종속성을 프로젝트에 추가 합니다. 아래 링크를 사용하여 NuGet의 패키지로 이동할 수 있습니다. 여기서 콘솔 명령(.NET CLI용 포함)을 찾아 각각의 최신 버전을 프로젝트에 추가할 수 있습니다.
 * [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). [.NET용 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)의 패키지입니다.
 * [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). 이 라이브러리는 Azure에 대한 인증에 유용한 도구를 제공합니다.

그런 다음 Visual Studio 솔루션 탐색기에서 샘플 코드가 있는 _function.cs_ 파일을 열고 함수에 다음 _using_ 문을 추가 합니다. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>함수에 인증 코드 추가

이제 클래스 수준 변수를 선언 하 고 함수가 Azure Digital Twins에 액세스할 수 있게 하는 인증 코드를 추가 합니다. {Your function name} .cs 파일의 함수에 다음을 추가 합니다.

* 환경 변수로 ADT 서비스 URL을 읽습니다. 함수에 하드 코딩 하는 대신 환경 변수에서 서비스 URL을 읽는 것이 좋습니다.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* HttpClient 인스턴스를 보유 하는 정적 변수입니다. HttpClient는 생성 하는 데 비교적 비용이 많이 들고 모든 함수 호출에 대해이 작업을 수행 하지 않으려고 합니다.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Azure Functions에서 관리 되는 id 자격 증명을 사용할 수 있습니다.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* 함수 내부에 _DigitalTwinsClient_ 지역 변수를 추가 하 여 함수 프로젝트에 Azure Digital twins 클라이언트 인스턴스를 포함 합니다. 클래스 내부에서이 변수를 정적으로 설정 *하지* 마십시오.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* _AdtInstanceUrl_ 에 대 한 null 검사를 추가 하 고 함수 논리를 try catch 블록으로 래핑하여 예외를 catch 합니다.

이러한 변경 후 함수 코드는 다음과 유사 합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

## <a name="publish-the-function-app-to-azure"></a>Azure에 함수 앱 게시

Azure의 함수 앱에 프로젝트를 게시 하려면 솔루션 탐색기의 함수 프로젝트 (솔루션 아님)를 마우스 오른쪽 단추로 선택 하 고 **게시** 를 선택 합니다.

> [!IMPORTANT] 
> Azure에서 함수 앱에 게시 하는 경우 Azure Digital Twins와 무관 하 게 구독에 대 한 추가 요금이 발생 합니다.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: Azure에 함수 게시":::

게시 대상으로 **Azure** 를 선택 하 고 **다음** 을 선택 합니다.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: 게시 Azure Functions 대화 상자에서 Azure를 선택 합니다. ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: 게시 함수 대화 상자에서 컴퓨터에 따라 Azure 함수 앱 (Windows) 또는 (Linux)를 선택 합니다.":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: 함수 게시 대화 상자, 새 Azure 함수 만들기":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: 함수 게시 대화 상자에서 필드를 입력 하 고 만들기를 선택 합니다.":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: 함수 게시 대화 상자의 목록에서 함수 앱을 선택 하 고 마침을 선택 합니다.":::

다음 페이지에서 새 함수 앱, 리소스 그룹 및 기타 세부 정보에 대해 원하는 이름을 입력 합니다.
함수 앱이 Azure 디지털 쌍에 액세스할 수 있으려면 시스템에서 관리 하는 id가 있어야 하 고 Azure Digital Twins 인스턴스에 액세스할 수 있는 권한이 있어야 합니다.

그런 다음 CLI 또는 Azure Portal를 사용 하 여 함수에 대 한 보안 액세스를 설정할 수 있습니다. 다음 방법 중 하나를 선택 합니다.

## <a name="set-up-security-access-for-the-function-app"></a>함수 앱에 대 한 보안 액세스 설정
다음 옵션 중 하나를 사용 하 여 함수 앱에 대 한 보안 액세스를 설정할 수 있습니다.

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>옵션 1: CLI를 사용 하 여 함수 앱에 대 한 보안 액세스 설정

이전 예의 함수 해골은 Azure 디지털 쌍으로 인증 하기 위해 전달자 토큰을 전달 해야 합니다. 이 전달자 토큰이 전달 되었는지 확인 하려면 함수 앱에 대해 [MSI (관리 서비스 ID)](../active-directory/managed-identities-azure-resources/overview.md) 를 설정 해야 합니다. 이 작업은 각 함수 앱에 대해 한 번만 수행 해야 합니다.

시스템 관리 id를 만들고 Azure Digital Twins 인스턴스의 _**Azure Digital Twins 데이터 소유자**_ 역할에 함수 앱의 id를 할당할 수 있습니다. 이렇게 하면 인스턴스에서 데이터 평면 작업을 수행할 수 있는 함수 앱 권한이 제공 됩니다. 그런 다음 환경 변수를 설정 하 여 함수에서 Azure Digital Twins 인스턴스의 URL에 액세스할 수 있도록 합니다.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

[Azure Cloud Shell](https://shell.azure.com) 를 사용 하 여 명령을 실행 합니다.

다음 명령을 사용하여 시스템 관리 ID를 만듭니다. 출력에서 _principalId_ 필드를 기록해 둡니다.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
다음 명령에서 _principalid_ 값을 사용 하 여 Azure Digital twins 인스턴스의 _Azure Digital Twins 데이터 소유자_ 역할에 함수 앱의 id를 할당 합니다.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
마지막으로 환경 변수를 설정 하 여 함수에서 Azure Digital Twins 인스턴스의 URL에 액세스할 수 있도록 설정할 수 있습니다. 환경 변수를 설정 하는 방법에 대 한 자세한 내용은 [*환경 변수*](/sandbox/functions-recipes/environment-variables)를 참조 하세요. 

> [!TIP]
> Azure Digital Twins 인스턴스의 URL은 Azure Digital Twins 인스턴스의 *호스트 이름* 앞에 *https://* 를 추가 하 여 수행 됩니다. 인스턴스의 모든 속성과 함께 호스트 이름을 보려면를 실행할 수 있습니다 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>옵션 2: Azure Portal을 사용 하 여 함수 앱에 대 한 보안 액세스 설정

시스템 할당 관리 id를 사용 하면 Azure 리소스가 코드에 자격 증명을 저장 하지 않고도 클라우드 서비스 (예: Azure Key Vault)에 인증할 수 있습니다. 사용 하도록 설정 되 면 Azure 역할 기반 액세스 제어를 통해 필요한 모든 권한을 부여할 수 있습니다. 이 유형의 관리 되는 id의 수명 주기는이 리소스의 수명 주기에 연결 됩니다. 또한 각 리소스 (예: 가상 머신)에는 시스템 할당 관리 id가 하나만 있을 수 있습니다.

[Azure Portal](https://portal.azure.com/)에서 이전에 만든 함수 앱 이름으로 검색 표시줄에서 _함수 앱_ 을 검색 합니다. 목록에서 *함수 앱* 을 선택 합니다. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Azure Portal: 함수 앱 검색":::

함수 앱 창의 왼쪽 탐색 모음에서 _id_ 를 선택 하 여 관리 되는 id를 사용 하도록 설정 합니다.
_시스템 할당 됨_ 탭에서 _상태_ 를 켜기로 전환 하 고 _저장_ 합니다. _시스템 할당 관리 id를 사용 하도록 설정_ 하는 팝업이 표시 됩니다.
_예_ 단추를 선택 합니다. 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure Portal: 시스템 관리 id 사용":::

함수가 Azure Active Directory에 성공적으로 등록 되었음을 알림에서 확인할 수 있습니다.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure Portal: 알림":::

또한 다음 섹션에서 사용 되므로 _id_ 페이지에 표시 된 **개체 id** 를 확인 합니다.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="나중에 사용할 개체 ID를 복사 합니다.":::

### <a name="assign-access-roles-using-azure-portal"></a>Azure Portal를 사용 하 여 액세스 역할 할당

Azure 역할 _할당 단추를_ 선택 하면 *azure 역할 할당* 페이지가 열립니다. 그런 다음 _+ 역할 할당 추가 (미리 보기)_ 를 선택 합니다.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure Portal: 역할 할당 추가":::

열리는 _역할 할당 추가 (미리 보기)_ 페이지에서 다음을 선택 합니다.

* _범위_: 리소스 그룹
* _구독_: Azure 구독을 선택 합니다.
* _리소스 그룹_: 드롭다운에서 리소스 그룹을 선택 합니다.
* _역할_: 드롭다운에서 _Azure Digital Twins 데이터 소유자_ 를 선택 합니다.

_저장_ 단추를 방문 하 여 세부 정보를 저장 합니다.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure Portal: 역할 할당 추가 (미리 보기) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Azure Portal를 사용 하 여 응용 프로그램 설정 구성

환경 변수를 설정 하 여 함수에서 Azure Digital Twins 인스턴스의 URL에 액세스할 수 있도록 설정할 수 있습니다. 이에 대 한 자세한 내용은 [*환경 변수*](/sandbox/functions-recipes/environment-variables)를 참조 하세요. 응용 프로그램 설정은 디지털 쌍 인스턴스에 액세스 하기 위한 환경 변수로 표시 됩니다. 

응용 프로그램 설정을 만들려면 ADT_INSTANCE_URL 필요 합니다.

인스턴스 호스트 이름에 **_https://_** 를 추가 하 여 ADT_INSTANCE_URL를 가져올 수 있습니다. Azure Portal 검색 창에서 인스턴스를 검색 하 여 디지털 쌍 인스턴스 호스트 이름을 찾을 수 있습니다. 그런 다음 왼쪽 탐색 모음에서 _개요_ 를 선택 하 여 _호스트 이름을_ 확인 합니다. 응용 프로그램 설정을 만들려면이 값을 복사 합니다.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure Portal: 개요-_Value_ 필드에서 사용할 호스트 이름을 복사 > 합니다.":::

이제 다음 단계에 따라 응용 프로그램 설정을 만들 수 있습니다.

* 검색 창에서 함수 앱 이름을 사용 하 여 앱을 검색 하 고 목록에서 함수 앱을 선택 합니다.
* 왼쪽의 탐색 모음에서 _구성_ 을 선택 하 여 새 응용 프로그램 설정을 만듭니다.
* _응용 프로그램 설정_ 탭에서 _+ 새 응용 프로그램 설정_ 을 선택 합니다.

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure Portal: 기존 함수 앱 검색":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure Portal: 응용 프로그램 설정 구성":::

열리는 창에서 위에 복사 된 값을 사용 하 여 응용 프로그램 설정을 만듭니다. \
_이름_  : ADT_SERVICE_URL \
_값_ : https://{-azure-디지털-t--hostname}

_확인_ 을 선택 하 여 응용 프로그램 설정을 만듭니다.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure Portal: 응용 프로그램 설정을 추가 합니다.":::

_이름_ 필드에 응용 프로그램 이름을 사용 하 여 응용 프로그램 설정을 볼 수 있습니다. 그런 다음 _저장_ 단추를 선택 하 여 응용 프로그램 설정을 저장 합니다.

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure Portal: 만든 응용 프로그램을 확인 하 고 응용 프로그램을 다시 시작 합니다.":::

응용 프로그램 설정에 대 한 변경 내용을 적용 하려면 응용 프로그램을 다시 시작 해야 합니다. _계속_ 을 선택 하 여 응용 프로그램을 다시 시작 합니다.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure Portal: 응용 프로그램 설정 저장":::

_알림_ 아이콘을 선택 하 여 응용 프로그램 설정이 업데이트 된 것을 볼 수 있습니다. 응용 프로그램 설정이 만들어지지 않은 경우 위의 프로세스에 따라 응용 프로그램 설정 추가를 다시 시도할 수 있습니다.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure Portal: 응용 프로그램 설정 업데이트에 대 한 알림":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 azure에서 Azure Digital Twins와 함께 사용 하기 위해 Azure에서 함수 앱을 설정 하는 단계를 따릅니다. 그런 다음 Event Grid에 대 한 함수를 구독 하 여 끝점에서 수신 대기할 수 있습니다. 이 끝점은 다음과 같을 수 있습니다.
* Azure digital 쌍에 연결 된 Event Grid 끝점 (예: 속성 변경 메시지, 쌍의 [디지털](concepts-twins-graph.md) 쌍에 의해 생성 된 원격 분석 메시지 또는 수명 주기 메시지)을 처리 하기 위해 azure digital 쌍에 연결 된 끝점
* IoT Hub에서 원격 분석 및 기타 장치 이벤트를 전송 하는 데 사용 하는 IoT 시스템 항목입니다.
* 다른 서비스에서 메시지를 수신 하는 Event Grid 끝점

다음으로, 기본 함수를 빌드하여 Azure Digital Twins에 IoT Hub 데이터를 수집 하는 방법을 참조 하세요.
* [*방법: IoT Hub에서 원격 분석 수집*](how-to-ingest-iot-hub-data.md)
