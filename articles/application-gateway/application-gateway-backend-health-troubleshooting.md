---
title: Azure 애플리케이션 게이트웨이의 백 엔드 상태 문제 해결
description: 이 문서에서는 Azure 애플리케이션 Gateway에 대 한 백 엔드 상태 문제를 해결 하는 방법을 안내 합니다.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: ce1d0542530c4f190ace52d45e2369d6ecc18772
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384087"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Application Gateway의 백 엔드 상태 문제 해결
==================================================

<a name="overview"></a>개요
--------

Application Gateway 기본적으로 백 엔드 서버를 검색 하 여 상태를 확인 하 고 요청을 처리할 준비가 되었는지 여부를 확인 합니다. 사용자는 사용자 지정 프로브를 만들어 호스트 이름, 검색할 경로 및 정상으로 허용할 상태 코드를 확인할 수 있습니다. 두 경우 모두 백 엔드 서버가 성공적으로 응답 하지 않으면 Application Gateway는 서버를 비정상으로 표시 하 고 요청을 서버로 전달 하는 것을 중지 합니다. 서버 응답이 성공적으로 시작 되 면 요청 처리를 다시 시작 합니다.

### <a name="how-to-check-backend-health"></a>백 엔드 상태를 확인 하는 방법

백 엔드 풀의 상태를 확인 하려면 Azure Portal에서 "백 엔드 상태" 페이지를 사용할 수 있습니다. 또는 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)또는 [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) 를 사용 하 여 상태를 가져올 수 있습니다. 자세한 정보를 보려면 각 방법에 대해 연결 된 문서를 확인 하세요.

위에서 언급 한 방법으로 검색 되는 상태는 다음과 같은 세 가지 유형 중 하나일 수 있습니다.

1.  정상

2.  비정상

3.  알 수 없음

서버에 대 한 백 엔드 상태가 정상 이면 Application Gateway 해당 서버로 요청을 전달 한다는 의미입니다. 그러나 백 엔드 풀의 모든 서버에 대 한 백 엔드 상태가 비정상 이거나 알려지지 않은 경우 응용 프로그램 액세스 중에 몇 가지 문제가 발생할 수 있습니다. 이 문서에서는 백 엔드 서버가 비정상 이거나 알 수 없을 때 표시 되는 각 오류에 대 한 증상, 원인 및 해결 방법을 설명 합니다.

<a name="backend-health-status-unhealthy"></a>백 엔드 상태 비정상
-------------------------------

백 엔드 상태가 비정상으로 표시 되 면 아래 스크린샷에 표시 된 것 처럼 포털에 표시 됩니다.

