---
title: 유지 관리 구성과 관련된 리소스를 다른 지역으로 이동
description: VM 유지 관리 구성과 연결된 리소스를 다른 Azure 지역으로 이동하는 방법 알아보기
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304447"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>유지 관리 제어 구성의 리소스를 다른 지역으로 이동

이 문서에 따라 유지 관리 제어 구성과 연결된 리소스를 다른 Azure 지역으로 이동합니다. 여러 가지 이유로 구성을 이동할 수 있습니다. 예를 들어 새 리전을 활용하거나, 특정 지역에서 사용할 수 있는 기능 또는 서비스를 배포하거나, 내부 정책 및 거버넌스 요구 사항을 충족하거나, 용량 계획에 응답합니다.

사용자 지정된 유지 관리 구성을 사용하여 유지 관리 제어를 사용하면 플랫폼 업데이트가 [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VM 및 Azure 전용 호스트에 적용되는 방법을 제어할 수 있습니다. 여러 리전간에 유지 관리 제어를 이동하기 위한 몇 가지 시나리오가 있습니다.

- 구성 자체가 아닌 유지 관리 구성과 연결된 리소스를 이동하려면 이 문서를 따르십시오.
- 구성과 연관된 리소스가 아닌 유지 관리 제어 구성을 이동하려면 [다음 지침을](move-region-maintenance-configuration.md)따르십시오.
- 유지 관리 구성과 연결된 리소스를 모두 이동하려면 먼저 [다음 지침을](move-region-maintenance-configuration.md)따르십시오. 그런 다음 이 문서의 지침을 따릅니다.

## <a name="prerequisites"></a>사전 요구 사항

유지 관리 제어 구성과 연결된 리소스 이동을 시작하기 전에 다음을 수행하십시오.

- 시작하기 전에 이동하는 리소스가 새 지역에 있는지 확인합니다.
- 이동하려는 Azure VM 및 Azure 전용 호스트와 연결된 유지 관리 제어 구성을 확인합니다. 각 리소스를 개별적으로 확인합니다. 현재 여러 리소스에 대한 구성을 검색할 수 있는 방법은 없습니다.
- 리소스에 대한 구성을 검색할 때:
    - Azure 전용 호스트 ID가 아닌 계정에 대한 구독 ID를 사용해야 합니다.
    - CLI: --output 테이블 매개 변수는 가독성에만 사용되며 삭제하거나 변경할 수 있습니다.
    - PowerShell: 형식-테이블 이름 매개 변수는 가독성에만 사용되며 삭제하거나 변경할 수 있습니다.
    - PowerShell을 사용하는 경우 연결된 구성이 없는 리소스에 대한 구성을 나열하려고 하면 오류가 발생합니다. 오류는 다음과 유사합니다: "작업 상태: '찾을 수 없습니다'와 함께 실패했습니다. 세부 정보: 404 클라이언트 오류: URL에 대 한 찾을 수 없습니다".

    
## <a name="prepare-to-move"></a>이동 준비

1. 시작하기 전에 이러한 변수를 정의합니다. 각 사례에 대한 예제를 제공했습니다.

    **변수** | **세부 정보** | **예제**
    --- | ---
    $subId | 유지 관리 구성이 포함된 구독용 ID | "구독 ID"
    $rsrcGroupName | 리소스 그룹 이름(Azure VM) | "VMResourceGroup"
    $vmName | VM 리소스 이름 |  "myVM"
    $adhRsrcGroupName |  리소스 그룹(전용 호스트) | "호스트 리소스 그룹"
    $adh | 전용 호스트 이름 | "마이호스트"
    $adhParentName | 상위 리소스 이름 | "호스트 그룹"
    
2. PowerShell [Get-AZConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0) 명령을 사용하여 유지 관리 구성을 검색하려면 다음을 수행하십시오.

    - Azure 전용 호스트의 경우 다음을 실행합니다.
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Azure VM의 경우 다음을 실행합니다.

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. CLI [az 유지](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest) 관리 할당 명령을 사용하여 유지 관리 구성을 검색하려면 다음을 수행하십시오.

    - Azure 전용 호스트의 경우:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Azure VM의 경우:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>이동 

1. [다음 지침에 따라](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) Azure VM을 새 지역으로 이동합니다.
2. 리소스를 이동한 후 유지 관리 구성을 이동했는지 여부에 따라 새 지역의 리소스에 유지 관리 구성을 적절하게 다시 적용합니다. [PowerShell](../virtual-machines/maintenance-control-powershell.md) 또는 [CLI를](../virtual-machines/maintenance-control-cli.md)사용하여 리소스에 유지 관리 구성을 적용할 수 있습니다.


## <a name="verify-the-move"></a>이동 확인

새 리전의 리소스를 확인하고 새 리전의 리소스에 대한 관련 구성을 확인합니다. 

## <a name="clean-up-source-resources"></a>소스 리소스 정리

이동 후 원본 영역에서 이동된 리소스를 삭제하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

유지 관리 구성을 이동해야 하는 경우 [다음 지침을](move-region-maintenance-configuration.md) 따르십시오. 
