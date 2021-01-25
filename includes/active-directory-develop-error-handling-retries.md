---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760685"
---
## <a name="retrying-after-errors-and-exceptions"></a>오류 및 예외 후 다시 시도

MSAL을 호출할 때 사용자 고유의 다시 시도 정책을 구현 해야 합니다. MSAL은 Azure AD 서비스에 대 한 HTTP 호출을 수행 하 고 가끔 오류가 발생할 수 있습니다. 예를 들어 네트워크는 다운 되거나 서버가 오버 로드 될 수 있습니다.  

### <a name="http-429"></a>429 HTTP

STS(서비스 토큰 서버)가 너무 많은 요청으로 인해 오버로드되는 경우 `Retry-After` 응답 필드에서 다시 시도할 수 있을 때까지의 시간에 대한 힌트를 포함하는 HTTP 오류 429를 반환합니다.