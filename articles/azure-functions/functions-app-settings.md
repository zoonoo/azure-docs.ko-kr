---
title: Azure Functions에 대 한 앱 설정 참조
description: Azure Functions 앱 설정 또는 환경 변수에 대 한 참조 설명서입니다.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 1c7f5f9f8f6f198c5fe74baa613306732fa9b55b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977270"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions에 대 한 앱 설정 참조

함수 앱의 앱 설정에는 해당 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션이 포함 되어 있습니다. 로컬로 실행 하는 경우 이러한 설정은 로컬 [환경 변수로](functions-run-local.md#local-settings-file)액세스 됩니다. 이 문서에서는 함수 앱에서 사용할 수 있는 앱 설정을 나열 합니다.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

다른 전역 구성 옵션은 [host. json](functions-host-json.md) 파일 및 [로컬 설정 json](functions-run-local.md#local-settings-file) 파일에 있습니다.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights를 사용 하는 경우 Application Insights 계측 키입니다. [Monitor Azure Functions](functions-monitoring.md)를 참조 하세요.

|Jelmagyarázat|Mintaérték|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

버전 2.x 이상 버전의 함수 런타임에서는 런타임 환경에 따라 앱 동작을 구성 합니다. [초기화 하는 동안](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43)이 값을 읽습니다. `AZURE_FUNCTIONS_ENVIRONMENT`를 값으로 설정할 수 있지만 [개발](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [준비](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)및 [프로덕션](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production)의 [세 가지 값](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) 이 지원 됩니다. `AZURE_FUNCTIONS_ENVIRONMENT` 설정 되지 않은 경우 기본적으로 로컬 환경에서 `Development` 하 고 Azure에서 `Production` 됩니다. 런타임 환경을 설정 하는 `ASPNETCORE_ENVIRONMENT` 대신이 설정을 사용 해야 합니다. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

로그를 저장 하 고 포털의 **모니터** 탭에 표시 하는 데 사용할 수 있는 선택적 저장소 계정 연결 문자열입니다. 저장소 계정은 blob, 큐 및 테이블을 지 원하는 범용 항목 이어야 합니다. [저장소 계정](functions-infrastructure-as-code.md#storage-account) 및 [저장소 계정 요구 사항](functions-create-function-app-portal.md#storage-account-requirements)을 참조 하세요.

|Jelmagyarázat|Mintaérték|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [키]|

> [!TIP]
> 성능 및 경험을 위해 AzureWebJobsDashboard 대신 모니터링에 APPINSIGHTS_INSTRUMENTATIONKEY 및 App Insights를 사용 하는 것이 좋습니다.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`은 함수 앱의 루트 URL에 대해 표시 되는 기본 방문 페이지를 사용 하지 않도록 설정 합니다. Az alapértelmezett érték a `false`.

|Jelmagyarázat|Mintaérték|
|---|------------|
|AzureWebJobsDisableHomepage|igaz|

이 앱 설정을 생략 하거나 `false`로 설정 하면 URL `<functionappname>.azurewebsites.net`에 대 한 응답으로 다음 예와 유사한 페이지가 표시 됩니다.

![함수 앱 방문 페이지](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`은 .NET 코드를 컴파일할 때 릴리스 모드를 사용 함을 의미 합니다. `false`은 디버그 모드를 사용 함을 의미 합니다. Az alapértelmezett érték a `true`.

|Jelmagyarázat|Mintaérték|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|igaz|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

사용할 수 있는 베타 기능의 쉼표로 구분 된 목록입니다. 이러한 플래그를 사용 하 여 사용 하도록 설정 된 베타 기능은 프로덕션 준비가 되지 않지만 라이브 상태로 전환 하기 전에 실험적 사용을 위해 사용 하도록 설정할 수 있습니다.

|Jelmagyarázat|Mintaérték|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, feature2>|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

키 저장소에 사용할 리포지토리 또는 공급자를 지정 합니다. 현재 지원 되는 리포지토리는 blob storage ("Blob") 및 로컬 파일 시스템 ("파일")입니다. 기본값은 버전 2에서는 blob이 고 버전 1에서는 파일 시스템입니다.

|Jelmagyarázat|Mintaérték|
|---|------------|
|AzureWebJobsSecretStorageType|Fájlok|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions 런타임은 HTTP 트리거된 함수를 제외한 모든 함수에 대해이 저장소 계정 연결 문자열을 사용 합니다. 저장소 계정은 blob, 큐 및 테이블을 지 원하는 범용 항목 이어야 합니다. [저장소 계정](functions-infrastructure-as-code.md#storage-account) 및 [저장소 계정 요구 사항](functions-create-function-app-portal.md#storage-account-requirements)을 참조 하세요.

|Jelmagyarázat|Mintaérték|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [키]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

TypeScript에 사용 되는 컴파일러의 경로입니다. 필요한 경우 기본값을 재정의할 수 있습니다.

|Jelmagyarázat|Mintaérték|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>함수\_앱\_편집\_모드

Azure Portal에서 편집할 수 있는지 여부를 지정 합니다. 유효한 값은 "readwrite" 및 "readonly"입니다.

|Jelmagyarázat|Mintaérték|
|---|------------|
|함수\_앱\_편집\_모드|readonly|

## <a name="functions_extension_version"></a>함수\_확장\_버전

이 함수 앱에서 사용할 함수 런타임의 버전입니다. 주 버전의 물결표는 해당 주 버전의 최신 버전을 사용 하는 것을 의미 합니다 (예: "~ 2"). 동일한 주 버전의 새 버전을 사용할 수 있는 경우 함수 앱에 자동으로 설치 됩니다. 앱을 특정 버전에 고정 하려면 전체 버전 번호 (예: "2.0.12345")를 사용 합니다. 기본값은 "~ 2"입니다. 값 `~1`는 앱을 런타임 버전 1.x에 고정 합니다.

|Jelmagyarázat|Mintaérték|
|---|------------|
|함수\_확장\_버전|~ 2|

## <a name="functions_worker_process_count"></a>함수\_작업자\_프로세스\_수

`1`기본값을 사용 하 여 언어 작업자 프로세스의 최대 수를 지정 합니다. 허용 되는 최대 값은 `10`입니다. 함수 호출은 언어 작업자 프로세스 간에 균등 하 게 분산 됩니다. 언어 작업자 프로세스는 함수\_작업자\_프로세스\_수에 도달할 때까지 10 초 마다 생성 됩니다. 여러 언어 작업자 프로세스를 사용 하는 것은 [크기 조정과](functions-scale.md)동일 하지 않습니다. 작업에 CPU 바인딩과 i/o 바인딩된 호출이 혼합 되어 있는 경우이 설정을 사용 하는 것이 좋습니다. 이 설정은 모든 non-.NET 언어에 적용 됩니다.

|Jelmagyarázat|Mintaérték|
|---|------------|
|함수\_작업자\_프로세스\_수|2|


## <a name="functions_worker_runtime"></a>WORKER\_런타임\_함수

함수 앱에 로드할 언어 작업자 런타임입니다.  이는 응용 프로그램에서 사용 되는 언어 (예: "dotnet")와 일치 합니다. 여러 언어의 함수에 대해 각각 해당 작업자 런타임 값을 포함 하는 여러 앱에 게시 해야 합니다.  유효한 값은 `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) 및 `python` (Python)입니다.

|Jelmagyarázat|Mintaérték|
|---|------------|
|WORKER\_런타임\_함수|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

소비 & 프리미엄 요금제에만 해당 합니다. 함수 앱 코드 및 구성이 저장 되는 저장소 계정에 대 한 연결 문자열입니다. [함수 앱 만들기를](functions-infrastructure-as-code.md#create-a-function-app)참조 하세요.

|Jelmagyarázat|Mintaérték|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [키]|

## <a name="website_contentshare"></a>웹 사이트\_CONTENTSHARE

소비 & 프리미엄 요금제에만 해당 합니다. 함수 앱 코드 및 구성에 대 한 파일 경로입니다. WEBSITE_CONTENTAZUREFILECONNECTIONSTRING와 함께 사용 됩니다. 기본값은 함수 앱 이름으로 시작 하는 고유 문자열입니다. [함수 앱 만들기를](functions-infrastructure-as-code.md#create-a-function-app)참조 하세요.

|Jelmagyarázat|Mintaérték|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>웹 사이트\_최대\_동적\_응용 프로그램\_규모\_확장

함수 앱이 확장할 수 있는 최대 인스턴스 수입니다. 기본값은 제한 없음입니다.

> [!NOTE]
> 이 설정은 미리 보기 기능이 며 값으로 설정 된 경우에만 신뢰할 수 < = 5

|Jelmagyarázat|Mintaérték|
|---|------------|
|웹 사이트\_최대\_동적\_응용 프로그램\_규모\_확장|5|

## <a name="website_node_default_version"></a>웹 사이트\_노드\_DEFAULT_VERSION

_Windows에만 해당 합니다._  
Windows에서 함수 앱을 실행할 때 사용할 node.js의 버전을 설정 합니다. 물결표 (~)를 사용 하 여 런타임에서 대상 주 버전의 사용 가능한 최신 버전을 사용 하도록 해야 합니다. 예를 들어 `~10`로 설정 된 경우 node.js 10의 최신 버전이 사용 됩니다. 주 버전의 대상이 물결표 인 경우 부 버전을 수동으로 업데이트 하지 않아도 됩니다. 

|Jelmagyarázat|Mintaérték|
|---|------------|
|웹 사이트\_노드\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>웹 사이트\_\_패키지에서\_실행

탑재 된 패키지 파일에서 함수 앱을 실행할 수 있도록 합니다.

|Jelmagyarázat|Mintaérték|
|---|------------|
|웹 사이트\_\_패키지에서\_실행|1|

유효한 값은 배포 패키지 파일의 위치로 확인 되는 URL 이거나 `1`입니다. `1`로 설정 된 경우 패키지는 `d:\home\data\SitePackages` 폴더에 있어야 합니다. 이 설정에서 zip 배포를 사용 하는 경우 패키지가이 위치에 자동으로 업로드 됩니다. 미리 보기에서이 설정은 `WEBSITE_RUN_FROM_ZIP`이름이 지정 되었습니다. 자세한 내용은 [패키지 파일에서 함수 실행](run-functions-from-deployment-package.md)을 참조 하세요.

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

기본적으로 함수 프록시는 새 HTTP 요청을 생성 하는 대신 바로 가기를 사용 하 여 프록시에서 동일한 함수 앱의 함수로 직접 API 호출을 보냅니다. 이 설정을 통해 해당 동작을 사용 하지 않도록 설정할 수 있습니다.

|Jelmagyarázat|Value (Díj)|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|igaz|지역 함수 앱의 함수를 가리키는 백 엔드 url을 사용 하는 호출은 더 이상 함수로 직접 전송 되지 않으며 대신 HTTP 프런트 엔드로 다시 전달 됩니다 함수 앱|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|hamis|Ez az alapértelmezett érték. 로컬 함수 앱의 함수를 가리키는 백 엔드 url을 사용 하는 호출은 해당 함수로 직접 전달 됩니다.|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

이 설정은 백 엔드 URL에 삽입 될 때 경로 매개 변수 에서% 2F가 슬래시로 디코딩되는 지 여부를 제어 합니다. 

|Jelmagyarázat|Value (Díj)|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|igaz|인코딩된 슬래시가 있는 경로 매개 변수는 해당 매개 변수가 디코딩됩니다. `example.com/api%2ftest` `example.com/api/test` 됩니다.|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|hamis|이는 기본 동작입니다. 모든 경로 매개 변수가 변경 되지 않은 상태로 전달 됩니다.|

### <a name="example"></a>Példa

다음은 URL의 함수 앱에 있는 myfunction.com의 예제입니다.

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|URL 디코딩|Input (Bemenet)|Kimenet|
|-|-|-|
|igaz|myfunction.com/test%2fapi|example.com/test/api
|hamis|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Következő lépések

[앱 설정을 업데이트 하는 방법 알아보기](functions-how-to-use-azure-function-app-settings.md#settings)

[호스트 json 파일의 전역 설정을 참조 하세요.](functions-host-json.md)

[App Service 앱에 대 한 기타 앱 설정을 참조 하세요.](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
