---
title: Windows VM 게스트 디스크에 Azure 디스크를 매핑하는 방법
description: Windows VM의 게스트 디스크를 언더레이 하는 Azure 디스크를 확인 하는 방법입니다.
author: timbasham
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 373fd26c36bf2f77de6a376f738bd3caaf735f00
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881875"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Windows VM 게스트 디스크에 Azure 디스크를 매핑하는 방법

VM의 게스트 디스크를 백업 하는 Azure 디스크를 확인 해야 할 수도 있습니다. 일부 시나리오에서는 디스크 또는 볼륨 크기를 연결 된 Azure 디스크 크기와 비교할 수 있습니다. VM에 연결 된 동일한 크기의 Azure 디스크가 여러 개 있는 시나리오에서 데이터 디스크의 LUN (논리 단위 번호)을 사용 해야 합니다. 

## <a name="what-is-a-lun"></a>LUN 이란?

LUN (논리 단위 번호)은 특정 저장 장치를 식별 하는 데 사용 되는 숫자입니다. 각 저장 장치에는 0부터 시작 하는 고유한 숫자 식별자가 할당 됩니다. 장치에 대 한 전체 경로는 버스 번호, 대상 ID 번호 및 LUN (논리 단위 번호)으로 표시 됩니다. 

예: ***Bus 번호 0, 대상 ID 0, LUN 3***

이 연습에서는 LUN을 사용 하기만 하면 됩니다.

## <a name="finding-the-lun"></a>LUN 찾기

LUN을 찾는 방법에는 두 가지가 있습니다. 여기서 선택 하는 항목은 [저장소 공간](/windows-server/storage/storage-spaces/overview) 을 사용 하는 경우에 따라 다릅니다.

### <a name="disk-management"></a>디스크 관리

저장소 풀을 사용 하지 않는 경우에는 [디스크 관리](/windows-server/storage/disk-management/overview-of-disk-management) 를 사용 하 여 LUN을 찾을 수 있습니다.

1. VM에 연결 하 고 디스크 관리 a를 엽니다. 시작 단추를 마우스 오른쪽 단추로 클릭 하 고 "디스크 관리"를 선택 합니다. `diskmgmt.msc`검색 시작 상자에 입력할 수도 있습니다.
1. 아래쪽 창에서 디스크를 마우스 오른쪽 단추로 클릭 하 고 "속성"을 선택 합니다.
1. LUN은 "일반" 탭의 "위치" 속성에 나열 됩니다.

### <a name="storage-pools"></a>저장소 풀

1. VM에 연결 하 고 서버 관리자를 엽니다.
1. "파일 및 저장소 서비스", "볼륨", "저장소 풀"을 선택 합니다.
1. 서버 관리자의 오른쪽 아래 모서리에는 "실제 디스크" 섹션이 있습니다. 저장소 풀을 구성 하는 디스크는 각 디스크에 대 한 LUN 뿐만 아니라 여기에 나열 됩니다.

## <a name="finding-the-lun-for-the-azure-disks"></a>Azure 디스크에 대 한 LUN 찾기

Azure Portal, Azure CLI 또는 Azure PowerShell를 사용 하 여 Azure 디스크에 대 한 LUN을 찾을 수 있습니다.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Azure Portal에서 Azure 디스크의 LUN 찾기

1. Azure Portal에서 "Virtual Machines"를 선택 하 여 Virtual Machines 목록을 표시 합니다.
1. Virtual Machine을 선택합니다.
1. "디스크"를 선택 합니다.
1. 연결 된 디스크 목록에서 데이터 디스크를 선택 합니다.
1. 디스크의 LUN이 디스크 세부 정보 창에 표시 됩니다. 여기에 표시 된 LUN은 장치 관리자 또는 서버 관리자를 사용 하 여 게스트에서 조회 된 Lun과 관련이 있습니다.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Azure CLI 또는 Azure PowerShell를 사용 하 여 Azure 디스크의 LUN 찾기

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---