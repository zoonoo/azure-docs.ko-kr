---
title: Azure DevTest 랩에서 Azure DevOps에서 이미지 팩터리 실행
description: 이 문서에서는 Azure DevOps(이전의 Visual Studio 팀 서비스)에서 이미지 팩터리를 실행하는 데 필요한 모든 준비 에 대해 다룹니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758685"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Azure DevOps에서 이미지 팩터리 실행
이 문서에서는 Azure DevOps(이전의 Visual Studio 팀 서비스)에서 이미지 팩터리를 실행하는 데 필요한 모든 준비 에 대해 다룹니다.

> [!NOTE]
> 모든 오케스트레이션 엔진이 작동합니다! Azure DevOps는 필수가 아닙니다. 이미지 팩터리는 Azure PowerShell 스크립트를 사용하여 실행되므로 Windows 작업 스케줄러, 다른 CI/CD 시스템 등을 사용하여 수동으로 실행할 수 있습니다.

## <a name="create-a-lab-for-the-image-factory"></a>이미지 팩터리용 랩 만들기
이미지 팩터리 를 설정하는 첫 번째 단계는 Azure DevTest 랩에서 랩을 만드는 것입니다. 이 랩은 가상 컴퓨터를 만들고 사용자 지정 이미지를 저장하는 이미지 팩터리 랩입니다. 이 랩은 전체 이미지 팩터리 프로세스의 일부로 간주됩니다. 랩을 만든 후에는 나중에 필요하므로 이름을 저장해야 합니다.

## <a name="scripts-and-templates"></a>스크립트 및 템플릿
팀의 이미지 팩터리 채택의 다음 단계는 사용 가능한 것을 이해하는 것입니다. 이미지 팩터리 스크립트 및 템플릿은 [DevTest Labs GitHub 리포지토리에서](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)공개적으로 사용할 수 있습니다. 다음은 조각의 개요입니다.

- 이미지 팩토리. 그것은 루트 폴더입니다.
    - 구성 이미지 팩터리에 대한 입력
        - 골든 이미지. 이 폴더에는 사용자 지정 이미지의 정의를 나타내는 JSON 파일이 포함되어 있습니다.
        - Labs.json. 팀이 특정 사용자 지정 이미지를 수신하도록 등록하는 파일입니다.
- 스크립트. 이미지 팩터리의 엔진입니다.

이 섹션의 문서에서는 이러한 스크립트 및 템플릿에 대한 자세한 내용을 제공합니다.

## <a name="create-an-azure-devops-team-project"></a>Azure DevOps 팀 프로젝트 만들기
Azure DevOps를 사용하면 소스 코드를 저장하고 Azure PowerShell을 한 곳에서 실행할 수 있습니다. 이미지를 최신 상태로 유지하기 위해 되풀이 실행을 예약할 수 있습니다. 결과를 기록하여 문제를 진단할 수 있는 좋은 시설이 있습니다.  그러나 Azure DevOps를 사용하는 것은 요구 사항이 아니며 Azure에 연결할 수 있고 Azure PowerShell을 실행할 수 있는 모든 하네스/엔진을 사용할 수 있습니다.

대신 사용하려는 기존 DevOps 계정 또는 프로젝트가 있는 경우 이 단계를 건너뜁니다.

시작하려면 Azure DevOps에서 무료 계정을 만드세요. Azure https://www.visualstudio.com/ **DevOps(이전** VSTS)에서 **무료로 시작하기를** 방문하여 선택합니다. 고유한 계정 이름을 선택하고 Git을 사용하여 코드를 관리하도록 선택해야 합니다. 이 작업이 만들어지면 팀 프로젝트에 URL을 저장합니다. 다음은 샘플 `https://<accountname>.visualstudio.com/MyFirstProject`URL입니다.

## <a name="check-in-the-image-factory-to-git"></a>Git에 이미지 팩토리를 확인
이미지 팩터리의 모든 PowerShell, 템플릿 및 구성은 [공용 DevTest Labs GitHub 리포지토리에](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)있습니다. 새 팀 프로젝트에 코드를 가져오는 가장 빠른 방법은 리포지토리를 가져오는 것입니다. 이렇게 하면 전체 DevTest Labs 리포지토리가 가져옵니다(추가 문서 및 샘플을 얻을 수 있습니다).

