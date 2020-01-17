---
title: Linux 용 OpenSSL을 구성 하는 방법
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
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156491"
---
# <a name="configure-openssl-for-linux"></a>Linux 용 OpenSSL 구성

1\.9.0 이전에 Speech SDK 버전을 사용 하는 경우 [OpenSSL](https://www.openssl.org) 는 호스트 시스템 버전으로 동적으로 구성 됩니다. 이후 버전의 Speech SDK에서는 OpenSSL (버전 [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html))이 speech sdk의 핵심 라이브러리에 정적으로 연결 되어 있습니다.

## <a name="troubleshoot-connectivity"></a>연결 문제 해결

Speech SDK의 1.9.0 릴리스를 사용 하는 경우 연결 오류가 발생 하는 경우 Linux 파일 시스템에 있는 `/usr/lib` 디렉터리에 `ssl/certs` 디렉터리가 있는지 확인 합니다. `ssl/certs` 디렉터리가 *없는*경우 다음 명령을 사용 하 여 시스템에 OpenSSL이 설치 되어 있는지 확인 합니다.

```bash
which openssl
```

그런 다음 OpenSSL `certs` 디렉터리를 찾고 해당 디렉터리의 내용을 `/usr/lib/ssl/certs` 디렉터리로 복사 합니다. 다음으로 다시 시도 하 여 연결 문제가 해결 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK 정보](speech-sdk.md)