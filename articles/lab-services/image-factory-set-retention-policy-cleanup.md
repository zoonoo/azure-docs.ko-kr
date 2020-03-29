---
title: Azure DevTest 랩에서 보존 정책 설정 | 마이크로 소프트 문서
description: 보존 정책을 구성하고, 공장을 정리하고, DevTest Labs에서 오래된 이미지를 폐기하는 방법을 알아봅니다.
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
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759417"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Azure DevTest 랩에서 보존 정책 설정
이 문서에서는 보존 정책 설정, 공장 정리 및 조직의 다른 모든 DevTest Labs에서 이전 이미지를 폐기하는 것을 다룹니다. 

## <a name="prerequisites"></a>사전 요구 사항
계속 하기 전에 다음 문서를 따라했는지 확인하십시오.

- [이미지 팩터리 만들기](image-factory-create.md)
- [Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md)
- [사용자 지정 이미지 저장 및 여러 랩에 배포](image-factory-save-distribute-custom-images.md)

다음 항목은 이미 배치되어 있어야 합니다.

- Azure DevTest 랩의 이미지 팩터리용 랩
- 공장에서 황금 이미지를 배포할 하나 이상의 대상 Azure DevTest 랩
- 이미지 팩터리를 자동화하는 데 사용되는 Azure DevOps 프로젝트입니다.
- 스크립트 및 구성을 포함하는 소스 코드 위치(이 예에서는 위에서 사용한 것과 동일한 DevOps 프로젝트에서)
- Azure Powershell 작업을 오케스트레이션하는 빌드 정의
 
## <a name="setting-the-retention-policy"></a>보존 정책 설정
정리 단계를 구성하기 전에 DevTest 연구소에서 유지하려는 기록 이미지 수를 정의합니다. [Azure DevOps 문서에서 이미지 팩터리 실행을](image-factory-set-up-devops-lab.md) 따랐을 때 다양한 빌드 변수를 구성했습니다. 그 중 하나는 **이미지 보존**. 이 변수를 `1`로 설정하면 DevTest Labs가 사용자 지정 이미지 기록을 유지 하지 않습니다. 최신 배포 이미지만 사용할 수 있습니다. 이 변수를 `2`로 변경하면 최신 분산 이미지와 이전 이미지가 유지됩니다. 이 값을 설정하여 DevTest Labs에서 유지 관리하려는 기록 이미지 수를 정의할 수 있습니다.

## <a name="cleaning-up-the-factory"></a>공장 청소
공장 정리의 첫 번째 단계는 이미지 팩토리에서 황금 이미지 VM을 제거하는 것입니다. 이전 스크립트와 마찬가지로 이 작업을 수행하는 스크립트가 있습니다. 첫 번째 단계는 다음 이미지와 같이 빌드 정의에 다른 **Azure Powershell** 작업을 추가하는 것입니다.

![파워쉘 스텝](./media/set-retention-policy-cleanup/powershell-step.png)

목록에 새 작업이 있으면 항목을 선택하고 다음 이미지와 같이 모든 세부 정보를 입력합니다.

![이전 이미지 PowerShell 작업 정리](./media/set-retention-policy-cleanup/configure-powershell-task.png)

스크립트 매개 변수는 `-DevTestLabName $(devTestLabName)`다음과 같습니다.

## <a name="retire-old-images"></a>오래된 이미지 폐기 
이 작업은 이전 이미지를 제거하여 **ImageRetention** 빌드 변수와 일치하는 기록만 유지합니다. 빌드 정의에 **Azure Powershell** 빌드 작업을 추가합니다. 작업이 추가되면 작업을 선택하고 다음 이미지와 같이 세부 정보를 입력합니다. 

![이전 이미지 PowerShell 작업 은 폐기](./media/set-retention-policy-cleanup/retire-old-image-task.png)

스크립트 매개 변수는 다음과 같습니다.`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>빌드 큐
빌드 정의를 완료한 후 새 빌드를 큐에 대기하여 모든 것이 작동하는지 확인합니다. 빌드가 완료되면 대상 랩에 새 사용자 지정 이미지가 표시되고 이미지 팩터리 랩을 확인하면 프로비저닝된 VM이 표시되지 않습니다. 또한 빌드를 더 많이 큐업하면 빌드 변수에 설정된 보존 값에 따라 DevTest Labs에서 오래된 사용자 지정 이미지를 폐기하는 정리 작업이 표시됩니다.

> [!NOTE]
> 시리즈의 마지막 아티클이 끝날 때 빌드 파이프라인을 실행한 경우 새 빌드를 대기하기 전에 이미지 팩터리 랩에서 만든 가상 컴퓨터를 수동으로 삭제합니다.  수동 정리 단계는 모든 것을 설정하고 작동하는지 확인하는 동안에만 필요합니다.



## <a name="summary"></a>요약
이제 필요에 따라 랩에 사용자 지정 이미지를 생성하고 배포할 수 있는 실행 중인 이미지 팩터리가 있습니다. 이 시점에서 이미지를 올바르게 설정하고 대상 랩을 식별하는 것만으로도 문제가 됩니다. 이전 문서에서 설명한 것처럼 **구성** 폴더에 있는 **Labs.json** 파일은 각 대상 랩에서 사용할 수 있는 이미지를 지정합니다. 조직에 다른 DevTest 랩을 추가할 때 새 랩에 대한 Labs.json에 항목을 추가하기만 하면 됩니다.

공장에 새 이미지를 추가하는 것도 간단합니다. 팩터리에 새 이미지를 포함하려면 [Azure 포털을](https://portal.azure.com)열고, 공장 DevTest Labs로 이동한 다음 VM을 추가할 단추를 선택하고 원하는 마켓플레이스 이미지 및 아티팩트를 선택합니다. 새 VM을 만들기 위해 **만들기** 단추를 선택하는 대신 **Azure 리소스 관리자 템플릿 보기를**선택하고 저장소의 **GoldenImages** 폴더 내의 .json 파일로 템플릿을 저장합니다. 다음에 이미지 팩터리를 실행하면 사용자 지정 이미지가 생성됩니다.


## <a name="next-steps"></a>다음 단계
1. [빌드/릴리스를 예약하여](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) 이미지 팩터리를 주기적으로 실행합니다. 공장에서 생성된 이미지를 정기적으로 새로 고칩니다.
2. 당신의 공장에 대한 더 많은 황금 이미지를 확인합니다. 또한 VM 설치 작업의 추가 부분을 스크립팅하고 팩터리 이미지에 아티팩트를 포함하도록 [아티팩트를 만드는](devtest-lab-artifact-author.md) 것도 고려해 볼 수 있습니다.
4. 별도의 [빌드/릴리스를](/azure/devops/pipelines/overview?view=azure-devops-2019) 만들어 DistributeImages 스크립트를 별도로 **실행합니다.** Labs.json을 변경하고 모든 이미지를 다시 만들지 않고도 대상 랩에 이미지를 복사할 때 이 스크립트를 실행할 수 있습니다.

