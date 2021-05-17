---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 164844a9da09563f8fbefe7ec60aff7eb05ace2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666724"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istioctl 클라이언트 이진 파일 다운로드 및 설치

Linux의 bash 기반 셸 또는 [Windows Subsystem for Linux][install-wsl]에서 `curl`을 사용하여 Istio 릴리스를 다운로드한 후, 다음과 같이 `tar`로 추출합니다.

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-linux-amd64.tar.gz" | tar xz
```

`istioctl` 클라이언트 이진 파일은 클라이언트 컴퓨터에서 실행되며 AKS 클러스터에 Istio를 설치하고 관리할 수 있습니다. 다음 명령을 사용하여 Linux 또는 [Windows Subsystem for Linux][install-wsl]의 Bash 기반 셸에 Istio `istioctl` 클라이언트 이진 파일을 설치합니다. 이 명령을 사용하여 `istioctl` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
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
[install-wsl]: /windows/wsl/install-win10
