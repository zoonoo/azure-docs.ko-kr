---
title: Azure Spring Cloud의 메트릭 이해
description: Azure Spring Cloud의 메트릭을 검토하는 방법 알아보기
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038502"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure Spring Cloud의 메트릭

Azure Monitor 메트릭 탐색기는 Microsoft Azure Portal의 구성 요소이며 차트를 그리고, 추세의 상관 관계를 시각적으로 지정하고, 메트릭에서 급증 및 하락을 조사할 수 있습니다. 메트릭 탐색기를 사용하여 리소스의 상태 및 사용률을 조사합니다. Azure Spring Cloud에서는 **애플리케이션 개요** 페이지 및 서비스 수준 메트릭 페이지의 차트를 보는 두 가지 옵션을 제공합니다.

## <a name="application-overview-page"></a>애플리케이션 개요 페이지

각 애플리케이션의 **애플리케이션 개요** 페이지에는 애플리케이션에 대한 빠른 상태 검사를 수행할 수 있는 메트릭 차트가 표시됩니다.  Azure Spring Cloud 서비스 페이지로 이동하여 **애플리케이션 대시보드**를 선택한 다음, 목록에서 애플리케이션을 선택합니다.  

다음 항목에 대해 매 분마다 업데이트되는 메트릭이 포함된 5개의 차트를 제공합니다.

* **Http 서버 오류**: 앱에 대한 HTTP 요청의 오류 수입니다.
* **데이터 입력**: 앱에서 받은 바이트입니다.
* **데이터 출력**: 앱에 전송되는 바이트입니다.
* **요청**: 앱에서 받은 요청입니다.
* **평균 응답 시간**: 앱의 평균 응답 시간입니다.

차트의 시간 범위를 1시간에서 최대 7일까지 선택할 수 있습니다.

## <a name="service-level-metric-queries"></a>서비스 수준 메트릭 쿼리

Azure Spring Cloud를 사용하면 다양한 애플리케이션 메트릭을 모니터링할 수 있습니다. 이 서비스에 대해 자세히 알아보려면 Azure Monitor 메트릭 [시작 가이드](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)를 검토하세요.

메트릭 데이터를 검토하려면 메트릭, **집계** 및 시간 범위를 선택합니다.  이러한 개념은 아래에 설명됩니다.

### <a name="aggregation"></a>집계 

Azure는 매 분마다 메트릭을 폴링하고 업데이트합니다. Azure는 선택된 기간 동안 데이터를 집계하는 세 가지 방법을 제공합니다.

* **Total**: 모든 메트릭을 대상 출력으로 더합니다.
* **Average**: 기간의 평균 값을 대상 출력으로 사용합니다.
* **최대/최소**: 기간의 최댓값/최솟값을 대상 출력으로 사용합니다.

### <a name="time-range"></a>시간 범위

기본 시간 범위를 선택하거나 고유한 시간 범위를 정의합니다.

### <a name="modifying-the-granularity-of-your-metric-query"></a>메트릭 쿼리의 세분성 수정

기본적으로 Azure는 모든 Azure Spring Cloud 서비스의 애플리케이션에 대한 메트릭을 집계합니다. 애플리케이션 또는 인스턴스 수준에서 메트릭을 검토하려면 필터 함수를 사용합니다.  
**필터 추가**를 선택하고, 속성을 **앱**으로 설정하고, 모니터링하려는 대상 애플리케이션을 선택합니다. 필요에 따라 **분할 적용** 옵션을 사용하여 차트의 각 앱에 대해 별도의 줄을 그립니다.

>[!TIP]
> 메트릭 페이지에서 사용자 고유의 차트를 빌드하고 **대시보드**에 고정할 수 있습니다. 먼저 차트 이름을 지정합니다.  다음으로, **오른쪽 위 모서리에서 대시보드에 고정**을 선택합니다. 이제 포털 **대시보드**에서 애플리케이션을 확인할 수 있습니다.