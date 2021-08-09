---
title: Peering Service 파트너 연습
titleSuffix: Azure
description: Peering Service 파트너 연습
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 292c850f5352c80112e3f0bcc5c868f8128819be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592316"
---
# <a name="peering-service-partner-walkthrough"></a>Peering Service 파트너 연습

이 섹션에서는 Peering Service에 Direct 피어링을 사용하기 위해 공급자가 따라야 하는 단계를 설명합니다.

## <a name="create-direct-peering-connection-for-peering-service"></a>Peering Service에 대한 Direct 피어링 연결 만들기
서비스 공급자는 Peering Service를 지원하는 새 Direct 피어링을 만들어 지리적 도달률을 확장할 수 있습니다. 이를 위해 다음 작업을 수행합니다.
1. 아직 Peering Service 파트너가 아닌 경우 Peering Service 파트너가 됩니다.
1. [포털을 사용하여 Direct 피어링 만들기 또는 수정](howto-direct-portal.md)에 대한 지침을 따릅니다. 고가용성 요구 사항을 충족하는지 확인합니다.
1. 그런 다음, 포털을 사용하여 Direct 피어링에 [Peering Service 사용](howto-peering-service-portal.md)에 대한 단계를 따릅니다.

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Peering Service에 대한 레거시 Direct 피어링 연결 사용
Peering Service를 지원하는 데 사용하려는 레거시 Direct 피어링이 있는 경우
1. 아직 Peering Service 파트너가 아닌 경우 Peering Service 파트너가 됩니다.
1. [포털을 사용하여 레거시 Direct 피어링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)에 대한 지침을 따릅니다. 필요한 경우 고가용성 요구 사항을 충족하기 위해 추가 회로를 주문합니다.
1. 그런 다음, 포털을 사용하여 Direct 피어링에 [Peering Service 사용](howto-peering-service-portal.md)에 대한 단계를 따릅니다.

## <a name="next-steps"></a>다음 단계

* [피어링 정책](https://peering.azurewebsites.net/peering)에 대해 자세한 정보를 알아봅니다.
* Microsoft와의 Direct 피어링을 설정하는 단계에 대한 자세한 내용은 [Direct 피어링 연습](walkthrough-direct-all.md)을 참조하세요.
* Microsoft와의 Exchange 피어링을 설정하는 단계에 대한 자세한 내용은 [Exchange 피어링 연습](walkthrough-exchange-all.md)을 참조하세요.