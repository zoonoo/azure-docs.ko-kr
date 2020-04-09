---
title: Azure 응용 프로그램 게이트웨이에서 백 엔드 상태 문제 해결
description: Azure 응용 프로그램 게이트웨이의 백 엔드 상태 문제를 해결하는 방법에 대해 설명합니다.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: a16120194b1b8015466005f42336828c2b4ace6c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983843"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>응용 프로그램 게이트웨이에서 백 엔드 상태 문제 해결
==================================================

<a name="overview"></a>개요
--------

기본적으로 Azure Application Gateway는 백 엔드 서버를 검색하여 해당 서버의 상태를 확인하고 요청을 제공할 준비가 되었는지 확인합니다. 또한 사용자는 호스트 이름, 조사할 경로 및 정상으로 수락할 상태 코드를 언급하는 사용자 지정 프로브를 만들 수도 있습니다. 각각의 경우 백 엔드 서버가 성공적으로 응답하지 않으면 응용 프로그램 게이트웨이는 서버를 비정상으로 표시하고 서버에 대한 요청을 전달하지 않습니다. 서버가 성공적으로 응답을 시작하면 응용 프로그램 게이트웨이가 요청을 전달다시 시작합니다.

### <a name="how-to-check-backend-health"></a>백 엔드 상태를 확인하는 방법

백 엔드 풀의 상태를 확인하려면 Azure 포털의 **백 엔드 상태** 페이지를 사용할 수 있습니다. 또는 [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)또는 [REST API를](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)사용할 수 있습니다.

이러한 메서드에서 검색한 상태는 다음 중 하나일 수 있습니다.

- Healthy

- 비정상

- 알 수 없음

서버의 백 엔드 상태 상태가 정상인 경우 응용 프로그램 게이트웨이가 해당 서버로 요청을 전달한다는 의미입니다. 그러나 백 엔드 풀의 모든 서버의 백 엔드 상태(정상 없음 또는 알 수 없음)인 경우 응용 프로그램에 액세스하려고 할 때 문제가 발생할 수 있습니다. 이 문서에서는 표시된 각 오류에 대한 증상, 원인 및 해결 에 대해 설명합니다.

<a name="backend-health-status-unhealthy"></a>백 엔드 상태: 비정상
-------------------------------

백 엔드 상태가 비정상이면 포털 보기는 다음 스크린샷과 유사합니다.

