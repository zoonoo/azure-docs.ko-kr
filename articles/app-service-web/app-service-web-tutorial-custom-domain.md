---
title: "Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑 | Microsoft Docs"
description: "Azure App Service의 Web App, 모바일 앱 백 엔드 또는 API 앱에 사용자 지정 DNS 도메인 이름(베니티 도메인)을 추가하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 08a83bf8dca71846dd06edb9aa253f69c24d253c
ms.lasthandoff: 04/27/2017


---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑

이 자습서에서는 기존 사용자 지정 DNS 이름을 [Azure Web Apps](app-service-web-overview.md)에 매핑하는 방법을 보여 줍니다. 

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

이 자습서에서는 두 개의 DNS 이름을 App Service의 앱에 매핑하는 세 가지 일반적인 시나리오를 보여 줍니다.

- `www.contoso.com` - `contoso.com`의 하위 도메인입니다. CNAME 레코드를 사용하여 앱에 매핑합니다.
- `contoso.com` - 루트 도메인입니다. A 레코드를 사용하여 앱에 매핑합니다.
- `*.contoso.com` - 와일드 카드 도메인 CNAME 레코드를 사용하여 앱에 매핑합니다.

**CNAME 레코드** 또는 **A 레코드**를 사용하여 사용자 지정 DNS 이름을 App Service에 매핑할 수 있습니다.

> [!NOTE]
> 루트 도메인(예: contoso.com)을 제외한 모든 사용자 지정 DNS 이름에 대해 CNAME을 사용하는 것이 좋습니다. 
> 
> 

## <a name="before-you-begin"></a>시작하기 전에

이 자습서를 완료하려면 도메인 공급자(예: GoDaddy)의 DNS 레지스트리에 액세스하고 도메인 구성을 편집할 수 있는 권한이 필요합니다. 

예를 들어 `contoso.com` 및 `www.contoso.com`에 대한 DNS 항목을 추가하려면 `contoso.com` 루트 도메인에 대한 DNS 설정을 구성할 수 있는 액세스 권한이 있어야 합니다. 

> [!NOTE]
> 기존 도메인 이름이 없는 경우 Azure Portal을 사용하여 도메인을 구매하려면 [App Service 도메인 자습서](custom-dns-web-site-buydomains-web-app.md)를 따릅니다. 
>
>

## <a name="prepare-your-app"></a>앱 준비
사용자 지정 DNS 이름을 매핑하려면 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/)이 유료 계층(**공유**, **기본**, **표준** 또는 **프리미엄**)이어야 합니다. 이 단계에서는 App Service 앱이 지원되는 가격 책정 계층에 있음을 확인합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

