---
title: 세션 선호도 문제 해결
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure 응용 프로그램 게이트웨이에서 세션 선호도 문제를 해결하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: aa3617b30fe1ef9b4d4a6c5fe5aac51bff95bb92
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866684"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Azure 응용 프로그램 게이트웨이 세션 선호도 문제 해결

Azure 응용 프로그램 게이트웨이를 사용하여 세션 선호도 문제를 진단하고 해결하는 방법을 알아봅니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

쿠키 기반 세션 선호도 기능은 동일한 서버에서 사용자 세션을 유지하려는 경우에 유용합니다. Application Gateway는 게이트웨이 관리형 쿠키를 사용하여 사용자 세션에서 동일한 서버에 후속 트래픽을 처리하도록 지시할 수 있습니다. 이는 세션 상태가 사용자 세션의 서버에 로컬로 저장된 경우에 특히 중요합니다.

## <a name="possible-problem-causes"></a>가능한 문제 원인

쿠키 기반 세션 선호도를 유지하는 데 따르는 문제는 다음과 같은 주요 원인으로 인해 발생할 수 있습니다.

- "쿠키 기반 선호도" 설정이 사용 되지 않습니다.
- 응용 프로그램이 쿠키 기반 선호도를 처리할 수 없음
- 응용 프로그램이 쿠키 기반 선호도를 사용하고 있지만 백 엔드 서버 간에 요청이 계속 수신 거부됩니다.

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>"쿠키 기반 선호도" 설정이 활성화되어 있는지 확인

"쿠키 기반 선호도" 설정을 사용하도록 설정하는 것을 잊어버린 경우 세션 선호도 문제가 발생할 수 있습니다. Azure 포털의 HTTP 설정 탭에서 "쿠키 기반 선호도" 설정을 사용하도록 설정했는지 확인하려면 지침을 따르십시오.

