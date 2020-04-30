---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81735093"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istio 클라이언트 이진 파일을 다운로드 하 여 설치 합니다.

Windows의 PowerShell 기반 셸에서를 사용 하 여 Istio 릴리스를 다운로드 한 후 다음과 같이를 `Invoke-WebRequest` `Expand-Archive` 사용 하 여 추출 합니다.

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

클라이언트 `istioctl` 이진 파일은 클라이언트 컴퓨터에서 실행 되며 istio 서비스 메시와 상호 작용할 수 있습니다. 다음 명령을 사용 하 여 Windows의 PowerShell 기반 `istioctl` 셸에서 istio 클라이언트 이진을 설치 합니다. 이러한 명령은 `istioctl` 클라이언트 이진 파일을 istio 폴더에 복사한 다음 즉시 (현재 셸에서) 및를 통해 영구적으로 (셸 다시 시작에서) 사용할 수 있도록 설정 `PATH`합니다. 이러한 명령을 실행 하는 데에는 상승 된 (관리자) 권한이 필요 하지 않으며 셸을 다시 시작 하지 않아도 됩니다.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```