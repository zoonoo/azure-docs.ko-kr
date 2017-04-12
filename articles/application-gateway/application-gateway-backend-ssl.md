---
title: "Application Gateway에서 SSL 정책 및 종단 간 SSL 사용 | Microsoft Docs"
description: "이 페이지에서는 Application Gateway 종단 간 SSL 지원에 대한 개요를 제공합니다."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 5009b13cec57e6974f71610c84fdaad837085df0
ms.openlocfilehash: 5f81d8146f8000e73a2eb578ff2371a62c8875e9
ms.lasthandoff: 03/01/2017


---
# <a name="overview-of-end-to-end-ssl-and-ssl-policy-on-application-gateway"></a>Application Gateway의 SSL 정책 및 종단간 SSL에 대한 개요

Application Gateway는 게이트웨이에서 SSL 종료를 지원합니다. 그 후의 트래픽은 일반적으로 암호화되지 않은 상태로 백 엔드 서버로 흐릅니다. 이 기능을 사용하면 비용이 많이 드는 암호화/암호 해독 오버 헤드로부터 웹 서버의 부담을 줄일 수 있습니다. 그러나 암호화되지 않은 백 엔드 통신을 허용할 수 없는 일부 고객도 있습니다. 이 암호화되지 않은 통신은 보안/규정 준수 요구 사항 때문이거나 응용 프로그램이 보안 연결만 수락하는 것이기 때문일 수 있습니다. 이러한 응용 프로그램을 위해 이제 Application Gateway는 종단 간 SSL 암호화를 지원합니다.

## <a name="overview"></a>개요

종단 간 SSL은 민감한 데이터를 암호화하여 백 엔드로 안전하게 전송하는 동시에 Application Gateway가 제공하는 계층 7 부하 분산 기능의 장점을 이용할 수 있습니다. 이러한 일부 기능으로는 쿠키 선호도, URL 기반 라우팅, 사이트 기반 라우팅 지원, X-Forwarded-* 헤더 삽입 기능 등이 있습니다.

종단 간 SSL 통신 모드를 구성한 경우 Application Gateway가 게이트웨이에서 사용자 SSL 세션을 종료하고 사용자 트래픽을 해독합니다. 그런 다음 구성된 규칙을 적용하여 트래픽을 라우팅할 적절한 백 엔드 풀 인스턴스를 선택합니다. 그 후 Application Gateway가 백 엔드 서버에 새로운 SSL 연결을 시작하고 백 엔드 서버의 공개 키 인증서를 사용하여 데이터를 다시 암호화한 다음 백 엔드에 요청을 전송합니다. 종단 간 SSL은 BackendHTTPSetting에서 프로토콜 설정을 Https로 설정하면 활성화되어 백 엔드 풀에 적용됩니다. 종단 간 SSL이 활성화된 백 엔드 풀의 각 백 엔드 서버는 보안 통신을 허용하도록 인증서를 사용하여 구성해야 합니다.

![종단 간 SSL 시나리오][1]

이 예제에서는 요청 TLS1.2를 사용하여 종단 간 SSL을 사용하여 백엔드 서버 Pool1에 라우팅됩니다.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>종단 간 SSL 및 인증서 허용 목록

Application Gateway는 알려진 백 엔드 인스턴스, 즉 Application Gateway을 통해 인증서가 허용 목록에 추가된 인스턴스와 유일하게 통신할 수 있습니다. 인증서 허용 목록을 사용하려면 백 엔드 서버 인증서의 공개 키를 응용 프로그램 게이트웨이(루트 인증서가 아니라)에 업로드해야 합니다. 그러면 알려진 허용 목록의 백 엔드에 대한 연결만 허용됩니다. 나머지 백 엔드는 게이트웨이 오류를 초래합니다. 자체 서명된 인증서는 테스트 용도이며 프로덕션 워크로드에는 권장하지 않습니다. 이러한 인증서를 사용하려면 위에서 설명했듯이 응용 프로그램 게이트웨이를 통해 허용 목록에 추가해야 합니다.

## <a name="application-gateway-ssl-policy"></a>Application Gateway SSL 정책

응용 프로그램 게이트웨이는 사용자가 구성 가능한 SSL 협상 정책도 지원합니다. 이 정책을 통해 사용자는 응용 프로그램 게이트웨이에서 SSL 연결을 보다 세밀하게 제어할 수 있습니다.

1. SSL 2.0 및 3.0은 모든 Application Gateway에 대해 기본적으로 비활성화됩니다. 전혀 구성할 수 없습니다.
2. SSL 정책 정의를 사용하여 다음 세 가지 프로토콜 **TLSv1\_0**, **TLSv1\_1**, **TLSv1\_2** 중에서 원하는 것을 비활성화할 수 있습니다.
3. SSL 정책을 하나도 정의하지 않으면&3;개(TLSv1\_0, TLSv1\_1, TLSv1_2) 모두 활성화됩니다.

## <a name="next-steps"></a>다음 단계

종단 간 SSL 및 SSL 정책에 대해 학습한 후에는 [Application Gateway에서 SSL 정책 및 종단 간 SSL을 사용하도록 설정](application-gateway-end-to-end-ssl-powershell.md) 으로 이동하여 종단 간 SSL을 사용하여 Application Gateway를 만듭니다.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png

