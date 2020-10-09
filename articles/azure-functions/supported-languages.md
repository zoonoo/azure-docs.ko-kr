---
title: Azure Functions에서 지원되는 언어
description: 지원 되는 언어 (GA) 및 미리 보기 상태인 언어와 함수 개발을 다른 언어로 확장 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 91a6ea886c3828678771b24d69bb7987af1fb105
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83994904"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions에서 지원되는 언어

이 문서에서는 Azure Functions에서 사용할 수 있는 언어에 대해 제공되는 지원 수준을 설명합니다. 기본적으로 지원 되지 않는 언어를 사용 하 여 함수를 만드는 전략에 대해서도 설명 합니다.

## <a name="levels-of-support"></a>지원 수준

지원 되는 두 가지 수준은 다음과 같습니다.

* **일반 공급(GA)** - 완전하게 지원되며 프로덕션용으로 승인되었습니다.
* **미리 보기** - 아직 지원되지 않지만 향후 GA 상태가 될 예정입니다.

## <a name="languages-by-runtime-version"></a>런타임 버전별 언어 

[세 가지 버전의 Azure Functions 런타임을](functions-versions.md) 사용할 수 있습니다. 다음 표는 각 런타임 버전에서 지원되는 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers-preview"></a>사용자 지정 처리기 (미리 보기) 

사용자 지정 처리기는 Azure Functions 호스트에서 이벤트를 수신 하는 간단한 웹 서버입니다. HTTP 기본 형식을 지 원하는 모든 언어는 사용자 지정 처리기를 구현할 수 있습니다. 즉, 사용자 지정 처리기를 사용 하 여 공식적으로 지원 되지 않는 언어로 함수를 만들 수 있습니다. 자세히 알아보려면 [Azure Functions 사용자 지정 처리기 (미리 보기)](functions-custom-handlers.md)를 참조 하세요.

## <a name="language-extensibility"></a>언어 확장성

버전 2.x부터 런타임은 [언어 확장성](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)을 제공 하도록 설계 되었습니다. 2.x 런타임의 JavaScript 및 Java 언어는 이 확장성을 사용하여 빌드됩니다.

## <a name="next-steps"></a>다음 단계

지원 되는 언어로 함수를 개발 하는 방법에 대해 자세히 알아보려면 다음 리소스를 참조 하세요.

+ [C # 클래스 라이브러리 개발자 참조](functions-dotnet-class-library.md)
+ [C # 스크립트 개발자 참조](functions-reference-csharp.md)
+ [Java 개발자 참조](functions-reference-java.md)
+ [JavaScript 개발자 참조](functions-reference-node.md)
+ [PowerShell 개발자 참조](functions-reference-powershell.md)
+ [Python 개발자 참조](functions-reference-python.md)
+ [TypeScript 개발자 참조](functions-reference-node.md#typescript)
