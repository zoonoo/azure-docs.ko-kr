---
title: Linux VM 게스트 디스크에 Azure 디스크를 매핑하는 방법
description: Linux VM의 게스트 디스크를 언더레이 Azure 디스크를 확인 하는 방법입니다.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.collection: linux
ms.openlocfilehash: bc6c6273ab3d1a4403763e4ed0a8c491995fb2df
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556726"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Linux VM 게스트 디스크에 Azure 디스크를 매핑하는 방법

VM의 게스트 디스크를 백업 하는 Azure 디스크를 확인 해야 할 수도 있습니다. 일부 시나리오에서는 디스크 또는 볼륨 크기를 연결 된 Azure 디스크 크기와 비교할 수 있습니다. VM에 연결 된 동일한 크기의 Azure 디스크가 여러 개 있는 시나리오에서 데이터 디스크의 LUN (논리 단위 번호)을 사용 해야 합니다. 

## <a name="what-is-a-lun"></a>LUN 이란?

LUN (논리 단위 번호)은 특정 저장 장치를 식별 하는 데 사용 되는 숫자입니다. 각 저장 장치에는 0부터 시작 하는 고유한 숫자 식별자가 할당 됩니다. 장치에 대 한 전체 경로는 버스 번호, 대상 ID 번호 및 LUN (논리 단위 번호)으로 표시 됩니다. 

예: ***Bus 번호 0, 대상 ID 0, LUN 3***

이 연습에서는 LUN을 사용 하기만 하면 됩니다.

## <a name="finding-the-lun"></a>LUN 찾기

아래에는 Linux에서 디스크의 LUN을 찾기 위한 두 가지 방법이 나와 있습니다.

### <a name="lsscsi"></a>lsscsi

1. VM에 연결
1. `sudo lsscsi`

나열 된 첫 번째 열에는 LUN이 포함 됩니다. 형식은 [Host: Channel: Target:**LUN**]입니다.

### <a name="listing-block-devices"></a>블록 장치 나열

1. VM에 연결
1. `sudo ls -l /sys/block/*/device`

나열 된 마지막 열에는 LUN이 포함 됩니다. 형식은 [Host: Channel: Target:**lun**]입니다.

## <a name="finding-the-lun-for-the-azure-disks"></a>Azure 디스크에 대 한 LUN 찾기

Azure CLI Azure Portal를 사용 하 여 Azure 디스크에 대 한 LUN을 찾을 수 있습니다.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Azure Portal에서 Azure 디스크의 LUN 찾기

1. Azure Portal에서 "Virtual Machines"를 선택 하 여 Virtual Machines 목록을 표시 합니다.
1. Virtual Machine을 선택합니다.
1. "디스크"를 선택 합니다.
1. 연결 된 디스크 목록에서 데이터 디스크를 선택 합니다.
1. 디스크의 LUN이 디스크 세부 정보 창에 표시 됩니다. 여기에 표시 된 LUN은 lsscsi를 사용 하 여 게스트에서 조회 하거나 블록 장치를 나열 하는 Lun과 관련이 있습니다.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure 디스크의 LUN 찾기

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
