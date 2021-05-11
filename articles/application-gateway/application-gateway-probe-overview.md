---
title: Azure Application Gateway에 대한 상태 모니터링 개요
description: Azure Application Gateway는 백 엔드 풀의 모든 리소스에 대한 상태를 모니터링하고, 풀에서 비정상으로 간주한 모든 리소스를 자동으로 제거합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: f0e5a153efe26640e54f386600f07c7b3d4711d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89649059"
---
# <a name="application-gateway-health-monitoring-overview"></a>Application Gateway 상태 모니터링 개요

Azure Application Gateway는 기본적으로 백 엔드 풀의 모든 리소스 상태를 모니터링하고 풀에서 비정상으로 간주한 모든 리소스를 자동으로 제거합니다. Application Gateway는 비정상 인스턴스를 계속 모니터링하며 사용할 수 있게 되고 상태 프로브에 응답하게 되면 정상 백 엔드 풀에 다시 추가합니다. 기본적으로 Application Gateway는 백 엔드 HTTP 설정에서 정의된 것과 동일한 포트를 사용하여 상태 프로브를 보냅니다. 사용자 지정 상태 프로브를 사용하여 사용자 지정 프로브 포트를 구성할 수 있습니다.

Application Gateway에서 상태 프로브에 사용하는 원본 IP 주소는 백 엔드 풀에 따라 다릅니다.
 
- 백 엔드 풀의 서버 주소가 퍼블릭 엔드포인트이면 원본 주소는 애플리케이션 게이트웨이의 프런트 엔드 공용 IP 주소입니다.
- 백 엔드 풀의 서버 주소가 프라이빗 엔드포인트이면 원본 IP 주소는 애플리케이션 게이트웨이 서브넷의 개인 IP 주소 공간에서 가져온 것입니다.

![Application Gateway 프로브 예제][1]

기본 상태 프로브 모니터링 사용 외에도 애플리케이션 프로그의 요구 사항에 맞게 상태 프로브를 사용자 지정할 수도 있습니다. 이 문서에서는 기본 및 사용자 지정 상태 프로브를 모두 설명합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>기본 상태 프로브

애플리케이션 게이트웨이는 사용자 지정 프로브 구성을 설정하지 않는 경우 기본 상태 프로브를 자동으로 구성합니다. 모니터링 동작은 백 엔드 풀에서 구성된 IP 주소 또는 FQDN에 대해 HTTP GET 요청을 수행하는 방식으로 작동합니다. 기본 프로브의 경우 백 엔드 HTTP 설정이 HTTPS로 구성되어 있으면 프로브는 HTTPS를 사용하여 백 엔드 서버의 상태를 테스트합니다.

