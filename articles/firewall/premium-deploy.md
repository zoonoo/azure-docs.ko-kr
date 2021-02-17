---
title: Azure 방화벽 프리미엄 미리 보기 배포 및 구성
description: Azure 방화벽 프리미엄을 배포 하 고 구성 하는 방법에 대해 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ec8fc4473669b0c056d0b22ff44e5818b87ba3fa
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549743"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Azure 방화벽 프리미엄 미리 보기 배포 및 구성

> [!IMPORTANT]
> Azure 방화벽 프리미엄은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

 Azure 방화벽 프리미엄 미리 보기는 매우 중요 하 고 규정을 준수 하는 환경에 필요한 기능을 제공 하는 차세대 방화벽입니다. 이 관리 팩에는 다음과 같은 기능이 포함되어 있습니다.

- **TLS 검사** -아웃 바운드 트래픽을 해독 하 고, 데이터를 처리 한 다음 데이터를 암호화 하 고 대상에 보냅니다.
- **Idps** -네트워크 침입 감지 및 방지 시스템 (idps)을 사용 하면 악의적인 활동의 네트워크 활동을 모니터링 하 고,이 활동에 대 한 정보를 기록 하 고, 보고 하 고, 선택적으로 차단할 수 있습니다.
- **Url 필터링** -Azure 방화벽의 FQDN 필터링 기능을 확장 하 여 전체 URL을 고려 합니다. 예를 들어 `www.contoso.com/a/c` 대신 `www.contoso.com` 입니다.
- **웹 범주** -관리자는 도박 websites, 소셜 미디어 웹 사이트 등의 웹 사이트 범주에 대 한 사용자 액세스를 허용 하거나 거부할 수 있습니다.

자세한 내용은 [Azure 방화벽 프리미엄 기능](premium-features.md)을 참조 하세요.

템플릿을 사용 하 여 세 개의 서브넷이 있는 중앙 VNet (10.0.0.0/16)을 포함 하는 테스트 환경을 배포 합니다.
- 작업자 서브넷 (10.0.10.0/24)
- Azure 방호 서브넷 (10.0.20.0/24)
- 방화벽 서브넷 (10.0.100.0/24)

