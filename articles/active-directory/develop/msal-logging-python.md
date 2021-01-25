---
title: Python 용 MSAL에서 오류 및 예외 로깅
titleSuffix: Microsoft identity platform
description: Python 용 MSAL에서 오류 및 예외를 기록 하는 방법 알아보기
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: f660bd17954afb4ae02da77e93d4d9cc36f3d355
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763527"
---
# <a name="logging-in-msal-for-python"></a>Python용 MSAL에서 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>Python 로깅을 위한 MSAL

MSAL의 로그인은 표준 Python 로깅 메커니즘을 사용 합니다. 예를 들어 `logging.info("msg")` [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)다음과 같이 msal 로깅을 구성할 수 있습니다.

### <a name="enable-debug-logging-for-all-modules"></a>모든 모듈에 대해 디버그 로깅 사용

기본적으로 Python 스크립트의 로깅은 해제 되어 있습니다. 전체 Python 스크립트의 모든 모듈에 대해 디버그 로깅을 사용 하도록 설정 하려면 다음을 사용 합니다.

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>침묵 전용 MSAL 로깅

MSAL 라이브러리 로깅을 대기 전용으로 설정 하 고 Python 스크립트의 모든 다른 모듈에서 디버그 로깅을 사용 하도록 설정 하는 동안 MSAL Python에서 사용 하는로 거를 해제 합니다.

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python의 개인 및 조직 데이터

Python 용 MSAL은 개인 데이터 나 조직 데이터를 기록 하지 않습니다. 개인 또는 조직 데이터 로깅을 설정 하거나 해제 하는 속성은 없습니다.

표준 Python 로깅을 사용 하 여 원하는 모든 항목을 기록할 수 있지만 중요 한 데이터를 안전 하 게 처리 하 고 규정 요구 사항을 준수 해야 합니다.

Python 로그인에 대 한 자세한 내용은 Python의  [로깅: 방법](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft id 플랫폼 코드 샘플](sample-v2-code.md)을 참조 하세요.

---