---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399902"
---
:::row:::
    :::column span="3":::
        Speech SDK는 Linux에서 사용할 때 다음 대상 아키텍처에서 **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**및 **CentOS 7/8** 만 지원 합니다.
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Linux ARM64를 대상으로 하 고 c #을 사용 하는 경우 .NET Core 3.x (dotnet-3.x 패키지)가 필요 합니다. ARM32 또는 ARM64를 대상으로 하는 경우 Python은 지원 되지 않습니다.

> [!NOTE]
> Ubuntu 16.04, Ubuntu 18.04 및 Debian 9의 x86 아키텍처는 Speech SDK를 사용한 c + + 개발만 지원 합니다.

### <a name="system-requirements"></a>시스템 요구 사항

네이티브 응용 프로그램의 경우 음성 SDK는를 사용 `libMicrosoft.CognitiveServices.Speech.core.so`합니다. 대상 아키텍처 (x86, x64)가 응용 프로그램과 일치 하는지 확인 합니다. Linux 버전에 따라 추가 종속성이 필요할 수도 있습니다.

- GNU C 라이브러리의 공유 라이브러리(POSIX Threads Programming 라이브러리, `libpthreads` 포함)
- OpenSSL 라이브러리 (`libssl.so.1.0.0` 또는 `libssl.so.1.0.2`)
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
> [SPEECH SDK에 대해 RHEL/CentOS 7을 구성 하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)에 대 한 지침을 따르세요.

> [!TIP]
> RHEL/CentOS 8에서 [Linux용 OpenSSL을 구성하는 방법](../how-to-configure-openssl-linux.md)의 지침을 따르세요.

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
