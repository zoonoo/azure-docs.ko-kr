---
title: 유지 관리 구성을 다른 Azure 지역으로 이동
description: VM 유지 관리 구성을 다른 Azure 지역으로 이동하는 방법 알아보기
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304460"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>유지 관리 제어 구성을 다른 지역으로 이동

이 문서에 따라 유지 관리 제어 구성을 다른 Azure 지역으로 이동합니다. 여러 가지 이유로 구성을 이동할 수 있습니다. 예를 들어 새 리전을 활용하거나, 특정 지역에서 사용할 수 있는 기능 또는 서비스를 배포하거나, 내부 정책 및 거버넌스 요구 사항을 충족하거나, 용량 계획에 응답합니다.

사용자 지정된 유지 관리 구성을 사용하여 유지 관리 제어를 사용하면 플랫폼 업데이트가 [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VM 및 Azure 전용 호스트에 적용되는 방법을 제어할 수 있습니다. 여러 리전간에 유지 관리 제어를 이동하기 위한 몇 가지 시나리오가 있습니다.

- 구성과 연관된 리소스가 아닌 유지 관리 제어 구성을 이동하려면 이 문서의 지침을 따르십시오.
- 구성 자체가 아닌 유지 관리 구성과 연결된 리소스를 이동하려면 [다음 지침을](move-region-maintenance-configuration-resources.md)따르십시오.
- 유지 관리 구성과 연결된 리소스를 모두 이동하려면 먼저 이 문서의 지침을 따르십시오. 그런 다음 [다음 지침을](move-region-maintenance-configuration-resources.md)따릅니다.

## <a name="prerequisites"></a>사전 요구 사항

유지 관리 제어 구성을 이동하기 전에 다음을 수행하십시오.

- 유지 관리 구성은 Azure VM 또는 Azure 전용 호스트와 연결됩니다. 시작하기 전에 VM/호스트 리소스가 새 지역에 있는지 확인합니다.
- Identify: 
    - 기존 유지 관리 제어 구성.
    - 기존 구성이 현재 상주하는 리소스 그룹입니다. 
    - 새 지역으로 이동한 후 구성이 추가될 리소스 그룹입니다. 
    - 이동하려는 유지 관리 구성과 연결된 리소스입니다.
    - 새 지역의 리소스가 현재 유지 관리 구성과 동일한지 확인합니다. 구성은 이전 지역과 동일한 이름을 가질 수 있지만 필수는 아닙니다.

## <a name="prepare-and-move"></a>준비 및 이동 

1. 각 구독의 모든 유지 관리 구성을 검색합니다. CLI [az 유지 관리 구성 목록](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) 명령을 실행하여 $subId 구독 ID로 대체합니다.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 구독 내에서 구성 레코드의 반환된 테이블 목록을 검토합니다. 예를 들면 다음과 같습니다. 목록에는 특정 환경에 대한 값이 포함됩니다.

    **이름** | **위치** | **리소스 그룹**
    --- | --- | ---
    유지 보수 건너뛰기 | eastus2 | 구성-리소스 그룹
    이그나이트데모콘피그 | eastus2 | 구성-리소스 그룹
    기본유지보수구성-동동 | eastus | 테스트 구성
    

3. 참조를 위해 목록을 저장합니다. 구성을 이동하면 모든 것이 이동되었는지 확인하는 데 도움이 됩니다.
4. 참조로 각 구성/리소스 그룹을 새 지역의 새 리소스 그룹에 매핑합니다.
5. [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)또는 [CLI를](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)사용하여 새 지역에서 새 유지 관리 구성을 만듭니다.
6. [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)또는 [CLI를](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)사용하여 구성을 새 지역의 리소스와 연결합니다.


## <a name="verify-the-move"></a>이동 확인

구성을 이동한 후 새 지역의 구성 및 리소스를 준비한 테이블 목록과 비교합니다.


## <a name="clean-up-source-resources"></a>소스 리소스 정리

이동 후 소스 영역, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)또는 [CLI에서](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)이동된 유지 관리 구성을 삭제하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

유지 관리 구성과 관련된 리소스를 이동해야 하는 경우 [다음 지침을](move-region-maintenance-configuration-resources.md) 따르십시오. 
