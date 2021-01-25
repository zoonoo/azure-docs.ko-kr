---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763479"
---
MSAL (Microsoft 인증 라이브러리) 앱은 문제를 진단 하는 데 도움이 될 수 있는 로그 메시지를 생성 합니다. 앱은 몇 줄의 코드를 사용하여 로깅을 구성하고 세부 정보 수준 및 개인 데이터와 조직 데이터가 기록되는지 여부에 대한 제어를 사용자 지정할 수 있습니다. MSAL 로깅 콜백을 만들고 사용자가 인증 문제가 있는 경우 로그를 제출 하는 방법을 제공 하는 것이 좋습니다.

## <a name="logging-levels"></a>로깅 수준

MSAL은 여러 수준의 로깅 세부 정보를 제공 합니다.

- 오류: 무언가 오류가 발생 하 여 오류가 발생 했음을 나타냅니다. 문제를 디버깅 하 고 식별 하는 데 사용 됩니다.
- 경고: 오류 또는 실패가 아니어도 되지만 진단 및 어설션 매크로나 문제를 해결 하기 위한 것입니다.
- 정보: MSAL은 디버깅을 위한 것이 아니라 정보를 제공 하기 위해 의도 된 이벤트를 로깅합니다.
- Verbose: 기본값입니다. MSAL은 라이브러리 동작에 대 한 전체 세부 정보를 기록 합니다.

## <a name="personal-and-organizational-data"></a>개인 및 조직 데이터

기본적으로 MSAL 로거가 매우 중요 한 개인 또는 조직 데이터를 캡처하지 않습니다. 이러한 작업을 수행 하기로 결정 한 경우 라이브러리는 개인 데이터와 조직 데이터를 로깅할 수 있는 옵션을 제공 합니다.

다음 섹션에서는 응용 프로그램에 대 한 MSAL 오류 로깅에 대 한 자세한 정보를 제공 합니다.