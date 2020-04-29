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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720251"
---
# <a name="peering-service-partner-walkthrough"></a>Peering Service 파트너 연습

이 섹션에서는 피어 링 서비스에 대 한 직접 피어 링을 사용 하도록 설정 하기 위해 공급자가 따라야 하는 단계를 설명 합니다.

## <a name="create-direct-peering-connection-for-peering-service"></a>피어 링 서비스에 대 한 직접 피어 링 연결 만들기
서비스 공급자는 피어 링 서비스를 지 원하는 새 직접 피어 링을 만들어 지리적 위치를 확장할 수 있습니다. 이렇게 하려면 다음을 수행 합니다.
1. 피어 링 서비스 파트너가 아직 없는 경우
1. 지침에 따라 [포털을 사용 하 여 직접 피어 링을 만들거나 수정](howto-direct-portal.md)합니다. 고가용성 요구 사항을 충족 하는지 확인 합니다.
1. 그런 다음 [포털을 사용 하 여 직접 피어 링에 서비스를 사용 하도록 설정](howto-peering-service-portal.md)하는 단계를 수행 합니다.

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>피어 링 서비스에 레거시 직접 피어 링 연결 사용
피어 링 서비스를 지 원하는 데 사용 하려는 레거시 직접 피어 링이 있는 경우
1. 아직 없는 경우 피어 링 서비스 파트너가 됩니다.
1. 지침에 따라 [포털을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)합니다. 필요한 경우 고가용성 요구 사항을 충족 하기 위해 추가 회로를 주문 합니다.
1. 그런 다음 [포털을 사용 하 여 직접 피어 링에 서비스를 사용 하도록 설정](howto-peering-service-portal.md)하는 단계를 수행 합니다.

## <a name="next-steps"></a>다음 단계

* [피어 링 정책](https://peering.azurewebsites.net/peering)에 대해 알아봅니다.
* Microsoft와 직접 피어 링을 설정 하는 단계에 대해 알아보려면 [직접 피어 링 연습](walkthrough-direct-all.md)을 수행 합니다.
* Microsoft와의 Exchange 피어 링을 설정 하는 단계에 대해 알아보려면 [exchange 피어 링 연습](walkthrough-exchange-all.md)을 수행 하세요.