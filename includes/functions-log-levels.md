---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164921"
---
*로그 수준은* 모든 로그에 할당 됩니다. 값은 상대적 중요도를 나타내는 정수입니다.

|LogLevel    |코드| Description |
|------------|---|--------------|
|추적       | 0 |가장 자세한 메시지를 포함하는 로그입니다. 메시지는 중요한 애플리케이션 데이터를 포함할 수 있습니다. 메시지는 기본적으로 사용하지 않도록 설정되며 프로덕션 환경에서 사용하면 안 됩니다.|
|디버그       | 1 | 개발 중에 대화형 조사에 사용되는 로그입니다. 해당 로그는 기본적으로 디버깅에 유용한 정보를 포함하고 장기적인 값은 포함하지 않아야 합니다. |
|정보 | 2 | 애플리케이션의 일반적인 흐름을 추적하는 로그입니다. 해당 로그는 장기적인 값을 포함해야 합니다. |
|Warning     | 3 | 응용 프로그램 흐름에서 비정상적인 이벤트 또는 예기치 않은 이벤트를 강조 표시 하는 로그입니다. 그렇지 않으면 응용 프로그램 실행이 중지 됩니다. |
|Error       | 4 | 오류로 인해 현재 실행 흐름이 중지 될 때를 강조 하는 로그입니다. 이러한 오류는 응용 프로그램 전체에 오류가 아니라 현재 활동의 오류를 나타내야 합니다. |
|위험    | 5 | 복구할 수 없는 애플리케이션 또는 시스템 크래시나 즉각적인 주의가 필요한 치명적인 오류를 설명하는 로그입니다. |
|None        | 6 | 지정 된 범주에 대 한 로깅을 사용 하지 않도록 설정 합니다. |

파일 구성 [ *에 대 한host.js* ](../articles/azure-functions/functions-host-json.md) 는 함수 앱이 Application Insights에 보내는 로깅의 양을 결정 합니다.  
