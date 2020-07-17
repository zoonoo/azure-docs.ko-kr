---
title: Azure DevTest Labs |에서 보존 정책 설정 Microsoft Docs
description: 보존 정책을 구성 하 고, 팩터리를 정리 하 고, DevTest Labs에서 이전 이미지를 사용 중지 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 115fdff215399a9a51171161191ecf5009e8e20e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85476056"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Azure DevTest Labs에서 보존 정책 설정
이 문서에서는 보존 정책을 설정 하 고, 팩터리를 정리 하 고, 조직의 다른 모든 DevTest 랩에서 이전 이미지를 사용 중지 하는 방법을 설명 합니다. 

## <a name="prerequisites"></a>사전 요구 사항
계속 진행 하기 전에 다음 문서를 따랐는지 확인 합니다.

- [이미지 팩터리 만들기](image-factory-create.md)
- [Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md)
- [사용자 지정 이미지 저장 및 여러 랩에 배포](image-factory-save-distribute-custom-images.md)

다음 항목이 이미 준비 되어 있어야 합니다.

- Azure DevTest Labs에서 이미지 팩터리의 랩
- 팩터리가 골든 이미지를 배포 하는 하나 이상의 대상 Azure DevTest Labs
- 이미지 팩터리를 자동화 하는 데 사용 되는 Azure DevOps 프로젝트입니다.
- 스크립트 및 구성이 포함 된 소스 코드 위치 (이 예제에서는 위에서 사용 된 것과 동일한 DevOps 프로젝트에서)
- Azure Powershell 작업을 오케스트레이션 하는 빌드 정의
 
## <a name="setting-the-retention-policy"></a>보존 정책 설정
정리 단계를 구성 하기 전에 DevTest Labs에 보존할 기록 이미지 수를 정의 합니다. [Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md) 문서를 실행 한 후에는 다양 한 빌드 변수를 구성 했습니다. 그 중 하나가 **ImageRetention**되었습니다. 이 변수를로 설정 합니다 `1` . 즉, DevTest Labs에서 사용자 지정 이미지의 기록을 유지 하지 않습니다. 최신 분산 이미지만 사용할 수 있습니다. 이 변수를로 변경 하는 경우 `2` 최신 분산 이미지와 이전 이미지를 합한 값이 유지 됩니다. 이 값을 설정 하 여 DevTest Labs에서 유지 하려는 기록 이미지의 수를 정의할 수 있습니다.

## <a name="cleaning-up-the-factory"></a>팩터리를 정리 하는 중
팩터리를 정리 하는 첫 번째 단계는 이미지 팩터리에서 골든 이미지 Vm을 제거 하는 것입니다. 이전 스크립트와 마찬가지로이 작업을 수행 하는 스크립트가 있습니다. 첫 번째 단계는 다음 이미지와 같이 빌드 정의에 다른 **Azure Powershell** 작업을 추가 하는 것입니다.

![PowerShell 단계](./media/set-retention-policy-cleanup/powershell-step.png)

목록에 새 작업이 있으면 항목을 선택 하 고 다음 이미지에 표시 된 것 처럼 모든 세부 정보를 입력 합니다.

![이전 이미지 정리 PowerShell 작업](./media/set-retention-policy-cleanup/configure-powershell-task.png)

스크립트 매개 변수는 `-DevTestLabName $(devTestLabName)` 입니다.

## <a name="retire-old-images"></a>이전 이미지 사용 중지 
이 작업은 **ImageRetention** 빌드 변수와 일치 하는 기록만 유지 하면서 모든 이전 이미지를 제거 합니다. 빌드 정의에 **Azure Powershell** 빌드 작업을 추가 합니다. 추가 되 면 작업을 선택 하 고 다음 이미지와 같이 세부 정보를 입력 합니다. 

![이전 이미지 PowerShell 작업 사용 중지](./media/set-retention-policy-cleanup/retire-old-image-task.png)

스크립트 매개 변수는 다음과 같습니다.`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>빌드 큐 대기
이제 빌드 정의를 완료 했으므로 새 빌드를 큐에 대기 하 여 모든 것이 작동 하는지 확인 합니다. 빌드가 성공적으로 완료 되 면 새 사용자 지정 이미지가 대상 랩에서 표시 되 고 이미지 팩터리 랩을 확인 하는 경우 프로 비전 된 Vm이 표시 되지 않습니다. 또한 추가 빌드를 큐에 대기 하는 경우 빌드 변수에 설정 된 보존 값에 따라 DevTest Labs에서 이전 사용자 지정 이미지를 사용 중지 하는 정리 작업이 표시 됩니다.

> [!NOTE]
> 시리즈의 마지막 아티클이 끝날 때 빌드 파이프라인을 실행 한 경우 새 빌드를 큐에 대기 시키기 전에 이미지 팩터리 랩에서 만든 가상 머신을 수동으로 삭제 합니다.  수동 정리 단계는 모든 항목을 설정 하 고 작동 하는지 확인 하는 경우에만 필요 합니다.



## <a name="summary"></a>요약
이제 주문형으로 랩에 사용자 지정 이미지를 생성 하 고 배포할 수 있는 실행 중인 이미지 팩터리가 있습니다. 이 시점에서 이미지를 올바르게 설정 하 고 대상 랩을 식별 하기만 하면 됩니다. 이전 문서에서 설명한 대로 **구성** 폴더에 있는 파일 **에 대 한Labs.js** 는 각 대상 랩에서 사용할 수 있도록 설정할 이미지를 지정 합니다. 다른 DevTest Labs를 조직에 추가 하는 경우 새 랩에 대 한 Labs.js에 항목을 추가 하기만 하면 됩니다.

팩터리에 새 이미지를 추가 하는 것도 간단 합니다. 팩터리에 새 이미지를 포함 하려는 경우 [Azure Portal](https://portal.azure.com)을 열고, 공장 DevTest Labs로 이동 하 고, VM을 추가 하는 단추를 선택 하 고, 원하는 마켓플레이스 이미지 및 아티팩트를 선택 합니다. **만들기** 단추를 선택 하 여 새 VM을 만드는 대신 **Azure Resource Manager 템플릿 보기**"를 선택 하 고 템플릿을 리포지토리의 **GoldenImages** 폴더 내에 있는 json 파일로 저장 합니다. 다음 번에 이미지 팩터리를 실행 하면 사용자 지정 이미지가 만들어집니다.


## <a name="next-steps"></a>다음 단계
1. [빌드/릴리스를 예약](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) 하 여 이미지 팩터리를 정기적으로 실행 합니다. 공장에서 생성 된 이미지를 정기적으로 새로 고칩니다.
2. 팩터리에 대 한 골든 이미지를 만드세요. 또한 VM 설치 작업의 추가 부분을 스크립팅 하는 [아티팩트를 만들고](devtest-lab-artifact-author.md) 팩터리 이미지에 아티팩트를 포함 하는 것을 고려할 수 있습니다.
4. [별도의 빌드/릴리스](/azure/devops/pipelines/overview?view=azure-devops-2019) 를 만들어 **DistributeImages** 스크립트를 별도로 실행 합니다. Labs.js를 변경 하 고 모든 이미지를 다시 다시 만들지 않고도 대상 랩에 복사 된 이미지를 가져오는 경우이 스크립트를 실행할 수 있습니다.

