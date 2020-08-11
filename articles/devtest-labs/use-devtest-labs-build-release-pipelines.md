---
title: Azure Pipelines 빌드 및 릴리스 파이프라인에서 DevTest Labs 사용
description: 파이프라인 빌드 및 릴리스 Azure Pipelines에서 Azure DevTest Labs를 사용 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d04ed5dd7bebac0c8f24deb9145c3d2e4b77122e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080337"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Azure Pipelines 빌드 및 릴리스 파이프라인에서 DevTest Labs 사용
이 문서에서는 Azure Pipelines 빌드 및 릴리스 파이프라인에서 DevTest Labs를 사용 하는 방법에 대 한 정보를 제공 합니다. 

## <a name="overall-flow"></a>전체 흐름
기본 흐름은 다음 작업을 수행 하는 **빌드 파이프라인** 을 포함 하는 것입니다.

1. 응용 프로그램 코드를 빌드합니다.
1. DevTest Labs에서 기본 환경을 만듭니다.
1. 사용자 지정 정보로 환경을 업데이트 합니다.
1. DevTest Labs 환경에 응용 프로그램 배포
1. 코드를 테스트 합니다. 

빌드가 성공적으로 완료 되 면 **릴리스 파이프라인** 은 빌드 아티팩트를 사용 하 여 스테이징 또는 프로덕션을 배포 합니다. 

필요한 프레미스 중 하나는 테스트 된 에코 시스템을 다시 만드는 데 필요한 모든 정보를 Azure 리소스의 구성을 포함 하 여 빌드 아티팩트 내에서 사용할 수 있다는 것입니다. Azure 리소스를 사용 하는 경우 비용이 발생 하므로 회사는 이러한 리소스의 사용을 제어 하거나 추적 하려고 합니다. 경우에 따라 리소스를 만들고 구성 하는 데 사용 되는 Azure Resource Manager 템플릿을 같은 다른 부서에서 관리할 수 있습니다. 이러한 템플릿은 다른 리포지토리에 저장 될 수 있습니다. 빌드를 만들고 테스트 하는 흥미로운 상황이 발생 하며, 프로덕션 환경에서 시스템을 제대로 다시 만들려면 코드와 구성을 모두 빌드 아티팩트 내에 저장 해야 합니다. 

빌드/테스트 단계 중에 DevTest Labs를 사용 하 여 빌드 소스에 템플릿 및 지원 파일 Azure Resource Manager 추가할 수 있습니다. 그러면 릴리스 단계 중에 테스트 하는 데 사용 된 정확한 구성이 프로덕션 환경에 배포 됩니다. 적절 한 구성을 사용 하 여 **Azure DevTest Labs 환경 만들기** 작업을 통해 빌드 아티팩트 내에 리소스 관리자 템플릿이 저장 됩니다. 이 예제에서는 [자습서: Azure App Service의 .Net Core 및 SQL Database 웹 앱 빌드](../app-service/tutorial-dotnetcore-sqldb-app.md)에서 코드를 사용 하 여 Azure에서 웹 앱을 배포 하 고 테스트 합니다.

