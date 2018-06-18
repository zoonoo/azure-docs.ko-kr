---
title: 클라우드에서 Kubernetes 개발 환경 만들기 | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
manager: douge
ms.openlocfilehash: 16ec493708f85e9b3819943e131b9f9c3649f27e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824641"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>빠른 시작: Azure Dev Spaces(.NET Core 및 Visual Studio)를 사용하여 Kubernetes 개발 환경 만들기

이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.

- Azure에서 관리되는 Kubernetes 클러스터를 사용하여 Azure Dev Spaces를 설정합니다.
- Visual Studio를 사용하여 컨테이너에서 반복적으로 코드를 개발합니다.
- 클러스터에서 실행되는 코드를 디버깅합니다.

> [!Note]
> **의문 사항이 있으면** 언제든지 [문제 해결](troubleshooting.md) 섹션을 참조하거나 이 페이지에 의견을 게시하세요. 또한 더 자세한 [자습서](get-started-netcore-visualstudio.md)를 시도해 볼 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

- EastUS, WestEurope 또는 CanadaEast 지역에서 Http 응용 프로그램 라우팅이 활성화된 상태로 Kubernetes 1.9.6을 실행 중인 Kubernetes 클러스터.

  ![Http 응용 프로그램 라우팅을 활성화해야 합니다.](media/common/Kubernetes-Create-Cluster-3.PNG)

- 설치된 Visual Studio 2017과 웹 개발 워크로드. 설치되어 있지 않으면 [여기](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)에서 다운로드하세요.

## <a name="set-up-azure-dev-spaces"></a>Azure Dev Spaces 설치

[Azure Dev Spaces용 Visual Studio 확장](https://aka.ms/get-azds-visualstudio)을 설치합니다.

## <a name="connect-to-a-cluster"></a>클러스터에 연결

다음으로, Azure Dev Spaces용 프로젝트를 만들고 구성합니다.

### <a name="create-an-aspnet-web-app"></a>ASP.NET 웹앱 만들기

Visual Studio 2017에서 새 프로젝트를 만듭니다. 현재 프로젝트는 **ASP.NET Core 웹 응용 프로그램**이어야 합니다. 프로젝트의 이름을 **webfrontend**로 지정합니다.

**웹 응용 프로그램 (모델-보기-컨트롤러)** 템플릿을 선택하고, **.NET Core** 및 **ASP.NET Core 2.0**을 대상으로 하고 있는지 확인합니다.

### <a name="create-a-dev-space-in-azure"></a>Azure에서 개발자 환경 만들기

방금 연 프로젝트를 사용하여 아래 그림과 같이 시작 설정 드롭다운에서 **Azure Dev Spaces**를 선택합니다.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

다음에 표시되는 대화 상자에서 적절한 계정으로 로그인했는지 확인한 다음, 기존 클러스터를 선택합니다.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

지금은 **공간** 드롭다운을 `default`로 설정해 둡니다. 공용 엔드포인트를 통해 웹앱에 액세스할 수 있도록 **공용 액세스 가능** 확인란을 선택합니다.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

**확인**을 클릭하여 클러스터를 선택하거나 만듭니다.

Azure Dev Spaces와 함께 작동하도록 구성되지 않은 클러스터를 선택하면 구성할 것인지를 묻는 메시지가 표시됩니다.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

**확인**을 선택합니다. 

### <a name="look-at-the-files-added-to-project"></a>프로젝트에 추가된 파일 확인
개발 환경이 생성될 때까지 기다리는 동안 Azure Dev Spaces를 사용하도록 선택할 때 프로젝트에 추가된 파일을 확인합니다.

- `charts`라는 폴더가 추가되었으며, 이 폴더 내에는 응용 프로그램에 대한 [Helm 차트](https://docs.helm.sh)가 스캐폴딩되어 있습니다. 이러한 파일은 개발 환경에 응용 프로그램을 배포하는 데 사용됩니다.
- `Dockerfile`에는 응용 프로그램을 표준 Docker 형식으로 패키징하는 데 필요한 정보가 있습니다.
- `azds.yaml`에는 개발 환경에서 필요로 하는 구성 정보(예: 응용 프로그램을 공용 엔드포인트를 통해 액세스할 수 있는지 여부)가 있습니다.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes에서 컨테이너 디버깅
개발 환경이 성공적으로 만들어지면 응용 프로그램을 디버깅할 수 있습니다. 코드에서 중단점을 설정합니다(예: `Message` 변수가 설정된 파일 `HomeController.cs`의 줄 20). **F5** 키를 클릭하여 디버깅을 시작합니다. 

Visual Studio는 개발 환경과 통신하여 응용 프로그램을 빌드 및 배포한 다음, 웹앱이 실행되는 브라우저를 엽니다. 컨테이너가 로컬에서 실행되는 것처럼 보일 수도 있지만, 실제로는 Azure의 개발 환경에서 실행됩니다. 로컬 호스트 주소인 이유는 Azure Dev Spaces가 Azure에서 실행 중인 컨테이너에 임시 SSH 터널을 생성하기 때문입니다.

중단점을 트리거하려면 페이지 상단에 있는 **정보** 링크를 클릭합니다. 호출 스택, 지역 변수, 예외 정보 등과 같은 코드가 로컬에서 실행되는 경우와 마찬가지로 디버그 정보에 대한 모든 액세스 권한을 갖습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [여러 컨테이너 작업 및 팀 개발](get-started-netcore-visualstudio.md#call-another-container)