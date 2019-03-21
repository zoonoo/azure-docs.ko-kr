---
title: Azure 응용 프로그램 게이트웨이 작동
description: 이 문서에서는 응용 프로그램 게이트웨이 작동 하는 방법에 정보를 제공
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: ef07def377b74fb74d57372f471efcf48fcf7aa2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881098"
---
# <a name="how-application-gateway-works"></a>Application Gateway의 작동 원리

이 문서에서는 들어오는 요청 하 고 라우팅하는 백 엔드에 application gateway의 수락 하는 방법에 대해 설명 합니다.

![how-application-gateway-works](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-a-request-is-accepted"></a>요청을 수락 하는 방법

클라이언트 응용 프로그램 게이트웨이에 요청을 보내면 전에 도메인 이름 시스템 (DNS) 서버를 사용 하 여 application gateway의 도메인 이름을 확인 합니다. 응용 프로그램 게이트웨이에 azure.com 도메인에 있기 때문에 DNS 항목을 Azure에 의해 제어 됩니다. Azure DNS는 클라이언트에 IP 주소를 반환 합니다 *프런트 엔드 IP 주소* Application Gateway의 합니다. Application gateway에는 하나 이상의 들어오는 트래픽을 허용 *수신기*합니다. 수신기는 연결 요청을 확인 하는 논리 엔터티입니다. 프런트 IP 주소, 프로토콜 및 application gateway에 대 한 클라이언트에서 연결에 대 한 포트 번호를 사용 하 여 구성 됩니다. 웹 응용 프로그램 방화벽 (WAF)을 사용 하는 경우 Application Gateway 확인 요청 헤더 및 본문 (있는 경우)에 대해 합니다 *WAF 규칙* 되는지 확인 하려면 요청 유효한 요청 경우 라우팅됩니다에 백 엔드-또는 보안 위협을, 있는 경우 요청이 차단 됩니다.  

응용 프로그램 게이트웨이 내부 응용 프로그램 부하 분산 장치 또는 인터넷 연결 응용 프로그램 부하 분산 장치를 사용할 수 있습니다. 인터넷 응용 프로그램 게이트웨이 공용 IP 주소에 있습니다. 인터넷 응용 프로그램 게이트웨이의 DNS 이름을 해당 공용 IP 주소에 공개적으로 확인 가능한 경우 따라서 인터넷 응용 프로그램 게이트웨이 인터넷을 통해 클라이언트에서 요청을 라우팅할 수 있습니다. 내부 application gateway만의 개인 IP 주소를 갖습니다. 내부 응용 프로그램 게이트웨이의 DNS 이름을 해당 개인 IP 주소에 공개적으로 확인 가능한 경우 따라서 내부 부하 분산 장치는 application gateway에 대 한 VNET에 대 한 액세스를 사용 하 여 클라이언트의 요청을 라우팅할 수 있습니다. 인터넷 연결 및 내부 응용 프로그램 게이트웨이 개인 IP 주소를 사용 하 여 백 엔드 서버에 요청을 라우팅합니다. 따라서 백 엔드 서버에서 내부 또는 인터넷 연결 응용 프로그램 게이트웨이 요청을 수신 하는 공용 IP 주소 필요는 없습니다.

## <a name="how-a-request-is-routed"></a>요청을 라우팅하는 방법

유효 요청이 없으면 (또는 WAF를 사용 하지 않는 경우), *요청 라우팅 규칙* 연관를 *수신기* 결정 하기 위해 평가 되는 *백 엔드 풀* 를 요청 하는 라우팅할 수 있습니다. 규칙은 포털에 나열된 순서대로 처리됩니다. 기반으로 합니다 *요청 라우팅 규칙* 아니면 특정 백 엔드 풀에 수신기에서 모든 요청 경로 또는 URL 경로에 따라 다른 백 엔드 풀으로 라우팅할을 결정 하는 응용 프로그램 게이트웨이 구성 *요청을 리디렉션할* 다른 포트 또는 외부 사이트

한 번을 *백 엔드* *풀* 선택한 응용 프로그램 게이트웨이 백 엔드 서버는 정상 상태는 풀에 구성 된 (y.y.y.y) 중 하나에 요청을 보냅니다. 서버의 상태에 따른은 *상태 프로브*합니다. 백 엔드 풀에서 둘 이상의 서버를 포함 하는 경우 application gateway는 정상 서버 간에 요청을 라우팅할 로드를 분산 서버에 요청을 라운드 로빈 알고리즘을 사용 합니다.

응용 프로그램 게이트웨이 구성에 따라 백 엔드 서버를 사용 하 여 새 TCP 세션을 엽니다 백 엔드 서버를 확인 한 후 합니다 *http-settings*합니다. *http-settings* 프로토콜, 포트 및 다른 라우팅 관련를 지정 하는 구성 요소를 설정 하는 백 엔드 서버를 사용 하 여 새 세션을 설정 하는 데 필요 합니다. 포트 및 HTTP 설정에 사용 되는 프로토콜에 따라서 종단 간 SSL을 수행 하거나 암호화 되지 않은 응용 프로그램 게이트웨이와 백 엔드 서버 간의 트래픽을 암호화 여부를 결정 합니다. 백 엔드 서버에 원래 요청을 보내는 동안 응용 프로그램 게이트웨이 프로토콜입니다; 호스트 이름, 경로 재정의 관련 된 HTTP 설정에 대 한 모든 사용자 지정 구성 유지 관리 쿠키 기반 세션 선호도, 드레이닝 중, 연결 등을 백 엔드의 호스트 이름을 선택 합니다.

내부 Application Gateway에 개인 IP 주소에만 있습니다. 내부 응용 프로그램 게이트웨이의 DNS 이름을 내부적으로 해당 개인 IP 주소로 확인할 수 있습니다. 따라서 내부 부하 분산 장치 Application Gateway에 대 한 VNET에 대 한 액세스를 사용 하 여 클라이언트의 요청을 라우팅할 수 있습니다.

참고는 인터넷 연결 및 내부 응용 프로그램 게이트웨이가 라우팅할 백 엔드 풀 리소스에 개인 IP 주소, VM NIC 구성 또는 내부적으로 확인할 수 있는 주소를 포함 하는 경우 개인 IP 주소를 사용 하 여 백 엔드 서버에 요청에 백 엔드 풀은 공용 끝점, Application Gateway의 프런트 엔드 공용 IP를 사용 하 여 서버에 도달 하도록 합니다. 프런트 엔드 공용 IP 주소를 프로 비전 하지 않은 경우 아웃 바운드 외부 연결에 대 한 하나 할당 됩니다.

### <a name="modifications-to-the-request"></a>요청 수정

응용 프로그램 게이트웨이 백 엔드에 요청 전달 하기 전에 모든 요청에 4 개의 추가 헤더를 삽입 합니다. 이러한 헤더는 X-전달 기능에 대 한 사람, X 전달 proto, X 전달 된 포트 및 X 원래 호스트 합니다. X-전달 기능에 대 한 헤더 형식은 쉼표로 구분 된 목록을 ip: port입니다. X 전달 프로토콜에 대 한 유효한 값은 HTTP 또는 HTTPS입니다. X-forwarded-port는 Application Gateway에서 요청이 전송된 포트를 지정합니다. X 원래 호스트 헤더는 요청이 도착 한 원래 호스트 헤더를 포함 합니다. 이 헤더는 트래픽을 백 엔드로 라우팅하기 전에 들어오는 호스트 헤더가 수정되는 Azure Website 통합과 같은 시나리오에서 유용합니다. 필요에 따라 세션 선호도 사용 하는 경우 게이트웨이 관리 되는 선호도 쿠키를 삽입 됩니다. 

또한 사용 하 여 헤더를 수정 하려면 응용 프로그램 게이트웨이 구성할 수 있습니다 [재작성 HTTP 헤더](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) 또는 설정을 경로 재정의 사용 하 여 URI 경로 수정 합니다. 하지만 이렇게 하려면 구성, 들어오는 모든 요청은 백 엔드에는 프록시입니다.


## <a name="next-steps"></a>다음 단계

에 학습 한 후 응용 프로그램 게이트웨이 작동 방식에 대 한 참조 [응용 프로그램 게이트웨이 구성 요소](application-gateway-components.md) 해당 구성 요소 자세히 알아야 합니다.
