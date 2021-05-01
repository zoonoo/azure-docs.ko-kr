---
title: 다른 Azure 서비스에 함수 연결
description: Azure Functions 프로젝트의 기존 함수에 다른 Azure 서비스에 연결하는 바인딩을 추가하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258204"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>바인딩을 사용하여 Azure 서비스에 함수 연결

함수를 만들 때 트리거 템플릿 집합에서 언어별 트리거 코드가 프로젝트에 추가됩니다. 입력 또는 출력 바인딩을 사용하여 함수를 다른 서비스에 연결하려면 함수에 특정 바인딩 정의를 추가해야 합니다. 바인딩에 대해 자세히 알아보려면 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

## <a name="local-development"></a>로컬 개발       

함수를 로컬로 개발하는 경우 바인딩을 추가하려면 함수 코드를 업데이트해야 합니다. Visual Studio Code를 사용하면 함수에 바인딩을 더 쉽게 추가할 수 있습니다.  

### <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code를 사용하여 함수를 개발하고 함수에서 function.json 파일을 사용하는 경우 Azure Functions 확장명은 기존 function.json 파일에 바인딩을 자동으로 추가할 수 있습니다. 자세히 알아보려면 [입력 및 출력 바인딩 추가](functions-develop-vs-code.md#add-input-and-output-bindings)를 참조하세요.   

### <a name="manually-add-bindings-based-on-examples"></a>예제에 따라 수동으로 바인딩 추가

기존 함수에 바인딩을 추가할 때 언어에서 사용할 경우 함수 코드와 function.json 구성 파일을 모두 업데이트해야 합니다. .NET 클래스 라이브러리와 Java 함수는 둘 다에서 function.json 대신 특성을 사용하므로 대신 업데이트해야 합니다.

다음 표를 사용하여 기존 함수 업데이트를 안내하는 데 사용할 수 있는 특정 바인딩 유형 예를 찾을 수 있습니다. 먼저 프로젝트에 해당하는 언어 탭을 선택합니다. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)에서 함수를 개발하는 경우 지정된 함수에 대한 **통합** 탭에서 입력 및 출력 바인딩을 추가합니다. 새 바인딩은 사용자의 언어에 따라 function.json 파일 또는 메서드 특성에 추가됩니다. 다음 문서에서는 포털에서 기존 함수에 바인딩을 추가하는 방법의 예를 보여 줍니다.

+ [Queue Storage 출력 바인딩](functions-integrate-storage-queue-output-binding.md)
+ [Azure Cosmos DB 출력 바인딩](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>다음 단계

+ [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)
