---
title: 컨테이너 영역 매핑에 대 한 azure Monitor
description: 이 문서에서는 Azure Monitor 컨테이너, Log Analytics 작업 영역 및 사용자 지정 메트릭 간의 지원 되는 지역 매핑을 설명 합니다.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518080"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor에서 지원 되는 지역 매핑

 Azure Monitor에 제출 된 사용자 지정 메트릭 수집 및 Log Analytics 작업 영역 및 AKS 클러스터를 연결 하는 것에 대 한 특정 지역 에서만 지원 됩니다 컨테이너에 대 한 Azure Monitor를 사용 하는 경우.

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 작업 영역 매핑을 지원

Log Analytics 작업 영역을 AKS 클러스터 리소스는 다른 지역에 있을 수 있습니다 하 고 다음 표에서 매핑을 보여 줍니다.

|**AKS 클러스터 지역** | **Log Analytics 작업 영역 지역** |
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
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|

<sup>1</sup> 용량 제한으로 인해 지역을 사용할 수 없는 새 리소스를 만들 때. Log Analytics 작업 영역을 포함합니다. 그러나 지역에서 기존 연결 된 리소스는 계속 작동 합니다.

## <a name="custom-metrics-supported-regions"></a>지원 되는 지역 사용자 지정 메트릭

클러스터 노드에 Azure Kubernetes 서비스 (AKS)에서 메트릭 수집 및 pod는 다음 에서만 사용자 지정 메트릭으로 게시에 대 한 지원 [Azure 지역](../platform/metrics-custom-overview.md#supported-regions)합니다.

## <a name="next-steps"></a>다음 단계

AKS 클러스터를 모니터링 하려면 검토 [컨테이너에 대 한 Azure Monitor를 사용 하도록 설정 하는 방법을](container-insights-onboard.md) 모니터링을 사용 하도록 요구 사항 및 사용할 수 있는 메서드를 알아야 합니다.  