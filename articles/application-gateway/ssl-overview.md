---
title: Azure Application Gateway에서 종단 간 SSL 사용
description: 이 문서는 Application Gateway 종단 간 SSL 지원에 대한 개요입니다.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 64b90afd598b96604fc9c3ddc4bc10586e714363
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279105"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Application Gateway를 사용 하는 SSL 종료 및 종단 간 SSL 개요

SSL (SSL(Secure Sockets Layer))은 웹 서버와 브라우저 간에 암호화 된 링크를 설정 하기 위한 표준 보안 기술입니다. 이 링크를 통해 웹 서버와 브라우저 간에 전달 되는 모든 데이터는 개인 및 암호화 된 상태로 유지 됩니다. Application gateway는 게이트웨이에서 SSL 종료 및 종단 간 SSL 암호화를 모두 지원 합니다.

## <a name="ssl-termination"></a>SSL 종료

Application Gateway는 게이트웨이에서 SSL 종료를 지원합니다. 그 후의 트래픽은 일반적으로 암호화되지 않은 상태로 백 엔드 서버로 흐릅니다. Application gateway에서 SSL 종료를 수행 하는 경우 다음과 같은 여러 가지 이점이 있습니다.

- **향상 된 성능** – SSL 암호 해독을 수행할 때 가장 큰 성능 저하는 초기 핸드셰이크입니다. 성능을 향상 시키기 위해 암호 해독을 수행 하는 서버는 SSL 세션 Id를 캐시 하 고 TLS 세션 티켓을 관리 합니다. 응용 프로그램 게이트웨이에서이 작업을 수행 하는 경우 동일한 클라이언트의 모든 요청은 캐시 된 값을 사용할 수 있습니다. 백 엔드 서버에서 작업이 수행 된 경우 클라이언트의 요청이 다른 서버로 이동할 때마다 클라이언트는 다시 인증 해야 합니다. TLS 티켓을 사용 하면이 문제를 완화할 수 있지만 모든 클라이언트에서 지원 되지 않으며 구성 및 관리가 어려울 수 있습니다.
- **백 엔드 서버의 활용도 향상** – SSL/TLS 처리는 CPU를 많이 사용 하며 키 크기가 증가 하면 더 많은 집약적입니다. 백 엔드 서버에서이 작업을 제거 하면 이러한 작업을 통해 콘텐츠를 전달 하는 가장 효율적인 항목에 집중할 수 있습니다.
- **지능형 라우팅** – 트래픽 암호를 해독 하면 응용 프로그램 게이트웨이에서 헤더, URI 등과 같은 요청 콘텐츠에 액세스할 수 있으며이 데이터를 사용 하 여 요청을 라우팅할 수 있습니다.
- **인증서 관리** – 인증서를 구매 하 고 모든 백 엔드 서버가 아닌 응용 프로그램 게이트웨이에만 설치 해야 합니다. 이렇게 하면 시간과 금액이 모두 절약 됩니다.

Ssl 종료를 구성 하려면 응용 프로그램 게이트웨이에서 SSL 프로토콜 사양에 따라 대칭 키를 파생할 수 있도록 수신기에 SSL 인증서를 추가 해야 합니다. 대칭 키는 게이트웨이로 전송 된 트래픽을 암호화 하 고 암호 해독 하는 데 사용 됩니다. SSL 인증서는 PFX (개인 정보 교환) 형식 이어야 합니다. 이 파일 형식을 사용하면 애플리케이션 게이트웨이에서 트래픽의 암호화 및 암호 해독을 수행하는 데 필요한 프라이빗 키를 내보낼 수 있습니다.

> [!NOTE] 
>
> Application gateway는 새 인증서를 만들거나 인증 기관에 인증서 요청을 보내는 기능을 제공 하지 않습니다.

SSL 연결이 작동 하려면 SSL 인증서가 다음 조건을 충족 하는지 확인 해야 합니다.

- 현재 날짜와 시간이 인증서의 "유효 기간" 및 "유효 기간" 날짜 범위 내에 있습니다.
- 인증서의 CN(일반 이름)이 요청의 호스트 헤더와 일치합니다. 예를 들어 클라이언트가 `https://www.contoso.com/`에 요청하는 경우 CN이 `www.contoso.com`이어야 합니다.

### <a name="certificates-supported-for-ssl-termination"></a>SSL 종료에 대해 지원 되는 인증서

Application gateway는 다음과 같은 유형의 인증서를 지원 합니다.

- CA (인증 기관) 인증서: CA 인증서는 CA (인증 기관)에서 발급 한 디지털 인증서입니다.
- EV (확장 유효성 검사) 인증서: EV 인증서는 업계 표준 인증서 지침입니다. 그러면 브라우저 로케이터 막대가 녹색으로 바뀌고 회사 이름도 게시 됩니다.
- 와일드 카드 인증서:이 인증서는 *. site.com을 기반으로 하는 여러 하위 도메인을 지원 합니다. 여기서 하위 도메인은 *를 대체 합니다. 그러나 site.com 지원 되지 않으므로 사용자가 "www"를 입력 하지 않고 웹 사이트에 액세스 하는 경우 와일드 카드 인증서에 포함 되지 않습니다.
- 자체 서명 된 인증서: 클라이언트 브라우저는 이러한 인증서를 신뢰 하지 않으며 사용자에 게 가상 서비스의 인증서가 신뢰 체인에 포함 되지 않았다는 경고를 표시 합니다. 자체 서명 된 인증서는 관리자가 클라이언트를 제어 하 고 브라우저의 보안 경고를 안전 하 게 무시할 수 있는 테스트 또는 환경에 적합 합니다. 프로덕션 워크 로드는 자체 서명 된 인증서를 사용해 서는 안 됩니다.

