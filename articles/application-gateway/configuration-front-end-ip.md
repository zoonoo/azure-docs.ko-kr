---
title: Azure Application Gateway 프런트 엔드 IP 주소 구성
description: 이 문서에서는 Azure Application Gateway 프런트 엔드 IP 주소를 구성하는 방법을 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: eff63510f70dd7b4cdd522cc5a2a68096cda7166
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548719"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Azure Gateway 프런트 엔드 IP 주소 구성

공용 IP 주소, 개인 IP 주소 또는 둘 다를 사용하도록 애플리케이션 게이트웨이를 구성할 수 있습니다. 클라이언트가 인터넷 연결 VIP(가상 IP)로 인터넷을 통해 액세스해야 하는 백 엔드를 호스트하는 경우 공용 IP가 필요합니다.

## <a name="public-and-private-ip-address-support"></a>공용 및 개인 IP 주소 지원

현재 Application Gateway V2는 사설 IP 전용 모드를 지원하지 않습니다. 지원되는 조합은 다음과 같습니다.

* 개인 IP 주소 및 고용 IP 주소
* 공용 IP 주소 전용

자세한 내용은 [Application Gateway](application-gateway-faq.yml#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)를 참조하세요.


공용 IP 주소는 인터넷에 노출되지 않은 내부 엔드포인트에는 필요하지 않습니다. 이를 ILB(*내부 부하 분산 장치*) 엔드포인트 또는 개인 프런트 엔드 IP라고 합니다. 애플리케이션 게이트웨이 ILB를 사용하면 인터넷에 노출되지 않은 내부 LOB(기간 업무) 애플리케이션에 유용합니다. 이는 인터넷에 노출되지 않은 보안 경계 내에 있는 다중 계층 애플리케이션의 서비스/계층에도 유용하지만, 여전히 라운드 로빈 로드 분배, 세션 고정, 또는 TLS 종료가 필요합니다.

하나의 공용 IP 주소와 하나의 개인 IP 주소만 지원됩니다. 애플리케이션 게이트웨이를 만들 때 프런트 엔드 IP를 선택합니다.

- 공용 IP 주소의 경우, 새 공용 IP 주소를 만들거나 애플리케이션 게이트웨이와 동일한 위치의 기존 공용 IP를 사용할 수 있습니다. 자세한 내용은 [고정 및 동적 공용 IP 주소](./application-gateway-components.md#static-versus-dynamic-public-ip-address)를 참조하세요.

- 개인 IP 주소의 경우, 애플리케이션 게이트웨이가 생성되는 서브넷의 개인 IP 주소를 지정할 수 있습니다. 하나를 지정하지 않으면 임의의 IP 주소가 서브넷에서 자동으로 선택됩니다. 선택한 IP 주소 유형(고정 또는 동적)은 나중에 변경할 수 없습니다. 자세한 내용은 [ILB(내부 부하 분산 장치)를 사용하여 애플리케이션 게이트웨이 만들기](./application-gateway-ilb-arm.md)를 참조하세요.

프런트 엔드 IP 주소는 프런트 엔드 IP에서 들어오는 요청을 확인하는 *수신기* 에 연결됩니다.

## <a name="next-steps"></a>다음 단계

- [수신기 구성에 대해 알아보기](configuration-listeners.md)
