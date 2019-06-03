---
title: Azure Application Gateway 세션 선호도 문제 해결
description: 이 문서에서는 Azure Application Gateway 세션 선호도 문제를 해결 하는 방법에 대해 설명
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 66f61b5d6fcb86ed93e4dbae802ae7a80613c83d
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66397848"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Azure Application Gateway 세션 선호도 문제 해결

진단 및 Azure Application gateway 세션 선호도 문제를 해결 하는 방법에 알아봅니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

쿠키 기반 세션 선호도 기능은 동일한 서버에서 사용자 세션을 유지하려는 경우에 유용합니다. Application Gateway는 게이트웨이 관리형 쿠키를 사용하여 사용자 세션에서 동일한 서버에 후속 트래픽을 처리하도록 지시할 수 있습니다. 이는 세션 상태가 사용자 세션의 서버에 로컬로 저장된 경우에 특히 중요합니다.

## <a name="possible-problem-causes"></a>가능한 문제 원인

쿠키 기반 세션 선호도 유지 관리의 문제는 다음과 같은 주요 이유로 인해 발생할 수 있습니다.

- "쿠키 기반 선호도" 설정을 사용 하지 않는
- 응용 프로그램 쿠키 기반 선호도 처리할 수 없습니다.
- 응용 프로그램 백 엔드 서버 간 반송 여전히 요청 하지만 쿠키 기반 선호도 사용

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>"쿠키 기반 선호도" 설정이 사용 되는지 여부를 확인 합니다.

경우에 따라 세션 선호도 문제는 "쿠키 선호도 기반 하는 데 사용" 설정을 사용 하도록 설정 해야 하는 경우 발생할 수 있습니다. Azure portal에서 HTTP 설정 탭에서 "쿠키 선호도 기반 하는 데 사용" 설정을 설정한 여부를 확인 하려면 지침을 따르세요.

