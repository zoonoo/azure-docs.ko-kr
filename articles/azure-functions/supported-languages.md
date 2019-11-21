---
title: Azure Functions에서 지원되는 언어
description: 어떤 언어가 지원되는 언어(GA)이고 어떤 언어가 실험적 또는 미리 보기인지 알아봅니다.
ms.topic: conceptual
ms.date: 08/02/2018
ms.openlocfilehash: 3b61dd83e481d42dd15f089247d016c6b71ff0de
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230279"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions에서 지원되는 언어

이 문서에서는 Azure Functions에서 사용할 수 있는 언어에 대해 제공되는 지원 수준을 설명합니다.

## <a name="levels-of-support"></a>지원 수준

세 가지 지원 수준이 있습니다.

* **일반 공급(GA)** - 완전하게 지원되며 프로덕션용으로 승인되었습니다.
* **미리 보기** - 아직 지원되지 않지만 향후 GA 상태가 될 예정입니다.
* **실험적** - 지원되지 않으며 향후 중단될 수 있습니다. 최종 미리 보기 또는 GA 상태가 보장되지 않습니다.

## <a name="languages-by-runtime-version"></a>Languages by runtime version 

[두 가지 버전의 Azure Functions 런타임](functions-versions.md)을 사용할 수 있습니다. 다음 표는 각 런타임 버전에서 지원되는 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>실험적 언어

버전 1.x에서 실험적 언어는 제대로 확장되지 않고 모든 바인딩을 지원하지도 않습니다.

실험적 기능에 대해서는 공식적인 지원이 없으므로 기반으로 사용하지 말아야 합니다. 실험적 언어 관련 문제에 대한 지원 사례를 열 수는 없습니다. 

버전 2.x 런타임은 실험적 언어를 지원하지 않습니다. 프로덕션 환경에서 언어를 지원할 수 있는 경우만 새 언어에 대한 지원을 추가합니다. 

### <a name="language-extensibility"></a>언어 확장성

Starting with version 2.x, the runtime is designed to offer [language extensibility](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). 2\.x 런타임의 JavaScript 및 Java 언어는 이 확장성을 사용하여 빌드됩니다.

## <a name="next-steps"></a>다음 단계

To learn more about how to develop functions in the supported languages, see the following resources:

+ [C# class library developer reference](functions-dotnet-class-library.md)
+ [C# script developer reference](functions-reference-csharp.md)
+ [Java developer reference](functions-reference-java.md)
+ [JavaScript developer reference](functions-reference-node.md)
+ [PowerShell developer reference](functions-reference-powershell.md)
+ [Python developer reference](functions-reference-python.md)
+ [TypeScript developer reference](functions-reference-node.md#typescript)
