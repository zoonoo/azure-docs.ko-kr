---
title: "OMS 게이트웨이를 사용하여 OMS에 컴퓨터 및 장치 연결 | Microsoft Docs"
description: "OMS 서비스가 인터넷에 연결되어 있지 않을 때 OMS 서비스에 데이터를 보내기 위해 OMS 관리 장치 및 Operations Manager 모니터링 컴퓨터를 OMS 게이트웨이와 연결합니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: dc6cdf1630407d1c4439d89aca5a8254bb806eba
ms.openlocfilehash: 41382ba85fee1c3f5b1570748cd3d5b0641b9593


---
# <a name="connect-computers-and-devices-to-oms-using-the-oms-gateway"></a>OMS 게이트웨이를 사용하여 OMS에 컴퓨터 및 장치 연결
이 문서는 OMS 관리 장치 및 SCOM(System Center Operations Manager) 모니터링 컴퓨터에서 OMS 서비스가 인터넷에 연결되어 있지 안을 때 OMS 서비스에 데이터를 보내는 방법을 설명합니다. OMS 게이트웨이는 OMS 서비스를 대신하여 데이터를 수집하여 OMS 서비스에 보낼 수 있습니다.

게이트웨이는 HTTP CONNECT 명령을 사용하여 HTTP 터널링을 지원하는 HTTP 전달 프록시입니다. 게이트웨이는 Windows를 실행하는 4코어 CPU, 16GB 서버를 사용하는 경우 OMS 동시 연결 장치를 2000개까지 처리할 수 있습니다.

예를 들어, 엔터프라이즈 또는 대규모 조직에 네트워크는 연결되어 있지만 인터넷에는 연결되어 있지 않은 서버가 있을 수 있습니다. 다른 예에서는, 직접 모니터링할 수 있는 방법이 없는 POS(point of sale) 장치가 많이 있을 수 있습니다. 다른 예의 경우, Operations Manager가 OMS 게이트웨이를 프록시 서버로 사용할 수 있습니다. 이런 경우 OMS 게이트웨이는 서버에 설치되어 있는 에이전트 또는 POS 장치에서 OMS로 데이터를 전송할 수 있습니다.

각각의 개별적인 에이전트가 OMS에 데이터를 직접 보내면서 인터넷에 직접 연결하는 대신에 모든 에이전트 데이터가 인터넷에 연결되어 있는 단일 컴퓨터를 통해 전송됩니다. 바로 이 컴퓨터에 게이트웨이를 설치하여 사용합니다. 이 시나리오에서는 데이터를 수집하려는 컴퓨터에 에이전트를 설치할 수 있습니다. 게이트웨이는 에이전트에서 OMS로 데이터를 직접 전송합니다. 게이트웨이는 전송되는 어떠한 데이터도 분석하지 않습니다.

OMS 게이트웨이를 모니터링하고 게이트웨이가 설치되어 있는 서버의 성능 또는 이벤트 데이터를 분석하려면 게이트웨이가 설치되어 있는 컴퓨터에 OMS 에이전트를 설치해야 합니다.

게이트웨이가 OMS에 데이터를 업로드하려면 인터넷에 연결되어 있어야 합니다. 각 에이전트는 게이트웨이와 데이터를 자동으로 송수신할 수 있도록 게이트웨이에 네트워크로 연결되어 있어야 합니다. 최상의 결과를 위해 도메인 컨트롤러 역할을 하는 컴퓨터에 게이트웨이를 설치하지 마십시오.

다음은 직접 에이전트에서 OMS로의 데이터 흐름을 보여주는 다이어그램입니다.

