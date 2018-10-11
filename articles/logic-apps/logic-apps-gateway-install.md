---
title: 온-프레미스 데이터 게이트웨이 설치 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 온-프레미스의 데이터에 액세스하려면 온-프레미스 데이터 게이트웨이를 다운로드하고 설치해야 합니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: yshoukry, LADocs
ms.topic: article
ms.date: 07/20/2018
ms.openlocfilehash: 5fc4ccacaaedfc3fe6c77fa9a0ad693530bdde93
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855428"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps에 온-프레미스 데이터 게이트웨이 설치

Azure Logic Apps에서 온-프레미스 데이터 원본에 연결하려면 로컬 컴퓨터에 온-프레미스 데이터 게이트웨이를 다운로드하고 설치합니다. 게이트웨이는 온-프레미스(클라우드 아님) 데이터 원본과 논리 앱 간에 빠른 데이터 전송 및 암호화를 제공하는 브리지 역할을 합니다. 이 문서에서는 온-프레미스 데이터 게이트웨이를 다운로드하고, 설치하고, 설정하는 방법을 설명합니다. 

Power BI, Microsoft Flow, PowerApps 및 Azure Analysis Services와 같은 기타 서비스에도 동일한 게이트웨이 설치를 사용할 수 있습니다. [데이터 게이트웨이 작동 원리](#gateway-cloud-service)에 대해 자세히 알아봅니다.

<a name="supported-connections"></a>

이 게이트웨이는 다음 데이터 원본에 대해 Azure Logic Apps의 [온-프레미스 커넥터](../connectors/apis-list.md#on-premises-connectors)를 지원합니다.

*   BizTalk Server 2016
*   파일 시스템
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle 데이터베이스
*   PostgreSQL
*   SAP 응용 프로그램 서버 
*   SAP 메시지 서버
*   SharePoint Server
*   SQL Server
*   Teradata

다른 서비스에서 게이트웨이를 사용하는 방법에 대한 내용은 다음 문서를 참조하세요.

* [Microsoft Power BI 온-프레미스 데이터 게이트웨이](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps 온-프레미스 데이터 게이트웨이](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow 온-프레미스 데이터 게이트웨이](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services 온-프레미스 데이터 게이트웨이](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>필수 조건

* [Azure 구독](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer)이 있는 [회사 또는 학교 계정](../active-directory/fundamentals/sign-up-organization.md) 

  게이트웨이 설치를 Azure 구독에 연결할 수 있도록 게이트웨이 설치 동안 이 계정에 로그인합니다. 
  나중에 Azure Portal에서 게이트웨이 설치를 위한 Azure 리소스를 만들 때도 동일한 계정을 사용합니다. 
  아직 Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* 로컬 컴퓨터에 대한 요구 사항은 다음과 같습니다.

  **최소 요구 사항**
  * .NET Framework 4.5.2
  * 64비트 버전의 Windows 7 또는 Windows Server 2008 R2 이상

  **권장 요구 사항**
  * 8 코어 CPU
  * 8GB 메모리
  * 64비트 버전의 Windows Server 2008 R2 이상

* **중요 고려 사항**

  * 온-프레미스 데이터 게이트웨이를 도메인 컨트롤러가 아닌 로컬 컴퓨터에 설치할 수 있습니다. 그렇지만 데이터 원본과 동일한 컴퓨터에 게이트웨이를 설치할 필요가 없습니다. 또한 모든 데이터 원본에 대해 하나의 게이트웨이만 필요하므로 각 데이터 원본에 대해 게이트웨이를 설치할 필요는 없습니다.

    > [!TIP]
    > 대기 시간을 최소화하려면 권한이 있다고 가정하여 가능하면 데이터 원본과 가깝게 또는 동일한 컴퓨터에 게이트웨이를 설치하면 됩니다.

  * 항상 켜져 있는 인터넷에 연결된 컴퓨터에 게이트웨이를 설치하고 절전 모드로 전환하지 *마세요*. 그렇지 않은 경우 게이트웨이를 실행할 수 없습니다. 또한 무선 네트워크에서는 성능이 저하될 수 있습니다.

  * 설치하는 동안 Microsoft 계정이 아니라 Azure Active Directory(Azure AD)에서 관리하는 [회사 또는 학교 계정](../active-directory/sign-up-organization.md)으로만 로그인할 수 있습니다. 
  또한 이 계정이 Azure B2B(게스트) 계정이 아닌지 확인합니다. 
  게이트웨이에 대해 Azure 리소스를 만들어 게이트웨이 설치를 등록할 때도 Azure Portal에서 동일한 로그인 계정을 사용해야 합니다. 
  그런 다음, 논리 앱에서 온-프레미스 데이터 원본으로의 연결을 만들 때 이 게이트웨이 리소스를 선택할 수 있습니다. 
  [Azure AD 회사 또는 학교 계정을 사용해야 하는 이유는 무엇인가요?](#why-azure-work-school-account)

  > [!TIP]
  > Office 365 서비스에 등록하고 실제 회사 이메일을 제공하지 않은 경우 로그인 주소는 예제 `username@domain.onmicrosoft.com`과 같을 수 있습니다. 
  >
  > [Visual Studio Standard 구독](https://visualstudio.microsoft.com/vs/pricing/)이 있는 Microsoft 계정을 사용하려는 경우 먼저 Microsoft 계정을 사용하여 [Azure Active Directory에서 디렉터리(테넌트)를 만들거나](../active-directory/develop/quickstart-create-new-tenant.md) 기본 디렉터리를 사용합니다. 
  > 암호가 있는 사용자를 디렉터리를 추가한 다음 해당 사용자에게 구독에 대한 액세스 권한을 제공합니다. 
  > 그런 다음 이 사용자 이름 및 암호를 사용하여 게이트웨이 설치 중에 로그인할 수 있습니다.

  * 게이트웨이 설치를 위해 선택한 지역은 나중에 Azure 리소스를 만들어 Azure에서 게이트웨이를 등록하는 위치를 결정합니다. 
  Azure에서 이 게이트웨이 리소스를 만들 때 게이트웨이 설치와 *동일한* 위치를 선택해야 합니다. 기본 지역은 Azure 계정을 관리하는 Azure AD 테넌트와 동일한 위치이지만 게이트웨이 설치하는 동안 이 위치를 변경할 수 있습니다.

  * 14.16.6317.4보다 이전 버전인 설치 관리자를 사용하여 설정한 게이트웨이가 이미 있는 경우 최신 설치 관리자를 실행하여 게이트웨이 위치를 변경할 수 없습니다. 그러나 최신 설치 관리자를 사용하여 새 게이트웨이를 원하는 위치로 설정할 수 있습니다.
  
    14.16.6317.4보다 이전 버전인 게이트웨이 설치 관리자가 있지만 게이트웨이를 아직 설치하지 않은 경우 대신, 최신 설치 관리자를 다운로드하여 사용할 수 있습니다.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>데이터 게이트웨이 설치

1. [로컬 컴퓨터에서 게이트웨이 설치 관리자를 다운로드하고 저장하고 실행합니다](https://aka.ms/on-premises-data-gateway-installer).

2. 기본 설치 경로를 그대로 적용하거나 게이트웨이를 설치하려는 컴퓨터의 위치를 지정합니다.

3. 개인정보처리방침 및 사용 약관을 검토하고 동의한 후 **설치**를 선택합니다.

   ![사용 약관 및 개인정보처리방침에 동의](./media/logic-apps-gateway-install/accept-terms.png)

4. 게이트웨이가 성공적으로 설치되면 회사 또는 학교 계정에 대한 이메일 주소를 입력하고 **로그인**을 선택합니다.

   ![회사 또는 학교 계정으로 로그인](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. **이 컴퓨터에 새 게이트웨이 등록** > **다음**를 선택하여 [게이트웨이 클라우드 서비스](#gateway-cloud-service)에 게이트웨이 설치를 등록합니다. 

   ![게이트웨이 등록](./media/logic-apps-gateway-install/register-new-gateway.png)

6. 게이트웨이 설치에 대해 다음 정보를 제공합니다.

   * 설치에 사용하려는 이름 

   * 만들려는 복구 기(8자 이상이어야 함)

     > [!IMPORTANT]
     > 복구 키를 저장한 후 안전한 장소에 보관합니다. 게이트웨이의 위치를 변경하거나, 기존 게이트웨이를 마이그레이션, 복구 또는 인수할 때 이 키가 필요합니다.

   * 복구 키 확인 

     ![게이트웨이 설정](./media/logic-apps-gateway-install/set-up-gateway.png)

7. 게이트웨이 설치에서 사용되는 게이트웨이 클라우드 서비스 및 Azure Service Bus에 대해 선택한 지역을 확인합니다. 

   ![지역 확인](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > 게이트웨이 설치를 마친 후 이 영역을 변경하려면 해당 게이트웨이 설치에 대한 복구 키가 필요합니다. 또한 게이트웨이를 제거했다가 다시 설치해야 합니다. 자세한 내용은 [위치 변경, 기존 게이트웨이 마이그레이션, 복구 또는 인수](#update-gateway-installation)를 참조하세요.

   *게이트웨이 설치에 대한 지역을 변경하는 이유는 무엇인가요?* 

   예를 들어, 대기 시간을 줄이기 위해 논리 앱과 동일한 지역으로 게이트웨이 지역을 변경할 수 있습니다. 
   또는 온-프레미스 데이터 원본에 가장 가까운 지역을 선택할 수 있습니다. 
   *Azure의 게이트웨이 리소스*와 논리 앱은 서로 다른 위치에 있을 수 있습니다.

8. 기본 지역을 그대로 사용하려면 **구성**을 선택합니다. 또는 기본 영역을 변경하려면 다음 단계를 수행합니다.

   1. 현재 지역 옆에 있는 **지역 변경**을 선택합니다. 

      ![지역 변경](./media/logic-apps-gateway-install/change-region.png)

   2. 다음 페이지에서 **지역 선택** 목록을 열고 원하는 지역을 선택한 후 **완료**를 선택합니다.

      ![다른 지역 선택](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. 확인 페이지가 나타나면 **닫기**를 선택합니다. 

   설치 관리자는 게이트웨이가 현재 온라인 상태이고 사용할 준비가 되었는지 확인합니다.

   ![완료된 게이트웨이](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. 이제 [게이트웨이 설치에 대한 Azure 리소스를 만들어](../logic-apps/logic-apps-gateway-connection.md) Azure에서 게이트웨이를 등록합니다. 

## <a name="enable-high-availability"></a>고가용성 사용

둘 이상의 게이트웨이 설치를 사용하며 이러한 게이트웨이를 클러스터로 설정하면 온-프레미스 데이터 게이트웨이는 고가용성을 지원합니다. 다른 게이트웨이 만들려고 할 때 기존 게이트웨이가 있는 경우 필요에 따라 고가용성 클러스터를 만들 수 있습니다. 이러한 클러스터는 게이트웨이를 단일 실패 지점을 방지하는 데 도움이 되는 그룹으로 구성합니다. 이 기능을 사용하려면 다음 요구 사항 및 고려 사항을 검토합니다.

* 일부 커넥터만 파일 시스템 커넥터 등과 같은 고가용성 시나리오를 지원합니다. 
     
* 해당 설치에 대한 기본 게이트웨이 및 복구 키가 있는 동일한 Azure 구독 내에 하나 이상의 게이트웨이 설치가 이미 있어야 합니다. 

* 기본 게이트웨이에서는 2017년 11월 또는 이후의 게이트웨이 업데이트가 실행되고 있어야 합니다.

이러한 요구 사항을 충족한 후, 다음 게이트웨이 만들 때 **기존 게이트웨이 클러스터에 추가**를 선택하고 클러스터에 대한 기본 게이트웨이를 선택한 후 해당 기본 게이트웨이에 대한 복구 키를 제공합니다.
자세한 내용은 참조 [온-프레미스 데이터 게이트웨이에 대한 고가용성 클러스터](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters)를 참조하세요.

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>위치 변경, 기존 게이트웨이 마이그레이션, 복원 또는 인수

게이트웨이 위치를 변경하거나, 게이트웨이 설치를 새 컴퓨터로 이동하거나, 손상된 게이트웨이를 복구하거나, 기존 게이트웨이에 대한 소유권을 가져와야 할 경우 게이트웨이 설치 동안 제공된 복구 키가 필요합니다. 이 작업을 수행하면 이전 게이트웨이의 연결이 끊어집니다.

1. 컴퓨터의 **제어판**에서 **프로그램 및 기능**으로 이동합니다. 프로그램 목록에서 **온-프레미스 데이터 게이트웨이**를 선택한 후 **제거**를 선택합니다.

2. [온-프레미스 데이터 게이트웨이 다시 설치](https://aka.ms/on-premises-data-gateway-installer).

3. 설치 관리자가 열리면 이전에 게이트웨이 설치에 사용한 것과 동일한 회사 또는 학교 계정으로 로그인합니다.

4. **Migrate, restore, or takeover an existing gateway**(기존 게이트웨이 마이그레이션, 복원 또는 인수)를 선택한 후 **다음**을 선택합니다.

   ![Migrate, restore, or takeover an existing gateway(기존 게이트웨이 마이그레이션, 복원 또는 인수) 선택](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. **사용 가능한 게이트웨이** 또는 **사용 가능한 게이트웨이 클러스터** 아래에서 변경하려는 게이트웨이 설치를 선택합니다. 게이트웨이 설치에 대한 복구 키를 입력합니다. 

   ![기본 게이트웨이 선택](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. 지역을 변경하려면 **지역 변경** 및 새 영역을 선택합니다.

7. 작업을 완료하면 **구성**을 선택합니다.

## <a name="configure-proxy-or-firewall"></a>프록시 또는 방화벽 구성

온-프레미스 데이터 게이트웨이는 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)에 대한 아웃바운드 연결을 만듭니다. 작업 환경에서 트래픽이 인터넷에 액세스하기 위해 프록시를 통과해야 할 경우 이러한 제한 때문에 데이터 게이트웨이가 게이트웨이 클라우드 서비스에 연결하지 못할 수 있습니다. 네트워크가 프록시를 사용하고 있는지 여부를 확인하려면 superuser.com에서 다음 문서를 검토하세요. 

[How do I know what proxy server I'm using?(사용 중인 프록시 서버를 확인하는 방법) (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

게이트웨이에 대한 프록시 정보를 제공하려면 [프록시 설정 구성](https://docs.microsoft.com/power-bi/service-gateway-proxy)을 참조하세요. 프록시 또는 방화벽이 연결을 차단할 수 있는지를 확인하려면 컴퓨터가 인터넷 및 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)에 실제로 연결할 수 있는지 여부를 확인합니다. PowerShell 창에서 다음 명령을 실행합니다.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> 이 명령은 Azure Service Bus에 대한 네트워크 연결 및 연결만을 테스트합니다. 이 명령은 게이트웨이 또는 게이트웨이 클라우드 서비스에서 자격 증명 및 게이트웨이 세부 정보를 암호화하고 저장하는 작업을 수행하는 것과 아무 관련이 없습니다. 
>
> 또한 이 명령은 Windows Server 2012 R2 이상 및 Windows 8.1 이상에서만 사용할 수 있습니다. 이전 OS 버전에서 연결을 테스트하는 데 텔넷을 사용할 수 있습니다. [Azure Service Bus 및 하이브리드 솔루션](../service-bus-messaging/service-bus-messaging-overview.md)에 대해 자세히 알아봅니다.

결과는 **TcpTestSucceeded**가 **True**로 설정된 다음 예제와 비슷하게 표시됩니다.

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

**TcpTestSucceeded**가 **True**로 설정되지 않으면 게이트웨이는 방화벽에 의해 차단될 수 있습니다. 포괄적으로 설정하려면 **ComputerName** 및 **포트** 값을 이 문서의 [포트 구성](#configure-ports) 아래에 나열된 값으로 바꿉니다.

또한 방화벽이 Azure Service Bus와 Azure 데이터 센터 간에 형성된 연결을 차단할 수도 있습니다. 이 시나리오를 실행할 경우 지역에서 해당 데이터 센터에 대한 모든 IP 주소를 승인(차단 해제)합니다. 해당 IP 주소는 [여기에서 Azure IP 주소 목록을 가져옵니다](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>포트 구성

게이트웨이는 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)에 대한 아웃바운드 연결을 만들고 아웃바운드 포트인 TCP 443(기본값), 5671, 5672, 9350~9354에서 통신합니다. 게이트웨이에 인바운드 포트가 필요하지 않습니다. [Azure Service Bus 및 하이브리드 솔루션](../service-bus-messaging/service-bus-messaging-overview.md)에 대해 자세히 알아봅니다.

게이트웨이는 다음과 같은 정규화된 도메인 이름을 사용합니다.

| 도메인 이름 | 아웃바운드 포트 | 설명 | 
| ------------ | -------------- | ----------- | 
| *.analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | 구성에 따라 인증에 사용합니다. | 
| *.msftncsi.com | 443 | Power BI 서비스에서 게이트웨이에 연결할 수 없는 경우 인터넷 연결을 테스트하는 데 사용합니다. | 
| \*.servicebus.windows.net | 443, 9350-9354 | TCP의 Service Bus Relay에 대한 수신기(Access Control 토큰 획득에 443 필요) | 
| \*.servicebus.windows.net | 5671-5672 | AMQP(고급 메시지 큐 프로토콜) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

일부 경우에는 정규화된 도메인 이름이 아닌 IP 주소를 사용해서 Azure Service Bus를 연결합니다. 따라서 방화벽에서 데이터 지역에 대한 IP 주소를 허용 목록에 추가하려고 할 수 있습니다. 도메인 대신 IP 주소를 허용 목록에 추가하려면 [Microsoft Azure 데이터 센터 IP 범위 목록](https://www.microsoft.com/download/details.aspx?id=41653)을 다운로드하여 사용할 수 있습니다. 이 목록의 IP 주소는 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법을 따릅니다.

### <a name="force-https-communication-with-azure-service-bus"></a>Azure Service Bus와의 HTTPS 통신 강제 적용

일부 프록시는 포트 80 및 443으로의 트래픽만 허용합니다. 기본적으로 Azure Service Bus와의 통신은 443 이외의 포트에서 발생합니다.
강제로 게이트웨이가 직접 TCP 대신 HTTPS를 통해 Azure Service Bus와 통신하도록 할 수 있지만 이렇게 하면 성능이 크게 저하될 수 있습니다. 이 작업을 수행하려면 다음 단계를 따릅니다.

1. 일반적으로 ```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```에서 찾을 수 있는 온-프레미스 데이터 게이트웨이 클라이언트에 대한 위치로 이동합니다.

   그렇지 않고 클라이언트 위치를 찾으려면 동일한 컴퓨터에서 서비스 콘솔을 열고, **온-프레미스 데이터 게이트웨이 서비스**를 찾은 후 **실행 파일 경로** 속성을 확인합니다.

2. *구성* 파일 **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**를 엽니다.

3. **ServiceBusSystemConnectivityModeString** 값을 **AutoDetect**에서 **Https**로 변경합니다.

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows 서비스 계정

온-프레미스 데이터 게이트웨이는 "온-프레미스 데이터 게이트웨이 서비스"라는 Windows 서비스로 실행되지만 해당 "다음 계정으로 로그온" 계정 자격 증명으로 "NT SERVICE\PBIEgwService"를 사용합니다. 기본적으로 온-프레미스 데이터 게이트웨이에는 게이트웨이를 설치하는 컴퓨터에 대한 "서비스로 로그온" 권한이 있습니다. Azure Portal에서 게이트웨이를 만들고 유지 관리하려면 Windows 서비스 계정에 적어도 **참가자** 권한이 있어야 합니다. 

> [!NOTE]
> Windows 서비스 계정은 온-프레미스 데이터 원본에 연결하는 데 사용되는 동일한 계정 및 클라우드 서비스에 로그인하는 데 사용되는 회사 또는 학교 계정과 다릅니다.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>게이트웨이 다시 시작

데이터 게이트웨이는 Windows 서비스로 실행되므로 다른 Windows 서비스와 마찬가지로 게이트웨이를 여러 가지 방법으로 시작하고 중지할 수 있습니다. 예를 들어, 게이트웨이가 실행 중인 컴퓨터에서 관리자 권한으로 명령 프롬프트를 열고, 다음 명령 중 하나를 실행할 수 있습니다.

* 서비스를 중지하려면 다음 명령을 실행합니다.
  
  `net stop PBIEgwService`

* 서비스를 시작하려면 이 명령을 실행 합니다.
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>테넌트 수준 관리 

현재, 테넌트 관리자가 다른 사용자가 설치 및 구성한 모든 게이트웨이를 관리할 수 있는 단일 위치가 없습니다. 테넌트 관리자인 경우 조직의 사용자에게 설치한 모든 게이트웨이에 대한 관리자로 추가해줄 것을 요청할 수 있습니다. 이와 같이 게이트웨이 설정 페이지 또는 [PowerShell 명령](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters)을 통해 조직에서 모든 게이트웨이를 관리할 수 있습니다. 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>게이트웨이는 어떻게 작동하나요?

데이터 게이트웨이는 논리 앱, 게이트웨이 클라우드 서비스 및 온-프레미스 데이터 원본 간의 빠르고 안전한 통신을 지원합니다. 게이트웨이 클라우드 서비스는 데이터 원본 자격 증명 및 게이트웨이 세부 정보를 암호화하고 저장합니다. 또한 서비스는 논리 앱, 온-프레미스 데이터 게이트웨이 및 온-프레미스 데이터 원본 간에 쿼리 및 해당 결과를 라우팅합니다. 

게이트웨이는 방화벽과 함께 작동하며 아웃바운드 연결만 사용합니다. 보안으로 시작하는 모든 트래픽은 게이트웨이 에이전트에서 트래픽을 아웃바운드합니다. 게이트웨이는 Azure Service Bus를 통해 암호화된 채널의 온-프레미스 원본에서 데이터를 릴레이합니다. 이 Service Bus는 게이트웨이와 호출 서비스 간에 채널을 만들지만 데이터를 저장하지는 않습니다. 게이트웨이를 통해 전송되는 모든 데이터는 암호화됩니다.

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

다음 단계에서는 클라우드의 사용자가 온-프레미스 데이터 원본에 연결된 요소와 상호 작용할 때 나타나는 결과를 설명합니다.

1. 게이트웨이 클라우드 서비스가 데이터 원본에 대해 암호화된 자격 증명과 함께 쿼리를 만들고, 게이트웨이가 처리할 수 있게 쿼리를 큐에 전송합니다.

2. 게이트웨이 클라우드 서비스에서 쿼리를 분석하고 Azure Service Bus에 대한 요청을 푸시합니다.

3. 온-프레미스 데이터 게이트웨이가 보류 중인 요청을 위해 Azure Service Bus를 폴링합니다.

4. 게이트웨이가 쿼리를 가져오고, 자격 증명의 암호를 해독하고, 해당 자격 증명으로 데이터 원본에 연결합니다.

5. 게이트웨이가 실행을 위해 데이터 원본에 쿼리를 보냅니다.

6. 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후 게이트웨이 클라우드 서비스로 전송됩니다. 게이트웨이 클라우드 서비스에서 해당 결과를 사용합니다.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="general"></a>일반

**Q**: Azure SQL Database와 같은 클라우드에서 데이터 원본에 대한 게이트웨이가 필요하나요? <br/>
**A**: 아니요. 게이트웨이는 온-프레미스 데이터 원본에만 연결됩니다.

**Q**: 데이터 원본과 동일한 컴퓨터에 게이트웨이를 설치해야 하나요? <br/>
**A**: 아니요. 게이트웨이는 제공된 연결 정보를 사용하여 데이터 원본에 연결됩니다. 이러한 의미에서 게이트웨이를 클라이언트 응용 프로그램이라고 생각할 수 있습니다. 게이트웨이는 제공된 서버 이름에 연결할 수 있는 기능이 필요합니다.

<a name="why-azure-work-school-account"></a>

**Q:**: 회사 또는 학교 계정을 사용하여 로그인해야 하는 이유는 무엇인가요? <br/>
**A**: 온-프레미스 데이터 게이트웨이를 설치할 때만 회사 또는 학교 계정을 사용할 수 있습니다. 로그인 계정은 Azure AD(Azure Active Directory)에서 관리하는 테넌트에 저장됩니다. 일반적으로 Azure AD 계정의 UPN(사용자 계정 이름)은 이메일 주소와 일치합니다.

**Q**: 자격 증명은 어디에 저장되나요? <br/>
**A**: 데이터 원본에 입력한 자격 증명은 게이트웨이 클라우드 서비스에 암호화되어 저장됩니다. 자격 증명은 온-프레미스 데이터 게이트웨이에서 해독됩니다.

**Q**: 네트워크 대역폭에 대한 요구 사항이 있나요? <br/>
**A**: 네트워크 연결의 처리량이 높은지 확인합니다. 모든 환경은 다르며 전송되는 데이터의 양은 결과에 영향을 미칠 수 있습니다. 온-프레미스 데이터 원본과 Azure 데이터 센터 간의 처리량 수준을 보장하려면 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 사용해보세요. 처리량 측정하기 위해 Azure Speed Test와 같은 외부 도구를 사용합니다.

**Q**: 게이트웨이에서 데이터 원본으로의 쿼리를 실행할 때 나타나는 대기 시간은 얼마나 되나요? 최선의 아키텍처는 무엇인가요? <br/>
**A**: 네트워크 대기 시간을 줄이려면 게이트웨이를 데이터 원본에 최대한 가깝게 설치합니다. 실제 데이터 원본에 게이트웨이를 설치할 수 있는 경우 이 근접성으로 인해 발생하는 대기 시간이 최소화됩니다. 또한 Azure 데이터 센터와의 근접성을 고려합니다. 예를 들어, 서비스에서 미국 서부 데이터 센터를 사용하고 Azure VM에서 SQL Server가 호스트되는 경우 Azure VM도 미국 서부 지역에 있는 것이 좋을 수 있습니다. 이 근접성으로 인해 대기 시간이 최소화되고 Azure VM에서 송신 요금이 발생하지 않습니다.

**Q**: 결과가 클라우드로 어떻게 다시 전송되나요? <br/>
**A**: Azure Service Bus를 통해 결과가 전송됩니다.

**Q**: 클라우드에서 게이트웨이로의 인바운드 연결이 있나요? <br/>
**A**: 아니요. 게이트웨이는 Azure Service Bus에 대해 아웃바운드 연결을 사용합니다.

**Q**: 아웃바운드 연결을 차단하면 어떻게 되나요? 이러한 연결을 열려면 무엇이 필요한가요? <br/>
**A**: 게이트웨이가 사용하는 포트 및 호스트를 참조하세요.

**Q**: 실제 Windows 서비스를 어떻게 지칭하나요? <br/>
**A**: 작업 관리자의 서비스 탭에서 서비스 이름이 "PBIEgwService" 또는 Power BI Enterprise Gateway Service로 표시됩니다. 서비스 콘솔에서는 서비스 이름이 "On-premises data gateway service"입니다. Windows 서비스는 "NT SERVICE\PBIEgwService"를 SSID(서비스 SID)로 사용합니다.

**Q**: Azure Active Directory 계정으로 게이트웨이 Windows 서비스를 실행할 수 있나요? <br/>
**A**: 아니요. Windows 서비스에는 유효한 Windows 계정이 있어야 합니다.

### <a name="disaster-recovery"></a>재해 복구

**Q**: 재해 복구를 위해 어떤 옵션을 사용할 수 있나요? <br/>
**A**: 복구 키를 사용하여 게이트웨이를 복원하거나 이동할 수 있습니다. 게이트웨이를 설치할 때 복구 키를 지정합니다.

**Q**: 복구 키를 사용할 경우의 이점은 무엇인가요? <br/>
**A**: 복구 키는 재해 발생 후 게이트웨이 설정을 마이그레이션하거나 복구할 수 있는 방법을 제공합니다.

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 온-프레미스 데이터 게이트웨이를 설정 및 사용하는 동안 발생할 수 있는 몇 가지 일반적인 문제에 대해 설명합니다.

**Q:**: 내 게이트웨이 설치가 실패한 이유는 무엇인가요? <br/>
**A**: 대상 컴퓨터의 바이러스 백신 소프트웨어가 오래된 경우 이 문제가 발생할 수 있습니다. 바이러스 백신 소프트웨어를 업데이트하거나 게이트웨이 설치 중에만 바이러스 백신 소프트웨어를 사용하지 않도록 설정했다가 다시 사용하도록 설정할 수 있습니다.

**Q:**: Azure에서 게이트웨이 리소스를 만들 때 게이트웨이 설치가 보이지 않는 이유는 무엇인가요? <br/>
**A**: 이 문제는 다음과 같은 이유 때문에 발생할 수 있습니다.

* 게이트웨이 설치가 Azure의 다른 게이트웨이 리소스에 의해 이미 등록되고 클레임되었습니다. 게이트웨이 설치는 게이트웨이 리소스가 만들어진 후에 인스턴스 목록에 나타나지 않습니다.
Azure Portal에서 게이트웨이 등록을 확인하려면 *모든* Azure 구독에 대해 **온-프레미스 데이터 게이트웨이** 유형을 사용하여 모든 Azure 리소스를 검토합니다. 

* 게이트웨이를 설치한 사용자의 Azure AD ID가 Azure Portal에 로그인한 사용자의 ID와 다릅니다. 게이트웨이를 설치할 때 사용한 것과 동일한 ID로 로그인되어 있는지 확인합니다.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: 게이트웨이 로그는 어디에 있나요? <br/>
**A**: 이 문서의 뒷부분에 나오는 [**로그** 섹션](#logs)을 참조하세요.

**Q**: 온-프레미스 데이터 원본으로 어떤 쿼리가 전송되고 있는지를 어떻게 알 수 있나요? <br/>
**A**: 전송 중인 쿼리를 포함하는 쿼리 추적을 사용하도록 설정할 수 있습니다. 문제 해결이 끝나면 쿼리 추적을 원래 값으로 다시 변경해야 합니다. 쿼리 추적 기능을 그대로 두면 큰 로그가 만들어집니다.

또한 추적 쿼리를 위해 데이터 원본이 포함하는 도구를 살펴볼 수도 있습니다. 예를 들어 SQL Server 및 Analysis Services의 경우 확장 이벤트 또는 SQL 프로파일러를 사용할 수 있습니다.

### <a name="outdated-gateway-version"></a>만료된 게이트웨이 버전

게이트웨이 버전이 오래되면 여러 가지 문제가 발생할 수 있습니다. 일반적으로는 최신 버전을 사용해야 합니다. 한 달 동안 게이트웨이를 업데이트하지 않은 경우 최신 버전의 게이트웨이를 설치하고 문제가 재현되는지 확인할 수 있습니다.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>오류: 그룹에 사용자를 추가하지 못했습니다. (-2147463168 PBIEgwService 성능 로그 사용자)

지원되지 않는 도메인 컨트롤러에 게이트웨이 설치하려고 하면 이 오류가 표시될 수 있습니다. 도메인 컨트롤러가 아닌 컴퓨터에 게이트웨이를 배포해야 합니다.

<a name="logs"></a>

### <a name="logs"></a>로그

이 문제를 해결하는 데 도움을 얻으려면 먼저 게이트웨이 로그를 수집 및 검토합니다. 로그를 수집하는 방법에는 몇 가지가 있지만 게이트웨이를 설치한 이후에 사용할 수 있는 가장 간단한 옵션은 게이트웨이 설치 관리자의 사용자 인터페이스를 사용하는 것입니다. 

1. 컴퓨터에서 온-프레미스 데이터 게이트웨이 설치 관리자를 엽니다.
2. 왼쪽 메뉴에서 **진단**을 선택합니다.
3. **게이트웨이 로그** 아래에서 **로그 내보내기**를 선택합니다.

   ![게이트웨이 설치 관리자에서 로그 내보내기](./media/logic-apps-gateway-install/export-logs.png)

다양한 로그를 찾을 수 있는 다른 위치는 다음과 같습니다.

| 로그 형식 | 위치 | 
|----------|----------| 
| **설치 관리자 로그** | %localappdata%\Temp\On-premises_data_gateway_<*yyyymmdd*>.<*number*>.log | 
| **구성 로그** | C:\Users\<*username*>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator<*yyyymmdd*>.<*number*>.log | 
| **엔터프라이즈 게이트웨이 서비스 로그** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway<*yyyymmdd*>.<*number*>.log | 
||| 

**이벤트 로그**

게이트웨이에 대한 이벤트 로그를 찾으려면 다음 단계를 수행합니다.

1. 게이트웨이가 설치된 컴퓨터에서 **이벤트 뷰어**를 엽니다. 
2. **이벤트 뷰어(로컬)** > **응용 프로그램 및 서비스 로그**를 확장합니다. 
3. **온-프레미스 데이터 게이트웨이 서비스**를 선택합니다.

   ![게이트웨이에 대한 이벤트 로그 보기](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="telemetry"></a>원격 분석

추가 모니터링 및 문제 해결을 위해 원격 분석을 켜고 수집할 수 있습니다. 

1. 일반적으로 ```C:\Program Files\On-premises data gateway```에서 찾을 수 있는 온-프레미스 데이터 게이트웨이 클라이언트에 대한 위치로 이동합니다.

   그렇지 않고 클라이언트 위치를 찾으려면 동일한 컴퓨터에서 서비스 콘솔을 열고, **온-프레미스 데이터 게이트웨이 서비스**를 찾은 후 **실행 파일 경로** 속성을 확인합니다.

2. *구성* 파일 **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**를 엽니다.

3. **SendTelemetry** 값을 **true**로 변경합니다.

   ```html
   <setting name="SendTelemetry" serializeAs="String">
      <value>true</value>
   </setting>
   ```

4. 변경 내용을 저장하고 Windows 서비스를 다시 시작합니다.

### <a name="review-slow-query-performance"></a>느린 쿼리 성능 검토

쿼리가 게이트웨이를 통해 느리게 실행되는 것으로 확인되면 쿼리 및 해당 기간을 출력하는 추가 로깅을 켤 수 있습니다. 이러한 로그는 느리거나 장기 실행 중인 쿼리를 찾는 데 도움이 될 수 있습니다. 쿼리 성능을 조정하기 위해 데이터 원본을 수정해야 할 수 있습니다(예: SQL Server 쿼리에 대한 인덱스 조정).

쿼리 기간을 확인하려면 다음 단계를 수행합니다.

1. 일반적으로 ```C:\Program Files\On-premises data gateway```에서 찾을 수 있는 게이트웨이 클라이언트와 동일한 위치로 이동합니다.

   그렇지 않고 클라이언트 위치를 찾으려면 동일한 컴퓨터에서 서비스 콘솔을 열고, **온-프레미스 데이터 게이트웨이 서비스**를 찾은 후 **실행 파일 경로** 속성을 확인합니다.

2. 다음 설명대로 이러한 구성 파일을 열어서 편집합니다.

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     이 파일에서 **EmitQueryTraces** 값을 **false**에서 **true**로 변경하여 게이트웨이가 게이트웨이에서 데이터 원본으로 전송된 쿼리를 로드할 수 있도록 합니다.

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > EmitQueryTraces 설정을 켜면 게이트웨이 사용량에 따라 로그 크기를 상당히 커질 수 있습니다. 로그 검토를 마친 후에는 오랫동안 이 설정을 그대로 두지 말고 EmitQueryTraces를 **false**로 다시 설정해야 합니다.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     게이트웨이가 기간을 표시하는 항목을 포함하여 자세한 정보를 표시하도록 하려면 다음 단계를 수행하여 **TracingVerbosity** 값을 **4**에서 **5**로 변경합니다. 

     * 이 구성 파일에서 **TracingVerbosity** 값을 **4**에서 **5**로 변경합니다. 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * 게이트웨이 설치 관리자를 열고 **진단**을 선택한 후 **추가 로깅**을 켜고 **적용**을 선택합니다.

       ![추가 로깅 켜기](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > TracingVerbosity 설정을 켜면 게이트웨이 사용량에 따라 로그 크기를 상당히 커질 수 있습니다. 로그 검토를 마친 후에는 오랫동안 이 설정을 그대로 두지 말고 게이트웨이 설치 관리자에서 **Additional logging**을 끄거나 TracingVerbosity를 **4**로 다시 설정해야 합니다.

3. 쿼리 기간을 확인하려면 다음 단계를 수행합니다.

   1. 게이트웨이 로그를 [내보낸 후](#logs) 엽니다.

   2. 쿼리를 찾으려면 다음과 같은 활동 유형을 검색합니다. 

      | 활동 유형 | 설명 | 
      |---------------|-------------| 
      | MGEQ | ADO.NET을 통해 실행되는 쿼리입니다. | 
      | MGEO | OLEDB를 통해 실행되는 쿼리입니다. | 
      | MGEM | 매시업 엔진에서 실행되는 쿼리입니다. | 
      ||| 

   3. 요청 ID에 해당하는 두 번째 GUID를 적어둡니다.

   4. 기간(밀리초)이 정해진 "FireActivityCompletedSuccessfullyEvent"라는 항목을 찾을 때까지 활동 유형을 계속 검색합니다. 
   항목이 동일한 요청 ID를 갖는지 확인합니다. 예를 들면 다음과 같습니다.

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > "FireActivityCompletedSuccessfullyEvent" 항목은 자세한 정보 표시 항목으로, "TracingVerbosity" 설정이 수준 5가 아니면 로깅되지 않습니다.

### <a name="trace-traffic-with-fiddler"></a>Fiddler 사용하여 트래픽 추적

[Fiddler](http://www.telerik.com/fiddler) 는 HTTP 트래픽을 모니터링하는 Telerik의 무료 도구입니다. 클라이언트 컴퓨터에서 Power BI를 사용하여 트래픽을 검토할 수 있습니다. 이 서비스는 오류 및 기타 관련된 정보를 표시할 수 있습니다.

## <a name="next-steps"></a>다음 단계
    
* [논리 앱에서 온-프레미스 데이터에 연결](../logic-apps/logic-apps-gateway-connection.md)
* [엔터프라이즈 통합 기능](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps용 커넥터](../connectors/apis-list.md)
