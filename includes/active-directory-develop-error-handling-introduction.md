---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760687"
---
이 문서에서는 다양한 유형의 오류를 개략적으로 설명하고 일반적인 로그인 오류 처리를 위한 권장 사항을 제공합니다.

## <a name="msal-error-handling-basics"></a>MSAL 오류 처리 기본 사항

MSAL (Microsoft 인증 라이브러리)의 예외는 앱 개발자가 최종 사용자에 게 표시 하는 것이 아니라 문제를 해결 하기 위한 것입니다. 예외 메시지는 지역화되지 않았습니다.

예외 및 오류를 처리할 때 예외 유형 자체와 오류 코드를 사용하여 예외를 구별할 수 있습니다.  오류 코드 목록은 [Azure AD 인증 및 권한 부여 오류 코드](../articles/active-directory/develop/reference-aadsts-error-codes.md)를 참조 하세요.

로그인 환경에서 동의, 조건부 액세스(MFA, 디바이스 관리, 위치 기반 제한), 토큰 발급 및 상환 및 사용자 속성에 대한 오류가 발생할 수 있습니다.

다음 섹션에서는 앱에 대 한 오류 처리에 대 한 자세한 정보를 제공 합니다.