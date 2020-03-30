---
title: Azure Pipelines 빌드 및 릴리스 파이프라인에서 DevTest Labs 사용
description: Azure 파이프라인 빌드 및 릴리스 파이프라인에서 Azure DevTest 랩을 사용하는 방법을 알아봅니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169233"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Azure Pipelines 빌드 및 릴리스 파이프라인에서 DevTest Labs 사용
이 문서에서는 Azure 파이프라인 빌드 및 릴리스 파이프라인에서 DevTest 랩을 사용할 수 있는 방법에 대한 정보를 제공합니다. 

## <a name="overall-flow"></a>전체 흐름
기본 흐름은 다음 작업을 수행하는 **빌드 파이프라인을** 갖습니다.

1. 응용 프로그램 코드를 빌드합니다.
1. DevTest 랩에서 기본 환경을 만듭니다.
1. 사용자 지정 정보로 환경을 업데이트합니다.
1. DevTest 랩 환경에 응용 프로그램 배포
1. 코드를 테스트합니다. 

빌드가 성공적으로 완료되면 릴리스 **파이프라인은** 빌드 아티팩트를 사용하여 스테이징 또는 프로덕션을 배포합니다. 

필요한 전제 중 하나는 테스트된 에코시스템을 다시 만드는 데 필요한 모든 정보를 Azure 리소스 구성을 포함하여 빌드 아티팩트 내에서 사용할 수 있다는 것입니다. Azure 리소스를 사용할 때 비용이 발생하므로 회사에서는 이러한 리소스의 사용을 제어하거나 추적하려고 합니다. 경우에 따라 리소스를 만들고 구성하는 데 사용되는 Azure 리소스 관리자 템플릿은 IT와 같은 다른 부서에서 관리할 수 있습니다. 또한 이러한 템플릿은 다른 리포지토리에 저장될 수 있습니다. 빌드를 만들고 테스트하는 흥미로운 상황이 발생하며, 프로덕션 환경에서 시스템을 제대로 재현하려면 빌드 아티팩트 내에 코드와 구성을 모두 저장해야 합니다. 

빌드/테스트 단계에서 DevTest Labs를 사용하여 Azure Resource Manager 템플릿 및 지원 파일을 빌드 원본에 추가하여 릴리스 단계에서 테스트하는 데 사용되는 정확한 구성이 프로덕션에 배포되도록 할 수 있습니다. 적절한 구성을 갖춘 **Azure DevTest Labs 환경 만들기** 작업은 빌드 아티팩트 내에 리소스 관리자 템플릿을 저장합니다. 이 예제에서는 자습서의 코드를 사용 하 여 [합니다.](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)

