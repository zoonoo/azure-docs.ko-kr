---
title: Linux용 OpenSSL을 구성하는 방법
titleSuffix: Azure Cognitive Services
description: 리눅스에 대 한 OpenSSL를 구성 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331147"
---
# <a name="configure-openssl-for-linux"></a>Linux용 OpenSSL 구성

1.9.0 이전에 음성 SDK 버전을 사용하는 경우 [OpenSSL은](https://www.openssl.org) 호스트 시스템 버전으로 동적으로 구성됩니다. 음성 SDK의 이후 버전에서 OpenSSL(버전 [1.1.1b)은](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)음성 SDK의 핵심 라이브러리에 정적으로 연결됩니다.

연결을 보장하려면 OpenSSL 인증서가 시스템에 설치되어 있는지 확인합니다. 명령 실행:
```bash
openssl version -d
```

우분투/데비안 기반 시스템의 출력은 다음과 같은 것입니다.
```
OPENSSLDIR: "/usr/lib/ssl"
```

OPENSSLDIR `certs` 아래에 하위 디렉터리가 있는지 확인합니다. 위의 예에서는 `/usr/lib/ssl/certs`.

* 개인 인증서 `/usr/lib/ssl/certs` 파일(사용 또는 `.crt` `.pem` 확장자)이 많고 포함되어 있는 경우 추가 작업이 필요하지 않습니다.

* OPENSSLDIR이 `/usr/lib/ssl` 여러 개별 파일 대신 단일 인증서 번들 파일이 있는 경우 인증서를 찾을 수 있는 위치를 나타내기 위해 적절한 SSL 환경 변수를 설정해야 합니다.

## <a name="examples"></a>예

- OPENSSLDIR입니다. `/opt/ssl` 많은 `certs` `.crt` 또는 `.pem` 파일이 있는 하위 디렉터리입니다.
Speech SDK를 `/opt/ssl/certs` 사용하는 프로그램을 실행하기 전에 환경 변수를 `SSL_CERT_DIR` 가리키도록 설정합니다. 예를 들어:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR은 `/etc/pki/tls` (RHEL / CentOS 기반 시스템에서와 같이)입니다. 예를 `certs` 들어 `ca-bundle.crt`인증서 번들 파일이 있는 하위 디렉터리입니다.
Speech SDK를 사용하는 프로그램을 실행하기 전에 해당 파일을 가리키도록 환경 변수를 `SSL_CERT_FILE` 설정합니다. 예를 들어:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK 정보](speech-sdk.md)
