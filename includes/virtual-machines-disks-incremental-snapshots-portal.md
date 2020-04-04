---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628406"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>국가별 가용성
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>포털


1. [Azure 포털에](https://portal.azure.com/) 로그인하고 스냅숏을 지정하려는 디스크로 이동합니다.
1. 디스크에서 **스냅샷 만들기를 선택합니다.**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="스크린샷. **+스냅샷 만들기**가 강조 표시된 디스크의 블레이드는 선택해야 합니다.":::

1. 사용할 리소스 그룹을 선택하고 이름을 입력합니다.
1. **증분** 및 **검토 + 만들기** 선택

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="스크린샷. 스냅샷 블레이드를 만들고 이름을 입력하고 증분을 선택한 다음 스냅샷을 만듭니다.":::

1. **만들기** 선택

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="스크린샷. 스냅샷에 대한 유효성 검사 페이지에서 선택한 항목을 확인한 다음 스냅샷을 만듭니다.":::

## <a name="next-steps"></a>다음 단계

.NET을 사용하여 증분 스냅숏의 차등 기능을 보여 주는 샘플 코드를 보려면 [증분 스냅숏의 차동 기능을 사용하여 다른 지역으로 Azure 관리 디스크 백업 복사를](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)참조하십시오.
