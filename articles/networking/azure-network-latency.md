---
title: Azure 네트워크 왕복 대기 시간 통계 | 마이크로 소프트 문서
description: Azure 지역 간의 왕복 대기 시간 통계에 대해 알아봅니다.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886955"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 네트워크 왕복 대기 시간 통계

Azure는 타사 합성 모니터링 서비스인 [ThousandEyes에서](https://thousandeyes.com)수집한 측정뿐만 아니라 내부 모니터링 도구를 사용하여 네트워크의 핵심 영역의 대기 시간(속도)을 지속적으로 모니터링합니다.

## <a name="how-are-the-measurements-collected"></a>측정은 어떻게 수집되나요?

대기 시간 측정은 전 세계 Azure 클라우드 지역에서 호스팅되는 ThousandEyes 에이전트에서 수집되어 1분 간격으로 네트워크 프로브를 지속적으로 전송합니다. 월별 대기 시간 통계는 한 달 동안 수집된 샘플을 평균화하여 파생됩니다.

## <a name="march-2020-round-trip-latency-figures"></a>2020년 3월 왕복 지연 수치

지난 31일(2020년 3월 31일 종료)의 Azure 지역 간 월평균 왕복 시간은 다음과 같습니다. 다음 측정은 천에 의해 [구동된다눈](https://thousandeyes.com).

[![Azure 지역 간 대기 시간 통계](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>다음 단계

Azure [지역에](https://azure.microsoft.com/global-infrastructure/regions/)대해 자세히 알아보기.
