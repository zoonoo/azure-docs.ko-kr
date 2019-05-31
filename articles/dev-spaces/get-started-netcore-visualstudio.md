---
title: .NET Core 및 Visual Studio를 사용하여 클라우드에서 Kubernetes 개발 환경 만들기
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 39948479cc563d2f622763ea7b4d09910ffc494c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779864"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Azure Dev Spaces에서 .NET Core 및 Visual Studio를 사용하여 시작

이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.

- Azure에서 관리되는 Kubernetes 클러스터를 사용하여 Azure Dev Spaces를 설정합니다.
- Visual Studio를 사용하여 컨테이너에서 반복적으로 코드를 개발합니다.
- 독립적으로 별도의 두 서비스를 개발하고, Kubernetes의 DNS 서비스 검색을 사용하여 다른 서비스를 호출합니다.
- 팀 환경에서 코드를 생산적으로 개발하고 테스트합니다.

> [!Note]
> **의문 사항이 있으면** 언제든지 [문제 해결](troubleshooting.md) 섹션을 참조하세요.


## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Azure Dev Space에 사용하도록 설정된 Kubernetes 클러스터 만들기

1. [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.
1. **리소스 만들기**를 선택하고 **Kubernetes**를 검색한 후 **Kubernetes Service** > **만들기**를 선택합니다.

   *Kubernetes 클러스터 만들기* 양식의 각 머리글 아래에서 다음 단계를 수행하고 선택한 [지역에서 Azure Dev Spaces를 지원](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams)하는지 확인합니다.

   - **프로젝트 세부 정보**: Azure 구독과 새로운 또는 기존 Azure 리소스 그룹을 선택합니다.
   - **클러스터 세부 정보**: AKS 클러스터의 이름, 지역, 버전 및 DNS 이름 접두사를 입력합니다.
   - **규모**: AKS 에이전트 노드의 VM 크기 및 노드 수를 선택합니다. Azure Dev Spaces를 시작하는 경우 노드 하나만 있으면 모든 기능을 탐색할 수 있습니다. 노드 수는 클러스터 배포 후 언제든지 쉽게 조정할 수 있습니다. AKS 클러스터를 만든 후에는 VM 크기를 변경할 수 없습니다. 하지만 규모를 확장해야 하는 경우 AKS 클러스터를 배포한 후 간단하게 더 큰 VM이 있는 새 AKS 클러스터를 만들고 Dev Spaces를 사용하여 해당 클러스터를 다시 배포하면 됩니다.

   ![Kubernetes 구성 설정](media/common/Kubernetes-Create-Cluster-2.PNG)


   완료되면 **다음: 인증**을 선택합니다.

1. RBAC(역할 기반 액세스 제어)에 대해 원하는 설정을 선택합니다. Azure Dev Spaces는 RBAC를 사용하거나 사용하지 않도록 설정한 클러스터를 지원합니다.

    ![RBAC 설정](media/common/k8s-RBAC.PNG)

1. **검토 + 만들기**를 선택한 후 완료되면 **만들기**를 선택합니다.

## <a name="get-the-visual-studio-tools"></a>Visual Studio 도구 가져오기
1. 최신 버전의 [Visual Studio 2017](https://www.visualstudio.com/vs/) 설치
1. Visual Studio 설치 관리자에서 다음 워크로드가 선택되어 있는지 확인합니다.
    * ASP.NET 및 웹 개발
1. [Visual Studio Tools for Kubernetes](https://aka.ms/get-azds-visualstudio) 설치

## <a name="create-a-web-app-running-in-a-container"></a>컨테이너에서 실행되는 웹앱 만들기

이 섹션에서는 ASP.NET Core 웹앱을 만들어 Kubernetes의 컨테이너에서 실행합니다.

### <a name="create-an-aspnet-web-app"></a>ASP.NET 웹앱 만들기

Visual Studio 2017에서 새 프로젝트를 만듭니다. 현재 프로젝트는 **ASP.NET Core 웹 애플리케이션**이어야 합니다. 프로젝트의 이름을 '**webfrontend**'로 지정합니다.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

대화 상자의 상단에 있는 두 개의 드롭다운에서 **웹 애플리케이션 (모델-보기-컨트롤러)** 템플릿을 선택하고, **.NET Core** 및 **ASP.NET Core 2.0**을 대상으로 하고 있는지 확인합니다. **확인**을 클릭하여 프로젝트를 만듭니다.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>AKS 클러스터에 대한 개발 환경을 사용하도록 설정

방금 만든 프로젝트를 사용하여 아래 그림과 같이 시작 설정 드롭다운에서 **Azure Dev Spaces**를 선택합니다.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

다음에 표시되는 대화 상자에서 적절한 계정으로 로그인했는지 확인한 다음, 기존 Kubernetes 클러스터를 선택합니다.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

지금은 **공간** 드롭다운을 기본값인 `default`로 그대로 둡니다. 나중에 이 옵션에 대해 자세히 알아보겠습니다. 공용 엔드포인트를 통해 웹앱에 액세스할 수 있도록 **공용 액세스 가능** 확인란을 선택합니다. 이 설정은 필요한 것은 아니지만 이 연습의 뒷부분에서 몇 가지 개념을 보여주는 데 도움이 될 것입니다. 두 경우 모두 Visual Studio를 사용하여 웹 사이트를 디버깅할 수는 있으니 걱정하지 마세요.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

클러스터를 선택하거나 만들려면 **확인**을 클릭합니다.

Azure Dev Spaces에서 작동하도록 설정되지 않은 클러스터를 선택하면 구성할 것인지 묻는 메시지가 표시됩니다.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

**확인**을 선택합니다.

> [!IMPORTANT]
> Azure Dev Spaces 구성 프로세스는 클러스터에서 `azds` 네임스페이스를 제거합니다(있는 경우).

 이 작업을 수행하기 위한 백그라운드 작업이 시작됩니다. 완료하는 데 몇 분 정도 걸립니다. 아직 생성 중인지 확인하려면 다음 그림과 같이 상태 표시줄의 왼쪽 아래 모서리에 있는 **백그라운드 작업** 아이콘 위로 포인터를 이동합니다.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> 개발 환경이 성공적으로 만들어질 때까지는 애플리케이션을 디버깅할 수 없습니다.

### <a name="look-at-the-files-added-to-project"></a>프로젝트에 추가된 파일 확인
개발 환경이 생성될 때까지 기다리는 동안 개발 환경을 사용하도록 선택할 때 프로젝트에 추가된 파일을 확인합니다.

먼저, `charts`라는 폴더가 추가되었으며, 이 폴더 내에는 애플리케이션에 대한 [Helm 차트](https://docs.helm.sh)가 스캐폴딩되어 있습니다. 이러한 파일은 개발 환경에 애플리케이션을 배포하는 데 사용됩니다.

`Dockerfile`이라는 파일이 추가되었습니다. 이 파일에는 애플리케이션을 표준 Docker 형식으로 패키징하는 데 필요한 정보가 있습니다.

마지막으로 개발 환경에서 필요한 개발 시간 구성을 포함하는 `azds.yaml`라는 파일이 표시됩니다.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes에서 컨테이너 디버깅
개발 환경이 성공적으로 만들어지면 애플리케이션을 디버깅할 수 있습니다. 코드에서 중단점을 설정합니다(예: `Message` 변수가 설정된 파일 `HomeController.cs`의 줄 20). **F5** 키를 클릭하여 디버깅을 시작합니다. 

Visual Studio는 개발 환경과 통신하여 애플리케이션을 빌드 및 배포한 다음, 웹앱이 실행되는 브라우저를 엽니다. 컨테이너가 로컬에서 실행되는 것처럼 보일 수도 있지만, 실제로는 Azure의 개발 환경에서 실행됩니다. 로컬 호스트 주소인 이유는 Azure Dev Spaces가 AKS에서 실행 중인 컨테이너에 임시 SSH 터널을 생성하기 때문입니다.

중단점을 트리거하려면 페이지 상단에 있는 **정보** 링크를 클릭합니다. 호출 스택, 지역 변수, 예외 정보 등과 같은 코드가 로컬에서 실행되는 경우와 마찬가지로 디버그 정보에 대한 모든 액세스 권한을 갖습니다.

## <a name="iteratively-develop-code"></a>반복적으로 코드 개발

Azure Dev Spaces는 Kubernetes에서 단순히 코드를 실행하는 것이 아니라, 클라우드의 Kubernetes 환경에서 코드 변경 내용을 신속하고 반복적으로 확인할 수 있게 해주는 것입니다.

### <a name="update-a-content-file"></a>콘텐츠 파일 업데이트
1. `./Views/Home/Index.cshtml` 파일을 찾고 이 HTML 파일을 편집합니다. 예를 들어 `<h2>Application uses</h2>`를 읽는 행 70을 `<h2>Hello k8s in Azure!</h2>`로 변경합니다.
1. 파일을 저장합니다.
1. 브라우저로 이동하여 페이지를 새로 고칩니다. 웹 페이지에 업데이트된 HTML이 표시되어야 합니다.

어떻게 된 건가요? HTML 및 CSS와 같은 콘텐츠 파일을 편집하는 경우 .NET Core 웹앱에서 다시 컴파일을 수행할 필요가 없기 때문에 활성 F5 세션은 수정된 콘텐츠 파일을 AKS에서 실행 중인 컨테이너에 자동으로 동기화하므로 콘텐츠 편집 내용을 바로 볼 수 있습니다.

### <a name="update-a-code-file"></a>코드 파일 업데이트
.NET Core 앱이 업데이트된 애플리케이션 이진 파일을 다시 빌드하고 생성해야 하기 때문에 코드 파일을 업데이트하려면 작업이 좀 더 필요합니다.

1. Visual Studio에서 디버거를 중지합니다.
1. `Controllers/HomeController.cs`라는 코드 파일을 열고, 정보 페이지(`ViewData["Message"] = "Your application description page.";`)에 표시될 메시지를 편집합니다.
1. 파일을 저장합니다.
1. **F5** 키를 눌러 다시 디버깅을 시작합니다. 

코드 편집이 완료될 때마다 상당한 시간이 소요되는 새 컨테이너 이미지 다시 빌드 및 다시 배포 작업을 수행하는 대신 Azure Dev Spaces는 기존 컨테이너 내에서 코드를 점진적으로 다시 컴파일하여 더 빠른 편집/디버그 루프를 제공합니다.

브라우저에서 웹앱을 새로 고치고 정보 페이지로 이동합니다. 사용자 지정 메시지가 UI에 표시되어야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [다중 서비스 개발에 대해 알아보기](multi-service-netcore-visualstudio.md)
