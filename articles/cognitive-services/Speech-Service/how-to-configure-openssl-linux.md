---
title: Linux용 OpenSSL을 구성하는 방법
titleSuffix: Azure Cognitive Services
description: Linux 용 OpenSSL을 구성 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81683159"
---
# <a name="configure-openssl-for-linux"></a>Linux용 OpenSSL 구성

1.9.0 이전에 Speech SDK 버전을 사용 하는 경우 [OpenSSL](https://www.openssl.org) 는 호스트 시스템 버전으로 동적으로 구성 됩니다. 이후 버전의 Speech SDK에서는 OpenSSL (버전 [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html))이 speech sdk의 핵심 라이브러리에 정적으로 연결 되어 있습니다.

연결을 보장 하려면 OpenSSL 인증서가 시스템에 설치 되어 있는지 확인 합니다. 다음 명령을 실행 합니다.
```bash
openssl version -d
```

Ubuntu/Debian 기반 시스템의 출력은 다음과 같아야 합니다.
```
OPENSSLDIR: "/usr/lib/ssl"
```

OPENSSLDIR 아래 `certs` 에 하위 디렉터리가 있는지 여부를 확인 합니다. 위의 예제에서는 `/usr/lib/ssl/certs`입니다.

* 이 `/usr/lib/ssl/certs` 있고 개별 인증서 파일 ( `.crt` 또는 `.pem` 확장명 포함)이 많으면 추가 작업이 필요 하지 않습니다.

* OPENSSLDIR이이 아닌 `/usr/lib/ssl` 다른 것 이거나 여러 개별 파일 대신 단일 인증서 번들 파일이 있는 경우 인증서를 찾을 수 있는 위치를 나타내는 적절 한 SSL 환경 변수를 설정 해야 합니다.

## <a name="examples"></a>예

- OPENSSLDIR은 `/opt/ssl`입니다. 또는 `.pem` 파일이 `certs` 많은 `.crt` 하위 디렉터리가 있습니다.
Speech SDK를 `SSL_CERT_DIR` 사용 하는 `/opt/ssl/certs` 프로그램을 실행 하기 전에를 가리키도록 환경 변수를 설정 합니다. 다음은 그 예입니다.
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR은 `/etc/pki/tls` (RHEL/CentOS 기반 시스템에서와 같이)입니다. 예를 `certs` 들어 `ca-bundle.crt`인증서 번들 파일이 있는 하위 디렉터리가 있습니다.
Speech SDK를 `SSL_CERT_FILE` 사용 하는 프로그램을 실행 하기 전에 해당 파일을 가리키도록 환경 변수를 설정 합니다. 다음은 그 예입니다.
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> 또한 일부 Linux 배포판에는 TMP 또는 TMPDIR 환경 변수가 정의 되어 있지 않습니다. 이렇게 하면 해당 CRL이 만료 될 때까지 다시 사용할 수 있도록 디스크에 캐시 하지 않고 매번 음성 SDK가 CRL (인증서 해지 목록)을 다운로드 합니다. 초기 연결 성능을 향상 시키기 위해 [TMPDIR 라는 환경 변수를 만들고 선택한 임시 디렉터리의 경로로 설정할](https://help.ubuntu.com/community/EnvironmentVariables)수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK 정보](speech-sdk.md)
