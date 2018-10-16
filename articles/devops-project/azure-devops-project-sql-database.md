---
title: Azure DevOps 프로젝트를 사용하여 ASP.NET 앱 및 Azure SQL Database 배포 | Azure DevOps Services 자습서
description: DevOps Project를 사용하면 Azure를 쉽게 시작할 수 있습니다. Azure DevOps 프로젝트를 사용하면 몇 가지 빠른 단계로 Azure SQL Database에서 ASP.NET을 쉽게 배포할 수 있습니다.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: d9c7c94e344daee5af87ce40ddf4dcb686696ded
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297336"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>자습서: Azure DevOps 프로젝트를 사용하여 ASP.NET 앱 및 Azure SQL Database 배포

Azure DevOps 프로젝트는 기존 코드와 Git 리포지토리를 가져오거나 샘플 응용 프로그램 중 하나를 선택하여 Azure에 CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인을 만드는 간소화된 환경을 제공합니다.  DevOps 프로젝트는 자동으로 Azure SQL Database 같은 Azure 리소스를 만들고, CI의 빌드 파이프라인을 포함하는 Azure DevOps의 릴리스 파이프라인을 만들고 구성하고, CD의 릴리스 파이프라인을 설정한 다음, 모니터링을 위해 Azure Application Insights 리소스를 만듭니다.

다음을 수행합니다.

> [!div class="checklist"]
> * ASP.NET 앱 및 Azure SQL Database용 Azure DevOps 프로젝트 만들기
> * Azure DevOps Services 및 Azure 구독 구성 
> * Azure DevOps Services CI 파이프라인 검토
> * Azure DevOps Services CD 파이프라인 검토
> * Azure DevOps Services의 변경 내용 커밋 및 자동으로 Azure에 배포
> * Azure SQL Server Database에 연결 
> * 리소스 정리

## <a name="prerequisites"></a>필수 조건

* Azure 구독. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료 구독을 구할 수 있습니다.

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>ASP.NET 앱 및 Azure SQL Database용 Azure DevOps 프로젝트 만들기

Azure DevOps 프로젝트는 Azure에 CI/CD 파이프라인을 만듭니다.  **새 Azure DevOps Services** 조직을 만들거나 **기존 조직**을 사용합니다.  또한 Azure DevOps 프로젝트는 선택한 **Azure 구독**에서 Azure SQL Database 같은 **Azure 리소스**를 만듭니다.

