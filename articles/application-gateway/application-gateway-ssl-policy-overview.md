---
title: Azure 응용 프로그램 게이트웨이에 대한 TLS 정책 개요
description: Azure 응용 프로그램 게이트웨이에 대한 TLS 정책을 구성하고 백 엔드 서버 팜에서 암호화 및 암호 해독 오버헤드를 줄이는 방법을 알아봅니다.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 871cb930e867002d8af1e7755de27d4873327543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257379"
---
# <a name="application-gateway-tls-policy-overview"></a>애플리케이션 게이트웨이 TLS 정책 개요

Azure 응용 프로그램 게이트웨이를 사용하여 TLS/SSL 인증서 관리를 중앙 집중화하고 백 엔드 서버 팜에서 암호화 및 암호 해독 오버헤드를 줄일 수 있습니다. 또한 이 중앙 집중식 TLS 처리를 통해 조직 보안 요구 사항에 적합한 중앙 TLS 정책을 지정할 수도 있습니다. 그러면 보안 지침과 권장되는 정보 및 규정 준수 요구 사항을 충족할 수 있습니다.

TLS 정책에는 TLS 프로토콜 버전의 제어뿐만 아니라 암호 제품군 및 TLS 핸드셰이크 중에 암호가 사용되는 순서가 포함됩니다. 응용 프로그램 게이트웨이는 TLS 정책을 제어하기 위한 두 가지 메커니즘을 제공합니다. 미리 정의된 정책이나 사용자 지정 정책을 사용할 수 있습니다.

## <a name="predefined-tls-policy"></a>미리 정의된 TLS 정책

Application Gateway에는 세 가지 미리 정의된 보안 정책이 있습니다. 적절한 보안 수준을 얻을 수 있도록 이러한 정책을 사용하여 게이트웨이를 구성할 수 있습니다. 정책 이름은 구성된 연도 및 월로 주석 처리됩니다. 각 정책은 서로 다른 TLS 프로토콜 버전과 암호 제품군을 제공합니다. 최상의 TLS 보안을 보장하기 위해 최신 TLS 정책을 사용하는 것이 좋습니다.

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

요구 사항에 맞게 미리 정의된 TLS 정책을 구성해야 하는 경우 사용자 지정 TLS 정책을 정의해야 합니다. 사용자 지정 TLS 정책을 사용하면 지원할 최소 TLS 프로토콜 버전과 지원되는 암호 제품군 및 우선 순위 순서를 완벽하게 제어할 수 있습니다.
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL 프로토콜 버전

* SSL 2.0 및 3.0은 모든 Application Gateway에 대해 기본적으로 사용되지 않습니다. 이러한 프로토콜 버전을 구성할 수 없습니다.
* 사용자 지정 TLS 정책은 게이트웨이의 최소 TLS 프로토콜 버전인 TLSv1_0, TLSv1_1 및 TLSv1_2 다음 세 가지 프로토콜 중 하나를 선택할 수 있는 옵션을 제공합니다.
* TLS 정책이 정의되지 않으면 세 가지 프로토콜(TLSv1_0, TLSv1_1 및 TLSv1_2)이 모두 활성화됩니다.

### <a name="cipher-suites"></a>암호 그룹

Application Gateway는 사용자 지정 정책을 선택할 수 있는 다음과 같은 암호 그룹을 지원합니다. 암호 제품군의 순서는 TLS 협상 중에 우선 순위를 결정합니다.


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
> 연결에 사용되는 TLS 암호 제품군은 사용 중인 인증서의 유형에 따라 다다. 클라이언트에서 응용 프로그램 게이트웨이 연결에 사용되는 암호 제품군은 응용 프로그램 게이트웨이 수신기의 서버 인증서 유형을 기반으로 합니다. 백 엔드 풀 연결에 대한 응용 프로그램 게이트웨이에서 사용되는 암호 제품군은 백 엔드 풀 서버의 서버 인증서 유형을 기반으로 합니다.

## <a name="known-issue"></a>알려진 문제
응용 프로그램 게이트웨이 v2는 현재 다음 암호를 지원하지 않습니다.
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>다음 단계

TLS 정책을 구성하는 방법을 배우려면 [응용 프로그램 게이트웨이에서 TLS 정책 버전 및 암호 제품군 구성을](application-gateway-configure-ssl-policy-powershell.md)참조하십시오.
