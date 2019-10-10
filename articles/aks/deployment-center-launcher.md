---
title: Azure Kubernetes 배포 센터
description: Azure DevOps의 배포 센터는 애플리케이션에 대한 강력한 Azure DevOps 파이프라인의 설정을 간소화합니다.
ms.author: puagarw
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 5384180720d391c6b4ae830f9316a70e80003063
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972971"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Azure Kubernetes 배포 센터

Azure DevOps의 배포 센터는 애플리케이션에 대한 강력한 Azure DevOps 파이프라인의 설정을 간소화합니다. 기본적으로 배포 센터는 애플리케이션 업데이트를 Kubernetes 클러스터에 배포하도록 Azure DevOps 파이프라인을 구성합니다. 기본 구성된 Azure DevOps 파이프라인을 확장하고, 더 다양한 기능, 즉 배포하기 전에 승인받고, 추가 Azure 리소스를 프로비저닝하고, 스크립트를 실행하고, 애플리케이션을 업그레이드하고, 더 많은 유효성 검사 테스트를 실행할 수 있는 기능을 추가할 수도 있습니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 애플리케이션 업데이트를 Kubernetes 클러스터에 배포하도록 Azure DevOps 파이프라인을 구성합니다.
> * CI(지속적인 통합) 파이프라인을 검사합니다.
> * CD(지속적인 업데이트) 파이프라인을 검사합니다.
> * 리소스를 정리합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료 구독을 구할 수 있습니다.

* AKS(Azure Kubernetes Service) 클러스터

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Portal의 메뉴 모음 오른쪽에서 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 옵션을 선택합니다.

