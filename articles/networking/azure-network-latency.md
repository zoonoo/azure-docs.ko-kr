---
title: Azure 네트워크 왕복 대기 시간 통계 | Microsoft Docs
description: Azure 지역 간의 왕복 대기 시간 통계에 대해 알아봅니다.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 1898bcf619f1fc9aaf19384968225e35c9589688
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847444"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 네트워크 왕복 대기 시간 통계

Azure는 [ThousandEyes](https://thousandeyes.com)에서 수집 하는 측정, 내부 모니터링 도구를 사용 하 여 네트워크의 핵심 영역에 대 한 대기 시간 (속도)을 지속적으로 모니터링 합니다.

## <a name="how-are-the-measurements-collected"></a>측정값이 어떻게 수집 되나요?

대기 시간 측정값은 전 세계 Azure 클라우드 지역에서 호스트 되는 ThousandEyes 에이전트에서 수집 되며, 1 분 간격으로 자체적으로 네트워크 프로브를 지속적으로 보냅니다. 월간 대기 시간 통계는 해당 월에 수집 된 샘플의 평균을 계산 하 여 파생 됩니다.

## <a name="july-2020-round-trip-latency-figures"></a>7 월 2020 왕복 대기 시간 수치

지난 31 일 동안 Azure 지역 간의 월별 평균 왕복 시간 (2020 년 7 월 31 일에 종료)은 다음과 같습니다. 다음 측정은 [ThousandEyes](https://thousandeyes.com)에 의해 구동 됩니다.

[![Azure 지역 간 대기 시간 통계](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>다음 단계

[Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 대해 알아봅니다.
