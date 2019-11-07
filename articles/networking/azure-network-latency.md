---
title: Azure 네트워크 왕복 대기 시간 통계 | Microsoft Docs
description: Azure 지역 간의 왕복 대기 시간 통계에 대해 알아봅니다.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587590"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 네트워크 왕복 대기 시간 통계

Azure는 [ThousandEyes](https://thousandeyes.com)에서 수집 하는 측정, 내부 모니터링 도구를 사용 하 여 네트워크의 핵심 영역에 대 한 대기 시간 (속도)을 지속적으로 모니터링 합니다.

## <a name="how-are-the-measurements-collected"></a>측정값이 어떻게 수집 되나요?

대기 시간 측정은 전 세계에 있는 Azure 클라우드 지역에서 호스트 되는 ThousandEyes 에이전트에서 수집 되며, 1 분 간격으로 자체적으로 네트워크 프로브를 지속적으로 보냅니다. 월간 대기 시간 통계는 해당 월에 수집 된 샘플의 평균을 계산 하 여 파생 됩니다.

## <a name="october-2019-latency-figures"></a>10 월 2019 대기 시간 수치

2019 년 10 월 31 일에 종료 되는 31 일 동안 집계 된 지역 내에서 월별 최소 및 최대 왕복 대기 시간 시간은 다음과 같습니다.

- **북아메리카** 지역 내에서 라운드트립이 **5 밀리초** 에서 **72 밀리초** 까지
- **유럽** 지역 내에서 왕복을 위한 **3 밀리초** ~ **28 밀리초**
- **아시아** 지역 내에서 왕복을 위한 **4 밀리초** ~ **134 밀리초** .

다음 지역 간 대기 시간 측정은 [ThousandEyes](https://thousandeyes.com)에서 구동 됩니다. 아래 표의 측정 단위는 밀리초 (밀리초)입니다.

![Azure 지역 간 대기 시간 통계](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>다음 단계
- [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 대해 알아봅니다.