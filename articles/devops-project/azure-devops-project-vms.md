---
title: Azure DevOps 프로젝트를 사용하여 Azure Virtual Machines에 ASP.NET 앱 배포 | Azure DevOps Services 자습서
description: DevOps Project를 사용하면 Azure를 쉽게 시작할 수 있습니다. Azure DevOps 프로젝트를 사용하면 몇 가지 빠른 단계로 Azure Virtual Machines에 ASP.NET 앱을 쉽게 배포할 수 있습니다.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b05e2c2c46aa9bfa8c92d3d3c5c83d018c547b9f
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299137"
---
# <a name="tutorial--deploy-your-aspnet-app-to-azure-virtual-machines-with-the-azure-devops-project"></a>자습서: Azure DevOps 프로젝트를 사용하여 Azure Virtual Machines에 ASP.NET 앱 배포

Azure DevOps 프로젝트는 기존 코드와 Git 리포지토리를 가져오거나 샘플 응용 프로그램 중 하나를 선택하여 Azure에 CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인을 만드는 간소화된 환경을 제공합니다.  DevOps 프로젝트는 자동으로 새 Azure Virtual Machine 같은 Azure 리소스를 만들고, CI의 빌드 파이프라인을 포함하는 Azure DevOps의 릴리스 파이프라인을 만들고 구성하고, CD의 릴리스 파이프라인을 설정한 다음, 모니터링을 위해 Azure Application Insights 리소스를 만듭니다.

다음을 수행합니다.

> [!div class="checklist"]
> * ASP.NET 앱용 Azure DevOps 프로젝트 만들기
> * Azure DevOps Services 및 Azure 구독 구성 
> * Azure DevOps Services CI 파이프라인 검토
> * Azure DevOps Services CD 파이프라인 검토
> * Azure DevOps Services의 변경 내용 커밋 및 자동으로 Azure에 배포
> * Azure Application Insights 모니터링 구성
> * 리소스 정리

## <a name="prerequisites"></a>필수 조건

* Azure 구독. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료 구독을 구할 수 있습니다.

## <a name="create-an-azure-devops-project-for-an-aspnet-app"></a>ASP.NET 앱용 Azure DevOps 프로젝트 만들기

Azure DevOps 프로젝트는 Azure에 CI/CD 파이프라인을 만듭니다.  **새 Azure DevOps Services** 조직을 만들거나 **기존 조직**을 사용합니다.  또한 Azure DevOps 프로젝트는 선택한 **Azure 구독**에서 가상 머신 같은 **Azure 리소스**를 만듭니다.

