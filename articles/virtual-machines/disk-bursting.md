---
title: 관리 디스크 버스팅
description: Azure 디스크 및 Azure virtual machines의 디스크 버스트에 대해 알아봅니다.
author: albecker1
ms.author: albecker
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1cedac5814d1c547a28e9b1c894f416af5a924b5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585092"
---
# <a name="managed-disk-bursting"></a>관리 디스크 버스팅
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Virtual Machine 수준 버스팅
VM 수준 버스트는에서 지원 되는 모든 지역에서 다음 VM 시리즈에 대해 사용 하도록 설정 됩니다.
- [Lsv2 시리즈](lsv2-series.md)
- [Dsv3 시리즈](dv3-dsv3-series.md)
- [Esv3 시리즈](ev3-esv3-series.md)

버스팅은 이를 지원하는 가상 머신에서 기본적으로 사용하도록 설정됩니다.

## <a name="disk-level-bursting"></a>디스크 수준 버스팅
버스팅은 Azure 퍼블릭, Government 및 중국 클라우드의 모든 지역에 있는 P20 이하 디스크 크기의 [프리미엄 SSD](disks-types.md#premium-ssd)에서도 사용할 수 있습니다. 디스크 버스트는이를 지 원하는 디스크 크기의 모든 신규 및 기존 배포에서 기본적으로 사용 하도록 설정 됩니다. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>다음 단계

버스트 리소스를 파악 하는 방법을 알아보려면 [디스크 버스트 메트릭](disks-metrics.md)을 참조 하세요.
