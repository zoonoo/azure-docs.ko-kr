---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81737185"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>링커드 링커드 클라이언트 바이너리 다운로드 및 설치

Windows의 PowerShell 기반 셸에서 `Invoke-WebRequest` 다음과 같이 Linkerd 릴리스를 다운로드하는 데 사용합니다.

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

`linkerd` 클라이언트 바이너리는 클라이언트 컴퓨터에서 실행되며 Linkerd 서비스 메시와 상호 작용할 수 있습니다. 다음 명령을 사용하여 Windows의 PowerShell `linkerd` 기반 셸에 Linkerd 클라이언트 바이너리를 설치합니다. 이러한 명령은 `linkerd` 클라이언트 바이너드를 Linkerd 폴더에 복사한 다음 을 통해 즉시(현재 셸에서) `PATH`영구적으로(셸 재시작) 사용할 수 있도록 합니다. 이러한 명령을 실행하기 위해 승격된(관리자) 권한이 필요하지 않으며 셸을 다시 시작할 필요가 없습니다.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```