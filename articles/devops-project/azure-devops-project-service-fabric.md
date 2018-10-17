---
title: Azure DevOps 프로젝트를 사용하여 Azure Service Fabric에 ASP.NET Core 앱 배포 | Azure DevOps Services 자습서
description: DevOps Project를 사용하면 Azure를 쉽게 시작할 수 있습니다. Azure DevOps 프로젝트를 사용하면 Azure Service Fabric을 사용하여 ASP.NET Core 앱을 몇 가지 빠른 단계로 쉽게 배포할 수 있습니다.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6a0539b2213b99e09021a4f90d914172eac62560
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300401"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>자습서: Azure DevOps 프로젝트를 사용하여 Azure Service Fabric에 ASP.NET Core 앱 배포

Azure DevOps 프로젝트는 기존 코드와 Git 리포지토리를 가져오거나 샘플 응용 프로그램 중 하나를 선택하여 Azure에 CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인을 만드는 간소화된 환경을 제공합니다.  DevOps 프로젝트는 자동으로 Azure Service Fabric 같은 Azure 리소스를 만들고, CI/CD 파이프라인 설정을 비롯하여 Azure DevOps에 릴리스 파이프라인을 만들고 구성한 다음, 모니터링을 위해 Azure Application Insights 리소스를 만듭니다.

다음을 수행합니다.

> [!div class="checklist"]
> * Service Fabric 및 ASP.NET Core 앱용 Azure DevOps 프로젝트 만들기
> * Azure DevOps Services 및 Azure 구독 구성 
> * CI 파이프라인 검토
> * CD 파이프라인 검토
> * Git에 변경 내용 커밋 및 자동으로 Azure에 배포
> * 리소스 정리

## <a name="prerequisites"></a>필수 조건

* Azure 구독. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료 구독을 구할 수 있습니다.

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>Service Fabric 및 ASP.NET Core 앱용 Azure DevOps 프로젝트 만들기

Azure DevOps 프로젝트는 CI/CD 파이프라인을 만듭니다.  **새 Azure DevOps Services** 조직을 만들거나 **기존 조직**을 사용합니다.  또한 Azure DevOps 프로젝트는 선택한 **Azure 구독**에서 Service Fabric 클러스터 같은 **Azure 리소스**를 만듭니다.

