---
title: 컨테이너 영역 매핑에 대 한 Azure Monitor
description: 컨테이너, Log Analytics 작업 영역 및 사용자 지정 메트릭에 대 한 Azure Monitor 간에 지원 되는 지역 매핑을 설명 합니다.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9e3b8635c70dfdf33b0a062be80c948cd77923cc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272908"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>컨테이너에 대해 Azure Monitor에서 지 원하는 지역 매핑

 컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 경우 Log Analytics 작업 영역 및 AKS 클러스터를 연결 하 고 Azure Monitor 제출 된 사용자 지정 메트릭을 수집 하는 데 특정 영역만 지원 됩니다.

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 작업 영역에서 지원 되는 매핑

지원 되는 AKS 지역은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)에 나열 됩니다. Log Analytics 작업 영역은 다음 표에 나열 된 지역을 제외 하 고 동일한 영역에 있어야 합니다. 업데이트에 대 한 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases) 를 시청 하세요.


|**AKS 클러스터 영역** | **Log Analytics 작업 영역 영역** |
|-----------------------|------------------------------------|
|**아프리카** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**오스트레일리아** | |
|AustraliaCentral2 |AustraliaCentral |
|**브라질** | |
|BrazilSouth | SouthCentralUS |
|**캐나다** ||
|CanadaEast |CanadaCentral |
|**유럽** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**인도** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**일본** | |
|JapanWest |JapanEast |
|**한국** | |
|KoreaSouth |KoreaCentral |
|**US** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> 용량 제한은 인해 새 리소스를 만들 때 지역을 사용할 수 없습니다. 여기에는 Log Analytics 작업 영역이 포함 됩니다. 그러나 지역에서 기존에 연결 된 리소스는 계속 작동 해야 합니다.

## <a name="custom-metrics-supported-regions"></a>사용자 지정 메트릭 지원 지역

AKS (Azure Kubernetes Services) 클러스터 노드 및 pod에서 메트릭을 수집 하는 것은 다음 [Azure 지역](../platform/metrics-custom-overview.md#supported-regions)에서만 사용자 지정 메트릭으로 게시 하도록 지원 됩니다.

## <a name="next-steps"></a>다음 단계

AKS 클러스터 모니터링을 시작 하려면 [컨테이너에 대해 Azure Monitor를 사용 하도록 설정](container-insights-onboard.md) 하 여 모니터링을 사용 하는 요구 사항 및 사용 가능한 방법을 이해 하는 방법을 검토 합니다.  
