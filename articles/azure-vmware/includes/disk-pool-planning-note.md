---
title: vNet용 디스크 풀 계획 참고 사항
description: Azure VMware Solution 호스트에 더 가까운 vNet 배포의 중요성에 대한 중요 참고 사항입니다.
ms.topic: include
ms.date: 07/14/2021
ms.openlocfilehash: 39bcf823a71852396c1c05db4902abf000433499
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640589"
---
[Azure 디스크 풀](../../virtual-machines/disks-pools.md)을 사용하여 Azure VMware Solution 호스트를 크기 조정하려는 경우 ExpressRoute 가상 네트워크 게이트웨이를 사용하여 호스트와 가까운 위치에 vNet을 배포하는 것이 중요합니다.  스토리지가 호스트에 가까울수록 성능이 향상됩니다.