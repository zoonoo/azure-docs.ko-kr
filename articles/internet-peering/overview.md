---
title: Microsoft와의 피어링 설정
titleSuffix: Azure
description: 피어링 개요
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75908925"
---
# <a name="internet-peering-overview"></a>인터넷 피어링 개요

피어링은 Microsoft 온라인 서비스 및 Microsoft Azure 서비스에서 인터넷 트래픽을 교환하기 위한 Microsoft의 글로벌 네트워크(AS8075)와 사용자 네트워크 간의 상호 연결입니다. 이동 통신 사업자 또는 서비스 공급자는 에지 위치 중 하나에서 Microsoft와의 연결을 요청할 수 있습니다. Microsoft에서 각 요청을 검토하여 피어링 정책을 준수하는지 확인합니다. 다음 두 가지 방법으로 Microsoft 네트워크와의 피어링을 설정할 수 있습니다.

* **직접 피어링:**

    피어링은 Microsoft Edge의 Microsoft 네트워크와 사용자 네트워크 간 직접적인 실제 연결을 통해 설정됩니다. BGP 세션은 라우팅 정책에 따라 이러한 연결에서 구성되며 미리 협상된 규약을 사용합니다. 이를 PNI라고도 합니다.

* **교환 피어링:**

    이는 IX(인터넷 교환)에서 표준 공용 피어링 연결을 의미합니다. Microsoft 네트워크와 사용자 네트워크 간의 실제 연결은 IX에서 운영하는 스위치 패브릭을 통해 수행됩니다. BGP 세션은 IX에서 제공하는 IP 공간을 사용하여 구성됩니다.

## <a name="benefits-of-peering-with-microsoft"></a>Microsoft와의 피어링의 이점
* Microsoft와의 피어링을 사용한 Microsoft 트래픽 전달로 전송 비용이 낮아집니다.
* Microsoft Edge 네트워크로의 네트워크 홉 및 대기 시간이 줄어들어 고객의 성능이 향상됩니다.
* 중복된 위치에서 Microsoft와의 피어링으로 네트워크 또는 전송 공급자의 네트워크에서 고객 트래픽을 오류로부터 보호합니다.
* 피어링 연결에 대한 성능 메트릭을 알아보고, 인사이트를 활용하여 네트워크 문제를 해결합니다.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Azure를 사용한 피어링 설정의 이점

Azure PowerShell 또는 포털을 사용하여 Microsoft와의 피어링을 요청할 수 있습니다. 이러한 방식으로 설정된 피어링은 Azure 리소스로 관리되며 다음과 같은 이점을 제공합니다.
* Microsoft와의 피어링을 설정 및 관리하는 단계가 간소화 및 자동화됩니다.
* 모든 피어링을 한 곳에서 빠르고 쉽게 보고 관리할 수 있습니다.
* 모든 연결에 대한 상태 및 대역폭 데이터를 추적할 수 있습니다.
* 동일한 구독을 사용하여 Azure Cloud Services에 액세스할 수 있습니다.

이미 Microsoft와의 피어링을 설정한 경우에는 **레거시 피어링**이라고 합니다. 위에 나온 혜택을 활용하기 위해 Azure 리소스로 이러한 피어링을 관리할 수 있습니다. 새 피어링 요청을 제출하거나 레거시 피어링을 Azure 리소스로 변환하려면 아래 **다음 단계** 섹션의 링크를 따르세요.

## <a name="peering-policy"></a>피어링 정책
Microsoft에는 선택적이지만 일반적으로 공개된 피어링 정책이 있습니다. 피어는 성능, 기능 및 상호 혜택이 있는 위치에 따라 선택되며 특정 기술, 상용 및 법적 요구 사항이 적용됩니다. 자세한 내용은 [피어링 정책](policy.md)을 참조하세요.

## <a name="faq"></a>FAQ
피어링에 대한 질문과 대답은 [인터넷 피어링 - FAQ](faqs.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Microsoft와의 직접 피어링을 설정하는 단계에 대한 자세한 내용은 [직접 피어링 연습](walkthrough-direct-all.md)을 참조하세요.
* Microsoft와의 교환 피어링을 설정하는 단계에 대한 자세한 내용은 [교환 피어링 연습](walkthrough-exchange-all.md)을 참조하세요.
* Azure의 다른 주요 [네트워킹 기능](https://docs.microsoft.com/azure/networking/networking-overview)에 대해 알아보세요.