![전체 흐름](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Azure 리소스 설정
미리 만들어야 할 몇 가지 항목이 있습니다.

- 두 개의 리포지토리입니다. 첫 번째 코드는 자습서의 코드를 사용 하 고 두 개의 추가 Vm이 있는 리소스 관리자 템플릿을 사용 합니다. 두 번째 항목에는 기본 Azure Resource Manager 템플릿 (기존 구성)이 포함 됩니다.
- 프로덕션 코드 및 구성을 배포 하기 위한 리소스 그룹입니다.
- 빌드 파이프라인에 대 한 [구성 리포지토리에](devtest-lab-create-environment-from-arm.md) 대 한 연결을 사용 하 여 랩을 설정 해야 합니다. DevTest Labs에서 템플릿을 인식 하 고 배포할 수 있도록 하려면 metadata.json으로 azuredeploy.js리소스 관리자 템플릿을 구성 리포지토리로 체크 인해야 합니다.

빌드 파이프라인은 DevTest Labs 환경을 만들고 테스트를 위한 코드를 배포 합니다.

## <a name="set-up-a-build-pipeline"></a>빌드 파이프라인 설정
Azure Pipelines [자습서: Azure App Service에서 .Net Core 및 SQL Database 웹 앱 빌드](../app-service/tutorial-dotnetcore-sqldb-app.md)의 코드를 사용 하 여 빌드 파이프라인을 만듭니다. **ASP.NET Core** 템플릿을 사용 하면 코드를 빌드, 테스트 및 게시 하는 데 필요한 작업을 채울 수 있습니다.

![ASP.NET 템플릿 선택](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

DevTest Labs에서 환경을 만들고 환경에 배포 하는 세 가지 추가 작업을 추가 해야 합니다.

![세 개의 작업을 포함 하는 파이프라인](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>환경 만들기 작업
환경 만들기 작업 (**Azure DevTest Labs 환경 만들기** 작업)에서 드롭다운 목록을 사용 하 여 다음 값을 선택 합니다.

- Azure 구독
- 랩 이름
- 리포지토리의 이름입니다.
- 환경이 저장 되는 폴더를 보여 주는 템플릿 이름입니다. 

정보를 수동으로 입력 하는 대신 페이지에서 드롭다운 목록을 사용 하는 것이 좋습니다. 정보를 수동으로 입력 하는 경우 정규화 된 Azure 리소스 Id를 입력 합니다. 작업은 리소스 Id 대신 이름을 표시 합니다. 

환경 이름은 DevTest Labs 내에 표시 되는 이름입니다. 각 빌드에 대 한 고유한 이름 이어야 합니다. 예: **Testenv $ (BuildId)**. 

리소스 관리자 템플릿에 정보를 전달 하는 매개 변수 파일 또는 매개 변수 중 하나를 지정할 수 있습니다. 

**환경 템플릿 출력을 기반으로 출력 변수 만들기** 옵션을 선택 하 고 참조 이름을 입력 합니다. 이 예에서는 참조 이름에 대해 **Baseenv** 를 입력 합니다. 다음 작업을 구성할 때이 BaseEnv를 사용 합니다. 

![Azure DevTest Labs 환경 만들기 작업](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>환경 채우기 작업
두 번째 작업 (**환경 채우기 작업 Azure DevTest Labs** )은 기존 DevTest Labs 환경을 업데이트 하는 것입니다. 환경 만들기 작업은이 작업에 대 한 환경 이름을 구성 하는 데 사용 되는 **Baseenv. 환경 resourceid** 를 출력 합니다. 이 예제의 리소스 관리자 템플릿에는 **Adminusername** 및 **adminusername**의 두 매개 변수가 있습니다. 

![Azure DevTest Labs 환경 채우기 작업](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>App Service 배포 작업
세 번째 작업은 **Azure App Service 배포** 작업입니다. 앱 유형이 **웹 앱** 으로 설정 되 고 App Service 이름이 **$ (웹 사이트)** 로 설정 됩니다.

![App Service 배포 작업](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>릴리스 파이프라인 설정
**Azure 배포: 리소스 그룹 만들기 또는 업데이트** 및 **Azure App Service 배포**의 두 가지 작업을 사용 하 여 릴리스 파이프라인을 만듭니다. 

첫 번째 작업의 경우 리소스 그룹의 이름과 위치를 지정 합니다. 템플릿 위치가 연결 된 아티팩트입니다. 리소스 관리자 템플릿에 연결 된 템플릿이 포함 된 경우 사용자 지정 리소스 그룹 배포를 구현 해야 합니다. 템플릿이 게시 된 drop 아티팩트에 있습니다. 리소스 관리자 템플릿에 대 한 템플릿 매개 변수를 재정의 합니다. 나머지 설정은 기본값을 그대로 유지할 수 있습니다. 

두 번째 작업 **배포 Azure App Service**에 대해 Azure 구독을 지정 하 고, **앱 유형에**대해 **웹 앱** 을 선택 하 고, **App Service 이름**으로 **$ (websites)** 를 선택 합니다. 나머지 설정은 기본값을 그대로 유지할 수 있습니다. 

## <a name="test-run"></a>테스트 실행
이제 두 파이프라인이 모두 설정 되었으므로 수동으로 빌드를 큐에 대기 하 고 작동 하는지 확인 합니다. 다음 단계는 빌드에 대 한 적절 한 트리거를 설정 하 고 빌드를 릴리스 파이프라인에 연결 하는 것입니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [Azure Pipelines 연속 통합 및 배달 파이프라인에 Azure DevTest Labs 통합](devtest-lab-integrate-ci-cd.md)
- [Azure Pipelines CI/CD 파이프라인에 환경 통합](integrate-environments-devops-pipeline.md)
