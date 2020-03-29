---
title: 컨테이너 지역 매핑에 대한 Azure 모니터
description: 이 문서에서는 컨테이너, 로그 분석 작업 영역 및 사용자 지정 메트릭에 대해 Azure Monitor 간에 지원되는 지역 매핑에 대해 설명합니다.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403418"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure 모니터에서 지원 되는 지역 매핑

 컨테이너에 대한 Azure Monitor를 사용하도록 설정하면 로그 분석 작업 영역과 AKS 클러스터를 연결하고 Azure Monitor에 제출된 사용자 지정 메트릭을 수집하기 위해 특정 지역만 지원됩니다.

## <a name="log-analytics-workspace-supported-mappings"></a>로그 분석 작업 영역 지원 매핑

AKS 클러스터 리소스 또는 Log Analytics 작업 영역은 다른 지역에 있을 수 있으며 다음 표에서는 매핑을 보여 주었습니다.

|**AKS 클러스터 지역** | **로그 분석 작업 영역** |
|-----------------------|------------------------------------|
|**아프리카** | |
|남아프리카 공화국북부 |WestEurope |
|남아프리카 공화국서부 |WestEurope |
|**오스트레일리아** | |
|오스트레일리아 이스트 |오스트레일리아 이스트 |
|오스트레일리아 센트럴 |오스트레일리아 센트럴 |
|오스트레일리아센트럴2 |오스트레일리아 센트럴 |
|오스트레일리아 이스트 |오스트레일리아 이스트 |
|**아시아 태평양** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**브라질** | |
|브라질남부 | 사우스센트럴US |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**유럽** | |
|프랑스 중부 |프랑스 중부 |
|프랑스남부 |프랑스 중부 |
|북유럽 |북유럽 |
|UKSouth |UKSouth |
|영국서부 |UKSouth |
|WestEurope |WestEurope |
|**인도** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**일본** | |
|JapanEast |JapanEast |
|재팬웨스트 |JapanEast |
|**대한민국** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**우리** | |
|센트럴US |센트럴US|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|웨스투스 주 |웨스투스 주 |
|WestUS2 |WestUS2 |
|웨스트센트럴US<sup>1</sup>|이스트US<sup>1</sup>|
|US Gov 버지니아 |US Gov 버지니아 |

<sup>1</sup> 용량 제한으로 인해 새 리소스를 만들 때 지역을 사용할 수 없습니다. 여기에는 로그 분석 작업 영역이 포함됩니다. 그러나 리전에서 기존 에 연결된 리소스는 계속 작동해야 합니다.

## <a name="custom-metrics-supported-regions"></a>사용자 지정 측정항목 지원 지역

AKS(Azure Kubernetes Services) 클러스터 노드 및 포드에서 메트릭을 수집하는 것은 다음 Azure [리전에서만](../platform/metrics-custom-overview.md#supported-regions)사용자 지정 메트릭으로 게시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

AKS 클러스터 모니터링을 시작하려면 [컨테이너에 대한 Azure Monitor를 사용하여 모니터링을 활성화하는](container-insights-onboard.md) 요구 사항 및 사용 가능한 방법을 이해하는 방법을 검토합니다.  
