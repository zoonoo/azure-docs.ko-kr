---
title: 관리 디스크 버스팅
description: Azure 디스크의 디스크 버스팅과 Azure 가상 머신의 디스크 버스트에 대해 알아봅니다.
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82594467"
---
# <a name="disk-bursting"></a>디스크 버스팅
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Virtual Machine 수준 버스팅
VM 수준 버스트 지원은 다음과 같은 크기로 지원되는 퍼블릭 클라우드의 모든 지역에서 사용할 수 있습니다. 
- [Lsv2 시리즈](../lsv2-series.md)

버스팅은 이를 지원하는 가상 머신에서 기본적으로 사용하도록 설정됩니다.

## <a name="disk-level-bursting"></a>디스크 수준 버스팅
버스트는 디스크 크기 P20 모든 지역에서 더 작은 [프리미엄 ssd](disks-types.md#premium-ssd) 에서 사용할 수 있습니다. 디스크 버스팅은 이를 지원하는 디스크 크기의 새 배포에서 기본적으로 사용하도록 설정됩니다. 디스크 버스팅을 지원하는 기존 디스크 크기는 다음 방법 중 하나를 통해 버스팅을 사용하도록 설정할 수 있습니다. 
- **VM 다시 시작** 
- **디스크 분리 및 다시 연결**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