이 테스트 환경에서는 간단한 단일 중앙 VNet을 사용 합니다. 프로덕션을 위해 피어 링 Vnet를 사용 하는 [허브 및 스포크 토폴로지가](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 더 일반적입니다.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="중앙 VNet 토폴로지":::

작업자 가상 머신은 방화벽을 통해 HTTP/S 요청을 전송 하는 클라이언트입니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="deploy-the-infrastructure"></a>인프라 배포

이 템플릿은 IDPS, TLS 검사, URL 필터링 및 웹 범주를 사용 하 여 Azure 방화벽 프리미엄을 위한 전체 테스트 환경을 배포 합니다.

- 핵심 기능 (IDPS, TLS 검사, URL 필터링 및 웹 범주)의 유효성을 쉽게 확인할 수 있도록 미리 정의 된 설정이 포함 된 새로운 Azure 방화벽 프리미엄 및 방화벽 정책
- Key Vault 및 관리 되는 Id를 비롯 한 모든 종속성을 배포 합니다. 프로덕션 환경에서는 이러한 리소스가 이미 만들어져 동일한 템플릿에서 필요 하지 않을 수 있습니다.
- 자체 서명 된 루트 CA를 생성 하 고 생성 된 Key Vault에 배포 합니다.
-  파생 된 중간 CA를 생성 하 고 Windows 테스트 가상 머신 (Vm)에 배포 합니다.
- 또한 BastionHost (요새 호스트)가 배포 되며 Windows 테스트 컴퓨터 (Vm)에 연결 하는 데 사용할 수 있습니다.



[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>방화벽 테스트

이제 IDPS, TLS 검사, 웹 필터링 및 웹 범주를 테스트할 수 있습니다.

### <a name="add-firewall-diagnostics-settings"></a>방화벽 진단 설정 추가

방화벽 로그를 수집 하려면 방화벽 로그를 수집 하는 진단 설정을 추가 해야 합니다.

1. **Demofirewall** 을 선택 하 고 **모니터링** 아래에서 **진단 설정** 을 선택 합니다.
2. **진단 설정 추가** 를 선택합니다.
3. **진단 설정 이름** 에 *fw-diag* 를 입력 합니다.
4. **로그** 아래에서 **AzureFirewallApplicationRule** 및 **AzureFirewallNetworkRule** 를 선택 합니다.
5. **대상 세부 정보** 에서 **Log Analytics 작업 영역으로 보내기를** 선택 합니다.
6. **저장** 을 선택합니다.

### <a name="idps-tests"></a>IDPS 테스트

IDPS를 테스트 하려면 적절 한 서버 인증서를 사용 하 여 자체 내부 웹 서버를 배포 해야 합니다. Azure 방화벽 프리미엄 미리 보기 인증서 요구 사항에 대 한 자세한 내용은 [Azure 방화벽 프리미엄 미리 보기 인증서](premium-certificates.md)를 참조 하세요.

`curl`를 사용 하 여 다양 한 HTTP 헤더를 제어 하 고 악의적인 트래픽을 시뮬레이션할 수 있습니다.

#### <a name="to-test-idps-for-http-traffic"></a>HTTP 트래픽에 대해 IDPS를 테스트 하려면:

1. Vm 가상 머신에서 관리자 명령 프롬프트 창을 엽니다.
2. 명령 프롬프트에서 다음 명령을 입력합니다.

   `curl -A "BlackSun" <your web server address>`
3. 웹 서버 응답이 표시 됩니다.
4. Azure Portal의 방화벽 네트워크 규칙 로그로 이동 하 여 다음 메시지와 유사한 경고를 찾습니다.

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="경고 메시지":::

   > [!NOTE]
   > 데이터가 로그에 표시 되기 시작 하는 데 다소 시간이 걸릴 수 있습니다. 로그가 데이터를 표시 하기 시작 하는 데 20 분 이상 걸릴 수 있습니다.
5. 서명 2008983에 대 한 서명 규칙을 추가 합니다.

   1. **DemoFirewallPolicy** 를 선택 하 고 **설정** 에서 **idps (미리 보기)** 를 선택 합니다.
   1. **서명 규칙** 탭을 선택 합니다.
   1. **서명 ID** 의 열기 텍스트 상자에 *2008983* 을 입력 합니다.
   1. **모드** 에서 **거부** 를 선택 합니다.
   1. **저장** 을 선택합니다.
   1. 계속 하기 전에 배포가 완료 될 때까지 기다립니다.



6. 작업 Vm에서 `curl` 명령을 다시 실행 합니다.

   `curl -A "BlackSun" <your web server address>`

   이제 HTTP 요청이 방화벽에 의해 차단 되므로 연결 시간 제한이 만료 되 면 다음 출력이 표시 됩니다.

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Azure Portal의 모니터 로그로 이동 하 여 차단 된 요청에 대 한 메시지를 찾습니다.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>IDPS를 HTTPS 트래픽에 대해 테스트 하려면

HTTP 대신 HTTPS를 사용 하 여 이러한 말아 넘기기 테스트를 반복 합니다. 예를 들어 다음과 같습니다.

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

HTTP 테스트와 동일한 결과가 표시 되어야 합니다.

### <a name="tls-inspection-with-url-filtering"></a>URL 필터링을 사용 하 여 TLS 검사

URL 필터링을 사용 하 여 TLS 검사를 테스트 하려면 다음 단계를 수행 합니다.

1. 방화벽 정책 응용 프로그램 규칙을 편집 하 고 라는 새 규칙을 `AllowURL` `AllowWeb` 규칙 컬렉션에 추가 합니다. 대상 URL `www.nytimes.com/section/world` , 원본 IP 주소 **\* *_, 대상 유형 _* URL (미리 보기)** 을 구성 하 고 **TLS 검사 (미리 보기)** 및 프로토콜 **http, https** 를 선택 합니다.

3. 배포가 완료 되 면 Vm에서 브라우저를 열고으로 이동 하 여 `https://www.nytimes.com/section/world` 브라우저에서 HTML 응답이 예상 대로 표시 되는지 확인 합니다.
4. Azure Portal에서는 응용 프로그램 규칙 모니터링 로그에서 전체 URL을 볼 수 있습니다.

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="URL을 표시 하는 경고 메시지":::

일부 HTML 페이지는 거부 된 다른 Url을 참조 하므로 불완전 하 게 보일 수 있습니다. 이 문제를 해결 하려면 다음 방법을 사용할 수 있습니다.

- HTML 페이지에 다른 도메인에 대 한 링크가 포함 되어 있는 경우 이러한 Fqdn에 대 한 액세스를 허용 하는 새 응용 프로그램 규칙에 이러한 도메인을 추가할 수 있습니다.
- HTML 페이지에 하위 Url에 대 한 링크가 포함 되어 있는 경우 규칙을 수정 하 고 URL에 별표를 추가할 수 있습니다. 예: `targetURLs=www.nytimes.com/section/world*`

   또는 규칙에 새 URL을 추가할 수 있습니다. 예를 들어 다음과 같습니다. 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>웹 범주 테스트

스포츠 웹 사이트에 대 한 액세스를 허용 하는 응용 프로그램 규칙을 만들어 보겠습니다.
1. 포털에서 리소스 그룹을 열고 **DemoFirewallPolicy** 를 선택 합니다.
2. **응용 프로그램 규칙** 을 선택한 다음 **규칙 컬렉션을 추가** 합니다.
3. **이름** 에 *일반 웹*, **우선 순위** *103*, **규칙 컬렉션 그룹** 선택 **DefaultApplicationRuleCollectionGroup** 을 입력 합니다.
4. **이름** 에 대 한 **규칙** 에서 *allowsports*, **원본** *\** , **프로토콜** *http, https*, **TLS 검사** 를 선택 하 고 **대상 유형** 선택 *웹 범주 (미리 보기)*, **대상** 선택 *스포츠* 를 선택 합니다.
5. **추가** 를 선택합니다.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="스포츠 웹 범주":::
6. 배포가 완료 되 면 기타  **vm** 으로 이동 하 여 웹 브라우저를 열고로 이동 `https://www.nfl.com` 합니다.

   NFL 웹 페이지가 표시 되 고 응용 프로그램 규칙 로그에 **웹 범주: 스포츠** 규칙이 일치 하 고 요청이 허용 되었음을 표시 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 Azure 방화벽 프리미엄 미리 보기](premium-portal.md)