자세한 내용은 [application gateway를 사용 하 여 SSL 종료 구성](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)을 참조 하세요.

### <a name="size-of-the-certificate"></a>인증서 크기
[Application Gateway 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) 섹션을 확인 하 여 지원 되는 최대 SSL 인증서 크기를 확인 합니다.

## <a name="end-to-end-ssl-encryption"></a>종단 간 SSL 암호화

일부 고객은 백엔드 서버에 암호화 되지 않은 통신을 원합니다. 이는 보안 요구 사항, 규정 준수 요구 사항 때문이거나 애플리케이션이 보안 연결만 수락하는 것이기 때문일 수 있습니다. 이러한 애플리케이션을 위해 Application Gateway에서 종단 간 SSL 암호화를 지원합니다.

종단 간 SSL을 사용하면 Application Gateway에서 제공하는 계층 7 부하 분산 기능의 이점을 활용하면서 중요한 데이터를 암호화하여 백 엔드로 안전하게 전송할 수 있습니다. 이러한 기능 중 일부는 쿠키 기반 세션 선호도, URL 기반 라우팅, 사이트 기반 라우팅 지원 또는 X-Forwarded-* 헤더 삽입 기능입니다.

종단 간 SSL 통신 모드로 구성한 경우 Application Gateway가 게이트웨이에서 SSL 세션을 종료하고 사용자 트래픽을 암호 해독합니다. 그런 다음 구성된 규칙을 적용하여 트래픽을 라우팅할 적절한 백 엔드 풀 인스턴스를 선택합니다. 그런 다음, Application Gateway에서 백 엔드 서버에 대한 새로운 SSL 연결을 시작하고, 백 엔드 서버의 공개 키 인증서를 사용하여 데이터를 다시 암호화한 다음, 백 엔드에 해당 요청을 전송합니다. 웹 서버의 모든 응답은 동일한 프로세스를 거쳐 최종 사용자에게 돌아갑니다. [백 엔드 HTTP 설정](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) 에서 프로토콜 설정을 HTTPS로 설정 하 여 종단 간 SSL을 사용 하도록 설정 합니다. 그런 다음 백 엔드 풀에 적용 됩니다.

SSL 정책은 프런트 엔드 및 백 엔드 트래픽에 모두 적용 됩니다. 프런트 엔드에서 Application Gateway 서버 역할을 하 고 정책을 적용 합니다. 백 엔드에서 Application Gateway 클라이언트 역할을 하 고 SSL 핸드셰이크 중에 프로토콜/암호 정보를 기본 설정으로 보냅니다.

Application gateway는 application gateway를 사용 하 여 인증서를 허용 목록 하거나 인증서 CN이 HTTP의 호스트 이름과 일치 하는 잘 알려진 CA 기관에서 서명한 인증서를 포함 하는 백 엔드 인스턴스와만 통신 합니다. 백 엔드 설정. 여기에는 Azure 앱 service web apps 및 Azure API Management와 같은 신뢰할 수 있는 Azure 서비스가 포함 됩니다.

백 엔드 풀에 있는 구성원의 인증서가 잘 알려진 CA 기관에서 서명 되지 않은 경우에는 보안 통신을 허용 하도록 종단 간 SSL을 사용 하는 백 엔드 풀의 각 인스턴스가 인증서로 구성 되어야 합니다. 인증서를 추가하면 Application Gateway가 알려진 백 엔드 인스턴스하고만 통신하게 됩니다. 그러면 엔드투엔드 통신의 보안이 유지됩니다.

> [!NOTE] 
>
> Azure 앱 service web apps 및 Azure API Management와 같은 신뢰할 수 있는 Azure 서비스에는 인증 인증서 설정이 필요 하지 않습니다.

> [!NOTE] 
>
> 백 엔드 서버를 인증 하기 위한 **백 엔드 HTTP 설정** 에 추가 된 인증서는 응용 프로그램 게이트웨이에서 SSL 종료를 위해 **수신기** 에 추가 된 인증서와 동일 하거나 보안 강화를 위해 다른 인증서와 동일할 수 있습니다.

![종단 간 SSL 시나리오][1]

이 예제에서는 요청 TLS1.2를 사용하여 종단 간 SSL을 사용하여 백엔드 서버 Pool1에 라우팅됩니다.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>종단 간 SSL 및 인증서 허용 목록

