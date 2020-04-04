---
title: RHEL / CentOS 7 - 음성 서비스를 구성하는 방법
titleSuffix: Azure Cognitive Services
description: 음성 SDK를 사용할 수 있도록 RHEL/CentOS 7을 구성하는 방법에 대해 알아봅니다.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639181"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>음성 SDK에 대해 RHEL/CentOS 7 구성

레드 햇 엔터프라이즈 리눅스 (RHEL) 8 x64 및 CentOS 8 x64 는 공식적으로 음성 SDK 버전 1.10.0 이상에 의해 지원됩니다. RHEL/CentOS 7 x64에서 음성 SDK를 사용할 수도 있지만 C++ 개발의 경우 C++ 컴파일러와 시스템의 공유 C++ 런타임 라이브러리를 업데이트해야 합니다.

C++ 컴파일러 버전을 확인하려면 다음을 실행하십시오.

```bash
g++ --version
```

컴파일러가 설치된 경우 출력은 다음과 같아야 합니다.

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

이 메시지를 통해 GCC 주 버전 4가 설치되어 있음을 알 수 있습니다. 이 버전은 음성 SDK에서 사용하는 C++ 11 표준에 대한 완전한 지원이 없습니다. 이 GCC 버전과 음성 SDK 헤더로 C++ 프로그램을 컴파일하려고 하면 컴파일 오류가 발생합니다.

공유 C++ 런타임 라이브러리(libstdc++)의 버전을 확인하는 것도 중요합니다. 대부분의 음성 SDK는 네이티브 C++ 라이브러리로 구현되므로 응용 프로그램을 개발하는 데 사용하는 언어에 관계없이 libstdc++에 따라 다릅니다.

시스템에서 libstdc++의 위치를 찾으려면 다음을 실행하십시오.

```bash
ldconfig -p | grep libstdc++
```

바닐라 RHEL/CentOS 7(x64)의 출력은 다음과 동일합니다.

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

이 메시지에 따라 다음 명령을 사용하여 버전 정의를 확인하는 것이 좋습니다.

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

출력은 다음과 같아야 합니다.

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

음성 SDK는 **CXXABI_1.3.9** 및 **GLIBCXX_3.4.21이**필요합니다. Linux 패키지의 음성 `ldd libMicrosoft.CognitiveServices.Speech.core.so` SDK 라이브러리에서 실행하여 이 정보를 찾을 수 있습니다.

> [!NOTE]
> 시스템에 설치된 GCC 버전은 일치하는 런타임 라이브러리와 함께 **5.4.0**이상인 것이 좋습니다.

## <a name="example"></a>예제

이 샘플 명령은 SDK 1.10.0 이상을 사용하여 개발용 RHEL/CentOS 7 x64(C++, C#, Java, 파이썬)를 구성하는 방법을 보여 주는 샘플 명령입니다.

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK 정보](speech-sdk.md)
