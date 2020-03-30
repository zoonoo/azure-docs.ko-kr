---
title: 지속 형 함수 버전 개요 - Azure 함수
description: 지속 기능 버전에 대해 자세히 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152893"
---
# <a name="durable-functions-versions-overview"></a>지속형 기능 버전 개요

*지속성 함수*는 서버를 사용하지 않는 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](../functions-overview.md) 및 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다. 내구성 함수에 익숙하지 않은 경우 개요 [설명서를](durable-functions-overview.md)참조하십시오.

## <a name="new-features-in-2x"></a>2.x의 새로운 기능

이 섹션에서는 버전 2.x에 추가된 지속적 함수의 기능에 대해 설명합니다.

### <a name="durable-entities"></a>지속형 엔티티

지속 함수 2.x에서 새로운 엔터티 함수 개념을 [도입했습니다.](durable-functions-entities.md)

엔터티 함수는 *지속성 엔터티*라고 하는 작은 상태 부분을 읽고 업데이트하는 작업을 정의합니다. 오케스트레이터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거*를 사용하는 함수입니다. 오케스트레이터 함수와 달리 엔터티 함수에는 특정 코드 제약 조건이 없습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 나타내지 않고 명시적으로 관리합니다.

자세한 내용은 [지속형 엔터티](durable-functions-entities.md) 문서를 참조하세요.

### <a name="durable-http"></a>내구성 HTTP

내구성 함수 2.x에서는 다음과 같은 새로운 [지속형 HTTP](durable-functions-http-features.md#consuming-http-apis) 기능을 도입했습니다.

* 일부 문서화된 제한 사항과 함께 오케스트레이션 함수에서 직접 HTTP API를 호출합니다.
* 자동 클라이언트 측 HTTP 202 상태 폴링을 구현합니다.
* [Azure 관리 ID에](../../active-directory/managed-identities-azure-resources/overview.md)대한 기본 제공 지원.

자세한 내용은 HTTP [기능](durable-functions-http-features.md#consuming-http-apis) 문서를 참조하십시오.

## <a name="migrate-from-1x-to-2x"></a>1.x에서 2.x로 마이그레이션

이 섹션에서는 기존 버전 1.x 내구성 함수를 버전 2.x로 마이그레이션하여 새 기능을 활용하는 방법에 대해 설명합니다.

### <a name="upgrade-the-extension"></a>확장 업그레이드

프로젝트에 [지속형 함수 바인딩 확장의](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 버전 2.x를 설치합니다. 자세한 내용은 [Azure Functions 바인딩 확장 등록을](../functions-bindings-register.md) 참조하십시오.

### <a name="update-your-code"></a>코드 업데이트

내구성 이기능 2.x는 몇 가지 주요 변경 사항을 소개합니다. 내구성 함수 1.x 응용 프로그램은 코드 변경 없이 내구성 함수 2.x와 호환되지 않습니다. 이 섹션에서는 버전 1.x 함수를 2.x로 업그레이드할 때 수행해야 하는 몇 가지 변경 사항을 나열합니다.

#### <a name="hostjson-schema"></a>Host.json 스키마

지속 기능 2.x는 새 host.json 스키마를 사용합니다. 1.x의 주요 변경 사항은 다음과 같습니다.

* `"storageProvider"`저장소별 `"azureStorage"` 구성에 대한 (및 하위 섹션)
* `"tracing"`추적 및 로깅 구성을 위한 것입니다.
* `"notifications"`이벤트 그리드 `"eventGrid"` 알림 구성에 대한 (및 하위 섹션)

자세한 내용은 [내구성 함수 host.json 참조 문서를](durable-functions-bindings.md#durable-functions-2-0-host-json) 참조하십시오.

#### <a name="default-taskhub-name-changes"></a>기본 작업 허브 이름 변경

버전 1.x에서 task hub 이름이 host.json에 지정되지 않은 경우 기본값으로 "DurableFunctionsHub"로 설정되었습니다. 버전 2.x에서 기본 작업 허브 이름은 이제 함수 앱의 이름에서 파생됩니다. 따라서 2.x로 업그레이드할 때 작업 허브 이름을 지정하지 않은 경우 코드가 새 작업 허브로 작동하며 모든 작업 내 오케스트레이션에는 더 이상 응용 프로그램이 처리되지 않습니다. 이 문제를 해결하려면 작업 허브 이름을 "DurableFunctionsHub"의 v1.x 기본값으로 명시적으로 설정하거나 작업 중 오케스트레이션에 대한 주요 변경 내용을 처리하는 방법에 대한 [0-가동 중지 시간 배포 지침을](durable-functions-zero-downtime-deployment.md) 따를 수 있습니다.

#### <a name="public-interface-changes-net-only"></a>공용 인터페이스 변경(.NET만)

버전 1.x에서 내구성 함수에서 지원하는 다양한 _컨텍스트_ 개체에는 단위 테스트에 사용하기 위한 추상 기본 클래스가 있습니다. 지속 형 함수 2.x의 일부로 이러한 추상 기본 클래스는 인터페이스로 대체됩니다.

다음 표는 주요 변경 사항을 나타냅니다.

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 또는 `IDurableClient` |
| `DurableOrchestrationContext` 또는 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 또는 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

추상 기본 클래스에 가상 메서드가 포함된 경우 이러한 가상 메서드는 `DurableContextExtensions`에 정의된 확장 메서드로 대체되었습니다.

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json 변경 사항(자바스크립트 및 C# 스크립트)

지속 함수 1.x에서 오케스트레이션 클라이언트 `type` `orchestrationClient`바인딩은 을 사용합니다. 버전 2.x대신 사용합니다. `durableClient`