다음은 그 예입니다.  포트 80에서 HTTP 네트워크 트래픽을 수신할 백 엔드 서버 A, B, C를 사용하도록 애플리케이션 게이트웨이를 구성합니다. 기본 상태 모니터링은 30초마다 서버 3대를 테스트하여 각 요청에 30초 시간 제한으로 정상 HTTP 응답을 확인합니다. 정상 HTTP 응답은 [상태 코드](https://msdn.microsoft.com/library/aa287675.aspx) 200에서 399 사이입니다. 예제에서 상태 프로브에 대한 HTTP GET 요청은 http://127.0.0.1/ 과 같이 표시됩니다.

서버 A에 대한 기본 프로브 검사가 실패하면 애플리케이션 게이트웨이는 이 서버에 더 이상 요청을 전달하지 않습니다. 기본 프로브는 서버 A에 대해 30초마다 계속 확인합니다. 서버 A가 기본 상태 프로브의 요청에 성공적으로 응답하면 애플리케이션 게이트웨이가 다시 서버에 요청을 전달하기 시작합니다.

### <a name="default-health-probe-settings"></a>기본 상태 프로브 설정

| 프로브 속성 | 값 | Description |
| --- | --- | --- |
| 프로브 URL |\<protocol\>://127.0.0.1:\<port\>/ |프로토콜과 포트는 프로브가 연결된 백 엔드 HTTP 설정에서 상속됩니다. |
| 간격 |30 |다음 상태 프로브가 전송되기 전에 대기할 시간의 양(초)입니다.|
| 시간 제한 |30 |프로브를 비정상으로 표시하기 전에 애플리케이션 게이트웨이에서 프로브 응답에 대해 대기할 시간의 양(초)입니다. 프로브가 정상으로 반환하는 경우 해당 백 엔드는 즉시 정상으로 표시됩니다.|
| 비정상 임계값 |3 |일반 상태 프로브에 오류가 발생하는 경우 보내는 프로브의 수를 제어합니다. v1 SKU에서는 추가 상태 프로브가 연속해서 빠르게 전송되어 백 엔드의 상태를 빠르게 확인하고 프로브 간격 동안 기다리지 않습니다. v2 SKU의 경우 상태 프로브에서 간격을 기다립니다. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 표시됩니다. |

기본 프로브는 \<protocol\>:\//127.0.0.1:\<port\>만 살펴보고 상태를 결정합니다. 사용자 지정 URL로 이동하거나 다른 모든 설정을 수정하도록 상태 프로브를 구성하려면 사용자 지정 프로브를 사용해야 합니다. HTTPS 프로브에 대한 자세한 내용은 [Application Gateway를 사용한 TLS 종료 및 엔드투엔드 TLS 개요](ssl-overview.md#for-probe-traffic)를 참조하세요.

### <a name="probe-intervals"></a>프로브 간격

Application Gateway의 모든 인스턴스는 서로 독립적으로 백 엔드를 검색합니다. 동일한 프로브 구성이 각 Application Gateway 인스턴스에 적용됩니다. 예를 들어 프로브 구성이 30초마다 상태 프로브를 보내는 것이고 애플리케이션 게이트웨이에 두 개의 인스턴스가 있는 경우 두 인스턴스 모두는 30초마다 상태 프로브를 보냅니다.

또한 여러 수신기가 있는 경우 각 수신기는 서로 독립적으로 백 엔드를 검색합니다. 예를 들어 두 개의 서로 다른 포트(두 개의 백 엔드 http 설정으로 구성됨)에서 동일한 백 엔드 풀을 가리키는 두 개의 수신기가 있는 경우 각 수신기는 동일한 백 엔드를 독립적으로 검색합니다. 이 경우 두 수신기에 대한 각 애플리케이션 게이트웨이 인스턴스에서 두 개의 프로브가 있습니다. 이 시나리오에서 애플리케이션 게이트웨이의 두 개의 인스턴스가 있는 경우 백 엔드 가상 머신에는 구성된 프로브 간격마다 4개의 프로브가 표시됩니다.

## <a name="custom-health-probe"></a>사용자 지정 상태 프로브

사용자 지정 프로브를 통해 상태 모니터링을 보다 세부적으로 제어할 수 있습니다. 사용자 지정 프로브를 사용하는 경우 사용자 지정 호스트 이름, URL 경로, 프로브 간격, 백 엔드 풀 인스턴스를 비정상으로 표시하기 전에 허용할 실패 응답 횟수를 구성할 수 있습니다.

### <a name="custom-health-probe-settings"></a>사용자 지정 상태 프로브 설정

다음 표에는 사용자 지정 상태 프로브의 속성을 위한 정의가 나와 있습니다.

| 프로브 속성 | Description |
| --- | --- |
| 속성 |프로브 이름입니다. 이 이름은 백 엔드 HTTP 설정에서 프로브를 식별하고 참조하는 데 사용됩니다. |
| 프로토콜 |프로브를 보내는 데 사용하는 프로토콜입니다. 연결된 백 엔드 HTTP 설정에서 정의된 프로토콜과 일치해야 합니다.|
| 호스트 |프로브를 보내는 데 사용할 호스트 이름입니다. v1 SKU에서 이 값은 프로브 요청의 호스트 헤더에만 사용됩니다. v2 SKU에서는 호스트 헤더와 SNI에 모두 사용됩니다. |
| 경로 |프로브의 상대 경로입니다. 올바른 경로는 '/'부터 시작합니다. |
| 포트 |정의된 경우 이 포트가 대상 포트로 사용됩니다. 그렇지 않으면 연결된 HTTP 설정과 동일한 포트가 사용됩니다. 이 속성은 v2 SKU에서만 사용할 수만 있습니다.
| 간격 |프로브 간격(초). 이 값은 연속된 두 프로브 사이의 시간 간격입니다. |
| 시간 제한 |프로브 시간 제한(초) 이 시간 제한 기간 내에 유효한 응답을 받지 못하면 프로브가 실패로 표시됩니다.  |
| 비정상 임계값 |프로브 재시도 횟수. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 다운된 것으로 표시됩니다. |

### <a name="probe-matching"></a>프로브 일치

기본적으로 상태 코드가 200-399인 HTTP(S) 응답은 정상으로 간주됩니다. 사용자 지정 상태 프로브는 또한 일치하는 두 조건을 지원합니다. 일치하는 조건을 사용하여 정상 응답을 만드는 항목에 대한 기본 해석을 필요에 따라 수정할 수 있습니다.

다음이 일치하는 조건입니다. 

- **HTTP 응답 상태 코드 일치** - 사용자 지정 http 응답 코드 또는 응답 코드 범위를 수용하기 위한 프로브 일치 조건입니다. 응답 상태 코드 또는 상태 코드 범위를 구분하는 개별 쉼표가 지원됩니다.
- **HTTP 응답 본문 일치** - HTTP 응답 본문을 살펴보고 사용자 지정 문자열과 일치하는 프로브 일치 조건입니다. 일치는 응답 본문에서 사용자 지정 문자열의 존재만 찾으며 전체 정규식과 일치하지는 않습니다.

`New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet을 사용하여 일치 조건을 지정할 수 있습니다.

다음은 그 예입니다.

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
일치 조건이 지정되면 PowerShell에서 `-Match` 매개 변수를 사용하여 프로브 구성에 연결할 수 있습니다.

## <a name="nsg-considerations"></a>NSG 고려 사항

들어오는 인터넷 트래픽을 Application Gateway v1 SKU의 경우 65503-65534 TCP 포트에서 허용하고, 대상 서브넷이 **Any** 이고 원본이 **GatewayManager** 서비스 태그인 v2 SKU의 경우 65200-65535 TCP 포트에서 허용해야 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다.

또한 아웃바운드 인터넷 연결은 차단할 수 없으며, **AzureLoadBalancer** 태그에서 들어오는 인바운드 트래픽은 허용해야 합니다.

자세한 내용은 [Application Gateway 구성 개요](configuration-infrastructure.md#network-security-groups)를 참조하세요.

## <a name="next-steps"></a>다음 단계
Application Gateway 상태 모니터링에 대해 알아본 후에 PowerShell 및 Azure Resource Manager 배포 모델을 사용하여 Azure Portal의 [사용자 지정 상태 프로브](application-gateway-create-probe-portal.md) 또는 [사용자 지정 상태 프로브](application-gateway-create-probe-ps.md)를 구성할 수 있습니다.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
