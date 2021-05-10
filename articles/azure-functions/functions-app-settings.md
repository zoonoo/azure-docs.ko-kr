---
title: Azure Functions에 대한 앱 설정 참조
description: Azure Functions 앱 설정 또는 환경 변수에 대한 참조 설명서입니다.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 327f120d387a3a08f0de9db2da718d530346e545
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773082"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions에 대한 앱 설정 참조

함수 앱의 앱 설정에는 해당 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션이 포함됩니다. 로컬에서 실행할 때 이러한 설정은 로컬 [환경 변수](functions-run-local.md#local-settings-file)로 액세스합니다. 이 문서에는 함수 앱에서 사용할 수 있는 앱 설정이 나열되어 있습니다.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[host.json](functions-host-json.md) 파일과 [local.settings.json](functions-run-local.md#local-settings-file) 파일에는 다른 전역 구성 옵션이 있습니다.

> [!NOTE]  
> 애플리케이션 설정을 사용하여 host.json 파일 자체를 변경할 필요 없이 host.json 설정 값을 재정의할 수 있습니다. 이 기능은 특정 환경에 대한 특정 host.json 설정을 구성하거나 수정해야 하는 경우에 유용합니다. 이렇게 하면 프로젝트를 다시 게시하지 않고도 host.json 설정을 변경할 수 있습니다. 자세한 내용은 [host.json 참조 문서](functions-host-json.md#override-hostjson-values)를 참조하세요. 함수 앱 설정을 변경하려면 함수 앱을 다시 시작해야 합니다.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights에 대한 계측 키입니다. `APPINSIGHTS_INSTRUMENTATIONKEY` 또는 `APPLICATIONINSIGHTS_CONNECTION_STRING` 중 하나만 사용합니다. 소버린 클라우드에서 Application Insights가 실행되는 경우 `APPLICATIONINSIGHTS_CONNECTION_STRING`을 사용합니다. 자세한 내용은 [Azure Functions에 대한 모니터링을 구성하는 방법](configure-monitoring.md)을 참조하세요. 

|키|샘플 값|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Application Insights에 대한 연결 문자열입니다. 다음과 같은 경우에는 `APPINSIGHTS_INSTRUMENTATIONKEY` 대신 `APPLICATIONINSIGHTS_CONNECTION_STRING`을 사용합니다.

+ 함수 앱에 연결 문자열을 사용하여 지원되는 추가된 사용자 지정이 필요한 경우 
+ Application Insights 인스턴스가 소버린 클라우드에서 실행되며 사용자 지정 엔드포인트가 필요한 경우

자세한 내용은 [연결 문자열](../azure-monitor/app/sdk-connection-string.md)을 참조하세요. 

|키|샘플 값|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey=[key];IngestionEndpoint=[url];LiveEndpoint=[url];ProfilerEndpoint=[url];SnapshotEndpoint=[url];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

기본적으로 [함수 프록시](functions-proxies.md)는 바로 가기를 사용하여 프록시에서 동일한 함수 앱의 함수로 API 호출을 직접 보냅니다. 이 바로 가기는 새 HTTP 요청을 만드는 대신 사용됩니다. 이 설정을 사용하면 바로 가기 동작을 사용하지 않도록 설정할 수 있습니다.

|키|값|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|로컬 함수 앱에서 함수를 가리키는 백 엔드 URL을 사용하는 호출은 해당 함수로 직접 전달되지 않습니다. 대신, 요청은 함수 앱에 대한 HTTP 프런트 엔드로 다시 전달됩니다.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|로컬 함수 앱에서 함수를 가리키는 백 엔드 URL을 사용하는 호출은 해당 함수로 직접 전달됩니다. 이것은 기본값입니다. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

이 설정은 백 엔드 URL로 삽입될 때 `%2F` 문자가 경로 매개 변수에서 슬래시로 디코딩되는지 여부를 제어합니다. 

|키|값|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|인코딩된 슬래시를 사용하는 경로 매개 변수는 디코딩됩니다. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|모든 경로 매개 변수는 기본 동작인 변경되지 않은 상태로 전달됩니다. |

예를 들어 `myfunction.com` 도메인의 함수 앱에 대한 proxies.json 파일을 살펴보겠습니다.

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

`AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES`가 `true`로 설정되면 URL은 `example.com/api%2ftest`를 `example.com/api/test`로 확인합니다. 기본적으로 URL은 변경되지 않고 `example.com/test%2fapi` 그대로 유지됩니다. 자세한 내용은 [함수 프록시](functions-proxies.md)를 참조하세요.

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

버전 2.x 이상 버전의 함수 런타임에서는 런타임 환경을 기준으로 앱 동작을 구성합니다. 초기화하는 동안 이 값을 읽고 임의의 값으로 설정할 수 있습니다. 런타임에는 `Development` `Staging` 및 `Production` 값만 허용됩니다. Azure에서 실행되는 경우 이 애플리케이션 설정이 표시되지 않으면 환경이 `Production`으로 간주됩니다. Azure의 런타임 환경을 `Production` 이외의 항목으로 변경해야 하는 경우 `ASPNETCORE_ENVIRONMENT` 대신 이 설정을 사용합니다. Azure Functions Core Tools는 로컬 컴퓨터에서 실행되는 경우 `AZURE_FUNCTIONS_ENVIRONMENT`을 `Development`로 설정하며, 이 값은 local.settings.json 파일에서 재정의할 수 없습니다. 자세히 알아보려면 [환경 기반 시작 클래스 및 메서드](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods)를 참조하세요.

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

버전 2.x 이상 버전의 Functions 런타임에서는 애플리케이션 설정이 현재 환경의 [host.json](functions-host-json.md) 설정을 재정의할 수 있습니다. 이러한 재정의는 `AzureFunctionsJobHost__path__to__setting`이라는 애플리케이션 설정으로 표현됩니다. 자세한 내용은 [host.json 값 재정의](functions-host-json.md#override-hostjson-values)를 참조하세요.

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

로그를 저장하고 포털의 **모니터** 탭에 표시하기 위한 선택적인 스토리지 계정 연결 문자열입니다. 이 설정은 Azure Functions 런타임의 버전 1.x를 대상으로 하는 앱에만 유효합니다. 스토리지 계정은 Blob, 큐 및 테이블을 지원하는 범용 계정이어야 합니다. 자세한 내용은 [스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조하세요.

|키|샘플 값|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> 성능 및 환경을 향상시키기 위해 런타임 2.x 이상 버전에서는 모니터링을 위해 `AzureWebJobsDashboard` 대신 APPINSIGHTS_INSTRUMENTATIONKEY 및 App Insights를 사용합니다.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`는 함수 앱의 루트 URL에 표시되는 기본 방문 페이지를 사용하지 않도록 설정한다는 의미입니다. 기본값은 `false`입니다.

|키|샘플 값|
|---|------------|
|AzureWebJobsDisableHomepage|true|

이 앱 설정이 생략되거나 `false`로 설정되면 URL `<functionappname>.azurewebsites.net`에 대한 응답으로 다음 예와 유사한 페이지가 표시됩니다.

![함수 앱 방문 페이지](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`는 .NET 코드를 컴파일할 때 릴리스 모드 사용을 의미합니다. `false`는 디버그 모드 사용을 의미합니다. 기본값은 `true`입니다.

|키|샘플 값|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

사용하도록 설정할 쉼표로 구분된 베타 기능 목록입니다. 이 플래그로 사용할 수 있는 베타 기능은 프로덕션 준비가 되지 않았지만 실제로 사용하기 전에 실험적 용도로 사용할 수 있습니다.

|키|샘플 값|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

키 스토리지에 사용할 리포지토리 또는 공급자를 지정합니다. 현재 지원되는 리포지토리는 Blob Storage(&quot;Blob&quot;) 및 로컬 파일 시스템(&quot;Files&quot;)입니다. 기본값은 버전 2에서는 Blob, 버전 1에서는 파일 시스템입니다.

|키|샘플 값|
|---|------------|
|AzureWebJobsSecretStorageType|파일|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions 런타임은 일반 작업을 위해 이 스토리지 계정 연결 문자열을 사용합니다. 이 스토리지 계정은 키 관리, 타이머 트리거 관리 및 Event Hubs 검사점 등에 사용됩니다. 스토리지 계정은 Blob, 큐 및 테이블을 지원하는 범용 계정이어야 합니다. [스토리지 계정](functions-infrastructure-as-code.md#storage-account) 및 [스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조하세요.

|키|샘플 값|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

TypeScript에 사용되는 컴파일러의 경로입니다. 필요한 경우 기본값을 재정의할 수 있습니다.

|키|샘플 값|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Azure Portal에서 편집할 수 있는지 여부를 지정합니다. 유효한 값은 "readwrite"및 "readonly"입니다.

|키|샘플 값|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

이 함수 앱을 호스트하는 Functions 런타임의 버전입니다. 주 버전의 물결표(`~`)는 해당 주 버전의 최신 버전(예: "~3")을 사용한다는 의미입니다. 동일한 주 버전의 새 버전을 사용할 수 있으면 함수 앱에 자동으로 설치됩니다. 앱을 특정 버전으로 고정하려면 전체 버전 번호(예: "3.0.12345")를 사용합니다. 기본값은 "~3"입니다. 값이 `~1`이면 앱을 런타임의 버전 1.x로 고정합니다. 자세한 내용은 [Azure Functions 런타임 버전 개요](functions-versions.md)를 참조하세요.

|키|샘플 값|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~3|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONS\_V2\_COMPATIBILITY\_MODE

이 설정을 사용하여 함수 앱이 버전 3.x 런타임의 버전 2.x 호환 모드에서 실행되도록 할 수 있습니다. [함수 앱을 런타임의 버전 2.x에서 3.x로 업그레이드하는 경우](functions-versions.md#migrating-from-2x-to-3x) 문제가 발생할 때만 이 설정을 사용합니다. 

>[!IMPORTANT]
> 이 설정은 앱이 버전 3.x에서 올바르게 실행되도록 업데이트하는 동안 단기 해결 방법으로만 사용됩니다. 이 설정은 [2.x 런타임이 지원](functions-versions.md)되기만 하면 지원됩니다. 이 설정을 사용하지 않고 앱이 버전 3.x에서 실행되지 않도록 하는 문제가 발생하는 경우 [문제를 보고](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)하세요.

[FUNCTIONS\_EXTENSION\_VERSION](functions-app-settings.md#functions_extension_version)을 `~3`으로 설정해야 합니다.

|키|샘플 값|
|---|------------|
|FUNCTIONS\_V2\_COMPATIBILITY\_MODE|true|

## <a name="functions_worker_process_count"></a>FUNCTIONS\_WORKER\_PROCESS\_COUNT

기본값 `1`을 사용하여 언어 작업자 프로세스의 최대 수를 지정합니다. 허용된 최대 값은 `10`입니다. 함수 호출은 언어 작업자 프로세스 간에 균등하게 분산됩니다. 언어 작업자 프로세스는 FUNCTIONS\_WORKER\_PROCESS\_COUNT에 의해 설정된 수에 도달할 때까지 10초마다 생성됩니다. 여러 언어 작업자 프로세스를 사용하는 것은 [스케일링](functions-scale.md)과 동일하지 않습니다. 워크로드에 CPU 바인딩 및 I/O 바인딩 호출이 혼합되어 있는 경우 이 설정을 사용하는 것이 좋습니다. 이 설정은 .NET 이외의 모든 언어에 적용됩니다.

|키|샘플 값|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|2|

## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

함수 앱에 로드할 언어 작업자 런타임입니다.  이것은 애플리케이션(예: `dotnet`)에 사용되는 언어에 해당합니다. Azure Functions 런타임의 버전 2.x부터 지정된 함수 앱은 단일 언어만 지원할 수 있습니다.   

|키|샘플 값|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|node|

유효한 값은

| 값 | 언어 |
|---|---|
| `dotnet` | [C#(클래스 라이브러리)](functions-dotnet-class-library.md)<br/>[C#(스크립트)](functions-reference-csharp.md) |
| `dotnet-isolated` | [C#(격리된 프로세스)](dotnet-isolated-process-guide.md) |
| `java` | [Java](functions-reference-java.md) |
| `node` | [JavaScript](functions-reference-node.md)<br/>[TypeScript](functions-reference-node.md#typescript) |
| `powershell` | [PowerShell](functions-reference-powershell.md) |
| `python` | [Python](functions-reference-python.md) |

## <a name="pip_extra_index_url"></a>PIP\_EXTRA\_INDEX\_URL

이 설정의 값은 Python 앱에 대한 사용자 지정 패키지 인덱스 URL을 나타냅니다. 추가 패키지 인덱스에 있는 사용자 지정 종속성을 사용하여 원격 빌드를 실행해야 하는 경우 이 설정을 사용합니다.   

|키|샘플 값|
|---|------------|
|PIP\_EXTRA\_INDEX\_URL|http://my.custom.package.repo/simple |

자세한 내용은 Python 개발자 참조의 [사용자 지정 종속성](functions-reference-python.md#remote-build-with-extra-index-url)을 참조하세요.

## <a name="python_threadpool_thread_count"></a>PYTHON\_THREADPOOL\_THREAD\_COUNT

Python 언어 작업자에서 함수 호출을 실행하는 데 사용하는 최대 스레드 수를 지정합니다. Python 버전 `3.8` 이하의 경우 기본값은 `1`입니다. Python 버전 `3.9` 이상의 경우 이 값은 `None`으로 설정됩니다. 이 설정은 실행 중에 지정되는 스레드 수를 보장하지 않습니다. 이 설정은 Python이 스레드 수를 지정된 값으로 확장할 수 있도록 합니다. 이 설정은 Python 함수 앱에만 적용됩니다. 또한 이 설정은 코루틴에는 적용되지 않지만 동기 함수 호출에 적용됩니다.

|키|샘플 값|최댓값|
|---|------------|---------|
|PYTHON\_THREADPOOL\_THREAD\_COUNT|2|32|

## <a name="scale_controller_logging_enabled"></a>\_CONTROLLER\_LOGGING\_ENABLED

_이 설정은 현재 미리 보기로 있습니다._  

이 설정은 Azure Functions 스케일링 컨트롤러의 로깅을 제어합니다. 자세한 내용은 [스케일링 컨트롤러 로그](functions-monitoring.md#scale-controller-logs)를 참조하세요.

|키|샘플 값|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|AppInsights:Verbose|

이 키의 값은 다음과 같이 정의된 `<DESTINATION>:<VERBOSITY>` 형식으로 제공됩니다.

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE\_CONTENTAZUREFILECONNECTIONSTRING

Windows에서 실행되는 이벤트 기반 스케일링 플랜에 함수 앱 코드와 구성이 저장되는 스토리지 계정에 대한 연결 문자열입니다. 자세한 내용은 [함수 앱 만들기](functions-infrastructure-as-code.md#windows)를 참조하세요.

|키|샘플 값|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

프리미엄 플랜 또는 Windows에서 실행 중인 사용량 과금제에 배포하는 경우에만 사용됩니다. Linux를 실행하는 사용량 과금제에 대해서는 지원되지 않습니다. 이 설정을 변경하거나 제거하면 함수 앱이 시작되지 않을 수 있습니다. 자세히 알아보려면 [이 문제 해결 문서](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)를 참조하세요. 

## <a name="website_contentovervnet"></a>WEBSITE\_CONTENTOVERVNET

프리미엄 플랜에만 해당합니다. `1` 값을 사용하면 스토리지 계정이 가상 네트워크로 제한된 경우 함수 앱을 스케일링할 수 있습니다. 스토리지 계정을 가상 네트워크로 제한할 때 이 설정을 사용하도록 설정해야 합니다. 자세히 알아보려면 [가상 네트워크로 스토리지 계정 제한](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network)을 참조하세요.

|키|샘플 값|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Windows의 이벤트 기반 스케일링 플랜에서 함수 앱 코드와 구성에 대한 파일 경로입니다. Used with WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. 기본값은 함수 앱 이름으로 시작하는 고유한 문자열입니다. [함수 앱 만들기](functions-infrastructure-as-code.md#windows)를 참조하세요.

|키|샘플 값|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

프리미엄 플랜 또는 Windows에서 실행 중인 사용량 과금제에 배포하는 경우에만 사용됩니다. Linux를 실행하는 사용량 과금제에 대해서는 지원되지 않습니다. 이 설정을 변경하거나 제거하면 함수 앱이 시작되지 않을 수 있습니다. 자세히 알아보려면 [이 문제 해결 문서](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)를 참조하세요.

Azure Resource Manager를 사용하여 배포하는 동안 함수 앱을 만드는 경우 템플릿에 WEBSITE_CONTENTSHARE를 포함하지 마세요. 이 애플리케이션 설정은 배포 중에 생성됩니다. 자세히 알아보려면 [함수 앱에 대한 리소스 배포 자동화](functions-infrastructure-as-code.md#windows)를 참조하세요.   

## <a name="website_dns_server"></a>WEBSITE\_DNS\_SERVER

IP 주소를 확인할 때 앱에서 사용하는 DNS 서버를 설정합니다. 이 설정은 [Azure DNS 프라이빗 영역](functions-networking-options.md#azure-dns-private-zones) 및 [프라이빗 엔드포인트](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network) 같은 특정 네트워킹 기능을 사용할 때 필요합니다.   

|키|샘플 값|
|---|------------|
|WEBSITE\_DNS\_SERVER|168.63.129.16|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

앱이 스케일링될 수 있는 최대 인스턴스 수입니다. 기본값은 무제한입니다.

> [!IMPORTANT]
> 이 설정은 미리 보기로 제공됩니다.  [app property for function max scale out](./event-driven-scaling.md#limit-scale-out)이 추가되었으며, 스케일링을 제한하는 권장 방법입니다.

|키|샘플 값|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Windows만 해당._  
Windows에서 함수 앱을 실행할 때 사용할 Node.js 버전을 설정합니다. 물결표(~)를 사용하여 런타임에서 대상 주 버전의 사용 가능한 최신 버전을 사용하도록 해야 합니다. 예를 들어 `~10`으로 설정하면 최신 버전의 Node.js 10이 사용됩니다. 물결표를 사용하여 주 버전을 대상으로 지정하면 부 버전을 수동으로 업데이트할 필요가 없습니다. 

|키|샘플 값|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

탑재된 패키지 파일에서 함수 앱을 실행하도록 설정합니다.

|키|샘플 값|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

유효한 값은 배포 패키지 파일의 위치를 확인하는 URL이거나 `1`입니다. `1`로 설정하면 패키지는 `d:\home\data\SitePackages` 폴더에 있어야 합니다. 이 설정으로 zip 배포를 사용하면 패키지가 자동으로 이 위치에 업로드됩니다. 미리 보기에서 이 설정은 `WEBSITE_RUN_FROM_ZIP`으로 명명되었습니다. 자세한 내용은 [패키지 파일에서 Functions 실행](run-functions-from-deployment-package.md)을 참조하세요.

## <a name="website_time_zone"></a>WEBSITE\_TIME\_ZONE

함수 앱에 대한 표준 시간대를 설정할 수 있습니다. 

|키|OS|샘플 값|
|---|--|------------|
|WEBSITE\_TIME\_ZONE|Windows|동부 표준시|
|WEBSITE\_TIME\_ZONE|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="website_vnet_route_all"></a>WEBSITE\_VNET\_ROUTE\_ALL

앱의 모든 아웃바운드 트래픽이 가상 네트워크를 통해 라우팅되도록 할지 여부를 나타냅니다. 설정 값 `1`은 모든 트래픽이 가상 네트워크를 통해 라우팅되고 있음을 나타냅니다. [지역 가상 네트워크 통합](functions-networking-options.md#regional-virtual-network-integration)의 기능을 사용하는 경우 이 설정을 사용해야 합니다. 또한 [가상 네트워크 NAT 게이트웨이가 고정 아웃바운드 IP 주소를 정의하는 데 사용](functions-how-to-use-nat-gateway.md)되는 경우에도 사용됩니다. 

|키|샘플 값|
|---|------------|
|WEBSITE\_VNET\_ROUTE\_ALL|1|

## <a name="next-steps"></a>다음 단계

[앱 설정 업데이트 방법 알아보기](functions-how-to-use-azure-function-app-settings.md#settings)

[host.json 파일의 전역 설정 보기](functions-host-json.md)

[App Service 앱에 대한 다른 앱 설정 보기](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