1. [Microsoft Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **리소스 만들기** 아이콘을 선택한 다음, **DevOps 프로젝트**를 검색합니다.  **만들기**를 선택합니다.

    ![지속적인 업데이트 시작](_img/azure-devops-project-github/fullbrowser.png)

1. **.NET**를 선택한 다음, **다음**을 선택합니다.

1. **응용 프로그램 프레임워크 선택**의 경우 **ASP.NET**을 선택합니다.

1. **데이터베이스 추가**를 선택한 다음, **다음**을 선택합니다.  

1. 이전 단계에서 선택한 응용 프로그램 프레임워크는 여기에서 사용 가능한 Azure 서비스 배포 대상의 유형을 나타냅니다.  **다음**을 선택합니다.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Azure DevOps Services 및 Azure 구독 구성

1. **새** Azure DevOps Services 조직을 만들거나 **기존** 조직을 선택합니다.  Azure DevOps 프로젝트의 **이름**을 선택합니다.  

1. Azure **구독**을 선택합니다.

1. 필요에 따라 Azure 구성 설정을 확인하려면 **변경** 링크를 선택하고, **데이터베이스 서버 로그인 세부 정보** 섹션에서 **사용자 이름**을 식별합니다.  이 자습서의 이후 단계를 위해 **사용자 이름**을 **저장**합니다.
 
1. 위의 단계를 수행한 경우 Azure 구성 영역을 종료하고 **완료**를 선택합니다.  그렇지 않을 경우 **완료**만 선택합니다.

1. 이 프로세스는 완료하는 데 몇 분 정도 걸립니다.  완료되면 Azure DevOps **프로젝트 대시보드**가 Azure Portal에 로드됩니다.  **Azure Portal**의 **모든 리소스**에서 직접 **Azure DevOps Project 대시보드**로 이동할 수도 있습니다.  대시보드의 오른쪽에서 **찾아보기**를 선택하여 실행 중인 응용 프로그램을 봅니다.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Azure DevOps Services CI 파이프라인 검토

Azure DevOps 프로젝트는 Azure DevOps Services 조직에서 전체 Azure CI/CD 파이프라인을 자동으로 구성합니다.  파이프라인을 탐색하고 사용자 지정할 수 있습니다.  Azure DevOps Services 빌드 정의를 숙지하려면 다음 단계를 수행합니다.

1. **Azure DevOps 프로젝트 대시보드**로 이동합니다.

1. **Azure DevOps 프로젝트 대시보드**의 **위쪽**에서 **빌드 파이프라인**을 선택합니다.  이 링크는 브라우저 탭 및 새 프로젝트에 대한 Azure DevOps Services 빌드 파이프라인을 엽니다.

1. **상태** 필드 옆에 빌드 파이프라인의 오른쪽으로 마우스 커서를 이동합니다. 나타나는 **줄임표**를 선택합니다.  이 작업은 **새 빌드 큐**, **빌드 일시 중지** 및 **빌드 파이프라인 편집** 같은 여러 활동을 수행할 수 있는 메뉴를 엽니다.

1. **편집**을 선택합니다.

1. 이 보기에서 빌드 파이프라인에 대한 **다양한 작업을 검사합니다**.  빌드는 Azure DevOps Services Git 리포지토리에서 원본 가져오기, 종속성 복원 및 배포에 사용된 출력 게시 등 다양한 작업을 실행합니다.

1. 빌드 파이프라인의 맨 위에서 **빌드 파이프라인 이름**을 선택합니다.

1. 빌드 파이프라인의 **이름**을 좀 더 구체적인 것으로 변경합니다.  **저장 및 큐**를 선택한 다음, **저장**을 선택합니다.

1. 빌드 파이프라인 이름에서 **History**를 선택합니다.  빌드에 대한 최근 변경 내용의 감사 내역이 표시됩니다.  Azure DevOps Services는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

1. **트리거**를 선택합니다.  Azure DevOps 프로젝트는 CI 트리거를 자동으로 생성하면 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다.  필요에 따라 CI 프로세스에서 분기를 포함할지를 선택할 수 있습니다.

1. **보존**을 선택합니다.  시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Azure DevOps Services CD 파이프라인 검토

Azure DevOps 프로젝트는 Azure DevOps Services 조직에서 Azure 구독에 배포하는 데 필요한 단계를 자동으로 만들고 구성합니다.  이러한 단계에는 Azure 구독에 Azure DevOps Services를 인증하기 위한 Azure 서비스 연결 구성이 포함됩니다.  또한 자동화는 Azure DevOps Services 릴리스 정의를 만들고 릴리스는 Azure에 CD를 제공합니다.  Azure DevOps Services 릴리스 정의에 대해 자세히 검사하려면 다음 단계를 수행합니다.

1. **빌드 및 릴리스**를 선택한 다음, **릴리스**를 선택합니다.  Azure DevOps 프로젝트는 Azure에 배포를 관리하는 Azure DevOps Services 릴리스 파이프라인을 만들었습니다.

1. 브라우저의 왼쪽에서 릴리스 파이프라인 옆에 있는 **줄임표**를 선택한 다음, **편집**을 선택합니다.

1. 릴리스 파이프라인에는 릴리스 프로세스를 정의하는 **파이프라인**이 포함됩니다.  **아티팩트** 아래에서 **드롭**을 선택합니다.  이전 단계에서 검사한 빌드 파이프라인을 아티팩트에 사용된 출력을 생성합니다. 

1. **Drop** 아이콘의 오른쪽에서 **지속적인 배포 트리거** **아이콘**(번개 표시)를 선택합니다.  이 릴리스 파이프라인을 CD 트리거를 사용하도록 설정했습니다.  새 빌드 아티팩트를 사용할 수 있을 때마다 트리거가 배포를 시작합니다.  필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필요하게 됩니다. 

1. Azure DevOps 프로젝트는 임의의 SQL 암호를 설정하고 이 암호를 릴리스 파이프라인을 위해 사용했습니다.  브라우저의 왼쪽에서 **변수**를 선택합니다. 

1. **SQL Server 암호를 변경한 경우 이 단계만 수행합니다.**  단일 **암호** 변수가 있습니다.  **값** 텍스트 상자 오른쪽에서 **자물쇠** 아이콘을 선택합니다.  새 암호를 **입력**한 다음, **저장**을 선택합니다.

1. 브라우저의 왼쪽에서 선택 **태스크**를 선택한 후에 **환경**합니다.  

1. 작업은 배포 프로세스가 수행되는 활동이며 **단계**에서 그룹화됩니다.  이 릴리스 파이프라인에 대한 단일 단계가 있습니다.  이 단계에는 **Azure App Service 배포** 및 **Azure SQL Database 배포** 작업이 포함됩니다.

1. **Azure SQL 실행** 작업을 선택하고 SQL 배포에 사용된 다양한 속성을 검사합니다.  **배포 패키지** 아래에서 작업이 **SQL DACPAC 파일**을 사용한다는 것을 유의합니다.

1. 브라우저의 오른쪽에서 **릴리스 보기**를 선택합니다.  이 보기에는 릴리스의 기록이 표시됩니다.

1. 한 릴리스 옆에 있는 **줄임표**를 선택하고 **열기**를 선택합니다.  **릴리스 요약**, **연결된 작업 항목** 및 **테스트** 등 여러 메뉴를 이 보기에서 탐색할 수 있습니다.

1. **커밋**을 선택합니다.  이 보기에는 특정 배포와 연결된 코드 커밋이 표시됩니다. 배포 간 커밋 차이를 보기 위해 릴리스를 비교할 수 있습니다.

1. **로그**를 선택합니다.  로그에는 배포 프로세스에 대한 유용한 정보가 포함됩니다.  배포 도중 및 이후 모두에서 로그를 볼 수 있습니다.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Azure DevOps Services의 변경 내용 커밋 및 자동으로 Azure에 배포 

 > [!NOTE]
 > 다음 단계는 간단한 텍스트 변경을 사용하여 CI/CD 파이프라인을 테스트합니다.  SQL 배포 프로세스를 테스트하려면 필요에 따라 SQL Server 스키마를 테이블로 변경할 수 있습니다.

이제 웹 사이트에 최신 작업을 자동으로 배포하는 CI/CD 프로세스를 사용하여 앱에서 팀과 협업할 준비가 되었습니다.  Azure DevOps Services Git 리포지토리를 변경할 때마다 Azure DevOps Services에서 빌드가 시작되고, Azure DevOps Services CD 파이프라인은 Azure로의 배포를 실행합니다.  리포지토리에 변경 내용을 커밋하려면 아래 단계를 수행하거나 다른 기술을 사용합니다.  코드 변경은 CI/CD 프로세스를 시작하고 Azure에 새 변경 내용을 자동으로 배포합니다.

1. Azure DevOps Services 메뉴에서 **코드**를 선택하고 리포지토리로 이동합니다.

1. **SampleWebApplication\Views\Home**으로 이동한 다음, **Index.cshtml** 파일 옆의 **줄임표**를 선택하고 **편집**을 선택합니다.

1. 한 **div 태그** 내부의 일부 텍스트 같은 파일을 변경합니다.  오른쪽 위에서 **커밋**을 선택합니다.  다시 **커밋**을 선택하여 변경 내용을 푸시합니다. 

1. 곧 **Azure DevOps Services에서 빌드가 시작**된 다음, 변경 내용을 배포하기 위해 릴리스가 실행됩니다.  Azure DevOps Services 조직이 있는 브라우저에서 또는 DevOps 프로젝트 대시보드를 사용하여 **빌드 상태**를 모니터링할 수 있습니다.

1. 릴리스가 완료되면 브라우저에서 **응용 프로그램을 새로 고침**하여 변경 내용을 확인합니다.

## <a name="connect-to-the-azure-sql-server-database"></a>Azure SQL Server Database에 연결

Azure SQL Database에 연결할 적절한 권한이 필요합니다.

1. Azure DevOps 프로젝트 대시보드에서 **SQL Database**를 선택하여 SQL DB에 대한 관리 페이지로 이동합니다.
   
1. **서버 방화벽 설정**을 선택한 다음, **+ 클라이언트 IP 추가**를 선택합니다.  

1. **저장**을 선택합니다.  클라이언트 IP가 이제 **SQL Server Azure 리소스**에 대한 액세스가 허용됩니다.

1. **SQL Database** 블레이드로 돌아갑니다. 

1. 화면의 오른쪽에서 **서버 이름**을 선택하여 **SQL Server**에 대한 구성 페이지로 이동합니다.

1. **암호 재설정**을 선택하고, **SQL Server 관리자 로그인** 암호를 입력한 다음, **저장**을 선택합니다.  이 자습서의 이후 단계를 위해 이 암호를 **유지**합니다.

1. 필요에 따라 이제 **SQL Server Management Studio** 또는 **Visual Studio**와 같은 클라이언트 도구를 사용하여 Azure SQL Server 및 Database에 연결할 수 있습니다.  연결할 **서버 이름** 속성을 사용합니다.

   처음에 DevOps 프로젝트를 구성하는 동안 DB 사용자 이름을 변경하지 않는 경우 사용자 이름은 이메일 주소의 로컬 파트입니다.  예를 들어 이메일 주소가 johndoe@microsoft.com인 경우 사용자 이름 johndoe입니다.

 > [!NOTE]
 > SQL 로그인 암호를 변경한 경우 **Azure DevOps Services CD 파이프라인 검사** 섹션에 설명된 대로 Azure DevOps Services 릴리스 파이프라인 변수에서 암호를 변경해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

 > [!NOTE]
 > 다음 단계는 리소스를 영구적으로 삭제합니다.  신중하게 표시되는 메시지를 읽은 후에만 이 기능을 사용합니다.

테스트하는 경우 청구 요금 발생을 방지하기 위해 리소스를 정리할 수 있습니다.  더 이상 필요 없는 경우 Azure DevOps 프로젝트 대시보드에서 **삭제** 기능을 사용하여 이 자습서에서 만든 Azure SQL Database 및 관련 리소스를 삭제할 수 있습니다.  **주의** 삭제 기능은 Azure 및 Azure DevOps Services에서 Azure DevOps 프로젝트에 의해 만들어진 데이터를 제거하므로 사라지면 데이터를 검색할 수 없습니다.

1. **Azure Portal**에서 **Azure DevOps 프로젝트**로 이동합니다.
2. 대시보드의 **오른쪽 위**에서 **삭제**를 선택합니다.  프롬프트를 읽은 후 **예**를 선택하여 리소스를 **영구적으로 삭제**합니다.

## <a name="next-steps"></a>다음 단계

팀의 요구를 충족하려면 필요에 따라 이러한 빌드 및 릴리스 파이프라인을 수정할 수 있습니다. 또한 다른 프로젝트에 대한 템플릿으로 이 CI/CD 패턴을 사용할 수 있습니다.  다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * ASP.NET 앱 및 Azure SQL Database용 Azure DevOps 프로젝트 만들기
> * Azure DevOps Services 및 Azure 구독 구성 
> * Azure DevOps Services CI 파이프라인 검토
> * Azure DevOps Services CD 파이프라인 검토
> * Azure DevOps Services의 변경 내용 커밋 및 자동으로 Azure에 배포
> * Azure SQL Server Database에 연결 
> * 리소스 정리

Azure 파이프라인에 대해 자세히 알아보려면 이 자습서를 참조합니다.

> [!div class="nextstepaction"]
> [CD 프로세스 사용자 지정](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>동영상

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]