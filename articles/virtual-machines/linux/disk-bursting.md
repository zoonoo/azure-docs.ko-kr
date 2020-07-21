---
title: 관리 디스크 버스팅
description: Azure 디스크의 디스크 버스팅과 Azure 가상 머신의 디스크 버스트에 대해 알아봅니다.
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4408b7d442ce3f4fbb1ea61031295375ae036493
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494550"
---
# <a name="managed-disk-bursting"></a>관리 디스크 버스팅
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Virtual Machine 수준 버스팅
VM 수준 버스트 지원은 다음과 같은 크기로 지원되는 퍼블릭 클라우드의 모든 지역에서 사용할 수 있습니다. 
- [Lsv2 시리즈](../lsv2-series.md)

버스팅은 이를 지원하는 가상 머신에서 기본적으로 사용하도록 설정됩니다.

## <a name="disk-level-bursting"></a>디스크 수준 버스팅
버스팅은 Azure 퍼블릭, Government 및 중국 클라우드의 모든 지역에 있는 P20 이하 디스크 크기의 [프리미엄 SSD](disks-types.md#premium-ssd)에서도 사용할 수 있습니다. 디스크 버스트는이를 지 원하는 디스크 크기의 모든 신규 및 기존 배포에서 기본적으로 사용 하도록 설정 됩니다. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