1. [Microsoft Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **+새로 만들기** 아이콘을 선택한 다음, **DevOps 프로젝트**를 검색합니다.  **만들기**를 선택합니다.

    ![지속적인 업데이트 시작](_img/azure-devops-project-github/fullbrowser.png)

1. **.NET**를 선택한 다음, **다음**을 선택합니다.

1. **응용 프로그램 프레임워크 선택**의 경우 **ASP.NET** 및 **다음**을 차례로 선택합니다. 

1. 이전 단계에서 선택한 응용 프로그램 프레임워크는 여기에서 사용 가능한 Azure 서비스 배포 대상의 유형을 나타냅니다.  **가상 머신**을 선택한 다음, **다음**을 선택합니다.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Azure DevOps Services 및 Azure 구독 구성

1. **새** Azure DevOps Services 조직을 만들거나 **기존** 조직을 선택합니다.  Azure DevOps 프로젝트의 **이름**을 선택합니다.  

1. **Azure 구독 서비스**를 선택합니다.  필요에 따라 **변경** 링크를 선택한 다음, Azure 리소스의 위치 변경 등 보다 자세한 구성 정보를 입력합니다.
 
1. 새 Azure 가상 머신 리소스에 대해 **가상 머신 이름**, **사용자 이름** 및 **암호**를 입력한 다음, **완료**를 선택합니다.

1. Azure 가상 머신이 준비되려면 몇 분 정도 걸립니다.  샘플 ASP.NET 응용 프로그램이 Azure DevOps Services 조직의 리포지토리에서 설정되고, 빌드 및 릴리스가 실행되고, 응용 프로그램이 새로 만든 Azure VM에 배포됩니다.  

    완료되면 Azure DevOps **프로젝트 대시보드**가 Azure Portal에 로드됩니다.  **Azure Portal**의 **모든 리소스**에서 직접 **Azure DevOps Project 대시보드**로 이동할 수도 있습니다.  

    이 대시보드에서는 Azure DevOps Services **코드 리포지토리**, **Azure CI/CD 파이프라인** 및 **Azure에서 실행되는 응용 프로그램**에 가시성을 제공합니다.    

    ![대시보드 보기](_img/azure-devops-project-vms/dashboardnopreview.png)

1. Azure DevOps 프로젝트는 리포지토리에 모든 코드 변경을 자동으로 배포하는 CI 빌드 및 릴리스 트리거를 자동으로 구성합니다.  Azure DevOps에서 추가 옵션을 추가로 구성할 수 있습니다.  대시보드의 오른쪽에서 **찾아보기**를 선택하여 실행 중인 응용 프로그램을 봅니다.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Azure DevOps Services CI 파이프라인 검토
 
Azure DevOps 프로젝트는 Azure DevOps Services 조직에서 전체 Azure CI/CD 파이프라인을 자동으로 구성합니다.  파이프라인을 탐색하고 사용자 지정할 수 있습니다.  Azure DevOps Services 빌드 정의를 숙지하려면 다음 단계를 수행합니다.

1. **Azure DevOps 프로젝트 대시보드**의 **위쪽**에서 **빌드 파이프라인**을 선택합니다.  이 링크는 브라우저 탭 및 새 프로젝트에 대한 Azure DevOps Services 빌드 파이프라인을 엽니다.

1. **상태** 필드 옆에 빌드 파이프라인의 오른쪽으로 마우스 커서를 이동합니다. 나타나는 **줄임표**를 선택합니다.  이 작업은 **새 빌드 큐**, **빌드 일시 중지** 및 **빌드 파이프라인 편집** 같은 여러 활동을 수행할 수 있는 메뉴를 엽니다.

1. **편집**을 선택합니다.

1. 이 보기에서 빌드 파이프라인에 대한 **다양한 작업을 검사합니다**.  빌드는 Azure DevOps Services Git 리포지토리에서 원본 가져오기, 종속성 복원 및 배포에 사용된 출력 게시 등 다양한 작업을 실행합니다.

1. 빌드 파이프라인의 맨 위에서 **빌드 파이프라인 이름**을 선택합니다.

1. 빌드 파이프라인의 **이름**을 좀 더 구체적인 것으로 변경합니다.  **저장 및 큐**를 선택한 다음, **저장**을 선택합니다.

1. 빌드 파이프라인 이름에서 **History**를 선택합니다.  빌드에 대한 최근 변경 내용의 감사 내역이 표시됩니다.  Azure DevOps Services는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

1. **트리거**를 선택합니다.  Azure DevOps 프로젝트는 CI 트리거를 자동으로 생성하면 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다.  필요에 따라 CI 프로세스에서 분기를 포함할지를 선택합니다.

1. **보존**을 선택합니다.  시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Azure DevOps Services CD 파이프라인 검토

Azure DevOps 프로젝트는 Azure DevOps Services 조직에서 Azure 구독에 배포하는 데 필요한 단계를 자동으로 만들고 구성합니다.  이러한 단계에는 Azure 구독에 Azure DevOps Services를 인증하기 위한 Azure 서비스 연결 구성이 포함됩니다.  또한 자동화는 Azure DevOps Services CD 파이프라인을 만들고 Azure Virtual Machine에 이 CD를 제공합니다.  Azure DevOps Services CD 파이프라인에 대해 자세히 검사하려면 다음 단계를 수행합니다.수행 합니다.

1. **빌드 및 릴리스**를 선택한 다음, **릴리스**를 선택합니다.  Azure DevOps 프로젝트는 Azure에 배포를 관리하는 Azure DevOps Services 릴리스 파이프라인을 만들었습니다.

1. 브라우저의 왼쪽에서 릴리스 파이프라인 옆에 있는 **줄임표**를 선택한 다음, **편집**을 선택합니다.

1. 릴리스 파이프라인에는 릴리스 프로세스를 정의하는 **파이프라인**이 포함됩니다.  **아티팩트** 아래에서 **드롭**을 선택합니다.  이전 단계에서 검사한 빌드 파이프라인을 아티팩트에 사용된 출력을 생성합니다. 

1. **Drop** 아이콘의 오른쪽에서 **지속적인 배포 트리거** **아이콘**(번개 표시)를 선택합니다.  이 릴리스 파이프라인을 CD 트리거를 사용하도록 설정했습니다.  새 빌드 아티팩트를 사용할 수 있을 때마다 트리거가 배포를 시작합니다.  필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필요하게 됩니다. 

1. 브라우저의 왼쪽에서 선택 **태스크**를 선택한 후에 **환경**합니다.  

1. 작업은 배포 프로세스가 실행되는 활동이며 **단계**에서 그룹화됩니다.  이 릴리스 파이프라인에 대해 두 개의 **단계**가 있습니다.  첫 번째 단계는 배포에 대한 VM을 구성하고 새 VM을 **Azure DevOps 배포 그룹**에 추가하는 **Azure 리소스 그룹 배포** 작업을 포함합니다.  Azure DevOps의 VM 배포 그룹은 **배포 대상** 컴퓨터의 논리 그룹을 관리합니다.

1. 이 두 번째 단계에서 **IIS 웹앱 관리** 작업이 생성돼 VM에 IIS 웹 사이트를 만들었습니다.  두 번째 **IIS 웹앱 배포** 작업이 생성돼 사이트를 배포했습니다.

1. 브라우저의 오른쪽에서 **릴리스 보기**를 선택합니다.  이 보기에는 릴리스의 기록이 표시됩니다.

1. 한 릴리스 옆에 있는 **줄임표**를 선택하고 **열기**를 선택합니다.  **릴리스 요약**, **연결된 작업 항목** 및 **테스트** 등 여러 메뉴를 이 보기에서 탐색할 수 있습니다.

1. **커밋**을 선택합니다.  이 보기에는 특정 배포와 연결된 코드 커밋이 표시됩니다. 배포 간 커밋 차이를 보기 위해 릴리스를 비교합니다.

1. **로그**를 선택합니다.  로그에는 배포 프로세스에 대한 유용한 정보가 포함됩니다.  배포 도중 및 이후 모두에서 로그를 볼 수 있습니다.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Azure DevOps Services의 변경 내용 커밋 및 자동으로 Azure에 배포 

이제 웹 사이트에 최신 작업을 자동으로 배포하는 CI/CD 프로세스를 사용하여 앱에서 팀과 협업할 준비가 되었습니다.  Azure DevOps Services Git 리포지토리를 변경할 때마다 Azure DevOps Services에서 빌드가 시작되고, Azure DevOps Services CD 파이프라인은 Azure VM으로의 배포를 실행합니다.  리포지토리에 변경 내용을 커밋하려면 아래 단계를 수행하거나 다른 기술을 사용합니다.  코드 변경은 CI/CD 프로세스를 시작하고 Azure VM의 IIS 웹 사이트에 새 변경 내용을 자동으로 배포합니다.

1. Azure DevOps Services 메뉴에서 **코드**를 선택하고 리포지토리로 이동합니다.

1. **Views\Home**으로 이동한 다음, **Index.cshtml** 파일 옆의 **줄임표**를 선택하고 **편집**을 선택합니다.

1. 한 **div 태그** 내부의 일부 텍스트 같은 파일을 변경합니다.  오른쪽 위에서 **커밋**을 선택합니다.  다시 **커밋**을 선택하여 변경 내용을 푸시합니다. 

1. 곧 **Azure DevOps Services에서 빌드가 시작**된 다음, 변경 내용을 배포하기 위해 릴리스가 실행됩니다.  Azure DevOps Services 조직이 있는 브라우저에서 또는 DevOps 프로젝트 대시보드를 사용하여 **빌드 상태**를 모니터링합니다.

1. 릴리스가 완료되면 브라우저에서 **응용 프로그램을 새로 고침**하여 변경 내용을 확인합니다.

## <a name="configure-azure-application-insights-monitoring"></a>Azure Application Insights 모니터링 구성

Azure Application Insights를 사용하면 응용 프로그램의 성능 및 사용량을 쉽게 모니터링할 수 있습니다.  Azure DevOps 프로젝트는 자동으로 응용 프로그램에 대한 Application Insights 리소스를 구성했습니다.  필요에 따라 다양한 경고 및 모니터링 기능을 추가로 구성할 수 있습니다.

1. **Azure Portal**의 **Azure DevOps 프로젝트** 대시보드로 이동합니다.  대시보드의 오른쪽 아래에서 앱에 대한 **Application Insights** 링크를 선택합니다.

1. **Application Insights** 블레이드가 Azure Portal에서 열립니다.  이 보기에는 앱에 대한 사용량, 성능 및 가용성 모니터링 정보가 포함됩니다.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. **시간 범위**를 선택한 다음, **지난 시간**을 선택합니다.  **업데이트**를 선택하여 결과를 필터링합니다.  이제 지난 60분의 모든 활동이 표시됩니다.  **x**를 선택하여 시간 범위를 종료합니다.

1. 대시보드의 위쪽 부근에서 **경고** 및 다른 여러 유용한 링크를 찾을 수 있습니다.  **경고**을 선택한 다음, **+ 메트릭 경고 추가**를 선택합니다.

1. 경고에 대한 **이름**을 입력합니다.

1. 기본 경고는 **1초 보다 큰 서버 응답 시간**의 경우에 해당합니다.  **메트릭** 드롭다운을 선택하여 다양한 경고 메트릭을 검사합니다.  예를 들어 ASP.NET 앱에 대한 **ASP.NET 요청 실행 시간**을 구성할 수 있습니다.  다양한 경고를 쉽게 구성하여 앱의 모니터링 기능을 개선할 수 있습니다.

1. **이메일 소유자, 기여자 및 구독자를 통해 알림** 확인란을 선택합니다.  필요에 따라 Azure 논리 앱을 실행하여 경고가 발생하면 추가 작업을 수행할 수 있습니다.

1. **확인**을 선택하여 경고를 만듭니다.  곧 경고가 대시보드에서 활성으로 표시됩니다.  경고 영역을 **종료**하고 **Application Insights 블레이드**로 돌아갑니다.

1. **가용성**을 선택한 다음, **+ 테스트 추가**를 선택합니다. 

1. **테스트 이름**을 입력한 다음, **만들기**를 선택합니다.  이는 간단한 ping 테스트를 생성하여 응용 프로그램의 가용성을 확인합니다.  몇 분 후 테스트 결과를 사용할 수 있고, Application Insights 대시보드에 가용성 상태가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

 > [!NOTE]
 > 다음 단계는 리소스를 영구적으로 삭제합니다.  신중하게 표시되는 메시지를 읽은 후에만 이 기능을 사용합니다.

테스트하는 경우 청구 요금 발생을 방지하기 위해 리소스를 정리할 수 있습니다.  더 이상 필요 없는 경우 Azure DevOps 프로젝트 대시보드에서 **삭제** 기능을 사용하여 이 자습서에서 만든 Azure 가상 머신 및 관련 리소스를 삭제할 수 있습니다.  **주의** 삭제 기능은 Azure 및 Azure DevOps에서 Azure DevOps 프로젝트에 의해 만들어진 데이터를 제거하므로 사라지면 데이터를 검색할 수 없습니다.

1. **Azure Portal**에서 **Azure DevOps 프로젝트**로 이동합니다.
2. 대시보드의 **오른쪽 위**에서 **삭제**를 선택합니다.  프롬프트를 읽은 후 **예**를 선택하여 리소스를 **영구적으로 삭제**합니다.

## <a name="next-steps"></a>다음 단계

팀의 요구를 충족하려면 필요에 따라 이러한 빌드 및 릴리스 파이프라인을 수정할 수 있습니다. 또한 다른 프로젝트에 대한 템플릿으로 이 CI/CD 패턴을 사용할 수 있습니다.  다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * ASP.NET 앱용 Azure DevOps 프로젝트 만들기
> * Azure DevOps Services 및 Azure 구독 구성 
> * Azure DevOps Services CI 파이프라인 검토
> * Azure DevOps Services CD 파이프라인 검토
> * Azure DevOps Services의 변경 내용 커밋 및 자동으로 Azure에 배포
> * Azure Application Insights 모니터링 구성
> * 리소스 정리

Azure CI/CD 파이프라인에 대해 자세히 알아보려면 이 자습서를 참조합니다.

> [!div class="nextstepaction"]
> [CD 프로세스 사용자 지정](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
