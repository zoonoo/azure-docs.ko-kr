---
title: Azure 스프링 클라우드의 메트릭 이해
description: Azure 스프링 클라우드에서 메트릭을 검토 하는 방법을 알아봅니다.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 952dfc1c707df3b7fa61443ea6ea18630352f0dc
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607802"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure 스프링 클라우드의 메트릭

Azure Monitor 메트릭 탐색기는 차트를 그리기, 추세를 시각적으로 상호 연결 하 고 메트릭의 급증 성과 dip를 조사할 수 있는 Microsoft Azure portal의 구성 요소입니다. 메트릭 탐색기를 사용 하 여 리소스의 상태 및 사용률을 조사할 수 있습니다. Azure 스프링 클라우드에서는 **응용 프로그램 개요** 페이지와 서비스 수준 메트릭 페이지에서 메트릭을 보는 두 가지 옵션을 제공 합니다.

## <a name="application-overview-page"></a>응용 프로그램 개요 페이지

각 응용 프로그램의 **응용 프로그램 개요** 페이지에는 응용 프로그램에 대 한 빠른 상태 검사를 수행할 수 있는 메트릭 차트가 표시 됩니다.  Azure 스프링 클라우드 서비스 페이지로 이동 하 여 **응용 프로그램 대시보드**를 선택 하 고 목록에서 응용 프로그램을 선택 합니다.  

다음에 대해 1 분 마다 메트릭이 업데이트 된 5 개의 차트를 제공 합니다.

* **Http 서버 오류**: 앱에 대 한 http 요청의 오류 수입니다.
* **데이터**: 앱에서 받은 바이트 수입니다.
* **데이터 출력**: 앱에 전송 되는 바이트 수입니다.
* **요청**: 앱에서 받은 요청입니다.
* **평균 응답 시간**: 앱의 평균 응답 시간입니다.

1 시간에서 최대 7 일까 지 차트의 시간 범위를 선택할 수 있습니다.

## <a name="service-level-metric-queries"></a>서비스 수준 메트릭 쿼리

Azure 스프링 클라우드를 사용 하면 다양 한 응용 프로그램 메트릭을 모니터링할 수 있습니다. 이 서비스에 대 한 자세한 내용은 Azure Monitor 메트릭 [시작 가이드](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) 를 검토 하세요.

메트릭 데이터를 검토 하려면 메트릭, **집계**및 시간 범위를 선택 합니다.  이러한 개념은 아래에 설명 되어 있습니다.

### <a name="aggregation"></a>집계 

Azure는 1 분 마다 메트릭을 폴링하고 업데이트 합니다. Azure는 선택 된 기간 동안 데이터를 집계 하는 세 가지 방법을 제공 합니다.

* **Total**: 모든 메트릭을 대상 출력으로 합 합니다.
* **Average**: 기간의 평균 값을 대상 출력으로 사용 합니다.
* **최대/최소**: 기간의 최대/최소 값을 대상 출력으로 사용 합니다.

### <a name="time-range"></a>시간 범위

기본 시간 범위를 선택 하거나 고유한 시간 범위를 정의 합니다.

### <a name="modifying-the-granularity-of-your-metric-query"></a>메트릭 쿼리의 세분성 수정

기본적으로 Azure는 모든 Azure 스프링 클라우드 서비스의 응용 프로그램에 대 한 메트릭을 집계 합니다. 응용 프로그램 또는 인스턴스 수준에서 메트릭을 검토 하려면 filter 함수를 사용 합니다.  
**필터 추가**를 선택 하 고, 속성을 **앱** 으로 설정 하 고, 모니터링 하려는 대상 응용 프로그램을 선택 합니다. 필요에 따라 **분할 적용** 옵션을 사용 하 여 차트의 각 앱에 대해 별도의 줄을 그립니다.

>[!TIP]
> 메트릭 페이지에서 사용자 고유의 차트를 빌드하고 **대시보드에**고정할 수 있습니다. 먼저 차트 이름을 지정 합니다.  다음으로, **오른쪽 위 모서리에서 대시보드에 고정**을 선택 합니다. 이제 포털 **대시보드에서**응용 프로그램을 확인할 수 있습니다.