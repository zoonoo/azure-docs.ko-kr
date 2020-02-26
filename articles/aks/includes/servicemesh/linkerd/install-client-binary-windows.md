---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593733"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Linkerd Linkerd 클라이언트 이진 파일을 다운로드 하 여 설치 합니다.

Windows의 PowerShell 기반 셸에서 다음과 같이 `Invoke-WebRequest`를 사용 하 여 Linkerd 릴리스를 다운로드 합니다.

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

`linkerd` 클라이언트 이진 파일은 클라이언트 컴퓨터에서 실행 되며 Linkerd 서비스 메시와 상호 작용할 수 있습니다. 다음 명령을 사용 하 여 Windows의 PowerShell 기반 셸에서 Linkerd `linkerd` 클라이언트 이진을 설치 합니다. 이 명령은 `linkerd` 클라이언트 이진 파일을 Linkerd 폴더에 복사한 다음 즉시 (현재 셸에서)와 `PATH`를 통해 영구적으로 (셸 다시 시작에서) 모두 사용할 수 있도록 설정 합니다. 이러한 명령을 실행 하는 데에는 상승 된 (관리자) 권한이 필요 하지 않으며 셸을 다시 시작 하지 않아도 됩니다.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```