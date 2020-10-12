---
title: RHEL/CentOS 7-Speech service를 구성 하는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용할 수 있도록 RHEL/CentOS 7을 구성 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: ba531164e024f96d3bdd23912f3f6e90275edda4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83589740"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Speech SDK 용 RHEL/CentOS 7 구성

Red Hat Enterprise Linux (RHEL) 8 x64 및 CentOS 8 x 64는 Speech SDK 버전 1.10.0 이상에서 공식적으로 지원 됩니다. RHEL/CentOS 7 x 64에서 Speech SDK를 사용할 수도 있지만이 경우에는 시스템에서 c + + 컴파일러 (c + + 개발용) 및 공유 c + + 런타임 라이브러리를 업데이트 해야 합니다.

C + + 컴파일러 버전을 확인 하려면 다음을 실행 합니다.

```bash
g++ --version
```

컴파일러가 설치 된 경우 출력은 다음과 같습니다.

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

이 메시지를 통해 GCC 주 버전 4가 설치 되어 있다는 것을 알 수 있습니다. 이 버전은 Speech SDK에서 사용 하는 c + + 11 표준을 완벽 하 게 지원 하지 않습니다. 이 GCC 버전 및 Speech SDK 헤더를 사용 하 여 c + + 프로그램을 컴파일하려고 하면 컴파일 오류가 발생 합니다.

또한 공유 c + + 런타임 라이브러리 (libstdc + +)의 버전을 확인 하는 것이 중요 합니다. 대부분의 Speech SDK는 네이티브 c + + 라이브러리로 구현 됩니다. 즉, 응용 프로그램을 개발 하는 데 사용 하는 언어에 관계 없이 libstdc + +에 따라 달라 집니다.

시스템에서 libstdc + +의 위치를 찾으려면 다음을 실행 합니다.

```bash
ldconfig -p | grep libstdc++
```

바닐라 RHEL/CentOS 7 (x64)의 출력은 다음과 같습니다.

```bash
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

이 메시지에 따라 다음 명령을 사용 하 여 버전 정의를 확인 합니다.

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

출력은 다음과 같아야 합니다.

```bash
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

음성 SDK를 사용 하려면 **CXXABI_1.3.9** 및 **GLIBCXX_3**가 필요 합니다. `ldd libMicrosoft.CognitiveServices.Speech.core.so`Linux 패키지에서 음성 SDK 라이브러리를 실행 하 여이 정보를 찾을 수 있습니다.

> [!NOTE]
> 시스템에 설치 된 GCC 버전은 일치 하는 런타임 라이브러리와 함께 **5.4.0**이상으로 설정 하는 것이 좋습니다.

## <a name="example"></a>예

이는 Speech SDK 1.10.0 이상에서 개발 (c + +, c #, Java, Python)에 RHEL/CentOS 7 x 64를 구성 하는 방법을 보여 주는 샘플 명령 집합입니다.

### <a name="1-general-setup"></a>1. 일반 설정

먼저 모든 일반 종속성을 설치 합니다.

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
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. c/c + + 컴파일러 및 런타임 라이브러리

다음 명령을 사용 하 여 필수 구성 요소 패키지를 설치 합니다.

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

> [!NOTE]
> Libmpc-devel 패키지는 RHEL 7.8 업데이트에서 더 이상 사용 되지 않습니다. 이전 명령의 출력에 메시지가 포함 된 경우
>
> ```bash
> No package libmpc-devel available.
> ```
>
> 그런 다음 원본 원본에서 필요한 파일을 설치 해야 합니다. 다음 명령을 실행합니다.
>
> ```bash
> curl https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz -O
> tar zxf mpc-1.1.0.tar.gz
> mkdir mpc-1.1.0-build && cd mpc-1.1.0-build
> ../mpc-1.1.0/configure --prefix=/usr/local --libdir=/usr/local/lib64
> make -j$(nproc)
> sudo make install-strip
> ```

다음으로 컴파일러 및 런타임 라이브러리를 업데이트 합니다.

```bash
# Build GCC 5.4.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

업데이트 된 컴파일러와 라이브러리를 여러 컴퓨터에 배포 해야 하는 경우에는 아래에서 다른 컴퓨터로 복사할 수 있습니다 `/usr/local` . 런타임 라이브러리만 필요한 경우의 파일에는 `/usr/local/lib64` 충분 합니다.

### <a name="3-environment-settings"></a>3. 환경 설정

다음 명령을 실행 하 여 구성을 완료 합니다.

```bash
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
