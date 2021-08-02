---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f4677d9800f0c94d9071838b9addbeb3cd1231c9
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071344"
---
로그아웃 흐름에 포함되는 단계는 다음과 같습니다.

1. 앱에서 사용자가 **로그아웃** 을 선택합니다.
1. 앱은 해당 세션 쿠키를 지우고, 인증 라이브러리는 해당 토큰 캐시를 지웁니다.
1. 앱이 사용자를 Azure AD B2C 로그아웃 엔드포인트로 리디렉션하여 Azure AD B2C 세션을 종료합니다.
1. 사용자가 앱으로 다시 리디렉션됩니다.