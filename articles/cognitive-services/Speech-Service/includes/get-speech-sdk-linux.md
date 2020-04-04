---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a298e78c32464680dab9feccb3cfc84f686ff81b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656503"
---
:::row:::
    :::column span="3":::
        음성 SDK는 **우분투 16.04/18.04,** **데비안 9,** **레드햇 엔터프라이즈 리눅스(RHEL) 8,** **그리고 CentOS 7/8을** 리눅스와 함께 사용할 때 다음 대상 아키텍처에서만 지원합니다.
        - x86
        - x64
        - ARM32
        - ARM64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Linux ARM64를 대상으로 하고 C#을 사용하는 경우 .NET Core 3.x(닷넷-sdk-3.x 패키지)가 필요합니다. ARM32 또는 ARM64를 대상으로 하는 경우 파이썬은 지원되지 않습니다.

> [!NOTE]
> 우분투 16.04, 우분투 18.04, 데비안 9의 x86 아키텍처는 음성 SDK를 통해 C++ 개발만 지원합니다.

### <a name="system-requirements"></a>시스템 요구 사항

네이티브 응용 프로그램의 경우 음성 SDK는 을 사용합니다. `libMicrosoft.CognitiveServices.Speech.core.so` 대상 아키텍처(x86, x64)가 응용 프로그램과 일치하는지 확인합니다. Linux 버전에 따라 추가 종속성이 필요할 수 있습니다.

- GNU C 라이브러리의 공유 라이브러리(POSIX Threads Programming 라이브러리, `libpthreads` 포함)
- OpenSSL 라이브러리`libssl.so.1.0.0` (또는) `libssl.so.1.0.2`
- ALSA 애플리케이션의 공유 라이브러리(`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[우분투 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-8-and-centos-78"></a>[RHEL 8 및 센토스 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> [음성 SDK에 대한 RHEL / CentOS 7을 구성하는 방법에 대한](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)지침을 따르십시오.

> [!TIP]
> RHEL /CentOS 8에서 [리눅스에 대한 OpenSSL을 구성하는 방법에 대한](../how-to-configure-openssl-linux.md)지침을 따르십시오.

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