1. [Azure 포털](https://portal.azure.com/)에 로그온합니다.

2. 왼쪽 **탐색** 창에서 **모든 리소스를**클릭합니다. 모든 리소스 블레이드에서 응용 프로그램 게이트웨이 이름을 클릭합니다. 선택한 구독에 이미 여러 리소스가 있는 경우 **이름으로 필터에** 응용 프로그램 게이트웨이 이름을 입력할 수 있습니다. 애플리케이션 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

3. **설정**에서 **HTTP 설정** 탭을 선택합니다.

   ![문제 해결 세션 -선호도 문제-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 오른쪽에 있는 **appGatewayBackendHttpSettings을** 클릭하여 쿠키 기반 선호도에 대해 **사용하도록 선택했는지** 확인합니다.

   ![문제 해결 세션 - 선호도 문제 -2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



다음 방법 중 하나를 사용하여 **"CookieBasedAffinity"** 값이 **"backendHttpSettingsCollection"** 아래에서 *사용하도록*설정되어 있는지 확인할 수도 있습니다.

- 파워쉘에서 [Get-AzApplication게이트웨이백설정](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) 실행
- Azure 리소스 관리자 템플릿을 사용하여 JSON 파일을 살펴봅니다.

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>응용 프로그램이 쿠키 기반 선호도를 처리할 수 없습니다.

#### <a name="cause"></a>원인

응용 프로그램 게이트웨이는 쿠키를 사용하여 세션 기반 선호도를 수행할 수 있습니다.

#### <a name="workaround"></a>해결 방법

응용 프로그램이 쿠키 기반 선호도를 처리할 수 없는 경우 외부 또는 내부 Azure 로드 밸런서 또는 다른 타사 솔루션을 사용해야 합니다.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>응용 프로그램이 쿠키 기반 선호도를 사용하고 있지만 백 엔드 서버 간에 요청이 계속 수신 거부됩니다.

#### <a name="symptom"></a>증상

예를 들어 [http://website](http://website/) Internet Explorer에서 짧은 이름 URL을 사용하여 응용 프로그램 게이트웨이에 액세스할 때 쿠키 기반 선호도 설정을 사용하도록 설정했습니다.

이 문제를 식별하려면 지침을 따르십시오.

1. 응용 프로그램 게이트웨이 뒤에 있는 응용 프로그램에 연결하는 "클라이언트"에서 웹 디버거 추적을 수행합니다(이 예제에서는 Fiddler를 사용하고 있습니다).
    **팁** Fiddler를 사용하는 방법을 모르는 경우 아래쪽에있는 웹 디버거 "를**사용하여 네트워크 트래픽을 수집하고 분석할**수있는 옵션 "을 선택하십시오.

2. 세션 로그를 확인하고 분석하여 클라이언트가 제공하는 쿠키에 ARRAffinity 세부 정보가 있는지 확인합니다. 쿠키 집합 내에서 **"ARRAffinity=** *ARRAffinityValue"와 같은 ARRAffinity*세부 정보를 찾지 못하면 클라이언트가 응용 프로그램 게이트웨이에서 제공하는 ARRA 쿠키로 회신하지 않음을 의미합니다.
    예를 들어:

    ![문제 해결 세션 - 선호도 문제-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![문제 해결 세션 - 선호도 문제-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

응용 프로그램은 응답을 받을 때까지 각 요청에 쿠키를 설정하려고 계속합니다.

#### <a name="cause"></a>원인

이 문제는 Internet Explorer 및 기타 브라우저에서 짧은 이름 URL로 쿠키를 저장하거나 사용할 수 없기 때문에 발생합니다.

#### <a name="resolution"></a>해결 방법

이 이슈를 해결하려면 FQDN을 사용하여 Application Gateway에 액세스해야 합니다. 예를 들어, [http://website.com](https://website.com/) [http://appgw.website.com](http://website.com/) 또는 를 사용하거나 .

## <a name="additional-logs-to-troubleshoot"></a>문제 해결을 위한 추가 로그

추가 로그를 수집하고 분석하여 쿠키 기반 세션 선호도와 관련된 문제를 해결할 수 있습니다.

### <a name="analyze-application-gateway-logs"></a>애플리케이션 게이트웨이 로그 분석

응용 프로그램 게이트웨이 로그를 수집하려면 다음 지침을 따르십시오.

Azure Portal을 통한 로깅 사용

1. Azure [포털에서](https://portal.azure.com/)리소스를 찾은 다음 **진단 로그를 클릭합니다.**

   응용 프로그램 게이트웨이의 경우 액세스 로그, 성능 로그, 방화벽 로그 등 세 개의 로그를 사용할 수 있습니다.

2. 데이터 수집을 시작하려면 **진단 설정 을 클릭합니다.**

   ![문제 해결 세션 -선호도 문제-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. **진단 설정** 블레이드에서는 진단 로그에 대한 설정을 제공합니다. 이 예제에서 Log Analytics는 로그를 저장합니다. **로그 분석** 아래에서 **구성을** 클릭하여 작업 영역을 설정합니다. 또한 이벤트 허브 및 스토리지 계정을 사용하여 진단 로그를 저장할 수도 있습니다.

   ![문제 해결 세션 - 선호도 문제-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 설정을 확인한 다음 **저장을**클릭합니다.

   ![문제 해결 세션 -선호도 문제-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>애플리케이션 게이트웨이 액세스 로그 보기 및 분석

1. 응용 프로그램 게이트웨이 리소스 보기 아래의 Azure 포털에서 **모니터링** 섹션에서 **진단 로그를 선택합니다.**

   ![문제 해결 세션 - 선호도 문제-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 오른쪽에 있는 "응용 프로그램 게이트웨이AccessLog" **로그 범주** 아래의 드롭다운 목록에서 **"응용 프로그램 게이트웨이액세스 로그"를**선택합니다.  

   ![문제 해결 세션 -선호도 문제-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. 응용 프로그램 게이트웨이 액세스 로그 목록에서 분석 및 내보낼 로그를 클릭한 다음 JSON 파일을 내보냅니다.

4. 3단계에서 내보낸 JSON 파일을 CSV 파일로 변환하고 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

5. 다음 데이터를 확인합니다.

- **ClientIP**- 연결 클라이언트의 클라이언트 IP 주소입니다.
- **ClientPort** - 요청에 대한 연결 클라이언트의 소스 포트입니다.
- **RequestQuery** - 요청이 수신된 대상 서버를 나타냅니다.
- **서버 라우팅**: 요청이 수신된 백 엔드 풀 인스턴스입니다.
- **X-AzureApplicationGateway-LOG-ID**: 요청에 사용된 상관 관계 ID입니다. 백 엔드 서버에서 트래픽 문제를 해결하는 데 사용할 수 있습니다. 예: X-AzureApplication게이트웨이-캐시-히트=0&서버 라우팅=10.0.2.4.

  - **SERVER-STATUS**: Application Gateway에서 백 엔드로부터 받은 HTTP 응답 코드

  ![문제 해결 세션 - 선호도 문제-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

두 항목이 동일한 ClientIP 및 클라이언트 포트에서 오고 있고 동일한 백 엔드 서버로 전송되는 경우 이는 응용 프로그램 게이트웨이가 올바르게 구성된다는 것을 의미합니다.

두 항목이 동일한 ClientIP 및 Client Port에서 오고 있고 다른 백 엔드 서버로 전송되는 경우, 이는 요청이 백 엔드 서버 간에 수신 거부된다는 것을 의미하며,**"응용 프로그램은 쿠키 기반 선호도를 사용하고 있지만 요청은 여전히 백 엔드 서버 간에 바운스되는**경우" 하단에서 문제를 해결합니다.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>웹 디버거를 사용하여 HTTP 또는 HTTPS 트래픽을 캡처하고 분석합니다.

Fiddler와 같은 웹 디버깅 도구는 인터넷과 테스트 컴퓨터 간의 네트워크 트래픽을 캡처하여 웹 응용 프로그램을 디버깅하는 데 도움이 될 수 있습니다. 이러한 도구를 사용하면 브라우저가 수신/전송할 때 들어오는 데이터와 나가는 데이터를 검사할 수 있습니다. 이 예제에서는 특히 인증 종류의 문제에 대해 웹 응용 프로그램과 관련하여 클라이언트 측 문제를 해결하는 데 도움이 되는 HTTP 재생 옵션이 있습니다.

선택한 웹 디버거를 사용합니다. 이 샘플에서는 Fiddler를 사용하여 http 또는 https 트래픽을 캡처하고 분석하고 지침을 따릅니다.

1. 에서 <https://www.telerik.com/download/fiddler>피들러 도구를 다운로드합니다.

    > [!NOTE]
    > 캡처 컴퓨터에 .NET 4가 설치되어 있는 경우 Fiddler4를 선택합니다. 그렇지 않으면 Fiddler2를 선택합니다.

2. 설치 실행 을 마우스 오른쪽 단추로 클릭 하 고 설치 관리자로 실행 합니다.

    ![문제 해결 세션 - 선호도 문제-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Fiddler를 열면 자동으로 트래픽 캡처가 시작됩니다(왼쪽 아래 모서리에 캡처 가 확인). F12를 눌러 트래픽 캡처를 시작하거나 중지합니다.

    ![문제 해결 세션 - 선호도 문제-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 대부분의 경우, 당신은 암호 해독 HTTPS 트래픽에 관심이있을 것입니다, 당신은 **도구** > **Fiddler 옵션을**선택하여 HTTPS 암호 해독을 활성화 할 수 있습니다 , 그리고 확인을 선택 " HTTPS 트래픽 의 **해독**" .

    ![문제 해결 세션 - 선호도 문제-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 다음 스크린샷으로 모두 **제거하기** > **X** X(아이콘)를 클릭하여 문제를 재현하기 전에 이전 관련 없는 세션을 제거할 수 있습니다. 

    ![문제 해결 세션 -선호도 문제-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 문제를 재현한 후에는 모든 세션**저장** >  **파일을** > 선택하여 검토를 위해 파일을**저장합니다.. .** 

    ![문제 해결 세션 - 선호도 문제-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 세션 로그를 확인하고 분석하여 문제가 무엇인지 확인합니다.

    예:

- **예 A:** 클라이언트에서 요청이 전송되는 세션 로그를 찾았고 응용 프로그램 게이트웨이의 공용 IP 주소로 이동하여 이 로그를 클릭하여 세부 정보를 봅니다.  오른쪽 하단 상자의 데이터는 응용 프로그램 게이트웨이가 클라이언트에 반환하는 데이터입니다. "RAW" 탭을 선택하고 클라이언트가 **"세트 쿠키: ARRAffinity=** *ARRAffinityValue"를*받고 있는지 확인합니다. 쿠키가 없는 경우 세션 선호도가 설정되지 않거나 응용 프로그램 게이트웨이가 클라이언트에 쿠키를 다시 적용하지 않습니다.

   > [!NOTE]
   > 이 ARRAffinity 값은 클라이언트가 특정 백 엔드 서버로 전송되도록 응용 프로그램 게이트웨이가 설정하는 쿠키 ID입니다.

   ![문제 해결 세션 - 선호도 문제-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **예제 B:** 다음 세션 로그 다음에 이전 세션로고는 ARRAAFFINITY를 설정한 응용 프로그램 게이트웨이에 다시 응답하는 클라이언트입니다. ARRAffinity 쿠키 ID가 일치하는 경우 패킷은 이전에 사용되었던 동일한 백 엔드 서버로 전송되어야 합니다. 다음 여러 번의 http 통신을 확인하여 클라이언트의 ARRAffinity 쿠키가 변경되고 있는지 확인합니다.

   ![문제 해결 세션 - 선호도 문제 -18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 동일한 통신 세션의 경우 쿠키가 변경되지 않아야 합니다. 오른쪽 상단 상자를 선택하고 "쿠키" 탭을 선택하여 클라이언트가 쿠키를 사용하고 있는지 확인하고 응용 프로그램 게이트웨이로 다시 전송합니다. 그렇지 않은 경우 클라이언트 브라우저가 쿠키를 대화에 유지및 사용하지 않습니다. 경우에 따라 클라이언트가 거짓말을 할 수 있습니다.

 

## <a name="next-steps"></a>다음 단계

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.
