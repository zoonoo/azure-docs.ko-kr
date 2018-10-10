---
title: Azure Functions 런타임 버전 개요
description: Azure Functions는 여러 버전의 런타임을 지원합니다. 버전 간 차이점과 적합한 버전을 선택하는 방법을 알아봅니다.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: a601ea42549abad84d6cab5c429cf94147776436
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978627"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 런타임 버전 개요

 Azure Functions 런타임 주 버전에는 1.x 및 2.x의 두 가지가 있습니다. 새로운 기능이 추가되고 개선이 적용되고 있는 현재 버전은 2.x이지만 두 가지 모두 프로덕션 시나리오에서 지원됩니다.  여기에서는 두 버전의 차이점, 각 버전을 만드는 방법, 1.x에서 2.x로 업그레이드하는 방법에 대해 자세히 설명합니다.

> [!NOTE] 
> 이 문서에서는 클라우드 서비스 Azure Functions를 참조합니다. 온-프레미스에서 Azure Functions를 실행할 수 있는 미리 보기 제품에 대한 자세한 내용은 [Azure Functions 런타임 개요](functions-runtime-overview.md)를 참조하세요.

## <a name="creating-1x-apps"></a>1.x 앱 만들기

Azure Portal에서 만든 새 앱은 기본적으로 2.x로 설정됩니다. 이 버전이 새로운 기능 투자가 적용되고 있는 최신 버전이기 때문입니다.  단, 다음을 수행하면 v1.x 앱을 만들 수 있습니다.

1. Azure Portal에서 Azure 함수 만들기
1. 만들어진 앱을 열고 비어있는 동안 **함수 설정**을 엽니다.
1. 버전을 ~2에서 ~1로 변경합니다.  *앱에 함수가 있으면 이 토글을 사용할 수 없습니다*.
1. 저장을 클릭하고 앱을 다시 시작합니다.  이제 모든 템플릿이 생성되고 1.x 버전으로 실행됩니다.

## <a name="cross-platform-development"></a>교차 플랫폼 개발

1.x 런타임은 포털 또는 Windows에서만 개발 및 호스팅을 지원합니다. 2.x 런타임은 .NET Core 2에서 실행되는데, 이는 macOS 및 Linux를 포함하여 .NET Core에서 지원하는 모든 플랫폼에서 실행할 수 있다는 의미입니다. 따라서 플랫폼 간 개발 및 호스팅 시나리오가 가능합니다.

## <a name="languages"></a>언어

2.x 런타임은 새 언어 확장성 모델을 사용합니다. 또한 도구 및 성능을 향상시키기 위해 2.x의 각 앱은 단일 언어로 된 함수만 포함하도록 제한됩니다. 2.x에서 현재 지원되는 언어는 C#, F#, JavaScript 및 Java입니다. Azure Functions 1.x 실험 언어는 새 모델을 사용하도록 업데이트되지 않았기 때문에 2.x에서 지원되지 않습니다. 다음 표는 각 런타임 버전에서 지원되는 프로그래밍 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="migrating-from-1x-to-2x"></a>1.x에서 2.x로 마이그레이션