![전체 흐름](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Azure 리소스 설정
미리 만들어야 하는 몇 가지 항목이 있습니다.

- 두 개의 리포지토리. 자습서의 코드와 두 개의 추가 VM이 있는 리소스 관리자 템플릿이 있는 첫 번째 템플릿입니다. 두 번째 에는 기본 Azure 리소스 관리자 템플릿(기존 구성)이 포함됩니다.
- 프로덕션 코드 및 구성 배포를 위한 리소스 그룹입니다.
- 랩은 빌드 [파이프라인의 구성 리포지토리에 대한 연결로](devtest-lab-create-environment-from-arm.md) 설정해야 합니다. DevTest Labs에서 템플릿을 인식하고 배포할 수 있도록 하려면 data.json을 사용하여 azuredeploy.json으로 구성 저장소에 리소스 관리자 템플릿을 체크 인해야 합니다.

빌드 파이프라인은 DevTest Labs 환경을 만들고 테스트를 위해 코드를 배포합니다.

## <a name="set-up-a-build-pipeline"></a>빌드 파이프라인 설정
Azure 파이프라인에서 자습서의 코드를 사용 하 여 빌드 [파이프라인을 만듭니다.](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md) ASP.NET **Core** 템플릿을 사용하여 코드를 빌드, 테스트 및 게시하는 데 필요한 작업을 채웁니다.

![ASP.NET 템플릿 선택](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

DevTest Labs에서 환경을 만들고 환경에 배포하려면 세 가지 작업을 추가해야 합니다.

![세 가지 작업이 있는 파이프라인](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>환경 작업 만들기
환경 만들기**작업(Azure DevTest Labs 환경 만들기** 작업)에서 드롭다운 목록을 사용하여 다음 값을 선택합니다.

- Azure 구독
- 랩 이름
- 저장소 의 이름
- 템플릿 의 이름(환경이 저장된 폴더를 보여 표시). 

정보를 수동으로 입력하는 대신 페이지에서 드롭다운 목록을 사용하는 것이 좋습니다. 정보를 수동으로 입력하는 경우 정규화된 Azure 리소스 ID를 입력합니다. 작업에는 리소스 ID 대신 친숙한 이름이 표시됩니다. 

환경 이름은 DevTest 랩에 표시된 이름입니다. 각 빌드에 대한 고유한 이름이어야 합니다. 예: **TestEnv$(Build.BuildId)**. 

리소스 관리자 템플릿에 정보를 전달하도록 매개 변수 파일 또는 매개 변수를 지정할 수 있습니다. 

환경 템플릿 출력 옵션을 **기반으로 출력 변수 만들기를** 선택하고 참조 이름을 입력합니다. 이 예제에서는 참조 이름에 **대해 BaseEnv를** 입력합니다. 다음 작업을 구성할 때 이 BaseEnv를 사용합니다. 

![Azure DevTest 랩 환경 작업 만들기](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>환경 작업 채우기
두 번째**작업(Azure DevTest Labs 채우기 환경** 작업)은 기존 DevTest 랩 환경을 업데이트하는 것입니다. 환경 만들기 작업은 이 작업에 대한 환경 이름을 구성하는 데 사용되는 **BaseEnv.environmentResourceId를** 출력합니다. 이 예제의 리소스 관리자 템플릿에는 두 가지 매개 변수인 **adminUserName** 및 **adminPassword**. 

![Azure DevTest 랩 환경 작업 채우기](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>앱 서비스 배포 작업
세 번째 작업은 **Azure 앱 서비스 배포** 작업입니다. 앱 유형이 **웹 앱으로** 설정되고 앱 서비스 이름이 **$(WebSite)로**설정됩니다.

![앱 서비스 배포 작업](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>릴리스 파이프라인 설정
두 가지 작업으로 릴리스 파이프라인을 만듭니다: **Azure 배포: 리소스 그룹 만들기 또는 업데이트** 및 Azure 앱 서비스 **배포.** 

첫 번째 작업의 경우 리소스 그룹의 이름과 위치를 지정합니다. 템플릿 위치는 연결된 아티팩트입니다. 리소스 관리자 템플릿에 연결된 템플릿이 포함된 경우 사용자 지정 리소스 그룹 배포를 구현해야 합니다. 템플릿이 게시된 드롭 아티팩트에 있습니다. 리소스 관리자 템플릿에 대한 템플릿 매개 변수를 재정의합니다. 나머지 설정을 기본값으로 남길 수 있습니다. 

두 번째 작업의 경우 **Azure 앱 서비스 배포**, Azure 구독을 지정하고 앱 **유형에**대한 **웹 앱을** 선택하고 앱 **서비스 이름에**대해 **$(WebSite)를** 선택합니다. 나머지 설정을 기본값으로 남길 수 있습니다. 

## <a name="test-run"></a>테스트 실행
이제 두 파이프라인이 모두 설정되었으므로 빌드를 수동으로 큐에 대기하고 작동하는지 확인합니다. 다음 단계는 빌드에 적합한 트리거를 설정하고 빌드를 릴리스 파이프라인에 연결하는 것입니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [Azure DevTest 랩을 Azure 파이프라인에 통합하여 지속적인 통합 및 제공 파이프라인](devtest-lab-integrate-ci-cd-vsts.md)
- [Azure 파이프라인 CI/CD 파이프라인에 환경 통합](integrate-environments-devops-pipeline.md)
