---
title: 유지 관리 구성을 다른 Azure 지역으로 이동
description: VM 유지 관리 구성을 다른 Azure 지역으로 이동 하는 방법을 알아봅니다.
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4cff7eb4a69005f2e74747b6e58447f100c69b60
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501605"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>유지 관리 제어 구성을 다른 지역으로 이동

이 문서를 따라 유지 관리 제어 구성을 다른 Azure 지역으로 이동할 수 있습니다. 여러 가지 이유로 구성을 이동 하는 것이 좋습니다. 예를 들어, 새 지역을 활용 하 여 특정 지역에서 사용할 수 있는 기능 또는 서비스를 배포 하 고 내부 정책 및 거 버 넌 스 요구 사항을 충족 하거나 용량 계획에 대 한 응답으로 배포할 수 있습니다.

사용자 지정 유지 관리 구성을 사용 하 여 유지 관리 제어를 통해 플랫폼 업데이트가 [Windows](./maintenance-control-cli.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) 및 [Linux](./maintenance-control-cli.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) Vm 및 Azure 전용 호스트에 적용 되는 방식을 제어할 수 있습니다. 여러 지역에서 유지 관리 제어를 이동 하기 위한 몇 가지 시나리오는 다음과 같습니다.

- 유지 관리 제어 구성을 이동 하지만 구성과 관련 된 리소스는 이동 하지 않으려면이 문서의 지침을 따르세요.
- 유지 관리 구성과 관련 된 리소스를 이동 하지만 구성 자체는 이동 하지 않으려면 [다음 지침](move-region-maintenance-configuration-resources.md)을 따르세요.
- 유지 관리 구성과 연결 된 리소스를 모두 이동 하려면 먼저이 문서의 지침을 따르세요. 그런 다음, 다음 [지침](move-region-maintenance-configuration-resources.md)을 따릅니다.

## <a name="prerequisites"></a>필수 구성 요소

유지 관리 제어 구성 이동을 시작 하기 전에 다음을 수행 합니다.

- 유지 관리 구성은 Azure Vm 또는 Azure 전용 호스트와 연결 됩니다. 시작 하기 전에 새 지역에 v m/호스트 리소스가 있는지 확인 합니다.
- Identify: 
    - 기존 유지 관리 제어 구성.
    - 기존 구성이 현재 상주 하는 리소스 그룹입니다. 
    - 새 지역으로 이동한 후 구성이 추가 될 리소스 그룹입니다. 
    - 이동 하려는 유지 관리 구성과 연결 된 리소스입니다.
    - 새 지역의 리소스가 현재 유지 관리 구성과 연결 된 리소스와 동일한 지 확인 합니다. 이전에와 동일한 이름을 새 지역에서 사용할 수 있지만, 반드시 그럴 필요는 없습니다.

## <a name="prepare-and-move"></a>준비 및 이동 

1. 각 구독에서 모든 유지 관리 구성을 검색 합니다. CLI [az maintenance configuration list](/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) 명령을 실행 하 여이 작업을 수행 하 고 $subId를 구독 ID로 바꿉니다.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 구독 내에서 구성 레코드의 반환 된 테이블 목록을 검토 합니다. 예를 들면 다음과 같습니다. 목록에는 특정 환경에 대 한 값이 포함 됩니다.

    **이름** | **위치** | **리소스 그룹**
    --- | --- | ---
    유지 관리 건너뛰기 | eastus2 | 구성-리소스 그룹
    IgniteDemoConfig | eastus2 | 구성-리소스 그룹
    defaultMaintenanceConfiguration-미국 | eastus | 테스트-구성
    

3. 참조용으로 목록을 저장 합니다. 구성을 이동할 때 모든 것이 이동 되었는지 확인 하는 데 도움이 됩니다.
4. 참조로 각 구성/리소스 그룹을 새 지역의 새 리소스 그룹에 매핑합니다.
5. [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)또는 [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)를 사용 하 여 새 지역에서 새 유지 관리 구성을 만듭니다.
6. [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)또는 [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)를 사용 하 여 구성을 새 지역의 리소스와 연결 합니다.


## <a name="verify-the-move"></a>이동 확인

구성을 이동한 후에는 새 지역의 구성과 리소스를 준비한 테이블 목록과 비교 합니다.


## <a name="clean-up-source-resources"></a>원본 리소스 정리

이동 후 원본 영역, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)또는 [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)에서 이동 된 유지 관리 구성을 삭제 하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

유지 관리 구성과 관련 된 리소스를 이동 해야 하는 경우 [다음 지침](move-region-maintenance-configuration-resources.md) 을 따르세요. 
