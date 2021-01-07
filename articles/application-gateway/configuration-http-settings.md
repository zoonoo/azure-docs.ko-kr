---
title: Azure 애플리케이션 Gateway HTTP 설정 구성
description: 이 문서에서는 Azure 애플리케이션 게이트웨이 HTTP 설정을 구성 하는 방법을 설명 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653020"
---
# <a name="application-gateway-http-settings-configuration"></a>Application Gateway HTTP 설정 구성

응용 프로그램 게이트웨이는 여기에서 지정한 구성을 사용 하 여 백 엔드 서버로 트래픽을 라우팅합니다. HTTP 설정을 만든 후 하나 이상의 요청 라우팅 규칙과 연결 해야 합니다.

## <a name="cookie-based-affinity"></a>쿠키 기반 선호도

Azure 애플리케이션 게이트웨이는 사용자 세션을 유지 관리 하기 위해 게이트웨이 관리 쿠키를 사용 합니다. 사용자가 Application Gateway에 대 한 첫 번째 요청을 보내면 해당 요청은 세션 정보를 포함 하는 해시 값을 사용 하 여 응답에 선호도 쿠키를 설정 하므로 선호도 쿠키를 전송 하는 후속 요청은 동일 하 게 유지 관리 하기 위해 동일한 백 엔드 서버로 라우팅됩니다. 

이 기능은 동일한 서버에 사용자 세션을 유지 하려는 경우와 사용자 세션에 대해 세션 상태가 서버에 로컬로 저장 된 경우에 유용 합니다. 응용 프로그램에서 쿠키 기반 선호도를 처리할 수 없는 경우에는이 기능을 사용할 수 없습니다. 이를 사용 하려면 클라이언트에서 쿠키를 지원 하는지 확인 합니다.

