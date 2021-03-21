---
title: App Service 인스턴스의 상태를 모니터링 합니다.
description: 상태 검사를 사용 하 여 App Service 인스턴스의 상태를 모니터링 하는 방법을 알아봅니다.
keywords: azure app service, 웹 앱, 상태 검사, 트래픽 라우팅, 정상 인스턴스, 경로, 모니터링,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 0e08d016ab85587d451ad2a1e296e7f494ba283e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596028"
---
# <a name="monitor-app-service-instances-using-health-check"></a>상태 검사를 사용 하 여 App Service 인스턴스 모니터링

![상태 검사 오류][2]

이 문서에서는 Azure Portal의 상태 검사를 사용 하 여 App Service 인스턴스를 모니터링 합니다. 상태 검사는 비정상 인스턴스를 제거 하 여 응용 프로그램의 가용성을 높입니다. 상태 검사를 사용 하려면 [App Service 계획](./overview-hosting-plans.md) 을 둘 이상의 인스턴스로 확장 해야 합니다. 상태 검사 경로는 응용 프로그램의 중요 한 구성 요소를 확인 해야 합니다. 예를 들어 응용 프로그램이 데이터베이스 및 메시징 시스템에 종속 된 경우 상태 검사 끝점은 해당 구성 요소에 연결 해야 합니다. 응용 프로그램에서 중요 한 구성 요소에 연결할 수 없는 경우이 경로는 앱이 비정상 상태임을 나타내는 500 수준 응답 코드를 반환 해야 합니다.

## <a name="what-app-service-does-with-health-checks"></a>상태 검사를 수행 하는 App Service

- 앱에서 경로를 지정 하는 경우 상태 검사는 1 분 간격으로 App Service 앱의 모든 인스턴스에서이 경로를 ping 합니다.
- 인스턴스가 두 개 이상의 요청 후에 200-299 (포함) 사이의 상태 코드를 사용 하 여 응답 하지 않거나 ping에 응답 하지 못하는 경우 시스템은 비정상 상태를 확인 하 고 제거 합니다.
- 제거 후 상태 검사는 비정상 인스턴스를 계속 ping 합니다. 계속 해 서 응답 하지 않는 경우 App Service는 기본 VM을 다시 시작 하 여 인스턴스를 정상 상태로 되돌립니다.
- 인스턴스가 1 시간 동안 비정상 상태로 유지 되 면 새 인스턴스로 바뀝니다.
- 또한 확장 하거나 축소할 때 새 인스턴스가 준비 되도록 Health check 경로를 ping App Service 합니다.

> [!NOTE]
> 상태 검사는 302 리디렉션을 따르지 않습니다. 최대 하나의 인스턴스만 시간당 교체 되며 App Service 요금제 당 최대 3 개의 인스턴스가 있습니다.
>

## <a name="enable-health-check"></a>상태 검사 사용

![Azure Portal에서 상태 검사 탐색][3]

- 상태 검사를 사용 하도록 설정 하려면 Azure Portal로 이동 하 여 App Service 앱을 선택 합니다.
- **모니터링** 아래에서 **상태 검사** 를 선택 합니다.
- **사용** 을 선택 하 고 응용 프로그램에서 또는와 같은 올바른 URL 경로를 제공 `/health` `/api/health` 합니다.
- **저장** 을 클릭합니다.

> [!CAUTION]
> 상태 검사 구성 변경 내용으로 앱을 다시 시작 합니다. 프로덕션 앱에 대 한 영향을 최소화 하려면 [스테이징 슬롯을 구성](deploy-staging-slots.md) 하 고 프로덕션으로 교환 하는 것이 좋습니다.
>

### <a name="configuration"></a>구성

상태 검사 옵션을 구성 하는 것 외에도 다음과 같은 [앱 설정을](configure-common.md)구성할 수 있습니다.

| 앱 설정 이름 | 허용되는 값 | Description |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Ping 실패의 최대 수입니다. 예를 들어로 설정 하면 `2` ping이 실패 한 후에 인스턴스가 제거 됩니다 `2` . 또한 확장 하거나 축소할 때 새 인스턴스가 준비 되도록 Health check 경로를 ping App Service 합니다. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | 정상 상태의 인스턴스를 방지 하려면 인스턴스 중 절반이 제외 됩니다. 예를 들어 App Service 요금제가 4 개의 인스턴스로 확장 되 고 3 개가 비정상 이면 최대 2 개가 제외 됩니다. 다른 두 인스턴스 (정상 및 비정상 하나)는 계속 해 서 요청을 받습니다. 모든 인스턴스가 비정상 인 최악의 시나리오에서는 none이 제외 됩니다. 이 동작을 재정의 하려면 앱 설정을 및 사이의 값으로 `0` 설정 `100` 합니다. 값이 높을수록 더 비정상 인스턴스가 제거 됩니다 (기본값은 50). |

#### <a name="authentication-and-security"></a>인증 및 보안

상태 검사는 App Service의 인증 및 권한 부여 기능과 통합 됩니다. 이러한 보안 기능을 사용 하도록 설정한 경우에는 추가 설정이 필요 하지 않습니다. 그러나 사용자 고유의 인증 시스템을 사용 하는 경우 상태 검사 경로에서 익명 액세스를 허용 해야 합니다. 사이트가 HTTP **s** 인 경우에만 사용 하도록 설정 된 경우 상태 검사 요청은 http **s** 를 통해 전송 됩니다.

대기업 개발 팀은 일반적으로 노출 된 Api에 대 한 보안 요구 사항을 준수 해야 합니다. 상태 검사 끝점의 보안을 유지 하려면 먼저 [IP 제한](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [클라이언트 인증서](app-service-ip-restrictions.md#set-an-ip-address-based-rule)또는 Virtual Network와 같은 기능을 사용 하 여 응용 프로그램 액세스를 제한 해야 합니다. 들어오는 요청 일치의를 요구 하 여 상태 검사 끝점의 보안을 유지할 수 있습니다 `User-Agent` `ReadyForRequest/1.0` . 이전 보안 기능으로 인해 요청이 이미 보호 되 고 있으므로 User-Agent는 스푸핑 될 수 없습니다.

## <a name="monitoring"></a>모니터링

응용 프로그램의 상태 검사 경로를 제공한 후 Azure Monitor를 사용 하 여 사이트의 상태를 모니터링할 수 있습니다. 포털의 **상태 검사** 블레이드에서 상단 도구 모음에 있는 **메트릭을** 클릭 합니다. 그러면 사이트의 기록 상태를 확인 하 고 새 경고 규칙을 만들 수 있는 새 블레이드가 열립니다. 사이트 모니터링에 대 한 자세한 내용은 [Azure Monitor 가이드를 참조](web-sites-monitor.md)하세요.

## <a name="limitations"></a>제한 사항

프리미엄 함수 사이트에서 상태 검사를 사용 하도록 설정 하면 안 됩니다. 프리미엄 기능의 빠른 크기 조정으로 인해 상태 검사 요청으로 인해 HTTP 트래픽이 불필요 하 게 변동 될 수 있습니다. 프리미엄 기능에는 확장 결정을 알리는 데 사용 되는 자체 내부 상태 프로브가 있습니다.

## <a name="next-steps"></a>다음 단계
- [구독의 모든 자동 크기 조정 엔진 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [구독에서 실패한 모든 자동 크기 조정 규모 감축/규모 확장 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