1. AKS 클러스터를 만들려면 다음 명령을 실행합니다.

    ```cmd
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Kubernetes 클러스터에 애플리케이션 업데이트 배포

1. 이전 섹션에서 만든 리소스 그룹으로 이동합니다.

1. AKS 클러스터를 선택한 다음, 왼쪽 블레이드에서 **배포 센터(미리 보기)** 를 클릭합니다. **시작**을 선택합니다.

   ![설정](media/deployment-center-launcher/settings.png)

1. 코드의 위치를 선택하고, **다음**을 선택합니다. 그런 다음, 현재 지원되는 **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** 또는 **GitHub** 리포지토리 중 하나를 선택합니다.

    Azure Repos는 코드를 관리하는 데 도움이 되는 버전 제어 도구 세트입니다. 소프트웨어 프로젝트의 크기에 관계없이 가능한 한 빨리 버전 제어를 사용하는 것이 좋습니다.

    - **Azure Repos**: 기존 프로젝트 및 조직에서 리포지토리를 선택합니다.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: GitHub 계정의 리포지토리를 인증하고 선택합니다.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. 배포 센터는 리포지토리를 분석하고 Dockerfile을 검색합니다. Dockerfile을 업데이트하려면 식별된 포트 번호를 편집할 수 있습니다.

    ![애플리케이션 설정](media/deployment-center-launcher/application-settings.png)

    리포지토리에 Dockerfile이 없으면 시스템에서 Dockerfile을 커밋하라는 메시지가 표시됩니다.

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. 기존 컨테이너 레지스트리를 선택하거나 새로 만든 다음, **마침**을 선택합니다. 파이프라인이 자동으로 만들어지고, [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops)에서 빌드를 큐에 넣습니다.

    Azure Pipelines는 코드 프로젝트를 자동으로 빌드 및 테스트하여 다른 사용자가 사용할 수 있도록 하는 데 사용할 수 있는 클라우드 서비스입니다. Azure Pipelines는 지속적인 통합과 지속적인 업데이트를 결합하여 코드를 지속적이고 일관되게 테스트 및 빌드하고 모든 대상에 제공합니다.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. 링크를 선택하여 진행 중인 파이프라인을 확인합니다.

1. 배포가 완료되면 성공적인 로그가 표시됩니다.

    ![로그](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>CI 파이프라인 검토

배포 센터는 Azure DevOps 조직의 CI/CD 파이프라인을 자동으로 구성합니다. 파이프라인을 검색하고 사용자 지정할 수 있습니다.

1. 배포 센터 대시보드로 이동합니다.  

1. 성공적인 로그 목록에서 빌드 번호를 선택하여 프로젝트에 대한 빌드 파이프라인을 확인합니다.

1. 오른쪽 위 모서리에서 줄임표 (...)를 선택합니다. 새 빌드 큐 대기, 빌드 보존 및 빌드 파이프라인 편집과 같은 몇 가지 옵션이 메뉴에 표시됩니다. **파이프라인 편집**을 선택합니다. 

1. 이 창에서는 빌드 파이프라인의 여러 작업을 검사할 수 있습니다. 빌드는 Git 리포지토리에서 원본 수집, 이미지 만들기, 컨테이너 레지스트리에 이미지 푸시 및 배포에 사용되는 출력 게시와 같은 다양한 작업을 수행합니다.

1. 파이프라인의 맨 위에 있는 빌드 파이프라인 이름을 선택합니다.

1. 빌드 파이프라인 이름을 더 구체적인 설명이 포함된 이름으로 변경하고, **저장 및 큐에 넣기**, **저장**을 차례로 선택합니다.

1. 빌드 파이프라인 아래에서 **기록**을 선택합니다. 이 창에는 최근의 빌드 변경에 대한 감사 로그가 표시됩니다. Azure DevOps는 빌드 파이프라인에 대한 모든 변경을 모니터링하고 버전을 비교할 수 있도록 합니다.

1. **트리거**를 선택합니다. CI 프로세스에서 분기를 포함하거나 제외할 수 있습니다.

1. **보존**을 선택합니다. 시나리오에 따라 여러 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

## <a name="examine-the-cd-pipeline"></a>CD 파이프라인 검토

배포 센터는 Azure DevOps 조직과 Azure 구독 간의 관계를 자동으로 만들고 구성합니다. Azure DevOps를 사용하여 Azure 구독을 인증하기 위한 Azure 서비스 연결을 설정하는 단계가 포함됩니다. 또한 자동화된 프로세스에서 지속적인 업데이트를 Azure에 제공하는 릴리스 파이프라인을 만듭니다.

1. **파이프라인**을 선택한 다음, **릴리스**를 선택합니다.

1. 릴리스 파이프라인을 편집하려면 **편집**을 선택합니다.

1. **아티팩트** 목록에서 **놓기**를 선택합니다. 이전 단계에서 검사한 생성 파이프라은 아티팩트에 사용되는 출력을 생성합니다. 

1. **놓기** 옵션의 오른쪽에서 **지속적인 배포** 트리거를 선택합니다. 이 릴리스 파이프라인에는 새 빌드 아티팩트를 사용할 수 있을 때마다 배포를 실행하는 CD 트리거를 사용하도록 설정되어 있습니다. 또한 배포를 수동으로 실행하도록 요구하는 트리거를 사용하지 않도록 설정할 수도 있습니다.

1. 파이프라인의 모든 작업을 검사하려면 **작업**을 선택합니다. 릴리스는 tiller 환경을 설정하고, `imagePullSecrets` 매개 변수를 구성하며, Helm 도구를 설치하고, Helm 차트를 Kubernetes 클러스터에 배포합니다.

1. 릴리스 기록을 보려면 **릴리스 보기**를 선택합니다.

1. 요약을 보려면 **릴리스**를 선택합니다. 릴리스 요약, 관련 작업 항목 및 테스트와 같은 여러 메뉴를 검색하는 단계 중 하나를 선택합니다. 

1. **커밋**을 선택합니다. 이 보기에는 이 배포와 관련된 코드 커밋이 표시됩니다. 릴리스를 비교하여 배포 간의 커밋 차이를 확인합니다.

1. **로그**를 선택합니다. 로그에는 배포 중 및 배포 후에 확인할 수 있는 유용한 배포 정보가 포함되어 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 만든 관련 리소스를 삭제할 수 있습니다. DevOps Projects 대시보드에서 삭제 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계

팀의 요구를 충족하려면 이러한 빌드 및 릴리스 파이프라인을 수정할 수 있습니다. 또는 이 CI/CD 모델을 다른 파이프라인의 템플릿으로 사용할 수 있습니다.
