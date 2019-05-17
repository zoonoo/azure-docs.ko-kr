---
title: SaaS에 대 한 액세스를 관리 하려면 테 넌 트 제한을 사용 하 여 클라우드 응용 프로그램-Azure | Microsoft Docs
description: Azure AD 테 넌 트를 기준으로 앱에 액세스할 수 있는 사용자를 관리 하려면 테 넌 트 제한을 사용 하는 방법입니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a340663a1ec4ddf748c6dc2bc3a4e2ce0c4228e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824391"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>SaaS 클라우드 응용 프로그램에 대 한 액세스를 관리 하는 데 사용 하 여 테 넌 트 제한

보안을 강조하는 대규모 조직에서는 Office 365와 같은 클라우드 서비스로 이동하려고 하지만, 사용자가 승인된 리소스에만 액세스할 수 있다는 사실을 인식하고 있어야 합니다. 일반적으로 회사에서는 액세스를 관리하려는 경우에 도메인 이름 또는 IP 주소를 제한합니다. 이 방법은 소프트웨어 서비스 (또는 SaaS) 앱과 같은 이름을 공유 도메인에서 실행 중인 공용 클라우드에서 호스팅되는 세계에서 실패 [outlook.office.com](https://outlook.office.com/) 하 고 [login.microsoftonline.com](https://login.microsoftonline.com/)합니다. 이러한 주소를 차단하면 사용자가 단순히 승인된 ID 및 리소스로만 제한되는 것이 아니라 웹상의 Outlook에 완전히 액세스할 수 없게 됩니다.

Azure Active Directory (Azure AD)에 대 한 해가이 문제는 테 넌 트 제한 이라는 기능입니다. 테 넌 트 제한을 사용 하 여 조직 Azure AD 테 넌 트 응용 프로그램에서 single sign-on에 대 한 사용에 따라 SaaS 클라우드 응용 프로그램에 대 한 액세스를 제어할 수 있습니다. 예를 들어 이러한 동일한 애플리케이션의 다른 조직 인스턴스에 대한 액세스를 차단하면서 조직의 Office 365 애플리케이션에 대한 액세스를 허용하려고 할 수 있습니다.  

테 넌 트 제한은 조직 사용자에 게 액세스를 허용 되는 테 넌 트 목록을 지정할 수 있습니다. 그러면 Azure AD는 이러한 허용된 테넌트에 대해서만 액세스를 허가합니다.

이 문서에서는 Office 365에 대 한 테 넌 트 제한을 중점적으로 다루지만 single sign on에 대 한 Azure AD를 사용 하 여 최신 인증 프로토콜을 사용 하는 모든 SaaS 클라우드 앱을 사용 하 여 기능 작동 해야 합니다. Office 365에서 사용되는 테넌트와는 다른 Azure AD 테넌트에서 SaaS 앱을 사용하는 경우 필요한 모든 테넌트가 허용되는지 확인합니다. 클라우드 SaaS 앱에 대한 자세한 내용은 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)를 참조하세요.

## <a name="how-it-works"></a>작동 방법

전체 솔루션은 다음 구성 요소로 구성됩니다.

1. **Azure AD**: 경우는 `Restrict-Access-To-Tenants: <permitted tenant list>` 허용 된 테 넌 트에 대 한 경우 Azure AD 보안 토큰만 발급 됩니다.

2. **온-프레미스 프록시 서버 인프라**: 이 인프라는 Secure Sockets Layer (SSL) 검사 수 있는 프록시 장치입니다. Azure AD에 대 한 트래픽은에 허용 된 테 넌 트의 목록을 포함 하는 헤더를 삽입 하려면 프록시를 구성 해야 합니다.

3. **클라이언트 소프트웨어**: 테 넌 트 제한을 지원 하려면 클라이언트 소프트웨어 토큰을 요청 해야 Azure AD에서 직접 프록시 인프라 트래픽을 가로챌 수 있도록 합니다. 브라우저 기반 Office 365 응용 프로그램 (예: OAuth 2.0) 최신 인증을 사용 하는 Office 클라이언트와 마찬가지로 현재 테 넌 트 제한을 지원 합니다.

