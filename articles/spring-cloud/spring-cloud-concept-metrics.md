---
title: Azure Spring Cloud의 메트릭 이해
description: Azure 스프링 클라우드에서 메트릭을 검토하는 방법 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256758"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Azure 스프링 클라우드에 대한 메트릭 이해

Azure 메트릭 탐색기는 차트를 플로팅하고, 추세를 시각적으로 상호 연관시키고, 메트릭의 급증 및 급락을 조사할 수 있는 Microsoft Azure 포털의 구성 요소입니다. 메트릭 탐색기를 사용하여 리소스의 상태 및 사용률을 조사합니다. 

Azure 스프링 클라우드에는 메트릭에 대한 두 가지 보기 지점이 있습니다.
* 각 응용 프로그램 개요 페이지의 차트
* 공통 메트릭 페이지

 ![메트릭 차트](media/metrics/metrics-1.png)

응용 프로그램 **개요의** 차트는 각 응용 프로그램에 대한 빠른 상태 확인을 제공합니다. 공통 **메트릭** 페이지에는 참조할 수 있는 모든 메트릭이 포함되어 있습니다. 공통 메트릭 페이지에서 고유한 차트를 작성하고 대시보드에 고정할 수 있습니다.

## <a name="application-overview-page"></a>응용 프로그램 개요 페이지
**앱 관리에서** 앱을 선택하여 개요 페이지에서 차트를 찾습니다.  

 ![응용 프로그램 메트릭 관리](media/metrics/metrics-2.png)

각 애플리케이션의 **애플리케이션 개요** 페이지에는 애플리케이션에 대한 빠른 상태 검사를 수행할 수 있는 메트릭 차트가 표시됩니다.  

 ![응용 프로그램 메트릭 개요](media/metrics/metrics-3.png)

Azure Spring Cloud는 분마다 업데이트되는 메트릭을 통해 이러한 5개 차트를 제공합니다.

* **HTTP 서버 오류**: 앱에 대한 HTTP 요청에 대한 오류 수
* **데이터 입력**: 앱에서 수신한 바이트
* **데이터 아웃**: 앱에서 보낸 바이트
* **요청**: 앱에서 받은 요청
* **평균 응답 시간**: 앱의 평균 응답 시간

차트의 경우 1시간에서 7일까지의 시간 범위를 선택할 수 있습니다.

## <a name="common-metrics-page"></a>공통 메트릭 페이지

왼쪽 탐색 창의 **메트릭은** 공통 메트릭 페이지로 연결됩니다.

먼저 볼 메트릭을 선택합니다.

![메트릭 보기 선택](media/metrics/metrics-4.png)

