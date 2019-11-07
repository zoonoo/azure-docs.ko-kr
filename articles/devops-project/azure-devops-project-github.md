---
title: '자습서: Azure DevOps Projects를 사용하여 기존 코드용 CI/CD 파이프라인 만들기'
description: Azure DevOps Projects를 사용하면 Azure를 쉽게 시작할 수 있습니다. DevOps Projects는 사용자 고유의 코드 및 GitHub 리포지토리를 사용하여 빠른 몇 단계로 Azure 서비스에서 앱을 시작하는 데 도움을 줍니다.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 15ac201a078864717d8e0079801507cf5fc0fe3b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481117"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>자습서: Azure DevOps Projects를 사용하여 기존 코드용 CI/CD 파이프라인 만들기

Azure DevOps Projects는 기존 코드와 Git 리포지토리를 가져오거나 샘플 애플리케이션을 선택하여 Azure에 CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인을 만드는 간소화된 환경을 제공합니다.

다음을 수행합니다.

> [!div class="checklist"]
> * DevOps Projects를 사용하여 CI/CD 파이프라인 만들기
> * GitHub 리포지토리에 대한 액세스 구성 및 프레임워크 선택
> * Azure DevOps 및 Azure 구독 구성 
> * GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포
> * Azure Pipelines CI/CD 파이프라인 검사
> * 리소스 정리

## <a name="prerequisites"></a>필수 조건

* Azure 구독. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료 구독을 구할 수 있습니다.
* .NET, Java, PHP, Node, Python 또는 정적 웹 코드를 포함하는 외부 Git 리포지토리 또는 GitHub에 액세스합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

Azure DevOps Projects는 Azure Pipelines에 CI/CD 파이프라인을 만듭니다. 새 Azure DevOps 조직을 만들거나 기존 조직을 사용할 수 있습니다. 또한 Azure DevOps Projects는 선택한 Azure 구독에서 Azure 리소스를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal 메뉴에서 **리소스 만들기**를 선택합니다.

   ![Azure Portal 메뉴 - 리소스 만들기](_img/azure-devops-project-github/createaresource.png)

3. **DevOps > DevOps 프로젝트**를 선택합니다.

   ![DevOps Projects 대시보드](_img/azure-devops-project-github/azuredashboard.png)

3. **사용자 코드 가져오기**를 선택한 다음, **다음**을 선택합니다.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>GitHub 리포지토리에 대한 액세스 구성 및 프레임워크 선택

1. **GitHub** 또는 외부 **Git**  코드 리포지토리 중 하나를 선택합니다. 이 자습서에서는 **GitHub**를 선택합니다. Azure가 GitHub 리포지토리에 액세스할 수 있도록 허용하려면 처음에 GitHub를 사용하여 인증해야 할 수도 있습니다.

2. **리포지토리** 및 **분기**를 선택하여 마치고 **다음**을 선택합니다.

3. Docker 컨테이너를 사용하는 경우 **컨테이너화된 앱입니까**를 **예**로 변경합니다. 이 자습서에서는 **아니오**를 선택한 상태로 두고 **다음**을 선택합니다. Docker 컨테이너 사용에 관해 자세히 알아보려면 **i** 아이콘을 마우스로 가리킵니다.

   ![.NET Framework](_img/azure-devops-project-github/appframework.png)

4. 드롭다운에서 **애플리케이션 런타임** 및 **프레임워크**를 선택한 다음, **다음**을 선택합니다. 이전에 선택한 애플리케이션 프레임워크는 여기에서 사용 가능한 Azure 서비스 배포 대상의 유형을 나타냅니다.

5. 애플리케이션을 배포할 **Azure 서비스**를 선택한 다음, **다음**을 선택합니다.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps 및 Azure 구독 구성

1. **프로젝트 이름**을 입력합니다.

2. 새 무료 **Azure DevOps 조직**을 만들거나 드롭다운에서 기존 조직을 선택합니다.

3. **Azure 구독**을 선택하고, **웹앱**에 사용할 이름을 입력하거나 기본값을 선택합니다. **위치**를 선택한 다음, **완료**를 선택합니다. 잠시 후에 DevOps Project 배포 개요가 Azure Portal에 표시됩니다.

4. **리소스로 이동**을 선택하여 DevOps Project 대시보드를 확인합니다. 오른쪽 위 모서리에서 빠른 액세스를 위해 **프로젝트**를 대시보드에 고정합니다. Azure DevOps Projects는 CI 빌드 및 릴리스 트리거를 자동으로 구성합니다. 코드가 GitHub 리포지토리 또는 다른 외부 리포지토리에 남아 있습니다. 샘플 앱은 **Azure DevOps 조직**의 리포지토리에서 설정됩니다. 빌드가 실행되고, 앱이 Azure에 배포됩니다.

   ![DevOps Projects 대시보드 보기](_img/azure-devops-project-github/projectsdashboard.png)

