---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593991"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>다운로드 및 이스티오 이스티옥틀 클라이언트 바이너리를 설치

Windows의 PowerShell 기반 셸에서 `Invoke-WebRequest` Istio 릴리스를 다운로드한 다음 `Expand-Archive` 다음과 같이 추출하는 데 사용합니다.

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

`istioctl` 클라이언트 바이너리는 클라이언트 컴퓨터에서 실행되며 Istio 서비스 메시와 상호 작용할 수 있습니다. 다음 명령을 사용하여 Windows의 PowerShell `istioctl` 기반 셸에 Istio 클라이언트 바이너리를 설치합니다. 이러한 명령은 `istioctl` 클라이언트 바이너리를 Istio 폴더에 복사한 다음 `PATH`을 통해 즉시(현재 셸에서) 영구적으로(셸 재시작) 사용할 수 있도록 합니다. 이러한 명령을 실행하기 위해 승격된(관리자) 권한이 필요하지 않으며 셸을 다시 시작할 필요가 없습니다.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```