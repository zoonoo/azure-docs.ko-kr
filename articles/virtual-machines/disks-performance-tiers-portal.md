---
title: Azure Portal를 사용 하 여 Azure managed disks의 성능 변경
description: Azure Portal를 사용 하 여 새 관리 디스크 및 기존 관리 디스크의 성능 계층을 변경 하는 방법에 대해 알아봅니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a868d5d236cf4c5a8d29f15490909c2f1a53546f
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "96016592"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Azure Portal를 사용 하 여 성능 계층 변경

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>시작

### <a name="new-disks"></a>새 디스크

다음 단계는 디스크를 처음 만들 때 디스크의 성능 계층을 변경 하는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 새 디스크를 만들려는 VM으로 이동 합니다.
1. 새 디스크를 선택 하는 경우 먼저 필요한 디스크 크기를 선택 합니다.
1. 크기를 선택한 후에는 다른 성능 계층을 선택 하 여 성능을 변경 합니다.
1. **확인** 을 선택 하 여 디스크를 만듭니다.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="디스크 만들기 블레이드의 스크린샷, 디스크가 강조 표시 되 고 성능 계층 드롭다운이 강조 표시 됩니다." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>기존 디스크

다음 단계는 기존 디스크의 성능 계층을 변경 하는 방법을 간략하게 설명 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 변경 하려는 디스크가 포함 된 VM으로 이동 합니다.
1. VM의 할당을 취소 하거나 디스크를 분리 합니다.
1. 디스크 선택
1. **크기 + 성능** 을 선택 합니다.
1. **성능 계층** 드롭다운에서 디스크의 현재 기준선과 다른 계층을 선택 합니다.
1. **크기 조정** 을 선택합니다.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="크기 + 성능 블레이드의 스크린샷, 성능 계층이 강조 표시 됩니다." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>다음 단계

더 높은 성능 계층을 활용 하기 위해 디스크 크기를 조정 해야 하는 경우 다음 문서를 참조 하세요.

- [Azure CLI를 사용하여 Linux VM에서 가상 하드 디스크 확장](linux/expand-disks.md)
- [Windows 가상 머신에 연결 된 관리 되는 디스크 확장](windows/expand-os-disk.md)
