---
title: Azure DevTest 랩에서 이미지 저장 및 배포 | 마이크로 소프트 문서
description: 이 문서에서는 Azure DevTest Labs에서 이미 생성된 가상 시스템(VM)에서 사용자 지정 이미지를 저장하는 단계를 제공합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759434"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>사용자 지정 이미지 저장 및 여러 랩에 배포
이 문서에서는 이미 생성된 가상 시스템(VM)에서 사용자 지정 이미지를 저장하는 단계를 제공합니다. 또한 이러한 사용자 지정 이미지를 조직의 다른 DevTest 랩에 배포하는 방법도 다룹니다.

## <a name="prerequisites"></a>사전 요구 사항
다음 항목은 이미 배치되어 있어야 합니다.

- Azure DevTest 랩의 이미지 팩터리용 랩입니다.
- 이미지 팩터리를 자동화하는 데 사용되는 Azure DevOps 프로젝트입니다.
- 스크립트 및 구성을 포함하는 소스 코드 위치(이 예에서는 이전 단계에서 언급한 것과 동일한 DevOps 프로젝트에서).
- Azure Powershell 작업을 오케스트레이션하기 위해 정의를 빌드합니다.

필요한 경우 [Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md) 단계를 수행하여 이러한 항목을 만들거나 설정합니다. 

## <a name="save-vms-as-generalized-vhds"></a>VM을 일반화된 VHD로 저장
기존 VM을 일반화된 VHD로 저장합니다.  기존 VM을 일반화된 VHD로 저장하는 샘플 PowerShell 스크립트가 있습니다. 먼저 다음 이미지와 같이 빌드 정의에 다른 **Azure Powershell** 작업을 추가합니다.

![Azure PowerShell 단계 추가](./media/save-distribute-custom-images/powershell-step.png)

목록에 새 작업이 있으면 다음 이미지와 같이 모든 세부 정보를 입력할 수 있도록 항목을 선택합니다. 

![파워쉘 설정](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>일반화 vs. 특수 사용자 지정 이미지
Azure [Portal에서는](https://portal.azure.com)가상 컴퓨터에서 사용자 지정 이미지를 만들 때 일반화또는 특수화된 사용자 지정 이미지를 갖도록 선택할 수 있습니다.

- **특수 사용자 정의 이미지:** Sysprep/프로비저닝 해제가 컴퓨터에서 실행되지 않았습니다. 이는 이미지가 기존 가상 시스템(스냅샷)에서 OS 디스크의 정확한 복사본임을 의미합니다.  이 사용자 지정 이미지에서 새 컴퓨터를 만들 때 동일한 파일, 응용 프로그램, 사용자 계정, 컴퓨터 이름 등이 모두 존재합니다.
- **일반화된 사용자 지정 이미지:** Sysprep/프로비저닝 해제가 컴퓨터에서 실행되었습니다.  이 프로세스가 실행되면 사용자 계정을 제거하고 컴퓨터 이름을 제거하고 사용자 레지스트리 하이브 등을 제거하여 다른 가상 컴퓨터를 만들 때 사용자 지정할 수 있도록 이미지를 일반화합니다.  가상 컴퓨터를 일반화하면(sysprep을 실행하여) 프로세스가 현재 가상 컴퓨터를 파괴하므로 더 이상 작동하지 않습니다.

이미지 팩터리에서 사용자 지정 이미지를 스냅하기 위한 스크립트는 이전 단계에서 만든 모든 가상 컴퓨터(Azure의 리소스에 대한 태그를 기반으로 식별됨)에 대한 VHD를 저장합니다.

## <a name="update-configuration-for-distributing-images"></a>이미지 배포를 위한 구성 업데이트
프로세스의 다음 단계는 이미지 팩터리 랩에서 사용자 지정 이미지를 필요한 다른 랩으로 푸시하는 것입니다. 이 프로세스의 핵심 부분은 **labs.json** 구성 파일입니다. 이미지 팩터리에 포함된 **구성** 폴더에서 이 파일을 찾을 수 있습니다.

labs.json 구성 파일에 는 두 가지 주요 사항이 나열되어 있습니다.

- 구독 ID와 랩 이름을 사용하여 특정 대상 랩을 고유하게 식별합니다.
- 구성 루트에 대한 상대 경로로 랩에 푸시해야 하는 특정 이미지 집합입니다. 전체 폴더를 지정할 수도 있습니다(해당 폴더의 모든 이미지를 얻으려면).

다음은 두 개의 랩이 나열된 예제 labs.json 파일입니다. 이 경우 이미지를 두 개의 서로 다른 랩에 배포합니다.

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
이 문서의 앞에서 보았던 것과 동일한 단계를 사용하여 정의를 빌드하는 **데 Azure Powershell** 빌드 작업을 추가합니다. 다음 이미지와 같이 세부 정보를 입력합니다. 

![이미지를 배포하는 작업 빌드](./media/save-distribute-custom-images/second-build-task-powershell.png)

매개 변수는 다음과 같습니다.`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

이 작업은 이미지 팩터리에 있는 사용자 지정 이미지를 가져와 Labs.json 파일에 정의된 모든 랩으로 푸시합니다.

## <a name="queue-the-build"></a>빌드 큐
배포 빌드 작업이 완료되면 새 빌드를 큐에 대기하여 모든 작업이 작동하는지 확인합니다. 빌드가 성공적으로 완료되면 Labs.json 구성 파일에 입력된 대상 랩에 새 사용자 지정 이미지가 표시됩니다.

## <a name="next-steps"></a>다음 단계
시리즈의 다음 문서에서는 보존 정책 및 정리 단계인 보존 정책 [설정 및 정리 스크립트 실행으로 이미지 팩터리를 업데이트합니다.](image-factory-set-retention-policy-cleanup.md)
