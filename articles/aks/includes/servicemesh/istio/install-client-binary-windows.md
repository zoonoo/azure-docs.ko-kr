---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: a02c17013a205ccc0da85536b491d467ef72fa48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666722"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istio 클라이언트 이진 파일을 다운로드 하 여 설치 합니다.

Windows의 PowerShell 기반 셸에서를 사용 `Invoke-WebRequest` 하 여 Istio 릴리스를 다운로드 한 후 다음과 같이를 사용 하 여 추출 합니다 `Expand-Archive` .

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.7.3"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-win.zip" -OutFile "istioctl-$ISTIO_VERSION.zip"
Expand-Archive -Path "istioctl-$ISTIO_VERSION.zip" -DestinationPath .
```

클라이언트 `istioctl` 이진 파일은 클라이언트 컴퓨터에서 실행 되며 AKS 클러스터에 Istio를 설치 하 고 관리할 수 있습니다. 다음 명령을 사용 하 여 `istioctl` Windows의 PowerShell 기반 셸에서 Istio 클라이언트 이진을 설치 합니다. 이러한 명령은 `istioctl` 클라이언트 이진 파일을 Istio 폴더에 복사한 다음 즉시 (현재 셸에서) 및를 통해 영구적으로 (셸 다시 시작에서) 사용할 수 있도록 설정 `PATH` 합니다. 이러한 명령을 실행 하는 데에는 상승 된 (관리자) 권한이 필요 하지 않으며 셸을 다시 시작 하지 않아도 됩니다.

```powershell
# Copy istioctl.exe to C:\Istio
New-Item -ItemType Directory -Force -Path "C:\Istio"
Move-Item -Path .\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```