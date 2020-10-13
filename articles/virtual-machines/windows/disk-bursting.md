---
title: 관리 디스크 버스팅
description: Azure 디스크의 디스크 버스팅과 Azure 가상 머신의 디스크 버스트에 대해 알아봅니다.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ab54b68ab3f7984ee18a39cf3a81fa663af54dee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90889110"
---
# <a name="disk-bursting"></a>디스크 버스팅
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Virtual Machine 수준 버스팅
VM 수준 버스트 지원은 다음과 같은 크기로 지원되는 퍼블릭 클라우드의 모든 지역에서 사용할 수 있습니다. 
- [Lsv2 시리즈](../lsv2-series.md)

VM 수준 버스트는 다음과 같은 지원 되는 크기에 대 한 미국 서 부에도 제공 됩니다.
- [Dsv3 시리즈](../dv3-dsv3-series.md)
- [Esv3 시리즈](../ev3-esv3-series.md)

버스팅은 이를 지원하는 가상 머신에서 기본적으로 사용하도록 설정됩니다.

## <a name="disk-level-bursting"></a>디스크 수준 버스팅
버스트는 디스크 크기 P20 모든 지역에서 더 작은 [프리미엄 ssd](disks-types.md#premium-ssd) 에서 사용할 수 있습니다. 디스크 버스팅은 이를 지원하는 디스크 크기의 새 배포에서 기본적으로 사용하도록 설정됩니다. 디스크 버스팅을 지원하는 기존 디스크 크기는 다음 방법 중 하나를 통해 버스팅을 사용하도록 설정할 수 있습니다. 
- **VM 다시 시작** 
- **디스크 분리 및 다시 연결**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
