---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92164921"
---
*로그 수준* 은 모든 로그에 할당됩니다. 값은 상대적 중요도를 나타내는 정수입니다.

|LogLevel    |코드| Description |
|------------|---|--------------|
|추적       | 0 |가장 자세한 메시지를 포함하는 로그입니다. 메시지는 중요한 애플리케이션 데이터를 포함할 수 있습니다. 메시지는 기본적으로 사용하지 않도록 설정되며 프로덕션 환경에서 사용하면 안 됩니다.|
|디버그       | 1 | 개발 중에 대화형 조사에 사용되는 로그입니다. 해당 로그는 기본적으로 디버깅에 유용한 정보를 포함하고 장기적인 값은 포함하지 않아야 합니다. |
|정보 | 2 | 애플리케이션의 일반적인 흐름을 추적하는 로그입니다. 해당 로그는 장기적인 값을 포함해야 합니다. |
|Warning     | 3 | 애플리케이션 흐름에서 비정상적이거나 예기치 않은 이벤트를 강조 표시하지만, 그렇지 않으면 애플리케이션 실행을 중지하지 않는 로그입니다. |
|Error       | 4 | 오류로 인해 현재 실행 흐름이 중지될 경우 이를 강조하는 로그입니다. 이러한 오류는 애플리케이션 전체의 오류가 아닌 현재 활동의 오류를 나타내야 합니다. |
|위험    | 5 | 복구할 수 없는 애플리케이션 또는 시스템 크래시나 즉각적인 주의가 필요한 치명적인 오류를 설명하는 로그입니다. |
|None        | 6 | 지정된 범주에 대한 로깅을 사용하지 않도록 설정합니다. |

[*host.json* 파일](../articles/azure-functions/functions-host-json.md) 구성은 함수 앱이 Application Insights에 보내는 로깅의 양을 결정합니다.  
