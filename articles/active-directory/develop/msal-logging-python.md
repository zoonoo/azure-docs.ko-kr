---
title: Python용 MSAL에서 오류 및 예외 로깅
titleSuffix: Microsoft identity platform
description: Python용 MSAL에서 오류 및 예외를 로그하는 방법 알아보기
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
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578756"
---
# <a name="logging-in-msal-for-python"></a>Python용 MSAL에서 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>Python용 MSAL 로깅

Python용 MSAL의 로깅은 [Python 표준 라이브러리의 로깅 모듈](https://docs.python.org/3/library/logging.html)을 활용합니다. 다음과 같이 MSAL 로깅을 구성할 수 있습니다([username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)의 작동 방식 참조).

### <a name="enable-debug-logging-for-all-modules"></a>모든 모듈에 대해 디버그 로깅 사용

기본적으로 Python 스크립트의 로깅 사용 설정은 해제되어 있습니다. 스크립트의 **모든** Python 모듈에 대한 자세한 로깅을 사용하도록 설정하려면 다음과 같이 `logging.DEBUG` 수준에서 `logging.basicConfig`을 사용합니다.

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

그렇게 하면 로깅 모듈에 지정된 모든 로그 메시지가 표준 출력으로 인쇄됩니다.

### <a name="configure-msal-logging-level"></a>MSAL 로깅 수준 구성

다음과 같이 `"msal"` 로거 이름이 있는 `logging.getLogger()` 메서드를 사용하여 Python 로그 공급자에 대한 MSAL의 로깅 수준을 구성할 수 있습니다.

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Azure App Insights를 사용하여 MSAL 로깅 구성

Python 로그는 로그 처리기에 제공되며 기본값은 `StreamHandler`입니다. 계측 키를 사용하여 Application Insights에 MSAL 로그를 보내려면 `opencensus-ext-azure` 라이브러리에서 제공하는 `AzureLogHandler`를 사용합니다.

설치를 위해 다음과 같이 `opencensus-ext-azure`에서 PyPI로부터 종속성 또는 pip 설치로 `opencensus-ext-azure` 패키지를 추가합니다.

```console
pip install opencensus-ext-azure
```

그런 다음 `APP_INSIGHTS_KEY` 환경 번수에 설정된 계측 키를 사용하여 `"msal"` 로그 공급자의 기본 처리기를 `AzureLogHandler` 인스턴스로 변경합니다.

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Python의 개인 및 조직 데이터

Python용 MSAL은 개인 데이터나 조직 데이터를 로그하지 않습니다. 개인 또는 조직 데이터 로깅 사용을 설정하거나 해제하는 속성은 없습니다.

표준 Python 로깅을 사용하여 원하는 모든 항목을 로그할 수 있지만, 중요한 데이터를 안전하게 처리하고 규정 요구 사항을 준수해야 합니다.

Python에서의 로깅에 자세한 내용은 Python의 [로깅: 방법](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)을 참조하세요.

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft ID 플랫폼 코드 샘플](sample-v2-code.md)을 참조하세요.
