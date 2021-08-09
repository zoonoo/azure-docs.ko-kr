---
title: Azure Application Gateway TLS 정책 개요
description: Azure Application Gateway TLS 정책을 구성하고 백 엔드 서버 팜에서 암호화 및 암호 해독 오버헤드를 줄이는 방법에 대해 알아봅니다.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: amsriva
ms.openlocfilehash: 77239cd8586b8fb07abf6862be436979541bdb99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97631693"
---
# <a name="application-gateway-tls-policy-overview"></a>Application Gateway TLS 정책 개요

Azure Application Gateway를 사용하여 TLS/SSL 인증서 관리를 중앙 집중화하고 백 엔드 서버 팜에서 암호화 및 암호 해독 오버헤드를 줄일 수 있습니다. 또한 중앙 집중식 TLS 처리를 통해 조직의 보안 요구 사항에 적합한 중앙 TLS 정책을 지정할 수 있습니다. 그러면 보안 지침과 권장되는 정보 및 규정 준수 요구 사항을 충족할 수 있습니다.

TLS 정책은 TLS 프로토콜 버전 및 TLS 핸드셰이크 도중 암호 그룹과 암호화 사용 순서를 제어합니다. Application Gateway는 TLS 정책을 제어하기 위한 두 가지 메커니즘을 제공합니다. 미리 정의된 정책이나 사용자 지정 정책을 사용할 수 있습니다.

## <a name="predefined-tls-policy"></a>미리 정의된 TLS 정책

Application Gateway에는 세 가지 미리 정의된 보안 정책이 있습니다. 적절한 보안 수준을 얻을 수 있도록 이러한 정책을 사용하여 게이트웨이를 구성할 수 있습니다. 정책 이름은 구성된 연도 및 월로 주석 처리됩니다. 각 정책은 다른 TLS 프로토콜 버전 및 암호 그룹을 제공합니다. TLS 보안을 극대화되도록 최신 TLS 정책을 사용하는 것이 좋습니다.

## <a name="known-issue"></a>알려진 문제
Application Gateway v2는 다음과 같은 DHE 암호화를 지원하지 않으며, DHE 암호화가 미리 정의된 정책에 언급된 경우에도 클라이언트와의 TLS 연결에는 사용되지 않습니다. DHE 암호화 대신, 안전하고 빨라진 ECDHE 암호화가 권장됩니다.

- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|속성  |값  |
|---|---|
|이름     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|기본값| True(미리 정의된 정책이 지정되지 않은 경우) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|속성  |값  |
|   ---      |  ---       |
|이름     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|기본값| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|속성  |값  |
|---|---|
|이름     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|기본값| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>사용자 지정 TLS 정책

요구 사항에 대해 미리 정의된 TLS 정책을 구성해야 하는 경우 고유의 사용자 지정 TLS 정책을 정의해야 합니다. 사용자 지정 TLS 정책을 통해, 지원되는 암호 그룹 및 해당 우선 순위 순서를 지원하려면 최소 TLS 프로토콜 버전을 완전히 제어해야 합니다.

> [!IMPORTANT]
> Application Gateway v1 SKU(Standard 또는 WAF)에서 사용자 지정 SSL 정책을 사용하는 경우, 필수 암호인 “TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256”을 목록에 추가해야 합니다. 이 암호는 Application Gateway v1 SKU에서 메트릭과 로깅을 사용하도록 설정하는 데 필요합니다.
> Application Gateway v2 SKU(Standard_v2 또는 WAF_v2)의 경우에는 필수가 아닙니다.
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL 프로토콜 버전

* SSL 2.0 및 3.0은 모든 Application Gateway에 대해 기본적으로 사용되지 않습니다. 이러한 프로토콜 버전을 구성할 수 없습니다.
* 사용자 지정 TLS 정책은 사용자에게 TLSv1_0, TLSv1_1 및 TLSv1_2라는 게이트웨이의 최소 TLS 프로토콜 버전으로 다음 세 가지 프로토콜 중 하나를 선택할 수 있는 옵션을 제공합니다.
* TLS 정책을 하나도 정의하지 않으면 3개 프로토콜(TLSv1_0, TLSv1_1, TLSv1_2) 모두 활성화됩니다.

### <a name="cipher-suites"></a>암호 그룹

Application Gateway는 사용자 지정 정책을 선택할 수 있는 다음과 같은 암호 그룹을 지원합니다. 암호 그룹의 순서는 TLS 협상 중 우선 순위 순서를 결정합니다.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> 연결에 사용되는 TLS 암호 그룹은 사용 중인 인증서의 형식에 따라 달라집니다. 클라이언트에서 애플리케이션 게이트웨이로 연결 시, 사용되는 암호 그룹은 애플리케이션 게이트웨이 수신기의 서버 인증서 유형에 따라 달라집니다. 애플리케이션 게이트웨이에서 백 엔드 풀로 연결 시, 사용되는 암호 그룹은 백 엔드 풀 서버의 서버 인증서 유형에 따라 달라집니다.

## <a name="next-steps"></a>다음 단계

TLS 정책을 구성하는 방법을 알아보려면 [Application Gateway에서 TLS 정책 버전 및 암호 그룹 구성](application-gateway-configure-ssl-policy-powershell.md)을 참조하세요.
