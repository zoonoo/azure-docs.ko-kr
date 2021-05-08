---
title: Application Insights 가용성 개요
description: 반복적인 웹 테스트를 설정하여 앱 또는 웹 사이트의 가용성 및 응답성을 모니터링합니다.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 3c2d248df98fcb9f75f5de9c05b9f600955cbac7
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600797"
---
# <a name="availability-tests-overview"></a>가용성 테스트 개요

웹앱/웹 사이트를 배포한 후 가용성 및 응답성을 모니터링하도록 반복 테스트를 설정할 수 있습니다. [Application Insights](./app-insights-overview.md)는 전 세계 지점에서 정기적인 간격으로 애플리케이션에 웹 요청을 보냅니다. 애플리케이션이 응답하지 않거나 응답이 너무 느린 경우 사용자에게 경고할 수 있습니다.

공용 인터넷에서 액세스 가능한 모든 HTTP 또는 HTTPS 엔드포인트에 대해 가용성 테스트를 설정할 수 있습니다. 테스트하는 웹 사이트를 변경할 필요가 없습니다. 실제로 사용자가 소유하는 사이트일 필요는 없습니다. 서비스에서 사용하는 REST API의 가용성을 테스트할 수 있습니다.

## <a name="types-of-availability-tests"></a>가용성 테스트 유형

가용성 테스트는 다음과 같은 세 종류가 있습니다.

* [URL ping 테스트](monitor-web-app-availability.md):이 범주에는 포털을 통해 만들 수 있는 두 가지 간단한 테스트가 있습니다.
* [다단계 웹 테스트](availability-multistep.md): 웹 요청 시퀀스의 기록을 재생하여 더욱 복잡한 시나리오를 테스트할 수 있습니다. 다단계 웹 테스트는 Visual Studio Enterprise에서 만들고 포털에 업로드하여 실행할 수 있습니다.
* [사용자 지정 추적 가용성 테스트](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): 가용성 테스트 실행을 위한 사용자 지정 애플리케이션을 만들기로 결정한 경우 `TrackAvailability()` 메서드를 사용하여 결과를 Application Insights에 보낼 수 있습니다.

> [!IMPORTANT]
> [URL 핑 테스트](monitor-web-app-availability.md)와 [다단계 웹 테스트](availability-multistep.md) 모두 퍼블릭 인터넷 DNS 인프라를 활용하여 테스트하는 엔드포인트의 도메인 이름을 확인합니다. 다시 말해 프라이빗 DNS를 사용하는 경우 퍼블릭 도메인 이름 서버에서도 테스트의 모든 도메인 이름을 확인할 수 있는지 확인해야 합니다. 확인할 수 없는 경우 [사용자 지정 추적 가용성 테스트](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)를 대신 사용할 수 있습니다.

**Application Insights 리소스당 최대 100개의 가용성 테스트를 만들 수 있습니다.**

## <a name="troubleshooting"></a>문제 해결

전용 [문제 해결 문서](troubleshoot-availability.md)

## <a name="next-step"></a>다음 단계

* [가용성 경고](availability-alerts.md)
* [다단계 웹 테스트](availability-multistep.md)
* [URL 테스트](monitor-web-app-availability.md)
* [Azure Functions를 사용하여 사용자 지정 가용성 테스트 만들기 및 실행](availability-azure-functions.md)