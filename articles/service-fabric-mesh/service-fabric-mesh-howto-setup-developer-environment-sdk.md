---
title: Service Fabric Mesh 앱을 빌드하기 위한 Windows 개발 환경 설정 | Microsoft Docs
description: Service Fabric Mesh 응용 프로그램을 만들고 Azure Service Fabric Mesh에 배포할 수 있도록 Windows 개발 환경을 설정합니다.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: 5ab817c65ab562f37b456cc3589624c1876084f0
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428204"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Service Fabric Mesh 앱을 빌드하기 위한 Windows 개발 환경 설정

Windows 개발 머신에서 Azure Service Fabric Mesh 애플리케이션을 빌드하고 실행하려면 다음이 필요합니다.

* Docker
* Visual Studio 2017 이상
* Service Fabric Mesh 런타임
* Service Fabric Mesh SDK 및 도구

그리고, 다음 Windows 버전 중 하나

* Windows 10(Enterprise, Professional 또는 Education) 버전 1709 (Fall Creators 업데이트) 또는 1803(Windows 10 2018년 4월 업데이트)
* Windows Server 버전 1709
* Windows Server 버전 1803

다음 지침을 실행 하는 Windows의 버전을 기반으로 하는 데 도움이 모든 항목이 설치 되어 있습니다.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 이상 Service Fabric 망상 조직 응용 프로그램을 배포 해야 합니다. [설치 버전 15.6.0][download-visual-studio] 이상은 다음 워크로드를 사용하도록 설정합니다.

* ASP.NET 및 웹 개발
* Azure 개발

## <a name="install-docker"></a>Docker 설치

Docker가 이미 설치되어 있으면 최신 버전인지 확인합니다. Docker는 새 버전 아웃 되었지만 최신 버전이 있는지 수동으로 확인 하는 경우 묻는 메시지가 될 수 있습니다.

#### <a name="install-docker-on-windows-10"></a>Windows 10에 Docker 설치

Service Fabric Mesh에서 사용하는 컨테이너화된 Service Fabric 앱을 지원하기 위해 [Docker Community Edition for Windows][download-docker] 최신 버전을 다운로드하여 설치합니다.

설치하는 동안 요청 시 **Linux 컨테이너 대신 Windows 컨테이너 사용**을 선택합니다.

Hyper-v 컴퓨터에서 활성화 되어 있지 않으면, Docker의 설치 관리자는 사용 하도록 제공 합니다. 메시지가 표시되면 **확인**을 클릭하여 설정합니다.

#### <a name="install-docker-on-windows-server-2016"></a>Windows Server 2016에 Docker 설치

Hyper-V 역할을 사용하도록 설정하지 않은 경우 관리자 권한으로 PowerShell을 열고 다음 명령을 실행하여 Hyper-V를 사용하도록 설정한 다음, 컴퓨터를 다시 시작합니다. 자세한 내용은 [Windows Server용 Docker Enterprise Edition][download-docker-server]을 참조하세요.

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

컴퓨터를 다시 시작합니다.

관리자 권한으로 PowerShell을 열고 다음 명령을 실행하여 Docker를 설치합니다.

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK 및 도구

다음 순서에 따라 Service Fabric Mesh 런타임, SDK 및 도구를 설치합니다.

1. 웹 플랫폼 설치 관리자를 사용하여 [Service Fabric Mesh SDK][download-sdkmesh]를 설치합니다. 그러면 Microsoft Azure Service Fabric SDK 및 런타임도 설치합니다.
2. Visual Studio Marketplace에서 [Visual Studio Service Fabric Mesh 도구(미리 보기) 확장][download-tools]을 설치합니다.

## <a name="build-a-cluster"></a>클러스터 빌드

> [!IMPORTANT]
> Docker는 클러스터를 빌드하기 전에 실행**해야** 합니다.
> 터미널 창을 열고 오류가 발생하는지를 확인하는 `docker ps`를 실행하여 Docker가 실행되는지 테스트합니다. 응답이 오류를 나타내지 않는 경우 Docker가 실행되고 클러스터를 빌드할 준비가 되었습니다.

> [!Note]
> Windows Fall Creators 업데이트(버전 1709) 머신에서 개발하는 경우 Windows 버전 1709 Docker 이미지만 사용할 수 있습니다.
> Windows 10 2018년 4월 업데이트(버전 1803) 머신에서 개발하는 경우 Windows 버전 1709 또는 1803의 Docker 이미지를 사용합니다.

Visual Studio를 사용 하는 경우 계정이 없는 경우 Visual Studio를 로컬 클러스터 만들기는 때문에이 섹션을 건너뛸 수 있습니다.

디버깅 성능을 만들고 한 번에 단일 Service Fabric 앱이 실행 되는 경우 최고 단일 노드 로컬 개발 클러스터를 만듭니다. 한 번에 여러 응용 프로그램을 실행 중인 경우에 다섯 개의 노드 로컬 개발 클러스터를 만듭니다. Service Fabric Mesh 프로젝트를 배포하거나 디버그할 때마다 클러스터가 실행되고 있어야 합니다.

런타임, SDK, Visual Studio 도구 및 Docker를 설치한 후에 Docker를 실행하고 개발 클러스터를 만듭니다.

1. PowerShell 창을 닫습니다.
2. 관리자 권한으로 새롭게 상승된 PowerShell 창을 엽니다. 이 단계는 최근에 설치한 Service Fabric 모듈을 로드하기 위해 필요합니다.
3. 다음 PowerShell 명령을 실행하여 개발 클러스터를 만듭니다.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. 로컬 클러스터 관리자 도구를 시작하려면 다음 PowerShell 명령을 실행합니다.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. 서비스 클러스터 관리자 도구를 실행하면(시스템 트레이에 표시됨) 해당 항목을 마우스 오른쪽 단추로 클릭하고 **로컬 클러스터 시작**을 클릭합니다.

![그림 1 - 로컬 클러스터 시작](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

이제 Service Fabric Mesh 응용 프로그램을 만들 준비가 되었습니다!

## <a name="next-steps"></a>다음 단계

[Azure Service Fabric 앱 만들기](service-fabric-mesh-tutorial-create-dotnetcore.md) 자습서를 읽습니다.

[일반적인 질문 및 알려진 문제](service-fabric-mesh-faq.md)에 대한 답변을 찾습니다.

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/