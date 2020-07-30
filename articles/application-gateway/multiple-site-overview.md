---
title: Azure Application Gateway에서 여러 사이트 호스팅
description: 이 문서에서는 Azure Application Gateway 다중 사이트 지원에 대한 개요를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 23f76f18256ecadcbef59a498292222ea358008f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290992"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway 다중 사이트 호스팅

여러 사이트 호스팅을 사용 하면 응용 프로그램 게이트웨이의 동일한 포트에 둘 이상의 웹 응용 프로그램을 구성할 수 있습니다. 이를 통해 하나의 응용 프로그램 게이트웨이에 최대 100 개 이상의 웹 사이트를 추가 하 여 배포에 보다 효율적인 토폴로지를 구성할 수 있습니다. 각 웹 사이트는 고유한 백 엔드 풀로 이동할 수 있습니다. 예를 들어, contoso.com, fabrikam.com 및 adatum.com 라는 세 개의 도메인은 응용 프로그램 게이트웨이의 IP 주소를 가리킵니다. 3 개의 다중 사이트 수신기를 만들고 각 포트 및 프로토콜 설정에 대해 각 수신기를 구성 합니다. 

다중 사이트 수신기에서 와일드 카드 호스트 이름을 정의 하 고 수신기 당 최대 5 개의 호스트 이름을 정의할 수도 있습니다. 자세히 알아보려면 [수신기의 와일드 카드 호스트 이름](#wildcard-host-names-in-listener-preview)을 참조 하세요.

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="다중 사이트 Application Gateway":::

> [!IMPORTANT]
> 규칙은 v1 SKU에 대 한 포털에 나열 된 순서 대로 처리 됩니다. V2 SKU의 경우 정확한 일치는 우선 순위가 높습니다. 기본 수신기를 구성하기 전에 먼저 다중 사이트 수신기를 구성하는 것이 좋습니다.  그러면 트래픽이 올바른 백 엔드로 라우팅됩니다. 기본 수신기가 먼저 나열되고 들어오는 요청과 일치하면 해당 수신기에서 처리합니다.

`http://contoso.com`에 대한 요청은 ContosoServerPool로 라우팅되고, `http://fabrikam.com`에 대한 요청은 FabrikamServerPool로 라우팅됩니다.

마찬가지로 동일한 응용 프로그램 게이트웨이 배포에서 동일한 부모 도메인의 여러 하위 도메인을 호스트할 수 있습니다. 예를 들어 `http://blog.contoso.com` `http://app.contoso.com` 단일 application gateway 배포에서 및를 호스트할 수 있습니다.

## <a name="wildcard-host-names-in-listener-preview"></a>수신기의 와일드 카드 호스트 이름 (미리 보기)

Application Gateway는 다중 사이트 HTTP (S) 수신기를 사용 하는 호스트 기반 라우팅을 허용 합니다. 이제 호스트 이름에 별표 (*) 및 물음표 (?)와 같은 와일드 카드 문자를 사용할 수 있으며 다중 사이트 HTTP (S) 수신기 당 최대 5 개의 호스트 이름을 사용할 수 있습니다. 예: `*.contoso.com`.

호스트 이름에 와일드 카드 문자를 사용 하 여 단일 수신기에서 여러 호스트 이름을 찾을 수 있습니다. 예를 들어는 및 등과 `*.contoso.com` 일치할 수 있습니다 `ecom.contoso.com` `b2b.contoso.com` `customer1.b2b.contoso.com` . 호스트 이름 배열을 사용 하 여 수신기에 대 한 호스트 이름을 두 개 이상 구성 하 여 요청을 백 엔드 풀로 라우팅할 수 있습니다. 예를 들어 수신기에는 `contoso.com, fabrikam.com` 호스트 이름에 대 한 요청을 허용 하는가 포함 될 수 있습니다.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="와일드 카드 수신기":::

>[!NOTE]
> 이 기능은 미리 보기 상태 이며 Application Gateway의 Standard_v2 및 WAF_v2 SKU에 대해서만 사용할 수 있습니다. 미리 보기에 대 한 자세한 내용은 [여기의 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

>[!NOTE]
>이 기능은 현재 [Azure PowerShell](tutorial-multiple-sites-powershell.md) 및 [Azure CLI](tutorial-multiple-sites-cli.md)를 통해서만 사용할 수 있습니다. 포털 지원이 곧 제공 될 예정입니다.

[Azure PowerShell](tutorial-multiple-sites-powershell.md)에서 대신를 사용 해야 합니다 `-HostNames` `-HostName` . 호스트 이름을 사용 하 여 최대 5 개의 호스트 이름을 쉼표로 구분 된 값으로 언급 하 고 와일드 카드 문자를 사용할 수 있습니다. 예를 들어 `-HostNames "*.contoso.com,*.fabrikam.com"`

[Azure CLI](tutorial-multiple-sites-cli.md)에서 대신를 사용 해야 합니다 `--host-names` `--host-name` . 호스트 이름을 사용 하 여 최대 5 개의 호스트 이름을 쉼표로 구분 된 값으로 언급 하 고 와일드 카드 문자를 사용할 수 있습니다. 예를 들어 `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>호스트 이름 필드에 허용 되는 문자:

* `(A-Z,a-z,0-9)`-영숫자 문자
* `-`-하이픈 또는 빼기
* `.`-마침표로 구분 기호
*   `*`-허용 된 범위에서 여러 문자를 찾을 수 있습니다.
*   `?`-허용 범위에서 단일 문자를 일치 시킬 수 있습니다.

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>수신기에서 와일드 카드 문자 및 여러 호스트 이름을 사용 하기 위한 조건:

*   단일 수신기에서 최대 5 개의 호스트 이름만 사용할 수 있습니다.
*   별표는 `*` 도메인 스타일 이름 또는 호스트 이름의 구성 요소에서 한 번만 설명 될 수 있습니다. 예를 들면 component1.*component2*. component3. `(*.contoso-*.com)`는 유효합니다.
*   호스트 이름에는 최대 두 개의 별표가 있을 수 있습니다 `*` . 예를 들어 `*.contoso.*` 는 유효 하며 유효 `*.contoso.*.*.com` 하지 않습니다.
*   호스트 이름에는 최대 4 개의 와일드 카드 문자만 사용할 수 있습니다. 예를 들어,는 `????.contoso.com` `w??.contoso*.edu.*` 올바르지만 유효 하지 않습니다 `????.contoso.*` .
*   `*` `?` 호스트 이름 (또는 또는)의 구성 요소에 별표와 물음표를 함께 사용 하 `*?` `?*` `**` 는 것은 유효 하지 않습니다. 예를 들어 `*?.contoso.com` 및 `**.contoso.com` 은 유효 하지 않습니다.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>수신기에서 와일드 카드 또는 여러 호스트 이름을 사용할 때의 고려 사항 및 제한 사항:

*   [Ssl 종료 및 종단 간 ssl](ssl-overview.md) 을 사용 하려면 프로토콜을 HTTPS로 구성 하 고 수신기 구성에서 사용할 인증서를 업로드 해야 합니다. 다중 사이트 수신기 인 경우 호스트 이름도 입력할 수 있습니다. 일반적으로 SSL 인증서의 CN입니다. 수신기에 여러 호스트 이름을 지정 하거나 와일드 카드 문자를 사용 하는 경우 다음 사항을 고려해 야 합니다.
    *   와일드 카드 호스트 이름이 *. contoso.com 인 경우에는 *. contoso.com와 같은 CN을 사용 하 여 와일드 카드 인증서를 업로드 해야 합니다.
    *   동일한 수신기에서 여러 호스트 이름을 언급 하는 경우에는 앞에서 언급 한 호스트 이름과 일치 하는 Cn을 사용 하 여 SAN 인증서 (주체 대체 이름)를 업로드 해야 합니다.
*   정규식을 사용 하 여 호스트 이름을 확인할 수 없습니다. 별표 (*) 및 물음표 (?)와 같은 와일드 카드 문자만 사용 하 여 호스트 이름 패턴을 구성할 수 있습니다.
*   백 엔드 상태 검사의 경우 HTTP 설정에 따라 여러 [사용자 지정 프로브](application-gateway-probe-overview.md) 를 연결할 수 없습니다. 대신 백 엔드에서 웹 사이트 중 하나를 검색 하거나 "127.0.0.1"을 사용 하 여 백 엔드 서버의 localhost를 검색할 수 있습니다. 그러나 수신기에서 와일드 카드 또는 여러 호스트 이름을 사용할 경우 지정 된 모든 도메인 패턴에 대 한 요청은 규칙 유형 (기본 또는 경로 기반)에 따라 백 엔드 풀로 라우팅됩니다.
*   "Hostname" 속성은 한 문자열을 입력으로 사용 합니다. 여기서 와일드 카드 없는 도메인 이름을 하나만 입력할 수 있으며 "hostname"은 문자열 배열을 입력으로 사용 하 여 와일드 카드 도메인 이름을 5 개까지 사용할 수 있습니다. 그러나 두 속성은 동시에 사용할 수 없습니다.
*   와일드 카드 또는 여러 호스트 이름을 사용 하는 대상 수신기를 사용 하 여 [리디렉션](redirect-overview.md) 규칙을 만들 수 없습니다.

다중 사이트 수신기에서 와일드 카드 호스트 이름을 구성 하는 방법에 대 한 단계별 가이드는 [Azure PowerShell을 사용 하 여 다중 사이트 만들기](tutorial-multiple-sites-powershell.md) 또는 [Azure CLI 사용](tutorial-multiple-sites-cli.md) 을 참조 하세요.

## <a name="host-headers-and-server-name-indication-sni"></a>호스트 헤더 및 SNI(서버 이름 표시)

동일한 인프라에서 여러 사이트를 호스트하도록 설정할 수 있는 세 가지 일반적인 메커니즘이 있습니다.

1. 여러 웹 애플리케이션 각각을 고유한 IP 주소에서 호스트합니다.
2. 동일한 IP 주소에서 호스트 이름을 사용하여 여러 웹 애플리케이션을 호스트합니다.
3. 동일한 IP 주소에서 다른 포트를 사용하여 여러 웹 애플리케이션을 호스트합니다.

현재 Application Gateway는 트래픽을 수신 대기 하는 단일 공용 IP 주소를 지원 합니다. 따라서 각각 고유한 IP 주소를 가진 여러 응용 프로그램은 현재 지원 되지 않습니다. 

Application Gateway는 각각 서로 다른 포트에서 수신 대기 하는 여러 응용 프로그램을 지원 하지만이 시나리오에서는 응용 프로그램이 비표준 포트에서 트래픽을 허용 해야 합니다. 이는 대개 원하는 구성이 아닙니다.

Application Gateway는 HTTP 1.1 호스트 헤더를 기반으로 동일한 공용 IP 주소 및 포트에서 둘 이상의 웹 사이트를 호스트합니다. Application gateway에서 호스트 되는 사이트는 SNI (서버 이름 표시) TLS 확장을 통해 TLS 오프 로드를 지원할 수도 있습니다. 따라서 이 시나리오는 클라이언트 브라우저 및 백 엔드 웹 팜은 RFC 6066에 정의된 대로 HTTP/1.1 및 TLS 확장을 지원해야 함을 의미합니다.

## <a name="next-steps"></a>다음 단계

Application Gateway에서 여러 사이트 호스팅을 구성 하는 방법에 대해 알아봅니다.
* [Azure Portal 사용](create-multiple-sites-portal.md)
* [Azure PowerShell 사용](tutorial-multiple-sites-powershell.md) 
* [Azure CLI 사용](tutorial-multiple-sites-cli.md)

종단 간 템플릿 기반 배포는 [다중 사이트 호스팅을 사용하는 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting)을 방문하세요.