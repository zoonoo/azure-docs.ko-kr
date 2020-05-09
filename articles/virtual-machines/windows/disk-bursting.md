---
title: 관리 디스크 버스트
description: Azure 디스크에 대 한 디스크 버스트 및 Azure virtual machines의 디스크 버스트에 대해 알아봅니다.
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594467"
---
# <a name="disk-bursting"></a>디스크 버스팅
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>가상 머신 수준 버스트
다음 지원 되는 크기의 공용 클라우드의 모든 지역에서 VM 수준 버스트 지원이 사용 됩니다. 
- [Lsv2 시리즈](../lsv2-series.md)

버스트는이를 지 원하는 가상 컴퓨터에 대해 기본적으로 사용 하도록 설정 됩니다.

## <a name="disk-level-bursting"></a>디스크 수준 버스트
버스트는 디스크 크기 P20 모든 지역에서 더 작은 [프리미엄 ssd](disks-types.md#premium-ssd) 에서 사용할 수 있습니다. 디스크 버스트는이를 지 원하는 디스크 크기의 새 배포에서 기본적으로 사용 하도록 설정 됩니다. 디스크 버스트를 지 원하는 경우 기존 디스크 크기는 다음 방법 중 하나를 통해 버스트를 사용 하도록 설정할 수 있습니다. 
- **VM 다시 시작** 
- **디스크 분리 및 다시 연결**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
