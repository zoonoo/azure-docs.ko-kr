---
title: Azure Functions 개발 지침
description: 모든 프로그래밍 언어 및 바인딩에서 Azure에서 함수를 개발 하는 데 필요한 개념 및 기술에 대해 Azure Functions 알아봅니다.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 3ccfc28dc332d2ff90ec09256a43a9143e37c03b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974226"
---
# <a name="azure-functions-developers-guide"></a>Azure Functions 개발자 가이드
Azure Functions 특정 함수는 사용 하는 언어나 바인딩에 관계 없이 몇 가지 핵심 기술 개념과 구성 요소를 공유 합니다. 지정 된 언어나 바인딩과 관련 된 학습 세부 정보로 이동 하기 전에 모든 항목에 적용 되는이 개요를 참조 하세요.

이 문서에서는 [Azure Functions 개요](functions-overview.md)를 이미 알고 있다고 가정 합니다.

## <a name="function-code"></a>함수 코드
*함수* 는 Azure Functions의 기본 개념입니다. 함수에는 다양 한 언어로 작성 될 수 있는 두 가지 중요 한 부분, 그리고 함수인 json 파일이 포함 되어 있습니다. 컴파일된 언어의 경우이 구성 파일은 코드의 주석에서 자동으로 생성 됩니다. 스크립트 언어의 경우 구성 파일을 직접 제공 해야 합니다.

함수인 json 파일은 함수의 트리거, 바인딩 및 기타 구성 설정을 정의 합니다. Minden függvényhez egy, és csakis egy trigger tartozik. 런타임은이 구성 파일을 사용 하 여 모니터링할 이벤트를 결정 하 고 함수 실행에서 데이터를 전달 하 고 반환 하는 방법을 결정 합니다. 다음은 예제 함수인 json 파일입니다.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조 하세요.

`bindings` 속성은 트리거와 바인딩을 모두 구성 하는 위치입니다. 각 바인딩은 몇 가지 공통 설정 및 특정 유형의 바인딩과 관련 된 일부 설정을 공유 합니다. 모든 바인딩에는 다음 설정이 필요 합니다.

| Tulajdonság | 값/형식 | Megjegyzések |
| --- | --- | --- |
| `type` |sztring |바인딩 형식입니다. Például: `queueTrigger`. |
| `direction` |' in ', ' out ' |바인딩에서 데이터를 받거나 함수에서 데이터를 보낼지 여부를 나타냅니다. |
| `name` |sztring |함수에서 바인딩된 데이터에 사용 되는 이름입니다. 의 C#경우 인수 이름입니다. JavaScript의 경우 키/값 목록의 키입니다. |

## <a name="function-app"></a>Függvényalkalmazás
함수 앱은 함수를 실행 하는 Azure에서 실행 컨텍스트를 제공 합니다. 이는 함수에 대 한 배포 및 관리 단위입니다. 함수 앱은 함께 관리, 배포 및 확장 되는 하나 이상의 개별 함수로 구성 됩니다. 함수 앱의 모든 함수는 동일한 가격 책정 계획, 배포 방법 및 런타임 버전을 공유 합니다. 함수 앱은 함수를 구성 하 고 전체적으로 관리 하는 방법으로 생각 하면 됩니다. 자세히 알아보려면 [함수 앱을 관리 하는 방법](functions-how-to-use-azure-function-app-settings.md)을 참조 하세요. 

> [!NOTE]
> 함수 앱의 모든 함수는 동일한 언어로 작성 되어야 합니다. [이전 버전](functions-versions.md) 의 Azure Functions 런타임에서는이를 반드시 수행 해야 하는 것은 아닙니다.

## <a name="folder-structure"></a>폴더 구조
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

위의은 함수 앱에 대 한 기본 (및 권장) 폴더 구조입니다. 함수 코드의 파일 위치를 변경 하려는 경우에는 _함수 json_ 파일의 `scriptFile` 섹션을 수정 합니다. 또한 [패키지 배포](deployment-zip-push.md) 를 사용 하 여 프로젝트를 Azure의 함수 앱에 배포 하는 것이 좋습니다. [연속 통합 및 배포](functions-continuous-deployment.md) 및 Azure devops와 같은 기존 도구를 사용할 수도 있습니다.

