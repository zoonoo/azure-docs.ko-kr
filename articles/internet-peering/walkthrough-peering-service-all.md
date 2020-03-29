---
title: Peering Service 파트너 연습
titleSuffix: Azure
description: Peering Service 파트너 연습
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720251"
---
# <a name="peering-service-partner-walkthrough"></a>Peering Service 파트너 연습

이 섹션에서는 피어링 서비스에 대한 직접 피어링을 활성화하기 위해 공급자가 따라야 하는 단계를 설명합니다.

## <a name="create-direct-peering-connection-for-peering-service"></a>피어링 서비스를 위한 직접 피어링 연결 만들기
서비스 공급자는 피어링 서비스를 지원하는 새로운 Direct 피어링을 만들어 지리적 범위를 확장할 수 있습니다. 이를 위해,
1. 아직 피어링 서비스 파트너가 되지 않은 경우
1. 지침에 따라 [포털을 사용하여 직접 피어링을 만들거나 수정합니다.](howto-direct-portal.md) 고가용성 요구 사항을 충족하는지 확인합니다.
1. 그런 다음 [포털을 사용하여 직접 피어링에서 피어링 서비스 활성화 단계를](howto-peering-service-portal.md)따릅니다.

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>피어링 서비스에 레거시 직접 피어링 연결 사용
피어링 서비스를 지원하는 데 사용할 레거시 Direct 피어링이 있는 경우
1. 피어링 서비스 파트너가 되지 않으면 피어링 서비스 파트너가 됩니다.
1. 지침에 따라 [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환합니다.](howto-legacy-direct-portal.md) 필요한 경우 고가용성 요구 사항을 충족하기 위해 추가 회로를 주문하십시오.
1. 그런 다음 [포털을 사용하여 직접 피어링에서 피어링 서비스 활성화 단계를](howto-peering-service-portal.md)따릅니다.

## <a name="next-steps"></a>다음 단계

* [피어링 정책에](https://peering.azurewebsites.net/peering)대해 자세히 알아봅니다.
* Microsoft를 사용하여 직접 피어링을 설정하는 단계에 대해 알아보려면 [직접 피어링 연습을](walkthrough-direct-all.md)따르십시오.
* Microsoft를 사용하여 Exchange 피어링을 설정하는 단계에 대해 알아보려면 [Exchange 피어링 단계를](walkthrough-exchange-all.md)따릅니다.