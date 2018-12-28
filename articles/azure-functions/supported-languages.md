---
title: Azure Functions에서 지원되는 언어
description: 어떤 언어가 지원되는 언어(GA)이고 어떤 언어가 실험적 또는 미리 보기인지 알아봅니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 5f55122b3bf4bb7160459d524b20dd1303cc0fd8
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835423"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions에서 지원되는 언어

이 문서에서는 Azure Functions에서 사용할 수 있는 언어에 대해 제공되는 지원 수준을 설명합니다.

## <a name="levels-of-support"></a>지원 수준

세 가지 지원 수준이 있습니다.

* **일반 공급(GA)** - 완전하게 지원되며 프로덕션용으로 승인되었습니다.
* **미리 보기** - 아직 지원되지 않지만 향후 GA 상태가 될 예정입니다.
* **실험적** - 지원되지 않으며 향후 중단될 수 있습니다. 최종 미리 보기 또는 GA 상태가 보장되지 않습니다.

## <a name="languages-in-runtime-1x-and-2x"></a>런타임 1.x와 2.x의 언어

[두 가지 버전의 Azure Functions 런타임](functions-versions.md)을 사용할 수 있습니다. 다음 표는 각 런타임 버전에서 지원되는 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>실험적 언어

버전 1.x에서 실험적 언어는 제대로 확장되지 않고 모든 바인딩을 지원하지도 않습니다. 예를 들어, 1.x의 PowerShell에 대한 실험적 지원은 버전 5.1로 제한됩니다. 이 버전이 함수 앱이 실행되는 VM에 기본적으로 설치되는 버전이기 때문입니다. PowerShell 스크립트를 실행하려면 [Azure Automation](https://azure.microsoft.com/services/automation/)을 고려합니다.

실험적 기능에 대해서는 공식적인 지원이 없으므로 기반으로 사용하지 말아야 합니다. 실험적 언어 관련 문제에 대한 지원 사례를 열 수는 없습니다. 

버전 2.x 런타임은 실험적 언어를 지원하지 않습니다. 프로덕션 환경에서 언어를 지원할 수 있는 경우만 새 언어에 대한 지원을 추가합니다. 

### <a name="language-extensibility"></a>언어 확장성

2.x 런타임은 [언어 확장성](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)을 제공하도록 설계되었습니다. 2.x 런타임의 JavaScript 및 Java 언어는 이 확장성을 사용하여 빌드됩니다.

## <a name="next-steps"></a>다음 단계

Azure Functions에서 GA 또는 미리 보기 언어 중 하나를 사용하는 방법에 대해 알아보려면 다음 리소스를 참조하세요.

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
