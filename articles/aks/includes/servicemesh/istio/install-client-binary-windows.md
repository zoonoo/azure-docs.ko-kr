---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: a02c17013a205ccc0da85536b491d467ef72fa48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666722"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istioctl 클라이언트 이진 파일 다운로드 및 설치

Windows의 PowerShell 기반 셸에서 `Invoke-WebRequest`를 사용하여 Istio 릴리스를 다운로드한 후, 다음과 같이 `Expand-Archive`로 추출합니다.

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.7.3"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-win.zip&quot; -OutFile &quot;istioctl-$ISTIO_VERSION.zip"
Expand-Archive -Path "istioctl-$ISTIO_VERSION.zip" -DestinationPath .
```

`istioctl` 클라이언트 이진 파일은 클라이언트 컴퓨터에서 실행되며 AKS 클러스터에 Istio를 설치하고 관리할 수 있습니다. 다음 명령을 사용하여 Windows의 PowerShell 기반 셸에 Istio `istioctl` 클라이언트 이진 파일을 설치합니다. 이러한 명령은 `istioctl` 클라이언트 이진 파일을 Istio 폴더에 복사한 다음, `PATH`를 통해 즉시(현재 셸에서) 및 영구적으로(셸 다시 시작에서) 사용할 수 있습니다. 이러한 명령을 실행하는 데에는 상승된(관리자) 권한이 필요하지 않으며 셸을 다시 시작하지 않아도 됩니다.

```powershell
# Copy istioctl.exe to C:\Istio
New-Item -ItemType Directory -Force -Path "C:\Istio"
Move-Item -Path .\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```