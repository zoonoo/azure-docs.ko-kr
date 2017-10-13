---
title: "Azure Traffic Manager의 트래픽 보기 | Microsoft Docs"
description: "Traffic Manager 트래픽 보기 소개"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: f1ab98ab173edc794bfabdf55d950ac689a37c0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager 트래픽 보기

>[!NOTE]
>Traffic Manager의 트래픽 보기 기능은 공개 미리 보기로 제공되며 일반 공급 릴리스에 있는 기능과 동일한 수준의 가용성 및 안정성을 제공하지 않을 수 있습니다. 이 기능은 지원되지 않으며, 기능이 제한될 수 있으며 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 이 기능의 가용성 및 상태에 대한 최신 알림은 [Azure Traffic Manager 업데이트](https://azure.microsoft.com/updates/?product=traffic-manager) 페이지를 참조하세요.

Traffic Manager는 DNS 수준 라우팅을 제공하므로 최종 사용자가 프로필이 생성될 때 설정한 라우팅 방법을 기준으로 정상 상태의 끝점으로 라우팅될 수 있습니다. 이를 통해 Traffic Manager에는 사용자 기반(DN 확인자 세분성 수준) 및 해당 트래픽 패턴 보기가 제공됩니다. 트래픽 보기를 사용하도록 설정하면 실행 가능한 정보를 제공할 수 있게 처리됩니다. 

트래픽 보기를 사용하여 다음을 수행할 수 있습니다.
- 사용자 기반이 있는 위치를 이해합니다(로컬 DNS 확인자 수준 세분성까지).
- 이러한 영역에서 시작된 트래픽의 양
- 이러한 사용자가 경험한 대표적 대기 시간.
- 이러한 각 사용자 기반에서 끝점이 있는 Azure 지역까지 구체적인 트래픽 패턴을 심층 탐구합니다. 

예를 들어 트래픽 보기를 사용하여 많은 수의 트래픽이 발생하지만 높은 대기 시간 문제를 겪고 있는 지역을 이해할 수 있습니다. 다음으로 새 Azure 지역에 대한 확장 계획을 세울 때 이 정보를 참조하여 사용자들이 대기 시간 문제를 덜 겪도록 할 수 있습니다.

## <a name="how-traffic-view-works"></a>트래픽 보기 작동 방식

트래픽 보기는 Traffic Manager가 이 기능이 사용되도록 설정된 프로필을 기준으로 지난 7일 동안 수신된 들어오는 쿼리를 비교할 수 있도록 합니다. 이 정보를 통해 사용자 위치를 나타내는 항목으로 사용되는 DNS 확인자의 원본 IPO 주소를 추출합니다. 이러한 항목은 DNS 확인자 수준 세분성에서 함께 그룹화되며, Traffic Manager가 유지 관리하는 IP 주소의 지리적 정보를 사용하여 사용자 기반 지역이 만들어집니다. 그런 후 Traffic Manager는 쿼리가 라우팅된 Azure 지역을 확인하고, 해당 지역으로부터 사용자에 대한 트래픽 흐름 맵을 생성합니다.
다음 단계에서 Traffic Manager는 사용자 기반 영역- Azure 지역 매핑과 여러 다른 최종 사용자 네트워크에 대해 유지 관리되는 네트워크 인텔리전스 대기 시간 표를 분석하고 상관 관계를 파악하여 Azure 지역에 연결될 때 해당 지역의 사용자가 겪게 되는 평균 대기 시간을 이해합니다. 그런 후에 이러한 모든 계산은 먼저 로컬 DNS 확인자별 IP 수준에 표로 작성된 후 사용자에게 제공됩니다. 선택한 분석 워크플로에 따라 이 정보를 처리할 수 있고, 이 정보를 CSV 파일로 다운로드할 수도 있습니다.
트래픽 보기를 사용할 경우 제공되는 정보를 생성하는 데 사용된 데이터 요소 수에 따라 요금이 청구됩니다. 현재 사용되는 유일한 데이터 요소 형식은 Traffic Manager 프로필에 대해 수신된 쿼리입니다. 가격 책정에 대한 자세한 내용은 [Traffic Manager 가격 책정 페이지](https://azure.microsoft.com/pricing/details/traffic-manager/)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Traffic Manager 작동 방식](traffic-manager-overview.md)
- Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.
- [트래픽 관리자 프로필을 만드는](traffic-manager-create-profile.md)

