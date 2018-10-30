---
title: Azure DevOps 프로젝트를 사용하여 기존 코드용 CI/CD 파이프라인 만들기 | Azure DevOps Services 자습서
description: DevOps Project를 사용하면 Azure를 쉽게 시작할 수 있습니다. 빠른 몇 단계에 원하는 Azure 서비스에서 앱을 시작하려면 사용자 고유의 코드 및 GitHub 리포지토리를 사용할 수 있습니다.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 02b6823a46c94edb0ba28c7a2a8b9ae0efc44ae8
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406095"
---
# <a name="tutorial--create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>자습서: Azure DevOps Project를 사용하여 기존 코드용 CI/CD 파이프라인 만들기

Azure DevOps 프로젝트는 기존 코드와 Git 리포지토리를 가져오거나 샘플 응용 프로그램 중 하나를 선택하여 Azure에 CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인을 만드는 간소화된 환경을 제공합니다.

다음을 수행합니다.

> [!div class="checklist"]
> * Azure DevOps 프로젝트 만들기
> * GitHub 리포지토리에 대한 액세스 구성 및 프레임워크 선택
> * Azure DevOps Services 및 Azure 구독 구성 
> * GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포
> * Azure DevOps Services CI/CD 파이프라인 검토
> * Azure Application Insights 모니터링 구성

## <a name="prerequisites"></a>필수 조건

* Azure 구독. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료 구독을 구할 수 있습니다.
* .NET, Java, PHP, Node, Python 또는 정적 웹 코드를 포함하는 외부 Git 리포지토리 또는 GitHub에 액세스합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

Azure DevOps 프로젝트는 Azure DevOps Services에 CI/CD 파이프라인을 만듭니다.  **새 Azure DevOps Services** 조직을 만들거나 **기존 조직**을 사용할 수 있습니다.  또한 Azure DevOps 프로젝트는 선택한 **Azure 구독**에서 **Azure 리소스**를 만듭니다.