![응용 프로그램 게이트웨이 백 엔드 상태 - 비정상](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

또는 Azure PowerShell, CLI 또는 Azure REST API 쿼리를 사용하는 경우 다음과 유사한 응답을 받게 됩니다.
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
백 엔드 풀의 모든 서버에 대해 비정상 백 엔드 서버 상태가 발생하면 요청이 서버로 전달되지 않고 응용 프로그램 게이트웨이가 요청 클라이언트에 "502 잘못된 게이트웨이" 오류를 반환합니다. 이 문제를 해결하려면 **백 엔드 상태** 탭의 세부 **정보** 열을 확인하십시오.

**세부 정보** 열에 표시된 메시지는 문제에 대한 자세한 정보를 제공하며 이를 기반으로 문제 해결을 시작할 수 있습니다.

> [!NOTE]
> 기본 프로브 요청은 \<프로토콜\>형식으로 전송됩니다 .127.0.0.1:\<포트\>/. 예를 들어 http://127.0.0.1:80 포트 80의 http 프로브의 경우 200에서 399까지의 HTTP 상태 코드만 정상으로 간주됩니다. 프로토콜 및 대상 포트는 HTTP 설정에서 상속됩니다. 응용 프로그램 게이트웨이가 다른 프로토콜, 호스트 이름 또는 경로를 검색하고 다른 상태 코드를 정상으로 인식하려면 사용자 지정 프로브를 구성하고 HTTP 설정과 연결합니다.

<a name="error-messages"></a>오류 메시지
------------------------
#### <a name="backend-server-timeout"></a>백엔드 서버 시간 시간

**메시지:** 응용 프로그램 게이트웨이의\'상태 프로브에 응답하기 위해 백 엔드에서 소요되는 시간은 프로브 설정의 시간 초과 임계값이상입니다.

**원인:** 응용 프로그램 게이트웨이가 백 엔드 서버에 HTTP(S) 프로브 요청을 보낸 후 구성된 기간 동안 백 엔드 서버의 응답을 기다립니다. 백 엔드 서버가 구성된 기간(시간 지정 값) 내에 응답하지 않으면 구성된 시간 지정 기간 내에 다시 응답하기 시작할 때까지 비정상으로 표시됩니다.

**해결 방법:** 구성된 시간 지정 기간 내에 백 엔드 서버 또는 응용 프로그램이 응답하지 않는 이유를 확인하고 응용 프로그램 종속성을 확인합니다. 예를 들어 데이터베이스에 응답 지연을 유발할 수 있는 문제가 있는지 확인합니다. 응용 프로그램의 동작을 알고 있고 시간 시간 값 이후에만 응답해야 하는 경우 사용자 지정 프로브 설정에서 시간 시간 지정 값을 늘립니다. 시간 시간 값을 변경하려면 사용자 지정 프로브가 있어야 합니다. 사용자 지정 프로브를 구성하는 방법에 대한 자세한 내용은 [설명서 페이지를 참조하십시오.](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)

시간 시간 값을 높이려면 다음 단계를 따르십시오.

1.  백 엔드 서버에 직접 액세스하고 서버가 해당 페이지에서 응답하는 데 걸린 시간을 확인합니다. 개발자 도구를 사용하는 브라우저를 포함하여 모든 도구를 사용하여 백 엔드 서버에 액세스할 수 있습니다.

1.  응용 프로그램이 응답하는 데 걸린 시간을 파악한 후 **상태 프로브 탭을** 선택한 다음 HTTP 설정과 연결된 프로브를 선택합니다.

1.  응용 프로그램 응답 시간보다 큰 시간 초과 값을 초 단위로 입력합니다.

1.  사용자 지정 프로브 설정을 저장하고 백 엔드 상태표시가 현재 정상으로 표시되는지 확인합니다.

#### <a name="dns-resolution-error"></a>DNS 해결 오류

**메시지:** 응용 프로그램 게이트웨이는 이 백 엔드에 대한 프로브를 만들 수 없습니다. 이 오류는 일반적으로 백 엔드의 FQDN이 올바르게 입력되지 않은 경우에 발생합니다. 

**원인:** 백 엔드 풀이 유형 IP 주소/FQDN 또는 앱 서비스인 경우 응용 프로그램 게이트웨이는 도메인 이름 시스템(DNS) (사용자 지정 또는 Azure 기본값)을 통해 입력된 FQDN의 IP 주소로 확인되고 HTTP 설정에 언급된 TCP 포트의 서버에 연결하려고 시도합니다. 그러나 이 메시지가 표시되면 응용 프로그램 게이트웨이가 입력한 FQDN의 IP 주소를 성공적으로 확인할 수 없다는 것을 알 수 있습니다.

**해결 방법:**

1.  백 엔드 풀에 입력된 FQDN이 올바른지, 공용 도메인인지 확인한 다음 로컬 컴퓨터에서 해결해 봅슬렌 합니다.

1.  IP 주소를 확인할 수 있는 경우 가상 네트워크의 DNS 구성에 문제가 있을 수 있습니다.

1.  가상 네트워크가 사용자 지정 DNS 서버로 구성되었는지 확인합니다. 이 경우 지정된 FQDN의 IP 주소로 확인할 수 없는 이유에 대해 DNS 서버를 확인합니다.

1.  Azure 기본 DNS를 사용하는 경우 도메인 이름 등록기관에 적절한 A 레코드 또는 CNAME 레코드 매핑이 완료되었는지 확인합니다.

1.  도메인이 개인 이거나 내부 인 경우 동일한 가상 네트워크의 VM에서 도메인을 해결 해 보십시오. 이 문제를 해결할 수 있는 경우 응용 프로그램 게이트웨이를 다시 시작하고 다시 확인합니다. 응용 프로그램 게이트웨이를 다시 시작하려면 이러한 연결된 리소스에 설명된 PowerShell 명령을 사용하여 [중지하고](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) [시작해야](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) 합니다.

#### <a name="tcp-connect-error"></a>TCP 연결 오류

**메시지:** 응용 프로그램 게이트웨이가 백 엔드에 연결할 수 없습니다.
프로브에 사용된 포트에서 백 엔드가 응답하는지 확인하십시오.
또한 NSG/UDR/방화벽이 이 백 엔드의 IP 및 포트에 대한 액세스를 차단하고 있는지 확인합니다.

**원인:** DNS 확인 단계 후 응용 프로그램 게이트웨이는 HTTP 설정에서 구성된 TCP 포트의 백 엔드 서버에 연결하려고 시도합니다. 응용 프로그램 게이트웨이가 지정된 포트에서 TCP 세션을 설정할 수 없는 경우 프로브는 이 메시지와 함께 비정상으로 표시됩니다.

**솔루션:** 이 오류가 발생하면 다음 단계를 따르십시오.

1.  브라우저 또는 PowerShell을 사용하여 HTTP 설정에 언급된 포트의 백 엔드 서버에 연결할 수 있는지 확인합니다. 예를 들어 다음 명령을 실행합니다.`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  언급된 포트가 원하는 포트가 아닌 경우 응용 프로그램 게이트웨이가 백 엔드 서버에 연결할 올바른 포트 번호를 입력합니다.

1.  로컬 컴퓨터에서도 포트에 연결할 수 없는 경우 다음을 수행합니다.

    a.  백 엔드 서버의 네트워크 어댑터 및 서브넷의 NSG(네트워크 보안 그룹) 설정을 확인하고 구성된 포트에 대한 인바운드 연결이 허용되는지 확인합니다. 그렇지 않은 경우 연결을 허용하는 새 규칙을 만듭니다. NSG 규칙을 만드는 방법에 대해 알아보려면 [설명서 페이지를 참조하십시오.](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)

    b.  응용 프로그램 게이트웨이 서브넷의 NSG 설정에서 아웃바운드 공용 및 개인 트래픽을 허용하는지 확인하여 연결을 만들 수 있습니다. NSG 규칙을 만드는 방법에 대해 자세히 알아보려면 3a 단계에서 제공되는 문서 페이지를 확인하십시오.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    다.  응용 프로그램 게이트웨이의 UDR(사용자 정의 경로) 설정과 백 엔드 서버의 서브넷에서 라우팅 이상 사항이 있는지 확인합니다. UDR이 백 엔드 서브넷에서 트래픽을 멀리 향하지 않는지 확인합니다. 예를 들어 Azure ExpressRoute 및/또는 VPN을 통해 응용 프로그램 게이트웨이 서브넷에 보급되는 네트워크 가상 어플라이언스 또는 기본 경로에 대한 경로를 확인합니다.

    d.  네트워크 어댑터의 유효 경로 및 규칙을 확인하려면 다음 PowerShell 명령을 사용할 수 있습니다.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  NSG 또는 UDR에 문제가 없는 경우 클라이언트가 구성된 포트에서 TCP 세션을 설정하지 못하도록 하는 응용 프로그램 관련 문제가 있는지 백 엔드 서버에서 확인합니다. 확인해야 할 몇 가지 사항은 다음과 같은 것입니다.

    a.  명령 프롬프트(Win+R\> - cmd)를 열고 입력을 `netstat`선택하고 Enter를 선택합니다.

    b.  서버가 구성된 포트에서 대기 중인지 확인합니다. 예를 들어:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    다.  구성된 포트에서 수신 대기 중이 아닌 경우 웹 서버 설정을 확인합니다. 예를 들어 IIS의 사이트 바인딩, NGINX의 서버 블록 및 아파치의 가상 호스트.

    d.  OS 방화벽 설정을 확인하여 포트로 들어오는 트래픽이 허용되는지 확인합니다.

#### <a name="http-status-code-mismatch"></a>HTTP 상태 코드 불일치

**메시지:** 백 엔드\'의 HTTP 응답의 상태 코드가 프로브 설정과 일치하지 않았습니다. 예상:{HTTP상태 코드0} 수신:{HTTPStatusCode1}.

**원인:** TCP 연결이 설정되고 TLS 핸드셰이크가 수행된 후(TLS가 활성화된 경우) 응용 프로그램 게이트웨이는 프로브를 HTTP GET 요청으로 백 엔드 서버로 보냅니다. 앞에서 설명한 바와 같이, \<기본\>프로브는 프로토콜 ://127.0.0.1:\<포트/이\>될 것이며, 분노 200에서 399까지의 응답 상태 코드를 정상으로 고려한다. 서버가 다른 상태 코드를 반환하면 이 메시지와 함께 비정상으로 표시됩니다.

**솔루션:** 백 엔드 서버의 응답 코드에 따라 다음 단계를 수행할 수 있습니다. 몇 가지 공통 상태 코드가 여기에 나열되어 있습니다.

| **오류** | **작업** |
| --- | --- |
| 프로브 상태 코드 불일치: 수신 된 401 | 백 엔드 서버에 인증이 필요한지 확인합니다. 응용 프로그램 게이트웨이 프로브는 이 시점에서 인증을 위한 자격 증명을 전달할 수 없습니다. 프로브 \"상태 코드\" 일치에서 HTTP 401을 허용하거나 서버가 인증이 필요하지 않은 경로로 프로브합니다. | |
| 프로브 상태 코드 불일치: 수신 된 403 | 액세스가 금지되었습니다. 백 엔드 서버에서 경로에 대한 액세스가 허용되는지 확인합니다. | |
| 프로브 상태 코드 불일치: 수신 된 404 | 페이지를 찾을 수 없습니다. 백 엔드 서버에서 호스트 이름 경로에 액세스할 수 있는지 확인합니다. 호스트 이름 또는 경로 매개 변수를 액세스 가능한 값으로 변경합니다. | |
| 프로브 상태 코드 불일치: 수신 된 405 | 응용 프로그램 게이트웨이에 대한 프로브 요청은 HTTP GET 메서드를 사용합니다. 서버에서 이 메서드를 허용하는지 확인합니다. | |
| 프로브 상태 코드 불일치: 수신 500 | 내부 서버 오류. 백 엔드 서버의 상태와 서비스가 실행 중인지 여부를 확인합니다. | |
| 프로브 상태 코드 불일치: 수신 503 | 서비스를 사용할 수 없습니다. 백 엔드 서버의 상태와 서비스가 실행 중인지 여부를 확인합니다. | |

또는 응답이 합법적이라고 생각하고 응용 프로그램 게이트웨이에서 다른 상태 코드를 정상으로 수락하려면 사용자 지정 프로브를 만들 수 있습니다. 이 방법은 백 엔드 웹 사이트에 인증이 필요한 경우에 유용합니다. 프로브 요청은 사용자 자격 증명을 수행하지 않으므로 실패하고 HTTP 401 상태 코드가 백 엔드 서버에서 반환됩니다.

사용자 지정 프로브를 만들려면 [다음 단계를](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)따르십시오.

#### <a name="http-response-body-mismatch"></a>HTTP 응답 본문 불일치

**메시지:** 백 엔드의\'HTTP 응답의 본체가 프로브 설정과 일치하지 않았습니다. 수신된 응답 본문에 {문자열}이 포함되어 있지 않습니다.

**원인:** 사용자 지정 프로브를 만들 때 응답 본문에서 문자열을 일치시켜 백 엔드 서버를 정상으로 표시하는 옵션이 있습니다. 예를 들어 일치하는 문자열로 "권한이 없는"을 허용하도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 프로브 요청에 대한 백 엔드 서버 응답에 **권한이 없는**문자열이 포함된 경우 정상으로 표시됩니다. 그렇지 않으면 이 메시지와 함께 비정상으로 표시됩니다.

**솔루션:** 이 문제를 해결하려면 다음 단계를 따르십시오.

1.  로컬 또는 프로브 경로의 클라이언트 컴퓨터에서 백 엔드 서버에 액세스하고 응답 본문을 확인합니다.

1.  응용 프로그램 게이트웨이 사용자 지정 프로브 구성의 응답 본문이 구성된 내용과 일치하는지 확인합니다.

1.  일치하지 않는 경우 허용할 올바른 문자열 값이 되도록 프로브 구성을 변경합니다.

[응용 프로그램 게이트웨이 프로브 일치에](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)대해 자세히 알아봅니다.

#### <a name="backend-server-certificate-invalid-ca"></a>백 엔드 서버 인증서가 잘못된 CA

**메시지:** 백 엔드에서 사용하는 서버 인증서는 잘 알려진 인증 기관(CA)에 의해 서명되지 않습니다. 백 엔드에서 사용하는 서버 인증서의 루트 인증서를 업로드하여 응용 프로그램 게이트웨이에서 백 엔드를 화이트리스트로 나열합니다.

**원인:** 응용 프로그램 게이트웨이 v2를 사용 하 여 종단 간 SSL 서버 정상으로 간주 하기 위해 백 엔드 서버의 인증서를 확인 해야 합니다.
TLS/SSL 인증서를 신뢰할 수 있도록 하려면 응용 프로그램 게이트웨이의 신뢰할 수 있는 저장소에 포함된 CA에서 백 엔드 서버의 인증서를 발급해야 합니다. 신뢰할 수 있는 CA에서 인증서를 발급하지 않은 경우(예: 자체 서명된 인증서가 사용된 경우) 사용자는 발급자의 인증서를 응용 프로그램 게이트웨이에 업로드해야 합니다.

**솔루션:** 다음 단계에 따라 신뢰할 수 있는 루트 인증서를 내보내고 응용 프로그램 게이트웨이에 업로드합니다. 이러한 단계는 Windows 클라이언트용입니다.

1.  응용 프로그램이 호스팅되는 컴퓨터에 로그인합니다.

1.  승리 +R을 선택하거나 **시작** 버튼을 마우스 오른쪽 버튼으로 클릭한 다음 **실행을**선택합니다.

1.  Enter `certmgr.msc` 및 Enter를 선택합니다. **시작** 메뉴에서 인증서 관리자를 검색할 수도 있습니다.

1.  일반적으로 에서 `\Certificates - Current User\\Personal\\Certificates\`인증서를 찾아 엽니다.

1.  루트 인증서를 선택한 다음 **인증서 보기를**선택합니다.

1.  인증서 속성에서 세부 **정보** 탭을 선택합니다.

1.  세부 **정보** 탭에서 **파일 복사** 옵션을 선택하고 파일을 기본 64 인코딩된 X.509() CER) 형식.

1.  Azure 포털에서 응용 프로그램 게이트웨이 HTTP **설정** 페이지를 엽니다.

1. HTTP 설정을 열고 **인증서 추가를**선택하고 방금 저장한 인증서 파일을 찾습니다.

1. HTTP 설정을 저장하려면 **저장을** 선택합니다.

또는 브라우저를 통해 서버에 직접 액세스(응용 프로그램 게이트웨이 우회)에 액세스하고 브라우저에서 루트 인증서를 내보내서 클라이언트 컴퓨터에서 루트 인증서를 내보낼 수 있습니다.

응용 프로그램 게이트웨이에서 신뢰할 수 있는 루트 인증서를 추출하고 업로드하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 루트 인증서 내보내기(v2 SKU용)를](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)참조하십시오.

#### <a name="trusted-root-certificate-mismatch"></a>신뢰할 수 있는 루트 인증서 불일치

**메시지:** 백 엔드에서 사용하는 서버 인증서의 루트 인증서가 응용 프로그램 게이트웨이에 추가된 신뢰할 수 있는 루트 인증서와 일치하지 않습니다. 백 엔드를 화이트리스트에 올바른 루트 인증서를 추가해야 합니다.

**원인:** 응용 프로그램 게이트웨이 v2를 사용 하 여 종단 간 SSL 서버 정상으로 간주 하기 위해 백 엔드 서버의 인증서를 확인 해야 합니다.
TLS/SSL 인증서를 신뢰할 수 있도록 하려면 응용 프로그램 게이트웨이의 신뢰할 수 있는 저장소에 포함된 CA에서 백 엔드 서버 인증서를 발급해야 합니다. 신뢰할 수 있는 CA에서 인증서를 발급하지 않은 경우(예: 자체 서명된 인증서가 사용됨) 사용자는 발급자의 인증서를 응용 프로그램 게이트웨이에 업로드해야 합니다.

응용 프로그램 게이트웨이 HTTP 설정에 업로드된 인증서는 백 엔드 서버 인증서의 루트 인증서와 일치해야 합니다.

**솔루션:** 이 오류 메시지가 발생하면 응용 프로그램 게이트웨이에 업로드된 인증서와 백 엔드 서버에 업로드된 인증서 간에 불일치가 있습니다.

이전 방법의 1-11 단계를 수행하여 올바른 신뢰할 수 있는 루트 인증서를 응용 프로그램 게이트웨이에 업로드합니다.

응용 프로그램 게이트웨이에서 신뢰할 수 있는 루트 인증서를 추출하고 업로드하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 루트 인증서 내보내기(v2 SKU용)를](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)참조하십시오.
> [!NOTE]
> 이 오류는 백 엔드 서버가 TLS 핸드셰이크 중에 루트 > 중간(해당하는 경우) > 리프를 포함하여 인증서의 전체 체인을 교환하지 않는 경우에도 발생할 수 있습니다. 확인하려면 모든 클라이언트에서 OpenSSL 명령을 사용하고 응용 프로그램 게이트웨이 프로브에서 구성된 설정을 사용하여 백 엔드 서버에 연결할 수 있습니다.

예를 들어:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
출력에 반환되는 인증서의 전체 체인이 표시되지 않으면 루트 인증서를 포함한 전체 체인을 사용하여 인증서를 다시 내보냅니다. 백 엔드 서버에서 해당 인증서를 구성합니다. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>백 엔드 인증서가 유효하지 않은 일반 이름(CN)

**메시지:** 백 엔드 인증서의 일반 이름(CN)이 프로브의 호스트 헤더와 일치하지 않습니다.

**원인:** 응용 프로그램 게이트웨이는 백 엔드 HTTP 설정에 지정된 호스트 이름이 백 엔드 서버의 TLS/SSL 인증서에서 제공하는 CN의 이름과 일치하는지 여부를 확인합니다. 이것은 Standard_v2 및 WAF_v2 SKU 동작입니다. 표준 및 WAF SKU의 서버 이름 표시(SNI)는 백 엔드 풀 주소의 FQDN으로 설정됩니다.

v2 SKU에서 기본 프로브가 있는 경우(사용자 지정 프로브가 구성및 연결되지 않은 경우) SNI는 HTTP 설정에 언급된 호스트 이름에서 설정됩니다. 또는 백 엔드 주소 풀에 유효한 FQDN이 포함된 HTTP 설정에서 "백 엔드 주소에서 호스트 이름 선택"이 언급된 경우 이 설정이 적용됩니다.

HTTP 설정과 연결된 사용자 지정 프로브가 있는 경우 사용자 지정 프로브 구성에 언급된 호스트 이름에서 SNI가 설정됩니다. 또는 사용자 지정 **프로브에서 백 엔드 HTTP 설정에서 호스트 이름 선택이** 선택된 경우 SNI는 HTTP 설정에 언급된 호스트 이름에서 설정됩니다.

**백 엔드 주소에서 호스트 이름 선택** HTTP 설정에 설정된 경우 백 엔드 주소 풀에 유효한 FQDN이 포함되어야 합니다.

이 오류 메시지가 표시되면 백 엔드 인증서의 CN이 사용자 지정 프로브 또는 HTTP 설정에 구성된 호스트 이름과 일치하지 **않습니다(백 엔드 HTTP 설정에서 호스트 이름 선택이** 선택된 경우). 기본 프로브를 사용하는 경우 호스트 이름이 **127.0.0.1로**설정됩니다. 원하는 값이 아닌 경우 사용자 지정 프로브를 만들어 HTTP 설정과 연결해야 합니다.

**솔루션:**

이 문제를 해결하려면 다음 단계를 수행합니다.

Windows의 경우:

1.  응용 프로그램이 호스팅되는 컴퓨터에 로그인합니다.

1.  승리 +R을 선택하거나 **시작** 버튼을 마우스 오른쪽 버튼으로 클릭하고 **실행을**선택합니다.

1.  **certmgr.msc를** 입력하고 Enter를 선택합니다. **시작** 메뉴에서 인증서 관리자를 검색할 수도 있습니다.

1.  인증서를 (일반적으로 `\Certificates - Current User\\Personal\\Certificates`에서) 찾아 인증서를 엽니다.

1.  세부 **정보** 탭에서 인증서 **제목을**확인합니다.

1.  세부 정보에서 인증서의 CN을 확인하고 사용자 지정 프로브의 호스트 이름 필드 또는 HTTP **설정(백 엔드 HTTP 설정에서 호스트 이름 선택이** 선택된 경우)에 동일하게 입력합니다. 웹 사이트에 원하는 호스트 이름이 아닌 경우 해당 도메인에 대한 인증서를 얻거나 사용자 지정 프로브 또는 HTTP 설정 구성에 올바른 호스트 이름을 입력해야 합니다.

OpenSSL을 사용하는 리눅스의 경우:

1.  OpenSSL에서 이 명령을 실행합니다.
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  표시된 속성에서 인증서의 CN을 찾아 http 설정의 호스트 이름 필드에 동일하게 입력합니다. 웹 사이트에 원하는 호스트 이름이 아닌 경우 해당 도메인에 대한 인증서를 얻거나 사용자 지정 프로브 또는 HTTP 설정 구성에 올바른 호스트 이름을 입력해야 합니다.

#### <a name="backend-certificate-is-invalid"></a>백 엔드 인증서가 잘못되었습니다.

**메시지:** 백 엔드 인증서가 잘못되었습니다. \"현재 날짜가 유효 하지\" 않습니다 \"및\" 유효한 날짜 범위 인증서에.

**원인:** 모든 인증서에는 유효성 범위가 제공되며 서버의 TLS/SSL 인증서가 유효하지 않으면 HTTPS 연결이 안전하지 않습니다. 현재 데이터는 **유효한 데이터** 내에 있어야 하며 **범위까지 유효해야** 합니다. 그렇지 않은 경우 인증서는 유효하지 않은 것으로 간주되며 응용 프로그램 게이트웨이가 백 엔드 서버를 비정상으로 표시하는 보안 문제가 발생합니다.

**솔루션:** TLS/SSL 인증서가 만료된 경우 공급업체와 인증서를 갱신하고 새 인증서로 서버 설정을 업데이트합니다. 자체 서명된 인증서인 경우 유효한 인증서를 생성하고 루트 인증서를 응용 프로그램 게이트웨이 HTTP 설정에 업로드해야 합니다. 이렇게 하려면 다음 단계를 수행하세요.

1.  포털에서 응용 프로그램 게이트웨이 HTTP 설정을 엽니다.

1.  만료된 인증서가 있는 설정을 선택하고 **인증서 추가를**선택하고 새 인증서 파일을 엽니다.

1.  인증서 옆에 있는 **삭제** 아이콘을 사용하여 이전 인증서를 제거한 다음 **저장을**선택합니다.

#### <a name="certificate-verification-failed"></a>인증서 확인에 실패했습니다.

**메시지:** 백 엔드 인증서의 유효성을 확인할 수 없습니다. 이유를 알아보려면 오류 코드 {errorCode}와 연결된 메시지에 대한 OpenSSL 진단을 선택합니다.

**원인:** 이 오류는 응용 프로그램 게이트웨이가 인증서의 유효성을 확인할 수 없는 경우에 발생합니다.

**솔루션:** 이 문제를 해결하려면 서버의 인증서가 제대로 만들어졌는지 확인합니다. 예를 들어 [OpenSSL을](https://www.openssl.org/docs/man1.0.2/man1/verify.html) 사용하여 인증서와 해당 속성을 확인한 다음 인증서를 응용 프로그램 게이트웨이 HTTP 설정으로 다시 업로드할 수 있습니다.

<a name="backend-health-status-unknown"></a>백 엔드 상태: 알 수 없음
-------------------------------
백 엔드 상태를 알 수 없음으로 표시하면 포털 보기는 다음 스크린샷과 유사합니다.

![응용 프로그램 게이트웨이 백 엔드 상태 - 알 수 없음](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

이 문제는 다음 이유 중 하나 이상에 대해 발생할 수 있습니다.

1.  응용 프로그램 게이트웨이 서브넷의 NSG는 "인터넷"에서 포트 65503-65534(v1 SKU) 또는 65200-65535(v2 SKU)에 대한 인바운드 액세스를 차단하고 있습니다.
1.  응용 프로그램 게이트웨이 서브넷의 UDR은 기본 경로(0.0.0.0/0)로 설정되고 다음 홉은 "인터넷"으로 지정되지 않습니다.
1.  기본 경로는 BGP를 통해 가상 네트워크에 대한 ExpressRoute/VPN 연결에 의해 보급됩니다.
1.  사용자 지정 DNS 서버는 공용 도메인 이름을 확인할 수 없는 가상 네트워크에서 구성됩니다.
1.  응용 프로그램 게이트웨이가 비정상 상태입니다.

**솔루션:**

1.  NSG가 **인터넷에서**포트 65503-65534(v1 SKU) 또는 65200-65535(v2 SKU)에 대한 액세스를 차단하고 있는지 확인합니다.

    a.  응용 프로그램 게이트웨이 **개요** 탭에서 **가상 네트워크/서브넷** 링크를 선택합니다.

    b.  가상 네트워크의 **서브넷** 탭에서 응용 프로그램 게이트웨이가 배포된 서브넷을 선택합니다.

    다.  NSG가 구성되었는지 확인합니다.

    d.  NSG가 구성된 경우 **검색** 탭 또는 모든 리소스 아래에서 해당 NSG **리소스를**검색합니다.

    e.  **인바운드 규칙** 섹션에서 v1 SKU의 경우 대상 포트 범위 65503-65534 또는 **소스가** **임의** 또는 **인터넷으로**설정된 65200-65535 v2 SKU를 허용하는 인바운드 규칙을 추가합니다.

    f.  **저장을** 선택하고 백 엔드를 정상으로 볼 수 있는지 확인합니다. 또는 [PowerShell/CLI를](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)통해 이 작업을 수행할 수 있습니다.

1.  UDR에 **인터넷으로**설정되지 않은 다음 홉이 있는 기본 경로(0.0.0.0/0)가 있는지 확인합니다.
    
    a.  1a 및 1b 단계를 수행하여 서브넷을 확인합니다.

    b.  UDR이 구성되었는지 확인합니다. 있는 경우 검색 표시줄 또는 **모든 리소스**아래에서 리소스를 검색합니다.

    다.  다음 홉이 **인터넷으로**설정되지 않은 기본 경로(0.0.0.0/0)가 있는지 확인합니다. 설정이 가상 **어플라이언스** 또는 **가상 네트워크 게이트웨이인**경우 가상 어플라이언스 또는 온-프레미스 장치가 패킷을 수정하지 않고 패킷을 인터넷 대상으로 올바르게 라우팅할 수 있는지 확인해야 합니다.

    d.  그렇지 않으면 다음 홉을 **인터넷으로**변경하고 **저장을**선택하고 백 엔드 상태를 확인합니다.

1.  익스프레스루트/VPN 연결로 BGP를 통해 가상 네트워크에 보급되는 기본 경로:

    a.  BGP를 통해 가상 네트워크에 ExpressRoute/VPN 연결이 있고 기본 경로를 광고하는 경우 패킷을 수정하지 않고 인터넷 대상으로 다시 라우팅되는지 확인해야 합니다. 응용 프로그램 게이트웨이 포털에서 **연결 문제 해결** 옵션을 사용하여 확인할 수 있습니다.

    b.  1.1.1.1과 같은 인터넷 라우팅 가능한 IP 주소로 수동으로 대상을 선택합니다. 대상 포트를 아무 것도 설정하지 않으며 연결을 확인합니다.

    다.  다음 홉이 가상 네트워크 게이트웨이인 경우 ExpressRoute 또는 VPN을 통해 보급되는 기본 경로가 있을 수 있습니다.

1.  가상 네트워크에 구성된 사용자 지정 DNS 서버가 있는 경우 서버(또는 서버)가 공용 도메인을 해결할 수 있는지 확인합니다. 응용 프로그램 게이트웨이OCSP 서버와 같은 외부 도메인에 도달하거나 인증서의 해지 상태를 확인해야 하는 경우 공용 도메인 이름 확인이 필요할 수 있습니다.

1.  응용 프로그램 게이트웨이가 정상이고 실행 중인지 확인하려면 포털의 **리소스 상태** 옵션으로 이동하여 상태가 **정상인지**확인합니다. **비정상** 또는 저하 **상태가** 표시되면 [지원 팀에 문의하십시오.](https://azure.microsoft.com/support/options/)

<a name="next-steps"></a>다음 단계
----------

[응용 프로그램 게이트웨이 진단 및 로깅에](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)대해 자세히 알아봅니다.
