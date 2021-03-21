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
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578756"
---
# <a name="logging-in-msal-for-python"></a>Python용 MSAL에서 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>Python 로깅을 위한 MSAL

Python 용 MSAL의 로깅은 [python 표준 라이브러리의 로깅 모듈](https://docs.python.org/3/library/logging.html)을 활용 합니다. 다음과 같이 MSAL 로깅을 구성할 수 있습니다 ( [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)의 작동 방식 참조).

### <a name="enable-debug-logging-for-all-modules"></a>모든 모듈에 대해 디버그 로깅 사용

기본적으로 Python 스크립트의 로깅은 해제 되어 있습니다. 스크립트의 **모든** Python 모듈에 대 한 자세한 정보 로깅을 사용 하도록 설정 하려면 다음 수준에서을 사용 합니다 `logging.basicConfig` `logging.DEBUG` .

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

그러면 로깅 모듈에 지정 된 모든 로그 메시지가 표준 출력으로 출력 됩니다.

### <a name="configure-msal-logging-level"></a>MSAL 로깅 수준 구성

`logging.getLogger()`로 거 이름이 있는 메서드를 사용 하 여 Python 로그 공급자에 대 한 MSAL의 로깅 수준을 구성할 수 있습니다 `"msal"` .

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Azure 앱 Insights를 사용 하 여 MSAL 로깅 구성

Python 로그는 로그 처리기에 제공 되며 기본값은 `StreamHandler` 입니다. 계측 키를 사용 하 여 Application Insights에 MSAL 로그를 보내려면 라이브러리에서 제공 하는를 사용 합니다 `AzureLogHandler` `opencensus-ext-azure` .

을 (를) 설치 하려면 `opencensus-ext-azure` `opencensus-ext-azure` PyPI에서 종속성 또는 pip 설치로 패키지를 추가 합니다.

```console
pip install opencensus-ext-azure
```

그런 다음 `"msal"` `AzureLogHandler` 환경 변수에 설정 된 계측 키를 사용 하 여 로그 공급자의 기본 처리기를 인스턴스로 변경 합니다 `APP_INSIGHTS_KEY` .

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Python의 개인 및 조직 데이터

Python 용 MSAL은 개인 데이터 나 조직 데이터를 기록 하지 않습니다. 개인 또는 조직 데이터 로깅을 설정 하거나 해제 하는 속성은 없습니다.

표준 Python 로깅을 사용 하 여 원하는 모든 항목을 기록할 수 있지만 중요 한 데이터를 안전 하 게 처리 하 고 규정 요구 사항을 준수 해야 합니다.

Python 로그인에 대 한 자세한 내용은 Python의  [로깅: 방법](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft id 플랫폼 코드 샘플](sample-v2-code.md)을 참조 하세요.
