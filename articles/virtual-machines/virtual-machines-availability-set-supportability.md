---
title: 기존 가용성 집합에 Azure VM 추가의 지원 가능성 | Microsoft Docs
description: 기존 가용성 집합에 Azure VM 추가의 지원 가능성
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155450"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>기존 가용성 집합에 Azure VM 추가의 지원 가능성

기존 가용성 집합에 새 VM(가상 머신)을 추가할 경우 제한이 발생하는 경우가 있을 수 있습니다. 다음 차트에는 동일한 가용성 집합에서 혼합해서 사용할 수 있는 VM 시리즈가 자세히 설명되어 있습니다.

다음은 다양한 유형의 VM에 대한 지원 가능성 행렬입니다.

시리즈 및 가용성 집합|두 번째 VM|변수를 잠그기 위한|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|첫 번째 VM|||||||
|변수를 잠그기 위한||확인|확인|확인|확인|확인|
|Av2||확인|확인|확인|확인|확인|
|D||확인|확인|확인|확인|확인|
|Dv2||확인|확인|확인|확인|확인|
|Dv3||확인|확인|확인|확인|확인|

다른 모든 시리즈는 특정 하드웨어가 필요하기 때문에 동일한 가용성 집합에 있을 수 없습니다.

A8/A9 VM 크기는 전용 RDMA 백 엔드 네트워크에 대 한 요구 사항으로 인해 혼합할 수 없습니다.
