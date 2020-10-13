---
title: Azure 애플리케이션 Gateway 프런트 엔드 IP 주소 구성
description: 이 문서에서는 Azure 애플리케이션 Gateway 프런트 엔드 IP 주소를 구성 하는 방법을 설명 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: d6cfac7f0fb3939e57ce64f552556138ce9feacd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653193"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Application Gateway 프런트 엔드 IP 주소 구성

공용 IP 주소, 개인 IP 주소 또는 둘 다를 갖도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 공용 IP 주소는 클라이언트가 인터넷 연결 VIP (가상 IP)를 통해 인터넷을 통해 액세스 해야 하는 백 엔드를 호스팅하는 경우에 필요 합니다.

## <a name="public-and-private-ip-address-support"></a>공용 및 개인 IP 주소 지원

현재 Application Gateway V2는 사설 IP 전용 모드를 지원하지 않습니다. 다음 조합을 지원 합니다.

* 개인 IP 주소 및 공용 IP 주소
* 공용 IP 주소만

자세한 내용은 [Application Gateway에 대 한 질문과 대답](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)을 참조 하세요.


공용 IP 주소는 인터넷에 노출 되지 않은 내부 끝점에 필요 하지 않습니다. 이를 ilb ( *내부 부하 분산 장치* ) 끝점 또는 개인 프런트 엔드 IP 라고 합니다. 응용 프로그램 게이트웨이 ILB는 인터넷에 노출 되지 않는 내부 lob (기간 업무) 응용 프로그램에 유용 합니다. 또한 인터넷에 노출 되지 않지만 라운드 로빈 부하 분산, 세션 유지, TLS 종료를 필요로 하는 보안 경계 내에 있는 다중 계층 응용 프로그램의 서비스 및 계층에도 유용 합니다.

하나의 공용 IP 주소 또는 하나의 개인 IP 주소만 지원 됩니다. 응용 프로그램 게이트웨이를 만들 때 프런트 엔드 IP를 선택 합니다.

- 공용 IP 주소의 경우 새 공용 IP 주소를 만들거나 application gateway와 동일한 위치에 있는 기존 공용 IP를 사용할 수 있습니다. 자세한 내용은 [고정 및 동적 공용 IP 주소](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)를 참조 하세요.

- 개인 IP 주소의 경우 응용 프로그램 게이트웨이가 생성 되는 서브넷의 개인 IP 주소를 지정할 수 있습니다. 하나를 지정 하지 않으면 임의 IP 주소가 서브넷에서 자동으로 선택 됩니다. 선택한 IP 주소 유형 (정적 또는 동적)은 나중에 변경할 수 없습니다. 자세한 내용은 [내부 부하 분산 장치를 사용 하 여 응용 프로그램 게이트웨이 만들기](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)를 참조 하세요.

프런트 엔드 ip 주소는 프런트 엔드 IP에서 들어오는 요청을 확인 하는 *수신기*에 연결 됩니다.

## <a name="next-steps"></a>다음 단계

- [수신기 구성에 대 한 자세한 정보](configuration-listeners.md)