1. [Microsoft Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **리소스 만들기** 아이콘을 선택한 다음, **DevOps 프로젝트**를 검색합니다.  **만들기**를 선택합니다.

    ![지속적인 업데이트 시작](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. **.NET**를 선택한 다음, **다음**을 선택합니다.

1. **응용 프로그램 프레임워크 선택**의 경우 **ASP.NET Core** 및 **다음**을 차례로 선택합니다.

1. **Service Fabric 클러스터** 및 **다음**을 차례로 선택합니다.  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Azure DevOps Services 및 Azure 구독 구성

1. **새** Azure DevOps Services 조직을 만들거나 **기존** 조직을 선택합니다.  Azure DevOps 프로젝트의 **이름**을 선택합니다.  

1. Azure **구독**을 선택합니다.

1. 추가 Azure 구성 설정을 참조하려면 **변경** 링크를 선택하고, **Service Fabric 클러스터**에 대한 **노드 가상 머신 크기** 및 **운영 체제**를 식별합니다.  Azure 서비스의 위치 및 유형을 구성하기 위한 다양한 옵션이 여기 있습니다.
 
1. Azure 구성 영역을 종료하고 **완료**를 선택합니다.

1. 이 프로세스는 완료하는 데 몇 분 정도 걸립니다.  샘플 ASP.NET Core 응용 프로그램이 Azure DevOps Services 조직의 리포지토리에서 설정되고 Service Fabric 클러스터가 만들어지고, CI/CD 파이프라인이 실행되고, 응용 프로그램이 Azure에 배포됩니다.  

    완료되면 Azure DevOps **프로젝트 대시보드**가 Azure Portal에 로드됩니다.  **Azure Portal**의 **모든 리소스**에서 직접 **Azure DevOps Project 대시보드**로 이동할 수도 있습니다.  

    이 대시보드에서는 Azure DevOps Services **코드 리포지토리**, **Azure DevOps Services CI/CD 파이프라인** 및 **Service Fabric 클러스터**에 가시성을 제공합니다.  Azure DevOps Services에서 추가 옵션을 추가로 구성할 수 있습니다.  대시보드의 오른쪽에서 **찾아보기**를 선택하여 실행 중인 응용 프로그램을 봅니다.

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Azure DevOps Services CI 파이프라인 검토

Azure DevOps 프로젝트는 Azure DevOps Services 조직에서 Azure DevOps Services CI/CD 파이프라인을 자동으로 구성합니다.  파이프라인을 탐색하고 사용자 지정할 수 있습니다.  Azure DevOps Services 빌드 파이프라인을 숙지하려면 다음 단계를 수행합니다.

1. **Azure DevOps 프로젝트 대시보드**로 이동합니다.

1. **Azure DevOps 프로젝트 대시보드**의 **위쪽**에서 **빌드 파이프라인**을 선택합니다.  이 링크는 브라우저 탭 및 새 프로젝트에 대한 Azure DevOps Services 빌드 파이프라인을 엽니다.

1. **상태** 필드 옆에 빌드 파이프라인의 오른쪽으로 마우스 커서를 이동합니다. 나타나는 **줄임표**를 선택합니다.  이 작업은 **새 빌드 큐**, **빌드 일시 중지** 및 **빌드 파이프라인 편집** 같은 여러 활동을 시작할 수 있는 메뉴를 엽니다.

1. **편집**을 선택합니다.

1. 이 보기에서 빌드 파이프라인의 **다양한 작업을 검사합니다**.  빌드는 Azure DevOps Services Git 리포지토리에서 원본 가져오기, 종속성 복원 및 배포에 사용된 출력 게시 등 다양한 작업을 실행합니다.

1. 빌드 파이프라인의 맨 위에서 **빌드 파이프라인 이름**을 선택합니다. 

1. 빌드 파이프라인 이름에서 **History**를 선택합니다.  빌드에 대한 최근 변경 내용의 감사 내역이 표시됩니다.  Azure DevOps Services는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

1. **트리거**를 선택합니다.  Azure DevOps 프로젝트는 CI 트리거를 자동으로 생성하면 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다.  필요에 따라 CI 프로세스에서 분기를 포함할지를 선택할 수 있습니다.

1. **보존**을 선택합니다.  시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Azure DevOps Services CD 파이프라인 검토

Azure DevOps 프로젝트는 Azure DevOps Services 조직에서 Azure 구독에 배포하는 데 필요한 단계를 자동으로 만들고 구성합니다.  이러한 단계에는 Azure 구독에 Azure DevOps Services를 인증하기 위한 Azure 서비스 연결 구성이 포함됩니다.  또한 자동화는 Azure DevOps Services 릴리스 파이프라인을 만들고, 이 릴리스는 Azure에 CD를 제공합니다.  Azure DevOps Services 릴리스 파이프라인에 대해 자세히 검사하려면 다음 단계를 수행합니다.

1. **빌드 및 릴리스**를 선택한 다음, **릴리스**를 선택합니다.  Azure DevOps 프로젝트는 Azure에 대한 배포를 관리하는 Azure DevOps Services 릴리스 파이프라인을 만들었습니다.

1. 브라우저의 왼쪽에서 릴리스 파이프라인 옆에 있는 **줄임표**를 선택한 다음, **편집**을 선택합니다.

1. 릴리스 파이프라인에는 릴리스 프로세스를 정의하는 **파이프라인**이 포함됩니다.  **아티팩트** 아래에서 **드롭**을 선택합니다.  이전 단계에서 검사한 빌드 파이프라인이 아티팩트에 사용된 출력을 생성합니다. 

1. **Drop** 아이콘의 오른쪽에서 **지속적인 배포 트리거** **아이콘**(번개 표시)를 선택합니다.  이 릴리스 파이프라인에는 사용하도록 설정된 CD 트리거가 있습니다.  새 빌드 아티팩트를 사용할 수 있을 때마다 트리거가 배포를 시작합니다.  필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필요하게 됩니다. 

1. 브라우저의 오른쪽에서 **릴리스 보기**를 선택합니다.  이 보기에는 릴리스의 기록이 표시됩니다.

1. 한 릴리스 옆에 있는 **줄임표**를 선택하고 **열기**를 선택합니다.  **릴리스 요약**, **연결된 작업 항목** 및 **테스트** 등 여러 메뉴를 이 보기에서 탐색할 수 있습니다.

1. **커밋**을 선택합니다.  이 보기에는 특정 배포와 연결된 코드 커밋이 표시됩니다. 배포 간 커밋 차이를 보기 위해 릴리스를 비교할 수 있습니다.

1. **로그**를 선택합니다.  로그에는 배포 프로세스에 대한 유용한 정보가 포함됩니다.  배포 도중 및 이후 모두에서 로그를 볼 수 있습니다.

## <a name="commit-changes-to-git-and-automatically-deploy-to-azure"></a>Git에 변경 내용 커밋 및 자동으로 Azure에 배포 

 > [!NOTE]
 > 다음 단계는 웹앱에 대한 간단한 텍스트 변경을 사용하여 CI/CD 파이프라인을 테스트합니다.

이제 웹 사이트에 최신 작업을 자동으로 배포하는 CI/CD 프로세스를 사용하여 앱에서 팀과 협업할 준비가 되었습니다.  Git 리포지토리를 변경할 때마다 빌드가 시작되고 릴리스는 변경 내용을 Azure에 배포합니다.  리포지토리에 변경 내용을 커밋하려면 아래 단계를 수행하거나 다른 기술을 사용합니다.  예를 들어 즐겨찾는 도구 또는 IDE에서 Git 리포지토리를 **복제**한 다음, 이 리포지토리에 변경 내용을 푸시할 수 있습니다.

1. Azure DevOps Services 메뉴에서 **코드** 및 **파일**을 차례로 선택하고 리포지토리로 이동합니다.
1. **Views\Home**으로 이동한 다음, **Index.cshtml** 파일 옆의 **줄임표**를 선택하고 **편집**을 선택합니다.

1. 한 **div 태그** 내부의 일부 텍스트 같은 파일을 변경합니다.  오른쪽 위에서 **커밋**을 선택합니다.  다시 **커밋**을 선택하여 변경 내용을 푸시합니다. 

1. 곧 **빌드가 시작**된 다음, 변경 내용을 배포하기 위해 릴리스가 실행됩니다.  Azure DevOps Services 실시간 로깅이 있는 브라우저에서 또는 DevOps 프로젝트 대시보드를 사용하여 **빌드 상태**를 모니터링할 수 있습니다.

1. 릴리스가 완료되면 브라우저에서 **응용 프로그램을 새로 고침**하여 변경 내용을 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

 > [!NOTE]
 > 다음 단계는 리소스를 영구적으로 삭제합니다.  신중하게 표시되는 메시지를 읽은 후에만 이 기능을 사용합니다.

테스트하는 경우 청구 요금 발생을 방지하기 위해 리소스를 정리할 수 있습니다.  더 이상 필요 없는 경우 Azure DevOps 프로젝트 대시보드에서 **삭제** 기능을 사용하여 이 자습서에서 만든 Azure Service Fabric 클러스터 및 관련 리소스를 삭제할 수 있습니다.  **주의** 삭제 기능은 Azure 및 Azure DevOps Services에서 Azure DevOps 프로젝트에 의해 만들어진 데이터를 제거하므로 사라지면 데이터를 검색할 수 없습니다.

1. **Azure Portal**에서 **Azure DevOps 프로젝트**로 이동합니다.
2. 대시보드의 **오른쪽 위**에서 **삭제**를 선택합니다.  프롬프트를 읽은 후 **예**를 선택하여 리소스를 **영구적으로 삭제**합니다.

## <a name="next-steps"></a>다음 단계

팀의 요구를 충족하려면 필요에 따라 Azure CI/CD 파이프라인을 수정할 수 있습니다. 또한 다른 프로젝트에 대한 템플릿으로 이 CI/CD 패턴을 사용할 수 있습니다.  다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Service Fabric 및 ASP.NET Core 앱용 Azure DevOps 프로젝트 만들기
> * Azure DevOps Services 및 Azure 구독 구성 
> * CI 파이프라인 검토
> * CD 파이프라인 검토
> * Git에 변경 내용 커밋 및 자동으로 Azure에 배포
> * 리소스 정리

Service Fabric 및 마이크로 서비스에 대해 자세히 알려면 아래를 참조합니다.

> [!div class="nextstepaction"]
> [응용 프로그램 빌드에 마이크로 서비스 접근 방식 사용](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
