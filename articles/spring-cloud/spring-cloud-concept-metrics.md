---
title: Azure Spring Cloud의 메트릭 이해
description: Azure 스프링 클라우드에서 메트릭을 검토 하는 방법 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: e6517f1a7374b3960c3b749e63a90fe9eb21e7b0
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277934"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Azure 스프링 클라우드의 메트릭 이해

Azure 메트릭 탐색기는 차트를 그리기, 추세를 시각적으로 상호 연결 하 고 메트릭의 급증 성과 dip를 조사할 수 있는 Microsoft Azure portal 구성 요소입니다. 메트릭 탐색기를 사용하여 리소스의 상태 및 사용률을 조사합니다. 

Azure 스프링 클라우드에서는 메트릭에 대 한 두 가지 뷰 지점이 있습니다.
* 각 응용 프로그램 개요 페이지의 차트
* 공통 메트릭 페이지

 ![메트릭 차트](media/metrics/metrics-1.png)

응용 프로그램 **개요** 의 차트는 각 응용 프로그램에 대 한 빠른 상태 검사를 제공 합니다. 공용 **메트릭** 페이지에는 참조에 사용할 수 있는 모든 메트릭이 포함 되어 있습니다. 공용 메트릭 페이지에서 고유한 차트를 빌드하고 대시보드에 고정할 수 있습니다.

## <a name="application-overview-page"></a>응용 프로그램 개요 페이지
개요 페이지에서 차트를 찾으려면 **앱 관리** 에서 앱을 선택 합니다.  

 ![응용 프로그램 메트릭 관리](media/metrics/metrics-2.png)

각 애플리케이션의 **애플리케이션 개요** 페이지에는 애플리케이션에 대한 빠른 상태 검사를 수행할 수 있는 메트릭 차트가 표시됩니다.  

 ![응용 프로그램 메트릭 개요](media/metrics/metrics-3.png)

Azure 스프링 클라우드는 1 분 마다 업데이트 되는 메트릭을 사용 하 여 다음과 같은 5 가지 차트를 제공 합니다.

* **Http 서버 오류**: 앱에 대 한 http 요청의 오류 수
* **데이터의 데이터**: 앱에서 받은 바이트 수
* **데이터 출력**: 앱에서 보낸 바이트 수
* **요청**: 앱에서 받은 요청
* **평균 응답 시간**: 앱의 평균 응답 시간

차트의 경우 1 시간에서 7 일 사이의 시간 범위를 선택할 수 있습니다.

## <a name="common-metrics-page"></a>공통 메트릭 페이지

왼쪽 탐색 창의 **메트릭은** 공통 메트릭 페이지에 연결 됩니다.

먼저 보려는 메트릭을 선택 합니다.

![메트릭 보기 선택](media/metrics/metrics-4.png)

다음으로 각 메트릭에 대해 집계 유형을 선택 합니다.

![메트릭 집계](media/metrics/metrics-5.png)

집계 유형은 시간을 집계 하는 방법을 나타냅니다. 1 분 마다 하나의 메트릭 지점이 있습니다.
* Total: 모든 메트릭을 대상 출력으로 합 합니다.
* Average: 기간의 평균 값을 대상 출력으로 사용 합니다.
* 최대/최소: 기간의 최대/최소 값을 대상 출력으로 사용 합니다.

표시할 시간 범위를 수정할 수도 있습니다.  시간 범위는 최근 30 분에서 30 일까지 또는 사용자 지정 시간 범위에서 선택할 수 있습니다.

![메트릭 수정](media/metrics/metrics-6.png)

기본 보기에는 모든 Azure 스프링 클라우드 서비스의 application's 포함 됩니다. 한 응용 프로그램 또는 인스턴스의 메트릭을 필터링 하 여 표시할 수 있습니다.  **필터 추가**를 클릭 하 고, 속성을 **앱**으로 설정 하 고, **값** 텍스트 상자에서 모니터링할 대상 응용 프로그램을 선택 합니다. 

두 가지 종류의 필터 (속성)를 사용할 수 있습니다.
* 앱: 앱 이름으로 필터링
* 인스턴스: 앱 인스턴스당 필터링

