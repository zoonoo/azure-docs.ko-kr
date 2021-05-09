---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760687"
---
이 문서에서는 다양한 유형의 오류를 개략적으로 설명하고 일반적인 로그인 오류 처리를 위한 권장 사항을 제공합니다.

## <a name="msal-error-handling-basics"></a>MSAL 오류 처리 기본 사항

MSAL(Microsoft 인증 라이브러리)의 예외는 앱 개발자가 최종 사용자에게 표시하지 않고 문제를 해결하기 위한 것입니다. 예외 메시지는 지역화되지 않았습니다.

예외 및 오류를 처리할 때 예외 유형 자체와 오류 코드를 사용하여 예외를 구별할 수 있습니다.  오류 코드 목록은 [Azure AD 인증 및 권한 부여 오류 코드](../articles/active-directory/develop/reference-aadsts-error-codes.md)를 참조하세요.

로그인 환경에서 동의, 조건부 액세스(MFA, 디바이스 관리, 위치 기반 제한), 토큰 발급 및 상환 및 사용자 속성에 대한 오류가 발생할 수 있습니다.

다음 섹션에서는 앱의 오류 처리에 대한 자세한 정보를 제공합니다.