Application Gateway는 알려진 백 엔드 인스턴스, 즉 Application Gateway을 통해 인증서가 허용 목록에 추가된 인스턴스와 유일하게 통신할 수 있습니다. 인증서 허용 목록을 사용하려면 백 엔드 서버 인증서의 공개 키를 애플리케이션 게이트웨이(루트 인증서가 아니라)에 업로드해야 합니다. 그러면 알려진 허용 목록의 백 엔드에 대한 연결만 허용됩니다. 나머지 백 엔드는 게이트웨이 오류를 초래합니다. 자체 서명된 인증서는 테스트 용도이며 프로덕션 워크로드에는 권장하지 않습니다. 마찬가지로 이러한 인증서를 사용하려면 위에서 설명했듯이 애플리케이션 게이트웨이를 통해 허용 목록에 추가해야 합니다.

> [!NOTE]
> Azure App Service와 같은 신뢰할 수 있는 Azure 서비스에는 인증 인증서 설정이 필요하지 않습니다.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>v2 SKU를 사용하는 종단 간 SSL

인증 인증서는 Application Gateway v2 SKU에서 더 이상 사용되지 않으며 신뢰할 수 있는 루트 인증서로 대체되었습니다. 이러한 인증서는 다음과 같은 몇 가지 주요 차이점을 제외하고 인증 인증서와 비슷합니다.

- 해당 CN이 HTTP 백 엔드 설정의 호스트 이름과 일치하는 잘 알려진 CA 기관에서 서명한 인증서의 경우 종단 간 SSL 작동을 위해 추가 단계가 필요하지 않습니다. 

   예를 들어, 백 엔드 인증서가 잘 알려진 CA에서 발급한 것이고 CN이 contoso.com이며, 백 엔드 http 설정의 호스트 필드도 contoso.com으로 설정된 경우 추가 단계가 필요하지 않습니다. 백 엔드 http 설정 프로토콜을 HTTPS로 설정할 수 있고, 상태 프로브 및 데이터 경로 둘 다 SSL을 사용하도록 설정할 수 있습니다. Azure App Service 또는 다른 Azure 웹 서비스를 백 엔드로 사용 하는 경우이를 암시적으로 신뢰할 수 있으며 종단 간 SSL에 추가 단계가 필요 하지 않습니다.
   
> [!NOTE] 
>
> SSL 인증서를 신뢰할 수 있도록 하려면 Application Gateway의 신뢰할 수 있는 저장소에 포함 된 CA에서 백 엔드 서버의 인증서를 발급 해야 합니다. 신뢰할 수 있는 CA에서 인증서를 발급 하지 않은 경우 Application Gateway에서 확인 됩니다. 발급 CA의 인증서가 신뢰할 수 있는 ca에서 발급 되었는지, 신뢰할 수 있는 ca를 찾을 때까지 (신뢰할 수 있는 보안 연결이 설정 된 경우) 또는 신뢰할 수 있는 CA를 찾을 수 없을 때까지 (Application Gateway에서 백 엔드를 표시 하는 경우)를 확인 합니다. 비정상). 따라서 백 엔드 서버 인증서에 루트 및 intermidiate Ca를 모두 포함 하는 것이 좋습니다.

- 인증서가 자체 서명되었거나 알 수 없는 매개체가 서명한 경우 v2 SKU에서 종단 간 SSL을 사용하도록 설정하려면 신뢰할 수 있는 루트 인증서를 정의해야 합니다. Application Gateway는 해당 서버 인증서의 루트 인증서가 풀과 연결된 백 엔드 http 설정의 신뢰할 수 있는 루트 인증서 목록 중 하나와 일치하는 백 엔드와만 통신합니다.

> [!NOTE] 
>
> 자체 서명 된 인증서는 인증서 체인의 일부 여야 합니다. 체인이 없는 단일 자체 서명 된 인증서는 V2 SKU에서 지원 되지 않습니다.

- 루트 인증서 일치 외에, Application Gateway는 백 엔드 http 설정에 지정된 호스트 설정이 백 엔드 서버의 SSL 인증서가 제공하는 CN(공통 이름)의 호스트 설정과 일치하는지도 확인합니다. 백 엔드에 대한 SSL 연결을 설정하려고 하면 Application Gateway는 SNI(서버 이름 표시) 확장을 백 엔드 http 설정에 지정된 호스트로 설정합니다.
- 백 엔드 http 설정의 호스트 필드 대신 **pick hostname from backend address**(백 엔드 주소에서 호스트 이름 선택)를 선택하면 SNI 헤더가 항상 백 엔드 풀 FQDN으로 설정되고, 백 엔드 서버 SSL 인증서의 CN은 해당 FQDN과 일치해야 합니다. 이 시나리오에서는 Ip를 사용 하는 백 엔드 풀 멤버가 지원 되지 않습니다.
- 루트 인증서는 백 엔드 서버 인증서의 base64 인코딩 루트 인증서입니다.

## <a name="next-steps"></a>다음 단계

종단 간 SSL에 대해 학습한 후에는 [PowerShell에서 Application Gateway를 사용하여 종단 간 SSL 구성](application-gateway-end-to-end-ssl-powershell.md)으로 이동하고 종단 간 SSL을 사용하는 Application Gateway를 만듭니다.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