Azure Portal을 엽니다. 이 작업을 수행하려면 사용자의 Azure 계정으로 [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.

### <a name="navigate-to-your-app"></a>앱으로 이동
왼쪽 메뉴에서 **App Services**를 클릭한 다음 앱의 이름을 클릭합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service 앱의 관리 블레이드(_블레이드_: 가로로 열리는 포털 페이지)가 열립니다.  

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

앱 블레이드의 왼쪽 탐색 영역에서 **설정** 섹션으로 스크롤하여 **강화(App Service 계획)**를 선택합니다.

![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

앱이 **무료** 계층에 있지 않은지 확인합니다. 앱의 현재 계층이 진한 파란색 상자로 강조 표시됩니다. 

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

사용자 지정 DNS는 **무료** 계층에서는 지원되지 않습니다. 강화해야 할 경우 다음 섹션을 수행합니다. 그렇지 않은 경우 **가격 책정 계층 선택** 블레이드를 닫고 [CNAME 레코드 매핑](#cname) 또는 [A 레코드 매핑](#a)으로 건너뜁니다.

### <a name="scale-up-your-app-service-plan"></a>App Service 계획 강화

무료가 아닌 계층(**공유**, **기본**, **표준** 또는 **프리미엄**) 중에서 선택합니다. 

**선택**을 클릭합니다.

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

아래에 알림이 표시되면 강화 작업이 완료됩니다.

![크기 조정 작업 확인](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>CNAME 레코드 매핑

자습서 예제에서 `www` 하위 도메인(`www.contoso.com`)에 대한 CNAME 레코드를 추가하려고 합니다. 

### <a name="access-dns-records-with-domain-provider"></a>도메인 공급자로 DNS 레코드 액세스

첫째, 도메인 공급자의 웹 사이트에 로그인합니다.

DNS 레코드를 관리하기 위한 페이지를 찾습니다. 각 도메인 공급자에는 자체 DNS 레코드 인터페이스가 있으므로 공급자의 설명서를 참조해야 합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다. 

계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 링크를 찾을 수 있습니다. 그런 다음 DNS 레코드를 관리할 수 있는 링크를 찾습니다. 이 링크는 **영역 파일**, **DNS 레코드** 또는 **고급 구성**이란 이름일 수 있습니다.

다음 스크린샷은 DNS 레코드 페이지의 예입니다.

![DNS 레코드 페이지 예](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

예제 스크린샷에서 **추가**를 클릭하여 레코드를 만듭니다. 일부 공급자에는 다른 레코드 형식을 추가하는 다양한 링크가 있습니다. 다시 공급자의 설명서를 참조하세요.

> [!NOTE]
> GoDaddy와 같은 특정 공급자의 경우 DNS 레코드를 변경해도 변경 내용을 만든 후 별도의 **변경 내용 저장** 링크를 클릭하기 전에는 적용되지 않습니다. 
>
>

### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

하위 도메인을 사용자 앱의 기본 호스트 이름(`<app_name>.azurewebsites.net`)에 매핑하는 CNAME 레코드를 추가합니다.

`www.contoso.com` 도메인 예제에서 CNAME 레코드는 이름 `www`를 `<app_name>.azurewebsites.net`으로 지정해야 합니다.

DNS 레코드 페이지가 다음 스크린샷과 같이 표시됩니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>앱에서 CNAME 레코드 매핑 사용

이제 구성된 DNS 이름을 앱에 추가할 준비가 되었습니다.

앱 블레이드의 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 클릭합니다. 

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

앱의 **사용자 지정 도메인** 블레이드에서 정규화된 사용자 지정 DNS 이름(`www.contoso.com`)을 목록에 추가해야 합니다.

**호스트 이름 추가** 옆에 있는 **+** 아이콘을 클릭합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

이전에 구성한 CNAME 레코드에 사용할 정규화된 도메인 이름을 입력(예: `www.contoso.com`)한 다음 **유효성 검사**를 클릭합니다.

그렇지 않은 경우 **호스트 이름 추가** 단추가 활성화됩니다. 

**호스트 이름 레코드 형식**이 **CNAME 레코드(example.com)**로 설정되어 있는지 확인합니다.

**호스트 이름 추가**를 클릭하여 앱에 DNS 이름을 추가합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

새 호스트 이름이 앱의 **사용자 지정 도메인** 페이지에 반영되는 데까지는 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

![추가된 CNAME 레코드](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

이전에 단계를 잊었거나 철자를 잘못 입력한 경우에는 페이지 아래쪽에 확인 오류가 표시됩니다.

![확인 오류](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>A 레코드 매핑

자습서의 예제에서는 루트 도메인 `contoso.com`에 대한 A 레코드를 추가하려고 합니다. 

<a name="info"></a>

### <a name="copy-your-apps-ip-address"></a>앱의 IP 주소 복사

A 레코드를 매핑하려면 앱의 외부 IP 주소가 필요합니다. 이 IP 주소는 **사용자 지정 도메인** 블레이드에서 찾을 수 있습니다.

앱 블레이드의 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 클릭합니다. 

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**사용자 지정 도메인** 페이지에서 앱의 IP 주소를 복사합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="access-dns-records-with-domain-provider"></a>도메인 공급자로 DNS 레코드 액세스

첫째, 도메인 공급자의 웹 사이트에 로그인합니다.

DNS 레코드를 관리하기 위한 페이지를 찾습니다. 각 도메인 공급자에는 자체 DNS 레코드 인터페이스가 있으므로 공급자의 설명서를 참조해야 합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다. 

계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 링크를 찾을 수 있습니다. 그런 다음 DNS 레코드를 관리할 수 있는 링크를 찾습니다. 이 링크는 **영역 파일**, **DNS 레코드** 또는 **고급 구성**이란 이름일 수 있습니다.

다음 스크린샷은 DNS 레코드 페이지의 예입니다.

![DNS 레코드 페이지 예](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

예제 스크린샷에서 **추가**를 클릭하여 레코드를 만듭니다. 일부 공급자에는 다른 레코드 형식을 추가하는 다양한 링크가 있습니다. 다시 공급자의 설명서를 참조하세요.

> [!NOTE]
> GoDaddy와 같은 특정 공급자의 경우 DNS 레코드를 변경해도 변경 내용을 만든 후 별도의 **변경 내용 저장** 링크를 클릭하기 전에는 적용되지 않습니다. 
>
>

### <a name="create-the-a-record"></a>A 레코드 만들기

앱에 A 레코드를 매핑하려면 App Service는 실제로 **두** DNS 레코드가 필요합니다.

- 사용자 앱의 IP 주소에 매핑할 **A** 레코드입니다.
- 사용자 앱의 기본 호스트 이름 `<app_name>.azurewebsites.net`에 매핑할 **TXT** 레코드입니다. 이 레코드를 사용하면 매핑할 사용자 지정 도메인을 사용자가 소유하고 있는지 App Service에서 확인할 수 있습니다.

`www.contoso.com` 도메인 예제의 경우 다음 표에 따라 A 및 TXT 레코드를 만듭니다(`@`는 일반적으로 루트 도메인을 나타냄). 

| 레코드 형식 | 호스트 | 값 |
| - | - | - |
| A | `@` | [앱의 IP 주소 복사](#info)의 IP 주소 |
| TXT | `@` | `<app_name>.azurewebsites.net` |

DNS 레코드 페이지가 다음 스크린샷과 같이 표시됩니다.

![DNS 레코드 페이지](./media/app-service-web-tutorial-custom-domain/a-record.png)

### <a name="enable-the-a-record-mapping-in-your-app"></a>앱에서 A 레코드 매핑 사용

이제 구성된 DNS 이름을 앱에 추가할 준비가 되었습니다.

Azure Portal에서 앱의 **사용자 지정 도메인** 페이지로 돌아가서 정규화된 사용자 지정 DNS 이름(`contoso.com`)을 목록에 추가해야 합니다.

**호스트 이름 추가** 옆에 있는 **+** 아이콘을 클릭합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

이전에 구성한 A 레코드에 사용할 정규화된 도메인 이름을 입력(예: `contoso.com`)한 다음 **유효성 검사**를 클릭합니다.

그렇지 않은 경우 **호스트 이름 추가** 단추가 활성화됩니다. 

**호스트 이름 레코드 형식**이 **A 레코드(example.com)**로 설정되어 있는지 확인합니다.

**호스트 이름 추가**를 클릭하여 앱에 DNS 이름을 추가합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

새 호스트 이름이 앱의 **사용자 지정 도메인** 페이지에 반영되는 데까지는 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

![추가된 A 레코드](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

이전에 단계를 잊었거나 철자를 잘못 입력한 경우에는 페이지 아래쪽에 확인 오류가 표시됩니다.

![확인 오류](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>와일드카드 도메인 매핑

[와일드카드 DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record)(예: `*.contoso.com`)를 App Service 앱에 매핑할 수도 있습니다. 

다음 단계에서는 CNAME 레코드를 사용하여 와일드카드 도메인 `*.contoso.com`을 매핑하는 방법을 보여 줍니다. 

### <a name="access-dns-records-with-domain-provider"></a>도메인 공급자로 DNS 레코드 액세스

첫째, 도메인 공급자의 웹 사이트에 로그인합니다.

DNS 레코드를 관리하기 위한 페이지를 찾습니다. 각 도메인 공급자에는 자체 DNS 레코드 인터페이스가 있으므로 공급자의 설명서를 참조해야 합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다. 

계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 링크를 찾을 수 있습니다. 그런 다음 DNS 레코드를 관리할 수 있는 링크를 찾습니다. 이 링크는 **영역 파일**, **DNS 레코드** 또는 **고급 구성**이란 이름일 수 있습니다.

다음 스크린샷은 DNS 레코드 페이지의 예입니다.

![DNS 레코드 페이지 예](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

예제 스크린샷에서 **추가**를 클릭하여 레코드를 만듭니다. 일부 공급자에는 다른 레코드 형식을 추가하는 다양한 링크가 있습니다. 다시 공급자의 설명서를 참조하세요.

> [!NOTE]
> GoDaddy와 같은 특정 공급자의 경우 DNS 레코드를 변경해도 변경 내용을 만든 후 별도의 **변경 내용 저장** 링크를 클릭하기 전에는 적용되지 않습니다. 
>
>

### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

와일드카드 이름을 앱의 기본 호스트 이름(`<app_name>.azurewebsites.net`)에 매핑하는 CNAME 레코드를 추가합니다.

`*.contoso.com` 도메인 예제에서 CNAME 레코드는 이름 `*`를 `<app_name>.azurewebsites.net`으로 지정해야 합니다.

DNS 레코드 페이지가 다음 스크린샷과 같이 표시됩니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>앱에서 CNAME 레코드 매핑 사용

이제 와일드카드 이름과 일치하는 하위 도메인을 추가할 수 있습니다.

`*.contoso.com` 와일드카드 예제의 경우 이제 `sub1.contoso.com` 및 `sub2.contoso.com`을 추가할 수 있습니다. 

앱 블레이드의 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 클릭합니다. 

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**호스트 이름 추가** 옆에 있는 **+** 아이콘을 클릭합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

와일드카드 도메인과 일치하는 하위 도메인의 정규화된 도메인 이름(예: `sub1.contoso.com`)을 입력한 다음 **유효성 검사**를 클릭합니다.

그렇지 않은 경우 **호스트 이름 추가** 단추가 활성화됩니다. 

**호스트 이름 레코드 형식**이 **CNAME 레코드(example.com)**로 설정되어 있는지 확인합니다.

**호스트 이름 추가**를 클릭하여 앱에 DNS 이름을 추가합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

새 호스트 이름이 앱의 **사용자 지정 도메인** 페이지에 반영되는 데까지는 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

**+** 아이콘을 다시 클릭하여 와일드카드 도메인과 일치하는 다른 호스트 이름을 추가할 수 있습니다.

예를 들어 위의 동일한 단계를 사용하여 `sub2.contoso.com`을 추가합니다.

![추가된 CNAME 레코드](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>브라우저에서 테스트

브라우저에서 이전에 구성한 DNS 이름을 찾습니다(`contoso.com` 및 `www.contoso.com`).

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/overview)을 사용하여 스크립트를 통해 사용자 지정 도메인의 관리를 자동화할 수 있습니다. 

### <a name="azure-cli"></a>Azure CLI 

다음 명령은 구성된 사용자 지정 DNS 이름을 App Service 앱에 추가합니다. 

```bash 
az appservice web config hostname add --webapp <app_name> --resource-group <resourece_group_name> \ 
--name <fully_qualified_domain_name> 
``` 

자세한 내용은 [Web App에 사용자 지정 도메인 매핑](scripts/app-service-cli-configure-custom-domain.md)을 참조하세요. 

### <a name="azure-powershell"></a>Azure PowerShell 

다음 명령은 구성된 사용자 지정 DNS 이름을 App Service 앱에 추가합니다. 

```PowerShell  
Set-AzureRmWebApp -Name <app_name> -ResourceGroupName <resourece_group_name> ` 
-HostNames @(<fully_qualified_domain_name>,"<app_name>.azurewebsites.net") 
```

자세한 내용은 [Web App에 사용자 지정 도메인 할당](scripts/app-service-powershell-configure-custom-domain.md)을 참조하세요.

## <a name="more-resources"></a>추가 리소스

[Azure App Service에서 App Service 도메인 구성](custom-dns-web-site-buydomains-web-app.md)