4. **최신 인증**: 클라우드 서비스 테 넌 트 제한을 사용 하 여 모든 허용 되지 않는 테 넌 트에 대 한 액세스를 차단 하 최신 인증을 사용 해야 합니다. 기본적으로 최신 인증 프로토콜을 사용 하려면 Office 365 클라우드 서비스를 구성 해야 합니다. 최신 인증에 대한 Office 365 지원과 관련된 최신 정보를 보려면 [업데이트된 Office 365 최신 인증](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)을 참조하세요.

다음 다이어그램은 고급 트래픽 흐름을 보여 줍니다. 테 넌 트 제한을 트래픽 Office 365 클라우드 서비스가 아니라 Azure AD에 대 한 SSL 검사를 해야 합니다. Azure AD에 인증을 위해 트래픽 볼륨이 일반적으로 Exchange Online 및 SharePoint Online과 같은 SaaS 응용 프로그램에 대 한 트래픽 볼륨 보다 훨씬 낮은 이므로 이러한 구분은 중요 합니다.

![제한 트래픽 흐름을 테 넌 트-다이어그램](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>테 넌 트 제한 설정

테 넌 트 제한을 시작 하는 방법은 다음 두 단계가 있습니다. 먼저, 클라이언트가 올바른 주소에 연결할 수 있는지 확인 합니다. 둘째, 프록시 인프라를 구성 합니다.

### <a name="urls-and-ip-addresses"></a>URL 및 IP 주소

테 넌 트 제한을 사용 하려면 클라이언트를 인증 하려면 다음 Azure AD Url에 연결할 수 있어야 합니다. [login.microsoftonline.com](https://login.microsoftonline.com/)를 [login.microsoft.com](https://login.microsoft.com/), 및 [ login.windows.net](https://login.windows.net/)합니다. 또한 Office 365에 액세스 하려면 클라이언트 수도 있어야 정규화 된 도메인 이름 (Fqdn) Url에 연결할 수 및에 정의 된 IP 주소 [Office 365 Url 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)합니다. 

### <a name="proxy-configuration-and-requirements"></a>프록시 구성 및 요구 사항

다음 구성은 프록시 인프라를 통해 테 넌 트 제한을 사용 하도록 설정 해야 합니다. 이 지침은 일반적인 것이므로 보다 구체적인 구현 단계는 프록시 공급업체의 설명서를 참조차세요.

#### <a name="prerequisites"></a>필수 조건

- 프록시는 FQDN/URL을 사용하여 SSL 가로채기, HTTP 헤더 삽입 및 대상 필터링을 수행할 수 있어야 합니다.

- 클라이언트는 SSL 통신을 위해 프록시에서 제공한 인증서 체인을 신뢰해야 합니다. 예를 들어 경우 내부에서 발급 한 인증서 [공개 키 인프라 (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) 는 사용, 내부 발급 루트 인증 기관 인증서 신뢰할 수 있어야 합니다.

- 이 기능은 Office 365 구독에 포함 되어 되지만 테 넌 트 제한을 사용 하 여 다른 SaaS 앱에 대 한 액세스를 제어 하려는 경우 다음 Azure AD Premium 1 라이선스가 필요 합니다.

#### <a name="configuration"></a>구성

login.microsoftonline.com, login.microsoft.com 및 login.windows.net으로 들어오는 각 요청에 대해 두 개의 HTTP 헤더인 *Restrict-Access-To-Tenants* 및 *Restrict-Access-Context*를 삽입합니다.

헤더에는 다음 요소가 포함되어야 합니다.

- 에 대 한 *제한 액세스-에-테 넌 트*의 값을 사용 하 여 \<테 넌 트 목록 허용\>, 사용자가 액세스할 수 있도록 하려면 테 넌 트의 쉼표로 구분 된 목록입니다. 테넌트에 등록된 모든 도메인은 이 목록의 테넌트를 식별하는 데 사용할 수 있습니다. 예를 들어 Contoso 및 Fabrikam 테넌트 모두에 대한 액세스를 허용하기 위해 이름/값 쌍은 다음과 같이 지정됩니다.  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- 에 대 한 *제한-액세스-컨텍스트*단일 디렉터리 ID 값을 사용, 테 넌 트를 선언는 테 넌 트 제한을 설정 합니다. 예를 들어 Contoso 테 넌 트 제한 정책을 설정 하는 테 넌 트를 선언 하려면 이름/값 쌍은 다음과 같습니다. `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> 디렉터리 ID를 찾을 수 있습니다 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/)합니다. 관리자로 로그인한 후 **Azure Active Directory**를 선택하고 **속성**을 선택합니다.

사용자가 승인 되지 않은 테 넌 트와 자체 HTTP 헤더를 삽입 하지 못하도록 하려면 프록시를 대체 해야 합니다 *제한 액세스-에-테 넌 트* 헤더는 들어오는 요청에 이미 있는 경우.

클라이언트는 Login.microsoftonline.com, login.microsoft.com 및 login.windows.net에 대한 모든 요청에 강제로 해당 프록시를 사용하도록 지정되어야 합니다. 예를 들어 PAC 파일을 직접 프록시를 사용 하도록 클라이언트를 사용 하는 경우 최종 사용자가 편집 하거나 PAC 파일을 사용 하지 않도록 설정 하는 일을 할 수 해서는 안 됩니다.

## <a name="the-user-experience"></a>사용자 환경

이 섹션에는 최종 사용자와 관리자 모두에 대 한 환경을 설명합니다.

### <a name="end-user-experience"></a>최종 사용자 환경

예제 사용자는 Contoso 네트워크에 있지만 Outlook 온라인과 같은 공유 SaaS 애플리케이션의 Fabrikam 인스턴스에 액세스하려고 합니다. Fabrikam 허용 되지 않는 테 넌 트를 Contoso 인스턴스에 대 한 경우 IT 부서에서 승인 되지 않은 조직에 속하는 리소스에 액세스 하려고 하는 액세스 거부 메시지를 사용자에 게 표시 합니다.

### <a name="admin-experience"></a>관리자 환경

기업 프록시 인프라에서 테 넌 트 제한 구성이 완료 되는 동안 관리자는 Azure portal에서 테 넌 트 제한 보고서를 직접 액세스할 수 있습니다. 보고서를 보려면:

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/)합니다. 합니다 **Azure Active Directory 관리 센터** 대시보드가 표시 됩니다.

2. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. Azure Active Directory 개요 페이지가 표시 됩니다.

3. 에 **다른 기능** 을 선택 **제한 테 넌 트**합니다.

제한 된 액세스-상황에 맞는 테 넌 트를 사용 하는 id를 포함 하 여 테 넌 트 제한 정책으로 인해 차단 된 로그인이이 보고서를 사용할 수로 지정 된 테 넌 트 관리자 및 대상 디렉터리 id입니다. 이러한 제한을 설정하는 테넌트가 로그인에 대해 사용자 테넌트이거나 리소스 테넌트인 경우 로그인이 포함됩니다.

Azure Portal의 다른 보고서와 마찬가지로, 필터를 사용하여 보고서의 범위를 지정할 수 있습니다. 특정 시간 간격, 사용자, 응용 프로그램, 클라이언트 또는 상태에서 필터링 할 수 있습니다. 선택 하는 경우는 **열** 단추, 다음 필드의 조합 사용 하 여 데이터를 표시 하도록 선택할 수 있습니다.

- **사용자**
- **애플리케이션**
- **상태**
- **Date**
- **날짜 (UTC)** (UTC는 Coordinated Universal Time)
- **MFA 인증 메서드** (다단계 인증 방법)
- **MFA 인증 세부 사항** (다단계 인증 세부 정보)
- **MFA 결과**
- **IP 주소**
- **클라이언트**
- **사용자 이름**
- **위치**:
- **대상 테 넌 트 ID**

## <a name="office-365-support"></a>Office 365 지원

Office 365 응용 프로그램에 완벽 하 게 테 넌 트 제한을 지원 하기 위해 두 가지 조건을 충족 해야 합니다.

1. 사용 하는 클라이언트는 최신 인증을 지원 합니다.
2. 최신 인증이 클라우드 서비스에 대한 기본 인증 프로토콜로 사용되어야 합니다.

현재 최신 인증을 지원하는 Office 클라이언트에 대한 최신 정보를 보려면 [업데이트된 Office 365 최신 인증](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)을 참조하세요. 해당 페이지에는 특정 Exchange Online 및 비즈니스용 Skype Online 테넌트에 대해 최신 인증을 사용하도록 설정하기 위한 지침으로 연결되는 링크도 포함되어 있습니다. SharePoint Online 이미 최신 인증 기본적으로 사용 합니다.

Office 365 브라우저 기반 응용 프로그램 (Office 포털, Yammer, SharePoint 사이트, 웹의 Outlook) 현재 테 넌 트 제한을 지원 합니다. 씩 클라이언트 (Outlook, Skype for Business, Word, Excel, PowerPoint 등)는 최신 인증을 사용 하는 경우에 테 넌 트 제한을 적용할 수 있습니다.  

Outlook 및 Skype 테 넌 트 제한을 효과적으로 우회할에 최신 인증을 지 원하는 비즈니스 클라이언트를 최신 인증을 사용 하도록 설정 되지 않습니다 테 넌 트에 대해 레거시 프로토콜을 사용할 수 있습니다. 테 넌 트 제한을 인증 하는 동안 login.microsoftonline.com, login.microsoft.com 또는 login.windows.net에 연결할 경우 레거시 프로토콜을 사용 하는 응용 프로그램을 차단할 수 있습니다.

Windows의 Outlook에서 고객은 최종 사용자가 프로필에 승인되지 않은 메일 계정을 추가하는 것을 방지하는 제한을 구현하도록 선택할 수 있습니다. 예제를 보려면 [비기본 Exchange 계정 추가 금지](https://gpsearch.azurewebsites.net/default.aspx?ref=1) 그룹 정책 설정을 참조하세요.

## <a name="testing"></a>테스트하는 중

전체 조직에 대 한 구현 하기 전에 테 넌 트 제한을 사용해 하려는 경우 두 가지 옵션이 있습니다: 프록시 설정의 단계별된 롤아웃 또는 Fiddler와 같은 도구를 사용 하는 호스트 기반 방식입니다.

### <a name="fiddler-for-a-host-based-approach"></a>호스트 기반 접근 방식을 위한 Fiddler

Fiddler는 HTTP 헤더 삽입을 비롯하여 HTTP/HTTPS 트래픽을 캡처하고 수정하는 데 사용할 수 있는 무료 웹 디버깅 프록시입니다. 테 넌 트 제한을 테스트 하도록 Fiddler를 구성 하려면 다음 단계를 수행 합니다.

1. [Fiddler 다운로드하고 설치합니다](https://www.telerik.com/fiddler).

2. [Fiddler 도움말 설명서](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)에 따라 HTTPS 트래픽 암호를 해독하도록 Fiddler를 구성합니다.

3. 사용자 지정 규칙을 사용하여 *Restrict-Access-To-Tenants* 및 *Restrict-Access-Context* 머리글을 삽입하도록 Fiddler를 구성합니다.

   1. Fiddler Web Debugger 도구에서 **Rules** 메뉴를 선택하고 **Customize Rules...** 를 선택하여 CustomRules 파일을 엽니다.

   2. 시작 부분에 다음 줄을 추가 합니다 `OnBeforeRequest` 함수입니다. 대체 \<테 넌 트 도메인과\> 테 넌 트를 사용 하 여 등록 된 도메인 (예를 들어 `contoso.onmicrosoft.com`). \<directory ID\>를 테넌트의 Azure AD GUID 식별자로 바꿉니다.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      여러 테넌트를 허용해야 하는 경우 쉼표로 테넌트 이름을 구분합니다. 예를 들면 다음과 같습니다.

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. CustomRules 파일을 저장한 후 닫습니다.

Fiddler를 구성한 후 **파일** 메뉴로 이동하고 **트래픽 캡처**를 선택하여 트래픽을 캡처할 수 있습니다.

### <a name="staged-rollout-of-proxy-settings"></a>프록시 설정의 단계별 롤아웃

프록시 인프라의 기능에 따라 사용자에게 설정을 단계별로 롤아웃할 수 있습니다. 다음은 고려해야 할 몇 가지 고급 옵션입니다.

1. 일반 사용자는 프로덕션 프록시 인프라를 계속 사용할 수 있지만 PAC 파일을 사용하여 테스트 사용자는 테스트 프록시 인프라를 가리키도록 합니다.
2. 일부 프록시 서버는 그룹을 사용하여 다양한 구성을 지원할 수 있습니다.

특정 세부 정보에 대 한 프록시 서버 설명서를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

- [업데이트된 Office 365 최신 인증](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) 참조
- [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 검토