1. [Azure 포털](https://portal.azure.com/)에 로그온합니다.

2. 에 **왼쪽 탐색** 창 클릭 **리소스를 모두**입니다. 모든 리소스 블레이드에서 응용 프로그램 게이트웨이 이름을 클릭 합니다. 이미 선택한 구독에 있는 몇 가지 리소스, 경우에 응용 프로그램 게이트웨이 이름을 입력할 수 있습니다는 **이름을 기준으로 필터링...** 애플리케이션 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

3. 선택 **http-settings** 탭에서 **설정**합니다.

   ![troubleshoot-session-affinity-issues-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 클릭 **appGatewayBackendHttpSettings** 오른쪽에 있는 선택한 여부를 확인 하려면 **Enabled** 쿠키 기반 선호도 대 한 합니다.

   ![troubleshoot-session-affinity-issues-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



값을 확인할 수도 있습니다는 "**CookieBasedAffinity**"로 설정 된 *Enabled*아래에서 "**backendHttpSettingsCollection**" 다음 방법 중 하나를 사용 하 여:

- 실행할 [Get AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) PowerShell에서
- Azure Resource Manager 템플릿을 사용 하 여 JSON 파일을 통해 확인 합니다.

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>응용 프로그램에서 쿠키 기반 선호도 처리할 수 없습니다.

#### <a name="cause"></a>원인

Application gateway 세션 기반 선호도 쿠키를 사용 하 여 수행할 수 있습니다.

#### <a name="workaround"></a>해결 방법

응용 프로그램 쿠키 기반 선호도 처리할 수 없는 경우 외부 또는 내부 azure load balancer 또는 다른 타사 솔루션을 사용 해야 합니다.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>응용 프로그램 백 엔드 서버 간 반송 여전히 요청 하지만 쿠키 기반 선호도 사용

#### <a name="symptom"></a>증상

예를 들어 Internet Explorer의 짧은 이름 URL을 사용 하 여 Application Gateway에 액세스할 때 쿠키 기반 선호도 설정을 설정한: [ http://website ](http://website/) , 요청은 여전히 백 엔드 서버 간 반송 됩니다.

이 문제를 식별 하려면 지침을 따르세요.

1. 응용 프로그램 Gateway(We are using Fiddler in this example) 뒤 응용 프로그램에 연결 하는 "클라이언트"에서 웹 디버거 추적을 수행 합니다.
    **팁** 옵션을 선택 하는 Fiddler를 사용 하는 방법을 모르면 "**네트워크 트래픽을 수집 및 웹 디버거를 사용 하 여 분석 하려는**" 맨 아래에 있습니다.

2. 확인 하 고 분석 세션 로그를 클라이언트에서 제공 하는 쿠키를 ARRAffinity 세부 정보가 있는지 확인 합니다. ARRAffinity 세부 정보를 같은 찾지 못한 경우 "**ARRAffinity =** *ARRAffinityValue*"을 의미 하는 클라이언트 ARRA 쿠키를 사용 하 여 회신 하지는에서 제공 하는 쿠키 집합 내에서 Application Gateway입니다.
    예를 들면 다음과 같습니다.

    ![troubleshoot-session-affinity-issues-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

응용 프로그램 계속 회신 도달할 때까지 각 요청에 쿠키를 설정 하려고 시도 합니다.

#### <a name="cause"></a>원인

이 문제는 Internet Explorer 및 다른 브라우저를 저장 하거나 없습니다 약식 이름 URL을 사용 하 여 쿠키를 사용 하기 때문에 발생 합니다.

#### <a name="resolution"></a>해결 방법

이 이슈를 해결하려면 FQDN을 사용하여 Application Gateway에 액세스해야 합니다. 사용 예를 들어 [ http://website.com ](https://website.com/) 하거나 [ http://appgw.website.com ](http://appgw.website.com/) 합니다.

## <a name="additional-logs-to-troubleshoot"></a>문제를 해결 하려면 추가 로그

추가 로그를 수집 하 고 문제에 대 한 관련된 쿠키 기반 세션 선호도 문제를 해결 하 고 분석할 수 있습니다.

### <a name="analyze-application-gateway-logs"></a>Application Gateway 로그 분석

응용 프로그램 게이트웨이 로그를 수집 하려면 지침을 따르세요.

Azure Portal을 통한 로깅 사용

1. 에 [Azure portal](https://portal.azure.com/), 리소스를 찾고 클릭 **진단 로그**합니다.

   Application Gateway의 경우 다음 세 가지 로그를 사용할 수 있습니다. 액세스 로그, 성능 로그, 방화벽 로그

2. 데이터 수집을 시작, 클릭 **진단 켜기**합니다.

   ![troubleshoot-session-affinity-issues-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. **진단 설정** 블레이드에서는 진단 로그에 대한 설정을 제공합니다. 이 예제에서 Log Analytics는 로그를 저장합니다. 클릭 **구성** 아래에서 **Log Analytics** 작업 영역을 설정 합니다. 또한 이벤트 허브 및 스토리지 계정을 사용하여 진단 로그를 저장할 수도 있습니다.

   ![troubleshoot-session-affinity-issues-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 설정을 확인 하 고 클릭 **저장할**합니다.

   ![troubleshoot-session-affinity-issues-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Application Gateway 액세스 로그 보기 및 분석

1. Application Gateway 리소스 보기에서 Azure portal에서 선택 **진단 로그** 에 **모니터링** 섹션입니다.

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 오른쪽에서 "**ApplicationGatewayAccessLog**" 드롭 다운 목록에서 **로그 범주.**  

   ![troubleshoot-session-affinity-issues-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Application Gateway 액세스 로그 목록에서 분석 하 고 내보내기 로그를 클릭 하 고 JSON 파일을 내보냅니다.

4. CSV 파일에 3 단계에서 내보낸 JSON 파일을 변환 하 고 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 합니다.

5. 다음 데이터를 확인 합니다.

- **ClientIP**– 연결 중인 클라이언트에서 클라이언트 IP 주소입니다.
- **ClientPort** -요청에 대 한 연결 중인 클라이언트에서 원본 포트입니다.
- **RequestQuery** -이 요청이 수신 되는 대상 서버를 나타냅니다.
- **Server-Routed**: 요청이 수신 되는 백 엔드 풀 인스턴스.
- **X-AzureApplicationGateway-LOG-ID**: 요청에 사용되는 상관 관계 ID입니다. 백 엔드 서버에서 트래픽 문제를 해결하는 데 사용할 수 있습니다. 예를 들면 다음과 같습니다. X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**: Application Gateway에서 백 엔드로부터 받은 HTTP 응답 코드입니다.

  ![troubleshoot-session-affinity-issues-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

두 항목이 동일한 ClientIP 및 클라이언트 포트에서 생성 되 고 동일한 백 엔드 서버로 보내지는 보이면는 올바르게 구성 된 Application Gateway를 의미 합니다.

두 항목이 동일한 ClientIP 및 클라이언트 포트에서 생성 되 고 다른 백 엔드 서버로 보내지는 보이면 즉, 요청은 백 엔드 서버 간에 반송 "**응용 프로그램 요청 하지만 쿠키 기반 선호도 사용 백 엔드 서버 간 반송 여전히**"아래쪽에 있는 문제를 해결 합니다.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>웹 디버거를 사용 하 여 HTTP 또는 HTTPS 트래픽을 분석 합니다.

웹 Fiddler와 같은 디버깅 도구, 인터넷 및 테스트 컴퓨터 간에 네트워크 트래픽을 수집 하 여 웹 응용 프로그램을 디버그 하는 데 도움이 됩니다. 이러한 도구를 사용 하 여 대로 브라우저 수신/송신을 들어오고 나가는 데이터를 검사할 수 있습니다. 이 예제에서는 fiddler를 특히 인증 종류의 문제에 대 한 웹 응용 프로그램을 사용 하 여 클라이언트 쪽 문제를 해결 하는 데 도움이 되는 HTTP 재생 옵션을 있습니다.

원하는 web debugger를 사용 합니다. 이 샘플에서는 사용 하 여 Fiddler를 캡처 및 http 또는 https 트래픽을 분석 하 고, 지시를 따릅니다.

1. Fiddler에서이 도구를 다운로드 <https://www.telerik.com/download/fiddler>합니다.

    > [!NOTE]
    > 캡처 컴퓨터에.NET 4를 설치 하는 경우 Fiddler4를 선택 합니다. 그렇지 않으면 Fiddler2를 선택 합니다.

2. 설치 실행 파일을 마우스 오른쪽 단추로 클릭 하 고 설치 하려면 관리자 권한으로 실행 합니다.

    ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Fiddler를 열 때 자동으로 시작 (이때 왼쪽 아래 모서리에 있는 캡처) 트래픽 캡처. F12 키를 눌러 시작 또는 트래픽 캡처를 중지 합니다.

    ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 대부분의 경우 암호 해독 된 HTTPS 트래픽의 경우 관심이 해야 하 고 HTTPS 암호 해독을 선택 하 여 설정할 수 있습니다 **도구가** > **Fiddler 옵션**, 확인란을 선택 하 고 " **암호 해독 HTTPS 트래픽을**"입니다.

    ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 클릭 하 여 문제를 재현 하기 전에 이전 관련이 없는 세션을 제거할 수 있습니다 **X** (아이콘) > **모두 제거** 따르는 스크린 샷: 

    ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 선택 하 여 문제를 재현 한 후 저장 파일을 검토 **파일** > **저장** > **모든 세션...** . 

    ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 확인 하 고 문제를 확인 하려면 세션 로그를 분석 합니다.

    예:

- **A: 예** 클라이언트에서 요청을 보내고 응용 프로그램 게이트웨이의 공용 IP 주소로 이동 하는 세션 로그 세부 정보를 보려면이 로그를 클릭 합니다.  오른쪽에서 아래쪽 상자에 데이터가 Application Gateway는 클라이언트에 게 반환 합니다. "RAW" 탭을 선택 하 고 클라이언트 수신 하는지 여부를 결정 한 "**Set-cookie: ARRAffinity=** *ARRAffinityValue*." 쿠키가 없는 경우 세션 선호도 설정 또는 응용 프로그램 게이트웨이 다시 클라이언트에 쿠키를 적용 하지 않습니다.

   > [!NOTE]
   > 이 ARRAffinity 값 쿠키 id이 고, 특정 백 엔드 서버에 보낼 클라이언트에 대 한 응용 프로그램 게이트웨이 설정 하는 경우

   ![troubleshoot-session-affinity-issues-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **예 2:** 다음 세션 로그를 이전 클라이언트는 ARRAAFFINITY 설정한 응용 프로그램 게이트웨이 다시 응답할 것 옵니다. ARRAffinity 쿠키 id를 일치 하는 경우 이전에 사용 했던 동일한 백 엔드 서버에 패킷을 전송 되어야 합니다. Http 통신 클라이언트의 ARRAffinity 쿠키 변경 되 고 있는지 여부를 확인 하려면 다음 몇 줄을 확인 합니다.

   ![troubleshoot-session-affinity-issues-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 동일한 통신 세션에 대 한 쿠키를 변경 하지 않도록 해야 합니다. "쿠키" 탭을 선택 하는지 여부를 확인 클라이언트 쿠키를 사용 하 여 응용 프로그램 게이트웨이 다시 보내기, 오른쪽 맨 위에 있는 상자를 확인 합니다. 그러지 않으면 클라이언트 브라우저 유지 아니며 쿠키를 사용 하 여 대화에 대 한 합니다. 경우에 따라 클라이언트가 있을 수 있습니다.

 

## <a name="next-steps"></a>다음 단계

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.
