---
title: 호스트의 디스크 기능
description: 클러스터를 빌드하거나 스케일링하는 데 사용되는 호스트는 격리된 호스트 풀에서 제공합니다.
ms.topic: include
ms.date: 04/23/2021
ms.openlocfilehash: 7ee15fbfd668d5db24282a6e4de8e4dfc6639c3e
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945764"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-cloud-clusters.md -->


Azure VMware Solution 클러스터는 운영 체제 미설치 상태의 초수렴형 인프라를 기반으로 합니다. 다음 표에서는 호스트의 RAM, CPU 및 디스크 용량을 보여 줍니다.

| 호스트 유형 | CPU   | RAM(GB)  | vSAN NVMe cache Tier(TB, raw)  | vSAN SSD capacity tier(TB, raw)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  dual Intel 18 core 2.3 GHz  |     576      |                3.2               |                15.20               |

클러스터를 빌드하거나 스케일링하는 데 사용되는 호스트는 격리된 호스트 풀에서 제공합니다. 해당 호스트는 하드웨어 테스트를 통과하고 모든 데이터를 안전하게 삭제한 상태입니다. 