5. 이 대시보드에서는 코드 리포지토리, CI/CD 파이프라인 및 Azure의 애플리케이션에 가시성을 제공합니다. Azure 리소스의 오른쪽에서 **찾아보기**를 선택하여 실행 중인 앱을 확인합니다.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포

이제 웹 사이트에 최신 작업을 자동으로 배포하는 CI/CD 프로세스를 사용하여 앱에서 팀과 협업할 준비가 되었습니다. GitHub 리포지토리에 대한 각 변경은 Azure DevOps에서 빌드를 시작하고, CD 파이프라인은 Azure에 배포를 실행합니다.

1. DevOps Project 대시보드에서 **리포지토리**를 선택합니다. GitHub 리포지토리가 새 브라우저 탭에서 열립니다. 애플리케이션을 변경한 다음, **변경 내용 커밋**을 클릭합니다.

2. 잠시 후 Azure Pipelines에서 빌드가 시작됩니다. 프로젝트 대시보드에서 **빌드 파이프라인**을 선택하면 DevOps Projects 대시보드에서 빌드 상태를 모니터링하거나 Azure DevOps 조직에서 모니터링할 수 있습니다.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD 파이프라인 검사

Azure DevOps Projects는 Azure Pipelines에서 CI/CD 파이프라인을 자동으로 구성합니다. 필요에 따라 파이프라인을 탐색하고 사용자 지정합니다. 빌드 및 릴리스 파이프라인에 익숙해지려면 다음을 수행합니다.

1. DevOps Projects 대시보드에서 **빌드 파이프라인**을 선택합니다.

2. **Azure Pipelines** 페이지가 열리면 최신 빌드의 기록과 각 빌드에 대한 상태가 표시됩니다.

   ![Azure DevOps 파이프라인 빌드](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. **빌드** 페이지의 오른쪽 위 모서리에는 현재 빌드를 **편집**하는 옵션, 새 빌드를 큐에 대기하는 **큐**, 더 많은 옵션이 포함된 메뉴를 여는 줄임표( **&#8942;** )가 있습니다. **편집**을 선택합니다.

4. 빌드는 리포지토리에서 원본 가져오기, 종속성 복원 및 배포에 사용된 출력 게시 등 다양한 작업을 수행합니다. **이름**의 오른쪽에서 빌드 파이프라인 이름을 더 설명적인 이름으로 변경합니다. **저장 및 큐에 넣기**, **저장**을 차례로 선택하고, 주석을 그대로 둔 다음, **저장**을 다시 선택합니다.

   ![Azure DevOps 빌드 페이지](_img/azure-devops-project-github/buildpage.png)

5. 빌드에 대한 최근 변경 내용의 감사 내역을 보려면 **기록** 탭을 선택합니다. Azure DevOps는 빌드 파이프라인에 대한 모든 변경을 모니터링하고 버전을 비교할 수 있도록 합니다.

6. **트리거** 탭을 선택합니다. Azure DevOps Project는 일부 기본 설정을 사용하여 CI 트리거를 자동으로 만듭니다. **지속적인 통합 활성화**와 같은 트리거를 설정하여 코드 변경 내용이 커밋될 때마다 빌드를 실행하도록 설정하거나 빌드를 특정 시간에 실행하도록 예약할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 자습서에서 만든 Azure App Service와 관련 리소스를 삭제할 수 있습니다. 이렇게 하려면 DevOps Projects 대시보드의 **삭제** 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서 CI/CD 프로세스를 구성한 경우 빌드 및 릴리스 파이프라인이 Azure DevOps Projects에서 자동으로 생성됐습니다. 팀의 요구를 충족하려면 이러한 빌드 및 릴리스 파이프라인을 수정할 수 있습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
>  * DevOps Projects를 사용하여 CI/CD 파이프라인 만들기
> * GitHub 리포지토리에 대한 액세스 구성 및 프레임워크 선택
> * Azure DevOps 및 Azure 구독 구성
> * GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포
> * Azure Pipelines CI/CD 파이프라인 검사
> * 리소스 정리

CI/CD 파이프라인에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [다단계 CD(연속 배포) 파이프라인 정의](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

애플리케이션 모니터링에 대해 자세히 알아보려면 다음을 참조하세요.
  
 > [!div class="nextstepaction"]
 > [Azure Monitor란?](https://docs.microsoft.com/azure/azure-monitor/overview)