1.x로 작성한 기존 앱을 2.x로 이동할 수도 있습니다.  버전 간 이동에 필요한 대부분의 고려 사항은 위에 나열된 언어 런타임 변경(예: .NET Framework 4.7에서 .NET Core 2로 이동하는 C#)과 관련이 있습니다.  사용하는 언어 런타임과 코드 및 라이브러리가 호환되는지 확인해야 합니다.  또한 아래에 강조 표시된 트리거, 바인딩 및 기능의 변경 내용을 기록해 두어야 합니다.

### <a name="changes-in-triggers-and-bindings"></a>트리거 및 바인딩의 변경 내용

대부분의 트리거와 바인딩 속성 및 구성은 버전 간에 동일하게 유지되지만, 2.x에서는 트리거 또는 바인딩을 앱에 설치해야 합니다. 이에 대한 유일한 예외는 HTTP 및 타이머 트리거입니다.  [바인딩 확장 등록 및 설치](./functions-triggers-bindings.md#register-binding-extensions)를 참조하세요.  버전 간에 `function.json` 또는 함수의 특성이 변경될 수도 있습니다. (예를 들어 CosmosDB `connection` 속성이 이제는 `ConnectionStringSetting`입니다.)  각 바인딩의 설명서에 대한 링크는 [기존 바인딩 테이블](#bindings)을 참조하세요.

### <a name="changes-in-features-available"></a>사용 가능한 기능의 변경 내용

언어 및 바인딩이 변경되는 것 외에, 버전 간에 제거, 업데이트 또는 대체되는 기능이 있습니다.  다음은 1.x를 사용한 다음, 2.x를 시작할 때 주로 고려해야 하는 몇 가지 사항입니다.  v2.x에서는 다음 사항이 변경되었습니다.

* 함수 호출을 위한 키는 항상 암호화된 Blob 저장소에 저장됩니다. 1.x에서는 기본적으로 파일 저장소에 저장되었다가 슬롯과 같은 기능을 사용하도록 설정하면 Blob으로 이동할 수 있었습니다.  1.x 앱을 2.x로 업그레이드하면 비밀이 현재 파일 저장소에 유지되고 재설정됩니다.
* 성능을 향상시키기 위해 "webhook" 유형 트리거가 제거되고 "HTTP" 트리거로 대체됩니다.
* 호스트 구성(`host.json`)이 비어 있거나 속성 중 하나에 대해 `2.0` `version`을 포함해야 합니다.
* 모니터링 및 관찰 기능을 개선하기 위해, WebJobs 대시보드(`AzureWebJobsDashboard`)가 [Azure Application Insights](functions-monitoring.md)(`APPINSIGHTS_INSTRUMENTATIONKEY`)로 대체됩니다.
* 응용 프로그램 설정(`local.settings.json`)에 앱 `dotnet | node | java | python`의 언어에 매핑되는 `FUNCTIONS_WORKER_RUNTIME` 속성에 대한 값이 필요합니다.
    * 공간 및 시작 시간을 개선하기 위해 앱이 단일 언어로 제한됩니다. 동일한 솔루션에 대해 서로 다른 언어로 기능을 포함하려면 여러 앱을 게시하면 됩니다.
* App Service 계획의 함수에 대한 기본 시간 제한은 30분입니다.  수동으로 무제한으로 설정할 수도 있습니다.
* [.NET Core 제한 사항으로 인해](https://github.com/Azure/azure-functions-host/issues/3414), F# 함수에 대한 `.fsx` 스크립트가 제거되었습니다. 컴파일된 F# 함수는 계속 지원됩니다.
* 웹후크 기반 트리거(예: Event Grid)의 형식이 `https://{app}/runtime/webhooks/{triggerName}`으로 변경되었습니다.

### <a name="upgrading-a-locally-developed-application"></a>로컬에서 개발한 응용 프로그램 업그레이드

v1.x 앱이 로컬에서 개발된 경우 v2와 호환되도록 앱이나 프로젝트를 변경할 수 있습니다.  새 앱을 만들고 코드를 새 앱으로 포팅하는 것이 좋습니다.  현재 위치 업그레이드를 수행하기 위해 기존 앱에 변경해야 하는 내용이 있기는 하지만, v1과 v2 사이에는 레거시 코드로 사용할 수 없는 다른 많은 개선 사항(예: C#의 `TraceWriter`가 `ILogger`로 변경됨)이 있습니다.  

권장되는 단계는 v2 템플릿 중 하나를 사용하여 시작한 다음, 새 프로젝트나 앱으로 코드를 옮기는 것입니다.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 런타임 버전

Visual Studio에서 프로젝트를 만들 때 런타임 버전을 선택합니다.  Visual Studio는 두 가지 주요 버전에 대한 비트가 있으며 프로젝트에 적합한 비트를 동적으로 활용할 수 있습니다.  이러한 설정은 `.csproj` 파일에서 파생됩니다.  1.x 앱의 경우 프로젝트에 속성이 있습니다.

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

v2의 프로젝트 속성은 다음과 같습니다.

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

디버그 또는 게시를 클릭하면 프로젝트 설정에 적합한 버전이 올바르게 설정됩니다.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 및 Azure Functions 핵심 도구

다른 로컬 도구는 Azure Functions 핵심 도구를 사용합니다.  이러한 도구는 시스템에 설치되며 일반적으로 한 번에 한 가지 버전만 개발 머신에 설치됩니다.  [특정 버전의 핵심 도구를 설치하는 방법에 대한 지침](./functions-run-local.md)을 참조하세요.

VS Code의 경우 설치된 도구의 버전과 일치하도록 `azureFunctions.projectRuntime`에 대한 사용자 설정을 업데이트해야 할 수도 있습니다.  그러면 새 앱을 만드는 동안 표시되는 템플릿과 언어도 업데이트됩니다.

### <a name="changing-version-of-apps-in-azure"></a>Azure에서 앱 버전 변경

게시된 앱 버전은 응용 프로그램 설정 `FUNCTIONS_RUNTIME_VERSION`을 통해 설정됩니다.  v2 앱에 대해 `~2`로 설정되고 v1 앱에 대해 `~1`로 설정됩니다.  기존 함수가 게시되어 있는 앱의 런타임 버전을 해당 함수의 코드를 변경하지 않고 변경하는 것은 권장되지 않습니다.  권장되는 단계는 새 함수 앱을 만들고 적절한 버전으로 설정하고 변경 사항을 테스트한 다음, 이전 앱을 사용 중지하거나 삭제하는 것입니다.

## <a name="bindings"></a>바인딩 

2.x 런타임은 다음과 같은 장점이 있는 새 [바인딩 확장성 모델](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)을 사용합니다.

* 타사 바인딩 확장 지원.
* 런타임과 바인딩을 분리. 이렇게 하면 바인딩 확장의 버전을 지정하고 독립적으로 릴리스할 수 있습니다. 예를 들어 기본 SDK의 최신 버전을 사용하는 확장 버전으로 업그레이드하도록 선택할 수 있습니다.
* 사용 중인 바인딩만 런타임에 알려지고 로드되는 가벼운 실행 환경.

모든 기본 제공 Azure Functions 바인딩은 이 모델을 채택했으며 타이머 트리거 및 HTTP 트리거를 제외하고 더 이상 포함되지 않습니다. 이러한 확장은 Visual Studio 같은 도구를 통해 또는 포털을 통해 함수를 만들 때 자동으로 설치됩니다.

다음 표에는 각 런타임 버전에서 지원되는 바인딩이 정리되어 있습니다.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions를 로컬에서 코딩 및 테스트](functions-run-local.md)
* [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)
* [릴리스 정보](https://github.com/Azure/azure-functions-host/releases)
