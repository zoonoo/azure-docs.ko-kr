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
ms.openlocfilehash: e207866b61d21334bc9923d0d784b900906b0045
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539722"
---
1. [Azure Portal](https://portal.azure.com/)에 로그인하고 스냅샷을 만들 디스크로 이동합니다.
1. 디스크에서 **스냅샷 만들기** 를 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="스크린샷. **+스냅샷 만들기**가 강조 표시된 디스크의 블레이드를 선택해야 합니다.":::

1. 사용할 리소스 그룹을 선택하고 이름을 입력합니다.
1. **증분** 을 선택하고 **검토 + 만들기** 를 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="스크린샷. 스냅샷 블레이드를 만들고, 이름을 입력하고, 증분을 선택한 다음, 스냅샷을 만듭니다.":::

1. **만들기** 를 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="스크린샷. 스냅샷의 유효성 검사 페이지에서 선택 사항을 확인한 다음, 스냅샷을 만듭니다.":::