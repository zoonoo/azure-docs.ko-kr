---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77593734"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Linkerd linkerd 클라이언트 이진 파일 다운로드 및 설치

MacOS의 bash 기반 셸에서 `curl`을 사용하여 다음과 같이 Linkerd 릴리스를 다운로드합니다.

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

`linkerd` 클라이언트 이진 파일은 클라이언트 컴퓨터에서 실행되며 Linkerd 서비스 메시와 상호 작용할 수 있습니다. 다음 명령을 사용하여 MacOS의 bash 기반 셸에서 Linkerd `linkerd` 클라이언트 이진 파일을 설치합니다. 이 명령을 사용하여 `linkerd` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Linkerd `linkerd` 클라이언트 이진 파일에 대한 명령줄을 완료하려면 다음과 같이 설정합니다.

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
