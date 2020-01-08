---
title: 컨테이너 영역 매핑에 대 한 Azure Monitor
description: 이 문서에서는 컨테이너, Log Analytics 작업 영역 및 사용자 지정 메트릭에 대 한 Azure Monitor 간에 지원 되는 지역 매핑을 설명 합니다.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75403418"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>컨테이너에 대해 Azure Monitor에서 지 원하는 지역 매핑

 컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 경우 Log Analytics 작업 영역 및 AKS 클러스터를 연결 하 고 Azure Monitor 제출 된 사용자 지정 메트릭을 수집 하는 데 특정 영역만 지원 됩니다.

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 작업 영역에서 지원 되는 매핑

AKS 클러스터 리소스 또는 Log Analytics 작업 영역은 다른 지역에 상주할 수 있으며 다음 표에서는 매핑을 보여 줍니다.

|**AKS 클러스터 영역** | **Log Analytics 작업 영역 영역** |
|-----------------------|------------------------------------|
|**아프리카** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**오스트레일리아** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**아시아 태평양** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**브라질** | |
|BrazilSouth | SouthCentralUS |
|**캐나다** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**유럽** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**인도** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**일본** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**한국** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**US** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|E Us<sup>1</sup>|
|US Gov 버지니아 |US Gov 버지니아 |

<sup>1</sup> 용량 제한은 인해 새 리소스를 만들 때 지역을 사용할 수 없습니다. 여기에는 Log Analytics 작업 영역이 포함 됩니다. 그러나 지역에서 기존에 연결 된 리소스는 계속 작동 해야 합니다.

## <a name="custom-metrics-supported-regions"></a>사용자 지정 메트릭 지원 지역

AKS (Azure Kubernetes Services) 클러스터 노드 및 pod에서 메트릭을 수집 하는 것은 다음 [Azure 지역](../platform/metrics-custom-overview.md#supported-regions)에서만 사용자 지정 메트릭으로 게시 하도록 지원 됩니다.

## <a name="next-steps"></a>다음 단계

AKS 클러스터 모니터링을 시작 하려면 [컨테이너에 대해 Azure Monitor를 사용 하도록 설정](container-insights-onboard.md) 하 여 모니터링을 사용 하는 요구 사항 및 사용 가능한 방법을 이해 하는 방법을 검토 합니다.  
