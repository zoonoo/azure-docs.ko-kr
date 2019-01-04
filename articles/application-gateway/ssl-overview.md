---
title: Azure Application Gateway에서 종단 간 SSL 사용
description: 이 문서는 Application Gateway 종단 간 SSL 지원에 대한 개요입니다.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2018
ms.author: amsriva
ms.openlocfilehash: e7020ef5c1f7411c7226e7a2db489112ee6bf0a4
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945504"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Application Gateway의 종단 간 SSL 개요

Application Gateway는 게이트웨이에서 SSL 종료를 지원합니다. 그 후의 트래픽은 일반적으로 암호화되지 않은 상태로 백 엔드 서버로 흐릅니다. 이 기능을 사용하면 비용이 많이 드는 암호화 및 암호 해독 오버헤드로부터 웹 서버의 부담을 줄일 수 있습니다. 그러나 암호화되지 않은 백 엔드 통신을 허용할 수 없는 일부 고객도 있습니다. 이 암호화되지 않은 통신은 보안 요구 사항, 규정 준수 요구 사항 때문이거나 애플리케이션에서 보안 연결만 수락할 수 있기 때문일 수 있습니다. 이러한 애플리케이션을 위해 Application Gateway에서 종단 간 SSL 암호화를 지원합니다.

종단 간 SSL을 사용하면 Application Gateway에서 제공하는 계층 7 부하 분산 기능의 이점을 활용하면서 중요한 데이터를 암호화하여 백 엔드로 안전하게 전송할 수 있습니다. 이러한 기능 중 일부는 쿠키 기반 세션 선호도, URL 기반 라우팅, 사이트 기반 라우팅 지원 또는 X-Forwarded-* 헤더 삽입 기능입니다.

종단 간 SSL 통신 모드로 구성한 경우 Application Gateway가 게이트웨이에서 SSL 세션을 종료하고 사용자 트래픽을 암호 해독합니다. 그런 다음 구성된 규칙을 적용하여 트래픽을 라우팅할 적절한 백 엔드 풀 인스턴스를 선택합니다. 그런 다음, Application Gateway에서 백 엔드 서버에 대한 새로운 SSL 연결을 시작하고, 백 엔드 서버의 공개 키 인증서를 사용하여 데이터를 다시 암호화한 다음, 백 엔드에 해당 요청을 전송합니다. 종단 간 SSL은 **BackendHTTPSetting**에서 프로토콜 설정을 HTTPS로 설정하여 활성화된 후에 백 엔드 풀에 적용됩니다. 종단 간 SSL이 활성화된 백 엔드 풀의 각 백 엔드 서버는 보안 통신을 허용하도록 인증서를 사용하여 구성해야 합니다.

![종단 간 SSL 시나리오][1]

이 예제에서는 요청 TLS1.2를 사용하여 종단 간 SSL을 사용하여 백엔드 서버 Pool1에 라우팅됩니다.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>종단 간 SSL 및 인증서 허용 목록

Application Gateway는 알려진 백 엔드 인스턴스, 즉 Application Gateway을 통해 인증서가 허용 목록에 추가된 인스턴스와 유일하게 통신할 수 있습니다. 인증서 허용 목록을 사용하려면 백 엔드 서버 인증서의 공개 키를 애플리케이션 게이트웨이(루트 인증서가 아니라)에 업로드해야 합니다. 그러면 알려진 허용 목록의 백 엔드에 대한 연결만 허용됩니다. 나머지 백 엔드는 게이트웨이 오류를 초래합니다. 자체 서명된 인증서는 테스트 용도이며 프로덕션 워크로드에는 권장하지 않습니다. 마찬가지로 이러한 인증서를 사용하려면 위에서 설명했듯이 애플리케이션 게이트웨이를 통해 허용 목록에 추가해야 합니다.

> [!NOTE]
> 인증 인증서 설정은 Azure Web Apps와 같은 신뢰할 수 있는 Azure 서비스에 필요하지 않습니다.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>v2 SKU를 사용하는 종단 간 SSL

인증 인증서는 Application Gateway v2 SKU에서 더 이상 사용되지 않으며 신뢰할 수 있는 루트 인증서로 대체되었습니다. 이러한 인증서는 다음과 같은 몇 가지 주요 차이점을 제외하고 인증 인증서와 비슷합니다.

- 해당 CN이 HTTP 백 엔드 설정의 호스트 이름과 일치하는 잘 알려진 CA 기관에서 서명한 인증서의 경우 종단 간 SSL 작동을 위해 추가 단계가 필요하지 않습니다. 

   예를 들어, 백 엔드 인증서가 잘 알려진 CA에서 발급한 것이고 CN이 contoso.com이며, 백 엔드 http 설정의 호스트 필드도 contoso.com으로 설정된 경우 추가 단계가 필요하지 않습니다. 백 엔드 http 설정 프로토콜을 HTTPS로 설정할 수 있고, 상태 프로브 및 데이터 경로 둘 다 SSL을 사용하도록 설정할 수 있습니다. Azure Web Apps 또는 다른 Azure 웹 서비스를 백 엔드로 사용하는 경우 이러한 항목도 암시적으로 신뢰되며, 종단 간 SSL을 위한 추가 단계가 필요하지 않습니다.
- 인증서가 자체 서명되었거나 알 수 없는 매개체가 서명한 경우 v2 SKU에서 종단 간 SSL을 사용하도록 설정하려면 신뢰할 수 있는 루트 인증서를 정의해야 합니다. Application Gateway는 해당 서버 인증서의 루트 인증서가 풀과 연결된 백 엔드 http 설정의 신뢰할 수 있는 루트 인증서 목록 중 하나와 일치하는 백 엔드와만 통신합니다.
- 루트 인증서 일치 외에, Application Gateway는 백 엔드 http 설정에 지정된 호스트 설정이 백 엔드 서버의 SSL 인증서가 제공하는 CN(공통 이름)의 호스트 설정과 일치하는지도 확인합니다. 백 엔드에 대한 SSL 연결을 설정하려고 하면 Application Gateway는 SNI(서버 이름 표시) 확장을 백 엔드 http 설정에 지정된 호스트로 설정합니다.
- 백 엔드 http 설정의 호스트 필드 대신 **pick hostname from backend address**(백 엔드 주소에서 호스트 이름 선택)를 선택하면 SNI 헤더가 항상 백 엔드 풀 FQDN으로 설정되고, 백 엔드 서버 SSL 인증서의 CN은 해당 FQDN과 일치해야 합니다. IP를 사용하는 백 엔드 풀 멤버는 이 시나리오에서 지원되지 않습니다.
- 루트 인증서는 백 엔드 서버 인증서의 base64 인코딩 루트 인증서입니다.

## <a name="next-steps"></a>다음 단계

종단 간 SSL에 대해 학습한 후에는 [PowerShell에서 Application Gateway를 사용하여 종단 간 SSL 구성](application-gateway-end-to-end-ssl-powershell.md)으로 이동하고 종단 간 SSL을 사용하는 Application Gateway를 만듭니다.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