1. 이전 단계에서 만든 Azure DevOps 프로젝트를 방문하십시오(URL은 **\//\<https: 계정 이름>.visualstudio.com/MyFirstProject)처럼**보입니다.
2. **리포지토리 가져오기를**선택합니다.
3. DevTest 연구소 리포지토리의 **복제 URL을** `https://github.com/Azure/azure-devtestlab`입력합니다.
4. **가져오기**를 선택합니다.

    ![Git 리포지토리 가져오기](./media/set-up-devops-lab/import-git-repo.png)

필요한 것(이미지 팩터리 파일)만 체크 인하기로 결정한 경우 [다음](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) 단계를 수행하여 Git 리포지토리를 복제하고 **스크립트/ImageFactory** 디렉터리에 있는 파일만 푸시합니다.

## <a name="create-a-build-and-connect-to-azure"></a>빌드 만들기 및 Azure에 연결
이 시점에서 Azure DevOps의 Git 리포지토리에 저장된 원본 파일이 있습니다. 이제 Azure PowerShell을 실행하려면 파이프라인을 설정해야 합니다. 이러한 단계를 수행하는 옵션이 많이 있습니다. 이 문서에서는 단순화하기 위해 빌드 정의를 사용하지만 DevOps 빌드, DevOps 릴리스(단일 또는 다중 환경), Windows 작업 스케줄러 와 같은 다른 실행 엔진 또는 Azure PowerShell을 실행할 수 있는 다른 실행 엔진과 함께 작동합니다.

> [!NOTE]
> PowerShell 파일 중 일부를 만드는 데 10개 이상의 사용자 지정 이미지가 많을 때 실행하는 데 시간이 오래 걸린다는 점에 유의해야 합니다. 무료 호스팅 DevOps 빌드/릴리스 에이전트는 30분의 시간 시간을 가지므로 많은 이미지 빌드를 시작하면 무료 호스팅 에이전트를 사용할 수 없습니다. 이 시간 제한 챌린지는 사용하려는 하네스에 적용되며 장기 실행 Azure PowerShell 스크립트에 대한 일반적인 시간 제한을 미리 확인하는 것이 좋습니다. Azure DevOps의 경우 유료 호스팅 에이전트를 사용하거나 사용자 고유의 빌드 에이전트를 사용할 수 있습니다.

1. 시작하려면 DevOps 프로젝트의 홈페이지에서 **빌드 설정을** 선택합니다.

    ![빌드 설정 버튼](./media/set-up-devops-lab/setup-build-button.png)
2. 빌드의 **이름을** 지정합니다(예: 개발자 테스트 랩에 이미지 빌드 및 배달).
3. **빈** 빌드 정의를 선택하고 **적용을** 선택하여 빌드를 만듭니다.
4. 이 단계에서 빌드 에이전트에 대해 **Hosted를** 선택할 수 있습니다.
5. 빌드 정의를 **저장합니다.**

    ![빌드 정의](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>빌드 변수 구성
명령줄 매개 변수를 단순화하려면 이미지 팩터리를 빌드 변수 집합으로 구동하는 키 값을 캡슐화합니다. 변수 **탭을** 선택하면 몇 가지 기본 변수 목록이 표시됩니다. Azure DevOps에 입력할 변수 목록은 다음과 같습니다.


| 변수 이름 | 값 | 메모 |
| ------------- | ----- | ----- |
| 구성 위치 | /스크립트/이미지팩토리/구성 | 이 경로는 **구성** 폴더에 대한 리포지토리의 전체 경로입니다. 위의 전체 리포지토리를 가져온 경우 왼쪽값이 정확합니다. 그렇지 않으면 구성 위치를 가리키도록 업데이트합니다. |
| 데브테스트랩네임 | 마이이미지팩토리 | Azure DevTest 랩의 랩 이름은 이미지를 생성하는 공장으로 사용됩니다. 없는 경우 하나를 만듭니다. 랩이 서비스 끝점에 액세스할 수 있는 것과 동일한 구독에 있는지 확인합니다. |
| 이미지 보존 | 1 | 각 유형을 저장할 이미지 수입니다. 기본값을 1로 설정합니다. |
| 기계 암호 | ******* | 가상 시스템에 대한 기본 제공 관리자 계정 암호입니다. 이 계정은 일시적인 계정이므로 안전한지 확인하십시오. 오른쪽에 있는 작은 잠금 아이콘을 선택하여 안전한 문자열인지 확인합니다. |
| 기계 사용자 이름 | 이미지팩토리유저 | 가상 시스템에 대한 기본 제공 관리자 계정 사용자 이름입니다. 일시적인 계정입니다. |
| 표준 시간 초과 분 | 30 | 정기적인 Azure 작업을 기다려야 하는 시간 시간 입니다. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 랩이 있는 구독의 ID와 서비스 끝점에 액세스할 수 있는 ID입니다. |
| VMSize | Standard_A3 | **만들기** 단계에 사용할 가상 시스템의 크기입니다. 생성된 VM은 일시적입니다. 크기는 [랩에 대해 활성화된](devtest-lab-set-lab-policy.md)크기여야 합니다. 구독 코어 할당량이 충분한지 [확인합니다.](../azure-resource-manager/management/azure-subscription-service-limits.md)

![변수 빌드](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Azure에 연결
다음 단계는 서비스 주체를 설정하는 것입니다. 이 ID는 Azure Active Directory의 ID로, DevOps 빌드 에이전트가 사용자를 대신하여 Azure에서 작동할 수 있도록 합니다. 설정하려면 먼저 Azure PowerShell 빌드 단계를 추가하는 것으로 시작합니다.

1. **작업 추가**를 선택합니다.
2. Azure **PowerShell을**검색합니다.
3. 일단 그것을 발견, 빌드에 작업을 추가 하려면 **추가** 선택 합니다. 이렇게 하면 작업이 추가됨에 따라 왼쪽에 표시되는 작업이 표시됩니다.

![파워쉘 단계 설정](./media/set-up-devops-lab/set-up-powershell-step.png)

서비스 주체를 설정하는 가장 빠른 방법은 Azure DevOps가 이를 수행하도록 하는 것입니다.

1. 방금 추가한 **작업을** 선택합니다.
2. **Azure 연결 유형의**경우 **Azure 리소스 관리자를**선택합니다.
3. 관리 **링크를** 선택하여 서비스 주체를 설정합니다.

자세한 내용은 이 [블로그 게시물을](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)참조하십시오. **관리** 링크를 선택하면 DevOps(블로그 게시물의 두 번째 스크린샷)에서 Azure에 대한 연결을 설정하기 위해 올바른 위치에 도착하게 됩니다. 이 설정을 설정할 때 **Azure 리소스 관리자 서비스 끝점을** 선택해야 합니다.

## <a name="complete-the-build-task"></a>빌드 작업 완료
빌드 작업을 선택하면 입력해야 하는 오른쪽 창에 모든 세부 정보가 표시됩니다.

1. 첫째, 빌드 작업의 이름을 **지정합니다.**
2. **Azure 리소스 관리자를** 선택하여 만든 서비스 **주체선택**
3. 서비스 **끝점을 선택합니다.**
4. **스크립트 경로의**경우 **다음을 선택합니다. (타원)** 오른쪽에 있습니다.
5. **만들기골든이미지VMs.ps1** 스크립트로 이동합니다.
6. 스크립트 매개 변수는 다음과 같아야 합니다.`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![빌드 정의 완료](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>빌드 큐
새 빌드를 대기하여 모든 것을 올바르게 설정했는지 확인해 보겠습니다. 빌드가 실행되는 동안 Azure [포털로](https://portal.azure.com) 전환하고 이미지 팩터리 랩의 **모든 가상 컴퓨터를** 선택하여 모든 것이 제대로 작동하는지 확인합니다. 랩에서 세 개의 가상 시스템이 만들어지는 것을 볼 수 있습니다.

![랩의 VM](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>다음 단계
Azure DevTest 랩을 기반으로 이미지 팩터리를 설정하는 첫 번째 단계가 완료되었습니다. 시리즈의 다음 문서에서는 이러한 VM을 일반화하고 사용자 지정 이미지에 저장합니다. 그런 다음 다른 모든 랩에 배포해야 합니다. 시리즈의 다음 문서 보기: [사용자 지정 이미지를 저장 하고 여러 랩에 배포 합니다.](image-factory-save-distribute-custom-images.md)
