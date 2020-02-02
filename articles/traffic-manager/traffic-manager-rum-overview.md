---
title: Azure Traffic Manager의 실제 사용자 측정
description: 이 소개에서는 Azure Traffic Manager 실제 사용자 측정 작동 하는 방법을 알아봅니다.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938445"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager 실제 사용자 측정 개요

성능 라우팅 방법을 사용하도록 Traffic Manager 프로필을 설정할 경우 서비스는 DNS 쿼리 요청이 생성된 위치를 확인하고, 해당 요청자에게 가장 낮은 우선 순위를 부여하는 Azure 지역으로 해당 요청자를 라우팅하는 결정을 내립니다. 이를 위해 Traffic Manager가 다른 최종 사용자 네트워크에 대해 유지 관리하는 네트워크 대기 시간 인텔리전스를 활용합니다.

실제 사용자 측정 기능을 사용하면 최종 사용자가 사용하는 클라이언트 애플리케이션에서 Azure 지역으로의 네트워크 대기 시간을 측정하고 Traffic Manager가 라우팅을 결정할 때 해당 정보도 고려하도록 할 수 있습니다. 실제 사용자 측정을 사용하도록 선택하여 최종 사용자가 위치한 네트워크에서 들어오는 요청에 대한 라우팅 정확도를 높일 수 있습니다. 

## <a name="how-real-user-measurements-work"></a>실제 사용자 측정이 작동하는 방식

실제 사용자 측정 기능은 Azure 지역에 대한 클라이언트 애플리케이션 측정 대기 시간을 사용된 최종 사용자 네트워크에서 확인된 값과 동일하게 유지하는 방식으로 작동합니다. 예를 들어 다른 위치(예: 북미 지역)의 사용자가 액세스하는 웹 페이지가 있는 경우 성능 라우팅 방법을 사용할 때 실제 사용자 측정 기능을 활용하여 서버 애플리케이션이 호스트되는 최선의 Azure 지역으로 이동되게 할 수 있습니다.

먼저 웹 페이지에 Azure 제공 JavaScript(고유 키 포함)를 포함하면 됩니다. 이 작업이 끝나면 사용자가 해당 웹 페이지를 방문할 때마다 JavaScript는 Traffic Manager를 쿼리하여 측정하려는 Azure 지역에 대한 정보를 가져옵니다. 이 서비스는 엔드포인트 집합을 스크립트로 반환합니다. 이러한 엔드포인트는 해당 Azure 지역에 호스트된 단일 픽셀 이미지를 다운로드하고 요청이 전송된 시간과 첫 번째 바이트가 수신된 시간 사이의 대기 시간을 기록하여 이러한 지역을 계속해서 측정합니다. 이러한 측정값은 Traffic Manager 서비스로 다시 보고됩니다.

시간이 지남에 따라 이 과정이 여러 네트워크 간에 여러 번 진행되면서 Traffic Manager는 최종 사용자가 상주하는 네트워크의 대기 시간 특성에 대해 보다 정확한 정보를 얻게 됩니다. 이 정보는 Traffic Manager의 라우팅 결정에 포함됩니다. 결과적으로 전송된 실제 사용자 측정을 기준으로 이러한 결정의 정확도가 높아집니다.

실제 사용자 측정을 사용하면 Traffic Manager로 전송되는 측정값 수에 따라 요금이 청구됩니다. 가격 책정에 대한 자세한 내용은 [Traffic Manager 가격 책정 페이지](https://azure.microsoft.com/pricing/details/traffic-manager/)를 참조하세요.

## <a name="faqs"></a>FAQ

* [실제 사용자 측정를 사용할 경우의 이점은 무엇 인가요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Azure 지역이 아닌 지역에서 실제 사용자 측정를 사용할 수 있나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [실제 사용자 측정에서 얻는 라우팅 방법](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [각 프로필을 별도로 실제 사용자 측정 사용 하도록 설정 해야 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [내 구독에 대 한 실제 사용자 측정 해제 어떻게 할까요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [웹 페이지가 아닌 클라이언트 응용 프로그램에서 실제 사용자 측정를 사용할 수 있나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [내 실제 사용자 측정 활성화 된 웹 페이지가 렌더링 될 때마다 얼마나 많은 측정값이 생성 되나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [웹 페이지에서 실제 사용자 측정 스크립트가 실행 되기 전에 지연이 있나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [측정 하려는 Azure 지역에만 실제 사용자 측정을 사용할 수 있나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [특정 숫자에 대 한 측정 수를 제한할 수 있나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [실제 사용자 측정의 일부로 클라이언트 응용 프로그램에서 측정 한 측정값을 볼 수 있나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Traffic Manager에서 제공 하는 측정 스크립트를 수정할 수 있습니까?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [다른 사용자가 실제 사용자 측정에서 사용 하는 키를 볼 수 있나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [다른 사용자가 내 RUM 키를 악용할 수 있나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [모든 웹 페이지에 JavaScript 측정을 추가 해야 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [실제 사용자 측정를 사용 하는 경우 최종 사용자에 대 한 정보를 Traffic Manager으로 식별할 수 있나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [실제 사용자 측정 웹 페이지에서 라우팅에 Traffic Manager를 사용 해야 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [실제 사용자 측정와 함께 사용 하기 위해 Azure 지역에서 서비스를 호스트 해야 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [실제 사용자 측정 사용 하는 경우 Azure 대역폭 사용량이 증가 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>다음 단계
- [웹 페이지에서 실제 사용자 측정](traffic-manager-create-rum-web-pages.md)을 사용하는 방법 알아봅니다.
- [Traffic Manager 작동 방식](traffic-manager-overview.md)
- [Mobile Center](https://docs.microsoft.com/mobile-center/)에 대해 자세히 알아봅니다.
- Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.
- [Traffic Manager 프로필을 만드는](traffic-manager-create-profile.md)

