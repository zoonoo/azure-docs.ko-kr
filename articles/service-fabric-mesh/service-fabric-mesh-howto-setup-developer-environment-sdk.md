---
title: Service Fabric Mesh 앱에 대한 Windows 개발 환경 설정 | Microsoft Docs
description: Service Fabric Mesh 응용 프로그램을 만들고 Azure Service Fabric Mesh에 배포할 수 있도록 Windows 개발 환경을 설정합니다.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/20/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 589bef1894a3bee1e6974a0ea2516200fae2891f
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185546"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Service Fabric 응용 프로그램을 빌드하도록 Windows 개발 환경 설정

Windows 개발 머신에서 Azure Service Fabric 앱을 빌드하고 실행하려면 Service Fabric 런타임, SDK, 도구를 설치합니다.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>지원되는 운영 체제 버전

개발을 위해 다음 운영 체제 버전이 지원됩니다.

* Windows 10(Enterprise, Professional 또는 Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017은 Service Fabric 앱을 배포해야 합니다. [설치 버전 15.6.0][download-visual-studio] 이상은 다음 워크로드를 사용하도록 설정합니다.

- ASP.NET 및 웹 개발
- Azure 개발
 
## <a name="windows-10---install-docker"></a>Windows 10 - Docker 설치

Service Fabric Mesh에서 사용하는 컨테이너화된 Service Fabric 앱을 지원하기 위해 [Docker Community Edition for Windows][download-docker] 최신 버전을 다운로드하여 설치합니다.

설치하는 동안 요청 시 **Linux 컨테이너 대신 Windows 컨테이너 사용**을 선택합니다. 머신에서 Hyper-V를 사용하도록 설정되지 않은 경우 Docker 설치 시 설정하라는 메시지가 표시됩니다. 메시지가 표시되면 **확인**을 클릭하여 설정합니다.

## <a name="windows-server-2016---install-hyper-v-and-docker"></a>Windows Server 2016 - Hyper-V 및 Docker 설치

**Hyper-V 설치**

먼저 관리자 권한으로 PowerShell을 열고 다음 명령을 실행하여 Hyper-V를 설치하고 컴퓨터를 다시 시작합니다. 자세한 내용은 [Windows Server용 Docker Enterprise Edition][download-docker-server]을 참조하세요.

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

**Docker 설치**

관리자 권한으로 PowerShell을 열고 다음 명령을 실행하여 Docker를 설치합니다.

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

컴퓨터를 다시 시작합니다.

## <a name="sdk-and-tools"></a>SDK 및 도구

다음 순서에 따라 Service Fabric Mesh 런타임, SDK 및 도구를 설치합니다.

1. 웹 플랫폼 설치 관리자를 사용하여 [Service Fabric Mesh SDK][download-sdkmesh]를 설치합니다. 그러면 Microsoft Azure Service Fabric SDK 및 런타임도 설치합니다.
2. Visual Studio Marketplace에서 [Visual Studio Service Fabric 도구(미리 보기) 확장] [download-tools]을 설치합니다.

## <a name="build-a-cluster"></a>클러스터 빌드

Visual Studio를 사용하는 경우 계정이 없으면 Visual Studio가 자동으로 로컬 클러스터를 만들기 때문에 이 섹션을 건너뛰어도 됩니다.

Service Fabric 앱을 만들고 실행할 때 디버깅 성능을 높이기 위해 단일 노드 로컬 개발 클러스터를 만드는 것이 좋습니다. Service Fabric Mesh 프로젝트를 배포하거나 디버깅할 때마다 이 클러스터를 실행해야 합니다.

Docker는 클러스터를 빌드하기 전에 실행**해야** 합니다. 터미널 창을 열고 오류가 발생하는지를 확인하는 `docker ps`를 실행하여 Docker가 실행되는지 테스트합니다. 응답이 오류를 나타내지 않는 경우 Docker가 실행되고 클러스터를 빌드할 준비가 되었습니다.

런타임, SDK 및 Visual Studio 도구를 설치한 후에 개발 클러스터를 만듭니다.

1. PowerShell 창을 닫습니다.
2. 관리자 권한으로 새롭게 상승된 PowerShell 창을 엽니다. 이 단계는 최근에 설치한 Service Fabric 모듈을 로드하기 위해 필요합니다.
3. 다음 PowerShell 명령을 실행하여 개발 클러스터를 만듭니다.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. 로컬 클러스터 관리자 도구를 시작하려면 다음 PowerShell 명령을 실행합니다.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

이제 Service Fabric Mesh 응용 프로그램을 만들 준비가 되었습니다!

## <a name="next-steps"></a>다음 단계

[Azure Service Fabric 앱 만들기](service-fabric-mesh-tutorial-create-dotnetcore.md) 자습서를 읽습니다.

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/