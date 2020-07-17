---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80628406"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>국가별 가용성
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>제한 사항

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>포털


1. [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 원하는 디스크로 이동 합니다.
1. 디스크에서 **스냅숏 만들기** 를 선택 합니다.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="샷에. 선택 해야 하는 것 처럼 * * + 스냅숏 만들기 * *를 사용 하 여 디스크의 블레이드가 강조 표시 됩니다.":::

1. 사용 하려는 리소스 그룹을 선택 하 고 이름을 입력 합니다.
1. **증분** 을 선택 하 고 **검토 + 만들기** 를 선택 합니다.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="샷에. 스냅숏 블레이드를 만들고 이름을 입력 한 다음 증분을 선택 하 고 스냅숏을 만듭니다.":::

1. **만들기**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="샷에. 스냅숏에 대 한 유효성 검사 페이지에서 선택 사항을 확인 한 다음 스냅숏을 만듭니다.":::

## <a name="next-steps"></a>다음 단계

.NET을 사용 하 여 증분 스냅숏의 차등 기능을 보여 주는 샘플 코드를 보려면 [증분 스냅숏의 차등 기능을 사용 하 여 다른 지역에 Azure Managed Disks 백업 복사](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)를 참조 하세요.