![메트릭 필터](media/metrics/metrics-7.png)

한 앱에 대해 여러 줄을 그리는 **분할 적용** 옵션을 사용할 수도 있습니다.

![메트릭 분할](media/metrics/metrics-8.png)

>[!TIP]
> 메트릭 페이지에서 사용자 고유의 차트를 빌드하고 **대시보드**에 고정할 수 있습니다. 먼저 차트 이름을 지정합니다.  다음으로, **오른쪽 위 모서리에서 대시보드에 고정**을 선택합니다. 이제 포털 **대시보드**에서 애플리케이션을 확인할 수 있습니다.

## <a name="user-portal-metrics-options"></a>사용자 포털 메트릭 옵션

다음 표에서는 사용 가능한 메트릭 및 세부 정보를 보여 줍니다.
>[!div class="mx-tdBreakAll"]
>| 이름 | 표시 이름 | 스프링 발동기 메트릭 이름 | 단위 | 세부 정보 |
>|----|----|----|----|------------|
>| SystemCpuUsagePercentage | 시스템 CPU 사용량 (%) | 시스템 cpu 사용량 | 백분율 | 전체 시스템에 대 한 최근 CPU 사용량입니다. 이 값은 [0.0, 1.0] 간격의 double입니다. 0\.0 값은 최근 관찰 된 기간 동안 모든 Cpu가 유휴 상태임을 의미 하 고, 값 1.0은 모든 Cpu가 최근 관찰 되는 기간 동안 100%를 적극적으로 실행 하 고 있음을 의미 합니다. 0\.0에서 1.0 사이의 모든 값은 시스템에서 진행 되는 작업에 따라 가능 합니다. 시스템의 최근 cpu 사용량을 사용할 수 없는 경우이 메서드는 음수 값을 반환 합니다. |
>| AppCpuUsagePercentage | 앱 CPU 사용량 비율 | 앱 CPU 사용량 비율 | 백분율 | Java Virtual Machine 프로세스에 대 한 최근 CPU 사용량입니다. 이 값은 [0.0, 1.0] 간격의 double입니다. 0\.0 값은 최근 관찰 된 기간 동안 JVM 프로세스에서 스레드를 실행 하 고 있던 Cpu가 없음을 의미 하 고, 값 1.0은 모든 Cpu가 최근 관찰 되는 시간 동안 JVM 100%에서 적극적으로 스레드를 실행 하 고 있음을 의미 합니다. JVM의 스레드는 응용 프로그램 스레드와 JVM 내부 스레드를 포함 합니다. 0\.0과 1.0 사이의 모든 값은 JVM 프로세스에서 진행 되는 작업과 전체 시스템에 따라 가능 합니다. Java Virtual Machine 최근 CPU 사용을 사용할 수 없는 경우이 메서드는 음수 값을 반환 합니다. |
>| AppMemoryCommitted | 할당 된 앱 메모리 | jvm. 메모리 커밋 | 바이트 | Java 가상 컴퓨터에서 사용할 수 있도록 보장 되는 메모리의 양 (바이트)을 나타냅니다. 커밋된 메모리의 양은 시간이 지남에 따라 변경 될 수 있습니다 (증가 또는 감소). Java 가상 컴퓨터에서 시스템으로 메모리를 해제 하 고 커밋할 수 있습니다. 커밋됨은 항상 사용 되는 것 보다 크거나 같아야 합니다. |
>| AppMemoryUsed | 사용 되는 앱 메모리 | jvm. memory. 사용 됨 | 바이트 | 현재 사용 중인 메모리의 양 (바이트)을 나타냅니다. |
>| AppMemoryMax | 최대 앱 메모리 | jvm. memory. max | 바이트 | 메모리 관리에 사용할 수 있는 최대 메모리 양 (바이트)을 나타냅니다. 해당 값이 정의 되어 있지 않을 수 있습니다. 정의 된 경우 최대 메모리 양은 시간이 지남에 따라 변경 될 수 있습니다. Max가 정의 된 경우 사용 된 메모리와 커밋된 메모리의 양은 항상 max 보다 작거나 같아야 합니다. 사용 된 < = max가 여전히 true 인 경우 (예: 시스템의 가상 메모리가 부족 한 경우)에도 사용 되는 사용 되 >는 메모리를 늘려야 하는 경우 메모리 할당이 실패할 수 있습니다. |
>| MaxOldGenMemoryPoolBytes | 사용 가능한 최대 이전 생성 데이터 크기 | jvm. 최대 데이터 크기 | 바이트 | Java 가상 머신이 시작 된 이후의 이전 세대 메모리 풀의 최대 메모리 사용량입니다. |
>| OldGenMemoryPoolBytes | 이전 세대 데이터 크기 | jvm. 데이터 크기 | 바이트 | 전체 GC 후의 이전 세대 메모리 풀 크기입니다. |
>| OldGenPromotedBytes | 이전 세대 데이터 크기로 수준 올리기 | jvm. | 바이트 | Gc 이전 세대 메모리 풀의 크기에서 GC 이후로의 양의 증가가 수입니다. |
>| YoungGenPromotedBytes | 젊은 생성 데이터 크기로 수준 올리기 | jvm. 메모리 할당 | 바이트 | 한 GC 후에 한 GC에서 다음 시간까지 증가 하는 경우 증가 합니다. |
>| GCPauseTotalCount | GC 일시 중지 횟수 | jvm. 일시 중지 (총 개수) | 카운트 | 젊은 및 이전 GC를 포함 하 여이 JMV가 시작 된 이후의 총 GC 수입니다. |
>| GCPauseTotalTime | GC 일시 중지 총 시간 | jvm. 일시 중지 (전체 시간) | 밀리초 | 젊은 및 이전 GC를 포함 하 여이 JMV가 시작 된 후 사용 된 총 GC 시간입니다. |
>| TomcatSentBytes | 총 보낸 바이트 Tomcat | tomcat | 바이트 | Tomcat 웹 서버에서 보낸 데이터의 양 (바이트) |
>| TomcatReceivedBytes | 총 수신 바이트 Tomcat | tomcat | 바이트 | Tomcat 웹 서버에서 받은 데이터의 양 (바이트) |
>| TomcatRequestTotalTime | Tomcat 요청 총 시간 | tomcat (총 시간) | 밀리초 | 요청을 처리 하는 Tomcat 웹 서버의 총 시간입니다. |
>| TomcatRequestTotalCount | Tomcat 요청 총 수 | tomcat (총 개수) | 카운트 | Tomcat 웹 서버 처리 된 요청의 총 수입니다. |
>| TomcatRequestMaxTime | Tomcat 요청 최대 시간 | tomcat. 최대 | 밀리초 | Tomcat 웹 서버가 요청을 처리 하는 최대 시간 |
>| TomcatErrorCount | Tomcat 전역 오류 | tomcat | 카운트 | 처리 된 요청에 발생 한 오류 수 |
>| TomcatSessionActiveMaxCount | Tomcat 세션 최대 활성 수 | tomcat. | 카운트 | 동시에 활성화 된 최대 세션 수 |
>| TomcatSessionAliveMaxTime | Tomcat 세션 최대 활성 시간 | tomcat. | 밀리초 | 만료 된 세션이 활성 상태인 가장 긴 시간 (초) |
>| TomcatSessionCreatedCount | Tomcat 세션 생성 횟수 | tomcat 생성 | 카운트 | 생성 된 세션 수 |
>| TomcatSessionExpiredCount | Tomcat 세션 만료 수 | tomcat. | 카운트 | 만료 된 세션 수 |
>| TomcatSessionRejectedCount | Tomcat 세션 거부 횟수 | tomcat 거부 | 카운트 | 최대 활성 세션 수에 도달 하 여 만들지 않은 세션 수입니다. |

## <a name="see-also"></a>참고 항목
* [Azure 메트릭 탐색기 시작](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [진단 설정을 사용 하 여 로그 및 메트릭 분석](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>다음 단계
* [자습서: 경고 및 작업 그룹을 사용 하 여 스프링 클라우드 리소스 모니터링](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Azure 스프링 클라우드의 할당량 및 서비스 계획](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