모든 메트릭 옵션에 대한 자세한 내용은 아래 [섹션에서](#user-metrics-options) 확인할 수 있습니다.

다음으로 각 메트릭에 대해 집계 유형을 선택합니다.

![메트릭 집계](media/metrics/metrics-5.png)

집계 유형은 차트에서 시간별로 메트릭 포인트를 집계하는 방법을 나타냅니다. 1분마다 하나의 원시 메트릭 포인트가 있으며 분 내의 사전 집계 유형은 메트릭 유형별로 미리 정의됩니다.
* 합계: 모든 값을 대상 출력으로 합산합니다.
* 평균: 기간의 평균 값을 대상 출력으로 사용합니다.
* 최대/최소: 기간의 최대/최소 값을 대상 출력으로 사용합니다.

표시 할 시간 범위도 수정할 수 있습니다. 시간 범위는 지난 30분에서 30일 간 또는 사용자 지정 시간 범위에서 선택할 수 있습니다.

![메트릭 수정](media/metrics/metrics-6.png)

기본 보기에는 Azure Spring Cloud 서비스의 응용 프로그램의 모든 메트릭이 함께 포함됩니다. 한 앱 또는 인스턴스의 메트릭을 디스플레이에서 필터링할 수 있습니다.  **필터 추가를**클릭하고 속성을 **앱으로**설정하고 **값** 텍스트 상자에서 모니터링할 대상 응용 프로그램을 선택합니다. 

두 종류의 필터(속성)를 사용할 수 있습니다.
* 앱: 앱 이름별로 필터링
* 인스턴스: 앱 인스턴스별로 필터링

![메트릭 필터](media/metrics/metrics-7.png)

한 앱에 대해 여러 줄을 그릴 분할 **적용** 옵션을 사용할 수도 있습니다.

![메트릭 분할](media/metrics/metrics-8.png)

>[!TIP]
> 메트릭 페이지에서 사용자 고유의 차트를 빌드하고 **대시보드**에 고정할 수 있습니다. 먼저 차트 이름을 지정합니다.  다음으로, **오른쪽 위 모서리에서 대시보드에 고정**을 선택합니다. 이제 포털 **대시보드**에서 애플리케이션을 확인할 수 있습니다.

## <a name="user-metrics-options"></a>사용자 메트릭 옵션

다음 표에는 사용 가능한 메트릭 및 세부 정보가 표시됩니다.

### <a name="error"></a>Error
>[!div class="mx-tdCol2BreakAll"]
>| 이름 | 스프링 액추에이터 메트릭 이름 | 단위 | 세부 정보 |
>|----|----|----|------------|
>| 톰캣 전역 오류 | tomcat.global.error | 개수 | 처리된 요청의 오류 수 |

### <a name="performance"></a>성능
>[!div class="mx-tdCol2BreakAll"]
>| 이름 | 스프링 액추에이터 메트릭 이름 | 단위 | 세부 정보 |
>|----|----|----|------------|
>|시스템 CPU 사용률 | 시스템.cpu.사용 | 백분율 | 전체 시스템에 대한 최근 CPU 사용량. 이 값은 [0.0,1.0] 간격의 두 배입니다. 값이 0.0이면 모든 CPU가 관찰된 최근 기간 동안 유휴 상태였고 값이 1.0이면 모든 CPU가 관찰되는 동안 100%의 시간을 활발하게 실행했다는 의미입니다.|
>| 앱 CPU 사용량 비율 | 앱 CPU 사용량 비율 | 백분율 | Java 가상 시스템 프로세스에 대한 최근 CPU 사용량. 이 값은 [0.0,1.0] 간격의 두 배입니다. 값이 0.0이면 최근 관찰된 기간 동안 JVM 프로세스에서 스레드를 실행하지 않은 CPU가 없음을 의미하지만 값이 1.0이면 모든 CPU가 최근 관찰 기간 동안 JVM 100%에서 스레드를 적극적으로 실행했다는 의미입니다. JVM의 스레드에는 응용 프로그램 스레드와 JVM 내부 스레드가 포함됩니다.|
>| 앱 메모리 할당 | jvm.memory.커밋 | 바이트 | JVM에서 사용할 수 있도록 보장된 메모리 양을 나타냅니다. JVM은 시스템에 메모리를 해제할 수 있으며 커밋된 메모리는 시작보다 적을 수 있습니다. 커밋된 것은 항상 사용되는 것보다 크거나 같을 것입니다. |
>| 사용된 앱 메모리 | jvm.memory.used | 바이트 | 현재 바이트에 사용되는 메모리 양을 나타냅니다. |
>| 앱 메모리 최대 | jvm.memory.max | 바이트 | 메모리 관리에 사용할 수 있는 최대 메모리 양을 나타냅니다. 사용된 메모리와 커밋된 메모리의 양은 max가 정의된 경우 항상 최대보다 작거나 같습니다. 사용 된 메모리를 늘리려고 하면 메모리 할당이 실패할 수 > 사용 > <사용 = max는 여전히 true(예: 시스템이 가상 메모리가 부족할 때)에도 마찬가지입니다. |
>| 사용 가능한 최대 이전 세대 데이터 크기 | jvm.gc.max.data.size | 바이트 | Java 가상 시스템이 시작된 이후 이전 세대 메모리 풀의 최대 메모리 사용량입니다. |
>| 이전 세대 데이터 크기 | jvm.gc.live.data.size | 바이트 | 전체 GC 후 이전 세대 메모리 풀의 크기입니다. |
>| 이전 세대 데이터 크기로 승격 | jvm.gc.memory.승진 | 바이트 | GC 이후의 이전 세대 메모리 풀 의 크기에서 양수 증가의 수입니다. |
>| 젊은 세대 데이터 크기로 승격 | jvm.gc.memory.할당 | 바이트 | 한 GC 후 젊은 세대 메모리 풀의 크기가 다음 GC 보다 증가합니다. |
>| GC 일시 정지 수 | jvm.gc.pause(총 수) | 개수 | 이 JMV가 시작된 후 의 총 GC 수( 영 및 올드 GC 포함). |
>| GC 일시 정지 총 시간 | jvm.gc.pause (총 시간) | 밀리초 | 영 및 올드 GC를 포함하여 이 JMV가 시작된 후 소비된 총 GC 시간입니다. |

### <a name="request"></a>요청
>[!div class="mx-tdCol2BreakAll"]
>| 이름 | 스프링 액추에이터 메트릭 이름 | 단위 | 세부 정보 |
>|----|----|----|------------|
>| 톰캣 총 전송 바이트 | tomcat.global.sent | 바이트 | 전송된 데이터 수 |
>| 톰캣 총 수신 바이트 | tomcat.global.received | 바이트 | Tomcat 웹 서버가 수신한 데이터 양 |
>| 톰캣 요청 총 시간 | tomcat.global.request(총 시간) | 밀리초 | 요청을 처리하는 Tomcat 웹 서버의 총 시간 |
>| 톰캣 요청 총 수 | tomcat.global.request(총 수) | 개수 | Tomcat 웹 서버 처리 된 요청의 총 수 |
>| 톰캣 요청 최대 시간 | tomcat.global.request.max | 밀리초 | 요청을 처리하는 Tomcat 웹 서버의 최대 시간 |

### <a name="session"></a>세션
>[!div class="mx-tdCol2BreakAll"]
>| 이름 | 스프링 액추에이터 메트릭 이름 | 단위 | 세부 정보 |
>|----|----|----|------------|
>| 톰캣 세션 최대 활성 카운트 | tomcat.sessions.active.max | 개수 | 동시에 활성화된 최대 세션 수 |
>| 톰캣 세션 최대 살아있는 시간 | tomcat.sessions.alive.max | 밀리초 | 만료된 세션이 살아 있었던 가장 긴 시간(초) |
>| 톰캣 세션 생성 개수 | tomcat.sessions.created | 개수 | 생성된 세션 수 |
>| 톰캣 세션 만료 개수 | tomcat.sessions.만료됨 | 개수 | 만료된 세션 수 |
>| 톰캣 세션 거부 카운트 | tomcat.sessions.거부 | 개수 | 최대 활성 세션 수에 도달했기 때문에 생성되지 않은 세션 수입니다. |

## <a name="see-also"></a>참조
* [Azure 메트릭 탐색기 시작](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [진단 설정으로 로그 및 메트릭 분석](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>다음 단계
* [자습서: 경고 및 작업 그룹을 사용하여 스프링 클라우드 리소스 모니터링](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Azure 스프링 클라우드에 대한 할당량 및 서비스 계획](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

