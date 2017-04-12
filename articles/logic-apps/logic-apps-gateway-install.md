---
title: "온-프레미스 데이터 게이트웨이 설치 - Azure Logic Apps | Microsoft Docs"
description: "온-프레미스 데이터 게이트웨이를 설치하여 논리 앱에서 온-프레미스 데이터에 액세스"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 23b806556667539156ee399e90c7bd4af808804f
ms.lasthandoff: 03/10/2017


---
# <a name="install-an-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps에 온-프레미스 데이터 게이트웨이 설치

온-프레미스 데이터 게이트웨이는 다음과 같은 데이터 원본 연결을 지원합니다.

*   BizTalk Server
*    DB2  
*   파일 시스템
*   Informix
*   MQ
*    Oracle 데이터베이스 
*   SAP 응용 프로그램 서버 
*   SAP 메시지 서버
*    SQL Server

이러한 연결에 대한 자세한 내용은 [Azure Logic Apps에 대한 커넥터](https://docs.microsoft.com/azure/connectors/apis-list)를 참조하세요.

## <a name="installation-and-configuration"></a>설치 및 구성

### <a name="requirements"></a>요구 사항

최소:

* .NET 4.5 Framework
* 64비트 버전의 Windows 7 또는 Windows Server 2008 R2 이상

권장:

* 8 코어 CPU
* 8GB 메모리
* 64비트 버전의 Windows 2012 R2 이상

관련 고려 사항:

* 온-프레미스 데이터 게이트웨이를 로컬 컴퓨터에 설치해야 합니다.
도메인 컨트롤러에 게이트웨이를 설치할 수 없습니다.

* 게이트웨이가 이러한 상황에서 실행되지 않을 수 있기 때문에 꺼지거나, 절전 또는 인터넷에 연결되지 않은 컴퓨터에서 게이트웨이를 설치하지 않습니다. 또한 무선 네트워크에서는 게이트웨이 성능이 저하될 수 있습니다.

* Azure에서 회사 또는 학교 전자 메일 주소를 사용할 수 있으므로 Azure Active Directory 기반 계정으로 온-프레미스 데이터 게이트웨이를 연결할 수 있습니다.

    Microsoft 계정(예: @outlook.com)을 사용하는 경우 Azure 계정을 사용하여   [회사 또는 학교 전자 메일 주소를 만들](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal) 수 있습니다.

### <a name="install-the-gateway"></a>게이트웨이 설치

1.    [여기에서 온-프레미스 데이터 게이트웨이용 설치 관리자를 다운로드합니다](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2.    **온-프레미스 데이터 게이트웨이**를 모드로 지정합니다.

3. Azure 회사 또는 학교 계정으로 로그인합니다. 

4. 새 게이트웨이를 설정하거나 기존 게이트웨이를 마이그레이션, 복원 또는 대신할 수 있습니다.

    게이트웨이 구성하려면 게이트웨이 및 복구 키의 이름을 입력한 다음 **구성**을 선택합니다.
  
    최소&8;자를 포함하는 복구 키를 지정하고 키를 안전한 장소에 보관합니다. 게이트웨이를 마이그레이션, 복원 또는 인계받으려는 경우 이 키가 필요합니다.

    기존 게이트웨이를 마이그레이션, 복원 또는 인계받으려면 게이트웨이를 만들 때 지정한 복구 키를 제공합니다.

### <a name="restart-the-gateway"></a>게이트웨이 다시 시작

게이트웨이는 Windows 서비스로 실행되므로 다른 Windows 서비스와 마찬가지로 서비스를 여러 가지 방법으로 시작하고 중지할 수 있습니다. 예를 들어 게이트웨이가 실행 중인 컴퓨터에서 관리자 권한으로 명령 프롬프트를 열고 다음 명령 중 하나를 실행할 수 있습니다.

* 서비스를 중지하려면 다음 명령을 실행합니다.
  
    `net stop PBIEgwService`

* 서비스를 시작하려면 이 명령을 실행 합니다.
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>방화벽 또는 프록시 구성

게이트웨이에 대한 프록시 정보를 제공하려면 [프록시 설정 구성](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/)을 참조하세요.

PowerShell 프롬프트에서 다음 명령을 실행하여 방화벽 또는 프록시가 연결을 차단할 수 있는지를 확인할 수 있습니다. 이 명령은 Azure Service Bus 및 네트워크 연결에 대한 연결성만 테스트하므로 명령은 클라우드 서버 또는 게이트웨이와 관련이 없습니다. 이 테스트는 컴퓨터가 실제로 인터넷에 연결될 수 있는지 여부를 확인하는 데도 도움이 됩니다.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

결과는 다음 예제와 비슷합니다. **TcpTestSucceeded**가 true가 아니면 방화벽에 의해 차단될 수 있습니다.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

확실히 하려면 **ComputerName** 및 **포트** 값을 이 항목의 [포트 구성](#configure-ports) 아래에 나열된 값으로 바꿉니다.

또한 방화벽이 Azure Service Bus와 Azure 데이터 센터 간에 형성된 연결을 차단할 수도 있습니다. 그렇다면 지역에서 해당 데이터 센터에 대한 모든 IP 주소를 승인(차단 해제)합니다.
[여기서 Azure IP 주소](https://www.microsoft.com/download/details.aspx?id=41653)목록을 확인할 수 있습니다.

### <a name="configure-ports"></a>포트 구성
게이트웨이에서는 Azure Service Bus에 대한 아웃바운드 연결을 만들고 TCP 443(기본값), 5671, 5672, 9350~9354라는 아웃바운드 포트에서 통신합니다. 게이트웨이에 인바운드 포트가 필요하지 않습니다.

[하이브리드 솔루션](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)에 대해 자세히 알아보세요.

| 도메인 이름 | 아웃바운드 포트 | 설명 |
| --- | --- | --- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | AMQP(고급 메시지 큐 프로토콜) | 
| *.servicebus.windows.net | 443, 9350-9354 | TCP의 서비스 버스 릴레이에 대한 수신기(액세스 제어 토큰 획득에 443 필요) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Power BI 서비스에서 게이트웨이에 연결할 수 없는 경우 인터넷 연결을 테스트하는 데 사용합니다. | 

도메인 대신 IP 주소를 승인해야 하는 경우 [Microsoft Azure 데이터 센터 IP 범위 목록](https://www.microsoft.com/download/details.aspx?id=41653)을 다운로드하여 사용할 수 있습니다. 일부 경우에는 정규화된 도메인 이름이 아닌 IP 주소를 사용해서 Azure Service Bus를 연결합니다.

### <a name="sign-in-accounts"></a>로그인 계정

조직 계정인 회사 또는 학교 계정 중 하나를 사용하여 로그인할 수 있습니다. Office 365 서비스에 등록하고 실제 회사 전자 메일을 제공하지 않은 경우 로그인 주소는 jeff@contoso.onmicrosoft.com과 같을 수 있습니다. 클라우드 서비스 내에서 계정은 Azure AD(Azure Active Directory)의 테넌트 내에 저장됩니다. 일반적으로 Azure AD 계정의 UPN는 전자 메일 주소와 일치합니다.

### <a name="windows-service-account"></a>Windows 서비스 계정

Windows 서비스 로그온 자격 증명의 경우 온-프레미스 데이터 게이트웨이는 NT SERVICE\PBIEgwService를 사용하도록 구성됩니다. 게이트웨이는 기본적으로 게이트웨이를 설치하는 컴퓨터의 컨텍스트 내에서 "서비스로 로그온(Log on as a service)"할 권한을 가집니다.

이 서비스 계정은 온-프레미스 데이터 원본에 연결하는 데 사용되는 동일한 계정이나 클라우드 서비스에 로그인하는 데 사용하는 회사 또는 학교 계정이 아닙니다.

## <a name="how-the-gateway-works"></a>게이트웨이 작동 원리
다른 사용자가 온-프레미스 데이터 원본에 연결된 요소와 상호 작용하면 다음 작업이 진행됩니다.

1. 클라우드 서비스가 데이터 원본에 대해 암호화된 자격 증명과 함께 쿼리를 만들고, 게이트웨이가 처리할 수 있게 쿼리를 큐에 전송합니다.
2. 서비스가 쿼리를 분석하고 Azure 서비스 버스에 요청을 푸시합니다.
3. 온-프레미스 데이터 게이트웨이가 보류 중인 요청을 위해 Azure 서비스 버스를 폴링합니다.
4. 게이트웨이가 쿼리를 가져오고, 자격 증명의 암호를 해독하고, 해당 자격 증명으로 데이터 원본에 연결합니다.
5. 게이트웨이가 실행을 위해 데이터 원본에 쿼리를 보냅니다.
6. 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후 클라우드 서비스로 전송됩니다. 서비스에서 해당 결과를 사용합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="general"></a>일반

**질문**: SQL Azure와 같은 클라우드에서 데이터 원본에 대한 게이트웨이가 필요하나요? <br/>
**대답**: 아니요. 게이트웨이는 온-프레미스 데이터 원본에만 연결됩니다.

**질문**: 실제 Windows 서비스를 어떻게 지칭하나요?<br/>
**대답**: 서비스에서 게이트웨이를 Power BI 엔터프라이즈 게이트웨이 서비스라고 합니다.

**질문**: 클라우드에서 게이트웨이로의 인바운드 연결이 있나요? <br/>
**대답**: 아니요. 게이트웨이는 Azure 서비스 버스에 대해 아웃바운드 연결을 사용합니다.

**질문**: 아웃바운드 연결을 차단하면 어떻게 되나요? 이러한 연결을 열려면 무엇이 필요한가요? <br/>
**대답**: 게이트웨이가 사용하는 포트 및 호스트를 참조하세요.

**질문**: 게이트웨이가 사용하는 게이트웨이를 설치해야 하나요? <br/>
**대답**: 아니요. 게이트웨이는 제공된 연결 정보를 사용하여 데이터 원본에 연결됩니다. 이러한 의미에서 게이트웨이를 클라이언트 응용 프로그램이라고 생각할 수 있습니다. 게이트웨이는 제공된 서버 이름에 연결할 수 있는 기능이 필요합니다.

**질문**: 게이트웨이에서 데이터 원본으로의 쿼리를 실행할 때 나타나는 대기 시간은 얼마나 되나요? 최선의 아키텍처는 무엇인가요? <br/>
**대답**: 네트워크 대기 시간을 줄이려면 게이트웨이를 데이터 원본에 최대한 가깝게 설치합니다. 실제 데이터 원본에 게이트웨이를 설치할 수 있는 경우 이 근접성으로 인해 발생하는 대기 시간이 최소화됩니다. 데이터 센터도 고려해야 합니다. 예를 들어 서비스에서 미국 서부 데이터 센터를 사용하고 Azure VM에서 SQL Server가 호스트되는 경우 Azure VM도 미국 서부에 있어야 합니다. 이 근접성으로 인해 대기 시간이 최소화되고 Azure VM에서 송신 요금이 발생하지 않습니다.

**질문**: 네트워크 대역폭에 대한 요구 사항이 있나요? <br/>
**응답**: 네트워크 연결의 처리량이 높은 것이 좋습니다. 모든 환경은 다르며 전송되는 데이터의 양은 결과에 영향을 미칩니다. Express 경로를 사용하면 온-프레미스 및 Azure 데이터 센터 간의 처리량 수준을 보장하는 데 도움이 될 수 있습니다.

타사 도구인 Azure Speed Test 앱을 사용하면 처리량을 측정하는 데 유용할 수 있습니다.

**질문**: Azure Active Directory 계정으로 게이트웨이 Windows 서비스를 실행할 수 있나요? <br/>
**대답**: 아니요. Windows 서비스에는 유효한 Windows 계정이 있어야 합니다. 기본적으로 서비스는 서비스 SID, NT SERVICE\PBIEgwService를 사용하여 실행됩니다.

**질문**: 결과가 클라우드로 어떻게 다시 전송되나요? <br/>
**응답**: Azure Service Bus를 통해 결과가 전송됩니다.

**질문**: 내 자격 증명은 어디에 저장되나요? <br/>
**대답**: 데이터 원본에 입력한 자격 증명은 게이트웨이 클라우드 서비스에 암호화되어 저장됩니다. 자격 증명은 온-프레미스 게이트웨이에서 해독됩니다.

### <a name="high-availabilitydisaster-recovery"></a>고가용성/재해 복구
**질문**: 게이트웨이에 고가용성 시나리오를 사용하기 위한 계획이 있나요? <br/>
**대답**: 이러한 시나리오는 로드맵에는 있지만 아직 일정은 나와 있지 않습니다.

**질문**: 재해 복구를 위해 어떤 옵션을 사용할 수 있나요? <br/>
**대답**: 복구 키를 사용하여 게이트웨이를 복원하거나 이동할 수 있습니다. 게이트웨이를 설치할 때 복구 키를 지정합니다.

**질문**: 복구 키를 사용할 경우의 이점은 무엇인가요? <br/>
**대답**: 복구 키는 재해 발생 후 게이트웨이 설정을 마이그레이션하거나 복구할 수 있는 방법을 제공합니다.

## <a name="troubleshooting"></a>문제 해결

**질문**: 게이트웨이 로그는 어디에 있나요? <br/>
**대답**: 이 항목 뒷부분에 나오는 도구를 참조하세요.

**질문**: 온-프레미스 데이터 원본으로 어떤 쿼리가 전송되고 있는지를 어떻게 알 수 있나요? <br/>
**대답**: 전송 중인 쿼리를 포함하는 쿼리 추적을 사용하도록 설정할 수 있습니다. 문제 해결이 끝나면 쿼리 추적을 원래 값으로 다시 변경해야 합니다. 쿼리 추적 기능을 그대로 두면 큰 로그가 만들어집니다.

또한 추적 쿼리를 위해 데이터 원본이 포함하는 도구를 살펴볼 수도 있습니다. 예를 들어 SQL Server 및 Analysis Services의 경우 확장 이벤트 또는 SQL 프로파일러를 사용할 수 있습니다.

### <a name="update-to-the-latest-version"></a>최신 버전으로 업데이트

게이트웨이 버전이 오래되면 여러 가지 문제가 발생할 수 있습니다. 일반적으로는 최신 버전을 사용해야 합니다. 한 달 동안 게이트웨이를 업데이트하지 않은 경우 최신 버전의 게이트웨이를 설치하고 문제가 재현되는지 확인할 수 있습니다.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>오류: 그룹에 사용자를 추가하지 못했습니다. (-2147463168 PBIEgwService 성능 로그 사용자)

지원되지 않는 도메인 컨트롤러에 게이트웨이 설치하려고 하면 이 오류가 표시될 수 있습니다. 도메인 컨트롤러가 아닌 컴퓨터에 게이트웨이를 배포해야 합니다.

## <a name="tools"></a>도구
### <a name="collect-logs-from-the-gateway-configurer"></a>게이트웨이 구성기에서 로그 수집

게이트웨이에 대한 여러 로그를 수집할 수 있습니다. 항상 로그부터 확인하세요.

#### <a name="installer-logs"></a>설치 관리자 로그

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>구성 로그

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>엔터프라이즈 게이트웨이 서비스 로그

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>이벤트 로그

데이터 관리 게이트웨이 및 PowerBIGateway 로그를 **응용 프로그램 및 서비스 로그** 아래에서 찾을 수 있습니다.

### <a name="fiddler-trace"></a>Fiddler 추적

[Fiddler](http://www.telerik.com/fiddler) 는 HTTP 트래픽을 모니터링하는 Telerik의 무료 도구입니다. 클라이언트 컴퓨터에서 Power BI를 사용하여 트래픽을 볼 수 있습니다. 이 서비스는 오류 및 기타 관련된 정보를 표시할 수 있습니다.

## <a name="next-steps"></a>다음 단계
    
* [논리 앱에서 온-프레미스 데이터에 연결](../logic-apps/logic-apps-gateway-connection.md)
* [엔터프라이즈 통합 기능](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps용 커넥터](../connectors/apis-list.md)