![Application Gateway 백 엔드 상태-비정상](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

또는 Azure PowerShell, CLI 또는 Azure REST API 쿼리를 사용 하는 경우 아래와 유사한 응답이 표시 됩니다.
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
백 엔드 풀의 모든 서버에 대해 백 엔드 서버 상태가 비정상으로 표시 되 면 요청이 전달 되지 않으며 Application Gateway는 요청 클라이언트에 502 잘못 된 게이트웨이 오류를 반환 합니다. 이 문제를 해결 하려면 백 엔드 상태 탭의 세부 정보 열을 확인 합니다.

백 엔드 상태 탭의 세부 정보 열에 표시 되는 메시지는 문제에 대 한 보다 자세한 정보를 제공 하 고이에 따라 문제 해결을 시작할 수 있습니다.

> [!NOTE]
> 기본 프로브 요청은 포트 80의 http 프로브 <protocol> <http://127.0.0.1/> 에 대해://<port>127.0.0.1:/형식으로 전송 되 고 http 상태 코드 200-399의 응답만 정상 응답으로 간주 합니다. 프로토콜 및 대상 포트는 HTTP 설정에서 상속 됩니다. 다른 프로토콜, 호스트 이름 또는 경로를 검색 하 고 다른 상태 코드를 정상으로 적용 하려면 사용자 지정 프로브를 구성 하 고 HTTP 설정과 연결 Application Gateway 합니다.

### <a name="error-messages"></a>오류 메시지

#### <a name="backend-server-timeout"></a>백 엔드 서버 시간 제한

**메시지:** 백 엔드에서 응용 프로그램 게이트웨이\'상태 프로브에 응답 하는 데 걸린 시간은 프로브 설정의 제한 시간 임계값 보다 작습니다.

**원인:** Application Gateway 백 엔드 서버에 대 한 HTTP (S) 프로브 요청을 보내면 백 엔드 서버의 응답이 구성 된 특정 시간 동안 대기 합니다. 백 엔드 서버가 시간 제한 값 내에 응답 하지 않는 경우 시간 제한 내에서 응답을 다시 시작할 때까지 비정상으로 표시 됩니다.

**해결 방법:** 구성 된 시간 제한 내에 응답 하지 않는 이유에 대해 백 엔드 서버 또는 응용 프로그램을 확인 하 고 응용 프로그램 종속성도 확인 합니다. 예를 들어 데이터베이스에 문제가 있는 경우에는 응답이 지연 될 수 있습니다. 응용 프로그램의 동작을 인식 하 고 제한 시간 값 이후에만 응답 해야 하는 경우에는 사용자 지정 프로브 설정에서 시간 제한 값을 늘립니다.
제한 시간 값을 변경 하려면 사용자 지정 프로브를 수행 해야 합니다. 사용자 지정 프로브를 구성 하는 방법을 알아보려면 [설명서 페이지](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)를 참조 하세요.

제한 시간을 늘리려면 다음 단계를 수행 합니다.

1.  백 엔드 서버에 직접 액세스 하 여 서버가 해당 페이지에서 응답 하는 데 걸린 시간을 확인 합니다. 개발자 도구를 사용 하 여 브라우저를 비롯 한 모든 도구를 사용 하 여에 액세스할 수 있습니다.

2.  응용 프로그램이 응답 하는 데 걸린 시간을 파악 했으면 상태 프로브 탭을 클릭 하 고 HTTP 설정에 연결 된 프로브를 선택 합니다.

3.  응용 프로그램 응답 시간 (초) 보다 더 높은 제한 시간 값을 입력 합니다.

4.  사용자 지정 프로브 설정을 저장 하 고 이제 정상으로 표시 되는 백 엔드 상태를 확인 합니다.

#### <a name="dns-resolution-error"></a>DNS 확인 오류

**메시지:** Application gateway에서이 백 엔드에 대 한 프로브를 만들지 못했습니다. 이는 일반적으로 백 엔드의 FQDN이 올바르게 입력 되지 않은 경우에 발생 합니다. 

**원인:** 백 엔드 풀이 IP 주소/FQDN 또는 App Service 유형인 경우 Application Gateway는 DNS 서버 (사용자 지정 또는 Azure 기본값)를 사용 하 여 입력 한 FQDN의 IP 주소로 확인 되 고 HTTP 설정에서 언급 된 TCP 포트의 서버에 연결을 시도 합니다. 그러나이 메시지가 표시 되 면 Application Gateway에서 입력 한 FQDN의 IP 주소를 성공적으로 확인할 수 없다는 것을 제안 합니다.

**해결 방법:**

1.  백 엔드 풀에 입력 한 FQDN이 올바른지 확인 하 고 공용 도메인 인지 확인 하 고 로컬 컴퓨터에서 확인 하십시오.

2.  IP 주소를 확인할 수 있으면 VNet의 DNS 구성에 문제가 있을 수 있습니다.

3.  VNet이 사용자 지정 DNS 서버를 사용 하 여 구성 되었는지 확인 합니다. 그렇다면 DNS 서버에서 지정 된 FQDN의 IP 주소를 확인할 수 없는 이유를 확인 합니다.

4.  Azure 기본 DNS 인 경우 적절 한 레코드 또는 CNAME 레코드 매핑이 완료 되 면 도메인 이름 등록 기관으로 확인 합니다.

5.  도메인이 전용/내부 도메인 인 경우 동일한 VNet의 VM에서 해당 도메인을 확인 하 고이를 해결할 수 있는 경우 Application Gateway 다시 시작 하 고 다시 확인 하세요. Application Gateway를 다시 시작 하려면 각 문서 링크에 제공 된 PowerShell 명령 사용을 [중지](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) 하 고 [시작](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) 해야 합니다.

#### <a name="tcp-connect-error"></a>TCP 연결 오류

**메시지:** Application gateway에서 백 엔드에 연결할 수 없습니다.
프로브에 사용 되는 포트에서 백 엔드가 응답 하는지 확인 하세요.
또한 NSG/UDR/방화벽이이 백 엔드의 Ip 및 포트에 대 한 액세스를 차단 하는지 확인 합니다.

**원인:** DNS 확인 단계 후에는 Application Gateway HTTP 설정에서 구성 된 TCP 포트의 백 엔드 서버에 연결을 시도 합니다. Application Gateway가 지정 된 포트에서 TCP 세션을 설정할 수 없는 경우 프로브는이 메시지와 함께 비정상으로 표시 됩니다.

**해결 방법:** 이 오류가 표시 되 면 다음을 확인 합니다.

1.  예를 들어 다음 명령을 사용 하 여 브라우저를 사용 하거나 PowerShell을 사용 하 여 HTTP 설정에 설명 된 포트를 사용 하 여 백 엔드 서버에 연결할 수 있는지 확인 합니다. 테스트 NetConnection-ComputerName www.bing.com-Port 443

2.  언급 된 포트가 원하는 포트가 아닌 경우 백 엔드 서버에 연결할 Application Gateway의 올바른 포트 번호를 입력 합니다.

3.  로컬 컴퓨터에서 포트에 연결할 수 없는 경우 다음을 수행 합니다.

    a.  백 엔드 서버의 NIC 및 서브넷에 대 한 NSG 설정을 확인 하 고 구성 된 포트에 대 한 인바운드 연결이 허용 되는지 확인 합니다. 허용 되지 않으면 연결을 허용 하는 새 규칙을 만듭니다. NSG 규칙을 만드는 방법에 대 한 자세한 내용은 [설명서 페이지](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)를 참조 하세요.

    b.  아웃 바운드 공용 및 개인 트래픽이 허용 되는 경우 Application Gateway의 서브넷에 대 한 NSG 설정을 확인 하 여 연결을 설정할 수 있습니다. (A)에 연결 된 문서를 확인 하 여 NSG 규칙을 만드는 방법에 대해 자세히 알아봅니다.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  라우팅 변칙에 대 한 Application Gateway 및 백 엔드 서버의 서브넷의 UDR 설정을 확인 합니다. UDR이 백 엔드 서브넷에서 트래픽을 멀리 이동 하지 않는지 확인 합니다. 예를 들어 Express 경로/v p n을 통해 응용 프로그램 게이트웨이 서브넷에 보급 되는 네트워크 가상 어플라이언스 또는 기본 경로에 대 한 경로를 확인 합니다.

    d.  NIC에 대 한 유효 경로 및 규칙을 확인 하려면 다음 PowerShell 명령을 사용할 수 있습니다.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  NSG 또는 UDR 관련 문제를 찾을 수 없는 경우 클라이언트에서 구성 된 포트에서 TCP 세션을 설정할 수 없기 때문에 응용 프로그램 관련 문제에 대 한 백 엔드 서버를 확인 합니다. 확인 해야 할 몇 가지 사항은 다음과 같습니다.

    e.  명령 프롬프트를 열고 (Win + R\> -cmd) netstat를 입력 하 고 enter 키를 누릅니다.

    f.  서버가 구성 된 포트에서 수신 대기 하 고 있는지 확인 합니다.
        예를 들어:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  그렇지 않은 경우 웹 서버 설정 (예: IIS의 사이트 바인딩, NGINX의 서버 블록 및 Apache의 가상 호스트)을 확인 합니다.

    h.  OS 방화벽 설정을 확인 하 여 포트에 들어오는 트래픽이 허용 되는지 확인 합니다.

#### <a name="http-status-code-mismatch"></a>HTTP 상태 코드 불일치

**메시지:** 백 엔드\'HTTP 응답의 상태 코드가 프로브 설정과 일치 하지 않습니다. 예상: {HTTPStatusCode0} Received: {HTTPStatusCode1}.

**원인:** TCP 연결이 설정 되 고 SSL 핸드셰이크가 완료 되 면 (SSL을 사용 하도록 설정 된 경우) Application Gateway는 프로브를 백 엔드 서버에 HTTP GET 요청으로 보냅니다. 위에서 설명한 것 처럼 \<기본 프로브는 protocol\>://127.0.0.1:\<port\>/로, r 200-399의 응답 상태 코드를 정상으로 간주 합니다. 서버에서 다른 상태 코드를 반환 하는 경우이 메시지는 비정상으로 표시 됩니다.

**해결 방법:** 백 엔드 서버의 응답 코드에 따라 다음 단계를 수행할 수 있습니다. 몇 가지 일반적인 상태 코드는 다음과 같습니다.

| **오류** | **actions** |
| --- | --- |
| 프로브 상태 코드 불일치: 수신 401 | 백 엔드 서버에 인증이 필요한 지 여부를 확인 합니다. Application gateway 프로브는이 시점에서 인증에 대 한 자격 증명을 전달할 수 없습니다. 프로브 상태 \"코드 일치\" 에서 HTTP 401을 허용 하거나 서버에서 인증을 요구 하지 않는 경로를 검색 합니다. | |
| 프로브 상태 코드 불일치: 수신 403 | 액세스가 금지 되었습니다. 백 엔드 서버에서 경로에 대 한 액세스가 허용 되는지 확인 합니다. | |
| 프로브 상태 코드 불일치: 수신 404 | 페이지를 찾을 수 없습니다. 백 엔드 서버에서 액세스할 수 있는 경우 호스트 이름 경로를 확인 합니다. Hostname 또는 path 매개 변수를 액세스할 수 있는 값으로 변경 합니다. | |
| 프로브 상태 코드 불일치: 수신 405 | Application Gateway의 프로브 요청은 HTTP GET 메서드를 사용 합니다. 서버에서 허용 하는지 여부를 확인 합니다. | |
| 프로브 상태 코드 불일치: 수신 500 | 내부 서버 오류. 백 엔드 서버의 상태를 확인 하 고 서비스가 실행 중인지 확인 합니다. | |
| 프로브 상태 코드 불일치: 수신 503 | 서비스를 사용할 수 없습니다. 백 엔드 서버의 상태를 확인 하 고 서비스가 실행 중인지 확인 합니다. | |

또는 응답이 legit 고 다른 상태 코드를 정상으로 허용 Application Gateway 하려는 경우 사용자 지정 프로브를 만들 수 있습니다. 이 수정 작업은 백 엔드 웹 사이트가 인증을 요구 하 고 프로브 요청이 사용자 자격 증명을 포함 하지 않기 때문에 실패 하 고 백 엔드 서버에서 HTTP 401 상태 코드가 반환 되는 경우에 유용 합니다.

사용자 지정 프로브를 만들려면 [여기](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)에 나열 된 단계를 따르세요.

#### <a name="http-response-body-mismatch"></a>HTTP 응답 본문 불일치

**메시지:** 백 엔드\'의 HTTP 응답 본문이 프로브 설정과 일치 하지 않습니다. 받은 응답 본문에 {string}이 (가) 없습니다.

**원인:** 사용자 지정 프로브를 만들 때 응답 본문의 문자열과 일치 하 여 백 엔드 서버를 정상적으로 표시 하는 옵션이 있습니다. 예를 들어, 일치 하는 문자열로 "권한 없음"을 허용 하도록 Application Gateway를 구성할 수 있습니다. 프로브 요청에 대 한 백 엔드 서버 응답이 "권한 없음" 문자열을 포함 하는 경우 정상으로 표시 됩니다.
그렇지 않으면이 메시지와 함께 비정상으로 표시 됩니다.

**해결 방법:** 아래 단계에 따라이 문제를 해결할 수 있습니다.

1.  백 엔드 서버에 로컬로 액세스 하거나 프로브 경로에 있는 클라이언트 컴퓨터에서 응답 본문을 확인 합니다.

2.  사용자 지정 프로브 구성 Application Gateway를 선택 하 여 응답 본문이 구성 된 것과 일치 하는지 확인 합니다.

3.  일치 하지 않는 경우 올바른 문자열 값으로 프로브 구성을 변경 하 여 허용 합니다.

[여기](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)에서 Application Gateway 프로브 일치에 대해 자세히 알아볼 수 있습니다.

#### <a name="backend-server-certificate-invalid-ca"></a>백 엔드 서버 인증서의 잘못 된 CA

**메시지:** 백 엔드에 사용 되는 서버 인증서는 잘 알려진 CA (인증 기관)에서 서명 되지 않습니다. 백 엔드에서 사용 되는 서버 인증서의 루트 인증서를 업로드 하 여 응용 프로그램 게이트웨이에서 백 엔드를 허용 목록 합니다.

**원인:** Application Gateway v 2를 사용 하는 종단 간 SSL을 사용 하려면 백 엔드 서버의 인증서를 확인 하 여 서버가 정상 상태 인지 확인 해야 합니다.
SSL 인증서를 신뢰할 수 있도록 하려면 Application Gateway의 신뢰할 수 있는 저장소에 포함 된 CA에서 백 엔드 서버의 인증서를 발급 해야 합니다. 인증서가 자체 서명 된 인증서와 같이 신뢰할 수 있는 CA에서 발급 되지 않은 경우 사용자는 발급자의 인증서를 Application Gateway에 업로드 해야 합니다.

**해결 방법:** 아래 단계에 따라 신뢰할 수 있는 루트 인증서를 Application Gateway으로 내보내고 업로드 합니다 (아래에 제공 된 단계는 Windows 클라이언트에 해당).

1.  응용 프로그램이 호스팅되는 컴퓨터에 로그인 합니다.

2.  Win + R을 눌러 실행을 열거나 시작 단추를 마우스 오른쪽 단추로 클릭 하 고 실행을 선택 합니다.

3.  Certmgr.exe를 입력 하 고 enter 키를 누릅니다. 시작 메뉴에서 인증서 관리자를 검색할 수도 있습니다.

4.  인증서-일반적으로 인증서- \'현재 사용자\\개인\\인증서\'에서 인증서를 찾고 인증서를 엽니다.

5.  인증서 속성에서 인증 경로 탭을 선택 합니다.

6.  루트 인증서를 선택 하 고 "인증서 보기" 옵션을 선택 합니다.

7.  인증서 속성에서 세부 정보 탭으로 전환 합니다.

8.  세부 정보 탭에서 "파일에 복사" 옵션을 선택 하 고 Base-64로 인코딩된 x.509 (. CER) 형식

9.  저장 되 면 Azure Portal에서 Application Gateway HTTP 설정 페이지를 엽니다.

10. HTTP 설정을 열고 "인증서 추가"를 클릭 하 고 최근에 저장 한 인증서 파일을 찾습니다.

11. 저장을 클릭 하 여 HTTP 설정을 저장 합니다.

또는 브라우저를 사용 하 여 Application Gateway 서버에 직접 액세스 하 고 브라우저에서 루트 인증서를 내보내 클라이언트 컴퓨터에서 루트 인증서를 내보낼 수 있습니다.

Application Gateway에서 신뢰할 수 있는 루트 인증서를 추출 및 업로드 하는 자세한 단계는 [여기](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)를 참조 하세요.

#### <a name="trusted-root-certificate-mismatch"></a>신뢰할 수 있는 루트 인증서 불일치

**메시지:** 백 엔드에서 사용 되는 서버 인증서의 루트 인증서가 응용 프로그램 게이트웨이에 추가 된 신뢰할 수 있는 루트 인증서와 일치 하지 않습니다. 백 엔드를 허용 목록 올바른 루트 인증서를 추가 해야 합니다.

**원인:** Application Gateway v 2를 사용 하는 종단 간 SSL을 사용 하려면 백 엔드 서버의 인증서를 확인 하 여 서버가 정상 상태 인지 확인 해야 합니다.
SSL 인증서를 신뢰할 수 있도록 하려면 Application Gateway의 신뢰할 수 있는 저장소에 포함 된 CA에서 백 엔드 서버의 인증서를 발급 해야 합니다. 인증서가 자체 서명 된 인증서와 같이 신뢰할 수 있는 CA에서 발급 되지 않은 경우 사용자는 발급자의 인증서를 Application Gateway에 업로드 해야 합니다.

Application Gateway HTTP 설정에 업로드 된 인증서는 백엔드 서버 인증서의 루트 인증서와 일치 해야 합니다.

**해결 방법:** 위에서 언급 한 오류 메시지가 표시 되 면 Application Gateway에 업로드 된 인증서와 백 엔드 서버에 업로드 된 인증서가 일치 하지 않음을 의미 합니다.

위에서 언급 한 1-11 단계를 수행 하 여 Application Gateway에 올바른 신뢰할 수 있는 루트 인증서를 업로드 합니다.

Application Gateway에서 신뢰할 수 있는 루트 인증서를 추출 및 업로드 하는 자세한 단계는 [여기](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)를 참조 하세요.
> [!NOTE]
> 백 엔드 서버가 루트 > 중간 (해당 하는 경우)을 포함 하 여 인증서의 전체 체인을 교환 하지 않는 경우 또는 TLS 핸드셰이크 중에 > 리프 인 경우에도 언급 된 오류가 발생할 수 있습니다. 확인 하려면 모든 클라이언트에서 OpenSSL 명령을 사용 하 고 Application Gateway 프로브에서 구성 된 설정을 사용 하 여 백 엔드 서버에 연결할 수 있습니다.

예를 들면 다음과 같습니다.
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
출력에 반환 되는 인증서의 전체 체인이 표시 되지 않으면 루트 인증서를 포함 하 여 전체 체인으로 인증서를 다시 내보내고 백 엔드 서버에서 해당 인증서를 구성 합니다. 

연결 됨 (00000188) \
depth = 0 OU = 도메인 컨트롤의 유효성을 검사 \*했습니다. CN =. example.com \
확인 오류: num = 20: 로컬 발급자 인증서를 가져올 수 없습니다. \
반환 확인: 1 \
depth = 0 OU = 도메인 컨트롤의 유효성을 검사 \*했습니다. CN =. example.com \
확인 오류: num = 21: 첫 번째 인증서를 확인할 수 없습니다. \
반환 확인: 1 \
\-\-\-\
인증서 체인 \
 0 s:/OU = 도메인 컨트롤의 유효성을 검사 한/CN = *. 예: .com \
   i:/C = US/ST = 애리조나/L = Scottsdale/O = godaddy, inc. =http://certs.godaddy.com/repository//CN=Go 아빠 보안 인증 기관-G2 \
\-----BEGIN CERTIFICATE-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\-----최종 인증서-----

#### <a name="backend-certificate-invalid-common-name-cn"></a>백 엔드 인증서의 잘못 된 CN (일반 이름)

**메시지:** 백 엔드 인증서의 CN (일반 이름)이 프로브의 호스트 헤더와 일치 하지 않습니다.

**원인:** Application Gateway 백 엔드 http 설정에 지정 된 호스트 이름이 백 엔드 서버의 SSL 인증서에 표시 된 CN (일반 이름)과 일치 하는지 여부를 확인 합니다. 이는 Standard_v2 및 WAF_v2 SKU 동작입니다. 표준 및 WAF SKU의 SNI는 백 엔드 풀 주소에서 FQDN으로 설정 됩니다.

V2 SKU에서 기본 프로브 (사용자 지정 프로브를 구성/연결 하지 않음)가 있는 경우 SNI는 HTTP 설정에 언급 된 호스트 이름에서 설정 되거나 "백 엔드 주소에서 호스트 이름 선택"은 백 엔드 주소 풀에 포함 된 HTTP 설정에 설명 되어 있습니다. 유효한 FQDN입니다.

HTTP 설정에 연결 된 사용자 지정 프로브가 있으면 사용자 지정 프로브 구성에 언급 된 호스트 이름에서 SNI가 설정 되거나 사용자 지정 프로브에서 "백 엔드 HTTP 설정에서 호스트 이름 선택"이 선택 된 경우 HTTP에 언급 된 호스트 이름에서 설정 됩니다. 설정.

HTTP 설정에서 "백 엔드 주소에서 호스트 이름 선택"이 설정 된 경우 백 엔드 주소 풀에 올바른 FQDN이 포함 되어야 합니다.

위에서 언급 한 오류 메시지가 표시 되는 경우 백 엔드 인증서의 CN (일반 이름)이 사용자 지정 프로브 또는 HTTP 설정에 구성 된 호스트 이름과 일치 하지 않음을 의미 합니다 ("백 엔드 HTTP 설정에서 호스트 이름 선택"이 선택 된 경우). 기본 프로브를 사용 하는 경우 호스트 이름이 "127.0.0.1"로 설정 됩니다. 원하는 값이 아닌 경우 사용자 지정 프로브를 만들어 HTTP 설정에 연결 해야 합니다.

**해결 방법:**

이 문제를 해결 하려면 다음 단계를 수행 합니다.

Windows의 경우:

1.  응용 프로그램이 호스팅되는 컴퓨터에 로그인 합니다.

2.  Win + R을 눌러 실행을 열거나 시작 단추를 마우스 오른쪽 단추로 클릭 하 고 실행을 선택 합니다.

3.  Certmgr.exe를 입력 하 고 enter 키를 누릅니다. 시작 메뉴에서 인증서 관리자를 검색할 수도 있습니다.

4.  인증서-일반적으로 인증서- \'현재 사용자\\개인\\인증서\'에서 인증서를 찾고 인증서를 엽니다.

5.  세부 정보 탭에서 인증서의 주체를 확인 합니다.

6.  세부 정보에서 인증서의 CN을 확인 하 고 사용자 지정 프로브 또는 HTTP 설정의 호스트 이름 필드에 동일한 이름을 입력 합니다 ("백 엔드 HTTP 설정에서 호스트 선택"이 선택 된 경우). 웹 사이트의 원하는 호스트 이름이 아닌 경우 해당 도메인에 대 한 인증서를 얻거나 사용자 지정 프로브/http 설정 구성에서 올바른 호스트 이름을 입력 해야 합니다.

OpenSSL를 사용 하는 Linux의 경우

1.  OpenSSL에서이 명령을 실행 합니다. 
```
openssl x509 -in certificate.crt -text -noout
```

2.  표시 된 속성에서 인증서의 CN을 찾고 http 설정의 호스트 이름 필드에 동일 하 게 입력 합니다. 웹 사이트의 원하는 호스트 이름이 아닌 경우 해당 도메인에 대 한 인증서를 얻거나 사용자 지정 프로브/http 설정 구성에서 올바른 호스트 이름을 입력 해야 합니다.

#### <a name="backend-certificate-is-invalid"></a>백 엔드 인증서가 잘못 되었습니다.

**메시지:** 백 엔드 인증서가 잘못 되었습니다. 현재 \"날짜는 인증서의\" 유효 기간 및 \"유효\" 기간 범위 내에 있지 않습니다.

**원인:** 서버의 SSL 인증서가 유효 하지 않으면 모든 인증서의 유효성을 검사 하 고 HTTPS 연결은 안전 하지 않습니다.
현재 데이터는 valid from 및 valid to 범위 내에 있어야 합니다. 그렇지 않으면 인증서가 잘못 된 것으로 간주 되 고 보안 문제가 됩니다. 이 시점에서 Application Gateway는 백엔드 서버를 비정상으로 표시 합니다.

**해결 방법:** SSL 인증서가 만료 된 경우 공급 업체에 인증서를 갱신 하 고 새 인증서를 사용 하 여 서버 설정을 업데이트 합니다. 자체 서명 된 인증서 인 경우 유효한 인증서를 생성 하 고 루트 인증서를 Application Gateway의 HTTP 설정에 업로드 해야 합니다. 이렇게 하려면 다음 단계를 수행 합니다.

1.  포털에서 Application Gateway HTTP 설정 열기

2.  인증서가 만료 된 HTTP 설정을 선택 하 고, 인증서 추가를 선택 하 고, 새 인증서 파일을 엽니다.

3.  인증서 옆의 삭제 아이콘을 사용 하 여 이전 인증서를 제거 하 고 저장을 클릭 합니다.

#### <a name="certificate-verification-failed"></a>인증서 확인 실패

**메시지:** 백 엔드 인증서의 유효성을 확인할 수 없습니다. 이유를 확인 하려면 오류 코드 {errorCode}와 연결 된 메시지에 대 한 SSL 진단 열기를 선택 합니다.

**원인:** 이 오류는 Application Gateway에서 인증서의 유효성을 확인할 수 없는 경우에 발생 합니다.

**해결 방법:** 이 문제를 해결 하려면 서버에서 인증서가 제대로 생성 되었는지 확인 합니다. 예를 들어 [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) 를 사용 하 여 인증서 및 해당 속성을 확인 하 고 인증서를 APPLICATION GATEWAY의 HTTP 설정으로 다시 업로드할 수 있습니다.

<a name="backend-health-status-unknown"></a>백 엔드 상태 알 수 없음
-------------------------------
백 엔드 상태가 알 수 없음으로 표시 되 면 아래 스크린샷에서 같이 포털에 표시 됩니다.

![Application Gateway 백 엔드 상태-알 수 없음](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

백 엔드 상태가 알 수 없음으로 표시 되는 경우 다음 이유 중 하나 이상이 원인일 수 있습니다.

1.  Application Gateway 서브넷의 NSG가 "Internet"에서 65503-65534 (v1 SKU) 또는 65200-65535 (v2 SKU) 포트에 대 한 인바운드 액세스를 차단 하 고 있습니다.
2.  기본 경로 (0.0.0.0/0)와 다음 홉이 인터넷이 아닌 Application Gateway 서브넷의 UDR
3.  BGP를 통해 VNet에 대 한 Express 경로/v p r 연결에서 보급 된 기본 경로
4.  공용 도메인 이름을 확인할 수 없는 VNet에 사용자 지정 DNS 서버가 구성 되어 있습니다.
5.  Application Gateway 비정상 상태입니다.

**해결 방법:**

1.  NSG가 "Internet"에서 65503-65534 (v1 SKU) 또는 65200-65535 (v2 SKU) 포트에 대 한 액세스를 차단 하는지 확인 합니다.

    a.  Application Gateway "개요" 탭에서 Virtual Network/서브넷 링크를 선택 합니다.

    b.  Virtual Network의 서브넷 탭에서 Application Gateway 배포 된 서브넷을 선택 합니다.

    c.  구성 된 NSG가 있는지 확인 합니다.

    d.  있는 경우 검색 탭에서 또는 모든 리소스 아래에서 해당 NSG 리소스를 검색 합니다.

    e.  인바운드 규칙 섹션에서 인바운드 규칙을 추가 하 여 v1 SKU에 대 한 대상 포트 범위 65503-65534을 허용 하는 인바운드 규칙을 추가 하거나, 원본 또는 인터넷을 사용 하는 65200-65535 v2 SKU

    f.  저장을 클릭 하 고 백 엔드를 정상적으로 볼 수 있는지 확인 합니다.

    g.  또는 [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) 를 통해 수행할 수 있습니다.

2.  UDR에 다음 홉이 인터넷이 아닌 기본 경로 (0.0.0.0/0)가 있는지 확인 합니다.
    
    a.  1\. a 및 1. b 단계를 수행 하 여 서브넷을 확인 합니다.

    b.  구성 된 UDR이 있는지 확인 합니다. 있는 경우 검색 표시줄에서 또는 모든 리소스에서 리소스를 검색 합니다.

    c.  기본 경로 (0.0.0.0/0)가 인터넷이 아닌 다음 홉을 사용 하는지 확인 합니다. 가상 어플라이언스 또는 Virtual Network 게이트웨이 인 경우 패킷을 수정 하지 않고 가상 어플라이언스 또는 온-프레미스 장치에서 패킷을 다시 인터넷 대상으로 다시 라우팅할 수 있는지 확인 해야 합니다.

    d.  그렇지 않으면 다음 홉을 인터넷으로 변경 하 고, 저장을 클릭 하 고, 백 엔드 상태를 확인 합니다.

3.  BGP를 통해 VNet에 대 한 Express 경로/v p r 연결에서 보급 된 기본 경로

    a.  BGP를 통해 VNet에 대 한 Express 경로/v p m 연결을 사용 하 고 기본 경로를 보급 하는 경우 패킷을 수정 하지 않고 인터넷 대상으로 다시 라우팅하도록 해야 합니다.

    b.  Application Gateway 포털에서 "연결 문제 해결" 옵션을 사용 하 여 확인할 수 있습니다.

    c.  "1.1.1.1"과 같은 인터넷 라우팅 가능한 IP 주소 및 대상 포트로 대상을 수동으로 선택 하 고 연결을 확인 합니다.

    d.  다음 홉이 게이트웨이 Virtual Network 경우 Express 경로 또는 VPN을 통해 보급 된 기본 경로가 있을 수 있습니다.

4.  VNet에 구성 된 사용자 지정 DNS 서버가 있으면 서버에서 공용 도메인을 확인할 수 있는지 확인 합니다. 공용 도메인 이름 확인은 Application Gateway에서 OCSP 서버와 같은 외부 도메인에 연결 하거나 인증서의 해지 상태 등을 확인 해야 하는 경우에 필요할 수 있습니다.

5.  Application Gateway 정상 상태이 고 실행 중인지 확인 하려면 포털에서 Resource Health 옵션으로 이동 하 여 "정상" 상태 인지 확인 합니다. 비정상 또는 저하 상태가 표시 되는 경우 [지원 담당자에 게 문의 하세요](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>다음 단계
----------

Application Gateway 진단 및 로깅에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)를 참조 하세요.
