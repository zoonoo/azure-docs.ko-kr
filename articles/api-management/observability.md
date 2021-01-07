---
title: Azure API Management의 관찰성 | Microsoft Docs
description: Azure API Management의 모든 관찰성 옵션에 대 한 개요입니다.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097826"
---
# <a name="observability-in-azure-api-management"></a>Azure API Management의 관찰성

관찰성는 생성 된 데이터에서 시스템의 내부 상태를 이해 하는 기능이 며, 해당 데이터를 탐색 하 여 발생 하는 작업과 그 이유에 대 한 질문에 답할 수 있습니다. 

Azure API Management를 사용 하면 조직이 모든 Api의 관리를 중앙 집중화할 수 있습니다. 모든 API 트래픽의 단일 진입점으로 사용 되기 때문에 Api를 관찰 하는 것이 좋습니다. 

## <a name="observability-tools"></a>관찰성 도구

다음 표에서는 Api를 관찰 하기 위해 API Management에서 지 원하는 모든 도구를 요약해 서 보여 줍니다. 각 도구는 하나 이상의 시나리오에 유용 합니다.

| 도구        | 유용한 분야    | 데이터 지연 | 보존 | 샘플링 | 데이터 종류 | 사용|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API 검사기](api-management-howto-api-inspector.md)** | 테스트 및 디버깅 | 인스턴트 | 마지막 100 추적 | 요청당 설정 | 요청 추적 | 항상
| **기본 제공 분석** | 보고 및 모니터링 | 분 | 수명 | 100% | 보고서 및 로그 | 항상 |
| **[Azure Monitor 메트릭](api-management-howto-use-azure-monitor.md)** | 보고 및 모니터링 | 분 | 93 일 (확장으로 업그레이드) | 100% | 메트릭 | 항상 |
| **[Azure Monitor 로그](api-management-howto-use-azure-monitor.md)** | 보고, 모니터링 및 디버깅 | 분 | 31 일/5GB (extend로 업그레이드) | 100% (조정 가능) | 로그 | 선택 사항 |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | 보고, 모니터링 및 디버깅 | 초 | 90 일/5GB (extend로 업그레이드) | 사용자 지정 | 로그, 메트릭 | 선택 사항 |
| **[Azure 이벤트 허브를 통해 로깅](api-management-howto-log-event-hubs.md)** | 사용자 지정 시나리오 | 초 | 관리되는 사용자 | 사용자 지정 | 사용자 지정 | 선택 사항 |

### <a name="self-hosted-gateway"></a>자체 호스팅 게이트웨이

위에서 언급 한 모든 도구는 클라우드에서 관리 되는 게이트웨이에서 지원 됩니다. [자체 호스팅 게이트웨이](self-hosted-gateway-overview.md) 는 현재 Azure Monitor에 진단 로그를 보내지 않습니다. 그러나 자체 호스팅 게이트웨이가 배포 되는 위치에서 로그를 로컬로 구성 하 고 유지할 수 있습니다. 자세한 내용은 [자체 호스팅 게이트웨이에 대 한 클라우드 메트릭 및 로그 구성](how-to-configure-cloud-metrics-logs.md) 및 [자체 호스팅 게이트웨이에 대 한 로컬 메트릭 및 로그 구성](how-to-configure-local-metrics-logs.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [API Management에 대 한 자세한 내용은 자습서를 참조 하세요.](import-and-publish.md)
