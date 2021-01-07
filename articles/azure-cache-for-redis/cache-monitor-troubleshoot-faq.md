---
title: Azure Cache for Redis 모니터링 및 문제 해결 Faq
description: Redis 용 Azure Cache를 모니터링 하 고 문제를 해결 하는 데 도움이 되는 일반적인 질문과 대답을 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537527"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Azure Cache for Redis 모니터링 및 문제 해결 Faq
이 문서에서는 Redis 용 Azure Cache를 모니터링 하 고 문제를 해결 하는 방법에 대 한 질문과 대답을 제공 합니다.

## <a name="common-questions-and-answers"></a>일반적인 질문과 답변
이 섹션에서는 다음 Faq를 다룹니다.

* [내 캐시의 상태 및 성능을 모니터링하려면 어떻게 해야 하나요?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [왜 시간 초과가 표시되나요?](#why-am-i-seeing-timeouts)
* [내 클라이언트가 캐시에서 연결이 끊어진 것은 무엇 때문인가요?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>내 캐시의 상태 및 성능을 모니터링하려면 어떻게 해야 하나요?
Microsoft Azure Cache for Redis 인스턴스는 [Azure Portal](https://portal.azure.com)에서 모니터링할 수 있습니다. 메트릭을 보고, 메트릭 차트를 시작 보드에 고정하고, 모니터링 차트의 날짜 및 시간 범위를 사용자 지정하고, 차트에서 메트릭을 추가 및 제거하고, 특정 조건이 충족될 경우의 경고를 설정할 수 있습니다. 자세한 내용은 [Azure Cache for Redis 모니터링](cache-how-to-monitor.md)을 참조하세요.

또한 Azure Cache for Redis **리소스 메뉴** 에는 캐시를 모니터링하고 문제를 해결할 수 있는 몇 가지 도구가 포함되어 있습니다.

* **문제 진단 및 해결** 에서는 일반적인 문제 및 문제 해결 전략에 대한 정보를 제공합니다.
* **리소스 상태** 기능은 리소스를 감시하고 예상대로 실행되는지를 알려줍니다. Azure 리소스 상태 관리 서비스에 대한 자세한 내용은 [Azure 리소스 상태 개요](../service-health/resource-health-overview.md)를 참조하세요.
* **새 지원 요청** 을 클릭하여 캐시에 대한 지원 요청을 엽니다.

이러한 도구는 Azure Cache for Redis 인스턴스의 상태를 모니터링할 수 있게 해주며 캐싱 애플리케이션 관리에 도움이 됩니다. 자세한 내용은 [Azure Cache for Redis를 구성하는 방법](cache-configure.md)의 "지원 및 문제 해결 설정" 섹션을 참조하세요.

### <a name="why-am-i-seeing-timeouts"></a>왜 시간 초과가 표시되나요?
시간 초과는 Redis와 통신하는 데 사용하는 클라이언트에서 발생합니다. Redis 서버에 명령이 전송될 때 명령은 큐에 배치되며 Redis 서버가 결국 명령을 선택하여 실행합니다. 그러나 이 프로세스 중에 클라이언트가 시간 초과될 수 있으며, 이 경우 호출 쪽에서 예외가 발생합니다. 시간 초과 문제를 해결하는 방법에 대한 자세한 내용은 [클라이언트 쪽 문제 해결](cache-troubleshoot-client.md) 및 [StackExchange.Redis 시간 초과 예외](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions)를 참조하세요.

### <a name="why-was-my-client-disconnected-from-the-cache"></a>내 클라이언트가 캐시에서 연결이 끊어진 것은 무엇 때문인가요?
캐시 연결 끊김의 몇 가지 일반적인 이유는 다음과 같습니다.

* 클라이언트 쪽 원인
  * 클라이언트 애플리케이션이 다시 배포되었습니다.
  * 클라이언트 애플리케이션이 크기 조정 작업을 수행했습니다.
    * Cloud Services나 Web Apps의 경우 자동 크기 조정 때문일 수 있습니다.
  * 클라이언트 쪽의 네트워킹 계층이 변경되었습니다.
  * 클라이언트 또는 클라이언트와 서버 간의 네트워크 노드에서 일시적인 오류가 발생했습니다.
  * 대역폭 임계값 제한에 도달했습니다.
  * CPU 바인딩된 작업을 완료하는 데 시간이 너무 오래 걸렸습니다.
* 서버 쪽 원인
  * 표준 캐시 기능에서 Redis 서비스에 대 한 Azure Cache는 주 노드에서 복제본 노드로 장애 조치 (failover)를 시작 했습니다.
  * Azure에서 캐시가 배포된 인스턴스에 패치를 적용하고 있었습니다.
    * 이 작업은 Redis 서버 업데이트 또는 일반적인 VM 유지 관리를 위한 것일 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Cache for Redis 인스턴스를 모니터링하고 관련 문제를 해결하는 방법에 대한 자세한 내용은 [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md) 및 다양한 문제 해결 안내서를 참조하세요.

[Redis faq 용 다른 Azure Cache](cache-faq.md)에 대해 알아봅니다.