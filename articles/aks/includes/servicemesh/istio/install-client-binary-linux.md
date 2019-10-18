---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: abc8727c2a9ad7bb6621b8c8e019e14fb8cdec97
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530408"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istio 클라이언트 이진 파일을 다운로드 하 여 설치 합니다.

Linux의 bash 기반 셸 또는 [linux 용 Windows 하위 시스템][install-wsl]에서 `curl`를 사용 하 여 istio 릴리스를 다운로드 한 후 다음과 같이 `tar`를 사용 하 여 추출 합니다.

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.3.2

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

@No__t_0 클라이언트 이진 파일은 클라이언트 컴퓨터에서 실행 되며 Istio 서비스 메시와 상호 작용할 수 있습니다. 다음 명령을 사용 하 여 linux의 bash 기반 셸 또는 [linux 용 Windows 하위 시스템][install-wsl]에 istio `istioctl` 클라이언트 이진을 설치 합니다. 이 명령을 사용하여 `istioctl` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

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