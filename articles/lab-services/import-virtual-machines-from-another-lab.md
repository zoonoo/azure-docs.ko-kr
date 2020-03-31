---
title: Azure DevTest 랩의 다른 랩에서 가상 컴퓨터 가져오기
description: 이 문서에서는 다른 랩에서 Azure DevTest Labs의 현재 랩으로 가상 컴퓨터를 가져오는 방법에 대해 설명합니다.
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
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759519"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Azure DevTest 랩의 다른 랩에서 가상 컴퓨터 가져오기
이 문서에서는 다른 랩에서 랩으로 가상 컴퓨터를 가져오는 방법에 대한 정보를 제공합니다.

## <a name="scenarios"></a>시나리오
다음은 한 랩에서 다른 랩으로 VM을 가져와야 하는 몇 가지 시나리오입니다.

- 팀의 한 개인이 엔터프라이즈 내의 다른 그룹으로 이동하고 있으며 개발자 데스크톱을 새 팀의 DevTest Labs로 가져가려고 합니다.
- 그룹이 [구독 수준 할당량에](../azure-resource-manager/management/azure-subscription-service-limits.md) 도달했으며 팀을 몇 개의 구독으로 분할하려고 합니다.
- 이 회사는 익스프레스 경로 (또는 다른 새로운 네트워킹 토폴로지)로 이동하고 팀은이 새로운 인프라를 사용하기 위해 가상 머신을 이동하려고합니다

## <a name="solution-and-constraints"></a>해결 방법 및 제약 조건
이 기능을 사용하면 한 랩(소스)에서 VM을 다른 랩(대상)으로 가져올 수 있습니다. 선택적으로 프로세스에서 대상 VM에 대한 새 이름을 지정할 수 있습니다. 가져오기 프로세스에는 디스크, 일정, 네트워크 설정 등과 같은 모든 종속성이 포함됩니다.

이 프로세스는 다소 시간이 걸리며 다음과 같은 요인의 영향을 받습니다.

- 원본 컴퓨터에 연결된 디스크의 수/크기(복사 작업이지 이동 작업이 아니므로)
- 목적지까지의 거리(예: 미국 동부 지역에서 동남아시아까지).

프로세스가 완료되면 소스 가상 머신이 종료된 상태로 유지되고 새 가상 시스템이 대상 랩에서 실행되고 있습니다.

한 랩에서 다른 랩으로 VM을 가져올 계획일 때 주의해야 할 두 가지 주요 제약 조건이 있습니다.

- 구독 및 리전 간에 가상 컴퓨터 가져오기가 지원되지만 구독은 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.
- 가상 컴퓨터는 원본 랩에서 청구 가능한 상태에 있어야 합니다.
- 원본 랩에서 VM의 소유자이자 대상 랩의 랩 소유자입니다.
- 현재 이 기능은 Powershell 및 REST API를 통해서만 지원됩니다.

## <a name="use-powershell"></a>PowerShell 사용
[GitHub에서](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)ImportVirtualMachines.ps1 파일을 다운로드합니다. 스크립트를 사용하여 원본 랩의 단일 VM 또는 모든 VM을 대상 랩으로 가져올 수 있습니다.

### <a name="use-powershell-to-import-a-single-vm"></a>PowerShell을 사용하여 단일 VM 가져오기
이 powershell 스크립트를 실행하려면 원본 VM 및 대상 랩을 식별하고 선택적으로 대상 컴퓨터에 사용할 새 이름을 제공해야 합니다.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>PowerShell을 사용하여 소스 랩의 모든 VM 가져오기
원본 가상 컴퓨터를 지정하지 않으면 스크립트는 DevTest 랩의 모든 VM을 자동으로 가져오게 됩니다.  예를 들어:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST를 사용하여 VM 가져오기
REST 호출은 간단합니다. 원본 및 대상 리소스를 식별하기에 충분한 정보를 제공합니다. 작업은 대상 랩 리소스에서 수행됩니다.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩에 대한 정책 설정](devtest-lab-get-started-with-lab-policies.md)
- [질문과 대답](devtest-lab-faq.md)