![직접 에이전트 다이어그램](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

다음은 Operations Manager에서 OMS로의 데이터 흐름을 보여주는 다이어그램입니다.

![Operations Manager 다이어그램](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="install-the-oms-gateway"></a>OMS 게이트웨이 설치
이 게이트웨이를 설치하면 전에 설치했던 이전 버전의 게이트웨이를(Log Analytics 전달자) 대체하게 됩니다.

필수 조건: .Net Framework 4.5, Windows Server 2012 R2 SP1 이상

1. [Microsoft 다운로드 센터](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi)에서 최신 버전의 OMS 게이트웨이를 다운로드합니다.
2. 설치를 시작하려면 **OMS Gateway.msi**를 두 번 클릭합니다.
3. 시작 페이지에서 **다음**을 클릭합니다.  
    ![게이트웨이 설치 마법사](./media/log-analytics-oms-gateway/gateway-wizard01.png)
4. 사용권 계약 페이지에서 **동의함**을 선택하여 EULA에 동의하고 **다음**을 클릭합니다.
5. 포트 및 프록시 주소 페이지에서:
   1. 게이트웨이에 사용될 TCP 포트 번호를 입력합니다. 설치 프로그램이 Windows 방화벽에서 이 포트 번호를 엽니다. 기본값은 8080입니다.
      유효한 포트 번호 범위는 1 ~ 65535입니다. 입력한 내용이 이 범위를 벗어나면 오류 메시지가 표시됩니다.
   2. 선택적으로, 게이트웨이가 설치되어 있는 서버에서 프록시를 사용해야 하는 경우에는 게이트웨이 연결이 필요한 프록시 주소를 입력합니다. 예: `http://myorgname.corp.contoso.com:80`. 만약 비워두면 게이트웨이는 인터넷에 직접 연결을 시도합니다. 그렇지 않은 경우 게이트웨이는 프록시에 연결합니다. 프록시 서버에 인증이 필요한 경우 사용자 이름과 암호를 입력합니다.
       ![게이트웨이 마법사 프록시 구성](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
   3.  **다음**
6. Microsoft 업데이트를 사용하도록 설정되어있지 않으면 Microsoft Updates를 사용하도록 설정을 선택할 수 있는 Microsoft Update 페이지가 표시됩니다. 선택한 후에 **다음**을 클릭합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.
7. 대상 폴더 페이지에서 기본 폴더 **%ProgramFiles%\OMS Gateway**를 유지하거나 게이트웨이를 설치할 위치를 입력하고 **다음**을 클릭합니다.
8. 설치 준비 완료 페이지에서 **설치**를 클릭합니다. 사용자 계정 컨트롤이 표시되고 설치 권한을 요청할 수 있습니다. 그런 경우에는 **예**를 클릭합니다.
9. 설치가 완료된 후에 **마침**을 클릭합니다. services.msc 스냅인을 열어서 서비스 목록에 **OMS 게이트웨이**가 표시되는 것을 확인하여 서비스가 실행 중인 것을 확인할 수 있습니다.  
    ![서비스 – OMS 게이트웨이](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>장치에 에이전트 설치
필요한 경우 직접 연결된 에이전트를 설치하는 방법에 대한 내용은 [Log Analytics에 Windows 컴퓨터 연결](log-analytics-windows-agents.md)을 참조하세요. 이 문서는 설치 마법사를 사용하여 또는 명령줄을 사용하여 에이전트를 설치할 수 있는 방법을 설명합니다.

## <a name="configure-oms-agents"></a>OMS 에이전트 구성
프록시 서버를 사용하도록 에이전트(이 경우 게이트웨이)를 구성하는 내용은 [Microsoft Monitoring Agent를 사용하여 프록시 및 방화벽 설정 구성](log-analytics-proxy-firewall.md)을 참조하세요.

Operations Manager 에이전트는 관리 서버를 통해 Operations Manager 경고, 구성 평가, 인스턴스 공간, 용량 데이터와 같은 데이터를 보냅니다. IIS 로그, 성능, 보안과 같은 기타 대용량 데이터는 OMS 게이트웨이에 직접 전송됩니다. 각 채널을 통해 전송되는 전체 데이터 목록은 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)를 참조하세요.

> [!NOTE]
> 부하 분산을 통해 게이트웨이를 사용할 계획이면 [선택적으로 네트워크 부하 분산 구성](#optionally-configure-network-load-balancing)(영문)을 참조하세요.
>
>

## <a name="configure-a-scom-proxy-server"></a>SCOM 프록시 서버 구성
프록시 서버 역할을 하는 게이트웨이를 추가하도록 Operations Manager를 구성합니다. 프록시 구성을 업데이트하면 Operations Manager에 보고하는 모든 에이전트에 프록시 구성이 자동으로 적용됩니다.

Operations Manager를 지원하도록 게이트웨이를 사용하려면 다음이 필요합니다.

* Microsoft Monitoring Agent(에이전트 버전 – **8.0.10900.0** 이상)가 게이트웨이 서버에 설치되어 있고 통신할OMS 작업 영역에 대해 구성되어 있어야 합니다.
* 게이트웨이가 인터넷에 연결되어 있거나 인터넷에 연결되어 있는 프록시 서버에 연결되어 있어야 합니다.

### <a name="to-configure-scom-for-the-gateway"></a>게이트웨이를 위해 SCOM을 구성하려면
1. Operations Manager 콘솔을 열고 **Operations Management Suite** 아래에서 **연결**을 클릭한 다음**프록시 서버 구성**을 클릭합니다.  
    ![Operations Manager – 프록시 서버 구성](./media/log-analytics-oms-gateway/scom01.png)
2. **프록시 서버를 사용하여 Operations Management Suite에 액세스**를 선택한 다음 OMS 게이트웨이 서버의 IP 주소를 입력합니다. `http://` 접두사부터 입력해야 합니다.  
    ![Operations Manager – 프록시 서버 주소](./media/log-analytics-oms-gateway/scom02.png)
3. **마침**을 클릭합니다. Operations Manager 서버가 OMS 작업 영역에 연결됩니다.

## <a name="configure-network-load-balancing"></a>네트워크 부하 분산 구성
클러스터를 생성하여 네트워크 부하 분산을 사용하는 고가용성을 위해 게이트웨이를 구성할 수 있습니다. 클러스터는 Microsoft Monitoring Agent에서 요청한 연결을 노드 전역으로 리디렉션하여 에이전트에서 나오는 트래픽을 관리합니다. 게이트웨이 서버가 하나 다운되면 트래픽은 다른 노드로 리디렉션됩니다.

1. 네트워크 부하 분산 관리자를 열고 클러스터를 만듭니다.
2. 게이트웨이를 추가하기 전에 클러스터를 마우스 오른쪽 단추로 클릭하고 **클러스터 속성**을 선택합니다. 자체 IP 주소를 갖도록 클러스터를 구성합니다.  
    ![네트워크 부하 분산 관리자 – 클러스터 IP 주소](./media/log-analytics-oms-gateway/nlb01.png)
3. OMS 게이트웨이 서버를 설치되어 있는 Microsoft Monitoring Agent와 연결하려면 클러스터의 IP 주소를 마우스 오른쪽 단추로 클릭한 후 **클러스터에 호스트 추가**를 클릭합니다.  
    ![네트워크 부하 분산 관리자 – 클러스터에 호스트 추가](./media/log-analytics-oms-gateway/nlb02.png)
4. 연결한 게이트웨이 서버의 IP 주소를 입력합니다.  
    ![네트워크 부하 분산 관리자 – 클러스터에 호스트 추가: 연결](./media/log-analytics-oms-gateway/nlb03.png)
5. 인터넷에 연결되지 않은 컴퓨터에서 **Microsoft Monitoring Agent 속성**을 구성할 때 클러스터의 IP 주소를 사용해야 합니다.  
    ![Microsoft Monitoring Agent 속성 – 프록시 설정](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Automation Hybrid Worker에 대한 구성
환경 내에 Automation Hybrid Worker가 있는 경우 다음 단계는 이를 지원하도록 게이트웨이를 구성하는 수동의 임시 해결책을 제공합니다.

다음 단계에서 Automation 계정이 있는 Azure 지역을 알아야 합니다. 지역을 확인하려면:

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. Azure Automation 서비스를 선택합니다.
3. 적절한 Azure Automation 계정을 선택합니다.
4. **위치** 아래에서 해당 지역을 확인합니다.  
    ![Azure Portal – Automation 계정 위치](./media/log-analytics-oms-gateway/location.png)

다음 테이블을 사용하여 각 위치에 대한 URL을 확인합니다.

**작업 런타임 데이터 서비스 URL**

| **위치** | **URL** |
| --- | --- |
| 미국 중북부 |ncus-jobruntimedata-prod-su1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 일본 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 오스트레일리아 |ase-jobruntimedata-prod-su1.azure-automation.net |

**에이전트 서비스 URL**

| **위치** | **URL** |
| --- | --- |
| 미국 중북부 |ncus-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-agentservice-prod-1.azure-automation.net |
| 북유럽 |ne-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-agentservice-prod-1.azure-automation.net |
| 인도 중부 |cid-agentservice-prod-1.azure-automation.net |
| 일본 |jpe-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 |ase-agentservice-prod-1.azure-automation.net |

컴퓨터가 업데이트 관리 솔루션을 사용하여 패치용으로 Hybrid Worker로 자동 등록되어 있는 경우 다음 단계를 사용합니다.

1. 작업 런타임 데이터 서비스 URL을 OMS 게이트웨이의 허용된 호스트 목록에 추가합니다. 예:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. PowerShell cmdlet `Restart-Service OMSGatewayService`를 사용하여 OMS 게이트웨이 서비스를 다시 시작합니다.

컴퓨터가 Hybrid Worker 등록 cmdlet을 사용하여 Azure Automation에 온보딩되어 있으면 다음 단계를 사용합니다.

1. 에이전트 서비스 등록 URL을 OMS 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. 작업 런타임 데이터 서비스 URL을 OMS 게이트웨이의 허용된 호스트 목록에 추가합니다. 예:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. OMS 게이트웨이 서비스를 다시 시작합니다.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>유용한 PowerShell cmdlet
cmdlet은 OMS 게이트웨이 구성 설정을 업데이트하는 데 필요한 작업을 완료하는 데 도움을 줄 수 있습니다. 사용하기 전에 다음을 수행해야 합니다.

1. OMS 게이트웨이를 설치합니다(MSI).
2. PowerShell 창을 엽니다.
3. 모듈을 가져오기 위해 `Import-Module OMSGateway` 명령을 입력합니다.
4. 이전 단계에서 발생한 오류가 없으면 모듈 가져오기가 완료된 것이고 cmdlet을 사용할 수 있습니다. `Get-Module OMSGateway`를 입력합니다.
5. cmdlet을 사용하여 변경한 후에는 게이트웨이 서비스를 다시 시작해야 합니다.

3단계에서 오류가 발생하면 모듈을 가져오기가 완료되지 못한 것입니다. PowerShell이 모듈을 찾을 수 없는 경우 오류가 발생할 수 있습니다. 게이트웨이 설치 경로 C:\Program File\Microsoft OMS Gateway\PowerShell에서 해당 모듈을 찾을 수 있습니다.

| **Cmdlet** | **매개 변수** | **설명** | **예** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |키(필수) <br> 값 |서비스 구성 변경 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |키 |서비스 구성 가져오기 |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |주소 <br> 사용자 이름 <br> 암호 |릴레이(업스트림) 프록시 주소(및 자격 증명) 설정 |1. 릴레이 프록시 및 자격 증명 설정: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. 인증이 필요 없는 릴레이 프록시 설정: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. 릴레이 프록시 설정 지우기 즉, 릴레이 프록시 필요 없음: `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |릴레이(업스트림) 프록시 주소 가져오기 |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에 추가 |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에서 제거 |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |현재 허용된 호스트 가져오기(로컬에서 구성되어 허용된 호스트만, 자동으로 다운로드되어 허용된 호스트는 포함하지 않음) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에 추가 |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에서 제거 |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |현재 허용된 클라이언트 인증서 주체 가져오기(로컬에서 구성되어 허용된 주체만, 자동으로 다운로드되어 허용된 주체는 포함하지 않음) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>문제 해결
OMS 에이전트를 게이트웨이가 설치되어 있는 컴퓨터에 설치하는 것이 좋습니다. 그럼 다음 에이전트를 사용하여 게이트웨이에 의해 기록된 이벤트를 수집할 수 있습니다.

![이벤트 뷰어 – OMS 게이트웨이 로그](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS 게이트웨이 이벤트 ID 및 설명**

다음 테이블은 OMS 게이트웨이 로그 이벤트의 이벤트 ID 및 설명을 보여줍니다.

| **ID** | **설명** |
| --- | --- |
| 400 |특정 ID가 없는 모든 응용 프로그램 오류 |
| 401 |잘못된 구성. 예: listenPort = 정수가 아닌 “텍스트" |
| 402 |TLS 핸드셰이크 메시지 구문 분석 중 예외 |
| 403 |네트워킹 오류. 예: 대상 서버에 연결 할 수 없음 |
| 100 |일반 정보 |
| 101 |서비스가 시작됨 |
| 102 |서비스가 중지됨 |
| 103 |클라이언트로부터 HTTP CONNECT 명령을 수신함 |
| 104 |HTTP CONNECT 명령이 아님 |
| 105 |대상 서버가 허용 목록에 없거나 대상 포트가 보안 포트(443)가 아님 <br> <br> 게이트웨이 서버의 MMA 에이전트 및 게이트웨이와 통신하는 에이전트는 동일한 Log Analytics 작업 영역에 연결되어야 합니다. |
| 105 |ERROR TcpConnection – 클라이언트 인증서가 잘못됨: CN=Gateway <br><br> 다음 사항을 확인합니다. <br>    <br> &#149; 버전 번호가 1.0.395.0 이상인 게이트웨이를 사용 중입니다. <br> &#149; 게이트웨이 서버의 MMA 에이전트 및 게이트웨이와 통신하는 에이전트가 동일한 Log Analytics 작업 영역에 연결되어 있습니다. |
| 106 |TLS 세션이 의심스럽고 거부되는 모든 이유 |
| 107 |TLS 세션이 확인됨 |

**수집할 성능 카운터**

다음 테이블은 OMS 게이트웨이에 사용할 수 있는 성능 카운터를 보여줍니다. 카운터는 성능 모니터를 사용하여 추가할 수 있습니다.

| **Name** | **설명** |
| --- | --- |
| OMS 게이트웨이/활성 클라이언트 연결 |활성 클라이언트 네트워크(TCP) 연결의 수 |
| OMS 게이트웨이/오류 수 |오류 수 |
| OMS 게이트웨이/연결된 클라이언트 |연결된 클라이언트 수 |
| OMS 게이트웨이/거부 횟수 |TLS 유효성 검사 오류로 인한 거부 횟수 |

![OMS 게이트웨이 성능 카운터](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>지원 받기
Azure Portal에 로그인되어 있으면 OMS 게이트웨이 또는 다른 Azure 서비스나 서비스의 기능에 대한 지원 요청을 작성할 수 있습니다.
지원을 요청하려면 포털의 오른쪽 위 모서리에 있는 물음표 기호를 클릭한 후 **새 지원 요청**을 클릭합니다. 그런 다음 새 지원 요청 양식을 입력합니다.

![새 지원 요청](./media/log-analytics-oms-gateway/support.png)

[Microsoft Azure 피드백 포럼](https://feedback.azure.com/forums/267889)에 OMS 또는 Log Analytics에 대한 피드백을 남길 수도 있습니다.

## <a name="next-steps"></a>다음 단계
* [데이터 원본을 추가](log-analytics-data-sources.md)하여 OMS 작업 영역의 연결된 원본에서 데이터를 수집하고 OMS 리포지토리에 저장합니다.



<!--HONumber=Nov16_HO4-->


