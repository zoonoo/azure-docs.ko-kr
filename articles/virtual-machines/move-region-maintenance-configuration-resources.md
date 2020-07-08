---
title: 유지 관리 구성과 연결 된 리소스를 다른 지역으로 이동
description: VM 유지 관리 구성과 관련 된 리소스를 다른 Azure 지역으로 이동 하는 방법을 알아봅니다.
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: baf7201176fc3d6c70881817ff21b44c2615241a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84676894"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>유지 관리 제어 구성의 리소스를 다른 지역으로 이동

유지 관리 제어 구성과 관련 된 리소스를 다른 Azure 지역으로 이동 하려면이 문서를 참조 하세요. 여러 가지 이유로 구성을 이동 하는 것이 좋습니다. 예를 들어, 새 지역을 활용 하 여 특정 지역에서 사용할 수 있는 기능 또는 서비스를 배포 하 고 내부 정책 및 거 버 넌 스 요구 사항을 충족 하거나 용량 계획에 대 한 응답으로 배포할 수 있습니다.

사용자 지정 유지 관리 구성을 사용 하 여 유지 관리 제어를 통해 플랫폼 업데이트가 [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) Vm 및 Azure 전용 호스트에 적용 되는 방식을 제어할 수 있습니다. 여러 지역에서 유지 관리 제어를 이동 하기 위한 몇 가지 시나리오는 다음과 같습니다.

- 유지 관리 구성과 관련 된 리소스를 이동 하지만 구성 자체는 이동 하지 않으려면이 문서를 따릅니다.
- 유지 관리 제어 구성을 이동 하지만 구성과 관련 된 리소스는 이동 하지 않으려면 [다음 지침](move-region-maintenance-configuration.md)을 따르세요.
- 유지 관리 구성과 연결 된 리소스를 모두 이동 하려면 먼저 [다음 지침](move-region-maintenance-configuration.md)을 따르세요. 그런 다음이 문서의 지침을 따릅니다.

## <a name="prerequisites"></a>사전 요구 사항

유지 관리 제어 구성과 연결 된 리소스 이동을 시작 하기 전에 다음을 수행 합니다.

- 시작 하기 전에 이동 하는 리소스가 새 지역에 있는지 확인 합니다.
- 이동 하려는 azure Vm 및 Azure 전용 호스트와 연결 된 유지 관리 제어 구성을 확인 합니다. 각 리소스를 개별적으로 확인 합니다. 현재 여러 리소스에 대 한 구성을 검색할 수 있는 방법은 없습니다.
- 리소스에 대 한 구성을 검색 하는 경우:
    - Azure 전용 호스트 ID가 아닌 계정에 대 한 구독 ID를 사용 해야 합니다.
    - CLI:--output table 매개 변수는 가독성을 위해서만 사용 되며 삭제 하거나 변경할 수 있습니다.
    - PowerShell: Format-Table Name 매개 변수는 가독성을 위해서만 사용 되며 삭제 하거나 변경할 수 있습니다.
    - PowerShell을 사용 하는 경우 연결 된 구성이 없는 리소스에 대 한 구성을 나열 하려고 하면 오류가 발생 합니다. 오류는 "작업이 실패 했습니다 (상태: ' 찾을 수 없음 ')와 유사 합니다. 세부 정보: 404 클라이언트 오류: url을 찾을 수 없습니다. "

    
## <a name="prepare-to-move"></a>이동 준비

1. 시작 하기 전에 이러한 변수를 정의 합니다. 각각에 대 한 예제를 제공 했습니다.

    **변수** | **세부 정보** | **예제**
    --- | ---
    $subId | 유지 관리 구성을 포함 하는 구독의 ID입니다. | "구독 ID"
    $rsrcGroupName | 리소스 그룹 이름 (Azure VM) | "VMResourceGroup"
    $vmName | VM 리소스 이름 |  MyVM
    $adhRsrcGroupName |  리소스 그룹 (전용 호스트) | "HostResourceGroup"
    $adh | 전용 호스트 이름 | MyHost
    $adhParentName | 부모 리소스 이름 | HostGroup
    
2. PowerShell [AZConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0) 명령을 사용 하 여 유지 관리 구성을 검색 하려면:

    - Azure 전용 호스트의 경우 다음을 실행 합니다.
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Azure Vm의 경우 다음을 실행 합니다.

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. CLI [az maintenance 대입문](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest) 을 사용 하 여 유지 관리 구성을 검색 하려면:

    - Azure 전용 호스트의 경우:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Azure Vm의 경우:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>이동 

1. [다음 지침에 따라](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) Azure vm을 새 지역으로 이동 합니다.
2. 리소스를 이동한 후 유지 관리 구성을 이동 했는지 여부에 따라 적절 하 게 새 지역의 리소스에 유지 관리 구성을 다시 적용 합니다. [PowerShell](../virtual-machines/maintenance-control-powershell.md) 또는 [CLI](../virtual-machines/maintenance-control-cli.md)를 사용 하 여 리소스에 유지 관리 구성을 적용할 수 있습니다.


## <a name="verify-the-move"></a>이동 확인

새 지역의 리소스를 확인 하 고 새 지역의 리소스에 대해 연결 된 구성을 확인 합니다. 

## <a name="clean-up-source-resources"></a>원본 리소스 정리

이동 후 원본 영역에서 이동한 리소스를 삭제 하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

유지 관리 구성을 이동 해야 하는 경우 [다음 지침](move-region-maintenance-configuration.md) 을 따르세요. 
