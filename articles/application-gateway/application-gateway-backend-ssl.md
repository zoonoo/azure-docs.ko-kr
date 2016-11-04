---
title: Application Gateway에서 SSL 정책 및 종단 간 SSL을 사용하도록 설정 | Microsoft Docs
description: 이 페이지에서는 Application Gateway 종단 간 SSL 지원에 대한 개요를 제공합니다.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva

ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: amsriva

---
# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Application Gateway에서 SSL 정책 및 종단 간 SSL을 사용하도록 설정
## <a name="overview"></a>개요
Application Gateway는 게이트웨이에서 SSL 종료를 지원합니다. 그 후의 트래픽은 일반적으로 암호화되지 않은 상태로 백 엔드 서버로 흐릅니다. 따라서 비용이 많이 드는 암호화/암호 해독 오버 헤드로부터 웹 서버의 부담을 줄일 수 있습니다. 그러나 암호화되지 않은 백 엔드 통신을 허용할 수 없는 일부 고객도 있습니다. 이는 보안/규정 준수 요구 사항 때문이거나 응용 프로그램이 보안 연결만 수락하는 것이기 때문일 수 있습니다. 이러한 응용 프로그램을 위해 이제 Application Gateway는 종단 간 SSL 암호화를 지원합니다.

종단 간 SSL은 민감한 데이터를 암호화하여 백 엔드로 안전하게 전송하는 동시에 쿠키 선호도, URL 기반 라우팅, 사이트 기반 라우팅 지원, X-Forwarded-* 헤더 삽입 기능 등 Application Gateway가 제공하는 계층 7 부하 분산 기능의 장점을 이용할 수 있습니다.

종단 간 SSL 통신 모드를 구성한 경우 Application Gateway가 게이트웨이에서 사용자 SSL 세션을 종료하고 사용자 트래픽을 해독합니다. 그런 다음 구성된 규칙을 적용하여 트래픽을 라우팅할 적절한 백 엔드 풀 인스턴스를 선택합니다. 그 후 Application Gateway가 백 엔드 서버에 새로운 SSL 연결을 시작하고 백 엔드 서버의 공개 키 인증서를 사용하여 데이터를 다시 암호화한 다음 백 엔드에 요청을 전송합니다. 종단 간 SSL은 BackendHTTPSetting에서 프로토콜 설정을 Https로 설정하면 활성화되어 백 엔드 풀에 적용됩니다. 종단 간 SSL이 활성화된 백 엔드 풀의 각 백 엔드 서버는 보안 통신을 허용하도록 인증서를 사용하여 구성해야 합니다.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

이 예에서 https://contoso.com에 대한 요청은 HTTP를 통해 ContosoServerPool으로 라우팅될 수 있으며, https://fabrikam.com에 대한 요청은 종단 간 SSL을 사용하여 HTTPS를 통해 FabrikamServerPool로 라우팅됩니다.

## <a name="end-to-end-ssl-and-white-listing-of-certificates"></a>종단 간 SSL 및 인증서 허용 목록
Application Gateway는 알려진 백 엔드 인스턴스, 즉 Application Gateway을 통해 인증서가 허용 목록에 추가된 인스턴스하고만 통신합니다. 인증서 허용 목록을 사용하려면 백 엔드 서버 인증서의 공개 키를 Application Gateway에 업로드해야 합니다. 그러면 알려지고 허용 목록에 추가된 백 엔드에 대한 연결만 허용되고 나머지 연결은 게이트웨이 오류가 발생합니다. 자체 서명된 인증서는 테스트 용도이며 프로덕션 워크로드에는 권장하지 않습니다. 마찬가지로 이러한 인증서를 사용하려면 위에서 설명했듯이 Application Gateway를 통해 허용 목록에 추가해야 합니다.

## <a name="application-gateway-ssl-policy"></a>Application Gateway SSL 정책
Application Gateway는 사용자가 구성 가능한 SSL 협상 정책도 지원합니다. 이 정책은 사용자가 Application Gateway에서 SSL 연결을 보다 세밀하게 제어할 수 있습니다.

1. SSL 2.0 및 3.0은 모든 Application Gateway에 대해 강제로 비활성화되며 전혀 구성할 수 없습니다.
2. SSL 정책 정의를 사용하여 3개 프로토콜인 TLSv1_0, TLSv1_1, TLSv1_2 중에서 원하는 것을 비활성화할 수 있습니다.
3. SSL 정책을 하나도 정의하지 않으면 3개(TLSv1_0, TLSv1_1, TLSv1_2) 모두 활성화됩니다.

## <a name="next-steps"></a>다음 단계
종단 간 SSL 및 SSL 정책에 대해 학습한 후에는 [Application Gateway에서 SSL 정책 및 종단 간 SSL을 사용하도록 설정](application-gateway-end-to-end-ssl-powershell.md) 으로 이동하여 트래픽을 암호화된 형태로 백 엔드에 전송하는 기능을 사용하여 Application Gateway를 만듭니다.

<!--HONumber=Oct16_HO2-->


