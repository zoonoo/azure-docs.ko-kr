---
title: 환경을 Azure Pipelines에 통합 Azure DevTest Labs
description: Azure DevTest Labs 환경을 Azure DevOps CI (지속적인 통합) 및 CD (지속적인 업데이트) 파이프라인에 통합 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483026"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Azure DevOps CI/CD 파이프라인에 환경 통합
Azure DevOps Services (이전의 Visual Studio Team Services)에 설치 된 Azure DevTest Labs 작업 확장을 사용 하 여 CI (지속적인 통합)/CD (지속적인 업데이트) 빌드 및 릴리스 파이프라인을 Azure DevTest Labs와 쉽게 통합할 수 있습니다. 이러한 확장을 통해 특정 테스트 작업에 대 한 [환경을](devtest-lab-test-env.md) 신속 하 게 배포 하 고 테스트가 완료 되 면 삭제할 수 있습니다. 

이 문서에서는 하나의 전체 파이프라인에서 환경을 만들고 배포한 다음 환경을 삭제 하는 방법을 보여 줍니다. 일반적으로 사용자 지정 빌드-테스트-배포 파이프라인에서 이러한 각 작업을 개별적으로 수행 합니다. 이 문서에서 사용 되는 확장은 [DTL VM 만들기/삭제 작업](devtest-lab-integrate-ci-cd.md)외에도 다음과 같은 작업을 수행 합니다.

- 환경 만들기
- 환경 삭제

## <a name="before-you-begin"></a>시작하기 전에
CI/CD 파이프라인을 Azure DevTest Labs와 통합 하려면 Visual Studio Marketplace에서 [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) 확장을 설치 합니다. 

## <a name="create-and-configure-the-lab-for-environments"></a>환경에 대 한 랩 만들기 및 구성
이 섹션에서는 Azure 환경을 배포할 랩을 만들고 구성 하는 방법을 설명 합니다.

1. 아직 없는 경우 [랩을 만듭니다](devtest-lab-create-lab.md) . 
2. [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)문서의 지침에 따라 랩을 구성 하 고 환경 템플릿을 만듭니다.
3. 이 예에서는 기존 Azure 빠른 시작 템플릿을 사용 [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) 합니다.
4. 2 단계에서 구성한 리포지토리의 **ArmTemplate** 폴더에 **201-redis** 폴더를 복사 합니다 ().

## <a name="create-a-release-definition"></a>릴리스 정의 만들기
릴리스 정의를 만들려면 다음을 수행합니다.

1.  **빌드 & 릴리스 허브**의 **릴리스** 탭에서 **더하기 기호 (+)** 단추를 선택 합니다.
2.  **릴리스 정의 만들기** 창에서 **빈** 템플릿을 선택한 후, **다음**을 선택합니다.
3.  **나중에 선택**을 선택한 후 **만들기**를 선택하여 하나의 기본 환경이 있고 연결된 아티팩트가 없는 새 릴리스 정의를 만듭니다.
4.  바로 가기 메뉴를 열려면 새 릴리스 정의에서 환경 이름 옆에 있는 **줄임표 (...)** 를 선택 하 고 **변수 구성**을 선택 합니다.
5.  **구성 - 환경** 창에서 릴리스 정의 작업에 사용하는 변수에 다음 값을 입력합니다.
1.  관리자 **로그인**에 SQL 관리자 로그인 이름을 입력 합니다.
2.  **AdministratorLoginPassword**의 경우 SQL 관리자 로그인에 사용할 암호를 입력 합니다. 암호를 숨기고 보호하려면 “자물쇠” 아이콘을 사용합니다.
3.  **DatabaseName**에 SQL Database 이름을 입력 합니다.
4.  이러한 변수는 예제 환경에 따라 다르며 환경 마다 다른 변수가 있을 수 있습니다.

## <a name="create-an-environment"></a>환경 만들기
배포의 다음 단계는 개발 또는 테스트 목적으로 사용할 환경을 만드는 것입니다.

1. 릴리스 정의에서 **작업 추가**를 선택합니다.
2. **작업** 탭에서 환경 만들기 작업 Azure DevTest Labs 추가 합니다. 아래와 같이 작업을 구성합니다.
    1. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.
2. **랩 이름**에 대해 앞에서 만든 인스턴스의 이름을 선택 합니다. *
3. **리포지토리 이름**에 대해 리소스 관리자 템플릿 (201)이 *로 푸시되는 리포지토리를 선택 합니다.
4. **템플릿 이름**에서 소스 코드 리포지토리 *에 저장 한 환경의 이름을 선택 합니다. 
5. **랩 이름**, **리포지토리 이름**및 **템플릿 이름은** Azure 리소스 id에 대 한 친숙 한 표현입니다. 이름을 수동으로 입력 하면 오류가 발생 하 고, 드롭다운 목록을 사용 하 여 정보를 선택 합니다.
6. **환경 이름**에 대해 랩 내에서 환경 인스턴스를 고유 하 게 식별 하는 이름을 입력 합니다.  랩 내에서 고유 해야 합니다.
7. **매개 변수 파일** 및 **매개 변수**를 사용 하 여 사용자 지정 매개 변수를 환경에 전달할 수 있습니다. 둘 중 하나 또는 둘 모두를 사용 하 여 매개 변수 값을 설정할 수 있습니다. 이 예에서는 매개 변수 섹션이 사용 됩니다. 환경에서 정의한 변수 이름을 사용 합니다. 예를 들면 다음과 같습니다.`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. 환경 템플릿 내의 정보는 템플릿의 출력 섹션에서 전달 될 수 있습니다. 다른 작업에서 데이터를 사용할 수 있도록 **환경 템플릿 출력을 기반으로 출력 변수 만들기** 를 선택 합니다. `$(Reference name.Output Name)`따라야 하는 패턴입니다. 예를 들어 참조 이름이 DTL이 고 템플릿의 출력 이름이 위치인 경우 변수는 `$(DTL.location)` 입니다.

## <a name="delete-the-environment"></a>환경 삭제
최종 단계는 Azure DevTest Labs 인스턴스에 배포한 환경을 삭제 하는 것입니다. 일반적으로 개발 작업을 실행 하거나 배포 된 리소스에 필요한 테스트를 실행 한 후 환경을 삭제 합니다.

릴리스 정의에서 **작업 추가**를 선택한 후 **배포** 탭에서 **Azure DevTest Labs 환경 삭제** 작업을 추가 합니다. 다음과 같이 구성합니다.

1. VM을 삭제 하려면 [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)을 참조 하세요.
    1. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.
    2. **랩 이름**에 환경이 있는 랩을 선택 합니다.
    3. **환경 이름**에 제거할 환경의 이름을 입력 합니다.
2. 릴리스 정의 대한 이름을 입력한 다음, 저장합니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 
- [리소스 관리자 템플릿으로 다중 VM 환경을 만듭니다](devtest-lab-create-environment-from-arm.md).
- [DevTest Labs GitHub 리포지토리에서](https://github.com/Azure/azure-quickstart-templates)DevTest labs 자동화를 위한 빠른 시작 리소스 관리자 템플릿입니다.
- [VSTS 문제 해결 페이지](/azure/devops/pipelines/troubleshooting)

