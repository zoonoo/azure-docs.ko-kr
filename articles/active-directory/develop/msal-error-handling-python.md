---
title: Python용 MSAL에서 오류 및 예외 처리
titleSuffix: Microsoft identity platform
description: Python용 MSAL 애플리케이션에서 오류와 예외, 조건부 액세스, 클레임, 챌린지 및 다시 시도를 처리하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761083"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Python용 MSAL에서 오류 및 예외 처리

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Python용 MSAL에서의 오류 처리

Python용 MSAL에서 대부분의 오류는 API 호출의 반환 값으로 전달됩니다. 이 오류는 Microsoft ID 플랫폼의 JSON 응답을 포함하는 사전으로 표시됩니다.

* 성공적인 응답에는 `"access_token"` 키가 포함됩니다. 응답의 형식은 OAuth2 프로토콜로 정의됩니다. 자세한 내용은 [5.1 성공 응답](https://tools.ietf.org/html/rfc6749#section-5.1)을 참조하세요.
* 오류 응답은 는 `"error"`를 포함되며 일반적으로 `"error_description"`입니다. 응답의 형식은 OAuth2 프로토콜로 정의됩니다. 자세한 내용은 [5.2 오류 응답](https://tools.ietf.org/html/rfc6749#section-5.2)을 참조하세요.

오류가 반환되면 `"error_description"` 키에 인간 판독 가능 메시지가 포함되며 여기에는 일반적으로 Microsoft ID 플랫폼 오류 코드가 포함됩니다. 다양한 오류 코드에 대한 자세한 내용은 [인증 및 권한 부여 오류 코드](./reference-aadsts-error-codes.md)를 참조하세요.

Python용 MSAL에서는 대부분의 오류가 오류 값을 반환하여 처리되기 때문에 예외가 드물게 발생합니다. `ValueError` 예외는 API 매개 변수의 형식이 잘못된 경우처럼 라이브러리를 사용하는 방법에 문제가 있는 경우에만 throw됩니다.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>다음 단계

문제를 진단하고 디버그할 수 있도록 [Python용 MSAL에서 로깅](msal-logging-python.md)을 사용하도록 설정하는 것이 좋습니다.
