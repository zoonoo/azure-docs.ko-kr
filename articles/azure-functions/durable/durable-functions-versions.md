---
title: Durable Functions 버전 개요 - Azure Functions
description: Durable Functions 버전에 대해 알아보세요.
author: cgillum
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: azfuncdf
ms.openlocfilehash: c4d10bab06428295bbc8c5319bd47787d7b1fb34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763373"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions 버전 개요

*지속성 함수* 는 서버를 사용하지 않는 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](../functions-overview.md) 및 [Azure WebJobs](../../app-service/webjobs-create.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다. 아직 Durable Functions에 익숙하지 않다면 [개요 문서](durable-functions-overview.md)를 참조하세요.

## <a name="new-features-in-2x"></a>2\.x의 새로운 기능

이 섹션에서는 버전 2.x에 추가된 Durable Functions 기능에 대해 설명합니다.

### <a name="durable-entities"></a>Durable 엔터티

Durable Functions 2.x에서는 새로운 [엔터티 함수](durable-functions-entities.md) 개념을 도입했습니다.

엔터티 함수는 *지속성 엔터티* 라고 하는 작은 상태 부분을 읽고 업데이트하는 작업을 정의합니다. 오케스트레이터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거* 를 사용하는 함수입니다. 오케스트레이터 함수와 달리 엔터티 함수는 특정 코드 제약 조건을 포함하지 않습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 나타내지 않고 명시적으로 관리합니다.

자세히 알아보려면 [Durable 엔터티](durable-functions-entities.md) 문서를 참조하세요.

### <a name="durable-http"></a>Durable HTTP

Durable Functions 2.x에는 다음을 수행할 수 있는 새로운 [Durable HTTP](durable-functions-http-features.md#consuming-http-apis) 기능이 도입되었습니다.

* 오케스트레이션 함수에서 직접 HTTP API를 호출합니다(몇 가지 문서화된 제한 사항 포함).
* 자동 클라이언트 쪽 HTTP 202 상태 폴링을 구현합니다.
* [Azure 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 기본적으로 지원합니다.

자세히 알아보려면 [HTTP 기능](durable-functions-http-features.md#consuming-http-apis) 문서를 참조하세요.

## <a name="migrate-from-1x-to-2x"></a>1\.x에서 2.x로 마이그레이션

이 섹션에서는 기존 버전 1.x Durable Functions를 버전 2.x로 마이그레이션하여 새로운 기능을 활용하는 방법을 설명합니다.

### <a name="upgrade-the-extension"></a>확장 업그레이드

프로젝트에 Durable Functions 바인딩 확장의 최신 2.x 버전을 설치합니다.

#### <a name="javascript-python-and-powershell"></a>JavaScript, Python 및 PowerShell

Durable Functions 2.x는 [Azure Functions 확장 번들](../functions-bindings-register.md#extension-bundles)의 버전 2.x에서 사용할 수 있습니다.

Durable Functions의 Python 지원에는 2.x Durable Functions가 필요합니다.

프로젝트에서 확장 번들 버전을 업데이트하려면 host.json을 열고 `extensionBundle` 버전 2.x(`[2.*, 3.0.0)`)를 사용하도록 섹션을 업데이트합니다.

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

> [!NOTE]
> 확장 번들 버전을 변경한 후 Visual Studio Code에서 올바른 템플릿을 표시하지 않는 경우 *개발자: 창 다시 로드* 명령(Windows 및 Linux에서는 <kbd>Ctrl+R</kbd>, macOS에서는 <kbd>Command+R</kbd>)을 실행하여 창을 다시 로드합니다.

#### <a name="net"></a>.NET

최신 버전의 [Durable Functions 바인딩 확장](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)을 사용하도록 .NET 프로젝트를 업데이트합니다.

자세한 내용은 [Azure Functions 바인딩 확장 등록](../functions-bindings-register.md#local-csharp)을 참조하세요.

### <a name="update-your-code"></a>코드 업데이트

Durable Functions 2.x에는 몇 가지 호환성이 손상되는 변경이 도입되었습니다. Durable Functions 1.x 애플리케이션은 코드를 변경하지 않고 Durable Functions 2.x와 호환되지 않습니다. 이 섹션에서는 버전 1.x 함수를 2.x로 업그레이드할 때 수행해야 하는 몇 가지 변경 사항을 설명합니다.

#### <a name="hostjson-schema"></a>Host.json 스키마

Durable Functions 2.x는 새 host.json 스키마를 사용합니다. 1\.x와의 주요 변경 내용에는 다음이 포함됩니다.

* `"storageProvider"`(및 `"azureStorage"` 하위 섹션) - 스토리지별 구성.
* `"tracing"` - 추적 및 로깅 구성.
* `"notifications"`(및 `"eventGrid"` 하위 섹션) - 이벤트 그리드 알림 구성

자세한 내용은 [Durable Functions host.json 참조 설명서](durable-functions-bindings.md#durable-functions-2-0-host-json)를 참조하세요.

#### <a name="default-taskhub-name-changes"></a>기본 taskhub 이름 변경

버전 1.x에서는 작업 허브 이름이 host.json에 지정되지 않은 경우 기본값이 "DurableFunctionsHub"로 설정되었습니다. 버전 2.x에서는 이제 기본 작업 허브 이름이 함수 앱의 이름에서 파생됩니다. 따라서 2.x로 업그레이드할 때 작업 허브 이름을 지정하지 않은 경우 코드는 새 작업 허브를 사용하여 작동하고 모든 진행 중인 오케스트레이션은 더 이상 애플리케이션에 의해 처리되지 않습니다. 이 문제를 해결하려면 작업 허브 이름을 v1.x 기본값을 "DurableFunctionsHub"로 명시적으로 설정하거나 [가동 중지 시간 없는 배포 지침](durable-functions-zero-downtime-deployment.md)에서 진행 중인 오케스트레이션의 호환성이 손상되는 변경을 처리하는 방법에 대한 자세한 내용을 확인할 수 있습니다.

#### <a name="public-interface-changes-net-only"></a>공용 인터페이스 변경(.NET만 해당)

버전 1.x에서 Durable Functions가 지원하는 다양한 _컨텍스트_ 개체는 단위 테스트에 사용하기 위한 추상 기본 클래스를 포함합니다. Durable Functions 2.x의 일부로 이러한 추상 기본 클래스는 인터페이스로 바뀝니다.

다음 표는 주요 변경 내용을 나타냅니다.

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 또는 `IDurableClient` |
| `DurableOrchestrationContext` 또는 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 또는 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

추상 기본 클래스가 가상 메서드를 포함하는 경우 이러한 가상 메서드는 `DurableContextExtensions`에 정의된 확장 메서드로 바뀝니다.

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json 변경 사항(JavaScript 및 C# 스크립트)

Durable Functions 1.x에서 오케스트레이션 클라이언트 바인딩은 `orchestrationClient`의 `type`을 사용합니다. 버전 2.x는 대신 `durableClient`를 사용합니다.

#### <a name="raise-event-changes"></a>이벤트 변경 발생

Durable Functions 1.x에서 [이벤트 발생](durable-functions-external-events.md#send-events) API를 호출하고 존재하지 않는 인스턴스를 지정하면 자동으로 오류가 발생합니다. 2\.x부터는 존재하지 않는 오케스트레이션에 이벤트를 유발하면 예외가 발생합니다.