1. [Microsoft Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **+새로 만들기** 아이콘을 선택한 다음, **DevOps 프로젝트**를 검색합니다.  **만들기**를 선택합니다.

    ![지속적인 업데이트 시작](_img/azure-devops-project-github/fullbrowser.png)

1. **사용자 고유의 코드 가져오기**를 선택합니다.  작업을 완료하면 **다음**을 선택합니다.

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>GitHub 리포지토리에 대한 액세스 구성 및 프레임워크 선택

1. **GitHub**를 선택합니다.  필요에 따라 **외부 Git 리포지토리**를 선택할 수 있습니다.  **리포지토리** 및 응용 프로그램을 포함하는 **분기**를 선택합니다.

1. **웹 프레임워크**를 선택합니다.  작업을 완료하면 **다음**을 선택합니다.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

1. 이전 단계에서 선택한 응용 프로그램 프레임워크는 여기에서 사용 가능한 Azure 서비스 배포 대상의 유형을 나타냅니다.  선택한 **대상 서비스**를 선택합니다.  작업을 완료하면 **다음**을 선택합니다.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Azure DevOps Services 및 Azure 구독 구성 

1. **새** Azure DevOps Services 조직을 만들거나 **기존** 조직을 선택합니다.  Azure DevOps 프로젝트의 **이름**을 선택합니다.  **Azure 구독** 및 **위치**를 선택하고 응용 프로그램의 **이름**을 선택합니다.  작업을 완료하면 **완료**를 선택합니다.

1. 잠시 후 **Azure DevOps 프로젝트 대시보드**가 Azure Portal에 로드됩니다.  샘플 응용 프로그램이 Azure DevOps Services 조직의 리포지토리에서 설정되고, 빌드가 실행되고, 응용 프로그램이 Azure에 배포됩니다.  이 대시보드에서는 GitHub **코드 리포지토리**, **Azure DevOps Services CI/CD 파이프라인** 및 **Azure의 응용 프로그램**에 가시성을 제공합니다.  대시보드의 오른쪽에서 **찾아보기**를 선택하여 실행 중인 응용 프로그램을 봅니다.

    ![대시보드 보기](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps 프로젝트는 CI 빌드 및 릴리스 트리거를 자동으로 구성합니다.  코드에 GitHub 또는 다른 외부 저장소에 남아 있습니다.  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포 

이제 웹 사이트에 최신 작업을 자동으로 배포하는 CI/CD 프로세스를 사용하여 앱에서 팀과 협업할 준비가 되었습니다.  GitHub 리포지토리에 대한 각 변경은 Azure DevOps에서 빌드를 시작하고, Azure DevOps CD 파이프라인은 Azure에 배포를 실행합니다.

1.  응용 프로그램을 변경하고 GitHub 리포지토리에 변경 내용을 **커밋**합니다.
2.  몇 분 내에 빌드가 Azure DevOps Services에서 시작됩니다.  Azure DevOps Services 조직이 있는 브라우저에서 또는 Azure DevOps 프로젝트 대시보드를 사용하여 빌드 상태를 모니터링할 수 있습니다.
3.  빌드가 완료되면 브라우저에서 **응용 프로그램을 새로 고침**하여 변경 내용을 확인합니다.

## <a name="examine-the-azure-devops-services-cicd-pipeline"></a>Azure DevOps Services CI/CD 파이프라인 검토

Azure DevOps 프로젝트는 Azure DevOps Services 조직에서 Azure DevOps Services CI/CD 파이프라인을 자동으로 구성합니다.  필요에 따라 파이프라인을 탐색하고 사용자 지정합니다.  Azure DevOps Services 빌드 및 릴리스 파이프라인을 숙지하려면 다음 단계를 수행합니다.

1. Azure DevOps 프로젝트 대시보드의 **위쪽**에서 **빌드 파이프라인**을 선택합니다.  이 링크는 브라우저 탭 및 새 프로젝트에 대한 Azure DevOps Services 빌드 파이프라인을 엽니다.

1. **상태** 필드 옆에 빌드 파이프라인의 오른쪽으로 마우스 커서를 이동합니다. 나타나는 **줄임표**를 선택합니다.  이 작업은 새 빌드 큐, 빌드 일시 중지 및 빌드 파이프라인 편집과 같은 여러 활동을 시작할 수 있는 메뉴를 엽니다.

1. **편집**을 선택합니다.

1. 이 보기에서 빌드 파이프라인의 **다양한 작업을 검사합니다**.  빌드는 Git 리포지토리에서 원본 가져오기, 종속성 복원 및 배포에 사용된 출력 게시 등 다양한 작업을 수행합니다.

1. 빌드 파이프라인의 맨 위에서 **빌드 파이프라인 이름**을 선택합니다.

1. 빌드 파이프라인의 **이름**을 좀 더 구체적인 것으로 변경합니다.  **저장 및 큐**를 선택한 다음, **저장**을 선택합니다.

1. 빌드 파이프라인 이름에서 **기록**을 선택합니다.  빌드에 대한 최근 변경 내용의 감사 내역이 표시됩니다.  Azure DevOps Services는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

1. **트리거**를 선택합니다.  Azure DevOps 프로젝트는 CI 트리거를 자동으로 생성하면 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다.  필요에 따라 CI 프로세스에서 분기를 포함할지를 선택할 수 있습니다.

1. **보존**을 선택합니다.  시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

1. **빌드 및 릴리스**를 선택한 다음, **릴리스**를 선택합니다.  Azure DevOps 프로젝트는 Azure에 배포를 관리하는 Azure DevOps Services 릴리스 파이프라인을 만들었습니다.

1. 브라우저의 왼쪽에서 릴리스 파이프라인 옆에 있는 **줄임표**를 선택한 다음, **편집**을 선택합니다.

1. 릴리스 파이프라인에는 릴리스 프로세스를 정의하는 **파이프라인**이 포함됩니다.  **아티팩트** 아래에서 **드롭**을 선택합니다.  이전 단계에서 검사한 빌드 파이프라인이 아티팩트에 사용된 출력을 생성합니다. 

1. **Drop** 아이콘의 오른쪽에서 **지속적인 배포 트리거**를 선택합니다.  이 릴리스 파이프라인은 새 빌드 아티팩트를 사용할 수 있을 때마다 배포를 실행하는 CD 트리거를 사용하도록 설정했습니다.  필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필수적입니다. 

1. 브라우저의 왼쪽에서 **작업**을 선택합니다.  작업은 배포 프로세스가 수행하는 활동입니다.  이 예제에서는 **Azure 앱 서비스**에 배포하기 위해 작업을 만들었습니다.

1. 브라우저의 오른쪽에서 **릴리스 보기**를 선택합니다.  이 보기에는 릴리스의 기록이 표시됩니다.

1. 한 릴리스 옆에 있는 **줄임표**를 선택하고 **열기**를 선택합니다.  릴리스 요약, 연결된 작업 항목 및 테스트 등 여러 메뉴를 이 보기에서 탐색할 수 있습니다.

1. **커밋**을 선택합니다.  이 보기에는 특정 배포와 연결된 코드 커밋이 표시됩니다. 

1. **로그**를 선택합니다.  로그에는 배포 프로세스에 대한 유용한 정보가 포함됩니다.  배포 도중 및 이후 모두에서 로그를 볼 수 있습니다.

## <a name="configure-azure-application-insights-monitoring"></a>Azure Application Insights 모니터링 구성

Azure Application Insights를 사용하면 응용 프로그램의 성능 및 사용량을 쉽게 모니터링할 수 있습니다.  Azure DevOps 프로젝트는 자동으로 응용 프로그램에 대한 Application Insights 리소스를 구성했습니다.  필요에 따라 다양한 경고 및 모니터링 기능을 추가로 구성할 수 있습니다.

1. Azure Portal의 **Azure DevOps 프로젝트** 대시보드로 이동합니다.  대시보드의 오른쪽 아래에서 앱에 대한 **Application Insights** 링크를 선택합니다.

1. **Application Insights** 블레이드가 Azure Portal에서 열립니다.  이 보기에는 앱에 대한 사용량, 성능 및 가용성 모니터링 정보가 포함됩니다.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. **시간 범위**를 선택한 다음, **지난 시간**을 선택합니다.  **업데이트**를 선택하여 결과를 필터링합니다.  이제 지난 60분의 모든 활동이 표시됩니다.  **x**를 선택하여 시간 범위를 종료합니다.

1. **경고**을 선택한 다음, **+ 메트릭 경고 추가**를 선택합니다.  

1. 경고에 대한 **이름**을 입력합니다.

1. **원본 변경**을 위한 드롭다운을 선택합니다.  **App Service 리소스**
<!-- Could you please confirm if this should be "Source Alter on" instead of "Source Alert on"? -->를 선택합니다.
1. 기본 경고는 **1초 보다 큰 서버 응답 시간**의 경우에 해당합니다.  **메트릭** 드롭다운을 선택하여 다양한 경고 메트릭을 검사합니다.  다양한 경고를 쉽게 구성하여 앱의 모니터링 기능을 개선할 수 있습니다.

1. **이메일 소유자, 기여자 및 구독자를 통해 알림** 확인란을 선택합니다.  필요에 따라 Azure 논리 앱을 실행하여 경고가 발생하면 추가 작업을 수행할 수 있습니다.

1. **확인**을 선택하여 경고를 만듭니다.  곧 경고가 대시보드에서 활성으로 표시됩니다.  경고 영역을 **종료**하고 **Application Insights 블레이드**로 돌아갑니다.

1. **가용성**을 선택한 다음, **+ 테스트 추가**를 선택합니다. 

1. **테스트 이름**을 입력한 다음, **만들기**를 선택합니다.  응용 프로그램의 가용성을 확인하려면 간단한 ping 테스트를 생성합니다.  몇 분 후 테스트 결과를 사용할 수 있고, Application Insights 대시보드에 가용성 상태가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 경우 Azure DevOps 프로젝트 대시보드에서 **삭제** 기능을 사용하여 이 빠른 시작에서 만든 Azure 앱 서비스 및 관련 리소스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서 CI/CD 프로세스를 구성한 경우 빌드 및 릴리스 파이프라인이 Azure DevOps 프로젝트에서 자동으로 생성됐습니다. 팀의 요구를 충족하려면 이러한 빌드 및 릴리스 파이프라인을 수정할 수 있습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure DevOps 프로젝트 만들기
> * GitHub 리포지토리에 대한 액세스 구성 및 프레임워크 선택
> * Azure DevOps Services 및 Azure 구독 구성 
> * GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포
> * Azure DevOps Services CI/CD 파이프라인 검토
> * Azure Application Insights 모니터링 구성

Azure DevOps Services CI/CD 파이프라인에 대해 자세히 알아보려면 이 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [CD 프로세스 사용자 지정](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
