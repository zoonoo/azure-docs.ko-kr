---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593993"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>다운로드 및 이스티오 이스티옥틀 클라이언트 바이너리를 설치

리눅스 또는 [리눅스에 대 한 Windows][install-wsl] `curl` 하위 시스템에 bash 기반 셸에서, `tar` Istio 릴리스를 다운로드 하 고 다음과 같이 추출 하는 데 사용:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

`istioctl` 클라이언트 바이너리는 클라이언트 컴퓨터에서 실행되며 Istio 서비스 메시와 상호 작용할 수 있습니다. 다음 명령을 사용하여 Linux 또는 [Windows Subsystem for Linux][install-wsl]의 Bash 기반 셸에 Istio `istioctl` 클라이언트 이진 파일을 설치합니다. 이 명령을 사용하여 `istioctl` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Istio `istioctl` 클라이언트 이진 파일에 대해 명령줄 완료를 사용할 경우 다음과 설정합니다.

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10