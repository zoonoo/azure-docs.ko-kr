---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593732"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>링커드 링커드 클라이언트 바이너리 다운로드 및 설치

리눅스 또는 [리눅스에 대 한 Windows][install-wsl] `curl` 하위 시스템에 bash 기반 셸에서, 다음과 같이 Linkerd 릴리스를 다운로드 하는 데 사용:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

`linkerd` 클라이언트 바이너리는 클라이언트 컴퓨터에서 실행되며 Linkerd 서비스 메시와 상호 작용할 수 있습니다. 다음 명령을 사용하여 리눅스 또는 `linkerd` [Linux용 Windows 서브시스템의][install-wsl]bash 기반 셸에 Linkerd 클라이언트 바이너리를 설치합니다. 이 명령을 사용하여 `linkerd` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Linkerd `linkerd` 클라이언트 바이너리에 대한 명령줄 완료를 원하는 경우 다음과 같이 설정하십시오.

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10