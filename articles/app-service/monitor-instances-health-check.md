---
title: App Service 인스턴스의 상태 모니터링
description: 상태 검사를 사용하여 App Service 인스턴스의 상태를 모니터링하는 방법을 알아봅니다.
keywords: Azure App Service, 웹앱, 상태 검사, 트래픽 라우팅, 정상 인스턴스, 경로, 모니터링,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 3cc8ba29629c36cc9fcb295b1cdd348fbcae1584
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109732905"
---
# <a name="monitor-app-service-instances-using-health-check"></a>상태 검사를 사용하여 App Service 인스턴스 모니터링

![상태 검사 오류][2]

이 문서에서는 Azure Portal의 상태 검사를 사용하여 App Service 인스턴스를 모니터링합니다. 상태 검사를 사용하면 비정상 인스턴스를 제거하여 애플리케이션의 가용성을 높일 수 있습니다. 상태 검사를 사용하려면 [App Service 계획](./overview-hosting-plans.md)을 둘 이상의 인스턴스로 확장해야 합니다. 상태 검사 경로에서는 애플리케이션의 중요한 구성 요소를 확인해야 합니다. 예를 들어 애플리케이션이 데이터베이스 및 메시지 시스템에 종속된 경우 상태 검사 엔드포인트는 해당 구성 요소에 연결해야 합니다. 애플리케이션에서 중요한 구성 요소에 연결할 수 없는 경우 이 경로는 앱이 비정상임을 나타내는 500 수준 응답 코드를 반환해야 합니다.

## <a name="what-app-service-does-with-health-checks"></a>App Service에서 상태 검사를 통해 수행하는 기능

- 앱에서 경로를 지정하는 경우 상태 검사는 1분 간격으로 App Service 앱의 모든 인스턴스에서 이 경로를 ping합니다.
- 인스턴스가 두 개 이상의 요청 후에 200-299(포함) 사이의 상태 코드를 사용하여 응답하지 않거나 ping에 응답하지 못하는 경우 시스템은 인스턴스가 비정상 상태임을 확인하고 제거합니다.
- 제거 후 상태 검사는 비정상 인스턴스를 계속 ping합니다. 계속해서 응답하지 않는 경우 App Service는 기본 VM을 다시 시작하여 인스턴스를 정상 상태로 되돌립니다.
- 인스턴스가 1시간 동안 비정상 상태로 유지되면 새 인스턴스로 바뀝니다.
- 또한 스케일 업하거나 스케일 아웃할 때 App Service는 새 인스턴스가 준비되도록 Health check 경로를 ping합니다.

> [!NOTE]
> 상태 검사는 302 리디렉션을 따르지 않습니다. 시간당 최대 하나의 인스턴스만 교체되며, App Service 계획 당 최대 3개의 인스턴스가 있습니다.
>

## <a name="enable-health-check"></a>상태 검사 사용하도록 설정

![Azure Portal에서 상태 검사 탐색][3]

- 상태 검사를 사용하도록 설정하려면 Azure Portal로 이동하여 App Service 앱을 선택합니다.
- **모니터링** 아래에서 **상태 검사** 를 선택합니다.
- **사용** 을 선택하고 애플리케이션에서 `/health`나 `/api/health`와 같은 올바른 URL 경로를 입력합니다.
- **저장** 을 클릭합니다.

> [!CAUTION]
> 상태 검사 구성을 변경하면 앱을 다시 시작합니다. 프로덕션 앱에 미치는 영향을 최소화하려면 [스테이징 슬롯을 구성](deploy-staging-slots.md)하고 프로덕션으로 교환하는 것이 좋습니다.
>

### <a name="configuration"></a>구성

상태 검사 옵션을 구성하는 외에도 다음과 같은 [앱 설정](configure-common.md)을 구성할 수 있습니다.

| 앱 설정 이름 | 허용되는 값 | Description |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | 최대 ping 실패 수입니다. 예를 들어 `2`로 설정하면 인스턴스가 ping `2`회 실패 후 제거됩니다. 또한 스케일 업하거나 스케일 아웃할 때 App Service는 새 인스턴스가 준비되도록 Health check 경로를 ping합니다. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 0 - 100 | 정상 상태의 인스턴스가 너무 많아지는 것을 방지하기 위해 인스턴스 중 절반 이하만 제외됩니다. 예를 들어 App Service 계획이 4개의 인스턴스로 확장되고 3개가 비정상이면 최대 2개가 제외됩니다. 나머지 두 인스턴스(정상 1 및 비정상 1)에서는 계속해서 요청을 받습니다. 모든 인스턴스가 비정상인 최악의 시나리오에서는 인스턴스가 제외되지 않습니다. 이 동작을 재정의하려면 앱 설정을 `0` - `100` 사이의 값으로 설정합니다. 값이 높을수록 더 비정상 인스턴스가 많이 제거됩니다(기본값은 50). |

#### <a name="authentication-and-security"></a>인증 및 보안

상태 검사는 App Service의 인증 및 권한 부여 기능과 통합됩니다. 이러한 보안 기능을 사용하도록 설정한 경우에는 추가 설정이 필요하지 않습니다. 그러나 사용자 고유의 인증 시스템을 사용하는 경우 상태 검사 경로에서 익명 액세스를 허용해야 합니다. 사이트가 HTTP **S** 인 경우에만 사용하도록 설정된 경우 상태 검사 요청은 HTTP **S** 를 통해 전송됩니다.

대규모 엔터프라이즈 개발 팀은 일반적으로 노출된 API에 대한 보안 요구 사항을 준수해야 합니다. 상태 검사 엔드포인트를 보호하려면 먼저 [IP 제한](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [클라이언트 인증서](app-service-ip-restrictions.md#set-an-ip-address-based-rule) 또는 Virtual Network와 같은 기능을 사용하여 애플리케이션 액세스를 제한해야 합니다. 들어오는 요청 일치 `HealthCheck/1.0`의 `User-Agent`를 요구하여 상태 검사 엔드포인트를 보호할 수 있습니다. 이전 보안 기능으로 인해 요청이 이미 보호되고 있으므로 User-Agent는 스푸핑될 수 없습니다.

## <a name="monitoring"></a>모니터링

애플리케이션의 상태 검사 경로를 제공한 후 Azure Monitor를 사용하여 사이트의 상태를 모니터링할 수 있습니다. 포털의 **상태 검사** 블레이드에서 상단 도구 모음에 있는 **메트릭** 을 클릭합니다. 그러면 사이트의 기록 상태를 확인하고 새 경고 규칙을 만들 수 있는 새 블레이드가 열립니다. 사이트 모니터링에 대한 자세한 내용은 [Azure Monitor 가이드](web-sites-monitor.md)를 참조하세요.

## <a name="limitations"></a>제한 사항

프리미엄 함수 사이트에서 상태 검사를 사용하도록 설정하면 안 됩니다. 프리미엄 함수의 빠른 크기 조정으로 인해 상태 검사 요청이 HTTP 트래픽의 불필요한 변동을 야기할 수 있습니다. 프리미엄 기능에는 확장 결정을 알리는 데 사용되는 자체 내부 상태 프로브가 있습니다.

## <a name="next-steps"></a>다음 단계
- [구독의 모든 자동 크기 조정 엔진 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-alert)
- [구독에서 실패한 모든 자동 크기 조정 규모 감축/규모 확장 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
