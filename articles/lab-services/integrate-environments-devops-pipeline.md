---
title: Azure DevTest 랩에서 Azure 파이프라인에 환경을 통합
description: Azure DevTest Labs 환경을 CI(Azure DevOps) 연속 통합 및 CD(연속 배달) 파이프라인에 통합하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
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
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169418"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Azure DevOps CI/CD 파이프라인에 환경 통합
Azure DevOps 서비스(이전의 Visual Studio Team Services)에 설치된 Azure DevTest Labs 작업 확장을 사용하여 지속적인 통합(CI)/CD(연속 배달) 빌드 및 릴리스 파이프라인을 Azure DevTest Labs와 쉽게 통합할 수 있습니다. 이러한 확장을 사용하면 특정 테스트 작업에 대한 [환경을](devtest-lab-test-env.md) 신속하게 배포한 다음 테스트가 완료되면 삭제하기가 더 쉬워집니다. 

이 문서에서는 환경을 만들고 배포한 다음 환경을 하나의 완전한 파이프라인에서 모두 삭제하는 방법을 보여 주며 이 문서에서는 일반적으로 사용자 지정 빌드 테스트 배포 파이프라인에서 이러한 각 작업을 개별적으로 수행합니다. 이 문서에서 사용되는 확장은 [DTL VM 작업을 만들/삭제하는](devtest-lab-integrate-ci-cd-vsts.md)것 외에도 다음과 같습니다.

- 환경 만들기
- 환경 삭제

## <a name="before-you-begin"></a>시작하기 전에
CI/CD 파이프라인을 Azure DevTest 랩과 통합하기 전에 Visual Studio 마켓플레이스에서 [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) 확장을 설치합니다. 

## <a name="create-and-configure-the-lab-for-environments"></a>환경에 맞게 랩 만들기 및 구성
이 섹션에서는 Azure 환경을 배포할 랩을 만들고 구성하는 방법에 대해 설명합니다.

1. 아직 랩이 없는 경우 [랩을 만듭니다.](devtest-lab-create-lab.md) 
2. 이 문서의 지침: [Azure 리소스 관리자 템플릿을 사용하여 다중 VM 환경 및 PaaS 리소스 만들기의](devtest-lab-create-environment-from-arm.md)지침에 따라 랩을 구성하고 환경 템플릿을 만듭니다.
3. 이 예제에서는 기존 Azure 빠른 [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)시작 템플릿을 사용합니다.
4. **201-web-app-redis-cache-sql-database** 폴더를 2단계에서 구성된 리포지토리의 **ArmTemplate** 폴더에 복사합니다.

## <a name="create-a-release-definition"></a>릴리스 정의 만들기
릴리스 정의를 만들려면 다음을 수행합니다.

1.  빌드 & 릴리스 **허브의** **릴리스** 탭에서 **더하기 기호(+)** 단추를 선택합니다.
2.  **릴리스 정의 만들기** 창에서 **빈** 템플릿을 선택한 후, **다음**을 선택합니다.
3.  **나중에 선택**을 선택한 후 **만들기**를 선택하여 하나의 기본 환경이 있고 연결된 아티팩트가 없는 새 릴리스 정의를 만듭니다.
4.  바로 가기 메뉴를 열려면 새 릴리스 정의에서 환경 이름 옆에 있는 **타원(...)을** 선택한 다음 **변수 구성을 선택합니다.**
5.  **구성 - 환경** 창에서 릴리스 정의 작업에 사용하는 변수에 다음 값을 입력합니다.
1.  **administratorLogin의**경우 SQL 관리자 로그인 이름을 입력합니다.
2.  **administratorLoginPassword의**경우 SQL 관리자 로그인에서 사용할 암호를 입력합니다. 암호를 숨기고 보호하려면 “자물쇠” 아이콘을 사용합니다.
3.  **데이터베이스Name의**경우 SQL 데이터베이스 이름을 입력합니다.
4.  이러한 변수는 예제 환경에 따라 다르며, 서로 다른 환경은 서로 다른 변수를 가질 수 있습니다.

## <a name="create-an-environment"></a>환경 만들기
배포의 다음 단계는 개발 또는 테스트 목적으로 사용할 환경을 만드는 것입니다.

1. 릴리스 정의에서 **작업 추가**를 선택합니다.
2. **작업** 탭에서 Azure DevTest 랩 만들기 환경 작업을 추가합니다. 아래와 같이 작업을 구성합니다.
    1. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.
2. **랩 이름의**경우 이전에 만든 인스턴스의 이름을 선택합니다*.
3. **리포지토리 이름의**경우 리소스 관리자 템플릿(201)이 *로 푸시된 리포지토리를 선택합니다.
4. **템플릿 이름의**경우 소스 코드 리포지토리에 저장한 환경의 이름을 선택합니다*. 
5. **랩 이름,** **리포지토리 이름**및 템플릿 **이름은** Azure 리소스 아이디의 친숙한 표현입니다. 친숙한 이름을 수동으로 입력하면 오류가 발생하고 드롭다운 목록을 사용하여 정보를 선택합니다.
6. **환경 이름의**경우 이름을 입력하여 랩 내의 환경 인스턴스를 고유하게 식별합니다.  랩 내에서 고유해야 합니다.
7. **매개 변수 파일** 및 매개 **변수는**사용자 지정 매개 변수를 환경에 전달할 수 있도록 합니다. 매개 변수 값을 설정하는 데 사용할 수 있습니다. 이 예제에서는 매개 변수 섹션이 사용됩니다. 예를 들어 환경에서 정의한 변수의 이름을 사용합니다.`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. 환경 템플릿 내의 정보는 템플릿의 출력 섹션에서 전달할 수 있습니다. 다른 작업이 데이터를 사용할 수 있도록 **환경 템플릿 출력을 기반으로 출력 변수 만들기를** 선택합니다. `$(Reference name.Output Name)`은 따라야 할 패턴입니다. 예를 들어 참조 이름이 DTL이고 템플릿의 출력 이름이 위치인 경우 `$(DTL.location)`변수가 됩니다.

## <a name="delete-the-environment"></a>환경 삭제
마지막 단계는 Azure DevTest Labs 인스턴스에 배포한 환경을 삭제하는 것입니다. 일반적으로 개발 작업을 실행하거나 배포된 리소스에 필요한 테스트를 실행한 후 환경을 삭제합니다.

릴리스 정의에서 **작업 추가를**선택한 다음 **배포** 탭에서 **Azure DevTest Labs 삭제 환경** 작업을 추가합니다. 다음과 같이 구성합니다.

1. VM을 삭제하려면 [Azure DevTest 랩 작업을](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)참조하십시오.
    1. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.
    2. **랩 이름에**대해 환경이 있는 랩을 선택합니다.
    3. **환경 이름의**경우 제거할 환경의 이름을 입력합니다.
2. 릴리스 정의 대한 이름을 입력한 다음, 저장합니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 
- [리소스 관리자 템플릿을 사용하여 다중 VM 환경을 만듭니다.](devtest-lab-create-environment-from-arm.md)
- DevTest 연구소 [GitHub 리포지토리에서 DevTest](https://github.com/Azure/azure-quickstart-templates)랩 자동화에 대한 빠른 시작 리소스 관리자 템플릿 .
- [VSTS 문제 해결 페이지](/azure/devops/pipelines/troubleshooting)

