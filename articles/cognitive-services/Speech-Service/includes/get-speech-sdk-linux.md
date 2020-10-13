---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: a93c478f0621bf62b710f58f3e6f06298bad9954
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83673053"
---
:::row:::
    :::column span="3":::
        Speech SDK는 Linux에서 사용할 때 다음 대상 아키텍처에서 **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**및 **CentOS 7/8** 만 지원 합니다.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- c + + 개발용 x86 (Debian/Ubuntu), x64, ARM32 (Debian/ubuntu) 및 ARM64 (Debian/Ubuntu)
- Java 용 x64, ARM32 (Debian/Ubuntu) 및 ARM64 (Debian/Ubuntu)
- .NET Core 용 x64, ARM32 (Debian/Ubuntu) 및 ARM64 (Debian/Ubuntu)
- Python 용 x64

> [!IMPORTANT]
> Linux ARM64 c #의 경우 .NET Core 3.x (dotnet-3.x 패키지)가 필요 합니다.

### <a name="system-requirements"></a>시스템 요구 사항

네이티브 응용 프로그램의 경우 음성 SDK는를 사용 `libMicrosoft.CognitiveServices.Speech.core.so` 합니다. 대상 아키텍처 (x86, x64)가 응용 프로그램과 일치 하는지 확인 합니다. Linux 버전에 따라 추가 종속성이 필요할 수도 있습니다.

- GNU C 라이브러리의 공유 라이브러리(POSIX Threads Programming 라이브러리, `libpthreads` 포함)
- OpenSSL 라이브러리 ( `libssl.so.1.0.0` 또는 `libssl.so.1.0.2` )
- ALSA 애플리케이션의 공유 라이브러리(`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 및 CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - RHEL/CentOS 7에서 [Speech SDK용 RHEL/CentOS 7을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)의 지침을 따르세요.
> - RHEL/CentOS 8에서 [Linux용 OpenSSL을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)의 지침을 따르세요.

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
