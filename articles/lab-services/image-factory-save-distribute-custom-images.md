---
title: Azure DevTest Labs |에 이미지 저장 및 배포 Microsoft Docs
description: 이 문서에서는 Azure DevTest Labs에 이미 생성 된 Vm (가상 머신)에서 사용자 지정 이미지를 저장 하는 단계를 제공 합니다.
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
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76759434"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>사용자 지정 이미지 저장 및 여러 랩에 배포
이 문서에서는 이미 만든 Vm (가상 머신)에서 사용자 지정 이미지를 저장 하는 단계를 제공 합니다. 또한 조직의 다른 DevTest Labs에 이러한 사용자 지정 이미지를 배포 하는 방법에 대해서도 설명 합니다.

## <a name="prerequisites"></a>전제 조건
다음 항목이 이미 준비 되어 있어야 합니다.

- Azure DevTest Labs에서 이미지 팩터리의 랩입니다.
- 이미지 팩터리를 자동화 하는 데 사용 되는 Azure DevOps 프로젝트입니다.
- 스크립트 및 구성이 포함 된 소스 코드 위치 (이 예제에서는 이전 단계에서 언급 한 것과 동일한 DevOps 프로젝트의 경우).
- Azure Powershell 작업을 오케스트레이션 하는 빌드 정의입니다.

필요한 경우 [Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md) 의 단계에 따라 이러한 항목을 만들거나 설정 합니다. 

## <a name="save-vms-as-generalized-vhds"></a>일반화 된 Vhd로 Vm 저장
기존 Vm을 일반화 된 Vhd로 저장 합니다.  기존 Vm을 일반화 된 Vhd로 저장 하는 샘플 PowerShell 스크립트가 있습니다. 이를 사용 하려면 먼저 다음 이미지에 표시 된 것 처럼 다른 **Azure Powershell** 작업을 빌드 정의에 추가 합니다.

![Azure PowerShell 단계 추가](./media/save-distribute-custom-images/powershell-step.png)

목록에서 새 작업을 완료 한 후에는 다음 이미지에 표시 된 것 처럼 모든 세부 정보를 입력할 수 있도록 항목을 선택 합니다. 

![PowerShell 설정](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>일반화 된 사용자 지정 이미지 및 특수 사용자 지정 이미지
[Azure Portal](https://portal.azure.com)에서 가상 머신에서 사용자 지정 이미지를 만들 때 일반화 된 사용자 지정 이미지를 포함 하도록 선택할 수 있습니다.

- **특수 한 사용자 지정 이미지:** Sysprep/프로 비전 해제가 컴퓨터에서 실행 되지 않았습니다. 즉, 이미지가 기존 가상 컴퓨터 (스냅숏)에 있는 OS 디스크의 정확한 복사본 임을 의미 합니다.  이 사용자 지정 이미지를 사용 하 여 새 컴퓨터를 만들 때 동일한 파일, 응용 프로그램, 사용자 계정, 컴퓨터 이름 등이 모두 표시 됩니다.
- **일반화 된 사용자 지정 이미지:** 컴퓨터에서 Sysprep/프로 비전 해제가 실행 되었습니다.  이 프로세스를 실행 하면 사용자 계정이 제거 되 고, 컴퓨터 이름이 제거 되 고, 사용자 레지스트리 하이브가 제거 됩니다. 또한 다른 가상 컴퓨터를 만들 때 이미지를 일반화 하 여 사용자 지정할 수 있습니다.  Sysprep을 실행 하 여 가상 컴퓨터를 일반화 하면 프로세스가 현재 가상 컴퓨터를 제거 하 고 더 이상 작동 하지 않습니다.

이미지 팩터리에 사용자 지정 이미지를 맞추기 위한 스크립트는 이전 단계에서 만든 가상 컴퓨터에 대 한 Vhd를 저장 합니다 (Azure의 리소스에 대 한 태그를 기반으로 식별).

## <a name="update-configuration-for-distributing-images"></a>이미지 배포에 대 한 구성 업데이트
프로세스의 다음 단계는 이미지 팩터리 랩의 사용자 지정 이미지를이를 필요로 하는 다른 랩에서 푸시하는 것입니다. 이 프로세스의 핵심 부분은 **랩에서 json** 구성 파일입니다. 이미지 팩터리에 포함 된 **구성** 폴더에서이 파일을 찾을 수 있습니다.

다음 두 가지 주요 항목은 labs. json 구성 파일에 있습니다.

- 구독 ID 및 랩 이름을 사용 하 여 특정 대상 랩을 고유 하 게 식별 합니다.
- 구성 루트에 대 한 상대 경로로 랩에서 푸시 해야 하는 특정 이미지 집합입니다. 전체 폴더를 지정 하 여 해당 폴더의 모든 이미지를 가져올 수 있습니다.

다음은 두 개의 랩을 나열 하는 예제 랩과 json 파일입니다. 이 경우 두 개의 다른 랩에 이미지를 배포 합니다.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>빌드 작업 만들기
이 문서의 앞부분에서 설명한 것과 동일한 단계를 사용 하 여 빌드 정의에 **Azure Powershell** 빌드 작업을 추가 합니다. 다음 이미지와 같이 세부 정보를 입력 합니다. 

![이미지를 배포 하는 빌드 작업](./media/save-distribute-custom-images/second-build-task-powershell.png)

매개 변수는 다음과 같습니다.`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

이 작업은 이미지 팩터리에 있는 사용자 지정 이미지를 사용 하 여 Labs 파일에 정의 된 모든 랩에서 푸시합니다.

## <a name="queue-the-build"></a>빌드 큐 대기
배포 빌드 작업이 완료 되 면 새 빌드를 큐에 대기 하 여 모든 것이 작동 하는지 확인 합니다. 빌드가 성공적으로 완료 되 면 새 사용자 지정 이미지가 실습실. json 구성 파일에 입력 된 대상 랩에 표시 됩니다.

## <a name="next-steps"></a>다음 단계
시리즈의 다음 문서에서는 보존 정책 및 정리 단계를 사용 하 여 이미지 팩터리를 업데이트 합니다. [보존 정책 설정 및 정리 스크립트 실행](image-factory-set-retention-policy-cleanup.md)
