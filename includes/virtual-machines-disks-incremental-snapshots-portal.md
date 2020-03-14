---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 64aa01995460837c820c90010d7c4e3f3d78e6a2
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300233"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>국가별 가용성
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>포털

전역 Azure Portal에서 증분 스냅숏을 만들고 구성 하려면 [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)링크를 사용 해야 합니다. 증분 스냅숏 만들기는 글로벌 Azure Portal에서 아직 사용할 수 없습니다.

1. 제공 된 링크를 사용 하 여 [Azure Portal](https://aka.ms/incrementalsnapshots) 에 로그인 하 고 원하는 디스크로 이동 합니다.
1. 디스크에서 **스냅숏 만들기** 를 선택 합니다.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text=" ":::

1. 사용 하려는 리소스 그룹을 선택 하 고 이름을 입력 합니다.
1. **증분** 을 선택 하 고 **검토 + 만들기** 를 선택 합니다.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text=" ":::

1. **만들기**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text=" ":::

## <a name="next-steps"></a>다음 단계

.NET을 사용 하 여 증분 스냅숏의 차등 기능을 보여 주는 샘플 코드를 보려면 [증분 스냅숏의 차등 기능을 사용 하 여 다른 지역에 Azure Managed Disks 백업 복사](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)를 참조 하세요.
