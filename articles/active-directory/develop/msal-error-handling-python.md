---
title: Python용 MSAL에서 오류 및 예외 처리
titleSuffix: Microsoft identity platform
description: Python 응용 프로그램에 대 한 오류 및 예외를 처리 하는 방법, 조건부 액세스 클레임 문제 및 MSAL의 재시도에 대해 알아봅니다.
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
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761083"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Python용 MSAL에서 오류 및 예외 처리

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Python에 대 한 MSAL의 오류 처리

Python용 MSAL에서 대부분의 오류는 API 호출의 반환 값으로 전달됩니다. 이 오류는 Microsoft ID 플랫폼의 JSON 응답을 포함하는 사전으로 표시됩니다.

* 성공적인 응답에는 `"access_token"` 키가 포함됩니다. 응답의 형식은 OAuth2 프로토콜로 정의됩니다. 자세한 내용은 [5.1 성공 응답](https://tools.ietf.org/html/rfc6749#section-5.1)을 참조하세요.
* 오류 응답은 는 `"error"`를 포함되며 일반적으로 `"error_description"`입니다. 응답의 형식은 OAuth2 프로토콜로 정의됩니다. 자세한 내용은 [5.2 오류 응답](https://tools.ietf.org/html/rfc6749#section-5.2)을 참조하세요.

오류가 반환되면 `"error_description"` 키에 인간 판독 가능 메시지가 포함되며 여기에는 일반적으로 Microsoft ID 플랫폼 오류 코드가 포함됩니다. 다양한 오류 코드에 대한 자세한 내용은 [인증 및 권한 부여 오류 코드](./reference-aadsts-error-codes.md)를 참조하세요.

Python용 MSAL에서는 대부분의 오류가 오류 값을 반환하여 처리되기 때문에 예외가 드물게 발생합니다. `ValueError`API 매개 변수가 잘못 된 경우와 같이 라이브러리를 사용 하는 방법에 문제가 있는 경우에만 예외가 throw 됩니다.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>다음 단계

문제를 진단 하 고 디버깅 하는 데 도움이 되도록 [Python 용 MSAL에서 로깅을](msal-logging-python.md) 사용 하도록 설정 합니다.