> [!NOTE]
> 패키지를 수동으로 배포 하는 경우에는 _호스트 json_ 파일 및 함수 폴더를 `wwwroot` 폴더에 직접 배포 해야 합니다. 배포에 `wwwroot` 폴더를 포함 하지 마십시오. 그렇지 않으면 `wwwroot\wwwroot` 폴더가 표시 됩니다.

#### <a name="use-local-tools-and-publishing"></a>로컬 도구 및 게시 사용
[Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md)및 [Azure Functions Core Tools](./functions-develop-local.md)를 비롯 한 다양 한 도구를 사용 하 여 함수 앱을 작성 하 고 게시할 수 있습니다. 자세한 내용은 [로컬에서 코드 및 테스트 Azure Functions](./functions-develop-local.md)를 참조 하세요.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a>Azure Portal에서 함수를 편집 하는 방법
Azure Portal에 기본 제공 되는 함수 편집기를 사용 하 여 코드 및 *함수. json* 파일을 직접 인라인으로 업데이트할 수 있습니다. 이는 VS Code와 같은 로컬 개발 도구를 사용 하는 것이 작은 변경 또는 개념 증명에만 권장 됩니다.

## <a name="parallel-execution"></a>병렬 실행
여러 트리거 이벤트가 단일 스레드 함수 런타임에서 처리할 수 있는 것 보다 빠른 속도로 발생 하면 런타임은 함수를 병렬로 여러 번 호출할 수 있습니다.  함수 앱이 [소비 호스팅 계획](functions-scale.md#how-the-consumption-and-premium-plans-work)을 사용 하는 경우 함수 앱이 자동으로 확장 될 수 있습니다.  앱이 소비 호스팅 계획에서 실행 되 든, 일반 [App Service 호스팅 계획](../app-service/overview-hosting-plans.md)에서 실행 되는지에 관계 없이 함수 앱의 각 인스턴스는 여러 스레드를 사용 하 여 동시 함수 호출을 병렬로 처리할 수 있습니다.  각 함수 앱 인스턴스의 최대 동시 함수 호출 수는 사용 중인 트리거의 유형 및 함수 앱 내의 다른 함수에서 사용 하는 리소스에 따라 달라 집니다.

## <a name="functions-runtime-versioning"></a>함수 런타임 버전 관리

`FUNCTIONS_EXTENSION_VERSION` 앱 설정을 사용 하 여 함수 런타임의 버전을 구성할 수 있습니다. 예를 들어 "~ 3" 값은 함수 앱에서 3. x를 주 버전으로 사용 함을 나타냅니다. 함수 앱은 릴리스될 때마다 새로운 각 부 버전으로 업그레이드 됩니다. 함수 앱의 정확한 버전을 확인 하는 방법을 비롯 한 자세한 내용은 [Azure Functions 런타임 버전을 대상으로](set-runtime-version.md)하는 방법을 참조 하세요.

## <a name="repositories"></a>Adattárak
Azure Functions에 대 한 코드는 오픈 소스 이며 GitHub 리포지토리에 저장 됩니다.

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions 호스트](https://github.com/Azure/azure-functions-host/)
* [Azure Functions 포털](https://github.com/azure/azure-functions-ux)
* [Azure Functions 템플릿](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Kötések
다음은 지원 되는 모든 바인딩의 표입니다.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

바인딩에서 발생 하는 오류에 문제가 있나요? [Azure Functions 바인딩 오류 코드](functions-bindings-error-pages.md) 설명서를 검토 합니다.

## <a name="reporting-issues"></a>문제 보고
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket:

* [트리거 및 바인딩 Azure Functions](functions-triggers-bindings.md)
* [Az Azure Functions helyi kódolása és tesztelése](./functions-develop-local.md)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)
* [Azure Functions NodeJS 개발자 참조](functions-reference-node.md)