[Chromium browser](https://www.chromium.org/Home) [V80 업데이트](https://chromiumdash.appspot.com/schedule) 는 [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) 특성이 없는 HTTP 쿠키를 SameSite = 느슨한으로 처리 해야 하는 것을 요구 합니다. CORS (크로스-원본 자원 공유) 요청의 경우 타사 컨텍스트에서 쿠키를 보내야 하는 경우 SameSite = None을 사용 해야 합니다 *. 보안* 특성 및이를 HTTPS를 통해 전송 해야 합니다. 그렇지 않으면 HTTP 전용 시나리오에서 브라우저는 타사 컨텍스트의 쿠키를 전송 하지 않습니다. Chrome에서이 업데이트의 목표는 보안을 강화 하 고 CSRF (교차 사이트 요청 위조) 공격을 방지 하는 것입니다. 

이러한 변경을 지원 하기 위해 17 2020 년 2 월 Application Gateway 일부 터 기존 *Applicationgatewayaffinity* 쿠키 외에도 *ApplicationGatewayAffinityCORS* 이라는 다른 쿠키를 삽입 합니다. *ApplicationGatewayAffinityCORS* 쿠키에 추가 된 두 개의 특성이 있습니다 (*"SameSite = None;). Secure "*)를 설정 하므로 원본 간 요청에 대해서도 고정 세션이 유지 됩니다.

기본 선호도 쿠키 이름은 *Applicationgatewayaffinity* 이며 변경할 수 있습니다. 사용자 지정 선호도 쿠키 이름을 사용 하는 경우 추가 쿠키는 CORS로 CORS를 사용 하 여 추가 됩니다. 예를 들면 *CustomCookieNameCORS*입니다.

> [!NOTE]
> *SameSite = None* 특성을 설정 하는 경우 쿠키에 *보안* 플래그가 포함 되 고 HTTPS를 통해 전송 되어야 합니다.  CORS를 통해 세션 선호도가 필요한 경우 작업을 HTTPS로 마이그레이션해야 합니다. 여기 Application Gateway에 대 한 TLS 오프 로드 및 종단 간 TLS 설명서를 참조 하세요. [개요](ssl-overview.md), [Azure Portal를 사용 하 여 tls 종료를 사용 하 여 응용 프로그램 게이트웨이 구성](create-ssl-portal.md), [포털에서 Application Gateway를 사용 하 여 종단 간 tls 구성](end-to-end-ssl-portal.md)

## <a name="connection-draining"></a>연결 드레이닝

연결 드레이닝은 계획 된 서비스 업데이트 중에 백 엔드 풀 멤버를 정상적으로 제거 하는 데 도움이 됩니다. HTTP 설정에서 연결 드레이닝을 사용 하도록 설정 하 여 백 엔드 풀의 모든 멤버에이 설정을 적용할 수 있습니다. 이를 통해 백 엔드 풀의 모든 있음이 인스턴스는 기존 연결을 계속 유지 하 고 구성 가능한 시간 제한에 대해 진행 중인 요청을 처리할 수 있으며 새 요청 또는 연결을 받지 않습니다. 이에 대 한 유일한 예외는 게이트웨이 관리 세션 선호도로 인해 있음이 인스턴스에 대해 바인딩된 요청이 며 있음이 인스턴스로 계속 전달 됩니다. 연결 드레이닝은 백 엔드 풀에서 명시적으로 제거 된 백 엔드 인스턴스에 적용 됩니다.

## <a name="protocol"></a>프로토콜

Application Gateway 백 엔드 서버에 대 한 라우팅 요청에 HTTP 및 HTTPS를 모두 지원 합니다. HTTP를 선택 하는 경우 백 엔드 서버에 대 한 트래픽이 암호화 되지 않습니다. 암호화 되지 않은 통신이 허용 되지 않는 경우 HTTPS를 선택 합니다.

수신기에서 HTTPS와 결합 된이 설정은 [종단 간 TLS](ssl-overview.md)를 지원 합니다. 이를 통해 백 엔드에 암호화 된 중요 한 데이터를 안전 하 게 전송할 수 있습니다. 종단 간 TLS를 사용 하는 백 엔드 풀의 각 백 엔드 서버는 보안 통신을 허용 하는 인증서를 사용 하 여 구성 해야 합니다.

## <a name="port"></a>포트

이 설정은 백 엔드 서버가 application gateway의 트래픽을 수신 대기 하는 포트를 지정 합니다. 1에서 65535 사이의 포트를 구성할 수 있습니다.

## <a name="request-timeout"></a>요청 시간 초과

이 설정은 application gateway가 백 엔드 서버에서 응답을 받기 위해 대기 하는 시간 (초)입니다.

## <a name="override-back-end-path"></a>백 엔드 경로 재정의

이 설정을 사용 하면 요청을 백 엔드에서 전달할 때 사용할 선택적 사용자 지정 전달 경로를 구성할 수 있습니다. **백 엔드 경로 재정의** 필드의 사용자 지정 경로와 일치 하는 들어오는 경로 부분은 전달 된 경로에 복사 됩니다. 다음 표에서는이 기능의 작동 방식을 보여 줍니다.

- HTTP 설정이 기본 요청 라우팅 규칙에 연결 된 경우:

  | 원래 요청  | 백 엔드 경로 재정의 | 백 엔드에 전달 된 요청 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | 설명은/override            | /override/home/              |
  | /home/secondhome/ | 설명은/override            | /override/home/secondhome/   |

- HTTP 설정이 경로 기반 요청 라우팅 규칙에 연결 된 경우:

  | 원래 요청           | 경로 규칙       | 백 엔드 경로 재정의 | 백 엔드에 전달 된 요청 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | 설명은/override            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | 설명은/override            | /override/home/secondhome/   |
  | /home/                     | pathrule      | 설명은/override            | /override/home/              |
  | /home/secondhome/          | pathrule      | 설명은/override            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | 설명은/override            | 설명은/override                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | 설명은/override            | /override/secondhome/        |
  | pathrule                 | pathrule      | 설명은/override            | 설명은/override                   |

## <a name="use-for-app-service"></a>App service에 사용

Azure App Service 백 엔드에 대 한 두 가지 필수 설정을 선택 하는 UI 전용 바로 가기입니다. **백 엔드 주소에서 호스트 이름을 선택**하 고 아직 없는 경우 새 사용자 지정 프로브를 만듭니다. 자세한 내용은이 문서의 [백 엔드 주소 설정에서 호스트 이름 선택](#pick-host-name-from-back-end-address)섹션을 참조 하세요. 새 프로브가 만들어지고 프로브 헤더는 백 엔드 구성원의 주소에서 선택 됩니다.

## <a name="use-custom-probe"></a>사용자 지정 프로브 사용

이 설정은 [사용자 지정 프로브](application-gateway-probe-overview.md#custom-health-probe) 를 HTTP 설정과 연결 합니다. HTTP 설정에는 사용자 지정 프로브를 하나만 연결할 수 있습니다. 사용자 지정 프로브를 명시적으로 연결 하지 않으면 [기본 프로브](application-gateway-probe-overview.md#default-health-probe-settings) 를 사용 하 여 백 엔드의 상태를 모니터링 합니다. 백 엔드의 상태 모니터링을 보다 효율적으로 제어 하기 위해 사용자 지정 프로브를 만드는 것이 좋습니다.

> [!NOTE]
> 해당 HTTP 설정이 수신기와 명시적으로 연결 되지 않은 경우 사용자 지정 프로브는 백 엔드 풀의 상태를 모니터링 하지 않습니다.

## <a name="pick-host-name-from-back-end-address"></a>백 엔드 주소에서 호스트 이름 선택

이 기능은 요청의 *호스트* 헤더를 백 엔드 풀의 호스트 이름으로 동적으로 설정 합니다. IP 주소 또는 FQDN을 사용 합니다.

이 기능을 사용 하면 백 엔드의 도메인 이름이 application gateway의 DNS 이름과 다를 수 있으며 백 엔드는 특정 호스트 헤더를 사용 하 여 올바른 끝점을 확인 합니다.

예를 들어, 다중 테 넌 트 서비스는 백 엔드입니다. App service는 단일 IP 주소를 사용 하 여 공유 공간을 사용 하는 다중 테 넌 트 서비스입니다. 따라서 앱 서비스는 사용자 지정 도메인 설정에서 구성 된 호스트 이름을 통해서만 액세스할 수 있습니다.

기본적으로 사용자 지정 도메인 이름은 *example.azurewebsites.net*입니다. App service에 명시적으로 등록 되지 않은 호스트 이름 또는 응용 프로그램 게이트웨이의 FQDN을 통해 응용 프로그램 게이트웨이를 사용 하 여 app service에 액세스 하려면 원래 요청에서 app service의 호스트 이름으로 호스트 이름을 재정의 합니다. 이렇게 하려면 **백 엔드 주소에서 호스트 이름 선택** 설정을 사용 하도록 설정 합니다.

기존 사용자 지정 DNS 이름을 app service에 매핑한 사용자 지정 도메인의 경우이 설정을 사용 하도록 설정할 필요가 없습니다.

> [!NOTE]
> 전용 배포 인 App Service Environment에는이 설정이 필요 하지 않습니다.

## <a name="host-name-override"></a>호스트 이름 재정의

이 기능은 응용 프로그램 게이트웨이에서 들어오는 요청의 *호스트* 헤더를 지정한 호스트 이름으로 바꿉니다.

예를 들어 **호스트 이름** 설정에 *www.contoso.com* 가 지정 된 경우 `https://appgw.eastus.cloudapp.azure.com/path1` `https://www.contoso.com/path1` 요청이 백 엔드 서버에 전달 되 면 원래 요청 *이 *로 변경 됩니다.

## <a name="next-steps"></a>다음 단계

- [백 엔드 풀에 대 한 자세한 정보](configuration-overview.md#back-end-pool)